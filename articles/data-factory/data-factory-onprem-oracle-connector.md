<properties 
	pageTitle="Oracle-Connector: Verschieben von Daten in und aus lokaler Oracle-Datenbank" 
	description="Informationen zum Oracle-Connector für den Data Factory-Dienst, mit dem Sie Daten in eine bzw. aus einer lokalen Oracle-Datenbank verschieben können." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>


<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2015" 
	ms.author="spelluru"/>


# Oracle-Connector: Verschieben von Daten in lokale Oracle-Datenbank 

Dieser Artikel beschreibt, wie Sie die Data Factory-Kopieraktivität zum Verschieben von Daten aus Oracle in einen anderen Datenspeicher verwenden können. Dieser Artikel baut auf dem Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit Kopieraktivität und unterstützten Datenspeicherkombinationen bietet.

## Beispiel: Kopieren von Daten aus Oracle in Azure-Blob

Das nachstehende Beispiel zeigt Folgendes:

1.	Einen verknüpften Dienst des Typs "OnPremisesOracle"
2.	Einen verknüpften Dienst des Typs "AzureStorage"
3.	Ein Eingabedataset des Typs "OracleTable" 
4.	Ein Ausgabedataset des Typs "AzureBlob"
5.	Eine Pipeline mit Kopieraktivität, die "OracleSource" als Quelle und "BlobSink" als Senke verwendet

Das Beispiel kopiert Daten stündlich aus einer Tabelle in einer lokalen Oracle-Datenbank in ein Blob. Weitere Informationen zu den verschiedenen Eigenschaften, die im folgenden Beispiel verwendet werden, werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

**Mit Oracle verknüpfter Dienst:**

	{
	  "name": "OnPremisesOracleLinkedService",
	  "properties": {
	    "type": "OnPremisesOracle",
	    "typeProperties": {
	      "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
	      "gatewayName": "<gateway name>"
	    }
	  }
	}

**Mit Azure-Blobspeicher verknüpfter Dienst:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
	    }
	  }
	}

**Oracle-Eingabedataset:**

Im Beispiel wird vorausgesetzt, dass Sie die Tabelle "MyTable" in Oracle erstellt haben, die für Zeitreihendaten eine Spalte namens "timestampcolumn" enthält.

Durch Festlegen von "external" auf "true" und Angeben der Richtlinie "externalData" wird der Data Factory angegeben, dass dies eine Tabelle ist, die für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

	{
	    "name": "OracleInput",
	    "properties": {
	        "type": "OracleTable",
	        "linkedServiceName": "OnPremisesOracleLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	           "external": true,
	        "availability": {
	            "offset": "01:00:00",
	            "interval": "1",
	            "anchorDateTime": "2014-02-27T12:00:00",
	            "frequency": "Hour"
	        },
	      "policy": {     
	           "externalData": {        
	                "retryInterval": "00:01:00",    
	                "retryTimeout": "00:10:00",       
	                "maximumRetry": 3       
	            }     
	          }
	    }
	}


**Azure-Blob-Ausgabedataset:**

Daten werden stündlich in ein neues Blob geschrieben ("frequency": "hour", "interval": 1). Ordnerpfad und Dateiname des Blobs werden basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.
	
	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}


**Pipeline mit Kopieraktivität:**

Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der oben genannten Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **RelationalSource** und der Typ **sink** auf **BlobSink** festgelegt. Die für die **oracleReaderQuery**-Eigenschaft angegebene SQL-Abfrage wählt die aus der letzten Stunde zu kopierenden Daten aus.

	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": " OracleInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "OracleSource",
	            "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}

## Eigenschaften des mit Oracle verknüpften Diensts

Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit Oracle verknüpften Dienst spezifisch sind.

Eigenschaft | Beschreibung | Erforderlich
-------- | ----------- | --------
Typ | Die "type"-Eigenschaft muss auf **OnPremisesOracle** festgelegt sein. | Ja
connectionString | Geben Sie Informationen, die zur Verbindung mit der Oracle Databaseinstanz erforderlich sind, für die Eigenschaft "connectionString" an. | Ja 
gatewayName | Name des Gateways, das zum Herstellen einer Verbindung mit dem lokalen Oracle-Server verwendet wird | Ja

## Eigenschaften des Dataset-Typs "Oracle"

Eine vollständige Liste der Abschnitte und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie Struktur, Verfügbarkeit und Richtlinie einer Dataset-JSON sind bei allen Dataset-Typen (Oracle, Azure-Blob, Azure-Tabelle usw.) ähnlich.
 
Der Abschnitt "typeProperties" unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt "typeProperties" für ein Dataset vom Typ "OracleTable" hat die folgenden Eigenschaften.

Eigenschaft | Beschreibung | Erforderlich
-------- | ----------- | --------
tableName | Name der Tabelle in der Oracle Database, auf die der verknüpfte Dienst verweist. | Ja

## Eigenschaften von Oracle-Kopieraktivitätstyp

Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen, verschiedene Richtlinien usw. sind für alle Arten von Aktivitäten verfügbar.

**Hinweis:** Die Kopieraktivität verwendet nur eine Eingabe und erzeugt nur eine Ausgabe.

Im Abschnitt "typeProperties" der Aktivität verfügbare Eigenschaften variieren hingegen bei jedem Aktivitätstyp. Bei der Kopieraktivität variieren sie je nach Typ der Quellen und Senken.

Wenn bei der Kopieraktivität "source" den Typ "SqlSource" hat, sind im Abschnitt "typeProperties" die folgenden Eigenschaften verfügbar:

Eigenschaft | Beschreibung |Zulässige Werte | Erforderlich
-------- | ----------- | ------------- | --------
oracleReaderQuery | Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. | SQL-Abfragezeichenfolge. 
Beispiel: select \* from MyTable <p>Falls nicht angegeben, die SQL-Anweisung, die ausgeführt wird: select \* from MyTable</p> | Nein

[AZURE.INCLUDE [data-factory-structure-for-rectangular-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Typzuordnung für Oracle

Wie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) beschrieben, führt die Kopieraktivität automatische Typkonvertierungen von Quelltypen in Senkentypen mithilfe des folgenden aus zwei Schritten bestehenden Ansatzes durch:

1. Konvertieren von systemeigenen Quelltypen in den .NET-Typ
2. Konvertieren vom .NET-Typ in systemeigenen Senkentyp

Beim Verschieben von Daten aus Oracle werden die folgenden Zuordnungen zwischen dem Datentyp "Oracle" und dem Typ ".NET" verwendet und umgekehrt.

Datentyp "Oracle" | Datentyp ".NET Framework"
---------------- | ------------------------
BFILE | Byte
BLOB | Byte
CHAR | String
CLOB | String
DATE | DateTime
FLOAT | Decimal
INTEGER | Decimal
INTERVAL YEAR TO MONTH | Int32
INTERVAL DAY TO SECOND | TimeSpan
LONG | String
LONG RAW | Byte
NCHAR | String
NCLOB | String
NUMBER | Decimal
NVARCHAR2 | String
RAW | Byte
ROWID | String
TIMESTAMP | DateTime
TIMESTAMP WITH LOCAL TIME ZONE | DateTime
TIMESTAMP WITH TIME ZONE | DateTime
UNSIGNED INTEGER | Number
VARCHAR2 | String
XML | String


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=August15_HO6-->