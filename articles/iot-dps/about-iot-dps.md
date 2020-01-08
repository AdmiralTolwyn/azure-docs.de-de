---
title: Übersicht über den Azure IoT Hub Device Provisioning-Dienst | Microsoft-Dokumentation
description: Hier finden Sie Informationen zur Gerätebereitstellung in Azure mit Device Provisioning Service (DPS) und IoT Hub.
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: overview
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: b28e09b2d304dc392442d98fe39654bab2c8d09c
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645054"
---
# <a name="provisioning-devices-with-azure-iot-hub-device-provisioning-service"></a>Bereitstellung von Geräten mit dem Azure IoT Hub Device Provisioning-Dienst
Microsoft Azure bietet einen umfangreichen Satz von integrierten öffentlichen Clouddiensten für alle Ihre IoT-Lösunganforderungen. Der IoT Hub Device Provisioning-Dienst ist ein Hilfsdienst für IoT Hub, der die JIT-Bereitstellung im richtigen IoT-Hub ohne manuelles Eingreifen ermöglicht, sodass Kunden Millionen von Geräten sicher und skalierbar bereitstellen können.

## <a name="when-to-use-device-provisioning-service"></a>Wann sollte der Device Provisioning-Dienst verwendet werden?
Es gibt viele Bereitstellungsszenarien, in denen der Device Provisioning-Dienst eine hervorragende Wahl ist, um Verbindungen von Geräten mit IoT Hub herzustellen und sie für IoT Hub zu konfigurieren, z.B.:

* Bereitstellung ohne manuelles Eingreifen für eine einzelne IoT-Lösung ohne werkseitige Hartcodierung von IoT Hub-Verbindungsinformationen (Anfangssetup)
* Mehrere Hubs übergreifender Lastenausgleich für Geräte
* Herstellen der Verbindung von Geräten mit der IoT-Lösung ihrer Besitzer auf Basis der Verkaufstransaktionsdaten (Mehrinstanzenfähigkeit)
* Herstellen der Verbindung von Geräten mit einer bestimmten IoT-Lösung abhängig vom Anwendungsfall (Lösungsisolation)
* Herstellen der Verbindung eines Geräts mit IoT Hub mit der geringsten Wartezeit (Geo-Sharding)
* Erneute Bereitstellung basierend auf einer Änderung im Gerät
* Wechseln der Schlüssel, die vom Gerät verwendet werden, um eine Verbindung mit IoT Hub herzustellen (wenn keine X. 509-Zertifikate verwendet werden, um Verbindungen herstellen)

## <a name="behind-the-scenes"></a>Abläufe im Hintergrund
Alle im vorherigen Abschnitt aufgelisteten Szenarien können mit dem Bereitstellungsdienst für die Bereitstellung ohne manuelles Eingreifen mit demselben Ablauf ausgeführt werden. Viele der normalerweise mit der Bereitstellung verbundenen manuellen Schritte sind beim Device Provisioning-Dienst automatisiert, um die Bereitstellung von IoT-Geräten zu beschleunigen und das Risiko eines manuellen Fehlers zu senken. Im Folgenden wird beschrieben, was bei der Bereitstellung eines Geräts im Hintergrund geschieht. Der erste Schritt ist manuell, alle folgenden Schritte sind automatisiert.

![Grundlegender Bereitstellungsablauf](./media/about-iot-dps/dps-provisioning-flow.png)

