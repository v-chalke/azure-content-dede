<properties linkid="develop-media-services-how-to-guides-manage-assets" urlDisplayName="Manage Assets in Media Services" pageTitle="How to Manage Assets in Media Services - Azure" metaKeywords="" description="Learn how to manage assets on Media Services. You can also manage jobs, tasks, access policies, locators, and more. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Manage Assets in storage" authors="migree" solutions="" manager="" editor="" />

Vorgehensweise: Verwalten von Medienobjekten im Speicher
========================================================

Dieser Artikel ist Teil einer Reihe zum Thema Programmierung von Azure-Mediendiensten. Das vorherige Thema war [Vorgehensweise: Schützen von Medienobjekten](http://go.microsoft.com/fwlink/?LinkID=301813&clcid=0x409).

Nachdem Sie Medienobjekte erstellt und nach Media Services hochgeladen haben, können Sie auf die Medienobjekte auf dem Server zugreifen und sie dort verwalten. Sie können auch andere Objekte auf dem Server verwalten, die Teil von Media Services sind, darunter Jobs, Aufgaben, Zugriffsrichtlinien, Locators usw.

Das folgende Beispiel zeigt das Abfragen eines Medienobjekts nach assetid.

``` {}
static IAsset GetAsset(string assetId)
{
    // Use a LINQ Select query to get an asset.
    var assetInstance =
        from a in _context.Assets
        where a.Id == assetId
        select a;
    // Reference the asset as an IAsset.
    IAsset asset = assetInstance.FirstOrDefault();

    return asset;
}
```

Um alle auf dem Server verfügbaren Medienobjekte aufzulisten, können Sie die folgende Methode verwenden, die die Medienobjektsammlung durchläuft und Details zu den einzelnen Medienobjekten anzeigt.

``` {}
 
static void ListAssets()
{
    string waitMessage = "Building the list. This may take a few "
        + "seconds to a few minutes depending on how many assets "
        + "you have."
        + Environment.NewLine + Environment.NewLine
        + "Please wait..."
        + Environment.NewLine;
    Console.Write(waitMessage);

    // Create a Stringbuilder to store the list that we build. 
    StringBuilder builder = new StringBuilder();

    foreach (IAsset asset in _context.Assets)
    {
        // Display the collection of assets.
        builder.AppendLine("");
        builder.AppendLine("******ASSET******");
        builder.AppendLine("Asset ID: " + asset.Id);
        builder.AppendLine("Name: " + asset.Name);
        builder.AppendLine("==============");
        builder.AppendLine("******ASSET FILES******");

        // Display the files associated with each asset. 
        foreach (IAssetFile fileItem in asset.AssetFiles)
        {
            builder.AppendLine("Name: " + fileItem.Name);
            builder.AppendLine("Size: " + fileItem.ContentFileSize);
            builder.AppendLine("==============");
        }
    }

    // Display output in console.
    Console.Write(builder.ToString());
}
```

Der folgende Codeausschnitt löscht alle Medienobjekte aus dem Media Services-Konto.

``` {}
foreach (IAsset asset in _context.Assets)
{
    asset.Delete();
}
```

Weitere Informationen zum Verwalten von Medienobjekten finden Sie unter:

-   [Verwalten von Medienobjekten mit dem Media Services SDK für .NET](http://msdn.microsoft.com/de-de/library/jj129589.aspx)
-   [Verwalten von Medienobjekten mit der Media Services REST-API](http://msdn.microsoft.com/de-de/library/jj129583.aspx)

Nächste Schritte
----------------

Da Sie jetzt wissen, wie Medienobjekte verwaltet werden, wechseln Sie zum Thema [Bereitstellen eines Medienobjekts durch Herunterladen](http://go.microsoft.com/fwlink/?LinkID=301734&clcid=0x409).
