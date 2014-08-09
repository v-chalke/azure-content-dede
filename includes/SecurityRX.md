
# Azure-Sicherheitsleitfaden

## Zusammenfassung

Beim Entwickeln von Anwendungen für Azure sind Identität und Zugriff die vorrangigen Sicherheitsaspekte, die es zu beachten gilt. In diesem Thema werden die wichtigsten Sicherheitserwägungen in Bezug auf Identität und Zugriff in der Cloud erläutert und beschrieben, wie Sie Ihre Cloudanwendungen am besten schützen können.

## Übersicht

Die Sicherheit einer Anwendung ist eine Funktion auf ihrer Oberfläche. Je mehr Oberfläche die Anwendung nach außen hin aufweist, desto größer die Sicherheitsbedenken. Eine Anwendung, über die ein unbeaufsichtigter Stapelverarbeitungsvorgang ausgeführt wird, bietet aus einer Sicherheitsperspektive heraus beispielsweise weniger Oberfläche als eine
öffentlich zugängliche Website.

Nach einem Umzug in die Cloud müssen Sie sich keine Sorgen mehr um Infrastruktur und Netzwerke machen, da diese in Datencentern mit erstklassigen Sicherheitsmethoden, -tools und -technologien verwaltet werden. Andererseits vergrößert die Cloud naturgemäß die Oberfläche der Anwendung, und dies kann von Hackern unter Umständen ausgenutzt werden. Der Grund hierfür ist, dass viele Cloud-Technologien und -Dienste, im Gegensatz zu In-Memory-Komponenten, als Endpunkte exponiert sind. Azure-Speicher, Service Bus, SQL-Datenbank (vormals SQL Azure) und viele weitere Dienste sind über ihre jeweiligen Endpunkte im Netzwerk zugänglich.

In Cloudanwendungen ruht mehr Verantwortung auf den Schultern der Anwendungsentwickler. Um Angreifer fernzuhalten, müssen diese Cloudanwendungen mit hohen Sicherheitsstandards entwickeln. Betrachten Sie das folgende Diagramm (aus [Azure Security Notes PDF][1] von J.D. Meier) (in englischer Sprache): Beachten Sie, wie der Cloudanbieter -- in unserem Fall Azure -- den Infrastrukturteil übernimmt. Das bedeutet mehr sicherheitsbezogene Aufgaben für die Anwendungsentwickler:

![Sichern der Anwendung](./media/SecurityRX/01_SecuringTheApplication.gif)

Die gute Nachricht ist, dass sämtliche Ihnen schon bekannten Methoden, Prinzipien und Techniken der Sicherheitsentwicklung auch beim Entwickeln von Cloudanwendungen noch Gültigkeit haben. Beachten Sie die folgenden zu berücksichtigenden Schlüsselbereiche:

* Alle Eingaben werden in Hinsicht auf Typ, Länge, Bereich und Zeichenfolgenmuster validiert, um Injection-Angriffe zu vermeiden. Außerdem werden alle von der Anwendung zurückgegebenen Daten ordnungsgemäß gesichert.
* Sensible Daten sollten sowohl im gespeicherten Zustand als auch bei der Übermittlung geschützt werden, um Bedrohungen durch das Offenlegen von Informationen und das Manipulieren von Daten zu verringern.
* Überwachung und Protokollierung müssen dnungsgemäß implementiert sein, um Bedrohungen durch Nicht-Abweisbarkeit zu verringern.
* Authentifizierung und Autorisierung sollten mithilfe auf der Plattform verfügbarer bewährter Mechanismen implementiert werden, um Bedrohungen durch Identitätsfälschungen und Berechtigungserhöhungen zu verhindern.

Eine vollständige Liste von Bedrohungen, Angriffen, Schwachstellen und Gegenmaßnahmen finden Sie in "patterns & practices" unter [Cheat Sheet: Web Application Security Frame][2] (in englischer Sprache) und [Security Guidance for Applications Index][3] (in englischer Sprache).

In der Cloud unterscheiden sich die Mechanismen für Authentifizierung und Zugriffsteuerung stark von solchen, die für vor Ort vorhandene Anwendungen zur Verfügung stehen. Für Cloudanwendungen werden wesentlich mehr Authenthifizierungs- und Zugriffsoptionen angeboten. Dies kann zu Verwirrung und in der Folge zu mangelhaften Implementierungen führen. Noch mehr Verwirrung entsteht, wenn definiert werden soll, was eine Cloudanwendung ist. Die Anwendung könnte beispielsweise in der Cloud bereitgestellt werden, während ihr Authentifizierungsmechanismus über Active Directory erfolgt. Andererseits könnte die Anwendung lokal bereitgestellt werden, während die Authentifizierungsmechanismen in der Cloud liegen (z. B. über Azure Active Directory Access Control (vormals Access Control Service oder ACS)).

## Bedrohungen, Schwachstellen und Angriffe

Eine Bedrohung besteht in einem potenziell schlechten Ergebnis, das Sie vermeiden möchten, z. B. die Offenlegung sensibler Daten oder der Ausfall eines Diensts. Nach gängiger Praxis werden Bedrohungen mit dem Akronym "STRIDE" klassifiziert:

* **S**poofing oder Identitätsdiebstahl
* **T**ampering with data (Datenfälschung/-manipulation)
* **R**epudiation of actions (Nichtanerkennung von Aktionen)
* **I**nformation disclosure (Offenlegung von Daten)
* **D**enial of Service
* **E**levation of privileges (Rechteerweiterung)

Schwachstellen sind Fehler, die Entwickler versehentlich im Code hinterlassen und die eine Anwendung angreifbar machen. Werden Daten beispielsweise als unverschlüsselter Text gesendet, erzeugt dies die Bedrohung einer Offenlegung von Daten, da der Datenverkehr ausgespäht werden könnte.

Angriffe sind das Ausnutzen dieser Schwachstellen, um einer Anwendung zu schaden. Ein siteübergreifendes Skripting, oder XSS, ist beispielsweise ein Angriff, der ungesicherte Ausgaben ausnutzt. Ein weiteres Beispiel ist das Ausspähen einer Übertragung zum Abfangen unverschlüsselter Anmeldedaten. Diese Angriffe können dazu führen, dass die Bedrohung einer Identitätsfälschung (Spoofing) umgesetzt wird. Einfach ausgedrückt, können Sie Bedrohungen, Schwachstellen und Angriffe als schlechte Dinge betrachten. Die folgenden Diagramme liefern Ihnen einen Überblick über diese schlechten Dinge im Kontext einer in Azure bereitgestellten Webanwendung (aus [Azure Security Notes PDF][1] von J.D. Meier) (in englischer Sprache):

![Bedrohungen, Schwachstellen und Angriffe](./media/SecurityRX/02_ThreatsVulnerabilitiesandAttacks.gif)

Sie, als ein Entwickler, können diese Schwachstellen steuern. Je weniger Schwachstellen Sie hinterlassen, desto weniger Optionen bleiben einem Angreifer.

