<properties linkid="storage-introduction" urlDisplayName="Introduction to Azure Storage" pageTitle="Introduction to Storage | Microsoft Azure" metaKeywords="Get started  Azure storage introduction  Azure storage overview  Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage  Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage Azure table   Azure nosql   Azure large structured data store   Azure table   Azure table storage   Azure " description="An overview of Microsoft Azure Storage." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="Introduction to Microsoft Azure Storage" authors="tamram" />

Einführung in Microsoft Azure Storage
=====================================

Dieser Artikel enthält eine Einführung in Microsoft Azure Storage für Entwickler, IT-Fachleute und geschäftliche Entscheidungsträger. In diesem Artikel lernen Sie Folgendes:

-   Was Azure Storage ist und wie Sie in Ihren Cloud-, Mobilgerät-, Server- und Desktop-Anwendungen davon profitieren können
-   Welche Arten von Daten Sie mithilfe folgender Azure Storage-Dienste speichern können: Blob-, Tabellen- und Warteschlangenspeicher
-   Wie der Zugriff auf Ihre Daten in Azure Storage verwaltet wird
-   Wie Ihre Azure Storage-Daten durch Redundanz und Replikation gesichert werden
-   Welche Schritte erforderlich ist, um Ihre erste Azure Storage-Anwendung zu erstellen

Was ist Azure Storage?
----------------------

Cloud-Computing ermöglicht neue Szenarios für Anwendungen, die eine skalierbare, dauerhafte und hoch verfügbare Datenspeicherung erfordern. Und genau aus diesem Grund hat Microsoft Azure Storage entwickelt. Azure Storage versetzt nicht nur Entwickler in die Lage, umfangreiche Anwendungen zur Unterstützung neuer Szenarios aufzubauen, sondern stellt auch das Fundament für die Speicherung der Microsoft-Infrastruktur als Dienstfunktion darf, um diese Speicherung möglichst robust zu gestalten.

Azure Storage ist in hohem Maße skalierbar. Sie können also Hunderte Terabytes von Daten speichern und verarbeiten, um die "Big Data"-Szenarios zu verwirklichen, die für Anwendungen in der Wissenschaft, in der Finanzbranche und in der Medienwelt erforderlich sind. Oder Sie können geringe Datenmengen einer kleinen Geschäftswebsite speichern. Was auch immer Sie benötigen – Sie bezahlen nur für die Daten, die Sie speichern. Azure Storage speichert aktuell Billionen einzigartiger Kundenobjekte und bearbeitet durchschnittlich mehrere Millionen Anfragen pro Sekunde.

Azure Storage ist flexibel, sodass Sie Anwendungen für eine große globale Zielgruppe entwerfen und diese Anwendungen nach Bedarf auf die Speichermenge und die Anzahl von Transaktionen, die Sie benötigen, skalieren können. Sie zahlen nur für das, was Sie verwenden, und nur dann, wenn Sie es verwenden.

Azure Storage verwendet ein automatisches Partitionierungssystem, das einen automatischen Lastenausgleich Ihrer Daten auf Grundlage des Datenverkehrs durchführt. Dies bedeutet, dass Azure Storage immer automatisch die nötigen Ressourcen zuordnet, um die wachsenden Anforderungen Ihrer Anwendung zu erfüllen.

Auf Azure Storage kann überall auf der Welt und mit jeder Anwendung zugegriffen werden – egal, ob in der Cloud, auf dem Desktop, auf einem lokalen Server oder auf einem Mobilgerät/Tablet. Sie können Azure Storage in mobilen Szenarios verwenden, in denen eine Anwendung eine Datenteilmenge auf einem Gerät speichert und sie mit der vollständigen Datenmenge in der Cloud synchronisiert.

