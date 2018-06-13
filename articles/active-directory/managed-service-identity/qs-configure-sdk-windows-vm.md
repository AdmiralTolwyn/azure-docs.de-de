---
title: Konfigurieren eines MSI-fähigen virtuellen Azure-Computers mithilfe eines Azure SDK
description: Schrittweise Anweisungen zum Konfigurieren und Verwenden einer verwalteten Dienstidentität (Managed Service Identity, MSI) auf einem virtuellen Azure-Computer mithilfe eines Azure SDK
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/28/2017
ms.author: daveba
ms.openlocfilehash: 781f332b2892d9af536bf9a6f81642842285927b
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2018
ms.locfileid: "33930806"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-an-azure-sdk"></a>Konfigurieren einer VM-MSI mithilfe eines Azure SDK

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Eine verwaltete Dienstidentität stellt für Azure-Dienste eine automatisch verwaltete Identität in Azure Active Directory (AD) bereit. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. 

In diesem Artikel erfahren Sie, wie Sie MSI für einen virtuellen Azure-Computer mit einem Azure SDK aktivieren und entfernen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-msi-support"></a>Azure SDKs mit MSI-Unterstützung 

Azure unterstützt mit einer Reihe von [Azure SDKs](https://azure.microsoft.com/downloads) mehrere Programmierplattformen. Mehrere davon wurden aktualisiert, um MSI zu unterstützen, und enthalten entsprechende Beispiele, um die Nutzung zu demonstrieren. Diese Liste wird jeweils aktualisiert, wenn weitere Unterstützung hinzugefügt wird:

| SDK | Beispiel |
| --- | ------ | 
| .NET   | [Manage resource from an MSI-enabled VM](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) (Verwalten von Ressourcen über eine MSI-fähige VM) |
| Java   | [Manage Storage from an MSI-enabled VM](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/) (Verwalten von Speicher über eine MSI-fähige VM)|
| Node.js| [Create a VM with MSI authentication enabled](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) (Erstellen eines virtuellen Computers mit aktivierter MSI-Authentifizierung) |
| Python | [Create a VM with MSI authentication enabled](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) (Erstellen eines virtuellen Computers mit aktivierter MSI-Authentifizierung) |
| Ruby   | [Create Azure virtual machines with Managed Service Identity using Ruby](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) (Erstellen virtueller Azure-Computer mit verwalteter Dienstidentität mithilfe von Ruby) |

## <a name="next-steps"></a>Nächste Schritte

- Informationen dazu, wie Sie auch das Azure-Portal, PowerShell, die CLI und Ressourcenvorlagen verwenden können, finden Sie in den verwandten Artikeln unter „Configure MSI for an Azure VM“ (Konfigurieren von MSI für einen virtuellen Azure-Computer).

Verwenden Sie den folgenden Kommentarabschnitt, um uns Feedback zu senden und uns bei der Verbesserung unserer Inhalte zu unterstützen.
