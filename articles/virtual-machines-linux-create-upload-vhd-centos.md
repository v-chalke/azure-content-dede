<properties urlDisplayName="Upload a CentOS-based VHD" pageTitle="Erstellen und Hochladen einer CentOS-basierten Linux-VHD in Azure" metaKeywords="Azure VHD, uploading Linux VHD, CentOS" description="Erfahren Sie, wie Sie eine virtuelle Azure-Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, die ein CentOS-basiertes Linux-Betriebssystem enth&auml;lt." metaCanonical="" services="virtual-machines" documentationCenter="" title="Erstellen und Hochladen einer virtuellen Festplatte, die ein CentOS-basiertes Linux-Betriebssystem enth&auml;lt" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="06/05/2014" ms.author="kathydav, szarkos" />

# Vorbereiten eines CentOS-basierten virtuellen Computers für Azure

-   [Vorbereiten eines virtuellen CentOS 6.x-Computers für Azure][Vorbereiten eines virtuellen CentOS 6.x-Computers für Azure]
-   [Vorbereiten eines virtuellen CentOS 7.0+-Computers für Azure][Vorbereiten eines virtuellen CentOS 7.0+-Computers für Azure]

## Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie bereits ein CentOS-Linux-Betriebssystem (oder eine ähnliche Ableitung) auf einer virtuellen Festplatte installiert haben. Es gibt mehrere Tools zum Erstellen von VHD-Dateien, beispielsweise eine Virtualisierungslösung wie Hyper-V. Anweisungen finden Sie unter [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers][Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers].

**Installationshinweise zu CentOS**

-   Das modernere VHDX-Format wird in Azure noch nicht unterstützt. Sie können den Datenträger mit dem Hyper-V-Manager oder dem convert-vhd-Cmdlet in das VHD-Format konvertieren.

-   Beim Installieren des Linux-Systems wird empfohlen, anstelle von LVM (bei vielen Installationen oftmals voreingestellt) die Standardpartitionen zu verwenden. Dadurch lässt sich vermeiden, dass ein LVM-Namenskonflikt mit geklonten virtuellen Computern auftritt, besonders dann, wenn ein BS-Datenträger zu Fehlerbehebungszwecken mit einem anderen virtuellen Computer verbunden wird. LVM oder [RAID][RAID] können bei Bedarf auf Datenträgern verwendet werden.

-   Aufgrund eines Fehlers in den Linux-Kernelversionen unter 2.6.37 wird NUMA bei größeren VMs nicht unterstützt. Dieses Problem betrifft in erster Linie jene Distributionen, die den Red Hat 2.6.32-Upstream-Kernel verwenden. Bei der manuellen Installation des Azure Linux-Agents (waagent) wird NUMA in der Grub-Konfiguration für den Linux-Kernel automatisch deaktiviert. Weitere Informationen dazu finden Sie in den folgenden Schritten.

-   Konfigurieren Sie keine SWAP-Partition auf einem Betriebssystemdatenträger. Der Linux-Agent kann konfiguriert werden, eine Auslagerungsdatei auf dem temporären Ressourcendatenträger zu erstellen. Weitere Informationen dazu finden Sie in den folgenden Schritten.

-   Alle virtuellen Festplatten müssen eine Größe aufweisen, die ein Vielfaches von 1 MB ist.

## <span id="centos6"></span> </a>CentOS 6.x

1.  Wählen Sie im Hyper-V-Manager den virtuellen Computer aus.

2.  Klicken Sie auf **Verbinden**, um ein Konsolenfenster für den virtuellen Computer zu öffnen.

3.  Deinstallieren Sie den NetworkManager, indem Sie den folgenden Befehl ausführen:

        # sudo rpm -e --nodeps NetworkManager

    **Hinweis:** Wenn das Paket noch nicht installiert ist, schlägt dieser Befehl mit einer Fehlermeldung fehl. Dies entspricht dem erwarteten Verhalten.

4.  Erstellen Sie eine Datei mit dem Namen **network** und folgendem Text im Verzeichnis `/etc/sysconfig/`:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  Erstellen Sie eine Datei mit dem Namen **ifcfg-eth0** und folgendem Text im Verzeichnis `/etc/sysconfig/network-scripts/`:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  Verschieben (oder entfernen) Sie die udev-Regeln, um eine Generierung statischer Regeln für die Ethernet-Schnittstelle zu vermeiden. Diese Regeln können beim Klonen eines virtuellen Computers unter Microsoft Azure oder Hyper-V zu Problemen führen:

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

7.  Stellen Sie sicher, dass der Netzwerkdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

        # sudo chkconfig network on

