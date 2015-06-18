<properties
	pageTitle="Erstellen einer Node.js-Web-App in Azure mit MongoDB mithilfe des MongoLab-Add-Ons"
	description="Erfahren Sie mehr über das Erstellen einer Node.js-Web-App in Azure App Service, die eine Verbindung zu einer von MongoLab gehosteten MongoDB-Instanz herstellt."
	tags="azure-classic-portal"
	services="app-service\web, virtual-machines"
	documentationCenter="nodejs"
	authors="chrisckchang"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/20/2014"
	ms.author="mwasson"/>






# Erstellen einer Node.js-Web-App in Azure mit MongoDB mithilfe des MongoLab-Add-Ons


<p><em>Von Eric Sedor, MongoLab</em></p>

Grüße, Abenteurer! Willkommen bei MongoDB-as-a-Service. In diesem Lernprogramm führen Sie folgende Schritte aus:

1. [Bereitstellen der Datenbank][provision] – Das Azure Marketplace-Add-On [MongoLab](http://mongolab.com) stellt eine MongoDB-Datenbank bereit, die in der Azure-Cloud gehostet und von der Clouddatenbankplattform von MongoLab verwaltet wird.
2. [Erstellen der App][create]: Hierbei handelt es sich um eine einfache Node.js-App für die Verwaltung einer Aufgabenliste.
3. [Bereitstellen der App][deploy] – Durch das Verknüpfen einiger Konfigurationshooks wird das Pushen unseres Codes zu den [Azure App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) sehr einfach.
4. [Verwalten der Datenbank][manage]: Schließlich zeigen wir Ihnen das webbasierte Datenbankverwaltungsportal von MongoLab, in dem Sie Daten problemlos suchen, visualisieren und ändern können.

Sie können in diesem Lernprogramm jederzeit eine E-Mail an [support@mongolab.com](mailto:support@mongolab.com) senden, falls Sie Fragen haben.

Bevor Sie fortfahren, sollten Sie sicherstellen, dass Sie Folgendes installiert haben:

* [Node.js]-Version 0.10.29+

* [Git]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## Schnellstart
Wenn Sie den Azure Store bereits kennengelernt haben, finden Sie in diesem Abschnitt einen Schnellstart. Fahren Sie andernfalls mit [Bereitstellen der Datenbank][provision] weiter unten fort.

1. Öffnen Sie durch Klicken auf den Azure Marketplace **Neu** > **Marketplace**.  
<!-- ![Store][button-store] -->
2. Klicken Sie auf das **MongoLab**-Add-On. ![MongoLab][entry-mongolab]
3. Klicken Sie in der Liste der Add-Ons auf das **MongoLab**-Add-On und dann auf **Verbindungsinformationen**. ![ConnectionInfoButton][button-connectioninfo]  
4. Kopieren Sie den **MONGOLAB_URI** in die Zwischenablage. ![ConnectionInfoScreen][screen-connectioninfo]

	>[AZURE.NOTE]Dieser URI enthält Ihren Benutzernamen und Ihr Kennwort für die Datenbank. Behandeln Sie diese Informationen als vertraulich, und geben Sie sie nicht weiter.

5. Fügen Sie den Wert der Liste mit den **Verbindungszeichenfolgen** im **Konfigurationsmenü** Ihrer Web-App im Azure App Service hinzu: ![WebAppConnectionStrings][focus-website-connectinfo]
6. Geben Sie unter **Name** **MONGOLAB_URI** ein.
7. Fügen Sie unter **Wert** die Verbindungszeichenfolge ein, die wir im vorhergehenden Abschnitt erzeugt haben.
8. Wählen Sie im Dropdownfeld für den Typ die Option **Benutzerdefiniert** aus (anstelle des Standards **SQLAzure**).
9. Führen Sie `npm install mongoose` aus, um Mongoose, einen MongoDB-Node-Treiber, abzurufen.
10. Richten Sie im Code einen Hook ein, um die MongoLab-Verbindungs-URI aus der Umgebungsvariablen abzurufen und eine Verbindung herzustellen:

        var mongoose = require('mongoose');  
 		...
 		var connectionString = process.env.CUSTOMCONNSTR_MONGOLAB_URI
 		...
 		mongoose.connect(connectionString);

Hinweis: Azure fügt das Präfix **CUSTOMCONNSTR\\_** zur ursprünglich deklarierten Verbindungszeichenfolge hinzu. Darum wird im Code auf **CUSTOMCONNSTR\\_MONGOLAB\\_URI.** und nicht auf **MONGOLAB\\_URI** verwiesen.

Nun folgt das vollständige Lernprogramm ...

<a name="provision"></a>
## Bereitstellen der Datenbank

[AZURE.INCLUDE [howto-provision-mongolab](../../includes/howto-provision-mongolab.md)]

<a name="create"></a>
## Erstellen der App

In diesem Abschnitt richten Sie Ihre Entwicklungsumgebung ein und erstellen den Code für eine grundlegende Aufgabenlisten-Webanwendung mithilfe von Node.js, Express und MongoDB. [Express] stellt ein Controlleranzeigeframework für den Knoten bereit, während [Mongoose] ein Treiber für die Kommunikation mit MongoDB in Node ist.

### Einrichtung

#### Generieren des Gerüsts und Installieren der Module

1. Erstellen Sie an der Befehlszeile das Verzeichnis **tasklist** und navigieren Sie zu diesem Verzeichnis. Dies ist Ihr Projektverzeichnis.
2. Geben Sie den folgenden Befehl für die Expressinstallation ein.

		npm install express -g

	￼￼￼`-g` gibt den globalen Modus an, in dem das <strong>express</strong>-Modul ohne Angabe eines Verzeichnispfads verfügbar ist. Wenn <strong>Fehler: EPERM, chmod '/usr/local/bin/express'</strong> angezeigt wird, verwenden Sie <strong>sudo</strong>, um npm auf einer höheren Berechtigungsstufe auszuführen.

    Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

		express@4.9.1 C:\Users\mongolab\AppData\Roaming\npm\node_modules\express
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── fresh@0.2.4
		├── cookie@0.1.2
		├── range-parser@1.0.2
		├── escape-html@1.0.1
		├── cookie-signature@1.0.5
		├── finalhandler@0.2.0
		├── vary@1.0.0
		├── media-typer@0.3.0
		├── parseurl@1.3.0
		├── serve-static@1.6.2
		├── methods@1.1.0
		├── path-to-regexp@0.1.3
		├── depd@0.4.5
		├── qs@2.2.3
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── debug@2.0.0 (ms@0.6.2)
		├── proxy-addr@1.0.1 (ipaddr.js@0.1.2)
		├── etag@1.3.1 (crc@3.0.0)
		├── send@0.9.2 (destroy@1.0.3, ms@0.6.2, mime@1.2.11)
		├── accepts@1.1.0 (negotiator@0.4.7, mime-types@2.0.1)
		└── type-is@1.5.1 (mime-types@2.0.1)

3. Verwenden Sie den Befehl **express**, um das Gerüst zu erstellen, das für diese Anwendung verwendet wird:

    	express

    Beachten Sie, dass in diesem Lernprogramm Express v4.x.x verwendet wird. Wenn Sie den Express 3-App-Generator bereits in Ihrem System installiert haben, sollten Sie diesen zuerst deinstallieren:

    	npm uninstall -g express

    Installieren Sie jetzt den neuen Generator für Version 4.x.x:

    	npm install -g express-generator

	Sobald der Befehl **express** ausgeführt wird, sollte die Ausgabe in etwa wie folgt aussehen:

		create : .
		create : ./package.json
		create : ./app.js
		create : ./public
		create : ./public/javascripts
		create : ./public/images
		create : ./public/stylesheets
		create : ./public/stylesheets/style.css
		create : ./routes
		create : ./routes/index.js
		create : ./routes/users.js
		create : ./views
		create : ./views/index.jade
		create : ./views/layout.jade
		create : ./views/error.jade
		create : ./bin
		create : ./bin/www

		install dependencies:
		$ cd . && npm install


	Nach Abschluss dieses Befehls sollten mehrere neue Verzeichnisse und Dateien im Verzeichnis **tasklist** vorhanden sein.

4. Geben Sie Folgendes ein, um die in der Datei **package.json** beschriebenen Module zu installieren:

        npm install

    Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

		cookie-parser@1.3.3 node_modules/cookie-parser
		├── cookie@0.1.2
		└── cookie-signature@1.0.5

		debug@2.0.0 node_modules/debug
		└── ms@0.6.2

		serve-favicon@2.1.4 node_modules/serve-favicon
		├── ms@0.6.2
		├── fresh@0.2.4
		└── etag@1.3.1 (crc@3.0.0)

		morgan@1.3.1 node_modules/morgan
		├── basic-auth@1.0.0
		├── depd@0.4.5
		└── on-finished@2.1.0 (ee-first@1.0.5)

		express@4.9.1 node_modules/express
		├── utils-merge@1.0.0
		├── merge-descriptors@0.0.2
		├── cookie@0.1.2
		├── fresh@0.2.4
		├── escape-html@1.0.1
		├── range-parser@1.0.2
		├── cookie-signature@1.0.5
		├── finalhandler@0.2.0
		├── vary@1.0.0
		├── media-typer@0.3.0
		├── serve-static@1.6.2
		├── parseurl@1.3.0
		├── methods@1.1.0
		├── path-to-regexp@0.1.3
		├── depd@0.4.5
		├── qs@2.2.3
		├── etag@1.3.1 (crc@3.0.0)
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── proxy-addr@1.0.1 (ipaddr.js@0.1.2)
		├── send@0.9.2 (destroy@1.0.3, ms@0.6.2, mime@1.2.11)
		├── type-is@1.5.1 (mime-types@2.0.1)
		└── accepts@1.1.0 (negotiator@0.4.7, mime-types@2.0.1)

		body-parser@1.8.2 node_modules/body-parser
		├── media-typer@0.3.0
		├── raw-body@1.3.0
		├── bytes@1.0.0
		├── depd@0.4.5
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── qs@2.2.3
		├── iconv-lite@0.4.4
		└── type-is@1.5.1 (mime-types@2.0.1)

		jade@1.6.0 node_modules/jade
		├── character-parser@1.2.0
		├── commander@2.1.0
		├── void-elements@1.0.0
		├── mkdirp@0.5.0 (minimist@0.0.8)
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
		├── constantinople@2.0.1 (uglify-js@2.4.15)
		└── with@3.0.1 (uglify-js@2.4.15)

	Die Datei **package.json** ist eine der durch den Befehl **express** erstellten Dateien. Diese Datei enthält eine Liste zusätzlicher Module, die für eine Express-Anwendung erforderlich sind. Wenn Sie später diese Anwendung in Azure App Service-Web-Apps bereitstellen, wird anhand dieser Datei bestimmt, welche Module auf Azure installiert werden müssen, um Ihre Anwendung zu unterstützen.

5. Geben Sie anschließend den folgenden Befehl ein, um das Mongoose-Modul lokal zu installieren und einen Eintrag dafür in der Datei **package.json** zu speichern:

		npm install mongoose --save

	Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

		mongoose@3.8.16 node_modules/mongoose
		├── regexp-clone@0.0.1
		├── muri@0.3.1
		├── sliced@0.0.5
		├── hooks@0.2.1
		├── mpath@0.1.1
		├── mpromise@0.4.3
		├── ms@0.1.0
		├── mquery@0.8.0 (debug@0.7.4)
		└── mongodb@1.4.9 (readable-stream@1.0.31, kerberos@0.0.3, bson@0.2.12)

### Der Code

Da Ihre Umgebung und das Gerüst jetzt fertig sind, erweitern wir die grundlegende Anwendung, die durch den Befehl **express** erstellt wurde, durch Hinzufügen einer Datei **task.js**, die das Modell für Ihre Aufgaben enthält. Außerdem ändern Sie die vorhandene Datei **app.js** und erstellen eine neue Controllerdatei **tasklist.js**, um das Modell zu verwenden.

#### Erstellen des Modells

1. Erstellen Sie im Verzeichnis **tasklist** ein neues Verzeichnis namens **models**.

2. Erstellen Sie im Verzeichnis **models** eine neue Datei namens **task.js**. Diese Datei enthält das Modell für die von Ihrer Anwendung erstellten Aufgaben.

3. Fügen Sie der Datei **task.js** den folgenden Code hinzu:

        var mongoose = require('mongoose'),
          Schema = mongoose.Schema;

        var TaskSchema = new Schema({
	      itemName      : String,
	      itemCategory  : String,
	      itemCompleted : { type: Boolean, default: false },
	      itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5. Speichern und schließen Sie die Datei **task.js**.

#### Erstellen des Controllers

1. Erstellen Sie im Verzeichnis **tasklist/routes** eine neue Datei namens **tasklist.js**, und öffnen Sie sie in einem Texteditor.

2. Fügen Sie **tasklist.js** den folgenden Code hinzu. Dadurch werden das Mongoose-Modul und das in **task.js** definierte Aufgabenmodell geladen. Die TaskList-Funktion wird verwendet, um die Verbindung zum MongoDB-Server basierend auf dem Wert **connection** herzustellen. Außerdem werden die Methoden **showTasks**, **addTask** und **completeTasks** bereitgestellt:

		var mongoose = require('mongoose'),
 		  task = require('../models/task.js');

		module.exports = TaskList;

		function TaskList(connection) {
		  mongoose.connect(connection);
		}

		TaskList.prototype = {
		  showTasks: function(req, res) {
		    task.find({ itemCompleted : false }, function foundTasks(err, items) {
		    res.render('index', { title: 'My ToDo List', tasks: items })
		    });
		  },

		  addTask: function(req,res) {
		    var item = req.body;
		    var newTask = new task();
		    newTask.itemName = item.itemName;
		    newTask.itemCategory = item.itemCategory;
		    newTask.save(function savedTask(err) {
		      if(err) {
		        throw err;
		      }
		    });
		    res.redirect('/');
		  },


		  completeTask: function(req,res) {
		    var completedTasks = req.body;
		    for(taskId in completedTasks) {
		      if(completedTasks[taskId]=='true') {
		        var conditions = { _id: taskId };
		        var updates = { itemCompleted: completedTasks[taskId] };
		        task.update(conditions, updates, function updatedTask(err) {
		          if(err) {
		            throw err;
		          }
		        });
		      }
		    }
		    res.redirect('/');
		  }
		}

3. Speichern Sie die Datei **tasklist.js**.

#### Ändern der Indexansicht

1. Wechseln Sie zum Verzeichnis **views**, und öffnen Sie die Datei **index.jade** in einem Texteditor.

2. Ersetzen Sie den Inhalt der Datei **index.jade** durch den folgenden Code. Damit werden die Ansicht zur Anzeige vorhandener Aufgaben sowie ein Formular für das Hinzufügen neuer Aufgaben und das Markieren von vorhandenen Aufgaben als abgeschlossen definiert.

		h1 #{title}
		form(action="/completetask", method="post")
		  table(border="1")
		    tr
		      td Name
		      td Category
		      td Date
		      td Complete
		    each task in tasks
		      tr
		        td #{task.itemName}
		        td #{task.itemCategory}
		        - var day   = task.itemDate.getDate();
		        - var month = task.itemDate.getMonth() + 1;
		        - var year  = task.itemDate.getFullYear();
		        td #{month + "/" + day + "/" + year}
		        td
		          input(type="checkbox", name="#{task._id}", value="#{!task.itemCompleted}", checked=task.itemCompleted)
		  input(type="submit", value="Update tasks")
		hr
		form(action="/addtask", method="post")
		  table(border="1")
		    tr
		      td Item Name:
		      td
		        input(name="itemName", type="textbox")
		    tr
		      td Item Category:
		      td
		        input(name="itemCategory", type="textbox")
		  input(type="submit", value="Add item")

3. Speichern und schließen Sie die Datei **index.jade**.

#### Ersetzen von "app.js"

1. Öffnen Sie im Verzeichnis **tasklist** die Datei **app.js** in einem Texteditor. Diese Datei wurde zuvor mit dem Befehl **express** erstellt.
2. Fügen Sie den folgenden Code am Anfang der Datei **app.js** hinzu. Dadurch wird **TaskList** mit der Verbindungszeichenfolge für den MongoDB-Server initialisiert:

		var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.CUSTOMCONNSTR_MONGOLAB_URI);

 	Beachten Sie die zweite Zeile: Sie rufen eine Umgebungsvariable auf, die Sie später konfigurieren werden und die die Verbindungsinformationen für die Mongo-Instanz enthält. Falls Sie eine lokale Mongo-Instanz zu Entwicklungszwecken ausführen, können Sie diesen Wert temporär auf "localhost" statt auf `process.env.CUSTOMCONNSTR_MONGOLAB_URI` festlegen.

3. Suchen Sie folgende Zeilen:

		app.use('/', routes);
		app.use('/users', users);

	Ersetzen Sie sie durch Folgendes:

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));

	Dadurch werden die in **tasklist.js** definierten Funktionen als Routen hinzugefügt.

