<properties 
	pageTitle="Datenstichproben in Azure Blob Storage | Microsoft Azure" 
	description="Erstellen von Datenstichproben im Azure-Blobspeicher" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/07/2016" 
	ms.author="sunliangms;fashah;garye;bradsev" />

#<a name="heading"></a>Datenstichproben im Azure-Blob-Speicher

## Einführung

Dieses Dokument behandelt das Erstellen von Stichproben aus Daten im Azure Blob-Speicher durch programmgesteuertes Herunterladen, um dann mit Python-Code Stichproben zu erstellen. Befolgen Sie dazu die folgenden Schritte:

**Warum eine Datenstichprobe entnehmen?** Wenn das Dataset, das Sie analysieren möchten, groß ist, sollten Sie in der Regel eine Komprimierung der Daten durchführen, um eine geringere aber immer noch repräsentative Größe zu erhalten. Dies erleichtert das Verständnis der Daten, das Durchsuchen und die Funktionsverarbeitung. Die Funktion besteht innerhalb des Cortana-Analyseprozesses darin, schnell Prototypen der Funktionen zur Datenverarbeitung und Modelle für das maschinelle Lernen zu erstellen.

Das nachstehende **Menü** enthält Links zu Themen, die beschreiben, wie Datenstichproben aus verschiedenen Speicherumgebungen erstellt werden.

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Diese Datenstichprobenaufgabe ist ein Teil des [Cortana Analytics-Prozesses (CAP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## Download und Downsampling von Daten
1. Laden Sie die Daten aus dem Azure-Blobspeicher mithilfe des Blobdiensts aus dem folgenden Python-Beispielcode herunter: 

	    from azure.storage import BlobService
    	import tables
    	
		STORAGEACCOUNTNAME= <storage_account_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		LOCALFILENAME= <local_file_name>		
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

    	#download from blob
    	t1=time.time()
    	blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    	blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    	t2=time.time()
    	print(("It takes %s seconds to download "+blobname) % (t2 - t1))

2. Lesen Sie die Daten aus der heruntergeladenen Datei in ein Pandas-DataFrame ein.

		import pandas as pd

	    #directly ready from file on disk
    	dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Erstellen Sie mit der `random.choice`-Funktion von `numpy` wie folgt Stichproben:

	    # A 1 percent sample
    	sample_ratio = 0.01 
    	sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
    	sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
    	dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

	Sie können nun mit dem DataFrame von oben mit der 1-%-Stichprobe weitere Datensuchvorgänge durchführen und Funktionen verarbeiten.

##<a name="heading"></a>Hochladen von Daten und Einlesen in Azure Machine Learning

Mit dem folgenden Beispielcode können Sie ein Downsampling der Daten durchführen und diese direkt in Azure ML verwenden:

1. Schreiben Sie den DataFrame in eine lokale Datei:

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Laden Sie die lokale Datei mit folgendem Beispielcode in ein Azure-Blob hoch:

		from azure.storage import BlobService
    	import tables

		STORAGEACCOUNTNAME= <storage_account_name>
		LOCALFILENAME= <local_file_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

	    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
	    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
	    
	    try:
	   
	    #perform upload
	    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
	    
	    except:	        
		    print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. Lesen Sie die Daten wie in der folgenden Abbildung dargestellt mit dem in Azure ML-[Reader](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) aus dem Azure-Blob aus:
 
![Reader-Blob][1]

[1]: ./media/machine-learning-data-science-sample-data-blob/reader_blob.png


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

<!---HONumber=AcomDC_0211_2016-->