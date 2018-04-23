---
title: Hinzufügen von Besitzern und Benutzern in Azure DevTest Labs | Microsoft Docs
description: Hinzufügen von Besitzern und Benutzern in Azure DevTest Labs über das Azure-Portal oder PowerShell
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: ''
ms.assetid: 4f51d9a5-2702-45f0-a2d5-a3635b58c416
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: v-craic
ms.openlocfilehash: f7f7562f0af4753bc08018227a967f9ca3736021
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/19/2018
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Hinzufügen von Besitzern und Benutzern in Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Der Zugriff in Azure DevTest Labs wird durch die [rollenbasierte Zugriffssteuerung in Azure (Role-Based Access Control, RBAC)](../role-based-access-control/overview.md)gesteuert. Mithilfe von RBAC können Sie Aufgaben in Ihrem Team in *Rollen* verteilen und Benutzern nur den Zugriff gewähren, den sie zur Ausführung ihrer Aufgaben benötigen. Drei dieser RBAC-Rollen sind *Besitzer*, *DevTest Labs-Benutzer* und *Mitwirkender*. In diesem Artikel erfahren Sie, welche Aktionen in jeder der drei wichtigsten RBAC-Rollen ausgeführt werden können. Sie erfahren, wie Sie Benutzer über das Portal oder über ein PowerShell-Skript zu einem Lab hinzufügen und Benutzer auf Abonnementebene hinzufügen.

## <a name="actions-that-can-be-performed-in-each-role"></a>Aktionen, die in jeder Rolle ausgeführt werden können
Es gibt drei wichtige Rollen, die Sie einem Benutzer zuweisen können:

* Owner (Besitzer)
* DevTest Labs-Benutzer
* Mitwirkender

Die folgende Tabelle zeigt die Aktionen, die von Benutzern in jeder dieser Rollen ausgeführt werden können:

| **Aktionen, die Benutzer in dieser Rolle ausführen können** | **DevTest Labs-Benutzer** | **Besitzer** | **Mitwirkender** |
| --- | --- | --- | --- |
| **Lab-Aufgaben** | | | |
| Benutzer zu einem Lab hinzufügen |Nein  |Ja |Nein  |
| Kosteneinstellungen aktualisieren |Nein  |Ja |Ja |
| **Grundlegende Aufgaben für virtuelle Computer** | | | |
| Benutzerdefinierte Images hinzufügen und entfernen |Nein  |Ja |Ja |
| Formeln hinzufügen, aktualisieren und löschen |Ja |Ja |Ja |
| Azure Marketplace-Images in eine Positivliste aufnehmen |Nein  |Ja |Ja |
| **Aufgaben für virtuelle Computer** | | | |
| Virtuelle Computer erstellen |Ja |Ja |Ja |
| Virtuelle Computer starten, beenden und löschen |Nur vom Benutzer erstellte virtuelle Computer |Ja |Ja |
| VM-Richtlinien aktualisieren |Nein  |Ja |Ja |
| Datenträgern zu virtuellen Computern hinzufügen bzw. davon entfernen |Nur vom Benutzer erstellte virtuelle Computer |Ja |Ja |
| **Artefakt-Aufgaben** | | | |
| Artefaktrepositorys hinzufügen und entfernen |Nein  |Ja |Ja |
| Artefakte anwenden |Ja |Ja |Ja |

