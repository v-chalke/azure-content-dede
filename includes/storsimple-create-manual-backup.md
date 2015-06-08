<properties 
   pageTitle="Erstellen einer manuellen Sicherung" description="Erläutert das Starten einer manuellen Sicherung nach Bedarf."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/01/2015"
   ms.author="v-sharos" />

###So erstellen Sie eine manuelle Sicherung

1. Navigieren Sie auf der Seite **Geräte** zu der Registerkarte **Sicherungsrichtlinien**. Auf dieser Registerkarte werden alle Sicherungsrichtlinien in einem Tabellenformat aufgelistet, darunter auch die Richtlinie für das Volume, das Sie sichern möchten.

2. Wählen Sie die Richtlinie aus, indem auf eine beliebige Stelle in der entsprechenden Zeile mit Ausnahme der ersten Spalte klicken. Klicken Sie unten auf der Seite auf **Sicherung erstellen**. Die Schaltfläche wird erweitert, um die Sicherungsoptionen anzuzeigen: "Lokale Momentaufnahme" und "Cloudmomentaufnahme".

3. Wenn Sie eine dieser Optionen auswählen, werden Sie zur Bestätigung aufgefordert. Klicken Sie auf **Ja**.

    ![Erstellen einer manuellen Sicherung 1](./media/storsimple-create-manual-backup/HCS_CreateManualBackup1-include.png)
 
    Es wird ein Auftrag zum Erstellen einer Momentaufnahme gestartet. Am unteren Rand der Seite wird eine Benachrichtigung angezeigt, nachdem der Auftrag erfolgreich erstellt wurde.

4. Klicken Sie im Benachrichtigungsbereich (unten auf der Seite) auf **Auftrag anzeigen**, um den Auftrag zu überwachen.

    ![Erstellen einer manuellen Sicherung 2](./media/storsimple-create-manual-backup/HCS_CreateManualBackup2-include.png)

5. Nachdem der Sicherungsauftrag abgeschlossen wurde, navigieren Sie zur Registerkarte **Sicherungskatalog**.

6. Legen Sie die Filterauswahl auf das entsprechende Gerät, die Sicherungsrichtlinie und den Zeitraum fest. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-create-manual-backup/HCS_CheckIcon-include.png), nachdem Sie die Filter festgelegt haben.

  Die Sicherung sollte in der Liste der Sicherungssätze enthalten sein, die im Katalog angezeigt wird.

<!--HONumber=52-->