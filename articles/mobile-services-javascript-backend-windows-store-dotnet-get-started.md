﻿<properties pageTitle="Erste Schritte mit Mobile Services für Windows Store-Apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C# or JavaScript. " metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="hero-article" ms.date="08/18/2014" ms.author="glenga" />

# <a name="getting-started"> </a>Erste Schritte mit Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

In diesem Lernprogramm wird gezeigt, wie Sie einer universellen Windows-App einen cloudbasierten Back-End-Dienst unter Verwendung von Azure Mobile Services hinzufügen. Universelle Windows-App-Lösungen beinhalten Projekte für Windows Store 8.1, Windows Phone Store 8.1-Apps und ein gemeinsames, geteiltes Projekt. Weitere Informationen finden Sie unter [Erstellen universeller Windows-Apps, die Windows und Windows Phone als Ziel verwenden](http://msdn.microsoft.com/de-de/library/windows/apps/xaml/dn609832.aspx).

In diesem Lernprogramm erstellen Sie sowohl einen neuen mobilen Dienst als auch eine einfache *To-Do-Listen*-App, die App-Daten in diesem neuen mobilen Dienst speichert. Der von Ihnen zu erstellende mobile Dienst verwendet JavaScript für die serverseitige Geschäftslogik. Informationen zum Erstellen eines mobilen Diensts, mit dem Sie serverseitige Geschäftslogik in den unterstützten .NET-Sprachen mithilfe von Visual Studio verfassen können, finden Sie in der .NET-Back-End-Version dieses Themas.

>[WACOM.NOTE]In diesem Thema wird das Erstellen eines neuen mobilen Dienstprojekts und einer universellen Windows-App mithilfe des Azure-Verwaltungsportals erläutert. In Visual Studio 2013 können Sie außerdem neue mobile Dienstprojekte zu einer vorhandenen Visual Studio-Lösung hinzufügen. Weitere Informationen finden Sie unter [Schnellstart: Hinzufügen von Mobile Services (JavaScript-Back-End)](http://msdn.microsoft.com/de-de/library/windows/apps/xaml/dn263180.aspx).

>Informationen zum Hinzufügen eines mobilen Diensts zu einem Windows Phone 8.0- oder Windows Phone Store 8.1-App-Projekt finden Sie unter [Erste Schritte mit Daten für Windows Phone](/de-de/documentation/articles/mobile-services-windows-phone-get-started-data).

[WACOM.INCLUDE [mobile-services-windows-universal-get-started](../includes/mobile-services-windows-universal-get-started.md)]

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Dienste, die Sie auch nach Ablauf der Testversion weiter nutzen können. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* [Visual Studio 2013 Express für Windows] 

## Erstellen eines neuen mobilen Dienstes

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Erstellen einer neuen universellen Windows-App

Sobald Sie den mobilen Dienst erstellt haben, können Sie einem einfachen Schnellstart im Verwaltungsportal folgen, um eine neue universelle Windows-App zu erstellen oder eine vorhandene Windows Store- oder Windows Phone-App für die Verbindung zum mobilen Dienst zu ändern. 

In diesem Abschnitt erstellen Sie eine neue universelle Windows-App, die sich mit dem mobilen Dienst verbindet.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

   
2. Klicken Sie auf der Registerkarte "Schnellstart" unter **Plattform auswählen** auf **Windows**, und erweitern Sie **Neue Windows Store-App erstellen**.

   	![](./media/mobile-services-javascript-backend-windows-store-dotnet-get-started/mobile-portal-quickstart.png)

   	Auf diese Weise werden die drei einfachen Schritte zum Erstellen einer Windows Store-App, die mit dem mobilen Dienst verbunden ist, angezeigt.

  	![](./media/mobile-services-javascript-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png)

3. Falls noch nicht geschehen, sollten Sie [Visual Studio 2013 Express für Windows] auf den lokalen Computer oder virtuellen Computer herunterladen und installieren.

4. Klicken Sie auf **TodoItems-Tabelle erstellen**, um eine Tabelle zum Speichern der App-Daten zu erstellen.

5. Wählen Sie unter **Ihre App herunterladen und ausführen** eine Sprache für Ihre App, und klicken Sie dann auf **Download**. 

  	Daraufhin wird das Projekt für die *To do list*-Beispielanwendung heruntergeladen, die mit dem mobilen Dienst verbunden ist. Speichern Sie die komprimierte Projektdatei auf Ihrem lokalen Computer, und notieren Sie sich den Speicherort.

## Ausführen der Windows-App

[WACOM.INCLUDE [mobile-services-javascript-backend-run-app](../includes/mobile-services-javascript-backend-run-app.md)]

>[WACOM.NOTE]Sie können den Code überprüfen, der auf Ihren Mobile Service zugreift, um Daten abzufragen und einzufügen. Sie finden ihn in der Datei "MainPage.xaml.cs".

## Nächste Schritte
Da Sie den Schnellstart jetzt abgeschlossen haben, erfahren Sie, wie zusätzliche wichtige Aufgaben in Mobile Services ausgeführt werden: 

* [Erste Schritte mit Daten]
  <br/>Informationen über das Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit der Offline-Datensynchronisierung]
  <br/>Erfahren Sie mehr darüber, wie Sie die Offline-Datensynchronisierung verwenden können, um die App reaktionsfähig und stabil zu machen.

* [Erste Schritte mit der Authentifizierung]
  <br/>Informationen über die Authentifizierung von Benutzern der App mit einem Identitätsanbieter.

* [Erste Schritte mit Pushbenachrichtigungen] 
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.

Weitere Informationen zu universellen Windows-Apps finden Sie unter [Unterstützen mehrerer Geräteplattformen durch einen einzelnen mobilen Dienst](/de-de/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs).

<!-- Anchors. -->
[Erste Schritte mit Mobile Services]:#getting-started
[Erstellen eines neuen mobilen Dienstes]:#create-new-service
[Definieren der mobilen Dienstinstanz]:#define-mobile-service-instance
[Nächste Schritte]:#next-steps

<!-- Images. -->



<!-- URLs. -->
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
[Erste Schritte mit der Offline-Datensynchronisierung]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-users
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push
[Visual Studio 2013 Express für Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Verwaltungsportal]: https://manage.windowsazure.com/
[Erste Schritte mit Daten in Mobile Services mit Visual Studio 2012]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012