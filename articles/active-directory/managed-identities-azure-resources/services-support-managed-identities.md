---
title: Azure-Dienste, die verwaltete Identitäten unterstützen – Azure AD
description: Liste der Dienste, die verwaltete Identitäten für Azure-Ressourcen und die Azure AD-Authentifizierung unterstützen
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 09/24/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b91d3bdf2ba4b6b30e7b3d5b748fd90921e2025
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841164"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Dienste, die verwaltete Identitäten für Azure-Ressourcen unterstützen

Verwaltete Identitäten für Azure-Ressourcen stellen für Azure-Dienste eine automatisch verwaltete Identität in Azure Active Directory bereit. Mit einer verwalteten Identität können Sie sich bei jedem Dienst authentifizieren, der die Azure AD-Authentifizierung unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. Verwaltete Identitäten für Azure-Ressourcen und die Azure AD-Authentifizierung werden derzeit in Azure integriert. Sie sollten sich regelmäßig über Aktualisierungen informieren.

> [!NOTE]
> „Verwaltete Identitäten für Azure-Ressourcen“ ist der neue Name für den Dienst, der früher als „Verwaltete Dienstidentität“ (Managed Service Identity, MSI) bezeichnet wurde.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure-Dienste, die verwaltete Identitäten für Azure-Ressourcen unterstützen

Die folgenden Azure-Dienste unterstützen verwaltete Identitäten für Azure-Ressourcen:

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Vom System zugewiesen | Verfügbar | Vorschau | Vorschau | Vorschau | 
| Vom Benutzer zugewiesen | Verfügbar | Vorschau | Vorschau | Vorschau |

