<properties
	pageTitle="Erste Schritte mit Azure Batch für PowerShell-Cmdlets"
	description="Einführung in die Azure PowerShell-Cmdlets zum Verwalten des Azure Batch-Diensts"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor="yidingz"/>

<tags
	ms.service="batch"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="powershell"
	ms.workload="big-compute"
	ms.date="04/15/2015"
	ms.author="danlep"/>

# Erste Schritte mit Azure Batch für PowerShell-Cmdlets
Dieser Artikel dient als kurze Einführung in die Azure PowerShell-Cmdlets, mit denen Sie Ihre Batch-Konten verwalten und Informationen über Ihre Batch-Arbeitsaufgaben, -Aufträge und -Aufgaben abrufen können.

Geben Sie ```get-help <Cmdlet_name>``` ein, um detaillierte Informationen zur Cmdlet-Syntax zu erhalten.


## Voraussetzungen

* **Batch-Vorschau**: Melden Sie sich für die [Batch-Vorschau](https://account.windowsazure.com/PreviewFeatures) an, sofern dies noch nicht erfolgt ist, um den Dienst verwenden zu können.
* **Azure PowerShell**: Anweisungen zu erforderlichen Komponenten, zum Download und zur Installation finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md). Batch-Cmdlets wurden ab Version 0.8.10 eingeführt.

## Verwenden der Batch-Cmdlets

