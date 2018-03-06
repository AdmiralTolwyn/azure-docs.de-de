---
title: Zum Herstellen einer Verbindung mit Azure Analysis Services erforderliche Clientbibliotheken | Microsoft-Dokumentation
description: "Beschreibt für Clientanwendungen und Tools erforderliche Clientbibliotheken zum Herstellen einer Verbindung mit Azure Analysis Services"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2018
ms.author: owend
ms.openlocfilehash: 5c847f5cd02503b708db8a0a0211b5d403df0943
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/28/2018
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Clientbibliotheken zum Herstellen einer Verbindung mit Azure Analysis Services

Damit Clientanwendungen und Tools eine Verbindung mit Analysis Services-Servern herstellen können, sind Clientbibliotheken erforderlich. 

## <a name="download-the-latest-client-libraries-windows-installer"></a>Herunterladen der neuesten Clientbibliotheken (Windows Installer)  

|Download  |Produktversion  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.0.1.208      |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |    15.0.1.208      |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   15.0.2     |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    15.0.2     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO und ADOMD (NuGet-Pakete)

Analysis Services Management Objects (AMO) und ADOMD-Clientbibliotheken stehen als installierbare Pakete auf [NuGet.org](https://www.nuget.org/) zur Verfügung. Es wird empfohlen, nicht den Windows Installer zu verwenden, sondern eine Migration zu NuGet-Verweisen auszuführen. 

|Paket  | Produktversion  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    15.0.2.0      |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   15.0.2.0      |

Die AssemblyVersion für NuGet-Paketassemblys folgt der semantischen Versionierung: MAJOR.MINOR.PATCH. NuGet-Verweise laden die erwartete Version selbst dann, wenn im GAC eine andere Version (als Ergebnis der MSI-Installation) vorhanden ist. PATCH wird für jedes Release erhöht. AMO- und ADOMD-Versionen werden synchronisiert.

## <a name="understanding-client-libraries"></a>Grundlegendes zu Clientbibliotheken

Für Analysis Services werden drei Clientbibliotheken verwendet, die auch als Datenanbieter bezeichnet werden. ADOMD.NET und Analysis Services Management Objects (AMO) sind verwaltete Clientbibliotheken. Der Analysis Services OLE DB-Anbieter (MSOLAP DLL) ist eine native Clientbibliothek. In der Regel werden alle drei gleichzeitig installiert. **Für Azure Analysis Services werden die neuesten Versionen aller drei Bibliotheken benötigt**. 

Bei Microsoft-Clientanwendungen wie Power BI Desktop und Excel werden alle drei Clientbibliotheken installiert und dann entsprechend aktualisiert, wenn neue Versionen verfügbar sind. Abhängig von der Version oder der Updatehäufigkeit weisen einige Clientbibliotheken dabei unter Umständen nicht die neuesten Versionen auf, die von Azure Analysis Services benötigt werden. Dies gilt auch für benutzerdefinierte Anwendungen oder andere Schnittstellen wie AsCmd, TOM, ADOMD.NET. Für diese Anwendungen müssen die Bibliotheken manuell oder programmgesteuert installiert werden. Die Clientbibliotheken für die manuelle Installation sind in SQL Server-Funktionspaketen wie verteilbaren Paketen enthalten. Allerdings sind diese Clientbibliotheken an die SQL Server-Version gebunden und möglicherweise nicht auf dem neuesten Stand.  

Clientbibliotheken für Clientverbindungen unterscheiden sich von Datenanbietern, die für die Verbindung eines Azure Analysis Services-Servers mit einer Datenquelle erforderlich sind. Weitere Informationen zu den Datenquellenverbindungen finden Sie unter [Datenquellenverbindungen](analysis-services-datasource.md).

## <a name="client-library-types"></a>Typen von Clientbibliotheken

### <a name="analysis-services-ole-db-provider-msolap"></a>Analysis Services OLE DB-Anbieter (MSOLAP) 

 Der Analysis Services OLE DB-Anbieter (MSOLAP) ist die native Clientbibliothek für Analysis Services-Datenbankverbindungen. Er wird indirekt von ADOMD.NET und AMO genutzt, und Verbindungsanforderungen werden an den Datenanbieter delegiert. Sie können den OLE DB-Anbieter auch direkt über den Anwendungscode aufrufen.  
  
 Der Analysis Services OLE DB-Anbieter wird von den meisten Tools und Clientanwendungen, die zum Zugreifen auf Analysis Services-Datenbanken verwendet werden, automatisch installiert. Er muss auf Computern installiert sein, die zum Zugreifen auf Analysis Services-Daten verwendet werden.  
  
 OLE DB-Anbieter werden häufig in Verbindungszeichenfolgen angegeben. Für eine Analysis Services-Verbindungszeichenfolge wird eine andere Nomenklatur genutzt, um auf den OLE DB-Anbieter zu verweisen: MSOLAP.\<version>.dll.

### <a name="amo"></a>AMO  

 AMO ist eine verwaltete Clientbibliothek, die für die Serververwaltung und Datendefinition verwendet wird. Sie wird von Tools und Clientanwendungen installiert und genutzt. Für SQL Server Management Studio (SSMS) wird AMO beispielsweise zum Herstellen einer Verbindung mit Analysis Services genutzt. Eine Verbindung, für die AMO genutzt wird, ist meist eine minimale Verbindung, die aus `“data source=\<servername>”` besteht. Nach dem Herstellen einer Verbindung verwenden Sie die API, um mit Datenbanksammlungen und größeren Objekten zu arbeiten. AMO wird sowohl von SSDT als auch von SSMS verwendet, um eine Verbindung mit einer Analysis Services-Instanz herzustellen.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET ist eine verwaltete Datenclientbibliothek, die zum Abfragen von Analysis Services-Daten eingesetzt wird. Sie wird von Tools und Clientanwendungen installiert und genutzt. 
  
 Beim Herstellen einer Verbindung mit der Datenbank sind die Eigenschaften der Verbindungszeichenfolgen für alle drei Bibliotheken ähnlich. Fast jede Verbindungszeichenfolge, die Sie für ADOMD.NET per [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](https://msdn.microsoft.com/library/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring.aspx) definieren, funktioniert auch für AMO und den Analysis Services OLE DB-Anbieter (MSOLAP). Weitere Informationen finden Sie unter [Verbindungszeichenfolgen-Eigenschaften (Analysis Services)](https://docs.microsoft.com/sql/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="bkmk_LibUpdate"></a> Ermitteln der Version einer Clientbibliothek   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Wechseln Sie zu „C:\Programme\Microsoft c:\Programme\Microsoft Analysis Services\AS OLEDB\“. Wählen Sie den Ordner mit der höheren Zahl, falls Sie mehr als einen Ordner haben.
  
2.  Klicken Sie mit der rechten Maustaste auf **msolap.dll** > **Eigenschaften** > **Details**. Wenn der Dateiname „msolap140.dll“ lautet, ist sie älter als die aktuelle Version und muss aktualisiert werden.
    
    ![Details zur Clientbibliothek](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Wechseln Sie zur Adresse `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Wählen Sie den Ordner mit der höheren Zahl, falls Sie mehr als einen Ordner haben.
2. Klicken Sie mit der rechten Maustaste auf **Microsoft.AnalysisServices** > **Eigenschaften** > **Details**.  

### <a name="adomd"></a>ADOMD

1. Wechseln Sie zur Adresse `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Wählen Sie den Ordner mit der höheren Zahl, falls Sie mehr als einen Ordner haben.
2. Klicken Sie mit der rechten Maustaste auf **Microsoft.AnalysisServices.AdomdClient** > **Eigenschaften** > **Details**.  


## <a name="next-steps"></a>Nächste Schritte
[Herstellen einer Verbindung mit Excel](analysis-services-connect-excel.md)    
[Herstellen einer Verbindung mit Power BI](analysis-services-connect-pbi.md)
