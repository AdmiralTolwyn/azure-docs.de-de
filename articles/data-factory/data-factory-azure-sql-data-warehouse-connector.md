<properties 
	pageTitle="Verschieben von Daten in/aus Azure SQL Data Warehouse | Microsoft Azure" 
	description="Erfahren Sie, wie Daten mithilfe von Azure Data Factory in und aus Azure SQL Data Warehouse verschoben werden." 
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
	ms.date="04/14/2016" 
	ms.author="spelluru"/>

# Verschieben von Daten in und aus Azure SQL Data Warehouse mithilfe von Azure Data Factory

Dieser Artikel beschreibt die Verwendung der Kopieraktivität in Azure Data Factory, um Daten aus Azure SQL Data Warehouse in einen anderen Datenspeicher und aus einem anderen Datenspeicher in Azure SQL zu verschieben. Dieser Artikel baut auf dem Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit Kopieraktivität und unterstützten Datenquellen und Senken für SQL Data Warehouse bietet.

In den folgenden Beispielen wird veranschaulicht, wie Sie Daten in und aus Azure SQL Data Warehouse und Azure-BLOB-Speicher kopieren. Daten können jedoch mithilfe der Kopieraktivität in Azure Data Factory **direkt** aus beliebigen Quellen in die im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md#supported-data-stores) aufgeführten Senken kopiert werden.

> [AZURE.NOTE] 
Eine Übersicht über den Azure Data Factory-Dienst finden Sie unter [Einführung in Azure Data Factory](data-factory-introduction.md).
> 
> Dieser Artikel bietet JSON-Beispiele, aber keine detaillierten Anleitungen zum Erstellen einer Data Factory. Im [Tutorial: Kopieren von Daten aus Azure-Blob in Azure SQL-Datenbank](data-factory-get-started.md) finden Sie eine kurze exemplarische Vorgehensweise mit detaillierten Anleitungen zur Verwendung der Kopieraktivität in Azure Data Factory.


## Beispiel: Kopieren von Daten aus Azure SQL Data Warehouse in ein Azure-Blob

Das nachstehende Beispiel zeigt Folgendes:

1. Einen verknüpften Dienst des Typs [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties)
2. Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 
3. Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties) 
4. Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)
4. Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [SqlDWSource](#azure-sql-data-warehouse-copy-activity-type-properties) und [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) verwendet

Im Beispiel werden Daten, die zu einer Zeitreihe gehören, stündlich aus einer Tabelle in einer Azure SQL Data Warehouse-Datenbank in ein Blob kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

**Mit Azure SQL Data Warehouse verknüpfter Dienst:**

	{
	  "name": "AzureSqlDWLinkedService",
	  "properties": {
	    "type": "AzureSqlDW",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Mit Azure-Blobspeicher verknüpfter Dienst:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure SQL Data Warehouse-Eingabedataset:**

Im Beispiel wird vorausgesetzt, dass Sie die Tabelle "MyTable" in Azure SQL Data Warehouse erstellt haben, die für Zeitreihendaten eine Spalte namens "timestampcolumn" enthält.
 
Durch Festlegen von "external" auf "true" und Angeben der Richtlinie "externalData" wird dem Data Factory-Dienst angegeben, dass dies eine Tabelle ist, die für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

	{
	  "name": "AzureSqlDWInput",
	  "properties": {
	    "type": "AzureSqlDWTable",
	    "linkedServiceName": "AzureSqlDWLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
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

Daten werden stündlich in ein neues Blob geschrieben ("frequency": "hour", "interval": 1). Der Ordnerpfad des Blobs wird basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.

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

Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der oben genannten Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **SqlDWSource** und der Typ **sink** auf **BlobSink** festgelegt. Die für die **SqlReaderQuery**-Eigenschaft angegebene SQL-Abfrage wählt die Daten der letzten Stunde zum Kopieren aus.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLDWtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSqlDWInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlDWSource",
	            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

> [AZURE.NOTE] Im obigen Beispiel ist **sqlReaderQuery** für "SqlSWSource" angegeben. Mit der Kopieraktivität wird diese Abfrage für die Azure SQL Data Warehouse-Quelle ausgeführt, um die Daten abzurufen.
>  
> Alternativ dazu können Sie eine gespeicherte Prozedur angeben, indem Sie **sqlReaderStoredProcedureName** und **storedProcedureParameters** angeben (sofern die gespeicherten Prozeduren Parameter verwenden).
>  
> Wenn Sie "sqlReaderQuery" oder "sqlReaderStoredProcedureName" nicht angeben, werden die im Strukturabschnitt des DataSet-JSON-Bereichs definierten Spalten verwendet, um eine Abfrage ("select column1, column2 from mytable") zur Ausführung für das SQL Data Warehouse zu erstellen. Falls die DataSet-Definition nicht über die Struktur verfügt, werden alle Spalten der Tabelle ausgewählt.

## Beispiel: Kopieren von Daten aus einem Azure-Blob in Azure SQL Data Warehouse

Das nachstehende Beispiel zeigt Folgendes:

1.	Einen verknüpften Dienst des Typs [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties)
2.	Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)
3.	Ein DataSet [dataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)
4.	Ein DataSet [dataset](data-factory-create-datasets.md) des Typs [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties)
4.	Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) und [SqlDWSink](#azure-sql-data-warehouse-copy-activity-type-properties) verwendet


Im Beispiel werden Daten, die zu einer Zeitreihe aus einem Azure-Blob gehören, stündlich in eine Tabelle in einer Azure SQL Data Warehouse-Datenbank kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

**Mit Azure SQL Data Warehouse verknüpfter Dienst:**

	{
	  "name": "AzureSqlDWLinkedService",
	  "properties": {
	    "type": "AzureSqlDW",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Mit Azure-Blobspeicher verknüpfter Dienst:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure-Blob-Eingabedataset:**

Daten werden stündlich aus einem neuen Blob übernommen ("frequency": "hour", "interval": 1). Ordnerpfad und Dateiname des Blobs werden basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Der Ordnerpfad verwendet die Bestandteile Jahr, Monat und Tag der Startzeit, und der Dateiname verwendet die Stunde der Startzeit. Die Festlegung von "external" auf "true" teilt dem Data Factory-Dienst mit, dass diese Tabelle für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
	      "fileName": "{Hour}.csv",
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
	        "columnDelimiter": ",",
	        "rowDelimiter": "\n"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
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

**Azure SQL Data Warehouse-Ausgabedataset:**

Das Beispiel kopiert Daten in eine Tabelle namens "MyTable" in einer Azure SQL Data Warehouse-Datenbank. Sie sollten die Tabelle in Azure SQL Data Warehouse mit der gleichen Anzahl von Spalten erstellen, die Sie in der Blob-CSV-Datei erwarten. Neue Zeilen werden der Tabelle stündlich hinzugefügt.

	{
	  "name": "AzureSqlDWOutput",
	  "properties": {
	    "type": "AzureSqlDWTable",
	    "linkedServiceName": "AzureSqlDWLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Pipeline mit Kopieraktivität**

Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der oben genannten Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **BlobSource** und der Typ **sink** auf **SqlDWSink** festgelegt.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoSQLDW",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureSqlDWOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource",
	            "blobColumnSeparators": ","
	          },
	          "sink": {
	            "type": "SqlDWSink"
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

Im Artikel [Laden von Daten mit Azure Data Factory](../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) in der Azure SQL Data Warehouse-Dokumentation finden Sie eine exemplarische Vorgehensweise.

## Eigenschaften des mit Azure SQL Data Warehouse verknüpften Diensts

Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit Azure SQL Data Warehouse verknüpften Dienst spezifisch sind.

Eigenschaft | Beschreibung | Erforderlich
-------- | ----------- | --------
Typ | Die "type"-Eigenschaft muss auf **AzureSqlDW** festgelegt sein. | Ja
**connectionString** | Geben Sie Informationen, die zur Verbindung mit der Azure SQL Data Warehouse-Instanz erforderlich sind, für die Eigenschaft "connectionString" ein. | Ja

Hinweis: Sie müssen die [Firewall für die Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) konfigurieren. Sie müssen den Datenbankserver konfigurieren, um [Azure-Diensten den Zugriff auf den Server zu erlauben](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Wenn Sie nicht aus Azure stammende Daten in Azure SQL Data Warehouse kopieren, inklusive Daten aus lokalen Datenquellen mit Data Factory-Gateway, müssen Sie außerdem den entsprechenden IP-Adressbereich für den Computer konfigurieren, der Daten an Azure SQL Data Warehouse sendet.

## Eigenschaften des Dataset-Typs „Azure SQL Data Warehouse“

Eine vollständige Liste der Abschnitte und Eigenschaften, die zum Definieren von DataSets zur Verfügung stehen, finden Sie im Artikel [Erstellen von DataSets](data-factory-create-datasets.md). Abschnitte wie "structure", "availability" und "policy" des JSON-Codes eines Datasets sind bei allen Typen von Datasets (Azure SQL, Azure-Blob, Azure-Tabelle usw.) ähnlich.

Der Abschnitt "typeProperties" unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt **typeProperties** für ein DataSet des Typs **AzureSqlDWTable** weist die folgenden Eigenschaften auf.

| Eigenschaft | Beschreibung | Erforderlich |
| -------- | ----------- | -------- |
| tableName | Name der Tabelle in der Azure SQL Data Warehouse-Datenbank, auf die der verknüpfte Dienst verweist. | Ja |

## Eigenschaften des Kopieraktivitätstyps „Azure SQL Data Warehouse“

Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen, verschiedene Richtlinien usw. sind für alle Arten von Aktivitäten verfügbar.

**Hinweis**: Die Kopieraktivität verwendet nur eine Eingabe und erzeugt nur eine Ausgabe.

Im Abschnitt "typeProperties" der Aktivität verfügbare Eigenschaften variieren hingegen bei jedem Aktivitätstyp. Bei der Kopieraktivität variieren sie je nach Typ der Quellen und Senken.

### SqlDWSource

Wenn bei der Kopieraktivität "source" den Typ **SqlDWSource** aufweist, sind im Abschnitt **typeProperties** die folgenden Eigenschaften verfügbar:

| Eigenschaft | Beschreibung | Zulässige Werte | Erforderlich |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. | SQL-Abfragezeichenfolge. Beispiel: select * from MyTable. | Nein |
| sqlReaderStoredProcedureName | Der Name der gespeicherten Prozedur, die Daten aus der Quelltabelle liest. | Name der gespeicherten Prozedur. | Nein |
| storedProcedureParameters | Parameter für die gespeicherte Prozedur. | Name-Wert-Paare. Die Namen und die Groß-/Kleinschreibung von Parametern müssen denen der Parameter der gespeicherten Prozedur entsprechen. | Nein |

Wenn **sqlReaderQuery** für "SqlDWSource" angegeben ist, führt die Kopieraktivität diese Abfrage für die Azure SQL Data Warehouse-Quelle aus, um die Daten abzurufen.

Alternativ dazu können Sie eine gespeicherte Prozedur angeben, indem Sie **sqlReaderStoredProcedureName** und **storedProcedureParameters** angeben (sofern die gespeicherten Prozeduren Parameter verwenden).

Wenn Sie "sqlReaderQuery" oder "sqlReaderStoredProcedureName" nicht angeben, werden die im Strukturabschnitt des DataSet-JSON-Bereichs definierten Spalten verwendet, um eine Abfrage ("select column1, column2 from mytable") zur Ausführung für das SQL Data Warehouse zu erstellen. Falls die DataSet-Definition nicht über die Struktur verfügt, werden alle Spalten der Tabelle ausgewählt.

#### Beispiel für "SqlDWSource"

    "source": {
        "type": "SqlDWSource",
        "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
        "storedProcedureParameters": {
            "stringData": { "value": "str3" },
            "id": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
        }
    }

**Die Definition der gespeicherten Prozedur:**

	CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
	(
		@stringData varchar(20),
		@id int
	)
	AS
	SET NOCOUNT ON;
	BEGIN
	     select *
	     from dbo.UnitTestSrcTable
	     where dbo.UnitTestSrcTable.stringData != stringData
	    and dbo.UnitTestSrcTable.id != id
	END
	GO
 

### SqlDWSink
**SqlDWSink** unterstützt die folgenden Eigenschaften:

| Eigenschaft | Beschreibung | Zulässige Werte | Erforderlich |
| -------- | ----------- | -------------- | -------- |
| writeBatchSize | Fügt Daten in die SQL-Tabelle ein, wenn die Puffergröße "writeBatchSize" erreicht. | Ganze Zahl. (Einheit = Zeilenanzahl) | Nein (Standard = 10000) |
| writeBatchTimeout | Die Wartezeit für den Abschluss der Batcheinfügung, bis das Timeout wirksam wird. | (Einheit = Zeitspanne) Beispiel: "00:30:00" (30 Minuten). | Nein | 
| sqlWriterCleanupScript | Benutzerdefinierte Abfrage, die Kopieraktivität so auszuführen, dass Daten von einem bestimmten Slice bereinigt werden. Im Abschnitt zur Wiederholbarkeit unten erfahren Sie weitere Einzelheiten. | Eine Abfrageanweisung. | Nein |
| allowPolyBase | Gibt an, ob (falls zutreffend) PolyBase anstelle des BULKINSERT-Mechanismus zum Laden von Daten in Azure SQL Data Warehouse verwendet werden soll. <br/><br/>Beachten Sie, dass aktuell nur **Azure-Blobdatasets** unterstützt werden, bei denen für **format** der Wert **TextFormat** als Quelldataset festgelegt ist. Unterstützung für weitere Quelltypen ist für die nahe Zukunft geplant. <br/><br/>Einschränkungen und Einzelheiten finden Sie im Abschnitt [Daten unter Verwendung von PolyBase in Azure SQL Data Warehouse laden](#use-polybase-to-load-data-into-azure-sql-data-warehouse). | True <br/>False (Standardwert) | Nein |  
| polyBaseSettings | Eine Gruppe von Eigenschaften, die angegeben werden können, wenn die **allowPolybase**-Eigenschaft auf **true** festgelegt ist. | &nbsp; | Nein |  
| rejectValue | Gibt die Anzahl oder den Prozentsatz von Zeilen an, die abgelehnt werden können, bevor für die Abfrage ein Fehler auftritt. <br/><br/>Weitere Informationen zu den PolyBase-Optionen zum Ablehnen finden Sie im Abschnitt **Argumente** des Themas [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). | 0 (Standardwert), 1, 2, … | Nein |  
| rejectType | Gibt an, ob die rejectValue-Option als Literalwert oder Prozentsatz angegeben ist. | Value (Standardwert), Percentage | Nein |   
| rejectSampleValue | Gibt die Anzahl von Zeilen an, die abgerufen werden, bevor PolyBase den Prozentsatz der abgelehnten Zeilen neu berechnet. | 1, 2, … | Ja, wenn für **rejectType** der Wert **percentage** festgelegt ist |  
| useTypeDefault | Gibt an, wie fehlende Werte in durch Trennzeichen getrennten Textdateien verarbeitet werden sollen, wenn PolyBase Daten aus der Textdatei abruft.<br/><br/>Weitere Informationen zu dieser Eigenschaft finden Sie im Abschnitt zu Argumenten im Thema [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). | True/False (Standardwert) | Nein | 


#### Beispiel für "SqlDWSink"


    "sink": {
        "type": "SqlDWSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00"
    }

## Daten unter Verwendung von PolyBase in Azure SQL Data Warehouse laden
**PolyBase** bietet eine effiziente Möglichkeit, um große Datenmengen mit hohem Durchsatz aus Azure Blob Storage in Azure SQL Data Warehouse zu laden. Wenn Sie PolyBase anstelle des standardmäßigen BULKINSERT-Mechanismus verwenden, wird der Durchsatz erheblich gesteigert.

Wenn Sie einen anderen Datenspeicher als Azure Blob Storage verwenden, sollten Sie die Daten gegebenenfalls zunächst aus dem Quelldatenspeicher nach Azure Blob Storage kopieren, um diesen Speicher als Stagingspeicher zu nutzen. Anschließend verwenden Sie PolyBase, um die Daten aus dem Stagingspeicher in Azure SQL Data Warehouse zu laden. In diesem Szenario führen Sie zwei Kopieraktivitäten aus. Mit der ersten Kopieraktivität werden Daten aus dem Quelldatenspeicher nach Azure Blob Storage kopiert, mit der zweiten Kopieraktivität werden Daten unter Verwendung von PolyBase aus Azure Blob Storage nach Azure SQL Data Warehouse kopiert.

Legen Sie für die **allowPolyBase**-Eigenschaft den Wert **true** fest, wie im folgenden Beispiel für Azure Data Factory gezeigt, um PolyBase für das Kopieren von Daten aus Azure Blob Storage nach Azure SQL Data Warehouse zu verwenden. Wenn Sie für „allowPolyBase“ den Wert „true“ festlegen, können Sie über die **polyBaseSettings**-Eigenschaftengruppe PolyBase-spezifische Eigenschaften festlegen. Im Abschnitt [SqlDWSink](#SqlDWSink) oben finden Sie Einzelheiten zu den Eigenschaften, die mit „polyBaseSettings“ verwendet werden können.


    "sink": {
        "type": "SqlDWSink",
		"allowPolyBase": true,
		"polyBaseSettings":
		{
			"rejectType": "percentage",
			"rejectValue": 10,
			"rejectSampleValue": 100,
			"useTypeDefault": true 
		}

    }

Azure Data Factory überprüft, ob die Daten die folgenden Anforderungen erfüllen, bevor PolyBase zum Kopieren der Daten nach Azure SQL Data Warehouse verwendet wird. Wenn die Anforderungen nicht erfüllt werden, wird automatisch der BULKINSERT-Mechanismus für die Datenverschiebung verwendet.

1.	**Der mit der Quelle verknüpfte Dienst** weist folgenden Typ auf: **Azure Storage**. Außerdem ist dieser Dienst nicht für die Verwendung der SAS-Authentifizierung (Shared Access Signature) konfiguriert. Ausführliche Informationen finden Sie unter [Mit Azure Storage verknüpfter Dienst](data-factory-azure-blob-connector.md#azure-storage-linked-service).  
2. Das **Eingabedataset** weist folgenden Typ auf: **Azure Blob**. Außerdem erfüllen die Typeigenschaften des Datasets die folgenden Kriterien: 
	1. Für **Type** muss **TextFormat** festgelegt sein. 
	2. Für **rowDelimiter** muss **\\n** festgelegt sein. 
	3. **nullValue** ist auf **empty string** ("") festgelegt. 
	4. **encodingName** ist auf **utf-8** festgelegt. Dies ist der **Standardwert**, legen Sie also keinen anderen Wert fest. 
	5. **escapeChar** und **quoteChar** sind nicht angegeben. 
	6. **Compression** weist einen anderen Wert als **BZIP2** auf.
	 
			"typeProperties": {
				"folderPath": "<blobpath>",
				"format": {
					"type": "TextFormat",     
					"columnDelimiter": "<any delimiter>", 
					"rowDelimiter": "\n",       
					"nullValue": "",           
					"encodingName": "utf-8"    
				},
            	"compression": {  
                	"type": "GZip",  
	                "level": "Optimal"  
    	        }  
			},
3.	Unter **BlobSource** ist keine **skipHeaderLineCount**-Einstellung für die Kopieraktivität in der Pipeline vorhanden. 
4.	Unter **SqlDWSink** ist keine **sliceIdentifierColumnName**-Einstellung für die Kopieraktivität in der Pipeline vorhanden. (PolyBase stellt sicher, dass in einem Durchlauf entweder alle oder keine Daten aktualisiert werden). Um **Wiederholbarkeit** zu erreichen, kann **sqlWriterCleanupScript** verwendet werden.
5.	In der zugehörigen Kopieraktivität wird keine **columnMapping** verwendet. 

### Bewährte Methoden bei Verwendung von PolyBase

#### Zeilengrößeneinschränkung
Polybase unterstützt eine maximale Zeilengröße von 32 KB. Beim Versuch, eine Tabelle mit Zeilen zu laden, die größer sind als 32 KB, tritt der folgende Fehler auf:

	Type=System.Data.SqlClient.SqlException,Message=107093;Row size exceeds the defined Maximum DMS row size: [35328 bytes] is larger than the limit of [32768 bytes],Source=.Net SqlClient

Wenn Sie über Quelldaten mit Zeilen verfügen, deren Größe über 32 KB liegt, sollten Sie die Quelltabellen vertikal in kleinere Tabellen unterteilen, sodass die Zeilengröße der einzelnen Tabellen den Höchstwert nicht überschreitet. Die kleineren Tabellen können dann mit PolyBase geladen und in Azure SQL Data Warehouse zusammengeführt werden.

#### „tableName“ in Azure SQL Data Warehouse
Die folgende Tabelle zeigt Beispiele für das Angeben der **tableName**-Eigenschaft in Dataset-JSON für diverse Kombinationen aus Schema und Tabellenname.

| Datenbankschema | Tabellenname | JSON-Eigenschaft „tableName“ |
| --------- | -----------| ----------------------- | 
| dbo | MyTable | MyTable oder dbo.MyTable oder [dbo].[MyTable] |
| dbo1 | MyTable | dbo1.MyTable oder [dbo1].[MyTable] |
| dbo | My.Table | [My.Table] oder [dbo].[My.Table] |
| dbo1 | My.Table | [dbo1].[My.Table] |

Wenn ein Fehler auftritt (siehe unten), könnte dies am Wert liegen, den Sie für die tableName-Eigenschaft angegeben haben. Die Tabelle oben zeigt, wie Werte für die JSON-Eigenschaft „tableName“ richtig angegeben werden.

	Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider

#### Spalten mit Standardwerten
Das PolyBase-Feature in Data Factory akzeptiert aktuell lediglich dieselbe Anzahl von Spalten wie in der Zieltabelle. Wenn Sie z. B. über eine Tabelle mit 4 Spalten verfügen und eine dieser Spalten mit einem Standardwert definiert ist, sollten die Eingabedaten trotzdem ebenfalls 4 Spalten umfassen. Bei Bereitstellung eines Eingabedatasets mit 3 Spalten würde wie unten gezeigt ein Fehler auftreten:

	All columns of the table must be specified in the INSERT BULK statement.

Der NULL-Wert ist eine Sonderform eines Standardwerts. Wenn die Spalte NULL-Werte zulässt, könnten die Eingabedaten (im Blob) für diese Spalte leer sein (sie dürfen im Eingabedataset nicht fehlen). PolyBase fügt für diese Daten in Azure SQL Data Warehouse „NULL“ ein.

#### Zweistufiger Kopiervorgang für die Verwendung von PolyBase
Für PolyBase gelten einige Einschränkungen im Hinblick auf die Datenspeicher und Formate, die verwendet werden können. Wenn Ihr Szenario die Anforderungen nicht erfüllt, sollten Sie die Daten über die Kopieraktivität in einen Datenspeicher kopieren, der von PolyBase unterstützt wird, und/oder die Daten in ein Format umwandeln, das von PolyBase unterstützt wird. Nachfolgend finden Sie Beispiele für die möglichen Umwandlungen:

-	Wandeln Sie Quelldateien mit anderen Codierungen in UTF-8-codierte Azure-Blobs um
-	Serialisieren Sie Daten in SQL Server/Azure SQL-Datenbank in Azure-Blobs im CSV-Format.
-	Ändern Sie die Spaltenreihenfolge mithilfe der columnMapping-Eigenschaft.

Nachfolgend finden Sie Tipps für das Durchführen dieser Umwandlungen:

- Wählen Sie bei der Umwandlung von Tabellendaten in CSV-Dateien ein geeignetes Trennzeichen.

	Dabei sollten als Spaltentrennzeichen Zeichen verwendet werden, deren Vorkommen in den Daten äußerst unwahrscheinlich ist. Gängige Trennzeichen sind z. B. Komma (,), Tilde (~), Pipe (|) und TAB(\\t). Wenn diese Zeichen in Ihren Daten enthalten sind, können Sie nicht druckbare Zeichen wie „\\u0001“ als Spaltentrennzeichen festlegen. PolyBase akzeptiert Spaltentrennzeichen aus mehreren Zeichen, sodass auch komplexere Spaltentrennzeichen gewählt werden können.	
- Format von datetime-Objekten

	Beim Serialisieren von datetime-Objekten verwendet die Kopieraktivität standardmäßig das folgende Format: jjjj-MM-tt HH:mm:ss.fffffff. Dieses Format wird standardmäßig nicht von PolyBase unterstützt. Die unterstützten datetime-Formate sind hier aufgeführt: [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). Wenn das von PolyBase erwartete datetime-Format nicht verwendet wird, tritt ein Fehler auf, wie nachfolgend gezeigt:

		Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 1 rows processed.
		(/AccountDimension)Column ordinal: 97, Expected data type: DATETIME NOT NULL, Offending value: 2010-12-17 00:00:00.0000000  (Column Conversion Error), Error: Conversion failed when converting the NVARCHAR value '2010-12-17 00:00:00.0000000' to data type DATETIME.

	Um diesen Fehler zu behandeln, geben Sie das datetime-Format wie im folgenden Beispiel gezeigt an:
	
		"structure": [
    		{ "name" : "column", "type" : "int", "format": "yyyy-MM-dd HH:mm:ss" }
		]


[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangular-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Typzuordnung für Azure SQL Data Warehouse

Wie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) beschrieben, führt die Kopieraktivität automatische Typkonvertierungen von Quelltypen in Senkentypen mithilfe des folgenden aus zwei Schritten bestehenden Ansatzes durch:

1. Konvertieren von systemeigenen Quelltypen in den .NET-Typ
2. Konvertieren vom .NET-Typ in systemeigenen Senkentyp

Beim Verschieben von Daten in und aus Azure SQL, SQL Server und Sybase werden die folgenden Zuordnungen zwischen SQL-Typ und .NET-Typ und umgekehrt verwendet.

Die Zuordnung ist mit der [SQL Server-Datentypzuordnung für ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx) identisch.

| Typ "SQL Server-Datenbankmodul" | Typ ".NET Framework" |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| binary | Byte |
| Bit | Boolean |
| char | String, Char |
| date | DateTime |
| Datetime | DateTime |
| datetime2 | DateTime |
| Datetimeoffset | DateTimeOffset |
| Decimal | Decimal |
| FILESTREAM-Attribut (varbinary(max)) | Byte |
| Float | Doppelt |
| image | Byte | 
| int | Int32 | 
| money | Decimal |
| nchar | String, Char |
| ntext | String, Char |
| numeric | Decimal |
| nvarchar | String, Char |
| real | Single |
| rowversion | Byte |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | Decimal | 
| sql\_variant | Object * |
| Text | String, Char |
| in | TimeSpan |
| timestamp | Byte |
| tinyint | Byte |
| uniqueidentifier | GUID |
| varbinary | Byte |
| varchar | String, Char |
| xml | Xml |



[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## Leistung und Optimierung  
Der Artikel [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) beschreibt wichtige Faktoren, die sich auf die Leistung der Datenverschiebung (Kopieraktivität) in Azure Data Factory auswirken, sowie verschiedene Möglichkeiten zur Leistungsoptimierung.

<!---HONumber=AcomDC_0420_2016-->