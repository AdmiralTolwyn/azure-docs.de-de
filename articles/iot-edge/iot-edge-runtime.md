---
title: Verwalten von Geräten mithilfe der Runtime –Azure IoT Edge | Microsoft-Dokumentation
description: Erfahren Sie, wie die Module, Sicherheit, Kommunikation und Berichterstellung auf Ihren Geräten von der Azure IoT Edge-Runtime verwaltet werden.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: bb2df9c32d5adc8160da82148e4a66a4ab68d182
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311598"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Grundlegendes zur Azure IoT Edge-Runtime und ihrer Architektur

Die IoT-Edge-Laufzeit ist eine Sammlung von Programmen, die auf einem Gerät installiert sein müssen, damit es als IoT Edge-Gerät gilt. Die Sammlung der Komponenten der IoT Edge-Laufzeit ermöglicht IoT Edge-Geräten den Empfang von Code für die Ausführung im Edgebereich und das Kommunizieren der Ergebnisse. 

Die IoT Edge-Laufzeit führt auf IoT Edge-Geräten die folgenden Funktionen aus:

* Installieren und Aktualisieren von Workloads auf dem Gerät
* Aufrechterhalten von Azure IoT Edge-Sicherheitsstandards auf dem Gerät
* Sicherstellen, dass die [IoT Edge-Module](iot-edge-modules.md) immer ausgeführt werden
* Melden der Modulintegrität an die Cloud für die Remoteüberwachung
* Ermöglichen der Kommunikation zwischen nachgeschalteten Blattknotengeräten und IoT Edge-Geräten
* Ermöglichen der Kommunikation zwischen Modulen auf dem IoT Edge-Gerät
* Ermöglichen der Kommunikation zwischen dem IoT Edge-Gerät und der Cloud

![Die IoT Edge-Runtime übermittelt an IoT Hub Einblicke und Informationen zur Modulintegrität](./media/iot-edge-runtime/Pipeline.png)

Die Aufgaben der IoT Edge-Laufzeit fallen in zwei Kategorien: Kommunikation und Modulverwaltung. Diese beiden Rollen werden von zwei Komponenten ausgeführt, aus denen die IoT Edge-Laufzeit besteht. Der *IoT Edge-Hub* ist für die Kommunikation verantwortlich, während der *IoT Edge-Agent* die Module bereitstellt und überwacht. 

Der IoT Edge-Hub und der IoT Edge-Agent sind Module wie jedes andere auf einem IoT Edge-Gerät ausgeführte Modul. 

## <a name="iot-edge-hub"></a>IoT Edge-Hub

Der IoT Edge-Hub ist eines der beiden Module, aus denen die Azure IoT Edge-Runtime besteht. Er fungiert als lokaler Proxy für IoT Hub, indem er die gleichen Protokollendpunkte wie IoT Hub verfügbar macht. Diese Konsistenz bedeutet, dass Clients (ob Geräte oder Module) auf die gleiche Weise eine Verbindung mit der IoT Edge-Laufzeit wie mit IoT Hub herstellen können. 

>[!NOTE]
> Der IoT Edge-Hub unterstützt Clients, die sich über MQTT oder AMQP verbinden. Clients, die HTTP verwenden, werden jedoch nicht unterstützt. 

Der IoT Edge-Hub ist keine vollständige Version des lokal ausgeführten IoT Hub. Einige Aufgaben werden vom IoT Edge-Hub automatisch an IoT Hub delegiert. Beispielsweise leitet der IoT Edge-Hub Authentifizierungsanforderungen an IoT Hub weiter, wenn ein Gerät zum ersten Mal eine Verbindung herzustellen versucht. Nachdem die erste Verbindung hergestellt wurde, werden die Sicherheitsinformationen vom IoT Edge-Hub lokal zwischengespeichert. Nachfolgende Verbindungen von diesem Gerät sind zulässig, ohne dass eine Authentifizierung bei der Cloud erforderlich ist. 

>[!NOTE]
>Die Laufzeit muss immer verbunden sein, wenn sie versucht, ein Gerät zu authentifizieren.

