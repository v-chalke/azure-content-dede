<properties 
	pageTitle="Erste Schritte mit Media Services im Azure-Verwaltungsportal" 
	description="Dieses Lernprogramm führt Sie durch die Schritte zum Implementieren einer Anwendung zur Video-on-Demand (VoD)-Inhaltsübermittlung mit Azure Media Services mithilfe des Azure-Verwaltungsportals." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="04/08/2015" 
	ms.author="juliako"/>


#Schnellstart: Erste Schritte mit Media Services im Azure-Verwaltungsportal

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]
> Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">1 Monat kostenlose Testversion</a>.

Dieses Lernprogramm führt Sie durch die Schritte zum Implementieren einer einfachen Anwendung zur Video-on-Demand (VoD)-Inhaltsübermittlung mithilfe des Azure-Verwaltungsportals. 

Die folgenden Aufgaben werden in diesem Schnellstart beschrieben.

1.  Erstellen eines Media Services-Kontos
2.  Konfigurieren von Streamingendpunkten
1.  Hochladen von Videodateien
1.  Codieren der Quelldatei in einen Satz von MP4-Dateien mit adaptiver Bitrate
1.  Veröffentlichen des Medienobjekts und Abrufen von URLs für Streaming und progressiven Download  
1.  Wiedergeben Ihrer Inhalte 


##Erstellen eines Media Services-Kontos

1. Klicken Sie im [Verwaltungsportal][] auf **Neu**, klicken Sie auf **Media Services** und dann auf **Schnellerfassung**.
   
	![Media Services-Schnellerfassung](./media/media-services-portal-get-started/wams-QuickCreate.png)

2. Geben Sie in das Feld **NAME** den Namen des neuen Kontos ein. Der Name eines Media Services-Kontos darf nur Kleinbuchstaben oder Ziffern ohne Leerzeichen enthalten und muss aus 3 bis 24 Zeichen bestehen. 

3. Wählen Sie unter **REGION** die geografische Region aus, in der die Metadaten-Datensätze für Ihr Media Services-Konto gespeichert werden sollen. In der Dropdownliste werden nur die verfügbaren Media Services-Regionen angezeigt. 

4. Wählen Sie unter **SPEICHERKONTO** ein Speicherkonto aus, das als Blob-Speicher für die Medieninhalte aus Ihrem Media Services-Konto dienen soll. Sie können ein vorhandenes Speicherkonto in derselben geografischen Region wie Ihr Media Services-Konto auswählen oder ein neues Speicherkonto erstellen. Ein neues Speicherkonto wird in derselben Region erstellt. 

5. Wenn Sie ein neues Speicherkonto erstellt haben, geben Sie im Feld **NEUER SPEICHERKONTONAME** einen Namen für das Speicherkonto an. Für Namen von Speicherkonten gelten die gleichen Regeln wie für Namen von Media Services-Konten.

6. Klicken Sie auf **Schnellerfassung** am unteren Rand des Formulars.

	Sie können den Status des Prozesses im Meldungsbereich unten im Fenster überwachen.

	Wenn das Konto erfolgreich erstellt wurde, ändert sich der Status in "Aktiv". 
	
	Am unteren Rand der Seite wird die Schaltfläche **SCHLÜSSEL VERWALTEN** angezeigt. Wenn Sie auf diese Schaltfläche klicken, wird ein Dialogfeld mit dem Media Services-Kontonamen und dem primären und sekundären Schlüssel angezeigt. Sie benötigen den Kontonamen und den Primärschlüssel, um programmgesteuert auf das Media Services-Konto zugreifen zu können. 

![Seite "Media Services"](./media/media-services-portal-get-started/wams-mediaservices-page.png)

	Wenn Sie auf den Kontonamen doppelklicken, wird standardmäßig die Seite "Schnellstart" angezeigt. Auf dieser Seite können Sie einige Verwaltungsaufgaben ausführen, die auch auf anderen Seiten des Portals verfügbar sind. Sie können beispielsweise eine Videodatei auf dieser Seite oder auf der Seite INHALT hochladen.

	 
##Konfigurieren von Streamingendpunkten mithilfe des Portals

