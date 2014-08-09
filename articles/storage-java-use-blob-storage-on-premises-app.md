<properties linkid="dev-java-how-to-on-premise-application-with-blob-storage" urlDisplayName="Image Gallery w/ Storage" pageTitle="On-premises application with blob storage (Java) | Microsoft Azure" metaKeywords="Azure blob storage, Azure blob Java, Azure blob example, Azure blob tutorial" description="Learn how to create a console application that uploads an image to Azure, and then displays the image in your browser. Code samples in Java." metaCanonical="" services="storage" documentationCenter="Java" title="On-Premises Application with Blob Storage" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" />

Lokale Anwendungen mit Blob-Speicher
====================================

Das folgende Beispiel zeigt, wie Sie den Azure-Speicher zur Speicherung von Bildern in Azure verwenden können. Der folgende Code implementiert eine Konsolenanwendung, die ein Bild in Azure hochlädt und anschließend eine HTML-Datei erstellt, die das Bild in Ihrem Browser anzeigt.

Inhaltsverzeichnis
------------------

-   [Voraussetzungen](#bkmk_prerequisites)
-   [Verwenden von Azure Blob-Speicher für Dateiuploads](#bkmk_uploadfile)
-   [So löschen Sie einen Container](#bkmk_deletecontainer)

Voraussetzungen
---------------

1.  Java Developer Kit (JDK), Version 1.6 oder höher.
2.  Das Azure SDK ist installiert.
3.  Die JAR-Datei der Azure-Bibliotheken für Java und alle sonstigen JAR-Abhängigkeiten sind installiert und im Buildpfad Ihres Java-Compilers eingebunden. Weitere Informationen zur Installation der Azure-Bibliotheken für Java finden Sie unter [Downloadseite des Azure-SDK für Java](http://www.windowsazure.com/de-de/develop/java/).
4.  Ein Azure-Speicherkonto wurde eingerichtet. Der folgende Code verwendet Kontonamen und Kontoschlüssel des Speicherkontos. Unter [Erstellen eines Speicherkontos](http://www.windowsazure.com/de-de/manage/services/storage/how-to-create-a-storage-account/) finden Sie Informationen zum Einrichten von Speicherkonten, und unter [Verwalten von Speicherkonten](http://www.windowsazure.com/de-de/manage/services/storage/how-to-manage-a-storage-account/) erfahren Sie, wie Sie Ihren Kontoschlüssel ermitteln können.
5.  Sie haben eine lokale Bilddatei unter dem Pfad c:\\myimages\\image1.jpg erstellt. Alternativ können Sie den **FileInputStream**-Konstruktor im Beispiel verändern, um einen anderen Pfad bzw. Dateinamen zu verwenden.

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

Verwenden von Azure Blob-Speicher für Dateiuploads
--------------------------------------------------

Hier finden Sie eine Schritt-für-Schritt-Anleitung. Falls Sie diese überspringen möchten, finden Sie den vollständigen Code weiter unten in diesem Artikel.

Importieren Sie zunächst die Core-Klassen für den Azure-Speicher, die Azure Blob-Clientklassen, die Java IO-Klassen und die **URISyntaxException**-Klasse:

    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;
    import java.io.*;
    import java.net.URISyntaxException;

Deklarieren Sie eine Klasse mit dem Namen **StorageSample** und fügen Sie eine offene geschweifte Klammer an,
**{**.

    public class StorageSample {

Deklarieren Sie in der Klasse **StorageSample** eine String-Variable für das Standard-Endpunktprotokoll, den Namen Ihres Speicherkontos und Ihren Speicherzugriffsschlüssel gemäß Ihres Azure-Speicherkontos. Ersetzen Sie die Platzhalterwerte **your\_account\_name** und **your\_account\_key** durch Ihren Kontonamen und Ihren Kontoschlüssel.

    public static final String storageConnectionString = 
           "DefaultEndpointsProtocol=http;" + 
               "AccountName=your_account_name;" + 
               "AccountKey=your_account_name"; 

Fügen Sie die **main**-Deklaration hinzu, öffnen Sie einen **try**-Block und fügen Sie die notwendigen geschweiften Klammern an, **{**.

    public static void main(String[] args) 
    {
        try
        {

Deklarieren die folgenden Variablen (die Beschreibung gibt deren Verwendungszweck an):

-   **CloudStorageAccount**: Wird zur Initialisierung des Konto-Objekts mit Ihrem Azure-Speicherkontonamen und -Schlüssel und zur Erstellung des Blob-Clientobjekts verwendet.
-   **CloudBlobClient**: Dient zum Zugriff auf den Blob-Dienst.
-   **CloudBlobContainer**: Dient zur Erstellung eines Blob-Containers, zum Auflisten der Blobs im Container und zum Löschen des Containers.
-   **CloudBlockBlob**: Ermöglicht den Upload einer lokalen Datei in den Container.

<!-- -->

    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;

Weisen Sie der **account**-Variable einen Wert zu.

    account = CloudStorageAccount.parse(storageConnectionString);

Weisen Sie der **serviceClient**-Variable einen Wert zu.

    serviceClient = account.createCloudBlobClient();

Weisen Sie der **container**-Variable einen Wert zu. Wir rufen einen Verweis auf einen Container mit dem Namen **gettingstarted** ab.

    // Containername muss aus Kleinbuchstaben bestehen.
    container = serviceClient.getContainerReference("gettingstarted");

Erstellen Sie den Container. Diese Methode erstellt den Container, falls dieser nicht existiert (und gibt **true** zurück). Falls der Container existiert, wird **false** zurückgegeben. Eine Alternative zu **createIfNotExist** ist die **create**-Methode (gibt einen Fehler zurück, falls der Container bereits existiert).

    container.createIfNotExist();

Anonymen Zugiff für den Container festlegen.

    // Anonymen Zugiff für den Container festlegen.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

Abrufen eines Verweises auf den Blockblob, der den Blob im Azure-Speicher repräsentiert.

    blob = container.getBlockBlobReference("image1.jpg");

Verwenden Sie den **File**-Konstruktor, um einen Verweis auf die lokale Datei abzurufen, die Sie hochladen möchten. (Stellen Sie sicher, dass die Datei existiert, bevor Sie den Code ausführen.)

    File fileReference = new File ("c:\myimages\image1.jpg");

Laden Sie die lokale Datei hoch, indem Sie die **CloudBlockBlob.upload**-Methode aufrufen. Der erste Parameter der **CloudBlockBlob.upload**-Methode ist ein **FileInputStream**-Objekt, das die lokale Datei repräsentiert, die in den Azure-Speicher hochgeladen wird. Der zweite Parameter ist die Dateigröße in Byte.

    blob.upload(new FileInputStream(fileReference), fileReference.length());

Rufen Sie eine Hilfsfunktion namens **MakeHTMLPage** auf, um eine einfache HTML-Seite zu erstellen, die ein **&lt;image\>**-Element enthält, das auf den nun in Ihrem Azure-Speicherkonto vorhandenen Blob zeigt. (Der Code für **MakeHTMLPage** wird später in diesem Artikel ebenfalls besprochen.)

    MakeHTMLPage(container);

Statusnachricht und Informationen über die erstellte HTML-Seite ausgeben.

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

Schließen Sie den **try**-Block mit einer geschweiften Klammer: **}**

Behandeln Sie die folgenden Ausnahmen:

-   **FileNotFoundException**: Kann vom **FileInputStream**- oder vom **FileOutputStream**-Konstruktor geworfen werden.
-   **StorageException**: Kann von der Clientbibliothek für den Azure-Speicher geworfen werden.
-   **URISyntaxException**: Kann von der **ListBlobItem.getUri**-Methode geworfen werden.
-   **Exception**: Allgemeine Fehlerbehandlung.

<!-- -->

    catch (FileNotFoundException fileNotFoundException)
    {
        System.out.print("FileNotFoundException encountered: ");
        System.out.println(fileNotFoundException.getMessage());
        System.exit(-1);
    }
    catch (StorageException storageException)
    {
        System.out.print("StorageException encountered: ");
        System.out.println(storageException.getMessage());
        System.exit(-1);
    }
    catch (URISyntaxException uriSyntaxException)
    {
        System.out.print("URISyntaxException encountered: ");
        System.out.println(uriSyntaxException.getMessage());
        System.exit(-1);
    }
    catch (Exception e)
    {
        System.out.print("Exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Schließen Sie **main** mit einer geschweiften Klammer: **}**

Erstellen Sie eine Methode mit dem Namen **MakeHTMLPage**, um eine einfache HTML-Seite zu generieren. Diese Methode hat einen Parameter vom Typ **CloudBlobContainer**, der zum Iterieren über die Liste der hochgeladenen Blobs verwendet wird. Diese Methode wirft Ausnahmen vom Typ **FileNotFoundException**, die aus dem **FileOutputStream**-Konstruktor stammen, und **URISyntaxException**, die von der **ListBlobItem.getUri**-Methode geworfen werden kann. Fügen Sie eine offene geschweifte Klammer an, **{**.

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

Erstellen Sie eine lokale Datei mit dem Namen **index.html**.

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

Schreiben Sie die Elemente **&lt;html\>**, **&lt;header\>** und **&lt;body\>** in die Datei.

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

Durchlaufen Sie die Liste der hochgeladenen Blobs. Erstellen Sie für jeden Blob ein **&lt;img\>**-Element in der Seite, dessen **src**-Attribut auf die URI des Blobs zeigt, der in Ihrem Azure-Speicherkonto liegt. In diesem Beispiel haben Sie zwar nur ein Bild hinzugefügt, aber dieser Code würde alle weiteren hochgeladenen Bilder ebenfalls durchlaufen.

Zur Vereinfachung geht dieses Beispiel davon aus, dass es sich bei allen hochgeladenen Blobs um Bilder handelt. Falls Sie Blobs hochgeladen haben, die keine Bilder sind, oder Seitenblobs anstelle von Blockblobs, müssen Sie Ihren Code entsprechend anpassen.

    // Aufzählen der hochgeladenen Blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // Auflisten der Blobs als <img>-Elemente im HTML-Body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

Schließen Sie das **&lt;body\>**- und das **&lt;html\>**-Element.

    stream.println("</body>");
    stream.println("</html>");

Schließen Sie die lokale Datei.

    stream.close();

Schließen Sie **MakeHTMLPage** mit einer geschweiften Klammer: **}**

Schließen Sie **StorageSample** mit einer geschweiften Klammer: **}**

Es folgt der vollständige Code für dieses Beispiel. Vergessen Sie nicht, die Platzhalterwerte **your\_account\_name** und **your\_account\_key** durch Ihren Kontonamen und Ihren Kontoschlüssel zu ersetzen.

    import com.microsoft.windowsazure.services.core.storage.*;
                import com.microsoft.windowsazure.services.blob.client.*;
                import java.io.*;
                import java.net.URISyntaxException;
                
                // Erstellen Sie ein Bild unter c:\myimages\image1.jpg bevor Sie dieses Beispiel ausführen.
    // Alternativ können Sie den Wert im FileInputStream-Konstruktor ändern
                // um einen anderen Pfad zu einer existierenden Datei anzugeben.
                public class StorageSample {
                
                    public static final String storageConnectionString = 
                            "DefaultEndpointsProtocol=http;" + 
                               "AccountName=your_account_name;" + 
                               "AccountKey=your_account_name"; 
                
                    public static void main(String[] args) 
                    {
                        try
                        {
                            CloudStorageAccount account;
                            CloudBlobClient serviceClient;
                            CloudBlobContainer container;
                            CloudBlockBlob blob;
                            
                            account = CloudStorageAccount.parse(storageConnectionString);
                            serviceClient = account.createCloudBlobClient();
                            // Der Containername muss aus Kleinbuchstaben bestehen.
                container = serviceClient.getContainerReference("gettingstarted");
                            container.createIfNotExist();
                            
                            // Anonymen Zugriff für den container festlegen.
                BlobContainerPermissions containerPermissions;
                            containerPermissions = new BlobContainerPermissions();
                            containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                            container.uploadPermissions(containerPermissions);
                
                            // Bilddatei hochladen.
                blob = container.getBlockBlobReference("image1.jpg");
                            File fileReference = new File ("c:\myimages\image1.jpg");
                            blob.upload(new FileInputStream(fileReference), fileReference.length());
                
                            // Das Bild ist nun hochgeladen.
                // HTML-Seite erstellen, die alle hochgeladenen Bilder auflistet.
                MakeHTMLPage(container);
                
                            System.out.println("Processing complete.");
                            System.out.println("Open index.html to see the images stored in your storage account.");
                
                        }
                        catch (FileNotFoundException fileNotFoundException)
                        {
                            System.out.print("FileNotFoundException encountered: ");
                            System.out.println(fileNotFoundException.getMessage());
                            System.exit(-1);
                        }
                        catch (StorageException storageException)
                        {
                            System.out.print("StorageException encountered: ");
                            System.out.println(storageException.getMessage());
                            System.exit(-1);
                        }
                        catch (URISyntaxException uriSyntaxException)
                        {
                            System.out.print("URISyntaxException encountered: ");
                            System.out.println(uriSyntaxException.getMessage());
                            System.exit(-1);
                        }
                        catch (Exception e)
                        {
                            System.out.print("Exception encountered: ");
                            System.out.println(e.getMessage());
                            System.exit(-1);
                        }
                    }
                
                    // Erstellen einer HTML-Seite, die zum Anzeigen der hochgeladenen Bilder verwendet wird.
        // Dieses Beispiel geht davon aus, dass es sich bei allen Blobs um Bilder handelt.
        public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
                {
                        PrintStream stream;
                        stream = new PrintStream(new FileOutputStream("index.html"));
                
                        // Erstellen der <html>, <header> und <body>-Elemente.
            stream.println("<html>");
                        stream.println("<header/>");
                        stream.println("<body>");
                
                        // Hochgeladene Blobs aufzählen.
            for (ListBlobItem blobItem : container.listBlobs()) {
                            // Auflisten der Blobs als <img>-Elemente im HTML-Body.
                stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
                        }
                
                        stream.println("</body>");
                
                        // <html>-Element schließen und Datei schließen.
            stream.println("</html>");
                        stream.close();
                    }
                }

Der Beispielcode lädt nicht nur eine lokale Datei in Ihren Azure-Speicher hoch, sondern erstellt auch eine lokale Datei mit dem Namen namedindex.html, die Sie in Ihrem Browser öffnen können, um Ihr hochgeladenes Bild anzuzeigen.

Der Code enthält Ihren Kontonamen und Kontoschlüssel. Stellen Sie daher sicher, dass der Code an einem sicheren Ort liegt.

So löschen Sie einen Container
------------------------------

Da der Speicher kostenpflichtig ist, sollten Sie den **gettingstarted**-Container löschen, nachdem Sie dieses Beispiel abgeschlossen haben. Verwenden Sie zum Löschen eines Containers die Methode **CloudBlobContainer.delete**:

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

Um die **CloudBlobContainer.delete**-Methode aufzurufen, müssen Sie die **CloudStorageAccount**-, **ClodBlobClient**- und **CloudBlobContainer**-Objekte auf dieselbe Weise initialisieren wie für die **createIfNotExist**-Methode gezeigt wurde. Das folgende Beispiel löscht den Container mit dem Namen **gettingstarted**.

    import com.microsoft.windowsazure.services.core.storage.*;
                import com.microsoft.windowsazure.services.blob.client.*;
                
                public class DeleteContainer {
                
                    public static final String storageConnectionString = 
                            "DefaultEndpointsProtocol=http;" + 
                               "AccountName=your_account_name;" + 
                               "AccountKey=your_account_key"; 
                
                    public static void main(String[] args) 
                    {
                        try
                        {
                            CloudStorageAccount account;
                            CloudBlobClient serviceClient;
                            CloudBlobContainer container;
                            
                            account = CloudStorageAccount.parse(storageConnectionString);
                            serviceClient = account.createCloudBlobClient();
                            // Der Containername muss aus Kleinbuchstaben bestehen.
                container = serviceClient.getContainerReference("gettingstarted");
                            container.delete();
                            
                            System.out.println("Container deleted.");
                
                        }
                        catch (StorageException storageException)
                        {
                            System.out.print("StorageException encountered: ");
                            System.out.println(storageException.getMessage());
                            System.exit(-1);
                        }
                        catch (Exception e)
                        {
                            System.out.print("Exception encountered: ");
                            System.out.println(e.getMessage());
                            System.exit(-1);
                        }
                    }
                }

Eine Übersicht über andere Blobspeicher-Klassen und Methoden finden Sie unter [Verwenden des Blob-Speicherdiensts in Python](http://www.windowsazure.com/de-de/develop/java/how-to-guides/blob-storage/).
