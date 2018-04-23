---
title: Verwalten des Zugriffs auf die Azure-Verwaltung mit dem bedingten Zugriff in Azure Active Directory
description: Sie erhalten Informationen zur Verwendung des bedingten Zugriffs in Azure AD, um den Zugriff auf die Azure-Verwaltung zu steuern.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: bryanla
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/22/2017
ms.author: skwan
ms.openlocfilehash: 1716ab45ab643e7220c2d1450a7aa6636647e62d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2018
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff

Der bedingte Zugriff in Azure Active Directory (Azure AD) steuert basierend auf den von Ihnen angegebenen Bedingungen den Zugriff auf Cloud-Apps. Um den Zugriff zuzulassen, erstellen Sie basierend darauf, ob die Anforderungen in der Richtlinie erfüllt werden oder nicht, bedingte Zugriffsrichtlinien, die den Zugriff gestatten oder blockieren. 

Normalerweise verwenden Sie den bedingten Zugriff zum Steuern des Zugriffs auf Ihre Cloud-Apps. Sie können auch Richtlinien einrichten, um den Zugriff auf die Azure-Verwaltung basierend auf bestimmten Bedingungen (wie das Anmelderisiko, der Standort oder das Gerät) zu steuern und um Anforderungen wie die mehrstufige Authentifizierung zu erzwingen.

Klicken Sie für die Erstellung einer Richtlinie für die Azure-Verwaltung unter **Cloud-Apps** auf **Microsoft Azure-Verwaltung**, wenn Sie die App auswählen, für welche die Richtlinie gelten soll.

![Bedingter Zugriff für die Azure-Verwaltung](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Die von Ihnen erstellte Richtlinie gilt für alle Azure-Verwaltungsendpunkte, einschließlich des klassischen Azure-Portals, des Azure-Portals, des Azure Resource Manager-Anbieters, der klassischen Dienstverwaltungs-APIs und von Azure PowerShell.

> [!CAUTION]
> Stellen Sie sicher, dass Sie die Funktionsweise des bedingten Zugriffs verstehen, bevor Sie eine Richtlinie einrichten, um den Zugriff auf die Azure-Verwaltung zu steuern. Versichern Sie sich, dass Sie keine Bedingungen erstellen, die Ihren eigenen Zugriff auf das Portal blockieren könnten.

Weiter Informationen zur Einrichtung und Verwendung des bedingten Zugriffs finden Sie unter [Bedingter Zugriff in Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).