---
title: 'Raspberry Pi in der Cloud (C): Verbinden von Raspberry Pi mit Azure IoT Hub | Microsoft-Dokumentation'
description: Erfahren Sie in diesem Tutorial, wie Sie Raspberry Pi einrichten und eine Verbindung mit Azure IoT Hub herstellen, damit Raspberry Pi Daten an die Azure-Cloudplattform senden kann.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: Azure IoT Raspberry Pi, Raspberry Pi IoT Hub, Raspberry Pi sendet Daten an Cloud, Raspberry Pi in der Cloud
ms.assetid: 68c0e730-1dc8-4e26-ac6b-573b217b302d
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/12/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b8fda17a8d1d1796d5299e3aba4b0fd5e719a4c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Verbinden von Raspberry Pi mit Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

In diesem Tutorial erlernen Sie die Grundlagen der Verwendung von Raspberry Pi 3 und Raspbian. Anschließend erfahren Sie, wie Sie Ihre Geräte mithilfe von [Azure IoT Hub](iot-hub-what-is-iot-hub.md) nahtlos mit der Cloud verbinden. Beispiele für Windows 10 IoT Core finden Sie im [Windows Dev Center](http://www.windowsondevices.com/).

Sie haben noch kein Kit? Probieren Sie den [Raspberry Pi-Onlinesimulator](iot-hub-raspberry-pi-web-simulator-get-started.md) aus. Oder erwerben Sie [hier](https://azure.microsoft.com/develop/iot/starter-kits) ein neues Kit.

## <a name="what-you-do"></a>Aufgaben

* Erstellen Sie einen IoT Hub.
* Registrieren Sie ein Gerät für Pi in Ihrem IoT Hub.
* Richten Sie Raspberry Pi ein.
* Führen Sie eine Beispielanwendung auf Pi aus, um Sensordaten an Ihren IoT Hub zu senden.

Verbinden Sie Raspberry Pi mit einem von Ihnen erstellten IoT Hub. Führen Sie anschließend eine Beispielanwendung auf Pi aus, um Temperatur- und Feuchtigkeitsdaten eines BME280-Sensors zu erfassen. Senden Sie abschließend die Sensordaten an Ihren IoT Hub.

## <a name="what-you-learn"></a>Lerninhalt

* Erstellen eines Azure IoT Hubs und Abrufen der Verbindungszeichenfolge für Ihr neues Gerät
* Herstellen der Verbindung von Pi mit einem BME280-Sensor
* Erfassen von Sensordaten durch Ausführen einer Beispielanwendung auf Pi
* Senden von Sensordaten an Ihren IoT Hub

## <a name="what-you-need"></a>Erforderliches Element

![Erforderliches Element](media/iot-hub-raspberry-pi-kit-c-get-started/0_starter_kit.jpg)

* Raspberry Pi 2- oder Raspberry Pi 3-Platine.
* Ein aktives Azure-Abonnement. Wenn Sie kein Azure-Konto besitzen, können Sie in nur wenigen Minuten ein [kostenloses Azure-Testkonto](https://azure.microsoft.com/free/) erstellen.
* Monitor, USB-Tastatur und Maus, die mit Pi verbunden werden.
* Mac oder PC, auf dem Windows oder Linux ausgeführt wird
* Internetverbindung.
* microSD-Karte mit mindestens 16 GB
* USB-SD-Adapter oder microSD-Karte, um das Betriebssystemimage auf die microSD-Karte zu kopieren
* Netzteil (5 V, 2 A) mit Micro-USB-Kabel (1,8 m)

Die folgenden Elemente sind optional:

* Ein zusammengebauter Adafruit BME280-Sensor für Temperatur, Luftdruck und Luftfeuchtigkeit
* Eine Steckplatine
* 6 F/M-Jumperdrähte
* 1 LED, diffus, 10 mm


> [!NOTE] 
Diese Elemente sind optional, da das Codebeispiel simulierte Sensordaten unterstützt.


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-raspberry-pi"></a>Einrichten von Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Installieren des Betriebssystems Raspbian für Pi

Bereiten Sie die microSD-Karte für die Installation des Raspbian-Image vor.

1. Laden Sie Raspbian herunter.
   1. [Laden Sie Raspbian Jessie mit Desktop herunter](https://www.raspberrypi.org/downloads/raspbian/) (die ZIP-Datei).
   1. Entpacken Sie das Raspbian-Image in einen Ordner auf Ihrem Computer.
1. Installieren Sie Raspbian auf der microSD-Karte.
   1. [Laden Sie das SD-Kartenbrennprogramm Etcher herunter, und installieren Sie es](https://etcher.io/).
   1. Führen Sie Etcher aus, und wählen Sie das Raspbian-Image, das Sie in Schritt 1 entpackt haben.
   1. Wählen Sie das microSD-Kartenlaufwerk. Hinweis: Etcher hat unter Umständen bereits das richtige Laufwerk ausgewählt.
   1. Klicken Sie auf „Flash“, um Raspbian auf der microSD-Karte zu installieren.
   1. Entfernen Sie die microSD-Karte aus dem Computer, wenn die Installation abgeschlossen ist. Es ist sicher, die microSD-Karte direkt zu entfernen, da Etcher die microSD-Karte nach Abschluss des Vorgangs automatisch auswirft bzw. die Bereitstellung aufhebt.
   1. Legen Sie die microSD-Karte in den Raspberry Pi ein.

### <a name="enable-ssh-and-spi"></a>Aktivieren von SSH und SPI

1. Verbinden Sie Pi mit dem Monitor, der Tastatur und Maus. Starten Sie Pi, und melden Sie sich bei Raspbian mit `pi` als Benutzername und `raspberry` als Kennwort an.
1. Klicken Sie auf das Raspberry-Symbol und dann auf **Preferences** > **Raspberry Pi Configuration**.

   ![Das Raspbian-Menü „Preferences“](media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. Legen Sie auf der Registerkarte **Interfaces** die Einstellungen **SPI** und **SSH** auf **Enable** fest, und klicken Sie dann auf **OK**. Wenn Sie keine physischen Sensoren besitzen und simulierte Sensordaten verwenden möchten, ist dieser Schritt optional.

   ![Aktivieren von SPI und SSH auf Raspberry Pi](media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
Weitere Referenzdokumente zum Aktivieren von SSH und SPI finden Sie auf[raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) und unter [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

### <a name="connect-the-sensor-to-pi"></a>Verbinden des Sensors mit Pi

Verwenden Sie die Steckplatine und Jumperdrähte, um eine LED und einen BME280-Sensor wie folgt zu verbinden. Wenn Sie keinen Sensor haben, [überspringen Sie diesen Abschnitt](#connect-pi-to-the-network).

![Die Raspberry Pi- und Sensorverbindung](media/iot-hub-raspberry-pi-kit-c-get-started/3_raspberry-pi-sensor-connection.png)

Mit dem BME280-Sensor können Daten zur Temperatur und Luftfeuchtigkeit erfasst werden. Die LED blinkt, wenn der Kommunikationsvorgang zwischen dem Gerät und der Cloud aktiv ist. 

Für Sensorstifte verwenden Sie die folgende Verkabelung:

| Start (Sensor und LED)     | Ende (Board)            | Kabelfarbe   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (Stift 5G)         | GPIO 4 (Stift 7)         | Weißes Kabel   |
| LED GND (Stift 6G)         | GND (Stift 6)            | Schwarzes Kabel   |
| VDD (Stift 18F)            | 3,3 V PWR (Stift 17)      | Weißes Kabel   |
| GND (Stift 20F)            | GND (Stift 20)           | Schwarzes Kabel   |
| SCK (Stift 21F)            | SPI0 SCLK (Stift 23)     | Oranges Kabel  |
| SDO (Stift 22F)            | SPI0 MISO (Stift 21)     | Gelbes Kabel  |
| SDI (Stift 23F)            | SPI0 MOSI (Stift 19)     | Grünes Kabel   |
| CS (Stift 24F)             | SPI0 CS (Stift 24)       | Blaues Kabel    |

Klicken Sie hier, um die [Raspberry Pi 2 und 3-Stiftzuordnungen](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) zur Referenz anzuzeigen.

Nachdem Sie den BME280 erfolgreich mit Ihrem Raspberry Pi verbunden haben, sollte das Gerät wie in der nachstehenden Abbildung aussehen.

![Verbindung zwischen Pi und BME280](media/iot-hub-raspberry-pi-kit-c-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Verbindung zwischen Pi und dem Netzwerk

Verbinden Sie den Raspberry Pi mit dem Micro-USB-Kabel mit der Stromversorgung. Verwenden Sie das Ethernet-Kabel zum Verbinden von Pi mit Ihrem verkabelten Netzwerk, oder befolgen Sie die [Anweisungen der Raspberry Pi Foundation](https://www.raspberrypi.org/learning/software-guide/wifi/), um Pi mit Ihrem WLAN zu verbinden. Notieren Sie sich die [IP-Adresse von Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address), nachdem es eine Verbindung zum Netzwerk hergestellt hat.

![Mit verkabeltem Netzwerk verbunden](media/iot-hub-raspberry-pi-kit-c-get-started/5_power-on-pi.jpg)


## <a name="run-a-sample-application-on-pi"></a>Ausführen einer Beispielanwendung auf Pi

### <a name="install-the-prerequisite-packages"></a>Installieren der Pakete mit den erforderlichen Komponenten

1. Verwenden Sie einen der folgenden SSH-Clients auf Ihrem Hostcomputer, um die Verbindung mit Ihrem Raspberry Pi herzustellen.
   
   **Windows-Benutzer**
   1. Laden Sie [PuTTY](http://www.putty.org/) für Windows herunter, und installieren Sie es. 
   1. Kopieren Sie die IP-Adresse von Pi, fügen Sie sie in den Abschnitt „Hostname (oder IP-Adresse)“ ein, und wählen Sie „SSH“ als Verbindungstyp.
   
   ![PuTTY](media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Mac- und Ubuntu-Benutzer**
   
   Verwenden Sie den integrierten SSH-Client unter Ubuntu oder macOS. Möglicherweise müssen Sie `ssh pi@<ip address of pi>` ausführen, um eine SSH-Verbindung mit Pi herzustellen.
   > [!NOTE] 
   Der Standardbenutzername ist `pi`, und das Kennwort ist `raspberry`.

1. Installieren Sie die erforderlichen Pakete für das Microsoft Azure IoT-Geräte-SDK für C und Cmake durch Ausführen der folgenden Befehle:

   ```bash
   grep -q -F 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
   grep -q -F 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
   sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys FDA6A393E4C2257F
   sudo apt-get update
   sudo apt-get install -y azure-iot-sdk-c-dev cmake libcurl4-openssl-dev git-core
   git clone git://git.drogon.net/wiringPi
   cd ./wiringPi
   ./build
   ```


### <a name="configure-the-sample-application"></a>Konfigurieren der Beispielanwendung

1. Klonen Sie die Beispielanwendung durch Ausführen des folgenden Befehls:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app
   ```
1. Öffnen Sie die Konfigurationsdatei durch Ausführen der folgenden Befehle:

   ```bash
   cd iot-hub-c-raspberrypi-client-app
   nano config.h
   ```

   ![Konfigurationsdatei](media/iot-hub-raspberry-pi-kit-c-get-started/6_config-file.png)

   Es gibt zwei Argumente in dieser Datei, die Sie konfigurieren können. Das erste ist `INTERVAL`, mit dem das Zeitintervall (in Millisekunden) zwischen zwei Nachrichten bestimmt wird, die an die Cloud gesendet werden. Das zweite heißt `SIMULATED_DATA` und ist ein boolescher Wert, der bestimmt, ob simulierte Sensordaten verwendet werden sollen.

   Wenn Sie **keinen Sensor haben**, legen Sie den Wert von `SIMULATED_DATA` auf `1` fest, damit die Beispielanwendung simulierte Sensordaten erstellt und nutzt.

1. Speichern und beenden Sie durch Drücken von Control+O > EINGABETASTE > Control+X.

### <a name="build-and-run-the-sample-application"></a>Erstellen und Ausführen der Beispielanwendung

1. Erstellen Sie die Beispielanwendung durch Ausführen des folgenden Befehls:

   ```bash
   cmake . && make
   ```
   ![Erstellen der Ausgabe](media/iot-hub-raspberry-pi-kit-c-get-started/7_build-output.png)

1. Führen Sie die Beispielanwendung durch Aufrufen des folgenden Befehls aus:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   Stellen Sie sicher, dass Sie die Verbindungszeichenfolge des Geräts kopieren und zwischen einfachen Anführungszeichen einfügen.


Die folgende Ausgabe sollte angezeigt werden, die die Sensordaten und Nachrichten zeigt, die an Ihren IoT Hub gesendet werden.

![Ausgabe: Von Raspberry Pi an Ihren IoT Hub gesendete Sensordaten](media/iot-hub-raspberry-pi-kit-c-get-started/8_run-output.png)

## <a name="next-steps"></a>Nächste Schritte

Sie haben eine Beispielanwendung ausgeführt, die Sensordaten sammelt und an Ihren IoT Hub sendet. Um die Nachrichten anzuzeigen, die Raspberry Pi an Ihre IoT Hub-Instanz gesendet hat, oder Nachrichten an Raspberry Pi in einer Befehlszeilenschnittstelle zu senden, gehen Sie das [Tutorial zum Verwalten von Cloudgerätemessaging mit iothub-explorer](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-explorer-cloud-device-messaging) durch.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
