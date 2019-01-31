---
title: Wann und wie verwenden Sie einen Azure Multi-Factor Authentication-Anbieter?
description: Wann sollten Sie einen Auth-Anbieter mit Azure MFA verwenden?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.openlocfilehash: 2d7156a1387184cff2eb4252648f0e844e8b1f50
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/26/2019
ms.locfileid: "55079706"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Erste Schritte mit einem Azure Multi-Factor Authentication-Anbieter

Die zweistufige Überprüfung ist standardmäßig für globale Administratoren, die mit Azure Active Directory arbeiten, und für Office 365-Benutzer verfügbar. Falls Sie die [erweiterten Funktionen](howto-mfa-mfasettings.md) nutzen möchten, müssen Sie die Vollversion von Azure Multi-Factor Authentication (MFA) erwerben.

Ein Azure Multi-Factor Authentication-Anbieter wird verwendet, um Features zu nutzen, die von Azure MFA für Benutzer bereitgestellt werden, die **keine Lizenzen haben**.

Wenn Sie über Lizenzen verfügen, die alle Benutzer in Ihrem Unternehmen abdecken, benötigen Sie keinen Azure Multi-Factor Authentication-Anbieter. Erstellen Sie einen Azure Multi-Factor Authentication-Anbieter nur, wenn Sie für einige Benutzer, die über keine Lizenzen verfügen, die zweistufige Überprüfung bereitstellen müssen.

> [!NOTE]
> Ab dem 1. September 2018 können keine neuen Authentifizierungsanbieter mehr erstellt werden. Vorhandene Authentifizierungsanbieter können ggf. weiterhin verwendet und aktualisiert werden. Die mehrstufige Authentifizierung ist in Azure AD Premium-Lizenzen weiterhin als Feature verfügbar.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Einschränkungen im Zusammenhang mit dem Azure MFA SDK

Beachten Sie, dass das SDK veraltet ist und nur noch bis zum 14. November 2018 funktioniert. Nach diesem Zeitpunkt führen Aufrufe an das SDK zu Fehlern.

## <a name="what-is-an-mfa-provider"></a>Was ist ein MFA-Anbieter?

Es gibt zwei Arten von Authentifizierungsanbietern. Der Unterschied besteht darin, wie Ihr Azure-Abonnement in Rechnung gestellt wird. Bei der Option pro Authentifizierung wird die Anzahl der Authentifizierungen berechnet, die für Ihren Mandanten in einem Monat ausgeführt werden. Diese Option ist am besten geeignet, wenn sich mehrere Benutzer nur gelegentlich authentifizieren. Bei der Option pro Benutzer wird die Anzahl der Personen in Ihrem Mandanten berechnet, die in einem Monat die zweistufige Überprüfung ausführen. Diese Option empfiehlt sich, wenn Sie über einige Benutzer mit Lizenzen verfügen, MFA aber auf weitere Benutzer außerhalb Ihrer Lizenzierungsgrenzwerte erweitern müssen.

## <a name="manage-your-mfa-provider"></a>Verwalten Ihres MFA-Anbieters

Das Nutzungsmodell (pro aktiviertem Benutzer oder pro Authentifizierung) kann nach der Erstellung eines MFA-Anbieters nicht mehr geändert werden.

Sofern die Anzahl erworbenen Lizenzen für alle Benutzer ausreicht, die für MFA aktiviert sind, können Sie den MFA-Anbieter auch ganz löschen.

Falls Ihr MFA-Anbieter nicht mit einem Azure AD-Mandanten verknüpft ist oder Sie den neuen MFA-Anbieter mit einem anderen Azure AD-Mandanten verknüpfen, werden Benutzereinstellungen und Konfigurationsoptionen nicht übernommen. Darüber hinaus müssen vorhandene Azure MFA-Server unter Verwendung von Aktivierungsanmeldeinformationen des MFA-Anbieters erneut aktiviert werden. Wenn Sie die MFA-Server erneut aktivieren, um sie mit dem MFA-Anbieter zu verknüpfen, hat das keinerlei Auswirkungen auf die Authentifizierung per Telefonanruf oder SMS, aber Benachrichtigungen der mobilen App funktionieren erst wieder, wenn die Benutzer die mobile App erneut aktivieren.

## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren von Azure Multi-Factor Authentication-Einstellungen](howto-mfa-mfasettings.md)
