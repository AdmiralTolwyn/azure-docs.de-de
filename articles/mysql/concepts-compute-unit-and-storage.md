---
title: "Erläuterungen zu Computeeinheiten in Azure-Datenbank für MySQL | Microsoft-Dokumentation"
description: "Azure-Datenbank für MySQL: In diesem Artikel werden das Konzept der Computeeinheiten und die Folgen bei Überschreitung der maximalen Anzahl von Computeeinheiten durch eine Workload erläutert."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: 2c4894ae9a4235f9ced4a8d9b991238543646f53
ms.sourcegitcommit: 09a2485ce249c3ec8204615ab759e3b58c81d8cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/13/2018
---
# <a name="explaining-compute-units-in-azure-database-for-mysql"></a>Erläuterungen zu Computeeinheiten in Azure-Datenbank für MySQL
In diesem Thema werden das Konzept der Computeeinheiten und die Folgen bei Überschreitung der maximalen Anzahl von Computeeinheiten durch eine Workload erläutert.

## <a name="what-are-compute-units"></a>Was sind Compute-Einheiten?
Computeeinheiten sind eine Maßeinheit für den CPU-Verarbeitungsdurchsatz, der für einen einzelnen Azure-Datenbank für MySQL-Server garantiert zur Verfügung steht. Eine Compute-Einheit stellt eine kombinierte Maßeinheit für CPU- und Speicherressourcen dar. Im Allgemeinen entsprechen 50 Computeeinheiten einem halben Kern. 100 Computeeinheiten entsprechen einem Kern. 2.000 Computeeinheiten entsprechen 20 Kernen, die als garantierter Verarbeitungsdurchsatz für Ihren Server zur Verfügung stehen.

Die Speichermenge pro Computeeinheit ist für die Tarife Basic und Standard optimiert. Eine Verdoppelung der Computeeinheiten durch das Erhöhen der Leistungsebene ist gleichbedeutend mit der Verdoppelung des Ressourcensatzes, der für diesen einzelnen Azure-Datenbank für MySQL-Server verfügbar ist.

800 Computeeinheiten im Standard-Tarif stellen beispielsweise 8-mal mehr CPU-Durchsatz und Arbeitsspeicher bereit als eine Konfiguration im Tarif Standard mit 100 Computeeinheiten. Während 100 Computeeinheiten im Standard-Tarif denselben CPU-Durchsatz wie 100 Computeeinheiten im Tarif Basic bieten, beträgt die im Tarif Standard vorkonfigurierte Speichermenge das Doppelte der im Tarif Basic konfigurierten Speichermenge. Aus diesem Grund bietet der Tarif Standard eine bessere Workloadleistung und niedrigere Transaktionslatenzen als der Tarif Basic mit denselben Computeeinheiten.

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>Wie kann ich die Anzahl der benötigten Compute-Einheiten für meine Workload bestimmen?
Wenn Sie einen vorhandenen lokalen MySQL-Server oder einen MySQL-Server, der auf einem virtuellen Computer ausgeführt wird, migrieren möchten, können Sie die Anzahl der Computeeinheiten bestimmen, indem Sie die Anzahl der Kerne schätzen, die für den Verarbeitungsdurchsatz Ihrer Workload erforderlich ist. 

Wenn der vorhandene lokale oder virtuelle Server derzeit 4 Kerne nutzt (ohne CPU-Hyperthread), können Sie zunächst bei 400 Computeeinheiten für Ihren Azure-Datenbank für MySQL-Server beginnen. Compute-Einheiten können abhängig von Ihrer Workload dynamisch mit nahezu keinen Anwendungsausfallzeiten zentral hoch- oder herunterskaliert werden. 

Halten Sie entweder den Metrikgraph im Azure-Portal im Blick, oder schreiben Sie Befehle für die Azure-Befehlszeilenschnittstelle zum Messen der Computeeinheiten. Wichtige zu überwachende Metriken sind der Prozentsatz von Computeeinheiten und der Grenzwert für Computeeinheiten.

>[!IMPORTANT]
> Wenn die Speicher-IOPS nicht voll ausgelastet sind, sollten Sie auch die Nutzung der Computeeinheiten überwachen. Durch das Erhöhen der Compute-Einheiten ist eventuell ein höherer E/A-Durchsatz möglich, da der Leistungsengpass aufgrund der begrenzten CPU- oder Arbeitsspeicherressourcen verringert wird.

## <a name="what-happens-when-i-hit-my-maximum-compute-units"></a>Was passiert, wenn die Obergrenze von Computeeinheiten erreicht ist?
Leistungsebenen werden kalibriert und gesteuert, um die erforderlichen Ressourcen zum Ausführen der Workload Ihrer Datenbank bis zu den maximalen Grenzwerten für den ausgewählten Tarif und die Leistungsebene bereitzustellen. 

Wenn die Workload die Grenzwerte für Computeeinheiten bzw. bereitgestellte IOPS erreicht, können Sie die Ressourcen auch weiterhin auf der maximal zulässigen Ebene nutzen. Es treten aber wahrscheinlich erhöhte Wartezeiten für Ihre Abfragen auf. Diese Grenzwerte führen nicht zu Fehlern, sondern nur zu einer Verlangsamung Ihrer Workload. Wenn die Verlangsamung aber zu schwerwiegend ist, tritt ein Timeout für die Abfragen auf. 

Wenn die Workload die Grenzwerte für die Anzahl von Verbindungen erreicht, werden explizite Fehler ausgelöst. Weitere Informationen zu Grenzwerten für Ressourcen finden Sie unter [Einschränkungen in Azure-Datenbank für MySQL](concepts-limits.md).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Tarifen finden Sie unter [Azure-Datenbank für MySQL – Tarife](./concepts-service-tiers.md).
