<properties
	pageTitle="FTP-Connector"
	description="Erste Schritte mit FTP-Connector"
	authors="anuragdalmia"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/05/2015"
	ms.author="rajeshramabathiran"/>

#FTP-Connector

##Übersicht
Mit dem FTP-Connector können Sie Daten von einem und auf einen FTP-Server verschieben. Der FTP-Connector besitzt folgende Hauptfunktionen:

- Abrufen von Dateien vom FTP-Server nach Bedarf
- Ausführen von Umfragen basierend auf einem konfigurierbaren Zeitplan
- Abfragen des FTP-Servers und Auslösen des Logikdatenflusses basierend auf neuen Dokumenten auf dem FTP-Server
- Angeben des FTP-Server als IP-Adresse, Port, Kennwort und Hostnamen
- Möglichkeit zum Ausführen von Sendevorgängen nach Bedarf
- Möglichkeit zum Löschen von Dateien auf dem FTP-Server nach Bedarf

##Erstellen eines neuen FTP-Connectors
Um einen neuen FTP-Connector zu erstellen, befolgen Sie die unten aufgeführten Schritte. – Starten Sie das Azure-Portal. – Öffnen Sie den Azure Marketplace über "+Neu" (unten auf der Seite) -> "Web + Mobil" -> "Azure Marketplace".

	![Launch Azure Marketplace][1]

- Klicken Sie auf die API-Apps.
- Suchen Sie nach "FTP", und wählen Sie "FTP-Connector".

	![Auswählen des FTP-Connectors][2]

- Klicken Sie auf "Erstellen".
- Geben Sie die folgenden Daten im daraufhin geöffneten Blatt zum FTP-Connector an.

	![Erstellen des FTP-Connectors][3]

- **Standort** – Wählen Sie den geografischen Standort, an dem Sie den Connector bereitstellen möchten.
- **Abonnement** – Wählen Sie ein Abonnement, in dem dieser Connector erstellt werden soll.
- **Ressourcengruppe** – Wählen oder erstellen Sie eine Ressourcengruppe, in der sich der Connector befinden soll.
- **Webhostingplan** – Wählen oder erstellen Sie einen Webhostingplan.
- **Preisstufe** – Wählen Sie eine Preisstufe für den Connector.
- **Name** – Geben Sie einen Namen für den FTP-Connector ein.
- **Paketeinstellungen**
	- **Serveradresse** – Geben Sie den FTP-Servernamen oder die IP-Adresse an.
	- **Benutzername** – Geben Sie den Benutzernamen für die Verbindung mit dem FTP-Server an.
	- **Kennwort** – Geben Sie das Kennwort für die Verbindung mit dem FTP-Server an.
	- **Stammordner** – Geben Sie den Pfad des Stammordners an.
	- **Binär verwenden** – Geben Sie "true" an, um den binären Übertragungsmodus zu verwenden, "false", um ASCII zu verwenden.
	- **SSL verwenden** – Geben Sie "true" an, um FTP über einen sicheren SSL-/TLS-Kanal zu verwenden.
	- **Serverport** – Geben Sie die Portnummer des FTP-Servers an.
- Klicken Sie auf "Erstellen". Ein neuer FTP-Connector wird erstellt.

##Verwenden des FTP-Connectors in Logik-Apps
Sobald der FTP-Connector erstellt wurde, kann er vom Datenfluss genutzt werden.

Erstellen Sie einen neuen Datenfluss über "+Neu" -> "Web + Mobil" -> "Logik-App". Stellen Sie die Metadaten für den Datenfluss samt Ressourcengruppe bereit.

![Erstellen einer Logik-App][4]

Klicken Sie auf *Trigger und Aktionen*. Der Datenfluss-Designer wird geöffnet.

![Logik-App – Leerer Datenfluss-Designer][5]

Der FTP-Connector kann sowohl als Trigger als auch als Aktion verwendet werden.

###Trigger
Klicken Sie im leeren Datenfluss-Designer im Katalogbereich rechts auf den FTP-Connector.

![Wählen des FTP-Triggers][6]

FTP-Connector wurde ein Trigger - ' Datei verfügbar (löschen Sie dann lesen)'. Dieser Trigger

- Fragt den Ordnerpfad nach neuen Dateien ab.
- Instanziiert den Logikfluss für jede neue Datei.
- Löscht die Datei aus dem Ordnerpfad, nachdem der Logikfluss instanziiert wurde.

Klicken Sie auf "Datei verfügbar (löschen Sie dann lesen)' Trigger.

![Grundlegende Eingaben – FTP-Trigger][7]

Anhand der Eingaben können Sie einen bestimmten Ordnerpfad konfigurieren, der mit einer geplanten Häufigkeit abgefragt wird. Die grundlegenden Eingaben sind – Häufigkeit – Gibt die Häufigkeit der FTP-Umfrage an – Intervall – Gibt das Intervall für die geplante Häufigkeit an – Ordnerpfad – Gibt den Ordnerpfad auf dem FTP-Server an – Dateityp – Gibt an, ob es sich um eine Text- oder um eine Binärdatei handelt

Klicken auf die Ellipsen '...' zeigt die erweiterten Eingaben.

![Grundlegende Eingaben – FTP-Trigger][8]

Die erweiterten Eingaben sind – Dateimaske – Gibt die Dateimaske während der Umfrage an – Dateimaske ausschließen – Gibt die Dateimasken an, die bei der Umfrage ausgeschlossen werden sollen

