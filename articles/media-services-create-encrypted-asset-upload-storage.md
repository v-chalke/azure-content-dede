<properties linkid="develop-media-services-how-to-guides-create-assets" urlDisplayName="Create Encrypted Asset and Upload to Storage" pageTitle="Create Encrypted Asset and Upload to Storage Azure" metaKeywords="" description="Learn how to get media content into Media Services by creating and uploading an encrypted asset." metaCanonical="" services="media-services" documentationCenter="" title="How to: Create an encrypted Asset and upload to storage" authors="migree" solutions="" manager="" editor="" />

Gewusst wie: Erstellen eines verschlüsselten Medienobjekts und hochladen in den Speicher
========================================================================================

Dieser Artikel ist Teil einer Reihe zum Thema Programmierung von Azure-Mediendiensten. Das vorherige Thema war [Einrichten Ihres Computers für Mediendienste](http://go.microsoft.com/fwlink/?LinkID=301751&clcid=0x409).

Um Medieninhalte in Mediendienste zu übertragen, müssen Sie zunächst ein Medienobjekt erstellen, dieses Objekt mit Dateien füllen und anschließend hochladen. Diesen Prozess nennt man auch die Erfassung von Inhalten.

Bei der Erstellung von Medienobjekten können Sie drei verschiedene Optionen für die Verschlüsselung auswählen.

-   **AssetCreationOptions.None**: keine Verschlüsselung. Wählen Sie diese Option aus, wenn Sie Ihr Medienobjekt nicht verschlüsseln möchten.
-   **AssetCreationOptions.CommonEncryptionProtected**: für Dateien mit Common Encryption (CENC). Ein Beispiel ist ein Satz von Dateien, die bereits PlayReady-verschlüsselt sind.
-   **AssetCreationOptions.StorageEncrypted**: Speicherverschlüsselung. Verschlüsselt eine unverschlüsselte Eingabedatei vor dem Hochladen in den Azure-Speicher.

**HINWEIS**: Beachten Sie, dass Mediendienste Speicherverschlüsselung auf der Festplatte bieten, und nicht für die Netzwerkkommunikation wie Digital Rights Manager (DRM.)

Der folgende Beispielcode führt die folgenden Aufgaben aus:

-   Erstellen eines leeren Medienobjekts.
-   Erstellt eine AssetFile-Instanz, der wir das Medienobjekt zuweisen werden.
-   Erstellt eine AccessPolicy-Instanz mit den Berechtigungen und der Zugriffsdauer auf das Medienobjekt.
-   Erstellt eine Locator-Instanz, die Zugriff auf das Medienobjekt bietet.
-   Lädt eine einzelne Datei in einen Mediendienst hoch.

<pre><code>
static private IAsset CreateEmptyAsset(string assetName, AssetCreationOptions assetCreationOptions)
{
    var asset = _context.Assets.Create(assetName, assetCreationOptions);

    Console.WriteLine("Asset name: " + asset.Name);
    Console.WriteLine("Time created: " + asset.Created.Date.ToString());

    return asset;
}

static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
{
    var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
    var asset = CreateEmptyAsset(assetName, assetCreationOptions);

    var fileName = Path.GetFileName(singleFilePath);

    var assetFile = asset.AssetFiles.Create(fileName);

    Console.WriteLine("Created assetFile {0}", assetFile.Name);
    Console.WriteLine("Upload {0}", assetFile.Name);

    assetFile.Upload(singleFilePath);
    Console.WriteLine("Done uploading of {0} using Upload()", assetFile.Name);

    return asset;
}
</code></pre>

Der folgende Code zeigt, wie Sie ein Medienobjekt erstellen und mehrere Dateien hochladen können.

<pre><code>
static public IAsset CreateAssetAndUploadMultipleFiles( AssetCreationOptions assetCreationOptions, string folderPath)
{
    var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

    var asset = CreateEmptyAsset(assetName, assetCreationOptions);

    var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                        AccessPermissions.Write | AccessPermissions.List);
    var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

    var blobTransferClient = new BlobTransferClient();
    blobTransferClient.NumberOfConcurrentTransfers = 20;
    blobTransferClient.ParallelTransferThreadCount = 20;

    blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

    var filePaths = Directory.EnumerateFiles(folderPath);

    Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

    if (!filePaths.Any())
    {
        throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
    }

    var uploadTasks = new List<Task>();
    foreach (var filePath in filePaths)
    {
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        Console.WriteLine("Created assetFile {0}", assetFile.Name);
                
        // AssetFiles sollten vor dem Hochladen validiert werden. 
        Console.WriteLine("Start uploading of {0}", assetFile.Name);
        uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
    }

    Task.WaitAll(uploadTasks.ToArray());
    Console.WriteLine("Done uploading the files");

    blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

    locator.Delete();
    accessPolicy.Delete();

    return asset;
}

static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
{
    if (e.ProgressPercentage > 4) // Jitter beim Start vermeiden, während die Uploadaufgaben hinzugefügt werden.
    {
        Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
    }
}

</code></pre>

Nächste Schritte
----------------

Sie haben nun ein Medienobjekt in den Mediendienst hochgeladen und können mit dem Artikel [Abrufren eines Medienprozessors](http://go.microsoft.com/fwlink/?LinkID=301732&clcid=0x409) fortfahren.

[How to Get a Media Processor]:http://go.microsoft.com/fwlink/?LinkID=301732&clcid=0x409