Identitäts- und zugriffsbezogene Schwachstellen öffnen allen im STRIDE-Modell genannten Bedrohungen Tür und Tor. Ein unsachgemäß implementierter Authentifizierungsmechanismus kann beispielsweise zu einer Identitätsfälschung und, in der Folge, zu einer Offenlegung von Informationen, zu Datenmanipulationen, zu Vorgängen unter unbefugt erweiterten Rechten oder sogar zum Komplettausfall des Dienstes führen. Beachten Sie die folgenden Fragen. Sie können Ihnen Hinweise auf potenzielle Schwachstellen in der Identitäts- und Zugriffsimplementierung Ihrer Cloud geben:

* Versenden Sie Anmeldeinformationen unverschlüsselt an Ihre Windows Azure-Dienste?
* Speichern Sie Anmeldeinformationen für Azure-Dienste unverschlüsselt?
* Folgen Ihre Anmeldeinformationen für Azure-Dienste den Richtlinien für starke Kennwörter?
* Verlassen Sie sich bei der Überprüfung der Anmeldeinformationen auf Azure, oder verwenden Sie benutzerdefinierte Überprüfungsmechanismen?
* Beschränken Sie die Authentifizierungssitzungen oder die Tokengültigkeitsdauer der Azure-Dienste auf ein angemessenes Zeitfenster?
* Überprüfen Sie Berechtigungen für jeden einzelnen Azure-Einstiegspunkt Ihrer verteilten Cloudanwendung?
* Ist ein Fehlschlagen Ihres Autorisierungsmechanismus sicher, ohne dass dabei sensible Daten offengelegt werden oder unbeschränkter Zugriff ermöglicht wird.

## Gegenmaßnahmen

Die beste Gegenmaßnahme gegen einen Angriff besteht darin, die von der Plattform angebotene Identität und die angebotenen Zugriffsmechanismen zu verwenden, anstatt Ihre eigenen zu implementieren. Berücksichtigen Sie die folgenden bedeutenden Identitäts- und Zugriffstechnologien:

**Windows Identity Foundation (WIF).** WIF ist eine .NET-Laufzeitbibliothek, die in .NET Framework 4.5 enthalten ist (sie steht außerdem als separater Download für .NET 3.5/4.0 zur Verfügung). WIF leistet die Schwerarbeit für Behandlungsprotokolle wie WS-Federation und WS-Trust und für Tokenbehandlung wie SAML. Daher müssen Sie keinen allzu komplexen sicherheitsbezogenen Code in Ihre Anwendung schreiben. In den folgenden Ressourcen finden Sie detaillierte Informationen über WIF:

* [Beispiele für Windows Identity Foundation 4.5][4] in der MSDN Code Gallery.
* [Windows Identity Foundation 4.5-Tools for Visual Studio 11 Beta][5] in der MSDN Code Gallery (in englischer Sprache).
* [Windows Identity Foundation-Laufzeit (.Net 3.5/4.0)][6] auf MSDN (in englischer Sprache).
* [Beispiele für Windows Identity Foundation 3.5/4.0 und Vorlagen für Visual Studio 2008/2010][7] auf MSDN (in englischer Sprache).

**Azure AD Access Control (vormals ACS genannt)**. Windows Azure AD Access Control ist ein Clouddienst, der den Sicherheitstokendienst (STS) bereitstellt und einen Verband mit verschiedenen Identitätsanbietern (IdPs), firmeneigenem Active Directory oder Internet-IdPs wie Windows Live ID/Microsoft-Konto, Facebook, Google, Yahoo! und OpenID 2.0 ermöglicht. In den folgenden Ressourcen finden Sie ausführliche Informationen zu Azure AD Access Control:

* [Access Control Service 2.0][8]
* [Szenarien und Lösungen mit ACS][9]
* [ACS -- Vorgehensweisen][10]
* [A Guide to Claims-Based Identity and Access Control (in englischer Sprache)][11]
* [Identity Developer Training Kit (nur in englischer Sprache)][12]
* [MSDN-hosted Identity Developer Training Course (in englischer Sprache)][13]

**Active Directory-Verbunddienste (AD FS).** Active Directory-Verbunddienste (AD FS) 2.0 unterstützt Ansprüche unterstützende Identitätslösungen mit Windows Server- und Active Directory-Technologie. AD-FS 2.0 unterstützt die WS-Trust-, WS-Federation- und SAML-Protokolle. In den folgenden Ressourcen finden Sie detaillierte Informationen über AD FS:

* [AD FS 2.0 Content Map (in englischer Sprache)][14]
* [Web SSO Design (in englischer Sprache)][15]
* [Federated Web SSO Design (in englischer Sprache)][16]

**Azure Shared Access Signatures.** Shared Access Signatures ermöglicht Ihnen die Feinabstimmung des Zugriffs auf eine Blob- oder Containerressource. In den folgenden Ressourcen finden Sie detaillierte Informationen über Shared Access Signatures:

* [Managing Access to Blobs and Containers (in englischer Sprache)][17]
* [New Storage Feature: Shared Access Signatures (in englischer Sprache)][18]
* [Shared Access Signatures Are Easy These Days (in englischer Sprache)][19]

## Szenarienübersicht

In diesem Abschnitt werden kurz die wichtigsten in diesem Thema behandelten Szenarien umrissen. Es verschafft Ihnen eine Übersicht, wenn Sie die richtige Lösung für Ihre Anwendung ermitteln möchten.

* **ASP.NET-Webformularanwendung mit Verbundauthentifizierung.** In diesem Szenario steuern Sie den Zugriff auf Ihre ASP.NET-Webformularanwendung entweder über eine Internetidentität wie Live ID/Microsoft-Konto oder über eine firmeninterne Identität, die jeweils in Windows Server Active Directory verwaltet wird.
* **WCF (SOAP)-Dienst mit Verbundauthentifizierung.** In diesem Szenario steuern Sie den Zugriff auf Ihren WCF (SOAP)-Dienst über Dienstidentitäten, die von Azure AD Access Control verwaltet werden.
* **WCF (SOAP)-Dienst mit Verbundauthentifizierung, Identitäten in Active Directory.** In diesem Szenario steuern Sie den Zugriff auf Ihren WCF (SOAP)-Webdienst über Identitäten, die von firmeninternem Windows Server Active Directory verwaltet werden.
* **WCF (REST)-Dienst mit Verbundauthentifizierung.** In diesem Szenario steuern Sie den Zugriff auf Ihren WCF (REST)-Dienst über Dienstidentitäten, die über Azure AD Access Control verwaltet werden.
* **WCF (REST)-Dienst mit Live ID/Microsoft-Konto, Facebook, Google, Yahoo!, Open ID.** In diesem Szenario steuern Sie den Zugriff auf Ihren WCF (REST)-Dienst über eine Internetidentität wie Live ID/ Microsoft-Konto.
* **ASP.NET-Webanwendung für den REST WCF-Dienst mit freigegebenem SWT-Token.** In diesem Szenario geht es um eine verteilte Anwendung mit einer Front-End-ASP.NET-Webanwendung und nachgelagertem REST-Dienst, bei der der Kontext des Endbenutzers durch physische Ebenen geleitet werden soll.
* **Autorisierung über rollenbasierte Zugriffssteuerung (RBAC) in Ansprüche unterstützenden Anwendungen und Diensten.** In diesem Szenario soll in Ihrer Anwendung eine auf Rollen basierende Autorisierungslogik implementiert werden.
* **Anspruchsbasierte Autorisierung in Ansprüche unterstützenden Anwendungen und Diensten.** In diesem Szenario soll in Ihrer Anwendung eine auf komplexen Autorisierungsregeln basierende Autorisierungslogik implementiert werden.
* **Azure Storage-Dienstidentität und Zugriffsszenarien.** In diesem Szenario müssen Sie auf sichere Weise Zugriff auf Azure Storage-Blobs und -Container freigeben.
* **Azure SQL-Datenbankidentität und Zugriffsszenarien.** Die SQL-Datenbank unterstützt nur SQL Server-Authentifizierung. Windows-Authentifizierung (integrierte Sicherheit) wird nicht unterstützt. Die Benutzer müssen bei jeder Verbindung zur SQL-Datenbank ihre Anmeldeinformationen (Benutzername und Kennwort) angeben.
* **Azure Service Bus-Identität und Zugriffsszenarien.** In diesem Szenario müssen Sie auf sichere Weise auf Azure Service Bus-Warteschlangen zugreifen.
* **In-Memory-Cacheidentität und Zugriffszenarien.** In diesem Szenario müssen Sie auf sichere Weise auf vom In-Memory-Cache verwaltete Daten zugreifen.
* **Azure Marketplace-Identität und Zugriffsszenarien.** In diesem Szenario müssen Sie auf sichere Weise auf Azure Marketplace-DataSets zugreifen.

