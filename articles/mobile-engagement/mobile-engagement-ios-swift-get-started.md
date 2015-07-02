<properties 
	pageTitle="Erste Schritte mit Azure Mobile Engagement für iOS in Swift" 
	description="Erfahren Sie mehr über die Verwendung von Azure Mobile Engagement mit Analysefunktionen und Pushbenachrichtigungen für iOS-Apps."
	services="mobile-engagement" 
	documentationCenter="Mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="swift" 
	ms.topic="article" 
	ms.date="04/30/2015" 
	ms.author="piyushjo" />

# Erste Schritte mit Azure Mobile Engagement für iOS-Apps in Swift

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md) 
- [iOS - Obj C](mobile-engagement-ios-get-started.md) 
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md) 

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Engagement die Nutzung Ihrer App verstehen und Pushbenachrichtigungen an segmentierte Benutzer einer iOS-Anwendung senden können. 
In diesem Lernprogramm erstellen Sie eine leere iOS-App, die einfache Daten erfasst und Pushbenachrichtigungen mithilfe des Apple-Pushbenachrichtigungsdiensts (APNS) empfängt. Nach Abschluss dieses Lernprogramms können Sie Pushbenachrichtigungen an alle Geräte oder zielspezifische Benutzer basierend auf ihren Geräteeigenschaften übertragen.

In diesem Lernprogramm wird ein einfaches Übertragungsszenario mit Mobile Engagement dargestellt. Bearbeiten Sie auch das nachfolgende Lernprogramm, um mehr über die Verwendung von Mobile Engagement zur Adressierung von speziellen Benutzern und Gerätegruppen zu erfahren. 

Für dieses Lernprogramm ist Folgendes erforderlich:

+ XCode, den Sie aus dem MAC App Store installieren können
+ das [Mobile Engagement iOS SDK]
+ Ein Pushbenachrichtigungszertifikat (P12), das Sie im Apple Dev Center abrufen können

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Engagement-Lernprogramme für iOS-Apps. 

> [AZURE.IMPORTANT] Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Engagement-Lernprogramme für iOS-Apps. Damit Sie es abschließen können, ist ein aktives Azure-Konto erforderlich. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank"> Monat kostenlose Testversion</a>.

<!--
##<a id="register"></a>Aktivieren des Apple Push Notification Service (APNS)

[WACOM.INCLUDE [Aktivieren von Apple-Pushbenachrichtigungen](../../includes/enable-apple-push-notifications.md)]
-->

##<a id="setup-azme"></a>Einrichten von Mobile Engagement für Ihre App

1. Melden Sie sich beim Azure-Verwaltungsportal an, und klicken Sie im unteren Teil des Bildschirms auf **+NEW**.

2. Klicken Sie auf **App Services**, dann auf **Mobile Engagement** und anschließend auf **Erstellen**.

   	![][7]

3. Geben Sie in das Popupfenster, das angezeigt wird, die folgenden Informationen ein:
 
   	![][8]

	- **Name der Anwendung**: Geben Sie den Namen Ihrer Anwendung ein. Sie können jedes beliebige Zeichen verwenden.
	- **Plattform**: Wählen Sie die Zielplattform (**iOS**) für die App aus (wenn Ihre App auf mehrere Plattformen ausgerichtet ist, wiederholen Sie dieses Lernprogramm für die einzelnen Plattformen). 
	- **Ressourcenname der Anwendung**: Dies ist der Name, mit dem Sie über APIs und URLs auf diese Anwendung zugreifen können. Sie dürfen nur die herkömmlichen URL-Zeichen verwenden. Der automatisch generierte Name sollte einen entsprechenden Ansatzpunkt bieten. Sie sollten außerdem den Plattformnamen anfügen, um Namenskonflikte zu vermeiden, da dieser Name eindeutig sein muss.
	- **Standort**: Wählen Sie das Rechenzentrum, in dem diese App (und vor allem deren Sammlung) gehostet werden soll.
	- **Sammlung**: Wenn Sie bereits eine Anwendung erstellt haben, wählen Sie eine zuvor erstellte Sammlung; wählen Sie andernfalls "Neue Sammlung".
	- **Name der Sammlung**: Dieser steht für die Anwendungsgruppe. Dadurch wird außerdem sichergestellt, dass sich alle Ihre Apps in einer Gruppe befinden, wodurch aggregierte Berechnungen von Metriken möglich sind. Sie sollten hier, falls möglich, Ihren Firmen- oder Abteilungsnamen verwenden.

