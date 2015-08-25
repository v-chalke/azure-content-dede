<properties
	pageTitle="App-Modell v2.0 | Microsoft Azure"
	description="Vorgehensweise beim Erstellen einer .NET-MVC-Web-App, die sowohl Token von persönlichen Microsoft-Konten als auch Geschäfts- oder Schulkonten akzeptiert."
	that signs users in with both personal Microsoft Account and work or school accounts.
	services="active-directory"
	documentationCenter=".net"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# App-Modell v2. 0 (Vorschauversion): Hinzufügen der Anmeldung bei einer .NET-MVC-Web-App

Mit dem App-Modell v2. 0 können Sie schnell eine Authentifizierung zu Ihren Web-Apps hinzufügen, die sowohl persönliche Microsoft-Konten als auch Geschäfts- oder Schulkonten unterstützt. Für ASP.NET-Webanwendungen erreichen Sie das Gleiche durch die in .NET Framework 4.5 enthaltenen OWIN-Middleware von Microsoft.

  >[AZURE.NOTE]Diese Informationen gelten für App-Modell v2.0 (öffentliche Vorschauversion). Anweisungen zum Integrieren in den allgemein verfügbaren Azure AD-Dienst finden Sie im [Azure Active Directory-Entwicklerhandbuch](active-directory-developers-guide.md).

 Hier verwenden wir OWIN für Folgendes: – Anmelden eines Benutzers bei der Anwendung mit Azure AD und dem App-Modell v2.0. – Anzeigen von Informationen zum Benutzer. – Abmelden des Benutzers von der Anwendung.

Dazu müssen Sie folgende Schritte ausführen:

1. Registrieren einer App
2. Konfigurieren Ihrer Anwendung für die Verwendung der OWIN-Authentifizierungspipeline
3. Ausgabe von An- und Abmeldeanforderungen an Azure AD mit OWIN
4. Ausdrucken von Informationen zum Benutzer

Der Code für dieses Tutorial wird [auf GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet) verwaltet. Um folgen zu können, können Sie [das App-Gerüst als ZIP-Datei herunterladen](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) oder das Gerüst klonen:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

The completed app is provided at the end of this tutorial as well.

