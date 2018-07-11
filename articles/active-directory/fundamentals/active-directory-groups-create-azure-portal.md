---
title: Erstellen einer Gruppe für Benutzer in Azure AD | Microsoft-Dokumentation
description: Erstellen einer Gruppe in Azure Active Directory und Hinzufügen von Mitgliedern zur Gruppe
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 08/04/2017
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 82d475e5adadb4e7670f24a6193348c9e1b37a16
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37767422"
---
# <a name="create-a-group-and-add-members-in-azure-active-directory"></a>Erstellen einer Gruppe und Hinzufügen von Mitgliedern in Azure Active Directory
> [!div class="op_single_selector"]
> * [Azure-Portal](active-directory-groups-create-azure-portal.md)
> * [PowerShell](../users-groups-roles/groups-settings-v2-cmdlets.md)

In diesem Artikel wird erläutert, wie Sie eine neue Gruppe in Azure Active Directory erstellen und auffüllen. Sie verwenden eine Gruppe zum Durchführen von Verwaltungsaufgaben, z.B. für das Zuweisen von Lizenzen oder Berechtigungen für mehrere Benutzer oder Geräte in einem Arbeitsschritt.

## <a name="how-do-i-create-a-group"></a>Wie erstelle ich eine Gruppe?
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) über ein Konto an, das als globaler Administrator für das Verzeichnis konfiguriert ist.
2. Wählen Sie **Alle Dienste** aus, geben Sie **Benutzer und Gruppen** in das Textfeld ein, und drücken Sie die **EINGABETASTE**.

   ![Öffnen der Benutzerverwaltung](./media/active-directory-groups-create-azure-portal/search-user-management.png)
3. Wählen Sie auf dem Blatt **Benutzer und Gruppen** die Option **Alle Gruppen** aus.

   ![Öffnen des Blatts „Gruppen“](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)
4. Wählen Sie auf dem Blatt **Benutzer und Gruppen** den Befehl **Hinzufügen** aus.

   ![Auswählen des Befehls „Hinzufügen“](./media/active-directory-groups-create-azure-portal/add-group-command.png)
5. Fügen Sie auf dem Blatt **Gruppe** einen Namen und eine Beschreibung für die Gruppe hinzu.
6. Um der Gruppe Mitglieder hinzuzufügen, wählen Sie im Textfeld **Mitgliedschaftstyp** die Einstellung **Zugewiesen** aus und klicken dann auf **Mitglieder**. Weitere Informationen zum dynamischen Verwalten der Mitgliedschaft finden Sie unter [Verwenden von Attributen zum Erstellen erweiterter Regeln für Gruppenmitgliedschaften](../active-directory-groups-dynamic-membership-azure-portal.md).

   ![Auswählen der hinzuzufügenden Mitglieder](./media/active-directory-groups-create-azure-portal/select-members.png)
7. Wählen Sie auf dem Blatt **Mitglieder** mindestens einen Benutzer oder ein Gerät aus, den/das Sie der Gruppe hinzufügen möchten, und klicken Sie auf die Schaltfläche **Auswählen** im unteren Bereich des Blatts, um das Element zur Gruppe hinzuzufügen. Das Feld **Benutzer** filtert die Anzeige basierend auf einem Abgleich Ihrer Eingabe mit einem beliebigen Teil eines Benutzer- oder Gerätenamens. In diesem Feld können keine Platzhalterzeichen verwendet werden.
8. Wenn Sie alle gewünschten Mitglieder zur Gruppe hinzugefügt haben, wählen Sie auf dem Blatt **Gruppe** die Option **Erstellen** aus.    

   ![Bestätigen der Gruppenerstellung](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)


## <a name="next-steps"></a>Nächste Schritte
Diese Artikel enthalten zusätzliche Informationen zu Azure Active Directory.

* [Anzeigen vorhandener Gruppen](active-directory-groups-view-azure-portal.md)
* [Verwalten der Einstellungen einer Gruppe](active-directory-groups-settings-azure-portal.md)
* [Verwalten der Mitglieder einer Gruppe](active-directory-groups-members-azure-portal.md)
* [Verwalten der Mitgliedschaften einer Gruppe](active-directory-groups-membership-azure-portal.md)
* [Verwalten dynamischer Regeln für Benutzer in einer Gruppe](../active-directory-groups-dynamic-membership-azure-portal.md)
