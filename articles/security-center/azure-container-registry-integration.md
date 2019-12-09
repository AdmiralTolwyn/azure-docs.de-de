---
title: Azure Security Center und Azure Container Registry
description: Erfahren Sie mehr über die Integration von Azure Security Center in Azure Container Registry.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/19/2019
ms.author: memildin
ms.openlocfilehash: 0ca7bfb276f49da720264305a92d31e81857cfd5
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229322"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Integration von Security Center in Azure Container Registry (Vorschau)

Azure Container Registry (ACR) ist ein verwalteter, privater Docker-Registrierungsdienst, der Ihre Containerimages für Azure-Bereitstellungen in einer zentralen Registrierung speichert und verwaltet. Er basiert auf der Open-Source-Docker-Registrierung 2.0.

Benutzer des Standard-Tarifs von Azure Security Center können das optionale Containerregistrierungspaket aktivieren, um tiefere Einblicke in die Sicherheitsrisiken der Registrierung und Images zu erhalten. Weitere Informationen finden Sie unter [Azure Data Lake Storage – Preise](security-center-pricing.md). Wenn das Paket aktiviert ist, scannt Security Center automatisch Images in der Registrierung, wenn ein Image per Push an die Registrierung übertragen wird.

> [!NOTE]
> Die erste Überprüfung einer Registrierung durch Security Center erfolgt erst, nachdem das Containerregistrierungspaket aktiviert und ein Image per Push an die Registrierung übertragen wurde.

Wenn die Überprüfung abgeschlossen ist (in der Regel nach ungefähr 10 Minuten), sind die Ergebnisse in Security Center-Empfehlungen wie folgt verfügbar:

[![Beispiel einer Azure Security Center-Empfehlung über in einem gehosteten Azure Container Registry-Image (ACR) erkannte Sicherheitsrisiken](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Vorteile der Integration

Security Center identifiziert ACR-Registrierungen in Ihrem Abonnement und bietet nahtlos Folgendes:

* **Azure-native Sicherheitsrisikoüberprüfung** für alle gepushten Linux-Images. Security Center überprüft das Image mithilfe eines Scanners von Qualys, dem branchenführenden Hersteller von Anwendungen zur Sicherheitsrisikoüberprüfung. Diese native Lösung ist standardmäßig nahtlos integriert.

* **Sicherheitsempfehlungen** für Linux-Images mit bekannten Sicherheitsrisiken. Security Center bietet Details zu den einzelnen gemeldeten Sicherheitsrisiken und eine Schweregradklassifizierung. Außerdem erhalten Sie Anleitungen zur Behebung der spezifischen Sicherheitsrisiken, die für jedes in die Registrierung gepushte Image gefunden wurden.

![Allgemeine Übersicht zu Azure Security Center und Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Containersicherheitsfeatures von Security Center finden Sie unter:

* [Containersicherheit in Security Center](container-security.md)

* [Integration mit Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* [Schützen von Computern und Anwendungen in Azure Security Center](security-center-virtual-machine-protection.md) – beschreibt Empfehlungen des Security Center