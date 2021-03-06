<properties
	pageTitle="SQL-Datenbank-Leistung und -optionen: Tarife | Microsoft Azure"
	description="Vergleich Sie die verschiedenen Leistungs- und Geschäftskontinuitätsoptionen der SQL-Datenbanktarife, um beim Skalieren Kosten und Funktionalität im Blick zu behalten."
	keywords="Datenbankoptionen, Datenbankleistung"
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor="jeffreyg"/>

<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="02/03/2016"
	ms.author="jeffreyg"/>

# SQL-Datenbankoptionen und -leistung: Grundlegendes zum Angebot in den einzelnen Tarifen

[Azure SQL-Datenbank](sql-database-technical-overview.md) umfasst mehrere Dienstebenen für unterschiedliche Workloads. Sie können Dienstebenen jederzeit und ohne Ausfallzeiten der Anwendung ändern. Sie haben auch die Möglichkeit zum [Erstellen einer Einzeldatenbank](sql-database-get-started.md) mit definierten Merkmalen und Preisen. Oder Sie können mehrere Datenbanken verwalten, indem Sie einen [Pool für elastische Datenbanken erstellen](sql-database-elastic-pool-portal.md). In beiden Fällen sind die Dienstebenen **Basic**, **Standard** und **Premium** verfügbar. Die Datenbankoptionen dieser Tarife unterscheiden sich jedoch abhängig davon, ob Sie eine Einzeldatenbank oder eine Datenbank in einem Pool für elastische Datenbanken erstellen. Dieser Artikel enthält ausführliche Informationen zu den Dienstebenen in beiden Kontexten.

## Tarife und Datenbankoptionen
Die Dienstebenen "Basic", "Standard" und "Premium" haben alle eine Betriebszeit-SLA von 99,99 % und bieten vorhersagbare Leistung, flexible Optionen für Geschäftskontinuität, Sicherheitsfeatures und stündliche Abrechnung. In der folgenden Tabelle sind Beispiele für Dienstebenen aufgeführt, die sich für unterschiedliche Anwendungsworkloads am besten eignen.

| Dienstebene | Zielworkloads |
|---|---|
| **Basic** | Eignet sich am besten für eine kleine Datenbank, in der normalerweise ein einzelner aktiver Vorgang zu einem bestimmten Zeitpunkt unterstützt wird. Beispiele hierfür sind Datenbanken, die für die Entwicklung, für Tests oder für kleine, selten verwendete Anwendungen verwendet werden. |
| **Standard** | Die richtige Wahl für die meisten Cloudanwendungen mit Unterstützung mehrerer gleichzeitiger Abfragen. Beispiele hierfür sind Arbeitsgruppen-oder Webanwendungen. |
| **Premium** | Konzipiert für hohe Transaktionsvolumen, unterstützt eine große Anzahl gleichzeitiger Benutzer und erfordert die höchste Stufe der Funktionen für Geschäftskontinuität. Beispiele hierfür sind Datenbanken für unternehmenskritische Anwendungen. |