4. Um Ihre Anwendung zu initialisieren, fügen Sie die folgende Zeile am Ende der Datei **app.js** hinzu:

		app.listen(3000); // Listen on port 3000
		module.exports = app;

5. Speichern Sie die Datei **app.js**.

<a name="deploy"></a>
## Bereitstellen der App

Nachdem die Anwendung entwickelt wurde, können Sie die Web-App für das Hosten der Anwendung in Azure App Service erstellen, konfigurieren und den Code bereitstellen. In diesem Abschnitt ist die Verwendung der MongoDB-Verbindungszeichenfolge (URI) ein zentraler Aspekt. Sie werden eine Umgebungsvariable für Ihre Web-App mit diesem URI konfigurieren, um den URI vom Code zu trennen. Sie sollten die URI als vertrauliche Information behandeln, da sie Anmeldeinformationen für die Verbindung zur Datenbank enthält.

Die Schritte in diesem Abschnitt für Mac, Linux und Windows Azure-Befehlszeilenschnittstelle verwenden, erstellen Sie eine neue Webanwendung in Azure-App-Dienst, und verwenden Sie Git, um Ihre Anwendung bereitzustellen. Zur Durchführung dieser Schritte benötigen Sie ein Azure-Abonnement.

### Installieren der Azure-Befehlszeilenschnittstelle

