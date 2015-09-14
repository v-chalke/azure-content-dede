<properties
   pageTitle="Übertragen eines Azure-Abonnements | Microsoft Azure"
	description="Übertragung eines Azure-Abonnements auf einen anderen Benutzer, und einige häufig gestellte Fragen (FAQS) zu dem Prozess."
	services="billing"
	documentationCenter=""
	authors="curtand"
	manager="msmStevenPo"
	editor=""/>

<tags
   ms.service="billing"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="billing"
	ms.date="08/19/2015"
	ms.author="curtand;ruchic"/>

# Übertragen eines Azure-Abonnements

Möchten Sie:

- die Abrechnung für das Azure-Abonnement an eine andere Person übergeben?
- das Konto wechseln, das Sie für die Anmeldung in Azure verwenden? Vielleicht haben Sie Ihr Microsoft-Konto verwendet, wollten aber eigentlich Ihr Geschäfts- oder Schulkonto verwenden?
- ihre Azure-Abonnement von einem Verzeichnis ins andere verschieben?
- Azure und Office 365, die in verschiedenen Mandanten sind, konsolidieren?

Ist Ihr Konto in den USA, können Sie dies jetzt problemlos im Microsoft Azure Account Center für nutzungsbasierte Abonnements tun. Wir haben die Möglichkeit hinzugefügt, Ihr Abonnement auf einen anderen Benutzer zu übertragen. Anders ausgedrückt können Sie jetzt das Administratorkonto auf ein beliebiges nutzungsbasiertes Abonnement ändern, das Sie besitzen.

## Übertragen eines Azure-Abonnements

1.  Melden Sie sich bei <https://account.windowsazure.com/Subscriptions> an.

2.  Wählen Sie das Abonnement, das Sie übertragen möchten.

3.  Klicken Sie auf die Otion **Abonnement übertragen**.

    ![Registerkarte „Azure-Kontoabonnements“](./media/billing-subscription-transfer/image1.png)

4.  Folgen Sie den Anweisungen, um den Empfänger anzugeben.

    ![Dialogfeld „Übertragen des Abonnements“](./media/billing-subscription-transfer/image2.PNG)

5.  Der Empfänger erhält automatisch eine E-Mail mit einem Link zum Akzeptieren.

    ![„Übertragen des Abonnements“-E-Mail an den Empfänger](./media/billing-subscription-transfer/image3.png)

6.  Der Empfänger klickt auf den Link und folgt den Anweisungen. Außerdem gibt er seine Zahlungsinformationen ein.

    ![Website für die erste Abonnementübertragung](./media/billing-subscription-transfer/image4.PNG)

    ![Website für die zweite Abonnementübertragung](./media/billing-subscription-transfer/image5.PNG)

7. Erfolg! Das Abonnement ist jetzt übertragen.

## Häufig gestellte Fragen (FAQ)

-   **Führt eine Übertragung des Abonnements zu Ausfallzeiten?**

    Die Übertragung hat keine Auswirkung auf den Dienst. Effektiv wird dabei das Abonnement unter dem laufenden Kontoadministrator gekündigt, und ein neues unter dem Konto des Empfängers erstellt. Dabei werden die zugrunde liegenden Azure-Dienste dem neuen Abonnement zugeordnet. Die Abonnement-ID bleibt unverändert.

-   **Wenn ich die Abrechnung eines Abonnements aus einer anderen Organisation übernehme, kann diese weiterhin auf meine Ressourcen zugreifen?**

    Wenn das Abonnement auf einen anderen Mandanten übertragen wird, verlieren die Benutzer, die dem vorherigen Mandanten zugeordnet sind, den Zugriff auf das Abonnement. Selbst wenn ein Benutzer kein Dienstadministrator oder Co-Administrator mehr ist, hat er über andere Sicherheitsmechanismen möglicherweise immer noch Zugriff auf das Abonnement. Dazu gehören: – Verwaltungszertifikate, die dem Benutzer Administratorrechte auf Abonnementressourcen gewähren. Weitere Informationen finden Sie unter [Erstellen und Hochladen eines Verwaltungszertifikats für Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx) – Zugriffschlüssel für Dienste wie Storage. Weitere Informationen finden Sie unter [Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys) – RAS-Anmeldeinformationen für Dienste wie virtuelle Azure Computer.

    Dies ist keine vollständige Liste. Der Empfänger sollte sich überlegen, ob er dem Dienst zugeordnete Schlüssel aktualisiert, wenn der Zugriff auf die Ressourcen eingeschränkt werden soll. Die meisten Ressourcen können wie folgt aktualisiert werden:

    1.   Öffnen Sie das Azure-Portal: [**https://portal.azure.com*](https://portal.azure.com)

    2.    Klicken Sie auf alle „Browse All -&gt; All Resources“.

    3.    Wählen Sie die Ressource. Daraufhin wird das Blatt "Ressourcen" geöffnet.

    4.    Klicken Sie im Blatt "Ressourcen" auf **Einstellungen**. Hier können Sie die vorhandenen Schlüssel anzeigen und aktualisieren.


-   **Wenn ich das Abonnement in der Mitte des Abrechnungszyklus übertrage, zahlt der Empfänger die Rechnung für den gesamten Zyklus?**

    Der Absender ist zuständig für die Zahlung für jegliche Nutzung, die bis zu dem Zeitpunkt gemeldet wurde, an dem die Übertragung abgeschlossen war. Der Empfänger ist verantwortlich für die Verwendung, die vom Zeitpunkt der Übertragung an berichtet wird. Möglicherweise gibt es Nutzung, die vor der Übertragung stattgefunden hat, aber danach gemeldet wurde. Dies wird in der Rechnung des Empfängers enthalten sein.

-   **Verfügt der Empfänger über Zugriff auf den Nutzungs- und Abrechnungsverlauf?**

    Zu diesem Zeitpunkt ist die einzige Information, die dem Empfänger angezeigt wird, der Betrag der letzten Rechnung (oder der derzeitige Kontostand, wenn das Abonnement übertragen wurde, bevor die erste Rechnung generiert wurde). Der restliche Nutzungs- und Abrechnungsverlauf wird nicht mit dem Abonnement übertragen.

-   **Kann das Angebot während der Übertragung geändert werden?**

    Das Angebot muss unverändert bleiben. Um Ihr Angebot zu ändern, müssen Sie sich [an den Support wenden](http://go.microsoft.com/fwlink/?LinkID=619338).

-   **Kann ein Abonnement auf ein Benutzerkonto in einem anderen Land übertragen werden?**

    Nein, zu diesem Zeitpunkt wird dies nicht unterstützt. Das Benutzerkonto des Empfängers muss sich im gleichen Land befinden.

-   **Kann der Empfänger einen anderen Zahlungsmechanismus nutzen?**

    Ja, und in der Tat können Sie diesen Mechanismus dazu nutzen, um die Zahlungsmethode für Ihr Abonnement von Rechnung auf Kreditkarte zu ändern. Führen Sie nur die Übertragung auf ein anderes Konto durch, und geben Sie Ihre Kreditkartendaten beim Empfang des Abonnements ein. Es gibt hier Einschränkungen: der Abrechnungsverlauf des Abonnements ist nun auf zwei Konten aufgeteilt. Der Vorteil ist jedoch, dass Sie sich dazu nicht an den [Support wenden](http://go.microsoft.com/fwlink/?LinkID=619338) müssen.

<!---HONumber=September15_HO1-->