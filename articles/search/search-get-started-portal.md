<properties 
	pageTitle="Erste Schritte mit Azure Search | Microsoft Azure | Cloudsuchdienst | DocumentDB" 
	description="Erstellen Sie anhand dieser exemplarischen Vorgehensweise in Form eines Tutorials Ihre erste Azure Search-Lösung. Erfahren Sie, wie Sie mit DocumentDB-Daten einen Azure Search-Index erstellen. Dies ist eine auf dem Portal basierende Übung ohne Code, bei der der Assistent „Daten importieren“ genutzt wird." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""
    tags="azure-portal"/>

<tags 
	ms.service="search" 
	ms.devlang="na" 
	ms.workload="search" 
	ms.topic="hero-article" 
	ms.tgt_pltfrm="na" 
	ms.date="02/10/2016" 
	ms.author="heidist"/>

# Erste Schritte mit Azure Search im Azure-Portal

Microsoft Azure Search ist ein Clouddienst, den Sie zum Hinzufügen von Suchfunktionalität zu benutzerdefinierten Anwendungen verwenden können. Der Dient stellt die Suchmaschine und Speicherplatz für Ihre Daten zur Verfügung. Zugriff und Verwaltung können über das Azure-Portal, über ein .NET-SDK oder über eine REST-API erfolgen.

