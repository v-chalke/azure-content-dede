<properties 
	pageTitle="Bereitstellen Sie und konfigurieren Sie eine SaaS-Connector-API-Anwendung" 
	description="Erfahren Sie, wie Sie einen SaaS-Connector konfigurieren, den Sie aus dem Azure Marketplace in Ihrem Azure-Abonnement installieren." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="tdykstra"/>

# Bereitstellen Sie und konfigurieren Sie eine SaaS-Connector-API-Anwendung in Azure-App-Dienst

## Übersicht

In diesem Lernprogramm wird gezeigt, wie Sie einen [SaaS-Connector (Software-as-a-Service)](../app-service-logic-what-are-bizTalk-api-apps.md) in [Azure App Service](/documentation/services/app-service/) installieren, konfigurieren und testen, um ihn programmgesteuert aufzurufen, beispielsweise aus einer mobilen App. Ein SaaS-Connector ist eine [API-App](app-service-api-apps-why-best-platform.md), welche die Interaktion mit einer SaaS-Plattform wie z. B. Office 365, Salesforce, Facebook und Dropbox vereinfacht wird.

Wenn Sie beispielsweise HTTP-Anforderungen zum Lesen und Schreiben von Dateien in Ihrem Dropbox-Konto schreiben möchten, ist der Authentifizierungsprozess zum direkten Arbeiten mit Dropbox kompliziert. Ein Dropbox-Connector übernimmt die Authentifizierung, sodass Sie sich darauf konzentrieren können, Ihren geschäftsspezifischen Code zu schreiben.

> [AZURE.NOTE]Die folgenden Anweisungen sind nicht erforderlich, wenn Sie einen SaaS-Connector aus einer Logik app verwenden möchten. Informationen zur Verwendung von SaaS-Connectors in der Logik apps finden Sie unter [Erstellen einer neuen Logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).
 
Dieses Lernprogramm verwendet einen Dropbox-Connector als Beispiel und leitet Sie durch die folgenden Schritte:

* Installieren des Dropbox-Connectors in einer [Ressourcengruppe](../resource-group-overview.md) Ihres Azure-Abonnements 
* Konfigurieren des Dropbox-Connectors, sodass er sich mit dem Dropbox-Dienst verbinden kann (für diesen Schritt ist ein Dropbox-Konto erforderlich)
* Konfigurieren der Ressourcengruppe, sodass nur authentifizierte Benutzer auf die API-Apps zugreifen können, die in der Ressourcengruppe enthalten sind
* Testen, ob sowohl Benutzerauthentifizierung als auch Dropbox-Authentifizierung funktionieren

## Installieren des Dropbox-Connectors

1. Wechseln Sie zur Startseite des [Azure-Vorschauportals], und klicken Sie auf **Marketplace**.

	![Marketplace im Azure-Vorschauportal](./media/app-service-api-connnect-your-app-to-saas-connector/marketplace.png)

2. Suchen Sie nach Dropbox, und klicken Sie dann auf das Symbol **Dropbox-Connector**.

	![Klicken Sie auf "Dropbox-Connector"](./media/app-service-api-connnect-your-app-to-saas-connector/searchdb.png)
 
3. Klicken Sie auf **Erstellen**.

	![Klicken Sie auf "Erstellen"](./media/app-service-api-connnect-your-app-to-saas-connector/clickcreate.png)
 
5. Geben Sie im Blatt **Dropbox-Connector** unterhalb von **App Service-Plan** auf **Neu erstellen**, und geben Sie im Feld **Neuen App Service-Plan erstellen** den Wert "DropboxPlan" ein.

	Weitere Informationen zu Azure App Service-Plänen finden Sie unter [Azure App Service-Pläne – Detaillierte Übersicht](azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. Klicken Sie unterhalb von **Ressourcengruppe** auf **Neu erstellen**, und geben Sie dann im Feld **Neue Ressourcengruppe erstellen** den Wert "DropboxRG" ein.

	Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../resource-group-overview.md).

