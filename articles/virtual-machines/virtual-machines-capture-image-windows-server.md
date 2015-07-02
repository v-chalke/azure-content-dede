<properties 
	pageTitle="Erfassen eines Images des virtuellen Computers unter Windows Server" 
	description="Erfahren Sie, wie Sie ein Image eines virtuellen Azure-Computers unter Windows Server 2008 R2 erfassen können." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/13/2015" 
	ms.author="kathydav"/>

#Erfassen eines virtuellen Windows-Computers, um ihn als Vorlage zu verwenden#

Dieser Artikel erläutert, wie Sie einen virtuellen Azure-Computer erfassen, auf dem Windows läuft, um ihn wie eine Vorlage zum Erstellen anderer virtueller Computer zu verwenden. Diese Vorlage umfasst den Betriebssystemdatenträger und alle an den virtuellen Computer angefügten Datenträger. Da die Vorlage keine Netzwerkkonfiguration enthält, müssen Sie die Konfiguration später vornehmen, wenn Sie die anderen virtuellen Computer erstellen, die auf dieser Vorlage basieren.

Azure behandelt diese Vorlage als lokales Image und speichert es unter**Eigene Images**. Hier werden sämtliche Images abgelegt, die Sie hochladen. Weitere Informationen zu Images finden Sie unter [Über Images virtueller Computer in Azure][].

##Voraussetzungen##

Diese Schritte setzen voraus, dass Sie bereits einen virtuellen Azure-Computer erstellt, das Betriebssystem konfiguriert und beliebige Datenträger angefügt haben. Falls dies noch nicht geschehen ist, finden Sie hier Anweisungen:

- [Erstellen eines benutzerdefinierten virtuellen Computers][]
- [Anfügen eines Datenträgers an einen virtuellen Computer][]

##Erfassen des virtuellen Computers##

1. Stellen Sie eine Verbindung mit dem virtuellen Computer her, indem Sie auf der Befehlsleiste auf **Verbinden** klicken. Ausführliche Informationen finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird][].

2.	Öffnen Sie ein Eingabeaufforderungsfenster als ein Administrator.


3.	Ändern Sie das Verzeichnis in `%windir%\system32\sysprep`, und führen Sie dann „sysprep.exe“ aus.


4. 	Das Dialogfeld **Systemvorbereitungstool** wird angezeigt. Gehen Sie wie folgt vor:


	- Wählen Sie unter **Systembereinigungsaktion** die Option **Out-of-Box-Experience (OOBE) für System aktivieren** und stellen Sie sicher, dass **Verallgemeinern** aktiviert ist. Weitere Informationen zur Verwendung von Sysprep finden Sie unter [How to Use Sysprep: An Introduction][].

	- Wählen Sie unter **Optionen für Herunterfahren** die Option **Herunterfahren**.

	- Klicken Sie auf **OK**.

	![Sysprep ausführen](./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png)

7.	Sysprep fährt den virtuellen Computer herunter. Dadurch wird der Status des virtuellen Computers im [Verwaltungsportal](http://manage.windowsazure.com) in **Angehalten** geändert.


8.	Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den virtuellen Computer aus, den Sie konfigurieren möchten.

9.	Klicken Sie in der Befehlsleiste auf **Aufnehmen**.

	![Virtuellen Computer erfassen](./media/virtual-machines-capture-image-windows-server/CaptureVM.png)

	Das Dialogfeld **Virtuellen Computer erfassen** wird angezeigt.

10.	Geben Sie unter **Imagename** den Namen für das neue Image ein.

11.	Bevor Sie ein Windows Server-Image zu Ihren benutzerdefinierten Images hinzufügen, muss es durch Ausführen von Sysprep, wie in den vorherigen Schritten beschrieben, verallgemeinert werden. Klicken Sie auf **Ich habe Sysprep auf dem virtuellen Computer ausgeführt**, um anzugeben, dass Sie dies durchgeführt haben.

12.	Aktivieren Sie das Kontrollkästchen, um das Image zu erfassen.

  **HINWEIS: Wenn Sie ein Image eines generalisierten virtuellen Computers erfassen, wird der virtuelle Computer gelöscht.**

 Das neue Image steht nun unter **Images** zur Verfügung.![Image-Erfassung erfolgreich](./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png)

##Nächste Schritte##
Das Image kann jetzt als Vorlage zum Erstellen virtueller Computer verwendet werden. Dazu erstellen Sie mithilfe der Methode **Aus Katalog** einen benutzerdefinierten virtuellen Computer und wählen das gerade erstellte Image aus. Anweisungen hierzu finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers][].

	
[Über Images virtueller Computer in Azure]: http://msdn.microsoft.com/library/azure/dn790290.aspx
[Erstellen eines benutzerdefinierten virtuellen Computers]: virtual-machines-create-custom.md
[Anfügen eines Datenträgers an einen virtuellen Computer]: storage-windows-attach-disk.md
[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]: http://www.windowsazure.com/manage/windows/how-to-guides/log-on-a-windows-vm/
[How to Use Sysprep: An Introduction]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

<!---HONumber=58--> 