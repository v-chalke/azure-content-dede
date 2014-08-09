<properties linkid="dev-net-service-bus-amqp-overview" urlDisplayName="Azure Notification Hubs" pageTitle="Azure Notification Hubs" metaKeywords="Azure push notifications, Azure notification hubs, Azure messaging" description="Learn how to use push notifications in Azure. Code samples written in C# using the .NET API." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="Azure Notification Hubs" authors="" />

Azure-Benachrichtigungshubs
===========================

Durch die Unterstützung von Pushbenachrichtigungen in Azure haben Sie Zugriff auf eine einfache, plattformübergreifende und skalierbare Infrastruktur, die die Verarbeitung von Pushbenachrichtigungen sowohl auf Privat- als auch auf Unternehmensanwendungen für mobile Plattformen erheblich erleichtert,

Was sind Pushbenachrichtigungen?Was sind Pushbenachrichtigungen?
----------------------------------------------------------------

Smartphones und Tablets können ihre Benutzer "benachrichtigen", wenn ein bestimmtes Ergebnis eingetreten ist. In Windows Store-Anwendungen kann eine solche Benachrichtigung zu einem *Toast* führen: Ein Fenster ohne Modus wird angezeigt, während mit einem Klang ein neuer Push signalisiert wird. Auf Apple iOS-Geräten erscheint der Push gleichzeitig mit einem Dialogfeld, in dem der Benutzer aufgefordert wird, die Benachrichtigung anzuzeigen oder zu schließen. Durch Klicken auf **Ansicht** wird die Anwendung angezeigt, die die Meldung erhalten hat.

Pushbenachrichtigungen dienen zur stromsparenden Anzeige aktueller Informationen auf Mobilgeräten. Pushbenachrichtigungen sind eine integrale Komponente für Privatanwender-Apps, in denen sie verwendet werden, um die Einbindung und Nutzung von Apps zu erhöhen. Benachrichtigungen sind auch für Unternehmen nützlich, um die Mitarbeiter besser über aktuelle geschäftliche Ereignisse zu informieren.

Hier einige spezielle Beispiele für Szenarios mit mobilen Einbindungen:

1.  Aktualisierung einer Kachel in Windows 8 oder Windows Phone mit aktuellen Finanzinformationen.
2.  Benachrichtigung eines Benutzers einer Workflow-App in einem Unternehmen, dass ihm eine bestimmte Arbeitsaufgabe zugewiesen wurde, mit einem Toast.
3.  Anzeige eines Badges mit der Anzahl aktueller Verkäufe in einer CRM-App (wie z. B. Microsoft Dynamics CRM).

Die Funktionsweise von PushbenachrichtigungenDie Funktionsweise von Pushbenachrichtigungen
------------------------------------------------------------------------------------------

Pushbenachrichtigungen werden über plattformspezifische Infrastrukturen ausgegeben, die *Platform Notification Systems* (PNS) genannt werden. PNS bieten Barebone-Funktionen (d. h. keine Broadcast-Unterstützung und keine Personalisierung) und haben keine gemeinsame Schnittstelle. Wenn ein Entwickler z. B. eine Benachrichtigung an eine Windows Store-App senden möchte, muss er Kontakt mit dem WNS (Windows Notification Service) aufnehmen. Um diese Nachricht dann noch an ein iOS-Gerät zu senden, muss er Kontakt mit dem APNS (Apple Push Notification Service) aufnehmen und die Nachricht ein zweites Mal senden.

Von einer höheren Ebene betrachtet folgen alle Plattform-Benachrichtigungssysteme demselben Muster:

1.  Die Client-App nimmt Kontakt mit dem PNS auf, um einen *Handle* abzurufen. Der Typ des Handles hängt vom System ab. Beim WNS handelt es sich um einen URI oder "Benachrichtigungskanal". Beim APNS handelt es sich um Token.
2.  Die Client-App speichert diesen Handle zur späteren Verwendung im *Back-End* der App. Beim WNS ist das Back-End normalerweise ein Clouddienst. In Apple wird das System *Anbieter* genannt.
3.  Um eine Pushbenachrichtigung zu senden, nimmt das Back-End der App über den Handle Kontakt mit dem PNS auf, um eine bestimmte Instanz der Client-App anzuzielen.
4.  Das PNS leitet die Benachrichtigung an das vom Handle angegebene Gerät weiter.

![](./media/notification-hubs-overview/SBPushNotifications1.gif)

Die Herausforderungen bei PushbenachrichtigungenDie Herausforderungen bei Pushbenachrichtigungen
------------------------------------------------------------------------------------------------

Diese Systeme sind zwar sehr leistungsstark, aber der App-Entwickler hat immer noch sehr viel Arbeit, selbst wenn er nur allgemeine Pushbenachrichtigungs-Szenarios wie Broadcast-Übertragungen oder das Senden von Pushbenachrichtigungen an einen Benutzer verwirklichen möchte.