1. Der Gerätehersteller fügt die Geräteregistrierungsinformationen der Registrierungsliste im Azure-Portal hinzu.
2. Das Gerät nimmt mit dem werkseitig festgelegten Bereitstellungsdienstendpunkt Kontakt auf. Das Gerät übergibt dem Bereitstellungsdienst seine Identifikationsinformationen, um seine Identität nachzuweisen.
3. Der Bereitstellungsdienst überprüft die Identität des Geräts entweder mit einer Nonce-Herausforderung ([Trusted Platform Module](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)) oder einer standardmäßigen X.509-Überprüfung (X.509), indem er Registrierungs-ID und Schlüssel anhand des Registrierungslisteneintrags überprüft.
4. Der Bereitstellungsdienst registriert das Gerät bei einem IoT Hub und füllt den [gewünschten Zwillingsstatus](../iot-hub/iot-hub-devguide-device-twins.md) des Geräts aus.
5. Der IoT Hub gibt die Geräte-ID-Informationen an den Bereitstellungsdienst zurück.
6. Der Bereitstellungsdienst gibt die IoT Hub-Verbindungsinformationen an das Gerät zurück. Das Gerät kann nun direkt Daten an den IoT Hub senden.
7. Das Gerät stellt eine Verbindung mit dem IoT Hub her.
8. Das Gerät ruft den gewünschten Status von seinem Gerätezwilling im IoT Hub ab.

## <a name="provisioning-process"></a>Bereitstellung
Es gibt zwei unterschiedliche Schritte im Bereitstellungsprozess eines Geräts, bei denen der Device Provisioning-Dienst einen Teil übernimmt, der auch eigenständig ausgeführt werden kann:

* Der **Fertigungsschritt**, in dem das Gerät werkseitig erstellt und vorbereitet wird, und
* Der **Cloudeinrichtungsschritt**, in dem der Device Provisioning-Dienst für die automatisierte Bereitstellung konfiguriert wird.

Beide Schritte fügen sich nahtlos in bestehende Fertigungs- und Bereitstellungsprozesse ein. Der Device Provisioning-Dienst vereinfacht auch einige Bereitstellungsprozesse, die mit viel manueller Arbeit zum Abrufen der Verbindungsinformationen auf das Gerät verbunden sind.

### <a name="manufacturing-step"></a>Fertigungsschritt
Dieser Schritt umfasst alles, was in der Fertigung geschieht. An diesem Schritt sind Hardwareentwickler und -hersteller, Integrator und/oder Endhersteller des Geräts beteiligt. Dieser Schritt betrifft das Erstellen der Hardware selbst.

Der Device Provisioning-Dienst führt keinen neuen Schritt in den Fertigungsprozess ein; stattdessen bindet er ihn in den vorhandenen Schritt ein, der die grundlegende Software und (im Idealfall) das HSM auf dem Gerät installiert. Statt dass in diesem Schritt eine Geräte-ID erstellt wird, wird das Gerät mit den Bereitstellungsdienstinformationen programmiert, sodass es den Bereitstellungsdienst aufrufen kann, um beim Einschalten seine Verbindungsinformationen/IoT-Lösungszuweisung zu erhalten.

Auch in diesem Schritt liefert der Hersteller dem Bereitsteller/Bediener des Geräts Indentifikationsschlüsselinformationen. Die Angabe dieser Informationen ist möglicherweise einfach nur die Bestätigung, dass alle Geräte über ein X.509-Zertifikat verfügen, das aus einem vom Gerätebereitsteller/-bediener bereitgestellten Signaturzertifikat generiert wurde, kann jedoch auch komplexer sein und das Extrahieren des öffentlichen Teils eines TPM-Endorsement Keys aus jedem TPM-Gerät beinhalten. Diese Dienste werden heute von vielen Hardwareherstellern angeboten.

### <a name="cloud-setup-step"></a>Cloudeinrichtungsschritt
In diesem Schritt wird die Cloud für die ordnungsgemäße automatische Bereitstellung konfiguriert. Im Allgemeinen sind zwei Arten von Benutzern am Cloudeinrichtungsschritt beteiligt: eine Person (ein Gerätebediener), die weiß, wie Geräte erstmalig eingerichtet werden müssen, und eine andere Person (ein Lösungsoperator), die weiß, wie Geräte auf die IoT Hubs aufgeteilt werden sollen.

