---
title: "Anzeigen verknüpfter Datenobjekte in Azure Data Catalog | Microsoft-Dokumentation"
description: "Dieser Artikel beschreibt, wie Sie in Azure Data Catalog die verknüpften Datenobjekte eines ausgewählten Datenobjekts anzeigen."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/17/2017
ms.author: maroche
ms.openlocfilehash: d45f2cabe712a7982f99a9d280fed4494fc4d377
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Anzeigen verknüpfter Datenobjekte in Azure Data Catalog
Azure Data Catalog ermöglicht Ihnen das Anzeigen von Datenobjekten, die mit einem ausgewählten Datenobjekt verknüpft sind, und der Beziehungen zwischen beiden. 

## <a name="supported-data-sources"></a>Unterstützte Datenquellen 
Wenn Sie Datenobjekte aus den folgenden Datenquellen registrieren, registriert Azure Data Catalog automatisch Metadaten zu den Verknüpfungsbeziehungen zwischen den ausgewählten Datenobjekten. 

- SQL Server
- Azure SQL-Datenbank
- MySQL
- Oracle

## <a name="view-related-data-assets"></a>Anzeigen verknüpfter Datenobjekte
Verwenden Sie zum Anzeigen von Datenobjekten, die mit einem ausgewählten Dataset verknüpft sind, die Registerkarte **Beziehungen** wie in der folgenden Abbildung gezeigt: 

![Azure Data Catalog: Anzeigen verknüpfter Datenobjekte](media\data-catalog-how-to-view-related-data-assets\relationships-tab.png)

In diesem Beispiel, es gibt zwei Beziehungen für das ausgewählte Datenobjekt **ProductSubCategory**: 

- Die Spalte „ProductSubcategoryID“ der Tabelle „Product“ hat eine Fremdschlüsselbeziehung mit der Spalte „ProductSubcategoryID“ der ausgewählten Tabelle „ProductSubcategory“. 
- Die Spalte „ProductCategoryID“ der Tabelle „ProductSubCategory“ hat eine Fremdschlüsselbeziehung mit der Spalte „ProductCategoryID“ der ausgewählten Tabelle „ProductCategory“.

> [!NOTE]
> Beachten Sie die Richtung des Pfeils in der Strukturansicht der Beziehungen.  

Um weitere Details anzuzeigen, wie z.B. den vollqualifizierten Namen der Spalte, bewegen Sie den Mauszeiger darüber, woraufhin wie in der folgenden Abbildung ein Popup eingeblendet wird: 

![Azure Data Catalog: Popup zu Beziehungen](media\data-catalog-how-to-view-related-data-assets\relationship-popup.png)

Um Beziehungen zwischen Objekte einzuschließen, die bereits registriert wurden, müssen Sie diese Objekte erneut registrieren.

## <a name="next-steps"></a>Nächste Schritte
- [Verwalten von Datenassets](data-catalog-how-to-manage.md)