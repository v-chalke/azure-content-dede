<properties
   pageTitle="Versionshinweise für Azure Data Catalog"
   description="Versionshinweise für die Azure Data Catalog-Version vom 13. Juli 2015"
   services="data-catalog"
   documentationCenter=""
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="07/13/2015"
   ms.author="derrickv"/>

# Hinweise für die Azure Data Catalog-Version vom 13. Juli 2015

Registrieren und Herstellen einer Verbindung mit der Oracle-Datenbank Beim Herstellen einer Verbindung mit Datenquellen der Oracle-Datenbank müssen Benutzer die richtigen Oracle-Treiber installieren, die der Bitness (32-Bit- oder 64-Bit) der verwendeten Software entsprechen.

-	Beim Registrieren von Oracle-Datenquellen auf einem 32-Bit-Windows-Computer werden die 32-Bit-Oracle-Treiber verwendet
-	Beim Registrieren von Oracle-Datenquellen auf einem Computer mit 64-Bit-Windows werden die 64-Bit-Oracle-Treiber verwendet
-	Beim Verbinden mit Oracle-Datenquellen mithilfe von Excel auf einem Computer mit der 32-Bit-Version von Microsoft Office (einschließlich 64-Bit-Windows-Versionen) werden 32-Bit-Oracle-Treiber verwendet
-	Beim Verbinden mit Oracle-Datenquellen mithilfe von Excel auf einem Computer mit der 64-Bit-Version von Microsoft Office werden die 64-Bit-Oracle-Treiber verwendet

### Registrieren und Verbinden mit SQL Server Reporting Services

Die Unterstützung von SQL Server Reporting Services (SSRS)-Datenquellen ist in der ersten Vorschauversion von Azure Data Catalog auf Server im systemeigenen Modus beschränkt. Unterstützung für SSRS im SharePoint-Modus erfolgt in einer späteren Version.

### Öffnen von Datenressourcen in Excel

Beim Öffnen von Datenressourcen aus dem Azure Data Catalog-Portal in Microsoft Excel wird Benutzern möglicherweise ein Dialogfeld mit einem **Sicherheitshinweis für Microsoft Excel** angezeigt. Hierbei handelt es sich um einen Standardvorgang. Benutzer können **Aktivieren** wählen, um den Vorgang fortzusetzen.

Weitere Informationen finden Sie unter [Aktivieren oder Deaktivieren von Sicherheitshinweisen zu Verknüpfungen und Dateien von verdächtigen Websites](https://support.office.com/de-de/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### Fehlende BLOB- und UDT-Spalten in der Vorschau

Wenn Sie Tabellen und Ansichten registrieren, die Spalten für BLOB (Binary Large Object) und benutzerdefinierten Datentyp (user-defined data type, UDT) enthalten, und Sie die Vorschau für die Datenressourcen berücksichtigen möchten, werden diese Spalten in der Vorschau nicht berücksichtigt.

### Proxy- und Richtlinienkonfiguration und Datenquellenregistrierung

Es kann vorkommen, dass sich Benutzer beim Azure Data Catalog-Portal anmelden können, jedoch eine Fehlermeldung erhalten, die ihre Anmeldung beim Tool zum Registrieren von Datenquellen verhindert.

Für dieses Problem sind zwei Ursachen möglich:

**Ursache 1: Konfiguration der Active Directory-Verbunddienste**Das Tool zum Registrieren von Datenquellen verwendet die Formularauthentifizierung, um Benutzeranmeldungen anhand von Active Directory zu überprüfen. Für eine erfolgreiche Anmeldung muss die Formularauthentifizierung durch einen Active Directory-Administrator in der globalen Authentifizierungsrichtlinie aktiviert sein.

In manchen Fällen tritt dieser Fehler möglicherweise nur dann auf, wenn der Benutzer im Unternehmensnetzwerk ist oder wenn der Benutzer von außerhalb des Unternehmensnetzwerks eine Verbindung herstellt. Mit der globalen Authentifizierungsrichtlinie können separate Authentifizierungsmethoden für Intranet- und Extranet-Verbindungen eingerichtet werden. Anmeldefehler können auftreten, wenn die Formularauthentifizierung nicht für das Netzwerk aktiviert ist, aus dem der Benutzer eine Verbindung herstellt.

Weitere Informationen finden Sie unter [Configuring intranet forms-based authentication for devices that do not support WIA](https://technet.microsoft.com/library/dn727110.aspx) (Konfigurieren der formularbasierten Intranet-Authentifizierung für Geräte ohne WIA-Unterstützung; nur in englischer Sprache).

**Ursache 2: Netzwerk-Proxykonfiguration**Wenn das Unternehmensnetzwerk einen Proxyserver verwendet, kann das Registrierungstool möglicherweise aufgrund des Proxys keine Verbindung zu Azure Active Directory herstellen. Um das Registrierungstool sicherzustellen, können Benutzer die Konfigurationsdatei bearbeiten , indem sie der Datei diesen Abschnitt hinzufügen:


	  <system.net>
	    <defaultProxy useDefaultCredentials="true" enabled="true">
	      <proxy usesystemdefault="True"
	                      proxyaddress="http://<your corporate network proxy url>"
	                      bypassonlocal="True"/>
	    </defaultProxy>
	  </system.net>


Starten Sie das Registrierungstool, um die Datei „RegistrationTool.exe.config“ zu finden, und öffnen Sie den Windows Task-Manager. Klicken Sie auf der Registerkarte „Details“ im Task-Manager mit der rechten Maustaste auf RegistrationTool.exe, und wählen Sie im Popupmenü „Dateispeicherort öffnen“.

<!---HONumber=July15_HO3-->