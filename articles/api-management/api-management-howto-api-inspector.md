---
title: Debuggen von APIs mithilfe der Anforderungsablaufverfolgung in Azure API Management | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie die Anforderungsverarbeitungsschritte in Azure API Management überprüfen.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: cf9c56fa2ba75dc5b5ad4af59d111a0124f1a9df
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39057326"
---
# <a name="debug-your-apis-using-request-tracing"></a>Debuggen von APIs mit der Anforderungsablaufverfolgung

In diesem Tutorial wird beschrieben, wie Sie die Anforderungsverarbeitung für das Debugging und die Problembehandlung für Ihre API überprüfen. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Verfolgen eines Aufrufs

![API-Inspektor](media/api-management-howto-api-inspector/api-inspector001.PNG)

## <a name="prerequisites"></a>Voraussetzungen

+ Absolvieren Sie den folgenden Schnellstart: [Erstellen einer Azure API Management-Instanz](get-started-create-service-instance.md).
+ Schließen Sie darüber hinaus das folgende Tutorial ab: [Import and publish your first API](import-and-publish.md) (Importieren und Veröffentlichen Ihrer ersten API).

## <a name="trace-a-call"></a>Verfolgen eines Aufrufs

1. Klicken Sie auf **APIs**.
2. Klicken Sie in der API-Liste auf **Demo Conference API**.
3. Wählen Sie den Vorgang **GetSpeakers** aus.
4. Wechseln Sie zur Registerkarte **Test**.
5. Stellen Sie sicher, dass ein HTTP-Header namens **Ocp-Apim-Trace** enthalten und der Wert auf **true** festgelegt ist.

    ![API-Ablaufverfolgungsheader](media/api-management-howto-api-inspector/api-management-tracing-header.png)

    > [!NOTE]
    > Wenn „Ocp-Apim-Subscription-Key“ nicht automatisch ausgefüllt wird, können Sie den Wert abrufen, indem Sie die Schlüssel im Entwicklerportal auf der Profilseite verfügbar machen.

6. Klicken Sie auf **Senden**, um einen API-Aufruf durchzuführen. 
7. Warten Sie, bis der Aufruf abgeschlossen ist. 
8. Wechseln Sie in der **API-Konsole** zur Registerkarte **Ablaufverfolgung**. Sie können auf einen der folgenden Links klicken, um ausführliche Informationen zur Ablaufverfolgung anzuzeigen: **Eingehend**, **Back-End**, **Ausgehend**.

    Im Abschnitt **Eingehend** werden die ursprüngliche Anforderung, die API Management vom Aufrufer erhalten hat, sowie alle auf die Anforderung angewendeten Richtlinien angezeigt, einschließlich der Richtlinien „rate-limit“ und „set-header“, die wir in Schritt 2 hinzugefügt haben.

    Im Abschnitt **Back-End** werden die Anforderungen, die API Management an das API-Back-End gesendet hat, sowie die erhaltene Antwort angezeigt.
    
    Im Abschnitt **Ausgehend** werden alle Richtlinien angezeigt, die auf die Antwort angewendet werden, bevor sie zurück an den Aufrufer gesendet wird.
 
    > [!TIP]
    > Jeder Schritt zeigt zudem die Zeit an, die verstrichen ist, seit die Anforderung von API Management empfangen wurde.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Verfolgen eines Aufrufs

Fahren Sie mit dem nächsten Tutorial fort:

> [!div class="nextstepaction"]
> [Gefahrloses Vornehmen geringfügiger Änderungen mithilfe von Revisionen](api-management-get-started-revise-api.md)
