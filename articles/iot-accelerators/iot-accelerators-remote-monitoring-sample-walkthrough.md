---
title: Architektur der Remoteüberwachungslösung – Azure | Microsoft-Dokumentation
description: Exemplarische Vorgehensweise zur Architektur des Solution Accelerators für Remoteüberwachung
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: 3effde81dfa48e9544d89153d40c160ff972d047
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2018
---
# <a name="remote-monitoring-solution-accelerator-architecture"></a>Architektur des Solution Accelerators für Remoteüberwachung

Der [Solution Accelerator](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md) für Remoteüberwachung implementiert eine End-to-End-Überwachungslösung für mehrere Computer an Remotestandorten. In der Lösung sind wichtige Azure-Dienste kombiniert, um eine generische Implementierung des Geschäftsszenarios zu erzielen. Sie können sie als Ausgangspunkt für Ihre Implementierung verwenden und [anpassen](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md), um Ihre eigenen speziellen Geschäftsanforderungen zu erfüllen.

In diesem Artikel werden einige wichtige Elemente der Lösung für die Remoteüberwachung beschrieben, um die Funktionsweise zu verdeutlichen. Dieses Wissen ist für folgende Zwecke hilfreich:

* Behandeln von Problemen in der Lösung
* Planen der Lösungsanpassung zur Erfüllung besonderer Anforderungen
* Entwerfen einer eigenen IoT-Lösung mit Verwendung von Azure-Diensten

## <a name="logical-architecture"></a>Logische Architektur

Im folgenden Diagramm sind die logischen Komponenten des Solution Accelerators für Remoteüberwachung zusammen mit der [IoT-Architektur](../iot-accelerators/iot-accelerators-what-is-azure-iot.md) dargestellt:

