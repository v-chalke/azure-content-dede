<properties linkid="develop-python-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (Python) - Azure" metaKeywords="Get started Azure Service Bus topics publising subscribe messaging Python" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Python applications." metaCanonical="" services="service-bus" documentationCenter="Python" title="How to Use Service Bus Topics/Subscriptions" authors="" solutions="" manager="" editor="" />

Verwenden von Service Bus-Themen/-Abonnements
=============================================

In diesem Leitfaden erfahren Sie, wie Sie Service Bus-Themen und -Abonnements von Python-Anwendungen verwenden. Die behandelten Szenarien umfassen das **Erstellen von Themen und Abonnements, Erstellen von Abonnementfiltern, Senden von Nachrichten** an ein Thema, das **Empfangen von Nachrichten von einem Abonnement** und das **Löschen von Themen und Abonnements**. Weitere Informationen zu Themen und Abonnements finden Sie im Abschnitt [Nächste Schritte](#Next_Steps).

Inhaltsverzeichnis
------------------

-   [Was sind Service Bus-Themen und -Abonnements?](#what-are-service-bus-topics)
-   [Erstellen eines Dienstnamespaces](#create-a-service-namespace)
-   [Abrufen der Standard-Anmeldeinformationen für den Namespace](#obtain-default-credentials)
-   [Gewusst wie: Erstellen eines Themas](#How_to_Create_a_Topic)
-   [Gewusst wie: Erstellen von Abonnements](#How_to_Create_Subscriptions)
-   [Gewusst wie: Senden von Nachrichten an ein Thema](#How_to_Send_Messages_to_a_Topic)
-   [Gewusst wie: Empfangen von Nachrichten aus einem Abonnement](#How_to_Receive_Messages_from_a_Subscription)
-   [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten](#How_to_Handle_Application_Crashes_and_Unreadable_Messages)
-   [Gewusst wie: Löschen von Themen und Abonnements](#How_to_Delete_Topics_and_Subscriptions)
-   [Nächste Schritte](#Next_Steps)

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

**Hinweis:** Informationen zur Installation von Python oder den Clientbibliotheken finden Sie im [Python-Installationshandbuch](../python-how-to-install/).

Erstellen eines Themas
----------------------

Das **ServiceBusService**-Objekt ermöglicht Ihnen das Arbeiten mit Themen. Fügen Sie am Anfang jeder Python-Datei, in der Sie programmgesteuert auf Azure Service Bus zugreifen möchten, den folgenden Code hinzu:

    from azure.servicebus import *

Der folgende Code erstellt ein **ServiceBusService**-Objekt. Ersetzen Sie 'mynamespace', 'mykey' und 'myissuer' durch die tatsächlichen Werte für Namespace, Schlüssel und Aussteller.

    bus_service = ServiceBusService(service_namespace='mynamespace', account_key='mykey', issuer='myissuer')

    bus_service.create_topic('mytopic')

**create\_topic** unterstützt zudem weitere Optionen, mit denen Sie die Standardthemeneinstellungen überschreiben können, wie zum Beispiel die Nachrichtenlebensdauer oder maximale Themengröße. Das folgende Beispiel zeigt, wie Sie die maximale Themengröße auf 5 GB bei einer Lebensdauer von 1 Minute festlegen:

    topic_options = Topic()
    topic_options.max_size_in_megabytes = '5120'
    topic_options.default_message_time_to_live = 'PT1M'

    bus_service.create_topic('mytopic', topic_options)

Erstellen von Abonnements
-------------------------

Themenabonnements werden ebenfalls mit dem **ServiceBusService**-Objekt erstellt. Abonnements werden benannt und können einen optionalen Filter aufweisen, der die Nachrichten einschränkt, die an die virtuelle Warteschlange des Abonnements übergeben werden.

**Hinweis**: Abonnements sind dauerhaft und existieren solange, bis sie oder das mit ihnen verbundene Thema gelöscht werden.

### Erstellen eines Abonnements mit dem Standardfilter (MatchAll)

**MatchAll** ist der Standardfilter, der verwendet wird, wenn beim Erstellen eines neuen Abonnements kein Filter angegeben wird. Wenn der Filter **MatchAll** verwendet wird, werden alle für das Thema veröffentlichten Nachrichten in die virtuelle Warteschlange des Abonnements gestellt. Mit dem folgenden Beispiel wird ein Abonnement namens "AllMessages" erstellt, für das der Standardfilter **MatchAll** verwendet wird.

    bus_service.create_subscription('mytopic', 'AllMessages')

### Erstellen von Abonnements mit Filtern

Sie können auch Filter einrichten, durch die Sie angeben können, welche an ein Thema gesendeten Nachrichten in einem bestimmten Themenabonnement angezeigt werden sollen.

Der von Abonnements unterstützte flexibelste Filtertyp ist **SqlFilter**, der eine Teilmenge von SQL92 implementiert. SQL-Filter werden auf die Eigenschaften der Nachrichten angewendet, die für das Thema veröffentlicht werden. Weitere Informationen zu den Ausdrücken, die mit einem SQL-Filter verwendet werden können, finden Sie in der Syntax [SqlFilter.SqlExpression][].

Sie können mithilfe der **create\_rule**-Methode des **ServiceBusService**-Objekts Filter zu einem Abonnement hinzufügen. Durch diese Methode können Sie neue Filter zu einem vorhandenen Abonnement hinzufügen.

**Hinweis**: Da der Standardfilter automatisch auf alle neuen Abonnements angewendet wird, müssen Sie zuerst den Standardfilter entfernen. Ansonsten überschreibt **MatchAll** alle Filter, die Sie angeben. Sie können die Standardregel mithilfe der **delete\_rule**-Methode des **ServiceBusService**-Objekts entfernen.

Mit dem folgenden Beispiel wird ein Abonnement namens "HighMessages" mit einem **SqlFilter**-Filter erstellt, der nur Nachrichten auswählt, deren benutzerdefinierte **messagenumber**-Eigenschaft größer ist als 3:

    bus_service.create_subscription('mytopic', 'HighMessages')

    rule = Rule()
    rule.filter_type = 'SqlFilter'
    rule.filter_expression = 'messagenumber > 3'

    bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
    bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)

Ebenso erstellt das folgende Beispiel ein Abonnement namens "LowMessages" mit einem **SqlFilter**-Filter, der nur Nachrichten auswählt, deren benutzerdefinierte **messagenumber**-Eigenschaft kleiner oder gleich 3 ist:

    bus_service.create_subscription('mytopic', 'LowMessages')

    rule = Rule()
    rule.filter_type = 'SqlFilter'
    rule.filter_expression = 'messagenumber <= 3'

    bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
    bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)

Wenn eine Nachricht an "mytopic" gesendet wird, wird diese nun stets an die Empfänger des Themenabonnements "AllMessages" zugestellt. Sie wird selektiv an die Empfänger der Themenabonnements "HighMessages" und "LowMessages" zugestellt (je nach Inhalt der Nachricht).

Senden von Nachrichten an ein Thema
-----------------------------------

Um eine Nachricht an ein Service Bus-Thema zu senden, muss die Anwendung die **send\_topic\_message**-Methode des **ServiceBusService**-Objekts verwenden.

Das folgende Beispiel zeigt, wie Sie fünf Testnachrichten an "mytopic" senden. Beachten Sie, dass der **messagenumber**-Eigenschaftswert jeder Nachricht gemäß der Iteration der Schleife variiert (dadurch wird bestimmt, welche Abonnements die Nachricht erhalten):

    for i in range(5):
        msg = Message('Msg ' + str(i), custom_properties={'messagenumber':i})
        bus_service.send_topic_message('mytopic', msg)

Service Bus-Themen unterstützen eine maximale Nachrichtengröße von 256 MB (der Header, der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 MB haben). Es gibt keine Beschränkung für die Anzahl der Nachrichten, die ein Thema enthält. Es gibt jedoch eine Obergrenze für die Gesamtgröße der Nachrichten eines Themas. Die Themengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

Empfangen von Nachrichten aus einem Abonnement
----------------------------------------------

Nachrichten werden von einem Abonnement über die **receive\_subscription\_message**-Methode auf dem **ServiceBusService**-Objekt empfangen:

    msg = bus_service.receive_subscription_message('mytopic', 'LowMessages')
    print(msg.body)

Nachrichten werden aus dem Abonnement gelöscht, wenn sie gelesen wurden. Sie können jedoch Nachrichten lesen (einen Blick darauf werfen) und sperren, ohne sie aus dem Abonnement zu löschen, indem Sie den optionalen Parameter **peek\_lock** auf **True** festlegen.

Das Standardverhalten für das Lesen und Löschen der Nachricht als Teil des Empfangsvorgangs ist das einfachste Modell. Es wird am besten für Szenarien eingesetzt, bei denen es eine Anwendung tolerieren kann, wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da Service Bus die Nachricht als verwendet markiert hat, wird er jene Nachricht auslassen, die vor dem Absturz verwendet wurde, wenn die Anwendung neu startet und erneut mit der Verwendung von Nachrichten beginnt.

Wenn der Parameter **peek\_lock** auf **True** festgelegt ist, wird der Empfangsvorgang zu einem zweistufigen Vorgang. Dadurch können Anwendungen unterstützt werden, die das Auslassen bzw. Fehlen von Nachrichten nicht zulassen können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann an die Anwendung zurück. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt Sie die zweite Phase des Empfangsprozesses durch Aufrufen der **delete**-Methode des **Message**-Objekts aus. Die **delete**-Methode markiert die Nachricht als verarbeitet, und entfernt sie aus dem Abonnement.

    msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
    print(msg.body)

    msg.delete()

Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten
-------------------------------------------------------------

Service Bus stellt Funktionen zur Verfügung, die Ihnen bei der ordnungsgemäßen Behebung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht helfen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, so kann sie die **unlock**-Methode zum **Message**-Objekt hinzufügen. Dies führt dazu, dass Service Bus die Nachricht innerhalb des Abonnements entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitenden Anwendung.

Zudem wird einer im Abonnement gesperrten Anwendung ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel wenn die Anwendung abstürzt), entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor Abrufen der **delete**-Methode abstürzt, wird die Nachricht wieder an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** (Verarbeitung mindestens einmal) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung in dem Szenario nicht zulässig ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Zustellung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der **MessageId**-Eigenschaft der Nachricht erzielt, die über mehrere Zustellungsversuche hinweg konstant bleibt.

Löschen von Themen und Abonnements
----------------------------------

Themen und Abonnements sind dauerhaft und müssen explizit über das Azure-Verwaltungsportal oder programmgesteuert gelöscht werden. Das Beispiel unten zeigt, wie Sie das Thema namens "mytopic" löschen:

    bus_service.delete_topic('mytopic')

Durch das Löschen eines Themas werden auch alle Abonnements gelöscht, die mit dem Thema registriert sind. Abonnements können auch unabhängig gelöscht werden. Der folgende Code zeigt, wie ein Abonnement namens "HighMessages" aus dem Thema "mytopic" gelöscht wird:

    bus_service.delete_subscription('mytopic', 'HighMessages')

Nächste Schritte
----------------

Nachdem Sie nun mit den Grundlagen der Service Bus-Themen vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Warteschlangen, Themen und Abonnements](http://msdn.microsoft.com/de-de/library/hh367516.aspx).
-   API-Referenz für [SqlFilter](http://msdn.microsoft.com/de-de/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx).
