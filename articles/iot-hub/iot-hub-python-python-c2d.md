---
title: C2D-Nachrichten mit Azure IoT Hub (Python) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe der Azure IoT SDKs für Python C2D-Nachrichten von einem Azure IoT Hub an ein Gerät senden. Sie passen eine simulierte Geräte-App für den Empfang von C2D-Nachrichten und eine Back-End-App zum Senden der C2D-Nachrichten an.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 3613062cf8765a4aec3327b660bb5818898f2dd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110417"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Senden von C2D-Nachrichten mit IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub ist ein vollständig verwalteter Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von Geräten und einem Lösungs-Back-End ermöglicht. In [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Python)](quickstart-send-telemetry-python.md) erfahren Sie, wie Sie einen IoT-Hub erstellen, eine Geräteidentität darin bereitstellen und eine simulierte Geräte-App programmieren, die D2C-Nachrichten (Device-to-Cloud, Gerät-zu-Cloud) sendet.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Dieses Tutorial baut auf [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Python)](quickstart-send-telemetry-python.md) auf. Es beschreibt Folgendes:

* Senden von C2D-Nachrichten aus Ihrem Lösungs-Back-End an ein einzelnes Gerät über IoT Hub.

* Empfangen von C2D-Nachrichten auf einem Gerät.

* Anfordern einer Übermittlungsbestätigung (*Feedback*) von Ihrem Lösungs-Back-End für Nachrichten, die von IoT Hub an ein einzelnes Gerät gesendet wurden.

Weitere Informationen zu Cloud-zu-Gerät-Nachrichten finden Sie im [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide-messaging.md).

Am Ende dieses Tutorials führen Sie zwei Python-Konsolen-Apps aus:

* **SimulatedDevice.py** ist eine modifizierte Version der in [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Python)](quickstart-send-telemetry-python.md) erstellten App. Sie stellt eine Verbindung mit Ihrer IoT Hub-Instanz her und empfängt C2D-Nachrichten.

* **SendCloudToDeviceMessage.py**, die über IoT Hub eine C2D-Nachricht an die simulierte Geräte-App sendet und dann die zugehörige Übermittlungsbestätigung empfängt.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Stellen Sie sicher, dass der Port 8883 in Ihrer Firewall geöffnet ist. Das Beispielgerät in diesem Artikel verwendet das MQTT-Protokoll, das über Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Empfangen von Nachrichten in der simulierten Geräte-App

In diesem Abschnitt erstellen Sie eine Python-Konsolen-App zum Simulieren des Geräts und empfangen C2D-Nachrichten aus dem IoT Hub.

1. Erstellen Sie in einem Text-Editor eine Datei namens **SimulatedDevice.py**.

2. Fügen Sie am Anfang der Datei `import`SimulatedDevice.py**die folgenden**-Anweisungen und -Variablen hinzu:

   ```python
    import threading
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

3. Fügen Sie der Datei **SimulatedDevice.py** dann folgenden Code hinzu. Ersetzen Sie den Platzhalterwert „{deviceConnectionString}“ durch die Verbindungszeichenfolge für das Gerät, das Sie in [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Python)](quickstart-send-telemetry-python.md) erstellt haben:

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Fügen Sie die folgende Funktion hinzu, um empfangene Nachrichten an der Konsole auszugeben:

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("Message received")
            print( "    Data: <<{}>>".format(message.data) )
            print( "    Properties: {}".format(message.custom_properties))
            print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
    ```

5. Fügen Sie den folgenden Code hinzu, um den Client zu initialisieren und auf den Empfang der C2D-Nachricht zu warten:

    ```python
    def iothub_client_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )
    ```

6. Fügen Sie die folgende main-Funktion hinzu:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Speichern und schließen Sie die Datei **SimulatedDevice.py**.

## <a name="get-the-iot-hub-connection-string"></a>Abrufen der IoT-Hub-Verbindungszeichenfolge

