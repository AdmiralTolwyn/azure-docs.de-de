---
title: Anbieten von Diensten in Azure Stack | Microsoft-Dokumentation
description: "Als Cloudbetreiber können Sie Dienste für Ihre Benutzer anbieten."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: erikje
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 7a54771d99f2719fcc345496b152a5d3acc02121
ms.contentlocale: de-de
ms.lasthandoff: 09/25/2017

---
# <a name="overview-of-offering-services-in-azure-stack"></a>Übersicht über das Anbieten von Diensten in Azure Stack

*Gilt für: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

[Microsoft Azure Stack](azure-stack-poc.md) ist eine Hybrid-Cloudplattform, die das Bereitstellen von Diensten aus Ihrem Datencenter ermöglicht. Als Dienstanbieter können Sie Dienste für Ihre Mandanten anbieten. Innerhalb eines Unternehmens oder einer Behörde ist es möglich, den Mitarbeitern lokale Dienste anzubieten. Die Dienste, die Sie bereitstellen können umfassen u.a.:

- PaaS-Dienste (Platform-as-a-Service) wie App Services, Mobile Apps, API-Apps, API-Funktionen, SQL, MySQL.

Sie können auch Dienste kombinieren, um komplexe Lösungen für verschiedene Benutzer zu integrieren und zu erstellen.

Sie müssen [Pläne, Angebote und Kontingente](azure-stack-plan-offer-quota-overview.md) erstellen, um diese Dienste für Ihre Benutzer bereitzustellen. Ihre Benutzer können Ihre Angebote dann abonnieren, um die Dienste zu nutzen.

## <a name="plan-your-service-offers"></a>Planen Ihrer Dienstangebote

Berücksichtigen Sie beim Planen Ihrer Angebote Folgendes:

**Testangebote**: Testangebote sind praktisch, um neue Benutzer zu gewinnen, die dann ein Upgrade für zusätzliche Dienste durchführen können. Erstellen Sie für ein Testangebot einen kleinen [Basisplan](azure-stack-plan-offer-quota-overview.md#base-plan) mit einem optionalen größeren Add-On-Plan.

**Kapazitätsplanung**: Möglicherweise haben Sie Bedenken, dass Benutzer große Mengen an Ressourcen abrufen und das System für alle Benutzer lahmlegen. Sie können [Ihre Pläne mit Kontingenten konfigurieren](azure-stack-plan-offer-quota-overview.md#plans), um eine Obergrenze für die Nutzung festzulegen und so die Leistung zu verbessern.

**Delegierte Anbieter**: Bieten Sie anderen die Möglichkeit, Angebote in Ihrer Umgebung zu erstellen. Wenn Sie z.B. ein Dienstanbieter sind, können Sie diese Möglichkeit an Ihre Wiederverkäufer [delegieren](azure-stack-delegated-provider.md). Oder Unternehmen nutzen diese Möglichkeit, um solche Optionen an bestimmte Abteilungen oder Niederlassungen zu delegieren.

## <a name="next-steps"></a>Nächste Schritte
[Erfahren Sie mehr über Angebote, Pläne, Kontingente und Abonnements](azure-stack-plan-offer-quota-overview.md)