Pushbenachrichtigungen gehören zu den am häufigsten nachgefragten Funktionen in Clouddiensten für mobile Apps. Der Grund dafür ist, dass die zu ihrer Funktion erforderliche Infrastruktur ziemlich komplex ist und mit der Hauptgeschäftslogik der App oft ziemlich wenig zu tun hat. Folgende Herausforderungen stellen sich beim Aufbau einer bedarfsgesteuerten Push-Infrastruktur:

-   **Plattformabhängigkeit.** Um Benachrichtigungen an Geräte auf verschiedenen Plattformen zu senden, müssen im Back-End mehrere Schnittstellen kodiert werden. Nicht nur die kleinen Details sind verschieden, sondern die Darstellung der Benachrichtigung (als Kachel, Toast oder Badge) ist auch plattformabhängig. Die Unterschiede können zu komplexen und schwer zu verwaltenden Back-End-Codes führen.

-   **Skalierung.** Bei der Skalierung dieser Infrastruktur sind zwei Aspekte zu berücksichtigen:
1.  Nach PNS-Richtlinien müssen Gerätetokens bei jedem Start einer App aktualisiert werden. Dies führt zu großen Datenverkehrsmengen (und damit vielen Datenbankzugriffen), nur um die Gerätetokens auf dem neuesten Stand zu halten. Wenn die Anzahl der Geräte wächst (möglicherweise in den Millionenbereich), sind die Kosten der Erstellung und Wartung dieser Infrastruktur beträchtlich.
2.  Die meisten PNS unterstützten keine Broadcast-Übertragung an mehrere Geräte. Daraus folgt, dass ein Broadcast an Millionen von Geräten zu Millionen von Anrufen beim PNS führt. Die Skalierung dieser Anfragen ist kein geringes Problem, da App-Entwickler normalerweise die Gesamtlatenz niedrig halten möchten. (Das heißt, das z. B. das letzte Gerät, das eine Nachricht erhält, diese Nachricht nicht 30 Minuten nach dem Absenden erhalten sollte, da dies aus mehreren Gründen dem Zweck der Pushbenachrichtigungen zuwiderläuft.)
-   **Arbeitsplan.** PNS ermöglichen das Senden einer Nachricht an ein Gerät. Allerdings werden Benachrichtigungen in den meisten Apps auf Benutzer und/oder Interessengruppen gezielt (z. B. alle Mitarbeiter, die einem bestimmten Kundenkonto zugeordnet sind.) Um die Benachrichtigungen an die korrekten Geräte zu senden, muss nun das Back-End der App eine Registrierung führen, die die Interessengruppen den Geräte-Tokens zuordnet. Dieser Aufwand kommt noch zur Markteinführungszeit und zu den Wartungskosten einer App hinzu.

Warum Notification Hubs verwenden?Warum Notification Hubs verwenden?
--------------------------------------------------------------------

Benachrichtigungshubs bieten eine benutzerfreundliche Pushbenachrichtigungs-Infrastruktur, die Folgendes unterstützt:

-   **Mehrere Plattformen.** Benachrichtigungshubs bieten eine gemeinsame Schnittstelle zum Senden von Benachrichtigungen an alle unterstützten Plattformen. Dieses App-Back-End kann Benachrichtigungen in plattformspezifischen oder plattformunabhängigen Formaten senden. Benachrichtigungshubs können Pushbenachrichtigungen an Windows Store-, iOS-, Android- und Windows Phone-Apps senden.
-   **Pub/Sub-Routing.** Jedes Gerät kann beim Senden seines Handles an einen Benachrichtigungshub ein oder mehrere *Tags* spezifizieren. Weitere Informationen über Tags erhalten Sie im folgenden Abschnitt. Tags müssen nicht vorab bereitgestellt oder entfernt werden. Tags bieten einen einfachen Weg, um Benachrichtigungen an Benutzer oder Interessengruppen zu senden. Da Tags jede beliebige App-spezifische Kennung (wie Benutzer- oder Gruppen-IDs) enthalten können, befreien Sie das App-Back-End von der Last, Geräte-Handles speichern und verwalten zu müssen.
-   **Skalierung.** Benachrichtigungshubs lassen sich für Millionen von Geräten skalieren, ohne dass die Architektur umgebaut oder partitioniert werden muss.

Benachrichtigungshubs verwenden eine vollständige plattformübergreifende und skalierte Infrastruktur für Pushbenachrichtigungen und reduzieren den Push-spezifischen Code, der im App-Back-End läuft, beträchtlich. Benachrichtigungshubs bieten die gesamte Funktionalität einer Push-Infrastruktur. Die Geräte sind nur für die Registrierung ihrer PNS-Handles verantwortlich, während das Back-End plattformunabhängige Nachrichten an Benutzer oder Interessengruppen sendet.

![](./media/notification-hubs-overview/SBPushNotifications2.gif)
