<properties
   pageTitle="Business-to-Business-Zusammenarbeit (B2B) mit Azure Active Directory"
   description="Die Azure Active Directory B2B-Zusammenarbeit ermöglicht es Geschäftspartnern auf Ihre Unternehmensanwendungen zuzugreifen."
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="msStevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2015"
   ms.author="curtand"/>

# Azure Active Directory B2B-Zusammenarbeit

Mit der Azure Active Directory B2B-Zusammenarbeit können Sie den Zugriff auf Unternehmensanwendungen mit von Partnern verwalteten Identitäten aktivieren. Sie können unternehmensübergreifende Beziehungen erstellen, indem Sie Benutzer von Partnerunternehmen zum Zugriff auf Ihre Ressourcen einladen und autorisieren. Die Komplexität wird verringert, da sich jedes Unternehmen einmal mit Azure Active Directory (Azure AD) verbindet, und jeder Benutzer wird durch ein einzelnes Azure AD-Konto dargestellt. Die Sicherheit wird erhöht, da der Zugriff aufgehoben wird, wenn Partnerbenutzer ihre Unternehmen verlassen, und der unbeabsichtigte Zugriff über die Mitgliedschaft in internen Verzeichnissen wird verhindert. Für Geschäftspartner, die Azure AD noch nicht besitzen, hat die B2B-Zusammenarbeit einen optimierten Registrierungsprozess, um Ihren Geschäftspartnern Azure AD-Konten bereitzustellen.

-   Ihre Geschäftspartner verwenden ihre eigenen Anmeldeinformationen. Das erspart Ihnen das Verwalten eines externen Partnerverzeichnisses und das Entfernen des Zugriffs, wenn Benutzer das Partnerunternehmen verlassen.

-   Sie verwalten den Zugriff auf Ihre Apps unabhängig vom Kontolebenszyklus Ihrer Geschäftspartner. Dies bedeutet beispielsweise, dass Sie den Zugriff widerrufen können, ohne die IT-Abteilung Ihres Geschäftspartners um Hilfe zu bitten.

## Funktionen

Die B2B-Zusammenarbeit vereinfacht die Verwaltung und verbessert die Sicherheit des Partnerzugriffs auf Unternehmensressourcen, einschließlich SaaS-Apps wie Office 365, Salesforce, Azure-Dienste und jede mobile, Cloud- und lokale Anwendung, die Ansprüche unterstützt. Die B2B-Zusammenarbeit ermöglicht Partnern das Verwalten ihrer eigenen Konten, und Unternehmen können Sicherheitsrichtlinien auf den Partnerzugriff anwenden.

Die Azure Active Directory B2B-Zusammenarbeit ist leicht zu konfigurieren und vereinfacht über einen mit E-Mails überprüften Prozess die Registrierung für Partner aller Größen, selbst wenn sie nicht über ein eigenes Azure Active Directory verfügen. Die Verwaltung ist auch einfach, da keine externen Verzeichnisse oder Verbundkonfigurationen pro Partner erforderlich sind.

Prozess:

1. Mit der Azure AD B2B-Zusammenarbeit kann ein Unternehmensadministrator eine Reihe externer Benutzern durch Hochladen einer Datei mit durch Kommas getrennten Werten (CSV) mit nicht mehr als 2000 Zeilen in das Portal für die B2B-Zusammenarbeit einladen und autorisieren.

  ![Dialogfeld zum Hochladen der CSV-Datei](./media/active-directory-b2b-collaboration-overview/upload-csv.png)

2. Das Portal sendet E-Mail-Einladungen an diese externen Benutzer.

3. Der eingeladene Benutzer meldet sich entweder mit einem vorhandenen Arbeitskonto bei Microsoft (verwaltet in Azure AD) an oder erhält ein neues Arbeitskonto in Azure AD.

4. Nach der Anmeldung wird der Benutzer zur App umgeleitet, die für ihn freigegeben wurde.

Einladungen an E-Mail-Adressen von Privatbenutzern (wie Gmail oder [*comcast.net*](http://comcast.net/)) werden derzeit nicht unterstützt.

Weitere Informationen zur Funktionsweise der B2B-Zusammenarbeit erhalten Sie in [diesem Video](http://aka.ms/aadshowb2b).

## CSV-Dateiformat

Die CSV-Datei weist das folgende Format auf.

**Email:** E-Mail-Adresse für den eingeladenen Benutzer.<br/> **DisplayName:** Anzeigename für den eingeladenen Benutzer (in der Regel Vor- und Nachname).<br/> **InviteAppID:** Die ID für die Anwendung, die für das Branding der E-Mail-Einladung und der Annahmeseiten verwendet wird.<br/> **InviteReplyURL:** Die URL, an die ein eingeladener Benutzer nach der Annahme der Einladung weitergeleitet wird. Dies sollte eine unternehmensspezifische URL sein (z. B. [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/)).<br/> **InviteAppResources:** AppIDs, denen Anwendungen Benutzer zuweisen können. AppIDs können durch Aufrufen von `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`abgerufen werden.<br/> **InviteGroupResources:** ObjectIDs für Gruppen, denen Benutzer hinzugefügt werden können. ObjectIDs können durch Aufrufen von `Get-MsolGroup | fl DisplayName, ObjectId` abgerufen werden.<br/> **InviteContactUsUrl:** Kontakt-URL, die in E-Mail-Einladungen aufgenommen werden sollte, falls sich der eingeladene Benutzer an Ihr Unternehmen wenden möchte.<br/>

## Beispiel-CSV-Datei
Dies ist ein Beispiel für eine CSV-Datei, die Sie für Ihre Zwecke ändern können. Speichern Sie sie mit einem beliebigen Dateinamen, aber stellen Sie sicher, dass sie die Erweiterung "CSV" aufweist.

```
Email,DisplayName,InviteAppID,InviteReplyUrl,InviteAppResources,InviteGroupResources,InviteContactUsUrl
wharp@contoso.com,Walter Harp,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/<br/>
jsmith@contoso.com,Jeff Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/<br/>
bsmith@contoso.com,Ben Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
```

<!---HONumber=Sept15_HO3-->