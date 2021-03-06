<properties 
	pageTitle="Bottle und Azure-Tabellenspeicher in Azure mit Python Tools 2.2 für Visual Studio" 
	description="Erfahren Sie, wie Sie mithilfe der Python-Tools für Visual Studio eine Bottle-Anwendung erstellen, die Daten im Azure-Tabellenspeicher speichert, und die Web-App für Azure App Service-Web-Apps bereitstellen." 
	services="app-service\web" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="11/18/2015"
	ms.author="huvalo"/>


# Bottle und Azure-Tabellenspeicher in Azure mit Python Tools 2.2 für Visual Studio 

In diesem Tutorial erstellen wir mit [Python-Tools für Visual Studio] eine einfache Web-App für Umfragen mithilfe einer der PTVS-Beispielvorlagen. Dieses Tutorial steht auch als [Video](https://www.youtube.com/watch?v=GJXDGaEPy94) zur Verfügung.

Die Umfrage-Web-App definiert für ihr Repository eine Abstraktion. Sie können also einfach zwischen unterschiedlichen Repository-Typen wechseln (In-Memory, Azure-Tabellenspeicher, MongoDB).

Sie erfahren, wie Sie ein Azure-Speicherkonto erstellen, die Web-App für die Nutzung von Azure Table Storage konfigurieren und sie für [Azure App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) veröffentlichen.

Weitere Artikel finden Sie im [Python Developer Center], wo die Entwicklung von Azure App Service-Web-Apps mit PTVS unter Einsatz der Webframeworks Bottle, Flask und Django mithilfe der Datenbankdienste MongoDB, Azure-Tabellenspeicher, MySQL und SQL behandelt wird. Der Schwerpunkt dieses Artikels liegt zwar auf App Service, die Schritte sind jedoch vergleichbar mit der Entwicklung von [Azure Cloud Services].

## Voraussetzungen

 - Visual Studio 2013 oder 2015
 - [Python Tools 2.2 für Visual Studio]
 - [Python Tools 2.2 für Visual Studio, Beispiel-VSIX]
 - [Azure SDK-Tools für VS 2013] oder [Azure SDK-Tools für VS 2015]
 - [Python 2.7 32-Bit] oder [Python 3.4 32-Bit]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Erstellen des Projekts

In diesem Abschnitt erstellen wir ein Visual Studio-Projekt mithilfe einer Beispielvorlage. Wir erstellen eine virtuelle Umgebung und installieren die erforderlichen Pakete. Dann führen wir die App lokal aus und verwenden dabei das Standard-Repository im Arbeitsspeicher.

1.  Wählen Sie in Visual Studio **Datei** -> **Neues Projekt** aus.

1.  Die Projektvorlagen aus der PTVS-Beispiel-VSIX-Datei stehen unter **Python**, **Beispiele** bereit. Wählen Sie **Polls Bottle-Webprojekt** aus, und klicken Sie auf "OK", um das Projekt zu erstellen.

  	![Dialogfeld "Neues Projekt"](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleNewProject.png)

1.  Sie werden aufgefordert, externe Pakete zu installieren. Wählen Sie **In einer virtuellen Umgebung installieren** aus.

  	![Dialogfeld für externe Pakete](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleExternalPackages.png)

1.  Wählen Sie **Python 2.7** oder **Python 3.4** als Basisinterpreter.

  	![Dialogfeld für das Hinzufügen der virtuellen Umgebung](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAddVirtualEnv.png)

1.  Drücken Sie `F5`, um sicherzustellen, dass die Anwendung funktioniert. Standardmäßig verwendet die Anwendung ein Repository im Arbeitsspeicher, das nicht konfiguriert werden muss. Wird der Server angehalten, gehen alle Daten verloren.

1.  Klicken Sie auf **Beispielumfragen erstellen**, und klicken Sie dann auf eine Umfrage und Abstimmung.

  	![Webbrowser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleInMemoryBrowser.png)

## Erstellen eines Azure-Speicherkontos

Für Speichervorgänge benötigen Sie ein Azure-Speicherkonto. Mit den folgenden Schritten können Sie ein Speicherkonto erstellen.

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Klicken Sie oben links im Portal auf das Symbol **Neu** und dann auf **Daten und Speicher** > **Speicherkonto**. Klicken Sie auf die Schaltfläche **Erstellen**, geben Sie dem Speicherkonto einen eindeutigen Namen, und erstellen Sie eine neue [Ressourcengruppe](../resource-group-overview.md) dafür.

  	<!-- ![New Button](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzurePlusNew.png) -->

	Wenn das Speicherkonto erstellt wurde, leuchtet auf der Schaltfläche **Benachrichtigungen** grün **ERFOLG**. Das Blatt des Speicherkontos wird geöffnet, und darauf wird angezeigt, dass es zu der neu erstellten Ressourcengruppe gehört.

  	<!-- ![Quick Create](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzureStorageCreate.png) -->

5. Klicken Sie auf dem Blatt des Speicherkontos auf den Abschnitt **Einstellungen**. Notieren Sie sich den Kontonamen und den Primärschlüssel.

	Wir benötigen diese Informationen im nächsten Abschnitt zum Konfigurieren des Projekts.

## Konfigurieren des Projekts

In diesem Abschnitt konfigurieren wir unsere Anwendung, sodass sie das eben erstellte Speicherkonto verwendet. Anschließend führen wir die Anwendung lokal aus.

1.  Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Eigenschaften** aus. Klicken Sie auf die Registerkarte **Debuggen**.

  	![Debugeinstellungen für das Projekt](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureTableStorageProjectDebugSettings.png)

1.  Legen Sie die Werte der von der App benötigten Umgebungsvariablen in **Serverbefehl debuggen**, **Umgebung** fest.

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<storage account name>
        STORAGE_KEY=<primary access key>

    Damit werden die Umgebungsvariablen festgelegt, wenn Sie das **Debugging starten**. Sollen die Variablen dann festgelegt werden, wenn Sie **ohne Debugging starten**, legen Sie dieselben Werte auch unter **Serverbefehl ausführen** fest.

    Alternativ können Sie Umgebungsvariablen mithilfe der Windows Systemsteuerung definieren. Das ist eine bessere Option wenn Sie das Speichern von Anmeldeinformationen im Quellcode bzw. der Projektdatei vermeiden möchten. Beachten Sie bitte, dass Sie Visual Studio neu starten müssen, damit die Umgebungswerte in der Anwendung verfügbar sind.

1.  Der Code, der das Azure Table Storage-Repository implementiert, befindet sich unter **models/azuretablestorage.py**. Weitere Informationen zur Nutzung des Tabellenspeicherdiensts in Python finden Sie in der [Dokumentation].

1.  Drücken Sie `F5`, um die Anwendung auszuführen. Die mittels **Beispielumfrage erstellen** erstellten Umfragen sowie die bei der Abstimmung erfassten Daten werden in Azure Table Storage serialisiert.

	> [AZURE.NOTE]Die virtuelle Umgebung für Python 2.7 wird möglicherweise eine unerwartete Unterbrechung in Visual Studio verursachen. Drücken Sie `F5`, um das Laden des Webprojekts fortzusetzen.

1.  Navigieren Sie zur Seite **Info**, und prüfen Sie, ob die Anwendung das Repository **Azure Table Storage** verwendet.

  	![Webbrowser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureTableStorageAbout.png)

## Untersuchen von Azure Table Storage

Das Anzeigen und Bearbeiten von Speichertabellen in Visual Studio ist mit Server-Explorer ganz einfach. In diesem Abschnitt verwenden wir Server-Explorer für die Anzeige der Inhalte aus den Tabellen der Umfrage-App.

> [AZURE.NOTE]Dafür müssen die Microsoft Azure-Tools installiert sein, die als Teil des [Azure SDK für .NET] erhältlich sind.

1.  Öffnen Sie **Server-Explorer**. Erweitern Sie **Azure**, **Speicher**, Ihr Speicherkonto und dann **Tabellen**.

  	<!-- ![Server Explorer](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorer.png) -->

1.  Doppelklicken Sie auf die Tabelle **polls** oder **choices**, sodass Sie den Inhalt der Tabelle in einem Dokumentenfenster sehen und Entitäten hinzufügen, entfernen oder bearbeiten können.

  	<!-- ![Table Query Results](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorerTable.png) -->

## Veröffentlichen der Web-App in Azure App Service

Das Azure .NET SDK bietet eine einfache Möglichkeit zum Bereitstellen Ihrer Web-App in Azure App Service.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Veröffentlichen** aus.

  	<!-- ![Publish Web Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonPublishWebSiteDialog.png) -->

1.  Klicken Sie auf **Microsoft Azure-Web-Apps**.

1.  Klicken Sie auf **Neu**, um eine neue Web-App zu erstellen.

1.  Füllen Sie die folgenden Felder aus, und klicken Sie auf **Erstellen**.
	-	**Name der Web-App**
	-	**App Service-Plan**
	-	**Ressourcengruppe**
	-	**Region**
	-	Lassen Sie für **Datenbankserver** die Option **Keine Datenbank** festlegt.

  	<!-- ![Create Web App on Microsoft Azure Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonCreateWebSite.png) -->

1.  Übernehmen Sie alle anderen Standardwerte, und klicken Sie auf **Veröffentlichen**.

1.  Ihr Webbrowser öffnet automatisch mit der veröffentlichten Web-App. Auf der Infoseite sehen Sie, dass als Repository nicht **Azure Table Storage**, sondern **In-Memory** verwendet wird.

    Das liegt daran, dass die Umgebungsvariablen nicht für die Web-Apps-Instanz in Azure App Service festgelegt wurden. Daher werden die in **settings.py** festgelegten Standardwerte verwendet.

## Konfigurieren der Web-Apps-Instanz

In diesem Abschnitt konfigurieren wir die Umgebungsvariablen für die Web-Apps-Instanz.

1.  Öffnen Sie im [Azure-Portal] das Blatt der Web-App, indem Sie auf **Durchsuchen** > **Web-Apps** und anschließend auf den Namen Ihrer Web-App klicken.

1.  Klicken Sie auf dem Blatt Ihrer Web-App auf **Alle Einstellungen** und anschließend auf **Anwendungseinstellungen**.

  	<!-- ![Top Menu](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteTopMenu.png) -->

1.  Navigieren Sie nach unten zum Abschnitt **App-Einstellungen**, und legen Sie die Werte für **REPOSITORY\_NAME**, **STORAGE\_NAME** und **STORAGE\_KEY** gemäß der Beschreibung im Abschnitt **Konfigurieren des Projekts** fest.

  	<!-- ![App Settings](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png) -->

1. Klicken Sie auf **SPEICHERN**, auf **NEU STARTEN** und dann auf **DURCHSUCHEN**.

  	<!-- ![Bottom Menu](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureBottomMenu.png) -->

1.  Die Web-App funktioniert nun wie erwartet und verwendet das Repository **Azure Table Storage**.

    Glückwunsch!

  	![Webbrowser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureBrowser.png)

## Nächste Schritte

Folgen Sie diesen Links, wenn Sie mehr über Python Tools für Visual Studio, Bottle und Azure Table Storage erfahren möchten.

- [Python Tools für Visual Studio – Dokumentation]
  - [Webprojekte]
  - [Cloud Service-Projekte]
  - [Remotedebugging in Microsoft Azure]
- [Dokumentation zu Bottle]
- [Azure Storage (in englischer Sprache)]
- [Azure-SDK für Python]
- [Verwenden des Tabellenspeicherdiensts in Python]

## Änderungen
* Hinweise zu den Änderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).


