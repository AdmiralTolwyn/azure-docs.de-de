---
title: Was ist mit dem ASP.NET-Projekt passiert? | Microsoft Docs
description: "Erfahren Sie, was nach dem Hinzufügen von Azure Storage zu einem ASP.NET-Projekt mit verbundenen Visual Studio-Diensten passiert."
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 086c2259aa4686e82f5fd121c4545f573c378414


---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Was ist mit dem ASP.NET-Projekt passiert (verbundene Visual Studio-Dienste für Azure Storage)?
## <a name="references-added"></a>Verweise wurden hinzugefügt
Das Azure Storage NuGet-Paket wurde zu Ihrem Visual Studio-Projekt hinzugefügt.  
Dieses Paket fügt die folgenden .NET-Verweise hinzu:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Die Verbindungszeichenfolge für Azure Storage wurde hinzugefügt
In der Datei web.config Ihres Projekts wurde ein Element mit der Verbindungszeichenfolge und dem Schlüssel des ausgewählten Speicherkontos erstellt.

Weitere Informationen finden Sie unter [ASP.NET](http://www.asp.net).




<!--HONumber=Nov16_HO3-->


