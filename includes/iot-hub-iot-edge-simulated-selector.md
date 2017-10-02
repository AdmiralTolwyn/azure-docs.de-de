> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-simulated-device.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-simulated-device.md)

Diese exemplarische Vorgehensweise für das [Beispiels für Clouduploads von simulierten Geräten] zeigt, wie Sie [Azure IoT Edge][lnk-sdk] verwenden, um Gerät-zu-Cloud-Telemetriedaten von simulierten Geräten an IoT Hub zu senden.

Diese Anleitung umfasst:

* **Architektur:** Informationen zur Architektur des [Beispiels für Clouduploads von simulierten Geräten]
* **Erstellen und Ausführen**: die zum Erstellen und Ausführen des Beispiels erforderlichen Schritte.

## <a name="architecture"></a>Architektur

Das [Beispiels für Clouduploads von simulierten Geräten] zeigt, wie Sie ein Gateway erstellen, das Telemetriedaten von simulierten Geräten an einen IoT-Hub sendet. Ein Gerät kann möglicherweise keine direkte Verbindung mit IoT Hub herstellen, da das Gerät:

* kein Kommunikationsprotokoll verwendet, das von IoT Hub gelesen werden kann.
* nicht über die notwendigen intelligenten Funktionen verfügt, um sich die ihm von IoT Hub zugewiesene Identität zu merken.

Ein IoT Edge-Gateway kann diese Probleme auf folgende Weise lösen:

* Das Gateway liest das von dem Gerät verwendete Protokoll, empfängt D2C-Telemetriedaten von dem Gerät und leitet diese Nachrichten an IoT Hub über ein Protokoll weiter, das vom IoT-Hub gelesen werden kann.

* Das Gateway ordnet IoT Hub-Identitäten Geräten zu und fungiert als Proxy, wenn ein Gerät Nachrichten an IoT Hub sendet.

Das folgende Diagramm zeigt die Hauptkomponenten des Beispiels, einschließlich der IoT Edge-Module:

![Diagramm: Nachricht des simulierten Geräts wird über das Gateway an IoT Hub übermittelt][1]

Dieses Beispiel umfasst drei Module, die das Gateway bilden:
1. Protokollerfassungsmodul
1. Modul zur Zuordnung zwischen MAC-Adressen und IoT Hub-IDs&lt;-&gt;
1. IoT Hub-Kommunikationsmodul

Die Module übergeben untereinander Nachrichten nicht direkt. Die Module veröffentlichen Nachrichten in einem internen Broker, der die Nachrichten mithilfe eines Abonnementmechanismus an die anderen Module übermittelt. Weitere Informationen finden Sie unter [Erste Schritte mit Azure IoT Edge][lnk-gw-getstarted].

![Diagramm: Gatewaymodule kommunizieren mit dem Broker][2]

### <a name="protocol-ingestion-module"></a>Protokollerfassungsmodul

Das Protokollerfassungsmodul ist der Ausgangspunkt für den Weg von Daten von Geräten über das Gateway in die Cloud. 

In dem Beispiel gilt für das Modul Folgendes:

1. Es erstellt simulierte Temperaturdaten. Wenn Sie reale Geräte verwenden, liest das Modul die Daten von diesen physischen Geräten.
1. Er erstellt eine Nachricht.
1. Es platziert die simulierten Temperaturdaten in den Inhalt einer Nachricht.
1. Es fügt der Nachricht eine Eigenschaft mit einer Pseudo-MAC-Adresse hinzu.
1. Es stellt dem nächsten Modul in der Kette die Nachricht zur Verfügung.

Das Protokollerfassungsmodul ist **simulated_device.c** im Quellcode.

### <a name="mac-lt-gt-iot-hub-id-module"></a>Modul zur Zuordnung zwischen MAC-Adressen und IoT Hub-IDs&lt;-&gt;

