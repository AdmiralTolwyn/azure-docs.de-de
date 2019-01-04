---
title: 'Azure Time Series Insights: Hinzufügen eines IoT-Hubs – Gewusst wie: Hinzufügen einer IoT Hub-Ereignisquelle zu Azure Time Series Insights | Microsoft-Dokumentation'
description: In diesem Artikel wird beschrieben, wie eine Ereignisquelle hinzugefügt wird, die mit einem IoT-Hub in Ihrer Time Series Insights-Umgebung verbunden ist.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/30/2018
ms.custom: seodec18
ms.openlocfilehash: 3e370bd4cebb84d7ee9f607fc6640218ee202bb9
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321270"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Hinzufügen einer IoT Hub-Ereignisquelle zu einer Time Series Insights-Umgebung

In diesem Artikel wird beschrieben, wie im Azure-Portal eine Ereignisquelle hinzugefügt wird, die Daten aus einem Azure IoT Hub in Ihre Azure Time Series Insights-Umgebung einliest.

> [!NOTE]
> Die Anweisungen in diesem Artikel gelten sowohl für Azure Time Series Insights (allgemein verfügbar)- als auch für Time Series Insights Preview-Umgebungen.

## <a name="prerequisites"></a>Voraussetzungen

* Erstellen einer [Azure Time Series Insights-Umgebung](time-series-insights-update-create-environment.md).
* Erstellen eines [IoT-Hubs über das Azure-Portal](../iot-hub/iot-hub-create-through-portal.md).
* An den IoT-Hub müssen aktive Nachrichtenereignisse gesendet werden.
* Erstellen Sie eine dedizierte Consumergruppe in dem IoT-Hub, die die Time Series Insight-Umgebung verwenden kann. Jede Time Series Insights-Ereignisquelle benötigt eine eigene dedizierte Consumergruppe, die nicht mit anderen Consumern gemeinsam genutzt wird. Wenn mehrere Leser Ereignisse aus der gleichen Consumergruppe nutzen, werden allen Lesern wahrscheinlich Fehler angezeigt. Weitere Details finden Sie im [Azure IoT Hub-Entwicklerhandbuch](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Hinzufügen einer Consumergruppe zu Ihrem IoT Hub

Anwendungen verwenden Consumergruppen, um Daten aus Azure IoT Hub abzurufen. Geben Sie eine dedizierte Consumergruppe an, die nur von dieser Time Series Insights-Umgebung verwendet wird, um zuverlässig Daten aus Ihrem IoT-Hub zu lesen.

So fügen Sie Ihrem IoT-Hub eine neue Consumergruppe hinzu

1. Suchen Sie im Azure-Portal nach Ihrem IoT-Hub, und öffnen Sie ihn.

1. Wählen Sie in dem Menü unter **Einstellungen** die Option **Integrierte Endpunkte** aus, und wählen Sie dann den Endpunkt **Ereignisse** aus.

   ![Wählen Sie auf der Seite „Integrierte Endpunkte“ die Schaltfläche „Ereignisse“ aus.][1]

1. Geben Sie unter **Consumergruppen** einen eindeutigen Namen für die Consumergruppe ein. Verwenden Sie diesen Namen, wenn Sie eine neue Ereignisquelle in Ihrer Time Series Insights-Umgebung erstellen.

1. Wählen Sie **Speichern**aus.

## <a name="add-a-new-event-source"></a>Hinzufügen einer neuen Ereignisquelle

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im linken Menü die Option **Alle Ressourcen** aus. Wählen Sie Ihre Time Series Insights-Umgebung aus.

1. Wählen Sie unter **Umgebungstopologie** den Eintrag **Ereignisquellen** und dann **Hinzufügen** aus.

   ![Wählen Sie „Ereignisquellen“ aus, und klicken Sie dann auf die Schaltfläche „Hinzufügen“.][2]

1. Geben Sie im Bereich **Neue Ereignisquelle** für **Ereignisquellenname** einen Namen ein, der in dieser Time Series Insights-Umgebung eindeutig ist. Geben Sie z. B. **event-stream** ein.

1. Wählen Sie als **Quelle** **IoT-Hub** aus.

1. Wählen Sie einen Wert für **Importoption** aus:

   * Wenn Sie in einem Ihrer Abonnements bereits über einen IoT-Hub verfügen, wählen Sie **IoT-Hub aus verfügbaren Abonnements verwenden** aus. Diese Option stellt den einfachsten Ansatz dar.
   * Wenn sich der IoT-Hub außerhalb Ihrer Abonnements befindet oder Sie erweiterte Optionen auswählen möchten, wählen Sie **IoT-Hub-Einstellungen manuell angeben** aus.

   ![Auswählen von Optionen im Bereich „Neue Ereignisquelle“][3]

1. In der folgenden Tabelle werden die Eigenschaften beschrieben, die für die Option **IoT Hub aus verfügbaren Abonnements verwenden** erforderlich sind:

   ![Bereich „Neue Ereignisquelle“: In der Option „IoT Hub aus verfügbaren Abonnements verwenden“ festzulegende Eigenschaften][4]

   | Eigenschaft | BESCHREIBUNG |
   | --- | --- |
   | Abonnement-ID | Wählen Sie das Abonnement aus, in dem dieser IoT-Hub erstellt wurde.
   | IoT Hub-Name | Wählen Sie den Namen des IoT-Hubs aus.
   | IoT Hub-Richtlinienname | Wählen Sie die SAS-Richtlinie aus. Sie finden die SAS-Richtlinie auf der Registerkarte „IoT-Hub-Einstellungen“. Jede SAS-Richtlinie umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. Die SAS-Richtlinie für die Ereignisquelle *muss* über die Berechtigung zum Herstellen einer **Dienstverbindung** verfügen.
   | IoT Hub-Richtlinienschlüssel | Der Schlüssel ist bereits angegeben.
   | IoT Hub-Consumergruppe | Die Consumergruppe, die Ereignisse aus dem IoT-Hub liest. Wir empfehlen ausdrücklich, dass Sie eine dedizierte Consumergruppe für Ihre Ereignisquelle verwenden.
   | Ereignisserialisierungsformat | Zurzeit ist JSON das einzige verfügbare Serialisierungsformat. Die Ereignismeldungen müssen in diesem Format vorliegen, damit Daten gelesen werden können. |
   | Name der Timestamp-Eigenschaft | Um diesen Wert ermitteln, müssen Sie das Nachrichtenformat der Nachrichtendaten kennen, die an den IoT-Hub gesendet werden. Dieser Wert entspricht **name** der spezifischen Ereigniseigenschaft in den Nachrichtendaten, die Sie als Ereigniszeitstempel verwenden möchten. Bei dem Wert wird die Groß-/Kleinschreibung beachtet. Wenn dieser Wert nicht angegeben wird, wird der Zeitpunkt der **Einreihung des Ereignisses** in die Warteschlange in der Ereignisquelle als Ereigniszeitstempel verwendet. |

1. In der folgenden Tabelle werden die für die Option **IoT Hub-Einstellungen manuell angeben** erforderlichen Eigenschaften beschrieben:

   | Eigenschaft | BESCHREIBUNG |
   | --- | --- |
   | Abonnement-ID | Das Abonnement, in dem der IoT-Hub erstellt wurde.
   | Ressourcengruppe | Der Name der Ressourcengruppe, in der der IoT-Hub erstellt wurde.
   | IoT Hub-Name | Der Name Ihres IoT-Hubs. Bei der Erstellung Ihres IoT-Hubs haben Sie einen Namen für den IoT-Hub eingegeben.
   | IoT Hub-Richtlinienname | Die SAS-Richtlinie. Sie können die SAS-Richtlinie auf der Registerkarte „IoT-Hub-Einstellungen“ erstellen. Jede SAS-Richtlinie umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. Die SAS-Richtlinie für die Ereignisquelle *muss* über die Berechtigung zum Herstellen einer **Dienstverbindung** verfügen.
   | IoT Hub-Richtlinienschlüssel | Der Schlüssel für den gemeinsamen Zugriff, der für die Authentifizierung des Zugriffs auf den Azure Service Bus-Namespace verwendet wird. Geben Sie hier den primären oder sekundären Schlüssel ein.
   | IoT Hub-Consumergruppe | Die Consumergruppe, die Ereignisse aus dem IoT-Hub liest. Wir empfehlen ausdrücklich, dass Sie eine dedizierte Consumergruppe für Ihre Ereignisquelle verwenden.
   | Ereignisserialisierungsformat | Zurzeit ist JSON das einzige verfügbare Serialisierungsformat. Die Ereignismeldungen müssen in diesem Format vorliegen, damit Daten gelesen werden können. |
   | Name der Timestamp-Eigenschaft | Um diesen Wert ermitteln, müssen Sie das Nachrichtenformat der Nachrichtendaten kennen, die an den IoT-Hub gesendet werden. Dieser Wert entspricht **name** der spezifischen Ereigniseigenschaft in den Nachrichtendaten, die Sie als Ereigniszeitstempel verwenden möchten. Bei dem Wert wird die Groß-/Kleinschreibung beachtet. Wenn dieser Wert nicht angegeben wird, wird der Zeitpunkt der **Einreihung des Ereignisses** in die Warteschlange in der Ereignisquelle als Ereigniszeitstempel verwendet. |

1. Fügen Sie den dedizierten Time Series Insights-Consumergruppennamen hinzu, den Sie Ihrem IoT-Hub hinzugefügt haben.

1. Klicken Sie auf **Erstellen**.

   ![Die Schaltfläche „Erstellen“.][5]

1. Nach der Erstellung der Ereignisquelle beginnt Time Series Insights automatisch damit, Daten in Ihre Umgebung zu streamen.

## <a name="next-steps"></a>Nächste Schritte

* [Definieren von Datenzugriffsrichtlinien](time-series-insights-data-access.md) zum Schützen der Daten
* [Senden von Ereignissen](time-series-insights-send-events.md) an die Ereignisquelle
* Zugreifen auf Ihre Umgebung über den [Time Series Insights-Explorer](https://insights.timeseries.azure.com)

<!-- Images -->
[1]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_one.png
[2]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_two.png
[3]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_three.png
[4]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_four.png
[5]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_five.png