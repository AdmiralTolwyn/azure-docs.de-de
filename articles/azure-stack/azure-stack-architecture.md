---
title: "Microsoft Azure Stack Development Kit – Architektur | Microsoft-Dokumentation"
description: Lernen Sie die Architektur des Microsoft Azure Stack Development Kits kennen.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: b754ff5b5a82ac284eb59ff9b9d30a581f3d3af5
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/24/2018
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Microsoft Azure Stack Development Kit – Architektur

*Gilt für: Azure Stack Development Kit*

Das Azure Stack Development Kit ist eine Bereitstellung von Azure Stack mit einem Knoten. Alle Komponenten werden auf virtuellen Computern installiert, die auf einem einzigen Hostcomputer ausgeführt werden. 

## <a name="logical-architecture-diagram"></a>Logisches Architekturdiagramm
Das folgende Diagramm veranschaulicht die logische Architektur des Azure Stack Development Kits und der dazugehörigen Komponenten.

![](media/azure-stack-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Rollen virtueller Computer
Das Azure Stack Development Kit bietet Dienste mithilfe der folgenden virtuellen Computer auf dem Host:

| NAME | BESCHREIBUNG |
| ----- | ----- |
| **AzS-ACS01** | Azure Stack-Speicherdienste|
| **AzS-ADFS01** | Active Directory-Verbunddienste (ADFS)  |
| **AzS-BGPNAT01** | Edgerouter, bietet NAT- und VPN-Funktionen für Azure Stack |
| **AzS-CA01** | Zertifizierungsstellendienste für Azure Stack-Rollendienste|
| **AzS-DC01** | Active Directory, DNS und DHCP-Dienste für Microsoft Azure Stack|
| **AzS-ERCS01** | Notfallwiederherstellungskonsolen-VM |
| **AzS-GWY01** | Edge-Gateway-Dienste, z.B. VPN-Site-to-Site-Verbindungen für Mandantennetzwerke|
| **AzS-NC01** | Netzwerkcontroller, der Azure Stack-Netzwerkdienste verwaltet  |
| **AzS-SLB01** | Lastenausgleichs-Multiplexerdienste in Azure Stack für beide Mandanten und Azure Stack-Infrastrukturdienste  |
| **AzS-SQL01** | Interner Datenspeicher für Azure Stack-Infrastrukturrollen  |
| **AzS-WAS01** | Azure Stack-Verwaltungsportal und Azure Resource Manager-Dienste|
| **AzS-WASP01**| Azure Stack-Benutzerportal (Mandantenportal) und Azure Resource Manager-Dienste|
| **AzS-XRP01** | Infrastrukturverwaltungscontroller für Microsoft Azure Stack, einschließlich Compute-, Netzwerk- und Speicherressourcenanbietern|


## <a name="next-steps"></a>Nächste Schritte
[Bereitstellen von Azure Stack](azure-stack-deploy.md)

[Erste Testszenarios](azure-stack-first-scenarios.md)

