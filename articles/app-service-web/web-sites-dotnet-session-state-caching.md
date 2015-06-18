<properties 
	pageTitle="Sitzungsstatus mit Azure-Redis-Cache in Azure App Service" 
	description="Erfahren Sie, wie Sie den Azure Cache Service zur Unterstützung des ASP.NET-Sitzungszustand-Caching verwenden." 
	services="app-service\web" 
	documentationCenter=".net" 
 	authors="Rick-Anderson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="riande"/>


# Sitzungsstatus mit Azure Redis Cache in Azure App Service


In diesem Thema wird erläutert, wie Sie den Azure-Redis-Cache-Dienst für den Sitzungszustand verwenden.

Falls Ihre ASP.NET-Web-App den Sitzungszustand verwendet, müssen Sie einen externen Sitzungszustandsanbieter (entweder den Redis Cache Service oder einen SQL Server-Sitzungszustandsanbieter) konfigurieren. Wird der Sitzungszustand ohne einen externen Anbieter verwendet, sind Sie auf eine Instanz Ihrer Web-App beschränkt. Der Redis Cache Service lässt sich am schnellsten und einfachsten aktivieren.

<h2><a id="createcache"></a>Erstellen des Caches</h2>
Befolgen Sie [diese Anweisungen](../cache-dotnet-how-to-use-azure-redis-cache.md#create-cache) zum Erstellen des Caches.

<h2><a id="configureproject"></a>Hinzufügen des RedisSessionStateProvider-NuGet-Pakets zur Web-App</h2>
Installieren Sie das `RedisSessionStateProvider`-NuGet-Paket. Verwenden Sie den folgenden Befehl zur Installation über die Paket-Manager-Konsole (**Tools** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole**):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`
  
Bei Installation über**Extras** > **NuGet-Paket-Manage** > **NuGet-Pakete für Projektmappe verwalten** suchen Sie nach `RedisSessionStateProvider`.

Weitere Informationen finden Sie auf der [NuGet-RedisSessionStateProvider-Seite](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/) und unter [Konfigurieren der Cacheclients](../cache-dotnet-how-to-use-azure-redis-cache.md#NuGet).

<h2><a id="configurewebconfig"></a>Ändern der Datei "web.config"</h2>
Das NuGet-Paket fügt nicht nur Assemblyverweise für Cache, sondern auch Stub-Einträge in die Datei *web.config* ein.

1. Öffnen Sie *web.config*, und suchen Sie das Element **sessionState**.

1. Geben Sie die Werte für `host`, `accessKey`, `port` (der SSL-Port sollte 6380 lauten) ein, und legen Sie `SSL` auf `true` fest. Diese Werte finden Sie im [Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715) auf dem Blatt für Ihre Cacheinstanz. Weitere Informationen finden Sie unter [Verbinden mit dem Cache](../cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Beachten Sie, dass der Nicht-SSL-Port für neue Caches standardmäßig deaktiviert ist. Weitere Informationen zum Aktivieren des Nicht-SSL-Ports finden Sie im Abschnitt [Zugriffsports](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) zum Thema [Konfigurieren eines Caches in Azure-Redis-Cache](https://msdn.microsoft.com/library/azure/dn793612.aspx). Das folgende Markup zeigt die Änderungen an der Datei *web.config*.


  <pre class="prettyprint">  
    &lt;system.web>
    &lt; CustomErrors Mode = "Off" / >
    &lt; Authentication Mode = "None" / >
    &lt;compilation debug="true" targetFramework="4.5" />
    &lt;httpRuntime targetFramework="4.5" />
  &lt; SessionState-Modus = "Custom" CustomProvider = "RedisSessionProvider" >
      &lt; Providers >  
          &lt;!--&lt; hinzufügen Name = "RedisSessionProvider" 
            Host = "127.0.0.1" [Zeichenfolge]
            Port = "" [Zahl]
            AccessKey = "" [Zeichenfolge]
            SSL = "False" [True|false]
            ThrowOnError = "True" [True|false]
            RetryTimeoutInMilliseconds = "0" [Zahl]
            DatabaseId = "0" [Zahl]
            ApplicationName = "" [Zeichenfolge]
          / >-->
         &lt; hinzufügen Name = "RedisSessionProvider" 
              Type="Microsoft.Web.Redis.RedisSessionStateProvider" 
              <mark>Port = "6380"
              Host = "movie2.redis.cache.windows.net" 
              AccessKey = "m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk =" 
              SSL = "true"</mark> / >
      &lt;!--&lt; hinzufügen Name = "MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" Host = "127.0.0.1" AccessKey = "" Ssl = "false" / >-->
      &lt;/providers>
    &lt;/sessionState>
  &lt;/system.web></pre>


<h2><a id="usesessionobject"></a>Verwenden des Sitzungsobjekts im Code</h2>
Der letzte Schritt ist die Verwendung des Sitzungsobjekts im ASP.NET-Code. Fügen Sie Objekte mit der Methode **Session.Add** zum Sitzungsstatus hinzu. Diese Methode verwendet Schlüssel-Wert-Paare, um Elemente im Sitzungszustandscache zu speichern.

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

Der folgende Code ruft diesen Wert aus dem Sitzungszustand ab.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue;	

Sie können den Redis Cache auch zum Zwischenspeichern von Objekten in Ihrer Web-App verwenden. Weitere Informationen finden Sie unter [MVC-Film-App mit Azure Redis Cache in 15 Minuten](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/). Weitere Informationen zur Verwendung des ASP.NET-Sitzungsstatus finden Sie unter [Überblick über den ASP.NET-Sitzungsstatus][].

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)

  *Von [Rick Anderson](https://twitter.com/RickAndMSFT)*
  
  [installed the latest]: http://www.windowsazure.com/downloads/?sdk=net
  [Überblick über den ASP.NET-Sitzungsstatus]: http://msdn.microsoft.com/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->