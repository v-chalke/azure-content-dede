<properties 
	pageTitle="Verwenden der Service Bus-Themen (Node.js) - Azure" 
	description="Erfahren Sie mehr zur Verwendung von Service Bus-Themen und -Abonnements in Azure. Die Codebeispiele wurden für Node.js-Anwendungen geschrieben." 
	services="service-bus" 
	documentationCenter="nodejs" 
	authors="sethmanheim" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="sethm"/>






# Verwenden von Service Bus-Themen und -Abonnements

In diesem Leitfaden erfahren Sie, wie Sie Service Bus-Themen und -Abonnements über Node.js-Anwendungen verwenden. Die behandelten Szenarien umfassen das **Erstellen von Themen und Abonnements, Erstellen von Abonnementfiltern, Senden von Nachrichten** an ein Thema, das **Empfangen von Nachrichten von einem Abonnement** und das **Löschen von Themen und Abonnements**. Weitere Informationen zu Themen und Abonnements finden Sie im Abschnitt [Nächste Schritte][].

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## Erstellen einer Node.js-Anwendung

Erstellen Sie eine leere Node.js-Anwendung. Hinweise zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website], [Node.js-Cloud-Dienst][Node.js-Cloud-Dienst] (mithilfe von Windows PowerShell) oder auf der [Website mit WebMatrix].

## Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus

Um Service Bus zu verwenden, laden Sie das Node.js-Azure-Paket herunter. Dieses Paket enthält eine Reihe von Bibliotheken, die mit den Service Bus-REST-Diensten kommunizieren.

### Verwenden von Node-Paket-Manager (NPM) zum Beziehen des Pakets

1.  Verwenden Sie eine Befehlszeilenschnittstelle, z. B. **PowerShell** (Windows,) **Terminal** (Mac) oder **Bash** (Unix), und navigieren Sie zu dem Ordner, in dem Sie die Beispielanwendung erstellt haben.

2.  Geben Sie **npm install azure** in das Befehlsfenster ein.
    Die Ausgabe dieses Befehls sollte wie folgt aussehen:

         azure@0.7.5 node_modules\azure
		├── dateformat@1.0.2-1.2.3
		├── xmlbuilder@0.4.2
		├── node-uuid@1.2.0
		├── mime@1.2.9
		├── underscore@1.4.4
		├── validator@1.1.1
		├── tunnel@0.0.2
		├── wns@0.5.3
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  Sie können den Befehl **ls** manuell ausführen, um sich davon zu überzeugen, dass der Ordner **node_modules** erstellt wurde. In diesem Ordner finden Sie ein **azure**-Paket, das die für den Zugriff auf Service Bus-Themen benötigten Bibliotheken enthält.

### Importieren des Moduls

Verwenden Sie Editor oder einen anderen Texteditor, um die folgende Zeile am Anfang der Datei **server.js** der Anwendung einzufügen:

    var azure = require('azure');

### Einrichten einer Service Bus-Verbindung

Das Azure-Modul entnimmt den Umgebungsvariablen "AZURE_SERVICEBUS_NAMESPACE" und "AZURE_SERVICEBUS_ACCESS_KEY" die Informationen, die zum Herstellen einer Verbindung mit Azure Service Bus benötigt werden. Wenn diese Umgebungsvariablen nicht festgelegt wurden, müssen Sie die Kontoinformationen beim Aufruf von **createServiceBusService** angeben.

Ein Beispiel zum Festlegen der Umgebungsvariablen in einer Konfigurationsdatei für einen Azure-Cloud-Dienst finden Sie unter [Node.js-Cloud-Dienst mit Speicher].

Ein Beispiel zum Festlegen der Umgebungsvariablen im Verwaltungsportal für eine Azure-Website finden Sie unter [Node.js-Webanwendung mit Speicher].

## Erstellen eines Themas

