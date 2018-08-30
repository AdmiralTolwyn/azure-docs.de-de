---
title: Azure Data Catalog-Voraussetzungen
description: Erfahren Sie, welche Voraussetzungen Sie für Ihre ersten Schritte mit Azure Data Catalog erfüllen müssen.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: ef497a54-dc4d-4820-b5bf-c361b64b964d
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 168083ed6226d8e1d55e116297dde5884875945b
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43041301"
---
# <a name="azure-data-catalog-prerequisites"></a>Azure Data Catalog-Voraussetzungen

Es gibt einige Dinge, die Sie erledigen müssen, bevor Sie Azure Data Catalog einrichten können. Keine Sorge, dieser Vorgang dauert nicht lange.

## <a name="azure-subscription"></a>Azure-Abonnement
Um Data Catalog einzurichten, müssen Sie Besitzer oder Mitbesitzer eines Azure-Abonnements sein.

Mit Azure-Abonnements können Sie den Zugriff auf die Clouddienstressourcen wie Data Catalog organisieren. Mit Abonnements können Sie darüber hinaus steuern, wie die Ressourcennutzung gemeldet, abgerechnet und bezahlt wird. Jedes Abonnement kann in Bezug auf Abrechnung und Zahlung unterschiedlich eingerichtet sein. Daher können Sie je nach Abteilung, Projekt, Niederlassung usw. mit verschiedenen Abonnements und Plänen arbeiten. Jeder Clouddienst gehört zu einem Abonnement, und Sie benötigen ein Abonnement, ehe Sie Data Catalog einrichten können. Weitere Informationen finden Sie unter [Verwalten von Konten, Abonnements und Administratorrollen](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="azure-active-directory"></a>Azure Active Directory
Zum Einrichten von Data Catalog müssen Sie sich mit einem Azure Active Directory-Benutzerkonto (Azure AD) anmelden.

Azure AD bietet Ihrem Unternehmen sowohl in der Cloud als auch lokal eine einfache Möglichkeit zur Identitäts- und Zugriffsverwaltung. Benutzer können ein einzelnes Geschäfts-, Schul- oder Unikonto für einmaliges Anmelden bei Webanwendungen in der Cloud oder lokalen Webanwendung verwenden. Data Catalog verwendet Azure AD für die Authentifizierung der Anmeldung. Weitere Informationen finden Sie unter [Was ist Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md).

> [!NOTE]
> Über das [Azure-Portal](http://portal.azure.com/) können Sie sich entweder mit einem persönlichen Microsoft-Konto oder einem Geschäfts-, Schul- oder Unikonto für Azure Active Directory anmelden. Zum Einrichten von Data Catalog über das Azure-Portal oder [Data Catalog-Portal](http://www.azuredatacatalog.com) müssen Sie sich mit einem Azure Active Directory-Konto und nicht mit einem persönlichen Konto anmelden.
>
>

## <a name="active-directory-policy-configuration"></a>Active Directory-Richtlinienkonfiguration
Es kann vorkommen, dass Sie sich beim Data Catalog-Portal anmelden können, jedoch eine Fehlermeldung erhalten, wenn Sie versuchen, sich beim Tool zum Registrieren von Datenquellen anzumelden. Dieses Problem tritt möglicherweise nur dann auf, wenn Sie im Unternehmensnetzwerk arbeiten oder Sie von außerhalb des Unternehmensnetzwerks eine Verbindung herstellen.

Das Tool zum Registrieren von Datenquellen verwendet die Formularauthentifizierung, um Benutzeranmeldeinformationen für Active Directory zu überprüfen. Für eine erfolgreiche Anmeldung muss der Azure Active Directory-Administrator in der globalen Authentifizierungsrichtlinie die Formularauthentifizierung aktivieren.

Wie im folgenden Screenshot gezeigt, kann die Authentifizierung in der globalen Authentifizierungsrichtlinie für Intranet- und Extranetverbindungen separat aktiviert werden. Anmeldefehler können auftreten, wenn die Formularauthentifizierung für das Netzwerk, aus dem Sie eine Verbindung herstellen, nicht aktiviert ist.

 ![Globale Authentifizierungsrichtlinie für Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter [Konfigurieren von Authentifizierungsrichtlinien](https://technet.microsoft.com/library/dn486781.aspx).
