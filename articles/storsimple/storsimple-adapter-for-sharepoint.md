<properties 
   pageTitle="StorSimple-Adapter für SharePoint | Microsoft Azure"
   description="Beschreibt die Installation des StorSimple-Adapters für SharePoint in einer SharePoint-Serverfarm."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/17/2015"
   ms.author="v-sharos" />

# StorSimple-Adapter für SharePoint

## Übersicht

Der StorSimple-Adapter für SharePoint ist eine Komponente, mit der Sie flexible Microsoft Azure StorSimple-Speicher- und Datenschutzfunktionen für SharePoint-Serverfarmen bereitstellen können. Sie können den Adapter verwenden, um BLOB-Inhalte \(Binary Large Object\) aus den SQL Server-Inhaltsdatenbanken auf das Microsoft Azure StorSimple-Hybrid-Cloudspeichergerät zu verschieben.

Der StorSimple-Adapter für SharePoint dient als Remote BLOB Storage \(RBS\)-Anbieter und nutzt die SQL Server Remote BLOB Storage-Funktion zum Speichern von nicht strukturiertem SharePoint-Inhalt \(in Form von BLOBs\) auf einem Dateiserver, der von einem StorSimple-Gerät unterstützt wird.

>[AZURE.NOTE]Der StorSimple-Adapter für SharePoint unterstützt SharePoint Server 2010 Remote BLOB Storage \(RBS\). SharePoint Server 2010 External BLOB Storage \(EBS\) wird nicht unterstützt.

- Sie können den StorSimple-Adapter für SharePoint im Microsoft Download Center unter [StorSimple-Adapter für SharePoint][1] herunterladen.

- Informationen zur Planung für RBS und RBS-Einschränkungen finden Sie unter [Verwenden von RBS in SharePoint 2013][2] und [Planen von RBS (SharePoint Server 2010)][3].

