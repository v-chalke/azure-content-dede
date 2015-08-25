<properties 
 pageTitle="Verwalten von Erweiterungen für virtuelle Computer | Microsoft Azure" 
 description="Beschreibt das Hinzufügen, Suchen, Aktualisieren und Entfernen von Erweiterungen." 
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
 ms.author="rasquill"/>
#Verwalten von Erweiterungen für virtuelle Computer
Beschreibt, wie VM-Erweiterungen mit virtuellen Windows- oder Linux-Computern in Azure gesucht, hinzugefügt, geändert oder entfernt werden.

##Verwenden von VM-Erweiterungen

Azure-VM-Erweiterungen implementieren Verhaltensweisen oder Features, mit denen andere Programme auf Azure-VMs verwendet werden können \(z. B. ermöglicht die Erweiterung **WebDeployForVSDevTest** Visual Studio für Web Deploy-Lösungen auf Ihrer Azure-VM\). Sie bieten aber auch die Möglichkeit zur Interaktion mit dem virtuellen Computer, um ein anderes Verhalten zu unterstützen \(z. B. können Sie mit den VM-Zugriffserweiterungen von PowerShell, der plattformübergreifenden Befehlszeilenschnittstelle und der REST-Clients Remotezugriffswerte auf Ihrer Azure-VM zurücksetzen oder ändern\).

