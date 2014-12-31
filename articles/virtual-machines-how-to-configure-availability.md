<properties title="Gewusst wie: Konfigurieren einer Verf&uuml;gbarkeitsgruppe f&uuml;r virtuelle Computer" pageTitle="Gewusst wie: Konfigurieren einer Verf&uuml;gbarkeitsgruppe f&uuml;r virtuelle Computer" description="Enth&auml;lt die Schritte zum Konfigurieren einer Verf&uuml;gbarkeitsgruppe f&uuml;r einen virtuellen Computer in Azure." metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="kathydav" manager="timlt" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="09/30/2014" ms.author="kathydav" />

# Gewusst wie: Konfigurieren einer Verfügbarkeitsgruppe für virtuelle Computer

Eine Verfügbarkeitsgruppe hilft, dass Ihre virtuellen Computer während eines Ausfalls wie während einer Wartung verfügbar bleiben. Das Platzieren von mindestens zwei ähnlich konfigurierten virtuellen Computern in einer Verfügbarkeitsgruppe erstellt die Redundanz, die zum Wahren der Verfügbarkeit der Anwendungen oder Dienste erforderlich ist, die auf Ihrem virtuellen Computer ausgeführt werden. Weitere Details finden Sie unter [Verwalten der Verfügbarkeit virtueller Computer][Verwalten der Verfügbarkeit virtueller Computer].

Sie sollten eine Kombination aus Verfügbarkeitssätzen und Endpunkten mit Lastenausgleich verwenden, um sicherzustellen, dass Ihre Anwendung stets verfügbar ist und effizient ausgeführt wird. Details über Endpunkte mit Lastenausgleich finden Sie unter [Lastenausgleich für Azure-Infrastrukturdienste][Lastenausgleich für Azure-Infrastrukturdienste].

Mithilfe einer der beiden folgenden Optionen können Sie einen virtuellen Computer in eine Verfügbarkeitsgruppe versetzen:

-   [Option 1: Erstellen Sie gleichzeitig einen virtuellen Computer und eine Verfügbarkeitsgruppe][Option 1: Erstellen Sie gleichzeitig einen virtuellen Computer und eine Verfügbarkeitsgruppe]. Fügen Sie der Gruppe anschließend die neuen virtuellen Computer hinzu, wenn Sie diese virtuellen Computer erstellen.
-   [Option 2: Fügen Sie einer Verfügbarkeitsgruppe einen vorhandenen virtuellen Computer hinzu][Option 2: Fügen Sie einer Verfügbarkeitsgruppe einen vorhandenen virtuellen Computer hinzu].

> [WACOM.NOTE] Virtuelle Computer, die Sie in dieselbe Verfügbarkeitsgruppe versetzen möchten, müssen zum selben Cloud-Dienst gehören.

## <span id="createset"></span> </a>Option 1: Erstellen Sie gleichzeitig einen virtuellen Computer und eine Verfügbarkeitsgruppe.

Dafür können Sie entweder das Verwaltungsportal oder die Azure PowerShell-Cmdlets verwenden.

So verwenden Sie das Verwaltungsportal

1.  Melden Sie sich auf dem Azure-[Verwaltungsportal][Verwaltungsportal] an, falls noch nicht geschehen.

2.  Klicken Sie in der Befehlsleiste auf **Neu**.

3.  Klicken Sie auf **Virtueller Computer** und dann auf **From Gallery**.

4.  Verwenden Sie die ersten beiden Bildschirme zum Auswählen eines Images, eines Benutzernamens, Kennworts usw. Weitere Details finden Sie unter [Erstellen eines virtuellen Windows Server-Computers][Erstellen eines virtuellen Windows Server-Computers].

