---
title: C2D-Nachrichten mit Azure IoT Hub (Java) | Microsoft Docs
description: Erfahren Sie, wie Sie mithilfe der Azure IoT SDKs für Java C2D-Nachrichten von einer Azure IoT Hub-Instanz an ein Gerät senden. Sie passen eine simulierte Geräte-App zum Empfangen von C2D-Nachrichten und eine Back-End-App zum Senden der C2D-Nachrichten an.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.author: dobett
ms.openlocfilehash: 410a156d60aa9b17da9c36e043082c291eea4849
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808110"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Senden von C2D-Nachrichten mit IoT Hub (Java)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub ist ein vollständig verwalteter Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von Geräten und einem Lösungs-Back-End ermöglicht. Im Tutorial [Erste Schritte mit IoT Hub] erfahren Sie, wie ein IoT-Hub erstellt, eine Geräteidentität im Hub bereitgestellt und eine simulierte Geräte-App programmiert wird, die D2C-Nachrichten (Device to Cloud, Gerät zu Cloud) sendet.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Dieses Tutorial baut auf [Erste Schritte mit IoT Hub]auf. Es beschreibt Folgendes:

* Senden von C2D-Nachrichten aus Ihrem Lösungs-Back-End an ein einzelnes Gerät über IoT Hub.
* Empfangen von C2D-Nachrichten auf einem Gerät.
* Anfordern einer Übermittlungsbestätigung (*Feedback*) von Ihrem Lösungs-Back-End für Nachrichten, die von IoT Hub an ein einzelnes Gerät gesendet wurden.

Weitere Informationen zu C2D-Nachrichten finden Sie im [IoT Hub-Entwicklerhandbuch][IoT Hub developer guide - C2D].

Am Ende dieses Tutorials führen Sie zwei Java-Konsolen-Apps aus:

* **simulated-device**, eine abgewandelte Version der in [Erste Schritte mit IoT Hub]erstellten App, die eine Verbindung mit IoT Hub herstellt und C2D-Nachrichten empfängt.
* **send-c2d-messages**, die über IoT Hub eine C2D-Nachricht an die simulierte Geräte-App sendet und dann die zugehörige Übermittlungsbestätigung empfängt.

> [!NOTE]
> IoT Hub bietet durch Azure IoT-Geräte-SDKs Unterstützung für zahlreiche Geräteplattformen und Sprachen (u.a. C, Java und JavaScript). Im [Azure IoT Developer Center] finden Sie Schritt-für-Schritt-Anweisungen zum Verbinden eines Geräts mit dem Code in diesem Tutorial sowie allgemeine Informationen zum Verbinden mit Azure IoT Hub.

Für dieses Tutorial benötigen Sie Folgendes:

* Eine vollständig funktionierende Version des Tutorials [Erste Schritte mit IoT Hub](iot-hub-java-java-getstarted.md) oder [Verarbeiten von D2C-Nachrichten mit IoT Hub](tutorial-routing.md)
* Das neueste [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Ein aktives Azure-Konto. (Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto][lnk-free-trial] erstellen.)

## <a name="receive-messages-in-the-simulated-device-app"></a>Empfangen von Nachrichten in der simulierten Geräte-App

In diesem Abschnitt ändern Sie die simulierte Geräte-App, die Sie in [Erste Schritte mit IoT Hub] erstellt haben, um C2D-Nachrichten von IoT Hub zu empfangen.

1. Öffnen Sie die Datei „simulated-device\src\main\java\com\mycompany\app\App.java“ mit einem Text-Editor.

2. Fügen Sie die folgende **MessageCallback**-Klasse als geschachtelte Klasse innerhalb der **App**-Klasse hinzu. Die **execute**-Methode wird aufgerufen, wenn das Gerät eine Nachricht von IoT Hub empfängt. In diesem Beispiel informiert das Gerät den IoT-Hub immer darüber, dass die Nachricht abgeschlossen wurde:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));
    
        return IotHubMessageResult.COMPLETE;
      }
    }
    ```
3. Ändern Sie die **main**-Methode so, dass vor dem Öffnen des Clients eine **AppMessageCallback**-Instanz erstellt und die **SetMessageCallback**-Methode aufgerufen wird:

    ```java
    client = new DeviceClient(connString, protocol);
   
    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Wenn Sie anstelle von MQTT oder AMQP den HTTPS-Transport verwenden, prüft die **DeviceClient**-Instanz nur selten (seltener als alle 25 Minuten), ob Nachrichten von IoT Hub vorliegen. Weitere Informationen zu den Unterschieden zwischen der MQTT-, AMQP- und HTTPS-Unterstützung sowie zur IoT Hub-Drosselung finden Sie im [Entwicklungsleitfaden für IoT Hub][IoT Hub developer guide - C2D].