Das **ServiceBusService**-Objekt ermöglicht Ihnen das Arbeiten mit Themen. Der folgende Code erstellt ein **ServiceBusService**-Objekt. Fügen Sie ihn am Anfang der Datei **server.js** hinzu, nach der Anweisung zum Importieren des Azure-Moduls:

    var serviceBusService = azure.createServiceBusService();

Durch das Aufrufen von **createTopicIfNotExists** auf dem **ServiceBusService**-Objekt wird das angegebene Thema zurückgegeben (falls vorhanden), oder es wird ein neues Thema mit dem angegebenen Namen erstellt. Der folgende Code nutzt
**createTopicIfNotExists** zur Erstellung oder zum Herstellen einer Verbindung mit dem Thema mit der Bezeichnung
"MyTopic":

    serviceBusService.createTopicIfNotExists('MyTopic',function(error){
        if(!error){
            // Topic was created or exists
            console.log('topic created or exists.');
        }
    });

**createServiceBusService** unterstützt zudem weitere Optionen, mit denen Sie die Standardthemeneinstellungen überschreiben können, wie zum Beispiel die Nachrichtenlebensdauer oder maximale Themengröße. Das folgende Beispiel zeigt, wie Sie die maximale Themengröße auf 5 GB bei einer Lebensdauer von 1 Minute festlegen:

    var topicOptions = {
            MaxSizeInMegabytes: '5120',
            DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
        if(!error){
            // topic was created or exists
        }
    });

### Filter

Mithilfe von **ServiceBusService** können Sie optionale Filtervorgänge auf Vorgänge anwenden. Filtervorgänge können Protokollierung, automatische Wiederholungsversuche usw. umfassen. Filter sind Objekte, die eine Methode mit einer Signatur implementieren:

		function handle (requestOptions, next)

Nachdem die Vorverarbeitung der Anforderungsoptionen angeschlossen ist, muss die Methode "next" aufrufen und hierbei eine Rückruffunktion mit der folgenden Signatur übergeben:

		function (returnObject, finalCallback, next)

Nachdem das returnObject-Objekt (die Antwort auf die an den Server gesendete Anforderung) verarbeitet wurde, muss in dieser Rückruffunktion entweder "next" aufgerufen werden, wenn die Tabelle vorhanden ist, um weitere Filter zu verarbeiten, oder andernfalls einfach "finalCallback" aufrufen, um den Dienstaufruf zu beenden.

Zwei Filter, die eine Wiederholungslogik implementieren, sind im Azure SDK für Node.js enthalten: **ExponentialRetryPolicyFilter** und **LinearRetryPolicyFilter**. Der folgende Code erstellt ein **ServiceBusService**-Objekt, das den **ExponentialRetryPolicyFilter** verwendet:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## Erstellen von Abonnements

Themenabonnements werden ebenfalls mit dem **ServiceBusService**-Objekt erstellt. Abonnements werden benannt und können einen optionalen Filter aufweisen, der die Nachrichten einschränkt, die an die virtuelle Warteschlange des Abonnements übermittelt werden.

> [AZURE.NOTE] Abonnements sind persistent und bleiben erhalten, bis sie selbst oder die mit ihnen verbundenen Themen gelöscht werden. Wenn Ihre Anwendung Logik beinhaltet, sollte sie bei der Erstellung eines Abonnements zuerst überprüfen, ob das Abonnement bereits vorhanden ist, indem die Methode
**Methode getSubscription** zu verwenden.

### Erstellen eines Abonnements mit dem Standardfilter (MatchAll)

**MatchAll** ist der Standardfilter, der verwendet wird, wenn beim Erstellen eines neuen Abonnements kein Filter angegeben wird. Bei Verwendung des Filters **MatchAll** werden alle für das Thema veröffentlichten Nachrichten in die virtuelle Warteschlange des Abonnements gestellt. Mit dem folgenden Beispiel werden ein Abonnement namens 'AllMessages' erstellt und der Standardfilter **MatchAll** verwendet.

    serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
        if(!error){
            // subscription created
        }
    });

