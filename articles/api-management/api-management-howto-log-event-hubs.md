---
title: Protokollieren von Ereignissen in Azure Event Hubs mit Azure API Management | Microsoft Docs
description: Erfahren Sie, wie Sie mit Azure API Management Ereignisse in Azure Event Hubs protokollieren.
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: a310236179677046ec49930b07cfdffdadc37974
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Protokollieren von Ereignissen in Azure Event Hubs mit Azure API Management
Azure Event Hubs ist ein hochgradig skalierbarer Dateneingangsdienst, der Millionen von Ereignissen pro Sekunde erfassen kann. Auf diese Weise können Sie riesige Datenmengen verarbeiten und analysieren, die von vernetzten Geräten und Anwendungen erzeugt werden. Event Hubs fungiert als „Eingangstür“ für eine Ereignispipeline. Nach der Erfassung in Event Hubs können Sie Daten mit einem beliebigen Echtzeit-Analyseanbieter oder mit Batchverarbeitungs-/Speicheradaptern umwandeln und speichern. Event Hubs entkoppelt die Erzeugung eines Datenstroms von Ereignissen von der Nutzung dieser Ereignisse, sodass  Ereignisconsumer nach einem eigenen Zeitplan auf Ereignisse zugreifen können.

Dies ist ein Begleitartikel zum Video [Integrate Azure API Management with Event Hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) (Integrieren von Azure API Management in Event Hubs; in englischer Sprache). Hier wird beschrieben, wie API Management-Ereignisse mithilfe von Azure Event Hubs protokolliert werden.

