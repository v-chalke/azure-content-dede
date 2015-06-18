<properties 
	pageTitle="Erste Schritte mit Azure Storage" 
	description="" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Erste Schritte](vs-storage-aspnet-getting-started-blobs.md)
> - [Was ist passiert?](vs-storage-aspnet-what-happened.md)

## Erste Schritte mit Azure Storage (ASP.NET-Projekte)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet-getting-started-blobs.md)
> - [Warteschlangen](vs-storage-aspnet-getting-started-queues.md)
> - [Tabellen](vs-storage-aspnet-getting-started-tables.md)

Die BLOB-Speicherung in Azure ist ein Dienst zur Speicherung großer Mengen unstrukturierter Daten, auf die von überall auf der Welt über HTTP oder HTTPS zugegriffen werden kann. Ein einzelner BLOB kann eine beliebige Größe aufweisen. BLOBs können Bilder, Audio- und Videodateien, Rohdaten und Dokumentdateien enthalten.

Im ersten Schritt müssen Sie ein Azure-Speicherkonto und dann mindestens einen Container im Speicher erstellen. Sie können z. B. einen Speicher namens "Notizbuch" und dann Container im Speicher namens "Bilder" zum Speichern von Bildern und "Audio" zum Speichern von Audiodateien erstellen. Nachdem Sie die Container erstellt haben, können Sie einzelne BLOB-Dateien in diese hochladen. Weitere Informationen zum programmgesteuerten Bearbeiten von Blobs finden Sie unter [Verwenden von BLOB-Speicher aus .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/ "How to use Blob Storage from .NET").

Fügen Sie die folgenden Codenamespace-Deklarationen am Anfang aller C#-Dateien hinzu, mit denen Sie programmgesteuert auf Azure Storage zugreifen möchten.
	
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

##### Abrufen der Verbindungszeichenfolge des Speichers
Bevor Sie einen BLOB verwenden können, müssen Sie die Verbindungszeichenfolge für das Speicherkonto abrufen, in dem die BLOBs gespeichert werden. Sie können den Typ **CloudStorageAccount** verwenden, um Ihre Speicherkontoinformationen darzustellen. Für ASP.NET-Projekte können Sie den Typ **ConfigurationManager** verwenden, um Ihre Verbindungszeichenfolge für den Speicher und die Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen. Der folgende Code zeigt dies.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[AZURE.INCLUDE [vs-storage-getting-started-blobs-include](../../includes/vs-storage-getting-started-blobs-include.md)]

Weitere Informationen finden Sie unter [ASP.NET](http://www.asp.net).

<!--HONumber=42-->
 