8.  **Nur CentOS 6.3**: Installieren Sie die Treiber für die Linux-Integrationsdienste.

    **Wichtig: Dieser Schritt gilt nur für CentOS 6.3 und älter.** In CentOS 6.4+ sind die Linux-Integrationsdienste *bereits im Kernel verfügbar*.

    a) Rufen Sie die ISO-Datei über das [Microsoft Download Center][Microsoft Download Center] ab, welche die Treiber für die Linux-Integrationsdienste enthält.

    b) Klicken Sie im Hyper-V-Manager im Fensterbereich **Aktionen** auf **Einstellungen**.

    ![Hyper-V-Einstellungen öffnen][Hyper-V-Einstellungen öffnen]

    c) Klicken Sie im Fensterbereich **Hardware** auf **IDE Controller 1**.

    ![DVD-Laufwerk per Installation von Medien hinzufügen][DVD-Laufwerk per Installation von Medien hinzufügen]

    d) Klicken Sie im Feld **IDE Controller** auf **DVD-Laufwerk** und anschließend auf **Hinzufügen**.

    e) Wählen Sie **Image-Datei**. Navigieren Sie zu **Linux IC v3.2.iso**, und klicken Sie dann auf **Öffnen**.

    f) Klicken Sie auf der Seite **Einstellungen** auf **OK**.

    g) Klicken Sie auf **Verbinden**, um das Fenster für den virtuellen Computer zu öffnen.

    h) Geben Sie im Eingabeaufforderungsfenster die folgenden Befehle ein:

        # sudo mount /dev/cdrom /media
        # sudo /media/install.sh
        # sudo reboot

9.  Installieren Sie das Paket python-pyasn1, indem Sie den folgenden Befehl ausführen:

        # sudo yum install python-pyasn1

10. Wenn Sie die in Azure-Rechenzentren gehosteten OpenLogic-Datenspiegel verwenden möchten, ersetzen Sie die Datei „/etc/yum.repos.d/CentOS-Base.repo“ durch die folgenden Repositorys. Dadurch wird auch das Repository **[openlogic]** hinzugefügt, das Pakete für den Azure Linux-Agent enthält:

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

    **Hinweis:** Im Rest dieses Leitfadens wird davon ausgegangen, dass Sie mindestens das Repository „[openlogic]“ verwenden, das im Folgenden zum Installieren des Azure Linux-Agents verwendet wird.

11. Fügen Sie die folgende Zeile zu /etc/yum.conf hinzu:

        http_caching=packages

    Fügen Sie außerdem bei **Nur CentOS 6.3** die folgende Zeile hinzu:

        exclude=kernel*

12. Deaktivieren Sie das yum-Modul „fastestmirror“, indem Sie die Datei „/etc/yum/pluginconf.d/fastestmirror.conf“ bearbeiten. Geben Sie außerdem unter `[main]` Folgendes ein:

        set enabled=0

13. Führen Sie den folgenden Kommentar aus, um die aktuellen yum-Metadaten zu löschen:

        # yum clean all

14. **Nur CentOS 6.3**; aktualisieren Sie den Kernel mit dem folgenden Befehl:

        # sudo yum --disableexcludes=all install kernel

15. Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dafür „/boot/grub/menu.lst“ in einem Text-Editor. Stellen Sie sicher, dass der Standardkernel die folgenden Parameter enthält:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Dadurch wird NUMA aufgrund eines Fehlers in der von CentOS 6 verwendeten Kernel-Version deaktiviert.

    Neben den oben erwähnten Punkten wird es empfohlen, die folgenden Parameter zu *entfernen*:

        rhgb quiet crashkernel=auto

    Weder der Graphical Boot noch der Quiet Boot sind in einer Cloud-Umgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen.

    Die Option `crashkernel` kann bei Bedarf konfiguriert gelassen werden. Beachten Sie, dass dieser Parameter die Menge an verfügbarem Arbeitsspeicher im virtuellen Computer durch 128 MB oder mehr reduziert, was möglicherweise bei kleineren VM-Größen problematisch sein kann.

16. Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt. Dies ist für gewöhnlich die Standardeinstellung.

17. Installieren Sie den Azure Linux Agent, indem Sie den folgenden Befehl ausführen:

        # sudo yum install WALinuxAgent

    Beachten Sie, dass durch eine Installation des WALinuxAgent-Pakets die NetworkManager- und NetworkManager-gnome-Pakete entfernt werden, sofern sie nicht bereits wie in Schritt 2 beschrieben entfernt wurden.

18. Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein.

    Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein *temporärer* Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Modifizieren Sie nach dem Installieren des Azure Linux Agent (siehe vorheriger Schritt) die folgenden Parameter in /etc/waagent.conf entsprechend:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

19. Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

20. Klicken Sie im Hyper-V-Manager auf **Aktion -\> Herunterfahren**. Ihre Linux-VHD kann nun in Azure hochgeladen werden.

------------------------------------------------------------------------

## <span id="centos7"></span> </a>CentOS 7.0+

**Änderungen in CentOS 7 (und ähnlichen Ableitungen)**

