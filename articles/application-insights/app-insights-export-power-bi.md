<properties 
	pageTitle="Anzeigen von Application Insights-Daten in Power BI" 
	description="Verwenden Sie Power BI zum Überwachen der Leistung und der Nutzung Ihrer Anwendung." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="awills"/>
 
# Power BI-Ansichten von Application Insights-Daten

[Microsoft Power BI](https://powerbi.microsoft.com/) stellt die Daten mit umfangreichen und unterschiedlichen Grafiken dar und bietet die Möglichkeit, Informationen aus mehreren Quellen zu kombinieren. Sie können die Telemetriedaten über die Leistung und Nutzung Ihrer Web- oder Gerät-Apps von Application Insights zu Power BI streamen.

![Beispiel für eine Power BI-Ansicht der Application Insights-Nutzungsdaten](./media/app-insights-export-power-bi/010.png)

In diesem Artikel zeigen wir, wie Daten von Application Insights exportiert werden und wie Stream Analytics zum Verschieben der Daten nach Power BI verwendet wird. [Stream Analytics](http://azure.microsoft.com/services/stream-analytics/) ist ein Azure-Dienst, der als Adapter verwendet wird.

![Beispiel für eine Power BI-Ansicht der Application Insights-Nutzungsdaten](./media/app-insights-export-power-bi/020.png)

## Video

Noam Ben Zeev zeigt, was in diesem Artikel beschrieben wird.

> [AZURE.VIDEO export-to-power-bi-from-application-insights]

## Überwachen Ihrer App mit Application Insights

Wenn Sie es noch nicht ausprobiert haben, ist jetzt der richtige Zeitpunkt, um zu beginnen. Mit Application Insights können alle Geräte- oder Web-Apps auf einer Vielzahl von Plattformen überwacht werden, einschließlich Windows, iOS, Android, J2EE und weiteren. [Erste Schritte](app-insights-get-started.md)

## Erstellen von Speicher in Azure

Durch fortlaufende Exportaktivitäten werden Daten an ein Azure-Speicherkonto übertragen, daher müssen Sie zuerst Speicher erstellen.

1. Erstellen Sie ein Speicherkonto in Ihrem Abonnement im [Azure-Portal](https://portal.azure.com).

    ![Wählen Sie im Azure-Portal "Neu", "Daten" und "Speicher".](./media/app-insights-export-power-bi/030.png)

2. Erstellen eines Containers

    ![Wählen Sie im neuen Speicher "Container" und anschließend "Hinzufügen".](./media/app-insights-export-power-bi/040.png)

3. Kopieren Sie den Speicherzugriffsschlüssel.

    Sie benötigen ihn bald, um die Eingabe für den Stream Analytics-Dienst einzurichten.

    ![Öffnen Sie im Speicher die Optionen "Einstellungen", "Schlüssel", und kopieren Sie den primären Zugriffsschlüssel.](./media/app-insights-export-power-bi/045.png)

## Starten des fortlaufenden Exports in den Azure-Speicher

Mit dem [fortlaufenden Export](app-insights-export-telemetry.md) werden Daten aus Application Insights in den Azure-Speicher verschoben.

1. Navigieren Sie im Azure-Portal zu der Application Insights-Ressource, die Sie für Ihre Anwendung erstellt haben.

    ![Wählen Sie "Durchsuchen", "Application Insights" und Ihre Anwendung aus.](./media/app-insights-export-power-bi/050.png)

2. Erstellen Sie einen fortlaufenden Export.

    ![Wählen Sie "Einstellungen", "Fortlaufender Export", "Hinzufügen".](./media/app-insights-export-power-bi/060.png)


    Wählen Sie das zuvor erstellte Speicherkonto aus:

    ![Legen Sie das Exportziel fest.](./media/app-insights-export-power-bi/070.png)
    
    Legen Sie die Ereignistypen fest, die Sie anzeigen möchten:

    ![Wählen Sie Ereignistypen aus.](./media/app-insights-export-power-bi/080.png)

Jetzt lehnen Sie sich zurück, und lassen Sie Ihre Benutzer die Anwendung eine Weile lang verwenden. Telemetriedaten gehen ein, und Sie sehen statistische Diagramme im [Metrik-Explorer](app-insights-metrics-explorer.md) sowie einzelne Ereignisse in der [Diagnosesuche](app-insights-diagnostic-search.md).

Darüber hinaus werden die Daten in Ihren Speicher exportiert.

## Erstellen einer Azure Stream Analytics-Instanz

Wählen Sie im [klassischen Azure-Portal](https://manage.windowsazure.com/) den Azure Stream Analytics-Dienst aus, und erstellen Sie einen neuen Stream Analytics-Auftrags:


![](./media/app-insights-export-power-bi/090.png)



![](./media/app-insights-export-power-bi/100.png)

Wenn der neue Auftrag erstellt ist, erweitern Sie die Details:

![](./media/app-insights-export-power-bi/110.png)


#### Festlegen des Blobspeicherorts

Legen Sie den Auftrag so fest, dass er Eingaben vom Blob für den fortlaufenden Export erhält:

![](./media/app-insights-export-power-bi/120.png)

Jetzt benötigen Sie den primären Zugriffsschlüssel aus Ihrem Speicherkonto, den Sie zuvor notiert haben. Legen Sie diesen als Speicherkontoschlüssel fest.

![](./media/app-insights-export-power-bi/130.png)

#### Festlegen des Präfixmusters des Pfads 

![](./media/app-insights-export-power-bi/140.png)

Achten Sie darauf, dass das Datum das Format "JJJJ-MM-TT" \(mit Bindestrichen\) aufweist.

Das Präfixmuster des Pfads gibt an, wie Stream Analytics die Eingabedateien im Speicher ermittelt. Sie müssen es so einrichten, dass es der Speicherung der Daten durch den fortlaufenden Export entspricht. Legen Sie ihn wie folgt fest:

    webapplication27_100000000-0000-0000-0000-000000000000/PageViews/{date}/{time}

In diesem Beispiel:

* `webapplication27` ist der Name der Application Insights-Ressource. 
* `1000...` Ist der Instrumentationsschlüssel der Application Insights-Ressource. 
* `PageViews` ist die Art der zu analysierenden Daten. Die verfügbaren Typen sind abhängig von dem Filter, den Sie im fortlaufenden Export festlegen. Untersuchen Sie die exportierten Daten, um die anderen verfügbaren Typen anzuzeigen.
* `/{date}/{time}` ist ein als Literal geschriebenes Muster.

Um den Namen und iKey Ihrer Application Insights-Ressource zu erhalten, öffnen Sie auf der Seite "Übersicht" den Eintrag "Essentials", oder öffnen Sie "Einstellungen".

#### Fertig stellen des ersten Setups

Bestätigen Sie das Serialisierungsformat:

![Bestätigen Sie den Vorgang, und schließen Sie den Assistenten.](./media/app-insights-export-power-bi/150.png)

Schließen Sie den Assistenten, und warten Sie, bis das Setup abgeschlossen ist.

## Festlegen der Ausgabe

Nun wählen Sie Ihren Auftrag aus und legen die Ausgabe fest.

![Wählen Sie den neuen Kanal aus, klicken Sie auf "Ausgaben", "Hinzufügen", "Power BI".](./media/app-insights-export-power-bi/160.png)

Autorisieren Sie Stream Analytics für den Zugriff auf Ihre Power BI-Ressource, und erstellen Sie dann einen Namen für die Ausgabe, für das Power BI-Zieldataset und für die Tabelle.

![Erfinden Sie drei Namen.](./media/app-insights-export-power-bi/170.png)

## Festlegen der Abfrage

Die Abfrage bestimmt die Übersetzung von der Eingabe zur Ausgabe.

![Wählen Sie den Auftrag aus, und klicken Sie auf "Abfrage". Fügen Sie das folgenden Beispiel ein.](./media/app-insights-export-power-bi/180.png)

Fügen Sie diese Abfrage ein:

```SQL

    SELECT
      flat.ArrayValue.name
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* "export-input" ist der Alias, den wir der Datenstromeingabe gegeben haben.
* "pbi-outout" ist der definierte Ausgabealias.
* Wir verwenden GetElements, da sich der Ereignisname in einem geschachtelten JSON-Arrray befindet. Die SELECT-Anweisung wählt dann den Ereignisnamen zusammen mit der Anzahl der Instanzen mit diesem Namen im angegebenen Zeitraum aus. Die Group By-Klausel gruppiert die Elemente in Zeiträume von 1 Minute.

## Ausführen des Auftrags

Sie können ein Datum in der Vergangenheit auswählen, ab dem der Auftrag ausgeführt werden soll.

![Wählen Sie den Auftrag aus, und klicken Sie auf "Abfrage". Fügen Sie das folgenden Beispiel ein.](./media/app-insights-export-power-bi/190.png)

Warten Sie, bis der Auftrag ausgeführt wird.

## Anzeigen der Ergebnisse in Power BI

Öffnen Sie Power BI, und wählen Sie das Dataset und die Tabelle aus, die Sie als Ausgabe des Stream Analytics-Auftrags definiert haben.

![Wählen Sie in Power BI Ihr Dataset und Felder aus.](./media/app-insights-export-power-bi/200.png)

Jetzt können Sie dieses Dataset in Berichten und Dashboards in [Power BI](https://powerbi.microsoft.com) verwenden.


![Wählen Sie in Power BI Ihr Dataset und Felder aus.](./media/app-insights-export-power-bi/210.png)

## Video

Noam Ben Zeev zeigt, wie nach Power BI exportiert wird.

> [AZURE.VIDEO export-to-power-bi-from-application-insights]

## Verwandte Themen

* [Fortlaufendem Export](app-insights-export-telemetry.md)
* [Application Insights](app-insights-overview.md)

<!---HONumber=July15_HO5-->