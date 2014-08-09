Gehen Sie wie folgt vor, um einen Datenträger einzubinden:

1.  Klicken Sie im [Azure-Verwaltungsportal][1] auf **Virtuelle Computer** und wählen anschließend den gerade von Ihnen eingebundenen virtuellen Computer aus (**testwinvm**).

2.  Klicken Sie in der Befehlsleiste auf **Einbinden** und anschließend auf **Leeren Datenträger einbinden**.
    
    Das Dialogfeld **Leeren Datenträger in virtuellen Computer einbinden** wird angezeigt.

3.  Der **Name des virtuellen Computers**, der **Speicherort** und der **Dateiname** sind bereits ausgefüllt. Sie müssen lediglich die für den Datenträger gewünschte Größe eingeben. Geben Sie **5** in das Feld **Größe** ein.
    
    ![Leeren Datenträger einbinden](./media/attach-data-disk-windows-server-2008-vm-in-portal/AttachDataDiskWinVM2.png)
    
    **Hinweis:** Alle Datenträger werden aus einer VHD-Datei im Azure-Speicher erstellt. Sie können einen Namen für die VHD-Datei angeben, die Sie zum Speicher hinzufügen möchten. Azure generiert den Namen des Datenträgers jedoch automatisch.

4.  Klicken Sie auf das Häkchen, um den Datenträger an den virtuellen Computer anzufügen.

5.  Klicken Sie auf den Namen des virtuellen Computers, um das Dashboard anzuzeigen; so können Sie überprüfen, ob der Datenträger erfolgreich in den virtuellen Computer eingebunden wurde.
    
    Die Anzahl der Datenträger für den virtuellen Computer beträgt zwei. Der von Ihnen eingebundene Datenträger wird in der Datenträgertabelle aufgeführt.
    
    ![Leeren Datenträger einbinden](./media/attach-data-disk-windows-server-2008-vm-in-portal/AttachDataDiskWinVM3.png)
    
    Nach dem Anschließen des Datenträgers an den virtuellen Computer ist der Datenträger offline und nicht initialisiert. Sie müssen sich an dem virtuellen Computer anmelden und den Datenträger initialisieren,
    bevor Sie in zum Speichern von Daten verwenden können.

## Verbinden mit dem virtuellen Computer mit Remotedesktop und Abschließen des Setup

1.  Nachdem der virtuelle Computer bereitgestellt wurde, klicken Sie im Verwaltungsportal auf **Virtuelle Computer** und anschließend auf Ihren virtuellen Computer. Informationen zu Ihrem virtuellen Computer werden angezeigt.

2.  Klicken Sie unten auf der Seite auf **Verbinden**. Öffnen Sie die RPD-Datei mit dem Windows-Remotedesktop-Programm (*%windir%\\system32\\mstsc.exe*).

3.  Geben Sie im Dialogfeld **Windows-Sicherheit** das Kennwort für das **Administrator**-Konto ein. (Sie werden möglicherweise dazu aufgefordert, die Anmeldedaten des virtuellen Computers zu überprüfen.) Beim ersten Anmelden auf dem virtuellen Computer müssen eventuell einige Prozesse ausgeführt werden, darunter die Einrichtung Ihres Desktops, Windows-Updates und die Fertigstellung der ersten Windows-Konfigurationsaufgaben. Nachdem Sie mit über Windows Remotedesktop mit dem virtuellen Computer verbunden sind, funktioniert der virtuelle Computer wie jeder andere Computer.

4.  Öffnen Sie den **Server-Manager**, nachdem Sie sich auf dem virtuellen Computer angemeldet haben. Erweitern Sie im linken Bereich das Feld **Speicher**, und klicken Sie dann auf **Datenträgerverwaltung**.
    
    ![Server-Manager](./media/attach-data-disk-windows-server-2008-vm-in-portal/servermanager.png)

5.  Das Fenster **Datenträger initialisieren** wird angezeigt. Klicken Sie auf **OK**.
    
    ![Datenträger initialisieren](./media/attach-data-disk-windows-server-2008-vm-in-portal/initializedisk0.png)

6.  Klicken Sie mit der rechten Maustaste auf den Speicherplatzzuordnungsbereich für Datenträger 2, klicken Sie auf **Neues einfaches Volume**, und beenden Sie anschließend den Assistenten mit den Standardwerten.
    
    ![Neues einfaches Volume](./media/attach-data-disk-windows-server-2008-vm-in-portal/initializediskvolume.png)
    
    Der Datenträger ist nun online und betriebsbereit mit einem neuen Laufwerksbuchstaben.
    
    ![Initialisierung erfolgreich](./media/attach-data-disk-windows-server-2008-vm-in-portal/initializesuccess.png)



[1]: http://manage.windowsazure.com