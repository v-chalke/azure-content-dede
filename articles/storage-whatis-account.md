﻿<properties 
	pageTitle="Über Azure-Speicherkonten | Microsoft Azure" 
	description="Erfahren Sie mehr über das Arbeiten mit Azure-Speicherkonten." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2014" 
	ms.author="tamram"/>


# Über Azure-Speicherkonten

Ein Azure-Speicherkonto ist ein sicheres Konto, mit dem Sie Zugriff auf Dienste in Azure-Speicher erhalten. Ihr Speicherkonto enthält den eindeutigen Namespace für Ihre Daten und ist standardmäßig nur für Sie als Kontoinhaber zugänglich. 

Zwei Typen von Speicherkonten stehen zur Verfügung:

- Ein Standardspeicherkonto enthält Blob-, Tabellen und Warteschlangenspeicher. Dateispeicher ist auf Anfrage über die [Azure-Vorschauseite] verfügbar(/de-de/services/preview/).
- Ein Premium-Speicherkonto unterstützt aktuell ausschließlich Festplatten virtueller Azure-Computer. Azure Premium Storage ist auf Anfrage über die [Azure-Vorschauseite](/de-de/services/preview/) verfügbar. Unter [Premium Storage: Hochleistungsspeicher für Arbeitsauslastungen auf virtuellen Azure-Computern](http://go.microsoft.com/fwlink/?LinkId=521898) finden Sie eine ausführliche Übersicht über Azure Premium Storage.

Die Nutzung von Azure-Speicher wird Ihnen auf der Basis Ihres Speicherkontos in Rechnung gestellt. Speicherkosten basieren auf vier Faktoren: Speicherkapazität, Replikationsschema, Speichertransaktionen und Datenausgang. 

- Speicherkapazität bezieht sich darauf, wie viel von Ihrer Speicherkontozuweisung zum Speichern von Daten verwendet wird. Die Kosten des einfachen Speicherns von Daten wird dadurch bestimmt, wie viele Daten Sie speichern und wie diese Daten repliziert werden. 
- Die Replikation bestimmt, wie viele Kopien Ihrer Daten auf einmal und an welchen Speicherorten beibehalten werden. 
- Transaktionen beziehen sich auf alle Lese- und Schreibvorgänge im Azure-Speicher. 
- Datenausgang bezieht sich auf Daten, die aus einer Azure-Region übertragen werden. Wenn eine Anwendung, die nicht in der gleichen Region ausgeführt wird und entweder ein Clouddienst oder ein anderer Anwendungstyp ist, auf die Daten in Ihrem Speicherkonto zugreift, fallen Gebühren für den Datenausgang an. (Für Azure-Dienste können Sie Maßnahmen durchführen, um Daten und Dienste in den gleichen Rechenzentren zu gruppieren und so Datenausgangsgebühren zu reduzieren oder zu eliminieren.)  

Die Seite mit den [Speicherpreisdetails](http://azure.microsoft.com/pricing/details/#storage) bietet detaillierte Preisinformationen für Speicherkapazität, Replikation und Transaktionen. In den [Datenübertragungs-Preisdetails](http://azure.microsoft.com/pricing/details/data-transfers/) finden Sie detaillierte Preisinformationen für den Datenausgang.

Weitere Informationen zu Kapazität und Leistungszielen von Speicherkonten finden Sie unter [Ziele für Skalierbarkeit und Leistung des Azure-Speichers](http://msdn.microsoft.com/library/windowsazure/dn249410.aspx).

> [AZURE.NOTE] Wenn Sie einen virtuellen Azure-Computer erstellen, wird ein Speicherkonto automatisch in der Bereitstellungsregion erstellt, falls Sie noch nicht über ein Speicherkonto in der entsprechenden Region verfügen. Es ist nicht erforderlich, den unten aufgeführten Schritten zum Anlegen eines Speicherkontos für die Festplatten Ihres virtuelles Computers zu folgen. Der Name des Speicherkontos basiert auf dem Namen des virtuellen Computers. In der [Dokumentation zu virtuellen Azure-Computern](/de-de/documentation/services/virtual-machines/) finden Sie weitere Details.  <br />

## Inhaltsverzeichnis ##

In diesem Artikel wird beschrieben, wie Sie ein Standardspeicherkonto erstellen. Sie erfahren zudem mehr über einige Entscheidungen, die Sie bei der Erstellung des Kontos in Betracht ziehen müssen. Er beschreibt auch, wie Sie die Zugriffsschlüssel für Ihr Speicherkonto verwalten und ein Speicherkonto löschen.


- [Vorgehensweise: Erstellen eines Speicherkontos](#create)
- [Vorgehensweise: Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln](#regeneratestoragekeys)
- [Vorgehensweise: Löschen eines Speicherkontos](#deletestorageaccount)
* [Nächste Schritte](#next)

## <a id="create"></a>Vorgehensweise: Erstellen eines Speicherkontos ##

1. Melden Sie sich beim [Verwaltungsportal](https://manage.windowsazure.com) an.

2. Klicken Sie auf **Neu erstellen**, auf **Speicher** und dann auf **Schnellerfassung**.

	![NewStorageAccount](./media/storage-create-storage-account/storage_NewStorageAccount.png)

3. Geben Sie unter **URL** einen Namen für Ihr Speicherkonto ein. Weitere Informationen finden Sie unter [Speicherkontoendpunkte](#account-endpoints) unten, insbesondere wie dieser Name für das Ansprechen von Objekten verwendet wird, die sich im Azure-Speicher befinden.

4. Wählen Sie unter **Speicherort/Affinitätsgruppe** einen Speicherort für Ihr Speicherkonto aus, der in Ihrer Nähe oder der Nähe Ihrer Kunden ist. Wird von einem anderen Azure-Dienst aus auf die Daten zugegriffen, wie etwa einem virtuellen Azure-Computer oder Cloud-Dienst, empfiehlt es sich, eine Affinitätsgruppe aus der Liste auszuwählen. Damit gruppieren Sie das Speicherkonto im selben Rechenzentrum wie andere von Ihnen verwendete Azure-Dienste und senken die Kosten. 

	> [AZURE.NOTE] Beachten Sie, dass Sie die Affinitätsgruppe beim Erstellen des Speicherkontos auswählen müssen; ein bestehendes Konto kann nicht einer Affinitätsgruppe zugeordnet werden.

	Details über Affinitätsgruppen finden Sie unter [Dienst am selben Standort wie eine Affinitätsgruppe](#affinity-group) unten.

	
5. Wenn Sie über mehrere Azure-Abonnements verfügen, wird das Feld **Abonnement** angezeigt. Geben Sie unter **Abonnement** das Azure-Abonnement ein, das Sie mit dem Speicherkonto verwenden möchten. Sie können bis zu fünf Speicherkontos für ein Abonnement erstellen.

6. Wählen Sie unter **Replikation** die gewünschte Replikationsstufe für das Speicherkonto. Die empfohlene Replikationsoption ist die georedundante Replikation. Sie sorgt für maximalen Bestand Ihrer Daten. Weitere Details zu den Replikationsoptionen für Azure-Speicher finden Sie unter [Speicherkonto-Replikationsoptionen](#replication-options) unten.

6. Klicken Sie auf **Speicherkonto erstellen**.

	Die Erstellung Ihres Speicherkontos kann einige Minuten in Anspruch nehmen. Sie können die Benachrichtigungen unten im Portal überwachen, um den Status zu überprüfen. Nachdem das Speicherkonto erstellt wurde, hat Ihr neues Speicherkonto den Status **Online** und kann verwendet werden. 

![StoragePage](./media/storage-create-storage-account/Storage_StoragePage.png)


### <a id="account-endpoints"></a>Speicherkontoendpunkte 

Jedes im Azure-Speicher abgelegte Objekt hat eine eindeutige URL-Adresse; der Speicherkontoname bildet die Unterdomäne dieser Adresse. Unterdomäne und Domänenname, die für jeden Dienst spezifisch sind, bilden einen *Endpunkt* für Ihr Speicherkonto. 

Wenn Ihr Speicherkonto beispielsweise *mystorageaccount* heißt, sind folgende Standardendpunkte für Ihr Speicherkonto vorhanden: 

- Blob-Dienst: http://*mystorageaccount*.blob.core.windows.net

- Tabellendienst: http://*mystorageaccount*.table.core.windows.net

- Warteschlangendienst: http://*mystorageaccount*.queue.core.windows.net

- Dateidienst: http://*mystorageaccount*.file.core.windows.net

Die Endpunkte für Ihr Speicherkonto werden im Speicher-Dashboard im Azure-Verwaltungsportal angezeigt, sobald Ihr Konto erstellt wurde.

Die URL für den Zugriff auf ein Objekt in einem Speicherkonto wird durch Anhängen des Objektstandorts im Speicherkonto an den Endpunkt generiert. Eine Blob-Adresse kann beispielsweise folgendes Format haben: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Sie können auch einen benutzerdefinierten Domänennamen konfigurieren, den Sie mit Ihrem Speicherkonto verwenden. Weitere Informationen finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für Blob-Daten in einem Speicherkonto](../storage-custom-domain-name/) .

### <a id="affinity-group"></a>Dienstzusammenstellung mit einer Affinitätsgruppe 

Eine *Affinitätsgruppe* ist eine geografische Gruppierung Ihrer Azure-Dienste und VMs mit Ihrem Azure-Konto. Eine Affinitätsgruppe kann die Dienstleistung verbessern, indem Computerarbeitslasten im gleichen Rechenzentrum oder in der Nähe der Zielbenutzer platziert werden. Außerdem fallen keine Gebühren für den Datenausgang an, wenn ein Dienst, der zur gleichen Affinitätsgruppe gehört, auf Daten im Speicherkonto zugreift.

> [AZURE.NOTE]  Um eine Affinitätsgruppe zu erstellen, öffnen Sie den Bereich <b>Einstellungen</b> im Verwaltungsportal, klicken auf <b>Affinitätsgruppen</b> und anschließend entweder auf <b>Affinitätsgruppe hinzufügen</b> oder auf die Schaltfläche <b>Hinzufügen</b>. Sie können Affinitätsgruppen auch mithilfe der Azure Service Management-API erstellen und verwalten. Weitere Informationen erhalten Sie unter <a href="http://msdn.microsoft.com/library/windowsazure/ee460798.aspx">Operations on Affinity Groups</a> (Vorgänge zu Affinitätsgruppen, in englischer Sprache).


### <a id="replication-options"></a>Speicherkonto-Replikationsoptionen

[AZURE.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]


## <a id="regeneratestoragekeys"></a>Vorgehensweise: Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln

Wenn Sie ein Speicherkonto erstellen, generiert Azure zwei 512-Bit-Speicherzugriffsschlüssel, die für die Authentifizierung verwendet werden, wenn der Zugriff auf das Speicherkonto erfolgt. Durch Bereitstellen von zwei Speicherzugriffsschlüsseln ermöglicht Azure Ihnen das erneute Generieren der Schlüssel ohne Unterbrechung des Speicherdiensts oder Zugriff auf diesen Dienst.

> [AZURE.NOTE] Sie sollten das Weitergeben von Speicherkonto-Zugriffsschlüsseln an andere vermeiden. Um den Zugriff auf Speicherressourcen zu gewähren, ohne den Zugriffsschlüssel weiterzugeben, verwenden Sie eine *Shared Access Signature*. Eine Shared Access Signature (SAS) bietet Zugriff auf eine Ressource in Ihrem Konto für ein von Ihnen definiertes Zeitintervall und mit den von Ihnen festgelegten Berechtigungen. Weitere Informationen finden Sie im [Shared Access Signature-Lernprogramm](../storage-dotnet-shared-access-signature-part-1/) .

Verwenden Sie im [Verwaltungsportal](http://manage.windowsazure.com) die Option **Schlüssel verwalten** auf dem Dashboard oder die Seite **Speicher**, um die Speicherzugriffsschlüssel, dir für den Zugriff auf Blob-, Tabellen- und Warteschlangendienste verwendet werden, anzuzeigen, zu kopieren und erneut zu generieren. 

### Kopieren eines Speicherzugriffsschlüssels ###

Sie können **Schlüssel verwalten** verwenden, um einen Speicherzugriffsschlüssel für die Verwendung in einer Verbindungszeichenfolge zu kopieren. Die Verbindungszeichenfolge benötigt für die Authentifizierung den Namen des Speicherkontos und einen Schlüssel. Informationen zum Konfigurieren von Verbindungszeichenfolgen für den Zugriff auf Azure-Speicherdienste finden Sie unter [Konfigurieren von Verbindungszeichenfolgen](http://msdn.microsoft.com/library/ee758697.aspx).

1. Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com) auf **Speicher** und dann auf den Namen des Speicherkontos, um das Dashboard zu öffnen.

2. Klicken Sie auf **Schüssel verwalten**.

 	**Zugriffsschlüssel verwalten** wird geöffnet.

	![Managekeys](./media/storage-create-storage-account/Storage_ManageKeys.png)

 
3. Markieren Sie den Schlüsseltext, um einen Speicherzugriffsschlüssel zu kopieren. Klicken Sie dann mit der rechten Maustaste, und klicken Sie auf **Kopieren**.

### Erneutes Generieren von Speicherzugriffsschlüsseln ###
Sie sollten regelmäßig die Zugriffsschlüssel für Ihr Speicherkonto ändern, um dafür zu sorgen, dass Ihre Speicherverbindungen möglichst sicher sind. Zwei Zugriffsschlüssel werden zugewiesen, um es Ihnen zu ermöglichen, Verbindungen zum Speicherkonto mit einem Zugriffsschlüssel aufrecht zu erhalten, während Sie den anderen Zugriffsschlüssel neu generieren. 

> [AZURE.WARNING] Das erneute Generieren der Zugriffsschlüssel wirkt sich auf virtuelle Computer, Mediendienste und alle Anwendungen aus, die vom Speicherkonto abhängen. Alle Clients, die den Zugriffsschlüssel verwenden, um auf das Speicherkonto zuzugreifen, müssen aktualisiert werden, um den neuen Schlüssel zu verwenden.

**Virtuelle Computer**: Falls Ihr Speicherkonto virtuelle Computer enthält, die ausgeführt werden, müssen Sie alle virtuellen Computer nach dem erneuten Generieren der Zugriffsschlüssel neu bereitstellen. Um die erneute Bereitstellung zu vermeiden, beenden Sie die virtuellen Computer, bevor Sie die Zugriffsschlüssel neu generieren.
 
**Mediendienste**: Falls Sie Mediendienste haben, die auf Ihr Speicherkonto angewiesen sind, müssen Sie die Zugriffsschlüssel mit Ihrem Mediendienst nach dem erneuten Generieren der Schlüssel erneut synchronisieren.
 
**Anwendungen**: Falls Sie Webanwendungen oder Clouddienste haben, die das Speicherkonto verwenden, verlieren Sie die Verbindungen beim erneuten Generieren von Schlüsseln - es sei denn, Sie führen einen Rollup für die Schlüssel aus. Der Prozess ist folgendermaßen:

1. Aktualisieren Sie die Verbindungszeichenfolgen im Anwendungscode, damit sie auf den sekundären Zugriffsschlüssel des Speicherkontos verweisen. 

2. Generieren Sie den primären Zugriffsschlüssel für das Speicherkonto neu. Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com) im Dashboard oder auf der Seite **Konfigurieren** auf **Schlüssel verwalten**. Klicken Sie unter dem primären Zugriffsschlüssel auf **Neu generieren** und dann auf **Ja**, um das Generieren eines neuen Schlüssels zu bestätigen.

3. Aktualisieren Sie die Verbindungszeichenfolgen in Ihrem Code, um auf den neuen primären Zugriffsschlüssel zu verweisen.

4. Generieren Sie den sekundären Zugriffsschlüssel neu.

## <a id="deletestorageaccount"></a>Vorgehensweise: Löschen eines Speicherkontos

Um ein nicht mehr verwendetes Speicherkonto zu entfernen, verwenden Sie **Löschen** im Dashboard oder auf der Seite **Konfigurieren**. **Durch Löschen** wird das gesamte Speicherkonto gelöscht, einschließlich aller Blobs, Tabellen und Warteschlangen im Konto. 

> [AZURE.WARNING] Es gibt keine Möglichkeit zur Wiederherstellung des Inhalts aus einem gelöschten Speicherkonto. Stellen 
	Sie sicher, dass Sie alle zu speichernden Inhalte sichern, bevor Sie das Konto löschen. <br />
	Falls Ihr Speicherkonto VHD-Dateien oder -Festplatten für einen virtuellen Azure-Computer enthält, müssen Sie alle Images und Festplatten, die diese VHD-Dateien verwenden, löschen, bevor Sie das Speicherkonto löschen können. Halten Sie zunächst den virtuellen Computer an, falls er ausgeführt wird, und löschen Sie ihn dann. Um Festplatten zu löschen, navigieren Sie zur Registerkarte "Festplatten". Löschen Sie alle im Speicherkonto enthaltenen Festplatten. Um Images zu löschen, navigieren Sie zur Registerkarte "Images". Löschen Sie alle im Konto gespeicherten Images.


1. Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com) auf **Speicher**.

2. Klicken Sie im Eintrag des Speicherkontos auf eine beliebige Stelle (nicht auf den Namen). Klicken Sie dann auf **Löschen**.

	 -Oder-

	Klicken Sie auf den Namen des Speicherkontos, um das Dashboard zu öffnen. Klicken Sie dann auf **Löschen**.

3. Klicken Sie auf **Ja**, um zu bestätigen, dass Sie das Speicherkonto löschen möchten.

## <a id="next"></a>Nächste Schritte

- Weitere Informationen über Azure Storage finden Sie in der entsprechenden Dokumentation auf [azure.com](http://azure.microsoft.com/documentation/services/storage/) und auf [MSDN](http://msdn.microsoft.com/library/gg433040.aspx). 

- Besuchen Sie den [Azure Storage-Teamblog](http://blogs.msdn.com/b/windowsazurestorage/).




<!--HONumber=42-->