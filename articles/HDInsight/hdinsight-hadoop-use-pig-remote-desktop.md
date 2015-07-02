<properties
   pageTitle="Verwenden von Pig mit Remotedesktop in HDInsight | Microsoft Azure"
   description="Erfahren Sie, wie Sie mit dem Pig-Befehl Pig Latin-Anweisungen über eine Remotedesktopverbindung zu einem Windows-basierten HDInsight-Cluster ausführen können."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/03/2015"
   ms.author="larryfr"/>

#Ausführen von Pig-Aufträgen über eine Remotedesktopverbindung

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Dieses Dokument bietet eine exemplarische Vorgehensweise für die Verwendung des Pig-Befehls zum Ausführen von Pig Latin-Anweisungen über eine Remotedesktopverbindung zu einem Windows-basierten HDInsight-Cluster. Mithilfe von Pig Latin können Sie MapReduce-Anwendungen erstellen, indem Sie Datentransformationen beschreiben, anstatt Map- und Reduce-Funktionen zu verwenden.

In diesem Dokument erfahren Sie Folgendes:

##<a id="prereq"></a>Voraussetzungen

Damit Sie die in dieser Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* Einen Windows-basierten HDInsight-Cluster (Hadoop in HDInsight)

* Einen Clientcomputer mit einem der Betriebssysteme Windows 10, Windows 8 oder Windows 7

##<a id="connect"></a>Verbinden mit dem Remotedesktop

Aktivieren Sie Remotedesktop für den HDInsight-Cluster, und stellen Sie anschließend mithilfe der Anweisungen unter <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Herstellen einer Verbindung zu HDInsight-Clustern mit RDP</a> eine Verbindung her.

##<a id="pig"></a>Verwenden des Pig-Befehls

2. Nachdem Sie die Remotedesktopverbindung hergestellt haben, starten Sie die **Hadoop-Befehlszeile** über das Symbol auf dem Desktop.

2. Starten Sie den Pig-Befehl wie folgt:

		%pig_home%\bin\pig

	Es wird eine `grunt>`-Eingabeaufforderung angezeigt.

3. Geben Sie die folgende Anweisung ein:

		LOGS = LOAD 'wasb:///example/data/sample.log';

	Dieser Befehl lädt den Inhalt der Datei "sample.log" in die Datei "LOGS". Sie können den Inhalt der Datei mit dem folgenden Befehl anzeigen:

		DUMP LOGS;

4. Transformieren Sie die Daten durch Anwendung eines regulären Ausdrucks, um nur den Protokolliergrad aus jedem Datensatz zu extrahieren.

		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

	Sie können **DUMP** zum Anzeigen der Daten nach der Transformation verwenden. In diesem Fall `DUMP LEVELS;`.

5. Fahren Sie mit der Anwendung der Transformationen mithilfe der folgenden Anweisungen fort. Verwenden Sie `DUMP`, um das Ergebnis der Transformation nach jedem Schritt anzuzeigen.

	<table>
<tr>
<th>Anweisung</th><th>Funktionsbeschreibung</th>
</tr>
<tr>
<td>FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;</td><td>Entfernt Zeilen mit NULL-Wert für die Protokollebene und speichert die Ergebnisse in FILTEREDLEVELS.</td>
</tr>
<tr>
<td>GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;</td><td>Gruppiert die Zeilen nach Protokollebene und speichert die Ergebnisse in GROUPEDLEVELS.</td>
</tr>
<tr>
<td>FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;</td><td>Erstellt eine neue Gruppe von Daten, die jeden eindeutigen Protokollebenenwert und die Häufigkeit seines Auftretens enthält. Dies wird in FREQUENCIES gespeichert.</td>
</tr>
<tr>
<td>RESULT = order FREQUENCIES by COUNT desc;</td><td>Ordnet die Protokollebenen nach Anzahl (absteigend) und speichert sie in RESULT.</td>
</tr>
</table>

6. Sie können die Ergebnisse einer Transformation auch mithilfe der `STORE`-Anweisung speichern. Das folgende Beispiel speichert das `RESULT` im Verzeichnis **/example/data/pigout** des Standardspeichercontainers für Ihren Cluster.

		STORE RESULT into 'wasb:///example/data/pigout'

	> [AZURE.NOTE]Die Daten werden im angegebenen Verzeichnis in Dateien namens **part-nnnnn** gespeichert. Wenn das Verzeichnis bereits vorhanden ist, wird ein Fehler ausgegeben.

7. Geben Sie die folgende Anweisung ein, um die grunt-Eingabeaufforderung zu beenden.

		QUIT;

###Pig Latin-Batchdateien

Den Pig-Befehl können Sie auch zum Ausführen von Pig Latin verwenden, das in einer Datei enthalten ist.

3. Öffnen Sie nach dem Beenden der grunt-Eingabeaufforderung den **Editor**, und erstellen Sie eine neue Datei namens **pigbatch.pig** im Verzeichnis **%PIG_HOME%**.

4. Geben Sie die folgenden Zeilen in die Datei **pigbatch.pig** ein, oder fügen Sie sie ein, und speichern Sie die Datei dann.

		LOGS = LOAD 'wasb:///example/data/sample.log';
		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
		FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		RESULT = order FREQUENCIES by COUNT desc;
		DUMP RESULT;

5. Gehen Sie wie folgt vor, um die Datei **pigbatch.pig** mit dem Pig-Befehl auszuführen.

		pig %PIG_HOME%\pigbatch.pig

	Wenn der Batchauftrag abgeschlossen ist, sollte die folgende Ausgabe angezeigt werden. Diese sollte mit der Ausgabe übereinstimmen, die Sie bei Verwendung von `DUMP RESULT;` in den vorherigen Schritten erhalten haben:

		(TRACE,816)
		(DEBUG,434)
		(INFO,96)
		(WARN,11)
		(ERROR,6)
		(FATAL,2)

##<a id="summary"></a>Zusammenfassung

Wie Sie sehen, können Sie mit dem Pig-Befehl MapReduce-Vorgänge interaktiv ausführen oder in einer Batchdatei gespeicherte Pig Latin-Aufträge ausführen.

##<a id="nextsteps"></a>Nächste Schritte

Allgemeine Informationen zu Pig in HDInsight:

* [Verwenden von Pig mit Hadoop in HDInsight](hdinsight-use-pig.md)

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von Hive mit Hadoop in HDInsight](hdinsight-use-hive.md)

* [Verwenden von MapReduce mit Hadoop in HDInsight](hdinsight-use-mapreduce.md)

<!--HONumber=54--> 