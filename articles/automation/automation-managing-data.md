<properties 
   pageTitle="Verwalten von Azure Automation-Daten"
   description="Dieser Artikel enthält mehrere Themen zum Verwalten einer Azure Automation-Umgebung. Er enthält derzeit die Themen ";Datenaufbewahrung";, ";Sichern von Azure Automation"; und ";Notfallwiederherstellung in Azure Automation";."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/08/2015"
   ms.author="bwren;sngun" />

# Verwalten von Azure Automation-Daten

Dieser Artikel enthält mehrere Themen zum Verwalten einer Azure Automation-Umgebung.

## Beibehaltung von Daten

Wenn Sie eine Ressource in Azure Automation löschen, wird diese zu Überwachungszwecken für 90 Tage aufbewahrt, bevor sie dauerhaft gelöscht wird. In diesem Zeitraum kann die Ressource weder angezeigt noch verwendet werden. Diese Richtlinie gilt auch für Ressourcen, die zu einem Automation-Konto gehören, das gelöscht wurde.

Aufträge, die älter sind als 90 Tage, werden in Azure Automation automatisch gelöscht und dauerhaft entfernt.

Die folgende Tabelle zeigt die Aufbewahrungsrichtlinie für unterschiedliche Ressourcen.

|Daten|Richtlinie|
|:---|:---|
|Konten|Dauerhafte Entfernung 90 Tage nach dem Löschen des Kontos durch einen Benutzer.|
|Objekte|Dauerhafte Entfernung 90 Tage nach dem Löschen des Objekts durch einen Benutzer, oder 90 Tage nach dem Löschen des Kontos mit dem Objekt durch einen Benutzer.|
|Module|Dauerhafte Entfernung 90 Tage nach dem Löschen des Moduls durch einen Benutzer, oder 90 Tage nach dem Löschen des Kontos mit dem Modul durch einen Benutzer.|
|Runbooks|Dauerhafte Entfernung 90 Tage nach dem Löschen der Ressource durch einen Benutzer, oder 90 Tage nach dem Löschen des Kontos mit der Ressource durch einen Benutzer.|
|Aufträge|Löschung und dauerhafte Entfernung 90 Tage nach der letzten Änderung. Dies kann der Fall sein, wenn der Job abgeschlossen, gestoppt oder angehalten wurde.|

Die Datenaufbewahrungsrichtlinie gilt für alle Benutzer und kann zurzeit nicht angepasst werden.

## Sichern von Azure Automation

Wenn Sie ein Automation-Konto in Microsoft Azure löschen, werden alle Objekte im Konto gelöscht – darunter Runbooks, Module, Einstellungen, Aufträge und Objekte. Die Objekte können nicht wiederhergestellt werden, nachdem das Konto gelöscht wurde. Sie können die Inhalte Ihres Automation-Kontos mithilfe der folgenden Informationen sichern, bevor Sie das Konto löschen.

### Runbooks

Sie können Ihre Runbooks entweder unter Verwendung des Azure-Verwaltungsportals oder mithilfe des Cmdlets [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) in Windows PowerShell in Skriptdateien exportieren. Diese Skriptdateien können in ein anderes Automation-Konto importiert werden, wie beschrieben unter [Erstellen oder Importieren eines Runbooks](https://msdn.microsoft.com/library/dn643637.aspx).


### Integrationsmodule

Integrationsmodule können nicht aus Azure Automation exportiert werden. Sie müssen sicherstellen, dass diese außerhalb des Automation-Kontos verfügbar sind.

### Objekte

[Objekte](https://msdn.microsoft.com/library/dn939988.aspx) können nicht aus Azure Automation exportiert werden. Sie müssen sich unter Verwendung des Azure-Verwaltungsportals die Details zu Variablen, Anmeldeinformationen, Zertifikaten, Verbindungen und Zeitplänen notieren. Anschließend müssen Sie alle Objekte, die in Runbooks verwendet und in ein anderes Automation-Konto importiert werden sollen, manuell erstellen.

Sie können mithilfe von [Azure-Cmdlets](https://msdn.microsoft.com/library/dn690262.aspx) Details aus nicht verschlüsselten Objekten abrufen und diese entweder zu Referenzzwecken speichern, oder Sie können gleichwertige Objekte in einem anderen Automation-Konto erstellen.

Es ist nicht möglich, den Wert verschlüsselter Variablen oder die Kennwortfelder für Anmeldeinformationen mithilfe von Cmdlets abzurufen. Wenn Sie diese Werte nicht kennen, können Sie sie mithilfe der Aktivitäten [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) und [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) aus einem Runbook abrufen.

Zertifikate können nicht aus Azure Automation exportiert werden. Sie müssen sicherstellen, dass die erforderlichen Zertifikate außerhalb von Azure zur Verfügung stehen.

##Georeplikation in Azure Automation

Azure Automation unterstützt die Georeplikation. Bei der Georeplikation werden Ihre Daten von Azure Automation in zwei Regionen beständig gespeichert. Beim Erstellen des Azure Automation-Kontos in Azure-Portal wählen Sie eine Region, in dem es erstellt werden soll. Dies ist die primäre Region. Die Region, in die Ihre Daten per Georeplikation übertragen werden, wird als sekundäre Region bezeichnet. Die primäre und sekundäre Region kommunizieren miteinander, um die Aktualisierungen am Automation-Konto zu georeplizieren. Da in der sekundären Region eine Kopie der Informationen gespeichert wird, stehen bei einem Failover eines Azure Automation-Kontos aus der primären Region in die sekundäre Region die Automation-Kontoinformationen in der sekundären Region weiter zur Verfügung.

Die Georeplikation ist in Automation-Kontos integriert und wird kostenlos geboten. Sie haben keine Kontrolle über die Auswahl der sekundären Region, die basierend auf Ihrer Wahl der primären Region automatisch bestimmt wird.

 
###Speicherorte von Georeplikaten

Derzeit können Automation-Konten in den folgenden fünf Regionen erstellt werden. Weitere Regionen sollen in Zukunft unterstützt werden. In der folgenden Tabelle werden die Paare primärer und sekundärer Regionen gezeigt:

|Primär |Sekundär
| ---------------   |----------------
|USA (Mitte/Süden) |USA (Mitte/Norden)
|USA (Ost 2) |USA (Mitte)
|Westeuropa |Nordeuropa
|Südostasien |Ostasien
|Japan Ost |Japan (Westen)


###Notfallwiederherstellung in Azure Automation

Wenn ein schwerwiegender Notfall die primäre Region beeinträchtigt, versucht das Automation-Team als Erstes, die primäre Region wiederherzustellen. Wenn es in bestimmten Fällen nicht möglich ist, die primäre Region wiederherzustellen, erfolgt ein Geofailover, worüber die betroffenen Kunden über ihr Abonnement benachrichtigt werden.

<!---HONumber=Oct15_HO3-->