>[AZURE.NOTE] Web Edition und Business Edition sind nicht mehr verfügbar. Erfahren Sie, wie Sie ein [Upgrade von Web- und Business-Editionen](sql-database-upgrade-new-service-tiers.md) durchführen. Bitte informieren Sie sich unter [Häufig gestellte Fragen zur Einstellung von Web und Business Edition](https://azure.microsoft.com/pricing/details/sql-database/web-business/), wenn Sie Web- und Business-Editionen weiterhin verwenden möchten.

### Tarife und Leistungsstufen für Einzeldatenbanken
Für Einzeldatenbanken sind mehrere Leistungsstufen innerhalb jeder Dienstebene verfügbar, sodass Sie flexibel die Ebene auswählen können, die Ihren Workloadanforderungen am besten entspricht. Wenn Sie zentral hoch- oder herunterskalieren müssen, können Sie die Ebenen Ihrer Datenbank problemlos und **ohne Ausfallzeiten der Anwendung** ändern. Ausführlichere Informationen finden Sie unter [Ändern der Dienstebenen und -Leistungsstufen von Datenbanken](sql-database-scale-up.md).

Die hier aufgeführten Leistungsmerkmale gelten für Datenbanken, die mit [SQL-Datenbank V12](sql-database-v12-whats-new.md) erstellt wurden. In Situationen, in denen die zugrunde liegende Hardware in Azure mehrere SQL-Datenbanken hostet, erhält Ihre Datenbank immer noch einen garantierten Satz von Ressourcen, und die erwarteten Leistungsmerkmale der individuellen Datenbank sind nicht betroffen.

[AZURE.INCLUDE [Tarife für SQL-Datenbank](../../includes/sql-database-service-tiers-table.md)]


Weitere Informationen zu DTUs finden Sie in diesem Thema im Abschnitt [DTU](#understanding-dtus).

>[AZURE.NOTE] Eine ausführliche Beschreibung aller anderen Zeilen in dieser Dienstebenentabelle finden Sie unter [Service tier capabilities and limits](sql-database-performance-guidance.md#service-tier-capabilities-and-limits) (Funktionen und Limits von Dienstebenen; in englischer Sprache).

### Tarife und Leistung für einen Pool für elastische Datenbanken in eDTUs
Neben dem Erstellen und Skalieren einer Einzeldatenbank haben Sie die Möglichkeit, mehrere Datenbanken in einem [Pool für elastische Datenbanken](sql-database-elastic-pool.md) zu verwalten. Alle Datenbanken in einem Pool für elastische Datenbanken nutzen einen gemeinsamen Satz von Ressourcen. Die Leistungsmerkmale werden in *Transaktionseinheiten für elastische Datenbanken* (eDTUs) gemessen. Wie für Einzeldatenbanken gibt es Pools für elastische Datenbanken drei Tarife: **Basic**, **Standard** und **Premium**. Auch für elastische Datenbanken werden mit diesen drei Dienstebenen die Leistungsgrenzen und verschiedene Funktionen definiert.

Durch Pools für elastische Datenbanken können diese Datenbanken DTU-Ressourcen teilen und nutzen, ohne dass den Datenbanken im Pool eine bestimmte Leistungsstufe zugewiesen werden muss. Beispielsweise kann eine Einzeldatenbank in einem Pool der Dienstebene "Standard" 0 eDTU bis zum maximalen eDTU-Wert für Datenbanken (entweder die durch die Dienstebene definierten 100 eDTUs oder ein benutzerdefinierter Wert, den Sie festlegen) nutzen. Dadurch können mehrere Datenbanken mit unterschiedlichen Workloads die für den gesamten Pool verfügbaren eDTU-Ressourcen effizient nutzen.

In der folgenden Tabelle sind die Merkmale der Dienstebenen eines Pools für elastische Datenbanken beschrieben. Definitionen und ausführlichere Informationen finden Sie unter [SQL-Datenbank – Referenz für Pools für elastische Azure SQL-Datenbanken](sql-database-elastic-pool-reference.md).

[AZURE.INCLUDE [Tabelle der SQL-Datenbank-Dienstebenen für elastische Datenbanken](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Für jede Datenbank in einem Pool gelten auch die Merkmale für Einzeldatenbanken für die entsprechende Dienstebene. Beispielsweise liegt für einen Pool mit dem Tarif „Basic“ die maximale Grenze bei 2.400 - 28.800 Sitzungen, für eine Einzeldatenbank in diesem Pool ist jedoch ein Limit von 300 Sitzungen definiert (entsprechend der Angabe für eine Einzeldatenbank mit dem Tarif „Basic“ im vorhergehenden Abschnitt).

### Grundlegendes zu DTUs

[AZURE.INCLUDE [Beschreibung von SQL-Datenbank-DTUs](../../includes/sql-database-understanding-dtus.md)]

## Überwachen der Datenbankleistung
Die Überwachung der Leistung einer SQL-Datenbank beginnt mit der Überwachung der Ressourcennutzung relativ zur gewählten Datenbankleistung. Diese relevanten Daten werden auf folgende Weise zur Verfügung gestellt:

1.	Im Azure-Portal

2.	In dynamischen Verwaltungssichten in der Benutzerdatenbank und in der Masterdatenbank des Servers, die die Benutzerdatenbank enthält.

Im [Azure-Portal](https://portal.azure.com/) können Sie die Nutzung einer Einzeldatenbank überwachen, indem Sie die Datenbank auswählen und auf das Diagramm **Überwachung** klicken. Dadurch wird das Fenster **Metrik** geöffnet, in dem Sie durch Klicken auf die Schaltfläche **Diagramm bearbeiten** Änderungen vornehmen können. Fügen Sie die folgenden Metriken hinzu:

- CPU-Prozentsatz
- DTU Percentage
- Data IO Percentage
- Storage Percentage

Nachdem Sie diese Metriken hinzugefügt haben, können Sie sie im Diagramm **Überwachung** mit weiteren Details im Fenster **Metrik** anzeigen. Alle vier Metriken geben die durchschnittliche prozentuale Nutzung relativ zur **DTU** der Datenbank an.

![Tarifbezogenes Überwachen der Datenbankleistung.](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

Sie können zudem Benachrichtigungen für die Leistungsmetriken konfigurieren. Klicken Sie im Fenster **Metrik** auf die Schaltfläche **Warnung hinzufügen**. Befolgen Sie die Anweisungen des Assistenten, um die Benachrichtigung zu konfigurieren. Sie haben die Möglichkeit, Benachrichtigungen für den Fall zu konfigurieren, dass Metriken einen Schwellenwert überschreiten oder unterschreiten.

Wenn Sie beispielsweise einen Anstieg der Workload Ihrer Datenbank erwarten, können Sie eine E-Mail-Benachrichtigung konfigurieren, die immer dann gesendet wird, wenn eine der Leistungsmetriken der Datenbank 80 % erreicht. Sie können dies als Frühwarnung verwenden, um zu ermitteln, wann Sie eventuell zur nächsthöheren Leistungsstufe wechseln müssen.

Anhand der Leistungsmetriken können Sie auch ermitteln, ob Sie möglicherweise eine Herabstufung auf eine niedrigere Leistungsstufe vornehmen können. Angenommen, Sie verwenden eine Datenbank der Dienstebene "Standard S2", und alle Leistungsmetriken zeigen, dass die Datenbank zu jedem Zeitpunkt durchschnittlich nicht mehr als 10 % nutzt. Hier ist es wahrscheinlich, dass sich die Datenbank auch mit der Dienstebene "Standard S1" verwenden lässt. Bevor Sie sich für einen Wechsel zu einer niedrigeren Leistungsstufe entscheiden, müssen Sie aber auch Workloads berücksichtigen, die Spitzen oder Schwankungen aufweisen können.

Dieselben Metriken, die im Portal bereitgestellt werden, sind auch über Systemsichten verfügbar: [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) in der logischen **„master“-Datenbank** Ihres Servers und [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) in der Benutzerdatenbank (**sys.dm_db_resource_stats** werden in jeder Basic-, Standard- und Premium-Benutzerdatenbank erstellt. Web- und Business Edition-Datenbanken geben ein leeres Resultset zurück.) Verwenden Sie **sys.resource_stats**, wenn Sie weniger differenzierte Daten über einen längeren Zeitraum überwachen möchten. Verwenden Sie **sys.dm_db_resource_stats**, wenn Sie differenzierte Daten in einem kürzeren Zeitraum überwachen möchten. Weitere Informationen finden Sie unter [Leitfaden zur Leistung für Azure SQL-Datenbanken](sql-database-performance-guidance.md#monitoring-resource-use-with-sysresourcestats).

In Pools für elastische Datenbanken können Sie Einzeldatenbanken im Pool mit den in diesem Abschnitt beschriebenen Methoden überwachen. Sie können den Pool jedoch auch als Ganzes überwachen. Informationen dazu finden Sie unter [Erstellen und Verwalten eines elastischen Azure SQL-Datenbankpools](sql-database-elastic-pool-portal.md#monitor-and-manage-an-elastic-database-pool).

## Nächste Schritte
Erfahren Sie mehr über die Preise für diese Ebenen unter [SQL-Datenbank Preise](https://azure.microsoft.com/pricing/details/sql-database/).

Wenn Sie mehrere Datenbanken als Gruppe verwalten möchten, finden Sie entsprechende Informationen unter [Elastische Datenbankpools](sql-database-elastic-pool-guidance.md) sowie unter [Überlegungen zum Preis und zur Leistung eines elastischen Datenbankpools](sql-database-elastic-pool-guidance.md).

Nachdem Sie jetzt die Ebenen der SQL-Datenbank kennen, können Sie sie mit einer [kostenlosen Testversion](https://azure.microsoft.com/pricing/free-trial/) ausprobieren und sich mit der [Erstellung Ihrer ersten SQL-Datenbank](sql-database-get-started.md) befassen!

<!---HONumber=AcomDC_0204_2016-->