Azure Storage unterstützt Kunden mit verschiedenen Betriebssystemen (z. B. Windows und Linux) und einer Vielzahl von Programmiersprachen (darunter .NET, Java und C++) für eine problemlose Entwicklung. Azure Storage macht auch Datenressourcen über einfache REST-APIs verfügbar, die auf jedem Client ausgeführt werden können, der Daten über HTTP/HTTPS senden und empfangen kann.

Anwenden der Azure Storage-Dienste
----------------------------------

Die Azure Storage-Dienste sind Blob-Speicher, Tabellenspeicher und Warteschlangenspeicher:

-   Der **Blob-Speicher** speichert Dateiendaten. Ein Blob kann jede Art von Text- oder binären Daten sein, z. B. ein Dokument, eine Mediendatei oder ein Anwendungs-Installer.
-   Der **Tabellenspeicher** speichert strukturierte Datensätze. Der Tabellenspeicher ist ein NoSQL-Schlüssel-/Attribut-Datenspeicher, der eine rasche Entwicklung und schnellen Zugriff auf große Datenmengen erlaubt.
-   Der **Warteschlangenspeicher** bietet eine zuverlässige Nachrichtenfunktion zur Workflow-Verarbeitung und zur Kommunikation zwischen Komponenten von Clouddiensten.

Diese drei Dienste sind in jedem Speicherkonto enthalten. Ein Speicherkonto ist ein eindeutiger Namespace, über den Sie Zugriff auf Azure Storage erhalten. Jedes Speicherkonto kann bis zu 200 TB kombinierte Blob-, Warteschlangen- und Tabellendaten enthalten.

Um ein Speicherkonto erstellen zu können, müssen Sie über ein Azure-Abonnement verfügen. Dies ist ein Plan, der Ihnen Zugriff auf eine Vielzahl von Azure-Diensten verschafft. Sie können bis zu 20 eindeutig benannte Speicherkonten unter einem einzigen Abonnement erstellen.

Sie können die Verwendung von Azure mit einem [kostenlosen Testkonto](/en-us/pricing/free-trial/) starten. Wenn Sie sich entscheiden, einen Plan zu kaufen, können Sie aus einer Vielzahl von [Kaufoptionen](/en-us/pricing/purchase-options/) auswählen. Wenn Sie ein [MSDN-Abonnent](/en-us/pricing/member-offers/msdn-benefits-details/) sind, erhalten Sie monatlich kostenlose Kreditpunkte, die Sie für Azure-Dienste wie Azure Storage verwenden können.

Blob-Speicher
-------------

Für Benutzer mit großen Mengen unstrukturierter Daten, die in einer Cloud gespeichert werden sollen, bietet der Blob-Speicher eine kostengünstige und skalierbare Lösung. Der Blob-Speicher dient zur Speicherung der folgenden Inhalte:

-   Dokumente
-   Daten aus sozialen Netzwerken wie Fotos, Videos, Musik und Blogs
-   Sicherungskopien von Dateien, Computern, Datenbanken und Geräten
-   Bilder und Texte für Webanwendungen
-   Konfigurationsdaten für Cloudanwendungen
-   Große Datenmengen wie Protokolle und andere große Datensätze

Jeder Blob ist in einem Container organisiert. Container bieten auch eine praktische Methode, um Gruppen von Objekten Sicherheitsrichtlinien zuzuordnen. Ein Speicherkonto kann eine beliebige Anzahl von Containern enthalten, und ein Container kann eine beliebige Anzahl von Blobs enthalten. Die Speicherkapazität eines Speicherkontos beträgt 200 TB.

Der Blob-Speicher bietet zwei Arten von Blobs: Block-Blobs und Seiten-Blobs (Festplatten). Block-Blobs sind für das Streaming und die Speicherung von Cloudobjekten optimiert und stellen eine gute Wahl zur Speicherung von Dokumenten, Mediendateien, Sicherungskopien usw. dar. Ein Block-Blob kann bis zu 200 GB groß sein. Seiten-Blobs sind für die Darstellung von IaaS-Festplatten und für die Unterstützung von Schreibzugriffen optimiert und können bis zu 1 TB groß sein. Eine an ein Netzwerk angehängte IaaS-Festplatte auf einem virtuellen Azure-Computer ist eine als Seiten-Blob gespeicherte virtuelle Festplatte.

