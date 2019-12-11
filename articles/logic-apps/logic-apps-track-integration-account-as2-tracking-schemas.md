---
title: AS2-Nachverfolgungsschemas für B2B-Nachrichten
description: Erstellen von AS2-Nachverfolgungsschemas zur Überwachung von B2B-Nachrichten in Integrationskonten für Azure Logic Apps mit Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 515d7cfc985ee9929f70de2c862170ff79ae4d60
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792811"
---
# <a name="create-schemas-for-tracking-as2-messages-and-mdns-in-integration-accounts-for-azure-logic-apps"></a>Erstellen von Schemas für die Nachverfolgung von AS2-Nachrichten und MDNs in Integrationskonten für Azure Logic Apps

Zur Unterstützung bei der Überwachung des Erfolgs, der Fehler und der Nachrichteneigenschaften für B2B-Transaktionen (Business-to-Buiness) können Sie in Ihrem Integrationskonto folgende AS2-Nachverfolgungsschemas verwenden:

* AS2-Nachrichten-Nachverfolgungsschema
* AS2-MDN-Nachverfolgungsschema

## <a name="as2-message-tracking-schema"></a>AS2-Nachrichten-Nachverfolgungsschema

```json
{
   "agreementProperties": {  
      "senderPartnerName": "",  
      "receiverPartnerName": "",  
      "as2To": "",  
      "as2From": "",  
      "agreementName": ""  
   },  
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "dispositionType": "",
      "fileName": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isMessageEncrypted": "",
      "isMessageCompressed": "",
      "correlationMessageId": "",
      "incomingHeaders": {
       },
      "outgoingHeaders": {
       },
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Eigenschaft | Typ | BESCHREIBUNG |
| --- | --- | --- |
| senderPartnerName | String | Name des Absenderpartners der AS2-Nachricht (Optional) |
| receiverPartnerName | String | Name des Empfängerpartners der AS2-Nachricht (Optional) |
| as2To | String | Name des Empfängers der AS2-Nachricht aus den Headern der AS2-Nachricht (Erforderlich) |
| as2From | String | Name des Absenders der AS2-Nachricht aus den Headern der AS2-Nachricht (Erforderlich) |
| agreementName | String | Name der AS2-Vereinbarung, nach der Nachrichten aufgelöst werden (Optional) |
| direction | String | Richtung des Nachrichtenflusses (Empfangen oder Senden) (Erforderlich) |
| messageId | String | ID der AS2-Nachricht aus den Headern der AS2-Nachricht (Optional) |
| dispositionType |String | MDN-Dispositionstypwert (Message Disposition Notification) (Optional) |
| fileName | String | Dateiname aus dem Header der AS2-Nachricht (Optional) |
| isMessageFailed |Boolean | Gibt an, ob die AS2-Nachricht fehlgeschlagen ist. (Erforderlich) |
| isMessageSigned | Boolean | Gibt an, ob die AS2-Nachricht signiert wurde. (Erforderlich) |
| isMessageEncrypted | Boolean | Gibt an, ob die AS2-Nachricht verschlüsselt wurde. (Erforderlich) |
| isMessageCompressed |Boolean | Gibt an, ob die AS2-Nachricht komprimiert wurde. (Erforderlich) |
| correlationMessageId | String | ID der AS2-Nachricht zum Korrelieren von Nachrichten mit MDNs (Optional) |
| incomingHeaders |Wörterbuch von JToken | Headerdetails von eingehenden AS2-Nachrichten (Optional) |
| outgoingHeaders |Wörterbuch von JToken | Headerdetails von ausgehenden AS2-Nachrichten (Optional) |
| isNrrEnabled | Boolean | Verwenden Sie den Standardwert, wenn der Wert unbekannt ist. (Erforderlich) |
| isMdnExpected | Boolean | Verwenden Sie den Standardwert, wenn der Wert unbekannt ist. (Erforderlich) |
| mdnType | Enum | Zulässige Werte sind **NotConfigured**, **Sync** und **Async**. (Erforderlich) |
||||

## <a name="as2-mdn-tracking-schema"></a>AS2-MDN-Nachverfolgungsschema

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": "g"
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "originalMessageId": "",
      "dispositionType": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isNrrEnabled": "",
      "statusCode": "",
      "micVerificationStatus": "",
      "correlationMessageId": "",
      "incomingHeaders": {
      },
      "outgoingHeaders": {
      }
   }
}
```

| Eigenschaft | Typ | BESCHREIBUNG |
| --- | --- | --- |
| senderPartnerName | String | Name des Absenderpartners der AS2-Nachricht (Optional) |
| receiverPartnerName | String | Name des Empfängerpartners der AS2-Nachricht (Optional) |
| as2To | String | Name des Partners, der die AS2-Nachricht erhält (Erforderlich) |
| as2From | String | Name des Partners, der die AS2-Nachricht sendet (Erforderlich) |
| agreementName | String | Name der AS2-Vereinbarung, nach der Nachrichten aufgelöst werden (Optional) |
| direction |String | Richtung des Nachrichtenflusses (Empfangen oder Senden) (Erforderlich) |
| messageId | String | ID der AS2-Nachricht (Optional) |
| originalMessageId |String | ID der ursprünglichen AS2-Nachricht (Optional) |
| dispositionType | String | MDN-Dispositionstypwert (Optional) |
| isMessageFailed |Boolean | Gibt an, ob die AS2-Nachricht fehlgeschlagen ist. (Erforderlich) |
| isMessageSigned |Boolean | Gibt an, ob die AS2-Nachricht signiert wurde. (Erforderlich) |
| isNrrEnabled | Boolean | Verwenden Sie den Standardwert, wenn der Wert unbekannt ist. (Erforderlich) |
| statusCode | Enum | Zulässige Werte sind **Accepted**, **Rejected** und **AcceptedWithErrors**. (Erforderlich) |
| micVerificationStatus | Enum | Zulässige Werte sind **NotApplicable**, **Succeeded** und **Failed**. (Erforderlich) |
| correlationMessageId | String | Korrelations-ID. Die ursprüngliche Nachrichten-ID (die Nachrichten-ID der Nachricht, für die MDN konfiguriert ist). (Optional) |
| incomingHeaders | Wörterbuch von JToken | Gibt Headerdetails von eingehenden Nachrichten an. (Optional) |
| outgoingHeaders |Wörterbuch von JToken | Gibt Headerdetails von ausgehenden Nachrichten an. (Optional) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B-Protokollnachverfolgungsschemas

Informationen zu B2B-Protokollnachverfolgungsschemas finden Sie unter:

* [X12-Nachverfolgungsschemas](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Benutzerdefinierte B2B-Nachverfolgungsschemas](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Nächste Schritte

* Machen Sie sich mit der [Überwachung von B2B-Nachrichten](logic-apps-monitor-b2b-message.md) vertraut.
* Informieren Sie sich über das [Nachverfolgen von B2B-Nachrichten in Azure Monitor-Protokollen](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).