4. Wählen Sie die gerade erstellte Anwendung auf der Registerkarte **Anwendung** aus.

5. Klicken Sie auf **Verbindungsinformationen**, um die Verbindungseinstellungen für die SDK-Integration in Ihrer mobilen App anzuzeigen.
 
   	![][10]

6. Kopieren Sie die **Verbindungszeichenfolge** - Diese benötigen Sie zum Identifizieren dieser App im Anwendungscode und zum Verbinden mit Mobile Engagement von Ihrer Telefon-App aus.

   	![][11]

##<a id="connecting-app"></a>Verbinden Ihrer App mit dem Mobile Engagement-Back-End

In diesem Lernprogramm wird eine "einfache Integration" dargestellt. Dabei handelt es sich um den minimalen erforderlichen Satz zur Sammlung von Daten und zum Senden einer Pushbenachrichtigung. Die vollständige Dokumentation zur Integration finden Sie in der [Dokumentation zum Mobile Engagement iOS SDK].

Wir erstellen eine einfache App mit XCode, um die Integration zu demonstrieren:

###Erstellen eines neuen iOS-Projekts

Sie können diesen Schritt überspringen, wenn Sie bereits eine App besitzen und mit der iOS-Entwicklung vertraut sind.

1. Starten Sie Xcode, und wählen Sie im Popupfenster **Create a new Xcode project** aus.

   	![][12]

2. Wählen Sie jetzt **Single View Application** aus, und klicken Sie dann auf "Next".

   	![][14]

3. Füllen Sie die Felder **Product Name**, **Organization Name** und **Organisation Identifier** aus. Stellen Sie sicher, dass Sie als Sprache **Swift** ausgewählt haben. 

   	![][40]

Xcode erstellt die Demo-App, in die wir Mobile Engagement integrieren.

###Verbinden Sie Ihre App mit dem Mobile Engagement-Back-End. 

1. Laden Sie das [Mobile Engagement iOS SDK] herunter.
2. Extrahieren Sie die ".tar.gz"-Datei in einem Ordner auf Ihrem Computer.
3. Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie "Add files to ..." aus.

	![][17]

4. Navigieren Sie zu dem Ordner, in dem Sie das SDK extrahiert haben, wählen Sie den Ordner  `EngagementSDK` aus, und klicken Sie auf "OK".

	![][18]

5. Öffnen Sie die Registerkarte  `Build Phases`, und fügen Sie im Menü  `Link Binary With Libraries` die Frameworks, wie im Folgenden gezeigt, hinzu:

	![][19]

6. Erstellen Sie einen Bridging-Header, um die Objective-C-APIs des SDKs verwenden zu können, indem Sie "Datei > Neu > Datei > iOS > Quelle > Headerdatei" auswählen.

	![][41]

7. Bearbeiten Sie die Bridging-Headerdatei, um AzME Objective-C-Code für Ihren Swift-Code verfügbar zu machen, und fügen Sie die folgenden Importanweisungen hinzu:

		/* Mobile Engagement Agent */
		#import "AEModule.h"
		#import "AEPushDelegate.h"
		#import "AEPushMessage.h"
		#import "AEStorage.h"
		#import "EngagementAgent.h"
		#import "EngagementTableViewController.h"
		#import "EngagementViewController.h"
		#import "AEIdfaProvider.h"

8. Stellen Sie unter "Build Settings" sicher, dass die Buildeinstellung "Objective-C Bridging Header" unter "Swift-Compiler - Code Generation" einen Pfad zu diesem Header aufweist. Hier folgt ein Pfadbeispiel: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (je nach Pfad)**

9. Wechseln Sie wieder zum Azure-Portal zur Seite  *Connection Info* Ihrer App, und kopieren Sie die "Verbindungszeichenfolge".

	![][11]

10. Fügen Sie nun die Verbindungszeichenfolge in den  `didFinishLaunchingWithOptions`-Delegaten ein.		

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool
		{
  			[...]
				EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
  			[...]
		}

##<a id="monitor"></a>Aktivieren der Echtzeitüberwachung

Um mit dem Senden von Daten zu beginnen und sicherzustellen, dass die Benutzer aktiv sind, müssen Sie mindestens einen Bildschirm (Aktivität) an das Mobile Engagement-Back-End schicken. 

- Öffnen Sie die Datei  `ViewController.h`, importieren Sie  `EngagementViewController.h`, und ersetzen Sie die übergeordnete Klasse der  `ViewController`-Schnittstelle durch  `EngagementViewController`.