Bei der Arbeit mit Azure Media Services ist eines der häufigsten Szenarios das Streaming mit adaptiver Bitrate an Clients. Beim Streaming mit adaptiver Bitrate kann der Client während der Videodarstellung abhängig von der aktuellen Netzwerkbandbreite, CPU-Auslastung und anderen Faktoren auf einen Stream mit höherer oder niedrigerer Bitrate wechseln. Media Services unterstützt die folgenden Technologien für das Streaming mit adaptiver Bitrate: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH und HDS (für Adobe PrimeTime/Access-Lizenznehmer verfügbar). 

Media Services bietet dynamische Paketerstellung zum Übermitteln Ihrer MP4-Dateien mit adaptiver Bitrate oder Smooth Streaming-codierten Inhalte in Streamingformaten, die von Media Services unterstützt werden (MPEG DASH, HLS, Smooth Streaming, HDS), ohne dass Sie diese Streamingformate erneut verpacken müssen. 

Um die dynamische Paketerstellung nutzen zu können, müssen Sie folgende Schritte ausführen:

- Codieren Ihrer Zwischendatei (Quelle) in einen Satz von MP4-Dateien oder Smooth Streaming-Dateien mit adaptiver Bitrate (die Codierungsschritte werden weiter unten in diesem Lernprogramm beschrieben)  
- Abrufen von mindestens einer Streamingeinheit für den **Streamingendpunkt**, von dem aus Sie die Bereitstellung Ihrer Inhalte planen.

Mit der dynamischen Paketerstellung müssen Sie die Dateien nur in einem Speicherformat speichern und bezahlen. Media Services erstellt und verarbeitet die entsprechende Antwort basierend auf Anforderungen von einem Client. 

Um die Anzahl der reservierten Einheiten für das Streaming zu ändern, gehen Sie folgendermaßen vor:

1. Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Media Services**. Klicken Sie anschließend auf den Namen des Mediendiensts.

2. Wählen Sie die Seite STREAMING-ENDPUNKTE aus. Klicken Sie anschließend auf den Streaming-Endpunkt, den Sie ändern möchten.

3. Um die Anzahl der Streamingeinheiten anzugeben, wählen Sie die Registerkarte "SKALIEREN" aus, und verschieben Sie anschließend den Schieberegler für die **reservierte Kapazität**.

	![Seite "Skalieren"](./media/media-services-portal-get-started/media-services-origin-scale.png)

