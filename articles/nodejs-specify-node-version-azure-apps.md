<properties linkid="develop-nodejs-common-tasks-specifying-a-node-version" urlDisplayName="Specifying a Node.js Version" pageTitle="Specifying a Node.js Version" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="Node.js" title="Specifying a Node.js version in an Azure application" authors="larryfr" solutions="" manager="paulettm" editor="mollybos" />

Festlegen einer Node.js-Version in einer Azure-Anwendung
========================================================

Beim Hosten von Node.js-Anwendungen sollten Sie sicherstellen, dass Ihre Anwendung eine bestimmte Version von Node.js verwendet. Es gibt mehrere Möglichkeiten, dies für Anwendungen unter Azure zu erreichen.

Standardversionen
-----------------

Azure unterstützt momentan die Node.js-Versionen 0.6.17, 0.6.20 und 0.8.4. Sofern nicht anderweitig angegeben wird 0.6.20 als Standardversion verwendet.

**Hinweis**

Wenn Sie Ihre Anwendung in einem Azure Cloud Service hosten (Web- oder Workerrolle) und Sie Ihre Anwendung zum ersten Mal bereitstellen, versucht Azure, dieselbe Node.js-Version wie in Ihrer Entwicklungsumgebung zu verwenden, falls diese mit einer der in Azure verfügbaren Standardversionen übereinstimmt.

Versionsverwaltung mit package.json
-----------------------------------

Sie können den folgenden Code zu Ihrer **package.json**-Datei hinzufügen, um die zu verwendende Node.js-Version anzugeben:

    "engines":{"node":version}

Wobei *version* die zu verwendende Versionsnummer ist. Sie können auch komplexere Bedingungen für die Version angeben, z. B.:

    "engines":{"node": "0.6.22 || 0.8.x"}

Da 0.6.22 keine der Standardversionen in der Hostumgebung ist, wird stattdessen die höchste verfügbare Version der 0.8-Reihe verwendet: 0.8.4.

Versionsverwaltung von Cloud-Diensten mit PowerShell
----------------------------------------------------

Wenn Sie Ihre Anwendung in einem Cloud-Dienst hosten und mit Azure PowerShell bereitstellen, können Sie die verwendete Node.js-Version mithilfe des **Set-AzureServiceProjectRole** PowerShell-Cmdlets überschreiben. Beispiel:

    Set-AzureServiceProjectRole WebRole1 node 0.8.4

Außerdem können Sie **Get-AzureServiceProjectRoleRuntime** verwenden, um eine Liste der verfügbaren Node.js-Versionen für Anwendungen abzurufen, die als Cloud-Dienste gehostet werden.

Verwenden benutzerdefinierter Versionen mit Azure Web Sites
-----------------------------------------------------------

Obwohl Azure verschiedene Standardversionen von Node.js anbietet, kann es sein, dass Sie eine andere Version verwenden möchten. Falls Ihre Anwendung als Azure Web Site gehostet wird, können Sie dies in der Datei **iisnode.yml** angeben. Die folgenden Schritte beschreiben, wie Sie eine benutzerdefinierte Version von Node.js mit einer Azure Web Site verwenden können:

1.  Erstellen Sie ein neues Verzeichnis und erstellen Sie eine Datei **server.js** in diesem Verzeichnis. Die Datei **server.js** sollte den folgenden Code enthalten:

         var http = require('http');
         http.createServer(function(req,res) {
           res.writeHead(200, {'Content-Type': 'text/html'});
           res.end('Hello from Azure running node version: ' + process.version + '</br>');
         }).listen(process.env.PORT || 3000);

    Dieser Code zeigt beim Aufrufen der Website die verwendete Node.js-Version an.

2.  Erstellen einer neuen Webseite und Abrufen des Website-Namens. Das folgende Beispiel verwendet die [Azure-Befehlszeilentools](/de-de/develop/nodejs/how-to-guides/command-line-tools/), um eine neue Azure Web Site mit dem Namen **mywebsite** zu erstellen und anschließend ein Git-Repository für die Website einzurichten.

         azure site create mywebsite --git

3.  Erstellen Sie ein neues Verzeichnis mit dem Namen **bin** unterhalb des Verzeichnisses, das die Datei **server.js** enthält.

4.  Laden Sie die Version von **node.exe** (die Windows-Version) herunter, die Sie in Ihrer Anwendung verwenden möchten. Das folgende Beispiel verwendet **curl**, um Version 0.8.1 herunterzuladen:

         curl -O http://nodejs.org/dist/v0.8.1/node.exe

    Speichern Sie die Datei **node.exe** in dem zuvor erstellten **bin**-Ordner.

5.  Erstellen Sie eine Datei **iisnode.yml** im gleichen Verzeichnis wie die **server.js** und fügen Sie den folgenden Inhalt zur Datei **iisnode.yml** hinzu:

         nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    Unter diesem Pfad befindet sich die Datei **node.exe** in Ihrem Projekt, sobald Sie Ihre Anwendung auf der Azure Web Site veröffentlicht haben.

6.  Veröffentlichen Sie Ihre Anwendung. Da die Website zuvor mit dem Parameter --git erstellt wurde, können Sie die Anwendungsdateien mit den folgenden Befehlen zu Ihrem lokalen Git-Repository hinzufügen und anschließend in das Website-Repository veröffentlichen:

         git add .
         git commit -m "testing node v0.8.1"
         git push azure master

    Öffnen Sie die Anwendung nach der Veröffentlichung in einem Browser. Sie sollten die folgende Nachricht sehen: "Hello from Azure running node version: v0.8.1".

Nächste Schritte
----------------

Nachdem Sie gelernt haben, wie Sie die von ihrer Anwendung verwendete Node.js-Version angeben können, empfehlen wir die Themen [Arbeiten mit Modulen](/de-de/develop/nodejs/common-tasks/working-with-node-modules/), [Erstellen und Bereitstellen einer Node.js-Website](/de-de/develop/nodejs/tutorials/create-a-website-(mac)/) und [Verwenden der Azure-Befehlszeilentools für Mac und Linux](/de-de/develop/nodejs/how-to-guides/command-line-tools/).