<!--Link references-->
[Python Developer Center]: /develop/python/
[Azure Cloud Services]: ../cloud-services-python-ptvs.md
[Dokumentation]: ../storage-python-how-to-use-table-storage.md
[Verwenden des Tabellenspeicherdiensts in Python]: ../storage-python-how-to-use-table-storage.md

<!--External Link references-->
[Azure-Portal]: https://portal.azure.com
[Azure SDK für .NET]: http://azure.microsoft.com/downloads/
[Python-Tools für Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 für Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=624025
[Python Tools 2.2 für Visual Studio, Beispiel-VSIX]: http://go.microsoft.com/fwlink/?LinkId=624025
[Azure SDK-Tools für VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK-Tools für VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools für Visual Studio – Dokumentation]: http://aka.ms/ptvsdocs
[Dokumentation zu Bottle]: http://bottlepy.org/docs/dev/index.html
[Remotedebugging in Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Webprojekte]: http://go.microsoft.com/fwlink/?LinkId=624027
[Cloud Service-Projekte]: http://go.microsoft.com/fwlink/?LinkId=624028
[Azure Storage (in englischer Sprache)]: http://azure.microsoft.com/documentation/services/storage/
[Azure-SDK für Python]: https://github.com/Azure/azure-sdk-for-python
 

<!---HONumber=AcomDC_1203_2015-->