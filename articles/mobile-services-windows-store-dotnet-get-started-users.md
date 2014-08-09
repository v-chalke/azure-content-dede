<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, FAcebook, GOogle, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="" editor="" />

Erste Schritte bei der Authentifizierung in Mobile Services
===========================================================

[Windows Store C\#](/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-users "Windows Store C#")[Windows Store JavaScript](/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-users "Windows Store JavaScript")[Windows Phone](/de-de/documentation/articles/mobile-services-windows-phone-get-started-users "Windows Phone")[iOS](/de-de/documentation/articles/mobile-services-ios-get-started-users "iOS")[Android](/de-de/documentation/articles/mobile-services-android-get-started-users "Android")[HTML](/de-de/documentation/articles/mobile-services-html-get-started-users "HTML")[Xamarin.iOS](/de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users "Xamarin.iOS")[Xamarin.Android](/de-de/documentation/articles/partner-xamarin-mobile-services-android-get-started-users "Xamarin.Android")
[.NET-Backend](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/ ".NET-Backend") | [JavaScript-Backend](/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/ "JavaScript-Backend")

In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre App authentifizieren. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

Sie können sich eine Videoversion dieses Lernprogramms ansehen, indem Sie rechts auf den Clip klicken.

[Lernprogramm ansehen](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services) [Video abspielen](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services)10:04:00

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1.  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services](#register)
2.  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer](#permissions)
3.  [Hinzufügen von Authentifizierung zur App](#add-authentication)

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services](/de-de/documentation/articles/mobile-services-windows-store-get-started/) abschließen.

> [WACOM.NOTE]Dieses Lernprogramm zeigt Ihnen die grundlegende Methode von Mobile Services zur Authentifizierung von Benutzern mithilfe einer Vielzahl von Identitätsanbietern. Diese Methode lässt sich einfach konfigurieren und unterstützt verschiedene Anbieter. Allerdings müssen Sie sich mit dieser Methode auch bei jedem Start Ihrer App anmelden. Wenn Sie stattdessen Live Connect für eine einmalige Anmeldung in Ihrer Windows Store App verwenden möchten, finden Sie hierzu weitere Information unter [Einmalige Anmeldung für Windows Store Apps mithilfe von Live Connect](/de-de/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on).

Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services
----------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  (Optional) Führen Sie die unter [Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung](/de-de/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/) beschriebenen Schritte durch.

    **Hinweis**

    Dieser Schritt ist optional, da er nur für Anmeldeanbieter für Microsoft Account relevant ist. Wenn Sie Ihre Windows Store-App-Paketinformationen bei Mobile Services registrieren, kann der Client die Anmeldeinformationen für Microsoft Account für eine einmalige Anmeldung verwenden. Wenn Sie dies nicht tun, werden Ihre Benutzer mit Microsoft Account-Login jedes Mal zur Anmeldung aufgefordert, wenn diese Anmeldemethode aufgerufen wird. Schließen Sie diesen Schritt ab, wenn Sie einen Identitätsanbieter für Microsoft Account verwenden möchten.

Einschränken von Berechtigungen für authentifizierte Benutzer
-------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  Öffnen Sie in Visual Studio 2012 Express für Windows 8 das Projekt, das Sie erstellt haben, als Sie das Lernprogramm [Erste Schritte mit Mobile Services](/de-de/documentation/articles/mobile-services-windows-store-get-started) abgeschlossen haben.

2.  Drücken Sie F5, um diese Schnellstart-basierte App auszuführen. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde.

    Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf den mobilen Dienst zugreift und die *TodoItem*-Tabelle nun eine Authentifizierung verlangt.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

Hinzufügen von Authentifizierung zur App
----------------------------------------

[WACOM.INCLUDE [mobile-services-windows-dotnet-authenticate-app](../includes/mobile-services-windows-dotnet-authenticate-app.md)]

Nächste Schritte
----------------

Im nächsten Lernprogramm [Dienstweite Autorisierung von Mobile Services-Benutzern](/de-de/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts) werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern. Erfahren Sie mehr über die Verwendung von Mobile Services mit .NET unter [Mobile Services .NET – Erstellen einer konzeptionellen Referenz](/de-de/develop/mobile/how-to-guides/work-with-net-client-library).
