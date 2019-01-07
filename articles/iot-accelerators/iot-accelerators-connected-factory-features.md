---
title: 'Features der Connected Factory-Lösung: Azure | Microsoft-Dokumentation'
description: Hier erhalten Sie einen Überblick über die Features der vorkonfigurierten Connected Factory-Lösung.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: dobett
ms.openlocfilehash: af2a2c84f9eb420a7ca9a8bd5909cbf856d29a5e
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309194"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Was ist der IoT Solution Accelerator für Connected Factory?

Connected Factory ist eine Open Source-Lösung und dient zur Implementierung der Azure IoT-Referenz von Microsoft in der Industrie. Sie können sie als Grundlage für ein kommerzielles Produkt verwenden. Über [Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/#solutions/types/CF) können Sie eine vorgefertigte Version der Connected Factory-Lösung in Ihrem Azure-Abonnement bereitstellen.

![Dashboard der Connected Factory-Lösung](./media/iot-accelerators-connected-factory-features/dashboard.png)

Der zum Solution Accelerator „Verbundene Factory“ gehörige [Code ist auf GitHub verfügbar](https://github.com/Azure/azure-iot-connected-factory).

Connected Factory beinhaltet die folgenden Features:

## <a name="industrial-device-interoperability"></a>Interoperabilität von Industriegeräten

- Herstellen einer Verbindung mit Industrieanlagen über eine OPC UA-Schnittstelle
- Anzeigen von Livetelemetriedaten mithilfe der simulierten Produktionslinien (mit OPC UA-Servern in Docker-Containern)
- Durchsuchen des OPC UA-Informationsmodells der OPC UA-Server über ein Clouddashboard

## <a name="remote-management"></a>Remoteverwaltung

- Konfigurieren von OPC UA-Anlagen über das Clouddashboard (Aufrufen von Methoden, Lesen und Schreiben von Daten)
- Veröffentlichen und Aufheben der Veröffentlichung von Telemetriedaten aus Ihren OPC UA-Ressourcen über das Clouddashboard

## <a name="cloud-dashboard"></a>Clouddashboard

- Anzeigen einer Vorschau von Telemetriedaten direkt auf einem Clouddashboard
- Anzeigen von Trends bei Telemetriedaten und Erstellen von Korrelationen mithilfe des Time Series Insights-Explorer-Dashboards
- Anzeigen der berechneten gesamten Geräteeffizienz (Overall Equipment Efficiency, OEE) und Key Performance Indicators (KPIs) über ein Clouddashboard
- Anzeigen der Hierarchien von Industrieanlagen in einer Baumtopologie und auf einer interaktiven Karte
- Anzeigen, Bestätigen und Schließen von Warnungen über ein Clouddashboard

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) ist zum Speichern, Visualisieren und Abfragen großer Mengen an Zeitreihendaten ausgelegt. Connected Factory nutzt diesen Dienst.
- Connected Factory kann in diesen Dienst integriert werden und ermöglicht Ihnen dadurch die umfassende Echtzeitanalyse Ihrer Gerätedaten.

## <a name="rules-and-alerts"></a>Regeln und Warnungen

[Konfigurieren von schwellenwertbasierten Regeln für Warnungen](iot-accelerators-connected-factory-configure.md)

## <a name="end-to-end-security"></a>End-to-End-Sicherheit

- Konfigurieren von Sicherheitsberechtigungen für Benutzer mit der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC)
- Implementierung von End-to-End-Verschlüsselung mithilfe von OPC UA-Authentifizierung (mit X.509-Zertifikaten) sowie Sicherheitstoken

## <a name="customizability"></a>Anpassbarkeit

- Passen Sie die Lösung an bestimmte Unternehmensanforderungen an.
- Der vollständige Quellcode der Lösung ist auf GitHub verfügbar. Informationen finden Sie im Repository zur [vorkonfigurierten Connected Factory-Lösung](https://github.com/Azure/azure-iot-connected-factory).

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln erfahren Sie mehr über die vorkonfigurierte Connected Factory-Lösung:

* [Vorkonfigurierte Connected Factory-Lösung – Exemplarische Vorgehensweise](iot-accelerators-connected-factory-sample-walkthrough.md)
* [Bereitstellen eines Edge-Gateways für die vorkonfigurierte Connected Factory-Lösung unter Windows oder Linux]( iot-accelerators-connected-factory-gateway-deployment.md)
