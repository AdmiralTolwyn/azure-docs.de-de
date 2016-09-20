<properties
	pageTitle="Verwenden des Azure IoT-Geräte-SDK für C | Microsoft Azure"
	description="Erfahren Sie mehr über den Beispielcode im Azure IoT-Geräte-SDK für C sowie über die ersten Schritte."
	services="iot-hub"
	documentationCenter=""
	authors="olivierbloch"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/06/2016"
     ms.author="obloch"/>

# Einführung in das Azure IoT-Geräte-SDK für C

Das **Azure IoT-Geräte-SDK** ist ein Satz von Bibliotheken, die konzipiert wurden, um den Versand von Ereignissen und den Empfang von Nachrichten vom **Azure IoT Hub**-Dienst zu vereinfachen. Es gibt verschiedene Varianten des SDK für die unterschiedlichen Plattformen. In diesem Artikel wird aber das **Azure IoT-Geräte-SDK für C** beschrieben.

Das Azure IoT-Geräte-SDK für C wurde für optimale Portabilität in ANSI C (C99) geschrieben. Dies vereinfacht den Einsatz auf einer Reihe von Plattformen und Geräten – vor allem, wenn es wichtig ist, die Festplatten- und Speichergröße zu reduzieren.

Es gibt eine Vielzahl von Plattformen, auf denen das SDK getestet wurde. (Einzelheiten finden Sie unter [Betriebssystemplattformen und Hardwarekompatibilität mit Geräte-SDKs](iot-hub-tested-configurations.md).) Denken Sie daran, dass der in diesem Artikel beschriebene Code für alle unterstützten Plattformen identisch ist, obwohl in diesem Artikel der Beispielcode für die Windows-Plattform verwendet wird.

In diesem Artikel erhalten Sie einen Einblick in die Architektur des Azure IoT-Geräte-SDK für C. Wir zeigen, wie Sie die Gerätebibliothek initialisieren, Ereignisse an IoT Hub senden und vom IoT Hub Nachrichten empfangen können. Die Informationen in diesem Artikel sollten ausreichen, um mit der Arbeit mit dem SDK zu beginnen. Zugleich erhalten Sie Hinweise darauf, wo Sie weitere Informationen zu den Bibliotheken finden können.

>> [AZURE.NOTE] Dieser Artikel enthält keine Informationen zur Verwendung der *Geräteverwaltungsfunktionen* der C-Bibliotheken im SDK. Informationen zur Verwendung der Geräteverwaltungsfunktionen finden Sie unter [Einführung in die Azure IoT Hub-Geräteverwaltungsbibliothek für C](iot-hub-device-management-library.md).

## SDK-Architektur