Eine einmalige anfängliche Einrichtung der Bereitstellung muss ausgeführt werden, und dies wird im Allgemeinen vom Lösungsoperator übernommen. Sobald der Bereitstellungsdienst konfiguriert ist, muss er nicht geändert werden, solange sich der Anwendungsfall nicht ändert.

Wenn der Dienst für die automatische Bereitstellung konfiguriert ist, muss er auf das Registrieren von Geräten vorbereitet werden. Dieser Schritt erfolgt durch den Gerätebediener, der die gewünschte Konfiguration der Geräte kennt und dafür verantwortlich ist, sicherzustellen, dass der Bereitstellungsdienst ordnungsgemäß die Identität des Geräts nachweisen kann, wenn es seinen IoT Hub sucht. Die Gerätebediener übernimmt die Indentifikationsschlüsselinformationen vom Hersteller und fügt sie der Registrierungsliste hinzu. Die Registrierungsliste kann später aktualisiert werden, indem neue Einträge hinzugefügt oder vorhandene Einträge mit den neuesten Informationen zu den Geräten aktualisiert werden.

## <a name="registration-and-provisioning"></a>Registrierung und Bereitstellung
*Bereitstellung* kann abhängig von der Branche, wo der Begriff verwendet wird, verschiedene Bedeutungen haben. Im Kontext der Bereitstellung von IoT-Geräten für ihre Cloudlösung ist sie ein aus zwei Teilen bestehender Prozess:

1. Im ersten Teil wird durch Registrieren des Geräts eine erstmalige Verbindung des Geräts mit der IoT-Lösung hergestellt.
2. Im zweiten Schritt wird die richtige Konfiguration des Geräts basierend auf den spezifischen Anforderungen der Lösung, für die es registriert wurde, angewendet.

Nach Ausführung dieser beiden Schritte kann das Gerät als vollständig bereitgestellt bezeichnet werden. Einige Clouddienste ermöglichen nur den ersten Schritt des Bereitstellungsvorgangs, das Registrieren von Geräten beim Endpunkt der IoT-Lösung, aber nicht die Erstkonfiguration. Der Device Provisioning-Dienst automatisiert beide Schritte und sorgt so für eine nahtlose Bereitstellung des Geräts.

## <a name="features-of-the-device-provisioning-service"></a>Features des Device Provisioning-Diensts
Der Device Provisioning-Dienst hat viele Features, die ihn zur idealen Lösung zur Bereitstellung von Geräten machen.