![Logische Architektur](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Gründe für die Verwendung von Microservices

Die Cloudarchitektur wurde ständig weiterentwickelt, seit Microsoft die ersten Solution Accelerators veröffentlicht hat. [Microservices](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) haben sich als bewährte Methode herausgestellt, um Skalierbarkeit und Flexibilität zu erzielen, ohne die Entwicklungsgeschwindigkeit zu opfern. Mehrere Microsoft-Dienste nutzen dieses Architekturmuster intern mit hervorragenden Ergebnissen bei der Zuverlässigkeit und Skalierbarkeit. Mit den aktualisierten Solution Accelerators werden diese Erkenntnisse in die Praxis umgesetzt, damit auch Sie davon profitieren können.

> [!TIP]
> Weitere Informationen zu Microservice-Architekturen finden Sie unter [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (.NET-Anwendungsarchitektur) und [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microservices: Eine Anwendungsrevolution auf Cloudbasis).

## <a name="device-connectivity"></a>Gerätekonnektivität

Die Lösung enthält die folgenden Komponenten im Teil zur Gerätekonnektivität der logischen Architektur:

### <a name="simulated-devices"></a>Simulierte Geräte

Die Lösung enthält einen Microservice, mit dem Sie einen Pool mit simulierten Geräten verwalten können, um den End-to-End-Datenfluss der Lösung zu testen. Die simulierten Geräte ermöglichen Folgendes:

* Generieren der Gerät-zu-Cloud-Telemetrie (D2C)
* Reagieren auf Cloud-zu-Gerät-Methodenaufrufe (C2D) aus IoT Hub

Der Microservice stellt einen RESTful-Endpunkt für Sie bereit, über den Sie Simulationen erstellen, starten und beenden können. Jede Simulation umfasst einen Satz mit virtuellen Geräten unterschiedlichen Typs, die Telemetriedaten senden und auf Methodenaufrufe reagieren.

Sie können simulierte Geräte über das Dashboard im Lösungsportal bereitstellen.

### <a name="physical-devices"></a>Physische Geräte

Sie können für physische Geräte eine Verbindung mit der Lösung herstellen. Sie können das Verhalten Ihrer simulierten Geräte implementieren, indem Sie die Azure IoT-Geräte-SDKs verwenden.

Sie können physische Geräte über das Dashboard im Lösungsportal bereitstellen.

### <a name="iot-hub-and-the-iot-manager-microservice"></a>IoT Hub und der IoT-Manager-Microservice

Der [IoT Hub](../iot-hub/index.yml) erfasst Daten, die von den Geräten in die Cloud gesendet werden, und stellt sie für den Microservice `telemetry-agent` zur Verfügung.

Zu den Aufgaben des IoT Hub in der Lösung zählen auch:

* Verwalten einer Identitätsregistrierung zum Speichern der IDs und Authentifizierungsschlüssel aller Geräte, die Verbindungen zum Portal herstellen dürfen. Sie können Geräte über die Identitätsregistrierung aktivieren und deaktivieren.
* Aufrufen von Methoden auf Ihren Geräten im Namen des Geräteportals
* Verwalte von Gerätezwillingen für alle registrierten Geräte. Ein Gerätezwilling speichert die von einem Gerät gemeldeten Eigenschaftswerte. Ein Gerätezwilling speichert außerdem die im Lösungsportal gespeicherten gewünschten Eigenschaften, damit das Gerät diese beim nächsten Verbinden abrufen kann.
* Planen von Aufträgen, um Eigenschaften für mehrere Geräte festzulegen oder Methoden auf mehreren Geräte aufzurufen.

Die Lösung enthält den Microservice `iot-manager` zum Verarbeiten von Interaktionen mit Ihrem IoT Hub, z.B.:

* Erstellen und Verwalten von IoT-Geräten
* Verwalten von Gerätezwillingen
* Aufrufen von Methoden auf Geräten
* Verwalten von IoT-Anmeldeinformationen

Dieser Dienst führt auch IoT Hub-Abfragen durch, um Geräte abzurufen, die zu benutzerdefinierten Gruppen gehören.

Der Microservice stellt einen RESTful-Endpunkt zum Verwalten von Geräten und Gerätezwillingen, Aufrufen von Methoden und Ausführen von IoT Hub-Abfragen bereit.

## <a name="data-processing-and-analytics"></a>Datenverarbeitung und Analysen

Die Lösung enthält die folgenden Komponenten im Teil zur Datenverarbeitung und zu Analysen der logischen Architektur:

### <a name="device-telemetry"></a>Gerätetelemetrie

Die Lösung enthält zwei Microservices für die Verarbeitung der Gerätetelemetrie.

Der Microservice [telemetry-agent](https://github.com/Azure/telemetry-agent-dotnet) ermöglicht Folgendes:

* Speichern von Telemetriedaten in Cosmos DB
* Analysieren des Telemetriedatenstroms von Geräten
* Generieren von Alarmen gemäß definierten Regeln

Die Alarme werden in Cosmos DB gespeichert.

Mit dem Microservice `telemetry-agent` kann das Lösungsportal die Telemetriedaten lesen, die von Geräten gesendet werden. Das Lösungsportal nutzt diesen Dienst außerdem für folgende Zwecke:

* Definieren von Überwachungsregeln, z.B. Schwellenwerte zur Auslösung von Alarmen
* Abrufen der Liste mit den vergangenen Alarmen

Verwenden Sie den RESTful-Endpunkt, der von diesem Microservice bereitgestellt wird, zum Verwalten von Telemetriedaten, Regeln und Alarmen.

### <a name="storage"></a>Speicher

Der Microservice [storage-adapter](https://github.com/Azure/pcs-storage-adapter-dotnet) ist ein Adapter, der dem Hauptspeicherdienst für den Solution Accelerator vorgeschaltet ist. Er ermöglicht eine einfache Sammlung und die Speicherung von Schlüsselwerten.

Die Standardbereitstellung des Solution Accelerators nutzt Cosmos DB als Hauptspeicherdienst.

In der Cosmos DB-Datenbank werden Daten des Solution Accelerators gespeichert. Der Microservice **storage-adapter** fungiert als Adapter für die anderen Microservices der Lösung, um auf Speicherdienste zuzugreifen.

## <a name="presentation"></a>Präsentation

Die Lösung enthält die folgenden Komponenten im Teil zur Darstellung der logischen Architektur:

Die [Webbenutzeroberfläche ist eine React-JavaScript-Anwendung](https://github.com/Azure/pcs-remote-monitoring-webui). Für die Anwendung gilt Folgendes:

* Ausschließliche Verwendung von JavaScript React und vollständige Ausführung im Browser
* Formatierung per CSS
* Interaktion mit öffentlichen Microservices über AJAX-Aufrufe

Die Benutzeroberfläche enthält alle Funktionen des Solution Accelerators und interagiert mit anderen Diensten, z.B.:

* Microservice [authentication](https://github.com/Azure/pcs-auth-dotnet) zum Schützen von Benutzerdaten
* Microservice [iothub-manager](https://github.com/Azure/iothub-manager-dotnet) zum Auflisten und Verwalten der IoT-Geräte

Mit dem Microservice [ui-config](https://github.com/Azure/pcs-config-dotnet) kann die Benutzeroberfläche Konfigurationseinstellungen speichern und abrufen.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie den Quellcode und die Entwicklerdokumentation erkunden möchten, können Sie mit einem der beiden GitHub-Hauptrepositorys beginnen:

* [Solution Accelerator für Remoteüberwachung mit Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/)
* [Solution Accelerator für Remoteüberwachung mit Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Architektur des Solution Accelerators für Remoteüberwachung](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture)

Weitere konzeptuelle Informationen zum Solution Accelerator für Remoteüberwachung finden Sie unter [Anpassen des Solution Accelerators für Remoteüberwachung](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).