4. Klicken Sie auf die Schaltfläche "Speichern", um die Änderungen zu speichern.

	Die Zuordnung neuer Einheiten dauert ca. 20 Minuten. 

	 
	>[AZURE.NOTE] Aktuell kann das Streaming für bis zu eine Stunde deaktiviert werden, wenn Sie einen positiven Wert für die Streamingeinheiten zurück auf null setzen.
	>
	> Die höchste für den 24-Stunden-Zeitraum angegebene Anzahl an Einheiten wird zum Berechnen der Kosten verwendet. Informationen zu den Preisen finden Sie unter [Media Services Pricing Details](http://go.microsoft.com/fwlink/?LinkId=275107).

##Hochladen von Inhalten 


1. Klicken Sie im [Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409) auf **Media Services** und anschließend auf den Media Services-Kontonamen.
2. Wählen Sie die Seite "INHALT" aus. 
3. Klicken Sie auf der Seite oder unten im Portal auf **Hochladen**. 
4. Navigieren Sie im Dialogfeld **Inhalte hochladen** zur gewünschten Medienobjektdatei. Klicken Sie auf die Datei, und klicken Sie dann auf **Öffnen**, oder drücken Sie **Eingabe**.

	![UploadContentDialog][uploadcontent]

5. Klicken Sie im Dialogfeld "Inhalte hochladen" auf das Häkchen, um die Datei und den Inhaltsnamen zu akzeptieren.
6. Das Hochladen beginnt, und Sie können den Fortschritt unten im Portal verfolgen.  

	![JobStatus][status]

Wenn der Upload abgeschlossen ist, wird in der Liste "Inhalt" das neue Medienobjekt aufgeführt. Entsprechend der Namenskonvention wird "**-Source**" an das Ende gehängt, damit neue Inhalte als Quellinhalte für Codieraufgaben erfasst werden können.

![ContentPage][contentpage]

Wenn der Wert für die Dateigröße nach dem Hochladen nicht aktualisiert wird, drücken Sie die Schaltfläche **Metadaten synchronisieren**. Damit wird die Größe der Medienobjektdatei mit der tatsächlichen Dateigröße im Speicher synchronisiert, und der Wert auf der Seite "Inhalt" wird aktualisiert.	


##Codieren von Inhalten

###Übersicht
Um digitale Videos über das Internet zu übermitteln, müssen Sie die Medien komprimieren. Media Services bietet einen Media Encoder, mit dem Sie angeben, wie Ihre Inhalte codiert werden sollen (z. B. Codecs, Dateiformat, Auflösung und Bitrate). 

Bei der Arbeit mit Azure Media Services ist eines der häufigsten Szenarios das Streaming mit adaptiver Bitrate an Clients. Beim Streaming mit adaptiver Bitrate kann der Client während der Videodarstellung abhängig von der aktuellen Netzwerkbandbreite, CPU-Auslastung und anderen Faktoren auf einen Stream mit höherer oder niedrigerer Bitrate wechseln. Media Services unterstützt die folgenden Technologien für das Streaming mit adaptiver Bitrate: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH und HDS (für Adobe PrimeTime/Access-Lizenznehmer verfügbar). 

Media Services bietet dynamische Paketerstellung zum Übermitteln Ihrer MP4-Dateien mit adaptiver Bitrate oder Smooth Streaming-codierten Inhalte in Streamingformaten, die von Media Services unterstützt werden (MPEG DASH, HLS, Smooth Streaming, HDS), ohne dass Sie diese Streamingformate erneut verpacken müssen. 

Um die dynamische Paketerstellung nutzen zu können, müssen Sie folgende Schritte ausführen:

- Codieren Ihrer Zwischendatei (Quelle) in einen Satz von MP4-Dateien oder Smooth Streaming-Dateien mit adaptiver Bitrate (die Codierungsschritte werden weiter unten in diesem Lernprogramm beschrieben)
- Abrufen von mindestens einer On-Demand-Streamingeinheit für den Streamingendpunkt, von dem aus Sie die Bereitstellung Ihrer Inhalte planen. Weitere Informationen finden Sie unter [Skalieren von reservierten Einheiten für bedarfsgesteuertes Streaming](media-services-manage-origins.md#scale_streaming_endpoints/).

Mit der dynamischen Paketerstellung müssen Sie die Dateien nur in einem Speicherformat speichern und bezahlen. Media Services erstellt und verarbeitet die entsprechende Antwort basierend auf Anforderungen von einem Client. 

Beachten Sie, dass die reservierten Einheiten für On-Demand-Streaming Ihnen zusätzlich zur dynamischen Paketerstellung auch eine dedizierte Ausgangskapazität bereitstellen, die in Schritten von 200 Mbit/s erworben werden kann. Standardmäßig wird das bedarfsgesteuerte Streaming in einem Modell mit einer gemeinsam genutzten Instanz konfiguriert, für das Serverressourcen (z. B. Rechen- und Ausgangskapazität usw.) mit allen anderen Benutzern gemeinsam genutzt werden. Um den Durchsatz des bedarfsgesteuerten Streamings zu erhöhen, sollten Sie die reservierten Einheiten für bedarfsgesteuertes Streaming kaufen.

###Codieren

Dieser Abschnitt beschreibt die Schritte, die Sie ausführen können, um Ihre Inhalte mit Azure Media Encoder im Verwaltungsportal zu codieren.

1.  Wählen Sie die Datei aus, die Sie codieren möchten.
	Wenn die Codierung für diesen Dateityp unterstützt wird, wird die Schaltfläche "Prozess" unten auf der Inhaltsseite aktiviert.
4. Wählen Sie im Dialogfeld **Prozess** den **Azure Media Encoder**-Prozessor aus.
5. Wählen Sie eine der **Codierungskonfigurationen** aus.

	![Process2][process2]

		
	Im Thema [Taskvoreinstellung für Media Services Encoder](https://msdn.microsoft.com/library/azure/dn619392.aspx) werden die Voreinstellungen in den Kategorien **Voreinstellungen für adaptives Streaming (dynamische Paketerstellung)**, **Voreinstellungen für progressiven Download** und **Legacy-Voreinstellungen für adaptives Streaming** erläutert.  


	Die **anderen** Konfigurationen werden nachfolgend beschrieben:

	+ **Codieren mit PlayReady-Inhaltsschutz**. Diese Voreinstellung produziert ein Medienobjekt, das mit dem PlayReady-Inhaltsschutz codiert ist.  
	
	
		Standardmäßig wird der Media Services PlayReady-Lizenzierungsdienst verwendet. Verwenden Sie REST oder die Media Services .NET SDK-APIs, um andere Dienste zu verwenden, von denen Clients eine Lizenz zum Abspielen der mit PlayReady verschlüsselten Inhalte abrufen können. Weitere Informationen finden Sie unter [Statische Verschlüsselung zum Schützen Ihrer Inhalte]() und durch Festlegen der **licenseAcquisitionUrl**-Eigenschaft in der Media Encryptor-Voreinstellung. Alternativ können Sie dynamische Verschlüsselung verwenden und die **PlayReadyLicenseAcquisitionUrl**-Eigenschaft laut Beschreibung unter [Verwenden dynamischer PlayReady-Verschlüsselung und des Lizenzbereitstellungsdiensts](http://go.microsoft.com/fwlink/?LinkId=507720 ) festlegen. 
	+ **Wiedergabe auf PC/Mac (über Flash/Silverlight)**. Diese Voreinstellung erzeugt ein Smooth Streaming-Medienobjekt mit den folgenden Eigenschaften: AAC-Stereo-Audio mit 44,1 kHz und 16 Bits/Sample, codiert mit konstanter Bitrate (96 Kbit/s) und 720p-Video codiert mit 6 konstanten Bitraten von 3400 Kbit/s bis 400 Kbit/s mithilfe des H.264-Hauptprofils und 2-Sekunden-GOPs.
	+ **Wiedergabe über HTML5 (IE, Chrome, Safari)**. Diese Voreinstellung erzeugt eine einzelne MP4-Datei mit den folgenden Eigenschaften: AAC-Stereo-Audio mit 44,1 kHz und 16 Bits/Sample codiert mit konstanter Bitrate (128 Kbit/s) und 720p-Video codiert mit konstanter Bitrate von 4500 Kbit/s mithilfe des H.264-Hauptprofils.
	+ **Wiedergabe auf iOS-Geräten und PC/Mac**. Diese Voreinstellung erzeugt eine Ressource mit den gleichen Eigenschaften wie die Smooth Streaming-Ressource (Beschreibung siehe oben), jedoch in einem Format, das zur Übermittlung von Apple HLS-Streams auf iOS-Geräten verwendet werden kann. 

5. Anschließend geben Sie den gewünschten Anzeigenamen für den Inhalt ein oder übernehmen den Standardnamen. Klicken Sie anschließend auf das Häkchen, um den Codiervorgang zu starten. Den Fortschritt des Vorgangs können Sie unten im Portal verfolgen.
6. Klicken Sie auf "OK".

	Nach Abschluss der Codierung enthält die Inhaltsseite die codierte Datei. 

	Um den Fortschritt des Codierungsauftrags anzuzeigen, wechseln Sie zur Seite **AUFTRÄGE**.  

	Wenn der Wert für die Dateigröße nicht aktualisiert wird, nachdem die Codierung erfolgt ist, klicken Sie auf die Schaltfläche **Sync Metadata**. Damit wird die Größe der ausgegebenen Medienobjektdatei mit der tatsächlichen Dateigröße im Speicher synchronisiert, und der Wert auf der Seite "Inhalt" wird aktualisiert.	


##Veröffentlichen von Inhalten

###Übersicht

Um Ihren Benutzern eine URL für das Streaming bzw. Herunterladen des Inhalts angeben zu können, müssen Sie zunächst das Medienobjekt "veröffentlichen", indem Sie einen Locator erstellen. Locator ermöglichen den Zugriff auf Dateien im Medienobjekt. Media Services unterstützt zwei Arten von Locatorobjekten: OnDemandOrigin-Locator, die zum Streamen von Medien (z. B. MPEG DASH, HLS oder Smooth Streaming) dienen, und Access Signature (SAS)-Locator, die zum Herunterladen von Mediendateien dienen.

Wenn Sie Ihre Medienobjekte über das Azure-Verwaltungsportal veröffentlichen, werden die Locators für Sie erstellt und eine OnDemandOrigin-basierte URL (wenn Ihr Medienobjekt eine ISM-Datei enthält) oder eine SAS-URL bereitgestellt. 

Eine SAS-URL weist das folgende Format auf:

	{blob container name}/{asset name}/{file name}/{SAS signature}

Eine Streaming-URL weist das folgende Format auf. Mit dieser URL können Sie Smooth Streaming-Medienobjekte wiedergeben.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Um eine HLS-Streaming-URL zu erstellen, fügen Sie "(format=m3u8-aapl)" an die URL an.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Um eine MPEG DASH-Streaming-URL zu erstellen, fügen Sie "(format=mpd-time-csf)" an die URL an.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Locator verfügen über ein Ablaufdatum. Wenn Sie Medienobjekte über das Portal veröffentlichen, werden Locator mit einem Ablaufdatum von 100 Jahren erstellt. 

>[AZURE.NOTE] Die vor März 2015 über das Portal erstellten Locator weisen ein Ablaufdatum von zwei Jahren auf.  

Verwenden Sie zum Aktualisieren des Ablaufdatums für einen Locator die [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator )- oder [.NET](http://go.microsoft.com/fwlink/?LinkID=533259)-APIs. Wenn Sie das Ablaufdatum eines SAS-Locators aktualisieren, ändert sich auch die URL. 

###Publish

So veröffentlichen Sie ein Medienobjekt über das Portal: 

1. Wählen Sie das Medienobjekt aus. 
2. Klicken Sie dann auf die Schaltfläche "Veröffentlichen". 
	
 ![PublishedContent][publishedcontent]


##Wiedergeben von Inhalten im Portal

Im **Azure-Verwaltungsportal** wird ein Inhaltsplayer bereitgestellt, mit dem Sie Ihre Videos testen können.

Klicken Sie auf das gewünschte Video und dann auf die Schaltfläche **Wiedergeben** unten im Portal. 
 
Folgende Überlegungen sollten berücksichtigt werden:

- Vergewissern Sie sich, dass das Video veröffentlicht wurde.
- Der **MEDIA SERVICES-INHALTSPLAYER** gibt die Inhalt vom standardmäßigen Streamingendpunkt wieder. Wenn Sie die Wiedergabe von einem anderen Streamingendpunkt starten möchten, verwenden Sie einen anderen Player. Sie können beispielsweise den [Azure Media Services-Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) nutzen.


![AMSPlayer][AMSPlayer]

###Zusätzliche Ressourcen
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure Media Services 101 - Get your video online now!</a> (in englischer Sprache)
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure Media Services 102 - Dynamic Packaging and Mobile Devices</a> (in englischer Sprache)


<!-- Anchors. -->


<!-- URLs. -->
[Verwaltungsportal]: http://manage.windowsazure.com/


<!-- Images -->
[portaloverview]: ./media/media-services-portal-get-started/media-services-content-page.png
[publishedcontent]: ./media/media-services-portal-get-started/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-portal-get-started/UploadContent.png
[status]: ./media/media-services-portal-get-started/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png
[contentpage]: ./media/media-services-portal-get-started/media-services-content-page.png
[process]: ./media/media-services-manage-content/media-services-process-video.png
[process2]: ./media/media-services-portal-get-started/media-services-process-video2.png
[encrypt]: ./media/media-services-manage-content/media-services-encrypt-content.png
[AMSPlayer]: ./media/media-services-portal-get-started/media-services-portal-player.png


<!--HONumber=52--> 