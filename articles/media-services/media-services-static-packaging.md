<properties 
	pageTitle="Ausführen von Aufgaben zur statischen Paketerstellung mithilfe von Azure Media Packager" 
	description="In diesem Thema werden verschiedene Aufgaben erläutert, die mit Azure Media Packager ausgeführt werden." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/10/2015"   
	ms.author="juliako"/>


# Ausführen von Aufgaben zur statischen Paketerstellung mithilfe von Azure Media Packager

>[AZURE.NOTE]Das Ende der Nutzung von Windows Azure Media Packager und Windows Azure Media Encryptor ist am 1. März 2016 erreicht. Ab diesem Zeitpunkt sind diese Komponenten nicht mehr verfügbar. Die Funktionen zur Formatkonvertierung und Verschlüsselung stehen dann über die dynamische Paketerstellung und die dynamische Verschlüsselung zur Verfügung.

## Übersicht

Um digitale Videos über das Internet zu übermitteln, müssen Sie die Medien komprimieren. Digitale Videodateien sind sehr umfangreich und möglicherweise zu groß, um sie über das Internet zu übermitteln oder auf den Geräten Ihrer Kunden ordnungsgemäß wiederzugeben. Bei der Codierung werden Video- und Audiodaten komprimiert, damit Ihre Kunden Ihre Medien anzeigen können. Nach der Codierung eines Videos kann dieses in verschiedenen Dateicontainern abgelegt werden. Der Vorgang des Ablegens codierter Medien in einem Container wird als Paketerstellung bezeichnet. Sie können beispielsweise eine MP4-Datei mithilfe von Azure Media Packager in Smooth Streaming- oder HLS-Inhalt konvertieren. Weitere Informationen finden Sie unter [Encoding versus Packaging](http://blog-ndrouin.azurewebsites.net/streaming-media-terminology-explained/) (in englischer Sprache).

Media Services unterstützt die dynamische und die statische Paketerstellung. Bei der statischen Paketerstellung müssen Sie eine Kopie Ihrer Inhalte in allen von Ihren Kunden benötigten Formaten erstellen. Bei der dynamischen Paketerstellung müssen Sie lediglich ein Asset erstellen, das einen Satz von MP4- oder Smooth Streaming-Dateien mit adaptiver Bitrate enthält. Dann wird durch den Server für das On-Demand-Streaming auf Basis des in der Manifest- oder Fragmentanforderung angegebenen Formats sichergestellt, dass Benutzer den Datenstrom im ausgewählten Protokoll erhalten. So müssen Sie die Dateien nur in einem Speicherformat speichern und bezahlen. Die entsprechende Antwort wird von Media Services basierend auf Clientanforderungen erstellt und verfügbar gemacht.

>[AZURE.NOTE]Es wird empfohlen, die [dynamische Paketerstellung](media-services-dynamic-packaging-overview.md) zu verwenden.

In einigen Szenarios ist jedoch die statische Paketerstellung erforderlich:

- Überprüfen von MP4-Dateien mit adaptiver Bitrate, die mit externen Encodern codiert wurden (z. B. mit Encodern von Drittanbietern).

Darüber hinaus können Sie mit der statischen Paketerstellung die folgenden Aufgaben ausführen. Dennoch wird empfohlen, die dynamische Verschlüsselung zu verwenden.

- Verwenden der statischen Verschlüsselung zum Schützen von Smooth Streaming und MPEG DASH mit PlayReady
- Verwenden der statischen Verschlüsselung zum Schützen von HLSv3 mit AES-128
- Verwenden der statischen Verschlüsselung zum Schützen von HLSv3 mit PlayReady


## Überprüfen von MP4-Dateien mit adaptiver Bitrate, die mit externen Encodern codiert wurden

Wenn Sie einen Satz MP4-Dateien mit adaptiver Bitrate (Multi-Bitrate) verwenden möchten, die nicht mit Media Services Encoder codiert wurden, sollten Sie die Dateien vor der Weiterverarbeitung überprüfen. Mit Media Services Packager kann ein Asset, das einen Satz MP4-Dateien enthält, überprüft werden, und es kann festgestellt werden, ob das Asset in Smooth Streaming oder HLS gepackt werden kann. Wenn die Überprüfungsaufgabe fehlschlägt, wird der Auftrag, mit dem die Aufgabe verarbeitet wurde, mit einem Fehler abgeschlossen. Den XML-Code, mit dem die Voreinstellung für die Überprüfungsaufgabe definiert wird, finden Sie im Thema [Taskvoreinstellung für Azure Media Packager](http://msdn.microsoft.com/library/azure/hh973635.aspx).

>[AZURE.NOTE]Verwenden Sie Media Services Encoder zum Generieren oder Media Services Packager zum Überprüfen Ihrer Inhalte, um Laufzeitprobleme zu vermeiden. Wenn der Server für das On-Demand-Streaming Ihre Quelldateien zur Laufzeit nicht analysieren kann, wird der HTTP 1.1-Fehler "415 Unsupported Media Type" ausgegeben. Wenn der Server wiederholt Ihre Quelldateien nicht analysieren kann, wird die Leistung des Servers für das On-Demand-Streaming beeinträchtigt und möglicherweise die verfügbare Bandbreite zur Verarbeitung anderer Anforderungen reduziert. Azure Media Services bietet eine Vereinbarung zum Servicelevel (SLA) für seine On-Demand-Streamingdienste. Diese SLA kann jedoch nicht erfüllt werden, wenn der Server auf die oben beschriebene Weise missbräuchlich verwendet wird.

In diesem Abschnitt wird die Verarbeitung der Überprüfungsaufgabe erläutert. Zudem wird beschrieben, wie der Status und die Fehlermeldung des Auftrags angezeigt werden, der mit "JobStatus.Error" abgeschlossen wird.

Zum Überprüfen Ihrer MP4-Dateien mit Media Services Packager müssen Sie eine eigene Manifestdatei (ISM) erstellen und zusammen mit den Quelldateien in das Media Services-Konto hochladen. Im Folgenden ist ein Beispiel für die mit Azure Media Encoder generierte ISM-Datei dargestellt. Bei den Dateinamen wird zwischen Groß- und Kleinschreibung unterschieden. Stellen Sie außerdem sicher, dass der Text in der ISM-Datei mit UTF-8 codiert ist.

	
	<?xml version="1.0" encoding="utf-8" standalone="yes"?>
	<smil xmlns="http://www.w3.org/2001/SMIL20/Language">
	  <head>
	<!-- Tells the server that these input files are MP4s – specific to Dynamic Packaging -->
	    <meta name="formats" content="mp4" /> 
	  </head>
	  <body>
	    <switch>
	      <video src="BigBuckBunny_1000.mp4" />
	      <video src="BigBuckBunny_1500.mp4" />
	      <video src="BigBuckBunny_2250.mp4" />
	      <video src="BigBuckBunny_3400.mp4" />
	      <video src="BigBuckBunny_400.mp4" />
	      <video src="BigBuckBunny_650.mp4" />
	      <audio src="BigBuckBunny_400.mp4" />
	    </switch>
	  </body>
	</smil>

Wenn Sie die MP4-Datei mit adaptiver Bitrate festgelegt haben, können Sie die dynamische Paketerstellung nutzen. Mit der dynamischen Paketerstellung können Datenströme ohne weitere Paketerstellung im angegebenen Protokoll übermittelt werden. Weitere Informationen finden Sie unter [Dynamische Paketerstellung](media-services-dynamic-packaging-overview.md).

Im folgenden Codebeispiel werden Azure Media Services .NET SDK-Erweiterungen verwendet. Ändern Sie den Code so, dass er auf den Ordner verweist, in dem sich Ihre MP4-Eingabedateien und die ISM-Datei befinden. Zudem muss er auf den Ordner verweisen, in dem sich die Datei "MediaPackager\_ValidateTask.xml" befindet. Diese XML-Datei wird im Thema [Taskvoreinstellung für Azure Media Packager](http://msdn.microsoft.com/library/azure/hh973635.aspx) definiert.
	
	using Microsoft.WindowsAzure.MediaServices.Client;
	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.IO;
	using System.Linq;
	using System.Text;
	using System.Threading;
	using System.Threading.Tasks;
	using System.Xml.Linq;
	
	namespace MediaServicesStaticPackaging
	{
	    class Program
	    {
	        private static readonly string _mediaFiles =
	            Path.GetFullPath(@"../..\Media");
	
	        // The MultibitrateMP4Files folder should also
	        // contain the .ism manifest file.
	        private static readonly string _multibitrateMP4s =
	            Path.Combine(_mediaFiles, @"MultibitrateMP4Files");
	
	        // XML Configruation files path.
	        private static readonly string _configurationXMLFiles = @"../..\Configurations";
	
	        private static MediaServicesCredentials _cachedCredentials = null;
	        private static CloudMediaContext _context = null;
	
	        // Media Services account information.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServicesAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	            // Use the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	
	            // Ingest a set of multibitrate MP4s.
	            //
	            // Use the SDK extension method to create a new asset by 
	            // uploading files from a local directory.
	            IAsset multibitrateMP4sAsset = _context.Assets.CreateFromFolder(
	                _multibitrateMP4s,
	                AssetCreationOptions.None,
	                (af, p) =>
	                {
	                    Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
	                });
	
	            // Use Azure Media Packager to validate the files.
	            IAsset validatedMP4s =
	                ValidateMultibitrateMP4s(multibitrateMP4sAsset);
	
	            // Publish the asset.
	            _context.Locators.Create(
	                LocatorType.OnDemandOrigin,
	                validatedMP4s,
	                AccessPermissions.Read,
	                TimeSpan.FromDays(30));
	
	                                 // Get the streaming URLs.
	            Console.WriteLine("Smooth Streaming URL:");
	            Console.WriteLine(validatedMP4s.GetSmoothStreamingUri().ToString());
	            Console.WriteLine("MPEG DASH URL:");
	            Console.WriteLine(validatedMP4s.GetMpegDashUri().ToString());
	            Console.WriteLine("HLS URL:");
	            Console.WriteLine(validatedMP4s.GetHlsUri().ToString());
	        }
	
	        public static IAsset ValidateMultibitrateMP4s(IAsset multibitrateMP4sAsset)
	        {
	            // Set .ism as a primary file 
	            // in a multibitrate MP4 set.
	            SetISMFileAsPrimary(multibitrateMP4sAsset);
	
	            // Create a new job.
	            IJob job = _context.Jobs.Create("MP4 validation and converstion to Smooth Stream job.");
	
	            // Read the task configuration data into a string. 
	            string configMp4Validation = File.ReadAllText(Path.Combine(
	                    _configurationXMLFiles,
	                    "MediaPackager_ValidateTask.xml"));
	
	            // Get the SDK extension method to  get a reference to the Azure Media Packager.
	            IMediaProcessor processor = _context.MediaProcessors.GetLatestMediaProcessorByName(
	                MediaProcessorNames.AzureMediaPackager);
	
	            // Create a task with the conversion details, using the configuration data. 
	            ITask task = job.Tasks.AddNew("Mp4 Validation Task",
	                processor,
	                configMp4Validation,
	                TaskOptions.None);
	
	            // Specify the input asset to be validated.
	            task.InputAssets.Add(multibitrateMP4sAsset);
	
	            // Add an output asset to contain the results of the job. 
	            // This output is specified as AssetCreationOptions.None, which 
	            // means the output asset is in the clear (unencrypted). 
	            task.OutputAssets.AddNew("Validated output asset",
	                    AssetCreationOptions.None);
	
	            // Submit the job and wait until it is completed.
	            job.Submit();
	            job = job.StartExecutionProgressTask(
	                j =>
	                {
	                    Console.WriteLine("Job state: {0}", j.State);
	                    Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
	                },
	                CancellationToken.None).Result;
	          
	            // If the validation task fails and job completes with JobState.Error,
	            // display the error message and throw an exception.
	            if (job.State == JobState.Error)
	            {
	                Console.WriteLine("  Job ID: " + job.Id);
	                Console.WriteLine("  Name: " + job.Name);
	                Console.WriteLine("  State: " + job.State);
	
	                foreach (var jobTask in job.Tasks)
	                {
	                    Console.WriteLine("  Task Id: " + jobTask.Id);
	                    Console.WriteLine("  Name: " + jobTask.Name);
	                    Console.WriteLine("  Progress: " + jobTask.Progress);
	                    Console.WriteLine("  Configuration: " + jobTask.Configuration);
	                    Console.WriteLine("  Running time: " + jobTask.RunningDuration);
	                    if (jobTask.ErrorDetails != null)
	                    {
	                        foreach (var errordetail in jobTask.ErrorDetails)
	                        {
	
	                            Console.WriteLine("  Error Message:" + errordetail.Message);
	                            Console.WriteLine("  Error Code:" + errordetail.Code);
	                        }
	                    }
	                }
	                throw new Exception("The specified multi-bitrate MP4 set is not valid.");
	            }
	
	
	            return job.OutputMediaAssets[0];
	        }
	
	        static void SetISMFileAsPrimary(IAsset asset)
	        {
	            var ismAssetFiles = asset.AssetFiles.ToList().
	                Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).ToArray();
	
	            // The following code assigns the first .ism file as the primary file in the asset.
	            // An asset should have one .ism file.  
	            ismAssetFiles.First().IsPrimary = true;
	            ismAssetFiles.First().Update();
	        }
	    }
	}

## Verwenden der statischen Verschlüsselung zum Schützen von Smooth Streaming und MPEG DASH mit PlayReady

Sie können wahlweise die [dynamische Verschlüsselung](media-services-protect-with-drm.md) (empfohlene Option) oder die statische Verschlüsselung (wie in diesem Abschnitt beschrieben) verwenden, wenn Sie Ihre Inhalte mit PlayReady schützen möchten.

Im Beispiel in diesem Abschnitt wird eine Zwischendatei (in diesem Fall eine MP4-Datei) in MP4-Dateien mit adaptiver Bitrate codiert. Anschließend werden die MP4-Dateien als Smooth Streaming gepackt, und Smooth Streaming wird mit PlayReady verschlüsselt. Damit sind Sie in der Lage, Smooth Streaming oder MPEG DASH zu streamen.

Media Services bietet jetzt einen Dienst für die Bereitstellung von Microsoft PlayReady-Lizenzen. Das Beispiel in diesem Artikel zeigt, wie der Media Services PlayReady-Lizenzbereitstellungsdienst konfiguriert wird (siehe die im Code unten definierte "ConfigureLicenseDeliveryService"-Methode). Weitere Informationen zum Media Services PlayReady-Lizenzbereitstellungsdienst finden Sie unter [Verwenden der dynamischen PlayReady-DRM-Verschlüsselung und des Lizenzübermittlungsdiensts](media-services-protect-with-drm.md).

>[AZURE.NOTE]Stellen Sie für die Bereitstellung von mit PlayReady verschlüsseltem MPEG DASH sicher, dass Sie CENC-Optionen verwenden, indem Sie die Eigenschaften "useSencBox" und "adjustSubSamples" (im Thema [Voreinstellungen für Aufgaben für Azure Media Encryptor](http://msdn.microsoft.com/library/azure/hh973610.aspx) erläutert) auf "true" festlegen.


Ändern Sie den folgenden Code so, dass er auf den Ordner verweist, in dem sich Ihre MP4-Eingabedatei befindet.

Außerdem muss der Code auf den Speicherort Ihrer Dateien "MediaPackager\_MP4ToSmooth.xml" und "MediaEncryptor\_PlayReadyProtection.xml" verweisen. Die Datei "MediaPackager\_MP4ToSmooth.xml" wird im Thema [Taskvoreinstellung für Azure Media Packager](http://msdn.microsoft.com/library/azure/hh973635.aspx) und die Datei "MediaEncryptor\_PlayReadyProtection.xml" wird im Thema [Voreinstellungen für Aufgaben für Azure Media Encryptor](http://msdn.microsoft.com/library/azure/hh973610.aspx) definiert.

Im Beispiel wird die "UpdatePlayReadyConfigurationXMLFile"-Methode definiert, die Sie für die dynamische Aktualisierung der Datei "MediaEncryptor\_PlayReadyProtection.xml" verwenden können. Wenn der Schlüsselwert verfügbar ist, können Sie die "CommonEncryption.GeneratePlayReadyContentKey"-Methode zum Generieren des Inhaltsschlüssels basierend auf den Werten "keySeedValue" und "KeyId" verwenden.

	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.IO;
	using System.Linq;
	using System.Text;
	using System.Threading;
	using System.Threading.Tasks;
	using Microsoft.WindowsAzure.MediaServices.Client;
	using System.Xml.Linq;
	using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
	using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
	
	namespace PlayReadyStaticEncryptAndKeyDeliverySvc
	{
	    class Program
	    {
	       
	        private static readonly string _mediaFiles =
	            Path.GetFullPath(@"../..\Media");
	
	        private static readonly string _singleMP4File =
	            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
	
	        // XML Configruation files path.
	        private static readonly string _configurationXMLFiles = @"../..\Configurations";
	
	
	        private static MediaServicesCredentials _cachedCredentials = null;
	        private static CloudMediaContext _context = null;
	
	        // Media Services account information.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServiceAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServiceAccountKey"];
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	            // Use the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	
	            // Encoding and encrypting assets //////////////////////
	            // Load a single MP4 file.
	            IAsset asset = IngestSingleMP4File(_singleMP4File, AssetCreationOptions.None);
	
	            // Encode an MP4 file to a set of multibitrate MP4s.
	            // Then, package a set of MP4s to clear Smooth Streaming.
	            IAsset clearSmoothStreamAsset =
	                ConvertMP4ToMultibitrateMP4sToSmoothStreaming(asset);
	
	            // Create a common encryption content key that is used 
	            // a) to set the key values in the MediaEncryptor_PlayReadyProtection.xml file
	            //    that is used for encryption.
	            // b) to configure the license delivery service and 
	            //
	            Guid keyId;
	            byte[] contentKey;
	
	            IContentKey key = CreateCommonEncryptionKey(out keyId, out contentKey);
	
	            // The content key authorization policy must be configured by you 
	            // and met by the client in order for the PlayReady license
	            // to be delivered to the client. 
	            // In this example the Media Services PlayReady license delivery service is used.
	            ConfigureLicenseDeliveryService(key);
	
	            // Get the Media Services PlayReady license delivery URL.
	            // This URL will be assigned to the licenseAcquisitionUrl property 
	            // of the MediaEncryptor_PlayReadyProtection.xml file.
	            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
	
	            // Update the MediaEncryptor_PlayReadyProtection.xml file with the key and URL info.
	            UpdatePlayReadyConfigurationXMLFile(keyId, contentKey, acquisitionUrl);
	
	
	            // Encrypt your clear Smooth Streaming to Smooth Streaming with PlayReady.
	            IAsset outputAsset = CreateSmoothStreamEncryptedWithPlayReady(clearSmoothStreamAsset);
	
	
	            // You can use the http://smf.cloudapp.net/healthmonitor player 
	            // to test the smoothStreamURL URL.
	            string smoothStreamURL = outputAsset.GetSmoothStreamingUri().ToString();
	            Console.WriteLine("Smooth Streaming URL:");
	            Console.WriteLine(smoothStreamURL);
	
	            // You can use the http://dashif.org/reference/players/javascript/ player 
	            // to test the dashURL URL.
	            string dashURL = outputAsset.GetMpegDashUri().ToString();
	            Console.WriteLine("MPEG DASH URL:");
	            Console.WriteLine(dashURL);
	        }
	
	        /// <summary>
	        /// Creates a job with 2 tasks: 
	        /// 1 task - encodes a single MP4 to multibitrate MP4s,
	        /// 2 task - packages MP4s to Smooth Streaming.
	        /// </summary>
	        /// <returns>The output asset.</returns>
	        public static IAsset ConvertMP4ToMultibitrateMP4sToSmoothStreaming(IAsset asset)
	        {
	            // Create a new job.
	            IJob job = _context.Jobs.Create("Convert MP4 to Smooth Streaming.");
	
	            // Add task 1 - Encode single MP4 into multibitrate MP4s.
	            IAsset MP4sAsset = EncodeMP4IntoMultibitrateMP4sTask(job, asset);
	            // Add task 2 - Package a multibitrate MP4 set to Clear Smooth Stream.
	            IAsset packagedAsset = PackageMP4ToSmoothStreamingTask(job, MP4sAsset);
	
	            // Submit the job and wait until it is completed.
	            job.Submit();
	            job = job.StartExecutionProgressTask(
	                j =>
	                {
	                    Console.WriteLine("Job state: {0}", j.State);
	                    Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
	                },
	                CancellationToken.None).Result;
	
	            // Get the output asset that contains the Smooth Streaming asset.
	            return job.OutputMediaAssets[1];
	        }
	
	        /// <summary>
	        /// Encrypts Smooth Stream with PlayReady.
	        /// Then creates a Smooth Streaming Url.
	        /// </summary>
	        /// <param name="clearSmoothAsset">Asset that contains clear Smooth Streaming.</param>
	        /// <returns>The output asset.</returns>
	        public static IAsset CreateSmoothStreamEncryptedWithPlayReady(IAsset clearSmoothStreamAsset)
	        {
	            // Create a job.
	            IJob job = _context.Jobs.Create("Encrypt to PlayReady Smooth Streaming.");
	
	            // Add task 1 - Encrypt Smooth Streaming with PlayReady 
	            IAsset encryptedSmoothAsset =
	                EncryptSmoothStreamWithPlayReadyTask(job, clearSmoothStreamAsset);
	
	            // Submit the job and wait until it is completed.
	            job.Submit();
	            job = job.StartExecutionProgressTask(
	                j =>
	                {
	                    Console.WriteLine("Job state: {0}", j.State);
	                    Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
	                },
	                CancellationToken.None).Result;
	
	            // The OutputMediaAssets[0] contains the desired asset.
	            _context.Locators.Create(
	                LocatorType.OnDemandOrigin,
	                job.OutputMediaAssets[0],
	                AccessPermissions.Read,
	                TimeSpan.FromDays(30));
	
	            return job.OutputMediaAssets[0];
	        }
	
	        /// <summary>
	        /// Create a common encryption content key that is used 
	        /// to set the key values in the MediaEncryptor_PlayReadyProtection.xml file
	        /// that is used for encryption.
	        /// </summary>
	        /// <param name="keyId"></param>
	        /// <param name="contentKey"></param>
	        /// <returns></returns>
	        public static IContentKey CreateCommonEncryptionKey(out Guid keyId, out byte[] contentKey)
	        {
	            keyId = Guid.NewGuid();
	            contentKey = GetRandomBuffer(16);
	
	            IContentKey key = _context.ContentKeys.Create(
	                                    keyId,
	                                    contentKey,
	                                    "ContentKey",
	                                    ContentKeyType.CommonEncryption);
	
	            return key;
	        }
	
	        /// <summary>
	        /// Update your configuration .xml file dynamically.
	        /// </summary>
	        public static void UpdatePlayReadyConfigurationXMLFile(Guid keyId, byte[] keyValue, Uri licenseAcquisitionUrl)
	        {
	            string xmlFileName = Path.Combine(_configurationXMLFiles,
	                                        @"MediaEncryptor_PlayReadyProtection.xml");
	
	            XNamespace xmlns = "http://schemas.microsoft.com/iis/media/v4/TM/TaskDefinition#";
	
	            // Prepare the encryption task template
	            XDocument doc = XDocument.Load(xmlFileName);
	
	            var licenseAcquisitionUrlEl = doc
	                    .Descendants(xmlns + "property")
	                    .Where(p => p.Attribute("name").Value == "licenseAcquisitionUrl")
	                    .FirstOrDefault();
	            var contentKeyEl = doc
	                    .Descendants(xmlns + "property")
	                    .Where(p => p.Attribute("name").Value == "contentKey")
	                    .FirstOrDefault();
	            var keyIdEl = doc
	                    .Descendants(xmlns + "property")
	                    .Where(p => p.Attribute("name").Value == "keyId")
	                    .FirstOrDefault();
	
	            // Update the "value" property.
	            if (licenseAcquisitionUrlEl != null)
	                licenseAcquisitionUrlEl.Attribute("value").SetValue(licenseAcquisitionUrl.ToString());
	
	            if (contentKeyEl != null)
	                contentKeyEl.Attribute("value").SetValue(Convert.ToBase64String(keyValue));
	
	            if (keyIdEl != null)
	                keyIdEl.Attribute("value").SetValue(keyId);
	
	            doc.Save(xmlFileName);
	        }
	
	        /// <summary>
	        /// Uploads a single file.
	        /// </summary>
	        /// <param name="fileDir">The location of the files.</param>
	        /// <param name="assetCreationOptions">
	        ///  You can specify the following encryption options for the AssetCreationOptions.
	        ///      None:  no encryption.  
	        ///      StorageEncrypted: storage encryption. Encrypts a clear input file 
	        ///        before it is uploaded to Azure storage. 
	        ///      CommonEncryptionProtected: for Common Encryption Protected (CENC) files. 
	        ///        For example, a set of files that are already PlayReady encrypted. 
	        ///      EnvelopeEncryptionProtected: for HLS with AES encryption files.
	        ///        NOTE: The files must have been encoded and encrypted by Transform Manager. 
	        ///     </param>
	        /// <returns>Returns an asset that contains a single file.</returns>
	        /// </summary>
	        /// <returns></returns>
	        private static IAsset IngestSingleMP4File(string fileDir, AssetCreationOptions assetCreationOptions)
	        {
	            // Use the SDK extension method to create a new asset by 
	            // uploading a mezzanine file from a local path.
	            IAsset asset = _context.Assets.CreateFromFile(
	                fileDir,
	                assetCreationOptions,
	                (af, p) =>
	                {
	                    Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
	                });
	
	            return asset;
	        }
	
	        /// <summary>
	        /// Creates a task to encode to Adaptive Bitrate. 
	        /// Adds the new task to a job.
	        /// </summary>
	        /// <param name="job">The job to which to add the new task.</param>
	        /// <param name="asset">The input asset.</param>
	        /// <returns>The output asset.</returns>
	        private static IAsset EncodeMP4IntoMultibitrateMP4sTask(IJob job, IAsset asset)
	        {
	            // Get the SDK extension method to  get a reference to the Azure Media Encoder.
	            IMediaProcessor encoder = _context.MediaProcessors.GetLatestMediaProcessorByName(
	                MediaProcessorNames.AzureMediaEncoder);
	
	            ITask adpativeBitrateTask = job.Tasks.AddNew("MP4 to Adaptive Bitrate Task",
	               encoder,
	               "H264 Adaptive Bitrate MP4 Set 720p",
	               TaskOptions.None);
	
	            // Specify the input Asset
	            adpativeBitrateTask.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job. 
	            // This output is specified as AssetCreationOptions.None, which 
	            // means the output asset is in the clear (unencrypted).
	            IAsset abrAsset = adpativeBitrateTask.OutputAssets.AddNew("Multibitrate MP4s",
	                                    AssetCreationOptions.None);
	
	            return abrAsset;
	        }
	
	        /// <summary>
	        /// Creates a task to convert the MP4 file(s) to a Smooth Streaming asset.
	        /// Adds the new task to a job.
	        /// </summary>
	        /// <param name="job">The job to which to add the new task.</param>
	        /// <param name="asset">The input asset.</param>
	        /// <returns>The output asset.</returns>
	        private static IAsset PackageMP4ToSmoothStreamingTask(IJob job, IAsset asset)
	        {
	            // Get the SDK extension method to  get a reference to the Azure Media Packager.
	            IMediaProcessor packager = _context.MediaProcessors.GetLatestMediaProcessorByName(
	                MediaProcessorNames.WindowsAzureMediaPackager);
	
	            // Azure Media Packager does not accept string presets, so load xml configuration
	            string smoothConfig = File.ReadAllText(Path.Combine(
	                        _configurationXMLFiles,
	                        "MediaPackager_MP4toSmooth.xml"));
	
	            // Create a new Task to convert adaptive bitrate to Smooth Streaming.
	            ITask smoothStreamingTask = job.Tasks.AddNew("MP4 to Smooth Task",
	               packager,
	               smoothConfig,
	               TaskOptions.None);
	
	            // Specify the input Asset, which is the output Asset from the first task
	            smoothStreamingTask.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job. 
	            // This output is specified as AssetCreationOptions.None, which 
	            // means the output asset is in the clear (unencrypted).
	            IAsset smoothOutputAsset =
	                smoothStreamingTask.OutputAssets.AddNew("Clear Smooth Stream",
	                    AssetCreationOptions.None);
	
	            return smoothOutputAsset;
	        }
	
	
	        /// <summary>
	        /// Creates a task to encrypt Smooth Streaming with PlayReady.
	        /// Note: To deliver DASH, make sure to set the useSencBox and adjustSubSamples 
	        /// configuration properties to true. 
	        /// In this example, MediaEncryptor_PlayReadyProtection.xml contains configuration.
	        /// </summary>
	        /// <param name="job">The job to which to add the new task.</param>
	        /// <param name="asset">The input asset.</param>
	        /// <returns>The output asset.</returns>
	        private static IAsset EncryptSmoothStreamWithPlayReadyTask(IJob job, IAsset asset)
	        {
	            // Get the SDK extension method to  get a reference to the Azure Media Encryptor.
	            IMediaProcessor playreadyProcessor = _context.MediaProcessors.GetLatestMediaProcessorByName(
	                MediaProcessorNames.WindowsAzureMediaEncryptor);
	
	            // Read the configuration XML.
	            //
	            // Note that the configuration defined in MediaEncryptor_PlayReadyProtection.xml
	            // is using keySeedValue. It is recommended that you do this only for testing 
	            // and not in production. For more information, see 
	            // http://msdn.microsoft.com/library/windowsazure/dn189154.aspx.
	            //
	            string configPlayReady = File.ReadAllText(Path.Combine(_configurationXMLFiles,
	                                        @"MediaEncryptor_PlayReadyProtection.xml"));
	
	            ITask playreadyTask = job.Tasks.AddNew("My PlayReady Task",
	               playreadyProcessor,
	               configPlayReady,
	               TaskOptions.ProtectedConfiguration);
	
	            playreadyTask.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job. 
	            // This output is specified as AssetCreationOptions.CommonEncryptionProtected.
	            IAsset playreadyAsset = playreadyTask.OutputAssets.AddNew(
	                                            "PlayReady Smooth Streaming",
	                                            AssetCreationOptions.CommonEncryptionProtected);
	
	            return playreadyAsset;
	        }
	
	        /// <summary>
	        /// Configures authorization policy for the content key. 
	        /// </summary>
	        /// <param name="contentKey">The content key.</param>
	        static public void ConfigureLicenseDeliveryService(IContentKey contentKey)
	        {
	            // Create ContentKeyAuthorizationPolicy with Open restrictions 
	            // and create authorization policy          
	
	            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
	            {
	                new ContentKeyAuthorizationPolicyRestriction 
	                { 
	                    Name = "Open", 
	                    KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
	                    Requirements = null
	                }
	            };
	
	            // Configure PlayReady license template.
	            string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
	
	            IContentKeyAuthorizationPolicyOption policyOption =
	                _context.ContentKeyAuthorizationPolicyOptions.Create("",
	                    ContentKeyDeliveryType.PlayReadyLicense,
	                        restrictions, newLicenseTemplate);
	
	            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
	                        ContentKeyAuthorizationPolicies.
	                        CreateAsync("Deliver Common Content Key with no restrictions").
	                        Result;
	
	
	            contentKeyAuthorizationPolicy.Options.Add(policyOption);
	
	            // Associate the content key authorization policy with the content key.
	            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
	            contentKey = contentKey.UpdateAsync().Result;
	        }
	
	        static private string ConfigurePlayReadyLicenseTemplate()
	        {
	            // The following code configures PlayReady License Template using .NET classes
	            // and returns the XML string.
	
	            PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();
	            PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
	
	            responseTemplate.LicenseTemplates.Add(licenseTemplate);
	
	            return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
	        }
	
	        static private byte[] GetRandomBuffer(int length)
	        {
	            var returnValue = new byte[length];
	
	            using (var rng =
	                new System.Security.Cryptography.RNGCryptoServiceProvider())
	            {
	                rng.GetBytes(returnValue);
	            }
	
	            return returnValue;
	        }
	    }
	}

## Verwenden der statischen Verschlüsselung zum Schützen von HLSv3 mit AES-128

Wenn Sie Ihre HLS-Inhalte mit AES-128 verschlüsseln möchten, können Sie wahlweise die dynamische Verschlüsselung (empfohlene Option) oder die statische Verschlüsselung (wie in diesem Abschnitt beschrieben) verwenden. Wenn Sie die dynamische Verschlüsselung verwenden möchten, finden Sie die entsprechenden Informationen unter [Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselübermittlungsdiensts](media-services-protect-with-aes128).

>[AZURE.NOTE]Zum Konvertieren Ihrer Inhalte in HLS müssen Sie zunächst Ihre Inhalte in Smooth Streaming konvertieren bzw. codieren. Damit HLS-Inhalte mit AES verschlüsselt werden, müssen Sie zudem sicherstellen, dass die folgenden Eigenschaften in der Datei "MediaPackager\_SmoothToHLS.xml" festgelegt sind. Legen Sie die "encrypt"-Eigenschaft auf "true" fest, und legen Sie die Werte "key" und "keyuri" so fest, dass sie auf Ihren Authentifizierungs- oder Autorisierungsserver verweisen. Media Services erstellt eine Schlüsseldatei und legt sie im Assetcontainer ab. Sie sollten die Datei "/asset-containerguid/*.key" auf Ihren Server kopieren (oder Ihre eigene Schlüsseldatei erstellen) und die *.key-Datei anschließend im Assetcontainer löschen.

Im Beispiel in diesem Abschnitt wird eine Zwischendatei (in diesem Fall eine MP4-Datei) in MP4-Dateien mit mehreren Bitraten codiert. Anschließend werden die MP4-Dateien in Smooth Streaming gepackt. Smooth Streaming wird dann in HTTP Live Streaming (HLS) gepackt, das mit Advanced Encryption Standard (AES)-128-Bit-Streamverschlüsselung verschlüsselt ist. Ändern Sie den folgenden Code so, dass er auf den Ordner verweist, in dem sich Ihre MP4-Eingabedatei befindet. Außerdem muss der Code auf den Speicherort Ihrer Konfigurationsdateien "MediaPackager\_MP4ToSmooth.xml" und "MediaPackager\_SmoothToHLS.xml" verweisen. Die Definition für diese Dateien finden Sie im Thema [Taskvoreinstellung für Azure Media Packager](http://msdn.microsoft.com/library/azure/hh973635.aspx).
	
	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.IO;
	using System.Linq;
	using System.Text;
	using System.Threading;
	using System.Threading.Tasks;
	using Microsoft.WindowsAzure.MediaServices.Client;
	using System.Xml.Linq;
	
	namespace MediaServicesContentProtection
	{
	    class Program
	    {
	        // Paths to support files (within the above base path). You can use 
	        // the provided sample media files from the "SupportFiles" folder, or 
	        // provide paths to your own media files below to run these samples.
	
	        private static readonly string _mediaFiles =
	            Path.GetFullPath(@"../..\Media");
	        
	        private static readonly string _singleMP4File =
	            Path.Combine(_mediaFiles, @"SingleMP4\BigBuckBunny.mp4");
	
	        // XML Configruation files path.
	        private static readonly string _configurationXMLFiles = @"../..\Configurations";
	
	        private static MediaServicesCredentials _cachedCredentials = null;
	        private static CloudMediaContext _context = null;
	
	        // Media Services account information.
	        private static readonly string _mediaServicesAccountName = 
	            ConfigurationManager.AppSettings["MediaServiceAccountName"];
	        private static readonly string _mediaServicesAccountKey = 
	            ConfigurationManager.AppSettings["MediaServiceAccountKey"];
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName, 
	                            _mediaServicesAccountKey);
	            // Use the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	
	            // Encoding and encrypting assets //////////////////////
	
	            // Load an MP4 file.
	            IAsset asset = IngestSingleMP4File(_singleMP4File, AssetCreationOptions.None);
	
	            // Encode an MP4 file to a set of multibitrate MP4s.
	            // Then, package a set of MP4s to clear Smooth Streaming.
	            IAsset clearSmoothStreamAsset = ConvertMP4ToMultibitrateMP4sToSmoothStreaming(asset);
	
	            // Create HLS encrypted with AES.
	            IAsset HLSEncryptedWithAESAsset = CreateHLSEncryptedWithAES(clearSmoothStreamAsset);
	
	            // You can use the following player to test the HLS with AES stream.
	            // http://apps.microsoft.com/windows/app/3ivx-hls-player/f79ce7d0-2993-4658-bc4e-83dc182a0614 
	            string hlsWithAESURL = HLSEncryptedWithAESAsset.GetHlsUri().ToString();
	            Console.WriteLine("HLS with AES URL:");
	            Console.WriteLine(hlsWithAESURL);
	        }
	
	
	        /// <summary>
	        /// Creates a job with 2 tasks: 
	        /// 1 task - encodes a single MP4 to multibitrate MP4s,
	        /// 2 task - packages MP4s to Smooth Streaming.
	        /// </summary>
	        /// <returns>The output asset.</returns>
	        public static IAsset ConvertMP4ToMultibitrateMP4sToSmoothStreaming(IAsset asset)
	        {
	            // Create a new job.
	            IJob job = _context.Jobs.Create("Convert MP4 to Smooth Streaming.");
	
	            // Add task 1 - Encode single MP4 into multibitrate MP4s.
	            IAsset MP4sAsset = EncodeSingleMP4IntoMultibitrateMP4sTask(job, asset);
	            // Add task 2 - Package a multibitrate MP4 set to Clear Smooth Streaming.
	            IAsset packagedAsset = PackageMP4ToSmoothStreamingTask(job, MP4sAsset);
	
	            // Submit the job and wait until it is completed.
	            job.Submit();
	            job = job.StartExecutionProgressTask(
	                j =>
	                {
	                    Console.WriteLine("Job state: {0}", j.State);
	                    Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
	                },
	                CancellationToken.None).Result;
	
	            // Get the output asset that contains Smooth Streaming.
	            return job.OutputMediaAssets[1];
	        }
	
	        /// <summary>
	        /// Encrypts an HLS with AES-128.
	        /// </summary>
	        /// <param name="clearSmoothAsset">Asset that contains clear Smooth Streaming.</param>
	        /// <returns>The output asset.</returns>
	        public static IAsset CreateHLSEncryptedWithAES(IAsset clearSmoothStreamAsset)
	        {
	            IJob job = _context.Jobs.Create("Encrypt to HLS with AES.");
	
	            // Add task 1 - Package clear Smooth Streaming to HLS with AES.
	            PackageSmoothStreamToHLS(job, clearSmoothStreamAsset);
	
	            // Submit the job and wait until it is completed.
	            job.Submit();
	            job = job.StartExecutionProgressTask(
	                j =>
	                {
	                    Console.WriteLine("Job state: {0}", j.State);
	                    Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
	                },
	                CancellationToken.None).Result;
	
	            // The OutputMediaAssets[0] contains the desired asset.
	            _context.Locators.Create(
	                LocatorType.OnDemandOrigin,
	                job.OutputMediaAssets[0],
	                AccessPermissions.Read,
	                TimeSpan.FromDays(30));
	
	            return job.OutputMediaAssets[0];
	        }
	
	        /// <summary>
	        /// Uploads a single file.
	        /// </summary>
	        /// <param name="fileDir">The location of the files.</param>
	        /// <param name="assetCreationOptions">
	        ///  You can specify the following encryption options for the AssetCreationOptions.
	        ///      None:  no encryption.  
	        ///      StorageEncrypted: storage encryption. Encrypts a clear input file 
	        ///        before it is uploaded to Azure storage. 
	        ///      CommonEncryptionProtected: for Common Encryption Protected (CENC) files. 
	        ///        For example, a set of files that are already PlayReady encrypted. 
	        ///      EnvelopeEncryptionProtected: for HLS with AES encryption files.
	        ///        NOTE: The files must have been encoded and encrypted by Transform Manager. 
	        ///     </param>
	        /// <returns>Returns an asset that contains a single file.</returns>
	        /// </summary>
	        /// <returns></returns>
	        private static IAsset IngestSingleMP4File(string fileDir, AssetCreationOptions assetCreationOptions)
	        {
	            // Use the SDK extension method to create a new asset by 
	            // uploading a mezzanine file from a local path.
	            IAsset asset = _context.Assets.CreateFromFile(
	                fileDir,
	                assetCreationOptions,
	                (af, p) =>
	                {
	                    Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
	                });
	 
	            return asset;
	        }
	
	        /// <summary>
	        /// Creates a task to encode to Adaptive Bitrate. 
	        /// Adds the new task to a job.
	        /// </summary>
	        /// <param name="job">The job to which to add the new task.</param>
	        /// <param name="asset">The input asset.</param>
	        /// <returns>The output asset.</returns>
	        private static IAsset EncodeSingleMP4IntoMultibitrateMP4sTask(IJob job, IAsset asset)
	        {
	            // Get the SDK extension method to  get a reference to the Azure Media Encoder.
	            IMediaProcessor encoder = _context.MediaProcessors.GetLatestMediaProcessorByName(
	                MediaProcessorNames.AzureMediaEncoder);
	
	            ITask adpativeBitrateTask = job.Tasks.AddNew("MP4 to Adaptive Bitrate Task",
	               encoder,
	               "H264 Adaptive Bitrate MP4 Set 720p",
	               TaskOptions.None);
	
	            // Specify the input Asset
	            adpativeBitrateTask.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job. 
	            // This output is specified as AssetCreationOptions.None, which 
	            // means the output asset is in the clear (unencrypted).
	            IAsset abrAsset = adpativeBitrateTask.OutputAssets.AddNew("Multibitrate MP4s", 
	                                    AssetCreationOptions.None);
	
	            return abrAsset;
	        }
	
	        /// <summary>
	        /// Creates a task to convert the MP4 file(s) to a Smooth Streaming asset.
	        /// Adds the new task to a job.
	        /// </summary>
	        /// <param name="job">The job to which to add the new task.</param>
	        /// <param name="asset">The input asset.</param>
	        /// <returns>The output asset.</returns>
	        private static IAsset PackageMP4ToSmoothStreamingTask(IJob job, IAsset asset)
	        {
	            // Get the SDK extension method to  get a reference to the Azure Media Packager.
	            IMediaProcessor packager = _context.MediaProcessors.GetLatestMediaProcessorByName(
	                MediaProcessorNames.WindowsAzureMediaPackager);
	
	            // Azure Media Packager does not accept string presets, so load xml configuration
	            string smoothConfig = File.ReadAllText(Path.Combine(
	                        _configurationXMLFiles, 
	                        "MediaPackager_MP4toSmooth.xml"));
	
	            // Create a new Task to convert adaptive bitrate to Smooth Streaming.
	            ITask smoothStreamingTask = job.Tasks.AddNew("MP4 to Smooth Task",
	               packager,
	               smoothConfig,
	               TaskOptions.None);
	
	            // Specify the input Asset, which is the output Asset from the first task
	            smoothStreamingTask.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job. 
	            // This output is specified as AssetCreationOptions.None, which 
	            // means the output asset is in the clear (unencrypted).
	            IAsset smoothOutputAsset = 
	                smoothStreamingTask.OutputAssets.AddNew("Clear Smooth Streaming", 
	                    AssetCreationOptions.None);
	
	            return smoothOutputAsset;
	        }
	
	        /// <summary>
	        /// Converts Smooth Streaming to HLS.
	        /// </summary>
	        /// <param name="job">The job to which to add the new task.</param>
	        /// <param name="asset">The Smooth Streaming asset.</param>
	        /// <returns>The asset that was packaged to HLS.</returns>
	        private static IAsset PackageSmoothStreamToHLS(IJob job, IAsset smoothStreamAsset)
	        {
	            // Get the SDK extension method to  get a reference to the Azure Media Packager.
	            IMediaProcessor processor = _context.MediaProcessors.GetLatestMediaProcessorByName(
	                MediaProcessorNames.WindowsAzureMediaPackager);
	
	            // Read the configuration data into a string. 
	            // For the HLS to get encrypted with AES make sure to set the
	            // encrypt configuration property to true.
	            //
	            // In production, it is recommended to do the following:
	            //    Set a Key url for your authn/authz server.
	            //    Copy the /asset-containerguid/*.key file to your server (or craft a key file for yourself).
	            //    Delete *.key from the asset container.
	            //
	            string configuration = File.ReadAllText(Path.Combine(_configurationXMLFiles, @"MediaPackager_SmoothToHLS.xml"));
	
	            // Create a task with the encoding details, using a configuration file.
	            ITask task = job.Tasks.AddNew("My Smooth Streaming to HLS Task",
	               processor,
	               configuration,
	               TaskOptions.ProtectedConfiguration);
	
	            // Specify the input asset to be encoded.
	            task.InputAssets.Add(smoothStreamAsset);
	
	            // Add an output asset to contain the results of the job. 
	            IAsset outputAsset = 
	                task.OutputAssets.AddNew("HLS asset", AssetCreationOptions.None);
	
	
	            return outputAsset;
	        }
	    }
	}

## Verwenden der statischen Verschlüsselung zum Schützen von HLSv3 mit PlayReady

Sie können wahlweise die [dynamische Verschlüsselung](media-services-protect-with-drm.md) (empfohlene Option) oder die statische Verschlüsselung (wie in diesem Abschnitt beschrieben) verwenden, wenn Sie Ihre Inhalte mit PlayReady schützen möchten.

>[AZURE.NOTE]Zum Schützen Ihrer Inhalte mit PlayReady müssen Sie zunächst Ihre Inhalte in ein Smooth Streaming-Format konvertieren bzw. codieren.

Im Beispiel in diesem Abschnitt wird eine Zwischendatei (in diesem Fall eine MP4-Datei) in MP4-Dateien mit mehreren Bitraten codiert. Anschließend werden die MP4-Dateien als Smooth Streaming gepackt, und Smooth Streaming wird mit PlayReady verschlüsselt. Damit HLS (HTTP Live Streaming) mit PlayReady verschlüsselt werden kann, muss das PlayReady Smooth Streaming-Asset in HLS gepackt werden. In diesem Thema wird die Ausführung aller Schritte erläutert.

Media Services bietet jetzt einen Dienst für die Bereitstellung von Microsoft PlayReady-Lizenzen. Das Beispiel in diesem Artikel zeigt, wie der Media Services PlayReady-Lizenzbereitstellungsdienst konfiguriert wird (siehe die im Code unten definierte **ConfigureLicenseDeliveryService**-Methode).

Ändern Sie den folgenden Code so, dass er auf den Ordner verweist, in dem sich Ihre MP4-Eingabedatei befindet. Außerdem muss der Code auf den Speicherort Ihrer Dateien "MediaPackager\_MP4ToSmooth.xml", "MediaPackager\_SmoothToHLS.xml" und "MediaEncryptor\_PlayReadyProtection.xml" verweisen. Die Dateien "MediaPackager\_MP4ToSmooth.xml" und "MediaPackager\_SmoothToHLS.xml" werden im Thema [Taskvoreinstellung für Azure Media Packager](http://msdn.microsoft.com/library/azure/hh973635.aspx) und die Datei "MediaEncryptor\_PlayReadyProtection.xml" wird im Thema [Voreinstellungen für Aufgaben für Azure Media Encryptor](http://msdn.microsoft.com/library/azure/hh973610.aspx) definiert.
	
	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.IO;
	using System.Linq;
	using System.Text;
	using System.Threading;
	using System.Threading.Tasks;
	using Microsoft.WindowsAzure.MediaServices.Client;
	using System.Xml.Linq;
	using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
	using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
	
	namespace MediaServicesContentProtection
	{
	    class Program
	    {
	        // Paths to support files (within the above base path). You can use 
	        // the provided sample media files from the "SupportFiles" folder, or 
	        // provide paths to your own media files below to run these samples.
	
	        private static readonly string _mediaFiles =
	            Path.GetFullPath(@"../..\Media");
	
	        private static readonly string _singleMP4File =
	            Path.Combine(_mediaFiles, @"SingleMP4\BigBuckBunny.mp4");
	
	        // XML Configruation files path.
	        private static readonly string _configurationXMLFiles = @"../..\Configurations";
	
	
	        private static MediaServicesCredentials _cachedCredentials = null;
	        private static CloudMediaContext _context = null;
	
	        // Media Services account information.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServiceAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServiceAccountKey"];
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	            // Used the chached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	
	            // Load an MP4 file.
	            IAsset asset = IngestSingleMP4File(_singleMP4File, AssetCreationOptions.None);
	
	            // Encode an MP4 file to a set of multibitrate MP4s.
	            // Then, package a set of MP4s to clear Smooth Streaming.
	            IAsset clearSmoothStreamAsset = ConvertMP4ToMultibitrateMP4sToSmoothStreaming(asset);
	
	            // Create a common encryption content key that is used 
	            // a) to set the key values in the MediaEncryptor_PlayReadyProtection.xml file
	            //    that is used for encryption.
	            // b) to configure the license delivery service and 
	            //
	            Guid keyId;
	            byte[] contentKey;
	
	            IContentKey key = CreateCommonEncryptionKey(out keyId, out contentKey);
	
	            // The content key authorization policy must be configured by you 
	            // and met by the client in order for the PlayReady license
	            // to be delivered to the client. 
	            // In this example the Media Services PlayReady license delivery service is used.
	            ConfigureLicenseDeliveryService(key);
	
	            // Get the Media Services PlayReady license delivery URL.
	            // This URL will be assigned to the licenseAcquisitionUrl property 
	            // of the MediaEncryptor_PlayReadyProtection.xml file.
	            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
	
	            // Update the MediaEncryptor_PlayReadyProtection.xml file with the key and URL info.
	            UpdatePlayReadyConfigurationXMLFile(keyId, contentKey, acquisitionUrl);
	
	            // Create HLS encrypted with PlayReady.
	            IAsset playReadyHLSAsset = CreateHLSEncryptedWithPlayReady(clearSmoothStreamAsset);
	            //
	            string hlsWithPlayReadyURL = playReadyHLSAsset.GetHlsUri().ToString();
	            Console.WriteLine("HLS with PlayReady URL:");
	            Console.WriteLine(hlsWithPlayReadyURL);
	        }
	
	        /// <summary>
	        /// Creates a job with 2 tasks: 
	        /// 1 task - encodes a single MP4 to multibitrate MP4s,
	        /// 2 task - packages MP4s to Smooth Streaming.
	        /// </summary>
	        /// <returns>The output asset.</returns>
	        public static IAsset ConvertMP4ToMultibitrateMP4sToSmoothStreaming(IAsset asset)
	        {
	            // Create a new job.
	            IJob job = _context.Jobs.Create("Convert MP4 to Smooth Streaming.");
	
	            // Add task 1 - Encode single MP4 into multibitrate MP4s.
	            IAsset MP4sAsset = EncodeSingleMP4IntoMultibitrateMP4sTask(job, asset);
	            // Add task 2 - Package a multibitrate MP4 set to Clear Smooth Streaming.
	            IAsset packagedAsset = PackageMP4ToSmoothStreamingTask(job, MP4sAsset);
	
	            // Submit the job and wait until it is completed.
	            job.Submit();
	            job = job.StartExecutionProgressTask(
	                j =>
	                {
	                    Console.WriteLine("Job state: {0}", j.State);
	                    Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
	                },
	                CancellationToken.None).Result;
	
	            // Get the output asset that contains Smooth Streaming.
	            return job.OutputMediaAssets[1];
	        }
	
	        /// <summary>
	        /// Create a common encryption content key that is used 
	        /// to set the key values in the MediaEncryptor_PlayReadyProtection.xml file
	        /// that is used for encryption.
	        /// </summary>
	        /// <param name="keyId"></param>
	        /// <param name="contentKey"></param>
	        /// <returns></returns>
	        public static IContentKey CreateCommonEncryptionKey(out Guid keyId, out byte[] contentKey)
	        {
	            keyId = Guid.NewGuid();
	            contentKey = GetRandomBuffer(16);
	
	            IContentKey key = _context.ContentKeys.Create(
	                                    keyId,
	                                    contentKey,
	                                    "ContentKey",
	                                    ContentKeyType.CommonEncryption);
	
	            return key;
	        }
	
	        /// <summary>
	        /// Update your configuration .xml file dynamically.
	        /// </summary>
	        public static void UpdatePlayReadyConfigurationXMLFile(Guid keyId, byte[] keyValue, Uri licenseAcquisitionUrl)
	        {
	            string xmlFileName = Path.Combine(_configurationXMLFiles,
	                                        @"MediaEncryptor_PlayReadyProtection.xml");
	
	            XNamespace xmlns = "http://schemas.microsoft.com/iis/media/v4/TM/TaskDefinition#";
	
	            // Prepare the encryption task template
	            XDocument doc = XDocument.Load(xmlFileName);
	
	            var licenseAcquisitionUrlEl = doc
	                    .Descendants(xmlns + "property")
	                    .Where(p => p.Attribute("name").Value == "licenseAcquisitionUrl")
	                    .FirstOrDefault();
	            var contentKeyEl = doc
	                    .Descendants(xmlns + "property")
	                    .Where(p => p.Attribute("name").Value == "contentKey")
	                    .FirstOrDefault();
	            var keyIdEl = doc
	                    .Descendants(xmlns + "property")
	                    .Where(p => p.Attribute("name").Value == "keyId")
	                    .FirstOrDefault();
	
	            // Update the "value" property.
	            if (licenseAcquisitionUrlEl != null)
	                licenseAcquisitionUrlEl.Attribute("value").SetValue(licenseAcquisitionUrl.ToString());
	
	            if (contentKeyEl != null)
	                contentKeyEl.Attribute("value").SetValue(Convert.ToBase64String(keyValue));
	
	            if (keyIdEl != null)
	                keyIdEl.Attribute("value").SetValue(keyId);
	
	            doc.Save(xmlFileName);
	        }
	
	        /// <summary>
	        // Encrypts clear Smooth Streaming to Smooth Streaming with PlayReady.
	        // Then, packages the PlayReady Smooth Streaming to HLS with PlayReady.
	        /// </summary>
	        /// <param name="clearSmoothAsset">Asset that contains clear Smooth Streaming.</param>
	        /// <returns>The output asset.</returns>
	        public static IAsset CreateHLSEncryptedWithPlayReady(IAsset clearSmoothStreamAsset)
	        {
	            IJob job = _context.Jobs.Create("Encrypt to HLS with PlayReady.");
	
	            // Add task 1 - Encrypt Smooth Streaming with PlayReady 
	            IAsset encryptedSmoothAsset =
	                EncryptSmoothStreamWithPlayReadyTask(job, clearSmoothStreamAsset);
	
	            // Add task 2 - Package to HLS with PlayReady.
	            PackageSmoothStreamToHLS(job, encryptedSmoothAsset);
	
	            // Submit the job and wait until it is completed.
	            job.Submit();
	            job = job.StartExecutionProgressTask(
	                j =>
	                {
	                    Console.WriteLine("Job state: {0}", j.State);
	                    Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
	                },
	                CancellationToken.None).Result;
	
	            // Since we had two tasks, the OutputMediaAssets[1]
	            // contains the desired asset.
	            _context.Locators.Create(
	                LocatorType.OnDemandOrigin,
	                job.OutputMediaAssets[1],
	                AccessPermissions.Read,
	                TimeSpan.FromDays(30));
	
	            return job.OutputMediaAssets[1];
	        }
	
	        /// <summary>
	        /// Uploads a single file.
	        /// </summary>
	        /// <param name="fileDir">The location of the files.</param>
	        /// <param name="assetCreationOptions">
	        ///  You can specify the following encryption options for the AssetCreationOptions.
	        ///      None:  no encryption.  
	        ///      StorageEncrypted: storage encryption. Encrypts a clear input file 
	        ///        before it is uploaded to Azure storage. 
	        ///      CommonEncryptionProtected: for Common Encryption Protected (CENC) files. 
	        ///        For example, a set of files that are already PlayReady encrypted. 
	        ///      EnvelopeEncryptionProtected: for HLS with AES encryption files.
	        ///        NOTE: The files must have been encoded and encrypted by Transform Manager. 
	        ///     </param>
	        /// <returns>Returns an asset that contains a single file.</returns>
	        /// </summary>
	        /// <returns></returns>
	        private static IAsset IngestSingleMP4File(string fileDir, AssetCreationOptions assetCreationOptions)
	        {
	            // Use the SDK extension method to create a new asset by 
	            // uploading a mezzanine file from a local path.
	            IAsset asset = _context.Assets.CreateFromFile(
	                fileDir,
	                assetCreationOptions,
	                (af, p) =>
	                {
	                    Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
	                });
	
	
	            return asset;
	
	        }
	        /// <summary>
	        /// Creates a task to encode to Adaptive Bitrate. 
	        /// Adds the new task to a job.
	        /// </summary>
	        /// <param name="job">The job to which to add the new task.</param>
	        /// <param name="asset">The input asset.</param>
	        /// <returns>The output asset.</returns>
	        private static IAsset EncodeSingleMP4IntoMultibitrateMP4sTask(IJob job, IAsset asset)
	        {
	            // Get the SDK extension method to  get a reference to the Azure Media Encoder.
	            IMediaProcessor encoder = _context.MediaProcessors.GetLatestMediaProcessorByName(
	                MediaProcessorNames.AzureMediaEncoder);
	
	            ITask adpativeBitrateTask = job.Tasks.AddNew("MP4 to Adaptive Bitrate Task",
	               encoder,
	               "H264 Adaptive Bitrate MP4 Set 720p",
	               TaskOptions.None);
	
	            // Specify the input Asset
	            adpativeBitrateTask.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job. 
	            // This output is specified as AssetCreationOptions.None, which 
	            // means the output asset is in the clear (unencrypted).
	            IAsset abrAsset = adpativeBitrateTask.OutputAssets.AddNew("Multibitrate MP4s",
	                                    AssetCreationOptions.None);
	
	            return abrAsset;
	        }
	
	        /// <summary>
	        /// Creates a task to convert the MP4 file(s) to a Smooth Streaming asset.
	        /// Adds the new task to a job.
	        /// </summary>
	        /// <param name="job">The job to which to add the new task.</param>
	        /// <param name="asset">The input asset.</param>
	        /// <returns>The output asset.</returns>
	        private static IAsset PackageMP4ToSmoothStreamingTask(IJob job, IAsset asset)
	        {
	            // Get the SDK extension method to  get a reference to the Azure Media Packager.
	            IMediaProcessor packager = _context.MediaProcessors.GetLatestMediaProcessorByName(
	                MediaProcessorNames.WindowsAzureMediaPackager);
	
	            // Azure Media Packager does not accept string presets, so load xml configuration
	            string smoothConfig = File.ReadAllText(Path.Combine(
	                        _configurationXMLFiles,
	                        "MediaPackager_MP4toSmooth.xml"));
	
	            // Create a new Task to convert adaptive bitrate to Smooth Streaming.
	            ITask smoothStreamingTask = job.Tasks.AddNew("MP4 to Smooth Task",
	               packager,
	               smoothConfig,
	               TaskOptions.None);
	
	            // Specify the input Asset, which is the output Asset from the first task
	            smoothStreamingTask.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job. 
	            // This output is specified as AssetCreationOptions.None, which 
	            // means the output asset is in the clear (unencrypted).
	            IAsset smoothOutputAsset =
	                smoothStreamingTask.OutputAssets.AddNew("Clear Smooth Streaming",
	                    AssetCreationOptions.None);
	
	            return smoothOutputAsset;
	        }
	
	
	        /// <summary>
	        /// Converts Smooth Stream to HLS.
	        /// </summary>
	        /// <param name="job">The job to which to add the new task.</param>
	        /// <param name="asset">The Smooth Stream asset.</param>
	        /// <returns>The asset that was packaged to HLS.</returns>
	        private static IAsset PackageSmoothStreamToHLS(IJob job, IAsset smoothStreamAsset)
	        {
	            // Get the SDK extension method to  get a reference to the Azure Media Packager.
	            IMediaProcessor processor = _context.MediaProcessors.GetLatestMediaProcessorByName(
	                MediaProcessorNames.WindowsAzureMediaPackager);
	
	            // Read the configuration data into a string. 
	            //
	            string configuration = File.ReadAllText(
	                        Path.Combine(_configurationXMLFiles,
	                                    @"MediaPackager_SmoothToHLS.xml"));
	
	            // Create a task with the encoding details, using a configuration file.
	            ITask task = job.Tasks.AddNew("My Smooth to HLS Task",
	               processor,
	               configuration,
	               TaskOptions.ProtectedConfiguration);
	
	            // Specify the input asset to be encoded.
	            task.InputAssets.Add(smoothStreamAsset);
	
	            // Add an output asset to contain the results of the job. 
	            IAsset outputAsset =
	                task.OutputAssets.AddNew("HLS asset", AssetCreationOptions.None);
	
	
	            return outputAsset;
	        }
	
	        /// <summary>
	        /// Creates a task to encrypt Smooth Streaming with PlayReady.
	        /// Note: Do deliver DASH, make sure to set the useSencBox and adjustSubSamples 
	        /// configuration properties to true.
	        /// </summary>
	        /// <param name="job">The job to which to add the new task.</param>
	        /// <param name="asset">The input asset.</param>
	        /// <returns>The output asset.</returns>
	        private static IAsset EncryptSmoothStreamWithPlayReadyTask(IJob job, IAsset asset)
	        {
	            // Get the SDK extension method to  get a reference to the Azure Media Encryptor.
	            IMediaProcessor playreadyProcessor = _context.MediaProcessors.GetLatestMediaProcessorByName(
	                MediaProcessorNames.WindowsAzureMediaEncryptor);
	
	            // Read the configuration XML.
	            //
	            // Note that the configuration defined in MediaEncryptor_PlayReadyProtection.xml
	            // is using keySeedValue. It is recommended that you do this only for testing 
	            // and not in production. For more information, see 
	            // http://msdn.microsoft.com/library/windowsazure/dn189154.aspx.
	            //
	            string configPlayReady = File.ReadAllText(Path.Combine(_configurationXMLFiles,
	                                        @"MediaEncryptor_PlayReadyProtection.xml"));
	
	            ITask playreadyTask = job.Tasks.AddNew("My PlayReady Task",
	               playreadyProcessor,
	               configPlayReady,
	               TaskOptions.ProtectedConfiguration);
	
	            playreadyTask.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job. 
	            // This output is specified as AssetCreationOptions.CommonEncryptionProtected.
	            IAsset playreadyAsset = playreadyTask.OutputAssets.AddNew(
	                                            "PlayReady Smooth Streaming",
	                                            AssetCreationOptions.CommonEncryptionProtected);
	
	
	            return playreadyAsset;
	        }
	
	
	        /// <summary>
	        /// Configures authorization policy for the content key. 
	        /// </summary>
	        /// <param name="contentKey">The content key.</param>
	        static public void ConfigureLicenseDeliveryService(IContentKey contentKey)
	        {
	            // Create ContentKeyAuthorizationPolicy with Open restrictions 
	            // and create authorization policy          
	
	            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
	            {
	                new ContentKeyAuthorizationPolicyRestriction 
	                { 
	                    Name = "Open", 
	                    KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
	                    Requirements = null
	                }
	            };
	
	            // Configure PlayReady license template.
	            string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
	
	            IContentKeyAuthorizationPolicyOption policyOption =
	                _context.ContentKeyAuthorizationPolicyOptions.Create("",
	                    ContentKeyDeliveryType.PlayReadyLicense,
	                        restrictions, newLicenseTemplate);
	
	            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
	                        ContentKeyAuthorizationPolicies.
	                        CreateAsync("Deliver Common Content Key with no restrictions").
	                        Result;
	
	
	            contentKeyAuthorizationPolicy.Options.Add(policyOption);
	
	            // Associate the content key authorization policy with the content key.
	            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
	            contentKey = contentKey.UpdateAsync().Result;
	        }
	
	        static private string ConfigurePlayReadyLicenseTemplate()
	        {
	            // The following code configures PlayReady License Template using .NET classes
	            // and returns the XML string.
	
	            PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();
	            PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
	
	            responseTemplate.LicenseTemplates.Add(licenseTemplate);
	
	            return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
	        }
	        static private byte[] GetRandomBuffer(int length)
	        {
	            var returnValue = new byte[length];
	
	            using (var rng =
	                new System.Security.Cryptography.RNGCryptoServiceProvider())
	            {
	                rng.GetBytes(returnValue);
	            }
	
	            return returnValue;
	        }
	
	    }
	}

##Media Services-Lernpfade

Sie können sich die AMS-Lernpfade hier ansehen:

- [Media Services - Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/) (in englischer Sprache)
- [Media Services - on Demand Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/) (in englischer Sprache)

<!---HONumber=Sept15_HO3-->