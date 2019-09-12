---
title: Herstellen der Verbindung eines Raspberry Pi-Geräts mit Ihrer Azure IoT Central-Anwendung (Python) | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie als Geräteentwickler ein Raspberry Pi-Gerät mit Ihrer Azure IoT Central-Anwendung über Python verbinden.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 5c6e99c62d199c36081113011845a3bf2607bcb3
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842558"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Verbinden eines Raspberry Pi-Geräts mit Ihrer Azure IoT Central-Anwendung (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

In diesem Artikel wird beschrieben, wie Sie als Geräteentwickler ein Raspberry Pi-Gerät mithilfe der Programmiersprache Python mit Ihrer Microsoft Azure IoT Central-Anwendung verbinden.

## <a name="before-you-begin"></a>Voraussetzungen

Um die in diesem Artikel aufgeführten Schritte auszuführen, benötigen Sie die folgenden Komponenten:

* Eine Azure IoT Central-Anwendung, die mit der Anwendungsvorlage **Beispiel-Entwickler-Kits** erstellt wurde. Weitere Informationen finden Sie unter [Schnellstart: Erstellen einer Anwendung](quick-deploy-iot-central.md).
* Sie verfügen über ein Raspberry Pi-Gerät, auf dem das Raspbian-Betriebssystem ausgeführt wird. Das Raspberry Pi-Gerät muss eine Verbindung mit dem Internet herstellen können. Weitere Informationen finden Sie unter [Einrichten Ihres Raspberry Pi-Geräts](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

> [!TIP]
> Weitere Informationen zum Einrichten eines Raspberry Pi-Geräts und Herstellen einer Verbindung damit finden Sie unter [Erste Schritte mit Raspberry Pi](https://projects.raspberrypi.org/en/pathways/getting-started-with-raspberry-pi).

## <a name="sample-devkits-application"></a>Anwendungsvorlage **Beispiel-DevKits**

Eine Anwendung, die mit der Anwendungsvorlage **Beispiel-Entwickler-Kits** erstellt wurde, enthält eine Gerätevorlage **Raspberry Pi** mit den folgenden Eigenschaften:

- Telemetriedaten, darunter die folgenden vom Gerät gesammelten Messungen:
  - Luftfeuchtigkeit
  - Temperatur
  - Pressure
  - Magnetometer (X, Y, Z)
  - Beschleunigungsmesser (X, Y, Z)
  - Gyroskop (X, Y, Z)
- Einstellungen
  - Spannung
  - Aktuell
  - Lüfterdrehzahl
  - IR-Umschalter
- Properties
  - Geräteeigenschaft „Nummer“
  - Cloudeigenschaft „Standort“

Vollständige Informationen zur Konfiguration der Gerätevorlage finden Sie unter [Details zur Raspberry Pi-Gerätevorlage](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Hinzufügen eines echten Geräts

Fügen Sie in Ihrer Azure IoT Central-Anwendung ein echtes Gerät aus der Gerätevorlage **Raspberry Pi** hinzu. Notieren Sie sich die Geräteverbindungsdetails (**Bereichs-ID**, **Geräte-ID** und **Primärschlüssel**). Weitere Informationen finden Sie unter [Hinzufügen eines echten Geräts zu Ihrer Azure IoT Central-Anwendung](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Konfigurieren des Raspberry Pi

Die folgenden Schritte beschreiben das Herunterladen und Konfigurieren der Python-Beispielanwendung über GitHub. Diese Beispielanwendung führt Folgendes durch:

* Senden von Telemetriedaten und Eigenschaftswerten an Azure IoT Central
* Reagieren auf Einstellungsänderungen, die in Azure IoT Central vorgenommen werden

1. Stellen Sie eine Verbindung mit einer Shellumgebung auf Ihrem Raspberry Pi her – entweder über den Raspberry Pi-Desktop oder remote per SSH.

1. Führen Sie den folgenden Befehl aus, um den IoT Central Python-Client zu installieren:

    ```bash
    pip install iotc
    ```

1. Herunterladen des Python-Beispielcodes:

    ```bash
    curl -O https://raw.githubusercontent.com/Azure/iot-central-firmware/master/RaspberryPi/app.py
    ```

1. Bearbeiten Sie die heruntergeladene Datei `app.py`, und ersetzen Sie die Platzhalter `DEVICE_ID`, `SCOPE_ID` und `PRIMARY/SECONDARY device KEY` durch die Verbindungswerte, die Sie sich zuvor notiert haben. Speichern Sie die Änderungen.

    > [!TIP]
    > In der Shell auf dem Raspberry Pi können Sie den Text-Editor **nano** oder **vi** verwenden.

1. Verwenden Sie den folgenden Befehl zum Ausführen des Beispiels:

    ```bash
    python app.py
    ```

    Ihr Raspberry Pi beginnt mit dem Senden von Telemetriemessungen an Azure IoT Central.

1. In Ihrer Azure IoT Central-Anwendung können Sie sehen, wie der auf dem Raspberry Pi-Gerät ausgeführte Code mit der Anwendung interagiert:

    * Auf der Seite **Messungen** für Ihr echtes Gerät werden Ihnen die vom Raspberry Pi gesendeten Telemetriedaten angezeigt.
    * Auf der Seite **Eigenschaften** können Sie die Geräteeigenschaft **Nummer** anzeigen.
    * Auf der Seite **Einstellungen** können Sie die Einstellungen am Raspberry Pi-Gerät ändern, wie z.B. Spannung und Lüfterdrehzahl. Wenn das Raspberry Pi-Gerät die Änderung bestätigt, wird die Einstellung als **synchronisiert** angezeigt.

## <a name="raspberry-pi-device-template-details"></a>Details zur Raspberry Pi-Gerätevorlage

Eine Anwendung, die mit der Anwendungsvorlage **Beispiel-Entwickler-Kits** erstellt wurde, enthält eine Gerätevorlage **Raspberry Pi** mit den folgenden Eigenschaften:

### <a name="telemetry-measurements"></a>Telemetriemessungen

| Feldname     | Units  | Minimum | Maximum | Dezimalstellen |
| -------------- | ------ | ------- | ------- | -------------- |
| Luftfeuchtigkeit       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Einstellungen

Numerische Einstellungen

| `Display name` | Feldname | Units | Dezimalstellen | Minimum | Maximum | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Spannung      | setVoltage | Volt | 0              | 0       | 240     | 0       |
| Aktuell      | setCurrent | Ampere  | 0              | 0       | 100     | 0       |
| Lüfterdrehzahl    | fanSpeed   | U/Min   | 0              | 0       | 1000    | 0       |

Einstellungen zum Ein-/Ausschalten

| `Display name` | Feldname | Text, wenn „eingeschaltet“ | Text, wenn „ausgeschaltet“ | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | EIN      | OFF      | Aus     |

### <a name="properties"></a>Properties

| type            | `Display name` | Feldname | Datentyp |
| --------------- | ------------ | ---------- | --------- |
| Geräteeigenschaft | Nummer   | dieNumber  | number    |
| Text            | Location     | location   | –       |

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun wissen, wie ein Raspberry Pi-Gerät mit Ihrer Azure IoT Central-Anwendung verbunden wird, empfiehlt sich als nächster Schritt das [Einrichten einer benutzerdefinierten Gerätevorlage](howto-set-up-template.md) für Ihr eigenes IoT-Gerät.
