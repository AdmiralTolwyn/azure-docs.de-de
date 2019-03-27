---
title: 'Schnellstart: Steuern eines Geräts über Azure IoT Hub (Android) | Microsoft-Dokumentation'
description: In dieser Schnellstartanleitung führen Sie zwei Java-Beispielanwendungen aus. Eine dieser Anwendungen ist eine Dienstanwendung, die mit Ihrem Hub verbundene Geräte remote steuern kann. Die andere Anwendung wird auf einem physischen oder einem simulierten Gerät ausgeführt, das mit Ihrem Hub verbunden ist und remote gesteuert werden kann.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/19/2018
ms.author: wesmc
ms.openlocfilehash: a9e5b00ad84a57f49303f2e6f9389b4791fd0f60
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58166945"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Schnellstart: Steuern eines mit einem IoT-Hub verbundenen Geräts (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub ist ein Azure-Dienst, mit dem Sie große Mengen von Telemetriedaten von Ihren IoT-Geräten in der Cloud erfassen und Ihre Geräte über die Cloud verwalten können. In dieser Schnellstartanleitung verwenden Sie eine *direkte Methode*, um ein simuliertes Gerät zu steuern, das mit Ihrer IoT Hub-Instanz verbunden ist. Sie können direkte Methoden verwenden, um das Verhalten eines mit Ihrer IoT Hub-Instanz verbundenen Geräts zu ändern.

In dieser Schnellstartanleitung werden zwei vorab geschriebene Java-Anwendungen verwendet:

* Eine simulierte Geräteanwendung, die auf direkte Methoden reagiert, die von einer Back-End-Dienstanwendung aufgerufen werden. Um die Aufrufe der direkten Methode zu empfangen, stellt diese Anwendung eine Verbindung mit einem gerätespezifischen Endpunkt in Ihrer IoT Hub-Instanz her.

* Eine Dienstanwendung, die die direkte Methode auf dem Android-Gerät aufruft. Um eine direkte Methode auf einem Gerät aufzurufen, stellt diese Anwendung eine Verbindung mit einem dienstseitigen Endpunkt in Ihrer IoT Hub-Instanz her.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen


* Android Studio (https://developer.android.com/studio/). Weitere Informationen zur Android Studio-Installation finden Sie unter [Install Android Studio](https://developer.android.com/studio/install) (Installieren von Android Studio).

* In dem Beispiel in diesem Artikel wird Android SDK 27 verwendet.

* Für diese Schnellstartanleitung sind zwei Beispielanwendungen erforderlich: die [Android-Beispielanwendung für das Geräte-SDK](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) und die [Android-Beispielanwendung für das Dienst-SDK](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample). Beide Beispiele finden Sie im Repository „azure-iot-samples-java“ auf GitHub. Laden Sie das Repository [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java) herunter, oder klonen Sie es.


## <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

Wenn Sie das vorherige Tutorial [Schnellstart: Senden von IoT-Telemetriedaten von einem Android-Gerät](quickstart-send-telemetry-android.md) abgeschlossen haben, können Sie diesen Schritt überspringen und den bereits erstellten IoT-Hub verwenden.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrieren eines Geräts

Wenn Sie das vorherige Tutorial [Schnellstart: Senden von IoT-Telemetriedaten von einem Android-Gerät](quickstart-send-telemetry-android.md) abgeschlossen haben, können Sie diesen Schritt überspringen und das in der vorherigen Schnellstartanleitung registrierte Gerät verwenden.

Ein Gerät muss bei Ihrer IoT Hub-Instanz registriert sein, um eine Verbindung herstellen zu können. In dieser Schnellstartanleitung verwenden Sie Azure Cloud Shell, um ein simuliertes Gerät zu registrieren.

1. Führen Sie die folgenden Befehle in Azure Cloud Shell aus, um die IoT Hub-CLI-Erweiterung hinzuzufügen und die Geräteidentität zu erstellen.

   **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

   **MyAndroidDevice**: Der für das registrierte Gerät angegebene Name. Verwenden Sie „MyAndroidDevice“ wie gezeigt. Wenn Sie für Ihr Gerät einen anderen Namen wählen, müssen Sie diesen unter Umständen innerhalb des gesamten Artikels verwenden und den Gerätenamen in den Beispielanwendungen aktualisieren, bevor Sie sie ausführen.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create \
      --hub-name YourIoTHubName --device-id MyAndroidDevice
    ```

2. Führen Sie die folgenden Befehle in Azure Cloud Shell aus, um die _Geräteverbindungszeichenfolge_ für das soeben registrierte Gerät abzurufen:

   **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub wählen.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name YourIoTHubName \
      --device-id MyAndroidDevice \
      --output table
    ```

    Notieren Sie sich die Geräteverbindungszeichenfolge, die wie folgt aussieht:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Dieser Wert wird später in der Schnellstartanleitung benötigt.

## <a name="retrieve-the-service-connection-string"></a>Abrufen der Verbindungszeichenfolge für den Dienst

Darüber hinaus benötigen Sie eine _Dienstverbindungszeichenfolge_, damit die Back-End-Dienstanwendung eine Verbindung mit Ihrem IoT-Hub herstellen und dann Methoden ausführen und Nachrichten abrufen kann. Der folgende Befehl ruft die Dienstverbindungszeichenfolge für Ihre IoT Hub-Instanz ab:

**YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

```azurecli-interactive
az iot hub show-connection-string --name YourIoTHubName --output table
```

Notieren Sie sich die Dienstverbindungszeichenfolge, die wie folgt aussieht:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}`

Dieser Wert wird später in der Schnellstartanleitung benötigt. Die Dienstverbindungszeichenfolge unterscheidet sich von der Geräteverbindungszeichenfolge.

## <a name="listen-for-direct-method-calls"></a>Lauschen auf Aufrufe direkter Methoden

Die Geräte-SDK-Beispielanwendung kann auf einem physischen Android-Gerät oder in einem Android-Emulator ausgeführt werden. Die Beispielanwendung stellt eine Verbindung mit einem gerätespezifischen Endpunkt auf Ihrem IoT-Hub her, sendet simulierte Telemetriedaten und lauscht auf Aufrufe direkter Methoden aus Ihrem Hub. In dieser Schnellstartanleitung weist der Aufruf einer direkten Methode aus dem Hub das Gerät an, das Intervall zu ändern, in dem es Telemetriedaten sendet. Das simulierte Gerät sendet eine Bestätigung an Ihren Hub, nachdem es die direkte Methode ausgeführt hat.

1. Öffnen Sie das Android-Beispielprojekt von GitHub in Android Studio. Das Projekt befindet sich im folgenden Verzeichnis Ihrer geklonten oder heruntergeladenen Kopie des Repositorys [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java):

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. Öffnen Sie in Android Studio die Datei *gradle.properties* für das Beispielprojekt, und ersetzen Sie den Platzhalter **Device_Connection_String** durch die zuvor notierte Geräteverbindungszeichenfolge.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. Klicken Sie in Android Studio auf **File** > **Sync Project with Gradle Files** (Datei > Projekt mit Gradle-Dateien synchronisieren). Vergewissern Sie sich, dass der Buildvorgang abgeschlossen wurde.

   > [!NOTE]
   > Wenn bei der Synchronisierung des Projekts ein Fehler auftritt, kann einer der folgenden Gründe infrage kommen:
   >
   > * Die Versionen von Android Gradle-Plug-In und Gradle, auf die im Projekt verwiesen wird, sind für Ihre Version von Android Studio veraltet. Befolgen Sie [diese Anweisungen](https://developer.android.com/studio/releases/gradle-plugin), um die richtigen Versionen des Plug-Ins und von Gradle für Ihre Installation zu installieren und auf sie zu verweisen.
   > * Der Lizenzvertrag für das Android SDK wurde nicht signiert. Befolgen Sie die Anweisungen in der Buildausgabe, um den Lizenzvertrag zu signieren, und laden Sie das SDK herunter.


4. Klicken Sie anschließend auf **Run** > **Run 'app'** (Ausführen > App ausführen). Konfigurieren Sie die App für die Ausführung auf einem physischen Android-Gerät oder in einem Android-Emulator. Weitere Informationen zum Ausführen einer Android-App auf einem physischen Gerät oder in einem Emulator finden Sie unter [Run your app](https://developer.android.com/training/basics/firstapp/running-app) (Ausführen Ihrer App).

5. Klicken Sie nach dem Laden der App auf die Schaltfläche **Start** (Starten), um mit dem Senden von Telemetriedaten an Ihre IoT Hub-Instanz zu beginnen:

    ![Anwendung](media/quickstart-send-telemetry-android/sample-screenshot.png)

Diese App muss während der Ausführung des Dienst-SDK-Beispiels weiter auf einem physischen Gerät oder in einem Emulator ausgeführt werden, damit das Telemetrieintervall während der Ausführungszeit aktualisiert wird.


## <a name="read-the-telemetry-from-your-hub"></a>Lesen der Telemetriedaten aus Ihrem Hub

In diesem Abschnitt verwenden Sie Azure Cloud Shell mit der [IoT-Erweiterung](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest), um die Gerätenachrichten zu überwachen, die vom Android-Gerät gesendet werden.

1. Führen Sie über Azure Cloud Shell den folgenden Befehl aus, um eine Verbindung mit Ihrem IoT-Hub herzustellen und Nachrichten zu lesen:

   **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub wählen.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```
    Der folgende Screenshot zeigt die Ausgabe, während der IoT-Hub die vom Android-Gerät gesendeten Telemetriedaten empfängt:

      ![Lesen der Gerätenachrichten mithilfe der Azure CLI](media/quickstart-send-telemetry-android/read-data.png)