Der IoT Edge-Hub optimiert die Anzahl der tatsächlich hergestellten Verbindungen mit der Cloud, um die von der IoT Edge-Lösung genutzte Bandbreite zu reduzieren. Der IoT Edge-Hub fasst logische Verbindungen von Clients, z.B. Modulen oder Blattknotengeräten, zu einer einzelnen physischen Verbindung mit der Cloud zusammen. Die Details dieses Vorgangs sind für den Rest der Lösung transparent. Clients können nicht erkennen, dass statt einer eigenen Verbindung mit der Cloud für alle Clients eine gemeinsame Verbindung verwendet wird. 

![Der IoT Edge-Hub ist ein Gateway zwischen physischen Geräten und IoT Hub](./media/iot-edge-runtime/Gateway.png)

Der IoT Edge-Hub kann ermitteln, ob er mit IoT Hub verbunden ist. Wenn die Verbindung unterbrochen wird, speichert der IoT Edge-Hub Nachrichten oder Zwillingsaktualisierungen lokal. Sobald eine Verbindung wieder hergestellt wurde, werden alle Daten synchronisiert. Der Speicherort für diesen temporären Cache wird durch eine Eigenschaft des Modulzwillings des IoT Edge-Hubs festgelegt. Die Größe des Caches wird nicht begrenzt, solange die Speicherkapazität des Geräts ausreicht. 

### <a name="module-communication"></a>Modulkommunikation

Der IoT Edge-Hub ermöglicht die Kommunikation zwischen Modulen. Die Verwendung des IoT Edge-Hubs als Nachrichtenbroker sorgt dafür, dass die Module voneinander unabhängig bleiben. Module müssen nur die Eingänge, an denen sie Nachrichten akzeptieren, und die Ausgänge, an die sie Nachrichten schreiben, angeben. Anschließend fügt ein Lösungsentwickler diese Eingänge und Ausgänge zusammen, damit die Module Daten in der speziellen Reihenfolge für die Lösung verarbeiten. 

![Der IoT Edge-Hub ermöglicht die Kommunikation zwischen Modulen.](./media/iot-edge-runtime/module-endpoints.png)