Verwenden Sie zum Installieren der Azure-CLI den folgenden Befehl ein:

	npm install azure-cli -g

Wenn Sie bereits installiert haben die <strong>Azure SDK für Node.js</strong> aus der <a href="/develop/nodejs/">Azure Developer Center</a>, der Azure-Befehlszeilenschnittstelle sollte bereits installiert sein. Weitere Informationen finden Sie unter <a href="../virtual-machines-command-line-tools.md">Azure CLI</a>.

Während der Azure-CLI-Tools vor allem für Mac und Linux-Benutzer erstellt wurden, werden sie basieren auf Node.js und sollten auf allen Systemen, die Node ausführen funktionieren.

### Importieren von Veröffentlichungseinstellungen

Bevor Sie mit der Azure-Befehlszeilenschnittstelle, müssen Sie zunächst eine Datei mit Informationen zu Ihrem Abonnement herunterladen. Führen Sie die folgenden Schritte aus, um diese Datei herunterzuladen und zu importieren.

1. Geben Sie an der Befehlszeile den folgenden Befehl ein, um den Browser zu starten, und navigieren Sie zur Downloadseite. Melden Sie sich bei Aufforderung mit dem Konto an, dass Ihrem Abonnement zugeordnet ist.

		azure account download

	![Die Downloadseite][download-publishing-settings]

	Der Dateidownload sollte automatisch beginnen. Klicken Sie andernfalls auf den Link oben auf der Seite, um die Datei manuell herunterzuladen.

