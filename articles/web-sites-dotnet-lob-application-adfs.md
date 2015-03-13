﻿<properties 
	pageTitle="LOB-Anwendung in Azure-Websites mit AD FS" 
	description="Informationen zum Erstellen einer ASP.NET MVC-LOB-Anwendung in Azure-Websites, die mit dem lokalen STS authentifiziert wird. Für dieses Lernprogramm gilt AD FS als lokaler STS." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="02/12/2015" 
	ms.author="cephalin"/>

# Erstellen Sie eine ASP.NET MVC-Line-of-Business-Anwendung in Azure-Websites, die mit AD FS authentifiziert wird. #

In diesem Artikel erfahren Sie, wie Sie eine ASP.NET MVC-Line-of-Business (LOB)-Anwendung in [Azure-Websites](http://azure.microsoft.com/services/websites/) mit einem lokalen [Active Directory Federation Service](http://technet.microsoft.com/ library/hh831502.aspx) als Identitätsanbieter erstellen. Dieses Szenario kann funktionieren, wenn Sie LOB-Anwendungen in Azure-Websites erstellen möchten, Ihre Organisation jedoch verlangt, alle Daten vor Ort zu speichern.

Einen Überblick über die unterschiedlichen Enterprise-Authentifizierungs- und Autorisierungsoptionen für Azure-Websites finden Sie unter [Authenticate and Authorize Users in LOB Applications in Azure Websites](./web-sites-authentication-authorization) (Authentifizieren und Autorisieren von Benutzern in LOB-Anwendungen in Azure-Websites, in englischer Sprache).

<a name="bkmk_build"></a>
## Sie erstellen Folgendes ##

Sie erstellen eine grundlegende ASP.NET-Anwendung in Azure-Websites mit den folgenden Features:

- Authentifizieren von Benutzern bei AD FS
- Verwenden von `[Autorisieren]` zum Autorisieren von Benutzern für verschiedene Aktionen
- Statische Konfiguration für Debuggen in Visual Studio und Veröffentlichen auf Azure-Websites (einmal konfigurieren, jederzeit debuggen und veröffentlichen)  

<a name="bkmk_need"></a>
## Sie benötigen Folgendes ##

[AZURE.INCLUDE [free-trial-note](../includes/free-trial-note.md)]

Sie benötigen Folgendes zum Bearbeiten dieses Lernprogramms:

- Eine lokale AD FS-Bereitstellung (eine umfassende exemplarische Vorgehensweise der Testumgebung, die ich verwende, finden Sie unter [Test Lab: Standalone STS with AD FS in Azure VM (for test only)] [Eigenständiger STS mit AD FS in Azure-VM (nur für Testzwecke), in englischer Sprache].
- Berechtigungen zum Erstellen von Vertrauensstellungen der vertrauenden Seite in AD FS-Verwaltung
- Visual Studio 2013

<a name="bkmk_sample"></a>
## Verwenden der Beispielanwendung für die LOB-Vorlage ##

Die Beispielanwendung in diesem Lernprogramm [WebApp-WSFederation-DotNet)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) wurde vom Azure Active Directory-Team erstellt. Da AD FS den WS-Verbund unterstützt, können Sie es ohne weiteres als Vorlage zum Erstellen neuer LOB-Anwendungen verwenden. Es bietet die folgenden Features:

- Verwendet [WS-Federation](http://msdn.microsoft.com/library/bb498017.aspx) zur Authentifizierung bei einer lokalen AD FS-Bereitstellung
- Funktionen zum An- und Abmelden
- Verwendet [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) (anstelle von Windows Identity Foundation, d. h. WIF). Dies ist das künftige ASP.NET und lässt sich wesentlich einfacher als WIF für Authentifizierung und Autorisierung einrichten.

<a name="bkmk_setup"></a>
## Einrichten der Beispielanwendung ##

2.	Klonen Sie die Beispielprojektmappe unter [WebApp-WSFederation-DotNet](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet), oder laden Sie sie in Ihr lokales Verzeichnis herunter.

	> [AZURE.NOTE] Die Anweisungen unter [Readme.MD](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet/blob/master/README.md) zeigen, wie Sie die Anwendung in Azure Active Directory einrichten. Da Sie sie in diesem Lernprogramm aber mit AD FS einrichten, führen Sie stattdessen die hier genannten Schritte aus.

3.	Öffnen Sie die Projektmappe, und öffnen Sie "Controllers\AccountController.cs" im **Projektmappen-Explorer**.

	Sie sehen, dass der Code einfach eine Authentifizierungsaufforderung ausgibt, um den Benutzer mithilfe des WS-Verbunds zu authentifizieren. Die gesamte Authentifizierung wird in der Datei "App_Start\Startup.Auth.cs" konfiguriert.

4.  Öffnen Sie die Datei "App_Start\Startup.Auth.cs". Beachten Sie in der `ConfigureAuth`-Methode die folgende Zeile:

        app.UseWsFederationAuthentication(
            new WsFederationAuthenticationOptions
            {
                Wtrealm = realm,
                MetadataAddress = metadata                                      
            });

	Im OWIN-Zusammenhang beschränkt sich der Aufwand zur Konfiguration der WS-Verbund-Authentifizierung auf dieses absolute Minimum. Der Vorgang ist wesentlich einfacher und eleganter als bei WIF, wo in die Datei "Web.config" überall XML-Code eingefügt werden muss. Die einzige Information, die Sie benötigen, sind die ID der vertrauenden Seite (RP) und die URL der Metadatendatei Ihres AD FS-Diensts. Hier sehen Sie ein Beispiel:

	-	RP-ID: `https://contoso.com/MyLOBApp`
	-	Metadaten-Adresse: `http://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

5.	Ändern Sie in der Datei "App_Start\Startup.Auth.cs" die Definitionen für statische Zeichenfolgen wie unten hervorgehoben:  
	<pre class="prettyprint">
	private static string realm = ConfigurationManager.AppSettings["ida:<mark>RPIdentifier</mark>"];
    <mark><del>private static string aadInstance = ConfigurationManager.AppSettings["ida:AADInstance"];</del></mark>
    <mark><del>private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];</del></mark>
    <mark><del>private static string metadata = string.Format("{0}/{1}/federationmetadata/2007-06/federationmetadata.xml", aadInstance, tenant);</del></mark>
    <mark>private static string metadata = string.Format("https://{0}/federationmetadata/2007-06/federationmetadata.xml", ConfigurationManager.AppSettings["ida:ADFS"]);</mark>

    <mark><del>string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant);</del></mark>
    </pre>

6.	Nehmen Sie jetzt die entsprechenden Änderungen in der Datei "Web.config" vor. Öffnen Sie die Datei "Web.config", und ändern Sie die App-Einstellungen wie unten dargestellt:  
	<pre class="prettyprint">
	&lt;appSettings&gt;
	  &lt;add key="webpages:Version" value="3.0.0.0" /&gt;
	  &lt;add key="webpages:Enabled" value="false" /&gt;
	  &lt;add key="ClientValidationEnabled" value="true" /&gt;
	  &lt;add key="UnobtrusiveJavaScriptEnabled" value="true" /&gt;
	  <mark><del>&lt;add key="ida:Wtrealm" value="[Enter the App ID URI of WebApp-WSFederation-DotNet https://contoso.onmicrosoft.com/WebApp-WSFederation-DotNet]" /&gt;</del></mark>
	  <mark><del>&lt;add key="ida:AADInstance" value="https://login.windows.net" /&gt;</del></mark>
	  <mark><del>&lt;add key="ida:Tenant" value="[Enter tenant name, e.g. contoso.onmicrosoft.com]" /&gt;</del></mark>
	  <mark>&lt;add key="ida:RPIdentifier" value="[Enter the relying party identifier as configured in AD FS, e.g. https://localhost:44320/]" /&gt;</mark>
	  <mark>&lt;add key="ida:ADFS" value="[Enter the FQDN of AD FS service, e.g. adfs.contoso.com]" /&gt;</mark>

	&lt;/appSettings&gt;
	</pre>

	Tragen Sie die Schlüsselwerte entsprechend Ihrer jeweiligen Umgebung ein.

7.	Erstellen Sie die Anwendung, um sicherzustellen, dass keine Fehler vorliegen.

Das ist alles. Jetzt kann die Beispielanwendung mit AD FS arbeiten. Später müssen Sie für diese Anwendung noch eine Vertrauensstellung der vertrauenden Seite in AD FS konfigurieren.

<a name="bkmk_deploy"></a>
## Bereitstellen der Beispielanwendung in Azure-Websites

Hier veröffentlichen Sie die Anwendung auf einer Azure-Website unter Beibehaltung der Debugumgebung. Beachten Sie, dass Sie die Anwendung veröffentlichen, bevor sie eine RP-Vertrauensstellung bei AD FS hat. Daher funktioniert die Authentifizierung noch nicht. Wenn Sie dies jedoch jetzt tun, erhalten Sie die URL der Website, die Sie später auch zum Konfigurieren der RP-Vertrauensstellung verwenden.

1. Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen**.

	![](./media/web-sites-dotnet-lob-application-adfs/01-publish-website.png)

2. Wählen Sie **Microsoft Azure-Websites**.
3. Wenn Sie sich noch nicht bei Azure angemeldet haben, klicken Sie auf **Anmelden**, und verwenden Sie dabei das Microsoft-Konto für Ihr Azure-Abonnement.
4. Klicken Sie nach der Anmeldung auf **Neu**, um eine neue Website zu erstellen.
5. Füllen Sie alle erforderlichen Felder aus. Da Sie später eine Verbindung mit lokalen Daten herstellen, erstellen Sie keine Datenbank für diese Website.

	![](./media/web-sites-dotnet-lob-application-adfs/02-create-website.png)

6. Klicken Sie auf **Erstellen**. Nachdem die Website erstellt wurde, wird das Dialogfeld "Web veröffentlichen" geöffnet.
7. Ändern Sie unter **Ziel-URL** den Eintrag **http** in **https**. Kopieren Sie die gesamte URL in einen Text-Editor. Sie können sie später verwenden. Klicken Sie anschließend auf **Veröffentlichen**.

	![](./media/web-sites-dotnet-lob-application-adfs/03-destination-url.png)

11. Öffnen Sie in Visual Studio die Datei **Web.Release.config** in Ihrem Projekt. Fügen Sie den folgenden XML-Code in das Tag `<configuration>` ein, und ersetzen Sie den Schlüsselwert durch die URL Ihrer zu veröffentlichenden Website.  
	<pre class="prettyprint">
&lt;appSettings&gt;
   &lt;add key="ida:RPIdentifier" value="<mark>[e.g. https://mylobapp.azurewebsites.net/]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" /&gt;
&lt;/appSettings&gt;</pre>

Danach sind in Ihrem Projekt zwei RP-IDs konfiguriert, eine für Ihre Debugumgebung in Visual Studio und eine für die veröffentlichte Website in Azure. Für beide Umgebungen wird in AD FS eine RP-Vertrauensstellung eingerichtet. Während des Debuggens werden die App-Einstellungen in der Datei "Web.config" verwendet, damit die **Debug**-Konfiguration in AD FS funktioniert. Bei der Veröffentlichung (standardmäßig wird die **Release**-Konfiguration veröffentlicht) wird eine transformierte Datei "Web.config" hochgeladen, welche die geänderten App-Einstellungen der Datei "Web.Release.config" enthält.

Wenn Sie die veröffentlichte Website dem Debugger anfügen möchten (d. h., Sie müssen Debugsymbole Ihres Codes in der veröffentlichten Website hochladen) können Sie einen Klon der Debug-Konfiguration für das Debuggen von Azure erstellen, jedoch mit einer eigenen benutzerdefinierten Web.config-Transformation (z. B. Web.AzureDebug.config), welche die App-Einstellungen der Datei "Web.Release.config" verwendet. Dadurch können Sie eine statische Konfiguration in verschiedenen Umgebungen verwalten.

<a name="bkmk_rptrusts"></a>
## Konfigurieren von Vertrauensstellungen der vertrauenden Seite in der AD FS-Verwaltung ##

Jetzt müssen Sie eine RP-Vertrauensstellung in der AD FS-Verwaltung konfigurieren, bevor sich Ihre Beispielanwendung tatsächlich mit AD FS authentifizieren kann. Sie müssen zwei separate RP-Vertrauensstellungen einrichten, eine für Ihre Debugumgebung und eine für die veröffentlichte Website.

> [AZURE.NOTE] Wiederholen Sie die folgenden Schritte für beide Umgebungen.

4.	Melden Sie sich mit Anmeldeinformationen, die Berechtigungen zur Verwaltung von AD FS umfassen, auf dem AD FS-Server an.
5.	Öffnen Sie die AD FS-Verwaltung. Klicken Sie mit der rechten Maustaste auf **AD FS\Trusted Relationships\Relying Party Trusts**, und wählen Sie **Add Relying Party Trust**.

	![](./media/web-sites-dotnet-lob-application-adfs/1-add-rptrust.png)

5.	Wählen Sie auf der Seite **Datenquelle auswählen** den Eintrag **Enter data about the relying party manually** aus. 

	![](./media/web-sites-dotnet-lob-application-adfs/2-enter-rp-manually.png)

6.	Geben Sie auf der Seite **Anzeigename angeben** einen Anzeigenamen für die Anwendung ein, und klicken Sie auf **Weiter**.
7.	Klicken Sie auf der Seite **Choose Protocol** auf **Weiter**.
8.	Klicken Sie auf der Seite **Zertifikat konfigurieren** auf **Weiter**.

	> [AZURE.NOTE] Da Sie HTTPS bereits verwenden sollten, sind verschlüsselte Token optional. Wenn Sie Token von AD FS auf dieser Seite verschlüsseln möchten, müssen Sie Ihrem Code auch Token-Entschlüsselungslogik hinzufügen. Weitere Informationen finden Sie unter [Manually configuring OWIN WS-Federation middleware and accepting encrypted tokens](http://chris.59north.com/post/2014/08/21/Manually-configuring-OWIN-WS-Federation-middleware-and-accepting-encrypted-tokens.aspx) (Manuelles Konfigurieren von OWIN-WS-Verbund-Middleware und Akzeptieren verschlüsselter Token, in englischer Sprache).
  
5.	Bevor Sie zum nächsten Schritt übergehen, benötigen Sie eine bestimmte Information aus Ihrem Visual Studio-Projekt. Notieren Sie in den Projekteigenschaften die **SSL-URL** der Anwendung. 

	![](./media/web-sites-dotnet-lob-application-adfs/3-ssl-url.png)

6.	Kehren Sie zur AD FS-Verwaltung zurück, und wählen Sie auf der Seite **URL konfigurieren** des **Assistenten zum Hinzufügen einer Vertrauensstellung für die vertrauende Seite** die Option **Enable support for the WS-Federation Passive protocol** aus, und geben Sie die SSL-URL Ihres Visual Studio-Projekts ein, die Sie im vorherigen Schritt notiert haben. Klicken Sie auf **Weiter**.

	![](./media/web-sites-dotnet-lob-application-adfs/4-configure-url.png)

	> [AZURE.NOTE] Die URL gibt an, auf welche Seite der Client nach der erfolgreichen Authentifizierung geleitet werden soll. Für die Debug-Umgebung lautet die URL folgendermaßen: <code>https://localhost:&lt;Port&gt;/</code>. Für die veröffentlichte Website muss dies die Website-URL sein.

7.	Prüfen Sie auf der Seite **Configure Identifiers**, ob die SSL-URL für Ihr Projekt bereits aufgeführt wird, und klicken Sie auf **Weiter**. Klicken Sie bis zum Abschluss des Assistenten mit den Standardeinstellungen stets auf **Weiter**.

	> [AZURE.NOTE] In der Datei "App_Start\Startup.Auth.cs" Ihres Visual Studio-Projekts wird diese ID verglichen mit dem Wert von <code>WsFederationAuthenticationOptions.Wtrealm</code> während der Verbundauthentifizierung. Standardmäßig wird die URL der Anwendung aus dem vorherigen Schritt als RP-ID hinzugefügt.

8.	Damit ist die Konfiguration der RP-Anwendung für Ihr Projekt in AD FS abgeschlossen. Anschließend konfigurieren Sie diese Anwendung zum Senden die Ansprüche, die Ihre Anwendung benötigt. Am Ende des Assistenten wird das Dialogfeld **Anspruchsregeln bearbeiten** standardmäßig geöffnet, damit Sie sofort mit der Bearbeitung beginnen können. Wir wollen mindestens die folgenden Ansprüche konfigurieren (Schemas sind in Klammern angegeben):

	-	Name (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name) - wird von ASP.NET zum Hydrieren von  `User.Identity.Name` verwendet.
	-	Benutzerprinzipalname (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn) - wird verwendet, um Benutzer in der Organisation eindeutig zu identifizieren.
	-	Gruppenmitgliedschaften als Rollen (http://schemas.microsoft.com/ws/2008/06/identity/claims/role) - kann mit der Dekoration `[Authorize (Rolles="role1 role2,...")]` zum Autorisieren von Controllern/Aktionen verwendet werden. Tatsächlich ist dies nicht unbedingt die leistungsfähigste Methode für die Rollenautorisierung, insbesondere dann nicht, wenn die Active Directory-Benutzer normalerweise Hunderten von Sicherheitsgruppen angehören, denn dies entspricht Hunderten von Rollenansprüche im SAML-Token. Eine alternative Methode ist das bedingte Senden eines einzelnen Rollenanspruchs je nach Mitgliedschaft des Benutzers in einer bestimmten Gruppe. Allerdings werden wir die Methode im Rahmen dieses Lernprogramms einfach halten.
	-	Namens-ID (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier) - kann zur Validierung des Fälschungsschutzes verwendet werden. Weitere Informationen darüber, wie diese bei der Validierung des Fälschungsschutzes eingesetzt werden kann, finden Sie im Abschnitt **Add LOB functionality** (LOB-Funktionen hinzufügen) auf der Seite [Create an ASP.NET MVC Line-of-Business Application in Azure Websites that Authenticates with AD FS](../ web-sites-dotnet-lob-application-adfs/#bkmk_crud) (Erstellen einer ASP.NET-MVC-LOB-Anwendung in Azure-Websites, die mit AD FS authentifiziert wird, in englischer Sprache).

	> [AZURE.NOTE] Die Anspruchstypen, die Sie für Ihre Anwendung konfigurieren müssen, richten sich nach den Anforderungen Ihrer Anwendung. Die Liste der Ansprüche, die von Azure Active Directory-Anwendungen (d. h. RP-Vertrauensstellungen) unterstützt werden, finden Sie z. B. unter [Unterstützte Token- und Anspruchstypen](http://msdn.microsoft.com/library/azure/dn195587.aspx).

8.	Klicken Sie im Dialogfeld "Anspruchsregeln bearbeiten" auf **Regel hinzufügen**.
9.	Konfigurieren Sie die Namens-, UPN- und Rollenansprüche wie unten gezeigt, und klicken Sie auf **Fertig stellen**.

	![](./media/web-sites-dotnet-lob-application-adfs/5-ldap-claims.png)

	Anschließend erstellen Sie einen vorübergehenden Namens-ID-Anspruch unter Verwendung der Schritte, die unter [Name Identifiers in SAML assertions](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx) (Namens-IDs in SAML-Assertionen, in englischer Sprache).

9.	Klicken Sie erneut auf **Regel hinzufügen**.
10.	Wählen Sie **Send Claims Using a Custom Rule**, und klicken Sie auf **Weiter**.
11.	Fügen Sie in das Feld **Benutzerdefinierte Regel** die folgende Regelsprache ein, benennen Sie die Regel **Sitzungsspezifische ID**, und klicken Sie auf **Fertig stellen**.  
	<pre class="prettyprint">
	c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] &amp;&amp;
	c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant"]
		add(
			store = "_OpaqueIdStore",
			types = ("<mark>http://contoso.com/internal/sessionid</mark>"),
			query = "{0};{1};{2};{3};{4}",
			param = "useEntropy",
			param = c1.Value,
			param = c1.OriginalIssuer,
			param = "",
			param = c2.Value);
	</pre>

	Die benutzerdefinierte Regel sollte wie folgt aussehen:

	![](./media/web-sites-dotnet-lob-application-adfs/6-per-session-identifier.png)

9.	Klicken Sie erneut auf **Regel hinzufügen**.
10.	Wählen Sie **Transform an Incoming Claim**, und klicken Sie auf **Weiter**.
11.	Konfigurieren Sie die Regel wie unten gezeigt (mit den Anspruchstyp, den Sie in der benutzerdefinierten Regel erstellt haben), und klicken Sie auf **Fertig stellen**.

	![](./media/web-sites-dotnet-lob-application-adfs/7-transient-name-id.png)

	Ausführliche Informationen zu den Schritten zum Erstellen des oben genannten vorübergehenden Namens-ID-Anspruchs finden Sie unter [Name Identifiers in SAML assertions](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx) (Namens-IDs in SAML-Assertionen, in englischer Sprache).

12.	Klicken Sie im Dialogfeld **Anspruchsregeln bearbeiten** auf **Übernehmen**. Das Dialogfeld sollte jetzt wie im folgenden Screenshot aussehen:

	![](./media/web-sites-dotnet-lob-application-adfs/8-all-claim-rules.png)

	> [AZURE.NOTE] Wiederholen Sie diese Schritte erneut für die Debugumgebung und die veröffentlichte Website.

<a name="bkmk_test"></a>
## Testen Sie die Verbundauthentifizierung für Ihre Anwendung.

Jetzt sind Sie bereit, die Authentifizierungslogik Ihrer Anwendung für AD FS zu testen. In meiner AD FS-Testumgebung habe ich einen Testbenutzer, der einer Testgruppe in Active Directory (AD) angehört.

![](./media/web-sites-dotnet-lob-application-adfs/10-test-user-and-group.png)

Um die Authentifizierung im Debugger zu testen, müssen Sie jetzt lediglich `F5` drücken. Wenn Sie die Authentifizierung in der veröffentlichten Website testen möchten, navigieren Sie zu der URL.

Klicken Sie nach dem Laden der Webanwendung auf **Anmelden**. Je nach der von AD FS gewählten Authentifizierungsmethode wird nun ein Dialogfeld zum Anmelden oder die von AD FS bereitgestellte Anmeldeseite angezeigt. So sieht das im Internet Explorer 11 aus.

![](./media/web-sites-dotnet-lob-application-adfs/9-test-debugging.png)

Nachdem Sie sich mit einem Benutzer in der AD-Domäne der AD FS-Bereitstellung anmelden, sollte nun erneut die Homepage mit der Begrüßung **Hallo <Benutzername>!** angezeigt werden. So sieht das bei mir aus.

![](./media/web-sites-dotnet-lob-application-adfs/11-test-debugging-success.png)

Bisher haben Sie Folgendes erreicht:

- Die Anwendung hat AD FS erreicht, und eine entsprechende RP-ID wird in der AD FS-Datenbank gefunden.
- AD FS hat einen Active Directory-Benutzer authentifiziert und leitet Sie zur Startseite der Anwendung zurück.
- AD FS hat den Namensanspruch (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name) an Ihre Anwendung gesendet. Dies geht daraus hervor, dass der Benutzername in der Ecke angezeigt wird. 

Wenn der Namensanspruch fehlt, würde **Hallo !** angezeigt. Wenn Sie die Datei "Views\Shared\_LoginPartial.cshtml" betrachten, werden Sie feststellen, dass Sie  `User.Identity.Name` verwendet, um den Benutzernamen anzuzeigen. Wie bereits erwähnt, füllt ASP.NET diese Eigenschaft mit den Namensanspruch des authentifizierten Benutzers, wenn dieser im SAML-Token verfügbar ist. Um alle Ansprüche zu sehen, die von AD FS gesendet werden, setzen Sie in der Datei "Controllers\HomeController.cs" einen Haltepunkt in der Index-Aktionsmethode. Nachdem der Benutzer authentifiziert wurde, überprüfen Sie die Auflistung  `System.Security.Claims.Current.Claims`.

![](./media/web-sites-dotnet-lob-application-adfs/12-test-debugging-all-claims.png) 

<a name="bkmk_authorize"></a>
## Autorisieren von Benutzern für bestimmte Domänencontroller oder Aktionen

Da Sie Gruppenmitgliedschaften als Rollenansprüche in die RP-Vertrauensstellungskonfiguration aufgenommen haben, können Sie diese jetzt direkt in der Dekoration `[Authorize(Roles="...")]` für Controller und Aktionen verwenden. In einer Line-of-Business-Anwendung mit dem Muster Create-Read-Update-Delete (CRUD) können Sie bestimmten Rollen Zugriff auf die einzelnen Aktionen gewähren. Vorläufig werden Sie diese Funktion auf dem vorhandenen Home-Controller lediglich testen.

1. Öffnen Sie die Datei "Controllers\HomeController.cs".
2. Dekorieren Sie die Aktionsmethoden `About` und `Contact` ähnlich wie unten mit Mitgliedschaften in Sicherheitsgruppen, über die Ihr authentifizierter Benutzer verfügt.  
	<pre class="prettyprint">
    <mark>[Authorize(Roles="Test Group")]</mark>
    public ActionResult About()
    {
        ViewBag.Message = "Your application description page.";

        return View();
    }

    <mark>[Authorize(Roles="Domain Admins")]</mark>
    public ActionResult Contact()
    {
        ViewBag.Message = "Your contact page.";

        return View();
    }
	</pre>

	Da ich fügte **Testbenutzer** zu **Testgruppe** in meiner AD FS-Testumgebung hinzugefügt habe, verwende ich Testgruppe zum Testen der Autorisierung in `About`. Für `Contact` teste ich den negativen Fall **Domänenadministratoren**, eine Gruppe, der der **Testbenutzer** nicht angehört.

3. Starten Sie den Debugger durch Eingabe von `F5`, melden Sie sich an, und klicken Sie auf **Info**. Sie sollten jetzt die Seite `~/About/Index` sehen können, wenn der authentifizierte Benutzer für diese Aktion berechtigt ist.
4. Klicken Sie nun auf **Kontakt**. In meinem Fall sollte keine Autorisierung von **Testbenutzer** für die Aktion erfolgen. Allerdings wird der Browser zu AD FS umgeleitet, und schließlich wird diese Meldung angezeigt:

	![](./media/web-sites-dotnet-lob-application-adfs/13-authorize-adfs-error.png)

	Wenn Sie diesen Fehler in der Ereignisanzeige auf dem AD FS-Server untersuchen, sehen Sie diese Ausnahmemeldung:  
	<pre class="prettyprint">
	Microsoft.IdentityServer.Web.InvalidRequestException: MSIS7042: <mark>The same client browser session has made '6' requests in the last '11' seconds.</mark> Contact your administrator for details.
	   at Microsoft.IdentityServer.Web.Protocols.PassiveProtocolHandler.UpdateLoopDetectionCookie(WrappedHttpListenerContext context)
	   at Microsoft.IdentityServer.Web.Protocols.WSFederation.WSFederationProtocolHandler.SendSignInResponse(WSFederationContext context, MSISSignInResponse response)
	   at Microsoft.IdentityServer.Web.PassiveProtocolListener.ProcessProtocolRequest(ProtocolContext protocolContext, PassiveProtocolHandler protocolHandler)
	   at Microsoft.IdentityServer.Web.PassiveProtocolListener.OnGetContext(WrappedHttpListenerContext context)
	</pre>

	Der Grund hierfür ist, dass MVC standardmäßig die Fehlermeldung "401 - Nicht autorisiert" zurückgibt, wenn die Rollen des Benutzers nicht autorisiert sind. Dadurch wird eine Anforderung zur erneuten Authentifizierung an Ihren Identitätsanbieter (AD FS) ausgelöst. Da der Benutzer bereits authentifiziert ist, kehrt AD FS zur selben Seite zurück- Diese gibt dann wiederum eine 401-Fehlermeldung aus, sodass eine Umleitungsschleife entsteht. Überschreiben Sie die  `HandleUnauthorizedRequest`-Methode von AuthorizeAttribute mit einfacher Logik, um etwas Sinnvolles anzuzeigen, anstatt die Umleitungsschleife fortzusetzen.

5. Erstellen Sie im Projekt die Datei "AuthorizeAttribute.cs", und fügen Sie den folgenden Code ein.

		using System;
		using System.Web.Mvc;
		using System.Web.Routing;
		
		namespace WebApp_WSFederation_DotNet
		{
		    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
		    public class AuthorizeAttribute : System.Web.Mvc.AuthorizeAttribute
		    {
		        protected override void HandleUnauthorizedRequest(AuthorizationContext filterContext)
		        {
		            if (filterContext.HttpContext.Request.IsAuthenticated)
		            {
		                filterContext.Result = new System.Web.Mvc.HttpStatusCodeResult((int)System.Net.HttpStatusCode.Forbidden);
		            }
		            else
		            {
		                base.HandleUnauthorizedRequest(filterContext);
		            }
		        }
		    }
		}

	Der überschreibende Code sendet die Fehlermeldung "HTTP 403 (Verboten)" anstelle von "HTTP 401 (Nicht autorisiert)", wenn zwar eine Authentifizierung, jedoch keine Autorisierung vorliegt.

6. Führen Sie den Debugger erneut mit `F5` aus. Wenn Sie auf **Kontakt** klicken, zeigt jetzt eine informativere (allerdings unschöne) Fehlermeldung angezeigt:

	![](./media/web-sites-dotnet-lob-application-adfs/14-unauthorized-forbidden.png)

7. Veröffentlichen Sie die Anwendung erneut auf Ihrer Azure-Website, und testen Sie das Verhalten der Live-Anwendung.

<a name="bkmk_data"></a>
## Herstellen einer Verbindung mit lokalen Daten

Ein Grund dafür, Ihre Line-of-Business-Anwendung mit AD FS statt mit Azure Active Directory zu implementieren, sind Kompatibilitätsprobleme beim externen Speichern von Organisationsdaten. Dies kann auch bedeuten, dass Ihre Azure-Website auf lokale Datenbanken zugreifen muss, da Sie [SQL-Datenbank](http://azure.microsoft.com/services/sql-database/) nicht als Datenebene für Ihre Websites verwenden dürfen.

Azure-Websites unterstützt zwei Methoden für den Zugriff auf lokale Datenbanken: [Hybridverbindungen](http://azure.microsoft.com/documentation/articles/integration-hybrid-connection-overview/) und [virtuelle Netzwerke](http://azure.microsoft.com/documentation/articles/web-sites-integrate-with-vnet/). Weitere Informationen finden Sie unter [Using VNET integration and Hybrid connections with Azure Websites](http://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/) (Verwenden der VNET-Integration und von Hybridverbindungen in Azure-Websites, in englischer Sprache).

<a name="bkmk_resources"></a>
## Weitere Ressourcen

- [Schützen der Anwendung durch SSL und das Attribut Authorize](../web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/#protect-the-application-with-ssl-and-the-authorize-attribute)
- [Authenticate and Authorize Users in LOB Applications in Azure Websites](../web-sites-authentication-authorization/) (Authentifizieren und Autorisieren von Benutzern in LOB-Anwendungen in Azure-Websites, in englischer Sprache)
- [Create an ASP.NET MVC Line-of-Business Application in Azure Websites that Authenticates with Azure Active Directory](../web-sites-dotnet-lob-application-azure-ad/) (Erstellen einer ASP.NET-MVC-LOB-Anwendung in Azure-Websites, die mit Azure Active Directory authentifiziert wird, in englischer Sprache)
- [Use the On-Premises Organizational Authentication Option (ADFS) With ASP.NET in Visual Studio 2013](http://www.cloudidentity.com/blog/2014/02/12/use-the-on-premises-organizational-authentication-option-adfs-with-asp-net-in-visual-studio-2013/) (Verwenden der lokalen Organisationsauthentifizierungsoption (ADFS) mit ASP.NET in Visual Studio 2013, in englischer Sprache)
- [Blog von Vittorio Bertocci](http://blogs.msdn.com/b/vbertocci/)
- [Migrate a VS2013 Web Project From WIF to Katana](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/) (Migrieren eines VS2013-Webprojekts von WIF nach Katana, in englischer Sprache)
- [Active Directory-Verbunddienste (Übersicht)](http://technet.microsoft.com/ library/hh831502.aspx)
- [WS-Federation 1.1 specification](http://download.boulder.ibm.com/ibmdl/pub/software/dw/specs/ws-fed/WS-Federation-V1-1B.pdf?S_TACT=105AGX04&S_CMP=LP) (WS-Verbund 1.1 - Spezifikationen, in englischer Sprache)

<!--HONumber=45--> 