Die Vorbereitung eines virtuellen CentOS 7-Computers für Azure entspricht in etwa der für CentOS 6. Es gibt jedoch einige wichtige Unterschiede:

-   Das NetworkManager-Paket führt nicht mehr zu Konflikten mit dem Azure Linux Agent. Dieses Paket ist standardmäßig installiert, und Sie sollten es nicht entfernen.
-   GRUB2 wird nun als standardmäßiger Bootloader verwendet. Daher hat sich die Prozedur für das Bearbeiten der Kernelparameter geändert (siehe unten).
-   XFS ist nun das Standarddateisystem. Das EXT4-Dateisystem kann bei Bedarf weiterhin verwendet werden.

**Konfigurationsschritte**

1.  Wählen Sie im Hyper-V-Manager den virtuellen Computer aus.

2.  Klicken Sie auf **Verbinden**, um ein Konsolenfenster für den virtuellen Computer zu öffnen.

3.  Erstellen Sie eine Datei mit dem Namen **network** und folgendem Text im Verzeichnis `/etc/sysconfig/`:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.  Erstellen Sie eine Datei mit dem Namen **ifcfg-eth0** und folgendem Text im Verzeichnis `/etc/sysconfig/network-scripts/`:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

5.  Verschieben (oder entfernen) Sie die udev-Regeln, um eine Generierung statischer Regeln für die Ethernet-Schnittstelle zu vermeiden. Diese Regeln können beim Klonen eines virtuellen Computers unter Microsoft Azure oder Hyper-V zu Problemen führen:

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

6.  Stellen Sie sicher, dass der Netzwerkdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

        # sudo chkconfig network on

7.  Installieren Sie das Paket python-pyasn1, indem Sie den folgenden Befehl ausführen:

        # sudo yum install python-pyasn1

8.  Wenn Sie die in Azure-Rechenzentren gehosteten OpenLogic-Datenspiegel verwenden möchten, ersetzen Sie die Datei „/etc/yum.repos.d/CentOS-Base.repo“ durch die folgenden Repositorys. Dadurch wird auch das Repository **[openlogic]** hinzugefügt, das Pakete für den Azure Linux-Agent enthält:

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

    **Hinweis:** Im Rest dieses Leitfadens wird davon ausgegangen, dass Sie mindestens das Repository „[openlogic]“ verwenden, das im Folgenden zum Installieren des Azure Linux-Agents verwendet wird.

9.  Führen Sie den folgenden Befehl aus, um die aktuellen yum-Metadaten zu löschen und um Updates zu installieren.

        # sudo yum clean all
        # sudo yum -y update

10. Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dazu „/etc/default/grub“ in einem Text-Editor, und bearbeiten Sie den Parameter `GRUB_CMDLINE_LINUX`, beispielsweise:

        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"

    Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Neben den oben erwähnten Punkten wird es empfohlen, die folgenden Parameter zu *entfernen*:

        rhgb quiet crashkernel=auto

    Weder der Graphical Boot noch der Quiet Boot sind in einer Cloud-Umgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen.

    Die Option `crashkernel` kann bei Bedarf konfiguriert gelassen werden. Beachten Sie, dass dieser Parameter die Menge an verfügbarem Arbeitsspeicher im virtuellen Computer durch 128 MB oder mehr reduziert, was möglicherweise bei kleineren VM-Größen problematisch sein kann.

11. Sobald Sie die oben beschriebene Bearbeitung von „/etc/default/grub“ abgeschlossen haben, führen Sie den folgenden Befehl zum erneuten Erstellen der Grub-Konfiguration aus:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

12. Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt. Dies ist für gewöhnlich die Standardeinstellung.

13. Installieren Sie den Azure Linux Agent, indem Sie den folgenden Befehl ausführen:

        # sudo yum install WALinuxAgent

14. Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein.

    Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein *temporärer* Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Modifizieren Sie nach dem Installieren des Azure Linux Agent (siehe vorheriger Schritt) die folgenden Parameter in /etc/waagent.conf entsprechend:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15. Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16. Klicken Sie im Hyper-V-Manager auf **Aktion -\> Herunterfahren**. Ihre Linux-VHD kann nun in Azure hochgeladen werden.

  [Vorbereiten eines virtuellen CentOS 6.x-Computers für Azure]: #centos6
  [Vorbereiten eines virtuellen CentOS 7.0+-Computers für Azure]: #centos7
  [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers]: http://technet.microsoft.com/library/hh846766.aspx
  [RAID]: ../virtual-machines-linux-configure-raid
  [Microsoft Download Center]: http://www.microsoft.com/de-de/download/details.aspx?id=41554
  [Hyper-V-Einstellungen öffnen]: ./media/virtual-machines-linux-create-upload-vhd/settings.png
  [DVD-Laufwerk per Installation von Medien hinzufügen]: ./media/virtual-machines-linux-create-upload-vhd/installiso.png