<properties 
	pageTitle="Herstellen einer Verbindung mit SQL-Datenbank: Bewährte Methoden | Microsoft Azure" 
	description="Ein Themeneinstieg, der Links und bewährte Empfehlungen für Client-Programme für das Aufbauen von Verbindungen mit Azure SQL-Datenbank mit Technologien wie ADO.NET und PHP zusammenfasst." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/07/2016" 
	ms.author="genemi"/>


# Verbindungen mit SQL-Datenbanken: Wichtige Empfehlungen


Dieses Thema ist ein guter Einstieg in die Client-Konnektivität für Azure SQL-Datenbank. Es enthält Links zu Codebeispielen für verschiedene Technologien, die Sie zum Herstellen einer Verbindung und zum Interagieren mit SQL-Datenbank verwenden können. Zu diesen Technologien gehören Enterprise Library, JDBC, PHP und weitere. Die bereitgestellten Informationen gelten unabhängig von der spezifischen Technologie, die Sie zum Herstellen der Verbindung mit der SQL-Datenbank verwenden.


<a id="a-tech-independent-recommend" name="a-tech-independent-recommend"></a>

## Technologieunabhängige Empfehlungen


- [Richtlinien zum programmgesteuerten Herstellen einer Verbindung mit einer Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/ee336282.aspx) – folgende Aspekte werden erörtert:
 - [Ports und Firewalls](sql-database-configure-firewall-settings.md)
 - Verbindungszeichenfolgen
- [Ressourcenverwaltung für die Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/dn338083.aspx) – folgende Aspekte werden erörtert:
 - Ressourcenkontrolle
 - Durchsetzung von Grenzwerten
 - Drosselung


<a id="b-authentication-recommend" name="b-authentication-recommend"></a>

## Empfehlungen für die Authentifizierung


- Verwenden Sie die Azure SQL-Datenbankauthentifizierung und nicht die Windows-Authentifizierung. Letztere ist in der Azure SQL-Datenbank nicht verfügbar.
- Geben Sie eine bestimmte Datenbank an, anstatt automatisch die *master*-Datenbank zu verwenden.
 - Sie können die Transact-SQL-Anweisung **USE myDatabaseName;** in SQL-Datenbanken nicht verwenden, um zu einer anderen Datenbank zu wechseln.


### Enthaltene Benutzer


Beim Hinzufügen einer Person als Benutzer Ihrer SQL-Datenbank stehen Ihnen Auswahlmöglichkeiten zur Verfügung:

- Hinzufügen einer *Anmeldung* mit einem Kennwort zur **Masterdatenbank** und anschließendes Hinzufügen eines entsprechenden *Benutzers* zu einer oder mehreren anderen Datenbanken auf demselben Server.

- Hinzufügen eines *enthaltenen Benutzers* mit einem Kennwort zu einer oder mehreren Datenbanken, und keine Verbindung mit einer *Anmeldung* in der **Masterdatenbank**.


Der Ansatz mit den enthaltenen Benutzer hat Vor- und Nachteile:

- Ein Vorteil ist, dass eine Datenbank problemlos von einem Azure SQL-Datenbankserver auf einen anderen verschoben werden kann, wenn alle Benutzer in der Datenbank enthaltene Benutzer sind.

- Ein Nachteil ist die schwierigere routinemäßige Verwaltung. Beispiel:
 - Es ist schwieriger, mehrere enthaltene Benutzer anstatt einer Anmeldung zu löschen.
 - Eine Person, die ein enthaltener Benutzer in mehreren Datenbanken ist, muss möglicherweise mehr Kennwörter behalten oder aktualisieren.


