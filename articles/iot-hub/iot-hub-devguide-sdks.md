---
title: Informationen zu Azure IoT SDKs | Microsoft Docs
description: 'Entwicklerhandbuch: Informationen und Links zu verschiedenen Geräte- und Dienst-SDKs für Azure IoT, mit denen Sie Geräte- und Back-End-Apps erstellen können.'
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aec2126369f45a89050dbd8b2d3cae7e00ccb8ed
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/03/2018
---
# <a name="understand-and-use-azure-iot-sdks"></a>Verstehen und Verwenden von Azure IoT SDKs

Es gibt drei Kategorien von Software Development Kits (SDKs) für die Arbeit mit IoT Hub:

* **Geräte-SDKs** ermöglichen das Erstellen von Apps, die auf Ihren IoT-Geräten ausgeführt werden. Mit diesen Apps werden Telemetriedaten an die IoT Hub-Instanz gesendet und optional Nachrichten von der IoT Hub-Instanz empfangen.

* **Dienst-SDKs** ermöglichen das Verwalten der IoT Hub-Instanz und senden optional Nachrichten an die IoT-Geräte.

* **Azure IoT-Edge** ermöglicht Ihnen, Gateways für Geräte zu erstellen, die nicht eines der unterstützten Protokolle verwenden. Gateways können auch Nachrichten in Edge verarbeiten.

SDKs dienen zur Unterstützung mehrerer Programmiersprachen.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Azure IoT-Geräte-SDKs

Die Microsoft Azure IoT-Geräte-SDKs enthalten Code, der das Erstellen von Geräten und Anwendungen ermöglicht, die eine Verbindung mit Azure IoT Hub-Diensten herstellen und von ihnen verwaltet werden.

Die folgenden Azure IoT-Geräte-SDKs sind auf GitHub zum Download verfügbar:

* [Azure IoT-Geräte-SDK für .NET][lnk-dotnet-device-sdk]
* [Azure IoT-Geräte-SDK für Java][lnk-java-device-sdk]
* [Azure IoT-Geräte-SDK für Node.js][lnk-node-device-sdk]
* [Azure IoT-Geräte-SDK für Python][lnk-python-device-sdk]
* [Azure IoT-Geräte-SDK für C][lnk-c-device-sdk] wurde in ANSI C (C99) geschrieben und ist auf Portabilität und hohe Plattformkompatibilität ausgelegt. Für C gibt es zwei Geräteclientbibliotheken: **iothub_client** auf niedriger Ebene und das **Serialisierungsmodul**.

> [!NOTE]
> In den „Readme“-Dateien in den GitHub-Repositorys finden Sie Informationen zum Verwenden sprach- und plattformspezifischer Paket-Manager zum Installieren von Binärdateien und Abhängigkeiten auf Ihrem Entwicklungscomputer.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Betriebssystemplattformen und Hardwarekompatibilität

Weitere Informationen zur Kompatibilität von SDKs mit bestimmten Hardwaregeräten finden Sie im [Katalog mit für Azure zertifizierten IoT-Geräten][lnk-certified].

## <a name="azure-iot-service-sdks"></a>Azure IoT-Dienst-SDKs

Die Azure IoT-Dienst-SDKs enthalten Code zum Erstellen von Anwendungen, die direkt mit IoT Hub interagieren, um Geräte und Sicherheit zu verwalten.

Die folgenden Azure IoT-Dienst-SDKs sind auf GitHub zum Download verfügbar:

* [Azure IoT-Dienst-SDK für .NET][lnk-dotnet-service-sdk]
* [Azure IoT-Dienst-SDK für Java][lnk-java-service-sdk]
* [Azure IoT-Dienst-SDK für Node.js][lnk-node-service-sdk]
* [Azure IoT-Dienst-SDK für Python][lnk-python-service-sdk]
* [Azure IoT-Dienst-SDK für C][lnk-c-service-sdk]

> [!NOTE]
> In den „Readme“-Dateien in den GitHub-Repositorys finden Sie Informationen zum Verwenden sprach- und plattformspezifischer Paket-Manager zum Installieren von Binärdateien und Abhängigkeiten auf Ihrem Entwicklungscomputer.

## <a name="azure-iot-edge"></a>Azure IoT Edge

Azure IoT Edge enthält die Infrastruktur und Module zum Erstellen von IoT-Gatewaylösungen. Sie können IoT Edge erweitern, um maßgeschneiderte Gateways für beliebige End-to-End-Szenarien zu erstellen.

Sie können [Azure IoT Edge][lnk-iot-edge] von GitHub herunterladen.

## <a name="online-api-reference-documentation"></a>Online verfügbare API-Referenzdokumentation

Die folgende Liste enthält Links zur online verfügbaren API-Referenzdokumentation für Azure IoT-Gerätebibliotheken, -Dienstbibliotheken und -Gatewaybibliotheken:

* [Internet der Dinge (IoT, Internet of Things) .NET][lnk-dotnet-ref]
* [Azure IoT-Geräte-SDK für Java][lnk-java-ref]
* [Azure IoT-Dienst-SDK für Java][lnk-java-service-ref]
* [Azure IoT-Geräte-SDK für Node.js][lnk-node-ref]
* [Azure IoT-Dienst-SDK für Node.js][lnk-node-service-ref]
* [Azure IoT-Geräte-SDK für C][lnk-c-ref]
* [IoT Hub REST][lnk-rest-ref]
* [Azure IoT Edge][lnk-gateway-ref]

## <a name="next-steps"></a>Nächste Schritte

Weitere Referenzthemen in diesem IoT Hub-Entwicklungsleitfaden:

* [IoT Hub-Endpunkte][lnk-devguide-endpoints]
* [IoT Hub-Abfragesprache für Gerätezwillinge, Aufträge und Nachrichtenrouting][lnk-devguide-query]
* [Kontingente und Drosselung][lnk-devguide-quotas]
* [IoT Hub-MQTT-Unterstützung][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-c-service-sdk]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_service_client
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/device
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/service
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/device
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/service
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device
[lnk-python-service-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/service
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-iot-edge]: https://github.com/Azure/iot-edge

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service
[lnk-node-service-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-gateway-ref]: http://azure.github.io/iot-edge/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