7. Wählen Sie die **Preisstufe** "Free". (Wenn diese nicht in der Liste angezeigt wird, klicken Sie auf **Alle anzeigen**. Nachdem Sie auf **F1 Free** geklickt haben, klicken Sie auf die Schaltfläche **Auswählen**.)

	Sie können eine kostenpflichtige Preisstufe wählen, im Rahmen dieses Lernprogramms ist dies jedoch nicht erforderlich.
 
11. Wählen Sie einen **Standort** in Ihrer Nähe.

9. <a id="gateway"></a>Behalten Sie den Standardwert "DropboxConnector" als **Name** des Connectors bei, und klicken Sie auf **Erstellen**.

	![Klicken Sie auf "Erstellen"](./media/app-service-api-connnect-your-app-to-saas-connector/createdropbox.png)

	Azure App Service erstellt eine Ressourcengruppe, und innerhalb der Ressourcengruppe werden eine Dropbox-Connector-API-App und eine *Gateway*-API-App erstellt. Die Funktion des Gateways besteht darin, den Zugriff auf alle API-Apps in der Ressourcengruppe zu verwalten.

	Sie können den Fortschritt der Ressourcenerstellung überprüfen, indem Sie auf der Startseite des Azure-Vorschauportals auf **Benachrichtigungen** klicken.

3. Wenn Azure die Erstellung des Connectors abgeschlossen hat, klicken Sie auf **Durchsuchen > Ressourcengruppen > DropboxRG**.
 
	Das Blatt **Ressourcengruppe** für DropboxRG zeigt den Connector und das Gateway in der Ressourcengruppe.

	![Diagramm der Ressourcengruppe](./media/app-service-api-connnect-your-app-to-saas-connector/rgdiagram.png)

## Konfigurieren Ihres Dropbox-Kontos und des Dropbox-Connectors

Um den API-Zugriff für Ihr Dropbox-Konto zu aktivieren, müssen Sie auf der Dropbox-Entwicklersite eine Dropbox-App erstellen. Kopieren Sie anschließend die Client-ID und den geheimen Clientschlüssel aus der Dropbox-App in Ihren Dropbox-Connector, und konfigurieren Sie den Connector so, dass nur authentifizierte Anforderungen akzeptiert werden.

### Erstellen einer Dropbox-App

Die folgenden Schritte zeigen das Vorgehen zum Erstellen einer Dropbox-App mithilfe der Website "Dropbox.com". Da die Website "Dropbox.com" ohne vorherige Ankündigung geändert werden kann, unterscheiden sich die tatsächlichen Benutzeroberflächenelemente möglicherweise von den hier gezeigten.