4. Führen Sie zum Erstellen der App **simulated-device** mit Maven den folgenden Befehl an der Eingabeaufforderung im Ordner „simulated-device“ aus:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="send-a-cloud-to-device-message"></a>Senden einer C2D-Nachricht

In diesem Abschnitt erstellen Sie eine Java-Konsolen-App, die C2D-Nachrichten an die simulierte Geräte-App sendet. Sie benötigen die Geräte-ID des Geräts, das Sie im Tutorial [Erste Schritte mit IoT Hub] hinzugefügt haben. Sie benötigen auch die IoT Hub-Verbindungszeichenfolge für den Hub, die Sie im [Azure-Portal] finden.

1. Erstellen Sie mithilfe des folgenden Befehls über die Eingabeaufforderung ein Maven-Projekt namens **send-c2d-messages**. Beachten Sie, dass es sich hierbei um einen einzelnen langen Befehl handelt:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Navigieren Sie an der Eingabeaufforderung zu dem neuen Ordner „send-c2d-messages“.

3. Öffnen Sie die Datei „pom.xml“ aus dem Ordner „send-c2d-messages“ in einem Text-Editor, und fügen Sie dem Knoten **dependencies** die folgende Abhängigkeit hinzu. Durch Hinzufügen der Abhängigkeit können Sie in Ihrer Anwendung das Paket **iothub-java-service-client** verwenden, um mit Ihrem IoT Hub-Dienst zu kommunizieren:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Sie finden die aktuelle Version von **iot-service-client** mithilfe der [Maven-Suche][lnk-maven-service-search].

4. Speichern und schließen Sie die Datei „pom.xml“.

5. Öffnen Sie die Datei „send-c2d-messages\src\main\java\com\mycompany\app\App.java“ in einem Text-Editor.

6. Fügen Sie der Datei die folgenden **import** -Anweisungen hinzu:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Fügen Sie der **App**-Klasse die folgenden Klassenebenenvariablen hinzu, und ersetzen Sie dabei **{yourhubconnectionstring}** und **{yourdeviceid}** durch die zuvor notierten Werte:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    ```

8. Ersetzen Sie die **main**-Methode durch den folgenden Code. Dieser Code stellt die Verbindung mit Ihrer IoT Hub-Instanz her, sendet eine Nachricht an Ihr Gerät und wartet dann auf eine Empfangs- und Verarbeitungsbestätigung des Geräts:
   
    ```java
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
   
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver();
        if (feedbackReceiver != null) feedbackReceiver.open();
   
        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);
   
        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");
   
        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }
   
        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [!NOTE]
    > Der Einfachheit halber wird in diesem Lernprogramm keine Wiederholungsrichtlinie implementiert. Im Produktionscode sollten Sie Wiederholungsrichtlinien implementieren (etwa einen exponentiellen Backoff), wie im MSDN-Artikel zum [Behandeln vorübergehender Fehler]beschrieben.


9. Führen Sie zum Erstellen der App **simulated-device** mit Maven den folgenden Befehl an der Eingabeaufforderung im Ordner „simulated-device“ aus:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Ausführen der Anwendungen

Sie können nun die Anwendungen ausführen.

1. Führen Sie an einer Befehlszeile im Ordner „simulated-device“ den folgenden Befehl aus, um mit dem Senden von Telemetriedaten an Ihren IoT Hub und Empfangen von C2D-Nachrichten, die von Ihrem Hub gesendet werden, zu beginnen:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Ausführen der simulierten Geräte-App][img-simulated-device]

2. Führen Sie an einer Befehlszeile im Ordner „send-c2d-messages“ den folgenden Befehl aus, um eine C2D-Nachricht zu senden, und warten Sie auf eine Feedbackbestätigung:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Ausführen des Befehls zum Senden der C2D-Nachricht][img-send-command]

## <a name="next-steps"></a>Nächste Schritte

In diesem Lernprogramm haben Sie gelernt, wie Cloud-zu-Gerät-Nachrichten gesendet und empfangen werden. 

Beispiele vollständiger Lösungen, die IoT Hub nutzen, finden Sie unter [Solution Accelerator für die Azure IoT-Remoteüberwachung].

Weitere Informationen zum Entwickeln von Lösungen mit IoT Hub finden Sie im [IoT Hub-Entwicklerhandbuch].

<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[Erste Schritte mit IoT Hub]: iot-hub-java-java-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[IoT Hub-Entwicklerhandbuch]: iot-hub-devguide.md
[Azure IoT Developer Center]: http://azure.microsoft.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java
[Behandeln vorübergehender Fehler]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure-Portal]: https://portal.azure.com
[Solution Accelerator für die Azure IoT-Remoteüberwachung]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22