5.  Auf dem dritten Bildschirm können Sie die Ressourcen für Netzwerk, Speicher und Verfügbarkeit konfigurieren. Gehen Sie wie folgt vor:

    1.  Treffen Sie die entsprechende Auswahl für den Cloud-Dienst. Lassen Sie die Option **Einen neuen Cloud-Dienst erstellen** ausgewählt (es sei denn, Sie fügen einem vorhandenen VM-Cloud-Dienst diesen neuen virtuellen Computer hinzu). Geben Sie dann unter **DNS-Name des Cloud-Diensts** einen Namen ein. Der DNS-Name wird ein Teil des URI, der zum Kontaktieren des virtuellen Computers verwendet wird. Der Cloud-Dienst fungiert als eine Kommunikations- und Isolationsgruppe. Alle virtuellen Computer im selben Cloud-Dienst können miteinander kommunizieren, für den Lastenausgleich eingerichtet werden und in derselben Verfügbarkeitsgruppe platziert werden.

    2.  Geben Sie unter **Region/Affinitätsgruppe/Virtuelles Netzwerk** ein virtuelles Netzwerk an, wenn Sie eins einsetzen möchten. **Wichtig**: Wenn der virtuelle Computer ein virtuelles Netzwerk verwenden soll, müssen Sie den virtuellen Computer mit dem virtuellen Netzwerk verknüpfen, wenn Sie den virtuellen Computer erstellen. Sie können den virtuellen Computer nicht mit einem virtuellen Netzwerk verknüpfen, nachdem Sie den virtuellen Computer erstellt haben. Weitere Informationen finden Sie unter [Überblick über Azure Virtual Network][Überblick über Azure Virtual Network].

    3.  Erstellen Sie die Verfügbarkeitsgruppe. Belassen Sie die Auswahl unter **Verfügbarkeitsgruppe** bei **Verfügbarkeitsgruppe erstellen**. Geben Sie dann einen Namen für die Gruppe ein.
    4.  Erstellen Sie die Standardendpunkte, und fügen Sie bei Bedarf weitere hinzu. Sie können Endpunkt auch später hinzufügen.

    ![Erstellen einer Verfügbarkeitsgruppe für einen neuen virtuellen Computer][Erstellen einer Verfügbarkeitsgruppe für einen neuen virtuellen Computer]

6.  Wählen Sie auf dem vierten Bildschirm die zu installierenden Erweiterungen aus. Erweiterungen bieten Features, die die Verwaltung des virtuellen Computers vereinfachen, beispielsweise das Ausführen von Antischadsoftware oder das Zurücksetzen von Kennwörtern. Details finden Sie im Thema zum [Azure VM-Agent und VM-Erweiterungen][Azure VM-Agent und VM-Erweiterungen].

7.  Klicken Sie auf den Pfeil, um den virtuellen Computer und den Verfügbarkeitssatz zu erstellen.

    Wenn Sie im Dashboard des neuen virtuellen Computers auf **Konfigurieren** klicken, sehen Sie, dass der virtuelle Computer zur neuen Verfügbarkeitsgruppe gehört.

So verwenden Sie Azure-Cmdlets

1.  Öffnen Sie eine Azure PowerShell-Sitzung, und führen Sie Befehle aus, die in etwa den im Folgenden gezeigten entsprechen. Beachten Sie, dass in diesen Beispielen davon ausgegangen wird, dass Sie einen virtuellen Computer, den Cloud-Dienst und die Verfügbarkeitsgruppe erstellen.

2.  Rufen Sie den Namen des Images ab, das Sie verwenden möchten, um den virtuellen Computer zu erstellen, und speichern Sie ihn in einer Variablen. Der Befehl verwendet die Indexnummer und die Eigenschaft „ImageName“ des Imageobjekts:

    `C:\PS> $image = (Get-AzureVMImage)[4].ImageName`

    > [WACOM.NOTE] Führen Sie `Get-AzureVMImage` ohne Parameter aus, um eine Liste sämtlicher Images aufzurufen, die für Ihr Abonnement gelten.

3.  Geben Sie die Konfiguration für den neuen virtuellen Computer an, und verwenden Sie dann die Pipeline zum Weitergeben eines Konfigurationsobjekts zum Cmdlet, das den virtuellen Computer erstellt. Ersetzen Sie die Platzhalter wie \<VmName\> und \<VmSize\> durch Ihre eigenen Werte.

    `C:\PS> New-AzureVMConfig -Name "<VmName>" -InstanceSize <VmSize> -AvailabilitySetName "<SetName>" -ImageName $image | Add-AzureProvisioningConfig -Windows -AdminUsername "<UserName>" -Password "<MyPassword>" | Add-AzureDataDisk -CreateNew -DiskSizeInGB 50 -DiskLabel 'datadisk1' -LUN 0 | New-AzureVM –ServiceName "<MySvc1>"`

