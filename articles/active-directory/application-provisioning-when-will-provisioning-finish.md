---
title: Die Benutzerbereitstellung für eine Azure AD-Kataloganwendung dauert Stunden oder länger | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie herausfinden, warum die Bereitstellung für die Anwendung möglicherweise länger als erwartet dauert.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: dc582ff3dac8f128972f070309d5c8a4ce21fb70
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335382"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Die Benutzerbereitstellung für eine Azure AD-Kataloganwendung dauert Stunden oder länger

Bei der ersten Aktivierung der automatischen Bereitstellung für eine Anwendung kann die anfängliche Synchronisierung zwischen 20 Minuten und mehreren Stunden dauern. Dies hängt von der Größe des Azure AD-Verzeichnisses und der Anzahl der Benutzer im Bereitstellungsbereich ab. 

Nachfolgende Synchronisierungen nach der anfänglichen Synchronisierung werden schneller durchgeführt, da der Bereitstellungsdienst Wasserzeichen speichert, die den Status beider Systeme nach der Erstsynchronisierung darstellen, sodass die Leistung nachfolgender Synchronisierungen verbessert wird.

## <a name="how-to-improve-provisioning-performance"></a>Verbessern der Bereitstellungsleistung

Wenn die erste Synchronisierung länger als einige Stunden dauert, haben Sie eine Möglichkeit, die Leistung zu verbessern:

-   **Benutzerbereichsfilter** Mit Bereichsfiltern können Sie die Daten, die der Bereitstellungsdienst aus Azure AD extrahiert, fein abstimmen, indem Sie Benutzer anhand bestimmter Attributwerte herausfiltern. Weitere Informationen zu Bereichsfiltern finden Sie unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Nächste Schritte
[Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](active-directory-saas-app-provisioning.md)