> [!NOTE]
> Wenn ein Benutzer einen virtuellen Computer erstellt, wird diesem Benutzer automatisch die **Besitzer** -Rolle des virtuellen Computers zugewiesen.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Hinzufügen eines Besitzers oder Benutzers auf der Lab-Ebene
Besitzer und Benutzer können über das Azure-Portal auf der Lab-Ebene hinzugefügt werden. Das schließt externe Benutzer mit einem [Microsoft-Konto (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account)ein.
Die folgenden Schritte führen Sie durch den Prozess des Hinzufügens eines Besitzers oder Benutzers zu einem Lab in Azure DevTest Labs:

1. Melden Sie sich auf dem [Azure-Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040)an.
2. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.
3. Wählen Sie in der Liste der Labs das gewünschte Lab aus.
4. Wählen Sie auf dem Blatt des Labs **Konfiguration**aus. 
5. Wählen Sie auf dem Blatt **Konfiguration** die Option **Benutzer** aus.
6. Wählen Sie auf dem Blatt **Benutzer** **+Hinzufügen**.
   
    ![Benutzer hinzufügen](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
7. Wählen Sie auf dem Blatt **Rolle auswählen** die gewünschte Rolle aus. Im Abschnitt [Aktionen, die in jeder Rolle ausgeführt werden können](#actions-that-can-be-performed-in-each-role) finden Sie die verschiedenen Aktionen, die Benutzer in den Rollen „Besitzer“, „DevTest-Benutzer“ und „Beitragender“ ausführen können.
8. Geben Sie auf dem Blatt **Hinzufügen von Benutzern** die E-Mail-Adresse oder den Namen des Benutzers ein, den Sie der Rolle hinzufügen möchten. Wenn der Benutzer nicht gefunden werden kann, erhalten Sie eine Fehlermeldung, die das Problem erklärt. Wird der Benutzer gefunden, so wird dieser Benutzer aufgeführt und ausgewählt. 
9. Wählen Sie **Auswählen**.
10. Wählen Sie **OK**, um das Blatt **Zugriff hinzufügen** zu schließen.
11. Wenn Sie zum Blatt **Benutzer** zurückkehren, wurde der Benutzer hinzugefügt.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Hinzufügen eines externen Benutzers zu einem Lab mit PowerShell
Sie können nicht nur Benutzer im Azure-Portal hinzufügen, sondern auch externe Benutzer über ein PowerShell-Skript zu Ihrem Lab hinzufügen. Ändern Sie im folgenden Beispiel einfach die Werte der Parameter unter dem Kommentar **Values to change** .
Sie können die Werte `subscriptionId`, `labResourceGroup` und `labName` aus dem Labblatt im Azure-Portal abrufen.

> [!NOTE]
> Im Beispielskript wird davon ausgegangen, dass der angegebene Benutzer dem Active Directory als Gast hinzugefügt wurde. Wenn dies nicht der Fall ist, tritt ein Fehler auf. Um einem Lab einen Benutzer hinzuzufügen, der nicht in Active Directory ist, weisen Sie dem Benutzer im Azure-Portal wie im Abschnitt [Hinzufügen eines Besitzers oder Benutzers auf der Lab-Ebene](#add-an-owner-or-user-at-the-lab-level) beschrieben eine Rolle zu.   
> 
> 

    # Add an external user in DevTest Labs user role to a lab
    # Ensure that guest users can be added to the Azure Active directory:
    # https://azure.microsoft.com/en-us/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource name here>"
    $labName = "<Enter lab name here>"
    $userDisplayName = "<Enter user's display name here>"

    # Log into your Azure account
    Connect-AzureRmAccount

    # Select the Azure subscription that contains the lab. 
    # This step is optional if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Retrieve the user object
    $adObject = Get-AzureRmADUser -SearchString $userDisplayName

    # Create the role assignment. 
    $labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    New-AzureRmRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Hinzufügen eines Besitzers oder Benutzers auf der Abonnementebene
Azure-Berechtigungen werden vom übergeordneten Bereich an den untergeordneten Bereich in Azure weitergegeben. Daher sind Besitzer eines Azure-Abonnements, das Labs enthält, automatisch Besitzer dieser Labs. Sie besitzen auch die virtuellen Computer und andere Ressourcen, die von den Lab-Benutzern und dem Azure DevTest Labs-Dienst erstellt werden. 

Sie können einem Lab über das entsprechende Blatt im [Azure-Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040)zusätzliche Besitzer hinzufügen. Der Verwaltungsbereich des hinzugefügten Besitzers ist jedoch weniger umfangreich als der Bereich des Abonnementbesitzers. Beispielsweise erhalten die hinzugefügten Besitzer keinen vollständigen Zugriff auf einige der Ressourcen, die vom DevTest Labs-Dienst im Abonnement erstellt werden. 

Um einen Besitzer zu einem Azure-Abonnement hinzuzufügen, gehen Sie folgendermaßen vor:

1. Melden Sie sich auf dem [Azure-Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040)an.
2. Wählen Sie **Alle Dienste** und dann **Abonnements** aus der Liste aus.
3. Wählen Sie das gewünschte Abonnement aus.
4. Wählen Sie das Symbol **Zugriff** aus. 
   
    ![Auf Benutzer zugreifen](./media/devtest-lab-add-devtest-user/access-users.png)
5. Wählen Sie auf dem Blatt **Benutzer** **Hinzufügen**.
   
    ![Benutzer hinzufügen](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. Wählen Sie auf dem Blatt **Rolle auswählen** **Besitzer**.
7. Geben Sie auf dem Blatt **Hinzufügen von Benutzern** die E-Mail-Adresse oder den Namen des Benutzers ein, den Sie als Besitzer hinzufügen möchten. Wenn der Benutzer nicht gefunden werden kann, erhalten Sie eine Fehlermeldung, die das Problem erklärt. Wenn der Benutzer gefunden wird, wird dieser Benutzer unter dem Textfeld **Benutzer** aufgeführt.
8. Wählen Sie den gefundenen Benutzernamen aus.
9. Wählen Sie **Auswählen**.
10. Wählen Sie **OK**, um das Blatt **Zugriff hinzufügen** zu schließen.
11. Wenn Sie zum Blatt **Benutzer** zurückkehren, wurde der Benutzer als Besitzer hinzugefügt. Dieser Benutzer ist nun Besitzer aller Labs, die in diesem Abonnement erstellt werden, und sie kann daher die Aufgaben eines Besitzers ausführen. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

