---
title: Was ist Azure IoT Central? | Microsoft-Dokumentation
description: Azure IoT Central ist eine End-to-End-SaaS-Lösung zur Erstellung und Verwaltung benutzerdefinierter IoT-Lösungen. Dieser Artikel enthält eine Übersicht über die Features von Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 9fc565996797c90a6d2ac9b3851ac3408f1842c7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183270"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>Was ist Azure IoT Central?

Azure IoT Central ist eine vollständig verwaltete IoT-SaaS-Lösung (Software-as-a-Service) zur einfachen Erstellung von Produkten, die die physische mit der digitalen Welt verbinden. Setzen Sie Ihre Vision für verbundene Produkte um, indem Sie:

- auf der Grundlage neuer Erkenntnisse, die Sie von verbundenen Geräten gewinnen, bessere Produkte und Erfahrungen für Ihre Kunden entwickeln.
- neue Verkaufschancen für Ihre Organisation generieren.

Im Vergleich zu einem typischen IoT-Projekt bietet Azure IoT Central folgende Vorteile, um die Verwaltung von IoT-Lösungen zu vereinfachen:

- Weniger Verwaltungsaufwand
- Geringere Betriebs- und Fixkosten
- Vereinfachte Anpassung Ihrer Anwendung bei gleichzeitiger Nutzung von:
  - branchenführenden Technologien wie [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) und [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/).
  - für Unternehmen konzipierten Sicherheitsfeatures (etwa End-to-End-Verschlüsselung).

Das folgende Video bietet einen Überblick über Azure IoT Central:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

Im weiteren Verlauf dieses Artikels werden folgende Punkte für Azure IoT Central behandelt:

- Typische Personas eines Projekts
- Erstellung einer Anwendung
- Herstellung einer Verbindung zwischen Ihren Geräten und Ihrer Anwendung
- Verwaltung Ihrer Anwendung

## <a name="personas"></a>Personas

In der Dokumentation von Azure IoT Central werden vier typische Personas verwendet, die mit einer Azure IoT Central-Anwendung interagieren:

- Ein _Ersteller_ definiert die Arten von Geräten, die eine Verbindung mit der Anwendung herstellen, und passt die Anwendung für den Bediener an.
- Ein _Bediener_ verwaltet die mit der Anwendung verbundenen Geräte.
- Ein _Administrator_ kümmert sich um administrative Aufgaben – etwa um die Verwaltung von Benutzern und Rollen innerhalb der Anwendung.
- Ein _Geräteentwickler_ erstellt den Code, der auf einem mit der Anwendung verbundenen Gerät ausgeführt wird.

## <a name="create-your-azure-iot-central-application"></a>Erstellen Ihrer Azure IoT Central-Anwendung

Als Ersteller verwenden Sie Azure IoT Central, um eine benutzerdefinierte, in der Cloud gehostete IoT-Lösung für Ihre Organisation zu erstellen. Eine benutzerdefinierte IoT-Lösung umfasst in der Regel Folgendes:

- Eine cloudbasierte Anwendung, die Telemetriedaten von Ihren Geräten empfängt und die Verwaltung dieser Geräte ermöglicht
- Mehrere Geräte, auf denen benutzerdefinierter Code ausgeführt wird und die mit Ihrer cloudbasierten Anwendung verbunden sind

Sie können schnell eine neue Azure IoT Central-Anwendung bereitstellen und anschließend direkt in Ihrem Browser an Ihre spezifischen Anforderungen anpassen. Mithilfe der webbasierten Tools können Azure IoT Central-Ersteller eine _Gerätevorlage_ für die Geräte erstellen, die eine Verbindung mit der Anwendung herstellen. Eine Gerätevorlage ist die Blaupause eines Gerätemodells. Alle Geräte, die mit der gleichen Gerätevorlage erstellt wurden, verwenden die gleiche Vorlage. Eine Gerätevorlage definiert die Merkmale und das Verhalten eines Gerätetyps. Hierzu zählt beispielsweise Folgendes:

- Die Telemetriedaten, die das Gerät sendet
- Geschäftliche Eigenschaften, die ein Bediener ändern kann
- Geräteeigenschaften, die von einem Gerät festgelegt werden und in der Anwendung schreibgeschützt sind
- Die Schwellenwerte, auf die die Anwendung reagiert
- Einstellungen, die das Verhalten des Geräts bestimmen

