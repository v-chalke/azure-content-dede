<properties
	pageTitle="Leitfaden zur Überwachung und Problembehandlung für den Schutz von VMM- und Hyper-V-Standorten" 
	description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung virtueller Computer auf lokalen Servern zu Azure oder einem sekundären Rechenzentrum. Verwenden Sie diesen Artikel zur Überwachung und Problembehandlung für den Schutz von VMM- und Hyper-V-Standorten." 
	services="site-recovery" 
	documentationCenter="" 
	authors="anbacker" 
	manager="mkjain" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="06/16/2015" 
	ms.author="anbacker"/>
	
# Leitfaden zur Überwachung und Problembehandlung für den Schutz von VMM- und Hyper-V-Standorten

In diesem Leitfaden zur Überwachung und Problembehandlung erhalten Sie Informationen zur Nachverfolgung der Replikationsintegrität und zu den Problembehandlungsverfahren für Azure Site Recovery.

## Grundlegendes zu den Komponenten

### Bereitstellung des VMM-Standorts für die Replikation zwischen lokalen Standorten.

Als Bestandteil der Einrichtung der Notfallwiederherstellung zwischen zwei lokalen Standorten muss Azure Site Recovery Provider heruntergeladen und auf dem VMM-Server installiert werden. Für Azure Site Recovery Provider muss eine Internetverbindung bestehen, um sicherzustellen, dass alle über das Azure-Portal ausgelösten Vorgänge in lokale Vorgänge umgewandelt werden, z. B. Aktivieren des Schutzes, Herunterfahren primärer virtueller Computer bei Failovers usw.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image1.png)

### Bereitstellung des VMM-Standorts für die Replikation zwischen lokalen Standorten und Azure.

Als Bestandteil der Einrichtung der Notfallwiederherstellung zwischen lokalen Standorten und Azure muss Azure Site Recovery Provider heruntergeladen und auf dem VMM-Server installiert werden. Zudem muss der Azure Recovery Services-Agent auf jedem Hyper-V-Host installiert werden.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image2.png)

### Bereitstellung des Hyper-V-Standorts für die Replikation zwischen lokalen Standorten und Azure.

Dies entspricht der Bereitstellung des VMM-Standorts mit der einzigen Ausnahme, dass der Provider und der Agent auf dem Hyper-V-Host installiert werden.

## Überwachen von Konfigurations-, Schutz- und Wiederherstellungsvorgängen

Jeder Vorgang in ASR wird überwacht und auf der Registerkarte "Aufträge" nachverfolgt. Navigieren Sie bei Fehlern bei Konfigurations-, Schutz- oder Wiederherstellungsvorgängen zur Registerkarte "Aufträge", um zu prüfen, ob dort Fehler angezeigt werden.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image3.png)

Wenn in der Ansicht "Aufträge" Fehler angezeigt werden, wählen Sie den entsprechenden Auftrag aus, und klicken Sie auf "Fehlerdetails" für diesen Auftrag.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image4.png)

Anhand der Fehlerdetails können Sie die mögliche Ursache ermitteln und erhalten Empfehlungen zum Beheben des Problems.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image5.png)

Im Fall oben scheint ein anderer aktiver Vorgang die Ursache dafür zu sein, dass bei der Konfiguration des Schutzes Fehler auftreten. Beheben Sie das Problem entsprechend der Empfehlung, und klicken Sie anschließend auf "Neu starten", um den Vorgang erneut zu starten.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image6.png)

Die Option "Neu starten" ist nicht für alle Vorgänge verfügbar. Bei den Vorgängen, für die diese Option nicht verfügbar ist, müssen Sie zurück zum Objekt navigieren und den Vorgang wiederholen. Jeder Auftrag kann während der Ausführung jederzeit über die Schaltfläche "Abbrechen" abgebrochen werden.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image7.png)

## Überwachen der Replikationsintegrität für virtuelle Computer

ASR stellt für alle geschützten Entitäten eine zentrale und Remoteüberwachung über das Azure-Portal bereit. Navigieren Sie zu den geschützten Elementen, und wählen Sie dann VMM-Clouds oder Schutzgruppen aus. Die Registerkarte "VMM-Clouds" steht nur für VMM-basierte Bereitstellungen zur Verfügung. Bei allen anderen Szenarios sind die geschützten Entitäten auf der Registerkarte "Schutzgruppen" aufgeführt.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image8.png)

Wählen Sie dann die geschützte Entität in der entsprechenden Cloud oder Schutzgruppe aus. Nach Auswahl der geschützten Einheit werden alle zulässigen Vorgänge im unteren Bereich angezeigt.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image9.png)

Im Beispiel oben ist die Integrität des virtuellen Computers gefährdet. Sie können auf die Schaltfläche "Fehlerdetails" im unteren Bereich klicken, um den Fehler anzuzeigen. Beheben Sie das Problem basierend auf den Angaben unter "Mögliche Ursachen" und "Empfehlung". In diesem Fall muss der virtuelle Computer neu synchronisiert werden. Klicken Sie dazu im Portal auf die Schaltfläche "Resynchronisieren".

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image10.png)

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image11.png)

