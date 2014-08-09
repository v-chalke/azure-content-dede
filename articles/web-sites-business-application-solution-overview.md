<properties linkid="websites-business-application" urlDisplayName="Create a Line-of-Business Application on Azure Web Sites" pageTitle="Create a Line-of-Business Application on Azure Web Sites" metaKeywords="Web Sites" description="This guide provides a technical overview of how to use Azure Web Sites to create intranet, line-of-business applications. This includes authentication strategies, service bus relay, and monitoring." umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="paulettm" title="Create a Line-of-Business Application on Azure Web Sites" authors="" />

Erstellen einer Branchenanwendung auf Azure-Websites
====================================================

Dieser Leitfaden bietet eine technische Übersicht über die Verwendung von Azure-Websites zur Erstellung von Branchenanwendungen. In diesem Dokument wird davon ausgegangen, dass es sich bei diesen Anwendungen um Intranetanwendungen handelt, die für interne Geschäftszwecke geschützt werden sollen. Es gibt zwei charakteristische Merkmale von Geschäftsanwendungen. Diese Anwendungen erfordern Authentifizierung, in der Regel mit einem Unternehmensverzeichnis. Und sie benötigen normalerweise Zugriff auf oder Integration in lokale Daten und Dienste. In diesem Leitfaden geht es primär um die Erstellung von Geschäftsanwendungen auf [Azure-Websites](/de-de/documentation/services/web-sites/). Es gibt jedoch Situationen, in denen [Azure-Clouddienste](/de-de/documentation/services/cloud-services/) oder [virtuelle Azure-Computer](/de-de/documentation/services/virtual-machines/) die bessere Wahl für Ihre Anforderungen wären. Sehen Sie sich unbedingt die Unterschiede dieser Optionen im Thema [Azure-Websites, Clouddienste und virtuelle Computer: Wann eignet sich welche Komponente?](/de-de/manage/services/web-sites/choose-web-app-service) an.

In diesem Leitfaden werden die folgenden Themen behandelt:

-   [Die Vorteile](#benefits)
-   [Die Wahl einer Authentifizierungsstrategie](#authentication)
-   [Erstellen einer Azure-Website zur Unterstützung von Authentifizierung](#createintranetsite)
-   [Verwenden von Service Bus zur Integration in lokale Ressourcen](#servicebusrelay)
-   [Überwachen der Anwendung](#monitor)

**Hinweis**

In diesem Leitfaden werden einige der häufigsten Themen und Aufgaben vorgestellt, die mit der öffentlichen .COM-Websiteentwicklung abgestimmt werden. Azure-Websites bieten jedoch noch weitere Funktionen, die Sie in Ihrer speziellen Implementierung nutzen können. Weitere Informationen zu diesen Funktionen finden Sie in den anderen Leitfäden unter [Globale Webpräsenz](http://www.windowsazure.com/de-de/manage/services/web-sites/global-web-presence-solution-overview/) und [Digitale Marketingkampagnen](http://www.windowsazure.com/de-de/manage/services/web-sites/digital-marketing-campaign-solution-overview) (beides in englischer Sprache).

Die Vorteile
------------

Da Branchenanwendungen in der Regel auf Unternehmensanwender ausgerichtet sind, sollten Sie die Vorteile der Verwendung der Cloud anstelle von lokalen Unternehmensressourcen und lokaler Infrastruktur bedenken. Zunächst wären da die typischen Vorteile eines Wechsels zur Cloud, wie beispielsweise die flexible Skalierbarkeit mit dynamischen Workloads. Nehmen wir als Beispiel eine Anwendung für jährliche Mitarbeiterbeurteilungen. Die meiste Zeit des Jahres fällt für diese Art von Anwendung nur wenig Verkehr an. Aber während der Beurteilungsphase steigt der Verkehr in einem großen Unternehmen rasant an. Azure bietet Skalierungsoptionen, mit deren Hilfe das Unternehmen horizontal skalieren kann, um die Last während des verkehrsreichen Bewertungszeitraums zu verarbeiten. Gleichzeitig kann das Unternehmen Geld sparen, indem es die Kapazitäten dieser Anwendung für den Rest des Jahres wieder reduziert. Ein weiterer Vorteil der Cloud ist, dass sich Unternehmen verstärkt auf die Anwendungsentwicklung konzentrieren können, da sie sich weniger um die Anschaffung und das Management der Infrastruktur kümmern müssen.

Neben diesen gängigen Vorteilen profitieren auch Mitarbeiter und Partner vom Verschieben einer Geschäftsanwendung in die Cloud: Sie können von überall aus auf die Anwendung zugreifen. Benutzer müssen nicht mit dem Unternehmensnetzwerk verbunden sein, um die Anwendung zu nutzen, und die IT-Abteilung muss keine komplexen Reverseproxy-Lösungen einsetzen. Es gibt verschiedene Authentifizierungsoptionen, um den Zugriff auf Unternehmensanwendungen zu schützen. Diese Optionen werden in den folgenden Abschnitten dieses Leitfadens erläutert.

Die Wahl einer Authentifizierungsstrategie
------------------------------------------

Bei dem Szenario mit der Geschäftsanwendung ist Ihre Authentifizierungsstrategie eine der wichtigsten Entscheidungen. Verschiedene Optionen stehen zur Verfügung:

-   Verwenden Sie den [Azure Active Directory-Dienst](/de-de/documentation/services/active-directory/). Sie können diesen als eigenständiges Verzeichnis verwenden, oder Sie synchronisieren ihn mit einem lokalen Active Directory. Anwendungen interagieren dann mit dem Azure Active Directory, um Benutzer zu authentifizieren. Einen Überblick über diesen Ansatz finden Sie unter [Verwenden von Azure Active Directory](/de-de/manage/windows/fundamentals/identity/#ad).
-   Verwenden Sie virtuelle Azure Computer und das virtuelle Netzwerk zur Installation des Active Directory. So haben Sie die Möglichkeit, eine lokale Active Directory-Installation in die Cloud zu erweitern. Sie können auch Active Directory-Verbunddienste (ADFS) verwenden, um einen Verbund von Identitätsanforderungen zu erstellen und an das lokale AD weiterzuleiten. Die Authentifizierung für die Azure-Anwendung wird dann über ADFS an das lokale Active Directory weitergeleitet. Weitere Informationen zu diesem Ansatz finden Sie unter [Ausführen von Windows Server Active Directory auf VMs](/de-de/manage/windows/fundamentals/identity/#adinvm) und [Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Azure](http://msdn.microsoft.com/de-de/library/windowsazure/jj156090.aspx).
-   Verwenden Sie einen Zwischendienst wie den [Azure-Zugriffssteuerungsdienst](http://msdn.microsoft.com/library/windowsazure/hh147631.aspx) (Access Control Service, ACS), um mehrere Identitätsdienste zur Authentifizierung von Benutzern zu einzusetzen. Daraus ergibt sich eine Abstraktion, um Benutzer entweder über Active Directory oder über einen anderen Identitätsanbieter zu authentifizieren. Weitere Informationen finden Sie unter [Verwenden von Azure Active Directory Access Control](/de-de/manage/windows/fundamentals/identity/#ac).

Bei diesem Szenario mit einer Geschäftsanwendung bietet die erste Option mit der Verwendung von Azure Active Directory die schnellste Möglichkeit zur Implementierung einer Authentifizierungsstrategie für Ihre Anwendung. In den folgenden Teilen dieses Leitfadens geht es primär um Azure Active Directory. Je nach Ihren Geschäftsanforderungen eignet sich möglicherweise eine der beiden Lösungen besser für Sie. Wenn Sie beispielsweise keine Identitätsinformationen in der Cloud synchronisieren dürfen, ist die ADFS-Lösung für Sie möglicherweise die bessere Option. Dies gilt auch, wenn Sie andere Identitätsanbieter wie Facebook unterstützen müssen.

Bevor Sie ein neues Azure Active Directory einrichten, sollten Sie beachten, dass vorhandene Dienste wie Office 365 oder Windows Intune bereits Azure Active Directory verwenden. In diesem Fall sollten Sie Ihr vorhandenes Abonnement mit Ihrem Azure-Abonnement verknüpfen. Weitere Informationen erhalten Sie unter [Was ist ein Windows Azure AD-Mandant?](http://technet.microsoft.com/en-us/library/jj573650.aspx).

Falls Sie aktuell nicht über einen Dienst verfügen, der Azure Active Directory bereits verwendet, können Sie ein neues Verzeichnis im Verwaltungsportal erstellen. Verwenden Sie den Bereich **Active Directory** im Verwaltungsportal, um ein neues Verzeichnis zu erstellen.

![BusinessApplicationsAzureAD](./media/web-sites-business-application-solution-overview/BusinessApplications_AzureAD.png)

Sobald das Verzeichnis erstellt ist, können Sie Benutzer, integrierte Anwendungen und Domänen erstellen und verwalten.

![BusinessApplicationsADUsers](./media/web-sites-business-application-solution-overview/BusinessApplications_AD_Users.png)

Die vollständige Vorgehensweise finden Sie unter [Adding Sign-On to Your Web Application Using Azure AD](http://msdn.microsoft.com/library/windowsazure/dn151790.aspx) (Anmeldung mithilfe von Azure AD zu Ihrer Webanwendung hinzufügen, in englischer Sprache). Wenn Sie dieses neue Verzeichnis als eigenständige Ressource verwenden, müssen Sie im nächsten Schritt Anwendungen entwickeln, die in das Verzeichnis integriert sind. Wenn Sie jedoch über lokale Active Directory-Identitäten verfügen, möchten Sie diese in der Regel mit dem neuen Azure Active Directory synchronisieren. Weitere Informationen zur Vorgehensweise finden Sie unter [Verzeichnisintegration](http://technet.microsoft.com/en-us/library/jj573653.aspx).

Sobald das Verzeichnis erstellt und gefüllt ist, müssen Sie Webanwendungen erstellen, die eine Authentifizierung erfordern, und diese in das Verzeichnis integrieren. Diese Schritte werden in den folgenden beiden Abschnitten behandelt.

Erstellen einer Azure-Website zur Unterstützung von Authentifizierung
---------------------------------------------------------------------

Im Szenario zur globalen Webpräsenz haben wir verschiedene Optionen für die Erstellung und die Bereitstellung einer neuen Website erläutert. Falls Sie neu bei Azure-Websites sind, empfehlen wir Ihnen, sich [diese Informationen anzusehen](/de-de/manage/services/web-sites/global-web-presence-solution-overview/). Eine ASP.NET-Anwendung in Visual Studio ist eine gängige Intranet-Webanwendung, die Windows-Authentifizierung verwendet. Einer der Gründe hierfür sind die enge Integration und die Unterstützung dieses Szenarios durch ASP.NET und Visual Studio.

Wenn Sie beispielsweise ein ASP.NET MVC 4-Projekt in Visual Studio erstellen, können Sie im Dialog für die Projekterstellung eine **Intranetanwendung** erstellen.

![BusinessApplicationsVSIntranetApp](./media/web-sites-business-application-solution-overview/BusinessApplications_VS_IntranetApp.png)

Dadurch werden Änderungen an den Projekteinstellungen vorgenommen, um Windows Authentifizierung zu unterstützen. Insbesondere wird in der Datei "web.config" für das Attribut **Modus** des Elements **Authentifizierung** **Windows** festgelegt. Wenn Sie ein anderes ASP.NET-Projekt erstellen, beispielsweise ein Web Forms-Projekt, oder wenn Sie an einem bestehenden Projekt arbeiten, müssen Sie diese Änderung manuell vornehmen.

Bei einem MVC-Projekt müssen Sie außerdem zwei Werte im Fenster mit den Projekteigenschaften ändern. Legen Sie für **Windows-Authentifizierung** **Aktiviert** fest und für **Anonyme Authentifizierung** **Deaktiviert**.

![BusinessApplicationsVSProperties](./media/web-sites-business-application-solution-overview/BusinessApplications_VS_Properties.png)

Registrieren Sie diese Anwendung im Verzeichnis, und ändern Sie diese Anwendungskonfiguration für Verbindungen, um die Authentifizierung mit Azure Active Directory durchzuführen. Hierzu gibt es zwei Möglichkeiten in Visual Studio:

-   [Identitäts- und Zugriffstool für Visual Studio](#identityandaccessforvs)
-   [Microsoft ASP.NET-Tools für Azure Active Directory](#aspnettoolsforwaad)

### Identitäts- und Zugriffstool für Visual Studio:

Eine Möglichkeit ist die Verwendung des [Identity and Access Tool](http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e), das Sie herunterladen und installieren können. Dieses Tool integriert Visual Studio in das Kontextmenü des Projekts. Die folgenden Anweisungen und Screenshots basieren auf Visual Studio 2012. Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Identity and Access**. Drei Einstellungen müssen konfiguriert werden. Geben Sie auf der Registerkarte **Eigenschaften** den **Pfad zum STS-Metadaten-Dokument** und den **URI der APP-ID** an. (Im Abschnitt [Registrieren von Anwendungen im Azure Active Directory](#registerwaadapp) erfahren Sie, wie Sie diese Werte abrufen.

![BusinessApplicationsVSIdentityAndAccess](./media/web-sites-business-application-solution-overview/BusinessApplications_VS_IdentityAndAccess.png)

Die letzte Konfigurationsänderung muss auf der Registerkarte **Konfiguration** des Dialogfelds **Identity and Access** vorgenommen werden. Aktivieren Sie das Kontrollkästchen **Enable web farm cookies**. Die detaillierte Vorgehensweise finden Sie unter [Adding Sign-On to Your Web Application Using Azure AD](http://msdn.microsoft.com/library/windowsazure/dn151790.aspx) (Anmeldung mithilfe von Azure AD zu Ihrer Webanwendung hinzufügen, in englischer Sprache).

#### Registrieren von Anwendungen in Azure Active Directory:

Registrieren Sie Ihre Anwendung bei Azure Active Directory, um die Registerkarte **Anbieter** auszufüllen. Wählen Sie im Azure-Verwaltungsportal im Bereich **Active Directory** Ihr Verzeichnis aus, und rufen Sie dann die Registerkarte **Anwendungen** auf. Sie haben dann die Möglichkeit, Ihre Azure-Website per URL hinzuzufügen. Beachten Sie beim Durchführen dieser Schritte, dass Sie zunächst die URL zur localhost-Adresse festlegen, die für das lokale Debugging in Visual Studio angegeben wurde. Später bei der Bereitstellung können Sie diese dann durch die tatsächliche URL Ihrer Website ersetzen.

![BusinessApplicationsADIntegratedApps](./media/web-sites-business-application-solution-overview/BusinessApplications_AD_IntegratedApps.png)

Sobald Sie diese hinzugefügt haben, stellt das Portal den Pfad zum STS-Metadaten-Dokument (die **URL für das Verbundmetadatendokument**) und den **URI der APP-ID** bereit. Dieser Werte werden auf der Registerkarte **Anbieter** im Dialogfeld **Identity and Access** in Visual Studio verwendet.

![BusinessApplicationsADAppAdded](./media/web-sites-business-application-solution-overview/BusinessApplications_AD_AppAdded.png)

### Microsoft ASP.NET-Tools für Azure Active Directory:

Alternativ zu den zuvor beschriebenen Schritten können Sie auch die [Microsoft ASP.NET-Tools für Azure Active Directory](http://go.microsoft.com/fwlink/?LinkID=282306) verwenden. Klicken Sie hierzu im Menü **Projekt** in Visual Studio auf das Element **Enable Azure Authentication**. Daraufhin wird ein einfaches Dialogfeld angezeigt, das zur Eingabe der Adresse Ihrer Azure Active Directory-Domäne auffordert (nicht die URL für Ihre Anwendung).

![BusinessApplicationsVSEnableAuth](./media/web-sites-business-application-solution-overview/BusinessApplications_VS_EnableAuth.png)

Wenn Sie der Administrator dieser Active Directory-Domäne sind, aktivieren Sie das Kontrollkästchen **Provision this application in the Azure AD**. Daraufhin wird die Anwendung bei Active Directory registriert. Falls Sie nicht der Administrator sind, deaktivieren Sie dieses Kontrollkästchen, und geben Sie die Informationen an, die einem Administrator angezeigt werden. Dieser Administrator kann das Verwaltungsportal verwenden, um eine integrierte Anwendung anhand der vorherigen Schritte mit dem Identitäts- und Zugriffstool zu erstellen. Die detaillierten Schritte zur Verwendung von ASP.NET-Tools für Azure Active Directory finden Sie unter [Windows Azure-Authentifizierung](http://www.asp.net/aspnet/overview/aspnet-and-visual-studio-2012/windows-azure-authentication).

Für die Verwaltung Ihrer Branchenanwendung können Sie ein beliebiges unterstütztes Quellcodeverwaltungssystem für die Bereitstellung verwenden. Aufgrund der sehr engen Integration in Visual Studio in diesem Szenario wählen Sie vermutlich Team Foundation Service (TFS) als Quellcodeverwaltungssystem. Beachten Sie in diesem Fall, dass Azure-Websites Integration mit TFS bietet. Rufen Sie im Verwaltungsportal die Registerkarte **Dashboard** Ihrer Website auf. Wählen Sie dann **Set up deployment from source control**. Befolgen Sie die Anweisungen zur Verwendung von TFS.

![BusinessApplicationsDeploy](./media/web-sites-business-application-solution-overview/BusinessApplications_Deploy.png)

Verwenden von Service Bus zur Integration in lokale Ressourcen
--------------------------------------------------------------

Viele Branchenanwendungen müssen in lokale Daten und Dienste integriert werden. Es gibt verschiedene Gründe, warum bestimmte Datentypen nicht in die Cloud verschoben werden können. Hierbei kann es sich um praktische oder rechtliche Gründe handeln. Falls Sie sich in der Planungsphase befinden und festlegen, welche Daten in Azure gehostet und welche lokal gespeichert werden sollen, sollten Sie sich unbedingt die Ressourcen im [Azure-Vertrauensstellungscenter](/en-us/support/trust-center/) ansehen. Hybride Webanwendungen werden in Azure ausgeführt und greifen auf Ressourcen zu, die lokal gespeichert werden müssen.

Wenn Sie virtuelle Computer oder Clouddienste verwenden, können Sie Anwendungen in Azure über ein virtuelles Netzwerk mit einem Unternehmensnetzwerk verbinden. Websites unterstützt jedoch keine virtuellen Netzwerke. Für diese Art der Integration mit Websites empfiehlt sich die Verwendung des Dienstes [Azure Service Bus-Relay](http://msdn.microsoft.com/de-de/library/windowsazure/jj860549.aspx). Mithilfe des Service Bus-Relay können Anwendungen in der Cloud sicher mit WCF-Diensten verbunden werden, die in einem Unternehmensnetzwerk ausgeführt werden. Dank Service Bus kann diese Kommunikation ohne Öffnen von Firewall-Ports erfolgen.

In dem Diagramm unten kommunizieren sowohl die Cloud-Anwendung als auch der lokale WCF-Dienst über einen zuvor erstellen Namespace mit Service Bus. Der lokale WCF-Dienst hat Zugriff auf interne Daten und Dienste, die nicht in die Cloud verschoben werden können. Der WCF registriert einen Endpunkt im Namespace. Die Website, die in Azure ausgeführt wird, verbindet sich in Service Bus ebenfalls mit diesem Endpunkt. Sie müssen lediglich in der Lage sein, ausgehende öffentliche HTTP-Anforderungen zu erstellen, um diesen Schritt abzuschließen.

![BusinessApplicationsServiceBusRelay](./media/web-sites-business-application-solution-overview/BusinessApplications_ServiceBusRelay.png)

Service Bus stellt dann eine Verbindung zwischen der Cloud-Anwendung und dem lokalen WCF-Dienst her. Dadurch entsteht eine Basisarchitektur für die Erstellung hybrider Anwendungen, die sowohl Azure- als auch lokale Ressourcen verwenden. Weitere Informationen finden Sie unter [Verwenden des Service Bus-Relay-Diensts](/de-de/develop/net/how-to-guides/service-bus-relay/) und im [Lernprogramm zu Service Bus-Relaymessaging](http://msdn.microsoft.com/de-de/library/windowsazure/ee706736.aspx). Ein Beispiel, bei dem diese Technik angewendet wird, finden Sie unter [Enterprise Pizza - Connecting Web Sites to On-premise Using Service Bus](http://code.msdn.microsoft.com/windowsazure/Enterprise-Pizza-e2d8f2fa) (Enterprise Pizza – Verbinden von Websites mit lokalen Ressourcen mithilfe von Service Bus, in englischer Sprache).

Überwachen der Anwendung
------------------------

Geschäftsanwendungen profitieren von den Website-Standardfunktionen wie Skalierung und Überwachung. Bei einer Geschäftsanwendung, die an bestimmten Tagen oder Uhrzeiten unterschiedliche Lasten verarbeiten muss, kann die Funktion für automatische Skalierung (Vorschau) bei der horizontalen Skalierung und der Reduzierung der Kapazitäten unterstützen, um die Ressourcen effizient zu nutzen. Die Überwachungsfunktionen umfassen Endpunktüberwachung und Überwachung des Kontingents. Alle diese Optionen wurden in den Szenarien [Globale Webpräsenz](/de-de/manage/services/web-sites/global-web-presence-solution-overview/) und [Digitale Marketingkampagnen](/de-de/manage/services/web-sites/digital-marketing-campaign-solution-overview) detaillierter abgedeckt.

Die Überwachungsanforderungen können zwischen verschiedenen Branchenanwendungen variieren, die eine unterschiedlich hohe Bedeutung für das Unternehmen haben. Für die geschäftskritischeren Anwendungen sollten Sie die Investition in einer Drittanbieter-Überwachungslösung wie [New Relic](http://newrelic.com/azure) in Erwägung ziehen.

Branchenanwendungen werden in der Regel von IT-Mitarbeitern verwaltet. Im Falle von unerwarteten Fehlern oder unerwartetem Verhalten können IT-Mitarbeiter eine detailliertere Protokollierung aktivieren und die daraus resultierenden Daten analysieren, um die Probleme zu ermitteln. Navigieren Sie im Verwaltungsportal zur Registerkarte **Configure**, und prüfen Sie die Optionen in den Bereichen **application diagnostics** und **site diagnostics**.

![BusinessApplicationsDiagnostics](./media/web-sites-business-application-solution-overview/BusinessApplications_Diagnostics.png)

Nutzen Sie die verschiedenen Anwendungs- und Site-Protokolle, um Probleme mit der Website zu beheben. Beachten Sie, dass einige der Optionen **Dateisystem** angeben. Dadurch werden die Protokolldateien im Dateisystem ihrer Site gespeichert. Auf diese kann entweder über FTP, Azure PowerShell oder die Azure-Befehlszeilentools zugegriffen werden. Bei anderen Optionen ist **Speicher** angegeben. Dadurch werden die Informationen an das von Ihnen angegebene Azure-Speicherkonto gesendet. Bei der **Webserverprotokollierung** können Sie außerdem ein Festplattenkontingent für das Dateisystem festlegen oder eine Aufbewahrungsrichtlinie für die Speicheroption. So wird verhindert, dass eine unendlich große Menge an gespeicherten Protokolldaten aufbewahrt wird.

![BusinessApplicationsDiagRetention](./media/web-sites-business-application-solution-overview/BusinessApplications_Diag_Retention.png)

Weitere Informationen zu diesen Protokolleinstellungen finden Sie unter [Vorgehensweise: Diagnosekonfiguration und Herunterladen von Protokollen für eine Website](/de-de/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics).

Sie können die nicht aufbereiteten Protokolle über FTP oder Speicherdienstprogramme wie Azure-Speicher-Explorer öffnen, oder Sie können die Protokollinformationen in Visual Studio anzeigen. Detaillierte Informationen zur Verwendung dieser Protokolle bei der Fehlerbehebung finden Sie unter [Problembehandlung von Azure-Websites in Visual Studio](/de-de/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

Zusammenfassung
---------------

Mit Azure können Sie sichere Intranetanwendungen in der Cloud hosten. Azure Active Directory bietet Ihnen die Möglichkeit, Benutzer zu authentifizieren, damit nur Mitglieder Ihres Unternehmens auf die Anwendungen zugreifen können. Der Service Bus-Relay-Dienst ermöglicht Webanwendungen die Kommunikation mit lokalen Diensten und Daten. Dank dieses hybriden Anwendungsansatzes lässt sich eine Geschäftsanwendung einfacher in der Cloud veröffentlichen, die zugehörigen Daten und Dienste müssen nicht ebenfalls migriert werden. Nach ihrer Bereitstellung profitieren die Geschäftsanwendungen von den Standardfunktionen für Skalierung und Überwachung von Azure Websites. Weitere Informationen finden Sie in den folgenden Fachartikeln:

<table data-morhtml="true" cellspacing="0" border="1">
<tr data-morhtml="true">
   <th data-morhtml="true" align="left" valign="top">Bereich</th>
   <th data-morhtml="true" align="left" valign="top">Ressourcen</th>
</tr>
<tr data-morhtml="true">
   <td data-morhtml="true" valign="middle"><strong data-morhtml="true">Planen</strong></td>
   <td data-morhtml="true" valign="top">- <a data-morhtml="true" href="http://www.windowsazure.com/de-de/manage/services/web-sites/choose-web-app-service">Azure-Websites, Clouddienste und virtuelle Computer: Wann eignet sich welche Komponente?</a></td>
</tr>
<tr data-morhtml="true">
   <td data-morhtml="true" valign="middle"><strong data-morhtml="true">Erstellen und Bereitstellen</strong></td>
   <td data-morhtml="true" valign="top">- <a data-morhtml="true" href="http://www.windowsazure.com/de-de/develop/net/tutorials/get-started/">Bereitstellen einer ASP.NET-Webanwendung f&uuml;r eine Azure-Website</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://www.windowsazure.com/de-de/develop/net/tutorials/web-site-with-sql-database/">Bereitstellen einer sicheren ASP.NET MVC f&uuml;r Azure</a></td>
</tr>
<tr data-morhtml="true">
   <td data-morhtml="true" valign="middle"><strong data-morhtml="true">Authentifizierung</strong></td>
   <td data-morhtml="true" valign="top">- <a data-morhtml="true" href="http://www.windowsazure.com/de-de/manage/windows/fundamentals/identity/">&Uuml;bersicht &uuml;ber die Azure-Identit&auml;tsoptionen</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://www.windowsazure.com/de-de/documentation/services/active-directory/">Azure Active Directory-Dienst</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://technet.microsoft.com/en-us/library/jj573650.aspx">Was ist ein Windows Azure AD-Mandant?</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://msdn.microsoft.com/library/windowsazure/dn151790.aspx">Adding Sign-On to Your Web Application Using Azure AD</a> (Anmeldung mithilfe von Azure AD zu Ihrer Webanwendung hinzuf&uuml;gen, in englischer Sprache)<br data-morhtml="true" />- <a data-morhtml="true" href="http://www.asp.net/aspnet/overview/aspnet-and-visual-studio-2012/windows-azure-authentication">Lernprogramm zur Azure-Authentifizierung</a></td>
</tr>
<tr data-morhtml="true">
   <td data-morhtml="true" valign="middle"><strong data-morhtml="true">Service Bus-Relay</strong></td>
   <td data-morhtml="true" valign="top">- <a data-morhtml="true" href="http://www.windowsazure.com/de-de/develop/net/how-to-guides/service-bus-relay/">How to Use the Service Bus Relay Service</a> (Verwenden des Service Bus-Relay-Diensts, in englischer Sprache)<br data-morhtml="true" />- <a data-morhtml="true" href="http://msdn.microsoft.com/de-de/library/windowsazure/ee706736.aspx">Lernprogramm zu Service Bus-Relaymessaging</a></td>
</tr>
<tr data-morhtml="true">
   <td data-morhtml="true" valign="middle"><strong data-morhtml="true">&Uuml;berwachen</strong></td>
   <td data-morhtml="true" valign="top">- <a data-morhtml="true" href="http://www.windowsazure.com/de-de/manage/services/web-sites/how-to-monitor-websites/">&Uuml;berwachen von Websites</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://msdn.microsoft.com/library/windowsazure/dn306638.aspx">Vorgehensweise: Empfangen von Warnbenachrichtigungen und Verwalten von Warnungsregeln in Azure</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://www.windowsazure.com/de-de/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics">Vorgehensweise: Diagnosekonfiguration und Herunterladen von Protokollen f&uuml;r eine Website</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://www.windowsazure.com/de-de/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/">Problembehandlung von Azure-Websites in Visual Studio</a></td>
</tr>
</table>