Das **Azure IoT-Geräte-SDK für C** finden Sie im GitHub-Repository mit [Microsoft Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks). Ausführliche Informationen zur API finden Sie in der [C-API-Referenz](http://azure.github.io/azure-iot-sdks/c/api_reference/index.html).

Die aktuelle Version der Bibliotheken finden Sie im **master**-Verzeichnis dieses Repository:

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

Dieses Repository enthält die gesamte Familie der Azure IoT-Geräte-SDKs. In diesem Artikel geht es jedoch um das Azure IoT-Geräte-SDK für *C*, das sich im Ordner **c** befindet.

  ![](media/iot-hub-device-sdk-c-intro/02-CFolder.PNG)

* Die Hauptimplementierung des SDKs befindet sich im Ordner **iothub\_client**, der die Implementierung der niedrigsten API-Ebene im SDK (die Bibliothek **IoTHubClient**) enthält. Die Bibliothek **IoTHubClient** enthält APIs zum Implementieren des Messagings zum Senden von Nachrichten an IoT Hub sowie zum Empfangen von Nachrichten von IoT Hub. Wenn Sie diese Bibliothek verwenden, müssen Sie die Nachrichtenserialisierung implementieren (wozu das nachstehend beschriebene Serialisierungsbeispielprogramm verwendet wird). Andere Details zur Kommunikation mit IoT Hub werden jedoch für Sie übernommen.
* Der Ordner **serializer** enthält Hilfsfunktionen und Beispiele, die zeigen, wie Daten vor dem Senden an Azure IoT Hub mithilfe der Clientbibliothek serialisiert werden. Beachten Sie, dass die Verwendung des Serialisierungsprogramms nicht obligatorisch ist und nur zur Veranschaulichung erläutert wird. Wenn Sie die Bibliothek des **Serialisierungsprogramms** verwenden, beginnen Sie zunächst mit der Definition eines Modells, das sowohl die Ereignisse, die Sie an IoT Hub senden möchten, als auch die Nachrichten angibt, die Sie von IoT Hub empfangen möchten. Sobald das Modell definiert ist, bietet das SDK Ihnen eine API-Oberfläche, mit der Sie problemlos mit Ereignissen und Nachrichten arbeiten können, ohne sich um Serialisierungsdetails Gedanken machen zu müssen. Die Bibliothek hängt von anderen Open-Source-Bibliotheken ab, die den Transport mithilfe mehrerer Protokolle (AMQP, MQTT) implementieren.
* Die Bibliothek **IoTHubClient** hängt von anderen Open-Source-Bibliotheken ab:
   * Der Bibliothek [Azure C shared utility](https://github.com/Azure/azure-c-shared-utility), die allgemeine Funktionen für grundlegende Aufgaben (etwa Zeichenfolgen- und Listenbearbeitung, E/A usw.) bereitstellt, die von mehreren Azure-bezogenen SDKs für C benötigt werden.
   * Der Bibliothek [Azure uAMQP](https://github.com/Azure/azure-uamqp-c), einer clientseitigen Implementierung von AMQP, die für Geräte mit eingeschränkten Ressourcen optimiert ist.
   * Der Bibliothek [Azure uMQTT](https://github.com/Azure/azure-umqtt-c), einer allgemeinen Bibliothek, die das MQTT-Protokoll implementiert und für Geräte mit eingeschränkten Ressourcen optimiert ist.

All dies ist einfacher anhand von Codebeispielen zu verstehen. Die folgenden Abschnitte enthalten eine Reihe von Beispielanwendungen, die im SDK enthalten sind. Dies sollte Ihnen eine gute Vorstellung von den verschiedenen Funktionen der Architekturebenen des SDK sowie einen Überblick über die Funktionsweise der APIs geben.

## Vor dem Ausführen der Beispiele

Vor dem Ausführen der Beispiele im Azure IoT-Geräte-SDK für C können müssen Sie eine Instanz des Diensts in Ihrem Azure-Abonnement erstellen, falls noch nicht geschehen, und zwei Aufgaben ausführen:
* Vorbereiten Ihrer Entwicklungsumgebung
* Abrufen von Geräteanmeldeinformationen

Wenn Sie eine Instanz von Azure IoT Hub in Ihrem Azure-Abonnement erstellen möchten, befolgen Sie [diese Anweisungen](https://github.com/Azure/azure-iot-sdks/blob/master/doc/setup_iothub.md).

Die [Infodatei](https://github.com/Azure/azure-iot-sdks/tree/master/c) im SDK enthält Anweisungen zum Vorbereiten der Entwicklungsumgebung und Abrufen von Geräteanmeldeinformationen. Die folgenden Abschnitte enthalten einige zusätzliche Kommentare zu diesen Anweisungen.

### Vorbereiten der Entwicklungsumgebung

Wenngleich für einige Plattformen Pakete bereitgestellt werden (z. B. NuGet für Windows oder apt\_get für Debian und Ubuntu) und in den Beispielen diese Pakete, sofern verfügbar, verwendet werden, wird in den nachstehenden Anweisungen erläutert, wie die Bibliothek und Beispiele direkt anhand des Codes erstellt werden.

Zunächst müssen Sie eine Kopie des SDK von GitHub abrufen und dann die Quelle erstellen. Rufen Sie eine Kopie der Quelle aus dem Verzeichnis **master** des [GitHub-Repositorys](https://github.com/Azure/azure-iot-sdks) ab.

Führen Sie nach dem Herunterladen der Quelle die im SDK-Artikel [Prepare your development environment](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md) (Vorbereiten Ihrer Entwicklungsumgebung) beschriebenen Schritte aus.


Im Folgenden sind einige Tipps angegeben, die Ihnen dabei helfen, das in der Vorbereitungsanleitung beschriebene Verfahren durchzuführen:

-   Wählen Sie bei der Installation des **CMake**-Hilfsprogramms die Option aus, mit der Sie **CMake** dem Systempfad für **alle Benutzer** hinzufügen (Sie können auch den **aktuellen Benutzer** hinzufügen):

  ![](media/iot-hub-device-sdk-c-intro/08-CMake.PNG)


-   Installieren Sie die Git-Befehlszeilentools, bevor Sie die **Developer-Eingabeaufforderung für VS2015** öffnen. Führen Sie folgende Schritte aus, um diese Tools zu installieren:

	1. Starten Sie das **Visual Studio 2015**-Setupprogramm (oder wählen Sie **Microsoft Visual Studio 2015** in der Systemsteuerung unter **Programme und Funktionen** aus, und wählen Sie dann **Ändern** aus).
	
	2. Vergewissern Sie sich, dass das Feature **Git für Windows** im Installationsprogramm ausgewählt ist. Aktivieren Sie außerdem die Option **GitHub-Erweiterung für Visual Studio**, um die IDE-Integration bereitzustellen:

  		![](media/iot-hub-device-sdk-c-intro/10-GitTools.PNG)

	3. Beenden Sie den Assistenten, um die Tools zu installieren.

	4. Fügen Sie das Git-Toolsverzeichnis **bin** zur **PATH**-Systemumgebungsvariable hinzu. Unter Windows sieht das Ergebnis wie folgt aus:

  		![](media/iot-hub-device-sdk-c-intro/11-GitToolsPath.PNG)


Wenn Sie alle auf der Seite [Prepare your development environment](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md) (Vorbereiten Ihrer Entwicklungsumgebung) beschriebenen Schritte abgeschlossen haben, können Sie die Beispielanwendungen kompilieren.

### Abrufen von Geräteanmeldeinformationen

Nachdem die Umgebung eingerichtet ist, müssen Sie als Nächstes verschiedene Geräteanmeldeinformationen abrufen. Damit ein Gerät auf einen IoT Hub zugreifen kann, müssen Sie das Gerät zuerst der IoT Hub-Geräteregistrierung hinzufügen. Wenn Sie Ihr Gerät hinzufügen, erhalten Sie einen Satz von Geräteanmeldeinformationen, die Sie benötigen, damit das Gerät eine Verbindung zu einem IoT Hub herstellen kann. Die Beispielanwendungen im nächsten Abschnitt erwarten diese Anmeldeinformationen in Form einer **Geräteverbindungszeichenfolge**.

Im SDK im Open-Source-Repository gibt es verschiedene Tools zum Unterstützen der Verwaltung des IoT Hub. Das eine ist eine Windows-Anwendung namens Device Explorer, das zweite ein auf node.js basierendes plattformübergreifendes CLI-Tool namens iothub-explorer. Weitere Informationen zu diesen Tools finden Sie [hier](https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md).

Beim Durchlaufen der Beispiele für Windows in diesem Artikel verwenden wir Device Explorer. Sie können aber auch iothub-explorer verwenden, wenn Sie CLI-Tools bevorzugen.

Das Tool [Device Explorer](https://github.com/Azure/azure-iot-sdks/tree/master/tools/DeviceExplorer) führt mithilfe der Azure IoT-Dienstbibliotheken verschiedene Aufgaben in IoT Hub aus – etwa das Hinzufügen von Geräten. Wenn Sie den Geräte-Explorer zum Hinzufügen eines Geräts verwenden, erhalten Sie eine entsprechende Verbindungszeichenfolge. Sie benötigen diese Verbindungszeichenfolge, um die Beispielanwendungen auszuführen.

Wenn Sie noch nicht bereits mit dem Prozess vertraut sind, beschreibt das folgende Verfahren, wie Sie den Geräte-Explorer verwenden können, um ein Gerät hinzuzufügen und eine Geräteverbindungszeichenfolge abzurufen.

Einen Windows Installer für Device Explorer finden Sie auf der [Seite für SDK-Veröffentlichungen](https://github.com/Azure/azure-iot-sdks/releases). Sie können das Tool jedoch auch direkt über den Code starten, indem Sie **[DeviceExplorer.sln](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/DeviceExplorer.sln)** in **Visual Studio 2015** öffnen und die Projektmappe erstellen.

Wenn Sie das Programm ausführen, wird diese Schnittstelle angezeigt:

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

Geben Sie Ihre IoT Hub-Verbindungszeichenfolge in das erste Feld ein, und klicken Sie auf **Aktualisieren**. Dadurch wird das Tool konfiguriert, sodass es mit IoT Hub kommunizieren kann.

Klicken Sie nach dem Konfigurieren der IoT Hub-Verbindungszeichenfolge auf die Registerkarte **Verwaltung**:

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

Dort verwalten Sie die in Ihrem IoT Hub registrierten Geräte.

Sie können ein Gerät erstellen, indem Sie auf die Schaltfläche **Erstellen** klicken. Es wird ein Dialogfeld angezeigt, in dem bereits mehrere Schlüssel (primär und sekundär) angegeben sind. Sie müssen lediglich eine **Geräte-ID** eingeben und dann auf **Erstellen** klicken.

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

Nach der Erstellung des Geräts wird die Geräteliste mit allen registrierten Geräten aktualisiert, einschließlich des soeben erstellten Geräts. Wenn Sie mit der rechten Maustaste auf das neue Gerät klicken, wird das folgende Menü angezeigt:

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

Bei Verwendung der Option **Copy connection string for selected device** (Verbindungszeichenfolge für das ausgewählte Gerät kopieren) wird die Verbindungszeichenfolge für das Gerät in die Zwischenablage kopiert. Behalten Sie eine Kopie der Verbindungszeichenfolge bei. Sie benötigen sie beim Ausführen der in den nächsten Abschnitten beschriebenen Beispielanwendungen.

Wenn Sie die oben genannten Schritte abgeschlossen haben, können Sie mit der Ausführung des Codes beginnen. Beide Beispiele weisen am Anfang der Hauptquelldatei eine Konstante auf, mit der Sie eine Verbindungszeichenfolge eingeben können. Die entsprechende Zeile aus der Anwendung **iothub\_client\_sample\_amqp** wird beispielsweise wie folgt angezeigt:

```
static const char* connectionString = "[device connection string]";
```

Wenn Sie der unten stehenden Beschreibung folgen möchten, geben Sie hier Ihre Geräteverbindungszeichenfolge ein und kompilieren die Projektmappe neu. Danach können Sie das Beispiel ausführen.

## IoTHubClient

Innerhalb des Ordners **iothub\_client** im Repository „azure-iot-sdks“ befindet sich der Ordner **samples** mit einer Anwendung namens **iothub\_client\_sample\_amqp**.

Die Windows-Version der **iothub\_client\_sample\_ampq**-Anwendung umfasst die folgende Visual Studio-Projektmappe:

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-amqp.PNG)

Die Lösung umfasst ein einziges Projekt. Beachten Sie, dass in dieser Projektmappe vier NuGet-Pakete installiert sind:

- Microsoft.Azure.C.SharedUtility
- Microsoft.Azure.IoTHub.AmqpTransport
- Microsoft.Azure.IoTHub.IoTHubClient
- Microsoft.Azure.uamqp

Zur Verwendung des SDKs benötigen Sie immer das Paket **Microsoft.Azure.C.SharedUtility**. Da dieses Beispiel auf AMQP beruht, müssen Sie auch die Pakete **Apache.uamqp** und **Microsoft.Azure.IoTHub.AmqpTransport** einschließen (für HTTP und MQTT sind entsprechende Pakete vorhanden). Da im Beispiel die Bibliothek **IoTHubClient** verwendet wird, müssen Sie auch das Paket **Microsoft.Azure.IoTHub.IoTHubClient** in die Projektmappe einschließen.

Die Implementierung für die Beispielanwendung befindet sich in der Quelldatei **iothub\_client\_sample\_amqp.c**.

Anhand dieser Beispielanwendung zeigen wir Ihnen Schritt für Schritt, was für die Nutzung der **IoTHubClient**-Bibliothek erforderlich ist.

### Initialisieren der Bibliothek

> [AZURE.NOTE] Bevor Sie beginnen, mit den Bibliotheken zu arbeiten, müssen sie möglicherweise plattformspezifische Initialisierungen durchführen. Wenn Sie z.B. planen, AMQPS unter Linux zu verwenden, müssen Sie die OpenSSL-Bibliothek initialisieren. Die Beispiele im [GitHub-Repository](https://github.com/Azure/azure-iot-sdks) rufen die Hilfsfunktion **Platform\_init** auf, wenn der Client startet, und die Funktion **Platform\_deinit**, bevor er beendet wird. Diese Funktionen sind in der Headerdatei „platform.h“ deklariert. Überprüfen Sie die Definitionen dieser Funktionen für Ihre Zielplattform im [Repository](https://github.com/Azure/azure-iot-sdks), um zu ermitteln, ob Sie einen Plattforminitialisierungscode in Ihren Client einschließen müssen.

Um mit der Arbeit mit den Bibliotheken zu beginnen, müssen Sie zuerst ein IoT Hub-Clienthandle erstellen:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Beachten Sie, dass eine Kopie der Geräteverbindungszeichenfolge an diese Funktion (die wir aus dem Geräte-Explorer abgerufen haben) übergeben wird. Wir legen auch das Protokoll fest, das wir verwenden möchten. In diesem Beispiel wird AMQP verwendet, doch MQTT und HTTP sind auch möglich.

Wenn Sie ein gültiges **IOTHUB\_CLIENT\_HANDLE** erstellt haben, können Sie damit beginnen, die APIs abzurufen, um Ereignisse an IoT Hub zu senden und Nachrichten von IoT Hub zu empfangen. Lassen Sie uns dies als Nächstes näher betrachten.

### Senden von Ereignissen

Für das Senden von Ereignissen an IoT Hub müssen Sie die folgenden Schritte ausführen:

Erstellen Sie zunächst eine Nachricht:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_Over_AMQP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText);
```

Senden Sie dann die Nachricht

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

Jedes Mal, wenn Sie eine Nachricht senden, geben Sie einen Verweis auf eine Rückruffunktion an, die aufgerufen wird, wenn die Daten gesendet werden:

```
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %d with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Beachten Sie den Aufruf der **IoTHubMessage\_Destroy**-Funktion, wenn Sie mit der Nachricht fertig sind. Sie müssen diesen Aufruf durchführen, um die bei der Erstellung der Nachricht zugeordneten Ressourcen freizugeben.

### Empfangen von Nachrichten

Der Empfang einer Nachricht ist ein asynchroner Vorgang. Zuerst registrieren Sie einen Rückruf, der aufgerufen wird, wenn das Gerät eine Nachricht empfängt:

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

Der letzte Parameter ist ein ungültiger Zeiger. Im Beispiel handelt es sich um einen Zeiger auf einen integer-Wert. Es könnte sich jedoch auch um einen Zeiger auf eine komplexere Datenstruktur handeln. Dies erlaubt es der Rückruffunktion, auf einem gemeinsamen Zustand mit dem Aufrufer dieser Funktion zu arbeiten.

Wenn das Gerät eine Nachricht empfängt, wird die registrierte Rückruffunktion aufgerufen:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) == IOTHUB_MESSAGE_OK)
    {
        (void)printf("Received Message [%d] with Data: <<<%.*s>>> & Size=%d\r\n", *counter, (int)size, buffer, (int)size);
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Beachten Sie, dass die **IoTHubMessage\_GetByteArray**-Funktion zum Abrufen der Nachricht verwendet wird. In diesem Beispiel handelt es sich dabei um eine Zeichenfolge.

### Aufheben der Initialisierung der Bibliothek

Wenn Sie mit dem Senden von Ereignissen und Empfangen von Nachrichten fertig sind, können Sie die Initialisierung der IoT-Bibliothek aufheben. Geben Sie hierzu den folgenden Funktionsaufruf aus:

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Dadurch werden die zuvor von der **IoTHubClient\_CreateFromConnectionString**-Funktion zugeordneten Ressourcen freigegeben.

Wie Sie sehen, ist es ganz einfach, mit der **IoTHubClient**-Bibliothek Ereignisse zu senden und Nachrichten zu empfangen. Die Bibliothek kümmert sich um die Details für die Kommunikation mit IoT Hub, z. B. die Auswahl des Protokolls (aus Sicht des Entwicklers ist dies eine einfache Konfigurationsoption).

Mit der Bibliothek **IoTHubClient** lässt sich präzise steuern, wie die Ereignisse serialisiert werden sollen, die Ihr Gerät an IoT Hub sendet. In einigen Fällen ist dies von Vorteil, aber in anderen Fällen ist dies ein Implementierungsdetail, um das Sie sich keine Gedanken machen möchten. In diesem Fall empfiehlt sich unter Umständen der Einsatz der Bibliothek des **Serialisierungsprogramms**. Eine entsprechende Beschreibung folgt im nächsten Abschnitt.

## Serialisierungsprogramm

Im Prinzip basiert die Bibliothek des **Serialisierungsprogramms** auf der **IoTHubClient**-Bibliothek im SDK. Sie nutzt die **IoTHubClient**-Bibliothek für die zugrunde liegende Kommunikation mit IoT Hub, ergänzt sie jedoch durch Modellierungsfunktionen, die die Nachrichtenserialisierung für den Entwickler übernehmen. Die Funktionsweise der Bibliothek wird am besten anhand eines Beispiels verdeutlicht.

Innerhalb des **serializer**-Ordners im azure-iot-sdks-Repository gibt es einen **samples**-Ordner mit einer Anwendung namens **simplesample\_amqp**. Die Windows-Version dieses Beispiels umfasst die folgende Visual Studio-Projektmappe:

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_amqp.PNG)

Genau wie im vorherigen Beispiel sind hier verschiedene NuGet-Pakete enthalten:

- Microsoft.Azure.C.SharedUtility
- Microsoft.Azure.IoTHub.AmqpTransport
- Microsoft.Azure.IoTHub.IoTHubClient
- Microsoft.Azure.IoTHub.Serializer
- Microsoft.Azure.uamqp

Die meisten haben wir schon im vorherigen Beispiel gesehen, aber **Microsoft.Azure.IoTHub.Serializer** ist neu. Dieses Paket ist erforderlich, wenn wir die Bibliothek des **Serialisierungsprogramms** verwenden.

Die Implementierung für die Beispielanwendung befindet sich in der Datei **simplesample\_amqp.c**.

Die folgenden Abschnitte führen Sie durch die wichtigsten Teile dieses Beispiels.

### Initialisieren der Bibliothek

Für die Arbeit mit der Bibliothek des **Serialisierungsprogramms** müssen Sie die Initialisierungs-APIs aufrufen:

```
serializer_init(NULL);

IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);

ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
```

Der Aufruf der **serializer\_init**-Funktion ist ein einmaliger Aufruf und dient zum Initialisieren der zugrunde liegenden Bibliothek. Anschließend rufen Sie die **IoTHubClient\_CreateFromConnectionString**-Funktion auf. Hierbei handelt es sich um die gleiche API wie im **IoTHubClient**-Beispiel. Mit diesem Aufruf wird die Geräteverbindungszeichenfolge festgelegt. (Hier wählen Sie auch das gewünschte Protokoll aus.) Beachten Sie, dass in diesem Beispiel AMQP als Transportprotokoll verwendet wird. Die Verwendung von HTTP ist auch möglich.

Rufen Sie abschließend die **CREATE\_MODEL\_INSTANCE**-Funktion auf. Beachten Sie, dass **WeatherStation** der Namespace des Modells und **ContosoAnemometer** der Name des Modells ist. Sobald die Modellinstanz erstellt wurde, können Sie damit beginnen, Ereignisse zu senden und Nachrichten zu empfangen. Zunächst ist es aber wichtig zu verstehen, was ein Modell ist.

### Definieren des Modells

Ein Modell in der Bibliothek des **Serialisierungsprogramms** definiert die Ereignisse, die das Gerät an IoT Hub senden kann, sowie die Nachrichten – in der Modellierungssprache so genannte *Aktionen* –, die es empfangen kann. Sie definieren ein Modell mit einem Satz von C-Makros, wie in der **simplesample\_amqp**-Beispielanwendung gezeigt:

```
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Die beiden Makros **BEGIN\_NAMESPACE** und **END\_NAMESPACE** verwenden den Namespace des Modells als Argument. Es wird erwartet, dass alles zwischen diesen Makros die Definition der Modelle und der Datenstrukturen ist, die die Modelle verwenden.

In diesem Beispiel gibt es ein einzelnes Modell namens **ContosoAnemometer**. Dieses Modell definiert zwei Ereignisse, die Ihr Gerät an IoT Hub senden kann: **DeviceId** und **WindSpeed**. Darüber hinaus werden drei Aktionen (Nachrichten) definiert, die Ihr Gerät empfangen kann: **TurnFanOn**, **TurnFanOff** und **SetAirResistance**. Jedes Ereignis hat einen Typ, und jede Aktion verfügt über einen Namen (und optional einen Satz von Parametern).

Die im Modell definierten Ereignisse und Aktionen definieren eine API-Oberfläche, über die Sie Ereignisse an IoT Hub senden und auf Nachrichten antworten können, die an das Gerät gesendet werden. Dies wird am besten anhand eines Beispiels verdeutlicht.

### Senden von Ereignissen

Das Modell definiert die Ereignisse, die Sie an IoT Hub senden können. In diesem Beispiel ist dies eines der beiden mit dem Makro **WITH\_DATA** definierten Ereignisse. Wenn Sie beispielsweise ein **WindSpeed**-Ereignis an einen IoT Hub senden möchten, sind dazu einige Schritte notwendig. Zunächst müssen Sie die Daten festlegen, die gesendet werden sollen:

```
myWeather->WindSpeed = 15;
```

Dank des zuvor definierten Modells müssen Sie dazu nur einen Member einer **Struktur** festlegen. Als Nächstes serialisieren Sie das Ereignis, das Sie senden möchten:

```
unsigned char* destination;
size_t destinationSize;

SERIALIZE(&destination, &destinationSize, myWeather->WindSpeed);
```

Mit diesem Code wird das Ereignis für einen Puffer serialisiert (Verweis mittels **destination**). Abschließend senden Sie das Ereignis mit folgendem Code an IoT Hub:

```
sendMessage(iotHubClientHandle, destination, destinationSize);
```

Dies ist eine Hilfsfunktion der Beispielanwendung, mit der die serialisierten Ereignisse an IoT Hub gesendet werden:

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
    messageTrackingId++;
}
```

Dieser Code ist dem der Anwendung **iothub\_client\_sample\_amqp** sehr ähnlich. Darin haben wir eine Nachricht aus einem Bytearray erstellt und diese dann mit **IoTHubClient\_SendEventAsync** an IoT Hub gesendet. Danach müssen wir einfach das Nachrichtenhandle und den zuvor zugeordneten serialisierten Datenpuffer freigeben.

Der vorletzte Parameter von **IoTHubClient\_SendEventAsync** ist ein Verweis auf eine Rückruffunktion, die aufgerufen wird, wenn die Daten erfolgreich gesendet wurden. Hier ist ein Beispiel für eine Rückruffunktion angegeben:

```
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    int messageTrackingId = (intptr_t)userContextCallback;

    (void)printf("Message Id: %d Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

Der zweite Parameter ist ein Zeiger auf den Benutzerkontext. Dabei handelt es sich um den gleichen Zeiger, den wir an **IoTHubClient\_SendEventAsync** übergeben haben. In diesem Fall ist der Kontext ein einfacher Zähler. Es kann sich aber um jeden beliebigen Kontext handeln.

So einfach ist das Senden von Ereignissen. Jetzt muss nur noch der Empfang von Nachrichten erläutert werden.

### Empfangen von Nachrichten

Der Empfang von Nachrichten funktioniert ähnlich wie die Verarbeitung von Nachrichten in der **IoTHubClient**-Bibliothek. Zunächst registrieren Sie eine Nachrichtenrückruffunktion:

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Anschließend schreiben Sie die Rückruffunktion, die beim Empfang einer Nachricht aufgerufen wird:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Dies ist ein Codebaustein, der für jede Projektmappe identisch ist. Diese Funktion empfängt die Nachricht und übernimmt mithilfe des Aufrufs von **EXECUTE\_COMMAND** die Weiterleitung an die entsprechende Funktion. Welche Funktion zu diesem Zeitpunkt aufgerufen wird, hängt von der Definition der Aktionen in unserem Modell ab.

Wenn Sie eine Aktion in Ihrem Modell definieren, müssen Sie eine entsprechende Funktion implementieren, die aufgerufen wird, wenn das Gerät eine Nachricht empfängt. Ein Beispiel – Ihr Modell definiert folgende Aktion:

```
WITH_ACTION(SetAirResistance, int, Position)
```

In diesem Fall müssen Sie eine Funktion mit folgender Signatur definieren:

```
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Beachten Sie, dass der Name der Funktion dem Namen der Aktion im Modell entspricht und die Parameter der Funktion mit den für die Aktion angegebenen Parametern übereinstimmen. Der erste Parameter ist immer erforderlich und enthält einen Zeiger auf die Instanz unseres Modells.

Wenn das Gerät eine Nachricht empfängt, die dieser Signatur entspricht, wird die entsprechende Funktion aufgerufen. Abgesehen davon, dass Sie die Codebausteine aus **IoTHubMessage** angeben müssen, muss für den Empfang von Nachrichten nur eine einfache Funktion für jede im Modell definierte Aktion definiert werden.

### Aufheben der Initialisierung der Bibliothek

Wenn Sie mit dem Senden von Daten und Empfangen von Nachrichten fertig sind, können Sie die Initialisierung der IoT-Bibliothek aufheben:

```
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Jede dieser drei Funktionen ist auf die drei zuvor beschriebenen Initialisierungsfunktionen abgestimmt. Durch Aufrufen dieser APIs können Sie sicherstellen, dass Sie zuvor zugeordnete Ressourcen freigeben.

## Nächste Schritte

In diesem Artikel wurden die Grundlagen zur Verwendung von Bibliotheken im **Azure IoT-Geräte-SDK für C** beschrieben. Nach dem Lesen dieses Artikels sollten Sie genügend Informationen haben, um zu wissen, was im SDK enthalten ist, und die Architektur zu verstehen. Dank der Windows-Beispiele können Sie direkt mit der Arbeit beginnen. Im nächsten Artikel wird das SDK näher vorgestellt. Sie erhalten [weitere Informationen zur IoTHubClient-Bibliothek](iot-hub-device-sdk-c-iothubclient.md).

Informationen zur Verwendung der Geräteverwaltungsfunktionen im **Azure IoT-Geräte-SDK für C** finden Sie unter [Einführung in die Clientbibliothek der Azure IoT Hub-Geräteverwaltung](iot-hub-device-management-library.md).

Weitere Informationen zum Entwickeln für IoT Hub finden Sie im Artikel zu den [IoT Hub SDKs][lnk-sdks].

Weitere Informationen zu den Funktionen von IoT Hub finden Sie unter:

- [Entwerfen Ihrer Lösung][lnk-design]
- [Erkunden der Geräteverwaltung mithilfe der Beispielbenutzeroberfläche][lnk-dmui]
- [Simulieren eines Geräts mit dem Gateway SDK][lnk-gateway]
- [Verwenden des Azure-Portals zur Verwaltung von IoT Hub][lnk-portal]

[lnk-file upload]: iot-hub-csharp-csharp-file-upload.md
[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-sdks-summary.md

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0907_2016-->