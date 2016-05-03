<properties 
	pageTitle="Tutorial: Erstellen einer Pipeline mit Kopieraktivität mithilfe von Visual Studio" 
	description="In diesem Tutorial erstellen Sie eine Azure Data Factory-Pipeline mit Kopieraktivität mithilfe von Visual Studio." 
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
	ms.topic="get-started-article" 
	ms.date="03/07/2016" 
	ms.author="spelluru"/>

# Tutorial: Erstellen einer Pipeline mit Kopieraktivität mithilfe von Visual Studio
> [AZURE.SELECTOR]
- [Übersicht über das Tutorial](data-factory-get-started.md)
- [Verwenden des Data Factory-Editors](data-factory-get-started-using-editor.md)
- [Verwenden von Visual Studio](data-factory-get-started-using-vs.md)
- [Mithilfe von PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Verwenden des Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md)

In diesem Lernprogramm führen Sie mit Visual Studio 2013 die folgenden Schritte aus:

1. Erstellen von zwei verknüpften Diensten: **AzureStorageLinkedService1** und **AzureSqlinkedService1**. Der AzureStorageLinkedService1 verknüpft einen Azure-Speicher, und AzureSqlLinkedService1 verknüpft eine Azure SQL-Datenbank mit der Data Factory: **ADFTutorialDataFactoryVS**. Die Eingabedaten für die Pipeline befinden sich in einem Blobcontainer im Azure-Blobspeicher. Ausgabedaten werden in einer Tabelle in der Azure SQL-Datenbank gespeichert. Daher fügen Sie diese beiden Datenspeicher als verknüpfte Dienste der Data Factory hinzu.
2. Erstellen von zwei Data Factory-Tabellen: **EmpTableFromBlob** und **EmpSQLTable**, die Ein- und Ausgabedaten darstellen, die in den Datenspeichern gespeichert sind. Für die Tabelle "EmpTableFromBlob" geben Sie den Blobcontainer an, der ein Blob mit den Quelldaten enthält. Für die Tabelle "EmpSQLTable" geben Sie die SQL-Tabelle an, in der die Ausgabedaten gespeichert werden sollen. Sie können auch andere Eigenschaften wie z. B. die Struktur der Daten, die Verfügbarkeit von Daten usw. angeben.
3. Erstellen einer Pipeline mit dem Namen **ADFTutorialPipeline** in ADFTutorialDataFactoryVS. Die Pipeline weist eine **Kopieraktivität** auf, die zum Kopieren von Eingabedaten aus dem Azure-Blob in die Azure SQL-Tabelle verwendet wird. Die Kopieraktivität führt die Datenverschiebung in Azure Data Factory durch, und die Aktivität wird von einem global verfügbaren Dienst gestützt, mit dem Daten zwischen verschiedenen Datenspeichern auf sichere, zuverlässige und skalierbare Weise kopiert werden können. Ausführliche Informationen zur Kopieraktivität finden Sie unter [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md). 
4. Erstellen einer Data Factory und Bereitstellen von verknüpften Diensten, Tabellen und der Pipeline.    

## Voraussetzungen
Lesen Sie sich den Artikel [Übersicht über das Tutorial](data-factory-get-started.md) durch, und führen Sie die vorbereitenden Schritte aus, bevor Sie mit diesem Tutorial beginnen.