## Azure-Identität und Zugriffsszenarien

In diesem Abschnitt werden gängige Identitäts- und Zugriffsszenarien für verschiedene Anwendungsarchitekturen umrissen. Über die Szenarienübersicht können Sie sich eine schnelle Orientierung verschaffen.

### ASP.NET-Webformularanwendung mit Verbundauthentifizierung

In diesem Anwendungsarchitekturszenario kann Ihre Webanwendung in Azure oder lokal bereitgestellt werden. Die Anwendung erfordert, dass sich der Benutzer entweder über firmeninternes Active Directory oder über Internet-Identitätsanbieter authentifiziert.

Verwenden Sie Azure AD Access Control und Windows Identity Foundation, um dieses Szenario zu lösen.

![Azure Active Directory Access Control](./media/SecurityRX/03_WindowsAzureADAccesscontrol.gif)

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

* [Vorgehensweise: Erstellen einer ersten Ansprüche unterstützenden ASP.NET-Anwendung mithilfe von ACS][20]
* [Vorgehensweise: Hosten von Anmeldeseiten in einer ASP-NET-Webanwendung][21]
* [Vorgehensweise: Implementieren von Anspruchsautorisierung in einer Ansprüche unterstützenden ASP.NET-Anwendung mithilfe von WIF und ACS][22]
* [Vorgehensweise: Implementieren von rollenbasierter Zugriffssteuerung (Role Based Access Control, RBAC) in einer Ansprüche unterstützenden ASP.NET-Anwendung mithilfe von WIF und ACS][23]
* [Vorgehensweise: Konfigurieren der Vertrauensstellung zwischen ACS und ASP.NET-Webanwendungen mithilfe von X.509-Zertifikaten][24]
* [Codebeispiel: Einfache ASP.NET-Formulare][25]

### WCF (SOAP)-Dienst mit Dienstidentität

In diesem Anwendungsarchitekturszenario kann Ihr WCF (SOAP)-Dienst in Azure oder lokal bereitgestellt werden. Der Zugriff auf diesen Dienst erfolgt als nachgelagerter Dienst über eine Webanwendung oder sogar über einen anderen Webdienst. Sie müssen den Zugriff auf diesen Dienst mithilfe einer anwendungsspezifischen Identität steuern. Denken Sie dazu an die Art des Anwendungspoolkontos, das Sie in IIS verwendet haben. Die Technologie ist zwar unterschiedlich, die Herangehensweise ist jedoch ähnlich, da auf den Dienst mithilfe eines Anwendungsbereichkontos anstelle eines Benutzerkontos zugegriffen wird.

Verwenden Sie die Funktion "Dienstidentität" in Azure AD Access Control. Dies ähnelt dem IIS-Anwendungspoolkonto, das Sie bei der Bereitstellung Ihrer Anwendungen in Windows Server und IIS verwendet haben. Konfigurieren Sie Azure AD Access Control so, dass SAML-Token ausgestellt werden, die von WIF im WCF (SOAP)-Dienst behan|delt werden.

![WCF (SOAP)-Dienst](<./media/SecurityRX/04_WCF(SOAP)Service.gif>)

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

* [Vorgehensweise: Hinzufügen von Dienstidentitäten mit einem X.509-Zertifikat, Kennwort oder symmetrischen Schlüssel][26]
* [Vorgehensweise: Authentifizierung mit einem Clientzertifikat bei einem durch ACS geschützten WCF-Dienst][27]
* [Vorgehensweise: Authentifizierung mit einem Benutzernamen und einem Kennwort bei einem durch ACS geschützten WCF-Dienst][28]
* [Codebeispiel: WCF-Zertifikatauthentifizierung][29]
* [Codebeispiel: WCF-Benutzernamenauthentifizierung][30]

### WCF (SOAP)-Dienst mit Verbundauthentifizierung, Identitäten in Active Directory

In diesem Anwendungsarchitekturszenario kann Ihr WCF (SOAP)-Dienst in Azure oder lokal bereitgestellt werden. Sie müssen den Zugriff auf diesen Dienst über eine Identität steuern, die von firmeneigener Windows Server Active Directory (AD) verwaltet wird.

Verwenden Sie Azure AD Access Control in einer Konfiguration für einen Verbund mit Windows Server AD FS. In diesem Fall müssen Sie die Dienstidentität nicht mit Azure AD Access Control konfigurieren. Der Agent, der auf den WCF (SOAP)-Dienst zugreifen muss, übermittelt Anmeldedaten an AD FS. Bei einer erfolgreichen Authentifizierung stellt AD FS anschließend das Token aus. Das Token wird anschließend an Azure AD Access Control übermittelt und zurück an den Agenten übergeben. Der Agent verwendet das Token, um die Anforderung an den WCF (SOAP)-Dienst zu übermitteln.

![WCF (SOAP)-Dienst mit AD](./media/SecurityRX/05_AzureADAccessControl.gif)

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

* [Vorgehensweise: Hinzufügen von Dienstidentitäten mit einem X.509-Zertifikat, Kennwort oder symmetrischen Schlüssel][26]
* [Vorgehensweise: Konfigurieren von AD FS 2.0 als Identitätsanbieter][31]
* [Vorgehensweise: Verwenden des Verwaltungsdiensts zum Konfigurieren von AD FS 2.0 als Unternehmensidentitätsanbieter][32]
* [Codebeispiel: WCF-Verbundauthentifizierung mit AD FS 2.0][33]

### WCF (REST)-Dienst mit Dienstidentitäten