Dieser Artikel ist eine Einführung in Azure Search ohne Code, indem die direkt in das Portal integrierten Funktionen beschrieben werden. In diesem Tutorial wird die Verwendung einer [Azure DocumentDB-Beispieldatenbank](#apdx-sampledata) vorausgesetzt, die Sie anhand unserer Beispieldaten und Anleitung schnell erstellen können. Sie können diesen Workflow aber auch auf Ihre vorhandenen Daten in DocumentDB oder SQL-Datenbank anwenden.

> [AZURE.NOTE] Für dieses Tutorial sind ein [Azure-Abonnement](../includes/free-trial-note.md) und ein [Azure Search-Dienst](search-create-service-portal.md) erforderlich. Wenn Sie sich noch nicht für ein Testabonnement registrieren möchten, können Sie dieses Lernprogramm überspringen und stattdessen [Azure App Service testen](search-tryappservice.md). Bei dieser Alternativoption erhalten Sie Azure Search mit einer ASP.NET-Web-App kostenlos – eine Stunde pro Sitzung, ganz ohne Abonnement.
 
## Suchen nach dem Dienst

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Öffnen Sie das Service-Dashboard des Azure-Suchdiensts. Im Anschluss finden Sie einige Möglichkeiten, um zum Dashboard zu gelangen.
	- Klicken Sie in der Navigationsleiste auf **Suchdienste**. In der Navigationsleiste werden alle Dienste angegeben, die unter Ihrem Abonnement bereitgestellt werden. Wenn ein Suchdienst definiert wurde, wird in der Liste der Eintrag **Suchdienste** angezeigt.
	- Klicken Sie in der Navigationsleiste auf **Durchsuchen**, und geben Sie im Suchfeld dann „search“ ein, um eine Liste mit allen Suchdiensten zu generieren, die für Ihre Abonnements erstellt wurden.

## Überprüfen des Speicherplatzes

Viele Kunden beginnen mit dem kostenlosen Dienst (Free). Diese Version ist auf drei Indizes, drei Datenquellen und drei Indexer beschränkt. Stellen Sie sicher, dass Platz für zusätzliche Elemente vorhanden ist, bevor Sie mit dieser exemplarischen Vorgehensweise beginnen. In dieser exemplarischen Vorgehensweise wird ein Objekt für jede Objektgruppe erstellt.

## Erstellen eines Index und Laden von Daten

Suchabfragen durchlaufen einen *Index* mit durchsuchbaren Daten, Metadaten und Konstrukten, die für die Optimierung bestimmter Suchverhalten verwendet werden. Im ersten Schritt müssen Sie einen Index definieren und auffüllen.

Es gibt mehrere Methoden zum Erstellen eines Index. Die Ansätze variieren je nach Grad der Automation oder Integration. Wenn Sie über verwendbare Daten in einem Datenspeicher verfügen, der von Azure Search per Crawler durchlaufen werden kann – z. B. Azure SQL-Datenbank, SQL Server auf einer Azure-VM oder DocumentDB –, können Sie einen Index leicht erstellen und auffüllen, indem Sie einen Indexer verwenden.

Zur Vereinfachung dieser Aufgabe wird Folgendes angenommen: Es ist eine Datenquelle vorhanden, die von Azure Search per Crawler durchlaufen werden kann, indem einer der *Indexer* und der Assistent **Daten importieren** verwendet werden.

Zur Vorbereitung können Sie schnell eine [DocumentDB-Beispieldatenbank](#apdx-sampledata) erstellen, die für dieses Tutorial genutzt werden kann. Sie können aber auch versuchen, diese Schritte für Ihre eigenen Daten auszuführen.

<a id="defineDS"></a>
#### Schritt 1: Definieren der Datenquelle

1. Klicken Sie auf dem Dashboard des Azure Search-Diensts in der Befehlsleiste auf **Daten importieren**, um einen Assistenten zu starten, mit dem ein Index erstellt und aufgefüllt wird.

2. Klicken Sie auf **Datenquelle** > **DocumentDB** > **Name**, und geben Sie einen Namen für die Datenquelle ein. Eine Datenquelle ist ein Verbindungsobjekt in Azure Search, das mit anderen Indexern verwendet werden kann. Nachdem die Datenquelle erstellt wurde, wird sie in Ihrem Dienst als „vorhandene Datenquelle“ verfügbar gemacht.

3. Wählen Sie Ihr vorhandenes DocumentDB-Konto sowie die Datenbank und die Sammlung aus. Wenn Sie die von uns bereitgestellten Beispieldaten nutzen, sieht Ihre Datenquellendefinition wie folgt aus:

  ![][2]

Beachten Sie, dass wir diese Abfrage überspringen. Der Grund ist, dass wir die Änderungsnachverfolgung hier nicht in unserem Dataset implementieren. Wenn Ihr Dataset ein Feld enthält, mit dem der Zeitpunkt der Datensatzaktualisierung nachverfolgt wird, können Sie einen Azure Search-Indexer konfigurieren, um die Änderungsnachverfolgung für selektive Updates Ihres Index zu nutzen.

Klicken Sie auf **OK**, um diesen Schritt des Assistenten abzuschließen.

#### Schritt 2: Definieren des Index

Klicken Sie im Assistenten auf **Index**, und sehen Sie sich die Entwurfsoberfläche zum Erstellen eines Azure Search-Index an. Für einen Index sind mindestens ein Name und eine Felderauflistung erforderlich, wobei ein Feld als Dokumentschlüssel gekennzeichnet ist. Da wir ein DocumentDB-Dataset verwenden, werden die Felder vom Assistenten automatisch erkannt, und Felder und Datentypzuweisungen werden vorab in den Index geladen.

  ![][3]

Die Felder und Datentypen sind zwar konfiguriert, aber Sie müssen trotzdem Attribute zuweisen. Die Kontrollkästchen oben in der Felderliste sind *Indexattribute*, mit denen gesteuert wird, wie das Feld verwendet wird.

- **Abrufbar** bedeutet, dass es in der Liste mit den Suchergebnissen angezeigt wird. Sie können einzelne Felder kennzeichnen, um sie aus den Suchergebnissen auszuschließen, z. B. wenn Felder nur in Filterausdrücken verwendet werden. 
- Mit **Filterbar**, **Sortierbar** und **Facettenreich** wird angegeben, ob ein Feld in einer Filter-, Sortier- oder Facettennavigationsstruktur verwendet werden kann. 
- **Durchsuchbar** bedeutet, dass ein Feld in die Volltextsuche einbezogen wird. Numerische Felder und boolesche Felder werden häufig als „Nicht durchsuchbar“ markiert. 

Markieren Sie für die Felder im Index vor dem Verlassen der Seite die Verwendung der folgenden Optionen (Abrufbar, Durchsuchbar usw.). Die meisten Felder sind abrufbar. Die meisten Zeichenfolgenfelder sind durchsuchbar (Sie müssen den Schlüssel nicht durchsuchbar machen). Einige Felder, z. B. „genre“, „orderableOnline“, „rating“ und „tags“, sind auch filterbar, sortierbar und facettenreich.
	
Feld | Typ | Optionen |
------|------|---------|
key | Edm.String | |
albumTitle | Edm.String | Abrufbar, durchsuchbar |
albumUrl | Edm.String | Abrufbar, durchsuchbar |
genre | Edm.String | Abrufbar, durchsuchbar, filterbar, sortierbar, facettenreich |
genreDescription | Edm.String | Abrufbar, durchsuchbar |
artistName | Edm.String | Abrufbar, durchsuchbar |
orderableOnline | Edm.Boolean | Abrufbar, filterbar, sortierbar, facettenreich |
tags | Collection(Edm.String) | Abrufbar, filterbar, facettenreich |
price | Edm.Double | Abrufbar, filterbar, facettenreich |
margin | Edm.Int32 | |
rating | Edm.Int32 | Abrufbar, filterbar, sortierbar, facettenreich |
inventory | Edm.Int32 | Abrufbar |
lastUpdated | Edm.DateTimeOffset | |

Zu Vergleichszwecken ist der folgende Screenshot eine Darstellung eines Index, der anhand der Spezifikation in der vorherigen Tabelle erstellt wurde.

 ![][4]

Klicken Sie auf **OK**, um diesen Schritt des Assistenten abzuschließen.

#### Schritt 3: Definieren des Indexers

Klicken Sie im Assistenten **Daten importieren** auf **Indexer** > **Name**, geben Sie einen Namen für den Indexer ein, und verwenden Sie die Standardwerte für alle anderen Werte. Mit diesem Objekt wird ein ausführbarer Prozess definiert. Nach der Erstellung können Sie einen wiederkehrenden Zeitplan einrichten. Verwenden Sie hier aber die Standardoption, um den Indexer einmal sofort auszuführen, wenn Sie auf **OK** klicken.

Ihre Dateneinträge für den Import sollten eingefügt und bereit sein.

  ![][5]

Klicken Sie zum Ausführen des Assistenten auf **OK**, um den Import zu starten und den Assistenten zu schließen.

## Überprüfen des Status

Wechseln Sie zum Überprüfen des Status zurück zum Dashboard des Diensts, und doppelklicken Sie auf die Kachel **Indexer**, um die Liste mit den Indexern zu öffnen. Der gerade erstellte Indexer sollte nun in der Liste angezeigt werden. Der Status sollte „In Bearbeitung“ oder „Erfolg“ lauten, und die Anzahl der für Azure Search indizierten Dokumente sollte angegeben werden.

  ![][6]

## Indexabfragen

Sie verfügen jetzt über einen Suchindex, der bereit für Abfragen ist.

Der **Suchexplorer** ist ein Abfragetool, das in das Portal integriert ist. Es verfügt über ein Suchfeld, mit dem Sie überprüfen können, ob für eine Sucheingabe die erwarteten Daten zurückgegeben werden.

1. Klicken Sie in der Befehlsleiste auf **Suchexplorer**.
2. Achten Sie darauf, welcher Index aktiv ist. Falls dies nicht der gerade erstellte Index ist, können Sie auf der Befehlsleiste auf **Index ändern** klicken, um den gewünschten Index auszuwählen.
2. Lassen Sie das Suchfeld leer, und klicken Sie dann auf die Schaltfläche**Suche**, um eine Platzhaltersuche auszuführen, bei der alle Dokumente zurückgegeben werden.
3. Geben Sie einige Volltextsuche-Abfragen ein. Sie können die Ergebnisse Ihrer Platzhaltersuche überprüfen, um sich mit Interpreten, Alben und Genres für Abfragen vertraut zu machen.
4. Probieren Sie andere Abfragesyntax aus, indem Sie die [Beispiele am Ende dieses Artikels](https://msdn.microsoft.com/library/azure/dn798927.aspx) nutzen, um Ideen zu erhalten. Ändern Sie dann Ihre Abfrage, um Suchzeichenfolgen zu verwenden, die in Ihrem Index mit ziemlicher Sicherheit gefunden werden.

## Nächste Schritte

Nachdem Sie den Assistenten einmal ausgeführt haben, können Sie einen Schritt zurück machen und einzelne Komponenten anzeigen oder ändern: Index, Indexer oder Datenquelle. Einige Bearbeitungen, z. B. das Ändern des Felddatentyps, sind für den Index nicht zulässig, aber die meisten Eigenschaften und Einstellungen können geändert werden. Klicken Sie zum Anzeigen der einzelnen Komponenten auf die Kacheln **Index**, **Indexer** oder **Datenquellen** auf Ihrem Dashboard, um eine Liste mit vorhandenen Objekten anzuzeigen.

Weitere Informationen zu anderen Features, die in diesem Artikel erwähnt werden, finden Sie unter diesen Links:

- [Indexer](search-indexer-overview.md)
- [Create Index (enthält eine ausführliche Erklärung der Indexattribute)](https://msdn.microsoft.com/library/azure/dn798941.aspx)
- [Suchexplorer](search-explorer.md)
- [Search Documents (enthält Beispiele für Abfragesyntax)](https://msdn.microsoft.com/library/azure/dn798927.aspx)

Sie können denselben Workflow auch ausprobieren, indem Sie den Assistenten „Daten importieren“ für andere Datenquellen wie Azure SQL-Datenbank oder SQL Server auf virtuellen Azure-Maschinen verwenden.

> [AZURE.NOTE] Neu angekündigt ist die Indexerunterstützung für das Durchsuchen von Azure Blob Storage per Crawler. Dieses Feature befindet sich aber noch in der Vorschauphase und ist noch keine Option im Portal. Sie müssen Code schreiben, um diesen Indexer auszuprobieren. Weitere Informationen finden Sie unter [Indizieren von Azure Blob Storage in Azure Search](search-howto-indexing-azure-blob-storage.md). <a id="apdx-sampledata"></a>


## Anhang: Abrufen von Beispieldaten aus dieser DocumentDB-Datenbank

In diesem Abschnitt wird eine kleine Datenbank in DocumentDB erstellt, die zum Durchführen der Aufgaben in diesem Tutorial verwendet werden kann.

Im Folgenden erhalten Sie eine allgemeine Anleitung, die aber nicht alle Aspekte vollständig abdeckt. Falls Sie bei der Navigation oder den Aufgaben im DocumentDB-Portal mehr Hilfe benötigen, können Sie die DocumentDB-Dokumentation nutzen. Die meisten benötigten Befehle befinden sich aber in der Befehlsleiste des Diensts oben im Dashboard oder auf dem Blatt der Datenbank.

  ![][1]

Wir stellen 246 JSON-Dokumente für dieses Dataset bereit. Sie müssen diese Dokumente im mehreren Batches (kleiner als 100) hochladen, um die Uploadanforderungen des Dokument-Explorers zu erfüllen.

[Klicken Sie hier](https://github.com/HeidiSteen/azure-search-get-started-sample-data), um die JSON-Datendateien für den Music Store herunterzuladen.

1. Fügen Sie DocumentDB Ihrem Abonnement hinzu, und öffnen Sie anschließend das Dashboard des Diensts.
2. Klicken Sie auf **Datenbank hinzufügen**, um eine neue Datenbank mit der ID `musicstoredb` zu erstellen. Sie wird nach der Erstellung in einer Datenbankliste weiter unten auf der Seite angezeigt.
2. Klicken Sie auf den Namen der Datenbank, um das Blatt für die Datenbank zu öffnen.
3. Klicken Sie auf **Sammlung hinzufügen**, um eine Sammlung mit der ID `musicstorecoll` zu erstellen.
3. Klicken Sie auf **Dokument-Explorer**.
4. Klicken Sie auf **Dokumente hinzufügen**.
5. Laden Sie unter **Dokument hinzufügen** JSON-Dateien hoch.
	- 386\.json
	- 387\.json
	- . . .
6. Klicken Sie auf **Abfrage-Explorer**, um zu überprüfen, ob die Daten hochgeladen wurden.
7. Eine einfache Möglichkeit ist hierbei die Änderung der Standardabfrage, sodass die obersten 300 Elemente ausgewählt werden (es sind insgesamt weniger als 300 Elemente vorhanden). Sie können aber auch den Code `select * from musicstorecoll` schreiben und dann auf **Abfrage ausführen** klicken.

Sie sollten eine JSON-Ausgabe zurückerhalten, die mit der Dokumentnummer 386 beginnt und mit Dokument 669 endet. Nach dem Laden der Daten können Sie sie [zum Starten dieser exemplarischen Vorgehensweise verwenden](#defineDS), indem Sie den **Import-Assistenten** von Azure Search nutzen.


<!--Image references-->
[1]: ./media/search-get-started-portal/AzureSearch-GetStart-Docdbmenu1.png
[2]: ./media/search-get-started-portal/AzureSearch-GetStart-DataSource.png
[3]: ./media/search-get-started-portal/AzureSearch-GetStart-DefaultIndex.png
[4]: ./media/search-get-started-portal/AzureSearch-GetStart-FinishedIndex.png
[5]: ./media/search-get-started-portal/AzureSearch-GetStart-ImportReady.png
[6]: ./media/search-get-started-portal/AzureSearch-GetStart-IndexerList.png

<!---HONumber=AcomDC_0211_2016-->