Geben Sie die Eingaben, und klicken Sie auf das Häkchen, um Eingabe-Konfiguration abzuschließen.

![Grundlegende Eingaben – FTP-Trigger][9]

Hinweis der konfigurierte FTP-Trigger sowohl angezeigt als auch die Ausgaben konfiguriert für die Eingabeparameter.

####Verwenden der Ausgabe des FTP-Triggers in nachfolgenden Aktionen
Die Ausgabe des FTP-Triggers kann als Eingabe für einige andere Aktionen im Datenfluss verwendet werden.

Sie können im Dialogfeld "input" Aktion "..." auf und die Ausgabe an, dass FTP direkt aus der Dropdownliste auswählen.

Sie können auch einen Ausdruck direkt in das Eingabefeld der Aktion schreiben. Der Datenflussausdruck für den Verweis auf die Ausgabe des FTP-Triggers wird unten aufgeführt.

	@triggers('ftpconnector').outputs.body.Content

###Aktionen
Klicken Sie im rechten Bereich auf den FTP-Connector. Der FTP-Connector listet die unterstützten Aktionen auf.

![Liste der FTP-Aktionen][10]

Der FTP-Connector unterstützt vier Aktionen. Dies sind:

- **Datei abrufen** – Ruft den Inhalt einer bestimmten Datei ab.
- **Datei hochladen** – Lädt eine Datei in den FTP-Ordnerpfad hoch.
- **Datei löschen** – Löscht eine Datei aus dem FTP-Ordnerpfad.
- **Dateien** -Listet alle Dateien im FTP-Ordnerpfad

Nehmen wir ein Beispiel - Datei hochladen. Klicken Sie auf "Datei hochladen".

Die grundlegenden Eingaben werden zuerst angezeigt.

![Grundlegende Eingaben der Aktion "Datei hochladen"][11]


- **Inhalt** – Gibt den Inhalt der Datei an, die hochgeladen werden soll.
- **Codierung für die Inhaltsübertragung** – Geben Sie keine oder base64 an.
- **Dateipfad** – Gibt den Dateipfad der Datei an, die hochgeladen werden soll.

Klicken Sie auf ..., um die erweiterten Eingaben anzuzeigen.

![Grundlegende Eingaben der Aktion "Datei hochladen"][12]


- **Angefügt werden, wenn vorhanden** - WAHR oder falsch "anfügen, wenn vorhanden". Wenn diese Option aktiviert ist, werden die Daten an die Datei angefügt, falls diese vorhanden ist. Ist die Option deaktiviert, wird die Datei überschrieben, falls sie vorhanden ist.
- **Temporärer Ordner** – Optional. Falls angegeben, lädt der Adapter die Datei in den Pfad des temporären Ordners. Nach Abschluss des Uploads wird die Datei in den "Ordnerpfad" verschoben. Der Pfad des temporären Ordners sollte sich auf dem gleichen physischen Datenträger befinden wie der Ordnerpfad, um sicherzustellen, dass der Verschiebevorgang atomarisch verläuft. Temporäre Ordner können nur verwendet werden, wenn die Eigenschaft "Anfügen, falls vorhanden" deaktiviert ist.

Geben Sie die Eingaben, und klicken Sie auf das Häkchen, um Eingabe-Konfiguration abzuschließen.

![Konfigurierte Aktion "Datei hochladen"][13]

Der Parameter 'Pfad' festgelegt ist, um

	@concat('/Output/',triggers().outputs.body.FileName)

Beachten Sie, dass die konfigurierte FTP-Aktion zum Hochladen der Datei sowohl Eingabeparameter als auch Ausgabeparameter anzeigt.

####Verwenden der Ausgaben früherer Aktionen als Eingabe für eine FTP-Aktion
Beachten Sie, dass der Wert für den Inhalt im konfigurierten Screenshot auf einen Ausdruck festgelegt ist.

	@triggers().outputs.body.Content


Sie können ihn auf einen beliebigen Wert festlegen. Dies ist nur ein Beispiel. Der Ausdruck nimmt die Ausgabe des Triggers der Logik-App und verwendet sie als Inhalt der Datei, die hochgeladen werden soll. So können Sie die Ausgabe einer vorherigen Aktion, beispielsweise einer Transformation, verwenden. In diesem Fall lautet der Ausdruck folgendermaßen:

	@actions('transformservice').outputs.body.OutputXML


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-ftp/LaunchAzureMarketplace.PNG
[2]: ./media/app-service-logic-connector-ftp/SelectFTPConnector.PNG
[3]: ./media/app-service-logic-connector-ftp/CreateFTPConnector.PNG
[4]: ./media/app-service-logic-connector-ftp/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-ftp/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-ftp/ChooseFTPTrigger.PNG
[7]: ./media/app-service-logic-connector-ftp/BasicInputsFTPTrigger.PNG
[8]: ./media/app-service-logic-connector-ftp/AdvancedInputsFTPTrigger.PNG
[9]: ./media/app-service-logic-connector-ftp/ConfiguredFTPTrigger.PNG
[10]: ./media/app-service-logic-connector-ftp/ListOfFTPActions.PNG
[11]: ./media/app-service-logic-connector-ftp/BasicInputsUploadFile.PNG
[12]: ./media/app-service-logic-connector-ftp/AdvancedInputsUploadFile.PNG
[13]: ./media/app-service-logic-connector-ftp/ConfiguredUploadFile.PNG
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->