### Erstellen von Abonnements mit Filtern

Sie können auch Filter erstellen, durch die Sie angeben können, welche an ein Thema gesendeten Nachrichten in einem bestimmten Themenabonnement angezeigt werden sollen.

Der von Abonnements unterstützte flexibelste Filtertyp ist
**SqlFilter**, der eine Teilmenge von SQL92 implementiert. SQL-Filter werden auf die Eigenschaften der Nachrichten angewendet, die für das Thema veröffentlicht werden. Weitere Informationen zu den Ausdrücken, die mit einem SQL-Filter verwendet werden können, finden Sie in der Syntax zu [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Sie können einem Abonnement mithilfe der Methode **createRule** des **ServiceBusService**-Objekts Filter hinzufügen. Mithilfe dieser Methode können Sie einem vorhandenen Abonnement neue Filter hinzufügen.

> [AZURE.NOTE]

> Da der Standardfilter automatisch auf alle neuen Abonnements angewendet wird, müssen Sie zuerst den Standardfilter entfernen, oder
<strong>"MatchAll"</strong> setzt alle anderen Filter außer Kraft, die Sie ggf. angeben. Sie können die Standardregel entfernen, indem Sie die Methode <strong>"deleteRule"</strong> des
<strong>ServiceBusService-Objekts</strong> verwenden.

Im folgenden Beispiel wird ein Abonnement namens 'HighMessages' mit einem
**SqlFilter**, der nur Nachrichten auswählt, die über eine benutzerdefinierte
**messagenumber**-Eigenschaft größer als 3 verfügen:

    serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
        if(!error){
            // subscription created
            rule.create();
        }
    });
    var rule={
        deleteDefault: function(){
            serviceBusService.deleteRule('MyTopic',
                'HighMessages', 
                azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
                rule.handleError);
        },
        create: function(){
            var ruleOptions = {
                sqlExpressionFilter: 'messagenumber > 3'
            };
            rule.deleteDefault();
            serviceBusService.createRule('MyTopic', 
                'HighMessages', 
                'HighMessageFilter', 
                ruleOptions, 
                rule.handleError);
        },
        handleError: function(error){
            if(error){
                console.log(error)
            }
        }
    }

Ebenso erstellt das folgende Beispiel ein Abonnement namens 'LowMessages' mit einem **SqlFilter**-Filter, der nur Nachrichten auswählt, deren benutzerdefinierte **messagenumber**-Eigenschaft kleiner oder gleich 3 ist:

    serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
        if(!error){
            // subscription created
            rule.create();
        }
    });
    var rule={
        deleteDefault: function(){
            serviceBusService.deleteRule('MyTopic',
                'LowMessages', 
                azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
                rule.handleError);
        },
        create: function(){
            var ruleOptions = {
                sqlExpressionFilter: 'messagenumber <= 3'
            };
            rule.deleteDefault();
            serviceBusService.createRule('MyTopic', 
                'LowMessages', 
                'LowMessageFilter', 
                ruleOptions, 
                rule.handleError);
        },
        handleError: function(error){
            if(error){
                console.log(error)
            }
        }
    }

Wenn eine Nachricht nun an 'MyTopic' gesendet wird, wird sie immer an die Empfänger gesendet, die das 'AllMessages'-Themenabonnement abonniert haben, und selektiv an die Empfänger, die das 'HighMessages'- und
"LowMessages"-Themenabonnement abonniert haben (dies hängt vom Inhalt der Nachricht ab).

## Senden von Nachrichten an ein Thema

