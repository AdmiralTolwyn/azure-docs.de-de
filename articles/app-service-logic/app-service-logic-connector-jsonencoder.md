---
title: Verwendung des BizTalk JSON Encoder-Connectors in Logik-Apps | Microsoft Docs
description: Erstellen und Konfigurieren des BizTalk JSON Encoder-Connectors oder einer API-App und Verwenden in einer Logik-App in Azure App Service
services: logic-apps
documentationcenter: .net,nodejs,java
author: rajeshramabathiran
manager: erikre
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 04/20/2016
ms.author: rajram

---
# Erste Schritte mit dem BizTalk JSON Encoder und das Hinzufügen zur Logik-App
[!INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Der BizTalk JSON Encode Decode-Connector unterstützt Ihre App in Bezug auf die Interoperabilität von JSON- und XML-Daten. Sie können damit eine angegebene JSON-Instanz in XML konvertieren und umgekehrt.

Sie können den BizTalk JSON Encoder dem geschäftlichen Workflow hinzufügen und Daten im Rahmen dieses Workflows in einer Logik-App verarbeiten.

## Verwenden des BizTalk JSON Encoder
Um den BizTalk JSON Encoder zu verwenden, müssen Sie zunächst eine Instanz der BizTalk JSON Encoder-API-App erstellen. Dies kann entweder inline beim Erstellen einer Logik-App oder durch Auswählen der BizTalk JSON-Encoder-API-App aus dem Azure Marketplace erfolgen.

## Verwenden von BizTalk JSON Encoder auf der Designeroberfläche von Logik-Apps
Führen Sie die Schritte zum [Erstellen einer Logik-App] aus. Der BizTalk JSON Encoder kann als Aktion verwendet werden. Er besitzt keine Trigger.

### Aktion
* Klicken Sie im rechten Bereich auf BizTalk JSON Encoder.
  
    ![Aktionseinstellungen][3]
* Klicken Sie auf ->
  
    ![Liste der Aktionen][4]
* Der BizTalk JSON Encoder unterstützt zwei Aktionen. Wählen Sie *XML zu JSON*.
  
    ![Eingabe "XML zu JSON"][5]
* Nehmen Sie die Eingaben für die Aktion vor, und konfigurieren Sie sie.
  
    ![Konfigurierte Elemente codieren und senden][6]

| Parameter | Typ | Beschreibung des Parameters |
| --- | --- | --- |
| Eingabe-XML |Objekt |Eingabe-XML-Inhalt |
| Äußeren Umschlag entfernen |string |Flag ist auf das Entfernen des Root-Knotens aus dem XML-Inhalt festgelegt |

Die Aktion gibt eine JSON-Darstellung des eingegebenen Inhalts zurück.

## Optimale Nutzung Ihres Connectors
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

> [!NOTE]
> Wenn Sie Azure Logik-Apps ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [Logik-App testen](https://tryappservice.azure.com/?appservice=logic) sofort kostenlos eine kurzlebige Starter-Logik-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.
> 
> 

Anzeigen der Swagger-REST-API-Referenz unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

<!--References -->
[1]: app-service-logic-connector-tpm
[2]: app-service-logic-create-a-trading-partner-agreement
[3]: ./media/app-service-logic-json-encoder/ActionSettings.PNG
[4]: ./media/app-service-logic-json-encoder/ListOfActions.PNG
[5]: ./media/app-service-logic-json-encoder/EncodeInput.PNG
[6]: ./media/app-service-logic-json-encoder/EncodeConfigured.PNG

<!--Links -->
[Erstellen einer Logik-App]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0803_2016-->