2. Nach Abschluss des Dateidownloads verwenden Sie den folgenden Befehl, um die Einstellungen zu importieren:

		azure account import <path-to-file>

	Geben Sie den Pfad und den Dateinamen der Datei mit den Veröffentlichungseinstellungen an, die Sie im vorherigen Schritt heruntergeladen haben. Nach Abschluss des Befehls sollten Sie eine Ausgabe ähnlich der folgenden erhalten:

		info:   Executing command account import
		info:   Found subscription: subscriptionname
		info:   Setting default subscription to: subscriptionname
		warn:   The '/Users/mongolab/.azure/publishSettings.xml' file contains sensitive information.
		warn:   Remember to delete it now that it has been imported.
		info:   Account publish settings imported successfully
		info:   account import command OK


3. Nach Abschluss des Imports sollten Sie die Datei mit den Veröffentlichungseinstellungen löschen, da sie nicht mehr benötigt wird und vertrauliche Informationen zu Ihrem Azure-Abonnement enthält.

### Erstellen einer neuen Web-App und Durchführen eines Pushvorgangs für Ihren Code

Das Erstellen einer Web-App in Azure App Service ist sehr einfach. Wenn dies Ihre erste Azure-Web-App ist, müssen Sie das Portal verwenden. Wenn Sie bereits mindestens eine Website erstellt haben, fahren Sie mit Schritt 7 fort.

