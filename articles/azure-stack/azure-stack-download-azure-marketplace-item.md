---
title: Herunterladen von Marketplace-Elementen von Azure | Microsoft-Dokumentation
description: Ich kann Marketplace-Elemente von Azure in meine Azure Stack-Bereitstellung herunterladen.
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
ms.topic: get-started-article
ms.date: 07/03/2017
ms.author: erikje
ms.openlocfilehash: 4d7c335a3c68cc9bb8cb0c823883716a3dd6620a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Herunterladen von Marketplace-Elementen von Azure in Azure Stack

*Gilt für: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Wenn Sie entscheiden, welche Inhalte in Ihrem Azure Stack-Marketplace enthalten sein sollen, berücksichtigen Sie auch die im Azure-Marketplace verfügbaren Inhalte. Zum Herunterladen steht Ihnen eine Aufstellung ausgewählter Marketplace-Elemente zur Verfügung, deren Ausführung unter Azure Stack vorab getestet wurde. Dieser Liste werden regelmäßig neue Elemente hinzugefügt. Prüfen Sie daher, ob neue Inhalte verfügbar sind.

Zum Herunterladen von Marketplace-Elementen müssen Sie zunächst [Azure Stack bei Azure registrieren](azure-stack-register.md). 

## <a name="download"></a>Download
1. Melden Sie sich beim Azure Stack-Administratorportal (https://portal.local.azurestack.external) an.
2. Einige Marketplace-Elemente sind unter Umständen sehr groß.  Stellen Sie sicher, dass auf Ihrem System ausreichend Speicherplatz vorhanden ist. Klicken Sie hierfür auf **Ressourcenanbieter** > **Speicher**.

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

3. Klicken Sie auf **Weitere Dienste** > **Marketplace Management** (Marketplace-Verwaltung).

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. Klicken Sie auf **Add from Azure** (Aus Azure hinzufügen), um eine Liste von Elementen anzuzeigen, die heruntergeladen werden können. Klicken Sie auf die einzelnen Elemente in der Liste, um deren Beschreibung und Downloadgröße anzuzeigen.

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. Wählen Sie das gewünschte Element in der Liste aus, und klicken Sie dann auf **Herunterladen**. Dadurch beginnt der Download des VM-Images für das ausgewählte Element. Die Downloadzeiten können variieren.

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. Wenn der Download abgeschlossen ist, können Sie Ihr neues Marketplace-Element entweder als Azure Stack-Betreiber oder -benutzer bereitstellen. Klicken Sie auf **+ Neu**, suchen Sie das neue Marketplace-Element in den Kategorien, und wählen Sie das Element aus.
7. Klicken Sie auf **Erstellen** , um die Erstellungsoberfläche für das neu heruntergeladenen Element zu öffnen. Führen Sie die schrittweisen Anweisungen aus, um Ihr Element bereitzustellen.

## <a name="next-steps"></a>Nächste Schritte

[Erstellen und Veröffentlichen eines Marketplace-Elements](azure-stack-create-and-publish-marketplace-item.md)
