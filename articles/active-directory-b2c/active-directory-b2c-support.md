---
title: 'Azure Active Directory B2C: Support | Microsoft Docs'
description: Senden von Supportanfragen für Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: 2cb881e50b9a08c99b59796a23107bfafb847b75
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Azure Active Directory B2C: Senden von Supportanfragen
Führen Sie die folgenden Schritte aus, um Supportanfragen für Azure Active Directory (Azure AD) B2C im Azure-Portal zu senden:

1. [Führen Sie diese Schritte aus, um im Azure-Portal zum Blatt „B2C-Funktionen“ zu navigieren](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Wechseln Sie aus Ihrem B2C-Mandanten in einen anderen Mandanten, dem ein Azure-Abonnement zugeordnet ist. Dies ist normalerweise Ihr Mitarbeitermandant oder der Standardmandant, der bei der Registrierung für ein Azure-Abonnement erstellt wurde. Weitere Informationen finden Sie unter [Beziehung zwischen einem Azure-Abonnement und Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md).
   
    ![Support: Mandantenwechsel](./media/active-directory-b2c-support/support-switch-dir.png)
3. Klicken Sie nach dem Wechsel von Mandanten auf **Hilfe und Support**.
   
    ![Support: Hilfe und Support](./media/active-directory-b2c-support/support-support.png)
4. Klicken Sie auf **Neue Supportanfrage**.
   
    ![Support: Neu](./media/active-directory-b2c-support/support-new.png)
5. Verwenden Sie diese Informationen auf dem Blatt **Grundlagen**, und klicken Sie auf **Weiter**.
   
   * Wählen Sie für **Problemtyp** den Eintrag **Technisch** aus.
   * Wählen Sie das entsprechende **Abonnement**.
   * **Dienst** ist **Active Directory**.
   * Wählen Sie den entsprechenden **Supportplan**. Wenn Sie keinen besitzen, können Sie sich [hier](https://azure.microsoft.com/en-us/support/plans/)für einen Supportplan registrieren.
     
     ![Support: Grundlagen](./media/active-directory-b2c-support/support-basics.png)
6. Verwenden Sie diese Informationen auf dem Blatt **Problem**, und klicken Sie auf **Weiter**.
   
   * Wählen Sie den entsprechenden **Schweregrad** .
   * Der **Problemtyp** lautet **B2C**.
   * Wählen Sie die entsprechende **Kategorie**.
   * Beschreiben Sie Ihr Problem im Feld **Details**. Geben Sie Details wie den Namen des B2C-Mandanten, eine Beschreibung des Problems, Fehlermeldungen, Korrelations-IDs (falls verfügbar) usw. an.
   * Geben Sie im Feld **Zeitrahmen** durch Datum und Uhrzeit (einschließlich Zeitzone) an, wann das Problem aufgetreten ist.
   * Laden Sie unter **Dateiupload**alle Screenshots und Dateien hoch, die Ihrer Meinung nach bei der Behebung des Problems hilfreich sein könnten.
     
     ![Support: Problem](./media/active-directory-b2c-support/support-problem.png)
7. Fügen Sie auf dem Blatt **Kontaktinformationen** Ihre Kontaktinformationen hinzu. Klicken Sie auf **Create**.
   
    ![Support: Kontakt](./media/active-directory-b2c-support/support-contact.png)
8. Nachdem Ihre Supportanfrage übermittelt wurde, können Sie sie überwachen, indem Sie im Startmenü auf **Hilfe und Support** und dann auf **Supportanfragen verwalten** klicken.

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>Bekanntes Problem: Senden von Supportanfragen im Kontext eines B2C-Mandanten
Wenn Sie den oben beschriebenen Schritt 2 nicht durchführen und versuchen, eine Supportanfrage im Kontext Ihres B2C-Mandanten zu erstellen, wird der folgende Fehler angezeigt.

> [!IMPORTANT]
> Versuchen Sie nicht, sich in Ihrem B2C-Mandanten für ein neues Azure-Abonnement zu registrieren.  
> 
> 

![Support – Kein Abonnement](./media/active-directory-b2c-support/support-no-sub.png)