In diesem Szenario kann Ihr WCF (REST)-Dienst in Windows Azure oder vor Ort bereitgestellt werden. Der Zugriff auf diesen Dienst erfolgt als nachgelagerter Dienst über eine Webanwendung oder über einen anderen Webdienst. Sie müssen den Zugriff auf diesen Dienst über eine anwendungsspezifische Identität steuern. Denken Sie dazu an die Art des Anwendungspoolkontos, das Sie in IIS verwendet haben. Die Technologie ist zwar unterschiedlich, die Herangehensweise ist jedoch ähnlich, da auf den Dienst mithilfe eines Anwendungsbereichkontos anstelle eines Benutzerkontos zugegriffen wird.

Verwenden Sie die Funktion "Dienstidentität" in Azure AD Access Control. Konfigurieren Sie Azure AD Access Control so, dass SWT (Simple Web Token)-Token ausgestellt werden. Zur Behandlung des SWT-Tokens auf Seiten des REST-Diensts können Sie entweder einen benutzerdefinierten Tokenhandler implementieren und ihn in die WIF-Pipeline einfügen oder das Token ohne Verwendung der WIF-Infrastruktur "manuell" analysieren.

Beachten Sie das folgende Diagramm (WIF ist optional):

![REST-Dienst](./media/SecurityRX/06_RESTService.gif)

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

* [Vorgehensweise: Konfigurieren der Vertrauensstellung zwischen ACS und dem WCF-Dienst mithilfe symmetrischer Schlüssel][34]
* [How To: Authenticate to a REST WCF Service Deployed to Azure Using ACS (in englischer Sprache)][35]
* [Codebeispiel: ASP.NET-Webdienst][36]
* [Codebeispiel: Windows Phone 7-Anwendung][36]
* [REST WCF With SWT Token Issued By Azure Access Control Service (ACS) (in englischer Sprache)][37]

### WCF (REST)-Dienst mit Live ID/Microsoft-Konto, Facebook, Google, Yahoo!, Open ID

In diesem Szenario kann Ihr WCF (REST)-Dienst in Windows Azure oder vor Ort bereitgestellt werden. Sie müssen den Zugriff auf diesen Dienst mithilfe einer öffentlichen Internetidentität wie Live ID/Microsoft-Konto oder Facebook steuern.

Verwenden Sie Azure AD Access Control zum Ausgeben von SWT-Token. Zur Behandlung des SWT-Tokens auf Seiten des REST-Diensts können Sie einen benutzerdefinierten Tokenhandler implementieren und ihn in die WIF-Pipeline einfügen oder das Token ohne Verwendung der WIF-Infrastruktur "manuell" analysieren.

Wichtig: Bedenken Sie, dass die Anwendung zum Sammeln von Benutzeranmeldeinformationen eine Webbrowsersteuerung verwenden muss, um dieses Szenario zu implementieren. Dadurch ist diese Anwendung ungeeignet für Szenarien, in denen über eine ASP.NET-Anwendung auf den REST-Dienst zugegriffen wird. Sie eignet sich nur für Szenarien, in denen der Zugriff auf den REST-Dienst über die Clientanwendung des Benutzers erfolgt, z. B. eine Windows Phone 7-App oder einen reichhaltigen Desktop-Client. Der Hauptgrund für die Verwendung der Webbrowsersteuerung besteht darin, dass Internetidentitäten aus sich heraus keine Szenarien mit aktivem Profil (Webdienstszenarien) unterstützen. Internetidentitäten unterstützen hauptsächlich Szenarien mit passivem Profil (Web-Apps), die sich auf Browserumleitungen stützen: Und genau da erweist sich die Webbrowsersteuerung als praktisch.

Beachten Sie das folgende Diagramm (WIF ist optional und wird daher nicht gezeigt):

![WIF ist optional.](./media/SecurityRX/07_WIFisOptional.gif)

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

* [How To: Authenticate to a REST WCF Service Deployed to Azure Using ACS (in englischer Sprache)][35]
* [How To: Konfigurieren von Google als Identitätsanbieter][38]
* [How To: Konfigurieren von Facebook als Identitätsanbieter][39]
* [How To: Konfigurieren von Yahoo! als Identitätsanbieter][40]
* [Codebeispiel: Windows Phone 7-Anwendung][36]
* [REST WCF With SWT Token Issued By Azure Access Control Service (ACS) (in englischer Sprache)][37]

### ASP.NET-Webanwendung für den REST WCF-Dienst mit freigegebenem SWT-Token

In diesem Szenario geht es um eine verteilte Anwendung mit einer Front-End-ASP.NET-Webanwendung und nachgelagertem REST-Dienst, bei der der Kontext des Endbenutzers über physische Ebenen gepflegt werden soll. Dies ist manchmal beim Implementieren von Autorisierungslogik oder beim identitätsbasierten Anmelden des Benutzers im nachgelagerten REST-Dienst erforderlich.

Konfigurieren Sie Azure AD Access Control zum Ausgeben von SWT-Token. Das SWT-Token wird an die Front-End-ASP.NET-Webanwendung ausgestellt und anschließend für den nachgelagerten REST-Dienst freigegeben. In diesem Fall ist nur eine abhängige Seite in Azure AD Access Control konfiguriert. Es bestehen jedoch mehrere Vorbehalte:

* Da mit WIF kein SWT-Tokenhandler bereitgestellt wird, müssen Sie einen benutzerdefinierten Tokenhandler konfigurieren, der mit der ASP.NET-Webanwendung verwendet wird. Sie sollten sich auf die Schwerarbeit verlassen, die WIF für die Unterstützung des über Browserumleitungen funktionierenden WS-Federation-Protokolls leistet, anstatt es selbst zu implementieren.
* Vergewissern Sie sich beim Implementieren eines benutzerdefinierten SWT-Tokenhandlers, dass das Bootstrap-Token adressiert wird. Dann ist sichergestellt, dass es beibehalten wird. Andernfalls können Sie es nicht freigeben und an den nachgelagerten REST-Dienst senden.
* Die Verwendung von WIF ist bei einem REST-Dienst nicht unbedingt notwendig. Stattdessen können Sie das Token "manuell" analysieren, da in diesem Fall keine Umleitungen behandelt werden müssen.

![ASP.NET-Webanwendung](./media/SecurityRX/08_ASPNETWebApptoREST.gif)

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

* [How To: Konfigurieren von Google als Identitätsanbieter][38]
* [How To: Konfigurieren von Facebook als Identitätsanbieter][39]
* [How To: Konfigurieren von Yahoo! als Identitätsanbieter][40]
* [ASP.NET Web App To REST WCF Service Delegation Using Shared SWT Token (in englischer Sprache)][41]

### Autorisierung über rollenbasierte Zugriffssteuerung (RBAC) in Ansprüche unterstützenden Anwendungen und Diensten

In diesem Szenario müssen Sie eine auf Benutzerrollen basierende Autorisierung in Ihre Webanwendung oder Ihren Dienst implementieren. Benutzer mit den erforderlichen Rollen erhalten Zugriff, während Benutzer ohne die erforderlichen Rollen abgewiesen werden. Einfach ausgedrückt, muss die Anwendung nur eine einfache Frage beantworten: "Hat der Benutzer die Rolle X?".

Dieses Szenario kann auf verschiedene Weise gelöst werden. Sie können Azure AD Access Control, WIF-ClaimsAuthenticationManager, eine samlSecurityTokenRequirement-Zuordnung oder einen benutzerdefinierten Rollen-Manager verwenden.

