<properties
	pageTitle="Was ist mit dem WebJob-Projekt passiert (verbundene Visual Studio-Dienste für Azure Storage)? | Microsoft Azure"
	description="Erfahren Sie, was nach dem Herstellen einer Verbindung mit einem Speicherkonto mithilfe von verbundenen Visual Studio-Diensten in einem Azure-WebJob-Projekt passiert."
	services="storage"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/08/2016"
	ms.author="tarcher"/>

# Was ist mit dem WebJob-Projekt passiert (verbundene Visual Studio-Dienste für Azure Storage)?

## Verweise wurden hinzugefügt

Das Azure Storage NuGet-Paket wurde zu Ihrem Visual Studio-Projekt hinzugefügt oder aktualisiert. Dieses Paket fügt die folgenden .NET-Verweise hinzu:

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.ConfigurationManager**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

## Die Verbindungszeichenfolge für Azure Storage wurde hinzugefügt
In der Datei "App.config" Ihres Projekts wurden die Einträge **AzureWebJobsStorage** und **AzureWebJobsDashboard** mit der Verbindungszeichenfolge und dem Schlüssel des ausgewählten Speicherkontos aktualisiert.

Weitere Informationen finden Sie unter [Dokumentationsressourcen für Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).

<!---HONumber=AcomDC_0511_2016-->