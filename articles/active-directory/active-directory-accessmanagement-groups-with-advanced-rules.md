
<properties
	pageTitle="Verwenden von Attributen zum Erstellen erweiterter Regeln | Microsoft Azure"
	description="Vorgehensweisen zum Erstellen erweiterter Regeln für eine Gruppe, einschließlich unterstützter Ausdrucksregeloperatoren und -parameter."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="curtand"/>


# Verwenden von Attributen zum Erstellen erweiterter Regeln
Das Azure-Verwaltungsportal bietet Ihnen die notwendige Flexibilität, um erweiterte Regeln einzurichten, mit denen Sie dynamische Mitgliedschaften für Gruppen aktivieren können.

**Erstellen der erweiterten Regel** Wählen Sie im Azure-Portal auf der Registerkarte **Konfigurieren** für die Gruppe das Optionsfeld **Erweiterte Regel** aus, und geben Sie die erweiterte Regel in das vorgesehene Textfeld ein. Sie können die erweiterte Regel mit den folgenden Informationen erstellen.

## Erstellen des Texts einer erweiterten Regel
Die erweiterte Regel, die Sie für die dynamischen Mitgliedschaften für Gruppen erstellen können, ist im Wesentlichen ein binärer Ausdruck, der aus drei Teilen besteht und ein wahres oder falsches Ergebnis liefert. Die drei Teile sind folgende:

- Linker Parameter
- Binärer Operator
- Rechte Konstante

Eine vollständige erweiterte Regel sieht etwa wie folgt aus: (leftParameter binaryOperator "RightConstant"). Für den gesamten Ausdruck sind öffnende und schließende Klammern erforderlich, die rechte Konstante muss in doppelte gerade Anführungszeichen eingeschlossen werden, und die Syntax für den linken Parameter lautet "user.property". Eine erweiterte Regel kann aus mehreren binären Ausdrücke bestehen, die durch die logischen Operatoren "-and", "-or" und "-not" getrennt sind. Hier finden Sie Beispiele für eine richtig aufgebaute erweiterte Regel:

- (user.department -eq "Sales") -or (user.department -eq "Marketing")
- (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")

Eine vollständige Liste der unterstützten Parameter und Ausdrucksregeloperatoren finden Sie in den folgenden Abschnitten.

Die Gesamtlänge des Texts der erweiterten Regel darf 2048 Zeichen nicht überschreiten.
> [AZURE.NOTE]
Bei string- und regex-Vorgängen wird die Groß-und Kleinschreibung nicht beachtet. Sie können auch NULL-Prüfungen durchführen, indem Sie "$null" als Konstante verwenden, z. B.: user.department -eq $null. Zeichenfolgen mit Anführungszeichen (") sollten mit dem Escapezeichen ` maskiert werden, z. B.: user.department -eq "Sa`"les".

##Unterstützte Ausdrucksregeloperatoren
Die folgende Tabelle enthält alle Ausdrucksregeloperatoren und ihre Syntax zur Verwendung im Text der erweiterten Regel:

| Operator | Syntax |
|-----------------|----------------|
| Not Equals | -ne |
| Equals | -eq |
| Not Starts With | -notStartsWith |
| Starts With | -startsWith |
| Not Contains | -notContains |
| Contains | -contains |
| Not Match | -notMatch |
| Match | -match |


| Abfrageanalysefehler | Fehlerverwendung | Korrigierte Verwendung |
|----------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Fehler: Das Attribut nicht unterstützt. | (user.invalidProperty -eq "Value") | (user.department -eq "value")  
Die Eigenschaft sollte einer der unterstützten Eigenschaften aus der obigen Liste entsprechen. |
| Fehler: Der Operator wird für das Attribut nicht unterstützt. | (user.accountEnabled -contains true) | (user.accountEnabled -eq true)  
Die Eigenschaft weist den Typ "boolesch" auf. Verwenden Sie die unterstützten booleschen Operatoren (-eq oder -ne) aus der oben stehenden Liste. |
| Fehler: Abfragekompilierungsfehler. | (user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") | (user.department -eq "Sales") -and (user.department -eq "Marketing")
Der logische Operator sollte einer der unterstützten Eigenschaften aus der obigen Liste entsprechen.
(user.userPrincipalName -match ".*@domain.ext")  
oder  
(user.userPrincipalName -match "@domain.ext$")  
Fehler im regulären Ausdruck. |
| Fehler: Die Binärausdruck weist nicht das richtige Format auf. | (user.department –eq “Sales”) (user.department -eq "Sales")(user.department-eq"Sales") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")  
Abfrage enthält mehrere Fehler. Die Klammern befinden sich nicht an der richtigen Stelle. |
| Fehler: Unbekannter Fehler beim Einrichten dynamischer Mitgliedschaften. | (user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")  
Abfrage enthält mehrere Fehler. Die Klammern befinden sich nicht an der richtigen Stelle. |

##Unterstützte Parameter
Im Folgenden werden alle Benutzereigenschaften aufgelistet, die Sie in der erweiterten Regel verwenden können:

**Eigenschaften vom Typ "boolesch"**

Zulässige Operatoren

* -eq


* -ne


| Eigenschaften | Zulässige Werte | Verwendung |
|----------------|-----------------|--------------------------------|
| accountEnabled | true false | user.accountEnabled -eq true) |
| dirSyncEnabled | true false null | (user.dirSyncEnabled -eq true) |

**Eigenschaften vom Typ "string"**

Zulässige Operatoren

* -eq


* -ne


* -notStartsWith


* -StartsWith


* -contains


* -notContains


* -match


* -notMatch

| Eigenschaften | Zulässige Werte | Verwendung |
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| city | Jeder string-Wert oder $null. | (user.city -eq "value") |
| country | Jeder string-Wert oder $null. | (user.country -eq "value") |
| department | Jeder string-Wert oder $null. | (user.department -eq "value") |
| displayName | Jeder string-Wert. | (user.displayName -eq "value") |
| facsimileTelephoneNumber | Jeder string-Wert oder $null. | (user.facsimileTelephoneNumber -eq "value") |
| givenName | Jeder string-Wert oder $null. | (user.givenName -eq "value") |
| jobTitle | Jeder string-Wert oder $null. | (user.jobTitle -eq "value") |
| mail | Jeder string-Wert oder $null. SMTP-Adresse des Benutzers. | (user.mail -eq "value") |
| mailNickName | Jeder string-Wert. E-Mail-Alias des Benutzers. | (user.mailNickName -eq "value") |
| mobile | Jeder string-Wert oder $null. | (user.mobile -eq "value") |
| objectId | GUID des Benutzerobjekts. | (user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| passwordPolicies | None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword | (user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName | Jeder string-Wert oder $null. | (user.physicalDeliveryOfficeName -eq "value") |
| postalCode | Jeder string-Wert oder $null. | (user.postalCode -eq "value") |
| preferredLanguage | ISO 639-1 code | (user.preferredLanguage -eq "de-DE") |
| sipProxyAddress | Jeder string-Wert oder $null. | (user.sipProxyAddress -eq "value") |
| state | Jeder string-Wert oder $null. | (user.state -eq "value") |
| streetAddress | Jeder string-Wert oder $null. | (user.streetAddress -eq "value") |
| surname | Jeder string-Wert oder $null. | (user.surname -eq "value") |
| telephoneNumber | Jeder string-Wert oder $null. | (user.telephoneNumber -eq "value") |
| usageLocation | Aus zwei Buchstaben bestehender Ländercode. | (user.usageLocation -eq "US") |
| userPrincipalName | Jeder string-Wert. | (user.userPrincipalName -eq "alias@domain") |
| userType | member guest $null | (user.userType -eq "Member") |

**Eigenschaften vom Typ "string collection"**

Zulässige Operatoren

* -contains


* -notContains

| Eigenschaften | Zulässige Werte | Verwendung |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails | Jeder string-Wert. | (user.otherMails -contains "alias@domain") |
| proxyAddresses | SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses -contains "SMTP: alias@domain") |

## Erweiterungsattribute und benutzerdefinierte Attribute
Erweiterungsattribute und benutzerdefinierte Attribute werden in Regeln für dynamische Mitgliedschaft unterstützt.

Erweiterungsattributes werden von einer lokalen Windows Server AD-Instanz synchronisiert und erhalten folgendes Format: ExtensionAttributeX. Dabei entspricht X 1 bis 15. Beispiel für eine Regel, die ein Erweiterungsattribut verwendet:

(user.extensionAttribute15 -eq "Marketing")

Benutzerdefinierte Attribute werden von einer lokalen Windows Server AD-Instanz oder von einer verbundenen SaaS-Anwendung aus synchronisiert und erhalten das Format „user.extension\_[GUID]\_\_[Attribute]“. Dabei ist [GUID] der eindeutige Bezeichner in AAD für die Anwendung, die das Attribut in AAD erstellt hat, und [Attribute] ist der Name des Attributs bei seiner Erstellung. Beispiel für eine Regel, die ein benutzerdefiniertes Attribut verwendet:

user.extension\_c272a57b722d4eb29bfe327874ae79cb\_\_OfficeNumber

Den Namen des benutzerdefinierten Attributs finden Sie im Verzeichnis. Fragen Sie dazu das Attribut eines Benutzers mithilfe des Graph-Explorers ab, und suchen Sie nach dem Attributnamen.

## Mitarbeiterregel
Sie können Mitglieder einer Gruppe jetzt basierend auf dem manager-Attribut eines Benutzers auffüllen.
So konfigurieren Sie eine Gruppe als Gruppe mit "Vorgesetzten"
--------------------------------------------------------------------------------
1. Klicken Sie im Administratorportal auf die Registerkarte **Konfigurieren**, und wählen Sie **ERWEITERTE REGEL** aus.
2. Geben Sie die Regel mit folgender Syntax ein: Mitarbeiter von *Mitarbeiter von {Benutzer-ID\_von\_Vorgesetztem}* Beispiel für eine gültige Regel für Mitarbeiter:

Mitarbeiter von „62e19b97-8b3d-4d4a-a106-4ce66896a863“

Dabei ist „62e19b97-8b3d-4d4a-a106-4ce66896a863“ die Objekt-ID des Managers. Die Objekt-ID kann im AAD Admin-Portal auf der Profilregisterkarte der Benutzerseite desjenigen Benutzers gefunden werden, welcher der Vorgesetzte ist.

3. Nach dem Speichern dieser Regel werden alle Benutzer, die diese Regel erfüllen, als Mitglieder dieser Gruppe eingetragen. Beachten Sie, dass das erste Auffüllen der Gruppe einige Minuten dauern kann.


## Zusätzliche Informationen
Diese Artikel enthalten zusätzliche Informationen zum Azure Active Directory.

* [Problembehandlung bei dynamischen Mitgliedschaften für Gruppen](active-directory-accessmanagement-troubleshooting.md)

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](active-directory-manage-groups.md)

* [Artikelindex für die Anwendungsverwaltung in Azure Active Directory](active-directory-apps-index.md)

* [Was ist Azure Active Directory?](active-directory-whatis.md)

* [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0211_2016-->