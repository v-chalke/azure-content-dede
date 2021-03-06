#### So erstellen Sie einen neuen Dienst

1.  Melden Sie sich mit Ihren Microsoft-Kontoanmeldeinformationen unter folgender URL beim klassischen Azure-Portal an: [https://manage.windowsazure.com/](https://manage.windowsazure.com/)

2.  Klicken Sie im Portal auf **Neu > Datendienste > StorSimple Manager > Schnellerfassung**.

3.  Gehen Sie im angezeigten Formular folgendermaßen vor:

	1.  Geben Sie einen eindeutigen **Namen** für Ihren Dienst an. Dies ist ein Anzeigename, der zum Identifizieren des Diensts verwendet werden kann. Der Name kann zwischen 2 und 50 Zeichen lang sein und darf nur Buchstaben, Zahlen und Bindestriche enthalten. Der Name muss mit einem Buchstaben oder einer Zahl beginnen und enden.

	2.  Wählen Sie in der Dropdownliste unter **Verwalteter Gerätetyp** die Option **Serie virtueller Geräte**, um einen Dienst zum Verwalten eines virtuellen StorSimple-Geräts auszuwählen.

	3.  Geben Sie einen **Standort** für Ihren Dienst an. Der Standort bezieht sich auf die geografische Region, in der Ihr Gerät bereitgestellt werden soll.

	 -   Falls Sie über weitere Workloads in Azure verfügen, die über Ihr StorSimple-Gerät bereitgestellt werden sollen, empfiehlt sich die Verwendung dieses Rechenzentrums.

   	 -   StorSimple Manager und der Azure-Speicher können sich an zwei verschiedenen Standorten befinden. In einem solchen Fall müssen Sie das StorSimple Manager- und das Azure-Speicherkonto separat erstellen. Um ein Azure-Speicherkonto zu erstellen, wechseln Sie zum Azure Storage-Dienst im Portal und befolgen Sie die Schritte unter [Erstellen eines Azure Storage-Kontos](storage-create-storage-account.md#create-a-storage-account). Nach dem Erstellen dieses Kontos fügen Sie es dem StorSimple Manager-Dienst hinzu, indem Sie die Schritte unter [Konfigurieren eines neuen Speicherkontos für den Dienst](#optional-step-configure-a-new-storage-account-for-the-service) ausführen.
   	 
   	 -   Hinweis: In der Vorschauversion können Sie den StorSimple Manager-Dienst nur in den Regionen „USA, Westen“ und „Japan, Osten“ erstellen.
	
	1.  Wählen Sie ein **Abonnement** aus der Dropdownliste aus. Das Abonnement ist mit Ihrem Abrechnungskonto verknüpft. Dieses Feld wird nicht angezeigt, wenn Sie nur ein Abonnement besitzen.

	1.  Aktivieren Sie **Neues Azure-Speicherkonto erstellen**, um automatisch ein Speicherkonto mit dem Dienst zu erstellen. Dieses Speicherkonto erhält einen speziellen Namen, z. B. „storsimplebwv8c6dcnf“. Deaktivieren Sie dieses Kontrollkästchen, wenn Sie Ihre Daten an einem anderen Speicherort benötigen.

	1.  Klicken Sie auf **StorSimple-Manager erstellen**, um den Dienst zu erstellen.

		![](./media/storsimple-ova-create-new-service/image1-include.png)

	Sie werden auf die Startseite **Dienst** weitergeleitet. Die Diensterstellung dauert einige Minuten. Nachdem der Dienst erfolgreich erstellt wurde, werden Sie entsprechend benachrichtigt.

	![](./media/storsimple-ova-create-new-service/image2-include.png)

	Der Status des Diensts ändert sich in **Aktiv**.

<!---HONumber=AcomDC_0128_2016-->