* Unterstützung des **sicheren Nachweises** für Identitäten auf X.509- und TPM-Basis.
* Eine **Registrierungsliste** mit der vollständigen Aufzeichnung von Geräten/Gerätegruppen, die sich zu einem beliebigen Zeitpunkt registrieren könnten. Die Registrierungsliste enthält Informationen zu der gewünschten Konfiguration des Geräts, sobald es registriert ist, und sie kann jederzeit aktualisiert werden.
* **Mehrere Zuordnungsrichtlinien**, um zu steuern, wie Device Provisioning Service zur Unterstützung Ihrer Szenarien Geräte IoT-Hubs zuweist: Niedrigste Latenz, gleichmäßig gewichtete Verteilung (Standard) und statische Konfiguration über die Registrierungsliste. Beachten Sie, dass die Latenz mithilfe der gleichen Methode wie bei [Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#performance) ermittelt wird.
* **Überwachungs- und Diagnoseprotokolle**, um sicherzustellen, dass alles ordnungsgemäß funktioniert
* **Unterstützung für mehrere Hubs**, sodass der Device Provisioning-Dienst Geräte mehreren IoT Hubs zuweisen kann. Der Device Provisioning-Dienst kann mehrere Azure-Abonnements übergreifend mit Hubs kommunizieren.
* **Regionsübergreifende Unterstützung**, sodass der Device Provisioning-Dienst Geräte IoT Hubs in anderen Regionen zuweisen kann

Weitere Informationen zu den Konzepten und Features in Verbindung mit der Gerätebereitstellung finden Sie unter [IoT Hub Device Provisioning Service device concepts](concepts-device.md) (IoT Hub Device Provisioning-Dienst: Gerätekonzepte), [IoT Hub Device Provisioning Service concepts](concepts-service.md) (IoT Hub Device Provisioning-Dienst: Konzepte) und [IoT Hub Device Provisioning Service security concepts](concepts-security.md) (IoT Hub Device Provisioning-Dienst: Sicherheitskonzepte).

## <a name="cross-platform-support"></a>Plattformübergreifende Unterstützung
Der Device Provisioning-Dienst ist wie alle Azure IoT-Dienste plattformübergreifend mit einer Vielzahl von Betriebssystemen einsetzbar. Azure bietet Open Source-SDKs in einer Vielzahl von [Sprachen](https://github.com/Azure/azure-iot-sdks) zur Erleichterung von Geräteverbindungen und Dienstverwaltung. Der Device Provisioning-Dienst unterstützt die folgenden Protokolle zum Verbinden von Geräten:

* HTTPS
* AMQP
* AMQP über Websockets
* MQTT
* MQTT über Websockets

Der Device Provisioning-Dienst unterstützt für Dienstvorgänge nur HTTPS-Verbindungen.

## <a name="regions"></a>Regions
Der Device Provisioning-Dienst ist in vielen Regionen verfügbar. Eine für alle Dienste laufend aktualisierte Liste der vorhandenen und neu angekündigten Regionen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/regions/). Die Verfügbarkeit des Device Provisioning-Diensts können Sie auf der Seite [Azure-Status](https://azure.microsoft.com/status/) überprüfen.

> [!NOTE]
> Der Device Provisioning-Dienst ist global und nicht an einen Standort gebunden. Sie müssen jedoch eine Region angeben, in der Ihre dem Device Provisioning-Dienstprofil zugeordneten Metadaten gespeichert werden sollen.

## <a name="availability"></a>Verfügbarkeit
Für den Device Provisioning-Dienst gilt eine Vereinbarung zum Servicelevel (SLA) von 99,9 Prozent, und Sie können die SLA [hier](https://azure.microsoft.com/support/legal/sla/iot-hub/) einsehen. Die vollständige [Azure-SLA](https://azure.microsoft.com/support/legal/sla/) erläutert die garantierte Verfügbarkeit von Azure insgesamt.

## <a name="quotas"></a>Kontingente
Für jedes Azure-Abonnement gelten standardmäßig bestimmte Kontingentgrenzen, die den Umfang Ihrer IoT-Lösung beeinträchtigen könnten. Die aktuelle Grenze auf Abonnementbasis sind 10 Device Provisioning-Dienste pro Abonnement.

[!INCLUDE [azure-iotdps-limits](../../includes/iot-dps-limits.md)]

Weitere Informationen zu Kontingentgrenzen finden Sie hier:
* [Einschränkungen für Azure-Abonnementdienste](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="related-azure-components"></a>Verwandte Azure-Komponenten
Der Device Provisioning-Dienst automatisiert die Bereitstellung von Geräten unter Azure IoT Hub. Erfahren Sie mehr über [IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

## <a name="next-steps"></a>Nächste Schritte
Sie haben jetzt einen Überblick über die Bereitstellung von IoT-Geräten in Azure erhalten. Der nächste Schritt ist, dass Sie ein End-to-End-IoT-Szenario testen.
> [!div class="nextstepaction"]
> [Einrichten des IoT Hub Device Provisioning-Diensts (Vorschauversion) mit dem Azure-Portal](quick-setup-auto-provision.md)
> [Erstellen und Bereitstellen eines simulierten Geräts mithilfe des IoT Hub Device Provisioning-Diensts (Vorschauversion)](quick-create-simulated-device.md)
> [Set up a device to provision using the Azure IoT Hub Device Provisioning Service](tutorial-set-up-device.md) (Einrichten eines Geräts für die Bereitstellung mit dem Azure IoT Hub Device Provisioning-Dienst)
