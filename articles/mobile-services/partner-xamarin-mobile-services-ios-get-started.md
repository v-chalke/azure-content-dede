<properties
	pageTitle="Erste Schritte mit Mobile Services für Xamarin iOS-Apps"
	description="Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung von Azure Mobile Services für die Xamarin iOS-Entwicklung."
	services="mobile-services"
	documentationCenter="xamarin"
	authors="conceptdev"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="11/22/2014"
	ms.author="craig.dunn@xamarin.com"/>

# <a name="getting-started"> </a>Erste Schritte mit Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

In diesem Lernprogramm erfahren Sie, wie Sie mit den Azure Mobile Services einen cloudbasierten Back-End-Dienst zu einer Xamarin.iOS-App hinzufügen können. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache <em>To-Do-Listen</em>-App, die App-Daten im neuen mobilen Dienst speichert.

Wenn Sie lieber ein Video zu diesem Thema ansehen möchten, können Sie den Clip unten auswählen. In diesem werden dieselben Schritte behandelt wie in diesem Lernprogramm.

Video: "Erste Schritte mit Xamarin und Azure Mobile Services" mit Craig Dunn, Developer Evangelist für Xamarin (Dauer: 10:05 Min.)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]



Unten sehen Sie einen Screenshot aus der fertigen App:

![][0]

Für das Abschließen dieses Lernprogramms sind XCode und [Xamarin Studio] für OS X oder das Xamarin Visual Studio-Plug-In für Visual Studio unter Windows erforderlich. Das Beispiel wird unter iOS 5.0 und höher ausgeführt.

> [AZURE.IMPORTANT] Sie benötigen ein Azure-Konto, um dieses Lernprogramm durchführen zu können. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Dienste, die Sie auch nach Ablauf der Testversion weiter nutzen können. Einzelheiten finden Sie unter [1 Monat kostenlose Testversion](http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-xamarin-ios%2F"%20target="_blank).

## <a name="create-new-service"> </a>Erstellen eines neuen mobilen Diensts

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

<h2>Erstellen einer neuen Xamarin.iOS-App</h2>

Sobald Sie den mobilen Dienst erstellt haben, können Sie einem einfachen Schnellstart im Verwaltungsportal folgen, um eine neue App zu erstellen oder eine vorhandene App für die Verbindung zum mobilen Dienst zu ändern.

In diesem Abschnitt erstellen Sie eine neue Xamarin.iOS-App, die mit dem mobilen Dienst verbunden ist.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2. Klicken Sie auf der Schnellstartregisterkarte unter **Plattform auswählen** auf **Xamarin.iOS**, und erweitern Sie dann **Neue Xamarin.iOS-App erstellen**.

	![][6]

	Dadurch werden drei einfache Schritte zum Erstellen einer Xamarin.iOS-App angezeigt, die mit dem mobilen Dienst verbunden wird.

  	![][7]

3. Falls noch nicht geschehen, laden Sie XCode (wir empfehlen die neueste Version, XCode 6.0 oder höher) und [Xamarin Studio] herunter, und installieren Sie beide Komponenten.

4. Klicken Sie auf **TodoItems-Tabelle erstellen**, um eine Tabelle zum Speichern der App-Daten zu erstellen.

5. Klicken Sie unter **App herunterladen und ausführen** auf **Herunterladen**.

	Dadurch wird das Projekt für die Beispielanwendung der To-do-Liste heruntergeladen, die mit dem mobilen Dienst verbunden ist und auf die Azure Mobile Services-Komponente für Xamarin.iOS verweist. Speichern Sie die komprimierte Projektdatei auf Ihrem lokalen Computer, und notieren Sie sich den Speicherort.

<h2>Ausführen der neuen Xamarin.iOS-App</h2>

Der letzte Schritt dieses Lernprogramms besteht im Erstellen und Ausführen der neuen App.

1. Navigieren Sie zu dem Verzeichnis, in dem Sie die komprimierten Projektdateien gespeichert haben, erweitern Sie die Dateien auf Ihrem Computer, und öffnen Sie die Datei **XamarinTodoQuickStart.iOS.sln** mithilfe von Xamarin Studio oder Visual Studio.

	![][8]

	![][9]

2. Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen und die App im iPhone-Emulator zu starten (dies ist die Voreinstellung für dieses Projekt).

3. Geben Sie in der App einen sinnvollen Text, wie z. B. _Lernprogramm abschließen_ ein, und klicken Sie dann auf das Plus-Symbol (**+**).

	![][10]

	Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden von dem mobilen Dienst zurückgegeben, und die Daten werden in der Liste angezeigt.

	> [AZURE.NOTE] Sie können den Code überprüfen, der auf den Mobile Service zugreift, um Daten abzufragen und einzufügen. Sie finden ihn in der C#-Datei "TodoService.cs".

4. Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItems**.

	![][11]

	Nun können Sie die von der App in die Tabelle eingefügten Daten durchsuchen.

	![][12]


## Nächste Schritte
Da Sie den Schnellstart jetzt abgeschlossen haben, erfahren Sie, wie zusätzliche wichtige Aufgaben in Mobile Services ausgeführt werden:

* [Erste Schritte mit der Offline-Datensynchronisierung]
  <br/>Erfahren Sie, wie der Schnellstart die Offline-Datensynchronisierung verwendet, um die App reaktionsfähig und stabil zu machen.

* [Erste Schritte mit der Authentifizierung]
  <br/>Informationen über die Authentifizierung von Benutzern der App mit einem Identitätsanbieter.

* [Erste Schritte mit Pushbenachrichtigungen]
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.

<!-- Anchors. -->
[Erste Schritte mit Mobile Services]:#getting-started
[Erstellen eines neuen mobilen Diensts]:#create-new-service
[Definieren der mobilen Dienstinstanz]:#define-mobile-service-instance
[Nächste Schritte]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Erste Schritte mit Daten]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Erste Schritte mit der Offline-Datensynchronisierung]: /develop/mobile/tutorials/mobile-services-xamarin-ios-get-started-offline-data
[Erste Schritte mit der Authentifizierung]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Erste Schritte mit Pushbenachrichtigungen]: /develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Xamarin Studio]: http://xamarin.com/download
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Verwaltungsportal]: https://manage.windowsazure.com/


<!--HONumber=52--> 