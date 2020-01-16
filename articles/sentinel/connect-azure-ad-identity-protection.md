---
title: Verknüpfen von Azure AD Identity Protection-Daten mit Azure Sentinel
description: Erfahren Sie, wie Sie Azure AD Identity Protection-Daten mit Azure Sentinel verknüpfen.
author: rkarlin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 11/17/2019
ms.author: rkarlin
ms.openlocfilehash: 8d916606913b19a9eb15cb280defbeb0440565e4
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563716"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Verknüpfen von Daten aus Azure AD Identity Protection



Sie können Protokolle aus [Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) an Azure Sentinel streamen, sodass Warnungen an Azure Sentinel gestreamt werden, um Dashboards anzuzeigen, benutzerdefinierte Warnungen zu erstellen und Untersuchungen zu verbessern. Azure Active Directory Identity Protection bietet eine konsolidierte Ansicht der gefährdeten Benutzer, der Risikoerkennungen und der Sicherheitsrisiken sowie die Möglichkeit, Risiken umgehend zu beseitigen und Richtlinien zur automatischen Behandlung zukünftiger Ereignisse festzulegen. Der Dienst basiert auf der umfangreichen Erfahrung von Microsoft in Bezug auf den Schutz von Kundenidentitäten und erzielt bei über 13 Milliarden Anmeldeereignissen pro Tag eine extrem hohe Genauigkeit. 


## <a name="prerequisites"></a>Voraussetzungen

- Sie müssen eine [Azure Active Directory Premium P1- oder P2-Lizenz](https://azure.microsoft.com/pricing/details/active-directory/) haben.
- Ein Benutzer mit globalen Administrator- oder Sicherheitsadministratorberechtigungen.


## <a name="connect-to-azure-ad-identity-protection"></a>Herstellen einer Verbindung mit Azure AD Identity Protection

Wenn Sie Azure AD Identity Protection bereits haben, vergewissern Sie sich, dass es [in Ihrem Netzwerk aktiviert ist](../active-directory/identity-protection/overview-identity-protection.md).
Wenn Azure AD Identity Protection bereitgestellt ist und Daten erhält, können die Warnungsdaten problemlos an Azure Sentinel gestreamt werden.


1. Klicken Sie in Azure Sentinel auf **Data connectors** (Datenconnectors) und anschließend auf die Kachel **Azure AD Identity Protection**.

2. Klicken Sie auf **Verbinden**, um das Streamen von Azure AD Identity Protection-Ereignissen an Azure Sentinel zu starten.


6. Suchen Sie nach **SecurityAlert**, um in der Protokollanalyse das relevante Schema für die Azure AD Identity Protection-Warnungen zu verwenden.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument erfahren Sie, wie Sie Azure AD Identity Protection mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).
