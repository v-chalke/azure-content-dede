﻿<properties
	pageTitle="Erste Schritte mit Azure Mobile Apps für Xamarin Android-Apps - Azure Mobile App"
	description="Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung von Azure Mobile Apps für die Xamarin Android-Entwicklung."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="chrande"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="11/11/2014"
	ms.author="chrande"/>

# <a name="getting-started"> </a>Erstellen einer Xamarin Android-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../includes/app-service-mobile-selector-get-started-preview.md)]

In diesem Lernprogramm erfahren Sie, wie Sie mit Azure Mobile App einen cloudbasierten Backend-Dienst zu einer Xamarin Android-App hinzufügen. In diesem Lernprogramm erstellen Sie einen neuen .NET-Dienst und eine einfache To-Do-Listen-App, die App-Daten im .NET-Back-End speichert.

Unten sehen Sie einen Screenshot aus der fertigen App:

![][0]

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle weiteren Azure Mobile Apps-Lernprogramme für Xamarin Android-Apps.

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Apps, die Sie auch nach Ablauf der Testversion weiter nutzen können. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE] Wenn Sie in Azure App Service einsteigen möchten, ohne sich zuvor für ein Azure-Konto zu registrieren, wechseln Sie zur Website [App Service ausprobieren](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), auf der Sie eine mobile App mit kurzer Gültigkeitsdauer für Einsteiger in App Service erstellen können. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Erstellen eines neuen mobilen App-Back-Ends

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Erstellen einer neuen Xamarin Android-App

Sobald Sie das Mobile App-Back-End erstellt haben, können Sie einem einfachen Schnellstart im [Azure-Portal] folgen, um entweder eine neue App zu erstellen oder eine vorhandene App für die Verbindung zum mobilen App-Back-End zu ändern.

In diesem Lernprogramm laden Sie eine neue Xamarin Android-App und ein .NET-Back-End-Dienstprojekt für Ihre mobile App herunter.

1. Klicken Sie im Azure-Portal auf **Durchsuchen** und **Mobile App**, und klicken Sie anschließend auf die soeben erstellte mobile App.

2. Klicken Sie oben im Blatt auf **Client hinzufügen**, und erweitern Sie **Xamarin.Android**.

    ![][6]

    Auf diese Weise werden die drei einfachen Schritte zum Erstellen einer Xamarin Android-App angezeigt, die mit Ihrem mobilen App-Back-End verbunden ist.


3. Falls noch nicht geschehen, sollten Sie <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> auf dem lokalen Computer oder virtuellen Computer herunterladen und installieren.  

4. Sofern nicht bereits geschehen, laden Sie [Xamarin Studio] herunter, und installieren Sie es. Sie können auch Xamarin für Visual Studio verwenden.

5. Klicken Sie unter **Dienst herunterladen und in der Cloud veröffentlichen** auf **Herunterladen**.

  	Daraufhin wird eine Lösung heruntergeladen, die Projekte für den Mobile App-Back-End-Code und die To-Do-Listen-Beispielclientanwendung enthält, die mit dem mobilen App-Back-End verbunden ist. Speichern Sie die komprimierte Projektdatei auf dem lokalen Computer und merken Sie sich, wo Sie sie gespeichert haben.

6. Laden Sie das Veröffentlichungsprofil herunter, speichern Sie die heruntergeladene Datei auf dem lokalen Computer, und notieren Sie sich den Speicherort.

## Testen des mobilen App-Back-Ends

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Veröffentlichen des mobilen App-Back-Ends

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Ausführen der Xamarin Android-App

Der letzte Schritt dieses Lernprogramms besteht im Erstellen und Ausführen der neuen App.

1. Navigieren Sie entweder in Visual Studio oder in Xamarin Studio zum Clientprojekt innerhalb der Mobile App-Lösung.

	![][8]

	![][9]

2. Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen und die App zu starten. Sie werden zur Auswahl eines Emulators oder eines angeschlossenen USB-Geräts aufgefordert.

	> [AZURE.NOTE] Sie müssen mindestens ein Android Virtual Device (AVD) definieren, um das Projekt im Android-Emulator auszuführen. Verwenden Sie den AVD Manager, um diese Geräte zu erstellen und zu verwalten.

3. Geben Sie in der App einen sinnvollen Text, wie z. B. _Lernprogramm abschließen_ ein, und klicken Sie dann auf das Plus-Symbol (**+**).

	![][10]

	Dadurch wird eine POST-Anforderung an das neue, in Azure gehosteten mobile App-Back-End gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden vom mobilen App-Back-End zurückgegeben, und die Daten werden in der Liste angezeigt.

	> [AZURE.NOTE]
   	> Sie können den Code überprüfen, der zum Abfragen und Einfügen von Daten auf das mobile App-Back-End zugreift. Der Code befindet sich in der C#-Datei "ToDoActivity.cs".



<!-- Images. -->
[0]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure-Portal]: https://azure.portal.com/
[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin für Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409

<!--HONumber=49-->