Starten Sie Azure PowerShell entsprechend dem Standardverfahren, und [stellen Sie eine Verbindung mit Ihren Azure-Abonnements her](../powershell-install-configure.md#Connect). Weitere Vorgänge:

* **Auswählen des Azure-Abonnements**: Wenn Sie über mehrere Abonnements verfügen, wählen Sie das Abonnement aus, in dem Sie die Funktion für die Batch-Vorschau hinzugefügt haben:

    ```
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

* **Wechseln in den AzureResourceManage-Modus**: Die Batch-Cmdlets befinden sich im Modul Azure-Ressourcen-Manager. Ausführliche Informationen siehe [Verwenden von Windows PowerShell mit dem Ressourcen-Manager](../powershell-azure-resource-manager.md). Führen Sie zur Verwendung dieses Moduls das Cmdlet [Switch-AzureMode](https://msdn.microsoft.com/library/dn722470.aspx) aus:

    ```
    Switch-AzureMode -Name AzureResourceManager
    ```

## Verwalten von Batch-Konten und Schlüsseln

Mit Azure PowerShell-Cmdlets können Sie Batch-Konten und Schlüssel erstellen und verwalten.

### Erstellen eines Batch-Kontos

**New-AzureBatchAccount** erstellt ein neues Batch-Konto in einer angegebenen Ressourcengruppe. Wenn Sie noch keine Ressourcengruppe erstellt haben, erstellen Sie sie durch Ausführen des Cmdlets [New-AzureResourceGroup](https://msdn.microsoft.com/library/dn654594.aspx). Geben Sie dabei eine der Azure-Regionen im **Location**-Parameter an. (Verfügbare Regionen für verschiedene Azure-Ressourcen finden Sie durch Ausführen von [Get-AzureLocation](https://msdn.microsoft.com/library/dn654582.aspx).) Beispiel:

```
New-AzureResourceGroup –Name MyBatchResourceGroup –location "Central US"
```

Erstellen Sie dann ein neues Batch-Konto in der Ressourcengruppe. Geben Sie zudem einen Kontonamen für "<*account_name*>" und einen Speicherort an, an dem der Batch-Dienst verfügbar ist. Die Erstellung des Kontos kann mehrere Minuten in Anspruch nehmen. Beispiel:

```
New-AzureBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup
```

> [AZURE.NOTE]Der Batch-Kontoname muss in Azure eindeutig sein, zwischen 3 und 24 Zeichen umfassen und kann nur Kleinbuchstaben und Zahlen enthalten.

### Abrufen von Kontozugriffsschlüsseln
**Get-AzureBatchAccountKeys** zeigt die Zugriffsschlüssel an, die einem Azure Batch-Konto zugeordnet sind. Führen Sie beispielsweise Folgendes aus, um den primären und sekundären Schlüssel des Kontos abzurufen, das Sie erstellt haben.

```
$Account = Get-AzureBatchAccountKeys –AccountName <accountname>
$Account.PrimaryAccountKey
$Account.SecondaryAccountKey
```

### Generieren eines neuen Zugriffsschlüssels
**New-AzureBatchAccountKey** generiert einen neuen primären oder sekundären Kontoschlüssel für ein Azure Batch-Konto. Geben Sie zum Generieren eines neuen primären Schlüssels für Ihr Batch-Konto z. B. Folgendes ein:

```
New-AzureBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [AZURE.NOTE]Um einen neuen sekundären Schlüssel zu generieren, geben Sie "Secondary" für den **KeyType**-Parameter ein. Sie müssen den primären und sekundären Schlüssel separat neu generieren.

### Löschen eines Batch-Kontos
**Remove-AzureBatchAccount** löscht ein Batch-Konto. Beispiel:

```
Remove-AzureBatchAccount -AccountName <account_name>
```

Bestätigen Sie bei der entsprechenden Aufforderung, dass Sie das Konto entfernen möchten. Das Entfernen des Kontos kann einige Zeit in Anspruch nehmen.

## Abfragen von Arbeitsaufgaben, Aufträgen und Aufgaben

Verwenden Sie Cmdlets wie z. B. **Get-AzureBatchWorkItem**, **Get-AzureBatchJob**, **Get-AzureBatchTask** oder **Get-AzureBatchPool** zum Abfragen von Entitäten, die in einem Batch-Konto erstellt wurden.

Um diese Cmdlets verwenden zu können, müssen Sie zunächst ein AzureBatchContext-Objekt erstellen, um Ihren Kontonamen und Ihre Schlüssel zu speichern:

```
$context = Get-AzureBatchAccountKeys "<account_name>"
```

Sie übergeben diesen Kontext in Cmdlets, die über den **BatchContext**-Parameter mit dem Batch-Dienst interagieren.

> [AZURE.NOTE]Standardmäßig wird der primäre Schlüssel des Kontos für die Authentifizierung verwendet. Sie können jedoch ausdrücklich den zu verwendenden Schlüssel auswählen, indem Sie die **KeyInUse**-Eigenschaft des BatchAccountContext-Objekts ändern: ```$context.KeyInUse = "Secondary"```.


### Abfragen von Daten

Verwenden Sie beispielsweise **Get-AzureBatchWorkItem** zum Suchen Ihrer Arbeitsaufgaben. Damit werden standardmäßig alle Arbeitsaufgaben unter Ihrem Konto abgefragt, sofern Sie das BatchAccountContext-Objekt bereits in *$context* gespeichert haben:

```
Get-AzureBatchWorkItem -BatchContext $context
```

Dies kann auch für andere Entitäten erfolgen, z. B. für Pools:

```
Get-AzureBatchPool -BatchContext $context
```
### Verwenden eines OData-Filters

Mit dem **Filter**-Parameter können Sie einen OData-Filter angeben, um nur bestimmte gewünschte Objekte zu suchen. Sie können beispielsweise alle Arbeitsaufgaben suchen, deren Namen mit "myWork" beginnt:

```
$filter = "startswith(name,'myWork') and state eq 'active'" 
Get-AzureBatchWorkItem -Filter $filter -BatchContext $context
``` 

Diese Methode ist nicht so flexibel wie die Verwendung von "Where-Object" in einer lokalen Pipeline. Die Abfrage wird jedoch direkt an den Batch-Dienst gesendet, sodass die gesamte Filterung auf dem Server erfolgt, was Internetbandbreite einspart.

### Verwenden des Name-Parameters

Eine Alternative zu einem OData-Filter ist die Verwendung des **Name**-Parameters. So führen Sie eine Abfrage einer bestimmten Arbeitsaufgabe mit dem Namen "MyWorkItem" durch:

``` 
Get-AzureBatchWorkItem -Name "myWorkItem" -BatchContext $context 

```
Der **Name**-Parameter unterstützt nur die Suche nach dem vollständigen Namen, jedoch keine Platzhalter oder Filter im OData-Format.

### Verwenden der Pipeline

Batch-Cmdlets können die PowerShell-Pipeline zum Senden von Daten zwischen Cmdlets nutzen. Dies hat dieselbe Auswirkung wie die Angabe eines Parameters, vereinfacht jedoch das Auflisten mehrerer Entitäten. Sie können beispielsweise alle Aufgaben unter Ihrem Konto suchen:

```
Get-AzureBatchWorkItem -BatchContext $context | Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context 
```

### Verwenden des MaxCount-Parameters

Jedes Cmdlet gibt standardmäßig bis zu 1.000 Objekte zurück. Wenn dieser Grenzwert erreicht ist, können Sie entweder den Filter weiter eingrenzen, sodass weniger Objekte zurückgegeben werden, oder mit dem **MaxCount**-Parameter explizit einen maximalen Wert festlegen. Beispiel:

```
Get-AzureBatchWorkItem -MaxCount 2500 -BatchContext $context 

```

Setzen Sie den **MaxCount**-Parameter auf 0 oder eine negative Zahl, um die Obergrenze zu entfernen.

## Verwandte Themen
* [Batch – Technische Übersicht](batch-technical-overview.md)
* [Download Azure PowerShell](http://go.microsoft.com/p/?linkid=9811175)
* [Azure-Cmdlet-Referenz](https://msdn.microsoft.com/library/jj554330.aspx)

<!---HONumber=GIT-SubDir-->