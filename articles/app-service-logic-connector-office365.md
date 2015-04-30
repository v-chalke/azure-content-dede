﻿<properties 
   pageTitle="Office 365-Connector" 
   description="Verwenden des Office 365-Connectors" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="sutalasi" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="sutalasi"/>


# Mithilfe des Office 365-Connectors in Ihrer Logik-App#

Logik-Apps können basierend auf einer Vielzahl von Datenquellen auslösen und Connectors das Abrufen und Verarbeiten von Daten im Rahmen des Datenflusses ermöglichen. Mit dem Office 365-Connector können Sie E-Mails senden und empfangen sowie Kalender und Kontakte in Ihrem Office 365-Konto verwalten. Sie können verschiedene Aktionen wie z. B. das Senden und Empfangen von E-Mails, das Erstellen und Löschen von Ereignissen in Ihrem Kalender sowie das Erstellen, Aktualisieren und Löschen von Kontakten durchführen.

**Grundlegende Aktionen**

- Neue E-Mail (Trigger)
- E-Mail senden
- E-Mail beantworten
- Ereignis senden
- Kontakt hinzufügen


## Erstellen einer Logik-App##
Zum Verwenden eines Office 365-Connectors in Ihrer Logik-App sollte die Connector-API-App bereits erstellt worden sein. Alternativ dazu können Sie sie dynamisch in der Logik-App erstellen. Wir erstellen eine einfache Logik-App, die ausgelöst wird, sobald eine E-Mail (an Ihre E-Mail-ID für Vertriebsanfragen - z. B. sales@contoso.com) eingeht. Zudem wird ein Ereignis erstellt, ein Kontakt mit den Absenderdetails hinzugefügt, eine E-Mail an Ihr persönliches Konto gesendet und schließlich eine Antwort mit einer Bestätigung gesendet.

1.	Melden Sie sich beim Azure-Portal an, und klicken Sie auf "Neu -> Web + Mobil -> Logik-App"

	![][1]

2.	Stellen Sie auf der Seite "Logik-App erstellen" die erforderlichen Details wie Name, App Service-Plan und -Ort bereit.

	![][2]

3.	Klicken Sie auf "Trigger und Aktionen". Der Bildschirm mit dem Logik-App-Editor wird angezeigt.

	![][3]

4.	Erweitern Sie "Neu erstellen" in der Galerie, um alle verfügbaren API-Apps anzuzeigen. Wählen Sie in der Galerie "Office 365" aus. Der "Office 365-Trigger" wird dem Datenfluss hinzugefügt.
	![][4]

5.	Dadurch wird eine neue API-App "Office 365-Connector" in derselben Ressourcengruppe wie die Logik-App erstellt. Es dauert ungefähr 30 Sekunden, bis die API-App erstellt ist.

6.	Nachdem die App erstellt wurde, klicken Sie auf "Autorisieren", um Office 365-Anmeldeinformationen bereitzustellen.

	![][5]

7.	Sie werden zur Office 365-Anmeldeseite umgeleitet und können sich mit Ihren Office 365-Anmeldeinformationen authentifizieren.

	![][6]

	![][7]

8.	Sobald die Autorisierung abgeschlossen ist, werden die Office 365-Trigger angezeigt.

	![][8]

9.	Wählen Sie den Trigger "Neue E-Mail", und die Eingabeparameter werden angezeigt.


10.	Ändern Sie die Häufigkeit des Triggers in 'Minutes', und klicken Sie auf ✓. 

	![][9]

11. Der Office 365-Trigger 'New Email' wird konfiguriert, und Sie können die Ausgabeparameter ebenfalls anzeigen.
	
	![][10]

12.	Wählen Sie in der Galerie im Abschnitt "Zuletzt verwendet" den Eintrag "Office 365-Connector". Eine neue "Office 365"-Aktion wird hinzugefügt.

13.	Wählen Sie "Ereignis senden" aus der Liste der Aktionen, und die Eingabeparameter der Aktion "Ereignis senden" werden angezeigt.

	![][11]

14.	Geben Sie die Ereignisdetails an, und klicken Sie auf ✓.

	![][12]

15.	Wählen Sie in der Galerie im Abschnitt "Zuletzt verwendet" den Eintrag "Office 365-Connector". Eine neue "Office 365"-Aktion wird hinzugefügt.

