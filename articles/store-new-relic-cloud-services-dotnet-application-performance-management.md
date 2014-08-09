<properties linkid="develop-net-how-to-guides-new-relic" urlDisplayName="New Relic" pageTitle="Using New Relic with Azure - Azure feature guides" metaKeywords="" description="Learn how to use the New Relic service to manage and monitor your Azure application." metaCanonical="" services="" documentationCenter=".NET" title="New Relic Application Performance Management on Azure" authors="" solutions="" manager="" editor="" />

Verwaltung der Anwendungsleistung mit New Relic auf Azure
=========================================================

In diesem Leitfaden wird beschrieben, wie Sie Ihren auf Azure gehosteten Anwendungen die erstklassige Leistungsüberwachung von New Relic hinzufügen. Wir erläutern das schnelle und einfache Verfahren zum Hinzufügen von New Relic zu Anwendungen und stellen einige der Funktionen von New Relic vor. Weitere Informationen zum Arbeiten mit New Relic finden Sie unter [Verwenden von New Relic](#using-new-relic).

Was ist New Relic?
------------------

New Relic ist ein für Entwickler konzipiertes Tool, mit dem Produktionsanwendungen überwacht und detaillierte Informationen zu deren Leistung und Zuverlässigkeit abgerufen werden können. Es beschleunigt die Identifizierung und Diagnostizierung von Leistungsproblemen und gibt Ihnen die benötigten Informationen an die Hand, um diese Probleme zu lösen.

New Relic verfolgt die Ladezeit und den Durchsatz von Webtransaktionen, sowohl vom Server als auch von den Browsern der Benutzer. Das Tool zeigt, wie viel Zeit Sie in der Datenbank verbracht haben, analysiert langsame Abfragen und Webanforderungen, bietet Verfügbarkeitsüberwachung und Warnfunktionen, verfolgt Anwendungsausnahmen und Vieles mehr.

Sonderpreis für New Relic im Azure Store
----------------------------------------

New Relic Standard ist für Azure-Benutzer kostenlos; New Relic Pro wird basierend auf der Instanzgröße für Azure-Clouddienste angeboten

Preisinformationen finden Sie auf der [New Relic-Seite im Azure Store](http://www.windowsazure.com/de-de/gallery/store/new-relic/new-relic/).

**Hinweis:**

Es werden nur Preise für bis zu zehn Serverinstanzen aufgeführt. Volumenpreise für mehr als zehn Instanzen erfragen Sie bitte bei New Relic (sales@newrelic.com).

Azure-Kunden erhalten bei Bereitstellung des New Relic-Agenten ein zweiwöchiges Testabonnement von New Relic Pro.

Anmelden bei New Relic über den Azure Store
-------------------------------------------

New Relic kann nahtlos in Azure-Web- und -Workerrollen integriert werden.

Um sich direkt im Azure Store bei New Relic anmelden, führen Sie diese drei einfachen Schritte aus.

### Schritt 1. Anmelden über den Azure Store

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.
2.  Klicken Sie im unteren Bereich des Verwaltungsportals auf **Neu**.
3.  Klicken Sie auf **Speichern**.
4.  Wählen Sie im Dialogfeld **Add-On auswählen** die Option **New Relic** aus, und klicken Sie auf **Weiter**.
5.  Wählen Sie im Dialogfeld **Add-On personalisieren** den gewünschten New Relic-Plan aus.
6.  Geben Sie ggf. einen Promotioncode ein.
7.  Geben Sie den Namen ein, mit dem der New Relic-Dienst in Ihren Azure-Einstellungen angezeigt werden soll, oder wählen Sie den Standardwert **NewRelic** aus. Dieser Name muss in der Liste der abonnierten Azure Store-Elemente eindeutig sein.
8.  Wählen Sie einen Wert für die Region aus, z. B. **West US**.
9.  Klicken Sie auf **Weiter**.
10. Prüfen Sie den Plan, die Preisinformationen sowie die rechtlichen Bedingungen im Dialogfeld **Kauf überprüfen**. Wenn Sie den Bedingungen zustimmen, klicken Sie auf **Kaufen**.
11. Nachdem Sie auf **Kaufen** geklickt haben, wird mit der Erstellung Ihres neuen New Relic-Kontos begonnen. Sie können den Status im Azure-Verwaltungsportal überwachen.
12. Zum Abrufen des New Relic-Lizenzschlüssels klicken Sie auf **Output Values**.
13. Kopieren Sie den angezeigten Lizenzschlüssel. Diesen müssen Sie bei der Installation des New Relic Nuget-Pakets eingeben.

### Schritt 2. Installieren des Nuget-Pakets

1.  Öffnen Sie Ihre Visual Studio-Projektmappe, oder wählen Sie **Datei \> Neu \> Projekt**, um eine neue zu erstellen.

    ![Visual Studio](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget01.png)

2.  Wenn Ihre Projektmappe noch kein Azure-Clouddienstprojekt enthält, fügen Sie eins hinzu, indem Sie im Projektmappen-Explorer mit der rechten Maustaste auf Ihre App klicken und **Add Azure Cloud Service Project** wählen.

    ![Clouddienst erstellen](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget02.png)

3.  Wählen Sie **Tools \> Library Package Manager \> Package Manager Console**, um die Paket-Manager-Konsole zu öffnen. Wählen Sie Ihr Projekt oben im Fenster der Paket-Manager-Konsole als Standardprojekt aus.

    ![Paket-Manager-Konsole](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget04.png)

4.  Geben Sie an der Eingabeaufforderung der Paket-Manager-Konsole `Install-Package NewRelicWindowsAzure` ein, und drücken Sie die **EINGABETASTE**.

    ![Installieren im Paket-Manager](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget06.png)

5.  Geben Sie an der Eingabeaufforderung für den Lizenzschlüssel den vom Azure Store erhaltenen Lizenzschlüssel ein.

    ![Lizenzschlüssel eingeben](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget07.png)

6.  Optional: Geben Sie an der Eingabeaufforderung für den Anwendungsnamen den Namen Ihrer App so ein, wie er im New Relic-Dashboard angezeigt wird. Alternativ können Sie den Projektmappennamen als Standard verwenden.

    ![Anwendungsnamen eingeben](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget08.png)

7.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Azure-Clouddienstprojekt, und wählen Sie **Veröffentlichen**.

    ![Cloudprojekt veröffentlichen](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget09.png)

**Hinweis:** Wenn Sie diese App zum ersten Mal in Azure bereitstellen, werden Sie zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert. Weitere Informationen finden Sie unter [Bereitstellen einer ASP.NET-Webanwendung für eine Azure-Website](/de-de/develop/net/tutorials/get-started/).

![Veröffentlichungseinstellungen](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget10.png)

### Schritt 3. Überprüfen der Anwendungsleistung in New Relic.

So zeigen Sie das New Relic-Dashboard an:

1.  Klicken Sie im Azure-Portal auf die Schaltfläche **Verwalten**.
2.  Melden Sie sich mit der E-Mail-Adresse und dem Kennwort Ihres New Relic-Kontos an.
3.  Wählen Sie in der New Relic-Menüleiste **Applications \> (Anwendungsname)**.

    Das Dashboard **Monitoring \> Overview** wird automatisch angezeigt.

    ![New Relic-Monitoring-Dashboard](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app.png)

    Nachdem Sie in der Liste des Menüs **Applications** eine App ausgewählt haben, werden im Dashboard **Overview** aktuelle App-Server- und Browserinformationen angezeigt.

### Verwenden von New Relic

Nachdem Sie Ihre App in der Liste des Menüs "Applications" ausgewählt haben, werden im Dashboard "Overview" aktuelle App-Server- und Browserinformationen angezeigt. Zum Umschalten zwischen den zwei Ansichten klicken Sie auf die Schaltfläche **App server** oder **Browser**.

Neben der [standardmäßigen New Relic-Benutzeroberfläche](https://newrelic.com/docs/site/the-new-relic-ui#functions) und [Dashboard-Drilldown](https://newrelic.com/docs/site/the-new-relic-ui#drilldown)-Funktionen bietet das Dashboard "Overview" weitere Funktionen.

<table data-morhtml="true" border="1">
  <thead data-morhtml="true">
    <tr data-morhtml="true">
      <th data-morhtml="true"><b data-morhtml="true">Gew&uuml;nschte Aktion</b></th>
      <th data-morhtml="true"><b data-morhtml="true">Ma&szlig;nahme</b></th>
    </tr>
  </thead>
  <tbody data-morhtml="true">
    <tr data-morhtml="true">
       <td data-morhtml="true">Anzeigen von Dashboardinformationen f&uuml;r den ausgew&auml;hlten App-Server oder Browser</td>
       <td data-morhtml="true">Klicken Sie auf die Schaltfl&auml;che <b data-morhtml="true">App server</b> oder <b data-morhtml="true">Browser</b>.</td>
    </tr>
     <tr data-morhtml="true">
       <td data-morhtml="true">Anzeigen von Schwellenwerten f&uuml;r das <a data-morhtml="true" href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a>-Ergebnis Ihrer App</td>
       <td data-morhtml="true">Zeigen Sie auf das <b data-morhtml="true">?<b data-morhtml="true">-Symbol</b></b> des Apdex-Ergebnisses.</td>
    </tr>
    <tr data-morhtml="true">
       <td data-morhtml="true">Anzeigen weltweiter Apdex-Details</td>
       <td data-morhtml="true">Zeigen Sie in der <b data-morhtml="true">Browser</b>-Ansicht der &Uuml;bersicht auf eine beliebige Stelle der globalen Apdex-Karte.<br data-morhtml="true" /><b data-morhtml="true">Tipp:</b> Wenn Sie direkt zum Dashboard <a data-morhtml="true" href="https://newrelic.com/docs/site/geography" target="_blank">Geography</a> der ausgew&auml;hlte App wechseln m&ouml;chten, klicken Sie auf den Titel <b data-morhtml="true">Global Apdex</b>, oder klicken Sie auf eine beliebige Stelle der globalen Apdex-Karte.</td>
    </tr>
    <tr data-morhtml="true">
       <td data-morhtml="true">Anzeigen des Dashboards <a data-morhtml="true" href="https://newrelic.com/docs/applications-dashboards/web-transactions" target="_blank">Web Transactions</a></td>
       <td data-morhtml="true">Klicken Sie im Dashboard &quot;Overview&quot; auf die Tabelle &quot;Web Transactions&quot;. Wenn Sie Details zu einer bestimmten Webtransaktion (einschlie&szlig;lich <a data-morhtml="true" href="https://newrelic.com/docs/site/key-transactions" target="_blank">Key Transactions</a> anzeigen m&ouml;chten), klicken Sie auf den entsprechenden Namen.</td>
    </tr>
    <tr data-morhtml="true">
       <td data-morhtml="true">Anzeigen des Dashboards <a data-morhtml="true" href="https://newrelic.com/docs/site/errors" target="_blank">Errors</a></td>
       <td data-morhtml="true">Klicken Sie im Dashboard &quot;Overview&quot; auf den Titel des Fehlerratendiagramms.<br data-morhtml="true" /><b data-morhtml="true">Tipp:</b> Sie k&ouml;nne das Dashboard &quot;Errors&quot; auch &uuml;ber <b data-morhtml="true">Applications</b> &gt; (Ihre App) &gt; Events &gt; Errors anzeigen.</td>
    </tr>
    <tr data-morhtml="true">
       <td data-morhtml="true">Anzeigen von Details zum App-Server</td>
       <td data-morhtml="true"><p data-morhtml="true">F&uuml;hren Sie einen der folgenden Schritte aus:<p data-morhtml="true">
        <ul data-morhtml="true">
          <li data-morhtml="true">Schalten Sie zwischen einer Tabellenansicht der Hosts und ausf&uuml;hrlichen Metrikdetails zu jedem Host um.</li>
          <li data-morhtml="true">Klicken Sie auf den Namen eines einzelnen Servers.</li>
          <li data-morhtml="true">Zeigen Sie auf das Apdex-Ergebnis eines einzelnen Servers.</li>
          <li data-morhtml="true">Klicken Sie auf die CPU-Auslastung oder den Arbeitsspeicher eines einzelnen Servers.</li>
        </ul>
       </p></p></td>
    </tr>
  </tbody>
</table>

Nachfolgend sehen Sie ein Beispiel des Dashboards "Overview" bei Auswahl der Browseransicht.

![Paket-Manager-Konsole](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app_browser.png)

Nächste Schritte
----------------

In den folgenden Ressourcen finden Sie weitere Informationen:

-   [Installing the .NET Agent on Azure](https://newrelic.com/docs/dotnet/installing-the-net-agent-on-azure): Installationsverfahren für den New Relic .NET Agent (in englischer Sprache)
-   [The New Relic User Interface](https://newrelic.com/docs/site/the-new-relic-ui): Überblick über die New Relic-Benutzeroberfläche, Definieren von Benutzerrechten und -profilen, Verwenden von Standardfunktionen und Details zum Drilldown in Dashboards (in englischer Sprache)
-   [Applications Overview](https://newrelic.com/docs/site/applications-overview): Merkmale und Funktionen des New Relic-Dashboards "Overview" (in englischer Sprache)
-   [Apdex](https://newrelic.com/docs/site/apdex): Erläuterung, wie Apdex die Endbenutzerzufriedenheit mit Ihrer Anwendung misst (in englischer Sprache)
-   [Real User Monitoring](https://newrelic.com/docs/features/real-user-monitoring): Überblick darüber, wie mit RUM (Real User Monitoring) die Zeit gemessen wird, die das Laden Ihrer Webseiten in den Browsern von Benutzern dauert, woher diese Benutzer stammen und welche Browser sie verwenden (in englischer Sprache)
-   [Finding Help](https://newrelic.com/docs/site/finding-help): Über das Online-Hilfecenter von New Relic aufrufbare Ressourcen (in englischer Sprache)
