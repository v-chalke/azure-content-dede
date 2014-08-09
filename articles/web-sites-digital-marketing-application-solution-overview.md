<properties linkid="websites-digital-marketing" urlDisplayName="Resources" pageTitle="Create a Digital Marketing Campaign on Azure Web Sites" metaKeywords="" description="This guide provides a technical overview of how to use Azure Web Sites to create digital marketing campaigns. This includes deployment, social media integration, scaling strategies, and monitoring." metaCanonical="" services="" documentationCenter="" title="Create a Digital Marketing Campaign on Azure Web Sites" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

Erstellen einer digitalen Marketingkampagne mithilfe von Azure-Websites
=======================================================================

Dieser Leitfaden bietet einen technischen Überblick, wie Azure-Websites zum Erstellen von digitalen Marketingkampagnen verwendet werden. Bei einer digitalen Marketingkampagne handelt es sich normalerweise um eine kurzlebige Entität, die ein kurzfristiges Marketingziel vorantreiben soll. Zwei wichtige Szenarien müssen berücksichtigt werden. Beim ersten Szenario erstellt ein fremdes Marketingunternehmen die Kampagne für ihre Kunden und verwaltet diese während der Aktionsdauer. Beim zweiten Szenario erstellt das Marketingunternehmen die Kampagne und überträgt anschließend den Besitz der Ressourcen für die digitale Marketingkampagne auf ihren Kunden. Danach führt der Kunde die digitale Marketingkampagne aus und verwaltet diese selbst.

[Azure-Websites](/de-de/documentation/services/web-sites/) sind für beide Szenarien bestens geeignet. Mit deren Hilfe ist eine schnelle Erstellung möglich. Darüber hinaus werden mehrere Frameworks, Sprachen und Skalierungen unterstützt, um Benutzeranforderungen nachzukommen. Zahlreiche Bereitstellungs- und Quellcodeverwaltungssysteme stehen außerdem zur Verfügung. Wenn Sie Azure verwenden, haben Sie auch Zugriff auf die anderen Azure-Dienste, z. B. Media Services, wodurch eine Marketingkampagne optimiert werden kann.

Obwohl [Azure-Clouddienste](/de-de/documentation/services/cloud-services/) oder [Azure Virtual Machines](/de-de/documentation/services/virtual-machines/) zum Hosten von Websites verwendet werden können, sind diese Komponenten nicht die ideale Lösung für dieses Szenario, es sei denn, dass eine bestimmte Funktion erforderlich ist, über die die Azure-Websites nicht verfügen. Weitere Informationen zu den Optionen finden Sie unter [Azure-Websites, Clouddienste und virtuelle Computer: Wann eignet sich welche Komponente?](/de-de/manage/services/web-sites/choose-web-app-service)

In diesem Leitfaden werden die folgenden Themen behandelt:

-   [Bereitstellen von bereits vorhandenen Websites](#deployexisting)
-   [Integrieren mit sozialen Medien](#socialmedia)
-   [Skalieren mithilfe der Benutzeranforderung](#scale)
-   [Integrieren mit anderen Diensten](#integrate)
-   [Überwachen der Kampagne](#monitor)

**Hinweis**

In diesem Leitfaden werden einige der häufigsten Themen und Aufgaben vorgestellt, die mit der öffentlichen .COM-Websiteentwicklung abgestimmt werden. Azure-Websites bieten jedoch noch weitere Funktionen, die Sie in Ihrer speziellen Implementierung nutzen können. Informationen zu diesen Funktionen finden Sie auch in den anderen Leitfäden unter [Globale Webpräsenz](http://www.windowsazure.com/de-de/manage/services/web-sites/global-web-presence-solution-overview/) und [Geschäftsanwendungen](http://www.windowsazure.com/de-de/manage/services/web-sites/business-application-solution-overview).

Bereitstellen von bereits vorhandenen Websites
----------------------------------------------

Im Szenario zur globalen Webpräsenz haben wir verschiedene Optionen für die Erstellung und die Bereitstellung einer neuen Website erläutert. Falls Sie neu bei Azure-Websites sind, empfehlen wir Ihnen, sich [diese Informationen anzusehen](/de-de/manage/services/web-sites/global-web-presence-solution-overview/). Wenn Sie des Öfteren digitale Marketingkampagnen erstellen, ist es möglich, dass Sie über bereits vorhandene Webressourcen verfügen, die Sie für verschiedene Aktionen anpassen. In diesem Abschnitt werden die Optionen zum Bereitstellen verschiedener Website-Typen aus der Quellcodeverwaltung genauer erläutert.

Wenn Sie Webressourcen für verschiedene Zwecke erneut verwenden, sollten Sie ein Quellcodeverwaltungssystem unbedingt in Betracht ziehen, falls Sie noch kein System dieser Art nutzen. Mithilfe dieses Systems können Sie Vorlagen von allgemeinen Weblösungen speichern, die für bestimmte Kunden verzweigt und angepasst werden können. Bei Websites ist die Synchronisierung mit vielen verschiedenen Quellcode-Repositorys möglich. Wählen Sie auf der Registerkarte **Dashboard** den Link **Set up deployment from source control** aus.

![DigitalMarketingDeploy1](./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Deploy1.png)

Ein Dialogfeld mit mehreren Optionen für die Quellcodeverwaltung wird angezeigt. Dazu gehören Quellcodeverwaltungssysteme mit allen Funktionen, wie beispielsweise TFS, sowie einfache Bereitstellungslösungen, wie beispielsweise Dropbox.

![DigitalMarketingDeploy2](./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Deploy2.png)

Sie können verschiedene Techniken für die Quellcodeverwaltung verwenden, um das neue Projekt zu verwalten, das auf einer vorhandenen Baseline beruht. Sie können beispielsweise ein zuvor gespeichertes Baseline-Repository kopieren, um mit der Arbeit am neuen Projekt zu beginnen. Alternativ können Sie auch eine neue Verzweigung erstellen, sodass die Anpassungen für das aktuelle Projekt nachverfolgt werden können. Ein gutes Beispiel für die Verwendung von Verzweigungen beim Verwalten verschiedener Bereitstellungen aus demselben Quellcodeverwaltungs-Repository finden Sie unter [Multiple Environments with Azure Web Sites](http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites). In diesem Beitrag wird gezeigt, wie die Git-Verzweigung zum Verwalten von Staging- und Produktionsumgebungen verwendet wird.

Wenn Sie eine Verbindung zwischen Ihrer Website und der Quellcodeverwaltung hergestellt haben, können Sie die Bereitstellungen über das Portal konfigurieren und nachverfolgen. Weitere Informationen zum Verwenden der Quellcodeverwaltung bei Websites finden Sie unter [Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites](/de-de/develop/net/common-tasks/publishing-with-git/).

Wenn Sie bereits vorhandene Webressourcen verwenden, ist eine gewisse Flexibilität erforderlich, um viele verschiedene Website-Typen hosten zu können. Auf der Registerkarte **Konfigurieren** können Sie die .NET- und PHP-Unterstützung für Ihre Website auswählen.

![DigitalMarketingFrameworkVersions](./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_FrameworkVersions.png)

Zusätzlich zu diesen Konfigurationsoptionen bietet Websites automatisch die Unterstützung für Python 2.7 und Node.js. Die standardmäßige Node.js-Version lautet 0.10.5.

Ein weiterer Vorteil von Azure-Websites ist die Geschwindigkeit beim Bereitstellen von gestaffelten Websites im Web. Während der Planung, beim Erstellen eines Prototyps und in der frühen Entwicklungsphase einer Website können die Agentur und deren Kunden tatsächliche Versionen der Kampagnen-Website anschauen, bevor diese veröffentlicht wird. Wenn die Website für die Produktion bereit ist, kann die Agentur entweder die Produktionsbereitstellung für den Kunden verwalten oder die Webressourcen dem Kunden zur Verfügung stellen, damit dieser die Ressourcen bereitstellen und verwalten kann.

Integrieren mit sozialen Medien
-------------------------------

Bei den meisten digitalen Marketingkampagnen kommen soziale Medienwebsites, wie beispielsweise Facebook oder Twitter, zum Einsatz. Ein Integrationspunkt besteht darin, die Identitäten der sozialen Medien für die Authentifizierung zu nutzen. Ein Beispiel für diese Vorgehensweise in Zusammenhang mit einer ASP.NET-Anwendung finden Sie unter [Bereitstellen einer sicheren ASP.NET MVC-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank für eine Azure-Website](/de-de/develop/net/tutorials/web-site-with-sql-database/).

Zahlreiche digitale Marketingkampagnen gehen über die Authentifizierung hinaus und nutzen die Integration der sozialen Medien als Schlüsselfunktion der entsprechenden Strategie. Soziale Medienwebsites verfügen normalerweise über einen Entwicklerbereich, in dem verschiedene Möglichkeiten aufgezeigt werden, wie Anwendungen mit den dazugehörigen Diensten integriert werden können. Dienste, bei denen eine REST-API zur Verfügung steht, können mit nahezu allen Web-Frameworks verwendet werden. Oftmals sind Informationen verfügbar, die speziell für die von Ihnen ausgewählte Sprache gelten. Beispielsweise verfügt Twitter über [eine Liste mit den verfügbaren Bibliotheken, die die Twitter-API unterstützen](https://dev.twitter.com/docs/twitter-libraries#dotnet), einschließlich derer für .NET, Node.js und PHP. Dabei handelt es sich lediglich um ein Beispiel. Suchen Sie auf allen sozialen Medienwebsites, die Sie als Ziel auswählen, direkt nach einem ähnlichen Entwicklerleitfaden.

Bei einem ASP.NET-Entwickler, der Facebook als Ziel hat, verfügt Visual Studio über eine Vorlage für eine MVC 4 Facebook-Anwendung.

![DigitalMarketingFacebook](./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Facebook.png)

Eine schnelle Anleitung für die Verwendung dieser Vorlage mit Websites finden Sie unter [Erstellen einer Facebook-Anwendung mithilfe von ASP.NET MVC Facebook-Vorlagen und kostenloses Hosten der Vorlagen auf Azure-Websites](http://blogs.msdn.com/b/africaapps/archive/2013/02/20/creating-a-facebook-app-using-asp-net-mvc-facebook-templates-and-hosting-them-for-free-on-windows-azure-websites.aspx). Ein ausführliches Lernprogramm sowie eine Beispielanwendung finden Sie unter [ASP.NET MVC Facebook Birthday App](http://www.asp.net/mvc/tutorials/mvc-4/aspnet-mvc-facebook-birthday-app) und [Die neue Facebook-Anwendungsvorlage und -Bibliothek für ASP.NET MVC](http://blogs.msdn.com/b/webdev/archive/2012/12/13/the-new-facebook-application-template-and-library-for-asp.net-mvc.aspx).

Wenn Sie ein ASP.NET-Entwickler sind, ist es wichtig zu wissen, dass die Integration mit sozialen Medien nicht auf Vorlagen beschränkt ist, die von Visual Studio zur Verfügung gestellt werden. Mithilfe einer Vorlage wird der Prozess lediglich vereinfacht. Wie bereits erwähnt, werden auf allen sozialen Medienwebsites normalerweise Informationen bereitgestellt, wie eine Verbindung über .NET und viele andere Sprachen und Frameworks hergestellt wird.

Skalieren mithilfe der Benutzeranforderung
------------------------------------------

Bei unvorhersehbaren Arbeitslasten ist Cloud-Computing hilfreich. Digitale Marketingkampagnen gehören zu dieser Kategorie. Es ist schwierig, die Beliebtheit einer relativ kurzlebigen Marketingwebsite vorherzusagen, da vieles davon abhängt, das Interesse der Benutzer zu wecken. Auch die zugehörigen Interaktionen bezüglich der sozialen Medien, sodass zusätzlicher Datenverkehr für die Website generiert wird, spielen eine große Rolle. Azure bietet mehrere Optionen für das Skalieren von Websites und Clouddiensten.

-   Manuelles Skalieren mithilfe des [Azure-Verwaltungsportals](http://manage.windowsazure.com/)
-   Programmgesteuertes Skalieren mithilfe der [Dienstverwaltungs-API](http://msdn.microsoft.com/de-de/library/windowsazure/ee460799.aspx) oder [PowerShell-Skripterstellung](http://msdn.microsoft.com/de-de/library/windowsazure/jj152841.aspx)
-   Automatisches Skalieren mithilfe der Funktion "Autoscale" (Vorschau)

Wechseln Sie im Verwaltungsportal zur Registerkarte **Skalierung** für Ihre Website. Für die Skalierung sind mehrere Optionen verfügbar. Mit der ersten Option wird der Website-Modus (**Free**, **Shared** oder **Standard**) festgelegt.

![DigitalMarketingScale](./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Scale.png)

Bei jeder Ebene kommen jeweils weitere Skalierbarkeitsfunktionen und -optionen hinzu. Beispielsweise können Websites im Modus **Free** nicht auf mehrere Instanzen verkleinert werden. Websites im Modus **Shared** können hingegen auf 6 Instanzen verkleinert werden. Das Hochskalieren ist auch möglich, indem Sie den Modus **Standard** auswählen. In diesem Modus wird Ihre Website auf dedizierten virtuellen Computern ausgeführt. Darüber hinaus sind die Optionen "Small", "Medium" und "Large" hinsichtlich der Größe des Computers verfügbar. Nachdem Sie die Größe des virtuellen Computers festgelegt haben, können Sie auch auf mehrere Instanzen verkleinern. Im Modus **Standard** ist es möglich, auf 10 Instanzen zu verkleinern. Eine vollständige Liste mit den Unterschieden zwischen den jeweiligen Modi finden Sie unter [Web Sites – Preisdetails](https://www.windowsazure.com/en-us/pricing/details/web-sites/).

Wenn Sie den Modus **Standard** auswählen, können Sie zusätzlich die Funktion "Autoscale" (Vorschau) aktivieren. Daraufhin erfolgt die Skalierung auf Basis der CPU. Die Option **Ziel-CPU**, die in Prozent angegeben wird, steht für die Prozessorauslastung, die Azure für Ihre Website-Instanzen vorgibt. Falls die durchschnittliche CPU-Auslastung unter dieser Vorgabe liegt, verringert Azure die Anzahl der Instanzen, da dieselbe Last auf weniger Instanzen zu einer erhöhten Auslastung auf den übrigen Instanzen führt. Die Anzahl der Instanzen kann nicht auf einen Wert festgelegt werden, der unterhalb des Minimalwerts für **Instance Count** liegt. Falls die durchschnittliche CPU-Auslastung höher ist als die **Ziel-CPU**, erhöht Azure die Anzahl der Instanzen. Wenn dieselbe Last auf zusätzliche Computer verteilt wird, wird die CPU-Auslastung auf jedem einzelnen Computer verringert. Die Anzahl der Instanzen, die hinzugefügt werden können, ist auf den Maximalwert für **Instance Count** beschränkt.

![DigitalMarketingAutoScale](./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_AutoScale.png)

Mithilfe der automatischen Skalierung können Ressourcen effektiver genutzt werden. Beispielsweise ist es möglich, dass die digitale Marketingkampagne abends und am Wochenende besonders aktiv ist. Dies gilt auch für ein Szenario, bei dem die Beliebtheit einer Kampagne überraschend steigt. Mit der automatischen Skalierung kann die erhöhte Last effektiv bewältigt werden. Die Anzahl der Instanzen und folglich auch die Kosten werden anschließend verringert, sobald die Last geringer wird.

Weitere Informationen zum Skalieren von Websites finden Sie unter [Skalieren von Websites](/de-de/manage/services/web-sites/how-to-scale-websites/). Dieses Thema steht in engem Zusammenhang mit der Überwachung. Weitere Informationen finden Sie im entsprechenden Abschnitt dieses Leitfadens unter [Überwachen der Kampagne](#monitor).

**Hinweis**

Für Webanwendungen, bei denen Clouddienste und Webrollen zum Einsatz kommen, ist eine zusätzliche Option für die Skalierung vorhanden, die auf der Länge der in einer Warteschlange befindlichen Elemente basiert. Bei einem Clouddienst sind Rollen, die Backend-Warteschlangen verarbeiten, ein weitverbreitetes Architekturmuster. Weitere Informationen zum Skalieren von Clouddiensten finden Sie unter [Skalieren eines Clouddiensts](http://www.windowsazure.com/de-de/manage/services/cloud-services/how-to-scale-a-cloud-service/).

Integrieren mit anderen Diensten
--------------------------------

Eine digitale Marketing-Website enthält oftmals Rich-Media-Inhalte, wie zum Beispiel Videostreaming. Werden diese Websites in Azure gehostet, ist eine enge Integration mit den dazugehörigen Azure-Diensten möglich. Sie können beispielsweise Azure Media Services zum Kodieren und Streamen von Videos für Ihre Website verwenden. Weitere Informationen zu Media Services finden Sie unter [Azure Media Services – Konzepte](http://msdn.microsoft.com/de-de/library/windowsazure/dn223282.aspx).

Weitere Azure-Dienste können zum Erstellen einer stabileren Anwendung genutzt werden. Für Websites kann das verteilte Zwischenspeichern verwendet werden, das über den neuen [Azure-Cachedienst (Vorschau)](http://msdn.microsoft.com/de-de/library/windowsazure/dn386094.aspx) zur Verfügung gestellt wird. Alternativ können Sie auch die Speicherdienste von Azure zum Speichern der Anwendungsdaten und Ressourcen nutzen. Beispielsweise können Grafiken, Videos und andere große Dateien dauerhaft in Blobs gespeichert werden. Datenbankdienste, wie z. B. Azure SQL-Datenbank und MySQL, sind ebenfalls für die Anforderungen bezüglich relationaler Daten verfügbar.

Überwachen der Kampagne
-----------------------

Die Registerkarte **Monitor** enthält Kennzahlen, mit deren Hilfe Sie den Erfolg und die Leistung Ihrer digitalen Marketing-Website bewerten können.

![DigitalMarketingMonitor](./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Monitor.png)

Beispielsweise können Sie Auslastungsmuster und -levels überprüfen, indem Sie sich Kennzahlen, wie z. B. **CPU Time**, **Requests** und **Data Out** anzeigen lassen. Alle diese Kennzahlen werden größer, weil die Marketingkampagne beliebter wird. Informationen zur Auslastung können verwendet werden, wenn die Entscheidung erfolgt, wann hoch- oder herunterskaliert wird. Weitere Informationen finden Sie unter [Überwachen von Websites](/de-de/manage/services/web-sites/how-to-monitor-websites/).

Bei den Modi **Free** und **Shared** sollten Sie auch Ressourcenkontingente berücksichtigen. Auf der Registerkarte **Dashboard** zeigt das Portal die aktuelle Nutzungsstatistik mehrerer Kontingente an und wann diese Kontingente zurückgesetzt werden. Diese Nutzungsstatistik variiert je nach ausgewähltem Modus. Im folgenden Screenshot ist die angezeigte Statistik für den Modus **Free** dargestellt. Im Modus **Shared** ist kein Kontingent für **Data Out** vorhanden. Im Modus **Standard** werden lediglich **File System Storage** und **Size** angezeigt.

![DigitalMarketingUsageOverview](./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_UsageOverview.png)

Falls Sie der Meinung sind, dass diese Kontingente nahezu erschöpft sind, erwägen Sie einen Wechsel von **Free** zu **Shared** oder von **Shared** zu **Standard**. Im Modus **Standard** verfügen Sie über dedizierte Ressourcen auf einem oder mehreren kleinen, mittelgroßen oder großen virtuellen Computern.

Für diese Informationen kann das Verwaltungsportal verwendet werden. Darüber hinaus gibt es zahlreiche Drittanbietertools, die erweiterte Überwachungsdaten für Ihre Websites zur Verfügung stellen. Sie können beispielsweise ein New Relic-Add-on aus dem Azure Store installieren. Eine Anleitung zum Verwenden von New Relic für die Überwachung finden Sie unter [Verwaltung der Anwendungsleistung mit New Relic auf Azure](/de-de/develop/net/how-to-guides/new-relic/).

Für Websites im Modus "Standard" können die Endpunktüberwachung und die Alarmierung aktiviert werden. Die Endpunktüberwachung versucht, in regelmäßigen Abständen Ihre Website zu erreichen und teilt anschließend die Reaktionszeit mit. Warnungen erfolgen in Form von E-Mail-Benachrichtigungen, falls die Reaktionszeit einen bestimmten Schwellenwert überschreitet. Weitere Informationen finden Sie im Abschnitt zur Überwachung des Szenarios [Globale Webpräsenz](/de-de/manage/services/web-sites/global-web-presence-solution-overview/) und im Thema [Vorgehensweise: Empfangen von Warnbenachrichtigungen und Verwalten von Warnungsregeln in Azure](http://msdn.microsoft.com/library/windowsazure/dn306638.aspx).

Zusammenfassung
---------------

Azure-Websites eignen sich gut für wiederverwendbare Webinhalte, die für einzelne Marketingkampagnen angepasst werden. Websites unterstützen zahlreiche gängige Sprachen, Frameworks und Quellcodeverwaltungssysteme, sodass das Migrieren zu diesen Objekten und Workflows in der Cloud einfacher ist. Mithilfe der ASP.NET Facebook-Anwendungsvorlage können Facebook-Anwendungen einfacher erstellt werden. Sie können allerdings auch eine beliebige Integration der sozialen Medien von Drittanbietern verwenden, wenn diese Webschnittstellen unterstützt. Über Azure Media Services und andere dazugehörige Dienste in Azure sind zusätzliche Tools verfügbar, sodass eine sorgfältig konzipierte Kampagnen-Website erstellt werden kann. Die zahlreichen Optionen für die manuelle und automatische Skalierung sind bei der Bewältigung der schwer einzuschätzenden Benutzeranforderungen hilfreich. Weitere Informationen finden Sie in den folgenden Fachartikeln:

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
   <td data-morhtml="true" valign="middle"><strong data-morhtml="true">Erstellen</strong></td>
   <td data-morhtml="true" valign="top">- <a data-morhtml="true" href="http://www.windowsazure.com/de-de/manage/services/web-sites/how-to-create-websites/">Erstellen und Bereitstellen einer Website</a></td>
</tr>
<tr data-morhtml="true">
   <td data-morhtml="true" valign="middle"><strong data-morhtml="true">Bereitstellen</strong></td>
   <td data-morhtml="true" valign="top">- <a data-morhtml="true" href="http://www.windowsazure.com/de-de/develop/net/common-tasks/publishing-with-git/">Ver&ouml;ffentlichen aus der Quellcodeverwaltung auf Azure-Websites</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://www.windowsazure.com/de-de/manage/services/web-sites/how-to-create-websites/">Bereitstellen einer ASP.NET-Webanwendung f&uuml;r eine Azure-Website</a></td>
</tr>
<tr data-morhtml="true">
   <td data-morhtml="true" valign="middle"><strong data-morhtml="true">Soziale Medien</strong></td>
   <td data-morhtml="true" valign="top">- <a data-morhtml="true" href="http://www.windowsazure.com/de-de/develop/net/tutorials/web-site-with-sql-database/">Bereitstellen einer sicheren ASP.NET MVC-Anwendung mit Mitgliedschaft, OAuth und einer SQL-Datenbank</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://blogs.msdn.com/b/africaapps/archive/2013/02/20/creating-a-facebook-app-using-asp-net-mvc-facebook-templates-and-hosting-them-for-free-on-windows-azure-websites.aspx">Erstellen einer Facebook-Anwendung mithilfe von ASP.NET MVC Facebook-Vorlagen</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://blogs.msdn.com/b/webdev/archive/2012/12/13/the-new-facebook-application-template-and-library-for-asp.net-mvc.aspx">Facebook-Anwendungsvorlage und -Bibliothek f&uuml;r ASP.NET MVC</a></td>
</tr>
<tr data-morhtml="true">
   <td data-morhtml="true" valign="middle"><strong data-morhtml="true">Skalieren</strong></td>
   <td data-morhtml="true" valign="top">- <a data-morhtml="true" href="http://www.windowsazure.com/de-de/manage/services/web-sites/how-to-scale-websites/">Skalieren von Websites</a></td>
</tr>
<tr data-morhtml="true">
   <td data-morhtml="true" valign="middle"><strong data-morhtml="true">Rich-Media-Inhalte</strong></td>
   <td data-morhtml="true" valign="top">- <a data-morhtml="true" href="http://msdn.microsoft.com/de-de/library/windowsazure/dn223282.aspx">Azure Media Services &ndash; Konzepte</a></td>
</tr>
<tr data-morhtml="true">
   <td data-morhtml="true" valign="middle"><strong data-morhtml="true">&Uuml;berwachen</strong></td>
   <td data-morhtml="true" valign="top">- <a data-morhtml="true" href="http://www.windowsazure.com/de-de/manage/services/web-sites/how-to-monitor-websites/">&Uuml;berwachen von Websites</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://msdn.microsoft.com/library/windowsazure/dn306638.aspx">Vorgehensweise: Empfangen von Warnbenachrichtigungen und Verwalten von Warnungsregeln in Azure</a>.</td>
</tr>
</table>

