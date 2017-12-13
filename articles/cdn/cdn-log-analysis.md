---
title: Analysieren von Azure CDN-Verwendungsmustern | Microsoft Docs
description: "Kunden können die Protokollanalyse für Azure CDN aktivieren."
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: v-semcev
ms.openlocfilehash: ce9ec021250a3548e23ad87273a9225cdf700c70
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2017
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analysieren von Azure CDN-Verwendungsmustern

Nachdem Sie CDN für Ihre Anwendung aktiviert haben, können Sie die CDN-Nutzung überwachen, die Integrität Ihrer Bereitstellung überprüfen und potenzielle Probleme behandeln. Es gibt die folgenden Möglichkeiten für die Bereitstellung dieser Funktionen über Azure CDN: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Kernanalyse über Azure-Diagnoseprotokolle

Die Kernanalyse ist für alle CDN-Endpunkte verfügbar, die zu CDN-Profilen von Verizon (Standard und Premium) und Akamai (Standard) gehören. Durch die Azure-Diagnoseprotokolle kann die Kernanalyse in den Azure-Speicher, Event Hubs oder OMS Log Analytics (OMS = Operations Management Suite) exportiert werden. OMS Log Analytics bietet eine Lösung mit Diagrammen, die von Benutzern konfiguriert und angepasst werden können. Weitere Informationen finden Sie unter [Azure-Diagnoseprotokolle](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Verizon-Kernberichte

Als Benutzer von Azure CDN mit einem Standard- oder Premiumprofil bei Verizon können Sie im Verizon-Portal Verizon-Kernberichte anzeigen. Auf Verizon-Kernberichte kann im Azure-Portal über die Option **Verwalten** zugegriffen werden. Sie bieten eine Vielzahl von Diagrammen und Ansichten. Weitere Informationen finden Sie unter [Kernberichte aus Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Benutzerdefinierte Verizon-Berichte

Als Benutzer von Azure CDN mit einem Standard- oder Premiumprofil bei Verizon können Sie im zusätzlichen Verizon-Portal benutzerdefinierte Verizon-Berichte anzeigen. Auf benutzerdefinierte Verizon-Berichte kann im Azure-Portal über die Option **Verwalten** zugegriffen werden. Auf der Seite für benutzerdefinierte Verizon-Berichte wird die Anzahl der Treffer oder übertragenen Daten für jeden „Edge CName“ angezeigt, der zu einem Azure CDN-Profil gehört. Die Daten können über einen beliebigen Zeitraum nach HTTP-Antwortcode oder Cachestatus gruppiert werden. Weitere Informationen finden Sie unter [Benutzerdefinierte Berichte aus Verizon](cdn-verizon-custom-reports.md).

## <a name="verizon-premium-reports"></a>Verizon Premium-Berichte

Mit **Azure CDN Premium von Verizon** können Sie darüber hinaus auf die folgenden Berichte zugreifen:
   * [Erweiterte HTTP-Berichte](cdn-advanced-http-reports.md)
   * [Echtzeitstatistiken](cdn-real-time-stats.md)
   * [Edgeknotenleistung](cdn-edge-performance.md)

