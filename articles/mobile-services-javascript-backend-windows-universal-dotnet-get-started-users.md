﻿<properties pageTitle="Erste Schritte mit der Authentifizierung (Windows Store) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/18/2014" ms.author="Glenn Gailey" />

# Hinzufügen von Authentifizierung zur Mobile Services-App

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]		

In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre universelle Windows-App authentifizieren. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1. [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2. [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3. [Hinzufügen von Authentifizierung zur App]
5. [Speichern von Authentifizierungstoken auf dem Client]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen. 

>[WACOM.NOTE]In diesem Thema erfahren Sie, wie Sie Benutzer in Windows Store- und Windows Phone Store 8.1-Apps authentifizieren. Informationen zu Windows Phone 8.0- und Windows Phone Silverlight 8.1-Apps finden Sie unter [Erste Schritte bei der Authentifizierung in Mobile Services](/de-de/documentation/articles/mobile-services-windows-phone-get-started-users).

>Dieses Lernprogramm demonstriert den von Mobile Services verwalteten Authentifizierungsfluss bei Verwendung einer Vielzahl von Identitätsanbietern. Diese Methode lässt sich einfach konfigurieren und unterstützt verschiedene Anbieter. Wenn Sie stattdessen Live Connect mit clientseitig verwalteter Authentifizierung verwenden und in Ihrer Windows Phone-App Single-Sign-on-Funktionalität realisieren möchten, finden Sie weitere Informationen unter [Einmalige Anmeldung für Windows Store-Apps mithilfe von Live Connect]. Bei Verwendung der clientseitig verwalteten Authentifizierung hat die App Zugriff auf zusätzliche Benutzerdaten, die vom Identitätsanbieter verwaltet werden. Sie können dieselben Benutzerdaten in Ihrem mobilen Dienst abrufen, indem Sie in Serverskripten die Funktion **user.getIdentities()** aufrufen. Weitere Informationen finden Sie in [diesem Blogeintrag](http://go.microsoft.com/fwlink/p/?LinkId=506605).

##<a name="register"></a> Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<ol start="5">
<li><p>(Optional) Führen Sie die unter <a href="/de-de/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung</a> beschriebenen Schritte aus</p>

    <div class="dev-callout"><b>Hinweis</b>
	<p>Dieser Schritt ist optional, da er nur für den Anmeldeanbieter für Microsoft-Konten relevant ist. Wenn Sie Ihre Windows Store-App-Paketinformationen bei Mobile Services registrieren, kann der Client die Anmeldeinformationen für Microsoft Account für eine einmalige Anmeldung verwenden. Wenn Sie dies nicht tun, werden Ihre Benutzer mit Microsoft Account-Login jedes Mal zur Anmeldung aufgefordert, wenn diese Anmeldemethode aufgerufen wird. Führen Sie diesen Schritt aus, wenn Sie den Identitätsanbieter den" Microsoft Account" verwenden möchten.</p>
    </div>
</li>
</ol>

##<a name="permissions"></a> Einschränken von Berechtigungen für authentifizierte Benutzer

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>Klicken Sie in Visual Studio mit der rechten Maustaste auf das Windows Store-Projekt für die TodoList-App und dann auf <strong>Als Startprojekt festlegen</strong>.</p></li>
<li><p>Öffnen Sie im gemeinsam genutzten Projekt die Projektdatei "App.xaml.cs", suchen Sie die Definition der <a href="http://msdn.microsoft.com/de-de/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a>-Instanz, und stellen Sie sicher, dass diese für den Zugriff auf den in Azure gehosteten mobilen Dienst konfiguriert ist.</p>
<div class="dev-callout"><strong>Hinweis</strong><p>Wenn Sie die Tools von Visual Studio verwenden, um die App mit einem mobilen Dienst zu verbinden, werden zwei Gruppen von <strong>MobileServiceClient</strong>-Definitionen erzeugt, und zwar eine pro Plattform. Dies ist ein guter Moment, den generierten Code zu vereinfachen, indem Sie die in <code>#if...#endif</code> gekapselten <strong>MobileServiceClient</strong>-Definitionen zu einer einzigen ungekapselten Definition zusammenfassen, die von beiden Versionen der App genutzt wird. Dies ist nicht erforderlich, wenn Sie die Schnellstart-App aus dem Azure-Verwaltungsportal heruntergeladen haben.</p></div>
</li> 
<li><p>Drücken Sie F5, um die Windows Store-App auszuführen. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde.</p>
   
   	<p>Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf Mobile Services zugreift und die <em>TodoItem</em>-Tabelle nun eine Authentifizierung verlangt.</p></li>
</ol>

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

##<a name="add-authentication"></a> Hinzufügen von Authentifizierung zur App

[WACOM.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)] 

##<a name="tokens"></a>Speichern des Authentifizierungstokens auf dem Client

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Nächste Schritte

Im nächsten Lernprogramm [Dienstseitige Autorisierung von Mobile Services-Benutzern][Autorisieren von Benutzern mit Skripts] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern. Erfahren Sie mehr über die Verwendung von Mobile Services mit .NET unter [Mobile Services .NET - Erstellen einer konzeptionellen Referenz].

<!-- Anchors. -->
[Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
[Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
[Hinzufügen von Authentifizierung zur App]: #add-authentication
[Speichern von Authentifizierungstoken auf dem Client]: #tokens
[Nächste Schritte]:#next-steps


<!-- URLs. -->
[Übermitteln einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Einmaliges Anmelden für Windows Store-Apps mit Live Connect]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started/
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-data/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
[Autorisieren von Benutzern mit Skripts]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts
[JavaScript und HTML]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-users/

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung]: /de-de/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/