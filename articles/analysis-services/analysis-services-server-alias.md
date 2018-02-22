---
title: Azure Analysis Services-Aliasservernamen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Servernamenaliase erstellen und verwenden.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: e0119580c4128ffd237127a62e29dac34297da5d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/21/2018
---
# <a name="alias-server-names"></a>Aliasservernamen

Mit einem Servernamenalias können Benutzer beim Herstellen der Verbindung mit Ihrem Azure Analysis Services-Server anstelle des Servernamens ein kürzeres *Alias* verwenden. Wenn die Verbindung über eine Clientanwendung hergestellt wird, wird der Alias unter Verwendung des Protokollformats **link://** als Endpunkt angegeben. Der Endpunkt gibt dann den tatsächlichen Servernamen für die Verbindungsherstellung zurück.

Aliasservernamen ermöglichen Folgendes:

- Modellmigration zwischen Servern ohne Beeinträchtigung der Benutzer 
- Verwendung leichter zu merkender Servernamen 
- Weiterleitung von Benutzern an unterschiedliche Server (abhängig von der Tageszeit) 
- Weiterleitung von Benutzern in verschiedenen Regionen an Instanzen, die jeweils geografisch näher liegen (wie bei Verwendung von Azure Traffic Manager) 

Als Alias kann jeder HTTP-Endpunkt fungieren, der einen gültigen Azure Analysis Services-Servernamen zurückgibt.

![Alias mit Linkformat](media/analysis-services-alias/aas-alias-browser.png)

Bei der Verbindungsherstellung über einen Client wird der Aliasservername im Protokollformat **link://** eingegeben. In Power BI Desktop sieht das beispielsweise wie folgt aus:

![Power BI Desktop-Verbindung](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Erstellen eines Alias

Für die Erstellung eines Aliasendpunkts können Sie eine beliebige Methode verwenden, die einen gültigen Azure Analysis Services-Servernamen zurückgibt. Beispiele wären etwa ein Verweis auf eine Datei in Azure Blob Storage mit dem tatsächlichen Servernamen oder die Erstellung und Veröffentlichung einer ASP.NET Web Forms-Anwendung.

In diesem Beispiel wird eine ASP.NET Web Forms-Anwendung in Visual Studio erstellt. Der Masterseitenverweis und das Benutzersteuerelement werden von der Seite „default.aspx“ entfernt. „Default.aspx“ enthält einfach nur die folgende Page-Direktive:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

Das Ereignis „Page_Load“ in der Datei „Default.aspx.cs“ verwendet die Methode „Response.Write()“, um den Azure Analysis Services-Servernamen zurückzugeben.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Weitere Informationen

[Clientbibliotheken](analysis-services-data-providers.md)   
[Herstellen einer Verbindung mit Power BI](analysis-services-connect-pbi.md)
