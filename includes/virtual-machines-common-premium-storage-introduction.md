---
title: include file
description: include file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b1287f9c7e946c7b4d035b2ad6301947ffad3cea
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717120"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Storage Premium: Entwurf für hohe Leistung

Dieser Artikel bietet Leitlinien zum Erstellen leistungsstarker Anwendungen mit Azure Storage Premium. Sie können die Anweisungen in diesem Dokument kombiniert mit den bewährten Methoden für hohe Leistung befolgen, die für von Ihrer Anwendung verwendeten Technologien gelten. Um die Leitlinien zu veranschaulichen, haben wir SQL Server in Storage Premium im gesamten Dokument als Beispiel verwendet.

Während wir uns in diesem Artikel mit leistungsbezogenen Szenarien auf der Speicherebene beschäftigen, ist es Ihre Aufgabe, die Anwendungsebene zu optimieren. Wenn z. B. Sie eine SharePoint-Farm in Azure Storage Premium hosten, können die SQL Server-Beispiele in diesem Artikel zum Optimieren des Datenbankservers verwenden. Optimieren Sie darüber hinaus den Webserver und Anwendungsserver der SharePoint-Farm, um die beste Leistung zu erzielen.

In diesem Artikel werden häufig gestellte Fragen zum Optimieren der Anwendungsleistung in Azure Storage Premium beantwortet:

* Wie kann die Leistung Ihrer Anwendung gemessen werden?  
* Warum erzielen Sie nicht die erwartete hohe Leistung?  
* Welche Faktoren beeinflussen die Anwendungsleistung in Storage Premium?  
* Wie wirken sich diese Faktoren auf die Leistung Ihrer Anwendung in Storage Premium aus?  
* Wie kann eine Optimierung hinsichtlich IOPS, Bandbreite und Latenz erfolgen?  

Wir stellen diese Leitlinien speziell für Storage Premium bereit, da in Storage Premium ausgeführte Workloads überaus leistungsabhängig sind. Sofern hilfreich, stellen wir Beispiele bereit. Einige dieser Leitlinien können auch für Anwendungen befolgt werden, die auf virtuellen IaaS-Computern mit Storage Standard-Datenträgern ausgeführt werden.