###Sicherstellen, dass Ihre App mit der Echtzeitüberwachung verbunden ist

In diesem Abschnitt erfahren Sie, wie Sie sicherstellen, dass Ihre App eine Verbindung mit dem Mobile Engagement-Back-End mithilfe der Echtzeitüberwachungsfunktion von Mobile Engagement herstellt.

1. Navigieren zum Mobile Engagement-Portal

	Stellen Sie über das Azure-Portal sicher, dass Sie sich in der App befinden, die wir für dieses Projekt verwenden, und klicken Sie dann am unteren Rand auf die Schaltfläche zum "Einstellen":

	![][26]

2. Sie gelangen dann zur Einstellungsseite des Mobile Engagement-Portals für Ihre App. Klicken Sie hier auf die Registerkarte "Überwachen":

	![][30]

3. Die Überwachung kann Ihnen ein beliebiges Gerät in Echtzeit anzeigen, das Ihre App startet.

	![][31]

4. Wenn Sie sich wieder in Xcode befinden, starten Sie Ihre App im Simulator oder auf einem angeschlossenen Gerät.

5. Wenn es funktioniert hat, sollte jetzt eine Sitzung in der Überwachung angezeigt werden! 

**Herzlichen Glückwunsch!** Sie haben den ersten Schritt dieses Lernprogramms abgeschlossen und verfügen über eine App, die eine Verbindung zum Mobile Engagement-Back-End herstellt, das bereits Daten sendet.

6. Wenn Sie auf die Starttaste des Simulators klicken, wird die Anzahl der Sitzungen im Monitor auf 0 zurückgesetzt, wie oben gezeigt.

	![][33]

##<a id="integrate-push"></a>Aktivieren von Pushbenachrichtigungen und In-App-Messaging

Mit Mobile Engagement können Sie mit Ihren Benutzern interagieren und diese mit Push-Benachrichtigungen und In-App-Nachrichten im Rahmen von Kampagnen ERREICHEN. Dieses Modul nennt sich REACH im Mobile Engagement-Portal.
In den folgenden Abschnitten wird Ihre App für den Empfang eingerichtet.

### Fügen Sie die Reach-Bibliothek zum Projekt hinzu.

1. Klicken Sie mit der rechten Maustaste auf Ihr Projekt.
2. Wählen Sie `Add file to ...` aus.
3. Navigieren Sie zu dem Ordner, in dem Sie das SDK extrahiert haben.
4. Wählen Sie den Ordner  `EngagementReach` aus.
5. Klicken Sie auf "Hinzufügen".
6. Bearbeiten Sie die Bridging-Headerdatei, um AzME Objective-C-Reach-Header verfügbar zu machen, und fügen Sie die folgenden Importanweisungen hinzu:

		/* Mobile Engagement Reach */
		#import "AE_TBXML.h"
		#import "AEAnnouncementViewController.h"
		#import "AEAutorotateView.h"
		#import "AEContentViewController.h"
		#import "AEDefaultAnnouncementViewController.h"
		#import "AEDefaultNotifier.h"
		#import "AEDefaultPollViewController.h"
		#import "AEInteractiveContent.h"
		#import "AENotificationView.h"
		#import "AENotifier.h"
		#import "AEPollViewController.h"
		#import "AEReachAbstractAnnouncement.h"
		#import "AEReachAnnouncement.h"
		#import "AEReachContent.h"
		#import "AEReachDataPush.h"
		#import "AEReachDataPushDelegate.h"
		#import "AEReachModule.h"
		#import "AEReachNotifAnnouncement.h"
		#import "AEReachPoll.h"
		#import "AEViewControllerUtil.h"
		#import "AEWebAnnouncementJsBridge.h"

### Ändern des Anwendungsdelegaten

1. Erstellen Sie innerhalb von  `didFinishLaunchingWithOptions` ein Reach-Modul, und übergeben Sie es an die vorhandene Engagement-Initialisierungszeile:

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
			let reach = AEReachModule.moduleWithNotificationIcon(UIImage(named:"icon.png")) as! AEReachModule
			EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
			[...]
			return true
		}	

### Aktivieren Ihrer App für den Empfang von APNS-Pushbenachrichtigungen.
1. Fügen Sie die folgende Zeile in die `didFinishLaunchingWithOptions` Methode ein:

		if application.respondsToSelector("registerUserNotificationSettings:")
		{
			application.registerUserNotificationSettings(UIUserNotificationSettings(
			forTypes: (UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound),
			categories: nil))
			application.registerForRemoteNotifications()
		}
		else
		{
			application.registerForRemoteNotificationTypes(UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound)
		}

