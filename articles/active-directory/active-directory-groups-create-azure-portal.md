---
title: "Erstellen einer Gruppe für Benutzer in Azure Active Directory | Microsoft-Dokumentation"
description: "Erstellen einer Gruppe in Azure Active Directory und Hinzufügen von Mitgliedern zur Gruppe"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: cc5f232a-1e77-45c2-b28b-1fcb4621725c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1be9947c43c70b7248201b9f470fb3cf5a11519e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2017
---
# <a name="create-a-group-and-add-members-in-azure-active-directory"></a>Erstellen einer Gruppe und Hinzufügen von Mitgliedern in Azure Active Directory
> [!div class="op_single_selector"]
> * [Azure-Portal](active-directory-groups-create-azure-portal.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

In diesem Artikel wird erläutert, wie Sie eine neue Gruppe in Azure Active Directory erstellen und auffüllen. Sie verwenden eine Gruppe zum Durchführen von Verwaltungsaufgaben, z.B. für das Zuweisen von Lizenzen oder Berechtigungen für mehrere Benutzer oder Geräte in einem Arbeitsschritt.

## <a name="how-do-i-create-a-group"></a>Wie erstelle ich eine Gruppe?
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) über ein Konto an, das als globaler Administrator für das Verzeichnis konfiguriert ist.
2. Wählen Sie **Weitere Dienste** aus, geben Sie **Benutzer und Gruppen** in das Textfeld ein, und drücken Sie die **EINGABETASTE** .

   ![Öffnen der Benutzerverwaltung](./media/active-directory-groups-create-azure-portal/search-user-management.png)
3. Wählen Sie auf dem Blatt **Benutzer und Gruppen** die Option **Alle Gruppen** aus.

   ![Öffnen des Blatts „Gruppen“](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)
4. Wählen Sie auf dem Blatt **Benutzer und Gruppen** den Befehl **Hinzufügen** aus.

   ![Auswählen des Befehls „Hinzufügen“](./media/active-directory-groups-create-azure-portal/add-group-command.png)
5. Fügen Sie auf dem Blatt **Gruppe** einen Namen und eine Beschreibung für die Gruppe hinzu.
6. Um der Gruppe Mitglieder hinzuzufügen, wählen Sie im Textfeld **Mitgliedschaftstyp** die Einstellung **Zugewiesen** aus und klicken dann auf **Mitglieder**. Weitere Informationen zum dynamischen Verwalten der Mitgliedschaft finden Sie unter [Verwenden von Attributen zum Erstellen erweiterter Regeln für Gruppenmitgliedschaften](active-directory-groups-dynamic-membership-azure-portal.md).

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
* [Verwalten dynamischer Regeln für Benutzer in einer Gruppe](active-directory-groups-dynamic-membership-azure-portal.md)
