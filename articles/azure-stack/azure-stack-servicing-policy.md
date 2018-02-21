---
title: Azure Stack-Wartungsrichtlinie | Microsoft-Dokumentation
description: "Hier erhalten Sie Informationen zur Azure Stack-Wartungsrichtlinie und erfahren, wie Sie den unterstützten Zustand eines integrierten Systems aufrechterhalten."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 13155349775e71e777e868b6cd311d2e8683706d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2018
---
# <a name="azure-stack-servicing-policy"></a>Azure Stack-Wartungsrichtlinie

*Gilt für: Integrierte Azure Stack-Systeme*

Dieser Artikel beschreibt die Wartungsrichtlinie für integrierte Azure Stack-Systeme, und was Sie tun müssen, um den unterstützten Zustand Ihres Systems aufrechtzuerhalten. 

## <a name="update-package-types"></a>Updatepakettypen

Es gibt zwei Arten von Updatepaketen für integrierte Systeme: Softwareupdates von Microsoft und spezifische Updates Ihres Originalgerätehersteller-Hardwareanbieters (Original Equipment Manufacturer, OEM) wie Treiber und Firmware. Diese Updates werden als separate Azure Stack-Updatepakete bereitgestellt und unabhängig voneinander verwaltet.

- **Softwareupdates von Microsoft**. Microsoft ist für den End-to-End-Wartungslebenszyklus der Softwareupdatepakete von Microsoft verantwortlich. Diese Pakete können die neuesten Sicherheitsupdates von Windows Server, nicht sicherheitsrelevante Updates und Updates von Azure Stack-Feature enthalten. Sie können diese Updatepakete direkt von Microsoft herunterladen.
- **Updates des OEM-Hardwareanbieters**. Azure Stack-Hardwarepartner sind für den End-to-End-Wartungslebenszyklus (inklusive Leitung) der hardwarebezogenen Firmware- und Treiberupdatepakete verantwortlich. Darüber hinaus obliegt den Azure Stack-Hardwarepartnern die Leitung für die gesamte Software und Hardware des Hardwarelebenszyklus-Hosts. Die OEM-Hardwareanbieter hosten diese Updatepakete auf ihren Downloadwebsites.

## <a name="update-package-release-cadence"></a>Updatepaketrelease-Intervall

Microsoft veröffentlicht Softwareupdatepakete in der Regel monatlich. Allerdings ist es möglich, dass in einem Monat mehrere oder keine Updatereleases veröffentlicht werden. OEM-Hardwareanbieter veröffentlichen ihre Updates nach Bedarf.

Die folgenden Benennungskonventionen eines Microsoft-Updatepakets erleichtern Ihnen, das Veröffentlichungsdatum zu identifizieren:

*MajorProductVersion.MinorProductVersion.YYMMDD.BuildNumber*

Beispielsweise könnte ein am 15. Juni 2017 veröffentlichtes Microsoft-Softwareupdate die Versionsnummer „1.0.170615.1“ haben.

## <a name="keep-your-system-under-support"></a>Halten Sie den Support für Ihr System aufrecht

Um Support für Ihr System zu erhalten, müssen Sie Ihren Azure Stack innerhalb eines bestimmten Zeitintervalls auf aktuellem Stand halten. Unsere Richtlinie für den Aufschub der Microsoft-Softwareupdates sind drei Monate. Wenn Ihr System mehr als drei Monate veraltet ist, ist die Konformität nicht mehr gegeben. Sie müssen das System mindestens auf die niedrigste unterstützte Version aktualisieren, um Support zu erhalten. 

Microsoft-Softwareupdatepakete sind nicht kumulativ und setzen das vorherige Updatepaket voraus. Wenn Sie eines oder mehrere Updates aufschieben möchten, berücksichtigen Sie die gesamte Laufzeit, wenn Sie auf die neueste Version aktualisieren möchten.

Die folgende Tabelle zeigt Beispiele für Updatepaketreleases, deren Voraussetzungen und die unterstützte Mindestversion, die Ihr System aufweisen muss, um Support zu erhalten. Die Tabelle basiert auf dem ersten Release der integrierten Azure Stack-Systeme (Build 1708) mit dem ersten Updatepaketrelease (1709) im September 2017. 

| Neuestes Updatepaket (*Beispiel*) | Voraussetzung | Unterstützte Mindestversion |
| -- | -- | -- |
| 1710 | 1709 | N/V |
| 1711 | 1710 | 1709 |
| 1.712 | 1711 | 1710 |
| 1802 | 1801 | 1.712 |
| 1803 | 1802 | 1801 |
| 1804 | 1803 | 1802 |
| 1805 | 1804 | 1803 |
| | | 

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Updates in Azure Stack](azure-stack-updates.md)