Um eine Nachricht an ein Service Bus-Thema zu senden, muss Ihre Anwendung die **sendTopicMessage**-Methode für das **ServiceBusService**-Objekt verwenden. Nachrichten, die an Service Bus-Themen gesendet werden, sind **BrokeredMessage**-Objekte.
**BrokeredMessage**-Objekte verfügen über eine Reihe von Standardeigenschaften (z. B. **Label** und **TimeToLive**), ein Wörterbuch, das für benutzerdefinierte, anwendungsspezifische Eigenschaften verwendet wird, und einen Objektkörper mit Zeichenfolgendaten. Eine Anwendung kann den Nachrichtentext festlegen, indem sie einen Zeichenfolgenwert an **sendTopicMessage** übergibt. Erforderliche Standardeigenschaften werden mit den Standardwerten aufgefüllt.

Das folgende Beispiel zeigt, wie Sie fünf Testnachrichten an "MyTopic" senden: Beachten Sie, dass der Eigenschaftenwert **messagenumber** jeder Nachricht gemäß der Iteration der Schleife variiert (auf diese Weise wird bestimmt, welche Abonnements die Nachricht erhalten):

    var message = {
        body: '',
        customProperties: {
            messagenumber: 0
        }
    }

    for (i = 0;i < 5;i++) {
        message.customProperties.messagenumber=i;
        message.body='This is Message #'+i;
        serviceBusService.sendTopicMessage(topic, message, function(error) {
          if (error) {
            console.log(error);
          }
        });
    }

Service Bus-Themen unterstützen eine maximale Nachrichtengröße von 256 MB (der Header, der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, darf maximal 64 MB groß sein). Die Anzahl der Nachrichten, die ein Thema enthält, ist nicht eingeschränkt. Es gilt jedoch ein Grenzwert für die Gesamtgröße der Nachrichten in einem Thema. Die Themengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

## Empfangen von Nachrichten aus einem Abonnement

Nachrichten werden von einem Abonnement über die
**receiveSubscriptionMessage**-Methode für das **ServiceBusService**-Objekt empfangen. Nachrichten werden standardmäßig aus dem Abonnement gelöscht, wenn sie gelesen wurden. Sie können jedoch Nachrichten lesen (Peek) und sperren, ohne sie aus dem Abonnement zu löschen, indem Sie den optionalen Parameter **isPeekLock** auf **true** festlegen.

Das Standardverhalten für das Lesen und Löschen der Nachricht als Teil des Empfangsvorgangs ist das einfachste Modell. Es wird am besten für Szenarien eingesetzt, bei denen es eine Anwendung tolerieren kann, wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Stellen Sie sich zum besseren Verständnis ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wurde. Da Service Bus die Nachricht als verwendet markiert hat, wird diese Nachricht verpasst, die vor dem Absturz verwendet wurde, wenn die Anwendung neu gestartet wird und erneut mit der Verwendung von Nachrichten beginnt.

Wenn der Parameter **isPeekLock** auf **true** festgelegt ist, wird der Empfangsvorgang zu einem zweistufigen Vorgang. Auf diese Weise können Anwendungen unterstützt werden, die das Verpassen bzw. Fehlen von Nachrichten nicht zulassen können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann zurück an die Anwendung.
Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt sie die zweite Phase des Empfangsvorgangs durch Aufrufen der Methode **deleteMessage** und Bereitstellen der zu löschenden Nachricht als Parameter aus. Die Methode **deleteMessage** markiert die Nachricht als verarbeitet und entfernt sie aus dem Abonnement.

Das Beispiel unten zeigt, wie Nachrichten mithilfe von **receiveSubscriptionMessage** empfangen und verarbeitet werden können. Im Beispiel wird zuerst eine Nachricht des 'LowMessages'-Abonnements empfangen und gelöscht und anschließend eine Nachricht des 'HighMessages'-Abonnements empfangen, wobei für
**isPeekLock** "true" festgelegt ist. Anschließend wird die Nachricht durch
**deleteMessage** gelöscht:

    serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
        if(!error){
            // Message received and deleted
            console.log(receivedMessage);
        }
    });
    serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
        if(!error){
            // Message received and locked
            console.log(lockedMessage);
            serviceBusService.deleteMessage(lockedMessage, function (deleteError){
                if(!deleteError){
                    // Message deleted
                    console.log('message has been deleted.');
                }
            }
        }
    });

