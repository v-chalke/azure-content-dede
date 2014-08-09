<properties linkid="develop-php-website-with-sql-database-and-git" urlDisplayName="Web w/ SQL + Git" pageTitle="PHP web site with SQL Database and Git - Azure tutorial" metaKeywords="" description="A tutorial that demonstrates how to create a PHP web site that stores data in SQL Database and use Git deployment to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter="PHP" title="Create a PHP web site with a SQL Database and deploy using Git" authors="waltpo" solutions="" manager="" editor="mollybos" />

Erstellen einer PHP-Website mit einer SQL-Datenbank und Bereitstellen mit Git
=============================================================================

In diesem Lernprogramm erfahren Sie, wie Sie eine PHP-Azure-Website mit einer Azure-SQL-Datenbank erstellen und über Git bereitstellen. Dieses Lernprogramm setzt voraus, dass auf dem Computer [PHP](http://www.php.net/manual/en/install.php), [SQL Server Express](http://www.microsoft.com/en-us/download/details.aspx?id=29062), die [Microsoft-Treiber für SQL Server für PHP](http://www.microsoft.com/en-us/download/details.aspx?id=20098), ein Webserver und [Git](http://git-scm.com/) installiert sind. Nach der Durchführung dieses Lernprogramms haben Sie eine in Azure ausgeführte PHP-SQL-Datenbank.

> [WACOM.NOTE] Sie können PHP, SQL Server Express, die Microsoft-Treiber für SQL Server für PHP und Internet Information Services (IIS) mit dem [Microsoft-Webplattform-Installer](http://www.microsoft.com/web/downloads/platform.aspx) installieren und konfigurieren.

Sie erhalten Informationen zu folgenden Themen:

-   Erstellen einer Azure-Website und einer SQL-Datenbank über das Azure-Verwaltungsportal. Da PHP standardmäßig auf Azure-Websites aktiviert ist, gelten für die Ausführung Ihres PHP-Codes keine besonderen Voraussetzungen.
-   Veröffentlichen und erneutes Veröffentlichen Ihrer Anwendung mithilfe von Git in Azure

Mithilfe dieses Lernprogramms erstellen Sie eine einfache Webanwendung für die Registrierung in PHP. Die Anwendung wird auf einer Azure-Website gehostet. Unten finden Sie einen Screenshot der vollständigen Anwendung:

![Azure-PHP-Website](./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Erstellen einer Azure-Website und Einrichten der Git-Veröffentlichung
---------------------------------------------------------------------

Führen Sie die folgenden Schritte aus, um eine Azure-Website und eine SQL-Datenbank zu erstellen:

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an.
2.  Klicken Sie unten links im Portal auf das Symbol **Neu**.

	![Neue Azure-Website erstellen](./media/web-sites-php-sql-database-deploy-use-git/new_website.jpg)

3.  Klicken Sie auf **Website** und dann auf **Custom Create**.

    ![Neue Website benutzerdefiniert erstellen](./media/web-sites-php-sql-database-deploy-use-git/custom_create.png)

    Geben Sie einen Wert für **URL** ein, wählen Sie die Option zum Erstellen einer neuen SQL-Datenbank**** aus der Dropdown-Liste **Datenbank** aus, und wählen Sie das Datencenter für Ihre Website aus der Dropdown-Liste **Region** aus. Klicken Sie unten im Dialogfeld auf den Pfeil.

    ![Websitedetails eingeben](./media/web-sites-php-sql-database-deploy-use-git/website_details_sqlazure.jpg)

4.  Geben Sie im Feld **Name** einen Namen für Ihre Datenbank ein, wählen Sie die **Edition** [(WEB oder BUSINESS)](http://msdn.microsoft.com/de-de/library/windowsazure/ee621788.aspx), die **Maximale Größe** für Ihre Datenbank, die **Sortierung** und anschließend **Neuer SQL Datenbankserver**. Klicken Sie unten im Dialogfeld auf den Pfeil.

    ![SQL-Datenbankeinstellungen eingeben](./media/web-sites-php-sql-database-deploy-use-git/database_settings.jpg)

5.  Geben Sie einen Administratornamen und ein Kennwort ein (bestätigen Sie das Kennwort anschließend), wählen Sie die Region, in der Ihr neuer SQL-Datenbankserver erstellt werden soll, und aktivieren Sie das Kontrollkästchen `Allow Azure Services to access the server`.

    ![Einen neuen SQL-Datenbankserver erstellen](./media/web-sites-php-sql-database-deploy-use-git/create_server.jpg)

    Wenn die Website erstellt wurde, wird der Text **Creation of Web Site "[SITENAME]" completed successfully** angezeigt. Nun können Sie die Git-Veröffentlichung aktivieren.

6.  Klicken Sie auf den Namen der Website, der in der Liste der Websites aufgeführt ist, um das Schnellstart-Dashboard der Website zu öffnen.

    ![Website-Dashboard öffnen](./media/web-sites-php-sql-database-deploy-use-git/go_to_dashboard.png)

7.  Klicken Sie unten auf der Schnellstart-Seite auf **Set up deployment from source control**.

    ![Einrichten der Git-Veröffentlichung](./media/web-sites-php-sql-database-deploy-use-git/setup_git_publishing.png)

8.  Wählen Sie bei der Frage „Wo ist Ihr Quellcode?“ **Local Git repository** aus, und klicken Sie dann auf den Pfeil.

    ![Wo ist Ihr Quellcode](./media/web-sites-php-sql-database-deploy-use-git/where_is_code.png)

9.  Um die Git-Veröffentlichung zu aktivieren, müssen Sie einen Benutzernamen und ein Kennwort angeben. Notieren Sie sich den erstellten Benutzernamen und das Kennwort. (Wenn Sie zuvor bereits ein Git-Repository eingerichtet haben, wird dieser Schritt übersprungen.)

    ![Anmeldedaten für die Veröffentlichung erstellen](./media/web-sites-php-sql-database-deploy-use-git/git-deployment-credentials.png)

    Das Einrichten Ihres Repositorys nimmt einige Sekunden in Anspruch.

10. Wenn Ihr Repository bereit ist, werden Anweisungen zum Pushen Ihrer Anwendungsdateien zum Repository angezeigt. Notieren Sie diese Anweisungen, da sie später benötigt werden.

    ![Git-Anweisungen](./media/web-sites-php-sql-database-deploy-use-git/git-instructions.png)

Abrufen von SQL-Datenbank-Verbindungsinformationen
--------------------------------------------------

Um eine Verbindung zur auf Azure-Websites laufenden SQL-Datenbankinstanz herzustellen, benötigen Sie die Verbindungsinformationen. Um die SQL-Datenbankverbindungsinformationen abzurufen, führen Sie die folgenden Schritte durch:

1.  Klicken Sie im Azure-Verwaltungsportal auf **Verknüpfte Ressourcen**, und klicken Sie dann auf den Datenbanknamen.

    ![Verknüpfte Ressourcen](./media/web-sites-php-sql-database-deploy-use-git/linked_resources.jpg)

2.  Klicken Sie auf **View connection strings**.

    ![Verbindungszeichenfolge](./media/web-sites-php-sql-database-deploy-use-git/connection_string.jpg)

3.  Notieren Sie sich im Abschnitt **PHP** des folgenden Dialogfelds die Werte für `Server`, `DATABASE` und `USERNAME`.

Lokales Erstellen und Testen der Anwendung
------------------------------------------

Die Registrierungsanwendung ist eine einfache PHP-Anwendung, die Ihnen die Registrierung für eine Veranstaltung durch Angabe Ihres Namens und Ihrer E-Mail-Adresse ermöglicht. Informationen zu vorherigen Registranten werden in einer Tabelle angezeigt. Registrierungsinformationen werden in einer SQL-Datenbank gespeichert. Die Anwendung besteht aus zwei Dateien (unten stehenden Code kopieren/einfügen):

-   **index.php**: Zeigt ein Registrierungsformular und eine Tabelle mit Registranteninformationen an.
-   **createtable.php**: Erstellt die SQL-Datenbanktabelle für die Anwendung. Diese Datei wird nur einmal verwendet.

Gehen Sie wie unten beschrieben vor, um die Anwendung lokal zu erstellen und auszuführen. Beachten Sie, dass Voraussetzung für diese Schritte ist, dass PHP, SQL Server Express und ein Webserver auf Ihrem lokalen Computer eingerichtet sind und die [PDO-Erweiterung für SQL-Server](http://php.net/pdo_sqlsrv) aktiviert ist.

1.  Erstellen Sie eine SQL-Server-Datenbank namens `registration`. Sie können dies in der `sqlcmd`-Eingabeaufforderung mit diesen Befehlen ausführen:

         >sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
         1> create database registration
         2> GO   

2.  Erstellen Sie im Stammverzeichnis Ihres Webservers einen Ordner mit dem Namen `registration`, und erstellen Sie darin zwei Dateien mit den Namen `createtable.php` und `index.php`.

3.  Öffnen Sie die Datei `createtable.php` in einem Texteditor oder IDE, und fügen Sie den folgenden Code hinzu. Dieser Code wird verwendet, um die Tabelle `registration_tbl` in der Datenbank `registration` zu erstellen.

         <
         php
         // DB connection info
         $host = "localhost\sqlexpress";
         $user = "user name";
         $pwd = "password";
         $db = "registration";
         try{
             $conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
             $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
             $sql = "CREATE TABLE registration_tbl(
             id INT NOT NULL IDENTITY(1,1) 
             PRIMARY KEY(id),
             name VARCHAR(30),
             email VARCHAR(30),
             date DATE)";
             $conn->query($sql);
         }
         catch(Exception $e){
             die(print_r($e));
         }
         echo "<h3>Table created.</h3>";
         
         >

    Beachen Sie. dass Sie die Werte für `$user` und `$pwd` mit Ihrem lokalen SQL-Server-Benutzernamen und -Kennwort aktualisieren.

4.  Öffnen Sie einen Webbrowser und navigieren Sie zu **http://localhost/registration/createtable.php**. Dadurch wird die Tabelle `registration_tbl` in der Datenbank erstellt.

5.  Öffnen Sie die Datei **index.php** in einem Texteditor oder IDE, und fügen Sie den Basis-HTML- und CSS-Code für die Seite hinzu (der PHP-Code wird in einem späteren Schritt hinzugefügt).

         <html>
         <head>
         <Title>Registration Form</Title>
         <style type="text/css">
             body { background-color: #fff; border-top: solid 10px #000;
                 color: #333; font-size: .85em; margin: 20; padding: 20;
                 font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
             }
             h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
             h1 { font-size: 2em; }
             h2 { font-size: 1.75em; }
             h3 { font-size: 1.2em; }
             table { margin-top: 0.75em; }
             th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
             td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
         </style>
         </head>
         <body>
         <h1>Register here!</h1>
         <p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
         <form method="post" action="index.php" enctype="multipart/form-data" >
               Name  <input type="text" name="name" id="name"/></br>
               Email <input type="text" name="email" id="email"/></br>
               <input type="submit" name="submit" value="Submit" />
         </form>
         <
         php

         
         >
         </body>
         </html>

6.  Fügen Sie innerhalb der PHP-Tags PHP-Code für die Verbindung zur Datenbank hinzu.

         // DB connection info
         $host = "localhost\sqlexpress";
         $user = "user name";
         $pwd = "password";
         $db = "registration";
         // Connect to database.
         try {
             $conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
             $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
         }
         catch(Exception $e){
             die(var_dump($e));
         }

    Auch hier müssen Sie die Werte für `$user` und `$pwd` durch Ihren lokalen MySQL-Benutzernamen und das Kennwort aktualisieren.

7.  Fügen Sie nach dem Datenbankverbindungscode den Code für die Eingabe der Registrierungsinformationen in die Datenbank hinzu.

         if(!empty($_POST)) {
         try {
             $name = $_POST['name'];
             $email = $_POST['email'];
             $date = date("Y-m-d");
             // Insert data
             $sql_insert = "INSERT INTO registration_tbl (name, email, date) 
                            VALUES (
         ,
         ,
         )";
             $stmt = $conn->prepare($sql_insert);
             $stmt->bindValue(1, $name);
             $stmt->bindValue(2, $email);
             $stmt->bindValue(3, $date);
             $stmt->execute();
         }
         catch(Exception $e){
             die(var_dump($e));
         }
         echo "<h3>Your're registered!</h3>";
         }

8.  Fügen Sie nach dem obigen Code den Code für das Abrufen der Daten von der Datenbank hinzu.

         $sql_select = "SELECT * FROM registration_tbl";
         $stmt = $conn->query($sql_select);
         $registrants = $stmt->fetchAll(); 
         if(count($registrants) > 0) {
             echo "<h2>People who are registered:</h2>";
             echo "<table>";
             echo "<tr><th>Name</th>";
             echo "<th>Email</th>";
             echo "<th>Date</th></tr>";
             foreach($registrants as $registrant) {
                 echo "<tr><td>".$registrant['name']."</td>";
                 echo "<td>".$registrant['email']."</td>";
                 echo "<td>".$registrant['date']."</td></tr>";
             }
            echo "</table>";
         } else {
             echo "<h3>No one is currently registered.</h3>";
         }

Nun können Sie **http://localhost/registration/index.php** aufrufen, um die Anwendung zu testen.

Veröffentlichen der Anwendung
-----------------------------

Nachdem Sie Ihre Anwendung lokal getestet haben, können Sie sie über Git auf ihrer Azure-Website veröffentlichen. Sie müssen jedoch zuerst die Datenbankverbindungsinformationen in der Anwendung aktualisieren. Aktualisieren Sie mithilfe der Datenbankverbindungsinformationen, die Sie zuvor erhalten haben (im Abschnitt **Abrufen von SQL-Datenbank-Verbindungsinformationen**), folgende Informationen in **beiden** Dateien `createdatabase.php` und `index.php` mit den entsprechenden Werten:

     // DB connection info
    $host = "tcp:<value of SERVER>";
    $user = "<value of USERNAME>@<server ID>";
    $pwd = "<your password>";
    $db = "<value of DATABASE>";

> [WACOM.NOTE] In `$host` muss dem Wert SERVER `tcp:` vorangestellt werden. Der Wert von `$user` ist die Verkettung des Werts von USERNAME, "@" und Ihrer Server-ID. Ihre Server-ID sind die ersten zehn Zeichen des Werts SERVER.

Sie können nun die Git-Veröffentlichung einrichten und die Anwendung veröffentlichen.

> [WACOM.NOTE] Dies sind dieselben Schritte, die am Ende des oben aufgeführten Abschnitts "Erstellen einer Azure-Website und Einrichten der Git-Veröffentlichung" aufgeführt sind.

1.  Öffnen Sie GitBash (oder eine Terminalsitzung, wenn sich Git in Ihrem `PATH` befindet), ändern Sie die Verzeichnisse zum Stammverzeichnis Ihrer Anwendung, und führen Sie die folgenden Befehle aus:

        git init
         git add .
         git commit -m "initial commit"
         git remote add azure [URL for remote repository]
         git push azure master

    Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben.

2.  Wechseln Sie zu **http://[Websitename].azurewebsites.net/createtable.php**, um die MySQL-Tabelle für die Anwendung zu erstellen.
3.  Wechseln Sie zu **http://[Websitename].azurewebsites.net/index.php**, um die Anwendung zu verwenden.

Nach Veröffentlichung Ihrer Anwendung können Sie Änderungen an ihr vornehmen und diese über Git veröffentlichen.

Veröffentlichen von Änderungen an der Anwendung
-----------------------------------------------

Befolgen Sie die folgenden Schritte, um Änderungen an der Anwendung zu veröffentlichen:

1.  Nehmen Sie lokal Änderungen an Ihrer Anwendung vor.
2.  Öffnen Sie GitBash (oder eine Terminalsitzung, wenn sich Git in Ihrem `PATH` befindet), ändern Sie die Verzeichnisse zum Stammverzeichnis Ihrer Anwendung, und führen Sie die folgenden Befehle aus:

         git add .
         git commit -m "comment describing changes"
         git push azure master

    Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben.

3.  Wechseln Sie zu **http://[Websitename].azurewebsites.net/index.php**, um die Änderungen anzuzeigen.
