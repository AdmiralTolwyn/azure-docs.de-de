---
title: Erstellen einer Funktion in Azure, die durch einen GitHub-Webhook ausgelöst wird | Microsoft-Dokumentation
description: Erstellen Sie mithilfe von Azure Functions eine serverlose Funktion, die durch einen GitHub-Webhook aufgerufen wird.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/28/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 05ad567e407a6506222acdb66ab38c4cfab76e4b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/30/2018
---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>Erstellen einer Funktion, die durch einen GitHub-Webhook ausgelöst wird

Erfahren Sie, wie Sie eine Funktion erstellen können, die von einer HTTP-Webhookanforderung mit einer GitHub-spezifischen Nutzlast ausgelöst wird.

![Eine Funktion im Azure-Portal, die durch einen GitHub-Webhook ausgelöst wird](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Voraussetzungen

+ Ein GitHub-Konto mit mindestens einem Projekt.
+ Ein Azure-Abonnement. Wenn Sie keins besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="create-an-azure-function-app"></a>Erstellen einer Azure Function-App

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Die Funktionen-App wurde erfolgreich erstellt.](./media/functions-create-first-azure-function/function-app-create-success.png)

Erstellen Sie als Nächstes in der neuen Funktionen-App eine Funktion.

<a name="create-function"></a>

## <a name="create-a-github-webhook-triggered-function"></a>Erstellen einer Funktion, die durch einen GitHub-Webhook ausgelöst wird

1. Erweitern Sie die Funktionen-App, und klicken Sie auf die Schaltfläche **+** neben **Functions**. Wenn dies die erste Funktion in Ihrer Funktionen-App ist, wählen Sie **Benutzerdefinierte Funktion**. Hiermit wird der vollständige Satz von Funktionsvorlagen angezeigt.

    ![Schnellstartseite für Funktionen im Azure-Portal](./media/functions-create-github-webhook-triggered-function/add-first-function.png)

2. Geben Sie im Suchfeld `github` ein, und wählen Sie dann Ihre gewünschte Sprache für die GitHub-Webhook-Triggervorlage aus. 

     ![Auswählen der GitHub-Webhook-Triggervorlage](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger.png) 

2. Geben Sie einen **Namen** für Ihre Funktion ein, und wählen Sie dann die Option **Erstellen**. 

     ![Konfigurieren der durch einen GitHub-Webhook ausgelösten Funktion im Azure-Portal](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger-2.png) 

3. Klicken Sie in der neuen Funktion auf **</> Funktions-URL abrufen**, kopieren Sie die Werte, und speichern Sie diese. Führen Sie die gleichen Schritte für **</> GitHub-Geheimnis abrufen** aus. Diese Werte benötigen Sie, um den Webhook in GitHub zu konfigurieren.

    ![Überprüfen des Funktionscodes](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png)

Erstellen Sie als Nächstes einen Webhook in Ihrem GitHub-Repository.

## <a name="configure-the-webhook"></a>Konfigurieren des Webhooks

1. Navigieren Sie in GitHub zu einem Repository, das Sie besitzen. Sie können auch ein anderes Repository verwenden, das Sie verzweigt haben. Verwenden Sie <https://github.com/Azure-Samples/functions-quickstart>, wenn Sie ein Repository forken müssen.

2. Wählen Sie **Einstellungen** > **Optionen**, und stellen Sie sicher, dass **Probleme** unter **Funktionen** aktiviert ist.

   ![„Probleme“ aktivieren](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook.png)

1. Wählen Sie in den **Einstellungen** die Option **Webhooks** > **Webhook hinzufügen**.

    ![Hinzufügen eines GitHub-Webhooks](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

1. Verwenden Sie die in der folgenden Tabelle angegebenen Einstellungen, und klicken Sie anschließend auf **Webhook hinzufügen**:

    ![Hinzufügen der Webhook-URL und Festlegen des Geheimnisses](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

| Einstellung | Empfohlener Wert | BESCHREIBUNG |
|---|---|---|
| **URL der Nutzlast** | Der kopierte Wert | Verwenden Sie den von **</> Abrufen der Funktions-URL** zurückgegebenen Wert. |
| **Inhaltstyp** | Anwendung/json | Die Funktion erwartet eine JSON-Nutzlast. |
| **Geheimnis**   | Der kopierte Wert | Verwenden Sie den von **</> Abrufen des GitHub-Geheimnisses** zurückgegebenen Wert. |
| Ereignistrigger | Auswählen einzelner Ereignisse erlauben | Es sollen nur Problemmeldungsereignisse ausgelöst werden.  |
| | Problembericht |  |

Der Webhook ist nun so konfiguriert, dass Ihre Funktion ausgelöst wird, wenn ein neuer Problembericht hinzugefügt wird.

## <a name="test-the-function"></a>Testen der Funktion

1. Öffnen Sie in Ihrem GitHub-Repository die Registerkarte **Probleme** in einem neuen Browserfenster.

1. Klicken Sie in dem neuen Fenster auf **Neues Problem**, geben Sie einen Titel ein, und klicken Sie anschließend auf **Submit new issue** (Neues Problem übermitteln).

1. Geben Sie in die Problemmeldung einen Kommentar ein, und klicken Sie auf **Kommentar**.

    ![Hinzufügen einer Problemmeldung in GitHub](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-add-comment.png)

1. Kehren Sie zum Portal zurück, und schauen Sie sich die Protokolle an. Sie sollten einen Nachverfolgungseintrag mit dem Text der neuen Meldung sehen.

     ![Ansicht des Meldungstexts in den Protokollen.](./media/functions-create-github-webhook-triggered-function/function-app-view-logs.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

Sie haben eine Funktion erstellt, die ausgelöst wird, wenn eine Anforderung von einem GitHub-Webhook empfangen wurde.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Weitere Informationen zu Webhooktriggern finden Sie unter [HTTP- und Webhookbindungen in Azure Functions](functions-bindings-http-webhook.md).