1. Klicken Sie im Azure-Portal auf **Neu**. ![Neu][button-new]
2. Wählen Sie **berechnen > Web app > Schnellerfassung**.
<!-- ![Create Web App][screen-mongolab-newwebsite] -->
3. Geben Sie einen URL-Präfix ein. Wählen Sie einen Namen aus, aber denken Sie daran, dass er eindeutig sein muss ("mymongoapp" ist wahrscheinlich nicht verfügbar).
4. Klicken Sie auf **Web-App erstellen**.
5. Klicken Sie nach Abschluss der Web-App-Erstellung auf den Namen der Web-App in der Web-App-Liste. Das Web-App-Dashboard zeigt.
<!-- ![Web App Dashboard][screen-mongolab-websitedashboard] -->
6. Klicken Sie auf **Git-Veröffentlichung einrichten** unter **Schnellansicht**. Geben Sie dann den gewünschten Benutzernamen und das gewünschte Kennwort für Git ein. Sie verwenden dieses Kennwort für Pushvorgänge zu Ihrer Web-App (in Schritt 9).  
7. Wenn Sie Ihre Web-App mit den oben genannten Schritten erstellt haben, wird der Prozess durch den folgenden Befehl abgeschlossen. Wenn Sie allerdings bereits mehr als eine Azure-Web-App haben, können Sie die oben genannten Schritte überspringen und eine neue Web-App mithilfe des gleichen Befehls erstellen. Im Projektverzeichnis **tasklist**:

		azure site create myuniquewebappname --git  
	Ersetzen Sie "myuniquewebappname" durch den eindeutigen Namen für Ihre Web-App. Wenn die Web-App mit diesem Befehl erstellt wird, werden Sie aufgefordert, das Datencenter anzugeben, in dem sich die Web-App befindet. Wählen ein Datencenter in geografischer Nähe zur MongoLab-Datenbank aus.

	Durch den Parameter `--git` wird Folgendes erstellt: * Ein lokales Git-Repository im Ordner **tasklist**, falls keins vorhanden ist. * Ein [Git-Remote] namens "azure", das verwendet wird, um die Anwendung in Azure zu veröffentlichen. * Die Datei [iisnode.yml] mit den Einstellungen, die von Azure verwendet werden, um Node-Anwendungen zu hosten. * Eine .gitignore-Datei, die verhindert, dass der Ordner mit den Node-Modulen auf .git veröffentlicht wird.

	Nach Abschluss dieses Befehls wird eine Ausgabe ähnlich der folgenden angezeigt. Beachten Sie, dass die Zeile, die mit **Created site at** beginnt, die URL für die Web-App enthält.

		info:   Executing command site create
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default web.config file
		info:   Creating a new web site
		info:   Created web site at  mongodbtasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure http://gitusername@myuniquewebappname.azurewebsites.net/mongodbtasklist.git`
		info:   site create command OK

