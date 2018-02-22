---
title: "Erstellen von Plänen in Azure Stack | Microsoft-Dokumentation"
description: "Erstellen Sie als Cloudadministrator einen Plan, mit dem Abonnenten virtuelle Computer bereitstellen können."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 5eefca3541ae9f73514f80b0f8df9e5027600f87
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-plan-in-azure-stack"></a>Erstellen von Plänen in Azure Stack

*Gilt für: integrierte Azure Stack-Systeme und Azure Stack Development Kit*

[Pläne](azure-stack-key-features.md) sind Gruppen mit mindestens einem Dienst. Als Anbieter können Sie Pläne erstellen und Ihren Benutzern anbieten. Im Gegenzug abonnieren Ihre Benutzer die Angebote, um die darin enthaltenen Pläne und Dienste zu verwenden. In diesem Beispiel wird veranschaulicht, wie Sie einen Plan erstellen, der die Compute-, Netzwerk- und Speicherressourcenanbieter enthält. Mit diesem Plan erhalten Abonnenten die Möglichkeit, virtuelle Computer bereitzustellen.

1. Melden Sie sich im Azure Stack-Administratorportal (https://adminportal.local.azurestack.external) an. Geben Sie die Anmeldeinformationen für das Konto ein, das Sie in Schritt 5 des Abschnitts zum Thema [Ausführen des PowerShell-Skripts](azure-stack-run-powershell-script.md) erstellt haben.

2. Klicken Sie zum Erstellen von Plänen und Angeboten, die Benutzer abonnieren können, auf **Neu** > **Mandantenangebote + Pläne** > **Plan**.

   ![](media/azure-stack-create-plan/image01.png)
3. Geben Sie auf dem Blatt **Neuer Plan** Werte für die Optionen **Anzeigename** und **Ressourcenname** ein. Der Anzeigename ist der verständliche Anzeigename des Plans, der für Benutzer angezeigt wird. Nur der Administrator kann den Ressourcennamen einsehen. Es handelt sich um den Namen, mit dem Administratoren den Plan als Azure-Ressourcen-Manager-Ressource bearbeiten.

   ![](media/azure-stack-create-plan/image02.png)
4. Erstellen Sie eine neue **Ressourcengruppe**, oder wählen Sie eine vorhandene aus, die als Container für den Plan dient.

   ![](media/azure-stack-create-plan/image02a.png)
5. Klicken Sie auf **Dienste**, wählen Sie **Microsoft.Compute**, **Microsoft.Network** und **Microsoft.Storage**, und klicken Sie dann auf **Auswählen**.

   ![](media/azure-stack-create-plan/image03.png)
6. Klicken Sie auf **Kontingente** und dann auf **Microsoft.Storage (local)**. Wählen Sie entweder das Standardkontingent aus, oder klicken Sie auf **Neues Kontingent erstellen**, um das Kontingent anzupassen.

   ![](media/azure-stack-create-plan/image04.png)
7. Geben Sie beim Erstellen eines neuen Kontingents einen Namen für das Kontingent ein, legen Sie die Kontingentwerte fest, und klicken Sie auf **OK** und dann auf den Namen des neuen Kontingents.

   ![](media/azure-stack-create-plan/image06.png)
8. Klicken Sie auf **Microsoft.Network (local)**. Wählen Sie entweder das Standardkontingent aus, oder klicken Sie auf **Neues Kontingent erstellen**, um das Kontingent anzupassen.

    ![](media/azure-stack-create-plan/image07.png)
9. Geben Sie beim Erstellen eines neuen Kontingents einen Namen für das Kontingent ein, legen Sie die Kontingentwerte fest, und klicken Sie auf **OK** und dann auf den Namen des neuen Kontingents.

    ![](media/azure-stack-create-plan/image08.png)
10. Klicken Sie auf **Microsoft.Compute (local)**. Wählen Sie entweder das Standardkontingent aus, oder klicken Sie auf **Neues Kontingent erstellen**, um das Kontingent anzupassen.

    ![](media/azure-stack-create-plan/image09.png)
11. Geben Sie beim Erstellen eines neuen Kontingents einen Namen für das Kontingent ein, legen Sie die Kontingentwerte fest, und klicken Sie auf **OK** und dann auf den Namen des neuen Kontingents.

    ![](media/azure-stack-create-plan/image10.png)
12. Klicken Sie auf dem Blatt **Kontingente** auf **OK**. Klicken Sie anschließend auf dem Blatt **Neuer Plan** auf **Erstellen**, um den Plan zu erstellen.

    ![](media/azure-stack-create-plan/image11.png)
13. Klicken Sie zum Anzeigen des neuen Plans auf **Alle Ressourcen**, suchen Sie nach dem Plan, und klicken Sie auf seinen Namen.

    ![](media/azure-stack-create-plan/image12.png)

### <a name="next-steps"></a>Nächste Schritte
[Erstellen von Angeboten](azure-stack-create-offer.md)
