<properties 
   pageTitle="Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole"
   description="Erläutert, wie die PuTTY-Terminalemulationssoftware für das Herstellen einer Verbindung zum StorSimple-Gerät verwendet wird."
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

###So stellen Sie über die serielle Konsole eine Verbindung her

1. Schließen Sie das serielle Kabel (direkt oder über einen seriellen USB-Adapter) an das Gerät an.

2. Öffnen Sie die **Systemsteuerung**, und öffnen Sie dann den **Geräte-Manager**.

3. Identifizieren Sie den COM-Anschluss, wie in der folgenden Abbildung dargestellt.

     ![Herstellen einer Verbindung über die serielle Konsole](./media/storsimple-use-putty/HCS_ConnectingDeviceS-include.png)

4. Starten Sie PuTTY.

5. Ändern Sie im rechten Bereich den **Verbindungstyp** in **Seriell**.

6. Geben Sie im rechten Bereich den entsprechenden COM-Anschluss ein. Stellen Sie sicher, dass die Parameter für die serielle Konfiguration wie folgt festgelegt sind:
  - Geschwindigkeit: 115.200
  - Datenbits: 8
  - Stoppbits: 1
  - Parität: Kein(e)
  - Flusssteuerung: Kein(e)

    Diese Einstellungen werden in der folgenden Abbildung gezeigt.

     ![PuTTY-Einstellungen](./media/storsimple-use-putty/HCS_ConnectingViaPutty-include.png)

    > [AZURE.NOTE]Wenn die Standardeinstellungen für die Flusssteuerung nicht funktionieren, versuchen Sie, die Flusssteuerung auf "XON/XOFF" festzulegen.

7. Klicken Sie auf **Öffnen**, um eine serielle Sitzung zu starten.
 
<!--HONumber=52-->