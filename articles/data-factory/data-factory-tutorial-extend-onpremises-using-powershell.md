<properties 
	pageTitle="Kopieren der Ausgabedaten aus dem Lernprogramm in eine lokale SQL Server-Datenbank" 
	description="Die exemplarische Vorgehensweise in diesem Lernprogramm baut auf dem Data Factory-Lernprogramm auf und beschreibt das Kopieren von Effektivitätsdaten für Marketingkampagnen in eine lokale SQL Server-Datenbank."
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="spelluru"/>


# Exemplarische Vorgehensweise: Kopieren von Daten zur Kampagneneffektivität in eine lokale SQL Server-Datenbank
In dieser exemplarischen Vorgehensweise erfahren Sie, wie die Umgebung eingerichtet werden muss, damit die Pipeline mit lokalen Daten arbeiten kann.
 
Im letzten Schritt des Protokollverarbeitungszenarien aus der ersten exemplarischen Vorgehensweise mit dem Workflow „Partition -> Enrich -> Analyze“ wurde die Ausgabe der Wirksamkeit der Marketingkampagne in eine Azure SQL-Datenbank kopiert. Sie können diese Daten auch in einen lokalen SQL Server für Analysen innerhalb Ihrer Organisation verschieben.
 
Um die Wirksamkeitsdaten der Marketingkampagne aus dem Azure-BLOB auf einen lokalen SQL Server zu kopieren, müssen Sie einen zusätzlichen lokalen verknüpften Dienst, eine Tabelle und eine Pipeline erstellen, die die gleichen Cmdlets wie in der ersten exemplarischen Vorgehensweise verwenden.

## Voraussetzungen

Sie **müssen** die exemplarische Vorgehensweise im [Lernprogramm: Verschieben und Verarbeiten von Protokolldateien mit Data Factory][datafactorytutorial] durchführen, bevor Sie die exemplarische Vorgehensweise in diesem Artikel befolgen.

**(Empfohlen)** Beschäftigen Sie sich mit der exemplarischen Vorgehensweise im Artikel [Aktivieren von Pipelines zum Arbeiten mit lokalen Daten][useonpremisesdatasources], um eine exemplarische Vorgehensweise zum Erstellen einer Pipeline zum Verschieben von Daten von einem lokalen SQL Server zu einem Azure-Blobspeicher zu erhalten.


In dieser exemplarischen Vorgehensweise führen Sie die folgenden Schritte aus:

1. [Schritt 1: Erstellen eines Datenverwaltungsgateways](#OnPremStep1). Das Datenverwaltungsgateway ist ein Client-Agent, der Zugriff auf lokale Datenquellen in Ihrer Organisation aus der Cloud bietet. Das Gateway ermöglicht die Übertragung von Daten zwischen einem lokalen SQL Server und Azure-Datenspeichern.	

	Sie müssen mindestens ein Gateway in Ihrer Unternehmensumgebung installiert haben und dieses auch bei Azure Data Factory registrieren, bevor Sie eine lokale SQL Server-Datenbank als verknüpften Dienst für eine Azure Data Factory hinzufügen.

2. [Schritt 2: Erstellen eines verknüpften Diensts für den lokalen SQL Server](#OnPremStep2). In diesem Schritt erstellen Sie zuerst eine Datenbank und eine Tabelle auf dem lokalen SQL Server-Computer und dann den verknüpften Dienst: **OnPremSqlLinkedService**.
3. [Schritt 3: Erstellen der Tabelle und der Pipeline](#OnPremStep3). In diesem Schritt erstellen Sie die Tabelle **MarketingCampaignEffectivenessOnPremSQLTable** und die Pipeline **EgressDataToOnPremPipeline**. 

4. [Schritt 4: Überwachen der Pipeline und Anzeigen des Ergebnisses](#OnPremStep4). In diesem Schritt überwachen Sie die Pipelines, Tabellen und Datenslices mithilfe des Azure-Portals.


## <a name="OnPremStep1"></a> Schritt 1: Erstellen eines Datenverwaltungsgateways

Das Datenverwaltungsgateway ist ein Client-Agent, der Zugriff auf lokale Datenquellen in Ihrer Organisation aus der Cloud bietet. Das Gateway ermöglicht die Übertragung von Daten zwischen einem lokalen SQL Server und Azure-Datenspeichern.
  
Sie müssen mindestens ein Gateway in Ihrer Unternehmensumgebung installiert haben und dieses auch bei Azure Data Factory registrieren, bevor Sie eine lokale SQL Server-Datenbank als verknüpften Dienst für eine Azure Data Factory hinzufügen.

Wenn Sie ein vorhandenes Datengateway haben, das Sie verwenden können, überspringen Sie diesen Schritt.

1.	Erstellen eines logischen Datengateways. In der **Azure Preview Portal**, klicken Sie auf **verknüpften Dienste** auf der **DATA FACTORY** Blade.
2.	Klicken Sie in der Befehlsleiste auf **(+) Datengateway hinzufügen**.  
3.	Klicken Sie auf dem Blatt **Neues Datengateway** auf **ERSTELLEN**.
4.	Geben Sie auf dem Blatt **Erstellen** als **Namen** für das Datengateway **MyGateway**.
5.	Klicken Sie auf **REGION AUSWÄHLEN**, und ändern Sie diese, falls erforderlich. 
6.	Klicken Sie auf dem Blatt **Erstellen** auf **OK**. 
7.	Daraufhin wird das Blatt **Konfigurieren** angezeigt. 
8.	Klicken Sie auf dem Blatt **Konfigurieren** auf **Direkt auf diesem Computer installieren**. Daraufhin wird das Gateway auf Ihren Computer heruntergeladen, installiert und konfiguriert und dann bei dem Dienst registriert. Wenn Sie ein vorhandenes Gateway auf dem Computer installiert haben, das Sie mit diesem logischen Gateway im Portal verknüpfen möchten, verwenden Sie den Schlüssel in diesem Fenster, um das Gateway mithilfe des Tools „Datenverwaltungsgateway-Konfigurations-Managers (Vorschau)“ erneut zu registrieren.

	![Datenverwaltungsgateway-Konfigurations-Manager][image-data-factory-datamanagementgateway-configuration-manager]

9. Klicken Sie auf **OK**, um das Blatt **Konfigurieren** zu schließen, und erneut auf **OK**, um das Blatt **Erstellen** zu schließen. Warten Sie, bis sich der Status von **MyGateway** auf dem Blatt **Verknüpfte Dienste** in **GUT** ändert. Sie können auch das Tool **Datenverwaltungsgateway-Konfigurations-Manager (Vorschau)** starten, um zu sich zu vergewissern, dass der Name des Gateways mit dem Namen im Portal übereinstimmt und dass der **Status** **Registriert** lautet. Sie müssen möglicherweise das Fenster „Verknüpfte Dienste“ schließen und erneut öffnen, um den aktuellen Status anzuzeigen. Es kann ein paar Minuten dauern, bis der Bildschirm mit dem aktuellen Status aktualisiert wird.

## <a name="OnPremStep2"></a> Schritt 2: Erstellen eines verknüpften Diensts für den lokalen SQL Server

In diesem Schritt erstellen Sie zuerst die erforderliche Datenbank und Tabelle auf dem lokalen SQL Server-Computer und dann den verknüpften Dienst.

### Vorbereiten der lokalen Datenbank und Tabelle

Zunächst müssen Sie die SQL Server-Datenbank, die Tabelle, benutzerdefinierte Typen und gespeicherte Prozeduren erstellen. Diese werden zum Verschieben der Ergebnisse von **MarketingCampaignEffectiveness** aus dem Azure-Blob in die SQL Server-Datenbank verwendet.

1.	Navigieren Sie in **Windows-Explorer** zum Unterordner **OnPremises** in **C:\ADFWalkthrough** (oder zu dem Speicherort, an dem Sie die Beispiele extrahiert haben).
2.	Öffnen Sie **prepareOnPremDatabase&Table.ps1** in Ihrem bevorzugten Editor, ersetzen Sie die markierten Informationen durch Ihre SQL Server-Informationen, und speichern Sie die Datei (geben Sie die Details der **SQL-Authentifizierung** ein). Aktivieren Sie für das Lernprogramm die SQL-Authentifizierung für die Datenbank. 
			
		$dbServerName = "<servername>"
		$dbUserName = "<username>"
		$dbPassword = "<password>"

3. Navigieren Sie in **Azure PowerShell** zum Ordner **C:\ADFWalkthrough\OnPremises**.
4.	Führen Sie **prepareOnPremDatabase&Table.ps1** aus **(entweder & in doppelten Anführungszeichen oder wie unten dargestellt)**.
			
		& '.\prepareOnPremDatabase&Table.ps1'

5. Wenn das Skript erfolgreich ausgeführt wird, wird Folgendes angezeigt:
			
		PS E:\ADF\ADFWalkthrough\OnPremises> & '.\prepareOnPremDatabase&Table.ps1'
		6/10/2014 10:12:33 PM Script to create sample on-premises SQL Server Database and Table
		6/10/2014 10:12:33 PM Creating the database [MarketingCampaigns], table and stored procedure on [.]...
		6/10/2014 10:12:33 PM Connecting as user [sa]
		6/10/2014 10:12:33 PM Summary:
		6/10/2014 10:12:33 PM 1. Database 'MarketingCampaigns' created.
		6/10/2014 10:12:33 PM 2. 'MarketingCampaignEffectiveness' table and stored procedure 


### Erstellen des verknüpften Diensts

1.	In der **Azure Preview Portal**, klicken Sie auf **verknüpften Dienste** Kachel auf die **DATA FACTORY** Blade für **LogProcessingFactory**.
2.	In der **verknüpften Dienste** Blade, klicken Sie auf **Hinzufügen (+) Datenspeicher**.
3.	In der **neuen Datenspeicher** Blade, geben Sie **OnPremSqlLinkedService** für den **Namen**. 
4.	Klicken Sie auf **Typ (erforderlichen Einstellungen)** und wählen Sie **SQL Server**. Sollte der **DATA GATEWAY**, **Server**, **Datenbank**, und **Anmeldeinformationen** Einstellungen in der **neuen Datenspeicher** Blade jetzt. 
5.	Klicken Sie auf **Daten-GATEWAY (Konfigurieren der erforderliche Einstellungen)** und wählen Sie **MyGateway** Sie zuvor erstellt haben. 
6.	Geben Sie **Name** des Datenbankservers, der hostet die **MarketingCampaigns** Datenbank. 
7.	Geben Sie **MarketingCampaigns** für die Datenbank. 
8.	Klicken Sie auf **Anmeldeinformationen**. 
9.	In der **Anmeldeinformationen** Blade, klicken Sie auf **Klicken Sie hier, um Anmeldeinformationen sicher**.
10.	Eine One-Click-Anwendung zum ersten Mal installiert und startet den **Festlegen der Anmeldeinformationen **Dialogfeld. 11.	In der **Festlegen der Anmeldeinformationen** Dialogfeld Geben Sie **Benutzername** und **Kennwort**, und klicken Sie auf **OK**. Warten Sie, bis das Dialogfeld geschlossen wird. 
12.	Klicken Sie auf **OK** in den **neuen Datenspeicher** Blade. 
13.	In der **verknüpften Dienste** Blade, überprüfen Sie, ob **OnPremSqlLinkedService** wird in der Liste und die **Status** des verknüpften Dienstes ist **gute**.

## <a name="OnPremStep3"></a> Schritt 3: Erstellen der Tabelle und der Pipeline

### Erstellen der lokalen logischen Tabelle

1.	In **Azure PowerShell**, wechseln Sie zu den **C:\ADFWalkthrough\OnPremises** Ordner. 
2.	Verwenden Sie das Cmdlet **neu AzureDataFactoryTable** zum Erstellen von der Tabellen wie folgt für **MarketingCampaignEffectivenessOnPremSQLTable.json**.

			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessOnPremSQLTable.json
	 
#### Erstellen der Pipeline zum Kopieren der Daten aus dem Azure-BLOB auf SQL Server

1.	Verwenden Sie das Cmdlet **neu AzureDataFactoryPipeline** beim Erstellen von der Pipeline folgendermaßen für **EgressDataToOnPremPipeline.json**.

			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EgressDataToOnPremPipeline.json
	 
2. Verwenden Sie das Cmdlet **Set AzureDataFactoryPipelineActivePeriod** an der aktiven Zeitraum für die **EgressDataToOnPremPipeline**.

			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name EgressDataToOnPremPipeline

	Drücken Sie **'Y'** um den Vorgang fortzusetzen.
	
## <a name="OnPremStep4"></a> Schritt 4: Überwachen der Pipeline und Anzeigen des Ergebnisses

Jetzt können Sie die gleichen Schritte eingeführten [Schritt 6: Überwachen von Tabellen und Pipelines](#MainStep6) zur Überwachung neue Pipeline und die Datensegmente für die neue lokale ADF-Tabelle.
 
Wenn Sie sehen, dass sich der Status eines Slice der Tabelle **MarketingCampaignEffectivenessOnPremSQLTable** in "Bereit" ändert, bedeutet das, dass die Pipeline die Ausführung für den Slice abgeschlossen hat. Um die Ergebnisse anzuzeigen, fragen Sie die Tabelle **MarketingCampaignEffectiveness** in der **MarketingCampaigns**-Datenbank in Ihrem SQL Server ab.
 
Glückwunsch! Sie haben die exemplarische Vorgehensweise zur Verwendung Ihrer lokalen Datenquelle erfolgreich abgeschlossen.
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[datafactorytutorial]: data-factory-tutorial-using-powershell.md
[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx

[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-extend-onpremises-using-powershell/DataManagementGatewayConfigurationManager.png

<!---HONumber=GIT-SubDir--> 