## Behandeln von Anwendungsabstürzen und nicht lesbaren Nachrichten

Service Bus stellt Funktionen zur Verfügung, die Sie bei der ordnungsgemäßen Behandlung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht unterstützen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, kann sie die Methode **unlockMessage** dem **ServiceBusService**-Objekt hinzufügen. Dies führt dazu, dass Service Bus die Nachricht innerhalb des Abonnements entsperrt und zur Verfügung stellt, damit sie erneut empfangen werden kann - durch die gleiche verarbeitende Anwendung oder durch eine andere verarbeitenden Anwendung.

Zudem wird einer im Abonnement gesperrten Nachricht ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel, wenn die Anwendung abstürzt), entsperrt
Service Bus die Nachricht automatisch und macht sie verfügbar, damit sie erneut empfangen werden kann.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor dem Aufruf der Methode **deleteMessage** abstürzt, wird die Nachricht erneut an die Anwendung übermittelt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** (mindestens einmalige Verarbeitung) bezeichnet und bedeutet, dass jede Nachricht mindestens ein Mal verarbeitet wird, unter bestimmten Umständen jedoch ggf. auch erneut übermittelt wird. Wenn eine doppelte Verarbeitung im betreffenden Szenario nicht geeignet ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Übermittlung doppelter Nachrichten hinzufügen. Dies erfolgt häufig mit der
**MessageId**-Eigenschaft der Nachricht, die über mehrere Übermittlungsversuche hinweg konstant bleibt.

## Löschen von Themen und Abonnements

Themen und Abonnements sind dauerhaft und müssen explizit über das Azure-Verwaltungsportal oder programmgesteuert gelöscht werden.
Das folgende Beispiel zeigt, wie Sie das Thema namens 'MyTopic' löschen:

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

Durch das Löschen eines Themas werden auch alle Abonnements gelöscht, die mit dem Thema registriert sind. Abonnements können auch unabhängig gelöscht werden. Der folgende Code veranschaulicht, wie ein Abonnement namens
"HighMessages" aus dem Thema 'MyTopic' gelöscht wird:

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

## Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Servicebus-Themen vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Warteschlangen, Themen und Abonnements][].
-   API-Referenz zu [SqlFilter][].
-   Besuchen Sie das [Azure SDK für Node]-Repository auf GitHub.

  [Azure SDK für Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Nächste Schritte]: #nextsteps
  [Was sind Service Bus-Themen und -Abonnements?]: #what-are-service-bus-topics
  [Erstellen eines Dienstnamespaces]: #create-a-service-namespace
  [Abrufen der Standard-Anmeldeinformationen für den Namespace]: #obtain-default-credentials
  [Erstellen einer Node.js-Anwendung]: #Create_a_Nodejs_Application
  [Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [Gewusst wie: Erstellen eines Themas]: #How_to_Create_a_Topic
  [Gewusst wie: Erstellen von Abonnements]: #How_to_Create_Subscriptions
  [Gewusst wie: Senden von Nachrichten an ein Thema]: #How_to_Send_Messages_to_a_Topic
  [Gewusst wie: Empfangen von Nachrichten aus einem Abonnement]: #How_to_Receive_Messages_from_a_Subscription
  [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [Gewusst wie: Löschen von Themen und Abonnements]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Warteschlangen, Themen und Abonnements]: http://msdn.microsoft.com/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Website mit WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js-Cloud-Dienst]: /documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js-Cloud-Dienst mit Speicher]: /develop/nodejs/tutorials/web-app-with-storage/
  [Node.js-Webanwendung mit Speicher]: /develop/nodejs/tutorials/web-site-with-storage/

<!--HONumber=47-->
 