WIF kommt in allen Fällen zum Einsatz. WIF unterstützt die IPrincipal.IsInRole("MyRole")-Methode. In den meisten Fällen liegt der Schlüssel darin sicherzustellen, dass eine Forderung nach dem Rollentyp in der URL http://schemas.microsoft.com/ws/2008/06/identity/claims/role im Token vorhanden ist, sodass WIF die Rollenmitgliedschaft bei Aufruf der IsInRole-Methode erfolgreich prüfen kann.

**Azure AD Access Control**. In dieser Implementierung wird das Anspruchtransformationsregel-Modul von Windows Azure AD Access Control verwendet. Durch den Einsatz von Regeln für das Anspruchtransformationsregel-Modul können Sie jeden eingehenden Anspruch in einen Anspruch auf einen Regeltyp umwandeln, sodass WIF diesen Rollenanspruch analysieren kann, wenn das Token auf die Anwendung oder einen Dienst trifft. Auf diese Weise wird sichergestellt, dass der IsInRole-Methodenaufruf erfolgreich ist.

![](./media/SecurityRX/09_RBAC.gif)

**WIF-ClaimsAuthenticationManager**. Verwenden Sie in dieser Implementierung ClaimsAuthenticationManager als den Erweiterungspunkt von WIF. Mit dieser Herangehensweise wandeln Sie beliebige eingehenden Ansprüche auf Ebene der Anwendung in einen Regelanspruchstyp um. Die Komplexität der Umwandlung ist lediglich durch den von Ihnen geschriebenen Code beschränkt.

![](./media/SecurityRX/10_WIFClaimsAuthenticationManager.gif)

**samlSecurityTokenRequriement-Zuordnung**. Verwenden Sie in dieser Implementierung die Konfiguration "samlSecurityTokenRequirement" in web.config, um WIF mitzuteilen, welche Anspruchtsypen sich wie Regelanspruchstypen verhalten. Wenn das Token beispielsweise Träger eines Anspruchs des Gruppentyps ist, können Sie es dem Rollenanspruchstyp zuordnen. Mit dieser Vorgehensweise sind nur einfache Zuordnungen möglich.

![](./media/SecurityRX/11_SecurityTokenRequriementmapping.gif)

**Benutzerdefinierter RoleManager.** In dieser Implementierung implementieren Sie einen benutzerdefinierten RoleManger. WIF überprüft eingehende Ansprüche beim Implementieren von benutzerdefinierten RoleManager-Schnittstellenmethoden wie GetAllRoles().

![](./media/SecurityRX/12_CustomRoleManager.gif)

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

* [How To: Implementieren von rollenbasierter Zugriffssteuerung (Role Based Access Control, RBAC) in einer Ansprüche unterstützenden ASP.NET-Anwendung mithilfe von WIF und ACS][23]
* [How To: Implementieren von Tokentransformationslogik mithilfe von Regeln][42]
* [Authorization With RoleManager For Claims Aware (WIF) ASP.NET Web Applications (in englischer Sprache)][43]
* Code Sample: Using Claims In IsInRole in [Windows Identity Foundation SDK][44] (in englischer Sprache)

### Anspruchsbasierte Autorisierung in Ansprüche unterstützenden Anwendungen und Diensten

In diesem Szenario müssen Sie komplexe Autorisierungslogik in Ihrer Webanwendung oder Ihrem Dienst implementieren, und die IsInRole()-Methode bringt keine zufriedenstellenden Ergebnisse für Ihre Autorisierungsanforderungen. Wenn Ihre Autorisierungsmethode auf Rollen basiert, ziehen Sie in Erwägung, die im vorherigen Abschnitt vorgestellte rollenbasierte Zugriffssteuerung zu implementieren.

Verwenden Sie ClaimsAuthorizationManager als den Erweiterungspunkt für WIF. ClaimsAuthorizationManager ermöglicht externe Aufrufe zur Zugriffsprüfung, mit denen Ihr Anwendungscode sauberer und pflegefreundlicher angezeigt wird als über die Zugriffsprüfungen, die im Anwendungscode selbst implementiert sind.

![](./media/SecurityRX/13_ClaimsAuthorizationManager.gif)

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

* [How To: Implementieren von Tokentransformationslogik mithilfe von Regeln][42]
* [How To: Implementieren von Anspruchsautorisierung in einer Ansprüche unterstützenden ASP.NET-Anwendung mithilfe von WIF und ACS][22]
* Code Sample: Claims based Authorization in [Windows Identity Foundation SDK][44] (in englischer Sprache)

## Azure Storage-Dienstidentität und Zugriffsszenarien

In diesem Szenario müssen Sie auf sichere Weise Zugriff auf Azure Storage-Blobs und -Container freigeben.

Verwenden Sie Shared Access Signatures. Für den Zugriff auf Ihr Speicherdienstkonto von Ihrer eigenen Anwendung aus verwenden Sie das freigegebene Hash, das über das Azure-Portal beim Konfigurieren und Speichern Ihrer Speicherdienstkonten bereitgestellt wird. Wenn Sie jemand anderem Zugriff auf die Blobs und Container in Ihrem Speicherdienstkonto gewähren möchten, verwenden Sie dazu die URLs für die Shared Access Signatures.

Achten Sie besonders auf den sicheren Umgang mit dem Daten, um eine Offenlegung zu verhindern; besondere Vorsicht ist auch bei der Gültigkeitsdauer der Shared Access Signatures geboten.

![](./media/SecurityRX/14_WindowsAzurestorage.gif)

In den folgenden Ressourcen finden Sie Hinweise zum Lösen dieses Szenarios:

* [Managing Access to Blobs and Containers (in englischer Sprache)][17]
* [New Storage Feature: Shared Access Signatures (in englischer Sprache)][18]
* [Shared Access Signatures Are Easy These Days (in englischer Sprache)][19]

## Azure SQL-Datenbankidentität und Zugriffsszenarien

Die SQL-Datenbank unterstützt nur SQL Server-Authentifizierung. Windows-Authentifizierung (integrierte Sicherheit) wird nicht unterstützt. Die Benutzer müssen bei jeder Verbindung zu einer SQL-Datenbank ihre Anmeldeinformationen (Benutzername und Kennwort) angeben. Seien Sie beim Verwalten Ihres Benutzernamens und Kennworts besonders vorsichtig, um das Offenlegen dieser Informationen zu vermeiden.

![](./media/SecurityRX/15_SQLAzureIdentityandAccessScenarios.gif)

In den folgenden Ressourcen finden Sie Hinweise zum Lösen dieses Szenarios:

* [Sicherheitsrichtlinien und Einschränkungen (Windows Azure SQL-Datenbank)][45]
* [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über sqlcmd][46]
* [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über ADO.NET][47]
* [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über ASP.NET][48]
* [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über WCF Data Services][49]
* [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über PHP][50]
* [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über JDBC][51]
* [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über das ADO.NET Entity Framework][52]

## Azure Service Bus-Identität und Zugriffsszenarien

