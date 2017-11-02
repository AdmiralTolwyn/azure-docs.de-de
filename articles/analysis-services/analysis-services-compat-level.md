---
title: "Kompatibilitätsgrad von Datenmodellen in Azure Analysis Services | Microsoft-Dokumentation"
description: "Grundlegendes zum Kompatibilitätsgrad von tabellarischen Datenmodellen"
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
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 41bf697cd1d91ea0ba56ed6fad0e5b9c442ecec2
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2017
---
# <a name="compatibility-level-for-analysis-services-tabular-models"></a>Kompatibilitätsgrad für tabellarische Analysis Services-Modelle

Der *Kompatibilitätsgrad* bezieht sich auf releasespezifische Verhalten im Analysis Services-Modul. Änderungen am Kompatibilitätsgrad werden üblicherweise im Zuge der Hauptreleases von SQL Server vorgenommen. Diese Änderungen werden auch in Azure Analysis Services implementiert, um die Parität zwischen beiden Plattformen aufrechtzuerhalten. Änderungen am Kompatibilitätsgrad wirken sich auch auf die in Ihren tabellarischen Modellen verfügbaren Funktionen aus. Beispielsweise weisen DirectQuery und tabellarische Objektmetadaten je nach Kompatibilitätsgrad verschiedene Implementierungen auf. 

Azure Analysis Services unterstützt tabellarische Modelle mit den Kompatibilitätsgraden 1200 und 1400.

Der aktuelle Kompatibilitätsgrad ist 1400. Der jeweilige Grad stimmt mit SQL Server 2017 Analysis Services überein. Zu den wesentlichen Features im Kompatibilitätsgrad 1400 zählen Folgende:

*  Neue Infrastruktur für die Datenkonnektivität und den Import in tabellarische Modelle mit Unterstützung für TOM-APIs und TMSL-Skripts. Diese neue Feature aktiviert die Unterstützung für zusätzliche Datenquellen wie etwa Azure Blob Storage.
*  Datentransformations- und Datenmashupfunktionen mithilfe von Get Data- und M-Ausdrücken
*  Measures bieten Unterstützung für eine Detailzeileneigenschaft mit einem DAX-Ausdruck. Diese Eigenschaft aktiviert Clienttools wie Microsoft Excel, um ein Drilldown auf detaillierte Daten aus einem aggregierten Bericht auszuführen. Wenn Benutzer beispielsweise den monatlichen Gesamtumsatz für eine Region anzeigen, können sie die zugehörigen Auftragsdetails einsehen. 
*  Sicherheit auf Objektebene für Tabellen- und Spaltennamen sowie die darin enthaltenen Daten
*  Verbesserte Unterstützung für unregelmäßige Hierarchien
*  Verbesserungen an der Leistung und Überwachung
  
## <a name="set-compatibility-level"></a>Festlegen des Kompatibilitätsgrads 
 Beim Erstellen eines neuen Projekts für tabellarische Modelle in SSDT können Sie im Dialogfeld **Designer für tabellarische Modelle** den Kompatibilitätsgrad angeben. 
  
 ![ssas_tabularproject_compat1200](./media/analysis-services-compat-level/aas-tabularproject-compat.png)  
  
 Wenn Sie die Option **Diese Meldung nicht mehr anzeigen** auswählen, wird bei allen nachfolgenden Projekten der als Standard angegebene Kompatibilitätsgrad verwendet. Sie können den Standardkompatibilitätsgrad in SSDT unter **Extras** > **Optionen** ändern.  
  
 Um für ein vorhandenes Projekt für tabellarische Modelle ein Upgrade in SSDT durchzuführen, legen Sie im Fenster **Eigenschaften** des Modells die Eigenschaft **Kompatibilitätsgrad** fest. Denken Sie daran, dass ein Upgrade des Kompatibilitätsgrads nicht rückgängig gemacht werden kann.
  
## <a name="check-compatibility-level-for-a-tabular-model-database-in-sql-server-management-studio"></a>Überprüfen des Kompatibilitätsgrads einer tabellarischen Modelldatenbank in SQL Server Management Studio 
 Klicken Sie in SSMS mit der rechten Maustaste auf den Datenbanknamen und dann auf **Eigenschaften** > **Kompatibilitätsgrad**.  
  
## <a name="check-supported-compatibility-level-for-a-server-in-ssms"></a>Überprüfen des unterstützten Kompatibilitätsgrads eines Servers in SSMS  
 Klicken Sie in SSMS mit der rechten Maustaste auf den Servernamen und dann auf **Eigenschaften** > **Unterstützter Kompatibilitätsgrad**.  
  
 Diese Eigenschaft gibt den höchsten Kompatibilitätsgrad einer Datenbank an, die auf dem Server (ausgenommen der Vorschau) ausgeführt wird. Der unterstützte Kompatibilitätsgrad kann nicht geändert werden.  

## <a name="next-steps"></a>Nächste Schritte
  [Erstellen eines Modells im Azure-Portal](analysis-services-create-model-portal.md)   
  [Verwalten von Analysis Services](analysis-services-manage.md)  
