<properties 
 pageTitle="Informationen zum Agent und zu Erweiterungen für virtuelle Computer | Microsoft Azure" 
 description="Bietet eine Übersicht über den Agent und die Erweiterungen sowie die Installation des Agents." 
 services="virtual-machines" 
 documentationCenter="" 
 authors="squillace" 
 manager="timlt" 
 editor=""/>
<tags 
ms.service="virtual-machines" 
 ms.devlang="na" 
 ms.topic="article" 
 ms.tgt_pltfrm="vm-multiple" 
 ms.workload="infrastructure-services"
 ms.date="03/10/2015" 
 ms.author="kathydav"/>
#Informationen zum Agent und zu Erweiterungen für virtuelle Computer
Der Azure-Agent für virtuelle Computer \(VM-Agent\) dient zum Installieren, Konfigurieren, Verwalten und Ausführen von Azure-Erweiterungen für virtuelle Computer \(VM-Erweiterungen\). VM-Erweiterungen bieten dynamische Features, die von Microsoft und Drittanbietern bereitgestellt werden. Der Agent und die Erweiterungen werden in erster Linie über das Verwaltungsportal hinzugefügt, jedoch können Sie auch die [PowerShell](../install-configure-powershell.md)-Cmdlets oder die [plattformübergreifende Befehlszeilenschnittstelle](virtual-machines-command-line-tools.md) zum Hinzufügen und Konfigurieren verwenden, wenn Sie einen virtuellen Computer erstellen oder vorhandene virtuelle Computer nutzen. VM-Erweiterungen umfassen Unterstützung für das [Remotedebuggen in Visual Studio](https://msdn.microsoft.com/library/y7f5zaaa.aspx), [System Center 2012](http://social.technet.microsoft.com/wiki/contents/articles/18274.system-center-2012-r2-virtual-machine-role-authoring-guide-resource-extension-package.aspx), [Microsoft Azure-Diagnose](http://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/) und [Docker](virtual-machines-docker-vm-extension.md) – um nur einige zu nennen.

VM-Erweiterungen helfen Ihnen bei folgenden Aufgaben:

-   Ändern von Sicherheits- und Identitätsfeatures, z. B. Zurücksetzen von Kontowerten und Verwenden von Antischadsoftware 
-   Starten, Beenden oder Konfigurieren der Überwachung und Diagnose 
-   Zurücksetzen oder Installieren von Konnektivitätsfeatures, z. B. RDP und SSH
-   Diagnostizieren, Überwachen und Verwalten Ihrer virtuellen Computer

Darüber hinaus gibt es viele andere Features. Neue VM-Erweiterungsfeatures werden regelmäßig veröffentlicht. Dieser Artikel beschreibt die Azure-VM-Agents für Windows und Linux und deren Unterstützung der Funktionalität von VM-Erweiterungen. Eine Liste der VM-Erweiterungen nach Featurekategorie finden Sie unter [Azure-VM-Erweiterungen und Features](https://msdn.microsoft.com/library/dn606311.aspx).

##Azure-VM-Agents für Windows und Linux

Der Azure-Agent für virtuelle Computer \(VM-Agent\) ist ein sicherer, einfacher Prozess, der VM-Erweiterungen auf Instanzen von virtuellen Azure-Computern im Imagekatalog und auf benutzerdefinierten VM-Instanzen installiert, konfiguriert und davon entfernt, wenn auf ihnen der VM-Agent installiert ist. Der VM-Agent dient als sicherer lokaler Steuerungsdienst für Ihre Azure-VM. Die Erweiterungen, die der Agent lädt, bieten bestimmte Features, um Ihre Produktivität bei der Nutzung der Instanz zu erhöhen.

Es gibt zwei Azure-VM-Agents: einen für virtuelle Windows-Computer und einen für virtuelle Linux-Computer. Standardmäßig wird der VM-Agent automatisch installiert, wenn Sie über den Imagekatalog einen virtuellen Computer erstellen, aber Sie können den VM-Agent auch installieren, nachdem die Instanz erstellt wurde. Darüber hinaus ist die Installation auf einem benutzerdefinierten VM-Image möglich, das Sie dann selbst hochladen.

>[AZURE.IMPORTANT]Diese VM-Agents sind sehr einfache Dienste, die eine sichere Verwaltung von Instanzen virtueller Computer ermöglichen. Es gibt möglicherweise Fälle, in denen Sie den VM-Agent nicht benötigen. Wenn dies der Fall ist, achten Sie darauf, dass Sie virtuelle Computer erstellen, auf denen der VM-Agent nicht installiert wird. Obwohl der VM-Agent physisch entfernt werden kann, ist das Verhalten von VM-Erweiterungen auf der Instanz nicht definiert. Daher wird das Entfernen des VM-Agents nach der Installation derzeit nicht unterstützt.

Der VM-Agent wird in den folgenden Situationen aktiviert:

-   Wenn Sie eine Instanz eines virtuellen Computers mithilfe der Methode **Schnellerfassung** im Verwaltungsportal erstellen oder die Methode **Benutzerdefiniert erstellen** im Verwaltungsportal verwenden und sicherstellen, dass das Kontrollkästchen **VM-Agent installieren** aktiviert ist \(wie in der folgenden Abbildung gezeigt\). Weitere Informationen finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers](virtual-machines-create-custom.md).

    ![Kontrollkästchen "VM-Agent"](./media/virtual-machines-extensions-agent-about/IC719409.png "Kontrollkästchen "VM-Agent"")

-   Wenn Sie eine Instanz eines virtuellen Computers mit dem Cmdlet [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) oder [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx) erstellen. Sie können einen virtuellen Computer erstellen, ohne dass der VM-Agent installiert wird, indem Sie den Parameter **–DisableGuestAgent** zum Cmdlet [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx) hinzufügen.

-   Durch das manuelle Herunterladen und Installieren des VM-Agents \(Windows- oder Linux-Version\) auf eine vorhandene VM-Instanz und das anschließende Festlegen des **ProvisionGuestAgent**-Werts auf **true** mithilfe der PowerShell oder eines REST-Aufrufs. \(Wenn Sie diesen Wert nach der manuellen Installation des VM-Agents nicht festlegen, wird das Hinzufügen des VM-Agents nicht richtig erkannt.\) Im folgenden Codebeispiel wird veranschaulicht, wie dies mithilfe von PowerShell ausgeführt wird, wobei die Argumente `$svc` und `$name` bereits ermittelt wurden.

        $vm = Get-AzureVM –serviceName $svc –Name $name
        $vm.VM.ProvisionGuestAgent = $TRUE
        Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc

-   Durch das Erstellen eines VM-Images, auf dem der VM-Agent installiert ist, bevor es in Azure hochgeladen wird. Laden Sie für einen virtuellen Windows-Computer die [MSI-Datei des Windows-VM-Agents](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) herunter, und installieren Sie den VM-Agent. Für einen virtuellen Linux-Computer installieren Sie ihn über <https://github.com/Azure/WALinuxAgent>. Weitere Informationen zum Installieren des VM-Agents unter Linux finden Sie im [Benutzerhandbuch für den Azure Linux-VM-Agent](virtual-machines-linux-agent-user-guide.md).

>[AZURE.NOTE]In PaaS heißt der VM-Agent **GuestAgent** und ist auf virtuellen Computern mit der Web- und Workerrolle immer verfügbar. \(Weitere Informationen finden Sie unter [Azure Role Architecture](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) \(in englischer Sprache\).\) Der VM-Agent für Rollen-VMs kann jetzt den Clouddienst-VMs auf die gleiche Weise Erweiterungen hinzufügen, wie den dauerhaften virtuellen Computern. Der größte Unterschied zwischen VM-Erweiterungen auf Rollen-VMs und persistenten virtuellen Computern ist, dass bei Rollen-VMs Erweiterungen zuerst dem Clouddienst und dann zu Bereitstellungen innerhalb des Clouddiensts hinzugefügt werden.

>Verwenden Sie das Cmdlet [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) zum Auflisten aller verfügbaren Rollen-VM-Erweiterungen.

##Suchen, Hinzufügen, Aktualisieren und Entfernen von VM-Erweiterungen  

Weitere Informationen zu diesen Aufgaben finden Sie unter [Hinzufügen, Suchen, Aktualisieren und Entfernen von Azure-VM-Erweiterungen](https://msdn.microsoft.com/library/dn850373.aspx).

<!---HONumber=August15_HO7-->