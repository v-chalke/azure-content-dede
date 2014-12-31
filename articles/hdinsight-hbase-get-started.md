<properties urlDisplayName="Get Started" pageTitle="Erste Schritte mit HBase mit Hadoop in HDInsight | Azure" metaKeywords="" description="Erste Schritte mit HBase mit Hadoop in HDInsight. Erfahren Sie, wie Sie HBase-Tabellen erstellen und mit Hive abfragen k&ouml;nnen." metaCanonical="" services="hdinsight" documentationCenter="" title="Erste Schritte mit HBase mit Hadoop in HDInsight" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="bradsev" />

# Erste Schritte mit HBase mit Hadoop in HDInsight

HBase ist eine NoSQL-Datenbank mit niedriger Latenz und ermöglicht die Onlinetransaktionsverarbeitung großer Datenmengen. HBase ist als verwalteter Cluster integriert in die Azure-Umgebung verfügbar. Die Cluster sind so konfiguriert, dass sie Daten direkt in den Azure-Blob-Speicher speichern. Dies sorgt für geringe Latenz und mehr Flexibilität bei Entscheidungen bezüglich Leistung und Kosten. So können Kunden interaktive Websites erstellen, die mit großen Datensätzen arbeiten, und Dienste entwickeln, die Sensor- und Telemetriedaten aus Millionen von Endpunkten speichern. Diese Daten können dann mit Hadoop-Jobs analysiert werden.

In diesem Lernprogramm erfahren Sie, wie Sie HBase-Tabellen mit HDInsight erstellen und abfragen können. Die folgenden Vorgehensweisen werden beschrieben:

-   Bereitstellen eines HBase-Clusters im Azure-Portal.
-   Aktivieren und Verwenden von RDP für den Zugriff auf die HBase-Befehlszeile. Erstellen einer HBase-Beispieltabelle, Einfügen von Zeilen und anschließendes Auflisten der Tabellenzeilen über die Befehlszeile.
-   Erstellen einer Hive-Tabelle, die zu einer existierenden HBase-Tabelle zugeordnet ist und Ausführen von Datenabfragen in der HBase-Tabelle per HiveQL.
-   Erstellen einer neuen HBase-Tabelle, Auflisten der HBase-Tabellen in Ihrem Konto und Hinzufügen und Abrufen von Zeilen in Ihren Tabellen mit dem .NET SDK.

> [WACOM.NOTE] HBase (Version 0.98.0) ist nur für HDInsight-Cluster der Version 3.1 in HDInsight verfügbar (basiert auf Apache Hadoop und YARN 2.4.0). Versionshinweise finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen?][Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen?]

**Voraussetzungen:**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-   Ein Azure-Abonnement. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen][Kaufoptionen], [Spezielle Angebote][Spezielle Angebote] oder [Kostenlose Testversion][Kostenlose Testversion].
-   Ein Azure-Speicherkonto. Informationen dazu finden Sie unter [Erstellen eines Speicherkontos][Erstellen eines Speicherkontos].
-   Eine Kopie von Visual Studio.

**Geschätzter Zeitaufwand:** 30 Minuten

## Dieses Lernprogramm umfasst folgende Punkte