Ein Modul ruft zum Senden von Daten an den IoT Edge-Hub die SendEventAsync-Methode auf. Das erste Argument gibt an, an welchen Ausgang die Nachricht gesendet werden soll. Mit dem folgenden Pseudocode wird eine Nachricht an output1 gesendet:

   ```csharp
   ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

Um eine Nachricht zu empfangen, registrieren Sie einen Rückruf, der eingehende Nachrichten an einem speziellen Eingang verarbeitet. Mit dem folgenden Pseudocode wird die Funktion „messageProcessor“ registriert, die für die Verarbeitung aller an input1 empfangenen Nachrichten verwendet werden soll:

   ```csharp
   await client.SetInputMessageHandlerAsync(“input1”, messageProcessor, userContext);
   ```

Weitere Informationen zur ModuleClient-Klasse und ihre Kommunikationsmethoden finden Sie in der API-Referenz für Ihre bevorzugte SDK-Sprache: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C und Python](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable) oder [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

Der Lösungsentwickler muss die Regeln angeben, die festlegen, wie Nachrichten vom IoT Edge-Hub zwischen Modulen übergeben werden. Routingregeln werden in der Cloud definiert und an den IoT Edge-Hub in dessen Gerätezwilling übertragen. Zum Definieren von Routen zwischen Modulen in Azure IoT Edge wird die gleiche Syntax wie für IoT Hub-Routen verwendet. 

<!--- For more info on how to declare routes between modules, see []. --->   

![Das Routing zwischen Modulen erfolgt über den IoT Edge-Hub](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>IoT Edge-Agent

Der IoT Edge-Agent ist das zweite Modul, aus dem die Azure IoT Edge-Laufzeit besteht. Er ist für das Instanziieren von Modulen verantwortlich, stellt sicher, dass sie weiterhin ausgeführt werden, und meldet den Status der Module an IoT Hub. Der IoT Edge-Agent verwendet den Modulzwilling wie jedes andere Modul zum Speichern der Konfigurationsdaten. 

Der [IoT Edge-Sicherheits-Daemon](iot-edge-security-manager.md) startet den IoT Edge-Agent beim Starten des Geräts. Der Agent ruft den Modulzwilling von IoT Hub ab und überprüft das Bereitstellungsmanifest. Das Bereitstellungsmanifest ist eine JSON-Datei, die die Module deklariert, die gestartet werden müssen. 

Jedes Element im Bereitstellungsmanifest enthält spezifische Informationen zu einem Modul und wird vom IoT Edge-Agent zum Steuern des Lebenszyklus des Moduls verwendet. Einige der wichtigeren Eigenschaften lauten: 

* **settings.image:** der Container, der vom IoT Edge-Agent zum Starten des Moduls verwendet wird. Der IoT Edge-Agent muss mit den Anmeldeinformationen für die Containerregistrierung konfiguriert werden, wenn das Image mit einem Kennwort geschützt ist. Anmeldeinformationen für die Containerregistrierung können remote mit dem Bereitstellungsmanifest konfiguriert werden oder auf dem IoT Edge-Gerät selbst durch Aktualisieren der Datei `config.yaml` im IoT Edge-Programmordner.
* **settings.createOptions** – Eine Zeichenfolge, die beim Starten des Containers eines Moduls direkt an den Docker-Daemon übergeben wird. Das Hinzufügen von Docker-Optionen in dieser Eigenschaft ermöglicht erweiterte Optionen, z.B. Portweiterleitung oder das Bereitstellen von Volumes im Container eines Moduls.  
* **status:** der Status, in den der IoT Edge-Agent das Modul versetzt. Dieser Wert wird in der Regel auf *running* festgelegt, da die meisten Personen möchten, dass der IoT Edge-Agent alle Module auf dem Gerät sofort startet. Sie können jedoch festlegen, dass der Anfangsstatus eines Moduls „Beendet“ lautet, und den IoT Edge-Agent zu einem zukünftigen Zeitpunkt zum Starten eines Moduls auffordern. Der IoT Edge-Agent meldet der Cloud den Status jedes Moduls in den gemeldeten Eigenschaften. Weicht die gemeldete Eigenschaft von der gewünschten Eigenschaft ab, ist dies ein Indikator für ein fehlerhaftes Gerät. Die unterstützten Status lauten:
   * Herunterladen
   * Wird ausgeführt
   * Fehlerhaft
   * Fehler
   * Beendet
* **restartPolicy:** legt fest, wie der IoT Edge-Agent ein Modul neu startet. Mögliche Werte sind:
   * Nie: Der IoT Edge-Agent führt niemals einen Neustart des Moduls aus.
   * Bei Fehler: Wenn das Modul abstürzt, wird es vom IoT Edge-Agent neu gestartet. Wenn das Modul ordnungsgemäß heruntergefahren wird, führt der IoT Edge-Agent keinen Neustart des Moduls aus.
   * Fehlerhaft: Wenn das Modul abstürzt oder als fehlerhaft angesehen wird, startet der IoT Edge-Agent es neu.
   * Immer: Wenn das Modul abstürzt, als fehlerhaft angesehen wird oder auf irgendeine Weise heruntergefahren wird, führt der IoT Edge-Agent den Neustart des Moduls aus. 

Der IoT Edge-Agent sendet eine Runtimeantwort an IoT Hub. Im Folgenden sehen Sie eine Liste der möglichen Antworten:
  * 200 – OK
  * 400 – Die Bereitstellungskonfiguration ist falsch formatiert oder ungültig.
  * 417 – Für das Gerät ist keine Bereitstellungskonfiguration festgelegt.
  * 412 – Die Schemaversion der Bereitstellungskonfiguration ist ungültig.
  * 406 – das IoT Edge-Gerät ist offline oder sendet keine Statusberichte.
  * 500 – in der IoT Edge-Runtime ist ein Fehler aufgetreten.

### <a name="security"></a>Sicherheit

Der IoT Edge-Agent hat eine wichtige Funktion für die Sicherheit eines IoT Edge-Geräts. Er überprüft beispielsweise das Image eines Moduls, bevor er es startet. 

Weitere Informationen zum Azure IoT Edge-Sicherheitsframework finden Sie in der Dokumentation zu [IoT Edge Security Manager](iot-edge-security-manager.md).

## <a name="next-steps"></a>Nächste Schritte

[Grundlegendes zu Azure IoT Edge-Zertifikaten](iot-edge-certs.md)
