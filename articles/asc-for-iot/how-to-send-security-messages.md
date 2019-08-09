---
title: Senden Ihrer Sicherheitsmeldungen an Azure Security Center für IoT | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre Sicherheitsmeldungen unter Verwendung von Azure Security Center für IoT senden.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2019
ms.author: mlottner
ms.openlocfilehash: c780eea15b9f064d3279c75ac2f967e8b6099ecb
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596209"
---
# <a name="send-security-messages-sdk"></a>Senden von Sicherheitsmeldungen (SDK)

In dieser Schrittanleitung werden die Funktionen von Azure Security Center für IoT erläutert, die zur Verfügung stehen, wenn Sie die Sicherheitsmeldungen Ihres Geräts ohne Verwendung eines Azure Security Center für IoT-Agents erfassen und senden möchten. Außerdem erfahren Sie, wie Sie dazu vorgehen müssen.  

In diesem Artikel lernen Sie Folgendes: 
> [!div class="checklist"]
> * Verwenden der API zum Senden von Sicherheitsmeldungen für C#
> * Verwenden der API zum Senden von Sicherheitsmeldungen für C

## <a name="azure-security-center-for-iot-capabilities"></a>Funktionen von Azure Security Center für IoT

Azure Security Center für IoT kann beliebige Sicherheitsmeldungsdaten verarbeiten und analysieren, solange die gesendeten Daten dem [Azure Security Center für IoT-Schema](https://aka.ms/iot-security-schemas) entsprechen und die Meldung als Sicherheitsmeldung festgelegt ist.

## <a name="security-message"></a>Sicherheitsmeldung

Azure Security Center für IoT definiert eine Sicherheitsmeldung anhand folgender Kriterien:
- Ob die Meldung mit dem Azure IoT C/C# SDK gesendet wurde
- Ob die Meldung dem [Sicherheitsmeldungsschema](https://aka.ms/iot-security-schemas) entspricht
- Ob die Meldung vor dem Senden als Sicherheitsmeldung festgelegt wurde

Jede Sicherheitsmeldung enthält die Metadaten des Senders, z. B. `AgentId`, `AgentVersion` oder `MessageSchemaVersion`, und eine Liste der Sicherheitsereignisse.
Das Schema definiert die gültigen und erforderlichen Eigenschaften der Sicherheitsmeldung, einschließlich der Ereignistypen.

>[!Note]
> Meldungen, die dem Schema nicht entsprechen, werden ignoriert. Überprüfen Sie das Schema, bevor Sie das Senden von Daten initiieren, da ignorierte Meldungen derzeit nicht gespeichert werden. 

>[!Note]
> Gesendete Meldungen, die nicht unter Verwendung des Azure IoT C/C# SDK als Sicherheitsmeldung festgelegt wurden, werden nicht an die Azure Security Center für IoT-Pipeline weitergeleitet.

## <a name="valid-message-example"></a>Beispiel für eine gültige Meldung

Das folgende Beispiel zeigt ein gültiges Sicherheitsmeldungsobjekt. Das Beispiel enthält die Metadaten der Meldung und ein `ProcessCreate`-Sicherheitsereignis.

Nachdem sie als Sicherheitsmeldung festgelegt und gesendet wurde, wird diese Meldung in Azure Security Center für IoT verarbeitet.

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/myApp",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "aUser",
                    "CommandLine": "myApp -a -b"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Senden von Sicherheitsmeldungen 

Sie können Sicherheitsmeldungen ohne Verwendung des Azure Security Center für IoT-Agents mithilfe des [Azure IoT C#-Geräte-SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) oder des [Azure IoT C-Geräte-SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) senden.

Wenn Sie Daten Ihrer Geräte zur Verarbeitung an Azure Security Center für IoT senden möchten, verwenden Sie eine der folgenden APIs, um Meldungen für die korrekte Weiterleitung an die Verarbeitungspipeline von Azure Security Center für IoT zu markieren. 

Alle gesendeten Daten müssen dem [Meldungsschema von Azure Security Center für IoT](https://aka.ms/iot-security-schemas) entsprechen. Das gilt auch für Daten, die mit dem korrekten Header markiert wurden. 

### <a name="send-security-message-api"></a>API zum Senden von Sicherheitsmeldungen

Die API zum **Senden von Sicherheitsmeldungen**ist derzeit in C und C# verfügbar.  

#### <a name="c-api"></a>C#-API

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```

#### <a name="c-api"></a>C-API

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {
 
    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
 
    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);
 
    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }
 
cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }
 
    return success;
}
 
static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }
 
    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```

## <a name="next-steps"></a>Nächste Schritte
- Lesen Sie die [Übersicht](overview.md) über Azure Security Center für IoT.
- Machen Sie sich mit der [Architektur](architecture.md) von Azure Security Center für IoT vertraut.
- Aktivieren Sie den [Dienst](quickstart-onboard-iot-hub.md).
- Lesen Sie die [häufig gestellten Fragen](resources-frequently-asked-questions.md).
- Informieren Sie sich, wie Sie auf [Sicherheitsrohdaten](how-to-security-data-access.md) zugreifen.
- Machen Sie sich mit [Empfehlungen](concept-recommendations.md) vertraut.
- Machen Sie sich mit [Warnungen](concept-security-alerts.md) vertraut.