Standardmäßig sendet die Telemetrie-App alle fünf Sekunden Telemetriedaten vom Android-Gerät. Im nächsten Abschnitt aktualisieren Sie mit dem Aufruf einer direkten Methode das Telemetrieintervall für das Android-IoT-Gerät.


## <a name="call-the-direct-method"></a>Aufrufen der direkten Methode

Die Dienstanwendung stellt eine Verbindung mit einem dienstseitigen Endpunkt in Ihrer IoT Hub-Instanz her. Die Anwendung sendet über Ihre IoT Hub-Instanz Aufrufe direkter Methoden an ein Gerät und lauscht auf Bestätigungen.

Führen Sie diese App auf einem separaten physischen Android-Gerät oder in einem Android-Emulator aus.

Eine IoT Hub-Back-End-Dienstanwendung wird in der Regel in der Cloud ausgeführt, um die Risiken im Zusammenhang mit der vertraulichen Verbindungszeichenfolge, die alle Geräte in einer IoT Hub-Instanz steuert, zu verringern. In diesem Beispiel wird sie nur zu Demonstrationszwecken als Android-App ausgeführt. Die anderen Sprachversionen in dieser Schnellstartanleitung enthalten weitere Beispiele, die stärker auf die Back-End-Dienstanwendung ausgerichtet sind.

1. Öffnen Sie das Android-Dienstbeispielprojekt von GitHub in Android Studio. Das Projekt befindet sich im folgenden Verzeichnis Ihrer geklonten oder heruntergeladenen Kopie des Repositorys [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java):

        \azure-iot-samples-java\iot-hub\Samples\service\AndroidSample

2. Öffnen Sie in Android Studio die Datei *gradle.properties* für das Beispielprojekt, und aktualisieren Sie den Wert der Eigenschaften **ConnectionString** und **DeviceId** mit der zuvor notierten Dienstverbindungszeichenfolge und der registrierten Android-Geräte-ID.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. Klicken Sie in Android Studio auf **File** > **Sync Project with Gradle Files** (Datei > Projekt mit Gradle-Dateien synchronisieren). Vergewissern Sie sich, dass der Buildvorgang abgeschlossen wurde.

   > [!NOTE]
   > Wenn bei der Synchronisierung des Projekts ein Fehler auftritt, kann einer der folgenden Gründe infrage kommen:
   >
   > * Die Versionen von Android Gradle-Plug-In und Gradle, auf die im Projekt verwiesen wird, sind für Ihre Version von Android Studio veraltet. Befolgen Sie [diese Anweisungen](https://developer.android.com/studio/releases/gradle-plugin), um die richtigen Versionen des Plug-Ins und von Gradle für Ihre Installation zu installieren und auf sie zu verweisen.
   > * Der Lizenzvertrag für das Android SDK wurde nicht signiert. Befolgen Sie die Anweisungen in der Buildausgabe, um den Lizenzvertrag zu signieren, und laden Sie das SDK herunter.


4. Klicken Sie anschließend auf **Run** > **Run 'app'** (Ausführen > App ausführen). Konfigurieren Sie die App für die Ausführung auf einem separaten physischen Android-Gerät oder in einem Android-Emulator. Weitere Informationen zum Ausführen einer Android-App auf einem physischen Gerät oder in einem Emulator finden Sie unter [Run your app](https://developer.android.com/training/basics/firstapp/running-app) (Ausführen Ihrer App).

5. Aktualisieren Sie nach dem Laden der App den Wert **Set Messaging Interval** (Nachrichtenintervall festlegen) auf **1000**, und klicken Sie auf **Invoke** (Aufrufen).

    Das Intervall für Telemetrienachrichten wird in Millisekunden angegeben. Das Telemetriestandardintervall des Gerätebeispiels wird auf fünf Sekunden festgelegt. Mit dieser Änderung wird das Android-IoT-Gerät aktualisiert, sodass Telemetriedaten jede Sekunde gesendet werden.

    ![Eingeben des Telemetrieintervalls](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. Die App erhält eine Bestätigung mit der Angabe, ob die Methode erfolgreich ausgeführt wurde.

    ![Bestätigung der direkten Methode](media/quickstart-control-device-android/direct-method-ack.png)



## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie in einer Back-End-Anwendung eine direkte Methode auf einem Gerät aufgerufen und in einer simulierten Geräteanwendung auf den Aufruf einer direkten Methode geantwortet.

Um zu erfahren, wie Sie Gerät-zu-Cloud-Nachrichten an verschiedene Ziele in der Cloud weiterleiten, fahren Sie mit dem nächsten Tutorial fort.

> [!div class="nextstepaction"]
> [Tutorial: Weiterleiten von Telemetriedaten zur Verarbeitung an verschiedene Endpunkte](tutorial-routing.md)
