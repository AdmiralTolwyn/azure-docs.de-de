---
title: Schützen Ihrer IoT-Bereitstellung in Azure | Microsoft-Dokumentation
description: " Die Azure-Dienste für das Internet der Dinge (Internet of Things, IoT) bieten zahlreiche Funktionen. Dieser Artikel hilft Ihnen, zu verstehen, wie Sie Ihre IoT-Lösungen in Azure schützen können. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: 1473c8dd-8669-48fb-86db-b3c50e2eaf59
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 41e422d0808cafb45b182c5f0a6bb7176a35516f
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35758619"
---
# <a name="internet-of-things-security-overview"></a>Internet der Dinge – Sicherheitsübersicht
Die Azure-Dienste für das Internet der Dinge (Internet of Things, IoT) bieten zahlreiche Funktionen. Diese Dienste auf Unternehmensniveau ermöglichen Folgendes:

* Sammeln von Daten von Geräten
* Analysieren von Datenströmen während des Betriebs
* Speichern und Abfragen von großen Datasets
* Anzeigen von Echtzeit- und Verlaufsdaten
* Integrieren mit Back-Office-Systemen

Zur Bereitstellung dieser Funktionen kombinieren Azure IoT Solution Accelerators mehrere Azure-Dienste mit benutzerdefinierten Erweiterungen als vorkonfigurierte Lösungen. Diese vorkonfigurierten Lösungen sind Basisimplementierungen von häufig verwendeten IoT-Lösungsmustern, mit denen Sie die Bereitstellung Ihrer IoT-Lösungen beschleunigen können. Mithilfe der IoT-Software Development Kits können Sie diese Lösungen Ihren Anforderungen anpassen und ggf. erweitern. Wenn Sie neue IoT-Lösungen entwickeln, können Sie diese Lösungen auch als Beispiele oder Vorlagen verwenden.

Die Azure IoT Solution Accelerators sind leistungsstarke Lösungen für Ihre IoT-Anforderungen. Allerdings ist es von größter Wichtigkeit, dass Ihre IoT-Lösungen von Anfang unter Berücksichtigung von Sicherheitsaspekten entworfen werden. Aufgrund der schieren Anzahl von IoT-Geräten kann ein Sicherheitsvorfall schnell zu einem weit verbreiteten Ereignis mit erheblichen Konsequenzen werden.

Damit Sie die IoT-Lösungen besser verstehen können, bieten wir Ihnen die folgenden Informationen.

## <a name="security-architecture"></a>Sicherheitsarchitektur
Beim Entwerfen eines Systems ist es wichtig zu verstehen, welche potenziellen Bedrohungen es für das System gibt. Anschließend müssen beim Entwerfen und Zusammenstellen des Systems die richtigen Abwehrmaßnahmen ergriffen werden. Es ist besonders wichtig, schon zu Beginn des Entwurfsprozesses für das Produkt an die Sicherheit zu denken. Indem ein Verständnis entwickelt wird, wie Angreifer ein System unter Umständen kompromittieren können, wird sichergestellt, dass von Anfang an für geeignete Lösungen gesorgt ist.

Informieren Sie sich in dem Artikel [Internet der Dinge (IoT) – Sicherheitsarchitektur](/azure/iot-fundamentals/iot-security-architecture)über die IoT-Sicherheitsarchitektur.

In diesem Artikel werden die folgenden Themen behandelt:

* [Sicherheit beginnt mit einem Bedrohungsmodell](/azure/iot-fundamentals/iot-security-architecture#security-starts-with-a-threat-model)
* [Sicherheit im Internet der Dinge (IoT)](/azure/iot-fundamentals/iot-security-architecture#security-in-iot)
* [Durchführen der Bedrohungsmodellierung für die Azure IoT-Referenzarchitektur](/azure/iot-fundamentals/iot-security-architecture#threat-modeling-the-azure-iot-reference-architecture)

## <a name="security-from-the-ground-up"></a>Sicherheit von Anfang an
Das IoT ist für Unternehmen weltweit mit neuen Anforderungen in Bezug auf Sicherheit, Datenschutz und Compliance verbunden. Im Gegensatz zur herkömmlichen Cyber-Technologie, bei der es um Software und ihre Implementierung geht, liegt der Schwerpunkt bei IoT darauf, was passiert, wenn die Cyber-Welt und die physische Welt aufeinandertreffen. Zum Schützen von IoT-Lösungen müssen die sichere Bereitstellung von Geräten, die sichere Verbindung zwischen diesen Geräten und der Cloud sowie der sichere Schutz von Daten in der Cloud während der Verarbeitung und Speicherung sichergestellt werden. Dieser Funktionalität stehen aber Geräte mit Ressourcenbeschränkung, die geografische Verteilung von Bereitstellungen und eine große Zahl von Geräten innerhalb einer Lösung entgegen.

Informationen zum Handhaben der Sicherheit in diesen Bereichen erfahren Sie unter [Schützen des Internets der Dinge von Anfang an](/azure/iot-fundamentals/iot-security-ground-up).

In diesem Artikel werden die folgenden Themen behandelt:

* [Von Anfang an eine sichere Infrastruktur](/azure/iot-fundamentals/iot-security-ground-up#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure – Sichere IoT-Infrastruktur für Ihr Unternehmen](/azure/iot-fundamentals/iot-security-ground-up#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Bewährte Methoden
Zum Schützen einer IoT-Infrastruktur ist eine umfassende Sicherheitsstrategie erforderlich. Jede Ebene – vom Sichern von Daten in der Cloud über den Schutz der Datenintegrität beim Übertragen über das öffentliche Internet bis hin zur sicheren Bereitstellung von Geräten – erhöht die Sicherheit in der allgemeinen Infrastruktur.

Informationen zu bewährten Methoden zur Sicherheit für das Internet der Dinge finden Sie unter [Internet der Dinge – bewährte Methoden für die Sicherheit](/azure/iot-fundamentals/iot-security-best-practices).

In diesem Artikel werden die folgenden Themen behandelt:

* [IoT-Hardwarehersteller/-integrator](/azure/iot-fundamentals/iot-security-best-practices#iot-hardware-manufacturerintegrator)
* [IoT-Lösungsentwickler](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-developer)
* [IoT-Lösungsbereitsteller](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-deployer)
* [IoT-Lösungsoperator](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-operator)
