---
title: Überprüfen des Zugriffs mithilfe der Azure AD-Zugriffsüberprüfung | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Zugriff mit der Azure Active Directory-Zugriffsüberprüfung überprüfen können.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 07/16/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 2e0a522f6125a9e34a37c50f547e725072ec65b4
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205002"
---
# <a name="review-access-with-azure-ad-access-reviews"></a>Überprüfen des Zugriffs mit Azure AD-Zugriffsüberprüfungen

Azure Active Directory (Azure AD) erleichtert Unternehmen das Verwalten des Zugriffs auf Anwendungen und Mitglieder von Gruppen in Azure AD und anderen Microsoft Online Services durch eine Funktion namens „Zugriffsüberprüfungen“. Sie haben möglicherweise eine E-Mail von Microsoft erhalten, in der Sie aufgefordert werden, den Zugriff von Mitgliedern einer Gruppe oder Benutzern mit Zugriff auf eine Anwendung zu überprüfen. 

## <a name="open-an-access-review"></a>Öffnen einer Zugriffsüberprüfung

Um die ausstehenden Zugriffsüberprüfungen anzuzeigen, klicken Sie auf den Zugriffsüberprüfungslink in der E-Mail. Ab August 2018 weisen die E-Mail-Benachrichtigungen für Azure AD-Rollen einen aktualisierten Entwurf auf. Das folgende Beispiel zeigt eine E-Mail, die gesendet wird, wenn ein Benutzer für die Rolle als Prüfer eingeladen wurde. 

![E-Mail zur Zugriffsüberprüfung](./media/active-directory-azure-ad-controls-perform-access-review/new-ar-email.png)

Wenn Sie nicht über die E-Mail verfügen, können Sie die Zugriffsüberprüfungen mit folgenden Schritten anzeigen:

1. Melden Sie sich beim [Azure AD-Zugriffsbereich](https://myapps.microsoft.com) an.

2. Wählen Sie das Benutzersymbol in der oberen rechten Ecke der Seite, das Ihren Namen und Ihre Standardorganisation anzeigt. Werden mehrere Organisationen aufgelistet, wählen Sie die Organisation aus, die eine Zugriffsüberprüfung angefordert hat.

3. Wenn rechts auf der Seite eine Kachel mit der Bezeichnung **Zugriffsüberprüfungen** vorhanden ist, wählen Sie sie. Wenn die Kachel nicht angezeigt wird, sind für diese Organisation keine Zugriffsüberprüfungen auszuführen. Daher ist zurzeit keine Aktion erforderlich.

## <a name="fill-out-an-access-review"></a>Ausfüllen einer Zugriffsüberprüfung

Wenn Sie eine Zugriffsüberprüfung aus der Liste auswählen, sehen Sie die Namen der Benutzer, die überprüft werden müssen. Sie sehen möglicherweise nur einen Namen – Ihren eigenen – wenn die Anforderung die Überprüfung Ihres eigenen Zugriffs war.

Sie können für jede Zeile auf der Liste entscheiden, ob Sie den Zugriff des Benutzers genehmigen oder verweigern. Wählen Sie die Zeile aus, und wählen Sie Genehmigung oder Verweigerung. (Wenn Sie den Benutzer nicht kennen, können Sie dies auch angeben.)

Der Prüfer kann möglicherweise verlangen, dass Sie eine Begründung für den weiteren Zugriffsbedarf oder die Gruppenmitgliedschaft angeben, bevor der Zugriff genehmigt wird.

## <a name="next-steps"></a>Nächste Schritte

Die Verweigerung des Zugriffs eines Benutzers wird nicht sofort aufgehoben. Sie kann aufgehoben werden, wenn die Überprüfung abgeschlossen ist oder wenn ein Administrator die Überprüfung beendet. Wenn Sie Ihre Antwort ändern und einen zuvor abgelehnten Benutzer genehmigen oder einen zuvor genehmigten Benutzer ablehnen möchten, wählen Sie die Zeile, setzen Sie die Antwort zurück und wählen Sie eine neue Antwort aus. Dies ist möglich, bis die Zugriffsüberprüfung beendet ist.



