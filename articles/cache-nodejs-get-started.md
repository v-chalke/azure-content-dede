﻿<properties 
   pageTitle="Verwenden von Azure-Redis-Cache mit Node.js" 
   description="Erste Schritte mit Azure-Redis-Cache mit Node.js und "node_redis"." 
   services="redis-cache" 
   documentationCenter="" 
   authors="MikeWasson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="nodejs"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="required" 
   ms.date="02/23/2015"
   ms.author="mwasson"/>

# Verwenden von Azure-Redis-Cache mit Node.js 

Azure-Redis-Cache bietet Zugriff auf einen sicheren dedizierten Redis-Cache, der von Microsoft verwaltet wird. Auf Ihren Cache kann in jeder Anwendung in Microsoft Azure zugegriffen werden.

Dieser Leitfaden beschreibt die ersten Schritte mit dem Azure-Redis-Cache und Node.js. Ein anderes Beispiel zum Verwenden von Azure-Redis-Cache mit Node.js finden Sie unter [Erstellen einer Node.js-Chatanwendung mit Socket.IO auf einer Azure-Website][].


## Voraussetzungen

Installieren Sie [node_redis](https://github.com/mranney/node_redis):

    npm install redis

In diesem Lernprogramm wird [node_redis](https://github.com/mranney/node_redis) verwendet. Sie können aber jeden Node.js-Client verwenden, der unter [http://redis.io/clients](http://redis.io/clients) aufgeführt ist.

## Erstellen eines Redis-Caches in Azure

Klicken Sie im [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkId=398536) auf **Neu** und dann auf **Redis-Cache**. 

  ![][1]

Geben Sie einen DNS-Hostnamen ein. Er muss das Format `<Name>.redis.cache.windows.net` haben. Klicken Sie auf **Erstellen**.

  ![][2]


Sobald der Cache erstellt wurde, klicken Sie im Portal darauf, um die Cache-Einstellungen anzuzeigen. Klicken Sie auf den Link unter **Schlüssel**, und kopieren Sie den Primärschlüssel. Sie benötigen ihn zum Authentifizieren von Anforderungen.

  ![][4]


## Aktivieren des Nicht-SSL-Endpunkts


Klicken Sie auf den Link unter **Ports**, und klicken Sie für "Zugriff nur über SSL zulassen" auf **Nein**. Dadurch wird der Nicht-SSL-Port für den Cache aktiviert. Der "node_redis"-Client unterstützt SSL derzeit nicht.

  ![][3]


## Fügen Sie dem Cache Daten hinzu, und rufen Sie sie ab.

	var redis = require("redis");
	
    // Put in your cache name and access key.
	var client = redis.createClient(6379,'<name>.redis.cache.windows.net', {auth_pass: '<key>' });
	
	client.set("foo", "bar", function(err, reply) {
	    console.log(reply);
	});
	
	client.get("foo",  function(err, reply) {
	    console.log(reply);
	});
    

Ausgabe:

	OK
	bar


## Nächste Schritte

- [Aktivieren Sie die Cachediagnose](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics), damit Sie die Integrität Ihres Caches [überwachen](https://msdn.microsoft.com/library/azure/dn763945.aspx) können. 
- Lesen Sie die offizielle [Redis-Dokumentation](http://redis.io/documentation).


<!--Image references-->
[1]: ./media/cache-java-get-started/cache01.png
[2]: ./media/cache-java-get-started/cache02.png
[3]: ./media/cache-java-get-started/cache03.png
[4]: ./media/cache-java-get-started/cache04.png

[Erstellen einer Node.js-Chatanwendung mit Socket.IO auf einer Azure-Website]: web-sites-nodejs-chat-app-socketio.md


<!--HONumber=49-->