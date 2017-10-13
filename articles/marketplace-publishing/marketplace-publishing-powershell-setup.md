---
title: "Einrichten von PowerShell zum Erstellen eines virtuellen Computers für den Marketplace | Microsoft Docs"
description: "Anweisungen zum Einrichten von Azure PowerShell und Verwenden als optionalen Prozessablauf zum Erstellen von VM-Images für die Bereitstellung und den Verkauf in Azure Marketplace"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: e19d6cda-76df-4e42-be84-c9fe47a636db
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2016
ms.author: hascipio
ms.openlocfilehash: bbcce5093d2bbd5326523063db7d0e565fe4de6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Einrichten von Azure PowerShell zum Erstellen eines Angebots für Azure Marketplace
Ausführliche Informationen zum Einrichten von PowerShell in Azure finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview). Ein einfacher Ansatz ist die Zertifikatmethode, bei der ein für die Authentifizierung benötigtes Zertifikat heruntergeladen und importiert wird. Um das erforderliche Zertifikat zu erhalten, verwenden Sie das Cmdlet **Get-AzurePublishSettingsFile** . Speichern Sie die Datei, wenn Sie dazu aufgefordert werden. Verwenden Sie das Cmdlet **Import-AzurePublishSettingsFile** , um das Zertifikat in eine PowerShell-Sitzung zu importieren.

Verwenden Sie zum Konfigurieren und Speichern der allgemeinen Microsoft Azure-Abonnementeinstellungen für die PowerShell-Sitzung die Cmdlets **Set-AzureSubscription** und **Select-AzureSubscription**:

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

Der erste Befehl ordnet dem Abonnement ein Standardspeicherkonto zu (das für einige VM-Bereitstellungsvorgänge erforderlich ist).  Die zweite richtet das Abonnement als das aktuelle ein (was von anderen Cmdlets erkannt wird).

## <a name="see-also"></a>Weitere Informationen
* [Erste Schritte: Veröffentlichen eines Angebots in Azure Marketplace](marketplace-publishing-getting-started.md)
* [Erstellen eines VM-Images für den Marketplace](marketplace-publishing-vm-image-creation.md)