## <span id="addmachine"></span> </a>Option 2: Fügen Sie einer Verfügbarkeitsgruppe einen vorhandenen virtuellen Computer hinzu.

Im Verwaltungsportal können Sie einer vorhandenen Verfügbarkeitsgruppe vorhandene virtuelle Computer hinzufügen oder einen neuen für sie erstellen. (Beachten Sie, die virtuellen Computer müssen zum selben Cloud-Dienst gehören.) Die Schritte sind beinah identisch. In der Azure PowerShell können Sie einer vorhandenen Verfügbarkeitsgruppe den virtuellen Computer hinzufügen.

1.  Melden Sie sich auf dem Azure-[Verwaltungsportal][Verwaltungsportal] an, falls noch nicht geschehen..

2.  Klicken Sie auf der Navigationsleiste auf **Virtuelle Computer**.

3.  Wählen Sie in der Liste der virtuellen Computer den virtuellen Computer aus, den Sie zur Gruppe hinzufügen möchten. Klicken Sie auf die Zeile des virtuellen Computers, um sein Dashboard zu öffnen.

4.  Klicken Sie auf den Registerkarten unter dem Namen des virtuellen Computers auf **Konfigurieren**.

5.  Suchen Sie im Abschnitt „Einstellungen“ nach **Verfügbarkeitsgruppe**. Führen Sie einen der folgenden Schritte aus:

    A. Wählen Sie **Verfügbarkeitsgruppe erstellen** aus, und geben Sie einen Namen für die Gruppe ein.

    B. Wählen Sie **Verfügbarkeitsgruppe auswählen** aus, und wählen Sie einen Namen aus der Liste aus.

    ![Erstellen einer Verfügbarkeitsgruppe für einen vorhandenen virtuellen Computer][Erstellen einer Verfügbarkeitsgruppe für einen vorhandenen virtuellen Computer]

6.  Klicken Sie auf **Speichern**.

So verwenden Sie Azure-Cmdlets

Öffnen Sie eine Azure PowerShell-Sitzung, und führen Sie den folgenden Befehl aus. Ersetzen Sie die Platzhalter wie \<VmCloudServiceName\> und \<VmName\> durch Ihre eigenen Werte.

    C:\PS> Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<MyAvSet>" | Update-AzureVM

> [WACOM.NOTE] Der virtuelle Computer muss möglicherweise neu gestartet werden, um das Hinzufügen zur Verfügbarkeitsgruppe abzuschließen.

## Zusätzliche Ressourcen

[Starten, Beenden und Löschen virtueller Computer][Starten, Beenden und Löschen virtueller Computer]

<!-- LINKS -->

  [Verwalten der Verfügbarkeit virtueller Computer]: ../virtual-machines-manage-availability
  [Lastenausgleich für Azure-Infrastrukturdienste]: ../virtual-machines-load-balance
  [Option 1: Erstellen Sie gleichzeitig einen virtuellen Computer und eine Verfügbarkeitsgruppe]: #createset
  [Option 2: Fügen Sie einer Verfügbarkeitsgruppe einen vorhandenen virtuellen Computer hinzu]: #addmachine
  [Verwaltungsportal]: http://manage.windowsazure.com
  [Erstellen eines virtuellen Windows Server-Computers]: ../virtual-machines-windows-tutorial
  [Überblick über Azure Virtual Network]: http://go.microsoft.com/fwlink/p/?linkid=294063
  [Erstellen einer Verfügbarkeitsgruppe für einen neuen virtuellen Computer]: ./media/virtual-machines-how-to-configure-availability/VMavailabilityset.png
  [Azure VM-Agent und VM-Erweiterungen]: http://go.microsoft.com/fwlink/p/?LinkId=XXX
  [Erstellen einer Verfügbarkeitsgruppe für einen vorhandenen virtuellen Computer]: ./media/virtual-machines-how-to-configure-availability/VMavailabilityExistingVM.png
  [Starten, Beenden und Löschen virtueller Computer]: http://msdn.microsoft.com/de-de/library/azure/dn763935.aspx