Für sehr große Datensätze, bei denen Netzwerkbeschränkungen das Hochladen oder Herunterladen von Daten über eine Kabelverbindung erschweren, können Sie eine Festplatte an Microsoft schicken, um Ihre Daten mithilfe des [Azure-Import-/Export-Diensts](http://www.windowsazure.com/documentation/articles/storage-import-export-service/) direkt aus dem oder in das Rechenzentrum importieren bzw. exportieren lassen zu können. Sie können auch Blob-Daten innerhalb Ihres Speicherkontos oder zwischen Speicherkonten kopieren.

Tabellenspeicher
----------------

Moderne Anwendungen erfordern oft Datenspeicher mit höherer Skalierbarkeit und Flexibilität als in früheren Software-Generationen. Der Tabellenspeicher bietet eine hoch verfügbare, in hohem Maße skalierbare Speicherung, so dass Ihre Anwendung automatisch an die Bedürfnisse der Benutzer angepasst werden kann. Der Tabellenspeicher ist der NoSQL-Schlüssel-/Attributspeicher von Microsoft. Sein schemaloses Design unterscheidet es von herkömmlichen relationalen Datenbanken. Mit einer schemalosen Datenspeicherung ist es einfach, Ihre Daten an die Entwicklung Ihrer Anwendungen anzupassen. Der Tabellenspeicher ist so benutzerfreundlich, dass Entwickler problemlos Anwendungen erstellen können. Der Datenzugriff ist für alle Arten von Anwendungen schnell und kostengünstig. Der Tabellenspeicher ist deutlich kostengünstiger als herkömmliches SQL für ähnliche Datenmengen.

Der Tabellenspeicher ist ein Schlüssel-/Attributspeicher. Dies bedeutet, dass jede Entität in einer Tabelle mit einem Eigenschaftsnamen gespeichert wird. Dieser Eigenschaftsname - der Schlüssel - kann zur Filterung und Festlegung von Auswahlkriterien verwendet werden. Der Schlüssel und eine Sammlung von Eigenschaften und ihrer Werte bilden eine Entität. Da die Tabellenspeicherung schemalos ist, können Entitäten in derselben Tabelle verschiedene Sammlungen von Eigenschaften enthalten, und diese Eigenschaften können von verschiedener Art sein.

Sie können den Tabellenspeicher zur Speicherung von flexiblen Datensätzen wie Benutzerdaten für Webanwendungen, Adressbüchern, Geräteinformationen und jeder Art von Metadaten verwenden, die Ihr Dienst erfordert. Sie können eine beliebige Anzahl von Entitäten in einer Tabelle speichern, und ein Speicherkonto kann eine beliebige Anzahl von Tabellen enthalten. Die Speicherkapazität eines Speicherkontos beträgt 200 TB.

Wie bei Blobs und Warteschlangen können Entwickler den Tabellenspeicher mit Standard-REST-Protokollen aufrufen und verwalten. Der Tabellenspeicher unterstützt allerdings auch eine Untergruppe des OData-Protokolls. Dies vereinfacht erweiterte Abfragefunktionen und ermöglicht die Arbeit mit den Formaten JSON und AtomPub (XML-basiert).

NoSQL-Datenbanken wie der Tabellenspeicher bieten für heutige internetbasierte Anwendungen eine beliebte Alternative zu herkömmlichen relationalen Datenbanken.

Warteschlangenspeicher
----------------------

Bei der Entwicklung von Anwendungen für Skalierungszwecke werden einzelne Anwendungskomponenten oft entkoppelt, damit sie unabhängig skaliert werden können. Der Warteschlangenspeicher bietet eine zuverlässige Nachrichtenlösung für asynchrone Kommunikation zwischen Anwendungskomponenten in der Cloud, auf dem Desktop, auf einem lokalen Server oder an einem Mobilgerät. Der Warteschlangenspeicher unterstützt auch die Verwaltung asynchroner Aufgaben und den Aufbau von Prozessworkflows.

Ein Speicherkonto kann eine beliebige Anzahl von Warteschlangen enthalten. Eine Warteschlange kann eine beliebige Anzahl von Nachrichten enthalten, und das entsprechende Speicherkonto hat eine Kapazität von 200 TB. Einzelne Nachrichten können bis zu 64 KB groß sein.

Zugriff auf Blob-, Tabellen- und Warteschlangenressourcen
---------------------------------------------------------

Standardmäßig kann nur der Besitzer eines Speicherkontos auf Ressourcen in diesem Konto zugreifen. Für die Sicherheit Ihrer Daten muss jede Abfrage über Ressourcen in Ihrem Konto authentifiziert werden. Die Authentifizierung beruht auf einem Modell mit gemeinsam verwendeten Schlüsseln. Blobs können auch für die Unterstützung anonymer Authentifizierungen konfiguriert werden.

Ihr Speicherkonto wird bei der Erstellung zwei privaten Zugangsschlüsseln zugeordnet, die zur Authentifizierung verwendet werden. Die Verwendung von zwei Schlüsseln stellt sicher, dass Ihre Anwendung auch dann verfügbar bleibt, wenn Sie die Schlüssel im Rahmen der regelmäßigen Sicherheitsverwaltung regelmäßig erneuern.

Wenn Sie anderen Benutzern kontrollierten Zugriff zu Ihren Speicherressourcen gewähren müssen, können Sie eine [Shared Access Signature](../storage-dotnet-shared-access-signature-part-1/) erstellen. Eine Shared Access Signature ist ein Token, das an eine URL angehängt werden kann und delegierten Zugriff auf einen Container, einen Blob, eine Tabelle oder eine Warteschlange erlaubt. Jeder Benutzer, der dieses Token besitzt, kann mit festgelegten Berechtigungen und während der Gültigkeitsdauer auf die Ressource zugreifen, auf die das Token verweist.

Abschließend können Sie noch einen Container und seine Blobs – oder einen speziellem Blob – öffentlich verfügbar machen. Wenn Sie einen Container oder einen Blob zu einer öffentlichen Ressource erklären, kann jeder Benutzer anonym drauf zugreifen, ohne eine Authentifizierung zu benötigen. Öffentliche Container und Blobs sind hilfreich zur Verfügbarmachung von Ressourcen wie Medien und Dokumenten, die auf Websites gehostet werden. Um die Netzwerklatenz für ein globales Publikum zu reduzieren, können Sie Blob-Daten, die von Websites verwendet werden, in einem Cache zwischenspeichern.

Replikation für Dauerhaftigkeit und hohe Verfügbarkeit
------------------------------------------------------

Die Daten in Ihrem Speicherkonto werden repliziert, um Dauerhaftigkeit und hohe Verfügbarkeit zu garantieren, sodass die [Azure Storage-SLA](/en-us/support/legal/sla/) auch bei vorübergehenden Hardware-Ausfällen erfüllt werden. Sie haben drei Optionen zum Replizieren der Daten in Ihrem Speicherkonto:

-   *Lokal redundanter Speicher (LRS)* – wird in einem einzelnen Rechenzentrum dreimal repliziert. Wenn Sie Daten in einen Blob, eine Warteschlange oder eine Tabelle schreiben, wird der Schreibvorgang synchron für alle drei Replikate durchgeführt. LRS schützt Ihre Daten vor normalen Hardware-Ausfällen.
-   *Georedundanter Speicher (GRS)* – wird dreimal in einer einzelnen Region und außerdem asynchron in einer zweiten Region repliziert, die mehrere 100 Kilometer entfernt ist. GRS hält 6 Kopien (Replikate) Ihrer Daten (3 in jeder Region) bereit. GRS ermöglicht Microsoft ein Failover in eine zweite Region, wenn wir die Daten in der ersten Region aufgrund eines größeres Ausfalls oder eines sonstigen Notfalls nicht wiederherstellen können. GRS wird gegenüber dem lokal redundanten Speicher empfohlen.
-   *Schreibgeschützter georedundanter Speicher (RA-GRS)* – bietet alle Vorteile der oben beschriebenen georedundanten Speicherung, aber darüber hinaus auch Schreibzugriff auf Daten in der sekundären Region, falls die primäre Region nicht verfügbar ist. Der schreibgeschützte georedundante Speicher wird für maximale Verfügbarkeit und Dauerhaftigkeit empfohlen.

Informationen zu den Preisunterschieden zwischen LRS, GRS und RA-GRS finden Sie auf der Seite [Speicherpreisdetails](/en-us/pricing/details/storage/).

Preise
------

Die Verwendung von Azure Storage wird dem Kunden auf Grundlage von vier Faktoren berechnet: der verwendeten Speicherkapazität, der ausgewählten Replikationsoption, der Anzahl der Abfragen an den Dienst und den Datenausgang.

Die Speicherkapazität bezeichnet den Anteil Ihrer Speicherkontozuweisung, der zum Speichern von Daten verwendet wird. Die Kosten des einfachen Speicherns von Daten werden dadurch bestimmt, wie viele Daten Sie speichern und wie diese Daten repliziert werden. Mit jedem Lese- und Schreibvorgang in Azure Storage erfolgt eine Abfrage an den Dienst. Der Begriff "Datenausgang" bezieht sich auf die Daten, die aus einer Windows Azure-Region heraus übertragen werden. Wenn eine Anwendung, die nicht in der gleichen Region ausgeführt wird und entweder ein Clouddienst oder ein anderer Anwendungstyp ist, auf die Daten in Ihrem Speicherkonto zugreift, fallen Gebühren für den Datenausgang an. (Für Azure-Dienste können Sie Maßnahmen ergreifen, um Ihre Daten und Dienste in den gleichen Rechenzentren zu gruppieren und auf diese Weise Prozess- und Datenausgangsgebühren zu senken oder komplett einzusparen.)

Die Seite mit den [Speicherpreisdetails](/en-us/pricing/details/storage/) bietet detaillierte Preisinformationen für Speicherkapazität, Replikation und Transaktionen. In den [Datenübertragungs-Preisdetails](/en-us/pricing/details/data-transfers/) finden Sie detaillierte Preisinformationen für den Datenausgang. Sie können den [Azure Storage-Preisrechner](/en-us/pricing/calculator/?scenario=data-management) verwenden, um Ihre Kosten zu bestimmen.

Entwicklung bei der Speicherung
-------------------------------

Azure Storage stellt Speicherressourcen über eine [REST-API](http://msdn.microsoft.com/library/windowsazure/dd179355.aspx) zur Verfügung, die in jeder Sprache aufgerufen werden kann, mit der sich HTTP/HTTPS-Anfragen durchführen lassen. Zusätzlich bietet Azure Storage Programmierbibliotheken für einige beliebte Sprachen. Diese Bibliotheken vereinfachen viele Aspekte der Arbeit mit Azure Storage durch Verarbeitung von Details wie synchronen und asynchronen Aufrufen, der Zusammenfassung von Vorgängen, Ausnahmeverwaltung, automatischen Neuversuchen, Betriebsverhalten usw. Bibliotheken sind aktuell für die folgenden Sprachen und Plattformen erhältlich. Weitere sind in Planung.

-   [.NET](http://msdn.microsoft.com/library/dn495001(v=azure.10).aspx)
-   [Nativer Code](http://msdn.microsoft.com/library/dn495438.aspx)
-   [Java](/de-de/develop/java/)
-   [Node.js](../storage/#node)
-   [PHP](../storage/#php)
-   [Ruby](../storage/#ruby)
-   [Python](../storage/#python)
-   [PowerShell](http://msdn.microsoft.com/library/dn495240.aspx)

Nächste Schritte
----------------

Informationen zu den ersten Schritten mit Azure Storage finden Sie in folgenden Ressourcen:

-   [Azure Storage-Dokumentation](/de-de/documentation/services/storage/)
-   [Skalierbarkeits- und Leistungsziele für Azure Storage](http://msdn.microsoft.com/library/windowsazure/dn249410.aspx)

### Für .NET-Entwickler

-   [How to use Blob Storage from .NET (Verwenden des Blob-Speichers mit .NET, in englischer Sprache)](../storage-dotnet-how-to-use-blobs-20/)
-   [How to use Table Storage from .NET (Verwenden des Tabellenspeichers mit .NET, in englischer Sprache)](../storage-dotnet-how-to-use-tables-20/)
-   [How to use Queue Storage from .NET (Verwenden des Warteschlangenspeichers mit .NET, in englischer Sprache)](../storage-dotnet-how-to-use-queues-20/)

### Für Java-Entwickler

-   [How to use Blob Storage from Java (Verwenden des Blob-Speichers mit Java, in englischer Sprache)](../storage-java-how-to-use-blob-storage/)
-   [How to use Table Storage from Java (Verwenden des Tabellenspeichers mit Java, in englischer Sprache)](..storage-java-how-to-use-table-storage/)
-   [How to use Queue Storage from Java (Verwenden des Warteschlangenspeichers mit Java, in englischer Sprache)](..storage-java-how-to-use-queue-storage/)

### Für Node.js-Entwickler

-   [How to use Blob Storage from Node.js (Verwenden des Blob-Speichers mit Node.js, in englischer Sprache)](../storage-nodejs-how-to-use-blob-storage/)
-   [How to use Table Storage from Node.js (Verwenden des Tabellenspeichers mit Node.js, in englischer Sprache)](../storage-nodejs-how-to-use-table-storage/)
-   [How to use Queue Storage from Node.js (Verwenden des Warteschlangenspeichers mit Node.js, in englischer Sprache)](../storage-nodejs-how-to-use-queue-storage/)

### Für PHP-Entwickler

-   [How to use Blob Storage from PHP (Verwenden des Blob-Speichers mit PHP, in englischer Sprache)](../storage-php-how-to-use-blob-storage/)
-   [How to use Table Storage from PHP (Verwenden des Tabellenspeichers mit PHP, in englischer Sprache)](..storage-php-how-to-use-table-storage/)
-   [How to use Queue Storage from PHP (Verwenden des Warteschlangenspeichers mit PHP, in englischer Sprache)](..storage-php-how-to-use-queue-storage/)

### Für Ruby-Entwickler

-   [How to use Blob Storage from Ruby (Verwenden des Blob-Speichers mit Ruby, in englischer Sprache)](../storage-ruby-how-to-use-blob-storage/)
-   [How to use Table Storage from Ruby (Verwenden des Tabellenspeichers mit Ruby, in englischer Sprache)](..storage-ruby-how-to-use-table-storage/)
-   [How to use Queue Storage from Ruby (Verwenden des Warteschlangenspeichers mit Ruby, in englischer Sprache)](..storage-ruby-how-to-use-queue-storage/)

### Für Python-Entwickler

-   [How to use Blob Storage from Python (Verwenden des Blob-Speichers mit Python, in englischer Sprache)](../storage-python-how-to-use-blob-storage/)
-   [How to use Table Storage from Python (Verwenden des Tabellenspeichers mit Python, in englischer Sprache)](..storage-python-how-to-use-table-storage/)
-   [How to use Queue Storage from Python (Verwenden des Warteschlangenspeichers mit Python, in englischer Sprache)](..storage-python-how-to-use-queue-storage/)
