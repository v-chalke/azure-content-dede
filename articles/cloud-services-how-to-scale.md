<properties linkid="manage-services-how-to-scale-a-cloud-service" urlDisplayName="How to scale" pageTitle="How to scale a cloud service - Azure" metaKeywords="Azure link resource, scaling cloud service" description="Learn how to scale a cloud service and linked resources in Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Scale an Application" authors="davidmu" solutions="" manager="jeffreyg" editor="mattshel" />

Skalieren einer Anwendung
=========================

[WACOM.INCLUDE [Haftungsausschluss](../includes/disclaimer.md)]

Auf der Seite "Skalieren" des Azure Verwaltungsportals können Sie Ihre Anwendung manuell skalieren oder Parameter für die automatische Skalierung festlegen. Skaliert werden können Anwendungen, die Webrollen, Workerrollen oder virtuelle Computer ausführen. Sie skalieren eine Anwendung, die Instanzen von Webrollen und Workerrollen ausführt, indem Sie Rolleninstanzen hinzufügen oder entfernen, um die Arbeitslast zu bewältigen.

Bei der Skalierung einer Anwendung, die virtuelle Computer ausführt, werden keine neuen Computer erstellt oder gelöscht, sondern lediglich in einer Verfügbarkeitsgruppe vorhandene Computer aktiviert oder deaktiviert. Sie können festlegen, dass eine Skalierung abhängig von der durchschnittlichen prozentualen CPU-Nutzung oder abhängig von der Anzahl Nachrichten in einer Warteschlange durchgeführt wird.

Folgendes ist allerdings zu beachten, bevor die Skalierung einer Anwendung konfiguriert wird:

-   Virtuelle Computer müssen nach der Erstellung zu einer Verfügbarkeitsgruppe hinzugefügt werden, um sie für die Skalierung einer Anwendung verwenden zu können. Die derart in eine Gruppe eingefügten virtuellen Computer können zunächst beliebig aktiviert oder deaktiviert sein. Beim Hochskalieren werden sie allerdings definitiv aktiviert und beim Herunterskalieren deaktiviert. Weitere Information zu virtuellen Computern und Verfügbarkeitsgruppen finden Sie unter [Verwaltung der Verfügbarkeit virtueller Computer](http://www.windowsazure.com/de-de/manage/windows/common-tasks/manage-vm-availability/).
-   Die Skalierung ist abhängig von der Kernspeichernutzung. Größere Rolleninstanzen oder virtuelle Computer erfordern mehr Kernspeicher. Sie können eine Anwendung nur innerhalb der für Ihr Abonnement geltenden Kernspeichergrenzwerte skalieren. Wenn als Grenzwert für Ihr Abonnement beispielsweise zwanzig Kernspeicher festgelegt sind und Sie eine Anwendung mit zwei mittelgroßen virtuellen Computern ausführen (insgesamt vier Kernspeicher), stehen für das Hochskalieren anderer bereitgestellter Clouddienste in Ihrem Abonnement nur noch sechzehn Kernspeicher zur Verfügung. Für die Skalierung einer Anwendung können nur gleich große virtuelle Computer einer Verfügbarkeitsgruppe verwendet werden. Weitere Informationen über Kernspeichernutzung und Größen von virtuellen Computern finden Sie unter [Größen virtueller Computer und Clouddienste für Windows Azure](http://msdn.microsoft.com/de-de/library/dn197896.aspx).
-   Sie müssen eine Warteschlange anlegen und diese einer Rolle oder einer Verfügbarkeitsgruppe zuweisen, bevor Sie eine Anwendung auf Basis eines Nachrichtenschwellwerts skalieren können. Weitere Informationen finden Sie unter [Verwenden des Warteschlangenspeicherdiensts](http://www.windowsazure.com/de-de/develop/net/how-to-guides/queue-service).
-   Sie können Ressourcen skalieren, die mit Ihrem Clouddienst verknüpft sind. Weitere Informationen zum Verknüpfen von Ressourcen finden Sie unter [Gewusst wie: Verknüpfen einer Ressource mit einem Clouddienst](http://www.windowsazure.com/de-de/manage/services/cloud-services/how-to-manage-a-cloud-service/#linkresources).
-   Um die Hochverfügbarkeit Ihrer Anwendung zu gewährleisten, sollte diese mit mindestens zwei Rolleninstanzen bzw. virtuellen Computern bereitgestellt werden. Weitere Informationen finden Sie unter [Vereinbarungen zum Servicelevel](https://www.windowsazure.com/en-us/support/legal/sla/).

Folgende Skalierungsaktionen können für einen Clouddienst ausgeführt werden:

-   [Manuelles Skalieren einer für die Ausführung von Webrollen und Workerrollen konzipierten Anwendung](#manualscale)
-   [Automatisches Skalieren einer für die Ausführung von Webrollen, Workerrollen oder virtuellen Computern konzipierten Anwendung](#autoscale)
-   [Skalieren verknüpfter Ressourcen](#scalelink)
-   [Planen der Anwendungsskalierung](#schedule)

Manuelles Skalieren einer für die Ausführung von Webrollen und Workerrollen konzipierten Anwendung
--------------------------------------------------------------------------------------------------

Auf der Skalierungsseite können Sie die Anzahl der in einem Clouddienst laufenden Instanzen manuell erhöhen oder verringern.

1.  Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Clouddienste** und dann auf den Namen des Clouddiensts, um das Dashboard zu öffnen.

2.  Klicken Sie auf **Skalieren**. Die automatische Skalierung ist standardmäßig für alle Rollen deaktiviert, sodass Sie die Anzahl der von Ihrer Anwendung verwendeten Instanzen manuell ändern können.

    ![Skalierungsseite](./media/cloud-services-how-to-scale/CloudServices_ManualScaleRoles.png)

3.  Jede Rolle in einem Clouddienst hat einen Regler, über den die Anzahl der verwendbaren Instanzen geändert werden kann. Um eine Rolleninstanz hinzuzufügen, ziehen Sie den Regler nach rechts. Um eine Rolleninstanz zu entfernen, ziehen Sie den Regler nach links.

    ![Skalierung (Rolle)](./media/cloud-services-how-to-scale/CloudServices_SliderRole.png)

    Sie können die Anzahl der verwendeten Instanzen nur dann erhöhen, wenn ausreichend Kernspeicher für die Unterstützung dieser Instanzen zur Verfügung stehen. Die Farben des Reglers lassen erkennen, wie viele Kernspeicher Ihres Abonnements belegt bzw. noch verfügbar sind:

	 - Blau steht für die Kernspeicher, die von der ausgewählten Rolle genutzt werden.
	 - Dunkelgrau steht für die Gesamtzahl der Kernspeicher, die von allen Rollen und virtuellen Computern in dem Abonnement genutzt werden.
	 - Hellgrau steht für die Kernspeicher, die für die Skalierung zur Verfügung stehen.
	 - Pink steht für eine Änderung, die noch nicht gespeichert worden ist.

4.  Klicken Sie auf **Speichern**. Je nach Reglerstellung werden Rolleninstanzen hinzugefügt oder entfernt.

Automatisches Skalieren einer für die Ausführung von Webrollen, Workerrollen oder virtuellen Computern konzipierten Anwendung
-----------------------------------------------------------------------------------------------------------------------------

Auf der Skalierungsseite können Sie Ihren Clouddienst so konfigurieren, dass die Anzahl der von Ihrer Anwendung verwendeten Instanzen oder virtuellen Computer automatisch erhöht oder verringert wird. Für diese Konfiguration der Skalierung stehen folgende Parameter zur Verfügung:

- [Durchschnittliche CPU-Nutzung](#averagecpu) - Wenn die durchschnittliche prozentuale CPU-Nutzung bestimmte Schwellwerte über- oder unterschreitet, werden Rolleninstanzen erstellt oder gelöscht oder virtuelle Computer in einer Verfügbarkeitsgruppe aktiviert oder deaktiviert.
- [Warteschlangennachrichten](#queuemessages) - Wenn die Anzahl der Nachrichten in einer Warteschlange bestimmte Schwellwerte über- oder unterschreitet, werden Rolleninstanzen erstellt oder gelöscht oder virtuelle Computer in einer Verfügbarkeitsgruppe aktiviert oder deaktiviert.

### Durchschnittliche CPU-Nutzung

1. Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Clouddienste** und dann auf den Namen des Clouddiensts, um das Dashboard zu öffnen.
2. Klicken Sie auf **Skalieren**.
3. Führen Sie einen Bildlauf zu dem Abschnitt für die Rolle bzw. Verfügbarkeitsgruppe aus, und klicken Sie dann auf **CPU**. Dann kann Ihre Anwendung basierend auf der durchschnittlichen prozentualen Nutzung von CPU-Ressourcen automatisch skaliert werden.

	![AutoSkalieren aktiviert](./media/cloud-services-how-to-scale/CloudServices_AutoscaleOn.png)

4. Jede Rolle bzw. Verfügbarkeitsgruppe hat einen Regler, über den die Anzahl der verwendbaren Instanzen geändert werden kann. Um die maximale Anzahl verwendbarer Instanzen einzustellen, ziehen Sie den rechten Regler nach rechts. Um die Mindestanzahl verwendbarer Instanzen einzustellen, ziehen Sie den linken Regler nach links.

	**Hinweis:** Auf der Skalierungsseite steht **Instanz** entweder für eine Rolleninstanz oder für eine Instanz eines virtuellen Computers.

	![Instanzenbereich](./media/cloud-services-how-to-scale/CloudServices_InstanceRange.png)

	Die maximale Anzahl Instanzen ist durch die Anzahl der im Abonnement verfügbaren Kernspeicher beschränkt. Die Farben des Reglers lassen erkennen, wie viele Kernspeicher Ihres Abonnements belegt bzw. noch verfügbar sind:

	- Blau steht für die maximale Anzahl der Kernspeicher, die die Rolle nutzen kann.
	- Dunkelgrau steht für die Gesamtzahl der Kernspeicher, die von allen Rollen und virtuellen Computern in dem Abonnement genutzt werden. Wenn dieser Wert die von der Rolle verwendeten Kernspeicher überlappt, ändert sich die Farbe in Dunkelblau.
	- Hellgrau steht für die Kernspeicher, die für die Skalierung zur Verfügung stehen.
	- Pink steht für eine Änderung, die noch nicht gespeichert worden ist.

5. Durch Verschieben eines Reglers wird der Bereich der durchschnittlichen prozentualen CPU-Nutzung eingestellt. Wenn die durchschnittliche prozentuale CPU-Nutzung den Höchstwert überschreitet, werden zusätzliche Rolleninstanzen erstellt bzw. virtuelle Computer aktiviert. Wenn die durchschnittliche prozentuale CPU-Nutzung den Mindestwert unterschreitet, werden Rolleninstanzen gelöscht bzw. virtuelle Computer deaktiviert. Um den Höchstwert der durchschnittlichen prozentualen CPU-Nutzung einzustellen, ziehen Sie den rechten Regler nach rechts. Um den Mindestwert der durchschnittlichen prozentualen CPU-Nutzung einzustellen, ziehen Sie den linken Regler nach links.

	![Ziel-CPU](./media/cloud-services-how-to-scale/CloudServices_TargetCPURange.png)

6. Sie können die Anzahl der Instanzen festlegen, die beim Hochskalieren Ihrer Anwendung jeweils hinzugefügt bzw. aktiviert werden sollen. Um diese Anzahl zu erhöhen, ziehen Sie den Regler nach rechts. Um sie zu verringern, ziehen Sie den Regler nach links.

	![Hochskalieren (CPU)](./media/cloud-services-how-to-scale/CloudServices_ScaleUpBy.png)

7. Geben Sie die Wartezeit zwischen der letzten Skalierungsaktion und dem nächsten Hochskalieren in Minuten ein. Bei der letzten Skalierungsaktion kann es sich entweder um ein Hoch- oder Herunterskalieren handeln.

	![Wartezeit (Hochskalieren)](./media/cloud-services-how-to-scale/CloudServices_ScaleUpWaitTime.png)

	Bei der Berechnung der durchschnittlichen prozentualen CPU-Nutzung werden alle Instanzen erfasst. Der Durchschnittswert basiert auf der Nutzung in der jeweils vorhergehenden Stunde. Je nachdem, wie viele Instanzen Ihre Anwendung verwendet, und wenn die festgelegte Wartezeit sehr kurz ist, kann es länger dauern, bis die nächste Skalierungsaktion stattfindet. Das Mindestintervall zwischen Skalierungsaktionen ist fünf Minuten. Skalierungsaktionen können nicht stattfinden, wenn sich eine oder mehrere Instanzen in einem Übergangszustand befinden.

8. Sie können auch die Anzahl der Instanzen festlegen, die beim Herunterskalieren Ihrer Anwendung jeweils gelöscht bzw. deaktiviert werden sollen. Um diese Anzahl zu erhöhen, ziehen Sie den Regler nach rechts. Um sie zu verringern, ziehen Sie den Regler nach links.

	![Herunterskalieren (CPU)](./media/cloud-services-how-to-scale/CloudServices_ScaleDownBy.png)

	Wenn bei Ihrer Anwendung ein plötzlicher Anstieg der CPU-Nutzung auftreten kann, müssen Sie sicherstellen, dass eine ausreichende Mindestanzahl von Instanzen verfügbar ist, um diese Nutzungsspitzen abzufangen.

9. Geben Sie die Wartezeit zwischen der letzten Skalierungsaktion und dem nächsten Herunterskalieren in Minuten ein. Bei der letzten Skalierungsaktion kann es sich entweder um ein Hoch- oder Herunterskalieren handeln.

	![Wartezeit (Herunterskalieren)](./media/cloud-services-how-to-scale/CloudServices_ScaleDownWaitTime.png)

10. Klicken Sie auf **Speichern**. Die Skalierungsaktion kann bis zu fünf Minuten dauern.

### Warteschlangennachrichten

1. Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Clouddienste** und dann auf den Namen des Clouddiensts, um das Dashboard zu öffnen.
2. Klicken Sie auf **Skalieren**.
3. Führen Sie einen Bildlauf zu dem Abschnitt für die Rolle bzw. Verfügbarkeitsgruppe aus, und klicken Sie dann auf **Warteschlange**. Dann kann Ihre Anwendung basierend auf einer vorgegebenen Anzahl Warteschlangennachrichten automatisch skaliert werden.

	![Skalieren (Warteschlange)](./media/cloud-services-how-to-scale/CloudServices_QueueScale.png)

4. Jede im Clouddienst definierte Rolle bzw. Verfügbarkeitsgruppe hat einen Regler, über den die Anzahl der verwendbaren Instanzen geändert werden kann. Um die maximale Anzahl verwendbarer Instanzen einzustellen, ziehen Sie den rechten Regler nach rechts. Um die Mindestanzahl verwendbarer Instanzen einzustellen, ziehen Sie den linken Regler nach links.

	![Warteschlangen (Bereich)](./media/cloud-services-how-to-scale/CloudServices_QueueRange.png)

	**Hinweis:** Auf der Skalierungsseite steht **Instanz** entweder für eine Rolleninstanz oder für eine Instanz eines virtuellen Computers.


	 Die maximale Anzahl Instanzen ist durch die Anzahl der im Abonnement verfügbaren Kernspeicher beschränkt. Die Farben des Reglers lassen erkennen, wie viele Kernspeicher Ihres Abonnements belegt bzw. noch verfügbar sind:
	- Blau steht für die maximale Anzahl der Kernspeicher, die die Rolle nutzen kann.
	- Dunkelgrau steht für die Gesamtzahl der Kernspeicher, die von allen Rollen und virtuellen Computern in dem Abonnement genutzt werden. Wenn dieser Wert die von der Rolle verwendeten Kernspeicher überlappt, ändert sich die Farbe in Dunkelblau.
	- Hellgrau steht für die Kernspeicher, die für die Skalierung zur Verfügung stehen.
	- Pink steht für eine Änderung, die noch nicht gespeichert worden ist.

5. Wählen Sie das Speicherkonto der Warteschlange aus, die Sie verwenden wollen.

	![Speicherkontoname](./media/cloud-services-how-to-scale/CloudServices_StorageAccountName.png)

6. Wählen Sie die Warteschlange aus.

	![Warteschlangenname](./media/cloud-services-how-to-scale/CloudServices_QueueName.png)

7. Geben Sie die Anzahl der Nachrichten ein, die jede Instanz voraussichtlich unterstützen muss. Die Skalierung der Instanzen erfolgt auf Basis der Gesamtzahl Nachrichten, dividiert durch die vorgegebene Anzahl Nachrichten pro Computer.

	![Nachrichtenanzahl](./media/cloud-services-how-to-scale/CloudServices_TargetMessageNumber.png)

8. Sie können die Anzahl der Instanzen festlegen, die beim Hochskalieren Ihrer Anwendung jeweils hinzugefügt bzw. aktiviert werden sollen. Um diese Anzahl zu erhöhen, ziehen Sie den Regler nach rechts. Um sie zu verringern, ziehen Sie den Regler nach links.

	![Hochskalieren (CPU)](./media/cloud-services-how-to-scale/CloudServices_ScaleUpBy.png)

9. Geben Sie die Wartezeit zwischen der letzten Skalierungsaktion und dem nächsten Hochskalieren in Minuten ein. Bei der letzten Skalierungsaktion kann es sich entweder um ein Hoch- oder Herunterskalieren handeln.

	![Wartezeit (Hochskalieren)](./media/cloud-services-how-to-scale/CloudServices_ScaleUpWaitTime.png)

	Das Mindestintervall zwischen Skalierungsaktionen ist fünf Minuten. Skalierungsaktionen können nicht stattfinden, wenn sich eine oder mehrere Instanzen in einem Übergangszustand befinden.

10.	Sie können auch die Anzahl der Instanzen festlegen, die beim Herunterskalieren Ihrer Anwendung jeweils gelöscht bzw. nicht mehr verwendet werden sollen. Das Skalierungsinkrement kann über einen Regler eingestellt werden. Um die Anzahl der beim Herunterskalieren der Anwendung gelöschten bzw. nicht mehr verwendeten Instanzen zu erhöhen, ziehen Sie den Regler nach rechts. Um sie zu verringern, ziehen Sie den Regler nach links.

	![Herunterskalieren (CPU)](./media/cloud-services-how-to-scale/CloudServices_ScaleDownBy.png)

11.	Geben Sie die Wartezeit zwischen der letzten Skalierungsaktion und dem nächsten Herunterskalieren in Minuten ein. Bei der letzten Skalierungsaktion kann es sich entweder um ein Hoch- oder Herunterskalieren handeln.

	![Wartezeit (Herunterskalieren)](./media/cloud-services-how-to-scale/CloudServices_ScaleDownWaitTime.png)

12.	Klicken Sie auf **Speichern**. Die Skalierungsaktion kann bis zu fünf Minuten dauern.

Skalieren verknüpfter Ressourcen
--------------------------------

Häufig empfiehlt es sich, beim Skalieren einer Rolle auch die von der Anwendung verwendete Datenbank zu skalieren. Wenn Sie die Datenbank mit dem Clouddienst verknüpfen, ändern Sie die Edition der SQL-Datenbank und die Größe der Datenbank auf der Skalierungsseite.

1.  Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Clouddienste** und dann auf den Namen des Clouddiensts, um das Dashboard zu öffnen.
2.  Klicken Sie auf **Skalieren**.
3.  Wählen Sie im Abschnitt "Linkes Resources" die für die Datenbank zu verwendende Edition aus.

    ![Verknüpfte Ressourcen](./media/cloud-services-how-to-scale/CloudServices_ScaleLinkedResources.png)

4.  Wählen Sie die Größe der Datenbank aus.
5.  Klicken Sie auf **Speichern**, um die verknüpften Ressourcen zu aktualisieren.

Planen der Anwendungsskalierung
-------------------------------

Sie können die automatische Skalierung Ihrer Anwendung planen, indem Sie Zeitpläne mit bestimmten Skalierungszeiten konfigurieren. Für die automatische Skalierung sind die folgenden Optionen verfügbar:

-   **No schedule** - Dies ist die Standardoption, d. h. Ihre Anwendung wird immer auf dieselbe Weise skaliert.

-   **Day and night** - Diese Option erlaubt die Festlegung der Skalierung zu bestimmten Zeiten während des Tages und der Nacht.

**Hinweis:** Für Anwendungen, die virtuelle Computer verwenden, werden Zeitpläne derzeit noch nicht unterstützt.

1.  Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Clouddienste** und dann auf den Namen des Clouddiensts, um das Dashboard zu öffnen.
2.  Klicken Sie auf **Skalieren**.
3.  Klicken Sie in der Skalierungsseite auf **set up schedule times**.

    ![Zeitplan (Skalierung)](./media/cloud-services-how-to-scale/CloudServices_SetUpSchedule.png)

4.  Wählen Sie den Skalierungszeitplantyp aus, den Sie einrichten möchten.

5.  Geben die Zeiten für den Tagesanfang und das Tagesende ein, und legen Sie die Zeitzone fest. Für die Planung von Tages- und Nachtzeiten gilt, dass diese Zeiten den Anfang und das Ende des betreffenden Tages markieren und die verbleibenden Zeiten die Nacht darstellen.

6.  Klicken Sie auf das Häkchen unten auf der Seite, um die Zeitpläne zu speichern.

7.  Nachdem Sie die Zeitpläne gespeichert haben, werden diese in der Liste angezeigt. Sie können einen Zeitplan auswählen, den Sie verwenden möchten, und dann die Skalierungseinstellungen ändern. Die Skalierungseinstellungen kommen nur während des ausgewählten Zeitplans zur Anwendung. Zum Bearbeiten von Zeitplänen klicken Sie auf **set up schedule times**.