Service Bus und Azure AD Access Control stehen in einer besonderen Beziehung zueinander, da jeder Service Bus-Dienstnamespace mit einem entsprechenden Access Control-Dienstnamespace gepaart ist. Dieser trägt den gleichen Namen mit dem Zusatz "-sb". Der Grund für diese besondere Beziehung liegt in der Art, wie der Dienstbus und Access Control ihre gegenseitige Vertrauensbeziehung und die damit verknüpften Verschlüsselungsgeheimnisse verwalten. Weitere Informationen finden Sie in den im Folgenden aufgeführten Ressourcen.

![](./media/SecurityRX/16_WindowsAzureServiceBusIdentity.gif)

In den folgenden Ressourcen finden Sie Hinweise zum Lösen dieses Szenarios:

* [Securing Service Bus with ACS][53] (Video, in englischer Sprache)
* [Securing Service Bus with ACS][54] (Folien, in englischer Sprache)
* [Service Bus-Authentifizierung und -Autorisierung mit dem Zugriffssteuerungsdienst][55]

## In-Memory-Cacheidentität und Zugriffszenarien

In-Memory-Cache (vormals Azure Cache genannt) stützt sich für die Authentifizierung auf Azure AD Access Control. Es verwendet die freigegebenen Schlüssel, die über das Verwaltungsportal verfügbar sind. Verwenden Sie die Schlüssel beim Cache-Zugriff im Code oder in den Konfigurationsdateien. Achten Sie auf eine sichere Speicherung der Schlüssel, um das Offenlegen von Daten zu verhindern.

![](./media/SecurityRX/17_WindowsAzureCacheIdentity.gif)

In den folgenden Ressourcen finden Sie Hinweise zum Lösen dieses Szenarios:

* [Gewusst wie: Programmgesteuertes Konfigurieren eines Cacheclients (freigegebene Windows Azure-Caches)][56]
* [Gewusst wie: Konfigurieren eines Cacheclients mithilfe der Anwendungskonfigurationsdatei (Windows Azure Shared Caching)][57]
* [Windows Azure AppFabric-Beispiele][58] (im Abschnitt "Cachebeispiele")

## Azure Marketplace-Identität und Zugriffsszenarien

Bei jedem kostenlosen oder kostenpflichtigen Zugriff auf einen Azure Marketplace-DataSet muss der Benutzer authentifiziert werden, bevor er Zugriff erhält. Beim Erstellen einer Anwendung, muss der Authentifizierungsvorgang im Code enthalten sein. Beachten Sie die folgenden, häufig auftretenden Szenarien:

### Zugriff auf eigenen DataSet

In diesem Szenario erstellen Sie eine Anwendung, die DataSets in ihrem Marketplace-Abonnement verbraucht. Sie sind der Benutzer der Anwendung. Die Anwendung kann unter Azure, lokal oder auf Marketplace bereitgestellt werden.

Verwenden Sie den gemeinsam verwendeten Schlüssel, der über das Marketplace-Abonnement erhältlich ist. Sie erhalten den gemeinsam verwendeten Schlüssel über die Verwendung des Marketing-Portals.

![](./media/SecurityRX/18_IAccessMyDataset.gif)

In den folgenden Ressourcen finden Sie Hinweise zum Lösen dieses Szenarios:

* [Implementieren von HTTP-Standardauthentifizierung in Ihrer Marketplace-App][59]

### Zugriff von Benutzern auf meine DataSets

In diesem Szenario erstellen Sie eine Anwendung, die Benutzern den Zugriff auf Ihren DataSet ermöglicht. Die Anwendung kann unter Azure, lokal oder auf Marketplace bereitgestellt werden.

Verwenden Sie zum Lösen dieses Szenarios die Delegierung "OAuth". Benutzer werden zur Eingabe ihrer Anmeldedaten für Live-ID/Microsoft-Konto aufgefordert. Anschließend werden sie durch den Zustimmungsvorgang geführt.

![](./media/SecurityRX/19_UsersAccessMyDatasets.gif)

In den folgenden Ressourcen finden Sie Hinweise zum Lösen dieses Szenarios:

* [OAuth Web Client Example (in englischer Sprache)][60]
* [OAuth Rich Client Example (in englischer Sprache)][61]

### Anwendungszugriff auf das Marketplace-API

In diesem Szenario erstellen Sie eine Anwendung, die auf das Marketplace-API zugreift. Die Marketplace-API erfordert eine Authentifizierung für die erfolgreiche Verarbeitung von Aufrufen. Die Anwendung wird in Azure Marketplace bereitgestellt.

![](./media/SecurityRX/20_ApplicationAccessMarketplaceAPI.gif)

Detaillierte Informationen über das Implementieren der Authentifizierung finden Sie auch im Marketing-Publishing-Kit.

In den folgenden Ressourcen finden Sie Hinweise zum Lösen dieses Szenarios:

* [Anwendungs-Publishing-Kit herunterladen][62]
* [Introduction to Azure Marketplace for Applications (in englischer Sprache)][63]

## Sicherheitsknöpfe

In diesem Abschnitt werden Sicherheitsköpfe für Windows Identity Foundation und Azure AD Access Control vorgestellt. Verwenden Sie sie beim Entwerfen und Bereitstellen Ihrer Anwendung als eine einfache Sicherheitscheckliste für diese Technologien.

### Windows Identity Foundation

Im folgenden werden wichtige Sicherheitsknöpfe von WIF aufgeführt. Die unten stehenden Informationen sind ein Auszug aus [Überlegungen zum Entwurf][64] und [Windows Identity Foundation (WIF) Security for ASP.NET Web Applications - Threats & CountermeasuresWindows Identity Foundation (WIF) Security for ASP.NET Web Applications - Threats & Countermeasures][65] (in englischer Sprache).

* **IssuerNameRegistry**. Legt vertrauenswürdige Sicherheitstokendienste (STS) fest. Vergewissern Sie sich, dass nur vertrauenswürdige STSs aufgeführt werden.
* **cookieHandler requireSsl="true"**. Legt fest, ob Sitzungs-Cookies über das SSL-Protokoll übertragen werden.
* **wsFederation's requireHttps="true"**. Legt fest ob die Kommunikation des Verbundprotokolls mit dem Identitätsanbieter über das SSL-Protokoll erfolgt.
* **tokenReplayDetection enabled="true"**. Legt fest ob die Funktion zur Erkennung einer Tokenwiedergabe aktiv ist. Achten Sie darauf, dass diese Funktion Serveraffinität verursacht, da sie lokale Kopien verwendeter Token verwaltet.
* **audienceUris**. Legt das Zielpublikum für das Token fest. Wenn Ihre Anwendung ein Token erhält, das nicht für sie gedacht war, wird es von WIF abgelehnt.
* **requestValidation** und **httpRuntime requestValidationType**. Aktiviert/deaktiviert die ASP.NET-Validierungsfunktion. Weitere hilfreiche Hinweise finden Sie unter [Windows Identity Foundation (WIF): A Potentially Dangerous Request.Form Value Was Detected from the Client][66] (in englischer Sprache)

### Azure AD Access Control

Beachten Sie die folgenden Sicherheitsknöpfe bei der Bereitstellung mit Azure AD Access Control Die unten stehenden Informationen sind ein Auszug aus [ACS-Sicherheitsrichtlinien][67] und [Verwaltungsrichtlinien für Zertifikate und Schlüssel][68].