8. Verwenden Sie die folgenden Befehle, um Dateien zu Ihrem lokalen Git-Repository hinzuzufügen und dann zu übernehmen:

		git add .
		git commit -m "adding files"

9. Pushvorgang für den Code:

		git push azure master  

	Wenn Sie die letzten Änderungen am Git-Repository per Push an die Azure-Web-App in Azure App Service übertragen, müssen Sie angeben, dass die Zielstruktur **master** lautet, da diese für den Web-App-Inhalt verwendet wird. Wenn Sie nach einem Kennwort gefragt werden, geben Sie das Kennwort ein, das Sie beim Einrichten der Git-Veröffentlichung für Ihre Web-App oben erstellt haben.

	Eine Ausgabe ähnlich der folgenden wird angezeigt. Während der Bereitstellung lädt Azure alle npm-Module herunter.

		Counting objects: 17, done.
		Delta compression using up to 8 threads.
		Compressing objects: 100% (13/13), done.
		Writing objects: 100% (17/17), 3.21 KiB, done.
		Total 17 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id 'ef276f3042'.
		remote: Preparing files for deployment.
		remote: Running NPM.
		...
		remote: Deploying Web.config to enable Node.js activation.
		remote: Deployment successful.
		To https://username@mongodbtasklist.azurewebsites.net/MongoDBTasklist.git
 		 * [new branch]      master -> master

