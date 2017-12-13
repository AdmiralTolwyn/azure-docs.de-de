---
title: Bereitstellen von Azure API Management-Diensten in mehreren Azure-Regionen | Microsoft-Dokumentation
description: "Erfahren Sie, wie Sie eine Azure API Management-Dienstinstanz für mehrere Azure-Regionen bereitstellen."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: e126e34bc9fce21243b0ef79f5ab661aec3a2de6
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2017
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Bereitstellen einer Azure API Management-Dienstinstanz für mehrere Azure-Regionen
API Management unterstützt eine Bereitstellung für mehrere Regionen, wodurch API-Herausgeber einen einzelnen API-Verwaltungsdienst in einer beliebigen Anzahl von gewünschten Azure-Regionen zur Verfügung stellen können. Dies trägt dazu bei, die Anforderungslatenz bei geografisch verteilten API-Nutzern zu verringern, und verbessert gleichzeitig die Dienstverfügbarkeit, wenn eine Region offline geht. 

Bei der anfänglichen Erstellung eines API Management-Diensts enthält dieser nur eine [Einheit][unit] und befindet sich in einer einzigen Azure-Region. Diese wird als primäre Region bezeichnet. Mit dem Azure-Portal können auf einfache Weise zusätzliche Regionen hinzugefügt werden. Ein API Management-Gatewayserver wird in jeder Region bereitgestellt und Datenverkehr durch Aufrufe wird an das nächstgelegene Gateway geroutet. Wenn eine Region offline geht, wird der Datenverkehr automatisch an das nächstgelegene Gateway umgeleitet. 

> [!IMPORTANT]
> Die Bereitstellung in mehreren Regionen steht nur im Tarif **[Premium][Premium]** zur Verfügung.
> 
> 

## <a name="add-region"></a>Bereitstellen einer API Management-Dienstinstanz für eine neue Region
> [!NOTE]
> Falls Sie noch keine API Management-Dienstinstanz erstellt haben, finden Sie weitere Informationen unter [Erstellen einer API Management-Dienstinstanz][Create an API Management service instance].
> 
> 

Navigieren Sie im Azure-Portal zur Seite **Scale and pricing (Skalieren und Preise)** für Ihre API Management-Dienstinstanz. 

![Registerkarte "Skalieren"][api-management-scale-service]

Klicken Sie auf der Symbolleiste auf **+ Region hinzufügen**, um die Bereitstellung in einer neuen Region auszuführen.

![Region hinzufügen][api-management-add-region]

Wählen Sie den Standort in der Dropdownliste aus, und legen Sie die Anzahl der Einheiten mit dem Schieberegler fest.

![Einheiten angeben][api-management-select-location-units]

Klicken Sie auf **Hinzufügen** um Ihre Auswahl in der Tabelle „Standorte“ zu platzieren. 

Wiederholen Sie diesen Vorgang, bis Sie alle Standorte konfiguriert haben, und klicken Sie auf der Symbolleiste auf **Speichern**, um den Bereitstellungsprozess zu starten.

## <a name="remove-region"></a>Löschen einer API Management-Dienstinstanz aus einem Standort
Navigieren Sie im Azure-Portal zur Seite **Scale and pricing (Skalieren und Preise)** für Ihre API Management-Dienstinstanz. 

![Registerkarte "Skalieren"][api-management-scale-service]

Öffnen Sie für den Standort, den Sie entfernen möchten, das Kontextmenü mit der Schaltfläche **...** ganz rechts in der Tabelle. Wählen Sie die Option **Löschen** aus.

![Region entfernen][api-management-remove-region]

Bestätigen Sie den Löschvorgang, und klicken Sie auf **Speichern** um die Änderungen zu übernehmen.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Get started with Azure API Management]: get-started-create-service-instance.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

