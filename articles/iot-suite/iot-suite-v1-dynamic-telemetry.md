---
title: Verwenden dynamischer Telemetriedaten | Microsoft Docs
description: In diesem Tutorial erfahren Sie, wie Sie dynamische Telemetriedaten mit der vorkonfigurierten Lösung für die Remoteüberwachung von Azure IoT Suite verwenden.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 60e9ee00fabf15a62e782c70bca251b1a8e617c3
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723652"
---
# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>Verwenden dynamischer Telemetriedaten mit der vorkonfigurierten Lösung für die Remoteüberwachung

Die an die vorkonfigurierte Lösung für die Remoteüberwachung gesendeten dynamischen Telemetriedaten lassen sich visualisieren. Die simulierten Geräte, die mit der vorkonfigurierten Lösung bereitgestellt werden, senden Telemetriedaten zu Temperatur und Luftfeuchtigkeit, die Sie im Dashboard visualisieren können. Wenn Sie die vorhandenen simulierten Geräte anpassen, neue simulierte Geräte erstellen oder physische Geräte mit der vorkonfigurierten Lösung verbinden, können Sie andere Telemetriewerte wie Außentemperatur, U/min oder Windgeschwindigkeit senden. Sie können diese zusätzlichen Telemetriedaten dann im Dashboard visualisieren.

Dieses Tutorial verwendet ein einfaches simuliertes Node.js-Gerät, das Sie problemlos anpassen können, um mit dynamischen Telemetriedaten zu experimentieren.

Zum Durchführen dieses Tutorials benötigen Sie Folgendes:

* Ein aktives Azure-Abonnement. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion][lnk_free_trial].
* [Node.js][lnk-node], Version 0.12.x oder höher.

Sie können dieses Tutorial unter allen Betriebssystemen durcharbeiten, die die Installation von Node.js unterstützen (z.B. Windows oder Linux).

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

[!INCLUDE [iot-suite-v1-send-external-temperature](../../includes/iot-suite-v1-send-external-temperature.md)]

## <a name="add-a-telemetry-type"></a>Hinzufügen eines Telemetriedatentyps

Der nächste Schritt besteht im Ersetzen der vom simulierten Node.js-Gerät generierten Telemetriedaten durch eine neue Menge von Werten:

1. Beenden Sie das simulierte Node.js-Gerät durch Eingabe von **STRG+C** an der Eingabeaufforderung oder in der Shell.
2. In der Datei „remote_monitoring.js“ sehen Sie die Basisdatenwerte für die vorhandenen Telemetriedaten für Temperatur, Luftfeuchtigkeit und Außentemperatur. Fügen Sie für **rpm** (U/Min.) folgendermaßen einen Basisdatenwert hinzu:

    ```nodejs
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. Das simulierte Node.js-Gerät verwendet die **generateRandomIncrement**-Funktion in der Datei „remote_monitoring.js“, um eine zufällige Erhöhung zu den Basisdatenwerten hinzuzufügen. Randomisieren Sie den Wert **rpm** , indem Sie hinter der vorhandenen Randomisierung eine Codezeile hinzufügen:

    ```nodejs
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. Fügen Sie den neuen RPM-Wert der JSON-Nutzlast hinzu, die das Gerät an IoT Hub sendet:

    ```nodejs
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. Führen Sie das simulierte Node.js-Gerät mit dem folgenden Befehl aus:

    `node remote_monitoring.js`

6. Beobachten Sie den neuen Telemetrietyp „RPM“, der im Diagramm im Dashboard angezeigt wird:

![Hinzufügen von RPM zum Dashboard][image3]

> [!NOTE]
> Sie müssen möglicherweise das Node.js-Gerät auf der Seite **Geräte** im Dashboard deaktivieren und aktivieren, damit die Änderung sofort wirksam wird.

## <a name="customize-the-dashboard-display"></a>Anpassen der Dashboardanzeige

Die Nachricht **Device-Info** kann Metadaten zur Telemetrie enthalten, die das Gerät an IoT Hub senden kann. Diese Metadaten können die Telemetriedaten angeben, die das Gerät sendet. Ändern Sie den Wert **deviceMetaData** in der Datei „remote_monitoring.js“ so, dass eine Definition von **Telemetry** auf die Definition von **Commands** folgt. Der folgende Codeausschnitt zeigt die **Commands**-Definition (setzen Sie unbedingt ein `,` hinter die Definition von **Commands**):

```nodejs
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [!NOTE]
> Die Remoteüberwachungslösung unterscheidet beim Vergleichen der Metadatendefinition mit Daten im Telemetriedatenstrom nicht zwischen Groß- und Kleinschreibung.