Folgendes muss auf Ihrem Computer installiert sein:
- Visual Studio 2013
- Laden Sie das Azure SDK für Visual Studio 2013 herunter. Navigieren Sie zur [Azure-Downloadseite](https://azure.microsoft.com/downloads/), und klicken Sie im Abschnitt **.NET** auf **VS 2013 installieren**.

## Erstellen eines Visual Studio-Projekts 
1. Starten Sie **Visual Studio 2013**. Klicken Sie auf **Datei**, zeigen Sie auf **Neu**, und klicken Sie auf **Projekt**. Das Dialogfeld **Neues Projekt** sollte angezeigt werden.  
2. Wählen Sie im Dialogfeld **Neues Projekt** die Vorlage **DataFactory** aus, und klicken Sie auf **Leeres Data Factory-Projekt**. Wenn die Vorlage DataFactory nicht angezeigt wird, schließen Sie Visual Studio, installieren Sie Azure SDK für Visual Studio 2013, und öffnen Sie Visual Studio erneut.  

	![Dialogfeld "Neues Projekt"](./media/data-factory-get-started-using-vs/new-project-dialog.png)

3. Füllen Sie für das Projekt die Felder **Name**, **Speicherort** und **Lösung** aus, und klicken Sie auf**OK**.

	![Projektmappen-Explorer](./media/data-factory-get-started-using-vs/solution-explorer.png)

## Erstellen von verknüpften Diensten
Verknüpfte Dienste verknüpfen Datenspeicher oder Serverdienste mit einer Azure Data Factory. Ein Datenspeicher kann ein Azure-Speicher, eine Azure SQL-Datenbank oder eine lokale SQL Server-Datenbank sein.

In diesem Schritt erstellen Sie zwei verknüpfte Dienste: **AzureStorageLinkedService1** und **AzureSqlLinkedService1**. Der verknüpfte Dienst „AzureStorageLinkedService1“ verknüpft ein Azure-Speicherkonto, und der Dienst „AzureSqlLinkedService“ verknüpft eine Azure SQL-Datenbank mit der Data Factory **ADFTutorialDataFactory**.

### Erstellen des mit Azure Storage verknüpften Diensts

4. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verknüpfte Dienste**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**.      
5. Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Option **Mit Azure-Speicher verknüpfter Dienst** aus der Liste aus, und klicken Sie auf **Hinzufügen**. 

	![Neuer verknüpfter Dienst](./media/data-factory-get-started-using-vs/new-linked-service-dialog.png)
 
3. Ersetzen Sie **accountname** und **accountkey** durch den Namen Ihres Azure-Speicherkontos bzw. den entsprechenden Schlüssel.

	![Mit Azure-Speicher verknüpfter Dienst](./media/data-factory-get-started-using-vs/azure-storage-linked-service.png)

4. Speichern Sie die Datei **AzureStorageLinkedService1.json**.

### Erstellen des mit Azure SQL verknüpften Diensts

5. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste erneut auf den Knoten **Verknüpfte Dienste**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**. 
6. Wählen Sie dieses Mal **Azure SQL Linked Service** aus, und klicken Sie dann auf **Hinzufügen**. 
7. Ersetzen Sie in der Datei **AzureSqlLinkedService1.json** die Werte **servername**, **databasename**, ****username@servername** und **password** durch die betreffenden Angaben für Azure SQL-Server, -Datenbank, -Benutzerkonto und -Kennwort.
8.  Speichern Sie die Datei **AzureSqlLinkedService1.json**. 


## Erstellen von Datasets
Im vorherigen Schritt haben Sie die verknüpften Dienste **AzureStorageLinkedService1** und **AzureSqlLinkedService1** erstellt, um ein Azure-Speicherkonto und eine Azure SQL-Datenbank mit der Data Factory **ADFTutorialDataFactory** zu verknüpfen. In diesem Schritt definieren Sie die beiden Data Factory-Tabellen **EmpTableFromBlob** und **EmpSQLTable**, die Ein- und Ausgabedaten in den Datenspeichern darstellen, auf die von „AzureStorageLinkedService1“ und „AzureSqlLinkedService1“ verwiesen wird. Für die Tabelle "EmpTableFromBlob" geben Sie den Blobcontainer an, der ein Blob mit den Quelldaten enthält. Für die Tabelle "EmpSQLTable" geben Sie die SQL-Tabelle an, in der die Ausgabedaten gespeichert werden sollen.

### Erstellen eines Eingabedatasets

9. Klicken Sie mit der rechten Maustaste auf **Tabellen** im **Projektmappen-Explorer**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**.
10. Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Option **Azure-Blob** aus, und klicken Sie auf **Hinzufügen**.   
10. Ersetzen Sie den JSON-Text durch den folgenden Text, und speichern Sie die **AzureBlobLocation1.json**-Datei. 

		{
		  "name": "EmpTableFromBlob",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
		    "typeProperties": {
		      "folderPath": "adftutorial/",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

### Erstellen des Ausgabedatasets

11. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste erneut auf **Tabellen**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**.
12. Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Option **Azure SQL** aus, und klicken Sie auf **Hinzufügen**. 
13. Ersetzen Sie den JSON-Text durch den folgenden JSON-Text, und speichern Sie die Datei **AzureSqlTableLocation1.json**.

		{
		  "name": "EmpSQLTable",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureSqlTable",
		    "linkedServiceName": "AzureSqlLinkedService1",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

## Erstellen der Pipeline 
Sie haben bisher verknüpfte Ein-/Ausgabe-Dienste und -Tabellen erstellt. Nun erstellen Sie eine Pipeline mit einer **Kopieraktivität**, um Daten aus dem Azure-Blob in Azure SQL-Datenbank zu kopieren.


1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Pipelines**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf**Neues Element**.  
15. Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Option **Copy Data Pipeline**, und klicken Sie auf **Hinzufügen**. 
16. Ersetzen Sie den JSON-Text durch den folgenden JSON-Text, und speichern Sie die Datei **CopyActivity1.json**.
			
		{
		  "name": "ADFTutorialPipeline",
		  "properties": {
		    "description": "Copy data from a blob to Azure SQL table",
		    "activities": [
		      {
		        "name": "CopyFromBlobToSQL",
		        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpTableFromBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "EmpSQLTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "BlobSource"
		          },
		          "sink": {
		            "type": "SqlSink",
		            "writeBatchSize": 10000,
		            "writeBatchTimeout": "60:00:00"
		          }
		        },
		        "Policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "NewestFirst",
		          "style": "StartOfInterval",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-07-12T00:00:00Z",
		    "end": "2015-07-13T00:00:00Z",
		    "isPaused": false
		  }
		}

## Veröffentlichen/Bereitstellen der Data Factory-Entitäten
  
18. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie auf **Veröffentlichen**. 
19. Wenn das Dialogfeld **Melden Sie sich bei Ihrem Microsoft-Konto an** angezeigt wird, geben Sie Ihre Anmeldeinformationen für das Konto mit dem Azure-Abonnement ein, und klicken Sie auf **Anmelden**.
20. Das folgende Dialogfeld sollte angezeigt werden:

	![Dialogfeld „Veröffentlichen“](./media/data-factory-get-started-using-vs/publish.png)

21. Führen Sie auf der Seite zum Konfigurieren der Data Factory folgende Schritte aus:
	1. Wählen Sie die Option **Neue Data Factory erstellen**.
	2. Geben Sie **VSTutorialFactory** als **Name** ein.  
	
		> [AZURE.NOTE]  
		Der Name der Azure Data Factory muss global eindeutig sein. Wenn beim Veröffentlichen ein Fehler bezüglich des Namens der Data Factory auftritt, ändern Sie den Namen der Data Factory (z. B. in „IhrNameVSTutorialFactory“), und versuchen Sie erneut, sie zu veröffentlichen. Im Thema [Data Factory – Benennungsregeln](data-factory-naming-rules.md) finden Sie Benennungsregeln für Data Factory-Artefakte.
		> 
		> Der Name der Data Factory kann in Zukunft als DNS-Name registriert und so öffentlich sichtbar werden.
	3. Wählen Sie im Feld **Abonnement** das richtige Abonnement aus. 
	4. Wählen Sie die **Ressourcengruppe** für die zu erstellende Data Factory aus. 
	5. Wählen Sie die **Region** für die Data Factory aus. 
	6. Klicken Sie auf **Weiter**, um zur Seite **Publish Items** zu wechseln. 
23. Stellen Sie auf der Seite **Publish Items** sicher, dass alle Data Factory-Entitäten ausgewählt sind, und klicken Sie auf **Weiter**, um zur Seite **Zusammenfassung** zu wechseln.     
24. Prüfen Sie die Zusammenfassung, und klicken Sie auf **Weiter**, um den Bereitstellungsprozess zu starten und den **Bereitstellungsstatus** anzuzeigen.
25. Auf der Seite **Bereitstellungsstatus** sollte der Status des Bereitstellungsprozesses angezeigt werden. Klicken Sie auf „Fertig stellen“, nachdem die Bereitstellung abgeschlossen ist. 

Wenn Sie eine Fehlermeldung wie „**Dieses Abonnement ist nicht zur Verwendung des Microsoft.DataFactory-Namespaces registriert**“ erhalten, führen Sie einen der folgenden Schritte aus, und versuchen Sie erneut zu veröffentlichen:

- Führen Sie in Azure PowerShell den folgenden Befehl aus, um den Data Factory-Anbieter zu registrieren. 
		
		Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
	
	Sie können den folgenden Befehl ausführen, um sicherzustellen, dass der Data Factory-Anbieter registriert ist.
	
		Get-AzureRmResourceProvider
- Melden Sie sich mit dem Azure-Abonnement beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu einem Data Factory-Blatt, (oder) erstellen Sie eine Data Factory im Azure-Portal. Dadurch wird der Anbieter automatisch für Sie registriert.


## Verwenden von Server-Explorer zum Anzeigen von Data Factorys

1. Klicken Sie in **Visual Studio** im Menü auf **Ansicht** und dann auf **Server-Explorer**.
2. Erweitern Sie im Server-Explorer-Fenster erst die Option **Azure** und dann **Data Factory**. Wenn **Anmelden bei Visual Studio** angezeigt wird, geben Sie das mit Ihrem Azure-Abonnement verknüpfte **Konto** ein, und klicken Sie auf **Weiter**. Geben Sie Ihr **Kennwort** ein, und klicken Sie auf **Anmelden**. Visual Studio versucht, Informationen über alle Azure Data Factorys abzurufen, die in Ihrem Abonnement enthalten sind. Der Status dieses Vorgangs wird im Fenster **Data Factory-Aufgabenliste** angezeigt. ![Server-Explorer](./media/data-factory-get-started-using-vs/server-explorer.png)
3. Durch Klicken mit der rechten Maustaste auf eine Data Factory und Auswählen von „Data Factory in neues Projekt exportieren“ können Sie anhand einer vorhandenen Data Factory ein Visual Studio-Projekt erstellen. ![Exportieren einer Data Factory in ein Visual Studio-Projekt](./media/data-factory-get-started-using-vs/export-data-factory-menu.png)  

## Aktualisieren von Data Factory-Tools für Visual Studio
Um die Azure Data Factory-Tools für Visual Studio zu aktualisieren, führen Sie folgende Schritte aus:

1. Klicken Sie im Menü auf **Extras**, und wählen Sie**Erweiterungen und Updates** aus. 
2. Wählen Sie im linken Bereich **Updates** und dann **Visual Studio Gallery** aus.
4. Wählen Sie **Azure Data Factory tools for Visual Studio** aus, und klicken Sie auf **Update**. Wenn dieser Eintrag nicht angezeigt wird, verfügen Sie bereits über die neueste Version der Tools. 

Unter [Überwachen von Datasets und Pipelines](data-factory-get-started-using-editor.md#monitor-pipeline) finden Sie eine Anleitung zum Überwachen der in diesem Tutorial erstellten Pipeline und Datasets über das Azure-Portal.

## Siehe auch
Ausführliche Informationen zur **Kopieraktivität** in Azure Data Factory finden Sie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md).

<!---HONumber=AcomDC_0427_2016-->