## <a name="create-an-azure-event-hub"></a>Erstellen eines Azure Event Hubs
Zum Erstellen eines neuen Event Hubs melden Sie sich beim klassischen [Azure-Portal](https://manage.windowsazure.com) an und klicken auf **Neu**->**App Services**->**Service Bus**->**Event Hub**->**Schnellerfassung**. Geben Sie einen Namen und eine Region für den Event Hub ein, und wählen Sie ein Abonnement und einen Namespace aus. Wenn Sie zuvor noch keinen Namespace erstellt haben, können Sie ihn erstellen, indem Sie im Textfeld **Namespace** einen Namen eingeben. Wenn alle Eigenschaften konfiguriert sind, klicken Sie auf **Neuen Event Hub erstellen** , um den Event Hub zu erstellen.

![Event Hub erstellen][create-event-hub]

Anschließend wechseln Sie zur Registerkarte **Konfigurieren** für den neuen Event Hub und erstellen zwei Richtlinien für **gemeinsamen Zugriff**. Nennen Sie die erste **Senden**, und geben Sie ihr die Berechtigungen zum **Senden**.

![Richtlinie zum Senden][sending-policy]

Nennen Sie die zweite **Empfangen**, geben Sie ihr Berechtigungen zum **Lauschen**, und klicken Sie auf **Speichern**.

![Richtlinie zum Empfangen][receiving-policy]

Jede Richtlinie für gemeinsamen Zugriff ermöglicht Anwendungen, Ereignisse an den Event Hub zu senden und von diesem zu empfangen. Für den Zugriff auf die Verbindungszeichenfolgen für diese Richtlinien wechseln Sie zur Registerkarte **Dashboard** des Event Hubs und klicken auf **Verbindungsinformationen**.

![Verbindungszeichenfolge][event-hub-dashboard]

Die Verbindungszeichenfolge **Senden** wird beim Protokollieren von Ereignissen verwendet, und die Verbindungszeichenfolge **Empfangen** wird beim Herunterladen von Ereignissen vom Event Hub verwendet.

![Verbindungszeichenfolge][event-hub-connection-string]

## <a name="create-an-api-management-logger"></a>Erstellen eines API Management-Loggers
Der Event Hub ist nun vorhanden. Der nächste Schritt besteht darin, einen [Logger](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) im API Management-Dienst zu konfigurieren, sodass Ereignisse im Event Hub protokolliert werden können.

API Management-Logger werden mit der [API Management-REST-API](http://aka.ms/smapi)konfiguriert. Machen Sie sich vor der erstmaligen Verwendung der REST-API mit den [Voraussetzungen](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#Prerequisites) vertraut, und stellen Sie sicher, dass der [Zugriff auf die REST- API aktiviert wurde](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Um den Logger zu erstellen, senden Sie mithilfe der folgenden Vorlage eine HTTP PUT-Anforderung.

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview`

* Ersetzen Sie `{your service}` durch den Namen Ihrer API Management-Dienstinstanz.
* Ersetzen Sie `{new logger name}` durch den gewünschten Namen für den neuen Logger. Sie verweisen auf diesen Namen, wenn Sie die [log-to-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) -Richtlinie konfigurieren.

Fügen Sie der Anforderung die folgenden Header hinzu:

* Content-Type: application/json
* Authorization : SharedAccessSignature 58...
  * Anweisungen zum Generieren von `SharedAccessSignature` finden Sie unter [Authentifizierung für Azure API Management-REST-API](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Geben Sie den Anforderungstext gemäß der folgenden Vorlage ein.

```json
{
  "type" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the Azure Classic Portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `type` muss auf `AzureEventHub` festgelegt werden.
* `description` stellt eine optionale Beschreibung des Loggers bereit und kann auf Wunsch auch leer gelassen werden (als Zeichenfolge mit der Länge null).
* `credentials` enthält den `name` und die `connectionString` Ihres Azure Event Hubs.

Wenn Sie die Anforderung senden und der Logger daraufhin erstellt wurde, wird der Statuscode `201 Created` zurückgegeben.

> [!NOTE]
> Informationen über weitere mögliche Rückgabecodes und die Gründe dafür erhalten Sie unter [Erstellen eines Loggers](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Die Vorgehensweisen für weitere Vorgänge, z.B. zum Auflisten, Aktualisieren und Löschen, finden Sie in der Dokumentation zur Entität [Logger](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity).
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Konfigurieren von log-to-eventhub-Richtlinien
Nachdem Sie den Logger in API Management konfiguriert haben, können Sie die Richtlinien zum Protokollieren im Event Hub („log-to-eventhub-Richtlinien“) für die gewünschten Ereignisse konfigurieren. Die log-to-eventhub-Richtlinie kann im Abschnitt mit Richtlinien für eingehenden Datenverkehr oder im Abschnitt mit Richtlinien für ausgehenden Datenverkehr verwendet werden.

Zum Konfigurieren der Richtlinien melden Sie sich im [Azure-Portal](https://portal.azure.com) an, navigieren zu Ihrem API Management-Service und klicken auf **Herausgeberportal**, um das Herausgeberportal zu öffnen.

![Herausgeberportal][publisher-portal]

Klicken Sie links im API Management-Menü auf **Richtlinien**, wählen Sie das gewünschte Produkt und die API aus, und klicken Sie auf **Richtlinie hinzufügen**. In diesem Beispiel fügen wir die Richtlinie der **Echo API** im Produkt **Unlimited** hinzu.

![Richtlinie hinzufügen][add-policy]

Positionieren Sie den Cursor auf dem Richtlinienabschnitt `inbound`, und klicken Sie auf die Richtlinie **Im EventHub protokollieren**, um die Anweisungsvorlage für die `log-to-eventhub`-Richtlinie einzufügen.

![Richtlinieneditor][event-hub-policy]

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```

Ersetzen Sie `logger-id` durch den Namen des API Management-Loggers, den Sie im vorherigen Schritt konfiguriert haben.

Sie können jeden Ausdruck verwenden, der eine Zeichenfolge als Wert für das Element `log-to-eventhub` zurückgibt. In diesem Beispiel wird eine Zeichenfolge protokolliert, die das Datum, die Uhrzeit, den Dienstnamen, die Anforderungs-ID, die IP-Adresse der Anforderung und den Vorgangsnamen enthält.

Klicken Sie auf **Speichern** , um die aktualisierte Richtlinienkonfiguration zu speichern. Die Richtlinie ist sofort nach dem Speichern aktiv, und im vorgesehenen Event Hub werden Ereignisse protokolliert.

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zu Azure Event Hubs
  * [Erste Schritte mit Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Empfangen von Nachrichten mit EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Programmierleitfaden für Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Erfahren Sie mehr über die Integration der API-Verwaltung und Event Hubs
  * [Verweis zu Protokollierungstool](https://docs.microsoft.com/rest/api/apimanagement/loggers)
  * [log-to-eventhub policy reference](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Überwachen von APIs mit Azure API Management, Event Hubs und Runscope](api-management-log-to-eventhub-sample.md)    

## <a name="watch-a-video-walkthrough"></a>Video zur exemplarischen Vorgehensweise
> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Integrate-Azure-API-Management-with-Event-Hubs/player]
>
>

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
