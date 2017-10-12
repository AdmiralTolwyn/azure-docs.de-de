---
title: Azure Deutschland-Entwicklerhandbuch | Microsoft-Dokumentation
description: "Dieser Artikel enthält einen Featurevergleich und Hilfestellung zum Entwickeln von Anwendungen für Azure Deutschland."
services: germany
cloud: na
documentationcenter: na
author: gitralf
manager: rainerst
ms.assetid: na
ms.service: germany
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2017
ms.author: ralfwi
ms.openlocfilehash: 5faafd1be168d7acc6cfaee8448f9a780ba23149
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-germany-developer-guide"></a>Microsoft Azure Deutschland – Entwickler- und Benutzerhandbuch
Bei der Azure Deutschland-Umgebung handelt es sich um eine Instanz von Microsoft Azure, die vom Rest des Microsoft-Netzwerks getrennt ist. Dieses Handbuch erläutert die Unterschiede, die Anwendungsentwickler und -administratoren kennen müssen, um mit dieser separaten Region von Azure interagieren und arbeiten zu können.

## <a name="overview"></a>Übersicht
Microsoft bietet verschiedene Tools, die Entwicklern beim Erstellen und Bereitstellen von Cloudanwendungen für die globalen Microsoft Azure-Dienste („globale Azure-Umgebung“) und Microsoft Azure Deutschland-Dienste helfen. Azure Deutschland berücksichtigt die Sicherheits- und Complianceanforderungen der Kunden, um den deutschen Datenschutzbestimmungen gerecht zu werden. Azure Deutschland ist physisch wie auch netzwerkseitig von Bereitstellungen in der globalen Azure-Umgebung isoliert und stellt einen Datentreuhänder zur Verfügung, der nach deutschem Recht handelt.

Bei der Erstellung und Bereitstellung von Anwendungen müssen Entwickler die Unterschiede zwischen Azure Deutschland und der globalen Azure-Umgebung kennen. Dies gilt insbesondere für die folgenden Bereiche: Einrichtung und Konfiguration der Programmierumgebung, Konfiguration der Endpunkte, Erstellung von Anwendungen sowie deren Bereitstellung als Dienste für Azure Deutschland.

Dieses Handbuch fasst die Unterschiede zusammen. Es ergänzt die Informationen, die auf den Websites [Microsoft Azure Deutschland](https://azure.microsoft.com/overview/clouds/germany/ "Microsoft Azure Deutschland") und [Erste Schritte mit Azure](https://azure.microsoft.com/documentation/) verfügbar sind. 

Offizielle Informationen sind möglicherweise auch an anderer Stelle verfügbar, z.B.:
* [Die vertrauenswürdige Cloud](https://azure.microsoft.com/support/trust-center/ "Die vertrauenswürdige Cloud") 
* [Microsoft Azure-Blog](https://azure.microsoft.com/blog/ "Microsoft Azure-Blog")
* [Azure Deutschland-Blog](https://blogs.msdn.microsoft.com/azuregermany/ "Azure Deutschland-Blog")

## <a name="guidance-for-developers"></a>Leitfaden für Entwickler
Bei den meisten der derzeit verfügbaren technischen Inhalte wird davon ausgegangen, dass Anwendungen für die globale Azure-Umgebung und nicht für Azure Deutschland entwickelt werden. Aus diesem Grund ist es wichtig, zwei Hauptunterschiede bei Anwendungen zu kennen, die Sie zum Hosten in Azure Deutschland entwickeln:

* Bestimmte Dienste und Funktionen, die in bestimmten Regionen der globalen Azure-Umgebung vorhanden sind, sind möglicherweise in Azure Deutschland nicht verfügbar.
* Die Featurekonfigurationen in Azure Deutschland können sich von denen in der globalen Azure-Umgebung unterscheiden. Sie sollten Ihren Beispielcode, Ihre Konfigurationen und Schritte überprüfen, um sicherzustellen, dass die Erstellung und Ausführung innerhalb der Cloud Services-Umgebung von Azure Deutschland erfolgt.

Aktuell sind in Azure Deutschland die Regionen „Deutschland, Mitte“ und „Deutschland, Nordosten“ verfügbar. Regionen und verfügbare Dienste finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/regions/services).


## <a name="endpoint-mapping"></a>Endpunktzuordnung
In der folgenden Tabelle finden Sie Informationen für die Zuordnung von Endpunkten in der globalen Azure-Umgebung zu Azure Deutschland-spezifischen Endpunkten:

| Name | Azure Deutschland-Endpunkt |
| --- | --- |
| ActiveDirectoryServiceEndpointResourceId | https://management.core.cloudapi.de/ |
| GalleryUrl                               | https://gallery.cloudapi.de/ |
| ManagementPortalUrl                      | http://portal.microsoftazure.de/ |
| ServiceManagementUrl                     | https://management.core.cloudapi.de/ |
| PublishSettingsFileUrl                   | https://manage.microsoftazure.de/publishsettings/index |
| ResourceManagerUrl                       | https://management.microsoftazure.de/ |
| SqlDatabaseDnsSuffix                     | .database.cloudapi.de |
| StorageEndpointSuffix                    | core.cloudapi.de |
| ActiveDirectoryAuthority                 | https://login.microsoftonline.de/ |
| GraphUrl                                 | https://graph.cloudapi.de/ |
| TrafficManagerDnsSuffix                  | azuretrafficmanager.de |
| AzureKeyVaultDnsSuffix                   | vault.microsoftazure.de |
| AzureKeyVaultServiceEndpointResourceId   | https://vault.microsoftazure.de |


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Azure Deutschland finden Sie in den folgenden Ressourcen:

* [Registrieren Sie sich für eine Testversion](https://azure.microsoft.com/free/germany/)
* [Microsoft Azure Deutschland](https://azure.microsoft.com/overview/clouds/germany/)
* Wenn Sie schon ein Azure Deutschland-Konto haben, können Sie diese [Anmeldeseite](https://portal.microsoftazure.de/) nutzen.
* [Welcome to Azure Germany](./germany-welcome.md) (Willkommen bei Azure Deutschland)
* [Azure Deutschland-Blog](http://blogs.msdn.microsoft.com/azuregermany/)
* [Microsoft Trust Center (Azure-Konformität)](https://www.microsoft.com/trustcenter/compliance/complianceofferings)