-   [Bereitstellen eines HBase-Clusters im Azure-Portal][Bereitstellen eines HBase-Clusters im Azure-Portal]
-   [Erstellen einer HBase-Beispieltabelle in der HBase-Befehlszeile][Erstellen einer HBase-Beispieltabelle in der HBase-Befehlszeile]
-   [Abfragen einer HBase-Tabelle mit Hive][Abfragen einer HBase-Tabelle mit Hive]
-   [Erstellen einer HBase-Tabelle und Abrufen von Daten aus der Tabelle mit den HBase C#-APIs][Erstellen einer HBase-Tabelle und Abrufen von Daten aus der Tabelle mit den HBase C#-APIs]
-   [Zusammenfassung][Zusammenfassung]
-   [Was kommt als Nächstes?][Was kommt als Nächstes?]

## <a name="create-hbase-cluster"></a>Bereitstellen eines HBase-Clusters im Azure-Portal

Dieser Abschnitt beschreibt die Bereitstellung eines HBase-Clusters im Azure-Portal.

[WACOM.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Bereitstellen eines HDInsight-Clusters im Azure-Portal**

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.

2.  Klicken Sie links auf **HDInsight**, um den Status der Cluster in Ihrem Konto anzuzeigen, und klicken Sie dann links unten auf das Symbol **+NEU**.

    ![][0]

3.  Klicken Sie auf das HDInsight-Symbol in der zweiten Spalte von links und anschließend auf die HBase-Optionen in der nächsten Spalte. Geben Sie die Werte für CLUSTERNAME und CLUSTERGRÖSSE den Namen des Speicherkontos und ein Kennwort für das neue HBase-Cluster ein.

    ![][1]

4.  Klicken Sie unten links auf das Häkchensymbol, um das HBase-Cluster zu erstellen.

## <a name="create-sample-table"></a>Erstellen einer HBase-Beispieltabelle in der HBase-Befehlszeile

Dieser Abschnitt beschreibt, wie Sie RDP für den Zugriff auf die HBase-Befehlszeile aktivieren und verwenden können und wie Sie eine HBase-Beispieltabelle erstellen, Zeilen einfügen und anschließend die Tabellenzeilen über die Befehlszeile auflisten können.

Es wird davon ausgegangen, dass Sie die im ersten Abschnitt beschriebene Prozedur ausgeführt haben und bereits über ein erfolgreich erstelltes HBase-Cluster verfügen.

**Aktivieren der RDP-Verbindung zum HBase-Cluster**

1.  Um die Remotedesktopverbindung für das HDInsight-Cluster zu aktivieren, wählen Sie das soeben erstellte HBase-Cluster aus und klicken Sie auf die Remotedesktopverbindung **KONFIGURATION**. Klicken Sie auf die Schaltfläche **REMOTE AKTIVIEREN** am unteren Seitenrand, um die RDP-Verbindung zum Cluster zu aktivieren.
2.  Geben Sie die Anmeldeinformationen und das Ablaufdatum im Assistenten **REMOTEDESKTOP KONFIGURIEREN** ein und klicken Sie auf das Häkchensymbol unten rechts. (Diese Operation kann einige Minuten in Anspruch nehmen.)
3.  Um sich mit dem HDInsight-Cluster zu verbinden, klicken Sie auf die Schaltfläche **VERBINDEN** am unteren Rand der Registerkarte **KONFIGURATION**.

**Öffnen der HBase-Befehlszeile**

1.  Klicken Sie innerhalb Ihrer RDP-Sitzung auf die Verknüpfung **Hadoop-Befehlszeile** auf dem Desktop.

2.  Wechseln Sie in das HBase-Stammverzeichnis:

        cd %HBASE_HOME%\bin

3.  Öffnen Sie die HBase-Befehlszeile:

        hbase shell

**Erstellen einer Beispieltabelle, Hinzufügen von Daten und Abrufen der Daten**

1.  Erstellen einer Beispieltabelle:

        create 'sampletable', 'cf1'

2.  Hinzufügen einer Zeile zur Beispieltabelle:

        put 'sampletable', 'row1', 'cf1:col1', 'value1'

3.  Auflisten der Zeilen in der Beispieltabelle:

        scan 'sampletable'

## <a name="hive-query"></a>Abfragen einer HBase-Tabelle mit Hive

Sie haben nun ein HBase-Cluster eingerichtet und eine Tabelle erstellt und können nun Abfragen mit Hive ausführen. In diesem Abschnitt erstellen Sie eine Hive-Tabelle, die zu Ihrer HBase-Tabelle zugeordnet ist und verwenden diese Tabelle, um die Daten in Ihrer HBase-Tabelle abzufragen.

**Öffnen des Cluster-Dashboards**

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Klicken Sie im linken Bereich auf **HDINSIGHT**. Sie sehen eine Liste der erstellten Cluster, inklusive des Clusters, das Sie im vorigen Abschnitt erstellt haben.
3.  Klicken Sie auf den Namen des Clusters, in dem Sie den Hive-Job ausführen möchten.
4.  Klicken Sie unten in der Seite auf **CLUSTER VERWALTEN**, um das Cluster-Dashboard zu öffnen. Daraufhin wird eine Webseite in einer neuen Browser-Registerkarte geöffnet.
5.  Geben Sie den Benutzernamen und das Kennwort des Hadoop-Benutzers ein. Der Standard-Benutzername ist **admin**, und das Kennwort haben Sie bei der Einrichtung ausgewählt. Das Dashboard sieht folgendermaßen aus:

    ![][2]

**So führen Sie eine Hive-Abfrage aus**

1.  Um eine Hive-Tabelle mit einer Zuordnung zur HBase-Tabelle zu erstellen, geben Sie das folgende HiveQL-Skript in das Hive-Konsolenfenster ein und klicken Sie auf **ABSCHICKEN**. Stellen Sie sicher, dass Sie die hier beschriebene Beispieltabelle in HBase mit der HBase-Befehlszeile erstellt haben, bevor Sie diese Anweisung ausführen.

        CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
        TBLPROPERTIES ('hbase.table.name' = 'sampletable');

2.  Um eine Hive-Abfrage über die Daten in HBase auszuführen, geben Sie das folgende HiveQL-Skript in das Hive-Konsolenfenster ein und klicken Sie auf **ABSCHICKEN**.

        SELECT count(*) FROM hbasesampletable;

3.  Um die Ergebnisse der Hive-Abfrage anzuzeigen, klicken Sie auf den Link **Details anzeigen** im Fenster **Job-Sitzung**, nachdem der Job abgeschlossen wurde.

Hinweis: Mit dem Link zur HBase-Befehlszeile können Sie zur **HBase-Befehlszeile** wechseln.

**So durchsuchen Sie die Ausgabedatei**

1.  Klicken Sie oben im Cluster-Dashboard auf **Dateien**.
2.  Klicken Sie auf **Templeton-Job-Status**.
3.  Klicken Sie auf die GUID-Nummer, deren letzte Änderung kurz nach der Startzeit des Jobs liegt, die Sie zuvor notiert haben. Notieren Sie sich die GUID. Sie benötigen sie im nächsten Abschnitt.
4.  Die **stdout**-Datei enthält die Daten, die Sie für den nächsten Abschnitt benötigen. Sie können **stdout** anklicken, falls Sie eine Kopie der Daten herunterladen möchten.

## <a name="hbase-powershell"></a>Erstellen einer HBase-Tabelle und Abrufen von Daten aus der Tabelle mit den HBase C#-APIs

Marlin ist eine dünne Ebene oberhalb der REST API zur Verwaltung der Interaktion mit HBase mithilfe von ProtoBuf in C#. Sie müssen das Marlin-Projekt von github herunterladen und unter Verwendung der HBase .NET SDK erstellen.

1.  Führen Sie dazu die Schritte aus, die auf der Downloadseite des Marlin-Projekts auf der [Marlin-Projektseite][Marlin-Projektseite] beschrieben werden. Entzippen Sie das Archiv in ein lokales Verzeichnis.

2.  Öffnen Sie das Projekt in Visual Studio. Öffnen Sie den Assistenten zum Verwalten von NuGet-Paketen über das Menü **EXTRAS** -\> **Bibliothekspaket-Manager** und den Eintrag **NuGet-Pakete für Lösung verwalten ...**

    ![][3]

3.  Suchen Sie oben rechts im Online-Suchfeld nach protobuf-net und installieren Sie die Version v2.0.0.68. Erstellen Sie das Marlin-Projekt, indem Sie mit der rechten Maustaste auf das **Marlin**-Projekt im **Projektmappen-Explorer** klicken und **Erstellen** auswählen.

4.  Suchen Sie die resultierende Datei marlin.dll und fügen Sie sie zu Ihrem C#-Projekt hinzu.

5.  Erstellen Sie eine neue Instanz von Marlin und mit den Cluster-Anmeldeinformationen und rufen Sie die Clusterversion ab:

        var credentials = ClusterCredentials.Create("https://yourclustername.azurehdinsight.net/", "user", "password");
            var marlin = new Marlin(credentials);
        // retrieve the version as a test
        var version = marlin.GetVersion();
        Console.WriteLine(version);

6.  Mit dem folgenden Code können Sie die Tabellen im Cluster auflisten:

        var tables = marlin.ListTables();
        foreach(var tableName in tables.name) 
                Console.WriteLine(tableName);

7.  Mit dem folgenden Code können Sie eine neue HBase-Tabelle erstellen:

        var schema = new TableSchema();
        schema.name = "sampletable";
        schema.columns.Add(new ColumnSchema() { name = "cf1" });
        schema.columns.Add(new ColumnSchema() { name = "cf2" });
        marlin.CreateTable(schema);

8.  Um eine Zeile nach deren Schlüssel abzufragen, geben Sie den Tabellennamen und einen Zeilenschlüssel an.

        var cells = marlin.GetCells("sampletable", "row1");
        var row = cells.rows[0];
            foreach(var val in row.values) 
            {
               Console.WriteLine(Encoding.UTF8.GetString(val.data));
            }

9.  Mit dem folgenden Code können Sie eine neue Datenzeile anlegen:

        CellSet set = new CellSet();
        CellSet.Row row = new CellSet.Row() { key = BitConverter.GetBytes(1337) };
            var value = new Cell()
                    {
                        column = Encoding.UTF8.GetBytes("cf1:d"),
                        data = Encoding.UTF8.GetBytes("Hello World!")
                    };
            row.values.Add(value);
            set.rows.Add(row);
        marlin.StoreCells("sampletable", set);

## <a name="summary"></a>Zusammenfassung

In diesem Lernprogramm haben Sie erfahren, wie Sie ein HBase-Cluster bereitstellen, Tabellen erstellen und die Daten in diesen Tabellen in der HBase-Befehlszeile anzeigen können. Außerdem haben Sie gelernt, wie Sie die Daten in HBase-Tabellen mit Hive abfragen und mit den HBase C#-APIs HBase-Tabellen erstellen und Daten aus der Tabelle abrufen können.

## <a name="next"></a> Wie geht es weiter?

[HBase mit HDInsight: Übersicht][HBase mit HDInsight: Übersicht]:
HBase ist eine Open-Source-NoSQL-Datenbank von Apache, die auf Hadoop aufbaut und wahlfreien Zugriff sowie starke Konsistenz für große Mengen unstrukturierter und teilstrukturierter Daten bietet.

[Bereitstellen von HBase-Clustern im virtuellen Azure-Netzwerk][Bereitstellen von HBase-Clustern im virtuellen Azure-Netzwerk]:
Mit der Integration in virtuelle Netzwerke können HBase-Cluster im selben virtuellen Netzwerk bereitgestellt werden wie Ihre Anwendungen, sodass Anwendungen direkt mit HBase kommunizieren können.

  [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen?]: ../hdinsight-component-versioning/
  [Kaufoptionen]: http://azure.microsoft.com/de-de/pricing/purchase-options/
  [Spezielle Angebote]: http://azure.microsoft.com/de-de/pricing/member-offers/
  [Kostenlose Testversion]: http://azure.microsoft.com/de-de/pricing/free-trial/
  [Erstellen eines Speicherkontos]: http://azure.microsoft.com/de-de/documentation/articles/storage-create-storage-account/
  [Bereitstellen eines HBase-Clusters im Azure-Portal]: #create-hbase-cluster
  [Erstellen einer HBase-Beispieltabelle in der HBase-Befehlszeile]: #create-sample-table
  [Abfragen einer HBase-Tabelle mit Hive]: #hive-query
  [Erstellen einer HBase-Tabelle und Abrufen von Daten aus der Tabelle mit den HBase C#-APIs]: #hbase-powershell
  [Zusammenfassung]: #summary
  [Was kommt als Nächstes?]: #next
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [0]: http://i.imgur.com/PmGynKZ.jpg
  [1]: http://i.imgur.com/ecxbB9K.jpg
  [2]: http://i.imgur.com/tMwXlj9.jpg
  [Marlin-Projektseite]: https://github.com/thomasjungblut/marlin
  [3]: http://i.imgur.com/hUNoJDJ.jpg
  [HBase mit HDInsight: Übersicht]: ../hdinsight-hbase-overview/
  [Bereitstellen von HBase-Clustern im virtuellen Azure-Netzwerk]: ../hdinsight-hbase-provision-vnet