* **Ablauf von STS-Token**. Legen Sie über das Verwaltungsportal von Azure AD Access Control einen aggressiven Tokenablauf fest.
* **Datenvalidierung bei Verwendung der Funktion "Fehler-URL"**. Die Funktion "Fehler-URL" von Azure AD Access Control erfordert einen anonymen Zugriff auf die Seite der Anwendung, an die sie Fehlermeldungen sendet. Gehen Sie bei sämtlichen Daten, die an diese Seite gehen, davon aus, dass sie gefährlich sind und aus einer nicht vertrauenswürdigen Quelle stammen.
* **Verschlüsseln von Token für hoch sensible Szenarien**. Erwägen Sie ein Verschlüsseln der Token, um die Bedrohung durch eine Offenlegung der im Token vorhandenen Informationen zu verringern.
* **Verschlüsseln von Cookies mit RSA beim Bereitstellen in Azure**. WIF verschlüsselt Cookies standardmäßig mit DPAPI. Dies führt zu Serveraffinität und kann bei Bereitstellung in Webfarm- und Azure-Umgebungen zu Ausnahmen führen. Verwenden Sie bei Webfarm- und Windows Azure-Szenarien stattdessen RSA.
* **Token-Signaturzertifikate**. Erneuern Sie Token-Signaturzertifikate regelmäßig, um einen Denial of Service zu vermeiden. Azure AD Access Control signiert alle von ihm ausgegebenen Token. Beim Erstellen einer Anwendung, die von ACS ausgestellte SAML-Token verbraucht, werden X.509-Zertifikate für die Signatur verwendet. Nach dem Ablauf von Signaturzertifikaten erhalten Sie Fehlermeldungen, wenn Sie versuchen, ein Token anzufordern.
* **Token-Signaturschlüssel**. Erneuern Sie Token-Signaturschlüssel regelmäßig, um einen Denial of Service zu vermeiden. Azure AD Access Control signiert alle von ihm ausgegebenen Token. Beim Erstellen einer Anwendung, die von ACS ausgestellte SWT-Token verbraucht, werden symmetrische 256-Bit-Signaturschlüssel verwendet. Nach dem Ablauf von Signaturschlüsseln erhalten Sie Fehlermeldungen, wenn Sie versuchen, ein Token anzufordern.
* **Token-Verschlüsselungszertifikate**. Erneuern Sie Token-Verschlüsselungszertifikate regelmäßig, um einen Denial of Service zu vermeiden. Eine Tokenverschlüsselung ist erforderlich, wenn es sich bei der Anwendung einer abhängigen Seite um einen Webdienst handelt, der Proof-of-Possession-Token über das WS-Trust-Protokoll verwendet. In anderen Fällen ist eine Verschlüsselung optional. Nach dem Ablauf von Verschlüsselungszertifikaten erhalten Sie Fehlermeldungen, wenn Sie versuchen, ein Token anzufordern.
* **Token-Entschlüsselungszertifikate**. Erneuern Sie Token-Entschlüsselungszertifikate regelmäßig, um einen Denial of Service zu vermeiden. Azure AD Access Control kann verschlüsselte Token von WS-Federation-Identitätsanbietern annehmen (z. B. AD FS 2.0). Zur Entschlüsselung wird ein in Azure AD Access Control gehostetes X.509-Zertifikat verwendet. Nach dem Ablauf von Entschlüsselungszertifikaten erhalten Sie Fehlermeldungen, wenn Sie versuchen, ein Token anzufordern.
* **Anmeldeinformationen für die Dienstidentität**. Erneuern Sie die Anmeldeinformationen für die Dienstidentität regelmäßig, um einen Denial-of-Service zu vermeiden. Dienstidentitäten verwenden Anmeldeinformationen, die global für Ihren Azure AD Access Control-Namespace konfiguriert werden und die Anwendungen und Clients eine direkte Authentifizierung bei Azure AD Access Control ermöglichen, um einen Token zu erhalten. Die Azure AD Access Control-Dienstidentität kann drei Arten von Anmeldeinformationen zugeordnet werden: Symmetrischer Schlüssel, Kennwort und X.509-Zertifikat. Wenn die Anmeldeinformationen abgelaufen ist, beginnen Sie, Ausnahmen zu erhalten.
* **Kontoanmeldeinformationen für den Azure AD Access Control-Verwaltungsdienst**. Erneuern Sie die Anmeldeinformationen für den Verwaltungsdienst regelmäßig, um einen Denial-of-Service zu vermeiden. Der Verwaltungsdienst von Azure AD Access Control ist eine Schlüsselkomponente, mit der Sie die Einstellungen für den Azure AD Access Control-Namespace programmatisch verwalten und konfigurieren können. Das Verwaltungsdienstkonto kann mit drei Arten von Anmeldeinformationen verknüpft werden. Dabei handelt es sich um einen symmetrischen Schlüssel, ein Kennwort und ein X.509-Zertifikat. Wenn die Anmeldeinformationen abgelaufen ist, beginnen Sie, Ausnahmen zu erhalten.
* **Signatur- und Verschlüsselungszertifikate für WS-Federation-Identitätsanbieter**. Führen Sie eine Abfrage der Zertifikatgültigkeit für den WS-Federation-Identitätsanbieter durch, um einen Denial-of-Service zu vermeiden. Das Zertifikat des WS-Federation-Identitätsanbieters steht über seine Metadaten zur Verfügung. Beim Konfigurieren eines WS-Federation-Identitätsanbieters, z. B. AD FS, wird das WS-Federation-Signaturzertifikat über per URL oder als Datei erhältliche WS-Federation-Metadaten konfiguriert. Sobald Sie den WS-Federation-Identitätsanbieter konfiguriert haben, fragen Sie die Gültigkeit seiner Zertifikate mithilfe des Verwaltungsdiensts von Azure AD Access Control ab. Nach dem Ablaufen des Zertifikats, werden Sie anfangen, Ausnahmen zu erhalten.

## Freigegebenes Hosting über Azure-Websites

Alle in diesem Thema umrissenen Szenarien und Lösungen sind gültig, wenn die Anwendung auf Azure-Websites gehostet wird.

## Azure Virtual Machines

Alle in diesem Thema umrissenen Szenarien und Lösungen sind gültig, wenn die Anwendung auf Azure Virtual Machines gehostet wird.

## Ressourcen

* [Identity Developer Training Kit (nur in englischer Sprache)][69]
* [MSDN-hosted Identity Developer Training Course (in englischer Sprache)][70]
* [A Guide to Claims-Based Identity and Access Control (in englischer Sprache)][71]
* [Zugriffssteuerungsdienst (ACS)][72]
* [ACS -- Vorgehensweisen][10]
* [Secure Azure Web Role ASP.NET Web Application Using Access Control Service v2.0 (in englischer Sprache)][73]
* [Azure AD Access Control Service (ACS) Academy Videos (in englischer Sprache)][74]
* [Microsoft Security Development Lifecycle (in englischer Sprache)][75]
* [SDL Threat Modeling Tool 3.1.8 (in englischer Sprache)][76]
* [Security and Privacy Blogs (in englischer Sprache)][77]
* [Security Response Center (in englischer Sprache)][78]
* [Security Intelligence Report (in englischer Sprache)][79]
* [Security Development Lifecycle (in englischer Sprache)][75]
* [Security Developer Center (MSDN)][80]



