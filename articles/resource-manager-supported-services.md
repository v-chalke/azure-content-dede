<properties
   pageTitle="Vom Ressourcen-Manager unterstützte Dienste und Regionen | Microsoft Azure"
   description="Beschreibt die Ressourcenanbieter, die den Ressourcen-Manager und die Regionen unterstützen, die die Ressourcen hosten können."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/13/2015"
   ms.author="tomfitz"/>

# Unterstützung des Azure-Ressourcen-Managers für Dienste und Regionen

Der Azure-Ressourcen-Manager bietet eine neue Möglichkeit, die Dienste, aus denen Ihre Anwendungen bestehen, bereitzustellen und zu verwalten. Die meisten (jedoch nicht alle) Dienste unterstützen den Ressourcen-Manager, und einige Dienste unterstützen ihn nur teilweise. Microsoft ermöglicht den Ressourcen-Manager für jeden Dienst, der für zukünftige Lösungen wichtig ist. Bis diese Unterstützung jedoch konsistent ist, müssen Sie den aktuellen Status jedes Diensts kennen. Dieses Thema enthält eine Liste der unterstützten Ressourcenanbieter für den Azure-Ressourcen-Manager.

Beim Bereitstellen von Ressourcen müssen Sie wissen, in welchen Regionen diese Ressourcen unterstützt werden. Der Abschnitt [Unterstützte Regionen](#supported-regions) zeigt, wie Sie herausfinden, in welchen Regionen Ihr Abonnement und Ihre Ressourcen verwendet werden können.

Die folgenden Tabellen zeigen, welche Dienste Bereitstellung und Verwaltung über den Ressourcen-Manager unterstützen und welche nicht. Die Spalte mit der Bezeichnung **Ressourcen verschieben** bezieht sich darauf, ob Ressourcen dieses Typs in eine neue Ressourcengruppe und ein neues Abonnement verschoben werden können. Die Spalte mit der Bezeichnung **Vorschauportal** gibt an, ob Sie den Dienst über das Vorschauportal erstellen können.


## Compute

| Dienst | Ressourcen-Manager aktiviert | Vorschauportal | Ressourcen verschieben | REST-API | Schema |
| ------- | ------------------------ | -------------- | -------------- |-------- | ------ |
| Virtual Machines | Ja | Ja | Nein | [VM erstellen](https://msdn.microsoft.com/library/azure/mt163591.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |
| Batch | Ja | Nein | | [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) (in englischer Sprache) | |
| Dynamics Lifecycle Services | Ja | Nein | | | |
| Virtuelle Computer (klassisch) | Eingeschränkt | Nein | Teilweise (siehe unten) | - | - | | Remote-App | Nein | - | - | - | - | | Service Fabric | Nein | - | - | - | - |

"Virtuelle Computer (klassisch)" bezieht sich auf Ressourcen, die über das klassische Bereitstellungsmodell statt über das Ressourcen-Manager-Bereitstellungsmodell bereitgestellt wurden. Im Allgemeinen unterstützen diese Ressourcen keine Ressourcen-Manager-Vorgänge, es wurden jedoch einige Vorgänge aktiviert. Weitere Informationen zu diesen Bereitstellungsmodellen finden Sie unter [Grundlegendes zur Bereitstellung über den Ressourcen-Manager im Vergleich zur klassischen Bereitstellung](resource-manager-deployment-model.md).

Ressourcen des Typs "Virtuelle Computer (klassisch)" können in neue Ressourcengruppen verschoben werden, nicht jedoch in ein neues Abonnement.

## Web- und mobile Anwendungen

| Dienst | Ressourcen-Manager aktiviert | Vorschauportal | Ressourcen verschieben | REST-API | Schema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| API Management| Ja | Nein | Ja | [API erstellen](https://msdn.microsoft.com/library/azure/dn781423.aspx#CreateAPI) | |
| API-Apps | Ja | Ja | | | [2015-03-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) |
| Web-Apps | Ja | Ja | Ja, mit Einschränkungen (siehe unten) | | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |
| Notification Hubs | Ja | Ja | | [Notification Hub erstellen:](https://msdn.microsoft.com/library/azure/dn223269.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) |
| Logik-Apps | Ja | Ja | | | |
| Mobile Engagements | Ja | Nein | Ja | | |

Bei der Arbeit mit Web-Apps können Sie nicht nur einen App Services-Plan verschieben. Zum Verschieben von Web-Apps stehen folgende Optionen bereit:

- Verschieben Sie alle Ressourcen aus einer Ressourcengruppe in eine andere Ressourcengruppe, wenn die Zielressourcengruppe noch keine Microsoft.Web-Ressourcen enthält.
- Verschieben Sie Web-Apps in eine andere Ressourcengruppe, behalten Sie jedoch den App Services-Plan in der ursprünglichen Ressourcengruppe bei.


## Daten und Speicher

| Dienst | Ressourcen-Manager aktiviert | Vorschauportal | Ressourcen verschieben | REST-API | Schema |
| ------- | ------- | ------- | -------------- | -------- | ------ |
| DocumentDB | Ja | Ja | Ja | [DocumentDB REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) | |
| Storage | Ja | Ja | | [Erstellen des Speicherkontos](https://msdn.microsoft.com/library/azure/mt163564.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Storage.json) |
| Redis-Cache | Ja | Ja | Ja | | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Cache.json) |
| SQL-Datenbank | Ja | Ja | Ja | [Erstellen einer Datenbank](https://msdn.microsoft.com/library/azure/mt163685.aspx) | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) |
| Suchen | Ja | Ja | Ja | [Azure-Suchdienst-REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) | |
| SQL Data Warehouse | Ja | Ja | | | |
| StorSimple | Nein | Nein | - | - | - | | Backup | Nein | Nein | - | - | - | | Site Recovery | Nein | Nein | - | - | - | | Managed Cache | Nein | Nein | - | - | - | | Data Catalog | Nein | Nein | - | - | - |

## Analyse

| Dienst | Ressourcen-Manager aktiviert | Vorschauportal | Ressourcen verschieben | REST-API | Schema |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| Event Hub | Ja | Nein | | [Event Hub erstellen](https://msdn.microsoft.com/library/azure/dn790676.aspx) | |
| Stream Analytics | Ja | Ja | | | |
| HDInsights | Ja | Ja | | | |
| Data Factory | Ja | Ja | Ja | [Erstellen einer Data Factory](https://msdn.microsoft.com/library/azure/dn906717.aspx) | |
| Machine Learning | Nein | Nein | - | - | - |

## Netzwerk

| Dienst | Ressourcen-Manager aktiviert | Vorschauportal | Ressourcen verschieben | REST-API | Schema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Application Gateway | Ja | | | | |
| DNS | Ja | | | [Erstellen einer DNS-Zone](https://msdn.microsoft.com/library/azure/mt130622.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Lastenausgleichsmodul | Ja | | | [Erstellen oder Aktualisieren eines Lastenausgleichs](https://msdn.microsoft.com/library/azure/mt163574.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Virtuelle Netzwerke | Ja | Ja | Nein | [Erstellen eines virtuellen Netzwerks](https://msdn.microsoft.com/library/azure/mt163661.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Traffic Manager | Ja | Nein | | [Erstellen oder Aktualisieren eines Traffic Manager-Profils](https://msdn.microsoft.com/library/azure/mt163581.aspx) | |
| Express Route | Nein | Nein | - | - | - |

## Medien und CDN

| Dienst | Ressourcen-Manager aktiviert | Vorschauportal | Ressourcen verschieben | REST-API | Schema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Mediendienst | Nein | Nein | | | |
| CDN | Nein | Nein | | | |

## Hybridintegration

| Dienst | Ressourcen-Manager aktiviert | Vorschauportal | Ressourcen verschieben | REST-API | Schema |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| BizTalk Services | Ja | Nein | | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |
| Service Bus | Ja | Nein | | [Service Bus REST-API-Referenz](https://msdn.microsoft.com/library/azure/hh780717.aspx) | |

## Identitäts- und Zugriffsverwaltung 

| Dienst | Ressourcen-Manager aktiviert | Vorschauportal | Ressourcen verschieben | REST-API | Schema |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| Azure Active Directory | Nein | Nein | - | - | - | | Azure Actice Directory B2C | Nein | Nein | - | - | - | | Multi-Factor Authentication | Nein | Nein | - | - | - |

## Entwicklerdienste 

| Dienst | Ressourcen-Manager aktiviert | Vorschauportal | Ressourcen verschieben | REST-API | Schema |
| ------- | ------- | ---------- | -------------- | -------- | ------ |
| Application Insights | Ja | Ja | | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) |
| Bing Maps | Ja | Ja | | | |
| Visual Studio-Konto | Ja | | | | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |

## Verwaltung 

| Dienst | Ressourcen-Manager aktiviert | Vorschauportal | Ressourcen verschieben | REST-API | Schema |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| Automation | Ja | Ja | | | |
| Schlüsseltresor | Ja | Nein | Ja | [Referenz zur Schlüsseltresor-REST-API](https://msdn.microsoft.com/library/azure/dn903609.aspx) | |
| Scheduler | Ja | Nein | | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) |
| Operational Insights | Ja | Nein | Ja | | |
| IoTHubs | Ja | Ja | | | |


## Unterstützte Regionen

Bei der Bereitstellung von Ressourcen müssen Sie in der Regel eine Region für die Ressourcen angeben. Der Ressourcen-Manager wird in allen Regionen unterstützt, aber die Ressourcen, die Sie bereitstellen, werden möglicherweise nicht in allen Regionen unterstützt. Darüber hinaus liegen möglicherweise Einschränkungen Ihres Abonnements vor, die verhindern, dass Sie einige Regionen verwenden, die die Ressource unterstützen. Diese Einschränkungen können im Zusammenhang mit steuerlichen Problemen in Ihrem Land stehen, oder Sie sind das Ergebnis einer Richtlinie, die von Ihrem Abonnementadministrator implementiert wurde, sodass Sie nur bestimmte Regionen verwenden können.

Überprüfen Sie vor dem Bereitstellen von Ressourcen die unterstützten Regionen für den Ressourcentyp mithilfe eines der folgenden Befehle.

### REST-API

Die beste Möglichkeit zur Ermittlung der verfügbaren Regionen für einen bestimmten Ressourcentyp ist der Vorgang zum [Auflisten aller Ressourcenanbieter](https://msdn.microsoft.com/library/azure/dn790524.aspx). Dieser Vorgang gibt nur die Regionen zurück, die für Ihr Abonnement und den Ressourcentyp zur Verfügung stehen.

### PowerShell

Das folgende Beispiel zeigt, wie Sie die unterstützten Regionen für Websites mithilfe von Azure PowerShell 1.0 Vorschau abrufen. Weitere Informationen zur Vorschauversion 1.0 finden Sie unter [Azure PowerShell 1.0 Preview](https://azure.microsoft.com/blog/azps-1-0-pre/) (in englischer Sprache).

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
Die Ausgabe ähnelt der folgenden:

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

### Azure-Befehlszeilenschnittstelle

Im folgenden Beispiel werden alle unterstützten Orte für jeden Ressourcentyp zurückgegeben.

    azure location list

Sie können die Ergebnisse auch mit einem Tool wie **jq** filtern. Informationen zu Tools wie "jq" finden Sie unter [Nützliche Tools für die Interaktion mit Azure](/virtual-machines/resource-group-deploy-debug/#useful-tools-to-interact-with-azure).

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

Ausgabe des Befehls:

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

<!---HONumber=Oct15_HO3-->