Das Modul zur Zuordnung zwischen MAC&lt;-&gt;Adressen und IoT Hub-IDs fungiert als Übersetzer. Dieses Beispiel verwendet eine MAC-Adresse als eindeutige Geräte-ID und korreliert sie mit einer IoT Hub-Geräteidentität. Sie können jedoch auch selbst ein Modul schreiben, das eine andere eindeutige ID verwendet. Ihre Geräte könnten beispielsweise eindeutige Seriennummern aufweisen, oder die Telemetriedaten enthalten einen eindeutigen eingebetteten Gerätenamen.

In dem Beispiel gilt für das Modul Folgendes:

1. Es sucht nach Nachrichten mit einer MAC-Adresseigenschaft.
1. Ist eine MAC-Adresse vorhanden, wird der Nachricht eine weitere Eigenschaft mit einem IoT Hub-Geräteschlüssel hinzugefügt. 
1. Es stellt dem nächsten Modul in der Kette die Nachricht zur Verfügung.

Der Entwickler richtet eine Zuordnung zwischen MAC-Adressen und IoT Hub-Identitäten ein, um IoT Hub-Geräteidentitäten simulierten Geräten zuzuordnen. Der Entwickler fügt die Zuordnung manuell als Teil der Modulkonfiguration hinzu.

Das Modul zur Zuordnung zwischen MAC&lt;-&gt;Adressen und IoT Hub-IDs ist **identitymap.c** im Quellcode. 

### <a name="iot-hub-communication-module"></a>IoT Hub-Kommunikationsmodul

Das IoT Hub-Kommunikationsmodul stellt eine einzelne HTTP-Verbindung zwischen dem Gateway und IoT Hub her. HTTP ist eines der drei Protokolle, die von IoT Hub gelesen werden können. Dieses Modul fasst die Verbindungen aller Geräte mittels Multiplexing zu einer einzelnen Verbindung zusammen, sodass Sie nicht für jedes Gerät eine Verbindung herstellen müssen. Dank dieses Konzepts können zahlreiche Geräte über ein einzelnes Gateway verbunden werden. 

In dem Beispiel gilt für das Modul Folgendes:

1. Es akzeptiert Nachrichten mit einer Eigenschaft mit einem IoT Hub-Geräteschlüssel, die vom vorhergehenden Modul zugewiesen wurde. 
1. Es sendet den Nachrichteninhalt unter Verwendung des HTTP-Protokolls an IoT Hub. 

Das IoT Hub-Kommunikationsmodul ist **iothub.c** im Quellcode.

## <a name="before-you-get-started"></a>Bevor Sie beginnen

Bevor Sie beginnen, müssen Sie Folgendes ausführen:

* [Erstellen Sie eine IoT Hub-Instanz][lnk-create-hub] in Ihrem Azure-Abonnement. Für diese exemplarische Vorgehensweise benötigen Sie den Namen Ihres Hubs. Wenn Sie kein Konto besitzen, können Sie in nur wenigen Minuten ein [kostenloses Konto][lnk-free-trial] erstellen.
* Fügen Sie Ihrer IoT Hub-Instanz zwei Geräte hinzu, und notieren Sie die IDs und Geräteschlüssel. Sie können das Tool [Device Explorer][lnk-device-explorer] oder [iothub-explorer][lnk-iothub-explorer] verwenden, um der IoT Hub-Instanz Geräte hinzuzufügen und die entsprechenden Schlüssel abzurufen.


<!-- Images -->
[1]: media/iot-hub-iot-edge-simulated-selector/image1.png
[2]: media/iot-hub-iot-edge-simulated-selector/image2.png

<!-- Links -->
[Beispiels für Clouduploads von simulierten Geräten]: https://github.com/Azure/iot-edge/blob/master/samples/simulated_device_cloud_upload/README.md
[lnk-sdk]: https://github.com/Azure/iot-edge
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
[lnk-create-hub]: ../articles/iot-hub/iot-hub-create-through-portal.md