Im Rest dieser Übersicht werden kurz die Rolle des StorSimple-Adapters für SharePoint und die SharePoint-Kapazitäts- und Leistungsgrenzen beschrieben, mit denen Sie vertraut sein sollten, bevor Sie den Adapter installieren und konfigurieren. Wechseln Sie nach dem Lesen dieser Informationen zu [Installation des StorSimple-Adapters für SharePoint](#storsimple-adapter-for-sharepoint-installation), um mit der Einrichtung des Adapters zu beginnen.

### Vorteile des StorSimple-Adapters für SharePoint

Auf einer SharePoint-Website wird Inhalt in Form von nicht strukturierten BLOB-Daten in einer oder mehreren Inhaltsdatenbanken gespeichert. Standardmäßig werden diese Datenbanken auf Computern gehostet, auf denen SQL Server ausgeführt wird und die Teil einer SharePoint-Serverfarm sind. Die Größe von BLOBs kann schnell zunehmen und viel lokalen Speicherplatz belegen. Aus diesem Grund kann es ratsam sein, sich nach einer anderen kostengünstigeren Speicherlösung umzusehen. SQL Server verfügt über eine Technologie mit dem Namen Remote Blob Storage \(RBS\), mit der Sie BLOB-Inhalt im Dateisystem außerhalb der SQL Server-Datenbank speichern können. Bei RBS können sich BLOBs im Dateisystem auf dem Computer befinden, auf dem SQL Server ausgeführt wird, oder sie können im Dateisystem auf einem anderen Servercomputer gespeichert werden.

RBS erfordert die Verwendung eines RBS-Anbieters, z. B. den StorSimple-Adapter für SharePoint, um RBS unter SharePoint zu aktivieren. Der StorSimple-Adapter für SharePoint funktioniert zusammen mit RBS, sodass Sie BLOBs auf einen Server verschieben können, der mit dem Microsoft Azure StorSimple-System gesichert wird. Microsoft Azure StorSimple speichert die BLOB-Daten dann basierend auf ihrer Verwendung lokal oder in der Cloud. BLOBs, die sehr aktiv sind \(meist als Tier 1-Daten oder „Hot Data“ bezeichnet\), sind lokal gespeichert. Weniger aktive Daten und Archivdaten befinden sich in der Cloud. Nachdem Sie RBS für eine Inhaltsdatenbank aktiviert haben, werden alle in SharePoint neu erstellten BLOB-Inhalte auf dem StorSimple-Gerät gespeichert, und nicht in der Inhaltsdatenbank.

Die Microsoft Azure StorSimple-Implementierung von RBS bietet die folgenden Vorteile:

- Indem Sie BLOB-Inhalt auf einen separaten Server verschieben, können Sie die Abfragenlast für SQL Server reduzieren. So kann die Reaktionsfähigkeit von SQL Server verbessert werden. 

- Azure StorSimple verwendet die Deduplizierung und Komprimierung, um die Datengröße zu reduzieren.

- Azure StorSimple sorgt durch Momentaufnahmen lokal und in der Cloud für den Schutz der Daten. Wenn Sie die Datenbank selbst auf dem StorSimple-Gerät anordnen, können Sie die Inhaltsdatenbank und BLOBs zusammen auf ausfallsichere Weise sichern. \(Das Verschieben der Inhaltsdatenbank auf das Gerät wird nur für das Gerät der Serie StorSimple 8000 unterstützt. Diese Funktion wird für die Serien 5000 und 7000 nicht unterstützt.\)

- Azure StorSimple enthält Funktionen für die Notfallwiederherstellung, z. B. Failover, Datei- und Volumewiederherstellung \(einschließlich Testwiederherstellung\) und die schnelle Wiederherstellung von Daten.

- Sie können Software für die Datenwiederherstellung, z. B. Kroll Ontrack PowerControls, mit StorSimple-Momentaufnahmen von BLOB-Daten verwenden, um eine Wiederherstellung von SharePoint-Inhalten auf Elementebene durchzuführen. \(Diese Software für die Datenwiederherstellung muss separat erworben werden.\)

- Der StorSimple-Adapter für SharePoint wird mit dem SharePoint-Zentraladministration-Portal verknüpft, damit Sie Ihre gesamte SharePoint-Lösung von einem zentralen Ort aus verwalten können.

Das Verschieben von BLOB-Inhalten in das Dateisystem kann auch zu weiteren Kosteneinsparungen und Vorteilen führen. Mit der Nutzung von RBS können Sie beispielsweise den Umfang der teuren Tier 1-Speicherung reduzieren. Da RBS außerdem eine Verkleinerung der Inhaltsdatenbank bewirkt, kann damit die Anzahl von Datenbanken verringert werden, die in der SharePoint-Serverfarm erforderlich sind. Andere Faktoren, z. B. Größenbeschränkungen für Datenbanken und die Menge an Nicht-RBS-Inhalt, können sich aber ebenfalls auf die Speicheranforderungen auswirken. Weitere Informationen zu den Kosten und Vorteilen der Verwendung von RBS finden Sie unter [Planen von RBS (SharePoint Server 2010)][4] und [Verwenden von RBS in SharePoint 2013][5].

### Kapazitäts- und Leistungsgrenzen

Bevor Sie die Verwendung von RBS in Ihrer SharePoint-Lösung erwägen, sollten Sie sich über die getesteten Leistungs- und Kapazitätsgrenzen von SharePoint Server 2010 und SharePoint Server 2013 sowie den Zusammenhang dieser Grenzen mit einer akzeptablen Leistung informieren. Weitere Informationen finden Sie unter „Softwarebeschränkungen und -grenzen für SharePoint 2013“.

Überprüfen Sie Folgendes, bevor Sie RBS konfigurieren:

- Stellen Sie sicher, dass die Gesamtgröße des Inhalts \(die Größe einer Inhaltsdatenbank zzgl. der Größe aller zugeordneten externen BLOBs\) nicht die RBS-Größenbeschränkung überschreitet, die von SharePoint unterstützt wird. Dieser Grenzwert beträgt 200 GB. 

    **So messen Sie die Größe der Inhaltsdatenbank und BLOBs**

     1. Führen Sie die Abfrage im Zentraladministration-WFE aus. Starten Sie die SharePoint-Verwaltungsshell, und geben Sie dann den folgenden Windows PowerShell-Befehl ein, um die Größe der Inhaltsdatenbanken abzurufen:

        `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`

         In diesem Schritt wird die Größe der Inhaltsdatenbank auf dem Datenträger abgerufen.

     2. Führen Sie eine der folgenden SQL-Abfragen in SQL Management Studio im SQL Server-Feld für jede Inhaltsdatenbank aus, und fügen Sie das Ergebnis der Zahl hinzu, die Sie im ersten Schritt abgerufen haben.

        Geben Sie für SharePoint 2013-Inhaltsdatenbanken Folgendes ein:

        `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`

        Geben Sie für SharePoint 2010-Inhaltsdatenbanken Folgendes ein:

        `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`

        In diesem Schritt wird die Größe der BLOBs abgerufen, die extern ausgelagert wurden.

- Es wird empfohlen, alle BLOB- und Datenbankinhalte lokal auf dem StorSimple-Gerät zu speichern. Das StorSimple-Gerät ist ein Cluster mit zwei Knoten für hohe Verfügbarkeit. Wenn Sie die Inhaltsdatenbanken und BLOBs auf dem StorSimple-Gerät anordnen, sorgt dies für hohe Verfügbarkeit.

    Wenden Sie die herkömmlichen bewährten Methoden für die SQL Server-Migration an, um die Inhaltsdatenbank auf das StorSimple-Gerät zu verschieben. Verschieben Sie die Datenbank erst, nachdem der gesamte BLOB-Inhalt aus der Datenbank per RBS auf die Dateifreigabe verschoben wurde. Wenn Sie die Inhaltsdatenbank auf das StorSimple-Gerät verschieben möchten, empfehlen wir, den Inhaltsdatenbankspeicher auf dem Gerät als primäres Volume zu konfigurieren.

- In Microsoft Azure StorSimple gibt es keine Möglichkeit sicherzustellen, dass lokal auf dem StorSimple-Gerät gespeicherter Inhalt nicht mehrstufig im Microsoft Azure-Cloudspeicher abgelegt wird. Um dafür zu sorgen, dass die Inhaltsdatenbank auf dem StorSimple-Gerät verbleibt und nicht nach Microsoft Azure verschoben wird \(was sich negativ auf die Reaktionszeiten der SharePoint-Transaktionen auswirken würde\), ist es wichtig, die anderen Workloads auf dem StorSimple-Gerät zu verstehen und zu verwalten. Es wird empfohlen, kein StorSimple-Gerät zum Hosten von Workloads mit hohen Raten von Schreibvorgängen zu konfigurieren, wenn das Gerät bereits SharePoint-Inhaltsdatenbank-Workloads und SharePoint-Dateifreigabe-Workloads hostet.

- Wenn Sie die Inhaltsdatenbanken nicht auf dem StorSimple-Gerät speichern, sollten Sie herkömmliche bewährte Methoden für die hohe Verfügbarkeit von SQL Server verwenden, bei denen RBS unterstützt wird. SQL Server-Clustering unterstützt RBS, während dies für die SQL Server-Spiegelung nicht der Fall ist.

>[AZURE.WARNING] Wenn Sie RBS nicht aktiviert haben, ist es nicht ratsam, die Inhaltsdatenbank auf das StorSimple-Gerät zu verschieben. Dies ist eine nicht getestete Konfiguration.
 
## Installation des StorSimple-Adapters für SharePoint

Vor der Installation des StorSimple-Adapters für SharePoint müssen Sie das StorSimple-Gerät konfigurieren und sicherstellen, dass die SharePoint-Serverfarm und die SQL Server-Instanziierung alle Voraussetzungen erfüllen. In diesem Tutorial werden die Konfigurationsanforderungen und die Verfahren zum Installieren und Aktualisieren des StorSimple-Adapters für SharePoint beschrieben.

## Konfigurieren der Voraussetzungen

Stellen Sie vor der Installation des StorSimple-Adapters für SharePoint sicher, dass das StorSimple-Gerät, die SharePoint-Serverfarm und die SQL Server-Instanziierung alle folgenden Voraussetzungen erfüllen.

### Systemanforderungen

Der StorSimple-Adapter für SharePoint funktioniert mit der folgenden Hardware und Software:

- Unterstützte Betriebssysteme – Windows Server 2008 R2 SP1, Windows Server 2012 und Windows Server 2012 R2 

- Unterstützte SharePoint-Versionen – SharePoint Server 2010 und SharePoint Server 2013

- Unterstützte SQL Server-Versionen – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition und SQL Server 2012 Enterprise Edition

- Unterstützte StorSimple-Geräte – StorSimple 8000-Serie, StorSimple 7000-Serie und StorSimple 5000-Serie

### Voraussetzungen für die Konfiguration des StorSimple-Geräts

Das StorSimple-Gerät ist ein Blockgerät und erfordert daher einen Dateiserver, auf dem die Daten gehostet werden können. Wir empfehlen Ihnen die Verwendung eines separaten Servers, anstatt eines vorhandenen Servers aus der SharePoint-Farm. Dieser Dateiserver muss sich in demselben LAN \(Local Area Network\) wie der SQL Server-Computer befinden, auf dem die Inhaltsdatenbanken gehostet werden.

>[AZURE.TIP] 
>
>- Wenn Sie Ihre SharePoint-Farm für hohe Verfügbarkeit konfigurieren, sollten Sie auch den Dateiserver für hohe Verfügbarkeit bereitstellen.
>
>- Wenn Sie die Inhaltsdatenbank nicht auf dem StorSimple-Gerät speichern, sollten Sie herkömmliche bewährte Methoden für die hohe Verfügbarkeit verwenden, bei denen RBS unterstützt wird. SQL Server-Clustering unterstützt RBS, während dies für die SQL Server-Spiegelung nicht der Fall ist.

Stellen Sie sicher, dass Ihr StorSimple-Gerät richtig konfiguriert ist und dass geeignete Volumes zur Unterstützung Ihrer SharePoint-Bereitstellung konfiguriert und für Ihren SQL Server-Computer zugänglich sind. Wechseln Sie zu [Bereitstellen lokaler StorSimple-Geräte](storsimple-deployment-walkthrough.md), falls Sie Ihr StorSimple-Gerät noch nicht bereitgestellt und konfiguriert haben. Notieren Sie sich die IP-Adresse des StorSimple-Geräts. Sie benötigen sie bei der Installation des StorSimple-Adapters für SharePoint.

Stellen Sie außerdem sicher, dass das Volume für die BLOB-Externalisierung die folgenden Anforderungen erfüllt:

- Das Volume muss mit einer Größe der Zuordnungseinheiten von 64 KB formatiert sein.

- Ihre Web-Front-End \(WFE\)- und Anwendungsserver müssen über einen Universal Naming Convention \(UNC\)-Pfad auf das Volume zugreifen können.

- Die SharePoint-Serverfarm muss für das Schreiben auf den Datenträger konfiguriert sein.

>[AZURE.NOTE] Nachdem Sie den Adapter installiert und konfiguriert haben, muss die gesamte externe BLOB-Auslagerung über das StorSimple-Gerät erfolgen \(das Gerät stellt die Volumes für SQL Server bereit und verwaltet die Speicherebenen\). Sie können keine anderen Ziele für die externe BLOB-Auslagerung verwenden.
 
Wenn Sie StorSimple Snapshot Manager zum Erstellen von Momentaufnahmen der BLOB- und Datenbankdaten verwenden möchten, sollten Sie StorSimple Snapshot Manager auf dem Datenbankserver installieren, damit der SQL Writer-Dienst zum Implementieren des Windows-Volumeschattenkopie-Diensts \(VSS\) verwendet werden kann.

>[AZURE.IMPORTANT] Der StorSimple Snapshot Manager unterstützt den SharePoint VSS Writer nicht und kann keine anwendungskonsistenten Momentaufnahmen von SharePoint-Daten erstellen. In einem SharePoint-Szenario werden von StorSimple Snapshot Manager nur ausfallsichere Sicherungen bereitgestellt.
 
## Voraussetzungen für die Konfiguration der SharePoint-Farm

Stellen Sie sicher, dass Ihre SharePoint-Serverfarm wie folgt richtig konfiguriert ist:

- Vergewissern Sie sich, dass sich die SharePoint-Serverfarm in einem intakten Zustand befindet, und überprüfen Sie Folgendes: 

- Alle registrierten SharePoint WFE- und -Anwendungsserver der Farm werden ausgeführt und sind per Ping von dem Server aus erreichbar, auf dem Sie den StorSimple-Adapter für SharePoint installieren.

- Der SharePoint-Zeitgeberdienst \(SPTimerV3 oder SPTimerV4\) wird auf jedem WFE-Server und Anwendungsserver ausgeführt.

- Sowohl der SharePoint-Zeitgeberdienst als auch der IIS-Anwendungspool, unter dem die Website für die SharePoint-Zentraladministration ausgeführt wird, verfügt über Administratorrechte.

- Stellen Sie sicher, dass die erweiterte Sicherheitskonfiguration von Internet Explorer \(IE ESC\) deaktiviert ist. Führen Sie diese Schritte aus, um IE ESC zu deaktivieren:

    1. Schließen Sie alle Instanzen von Internet Explorer.

    2. Starten Sie den Server-Manager.

    3. Klicken Sie im linken Bereich auf **Lokaler Server**.

    4. Klicken Sie im rechten Bereich neben **Verstärkte Sicherheitskonfiguration für IE** auf **Ein**.

    5. Klicken Sie unter **Administratoren** auf **Aus**.

    6. Klicken Sie auf **OK**.

## Voraussetzungen für Remote BLOB Storage \(RBS\)

Stellen Sie sicher, dass Sie eine unterstützte Version von SQL Server verwenden. Nur die folgenden Versionen werden unterstützt und können mit RBS verwendet werden:

- SQL Server 2008 Enterprise Edition

- SQL Server 2008 R2 Enterprise Edition

- SQL Server 2012 Enterprise Edition

BLOBs können nur auf den Volumes extern ausgelagert werden, die vom StorSimple-Gerät für SQL Server bereitgestellt werden. Es werden keine anderen Ziele für die externe BLOB-Auslagerung unterstützt.

Wenn Sie alle erforderlichen Konfigurationsschritte abgeschlossen haben, wechseln Sie zu [Installieren des StorSimple-Adapters für SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## Installieren des StorSimple-Adapters für SharePoint

Führen Sie die folgenden Schritte aus, um den StorSimple-Adapter für SharePoint zu installieren. Informationen für eine erneute Installation der Software finden Sie unter [Aktualisieren oder Neuinstallieren des StorSimple-Adapters für SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). Die für die Installation erforderliche Zeit richtet sich nach der Gesamtzahl der SharePoint-Datenbanken in Ihrer SharePoint-Serverfarm.

[AZURE.INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]


## Konfigurieren von RBS

Nach der Installation des StorSimple-Adapters für SharePoint konfigurieren Sie RBS wie im folgenden Verfahren beschrieben.

>[AZURE.TIP] Der StorSimple-Adapter für SharePoint wird in die Seite mit der SharePoint-Zentraladministration eingebettet und ermöglicht die Aktivierung oder Deaktivierung jeder Inhaltsdatenbank in der SharePoint-Farm. Das Aktivieren bzw. Deaktivieren von RBS in der Inhaltsdatenbank führt aber zu einer IIS-Zurücksetzung. Dies kann je nach Farmkonfiguration eine vorübergehende Störung der Verfügbarkeit des SharePoint-Web-Front-Ends \(WFE\) nach sich ziehen. \(Durch Faktoren wie die Verwendung eines Load Balancers für das Front-End, die aktuelle Server-Workload usw. kann diese Störung eingedämmt oder beseitigt werden.\) Zum Schützen der Benutzer vor einer Störung empfehlen wir Ihnen, RBS nur während eines geplanten Wartungsfensters zu aktivieren oder zu deaktivieren.

[AZURE.INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]
 

## Konfigurieren der automatischen Speicherbereinigung

Wenn Objekte von einer SharePoint-Website gelöscht werden, werden sie nicht automatisch aus dem RBS-Speichervolume gelöscht. Stattdessen werden verwaiste BLOBs mit einem im Hintergrund ausgeführten asynchronen Wartungsprogramm gelöscht. Systemadministratoren können diesen Prozess so planen, dass er in regelmäßigen Abständen ausgeführt wird, oder ihn jeweils nach Bedarf starten.

Dieses Wartungsprogramm \(Microsoft.Data.SqlRemoteBlobs.Maintainer.exe\) wird auf allen SharePoint WFE-Servern und -Anwendungsservern automatisch installiert, wenn Sie RBS aktivieren. Das Programm wird am folgenden Speicherort installiert: <boot drive>:\\Programme\\Microsoft SQL Remote Blob Storage 10.50\\Maintainer\\

Informationen zum Konfigurieren und Verwenden des Wartungsprogramms finden Sie unter [RBS-Verwaltung in SharePoint Server 2013][8].

>[AZURE.IMPORTANT] Der RBS-Wartungsprogramm ist ressourcenintensiv. Planen Sie seine Ausführung nur für Zeiten, in denen in der SharePoint-Farm geringe Aktivität herrscht.

### Sofortiges Löschen von verwaisten BLOBs

Wenn Sie verwaiste BLOBs sofort löschen müssen, können Sie die folgende Anleitung verwenden. Beachten Sie, dass diese Anleitung ein Beispiel dafür ist, wie dieser Schritt in einer SharePoint 2013-Umgebung mit den folgenden Komponenten ausgeführt werden kann:

- Der Name der Inhaltsdatenbank lautet „WSS\_Content“.
- Der SQL Server-Name lautet „SHRPT13-SQL12\\SHRPT13“.
- Der Name der Webanwendung lautet „SharePoint – 80“.

[AZURE.INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]


## Aktualisieren oder Neuinstallieren des StorSimple-Adapters für SharePoint

Verwenden Sie das folgende Verfahren zum Aktualisieren des SharePoint-Servers mit anschließender Neuinstallation des StorSimple-Adapters für SharePoint oder einfach zum Aktualisieren und Neuinstallieren des Adapters in einer vorhandenen SharePoint-Serverfarm.

>[AZURE.IMPORTANT] Lesen Sie sich die folgenden Informationen durch, bevor Sie versuchen, Ihre SharePoint-Software zu aktualisieren bzw. den StorSimple-Adapter für SharePoint zu aktualisieren oder neu zu installieren:
>
>- Alle Dateien, die per RBS bereits in einen externen Speicher verschoben wurden, sind erst wieder verfügbar, wenn die Neuinstallation abgeschlossen und die RBS-Funktion wieder aktiviert ist. Führen Sie Aktualisierungen oder Neuinstallationen während eines geplanten Wartungsfensters durch, um die Auswirkungen für Benutzer gering zu halten.
>
>- Die erforderliche Zeit für die Aktualisierung/Neuinstallation kann in Abhängigkeit davon variieren, wie hoch die Gesamtzahl der SharePoint-Datenbanken in der SharePoint-Serverfarm ist.
>
>- Nach Abschluss der Aktualisierung/Neuinstallation müssen Sie RBS für die Inhaltsdatenbanken aktivieren. Weitere Informationen finden Sie unter [Konfigurieren von RBS](#configure-rbs).
>
>- Wenn Sie RBS für eine SharePoint-Farm konfigurieren, die über eine sehr große Zahl von Datenbanken verfügt \(mehr als 200\), kann es für die Seite **SharePoint-Zentraladministration** zu einer Zeitüberschreitung kommen. Aktualisieren Sie die Seite, falls dies passiert. Dies wirkt sich nicht auf den Konfigurationsprozess aus.

## Nächste Schritte

[Weitere Informationen zu StorSimple](storsimple-overview.md) und [StorSimple-Komponenten](storsimple-components.md)

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/en-us/library/ff943565.aspx

<!-----HONumber=July15_HO5-->