>[AZURE.IMPORTANT]Eine vollständige Liste der Erweiterungen nach unterstützten Features finden Sie unter [Azure-VM-Erweiterungen und Features](https://msdn.microsoft.com/library/dn606311.aspx). Da jede VM-Erweiterung ein bestimmtes Feature unterstützt, hängt es von der Erweiterung ab, was Sie mit der Erweiterung genau machen können. Bevor Sie Ihren virtuellen Computer ändern, sollten Sie daher die Dokumentation für die VM-Erweiterung lesen, die Sie verwenden möchten. Das Entfernen einiger VM-Erweiterungen wird nicht unterstützt. Andere weisen Eigenschaften auf, die festgelegt werden können und das VM-Verhalten grundlegend ändern.

Die häufigsten Aufgaben sind:

1.  Suchen nach verfügbaren Erweiterungen
    
2.  Aktualisieren von geladenen Erweiterungen

3.  Hinzufügen von Erweiterungen

4.  Entfernen von Erweiterungen

##Suchen nach verfügbaren Erweiterungen

Azure-VM-Erweiterungen: Eine vollständige Liste der Erweiterungen nach unterstützten Features finden Sie unter [Azure-VM-Erweiterungen und Features](https://msdn.microsoft.com/library/dn606311.aspx). Sie können die Erweiterung und die erweiterten Informationen wie folgt finden:

-   PowerShell
-   Plattformübergreifende Azure-Schnittstelle \(xplat-cli\)
-   Dienstverwaltungs-REST-API

Verwenden Sie [Azure PowerShell](https://msdn.microsoft.com/library/azure/dn495240.aspx)-Cmdlets oder die [REST-APIs der Dienstverwaltung](https://msdn.microsoft.com/library/ee460799.aspx), um Informationen zu verfügbaren Erweiterungen zu finden.

###Azure PowerShell

Einige Erweiterungen verfügen über spezielle PowerShell-Cmdlets, die ihre Konfiguration über PowerShell vereinfachen können. Die folgenden Cmdlets funktionieren jedoch für alle VM-Erweiterungen.

Sie können die folgenden Cmdlets zum Abrufen von Informationen zu den verfügbaren Erweiterungen verwenden:

-   Für Instanzen von Webrollen oder Workerrollen können Sie das Cmdlet [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) verwenden.
-   Für Instanzen von virtuellen Computern können Sie das Cmdlet [Get-AzureVMAvailableExtension](https://msdn.microsoft.com/library/azure/dn722480.aspx) verwenden.

     Im folgenden Codebeispiel wird z. B. veranschaulicht, wie Informationen für die Erweiterung **IaaSDiagnostics** mithilfe von PowerShell aufgelistet werden.
    
        PS C:\PowerShell> Get-AzureVMAvailableExtension -ExtensionName IaaSDiagnostics
        VERBOSE: 5:09:01 PM - Begin Operation: Get-AzureVMAvailableExtension
        VERBOSE: 5:09:06 PM - Completed Operation: Get-AzureVMAvailableExtension

        Publisher                   : Microsoft.Azure.Diagnostics
        ExtensionName               : IaaSDiagnostics
        Version                     : 1.2
        Label                       : Microsoft Monitoring Agent Diagnostics
        Description                 : Microsoft Monitoring Agent Extension
        PublicConfigurationSchema   :
        PrivateConfigurationSchema  :
        IsInternalExtension         : False
        SampleConfig                :
        ReplicationCompleted        : True
        Eula                        :
        PrivacyUri                  :
        HomepageUri                 :
        IsJsonExtension             : True
        DisallowMajorVersionUpgrade : False
        SupportedOS                 :
        PublishedDate               :
        CompanyName                 :


###Azure-Befehlszeilenschnittstelle \(xplat-cli\)

Einige Erweiterungen weisen Befehle der plattformübergreifenden Befehlszeilenschnittstelle \(xplat-cli\) auf, die spezifisch für sie sind \(die Docker-VM-Erweiterung ist ein Beispiel dafür\) und die möglicherweise die Konfiguration vereinfachen. Die folgenden Befehle funktionieren jedoch für alle VM-Erweiterungen.

Sie können den Befehl **azure vm extension list** zum Abrufen von Informationen zu verfügbaren Erweiterungen verwenden und mit der **–-json**-Option alle verfügbaren Informationen über eine oder mehrere Erweiterungen anzeigen. Wenn Sie keinen Erweiterungsnamen verwenden, gibt der Befehl eine JSON-Beschreibung aller verfügbaren Erweiterungen zurück.

Beispiel: Das folgende Codebeispiel veranschaulicht das Auflisten der Informationen für die Erweiterung **IaaSDiagnostics** mit dem Befehl der plattformübergreifenden Befehlszeilenschnittstelle **azure vm extension list** und verwendet die **–-json**-Option, um vollständige Informationen zurückzugeben.


    $ azure vm extension list -n IaaSDiagnostics --json
    [
      {
        "publisher": "Microsoft.Azure.Diagnostics",
        "name": "IaaSDiagnostics",
        "version": "1.2",
        "label": "Microsoft Monitoring Agent Diagnostics",
        "description": "Microsoft Monitoring Agent Extension",
        "replicationCompleted": true,
        "isJsonExtension": true
      }
    ]



###Dienstverwaltungs-REST-APIs

Sie können die folgenden REST-APIs zum Abrufen von Informationen zu den verfügbaren Erweiterungen verwenden:

-   Für Instanzen von Webrollen oder Workerrollen können Sie den Vorgang [List Available Extensions](https://msdn.microsoft.com/library/dn169559.aspx) verwenden. Zum Auflisten der Versionen der verfügbaren Erweiterungen können Sie den Vorgang [List Extension Versions](https://msdn.microsoft.com/library/dn495437.aspx) verwenden.

-   Für Instanzen von virtuellen Computern können Sie den Vorgang [List Resource Extensions](https://msdn.microsoft.com/library/dn495441.aspx) verwenden. Zum Auflisten der Versionen der verfügbaren Erweiterungen können Sie den Vorgang [List Resource Extension Versions](https://msdn.microsoft.com/library/dn495440.aspx) verwenden.

##Hinzufügen, Aktualisieren oder Deaktivieren von Erweiterungen

Erweiterungen können hinzugefügt werden, wenn eine Instanz erstellt wird. Sie können aber auch einer aktiven Instanz hinzugefügt werden. Erweiterungen können aktualisiert, deaktiviert oder entfernt werden. Sie können diese Aktionen mithilfe von Azure PowerShell-Cmdlets oder mithilfe von REST-API-Vorgängen der Dienstverwaltung ausführen. Parameter sind zum Installieren und Einrichten einiger Erweiterungen erforderlich. Öffentliche und private Parameter werden für Erweiterungen unterstützt.


###Azure PowerShell

Die Verwendung von Azure PowerShell-Cmdlets ist die einfachste Möglichkeit zum Hinzufügen und Aktualisieren von Erweiterungen. Wenn Sie die Erweiterungs-Cmdlets verwenden, wird der größte Teil der Konfiguration der Erweiterung für Sie erledigt. Von Zeit zu Zeit müssen Sie möglicherweise eine Erweiterung programmgesteuert hinzufügen. In diesem Fall müssen Sie die Konfiguration der Erweiterung angeben.

Sie können die folgenden Cmdlets verwenden, um herauszufinden, ob für eine Erweiterung die Konfiguration öffentlicher und privater Parameter erforderlich ist:

-   Für Instanzen von Webrollen oder Workerrollen können Sie das Cmdlet **Get-AzureServiceAvailableExtension** verwenden.

-   Für Instanzen von virtuellen Computern können Sie das Cmdlet **Get-AzureVMAvailableExtension** verwenden.

###Dienstverwaltungs-REST-APIs

Wenn Sie eine Liste der verfügbaren Erweiterungen mithilfe der REST-APIs abrufen, erhalten Sie Informationen zur erforderlichen Konfiguration der Erweiterung. Die zurückgegebenen Informationen enthalten möglicherweise Parameterinformationen, die durch ein öffentliches Schema und ein privates Schema dargestellt werden. Öffentliche Parameterwerte werden in Abfragen zu den Instanzen zurückgegeben. Private Parameterwerte werden nicht zurückgegeben.

Sie können die folgenden REST-APIs verwenden, um herauszufinden, ob für eine Erweiterung die Konfiguration öffentlicher und privater Parameter erforderlich ist:

-   Für Instanzen von Webrollen oder Workerrollen enthalten die Elemente **PublicConfigurationSchema** und **PrivateConfigurationSchema** die Informationen in der Antwort des Vorgangs [List Available Extensions](https://msdn.microsoft.com/library/dn169559.aspx).

-   Für Instanzen von virtuellen Computern enthalten die Elemente **PublicConfigurationSchema** und **PrivateConfigurationSchema** die Informationen in der Antwort des Vorgangs [List Resource Extensions](https://msdn.microsoft.com/library/dn495441.aspx).

>[AZURE.NOTE]Erweiterungen können auch Konfigurationen verwenden, die mit JSON definiert sind. Wenn diese Typen von Erweiterungen verwendet werden, wird nur das Element **SampleConfig** verwendet.

<!---HONumber=August15_HO7-->