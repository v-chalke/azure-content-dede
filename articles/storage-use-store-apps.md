<properties linkid="manage-services-storage-using-storage-with-windows-store-apps" urlDisplayName="Azure Storage in Windows Store apps" pageTitle="Use Azure storage in Windows Store Apps | Azure" metaKeywords="" description="Learn how to use Azure blobs, queues, and tables to store data for a Windows Store app." metaCanonical="" services="storage" documentationCenter="" title="How to use Azure Storage in Windows Store Apps" authors="" solutions="" manager="" editor="" />

Verwenden von Azure Storage in Windows Store-Apps
=================================================

Dieser Leitfaden zeigt die ersten Schritte der Entwicklung einer Windows Store-App, die Azure Storage nutzt.

Herunterladen erforderlicher Tools
----------------------------------

-   [Visual Studio 2012](http://msdn.microsoft.com/de-de/library/windows/apps/br211384) vereinfacht das Erstellen, Debuggen, Lokalisieren, Packen und Bereitstellen von Windows Store-Apps.
-   [Windows Azure Storage Client Library for Windows Runtime](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/05/windows-azure-storage-client-library-for-windows-runtime.aspx) enthält Typen, die für die Kommunikation mit Azure Storage verwendet werden, gepackt in eine Windows Runtime-Komponente.
-   [WCF Data Services Tools for Windows Store Apps](http://www.microsoft.com/en-us/download/details.aspx?id=30714) erwartet die Add Service Reference-Nutzung durch clientseitige OData-Unterstützung für Windows Store-Apps in Visual Studio 2012.

Entwickeln von Apps
-------------------

### Vorbereitung

Erstellen Sie ein neues Windows Store-App-Projekt in Visual Studio 2012:

![store-apps-storage-vs-project](./media/storage-use-store-apps/store-apps-storage-vs-project.png)

Fügen Sie dann einen Verweis Referenz zur Azure Storage-Clientbibliothek hinzu, indem Sie mit der rechten Maustaste auf **Verweise** klicken und dann **Verweis hinzufügen** auswählen. Wechseln Sie dann zur Speicherclientbibliothek für Windows Runtime, die Sie heruntergeladen haben:

![store-apps-storage-choose-library](./media/storage-use-store-apps/store-apps-storage-choose-library.png)

### Verwenden der Bibliothek mit den Blob- und Warteschlangendiensten

An diesem Punkt ist Ihre App bereit zur Kommunikation mit den Blob- und Warteschlangendiensten. Fügen Sie die folgenden **using**-Anweisungen hinzu, sodass direkt auf Azure Storage-Typen verwiesen werden kann:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;

Fügen der Seite dann eine Schaltfläche hinzu. Fügen Sie dem **Click**-Ereignis den folgenden Code hinzu, und ändern Sie die Ereignishandlermethode mit dem [async-Schlüsselwort](http://msdn.microsoft.com/de-de/library/vstudio/hh156513.aspx):

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();

Dieser Code setzt voraus, dass Sie zwei Zeichenfolgenvariablen haben, nämlich *accountName* und *accountKey*, die den Namen des Speicherkontos und den Kontoschlüssel darstellen, die dem Konto zugeordnet sind.

Erstellen Sie die Anwendung, und führen Sie sie aus. Beim Klick auf die Schaltfläche wird zunächst überprüft, ob ein Container namens *container1* in Ihrem Konto vorhanden ist. Andernfalls wird er erstellt.

### Verwenden der Bibliothek mit dem Tabellenspeicherdienst

Die Typen, die für die Kommunikation mit dem Tabellenspeicherdienst verwendet werden, hängen von der WCF Data Services for Windows Store Apps-Bibliothek ab. Fügen Sie dann einen Verweis zu den erforderlichen WCF-Bibliotheken mithilfe der Package Manager-Konsole hinzu:

![store-apps-storage-package-manager](./media/storage-use-store-apps/store-apps-storage-package-manager.png)

Verweisen Sie Package Manager mithilfe des folgenden Befehls auf den Speicherort auf dem Computer:

    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services.0\bin\NuGet"

Dieser Befehl fügt dem Projekt automatisch alle erforderlichen Verweise hinzu. Falls Sie die Package Manager-Konsole nicht verwenden möchten, können Sie auch den NuGet-Ordner von WCF Data Services auf dem lokalen Computer hinzufügen, um die Paketquellen aufzulisten. Fügen Sie dann den Verweis über die im Thema zum [Verwalten von NuGet-Paketen mithilfe des Dialogs](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog) beschriebene Oberfläche hinzu.

Wenn Sie auf das NuGet-Paket von WCF Data Services verwiesen haben, ändern Sie den Code im **Click**-Ereignis der Schaltfläche:

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();

Dieser Code überprüft, ob eine Tabelle namens *table1* in Ihrem Konto vorhanden ist. Andernfalls wird sie erstellt.

Sie können auch einen Verweis zu "Microsoft.WindowsAzure.Storage.Table.dll" hinzufügen. Diese Datei ist im gleichen heruntergeladenen Paket verfügbar. Diese Bibliothek enthält zusätzliche Funktionen, zum Beispiel Serialisierung und allgemeine Abfragen basierend auf Reflexion. Beachten Sie, dass diese Bibliothek JavaScript nicht unterstützt.