Weitere Informationen finden Sie unter [Eigenständige Datenbankbenutzer – machen Sie Ihre Datenbank portabel](http://msdn.microsoft.com/library/ff929188.aspx).


<a id="c-connection-recommend" name="c-connection-recommend"></a>

## Empfehlungen für die Verbindung


- Ändern Sie in der Clientverbindungslogik das Standardtimeout in 30 Sekunden.
 - Der Standardwert von 15 Sekunden ist zu kurz für Verbindungen, die über das Internet hergestellt werden.


- Vergewissern Sie sich, dass auf dem Computer, der das Clientprogramm hostet, die Firewall ausgehende TCP-Kommunikation über Port 1433 zulässt.


- Wenn Ihr Clientprogramm eine Verbindung mit SQL-Datenbank V12 herstellt, wobei der Client auf einem virtuellen Azure-Computer ausgeführt wird, müssen Sie die Portbereiche 11000 11999 und 14000-14999 auf dem virtuellen Computer öffnen. Klicken Sie [hier](sql-database-develop-direct-route-ports-adonet-v12.md), um ausführliche Informationen zu erhalten.


- Um *vorübergehende Fehler* zu behandeln, fügen Sie Ihren Clientprogrammen, die mit der Azure SQL-Datenbank interagieren, [Logik für *Wiederholungsversuche*](#TransientFaultsAndRetryLogicGm) hinzu.


### Verbindungspool


Wenn Sie einen [Verbindungspool](http://msdn.microsoft.com/library/8xx3tyca.aspx) verwenden, trennen Sie die Verbindung, sobald Ihre Anwendung sie nicht aktiv verwendet und nicht gerade auf die erneute Verwendung der Verbindung vorbereitet wird.

Sofern Ihre Anwendung die Verbindung nicht unmittelbar und ohne Pause für einen anderen Vorgang wiederverwendet,wird die folgende Vorgehensweise empfohlen:

- Stellen Sie eine Verbindung her.
- Führen Sie über die Verbindung einen Vorgang aus.
- Trennen Sie die Verbindung.


### Andere Ports als 1433 in V12.


Bei Clientverbindungen mit Azure SQL-Datenbank V12 wird der Proxy manchmal umgangen und direkt mit der Datenbank interagiert. Andere Ports als 1433 werden wichtig. Ausführliche Informationen finden Sie unter <br/> [Andere Ports als 1433 für ADO.NET 4.5 und SQL-Datenbank V12](sql-database-develop-direct-route-ports-adonet-v12.md).


Im nächsten Abschnitt erfahren Sie mehr über die Wiederholungslogik und die Behandlung vorübergehender Fehler.



<a name="TransientFaultsAndRetryLogicGm" id="TransientFaultsAndRetryLogicGm"></a>

&nbsp;

## Vorübergehende Fehler und Wiederholungslogik


Das Azure-System verfügt über die Möglichkeit, Server dynamisch neu zu konfigurieren, wenn hohe Arbeitslasten im der SQL-Datenbankdienst auftreten.

Eine Neukonfiguration führt jedoch u. U. dazu, dass die Verbindung zwischen Ihrem Clientprogramm und der SQL-Datenbank getrennt wird. Dieser Fehler wird als *Übergangsfehler* bezeichnet.

Wenn das Clientprogramm über Wiederholungslogik verfügt, kann es versuchen, erneut eine Verbindung herzustellen, nachdem der vorübergehende Fehler Zeit hatte, sich selbst zu korrigieren.

Es wird empfohlen, dass vor dem ersten Wiederholungsversuch eine Verzögerungszeit von fünf Sekunden verwendet wird. Wiederholungsversuche nach weniger als fünf Sekunden können den Clouddienst überfordern. Für jeden nachfolgenden Wiederholungsversuch sollte die Verzögerung exponentiell steigen, bis zu einem Maximum von 60 Sekunden.

Eine Erörterung der *Sperrfrist* für Clients, die ADO.NET verwenden, finden Sie unter [SQL Server-Verbindungspooling (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).


Codebeispiele, die die Wiederholungslogik veranschaulichen, finden Sie in folgenden Themen:

- [Clientcodebeispiele für die ersten Schritte mit SQL-Datenbank](sql-database-develop-quick-start-client-code-samples.md)


### Fehlernummern für vorübergehende Fehler


Wenn in der SQL-Datenbank ein Fehler auftritt, wird eine [SqlException](http://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx)-Ausnahme ausgelöst. Die **SqlException** enthält einen numerischen Fehlercode in der **Number**-Eigenschaft. Wenn der Fehlercode einen vorübergehenden Fehler bezeichnet, sollte die Anwendung den Aufruf wiederholen.


- [Fehlermeldungen für Clientprogramme der SQL-Datenbank](sql-database-develop-error-messages.md#bkmk_connection_errors)
 - Der Abschnitt **Vorübergehende Fehler, Fehler bei Verbindungsabbruch** enthält eine Liste der vorübergehenden Fehler, bei denen sich automatische Wiederholungsversuche empfehlen.
 - Wiederholen Sie den Vorgang beispielsweise, wenn Fehler Nummer 40613 auftritt, der in etwa Folgendes besagt: <br/>*Datenbank „mydatabase“ auf Server „Server“ ist derzeit nicht verfügbar.*


Weitere Informationen finden Sie unter:

- [Azure SQL-Datenbankentwicklung: Themen zur Vorgehensweise](http://msdn.microsoft.com/library/azure/ee621787.aspx)

<!--  (per Penny Lee, 2016/01/07.  MightyPen==GeneMi)
- [Troubleshoot connection problems to Azure SQL Database](http://support.microsoft.com/kb/2980233/)
-->


<a id="e-technologies" name="e-technologies"></a>

## Technologien


Die folgenden Themen enthalten Links zu Codebeispielen für mehrere Sprachen und Treiber-Technologien, die Sie zum Herstellen einer Verbindung mit Azure SQL-Datenbank aus dem Clientprogramm verwenden können.


Für Clients, die unter Windows, Linux und Mac OS X ausgeführt werden, sind unterschiedliche Codebeispiele angegeben.


**Allgemeine Beispiele:** Es gibt [Codebeispiele](sql-database-develop-quick-start-client-code-samples.md) für eine Vielzahl von Programmiersprachen, einschließlich PHP, Python, Node.js und .NET CSharp. Darüber hinaus gibt es Beispiele für Clients, die unter Windows, Linux und Mac OS X ausgeführt werden.


**Elastische Skalierung:** Informationen zur Konnektivität für Datenbanken mit elastischer Skalierung finden Sie unter:

- [Erste Schritte mit der Vorschauversion von Elastic Scale für Azure SQL-Datenbank](sql-database-elastic-scale-get-started.md)
- [Datenabhängiges Routing](sql-database-elastic-scale-data-dependent-routing.md)


**Treiberbibliotheken:** Informationen zu Verbindungstreiberbibliotheken, einschließlich empfohlener Versionen, finden Sie unter:

- [Connection Libraries for SQL Database and SQL Server (Verbindungsbibliotheken für SQL-Datenbanken und SQL Server, in englischer Sprache)](sql-database-libraries.md)

<!---HONumber=AcomDC_0114_2016-->