1. Wechseln Sie zum [Dropbox-Entwicklerportal](https://www.dropbox.com/developers/apps), klicken Sie auf **App Console** und anschließend auf **Create app**.

	![Erstellen einer Dropbox-App](./media/app-service-api-connnect-your-app-to-saas-connector/dbappcreate.png)

2. Wählen Sie **Dropbox API app**, und konfigurieren Sie die weiteren Einstellungen.
 
	Die im nachstehenden Screenshot gezeigten Dateizugriffsoptionen ermöglichen es Ihnen, den Zugriff auf Ihr Dropbox-Konto mit einer einfachen HTTP-Get-Anforderung zu testen, wenn Sie über beliebige Dateien in Ihrem Konto verfügen.

	Der Name der Dropbox-API-App kann beliebig gewählt werden, solange er von der Dropbox-Website akzeptiert wird.

3. Klicken Sie auf **Create app**.

	![Erstellen einer Dropbox-App](./media/app-service-api-connnect-your-app-to-saas-connector/dbapiapp.png)

	Auf der nächsten Seite werden der App-Schlüssel und die geheimen App-Einstellungen gezeigt (in Azure als Client-ID und geheimer Clientschlüssel bezeichnet), die Sie zum Konfigurieren Ihres Azure-Dropbox-Connectors benötigen.

	Diese Seite enthält außerdem ein Feld, in dem Sie den Umleitungs-URI eingeben können. Diesen Wert rufen Sie im nächsten Abschnitt ab.

	![Erstellen einer Dropbox-App](./media/app-service-api-connnect-your-app-to-saas-connector/dbappsettings.png)

### Kopieren der Dropbox-App-Einstellungen in den Azure-Dropbox-Connector und umgekehrt 

4. Wechseln Sie in einem anderen Browserfenster oder in einer anderen Browserregisterkarte zum [Azure-Vorschauportal]. 

3. Wechseln Sie zum Blatt **API-App** für Ihren Dropbox-Connector. (Wenn Sie noch das Blatt **Ressourcengruppe** anzeigen, klicken Sie einfach auf den Dropbox-Connector im Diagramm.)

4. Klicken Sie auf **Einstellungen**, und klicken Sie dann im Blatt **Einstellungen** auf **Authentifizierung**.

	![Klicken Sie auf "Einstellungen"](./media/app-service-api-connnect-your-app-to-saas-connector/clicksettings.png)

	![Klicken Sie auf "Authentifizierung"](./media/app-service-api-connnect-your-app-to-saas-connector/clickauth.png)

5. Geben Sie im Blatt "Authentifizierung" die Client-ID und den geheimen Clientschlüssel von der Dropbox-Website ein, und klicken Sie dann auf **Speichern**.

	![Geben Sie Einstellungen ein, und klicken Sie auf "Speichern"](./media/app-service-api-connnect-your-app-to-saas-connector/authblade.png)

3. Kopieren Sie den **Umleitungs-URI** (graues Feld oberhalb von Client-ID und dem geheimen Clientschlüssel), und fügen Sie den Wert auf der Seite ein, die Sie im vorherigen Schritt geöffnet gelassen haben.

	Der Umleitungs-URI folgt diesem Muster:

		[gatewayurl]/api/consent/redirect/[connectorname]

	Zum Beispiel:

		https://dropboxrgaeb4ae60b7.azurewebsites.net/api/consent/redirect/DropboxConnector

	![Abrufen des Umleitungs-URI](./media/app-service-api-connnect-your-app-to-saas-connector/redirecturi.png)

	![Erstellen einer Dropbox-App](./media/app-service-api-connnect-your-app-to-saas-connector/dbappsettings2.png)

### Festlegen des Dropbox-Connectors für einen erforderlichen authentifizierten Zugriff

Standardmäßig ist die **Zugriffsebene** für den Connector auf **Intern** festgelegt. Dies bedeutet, dass der Connector nur von anderen API-Apps und Web-Apps in derselben Ressourcengruppe aufgerufen werden kann. Dropbox gestattet jedoch nur authentifizierten Benutzern den Zugreifen auf Ihr Dropbox-Konto. Deshalb müssen Sie die Einstellung für die Zugriffsebene ändern, sodass nur authentifizierte Benutzer Zugriff erhalten.

1. Wechseln Sie zurück zum Blatt **Einstellungen**, und klicken Sie auf **Anwendungseinstellungen**.

2. Ändern Sie im Blatt **Anwendungseinstellungen** die **Zugriffsebene** in **Öffentlich (authentifiziert)**, und klicken Sie dann auf **Speichern**.
	
	![Festlegung auf "Öffentlich (authentifiziert)"](./media/app-service-api-connnect-your-app-to-saas-connector/pubauth.png)

Sie haben den Dropbox-Connector jetzt so konfiguriert, dass ausgehende Aufrufe auf Ihr Dropbox-Konto zugreifen können, eingehende Aufrufe müssen von authentifizierten Benutzern ausgeführt werden. Im nächsten Abschnitt geben Sie an, welchen Authentifizierungsanbieter Sie zum Authentifizieren von Benutzern verwenden möchten.

## Konfigurieren des Gateways

Wie bereits [weiter oben](#gateway) erläutert, ist das Gateway eine spezielle Web-App, die den Zugriff auf alle API-Apps in einer Ressourcengruppe verwaltet. Um das Gateway zum Authentifizieren von Benutzern zu konfigurieren, wählen Sie einen Authentifizierungsanbieter wie beispielsweise Azure Active Directory, Google oder Twitter aus. Benutzer müssen sich dann zunächst beim ausgewählten Anbieter authentifizieren, bevor sie auf den Dropbox-Connector zugreifen können.

- Wechseln Sie hierzu zum Abschnitt [Konfigurieren des Gateways](app-service-api-dotnet-add-authentication.md#configure-the-gateway) im Lernprogramm [Schützen einer API-App](app-service-api-dotnet-add-authentication.md), und folgen Sie den dort genannten Anweisungen, um das Gateway in Ihrer Ressourcengruppe "DropboxRG" zu konfigurieren.

## Testen der Benutzer- und Dropbox-Authentifizierung

Nachdem Sie das Gateway in Ihrer Ressourcengruppe "DropboxRG" so konfiguriert haben, dass ein Authentifizierungsanbieter verwendet wird, können Sie Ihren Dropbox-Connector testen.

In der Regel verwenden Sie einen Connector, indem Sie ihn über den Code aufrufen. Hierzu werden Lernprogramme bereitgestellt, in denen die erforderliche Vorgehensweise hierfür erläutert wird. Gelegentlich kann es aber vorkommen, dass Sie vorab prüfen möchten, ob der Connector funktioniert, bevor Sie ihn mit anderen Elementen einer App verknüpfen. In diesem Lernprogramm wird gezeigt, wie Sie mithilfe eines Browsers und einem einfachen REST-Clienttool prüfen, ob Sie über den soeben erstellten und konfigurierten Dropbox-Connector mit dem Dropbox-Dienst interagieren können.

Die folgenden Anweisungen zeigen, wie Sie diese Schritte mit den Entwicklertools für den Chrome-Browser und dem Postman REST-Clienttool ausführen. Dies ist lediglich ein Beispiel, und Sie können dieselben Schritte mit anderen Browsern und Tools ausführen.

### Anmelden als Endbenutzer

Führen Sie die folgenden Schritte in einem neuen Browserfenster aus. Je nach gewähltem Authentifizierungsanbieter müssen Sie möglicherweise ein privates oder Inkognito-Browserfenster verwenden.

2. Wechseln Sie zur Anmelde-URL für das Gateway und den Authentifizierungsanbieter, das bzw. den Sie konfiguriert haben. Die URL folgt diesem Muster: 

    	http://[gatewayurl]/login/[providername]

	Sie können die Gateway-URL aus dem Blatt **Gateway** im [Azure-Vorschauportal] abrufen. (Sie gelangen zum Blatt **Gateway**, indem Sie im Blatt **Ressourcengruppe** auf das Gateway im angezeigten Diagramm klicken.)

	![Gateway-URL](./media/app-service-api-connnect-your-app-to-saas-connector/gatewayurl.png)

	Der Wert [providername] lautet "facebook" für Facebook, "twitter" für Twitter, "aad" für Azure Active Directory usw.

	Nachfolgend wird eine beispielhafte Anmelde-URL für Azure Active Directory gezeigt:

		https://dropboxrgaeb4ae60b7cb4f3d966dfa43.azurewebsites.net/login/aad/

3. Geben Sie Ihre Anmeldeinformationen ein, wenn der Browser eine Anmeldeseite anzeigt.
 
	Wenn Sie die Azure Active Directory-Anmeldung konfiguriert haben, verwenden Sie einen der auf der Registerkarte **Benutzer** aufgeführten Benutzer für die Anwendung, die Sie auf der Registerkarte "Azure Active Directory" im [Azure-Portal] erstellt haben, z. B. admin@contoso.onmicrosoft.com.

	Nach erfolgreicher Anmeldung wird die Seite "Anmeldung abgeschlossen" angezeigt.

	![](./media/app-service-api-connnect-your-app-to-saas-connector/logindone.png)

### Bereitstellen der Benutzeridentität in Dropbox

Um die Dropbox-Autorisierung zum Verwenden der Dropbox-API zu erhalten, müssen Sie die Benutzeranmeldeinformationen in Dropbox bereitstellen. Azure übernimmt diese Aufgabe für Sie, aber zum Auslösen des Vorgangs müssen Sie zu einer besonderen URL im Browser wechseln. Um diese URL abzurufen, senden Sie eine HTTP-Post-Anforderung an das Gateway.

Die HTTP-Post-Anforderung an das Gateway muss das Authentifizierungstoken enthalten, das Azure bei Ihrer Anmeldung bereitgestellt hat. Für Browseranforderungen erfolgt der Einschluss des Tokens automatisch, da es in einem Cookie gespeichert ist. Bei einer HTTP-Post-Anforderung mit Verwendung eines REST-Clienttools müssen Sie das Token zunächst aus dem Cookie abrufen und in den Anforderungsheader der HTTP-Post-Anforderung einfügen.

1. Wechseln Sie im Browserfenster mit der Meldung "Anmeldung abgeschlossen" zu den Entwicklertools für den Browser, und suchen Sie nach dem `x-zumo-auth`-Cookie. Lassen Sie das Fenster geöffnet, damit Sie den Cookiewert im nächsten Schritt kopieren können.
 
	Führen Sie die folgenden Schritte aus, um den Cookiewert in Chrome abzurufen:

	- Drücken Sie F12, um die Entwicklertools zu öffnen.
	- Wechseln Sie zur Registerkarte **Ressourcen**.
	- Suchen Sie die Cookies für Ihre Gatewaysite, und klicken Sie dreimal auf den **Wert** des `x-zumo-auth`-Cookies, um alle Daten auszuwählen. (Stellen Sie sicher, dass Sie den gesamten Cookiewert abrufen. Bei einem Doppelklick wird möglicherweise nur der erste Teil ausgewählt.)  

	![Kopieren des Tokens](./media/app-service-api-connnect-your-app-to-saas-connector/copytoken.png)

4. Erstellen und senden Sie in einem neuen Browserfenster oder einer neuen Browserregisterkarte eine HTTP-Post-Anforderung an das Gateway, um einen Zustimmungs-URL anzufordern. Schließen Sie das `x-zumo-auth`-Token als HTTP-Header ein.

	Die URL folgt diesem Muster:

		[gatewayurl]/api/consent/list?api-version=2015-01-14&redirecturl=[a URL you want the browser to go to after you authenticate]

	Zum Beispiel:

		https://dropboxrgaeb4ae60b7cb4f3d966dfa43.azurewebsites.net/api/consent/list?api-version=2015-01-14&redirecturl=https://portal.azure.com

	Führen Sie die folgenden Schritte aus, um die Anforderung in Postman in Chrome zu senden:

	- Geben Sie die **Anforderungs-URL** wie oben beschrieben ein.
	- Legen Sie die Methode auf **Post** fest.
	- Fügen Sie einen Header mit Namen `x-zumo-auth` hinzu.
	- Fügen Sie im Feld **Value** für den Header den Wert ein, den Sie aus dem `x-zumo-auth`-Cookie kopiert haben.
	- Klicken Sie auf **Send**.
	 
	Die folgende Abbildung zeigt das Postman-Tool in Chrome:

	![Senden einer Zustimmungs-URL](./media/app-service-api-connnect-your-app-to-saas-connector/sendforconsent.png)

	Die Antwort enthält eine URL, die Sie zum Authentifizieren des Benutzer für Dropbox verwenden können. (Wenn Sie eine Fehlermeldung erhalten, die angibt, dass die Get-Methode nicht unterstützt wird, obwohl Sie die Methode auf **Post** festgelegt haben, müssen Sie möglicherweise ein anderes REST-Clienttool verwenden. "Advanced REST Client" ist ein weiteres Chrome-Add-In, das Sie verwenden können.)

	![Zustimmungs-URL](./media/app-service-api-connnect-your-app-to-saas-connector/getconsenturl.png)

2. Wechseln Sie zur URL, die Sie als Antwort auf die HTTP-Post-Anforderung erhalten haben.

	Dropbox ordnet die Benutzeridentität Ihrer Dropbox-API-App zu und leitet den Browser dann an die Umleitungs-URL weiter, die Sie angegeben haben (z. B. das Azure-Vorschauportal, wenn Sie dem Beispiel gefolgt sind und https://portal.azure.com) verwendet haben).

	Da sich Ihre Dropbox-App im Entwicklermodus befindet, wird möglicherweise eine Anmeldeseite von Dropbox angezeigt, bevor der Browser zur Umleitungs-URL wechselt.  Nachdem Sie sich mit Ihren Dropbox-Anmeldeinformationen angemeldet haben, wird die Benutzeridentität, die Sie zur Anmeldung am Gateway verwendet haben, Ihrer Dropbox-App zugeordnet. Zukünftig ist der Schritt der Dropbox-Anmeldung dann für diese Benutzeridentität nicht mehr erforderlich.

3. Lassen Sie das Browserfenster geöffnet, da Sie es im nächsten Abschnitt benötigen. 

### Aufrufen der Dropbox-Connector-API 

Azure verwaltet jetzt drei Authentifizierungstoken für Sie:

- Ein Token vom Authentifizierungsanbieter, den Sie für das Gateway konfiguriert haben (z. B. Azure Active Directory).
- Ein Token von Dropbox.
- Ein Token, das Azure erstellt (das Token "zumo").

Das einzige Token, dass Sie für HTTP-Anforderungen zum Arbeiten mit Dropbox verwenden müssen, ist das zumo-Token. Azure hat dieses Token im Hintergrund den zwei weiteren Token zugeordnet, und der Connector stellt die zwei weiteren Token in Ihrem Namen bereit, wenn Aufrufe an Dropbox gesendet werden.

In den folgenden Schritten senden Sie eine Get-Anforderung an den Dropbox-Connector, um alle Dateien in Ihrem Dropbox-Konto anzuzeigen. Da Sie ein Browserfenster für eine Get-Anforderung verwenden können, und da Ihr Browserfenster bereits das zumo-Token in einem Cookie enthält, müssen Sie lediglich zu einer URL wechseln, um die Dropbox-Daten abzurufen.

1. Wechseln Sie im Browserfenster mit geöffnetem Azure-Vorschauportal zurück zum Blatt **API-App** für Ihren Dropbox-Connector. 

2. Kopieren Sie die URL der API-App.
 
4. Klicken Sie auf das Symbol **API-Definition**, um die im Connector verfügbaren API-Methoden anzuzeigen.

	![Blatt "API-App"](./media/app-service-api-connnect-your-app-to-saas-connector/apiappblade.png)

	![API-Definition](./media/app-service-api-connnect-your-app-to-saas-connector/apidef.png)

2. Rufen Sie im Browserfenster, dass Sie für die Authentifizierung beim Gateway verwendet haben, die GET-Methode auf, mit der Dateinamen aus einem Ordner abgerufen werden. Dies ist das URL-Muster:

		[connectorurl]/folder/[foldername]
   
3. Wenn Ihre Connector-URL https://dropboxrg784237ad3e7.azurewebsites.net lautet und Sie die Dateien im Ordner "blob" anzeigen möchten, lautet die URL folgendermaßen:

		https://dropboxrg784237ad3e7.azurewebsites.net/folder/blog

	Die verschiedenen Browser antworten unterschiedlich auf API-Aufrufe. Wenn Sie Chrome verwenden, wird eine Antwort ähnlich wie im folgenden Beispiel angezeigt:

	![Antwort auf Ordneranforderung](./media/app-service-api-connnect-your-app-to-saas-connector/dbresponse.png)

## Nächste Schritte

Sie haben erfahren Sie wie einen SaaS-Connector installieren, konfigurieren und testen. Weitere Informationen finden Sie unter [Verwenden von Connectors](../app-service-logic/app-service-logic-use-biztalk-connectors.md).

[Azure-Vorschauportal]: https://portal.azure.com/
[Azure-Vorschauportals]: https://portal.azure.com/
[Azure-Portal]: https://manage.windowsazure.com/
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->