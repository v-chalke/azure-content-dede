<properties 
	pageTitle="Erstellen einer ASP.NET MVC-App mit Authentifizierung, SQL-Datenbank und Bereitstellung in Azure App Service" 
	description="Erfahren Sie, wie Sie eine ASP.NET-MVC 5-App mit einem SQL-Datenbank-Back-End und Authentifizierung sowie Autorisierung entwickeln und in Azure bereitstellen." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="Rick-Anderson" 
	writer="Rick-Anderson" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/06/2015" 
	ms.author="riande"/>



# Erstellen einer ASP.NET MVC-App mit Authentifizierung, SQL-Datenbank und Bereitstellung in Azure App Service

In diesem Lernprogramm wird die Erstellung einer sicheren ASP.NET MVC 5-Webanwendung erläutert, mit der Benutzer sich mithilfe der Anmeldeinformationen von Facebook oder Google anmelden können. Darüber hinaus wird die Anwendung in [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) bereitgestellt.

Sie können ein kostenloses Azure-Konto erstellen. Wenn Sie noch nicht über Visual Studio 2013 verfügen, wird Visual Studio 2013 for Web Express automatisch durch das SDK installiert. Sie können kostenlos mit der Entwicklung für Azure beginnen.

Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Nach Abschluss dieses Lernprogramms verfügen Sie über eine sichere datengesteuerte Webanwendung, die unter Rückgriff auf eine Clouddatenbank in der Cloud ausgeführt wird.

Sie lernen Folgendes:

* Erstellen eines sicheren ASP.NET MVC 5-Projekts und Veröffentlichen dieses Projekts in einer [App Service-Web-App](http://go.microsoft.com/fwlink/?LinkId=529714) in Azure App Service
* Verwenden von [OAuth](http://oauth.net/ "http://oauth.net/") und der ASP.NET-Mitgliedschaftsdatenbank zum Sichern der Anwendung
* Verwenden einer SQL-Datenbank zum Speichern von Daten in Azure

Sie erstellen eine einfache Kontaktlisten-Webanwendung, die auf ASP.NET MVC 5 beruht und für den Datenbankzugriff ADO.NET Entity Framework verwendet. In der folgenden Abbildung wird die Anmeldeseite der fertiggestellten Anwendung dargestellt:

![Anmeldeseite][rxb]

>[AZURE.NOTE]Sie benötigen ein Microsoft Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie kein Konto haben, können Sie <a href="/de-de/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">Ihre MSDN-Abonnentenvorteile aktivieren</a> oder <a href="/de-de/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">sich für eine kostenlose Testversion registrieren</a>.

>Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Zur Einrichtung Ihrer Entwicklungsumgebung müssen Sie [Visual Studio 2013 Update 4](http://go.microsoft.com/fwlink/?LinkId=390521) oder höher und die neueste Version von [Azure SDK für Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) installieren. Dieser Artikel wurde für Visual Studio Update 4 und SDK 2.5.1 geschrieben.

## Erstellen einer ASP.NET MVC 5-Anwendung

### Erstellen des Projekts

1. Aus der **Datei** Menü klicken Sie auf **Neues Projekt**.

	!["Neues Projekt" im Menü "Datei"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/gs13newproj.png)

1. In der **Neues Projekt** Dialogfeld erweitern Sie **C#-** und wählen Sie **Web** unter **Installierte Vorlagen**, und wählen Sie dann **ASP.NET-Webanwendung**.

1. Nennen Sie die Anwendung **ContactManager** und klicken Sie auf **OK**.

	![Dialogfeld "Neues Projekt"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13newprojdb.png)
 
	**Hinweis:** Stellen Sie sicher, dass Sie "ContactManager" eingeben. In den Codeblöcken, die Sie später kopieren, wird davon ausgegangen, dass der Projektname "ContactManager" lautet.

1. In der **Neues ASP.NET-Projekt** Wählen Sie im Dialogfeld die **MVC** Vorlage. Überprüfen Sie **Authentifizierung** festgelegt ist, um **einzelne Benutzerkonten**, **Host in der Cloud** aktiviert ist und **Web App** ausgewählt ist.

	![Dialogfeld "Neues ASP.NET-Projekt"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newproject.png)

1. Der Konfigurationsassistent schlägt basierend auf *ContactManager* einen eindeutigen Namen vor. Sie müssen sich entscheiden, ob Sie einen neuen App Service-Plan und eine neue Ressourcengruppe erstellen. Entscheidungshilfen hierzu finden Sie unter [Azure App Service-Pläne – Detaillierte Übersicht](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Für dieses Lernprogramm empfiehlt es sich, mindestens eine neue Ressourcengruppe zu erstellen. Wählen Sie eine Region in Ihrer Nähe. Über [azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com") können Sie das Rechenzentrum mit der niedrigsten Latenz suchen. Richten Sie die Datenbank im nächsten Schritt damit nicht klicken **OK** noch.

   ![Neue Plan- und Ressourcengruppe](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newplanandgroup.png)
 
2. Wenn Sie einen Datenbankserver vor erstellt haben, wählen Sie **Erstellen neuer Server**, geben Sie einen Datenbanknamen, Benutzernamen und Kennwort.

   ![Verwenden einer neuen Datenbank](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newdb.png)

3. Wenn Sie über einen Datenbankserver verfügen, verwenden Sie diesen, um eine neue Datenbank zu erstellen. Datenbankserver sind wertvolle Ressourcen, und Sie möchten in der Regel zwecks Test und Entwicklung mehrere Datenbanken auf demselben Server erstellen, anstatt einen Datenbankserver pro Datenbank zu erstellen. Vergewissern Sie sich, dass sich Web-App und Datenbank in derselben Region befinden.

    ![Verwenden einer bestehenden Datenbank](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/useexistingdb.png)

### Einrichten der Seitenkopf- und -fußzeile


1. In **Projektmappen-Explorer** Öffnen Sie die *Layout.cshtml* Datei der *Views\\Shared* Ordner.

	![_Layout.cshtml im Projektmappen-Explorer][newapp004]

1. Ersetzen Sie das Markup in der Datei *Layout.cshtml* durch den folgenden Code. Die Änderungen sind unten hervorgehoben.

<pre>
			&lt;!DOCTYPE html>
			&lt;html>
			&lt;head>
			    &lt;meta charset="utf-8" />
			    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0">
			    &lt;title>@ViewBag.Title - <mark>Contact Manager</mark>&lt;/title>
			    @Styles.Render("~/Content/css")
			    @Scripts.Render("~/bundles/modernizr")
			
			&lt;/head>
			&lt;body>
			    &lt;div class="navbar navbar-inverse navbar-fixed-top">
			        &lt;div class="container">
			            &lt;div class="navbar-header">
			                &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
			                    &lt;span class="icon-bar">&lt;/span>
			                    &lt;span class="icon-bar">&lt;/span>
			                    &lt;span class="icon-bar">&lt;/span>
			                &lt;/button>
			                @Html.ActionLink("<mark>CM Demo</mark>", "Index", "<mark>Cm</mark>", new { area = "" }, new { @class = "navbar-brand" })
			            &lt;/div>
			            &lt;div class="navbar-collapse collapse">
			                &lt;ul class="nav navbar-nav">
			                    &lt;li>@Html.ActionLink("Home", "Index", "Home")&lt;/li>
			                    &lt;li>@Html.ActionLink("About", "About", "Home")&lt;/li>
			                    &lt;li>@Html.ActionLink("Contact", "Contact", "Home")&lt;/li>
			                &lt;/ul>
			                @Html.Partial("_LoginPartial")
			            &lt;/div>
			        &lt;/div>
			    &lt;/div>
			    &lt;div class="container body-content">
			        @RenderBody()
			        &lt;hr />
			        &lt;footer>
			            &lt;p>&amp;copy; @DateTime.Now.Year - <mark>Contact Manager</mark>&lt;/p>
			        &lt;/footer>
			    &lt;/div>
			
			    @Scripts.Render("~/bundles/jquery")
			    @Scripts.Render("~/bundles/bootstrap")
			    @RenderSection("scripts", required: false)
			&lt;/body>
			&lt;/html>
</pre>

### Lokales Ausführen der Anwendung

1. Drücken Sie STRG+F5, um die Anwendung auszuführen.

	Die Startseite der Anwendung wird im Standardbrowser angezeigt.

	![Lokal ausgeführte Web-App](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr2.png)

Darüber hinaus müssen Sie keine weiteren Aktionen ausführen, um die Anwendung zu erstellen, die Sie für Azure bereitstellen.

## Aktivieren von SSL für das Projekt ##

1. Aktivieren Sie SSL. Klicken Sie im Projektmappen-Explorer auf die **ContactManager** Projekt, und klicken Sie auf F4, um das Dialogfeld "Eigenschaften" zu öffnen. Änderung **SSL aktiviert** auf "true". Kopieren der **SSL-URL**. Die SSL-URL lautet https://localhost:44300/, sofern Sie zuvor noch keine SSL-Websites erstellt haben.

	![SSL aktivieren][rxSSL]
 
1. Klicken Sie im Projektmappen-Explorer rechts auf der **Contact Manager** Projekt, und klicken Sie auf **Eigenschaften**.
1. Klicken Sie auf der linken Registerkarte auf **Web**.
1. Ändern der **Projekt-Url** verwenden die **SSL-URL** und speichern Sie die Seite (STRG + S).

	![SSL aktivieren](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr1.png)
 
1. Stellen Sie sicher, dass Internet Explorer von Visual Studio gestartet wird, wie im folgenden Bild dargestellt:

	![Standardbrowser](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss12.PNG)

	In der Browserauswahl können Sie den Browser angeben, der von Visual Studio gestartet wird.

 	![Browserauswahl](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss13.png)

	Sie können mehrere Browser auswählen und die Browser von Visual Studio aktualisieren lassen, wenn Sie Änderungen vornehmen. Weitere Informationen finden Sie unter [Using Browser Link in Visual Studio 2013](http://www.asp.net/visual-studio/overview/2013/using-browser-link) (in englischer Sprache).


1. Drücken Sie STRG+F5, um die Anwendung auszuführen. Befolgen Sie die Anweisungen, um das von IIS Express generierte selbstsignierte Zertifikat als vertrauenswürdig einzustufen.

	 ![Anweisungen, um das von IIS Express generierte selbstsignierte Zertifikat als vertrauenswürdig einzustufen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss26.PNG)

1. Lesen der **Sicherheitswarnung** Dialogfeld, und klicken Sie dann auf **Ja** Wenn Sie das Zertifikat darstellt installieren möchten **Localhost**.

 	![IIS Express-Zertifikatwarnung für Localhost](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss27.PNG)

1. Die Seite *Home* wird in IE angezeigt, und es gibt keine SSL-Warnungen.

	 ![IE mit Localhost-SSL, keine Warnungen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss28.PNG)

	Google Chrome akzeptiert das Zertifikat ebenfalls und zeigt HTTPS-Inhalt ohne Warnung an. Firefox verwendet einen eigenen Zertifikatspeicher, deshalb wird eine Warnung angezeigt.

	 ![Firefox-Zertifikatwarnung](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss30.PNG)

## Bereitstellen der Anwendung in Azure

1. Visual Studio mit der rechten Maustaste des Projekts im **Projektmappen-Explorer** und wählen Sie **Veröffentlichen** aus dem Kontextmenü.

	!["Veröffentlichen" im Kontextmenü des Projekts](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
	
   Der **Web veröffentlichen** -Assistent wird geöffnet.

1. In der **Web veröffentlichen** (Dialogfeld), klicken Sie auf **Veröffentlichen**.

	![Veröffentlichen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr3.png)

	Die erstellte Anwendung wird nun in der Cloud ausgeführt. Bei der nächsten Bereitstellung der Anwendung werden nur die geänderten (oder neuen) Dateien bereitgestellt.

	![In Cloud ausgeführt](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss4.PNG)

## Hinzufügen einer Datenbank zur Anwendung

Als Nächstes aktualisieren Sie die App, um Funktionen zum Anzeigen und Aktualisieren von Kontakten sowie zum Speichern der Daten in einer Datenbank hinzuzufügen. Die App verwendet das Entity Framework (EF), um die Datenbank zu erstellen und Daten zu lesen und zu aktualisieren.

### Hinzufügen von Datenmodellklassen für die Kontakte

Sie beginnen mit der Erstellung eines einfachen Datenmodells in Code.

1. In **Projektmappen-Explorer**, mit der rechten Maustaste auf den Ordner Models, klicken Sie auf **Hinzufügen**, und klicken Sie dann **Klasse**.

	![Klasse hinzufügen im Kontextmenü des Ordners "Models"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr5.png)

2. In der **Neues Element hinzufügen** (Dialogfeld), Name der neuen Klassendatei *Contact.cs*, und klicken Sie dann auf **Hinzufügen**.

	![Dialogfeld "Neues Element hinzufügen"][adddb002]

3. Ersetzen Sie den Inhalt der Datei Contacts.cs durch den folgenden Code.

        using System.ComponentModel.DataAnnotations;
        using System.Globalization;
        namespace ContactManager.Models
        {
            public class Contact
            {
                public int ContactId { get; set; }
                public string Name { get; set; }
                public string Address { get; set; }
                public string City { get; set; }
                public string State { get; set; }
                public string Zip { get; set; }
                [DataType(DataType.EmailAddress)]
                public string Email { get; set; }
            }
        }
Die **Kontakte** -Klasse definiert die Daten, die Sie für jeden Kontakt sowie ein Primärschlüssel, speichert *ContactID*, d. h. von der Datenbank benötigt.

### Erstellen von Webseiten, die Anwendungsbenutzern das Arbeiten mit den Kontakten ermöglichen

Mit dem ASP.NET MVC-Gerüstfeature kann automatisch Code generiert werden, der Erstellungs-, Lese-, Aktualisierungs- und Löschaktionen (CRUD) durchführt.

## Hinzufügen eines Controllers und einer Ansicht für die Daten

1. Erstellen Sie das Projekt **(STRG + UMSCHALT + B)**. (Sie müssen das Projekt vor der Verwendung des Gerüstmechanismus erstellen.) 
1. In **Projektmappen-Explorer**, mit der rechten Maustaste auf den Ordner Controller, und klicken Sie auf **Hinzufügen**, und klicken Sie dann auf **Controller**.

	![Controller hinzufügen im Kontextmenü des Ordners "Controllers"][addcode001]

5. In der **Add Scaffold** Wählen Sie im Dialogfeld **MVC 5-Controller mit Ansichten, mit EF** und klicken Sie dann auf **Hinzufügen**.
	
	![Dialogfeld "Add Scaffold"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr6.png)


1. In der **Model Class** Wählen Sie im Dropdown-Liste **Contact (ContactManager.Models)**. (Siehe Abbildung unten.)
1. In der **Datenkontextklasse**, Option **ApplicationDbContext (ContactManager.Models)**. Die **ApplicationDbContext** für die Mitgliedschaftsdatenbank und die Kontaktdaten verwendet werden.
1. In der **Controllernamen** Texteingabe Geben Sie "CmController" für den Namen des Controllers. 

	![Kontextdialogfeld "Neue Daten"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss5.PNG)

1. Klicken Sie auf **Hinzufügen**.

   Visual Studio erstellt eine Controllermethoden und Ansichten für CRUD-Datenbankvorgänge für **Kontakt** Objekte.

## Aktivieren von Migrationen, Erstellen der Datenbank, Hinzufügen von Beispieldaten und eines Dateninitialisierers ##

Die nächste Aufgabe besteht darin, das Feature [Code First-Migrationen](http://msdn.microsoft.com/library/hh770484.aspx) zu aktivieren, um die Datenbank basierend auf dem erstellten Datenmodell zu erstellen.

1. In der **Tools** Klicken Sie im Menü **NuGet Package Manager** und **-Paket-Manager-Konsole**. !["Paket-Manager-Konsole" im Menü "Extras"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/SS6.png)

2. In der **-Paket-Manager-Konsole** Fenster, geben Sie den folgenden Befehl aus:

		enable-migrations
	Die **Enable-Migrations-** Befehl erstellt eine *Migrationen* Ordner und dessen setzt in diesen Ordner ein *Configuration.cs* -Datei, die Sie bearbeiten können, um Startwerte für die Datenbank und Migrationen zu konfigurieren.

2. In der **-Paket-Manager-Konsole** Fenster, geben Sie den folgenden Befehl aus:

		add-migration Initial


	Die **Hinzufügen-Migration Initial** Befehl generiert eine Datei namens **& Lt; Zeitstempel & Gt; der erste** in der *Migrationen* Ordner, der die Datenbank erstellt. Der erste Parameter ( **anfängliche** ) ist willkürlich und wird verwendet, um den Namen der Datei zu erstellen. Sehen Sie in die neuen Klassendateien **Projektmappen-Explorer**. In der **anfängliche** -Klasse, die **von** -Methode erstellt die Contacts-Tabelle und die **nach unten** Methode (wird verwendet, wenn Sie zum vorherigen Status zurückkehren möchten) gelöscht.
3. Öffnen Sie die Datei *Migrations\\Configuration.cs*. 
4. Fügen Sie den folgenden Namespace hinzu. 

    	 using ContactManager.Models;



5. Ersetzen Sie die *Seed*-Methode durch den folgenden Code:

        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            context.Contacts.AddOrUpdate(p => p.Name,
               new Contact
               {
                   Name = "Debra Garcia",
                   Address = "1234 Main St",
                   City = "Redmond",
                   State = "WA",
                   Zip = "10999",
                   Email = "debra@example.com",
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                }
                );
        }

	Mit diesem Code wird die Datenbank mit den Kontaktinformationen initialisiert (es wird ein Seeding dafür ausgeführt). Weitere Informationen zum Ausführen eines Seedings für die Datenbank finden Sie unter [Seeding and Debugging Entity Framework (EF) DBs](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx) (in englischer Sprache).


6. In der **-Paket-Manager-Konsole** Geben Sie den Befehl:

		update-database

	![Befehle in der Paket-Manager-Konsole][addcode009]

	Die **Update-Database** führt die erste Migration der Datenbank erstellt. Standardmäßig wird die Datenbank als SQL Server Express LocalDB-Datenbank erstellt.

7. Drücken Sie STRG + F5, um die Anwendung auszuführen, und klicken Sie dann auf die **CM Demo** verknüpfen, oder navigieren Sie zu https://localhost:(port#)/Cm.

	In der Anwendung werden die Seeddaten angezeigt und Links zum Bearbeiten und Löschen und zu Details bereitgestellt. Sie können Daten erstellen, bearbeiten, löschen und anzeigen.

	![MVC-Datenansicht][rx2]



## Hinzufügen eines OAuth2-Anbieters

[OAuth](http://oauth.net/ "http://oauth.net/") ist ein offenes Protokoll, das die sichere Autorisierung in einer einfachen und Standardmethode von Web-, Mobil- und Desktopanwendungen ermöglicht. OAuth wird von der ASP.NET MVC-Internetvorlage verwendet, um Facebook, Twitter, Google und Microsoft als Authentifizierungsanbieter verfügbar zu machen. Auch wenn in diesem Lernprogramm nur Google als Authentifizierungsanbieter eingesetzt wird, können Sie den Code problemlos für die Verwendung einer der anderen Anbieter anpassen. Die Schritte zur Implementierung anderer Anbieter unterscheiden sich kaum von den Schritten in diesem Lernprogramm. Informationen zum Verwenden von Facebook als Authentifizierungsanbieter finden Sie im Lernprogramm [MVC 5 App with Facebook, Twitter, LinkedIn and Google OAuth2 Sign-on](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on) (in englischer Sprache).

Abgesehen von der Authentifizierung werden in diesem Lernprogramm auch Rollen verwendet, um die Autorisierung zu implementieren. Nur Benutzer, die Sie der Rolle *canEdit* hinzufügen, können Daten ändern (d. h. Kontakte erstellen, bearbeiten oder löschen).

Folgen Sie den Anweisungen im Lernprogramm [MVC 5 App with Facebook, Twitter, LinkedIn und Google OAuth2 anmelden ](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog) unter **Erstellen einer Google-app für OAuth-2 zum Einrichten einer Google-app für OAuth2**. Führen Sie die App aus, und testen Sie, ob Sie sich mit der Google-Authentifizierung anmelden können.

## Verwenden der Mitgliedschafts-API
In diesem Abschnitt fügen Sie der Mitgliedschaftsdatenbank einen lokalen Benutzer und die Rolle *canEdit* hinzu. Nur Benutzer mit der Rolle *canEdit* können Daten bearbeiten. Eine Best Practice besteht darin, Rollen nach den Aktionen zu benennen, die damit ausgeführt werden können. Entsprechend ist *canEdit* einer Rolle mit dem Namen *admin* vorzuziehen. Mit fortschreitender Fertigstellung der Anwendung können Sie neue Rollen wie *canDeleteMembers* anstelle von weniger aussagekräftigen Rollen wie *superAdmin* hinzufügen.

1. Öffnen Sie die Datei *migrations\\configuration.cs*, und fügen Sie die folgenden `using`-Anweisungen hinzu:

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

1. Fügen Sie die folgenden **AddUserAndRole** -Methode, um die Klasse:

    
         bool AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
         {
            IdentityResult ir;
            var rm = new RoleManager<IdentityRole>
                (new RoleStore<IdentityRole>(context));
            ir = rm.Create(new IdentityRole("canEdit"));
            var um = new UserManager<ApplicationUser>(
                new UserStore<ApplicationUser>(context));
            var user = new ApplicationUser()
            {
               UserName = "user1@contoso.com",
            };
            ir = um.Create(user, "P_assw0rd1");
            if (ir.Succeeded == false)
               return ir.Succeeded;
            ir = um.AddToRole(user.Id, "canEdit");
            return ir.Succeeded;
         }

1. Rufen Sie die neue Methode aus der **Ausgangswert** Methode:
	<pre>
    protected override void Seed(ContactManager.Models.ApplicationDbContext context)
    {
        <mark>AddUserAndRole(context);</mark>
        context.Contacts.AddOrUpdate(p => p.Name,
            // Code aus Platzgründen entfernt
    }
</pre>
<span></span> Das folgende Bild zeigt die Änderungen an *Ausgangswert* Methode:

	![Codebild](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss24.PNG)

   Dieser Code erstellt eine neue Rolle namens *canEdit* sowie den neuen lokalen Benutzer *user1@contoso.com* und fügt *user1@contoso.com* zur Rolle *canEdit* hinzu. Weitere Informationen finden Sie in den [ASP.NET Identity-Lernprogrammen](http://www.asp.net/identity/overview/features-api).

## Verwenden von temporärem Code zum Hinzufügen neuer Benutzer mit Anmeldung zu sozialen Netzwerken zur Rolle "canEdit"  ##
In diesem Abschnitt ändern Sie vorübergehend die **ExternalLoginConfirmation** -Methode im Controller Konto Hinzufügen neuer Benutzer, die mit einer OAuth-Anbieter registrieren der *CanEdit* Rolle. Ändern wir vorübergehend die **ExternalLoginConfirmation** -Methode, um automatisch neue Benutzer einer Administratorrolle hinzufügen. Bis ein Tool zum Hinzufügen und Verwalten von Rollen bereitgestellt wird, verwenden wir den unten dargestellten Code zur temporären automatischen Registrierung. Es ist geplant, in Zukunft ein [WSAT](http://msdn.microsoft.com/library/ms228053.aspx) vergleichbares Tool bereitzustellen, mit dem Benutzerkonten und -rollen erstellt und bearbeitet werden können.

1. Öffnen der **controllers\\accountcontroller** Datei, und navigieren Sie zu den **ExternalLoginConfirmation** Methode.
1. Fügen Sie den folgenden Aufruf **AddToRoleAsync** kurz vor dem **SignInAsync** aufrufen.

                await UserManager.AddToRoleAsync(user.Id, "canEdit");

   Der obige Code fügt den neu registrierten Benutzer zur Rolle "CanEdit" hinzu, die ihm Zugriff auf Aktionsmethoden für die Datenbearbeitung Daten einräumt. <pre> // POST: /Account/ExternalLoginConfirmation [HttpPost][AllowAnonymous] [ValidateAntiForgeryToken] public async Task<ActionResult> ExternalLoginConfirmation(ExternalLoginConfirmationViewModel model, string returnUrl) { if (User.Identity.IsAuthenticated) { return RedirectToAction("Index", "Manage"); } if (ModelState.IsValid) { // Get the information about the user from the external login provider var info = await AuthenticationManager.GetExternalLoginInfoAsync(); if (info == null) { return View("ExternalLoginFailure"); } var user = new ApplicationUser { UserName = model.Email, Email = model.Email }; var result = await UserManager.CreateAsync(user); if (result.Succeeded) { result = await UserManager.AddLoginAsync(user.Id, info.Login); if (result.Succeeded) { <mark>await UserManager.AddToRoleAsync(user.Id, "canEdit");</mark> await SignInManager.SignInAsync(user, isPersistent: false, rememberBrowser: false); return RedirectToLocal(returnUrl); } } AddErrors(result); } ViewBag.ReturnUrl = returnUrl; return View(model); } </pre>

An späterer Stelle in diesem Lernprogramm stellen Sie die Anwendung für Azure bereit. Die Anmeldung erfolgt dann über Google oder einen anderen Authentifizierungsdrittanbieter. Dadurch wird Ihr neu registriertes Konto der Rolle *canEdit* hinzugefügt. Jede Person, die die URL Ihrer Web-App findet und über eine Google-ID verfügt, kann sich dann registrieren und die Datenbank aktualisieren. Sie können die Website beenden, um dies zu verhindern. Durch eine Untersuchung der Datenbank können Sie überprüfen, wer der Rolle *canEdit* angehört.

In der **-Paket-Manager-Konsole** hit-oben-Taste, um Folgendes anzuzeigen:

		Update-Database

Ausführen der **Update-Database** Befehl ausgeführt werden die **Ausgangswert** -Methode führt die **AddUserAndRole** Sie gerade hinzugefügt haben. Die **AddUserAndRole** Erstellen Sie den Benutzer *user1@contoso.com* und die *CanEdit* Rolle.

## Schützen der Anwendung durch SSL und das "Authorize"-Attribut ##

In diesem Abschnitt wenden Sie das [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx)-Attribut an, um den Zugriff auf die Aktionsmethoden einzuschränken. Anonyme Benutzer werden in der Lage, Anzeigen der **Index** Aktion-Methode nur den home-Controller. Registrierten Benutzern werden Daten angezeigt (die **Index** und **Details** Seiten des Controllers Cm), Info- und kontaktseiten. Nur Benutzer mit der Rolle *canEdit* können auf Aktionsmethoden zugreifen, mit denen Daten geändert werden.

1. Fügen Sie der Anwendung die Filter [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) und [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) hinzu. Alternativ können Sie das [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx)-Attribut und das [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx)-Attribut jedem Controller hinzufügen, es wird jedoch als Best Practice im Bereich Sicherheit betrachtet, die Attribute der gesamten Anwendung hinzuzufügen. Auf diese Weise wird jeder neu hinzugefügte Controller und jede neu hinzugefügte Aktionsmethode automatisch geschützt und Sie müssen nicht daran denken, die Attribute anzuwenden. Weitere Informationen finden Sie unter [Securing your ASP.NET MVC App and the new AllowAnonymous Attribute](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) (in englischer Sprache). Öffnen Sie die Datei *App_Start\\FilterConfig.cs*, und ersetzen Sie darin die Methode *RegisterGlobalFilters* durch folgenden Code (der die beiden Filter hinzufügt): <pre> public static void RegisterGlobalFilters(GlobalFilterCollection filters) { filters.Add(new HandleErrorAttribute()); <mark>filters.Add(new System.Web.Mvc.AuthorizeAttribute()); filters.Add(new RequireHttpsAttribute());</mark> } </pre>




	Der im obigen Code angewendete Filter [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) verhindert, dass anonyme Benutzer auf Methoden in der Anwendung zugreifen. Sie verwenden das [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx)-Attribut, um die Autorisierungsanforderung in einigen Methoden zu deaktivieren, sodass anonyme Benutzer sich anmelden und die Startseite anzeigen können. [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) erfordert, dass der gesamte Zugriff auf die Webanwendung durch HTTPS erfolgen muss.

1. Hinzufügen der [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) -Attribut auf die **Index** Methode des Home-Controllers. Mit dem Attribut [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) können Sie Methoden von der Autorisierung ausnehmen. <pre> public class HomeController : Controller { <mark>[AllowAnonymous]</mark> public ActionResult Index() { return View(); } </pre>

2. Führen Sie eine globale Suche nach *AllowAnonymous* durch. Sie werden feststellen, dass es in den Anmelde- und Registrierungsmethoden des Kontocontrollers verwendet wird.
1. Fügen Sie in der Datei *CmController.cs* den Code `[Authorize(Roles = "canEdit")]` zu den HttpGet- und HttpPost-Methoden hinzu, mit denen Daten im Controller *Cm* geändert werden (Erstellen, Bearbeiten, Löschen sowie jede Aktionsmethode abgesehen von Index und Details). Hier ein Ausschnitt des vollständigen Codes: <pre> // GET: Cm/Create <mark>[Authorize(Roles = "canEdit")]</mark> public ActionResult Create() { return View(new Contact { Address = "123 N 456 W", City="Great Falls", Email = "ab@cd.com", Name="Joe Smith", State="MT", Zip = "59405"}); } // POST: Cm/Create // Zum Schutz vor Overposting-Angriffen aktivieren Sie die jeweiligen Bind-Eigenschaften. // Weitere Informationen finden Sie unter http://go.microsoft.com/fwlink/?LinkId=317598. [HttpPost][ValidateAntiForgeryToken] <mark>[Authorize(Roles = "canEdit")]</mark> public ActionResult Create([Bind(Include = "ContactId,Name,Address,City,State,Zip,Email")] Contact contact) { if (ModelState.IsValid) { db.Contacts.Add(contact); db.SaveChanges(); return RedirectToAction("Index"); } return View(contact); } // GET: Cm/Edit/5 <mark>[Authorize(Roles = "canEdit")]</mark> public ActionResult Edit(int? id) { if (id == null) { return new HttpStatusCodeResult(HttpStatusCode.BadRequest); } Contact contact = db.Contacts.Find(id); if (contact == null) { return HttpNotFound(); } return View(contact); } </pre>

1. Wenn Sie immer noch aus einer vorherigen Sitzung angemeldet sind, drücken Sie die **Abmelden** Link.
1. Klicken Sie auf die **über** oder **Kontakt** Links. Sie werden zur Anmeldeseite weitergeleitet, da anonyme Benutzer diese Seiten nicht anzeigen können. 
1. Klicken Sie auf der **als neuer Benutzer registrieren** verknüpfen, und fügen Sie einen lokalen Benutzer mit der e-Mail- *joe@contoso.com*. Stellen Sie sicher, dass *Joe* die Seiten "Home", "About" und "Contact" anzeigen kann. 
	![Anmeldung](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss14.PNG)

1. Klicken Sie auf den Link *CM Demo*, und überprüfen Sie, ob die Daten sichtbar sind.
1. Klicken Sie auf einen Bearbeitungslink auf der Seite. Sie werden zur Anmeldeseite weitergeleitet (da kein neuer lokaler Benutzer zur Rolle *canEdit* hinzugefügt wurde).
1. Melden Sie sich als *user1@contoso.com* mit dem Kennwort "P_assw0rd1" an (das "0" in "word" ist eine Null). Sie werden zu der Bearbeitungsseite weitergeleitet, die Sie zuvor ausgewählt haben. <br/> Wenn Sie sich nicht mit diesem Konto und Kennwort anmelden können, versuchen Sie, das Kennwort aus dem Quellcode zu kopieren und einzufügen. Wenn Sie sich dennoch nicht anmelden können, überprüfen Sie die **Benutzername** Spalte die **AspNetUsers** Tabelle, um sicherzustellen *user1@contoso.com* wurde hinzugefügt. 
1. Stellen Sie sicher, dass Sie Daten ändern können.

## Bereitstellen der Anwendung in Azure

1. Visual Studio mit der rechten Maustaste des Projekts im **Projektmappen-Explorer** und wählen Sie **Veröffentlichen** aus dem Kontextmenü.

	!["Veröffentlichen" im Kontextmenü des Projekts][firsdeploy003]

	Der **Web veröffentlichen** -Assistent wird geöffnet.

1. Klicken Sie auf die **Einstellungen** auf der linken Seite der Registerkarte der **Web veröffentlichen** (Dialogfeld). Klicken Sie auf die **v** Symbol, wählen die **Remote-Verbindungszeichenfolge** für **ApplicationDbContext** und wählen Sie die **ContactManagerNN_db**.

   
	![Einstellungen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrc2.png)

1. Klicken Sie unter **ContactManagerContext**, auf **führen Code First-Migrationen**.

	![Einstellungen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrc3.png)

1. Klicken Sie auf **Veröffentlichen**.
1. Melden Sie sich als *user1@contoso.com* mit dem Kennwort "P_assw0rd1" an, und überprüfen Sie, ob Sie die Daten bearbeiten können. 1. Melden Sie sich ab.
1. Klicken Sie auf die [Google-Entwickler-Konsole](https://console.developers.google.com/) auf der **Anmeldeinformationen** Registerkarte Update die Umleitungs-URIS und JavaScript Orgins der Azure-URL verwenden.
1. Melden Sie sich über Google oder Facebook an. Wird das Google- oder Facebook-Konto zum Hinzufügen der **CanEdit** Rolle. Wenn der HTTP 400-Fehler mit der Meldung *The redirect URI in the request: https://contactmanager{my version}.azurewebsites.net/signin-google did not match a registered redirect URI.* angezeigt wird, müssen Sie warten, bis Ihre Änderungen übermittelt wurden. Wenn dieser Fehler auch nach mehreren Minuten noch angezeigt wird, überprüfen Sie die URIs.

### Beenden der Web-App, um die Registrierung anderer Personen zu verhindern  

1. Im **Server-Explorer**, navigieren Sie zu **Webanwendungen**.
4. Klicken Sie mit der rechten Maustaste auf die Webanwendung, und wählen Sie **Beenden**. 

	![Web-App beenden](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/s1.png)

	Alternativ aus der [Azure Portal](http://go.microsoft.com/fwlink/?LinkId=529715), können Sie die Web app auswählen und dann auf die **Beenden** Symbol am unteren Rand der Seite.

	![Web-App-Portal beenden](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/stopweb.png)

### Entfernen von "AddToRoleAsync", "Publish" und "Test"

1. Kommentieren Sie Sie aus, oder entfernen Sie den folgenden Code aus der **ExternalLoginConfirmation** -Methode im Controller Konto: `await UserManager.AddToRoleAsync(user.Id, "canEdit");`
1. Erstellen Sie das Projekt (die Dateiänderungen werden dabei gespeichert und es wird sichergestellt, dass keine Kompilierungsfehler vorhanden sind).
5. Mit der rechten Maustaste in des Projekts im **Projektmappen-Explorer** und wählen Sie **Veröffentlichen**.

	   !["Veröffentlichen" im Kontextmenü des Projekts](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
	
4. Klicken Sie auf die **Vorschau starten** Schaltfläche. Es werden nur die Dateien bereitgestellt, die aktualisiert werden müssen.
5. Starten Sie die Web-App in Visual Studio oder über das Portal. **Sie können nicht während einer Webanwendung veröffentlichen**.

	![Web-App starten](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss15.png)

5. Gehen Sie zurück zu Visual Studio, und klicken Sie auf **Veröffentlichen**.
3. Die Azure-Anwendung wird im Standardbrowser geöffnet. Wenn Sie angemeldet sind, melden Sie sich ab, sodass Sie die Startseite als anonymer Benutzer anzeigen können.  
4. Klicken Sie auf die **über** Link. Sie werden zur Anmeldeseite weitergeleitet.
5. Klicken Sie auf die **registrieren** Verknüpfen Sie auf der Anmeldeseite und lokales Konto erstellen. Dieses lokale Konto wird verwendet, um zu überprüfen, ob Sie auf die schreibgeschützten Seiten zugreifen können, jedoch nicht auf die Seiten mit Datenänderungen (die durch die Rolle *canEdit* geschützt sind). An späterer Stelle in diesem Lernprogramm wird der lokale Kontozugriff entfernt. 

	![Registrieren](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss16.PNG)

1. Stellen Sie sicher, dass Sie zu den Seiten *About* und *Contact* navigieren können.

	![Abmelden](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss17.PNG)

1. Klicken Sie auf die **CM Demo** Link zum Navigieren der **Cm** Controller. Alternativ können Sie *Cm* an die URL anhängen.

	![CM-Seite](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr4.png)
 
1. Klicken Sie auf einen Bearbeitungslink. Sie werden zur Anmeldeseite weitergeleitet. Unter **einen anderen Dienst zum Anmelden verwenden**, klicken Sie auf Google oder Facebook, und melden sich mit dem Konto, das Sie bereits registriert. (Wenn Sie schnell arbeiten und das Sitzungscookie noch nicht abgelaufen ist, werden Sie automatisch mit dem zuvor verwendeten Google- oder Facebook-Konto angemeldet.)
2. Stellen Sie sicher, dass Sie Daten bearbeiten können, während Sie beim Konto angemeldet sind. **Hinweis:** Sie können nicht beim Abmelden Google aus dieser app und die Protokolldateien in einem anderen Google-Konto mit demselben Browser. Wenn Sie einen einzigen Browser verwenden, müssen Sie zu Google navigieren und sich abmelden. Sie können sich über den Drittanbieterauthentifikator (wie Google) mithilfe eines anderen Browsers anmelden.

Falls Sie Ihren Vornamen und Nachnamen im Google-Konto nicht ausgefüllt haben, tritt ein NullReferenceException-Ausnahmefehler auf.


## Überprüfen der SQL Azure-DB ##

1. Im **Server-Explorer**, navigieren Sie zu den **ContactDB**
2. Klicken Sie mit der rechten Maustaste auf **ContactDB** und wählen Sie **im Objekt-Explorer von SQL Server geöffnet**.
 
	![In SSOX öffnen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr12.png)
 
3. Bei der erstmaligen Verbindung mit dieser Datenbank werden Sie unter Umständen zum Hinzufügen einer Firewallregel aufgefordert, die den Zugriff Ihrer aktuellen IP-Adresse erlaubt. Die IP-Adresse ist bereits eingetragen. Klicken Sie einfach im **Firewallregel hinzufügen** um Zugriff zu ermöglichen.

  ![Firewallregel hinzufügen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/addfirewallrule.png)
        
  Melden Sie sich dann bei der Datenbank mit dem Benutzernamen und dem Kennwort an, die Sie bei der Erstellung der Datenbank angegeben haben.
 
1. Klicken Sie mit der rechten Maustaste auf die **AspNetUsers** Tabelle, und wählen Sie **Ansichtsdaten**.

	![CM-Seite](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr8.png)
 
1. Beachten Sie die Id aus der Google-Konto, das Sie registriert werden die **CanEdit** Rolle und die Id des *user1@contoso.com*. Diese sollte nur Benutzer in der **CanEdit** Rolle. (Wird im nächsten Schritt überprüft.)

	![CM-Seite](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/s2.png)
 
2. In **Objekt-Explorer von SQL Server**, klicken Sie mit der rechten Maustaste auf **AspNetUserRoles** und wählen Sie **Ansichtsdaten**.

	![CM-Seite](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rs1.png)
 
Überprüfen Sie die **UserId**sind aus *user1@contoso.com* und dem registrierten Google-Konto. 


## Nächste Schritte

Befolgen Sie die Lernprogramme, die auf diesem Beispiel aufbauen:

1.	[Erstellen einer sicheren ASP.NET MVC 5-Webanwendung mit Anmeldung, E-Mail-Bestätigung und Kennwortzurücksetzung](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
2.	[ASP.NET MVC 5-Anwendung mit SMS und Two-Factor Authentication über E-Mail](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
3.	[Best Practices für das Bereitstellen von Kennwörtern und anderen vertraulichen Daten auf ASP.NET und Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) 
4.	[Erstellen einer ASP.NET MVC 5-App mit Facebook und Google OAuth2](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on) Dieser Artikel enthält Anweisungen zum Hinzufügen von Profildaten zur Benutzerregistrierungsdatenbank sowie ausführliche Anweisungen zur Verwendung von Facebook als Authentifizierungsanbieter.
5.	[Erste Schritte mit ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

Informationen zum Aktivieren der Anmeldeschaltflächen für soziale Netzwerke, die oben in diesem Lernprogramm dargestellt sind, finden Sie unter [Pretty social login buttons for ASP.NET MVC 5](http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/) (in englischer Sprache).

Im ausgezeichneten Artikel [Getting Started with EF and MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) von Tom Dykstra erhalten Sie einen Einblick in die fortgeschrittene MVC- und EF-Programmierung.

Dieses Lernprogramm und die Beispielanwendung wurden von [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) sowie Tom Dykstra und Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)) geschrieben.

*** Bitte abgeben*** Was Sie gefallen hat und was Sie möchte finden verbesserte, nicht nur über das Lernprogramm selbst, sondern auch über die Produkte, die veranschaulicht. Ihr Feedback unterstützt uns dabei, eine entsprechende Priorität für Verbesserungen festzulegen. Unter [Show Me How With Code](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code) (in englischer Sprache) können Sie neue Themen anfordern oder eine Stimme dafür abgeben.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- bookmarks -->
[Add an OAuth Provider]: #addOauth
[Using the Membership API]: #mbrDB
[Create a Data Deployment Script]: #ppd
[Update the Membership Database]: #ppd2

[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase


<!-- images-->
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rx2.png

[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png

[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rxb.png


[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rxSSL.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT2.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rr1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr1.png

[rxPrevDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxPrevDB.png

[rxWSnew]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxWSnew2.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxCreateWSwithDB.png

[setup007]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-setup-azure-site-004.png

[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-createapp-004.png

[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-deploy1-publish-001.png

[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-controller-add-context-menu.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-migrations-package-manager-console.png


[Important information about ASP.NET in Azure web apps]: #aspnetwindowsazureinfo
[Next steps]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->