Durch Hinzufügen der **Telemetry** -Definition, wie vorherigen Codeausschnitt gezeigt, ändert sich nicht das Verhalten des Dashboards. Allerdings können die Metadaten auch ein **DisplayName** -Attribut enthalten, um die Anzeige im Dashboard anzupassen. Aktualisieren Sie die Metadatendefinition von **Telemetry** wie im folgenden Codeausschnitt gezeigt:

```nodejs
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

Der folgende Screenshot zeigt, wie sich durch diese Änderung am Dashboard die Diagrammlegende ändert:

![Anpassen der Diagrammlegende][image4]

> [!NOTE]
> Sie müssen möglicherweise das Node.js-Gerät auf der Seite **Geräte** im Dashboard deaktivieren und aktivieren, damit die Änderung sofort wirksam wird.

## <a name="filter-the-telemetry-types"></a>Filtern der Telemetrietypen

Standardmäßig zeigt das Diagramm im Dashboard jede Datenreihe im Telemetriedatenstrom an. Mithilfe der **Device-Info** -Metadaten können Sie die Anzeige bestimmter Telemetrietypen im Diagramm unterdrücken. 

Damit im Diagramm nur die Telemetriedaten zu Temperatur und Luftfeuchtigkeit angezeigt werden, entfernen Sie **ExternalTemperature** wie folgt aus den Metadaten zu **Device-Info** **Telemetry**:

```nodejs
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

**Outdoor Temperature** wird nicht mehr im Diagramm angezeigt:

![Filtern der Telemetriedaten im Dashboard][image5]

Diese Änderung wirkt sich nur auf die Darstellung des Diagramms aus. Die Datenwerte für **ExternalTemperature** werden weiterhin gespeichert und für die Back-End-Verarbeitung zur Verfügung gestellt.

> [!NOTE]
> Sie müssen möglicherweise das Node.js-Gerät auf der Seite **Geräte** im Dashboard deaktivieren und aktivieren, damit die Änderung sofort wirksam wird.

## <a name="handle-errors"></a>Fehlerbehandlung

Um einen Datenstrom im Diagramm anzuzeigen, muss dessen **Typ** in den Metadaten zu **Device-Info** mit dem Datentyp der Telemetriewerte übereinstimmen. Wenn beispielsweise die Metadaten angeben, dass der **Typ** der Luftfeuchtigkeitsdaten **int** ist, und **double** im Telemetriedatenstrom gefunden wird, werden die Telemetriedaten zur Luftfeuchtigkeit nicht im Diagramm gezeigt. Allerdings werden die **Humidity**-Werte weiterhin gespeichert und für die Back-End-Verarbeitung zur Verfügung gestellt.

## <a name="next-steps"></a>Nächste Schritte

Sie wissen nun, wie Sie dynamische Telemetriedaten verwenden. Weitere Informationen dazu, wie die vorkonfigurierten Lösungen Geräteinformationen nutzen, finden Sie unter: [Geräteinformationen-Metadaten in der vorkonfigurierten Lösung für die Remoteüberwachung][lnk-devinfo].

[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[image3]: media/iot-suite-v1-dynamic-telemetry/image3.png
[image4]: media/iot-suite-v1-dynamic-telemetry/image4.png
[image5]: media/iot-suite-v1-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
