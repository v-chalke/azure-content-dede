<properties 
	pageTitle="Stream Analytics Power BI-Dashboard | Azure" 
	description="Erfahren Sie, wie Sie ein Live-Power BI-Dashboard mit Daten aus einem Stream Analytics-Auftrag füllen." 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="04/24/2015" 
	ms.author="jeffstok"/>
	
#Azure Stream Analytics & Power BI: Live-Dashboard mit Echtzeit-Analyse von Streamingdaten

Ein häufiger Anwendungsfall für Azure Stream Analytics ist die Analyse von hohen Volumen an Streamingdaten in Echtzeit und Darstellung dieser in einem Live-Dashboard (einem Dashboard, das sich in Echtzeit aktualisiert, ohne dass der Benutzer den Browser aktualisieren muss). [Microsoft Power BI](https://powerbi.com/) eignet sich optimal, um ein solches Live-Dashboard in kürzester Zeit zu erstellen. [Hier finden Sie ein Beispielvideo, um das Szenario zu veranschaulichen.](https://www.youtube.com/watch?v=SGUpT-a99MA) In diesem Artikel erfahren Sie, wie Sie Power BI als Ausgabe für Ihre Aufträge in Azure Stream Analytics verwenden. Hinweis: Azure Stream Analytics ist allgemein verfügbar, Power BI ist zu diesem Zeitpunkt jedoch eine Vorschaufunktion von Azure Stream Analytics.

##Voraussetzungen

* Microsoft Azure-Konto mit Organisations-ID (Power BI funktioniert nur mit Ihrer Organisations-ID. Die Organisations-ID ist Ihre geschäftliche E-Mail-Adresse, z. B. xyz@mycompany.com. Persönliche E-Mail-Adressen wie xyz@hotmail.com sind keine Organisations-IDs. [Erfahren Sie hier mehr über die Organisations-ID](https://www.arin.net/resources/request/org.html)).
* Ein Eingabestream für Ihren ASA-Auftrag (Azure Stream Analytics), aus dem Streamingdaten verwendet werden können. Derzeit akzeptiert ASA die Eingabe aus einem Azure-Ereignis-Hub oder aus Azure-Blobspeicher.  

##Erstellen eines Azure Stream Analytics-Auftrags

Klicken Sie im [Azure-Portal](https://manage.windowsazure.com) auf **Neu, Datendienste, Stream Analytics und Schnellerfassung**.

Geben Sie die folgenden Werte an und klicken Sie dann auf **Stream Analytics-Auftrag erstellen**:

* **Auftragsname** – Geben Sie einen Auftragsnamen ein. Zum Beispiel **DeviceTemperatures**.
* **Region** – Wählen Sie die Region aus, in der der Auftrag gespeichert werden soll. Ziehen Sie es in Betracht, den Auftrag und den Event Hub in derselben Region zu platzieren, um eine optimale Leistung sicherzustellen und zu vermeiden, dass Übertragungskosten zwischen mehreren Regionen entstehen.
* **Speicherkonto** – Wählen Sie das Speicherkonto, das Sie zum Speichern von Überwachungsdaten für alle Stream Analytics-Aufträge verwenden möchten, die innerhalb dieser Region ausgeführt werden. Sie haben die Möglichkeit, ein vorhandenes Speicherkonto auszuwählen oder ein neues zu erstellen.

Klicken Sie im linken Bereich auf **Stream Analytics**, um die Stream Analytics-Aufträge aufzulisten.

![Grafik1][graphic1]

> [AZURE.TIP]Der neue Auftrag wird mit dem Status **Nicht gestartet** aufgeführt. Beachten Sie, dass die Schaltfläche **Start** am unteren Seitenrand deaktiviert ist. Dies entspricht dem erwarteten Verhalten, da Sie die Auftragseingabe, -ausgabe, -abfrage usw. konfigurieren müssen, bevor Sie den Auftrag starten können.

##Festlegen der Auftragseingabe

In diesem Lernprogramm wird davon ausgegangen, dass Sie EventHub als Eingabe mit JSON-Serialisierung und UTF-8-Codierung verwenden.

* Klicken Sie auf den Auftragsnamen.
* Klicken Sie am oberen Seitenrand auf **Eingaben** und dann auf **Eingabe hinzufügen**. Das aufgerufene Dialogfeld führt Sie durch eine Reihe von Schritten, um Ihre Eingabe einzurichten.
*	Wählen Sie **Datenstrom**, und klicken Sie dann auf die rechte Taste.
*	Wählen Sie **Event Hub**, und klicken Sie dann auf die rechte Taste.
*	Geben Sie die folgenden Werte auf der dritten Seite ein, oder wählen Sie sie aus:
  *	**Alias eingeben** – Geben Sie einen Anzeigenamen für diesen Auftrag ein. Beachten Sie, dass Sie diesen Namen später in der Abfrage verwenden werden.
  * **Event Hub** – Wenn der Event Hub, den Sie erstellt haben, sich in demselben Abonnement wie der Stream Analytics-Auftrag befindet, wählen Sie den Namespace aus, in dem sich der Event Hub befindet.
*	Wenn sich Ihr Event Hub in einem anderen Abonnement befindet, wählen Sie **Event Hub aus anderem Abonnement verwenden**, und geben Sie manuell den **Service Bus-Namespace**, **Event Hub-Namen**, **Event Hub-Richtliniennamen**, **Event Hub-Richtlinienschlüssel** und die **Event Hub-Partitionsanzahl** ein.

> [AZURE.NOTE]In diesem Beispiel wird die Standardanzahl an Partitionen verwendet, sprich 16.

* **Event Hub-Name** – Wählen Sie den Namen Ihres Azure Event Hubs aus.
* **Event Hub-Richtlinienname** – Wählen Sie die Richtlinie für den Event Hub aus, den Sie verwenden. Stellen Sie sicher, dass diese Richtlinie über Verwaltungsberechtigungen verfügt.
*	**Event Hub-Verbrauchergruppe** – Dieses Feld können Sie leer lassen oder eine der Verbrauchergruppen Ihres Event Hub angeben. Beachten Sie, dass jede Verbrauchergruppe eines Event Hub nur fünf Leser gleichzeitig haben darf. Wählen Sie also die richtige Verbrauchergruppe für Ihren Auftrag aus. Wenn Sie das Feld leer lassen, wird die Standardverbrauchergruppe verwendet.

*	Klicken Sie auf die rechte Taste.
*	Geben Sie die folgenden Werte an:
  *	**Ereignisserialisierungsformat** – JSON
  *	**Codierung** – UTF8
*	Klicken Sie auf das Häkchen, um diese Quelle hinzuzufügen und um zu überprüfen, ob Stream Analytics erfolgreich mit dem Event Hub verbunden werden kann.

##Power BI-Ausgabe hinzufügen

1.  Klicken Sie am oberen Seitenrand auf **Ausgabe** und dann auf **Ausgabe hinzufügen**. Sie sehen, dass Power BI als Ausgabeoption aufgelistet ist.

![Grafik2][graphic2]

> [AZURE.NOTE]Hinweis: Die Power BI-Ausgabe ist nur für Azure-Konten mit Org-Ids verfügbar. Wenn Sie für Ihr Azure-Konto keine Organisations-ID verwenden (sondern z. B. Ihre Live-ID / Ihr persönliches Microsoft-Konto), wird die Power BI-Ausgabeoption nicht angezeigt.

2.  Wählen Sie **Power BI**, und klicken Sie dann auf die rechte Taste.
3.  Es wird ein Bildschirm ähnlich dem folgenden angezeigt:

![Grafik3][graphic3]

4.  In diesem Schritt müssen Sie darauf achten, dieselbe Organisations-ID zu verwenden, die Sie für den ASA-Auftrag verwenden. Derzeit muss die Power BI-Ausgabe dieselbe Organisations-ID wie Ihr ASA-Auftrag verwenden. Wenn Sie bereits ein Power BI-Konto mit der gleichen Organisations-ID haben, wählen Sie „Jetzt autorisieren“. Falls nicht, wählen Sie „Jetzt registrieren“, und verwenden Sie während des Anmeldevorgangs für Power BI dieselbe Organisations-Id wie für Ihr Azure-Konto. [Hier finden Sie ein gutes Blog über die Einzelheiten der Power BI-Anmeldung (in englischer Sprache)](http://blogs.technet.com/b/powerbisupport/archive/2015/02/06/power-bi-sign-up-walkthrough.aspx).
5.  Als Nächstes wird ein Bildschirm ähnlich dem folgenden angezeigt:

![Grafik4][graphic4]

Geben Sie die Werte wie nachfolgend gezeigt ein:

* **Ausgabealias** – Sie können jeden Ausgabealias verwenden, auf den Sie auf einfache Weise verweisen können. Dieser Ausgabealias ist besonders hilfreich, wenn Sie sich mehrere Ausgaben für den Auftrag benötigen. In diesem Fall müssen Sie in der Abfrage auf diesen Alias verweisen. Verwenden wir beispielsweise den Ausgabealiaswert = „OutPbi“.
* **Datasetname** – Geben Sie einen Datasetnamen für die Power BI-Ausgabe an. Verwenden wir z. B. „pbidemo“.
*	**Tabellenname** – Geben Sie einen Tabellennamen unter dem Dataset der Power BI-Ausgabe ein. Wir verwenden hier „pbidemo“. Derzeit darf die Power BI-Ausgabe von ASA-Aufträgen nur eine Tabelle pro Dataset haben.

>	[AZURE.NOTE] Note - You should not explicitly create this dataset and table in your Power BI account. They will be automatically created when you start your ASA job and the job starts pumping output into Power BI. If your ASA job query doesn’t return any results, the dataset and table will not be created.

*	Klicken Sie auf **OK** und anschließend auf **Testverbindung**. Die Ausgabekonfiguration ist abgeschlossen.

>	[AZURE.WARNING] Also be aware that if Power BI already had a dataset and table with the same name as the one you provided in this ASA job, the existing data will be overwritten.


##Schreiben von Abfragen

Gehen Sie zur Registerkarte **Abfrage** des Auftrags. Schreiben Sie die Abfrage, deren Ausgabe Sie in Power BI sehen möchten. Dies kann z. B. die folgende SQL-Abfrage sein:

    SELECT
    	MAX(hmdt) AS hmdt,
    	MAX(temp) AS temp,
    	System.TimeStamp AS time,
    	dspl
    INTO
        OutPBI
    FROM
    	Input TIMESTAMP BY time
    GROUP BY
    	TUMBLINGWINDOW(ss,1),
    	dspl

    
    
Starten Sie den Auftrag. Überprüfen Sie, ob der Event Hub Ereignisse empfängt und Ihre Abfrage die erwarteten Ergebnisse generiert. Wenn Ihre Abfrage 0 Zeilen ausgibt, werden Power BI-Dataset und -Tabellen nicht automatisch erstellt.

##Erstellen des Dashboards in Power BI

Wechseln Sie zu [Powerbi.com](https://powerbi.com) und melden Sie sich mit Ihrer Organisations-ID an. Gibt die ASA-Auftragsabfrage Ergebnisse aus, sehen Sie, dass das Dataset bereits erstellt wurde:

![Grafik5][graphic5]

Um ein neues Dashboard zu erstellen, gehen Sie zur Option „Dashboards“.

![Grafik6][graphic6]

In diesem Beispiel nennen wir es „Demo Dashboard“.

Klicken Sie jetzt auf das Dataset, das durch den ASA-Auftrag (im aktuellen Beispiel „pbidemo“) erstellt wurde. Zum Erstellen eines Diagramms auf diesem Dataset werden Sie zu einer anderen Seite weitergeleitet. Im Folgenden nur ein Beispiel für die Berichte, die Sie erstellen können:

Wählen Sie die Felder „Σ temp“ und „time“ aus. Sie werden automatisch dem Wert und der Achse des Diagramms zugewiesen:

![Grafik7][graphic7]

Mit dieser Option erhalten Sie automatisch ein Diagramm wie nachfolgend gezeigt:

![Grafik8][graphic8]

Klicken Sie im Abschnitt „Wert“ auf die Dropdownliste für „temp“ und wählen Sie für die Temperatur **Durchschnitt**. Klicken Sie anschließend im Diagramm auf **Visualisierung** und wählen Sie **Liniendiagramm**:

![Grafik9][graphic9]

Jetzt erhalten Sie ein Liniendiagramm der Durchschnittstemperatur im Laufe des Zeitraums. Verwenden die Option zum Anheften (siehe unten) und verknüpfen Sie das Diagramm mit dem Dashboard, das Sie zuvor erstellt haben:

![Grafik10][graphic10]

Jetzt sehen Sie beim Anzeigen des Dashboards mit diesem angehefteten Bericht, wie dieser in Echtzeit aktualisiert wird. Ändern Sie versuchsweise die Daten der Ereignisse – wählen Sie z. B. „Temperaturspitzen“ o. ä. – und sehen Sie sich an, wie dies in Echtzeit im Dashboard wiedergegeben wird.

Beachten Sie, dass in diesem Lernprogramm nur die Erstellung einer Art von Diagramm für ein Dataset gezeigt wurde. Die Möglichkeiten von Power BI sind dagegen praktisch unbegrenzt. Ein weiteres Beispiel für ein Power BI-Dashboard sehen Sie im Video [Erste Schritte mit Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s).

Eine weitere nützliche Ressource, um mehr über das Erstellen von Dashboards mit Power BI zu erfahren, sind die [Dashboards in der Power BI-Vorschau](http://support.powerbi.com/knowledgebase/articles/424868-dashboards-in-power-bi-preview).

##Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

##Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics-Abfragesprachreferenz](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-power-bi-dashboard/1-stream-analytics-power-bi-dashboard.png
[graphic2]: ./media/stream-analytics-power-bi-dashboard/2-stream-analytics-power-bi-dashboard.png
[graphic3]: ./media/stream-analytics-power-bi-dashboard/3-stream-analytics-power-bi-dashboard.png
[graphic4]: ./media/stream-analytics-power-bi-dashboard/4-stream-analytics-power-bi-dashboard.png
[graphic5]: ./media/stream-analytics-power-bi-dashboard/5-stream-analytics-power-bi-dashboard.png
[graphic6]: ./media/stream-analytics-power-bi-dashboard/6-stream-analytics-power-bi-dashboard.png
[graphic7]: ./media/stream-analytics-power-bi-dashboard/7-stream-analytics-power-bi-dashboard.png
[graphic8]: ./media/stream-analytics-power-bi-dashboard/8-stream-analytics-power-bi-dashboard.png
[graphic9]: ./media/stream-analytics-power-bi-dashboard/9-stream-analytics-power-bi-dashboard.png
[graphic10]: ./media/stream-analytics-power-bi-dashboard/10-stream-analytics-power-bi-dashboard.png

<!--HONumber=52-->
 