## 1. Register an App
Create a new app at [apps.dev.microsoft.com](https://apps.dev.microsoft.com), or follow these [detailed steps](active-directory-v2-app-registration.md).  Make sure to:

- Copy down the **Application Id** assigned to your app, you'll need it soon.
- Add the **Web** platform for your app.
- Enter the correct **Redirect URI**. The redirect uri indicates to Azure AD where authentication responses should be directed - the default for this tutorial is `https://localhost:44326/`.

## 2. Set up your app to use the OWIN authentication pipeline
Here, we'll configure the OWIN middleware to use the OpenID Connect authentication protocol.  OWIN will be used to issue sign-in and sign-out requests, manage the user's session, and get information about the user, amongst other things.

-	To begin, open the `web.config` file in the root of the project, and enter your app's configuration values in the `<appSettings>` section.
    -	The `ida:ClientId` is the **Application Id** assigned to your app in the registration portal.
    -	The `ida:RedirectUri` is the **Redirect Uri** you entered in the portal.

-	Next, add the OWIN middleware NuGet packages to the project using the Package Manager Console.

```
PM > Install-Package Microsoft.Owin.Security.OpenIdConnect PM > Install-Package Microsoft.Owin.Security.Cookies PM > Install-Package Microsoft.Owin.Host.SystemWeb ```

-	Fügen Sie dem Projekt eine „OWIN-Startklasse“ mit dem Namen `Startup.cs` hinzu. Klicken Sie dazu mit der rechten Maustaste auf das Projekt, wählen Sie **Hinzufügen** --> **Neues Element** aus, und suchen Sie nach „OWIN“. Die OWIN-Middleware ruft beim Starten Ihrer Anwendung die Methode `Configuration(...)` auf.
-	Ändern Sie die Klassendeklaration in `public partial class Startup` – einen Teil dieser Klasse haben wir bereits für Sie in einer anderen Datei implementiert. Rufen Sie in der Methode `Configuration(...)` „ConfigureAuth(...)“ auf, um die Authentifizierung für Ihre Webanwendung einzurichten.  

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-	Öffnen Sie die Datei `App_Start\Startup.Auth.cs`, und implementieren Sie die Methode `ConfigureAuth(...)`. Die Parameter, die Sie in `OpenIdConnectAuthenticationOptions` bereitstellen, dienen als Koordinaten für Ihre Anwendung zur Kommunikation mit Azure AD. Außerdem müssen Sie eine Cookie-Authentifizierung einrichten, da die Middleware OpenID Connect im Hintergrund Cookies verwendet.

```C#
public void ConfigureAuth(IAppBuilder app)
			 {
					 app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

					 app.UseCookieAuthentication(new CookieAuthenticationOptions());

					 app.UseOpenIdConnectAuthentication(
							 new OpenIdConnectAuthenticationOptions
							 {
									 // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
									 // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
									 // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

									 ClientId = clientId,
									 Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
									 RedirectUri = redirectUri,
									 Scope = "openid",
									 PostLogoutRedirectUri = redirectUri,
									 TokenValidationParameters = new TokenValidationParameters
									 {
											 ValidateIssuer = false,
									 },
									 Notifications = new OpenIdConnectAuthenticationNotifications
									 {
											 AuthenticationFailed = OnAuthenticationFailed,
									 }
							 });
			 }
```

## 3\. Ausgabe von An- und Abmeldeanforderungen an Azure AD mit OWIN
Ihre Anwendung ist nun ordnungsgemäß für die Kommunikation mit dem v2.0-Endpunkt über das Authentifizierungsprotokoll OpenID Connect konfiguriert. OWIN kümmert sich um all die mühseligen Details der Erstellung von Authentifizierungsnachrichten, Überprüfung der Azure AD-Tokens und Verwaltung der Benutzersitzungen. Sie müssen lediglich dafür sorgen, dass sich Ihre Benutzer an- und abmelden können.

- Über Autorisierungstags in Ihren Controllern können Sie die Benutzer vor dem Zugriff auf eine bestimmte Seite zur Anmeldung zwingen. Öffnen Sie `Controllers\HomeController.cs`, und fügen Sie dem Info-Controller den Tag `[Authorize]` hinzu.

```C#
[Authorize]
public ActionResult About()
{
  ...
```

-	Mit OWIN können Sie Authentifizierungsanforderungen auch direkt aus Ihrem Code ausgeben. Öffnen Sie `Controllers\AccountController.cs`. Geben Sie in den Aktionen „SignIn()“ und „SignOut()“ OpenID Connect-Challenge- und Abmeldeanforderungen ein.

```C#
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

// BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
    Response.Redirect("/");
}
```

-	Öffnen Sie dann `Views\Shared\_LoginPartial.cshtml`. In dieser Ansicht werden dem Benutzer sein Benutzername sowie die An- und Abmeldelinks für Ihre Anwendung angezeigt.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">

                @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@

                Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

## 4\. Anzeigen der Benutzerinformationen
Bei der Authentifizierung von Benutzern mit OpenID Connect gibt der v2.0-Endpunkt ein ID-Token an die App zurück. Dieses enthält die [Ansprüche](active-directory-v2-tokens.md#id_tokens) bzw. Assertionen des Benutzers. Mit diesen Ansprüchen können Sie Ihre Anwendung personalisieren:

- Öffnen Sie die Datei `Controllers\HomeController.cs`. Auf die Ansprüche des Benutzers können Sie in Ihren Controllern über das Sicherheitsprinzipalobjekt `ClaimsPrincipal.Current` zugreifen.

```C#
[Authorize]
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;

    // The object ID claim will only be emitted for work or school accounts at this time.
    Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
    ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;

    // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
    ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;

    // The subject or nameidentifier claim can be used to uniquely identify the user
    ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;

    return View();
}
```

Erstellen und führen Sie Ihre Anwendung zum Schluss aus! Melden Sie sich entweder mit einem persönlichen Microsoft-Konto oder einem Geschäfts- oder Schulkonto an, und beachten Sie, wie die Identität des Benutzers in der oberen Navigationsleiste dargestellt wird. Sie verfügen jetzt über eine mit branchenüblichen Protokollen gesicherte Web-App, die Benutzer mit ihren persönlichen Konten oder ihren Geschäfts- oder Schulkonten authentifizieren kann.

Als Referenz stellen wir das vollständige Beispiel (ohne Ihre Konfigurationswerte) [hier als ZIP-Datei bereit](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip). Sie können es alternativ aus GitHub klonen:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## Nächste Schritte

Sie können nun mit den Themen für fortgeschrittenere Benutzer fortfahren. Wie wäre es zum Beispiel mit Folgendem:

[Sichern einer Web-API mit dem App-Modell v2.0 >>](active-directory-devquickstarts-webapi-dotnet.md)

Weitere Ressourcen: - [Die App-Modell v2.0-Vorschauversion >>](active-directory-appmodel-v2-overview.md) - [StackOverflow-"azure-active-directory"-Tag >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

<!---HONumber=August15_HO7-->