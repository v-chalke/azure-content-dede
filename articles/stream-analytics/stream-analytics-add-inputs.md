<properties 
	pageTitle="Hinzufügen von Eingaben | Microsoft Azure" 
	description="Eingaben hinzufügen – Lernpfadsegment"
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="09/09/2015" 
	ms.author="jeffstok"/>

# Hinzufügen von Eingaben

Azure Stream Analytics-Aufträge können mit einer oder mehreren Eingaben verknüpft werden, die eine Verbindung zu einer vorhandenen Datenquelle definieren. Werden Daten an diese Datenquelle gesendet, werden sie von dem Stream Analytics-Auftrag übernommen und in Echtzeit verarbeitet. Stream Analytics verfügt über eine hervorragende Integration in [Azure Event Hubs](http://azure.microsoft.com/services/event-hubs/) und [Azure-Blobspeicher](./storage/storage-dotnet-how-to-use-blobs.md) sowohl innerhalb als auch außerhalb des Auftragsabonnements. Es gibt zwei Eingabetypen in Stream Analytics: Datenströme und Verweisdaten.

- **Datenströme**: Stream Analytics-Aufträge müssen mindestens eine Datenstromeingabe enthalten, die vom Auftrag genutzt und umgewandelt werden soll. Azure-BLOB-Speicher und Azure Event Hubs werden als Datenstrom-Eingabequellen unterstützt. Azure Event Hubs werden verwendet, um Ereignisströme von verbundenen Geräten, Diensten und Anwendungen zu erfassen. Azure-Blobspeicher kann als eine Eingabequelle für das Erfassen von Massendaten als Strom verwendet werden.  
- **Verweisdaten**: Stream Analytics unterstützt einen zweiten zusätzlichen Eingabetyp, der als „Verweisdaten“ bezeichnet wird. Im Gegensatz zu Daten in Bewegung sind diese Daten statisch oder ändern sich nur langsam. In der Regel werden Sie zum Durchführen von Suchen und Korrelationen mit Datenströmen verwendet, um einen umfangreicheren Datensatz zu erstellen. Azure-BLOB-Speicher ist derzeit die einzige unterstützte Eingabequelle für Verweisdaten.  

So fügen Sie Ihrem Stream Analytics-Auftrag eine Eingabe hinzu:

1. Klicken Sie in Ihrem Stream Analytics-Auftrag auf **Eingaben** und dann auf **Eingabe hinzufügen**.

    ![Hinzufügen von Eingaben](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)

2. Geben Sie den Eingabetyp an: entweder **Datenstrom** oder **Verweisdaten**.

    ![Hinzufügen von Daten](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)

3. Geben Sie beim Erstellen einer Datenstromeingabe den Quelltyp für die Eingabe an. Während der Erstellung von Verweisdaten wird dieser Bildschirm übersprungen, da nur Blobspeicher unterstützt wird.

    ![Hinzufügen eines Datenstroms](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)

4. Geben Sie im Feld „Eingabealias“ einen Anzeigenamen für diese Eingabe ein. Dieser Name wird später in der Abfrage Ihres Auftrags zum Verweisen auf die Eingabe verwendet.

    Geben Sie den Rest der erforderlichen Verbindungseigenschaften ein, um eine Verbindung mit Ihrer Datenquelle herzustellen. Diese Felder unterscheiden sich je nach Eingabe- und Quelltyp und sind [hier](stream-analytics-create-a-job.md.) im Detail definiert.

    ![Hinzufügen eines Event Hubs](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)

5. Geben Sie die Serialisierungseinstellungen für die Eingabedaten an:
	- Um sicherzustellen, dass Ihre Abfragen erwartungsgemäß funktionieren, geben Sie das **Ereignisserialisierungsformat** von eingehenden Daten an. Unterstützte Serialisierungsformate sind JSON, CSV und Avro.
	- Überprüfen Sie die **Codierung** für die Daten. UTF-8 ist derzeit das einzige unterstützte Codierungsformat.

    ![Einstellungen für die Datenserialisierung](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)

6. Nach Abschluss der Eingabeerstellung überprüft Stream Analytics, ob eine Verbindung mit der Eingabequelle hergestellt werden kann. Sie können den Status des Verbindungstests im Benachrichtigungshub anzeigen.

    ![Verbindung testen](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)


## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).

## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Sept15_HO2-->