Konfigurieren Sie die verwaltete Identität für Azure Virtual Machines anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure-Befehlszeilenschnittstelle](qs-configure-cli-windows-vm.md)
- [Azure-Ressourcen-Manager-Vorlagen](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Skalierungsgruppen für virtuelle Azure-Computer

|Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Vom System zugewiesen | Verfügbar | Vorschau | Vorschau | Vorschau |
| Vom Benutzer zugewiesen | Verfügbar | Vorschau | Vorschau | Vorschau |

Konfigurieren Sie die verwaltete Identität für Azure Virtual Machine Scale Sets anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure-Befehlszeilenschnittstelle](qs-configure-cli-windows-vm.md)
- [Azure-Ressourcen-Manager-Vorlagen](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Vom System zugewiesen | Verfügbar | Verfügbar | Verfügbar | Verfügbar |
| Vom Benutzer zugewiesen | Verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

Konfigurieren Sie die verwaltete Identität für Azure App Service anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure-Befehlszeilenschnittstelle](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-Vorlage](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprint

|Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Vom System zugewiesen | Verfügbar | Verfügbar | Nicht verfügbar | Nicht verfügbar |
| Vom Benutzer zugewiesen | Verfügbar | Verfügbar | Nicht verfügbar | Nicht verfügbar |

Verwenden Sie die folgende Liste, um eine verwaltete Identität mit [Azure Blueprints](../../governance/blueprints/overview.md) zu nutzen:

- [Azure-Portal: Blaupausenzuweisung](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST-API: Blaupausenzuweisung](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure-Funktionen

Typ der verwalteten Identität |Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Vom System zugewiesen | Verfügbar | Verfügbar | Verfügbar | Verfügbar |
| Vom Benutzer zugewiesen | Verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

Konfigurieren Sie die verwaltete Identität für Azure Functions anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure-Befehlszeilenschnittstelle](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-Vorlage](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Vom System zugewiesen | Vorschau | Vorschau | Nicht verfügbar | Vorschau |
| Vom Benutzer zugewiesen | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

Konfigurieren Sie die verwaltete Identität für Azure Logic Apps anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure portal](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Azure Resource Manager-Vorlage](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Vom System zugewiesen | Verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |
| Vom Benutzer zugewiesen | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

Konfigurieren Sie die verwaltete Identität für Azure Data Factory V2 anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Vom System zugewiesen | Verfügbar | Verfügbar | Nicht verfügbar | Nicht verfügbar |
| Vom Benutzer zugewiesen | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

Konfigurieren Sie die verwaltete Identität für Azure API Management anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure Resource Manager-Vorlage](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Vom System zugewiesen | Linux: Vorschau<br>Windows: Nicht verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |
| Vom Benutzer zugewiesen | Linux: Vorschau<br>Windows: Nicht verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

Konfigurieren Sie die verwaltete Identität für Azure Container Instances anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure-Befehlszeilenschnittstelle](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager-Vorlage](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Vom System zugewiesen | Verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |
| Vom Benutzer zugewiesen | Vorschau | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

Konfigurieren Sie die verwaltete Identität für Azure Container Registry Tasks anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure-Befehlszeilenschnittstelle](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-service-fabric"></a>Azure Service Fabric
[Verwaltete Identität für Service Fabric-Anwendungen](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) ist als Vorschauversion in allen Regionen verfügbar.

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Vom System zugewiesen | Verfügbar | Nicht verfügbar. | Nicht verfügbar. | Nicht verfügbar. |
| Vom Benutzer zugewiesen | Verfügbar | Nicht verfügbar. | Nicht verfügbar. |Nicht verfügbar. |

Informationen zum Konfigurieren der verwalteten Identität für Azure Service Fabric-Anwendungen in allen Regionen finden Sie in der folgenden Liste:
- [Azure Resource Manager-Vorlage](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-Dienste, die die Azure AD-Authentifizierung unterstützen

Die folgenden Dienste unterstützen die Azure AD-Authentifizierung und wurden mit Clientdiensten getestet, die verwaltete Identitäten für Azure-Ressourcen verwenden.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Konfigurieren Sie den Zugriff auf Azure Resource Manager anhand der folgenden Liste:

- [Zuweisen des Zugriffs im Azure-Portal](howto-assign-access-portal.md)
- [Zuweisen des Zugriffs mithilfe von PowerShell](howto-assign-access-powershell.md)
- [Zuweisen des Zugriffs mithilfe der Azure CLI](howto-assign-access-CLI.md)
- [Zuweisen des Zugriffs mit Azure Resource Manager-Vorlagen](../../role-based-access-control/role-assignments-template.md)

| Cloud | Ressourcen-ID | Status |
|--------|------------|--------|
| Azure Global | `https://management.azure.com/`| Verfügbar |
| Azure Government | `https://management.usgovcloudapi.net/` | Verfügbar |
| Azure Deutschland | `https://management.microsoftazure.de/` | Verfügbar |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | Verfügbar |

### <a name="azure-key-vault"></a>Azure-Schlüsseltresor

| Cloud | Ressourcen-ID | Status |
|--------|------------|--------|
| Azure Global | `https://vault.azure.net`| Verfügbar |
| Azure Government | `https://vault.usgovcloudapi.net` | Verfügbar |
| Azure Deutschland |  `https://vault.microsoftazure.de` | Verfügbar |
| Azure China 21Vianet | `https://vault.azure.cn` | Verfügbar |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Cloud | Ressourcen-ID | Status |
|--------|------------|--------|
| Azure Global | `https://datalake.azure.net/` | Verfügbar |
| Azure Government |  | Nicht verfügbar. |
| Azure Deutschland |   | Nicht verfügbar. |
| Azure China 21Vianet |  | Nicht verfügbar. |

### <a name="azure-sql"></a>Azure SQL 

| Cloud | Ressourcen-ID | Status |
|--------|------------|--------|
| Azure Global | `https://database.windows.net/` | Verfügbar |
| Azure Government | `https://database.usgovcloudapi.net/` | Verfügbar |
| Azure Deutschland | `https://database.cloudapi.de/` | Verfügbar |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | Verfügbar |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Cloud | Ressourcen-ID | Status |
|--------|------------|--------|
| Azure Global | `https://eventhubs.azure.net` | Verfügbar |
| Azure Government |  | Nicht verfügbar. |
| Azure Deutschland |   | Nicht verfügbar. |
| Azure China 21Vianet |  | Nicht verfügbar. |

### <a name="azure-service-bus"></a>Azure-Servicebus

| Cloud | Ressourcen-ID | Status |
|--------|------------|--------|
| Azure Global | `https://servicebus.azure.net`  | Verfügbar |
| Azure Government |  | Verfügbar |
| Azure Deutschland |   | Nicht verfügbar. |
| Azure China 21Vianet |  | Nicht verfügbar. |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage-Blobs und -Warteschlangen

| Cloud | Ressourcen-ID | Status |
|--------|------------|--------|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Verfügbar |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | Verfügbar |
| Azure Deutschland | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | Verfügbar |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | Verfügbar |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Cloud | Ressourcen-ID | Status |
|--------|------------|--------|
| Azure Global | `https://*.asazure.windows.net` | Verfügbar |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | Verfügbar |
| Azure Deutschland | `https://*.asazure.cloudapi.de` | Verfügbar |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | Verfügbar |