2. Fügen Sie die  `didRegisterForRemoteNotificationsWithDeviceToken`-Methode wie folgt hinzu:

		func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData)
		{
			EngagementAgent.shared().registerDeviceToken(deviceToken)
		}

3. Fügen Sie die `didReceiveRemoteNotification` Methode wie folgt hinzu:

		func application(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject])
		{
			EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo)
		}

###Erteilen Sie Ihrem Pushzertifikat den Zugriff auf Mobile Engagement.

Damit Mobile Engagement Pushbenachrichtigungen in Ihrem Namen senden darf, müssen Sie den Zugriff auf Ihr Zertifikat gestatten. Dies wird durch Konfigurieren und Eingeben Ihres Zertifikats im Mobile Engagement-Portal erreicht. Stellen Sie sicher, dass Sie Ihr P12-Zertifikat erhalten, wie in der Apple-Dokumentation beschrieben.

1. Navigieren Sie zum Mobile Engagement-Portal. Stellen Sie sicher, dass Sie sich in der App befinden, die wir für dieses Projekt verwenden, und klicken Sie dann am unteren Rand auf die Schaltfläche "Engage":

	![][26]

2. Sie gelangen jetzt auf die Einstellungsseite des Mobile Engagement-Portals. Klicken Sie hier auf den Abschnitt "Systemeigener Push", um Ihr P12-Zertifikat einzugeben:

	![][27]

3. Wählen Sie Ihr P12-Zertifikat aus, laden Sie es hoch, und geben Sie anschließend Ihr Kennwort ein:

	![][28]

4. Fügen Sie jetzt Ihr Bereitstellungsprofil hinzu, und erstellen Sie Ihre App für ein Zielgerät.

Sie sind fertig und wir werden jetzt überprüfen, ob Sie diese einfache Integration ordnungsgemäß durchgeführt haben.

##<a id="send"></a>Senden von Benachrichtigungen an Ihre App

Wir erstellen jetzt eine einfache Pushbenachrichtigungskampagne, die eine Pushbenachrichtigung an die App sendet.

1. Navigieren Sie im Mobile Engagement-Portal zur Registerkarte "REACH".

2. Klicken Sie auf **Neue Ankündigung**, um Ihre Pushkampagne zu erstellen.
	
	![][35]

3. Richten Sie das erste Feld der Kampagne ein:

	![][36]

	- 	Wählen Sie einen beliebigen Namen für die Kampagne.
	- 	Wählen Sie für "Übermittlungszeit" die Option "Nur außerhalb der App" aus: Dies ist der einfache Apple-Pushbenachrichtigungstyp, der Text unterstützt.
	- 	Geben Sie in den Benachrichtigungstext zunächst den Titel ein, der bei der Pushübertragung als erste Zeile angezeigt wird.
	- 	Geben Sie dann Ihre Nachricht ein, die die zweite Zeile darstellt.


4. Navigieren Sie nach unten, und wählen Sie im Inhaltsbereich "Nur Benachrichtigung" aus.

	![][37]

5. Sie haben das Festlegen der Basiskampagne abgeschlossen, scrollen Sie nun nach unten und erstellen Sie Ihre Kampagne, um sie zu speichern!
![][38]

6. Als letzten Schritt aktivieren Sie die Kampagne.
![][39]

7. Jetzt sollte eine Pushbenachrichtigung auf dem Gerät angezeigt werden.

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553
[Dokumentation zum Mobile Engagement Android SDK]: http://go.microsoft.com/?linkid=9874682

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-swift-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-swift-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-ios-swift-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-swift-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-swift-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-swift-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-swift-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-swift-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-swift-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[26]: ./media/mobile-engagement-ios-swift-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-swift-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-swift-get-started/native-push-settings.png
[30]: ./media/mobile-engagement-ios-swift-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-swift-get-started/monitor.png
[33]: ./media/mobile-engagement-ios-swift-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-swift-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-swift-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-swift-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-swift-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-swift-get-started/campaign-activate.png
[40]: ./media/mobile-engagement-ios-swift-get-started/SwiftSelection.png
[41]: ./media/mobile-engagement-ios-swift-get-started/AddHeaderFile.png

<!--HONumber=54--> 