Sie sind fast fertig!

### Konfigurieren der Umgebung
Erinnern Sie sich an "process.env.CUSTOMCONNSTR_MONGOLAB_URI" im Code? Wir möchten diese Umgebungsvariable mit dem Wert auffüllen, der Azure während der MongoLab-Datenbankbereitstellung bereitgestellt wird.

#### Abrufen der MongoLab-Verbindungszeichenfolge

[AZURE.INCLUDE [howto-get-connectioninfo-mongolab](../../includes/howto-get-connectioninfo-mongolab.md)]

#### Hinzufügen der Verbindungszeichenfolge zu den Umgebungsvariablen der Web-App

[AZURE.INCLUDE [howto-save-connectioninfo-mongolab](../../includes/howto-save-connectioninfo-mongolab.md)]

## Erfolg!

Führen Sie `azure site browse` in Ihrem Projektverzeichnis aus, um automatisch einen Browser zu öffnen, oder öffnen Sie einen Browser, und navigieren Sie manuell zur URL Ihrer Web-App ("myuniqueerbappname.azurewebsites.net"):

![Eine Webseite mit einer leeren Aufgabenliste][node-mongo-finished]

<a name="manage"></a>
## Verwalten der Datenbank

[AZURE.INCLUDE [howto-access-mongolab-ui](../../includes/howto-access-mongolab-ui.md)]

Glückwunsch! Sie haben gerade eine "Node.js"-Anwendung mit einer von MongoLab gehosteten MongoDB-Datenbank gestartet! Das Sie jetzt eine MongoLab-Datenbank haben, können Sie sich mit allen Fragen oder Bedenken bezüglich Ihrer Datenbank sowie mit Hilfeanfragen bezüglich MongoDB oder dem Node-Treiber selbst an [support@mongolab.com](mailto:support@mongolab.com) wenden. Viel Glück!

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.


[screen-mongolab-websitedashboard]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-websitedashboard.png
[screen-mongolab-newwebsite]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-newwebsite.png
[button-new]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-new.png
[button-store]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-store.png
[entry-mongolab]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/entry-mongolab.png
[button-connectioninfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-connectioninfo.png
[screen-connectioninfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/dialog-mongolab_connectioninfo.png
[focus-website-connectinfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
[provision]: #provision
[create]: #create
[deploy]: #deploy
[manage]: #manage
[Node.js]: http://nodejs.org
[MongoDB]: http://www.mongodb.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[Mongoose]: http://mongoosejs.com
[for free]: /pricing/free-trial
[Git-Remote]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Azure CLI]: ../virtual-machines-command-line-tools.md
[Azure Developer Center]: /develop/nodejs/
[Create and deploy a Node.js application to Azure Web Sites]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Continuous deployment using GIT in Azure App Service]: web-sites-publish-source-control.md
[MongoLab]: http://mongolab.com
[Node.js Web Application with Storage on MongoDB (Virtual Machine)]: /develop/nodejs/tutorials/website-with-mongodb-(mac)/
[node-mongo-finished]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/todo_list_noframe.png
[node-mongo-express-results]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/express_output.png
[download-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azure-account-download-cli.png
[import-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azureimport.png
[mongolab-create]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-create.png
[mongolab-view]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-view.png
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->