16.	Wählen Sie "Kontakt hinzufügen" aus der Liste der Aktionen, und die Eingabeparameter der Aktion "Kontakt hinzufügen" werden angezeigt.

	![][13]

17.	Klicken Sie neben dem Feld 'Email Address' auf "+", und wählen Sie den Wert des Ausgabefelds 'From' aus dem Trigger aus.

	![][14]

18. Klicken Sie auf ✓, und die Aktionskonfiguration ist abgeschlossen.

	![][15]

19.	Wählen Sie in der Galerie im Abschnitt "Zuletzt verwendet" den Eintrag "Office 365-Connector". Eine neue "Office 365"-Aktion wird hinzugefügt.


20.	Wählen Sie "E-Mail senden" aus der Liste der Aktionen, und die Eingabeparameter der Aktion "E-Mail senden" werden angezeigt.

	![][19]

21.	Geben Sie die Details an, die zum Senden der E-Mail erforderlich sind. Sie können eine Meldung erstellen, indem Sie zum Beispiel Folgendes eingeben. Sobald die 'Send Email'-Aktion konfiguriert ist, klicken Sie auf ✓.

		Body - @concat('You got a new sales enquiry from',triggers().output.body.From)

	![][20]
22.	Wählen Sie in der Galerie im Abschnitt "Zuletzt verwendet" den Eintrag "Office 365-Connector". Eine neue "Office 365"-Aktion wird hinzugefügt.


23.	Wählen Sie "Antworten an" aus der Liste der Aktionen, und die Eingabeparameter der Aktion "Antworten an" werden angezeigt.

	![][16]

24.	Klicken Sie neben dem 'From'-Feld auf "+", und wählen Sie den Wert aus der Ausgabenachricht-ID des Triggers, und klicken Sie auf ✓.

	![][17]

25. Klicken Sie im Bildschirm des Logik-App-Editors auf "OK", und klicken Sie dann auf 'Create'. Es dauert ungefähr 30 Sekunden, bis die Erstellung abgeschlossen ist.

26. Senden Sie eine E-Mail an das Konto, mit dem Sie den Trigger konfiguriert haben. Sie sollten eine E-Mail in Ihrem persönlichen E-Mail-Konto vorfinden sowie ein Ereignis und einen Kontakt in Ihrem Büro-E-Mail-Konto. Darüber hinaus sollten Sie eine Antwort zur Bestätigung erhalten, dass die Vertriebsanfrage in Kürze beantwortet wird.

<!--Image references-->
[1]: ./media/app-service-logic-connector-office365/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-office365/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-office365/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-office365/4_Select_Office365_Gallery.png
[5]: ./media/app-service-logic-connector-office365/5_Office365_Authorize.png
[6]: ./media/app-service-logic-connector-office365/6_Office365_Login.png
[7]: ./media/app-service-logic-connector-office365/7_Office365_User_Consent.png
[8]: ./media/app-service-logic-connector-office365/8_Office365_Trigger.png
[9]: ./media/app-service-logic-connector-office365/9_Office365_Trigger_Settings.png
[10]: ./media/app-service-logic-connector-office365/10_Office365_Trigger_Configured.png
[11]: ./media/app-service-logic-connector-office365/11_Office365_Actions_List.png
[12]: ./media/app-service-logic-connector-office365/12_Office365_Create_Event_Inputs.png
[13]: ./media/app-service-logic-connector-office365/13_Office365_Add_Contact_Inputs.png
[14]: ./media/app-service-logic-connector-office365/14_Office365_Add_Contact_Email_FromTrigger.png
[15]: ./media/app-service-logic-connector-office365/15_Office365_Add_Contacts_Configured.png
[16]: ./media/app-service-logic-connector-office365/16_Office365_Reply_To_Inputs.png
[17]: ./media/app-service-logic-connector-office365/17_Office365_Reply_To_MessageId.png
[18]: ./media/app-service-logic-connector-office365/18_Office365_Reply_To_Configured.png
[19]: ./media/app-service-logic-connector-office365/19_Office365_Send_Inputs.png
[20]: ./media/app-service-logic-connector-office365/20_Office365_Send_Configured.png


<!--HONumber=49-->