Die Gerätevorlagen und die Anwendung können sofort mit von Azure IoT Central generierten Simulationsdaten getestet werden.

Als Ersteller können Sie auch die Benutzeroberfläche der Azure IoT Central-Anwendung für die Bediener anpassen, die die Anwendung tagtäglich verwenden. Die Anpassungsmöglichkeiten für Ersteller umfassen Folgendes:

- Definieren des Layouts von Eigenschaften und Einstellungen für eine Gerätevorlage
- Konfigurieren benutzerdefinierter Dashboards, um Bediener bei der Gewinnung von Erkenntnissen sowie bei der schnelleren Behebung von Problemen zu unterstützen
- Konfigurieren benutzerdefinierter Analysen zur Untersuchung von Zeitreihendaten Ihrer verbundenen Geräte

## <a name="connect-your-devices"></a>Verbinden von Geräten

Nachdem der Ersteller die Arten von Geräten definiert hat, die eine Verbindung mit der Anwendung herstellen können, erstellt ein Geräteentwickler den Code, der auf den Geräten ausgeführt werden soll. Zur Erstellung des Gerätecodes verwenden Geräteentwickler quelloffene [Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks) von Microsoft. Mit umfassender Sprach-, Plattform- und Protokollunterstützung bieten diese SDKs alles, was Sie benötigen, um Ihre Geräte mit Ihrer Azure IoT Central-Anwendung zu verbinden. Die SDKs unterstützen Sie beim Ausführen folgender Aufgaben auf dem mit Azure IoT Central verbundenen Gerät:

- Erstellen einer sicheren Verbindung
- Senden von Telemetriedaten
- Melden des Status
- Empfangen von Konfigurationsupdates

Weitere Informationen finden Sie im Blogbeitrag [Benefits of using the Azure IoT SDKs, and pitfalls to avoid if you don’t](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) (Vorteile der Azure IoT SDKs und vermeidbare Fehler, wenn Sie die SDKs nicht verwenden).

## <a name="manage-your-application"></a>Verwalten Ihrer Anwendung

Azure IoT Central-Anwendungen werden vollständig von Microsoft gehostet, was den Verwaltungsaufwand für Ihre Anwendungen verringert.

Als Bediener verwenden Sie die Benutzeroberfläche der Azure IoT Central-Anwendung, um die Geräte in Ihrer Azure IoT Central-Lösung zu verwalten. Bediener können unter anderem folgende Aufgaben ausführen:

- Überwachen der mit der Anwendung verbundenen Geräte
- Behandeln und Beheben von Problemen mit Geräten
- Bereitstellen neuer Geräte

Ersteller können benutzerdefinierte Regeln und Aktionen für Streamingdaten auf der Gerätevorlagenebene definieren. Bediener können diese Regeln auf der Geräteebene aktivieren oder deaktivieren, um Aufgaben innerhalb der Anwendung zu steuern und zu automatisieren.

Administratoren können über [Benutzerrollen und Berechtigungen](howto-administer.md) den Zugriff auf Ihre Anwendung verwalten.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun einen Überblick über Azure IoT Central verschafft haben, können Sie als Nächstes mit folgenden Schritten fortfahren:

- Informieren Sie sich über die Unterschiede zwischen [Azure IoT Central und Azure IoT Solution Accelerators](overview-iot-options.md).
- Machen Sie sich mit der [Benutzeroberfläche von Azure IoT Central](overview-iot-central-tour.md) vertraut.
- [Erstellen Sie eine Azure IoT Central-Anwendung.](quick-deploy-iot-central.md)
- Machen Sie sich anhand einer Reihe von Tutorials mit Folgendem vertraut:
  - [Erstellen einer Gerätevorlage](tutorial-define-device-type.md) (Tutorial für Ersteller)
  - [Hinzufügen von Regeln zum Automatisieren Ihrer Lösung](tutorial-configure-rules.md) (Tutorial für Ersteller)
  - [Anpassen der Anwendung für die Bediener](tutorial-customize-operator.md) (Tutorial für Ersteller)
  - [überwachen Ihrer Geräte](tutorial-monitor-devices.md) (Tutorial für Bediener)
  - [Hinzufügen eines echten Geräts zu Ihrer Lösung](tutorial-add-device.md) (Tutorial für Bediener)
  - [Erstellen von Code für Ihre Geräte](tutorial-add-device.md#prepare-the-client-code) (Tutorial für Geräteentwickler)