[1]: http://blogs.msdn.com/b/jmeier/archive/2010/08/03/now-available-azure-security-notes-pdf.aspx
[2]: http://msdn.microsoft.com/de-de/library/ff649461.aspx
[3]: http://msdn.microsoft.com/de-de/library/ff650760.aspx
[4]: http://code.msdn.microsoft.com/site/search?f%5B0%5D.Type=SearchText&f%5B0%5D.Value=wif&f%5B1%5D.Type=Topic&f%5B1%5D.Value=claims-based%20authentication
[5]: http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e
[6]: http://www.microsoft.com/en-us/download/details.aspx?id=17331
[7]: http://www.microsoft.com/en-us/download/details.aspx?displaylang=en&id=4451
[8]: http://msdn.microsoft.com/library/gg429786.aspx
[9]: http://msdn.microsoft.com/de-de/library/gg185920.aspx
[10]: http://msdn.microsoft.com/de-de/library/windowsazure/gg185939.aspx
[11]: http://msdn.microsoft.com/de-de/library/ff423674.aspx
[12]: http://www.microsoft.com/en-us/download/details.aspx?id=14347
[13]: http://msdn.microsoft.com/de-de/IdentityTrainingCourse
[14]: http://social.technet.microsoft.com/wiki/contents/articles/2735.ad-fs-2-0-content-map.aspx
[15]: http://technet.microsoft.com/en-us/library/dd807033(WS.10).aspx
[16]: http://technet.microsoft.com/en-us/library/dd807050(WS.10).aspx
[17]: http://msdn.microsoft.com/de-de/library/ee393343.aspx
[18]: http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures
[19]: http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days
[20]: http://msdn.microsoft.com/de-de/library/gg429779.aspx
[21]: http://msdn.microsoft.com/de-de/library/gg185926.aspx
[22]: http://msdn.microsoft.com/de-de/library/gg185907.aspx
[23]: http://msdn.microsoft.com/de-de/library/gg185914.aspx
[24]: http://msdn.microsoft.com/de-de/library/gg185947.aspx
[25]: http://msdn.microsoft.com/de-de/library/gg185938.aspx
[26]: http://msdn.microsoft.com/de-de/library/gg185924.aspx
[27]: http://msdn.microsoft.com/de-de/library/hh289316.aspx
[28]: http://msdn.microsoft.com/de-de/library/gg185954.aspx
[29]: http://msdn.microsoft.com/de-de/library/gg185952.aspx
[30]: http://msdn.microsoft.com/de-de/library/gg185927.aspx
[31]: http://msdn.microsoft.com/de-de/library/gg185961.aspx
[32]: http://msdn.microsoft.com/de-de/library/gg185905.aspx
[33]: http://msdn.microsoft.com/de-de/library/hh127796.aspx
[34]: http://msdn.microsoft.com/de-de/library/gg185958.aspx
[35]: http://msdn.microsoft.com/de-de/library/hh289317.aspx
[36]: http://msdn.microsoft.com/de-de/library/gg983271.aspx
[37]: http://code.msdn.microsoft.com/REST-WCF-With-SWT-Token-123d93c0
[38]: http://msdn.microsoft.com/de-de/library/gg185976.aspx
[39]: http://msdn.microsoft.com/de-de/library/gg185919.aspx
[40]: http://msdn.microsoft.com/de-de/library/gg185977.aspx
[41]: http://code.msdn.microsoft.com/ASPNET-Web-App-To-REST-WCF-b2b95f82
[42]: http://msdn.microsoft.com/de-de/library/gg185955.aspx
[43]: http://blogs.msdn.com/b/alikl/archive/2010/11/18/authorization-with-rolemanager-for-claims-aware-wif-asp-net-web-applications.aspx
[44]: http://www.microsoft.com/downloads/details.aspx?FamilyID=c148b2df-c7af-46bb-9162-2c9422208504
[45]: http://msdn.microsoft.com/de-de/library/windowsazure/ff394108.aspx#authentication
[46]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336280.aspx
[47]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336243.aspx
[48]: http://msdn.microsoft.com/de-de/library/windowsazure/ee621781.aspx
[49]: http://msdn.microsoft.com/de-de/library/windowsazure/ee621789.aspx
[50]: http://msdn.microsoft.com/de-de/library/windowsazure/ff394110.aspx
[51]: http://msdn.microsoft.com/de-de/library/windowsazure/gg715284.aspx
[52]: http://msdn.microsoft.com/de-de/library/windowsazure/ff951633.aspx
[53]: http://channel9.msdn.com/posts/Securing-Service-Bus-with-ACS
[54]: https://skydrive.live.com/view.aspx?cid=123CCD2A7AB10107&resid=123CCD2A7AB10107%211849
[55]: http://msdn.microsoft.com/de-de/library/hh403962.aspx
[56]: http://msdn.microsoft.com/de-de/library/windowsazure/gg618003.aspx
[57]: http://msdn.microsoft.com/de-de/library/windowsazure/gg278346.aspx
[58]: http://msdn.microsoft.com/de-de/library/ee706741.aspx
[59]: http://msdn.microsoft.com/de-de/library/gg193417.aspx
[60]: http://go.microsoft.com/fwlink/?LinkId=219162
[61]: http://go.microsoft.com/fwlink/?LinkId=219163
[62]: http://go.microsoft.com/fwlink/?LinkId=221323
[63]: https://datamarket.azure.com/
[64]: http://msdn.microsoft.com/de-de/library/ee517298.aspx
[65]: http://blogs.msdn.com/b/alikl/archive/2010/12/02/windows-identity-foundation-wif-security-for-asp-net-web-applications-threats-amp-countermeasures.aspx
[66]: http://social.technet.microsoft.com/wiki/contents/articles/1725.windows-identity-foundation-wif-a-potentially-dangerous-request-form-value-was-detected-from-the-client-wresult-t-requestsecurityto.aspx
[67]: http://msdn.microsoft.com/de-de/library/gg185962.aspx
[68]: http://msdn.microsoft.com/de-de/library/hh204521.aspx
[69]: http://go.microsoft.com/fwlink/?LinkId=214555
[70]: http://go.microsoft.com/fwlink/?LinkId=214561
[71]: http://go.microsoft.com/fwlink/?LinkId=214562
[72]: http://msdn.microsoft.com/de-de/library/windowsazure/gg429786.aspx
[73]: http://social.technet.microsoft.com/wiki/contents/articles/2590.aspx
[74]: http://social.technet.microsoft.com/wiki/contents/articles/2777.aspx
[75]: http://www.microsoft.com/security/sdl/default.aspx
[76]: http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=2955
[77]: http://www.microsoft.com/about/twc/en/us/blogs.aspx
[78]: http://www.microsoft.com/security/msrc/default.aspx
[79]: http://www.microsoft.com/security/sir/
[80]: http://msdn.microsoft.com/security/