Hinweis: Wenn aktive Vorgänge ausgeführt werden oder fehlgeschlagen sind, navigieren Sie entsprechend der Beschreibung weiter oben zur Ansicht "Aufträge", um den Fehler für den spezifischen Auftrag anzuzeigen.

## Problembehandlung bei lokalen Standorten

Stellen Sie eine Verbindung mit der lokalen Hyper-V-Manager-Konsole her, wählen Sie den virtuellen Computer aus, und prüfen Sie die Replikationsintegrität.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image12.png)

In diesem Fall wird die *Replikationsintegrität* als "Kritisch" angegeben. Klicken Sie auf *Replikationsstatus anzeigen*, um Details anzuzeigen.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image13.png)

#### Ereignisanzeige

| Szenarios | Ereignisquellen |
|-------------------------	|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| Schutz des VMM-Standorts | VMM-Server <ul><li> **Applications and Service Logs/Microsoft/VirtualMachineManager/Server/Admin** </li></ul> Hyper-V-Host <ul><li> **Applications and Service Logs/MicrosoftAzureRecoveryServices/Replication** (für Azure als Ziel)</li><li> **Applications and Service Logs/Microsoft/Windows/Hyper-V-VMMS/Admin** </li></ul> |
| Schutz des Hyper-V-Standorts | <ul><li> **Applications and Service Logs/MicrosoftAzureRecoveryServices/Replication** </li><li> **Applications and Service Logs/Microsoft/Azure Site Recovery/Provider/Operational** </li><li> **Applications and Service Logs/Microsoft/Windows/Hyper-V-VMMS/Admin** </li><ul>|

#### Protokollierungsoptionen für die Hyper-V-Replikation

Alle Ereignisse für Hyper-V Replica werden im Protokoll "Hyper-V-VMMS\\Admin" unter **Applications and Services Logs\\Microsoft\\Windows** protokolliert. Darüber hinaus kann ein analytisches Protokoll für Hyper-V-VMMS aktiviert werden. Um dieses Protokoll zu aktivieren, müssen Sie zunächst die analytischen und Debugprotokolle in der Ereignisanzeige einblenden. Öffnen Sie die Ereignisanzeige, und klicken Sie im Menü **Ansicht** auf **Analytische und Debugprotokolle einblenden**.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image14.png)

Unter "Hyper-V-VMMS" wird ein analytisches Protokoll angezeigt.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image15.png)

Klicken Sie im Bereich **Aktionen** auf **Protokoll aktivieren**. Nach der Aktivierung wird das Protokoll in **Systemmonitor** als Ereignisablaufverfolgungssitzung unter **Datensammlersätze** angezeigt.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image16.png)

Beenden Sie zum Anzeigen der gesammelten Informationen zunächst die Ablaufverfolgungssitzung, indem Sie das Protokoll deaktivieren. Speichern Sie dann das Protokoll, und öffnen Sie es in der Ereignisanzeige, oder konvertieren Sie es mithilfe anderer Tools in das gewünschte Format.


## Grundlegendes zum Lebenszyklus virtueller Computer

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image17.png)


## Microsoft-Support

### Protokollsammlung

Im Hinblick auf den Schutz von VMM-Standorten finden Sie Informationen zum Sammeln der erforderlichen Protokolle unter [ASR Log Collection using Support Diagnostics Platform (SDP) Tool](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx) (in englischer Sprache).

Laden Sie für den Schutz von Hyper-V-Zweigstellen und SMB-Standorten dieses [Tool](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) herunter, und führen Sie es auf dem Hyper-V-Host aus, um die Protokolle zu sammeln.

Informationen zum Sammeln der erforderlichen Protokolle für physische oder VMware-Szenarios finden Sie unter [Azure Site Recovery Log Collection for VMware and Physical site protection](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) (in englischer Sprache).

Das SDP-Tool sammelt die Protokolldatei lokal. Diese finden Sie auch in einem zufällig benannten Unterordner unter **%LocalAppData%\\ElevatedDiagnostics**.

### Öffnen eines Supporttickets

Um ein Supportticket für ASR zu öffnen, wenden Sie sich an den Azure-Support unter der URL <http://aka.ms/getazuresupport>.

## KB-Artikel

-   [How to preserve the drive letter for protected virtual machines (in englischer Sprache)
    > http://support.microsoft.com/kb/3031135

-   [How to troubleshoot Azure Recovery
    > Services (in englischer Sprache)] (http://support.microsoft.com/kb/3005185)

-   [How to Enable Debug Logging for the Azure Site Recovery in Hyper-V
    > Site Protection (in englischer Sprache)] (http://support.microsoft.com/kb/3033922)

-   [ASR: "The cluster resource could not be found" error when you try
    > to enable protection for a virtual machine (in englischer Sprache)] (http://support.microsoft.com/kb/3010979)
    
-   [Understand & Troubleshoot Hyper-V Replica
    > Guide (in englischer Sprache)] (http://www.microsoft.com/de-de/download/details.aspx?id=29016)

<!---HONumber=62-->