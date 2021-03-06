<properties
	pageTitle="Überwachen eines Speicherkontos | Microsoft Azure"
	description="Erfahren Sie, wie ein Speicherkonto in Azure mithilfe des Azure-Portals überwacht wird."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="tamram"/>

# Überwachen eines Speicherkontos im Azure-Portal

## Übersicht

Sie können Ihr Speicherkonto im [Azure-Portal](https://portal.azure.com) überwachen. Wenn Sie Ihr Speicherkonto für die Überwachung über das Portal konfigurieren, verwendet Azure Storage die [Speicheranalyse](http://msdn.microsoft.com/library/azure/hh343270.aspx) zum Erfassen von Metriken für Ihr Konto und zum Protokollieren von Anforderungsdaten.

> [AZURE.NOTE] Für die Untersuchung von Überwachungsdaten im [Azure-Portal](https://portal.azure.com) fallen zusätzliche Kosten an. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/library/azure/hh360997.aspx">Speicheranalyse und Speicheranalysekosten</a>.<br />

> Der Azure-Dateispeicher unterstützt derzeit Metriken der Speicheranalyse, allerdings noch keine Protokollierung. Sie können die Metriken für den Azure-Dateispeicher über das [Azure-Portal](https://portal.azure.com) aktivieren.

> Eine ausführliche Anleitung zum Verwenden der Speicheranalyse sowie weiterer Tools, um Azure Storage-bezogene Probleme zu identifizieren, zu diagnostizieren und zu beheben, finden Sie unter [Microsoft Azure Storage: Überwachung, Diagnose und Problembehandlung](storage-monitoring-diagnosing-troubleshooting.md).


## Gewusst wie: Konfigurieren der Überwachung für ein Speicherkonto

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Speicher** und dann auf den Speicherkontonamen, um das Dashboard zu öffnen.

2. Klicken Sie auf **Konfigurieren**, und blättern Sie nach unten zu den Einstellungen unter **Überwachung** für die Blob-, Tabellen- und Warteschlangendienste.

	![Überwachungsoptionen](./media/storage-monitor-storage-account/Storage_MonitoringOptions.png)

3. Legen Sie unter **monitoring** die Überwachungsstufe und die Datenaufbewahrungsrichtlinie für jeden Dienst fest:

	-  Zum Festlegen der Überwachungsstufe wählen Sie eine der folgenden Optionen:

      **Minimal** – Erfasst Metriken wie Eingang/Ausgang, Verfügbarkeit, Latenz sowie Erfolgsprozentwerte, die für die Blob-, Tabellen- und Warteschlangendienste aggregiert werden.

      **Ausführlich** – Sammelt zusätzlich zu den minimalen Metriken den gleichen Satz Metriken für jeden Speichervorgang in der Azure-Speicherdienst-API. Ausführliche Metriken ermöglichen eine genauere Analyse von Problemen, die bei Anwendungsvorgängen auftreten.

      **Aus** – Deaktiviert die Überwachung. Vorhandene Überwachungsdaten bleiben bis zum Ende des Aufbewahrungszeitraums gespeichert.

- Zum Festlegen der Datenaufbewahrungsrichtlinie geben Sie im Feld **Aufbewahrung (Tage)** die Anzahl der Tage ein, für die Daten aufbewahrt werden sollen (zwischen 1 und 365 Tage). Wenn Sie keine Aufbewahrungsrichtlinie festlegen möchten, geben Sie null (0) ein. Ist keine Aufbewahrungsrichtlinie festgelegt, müssen Sie die Überwachungsdaten selbst löschen. Wir empfehlen, die Aufbewahrungsrichtlinie entsprechend dem Zeitraum festzulegen, den Sie Speicheranalysedaten für Ihr Konto behalten möchten, sodass alte und nicht verwendete Analysedaten kostenlos vom System gelöscht werden können.

4. Klicken Sie nach Abschluss der Überwachungskonfiguration auf **Speichern**.

Nach ungefähr einer Stunde sollten die ersten Überwachungsdaten im Dashboard und auf der Seite **Überwachen** angezeigt werden.

Bis Sie die Überwachung für ein Speicherkonto konfigurieren, werden keine Überwachungsdaten gesammelt, und die Metrikendiagramme im Dashboard und auf der Seite **Überwachen** sind leer.

Nachdem Sie die Überwachungsstufen und Aufbewahrungsrichtlinien festgelegt haben, können Sie wählen, welche der verfügbaren Metriken im [Azure-Portal](https://portal.azure.com) überwacht und welche in Metrikendiagrammen gezeichnet werden sollen. Auf jeder Überwachungsstufe wird ein Standardsatz Metriken angezeigt. Mit **Metriken hinzufügen** können Sie Metriken zur Metrikenliste hinzufügen oder daraus entfernen.

Metriken werden im Speicherkonto in vier Tabellen namens $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue und $MetricsCapacityBlob gespeichert. Weitere Informationen finden Sie unter [Informationen zu Metriken der Speicheranalyse](http://msdn.microsoft.com/library/azure/hh343258.aspx).


## Gewusst wie: Anpassen des Dashboards für die Überwachung

Im Dashboard können Sie aus neun verfügbaren Metriken bis zu sechs Metriken zum Zeichnen im Metrikendiagramm auswählen. Für jeden Dienst (Blob, Tabelle und Warteschlange) sind die Metriken "Verfügbarkeit", "Prozentsatz erfolgreich" und "Anforderungen insgesamt" verfügbar. Für minimale und ausführliche Überwachung stehen im Dashboard die gleichen Metriken zur Verfügung.

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Speicher** und dann auf den Namen des Speicherkontos, um das Dashboard zu öffnen.

2. Führen Sie eine der folgenden Aktionen aus, um die im Diagramm gezeichneten Metriken zu ändern:

	- Um dem Diagramm eine neue Metrik hinzuzufügen, aktivieren Sie das farbige Kontrollkästchen neben der Metriküberschrift in der Tabelle unterhalb des Diagramms.

	- Um eine im Diagramm gezeichnete Metrik auszublenden, deaktivieren Sie das farbige Kontrollkästchen neben der Metriküberschrift.

		![Überwachung\_n more](./media/storage-monitor-storage-account/storage_Monitoring_nmore.png)

3. Standardmäßig zeigt das Diagramm Trends an, da nur der aktuelle Wert der einzelnen Metriken angezeigt wird (Option **Relativ** oben im Diagramm). Um eine Y-Achse einzublenden, sodass absolute Werte angezeigt werden, wählen Sie **Absolut** aus.

4. Wählen Sie 6 Stunden, 24 Stunden oder 7 Tage im oberen Bereich des Diagramms aus, um das Zeitintervall für das Metrikendiagramm zu ändern.


## Gewusst wie: Anpassen der Seite "Überwachen"

Auf der Seite **Überwachen** werden alle Metriken für Ihr Speicherkonto angezeigt.

- Wenn für Ihr Speicherkonto die minimale Überwachung konfiguriert ist, werden Metriken wie Eingang/Ausgang, Verfügbarkeit, Latenz sowie Erfolgsprozentwerte aus den Blob-, Tabellen- und Warteschlangendiensten aggregiert.

- Wenn für Ihr Speicherkonto die ausführliche Überwachung konfiguriert ist, stehen die Metriken nicht nur als Aggregatwerte auf Dienstebene, sondern auch in detaillierterer Form für einzelne Speichervorgänge zur Verfügung.

Mit den folgenden Vorgehensweisen können Sie wählen, welche Speichermetriken in den Metrikendiagrammen und in der Metrikentabelle auf der Seite **Überwachen** angezeigt werden sollen. Diese Einstellungen haben keine Auswirkung auf Sammlung, Aggregierung und Speicherung von Überwachungsdaten im Speicherkonto.

## Hinzufügen von Metriken zur Metrikentabelle


1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Speicher** und dann auf den Namen des Speicherkontos, um das Dashboard zu öffnen.

2. Klicken Sie auf **Überwachen**.

	Die Seite **Überwachen** wird geöffnet. Standardmäßig zeigt die Metrikentabelle eine Teilmenge der für die Überwachung verfügbaren Metriken an. Die Abbildung zeigt die Standardanzeige der Seite "Überwachen" für ein Speicherkonto, für das für alle drei Dienste die ausführliche Überwachung konfiguriert ist. Verwenden Sie **Metriken hinzufügen**, um unter sämtlichen verfügbaren Metriken die zu überwachenden Metriken auszuwählen.

	![Überwachung\_AusführlicheAnzeige](./media/storage-monitor-storage-account/Storage_Monitoring_VerboseDisplay.png)

	> [AZURE.NOTE] Berücksichtigen Sie bei der Auswahl der Metriken die Kosten. Für die Aktualisierung von Überwachungsanzeigen fallen Transaktionsgebühren und Kosten für eingehenden Datenverkehr an. Weitere Informationen finden Sie unter [Speicheranalyse und Speicheranalysekosten](http://msdn.microsoft.com/library/azure/hh360997.aspx).

3. Klicken Sie auf **Metriken hinzufügen**.

	Die aggregierten Metriken für eine minimale Überwachung werden am Anfang der Liste angezeigt. Wenn das Kontrollkästchen aktiviert ist, wird die Metrik in der Metrikenliste angezeigt.

	![Metriken hinzufügen\_Anfängliche Anzeige](./media/storage-monitor-storage-account/Storage_AddMetrics_InitialDisplay.png)

4. Zeigen Sie mit der Maus auf die rechte Seite des Dialogfelds, um eine Bildlaufleiste einzublenden, die Sie ziehen können, um weitere Metriken anzuzeigen.

	![Metriken hinzufügen\_Bildlaufleiste](./media/storage-monitor-storage-account/Storage_AddMetrics_Scrollbar.png)


5. Klicken Sie auf den Nach-unten-Pfeil neben einer Metrik, um eine Liste vor Vorgängen zu erweitern, die im Funktionsumfang der Metrik enthalten sind. Wählen Sie jeden Vorgang aus, der in der Metrikentabelle im [Azure-Portal](https://portal.azure.com) angezeigt werden soll.

	In der folgenden Abbildung wurde die Metrik PROZENTSATZ DER AUTORISIERUNGSFEHLER erweitert.

	![Erweitern\_Reduzieren](./media/storage-monitor-storage-account/Storage_AddMetrics_ExpandCollapse.png)


6. Nachdem Sie Metriken für alle Dienste ausgewählt haben, klicken Sie auf "OK" (Häkchen), um die Überwachungskonfiguration zu aktualisieren. Die ausgewählten Metriken werden der Metrikentabelle hinzugefügt.

7. Um eine Metrik aus der Tabelle zu löschen, klicken Sie auf die Metrik, um diese auszuwählen, und anschließend auf **Metrik löschen**.

	![Metrik löschen](./media/storage-monitor-storage-account/Storage_DeleteMetric.png)

## Gewusst wie: Anpassen des Metrikendiagramms auf der Seite "Überwachen"

1. Wählen Sie auf der Seite **Überwachen** für das Speicherkonto in der Metrikentabelle bis zu sechs Metriken zum Zeichnen im Metrikendiagramm aus. Klicken Sie auf das Kontrollkästchen links von einer Metrik, um diese auszuwählen. Um eine Metrik aus dem Diagramm zu entfernen, deaktivieren Sie das Kontrollkästchen.

2. Um die Diagrammanzeige zwischen relativen Werten (nur Endwert) und absoluten Werte (Y-Achse) umzuschalten, wählen Sie oben im Diagramm **Relativ** oder **Absolut** aus.

3.	Wählen Sie **6 Stunden**, **24 Stunden** oder **7 Tage** im oberen Bereich des Diagramms aus, um das Zeitintervall für das Metrikendiagramm zu ändern.



## Gewusst wie: Konfigurieren der Protokollierung

Für jeden der für Ihr Speicherkonto verfügbaren Speicherdienste (Blob, Warteschlange und Tabelle) können Sie Diagnoseprotokolle für Lese-, Schreib- und/oder Löschanforderungen speichern, und Sie können die Datenaufbewahrungsrichtlinie für jeden der Dienste festlegen.

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Speicher** und dann auf den Namen des Speicherkontos, um das Dashboard zu öffnen.

2. Klicken Sie auf **Konfigurieren**, und blättern Sie dann mit der Nach-unten-Taste zum Abschnitt **Protokollierung**.

	![Speicher\_logging](./media/storage-monitor-storage-account/Storage_LoggingOptions.png)


3. Konfigurieren Sie für jeden Dienst (Blob, Tabelle und Warteschlange) die folgenden Einstellungen:

	- Die zu protokollierenden Anforderungstypen: Leseanforderungen, Schreibanforderungen und Löschanforderungen.

	- Die Anzahl der Tage, die protokollierte Daten aufbewahrt werden sollen. Wenn Sie keine Aufbewahrungsrichtlinie festlegen möchten, geben Sie null (0) ein. Ist keine Aufbewahrungsrichtlinie festgelegt, müssen Sie die Protokolle selbst löschen.

4. Klicken Sie auf **Speichern**.

Die Diagnoseprotokolle werden in einem Blob-Container namens $logs in Ihren Speicherkonto gespeichert. Informationen zum Zugreifen auf den Container $logs finden Sie unter [Informationen zur Protokollierung durch die Speicheranalyse](http://msdn.microsoft.com/library/azure/hh343262.aspx).

<!---HONumber=AcomDC_0128_2016-->