In diesem Artikel erstellen Sie einen Back-End-Dienst, um Cloud-zu-Gerät-Nachrichten über den IoT-Hub zu senden, den Sie unter [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Node.js)](quickstart-send-telemetry-python.md) erstellt haben. Damit Ihr Dienst Cloud-zu-Gerät-Nachrichten senden kann, muss er über die Berechtigung **Dienstverbindung** verfügen. Standardmäßig wird jeder IoT-Hub mit einer SAS-Richtlinie namens **service** erstellt, die diese Berechtigung erteilt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Senden einer C2D-Nachricht

In diesem Abschnitt erstellen Sie eine Python-Konsolen-App, die C2D-Nachrichten an die simulierte Geräte-App sendet. Sie benötigen die Geräte-ID des Geräts, das Sie in [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Python)](quickstart-send-telemetry-python.md) erstellt haben. Außerdem benötigen Sie die IoT-Hub-Verbindungszeichenfolge, die Sie zuvor unter [Abrufen der IoT-Hub-Verbindungszeichenfolge](#get-the-iot-hub-connection-string) kopiert haben.

1. Erstellen Sie in einem Text-Editor eine Datei namens **SendCloudToDeviceMessage.py**.

2. Fügen Sie am Anfang der Datei `import`SendCloudToDeviceMessage.py**die folgenden**-Anweisungen und -Variablen hinzu:

    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

3. Fügen Sie der Datei **SendCloudToDeviceMessage.py** dann folgenden Code hinzu. Ersetzen Sie die Platzhalterwerte „{IoTHubConnectionString}“ und „{deviceId}“ durch die IoT-Hub-Verbindungszeichenfolge bzw. durch die Geräte-ID, die Sie zuvor notiert haben:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. Fügen Sie die folgende Funktion hinzu, um Feedbacknachrichten an der Konsole auszugeben:

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. Fügen Sie den folgenden Code hinzu, um eine Nachricht an Ihr Gerät zu senden und die Feedbacknachricht zu verarbeiten, wenn das Gerät die C2D-Nachricht bestätigt:

    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

6. Fügen Sie die folgende main-Funktion hinzu:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

7. Speichern und schließen Sie die Datei **SendCloudToDeviceMessage.py**.

## <a name="run-the-applications"></a>Ausführen der Anwendungen

Sie können nun die Anwendungen ausführen.

1. Öffnen Sie eine Eingabeaufforderung, und installieren Sie das **Azure IoT Hub Device SDK für Python**.

    ```shell
    pip install azure-iothub-device-client
    ```

2. Führen Sie an der Eingabeaufforderung den folgenden Befehl aus, um auf C2D-Nachrichten zu lauschen:

    ```shell
    python SimulatedDevice.py
    ```

    ![Ausführen der simulierten Geräte-App](./media/iot-hub-python-python-c2d/simulated-device.png)

3. Öffnen Sie eine neue Eingabeaufforderung, und installieren Sie das **Azure IoT Hub Service SDK für Python**.

    ```shell
    pip install azure-iothub-service-client
    ```

4. Führen Sie an einer Eingabeaufforderung den folgenden Befehl aus, um eine C2D-Nachricht zu senden und auf das Nachrichtenfeedback zu warten:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Ausführen der App zum Senden des C2D-Befehls](./media/iot-hub-python-python-c2d/send-command.png)

5. Beachten Sie die vom Gerät empfangene Nachricht.

    ![Empfangene Nachricht](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Lernprogramm haben Sie gelernt, wie Cloud-zu-Gerät-Nachrichten gesendet und empfangen werden.

Beispiele vollständiger Lösungen, die IoT Hub nutzen, finden Sie unter [Azure IoT-Solution Accelerator für die Remoteüberwachung](https://azure.microsoft.com/documentation/suites/iot-suite/).

Weitere Informationen zum Entwickeln von Lösungen mit IoT Hub finden Sie im [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide.md).
