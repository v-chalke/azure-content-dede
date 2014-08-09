

<properties linkid="develop-mobile-tutorials-handle-conflcits-offline-data-dotnet" urlDisplayName="Handle Conflicts with Offline Data" pageTitle="Handle Conflicts with offline data in Mobile Services (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to handle conflicts with offline data in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling conflicts with offline data in Mobile Services" authors="wesmc" />

Behandeln von Konflikten mit Offlinedaten in Mobile Services
============================================================

[Windows Store C\#](/de-de/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data "Windows Store C#")

In diesem Thema erfahren Sie, wie Sie Daten synchronisieren und Konflikte behandeln können, wenn Sie die Offlinefunktionen von Azure Mobile Services verwenden. In diesem Lernprogramm laden Sie eine App herunter, die Offline- und Onlinedaten unterstützt, integrieren den mobilen Dienst mit der App und melden sich anschließend im Azure-Verwaltungsportal an, um die Datenbank beim Ausführen der App anzuzeigen und zu aktualisieren.

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Offlinedaten](/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data). Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Offlinedaten](/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data) abschließen.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Herunterladen des Windows Store-App-Projekts](#download-app)
2.  [Hinzufügen einer Spalte mit Fälligkeitsdatum zur Datenbank](#add-column)
    -   [Aktualisieren der Datenbank für mobile Dienste mit .NET-Backend](#dotnet-backend)
    -   [Aktualisieren der Datenbank für mobile Dienste mit JavaScript](#javascript-backend)

3.  [Testen der App mit einem mobilen Dienst](#test-app)
4.  [Manuelles Aktualisieren der Daten im Backend zum Erzeugen eines Konflikts](#handle-conflict)

In diesem Lernprogramm wird Visual Studio 2013 für Windows 8.1 verwendet.

<a name="download-app"></a>Herunterladen des Beispielprojekts
----------------------------------

Dieses Lernprogramm baut auf dem [Handling conflicts code sample](http://go.microsoft.com/fwlink/?LinkId=394787) (Codebeispiel zur Behandlung von Konflikten) auf. Dabei handelt es sich um ein Windows Store-App-Projekt in Visual Studio 2013. Die Benutzeroberfläche für diese App ähnelt der App im Lernprogramm [Erste Schritte mit Offlinedaten](/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data). Der Unterschied besteht darin, dass für jedes TodoItem eine neue Datumsspalte vorhanden ist.

1.  Laden Sie die C\#-Version des [Handling conflicts code sample](http://go.microsoft.com/fwlink/?LinkId=394787) (Codebeispiel zur Behandlung von Konflikten) herunter.

2.  Installieren Sie [SQLite für Windows 8.1](http://go.microsoft.com/fwlink/?LinkId=394776), wenn Sie die Installation noch nicht vorgenommen haben.

3.  Öffnen Sie das heruntergeladene Projekt in Visual Studio 2013. Drücken Sie die **F5**-Taste, um das Projekt neu zu erstellen und die App zu starten.

4.  Geben Sie in der App in **Insert a TodoItem** Text ein, und klicken Sie dann auf **Save**. Sie können auch das Fälligkeitsdatum der todo-Elemente ändern, die Sie hinzufügen.

Beachten Sie, dass noch keine Verbindung zwischen der App und einem mobilen Dienst besteht, daher erzeugen die Schaltflächen **Push** und **Pull** Ausnahmefehler.

<a name="add-column"></a>Hinzufügen einer Spalte zum Datenmodell
---------------------------------------

In diesem Abschnitt aktualisieren Sie die Datenbank für Ihren mobilen Dienst so, dass eine TodoItem-Tabelle mit einer Spalte für das Fälligkeitsdatum enthalten ist. In der App können Sie das Fälligkeitsdatum für ein Element in der Laufzeit ändern, sodass Sie in einem späteren Abschnitt diese Lernprogramms Synchronisierungskonflikte generieren können.

Die `TodoItem`-Klasse im Beispiel ist in "MainPage.xaml.cs" definiert. Beachten Sie, dass die Klasse das folgende Attribut besitzt, das die Synchronisierungsvorgänge auf die Tabelle ausrichtet.

        [DataTable("TodoWithDate")]

Aktualisieren Sie die Datenbank so, dass diese Tabelle enthalten ist.

### <a name="dotnet-backend"></a>Aktualisieren der Datenbank für mobile Dienste mit .NET-Backend

Wenn Sie für Ihren mobilen Dienst das .NET-Backend verwenden, führen Sie diese Schritte aus, um das Schema für die Datenbank zu aktualisieren.

1.  Öffnen Sie das Projekt für den mobilen Dienst mit .NET-Backend in Visual Studio.
2.  Erweitern Sie im Projektmappen-Explorer von Visual Studio in Ihrem Dienstprojekt den Ordner **Models**, und öffnen Sie "ToDoItem.cs". Fügen Sie die `DueDate`-Eigenschaft wie folgt hinzu.

           public class TodoItem : EntityData
           {
             public string Text { get; set; }
             public bool Complete { get; set; }
             public System.DateTime DueDate { get; set; }
           }

3.  Erweitern Sie im Projektmappen-Explorer in Visual Studio den Ordner **App\_Start**, und öffnen Sie die Datei "WebApiConfig.cs".

    Beachten Sie, dass in der Datei "WebApiConfig.cs" die standardmäßige Initialisierungsklasse für Datenbanken aus der Klasse `DropCreateDatabaseIfModelChanges` abgeleitet ist. Das bedeutet, alle Änderungen am Modell sorgen dafür, dass die Tabelle gelöscht und neu erstellt wird, um dem neuen Modell zu entsprechen. Daher gehen die Daten in der Tabelle verloren und für die Tabelle wird ein neues Seeding ausgeführt. Modifizieren Sie die Seed-Methode des Dateninitialisierers so, dass die Initialisierungsfunktion `Seed()` wie folgt aussieht, um die neue DueDate-Spalte zu initialisieren. Speichern Sie die Datei "WebApiConfig.cs".

    > [WACOM.NOTE] Wenn Sie den standardmäßigen Datenbankinitialisierer verwenden, löscht Entity Framework die Datenbank und erstellt sie erneut, wenn eine Änderung am Datenmodell in der Code First-Modelldefinition erkannt wird. Sie müssen Code First-Migrationen verwenden, um diese Datenmodelländerungen vornehmen und vorhandene Daten in der Datenbank erhalten zu können. Weitere Informationen finden Sie unter [Gewusst wie: Verwenden von Code First-Migrationen zur Aktualisierung des Datenmodells](./articles/mobile-services-dotnet-backend-use-code-first-migrations).

         new TodoItem { Id = "1", Text = "First item", Complete = false, DueDate = DateTime.Today },
         new TodoItem { Id = "2", Text = "Second item", Complete = false, DueDate = DateTime.Today },

4.  Erweitern Sie im Projektmappen-Explorer in Visual Studio den Ordner **Controllers**, und öffnen Sie die Datei "ToDoItemController.cs". Benennen Sie die Klasse `TodoItemController` in `TodoWithDateController` um. Dadurch ändern Sie den REST-Endpunkt für Tabellenoperationen.

         public class TodoWithDateController : TableController<TodoItem>

5.  Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf das Projekt für den mobilen Dienst mit .NET-Backend, und klicken Sie auf **Veröffentlichen**, um Ihre Änderungen zu veröffentlichen.

### <a name="javascript-backend"></a>Aktualisieren der Datenbank für mobile Dienste mit JavaScript-Backend

Für mobile Dienste mit JavaScript-Backend fügen Sie eine neue Tabelle mit dem Namen **TodoWithDate** hinzu. Führen Sie die folgenden Schritte aus, um die Tabelle **TodoWithDate** für mobile Dienste mit JavaScript-Backend hinzuzufügen.

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an.

2.  Navigieren Sie zur Registerkarte **Daten** des mobilen Dienstes.

3.  Klicken Sie unten auf der Seite auf **Erstellen**, und erstellen Sie eine neue Tabelle mit dem Namen **TodoWithDate**.

<a name="test-app"></a>Testen der App mit dem mobilen Dienst
-------------------------------------

Testen Sie jetzt die App mit Mobile Services.

1.  Suchen Sie im Azure-Verwaltungsportal den Anwendungsschlüssel Ihres mobilen Dienstes, indem Sie in der Befehlsleiste auf **Schlüssel verwalten** klicken. Kopieren Sie den **Anwendungsschlüssel**.

2.  Öffnen Sie im Projektmappen-Explorer von Visual Studio im Client-Beispielprojekt die Datei "App.xaml.cs". Ändern Sie die Initialisierung von **MobileServiceClient** so, dass die URL und der Anwendungsschlüssel Ihres mobilen Dienstes verwendet werden:

          public static MobileServiceClient MobileService = new MobileServiceClient(
             "https://your-mobile-service.azure-mobile.net/",
             "Your AppKey"
         );

3.  Drücken Sie in Visual Studio die Taste **F5**, um die App zu erstellen und auszuführen.

4.  Geben Sie wie zuvor Text im Textfeld ein, und klicken Sie danach auf die Schaltfläche **Speichern**. So werden die Daten in der lokalen Synchronisierungstabelle, jedoch nicht auf dem Server gespeichert.

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png)

5.  Um den aktuellen Status der Datenbank anzuzeigen, melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, klicken Sie auf **Mobile Services**. Klicken Sie dann auf Ihren mobilen Dienst.

-   Wenn Sie für Ihren mobilen Dienst das JavaScript-Backend verwenden, klicken Sie auf die Registerkarte **Daten**, und klicken Sie dann auf die Tabelle **TodoWithDate**. Klicken Sie auf **Durchsuchen**, um anzuzeigen, dass die Tabelle immer noch leer ist, da die Änderungen noch nicht per Push-Vorgang von der App auf den Server übertragen wurden.

	![][1]

-   Wenn Sie für Ihren mobilen Dienst das .NET-Backend verwenden, klicken Sie auf die Registerkarte **Konfigurieren**, und klicken Sie dann auf die SQL-Datenbank. Klicken Sie unten auf **Manage**, um sich beim SQL-Azure-Verwaltungsportal anzumelden und die Datenbank anzuzeigen.

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-empty-sql.png)

1.  Klicken Sie dann in der App auf **Push**.

2.  Klicken Sie im Verwaltungsportal in der Tabelle **TodoItem** auf **Aktualisieren**. Jetzt sollten die Daten angezeigt werden, die Sie in der App eingegeben haben.

	![][3]

<a name="handle-conflict"></a>Aktualisieren der Daten im Backend zum Erzeugen eines Konflikts
---------------------------------------------------------------

In einem realen Szenario würde ein Synchronisierungskonflikt auftreten, wenn eine App Aktualisierungen per Push-Vorgang auf einen Datensatz in der Datenbank überträgt und dann eine andere App versucht, eine Änderung per Push-Vorgang auf denselben Datensatz zu übertragen, jedoch basierend auf einer veralteten Version dieses Datensatzes. Wenn eine Instanz der App versucht, denselben Datensatz zu aktualisieren, ohne den aktualisierten Datensatz per Pull-Vorgang abzurufen, tritt ein Konflikt auf, und dieser wird in der App als `MobileServicePreconditionFailedException` erfasst.

Wenn Sie die App auf einem anderen Computer bereitstellen möchten, um zwei Instanzen der App auszuführen und einen Konflikt zu erzeugen, können Sie die Bereitstellungsanweisungen aus dem Lernprogramm [Behandeln von Konflikten in Datenbanken](/de-de/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/#test-app) verwenden.

Mit den folgenden Schritten können Sie die Datenbank in Visual Studio so aktualisieren, dass ein Konflikt auftritt.

1.  Führen Sie in Visual Studio den Server-Explorer aus, und stellen Sie eine Verbindung zu Ihrem Azure-Konto her. Erweitern Sie **SQL Databases** für Ihr Azure-Konto.

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-server-explorer.png)

2.  Klicken Sie mit der rechten Maustaste auf Ihre SQL-Datenbank in der Liste, und klicken Sie auf **Open in SQL Server Object Explorer**.
3.  Erweitern Sie im Objekt-Explorer von SQL Server die Datenbank, und erweitern Sie **Tabellen**. Klicken Sie mit der rechten Maustaste auf die Tabelle **TodoWithDate**, und klicken Sie auf **Daten anzeigen**.

4.  Ändern Sie das **complete**-Feld für eines der Elemente zu "True".

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-sql-server-object-explorer-update-data.png)

5.  Bearbeiten Sie in der Todo-App das Element, das Sie direkt in der Datenbank geändert haben.

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run2.png)

6.  Klicken Sie auf die Schaltfläche **Push**. Es wird ein Dialogfeld angezeigt, in dem gefragt wird, wie der Konflikt gelöst werden soll. Wählen Sie eine der beiden Optionen, um dem Konflikt zu lösen.

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run3.png)

Prüfen des Codes zur Behebung von Synchronisierungskonflikten
-------------------------------------------------------------

Sie müssen eine Versionsspalte in die lokale Datenbank und das Datenübertragungsobjekt einfügen, damit die Offlinefunktion Konflikte erkennt. Die Klasse `TodoItem` besitzt das folgende Element:

        [Version]
        public string Version { get; set; }

Die Spalte `__version` ist außerdem in der lokalen Datenbank festgelegt, in der Methode `OnNavigatedTo()`.

Erstellen Sie eine Klasse, die `IMobileServiceSyncHandler` implementiert, um Offlinesynchronisierungskonflikte in Ihrem Code zu beheben. Übergeben Sie ein Objekt dieses Typs an den Aufruf für `InitializeAsync`:

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

Die Klasse `SyncHandler` in **MainPage.xaml.cs** implementiert `IMobileServiceSyncHandler`. Die Methode `ExecuteTableOperationAsync` wird aufgerufen, wenn die Push-Vorgänge an den Server gesendet werden. Wenn ein Ausnahmefehler des Typs `MobileServicePreconditionFailedException` ausgegeben wird, bedeutet dies, dass ein Konflikt zwischen der lokalen und der Remote-Version eines Elements besteht.

Um Konflikte zugunsten des lokalen Elements zu lösen, führen Sie den Vorgang einfach erneut aus. Wenn ein Konflikt aufgetreten ist, wird die Version des lokalen Elements so aktualisiert, dass sie mit der Serverversion übereinstimmt. Wenn Sie den Vorgang erneut ausführen, werden also die Serveränderungen mit den lokalen Änderungen überschrieben:

    await operation.ExecuteAsync(); 

Um Konflikte zugunsten des Serverelements zu lösen, verlassen Sie einfach `ExecuteTableOperationAsync`. Die lokale Version des Objekts wird verworfen und durch den Wert vom Server ersetzt.

Um den Push-Vorgang zu beenden (die Änderungen in der Warteschlange jedoch zu erhalten), verwenden Sie die Methode `AbortPush()`:

    operation.AbortPush();

Dadurch wird der aktuelle Push-Vorgang beendet. Es werden jedoch alle ausstehenden Änderungen beibehalten, einschließlich des aktuellen Vorgangs, wenn `AbortPush` von `ExecuteTableOperationAsync` aufgerufen wird. Wenn `PushAsync()` das nächste Mal aufgerufen wird, werden diese Änderungen an den Server gesendet.

Wenn ein Push-Vorgang abgebrochen wird, gibt `PushAsync` eine `MobileServicePushFailedException` aus, und die Ausnahmeeigenschaft `PushResult.Status` hat den Wert `MobileServicePushStatus.CancelledByOperation`.

<!-- Anchors. -->
[Download the Windows Store app project]: #download-app
[Create the mobile service]: #create-service
[Add a due date column for the database]: #add-column
[Updating the database for .NET backend mobile services]: #dotnet-backend  
[Updating the database for JavaScript mobile services]: #javascript-backend
[Test the app against a mobile service]: #test-app
[Manually update the data in the backend to create a conflict]: #handle-conflict
[Next Steps]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
[1]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-empty.png
[2]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-empty-sql.png
[3]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-push1.png
[4]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-design-edit.png
[5]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-server-explorer.png
[6]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-sql-server-object-explorer-update-data.png
[7]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run2.png
[8]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run3.png




<!-- URLs -->
[Handling conflicts code sample]: http://go.microsoft.com/fwlink/?LinkId=394787
[Get started with Mobile Services]: /de-de/documentation/articles/mobile-services-windows-store-get-started/
[Get started with offline data]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[Azure Management Portal]: https://manage.windowsazure.com/
[Handling Database Conflicts]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/#test-app