
* Führen Sie die Schritte unter [Installing a Client SSL Signing Identity on the Server](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW15) (in englischer Sprache) aus, um das Zertifikat zu exportieren, das Sie die im vorherigen Schritt in eine P12-Datei heruntergeladen haben.

* Klicken Sie im klassischen Azure-Portal auf **Mobile Services** > Ihre App > Registerkarte **Push** > **Apple-Pushbenachrichtigungseinstellungen** > **Hochladen**. Laden Sie die P12-Datei hoch, und stellen Sie sicher, dass der richtige **Modus** ausgewählt ist (Sandbox oder Produktion, je nachdem, ob das von Ihnen erstellte Client-SSL-Zertifikat für die Entwicklung oder Verteilung galt). Ihr mobiler Dienst ist jetzt so konfiguriert, dass er mit Pushbenachrichtigungen unter iOS arbeitet.

<!---HONumber=AcomDC_1203_2015-->