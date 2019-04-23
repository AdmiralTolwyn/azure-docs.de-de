---
title: Tarif „Unternehmenskritisch“ – Azure SQL-Datenbankdienst | Microsoft-Dokumentation
description: Informationen zum Tarif „Unternehmenskritisch“ für Azure SQL-Datenbank
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: e9f40e749642f2025c5298df74f9d8ff87aec14b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784055"
---
# <a name="business-critical-tier---azure-sql-database"></a>Tarif „Unternehmenskritisch“ – Azure SQL-Datenbank

> [!NOTE]
> Der Tarif „Unternehmenskritisch“ hat im DTU-Kaufmodell den Namen „Premium“. Einen Vergleich zwischen V-Kern-basiertem Kaufmodell und DTU-basiertem Kaufmodell finden Sie unter [Kaufmodelle für Azure SQL-Datenbank und Ressourcen](sql-database-purchase-models.md).

Azure SQL-Datenbank basiert auf der an die Cloudumgebung angepasste Architektur der SQL Server-Datenbank-Engine, um die Verfügbarkeit von 99,99 % selbst bei Infrastrukturausfällen sicherzustellen. In Azure SQL-Datenbank werden drei Architekturmodelle verwendet:
- Universell/Standard 
- Unternehmenskritisch/Premium
- Hyperscale

Das Diensttarifmodell des Typs „Premium/Unternehmenskritisch“ basiert auf einem Cluster von Datenbank-Engine-Prozessen. Dieses Architekturmodell beruht darauf, dass immer ein Quorum von verfügbaren Datenbank-Engine-Knoten vorhanden ist, und weist selbst während Wartungsaktivitäten eine minimale Leistungsbeeinträchtigung der Workload auf.

In Azure werden das zugrunde liegende Betriebssystem, Treiber und die SQL Server-Datenbank-Engine transparent mit minimaler Ausfallzeit für Endbenutzer aktualisiert und gepatcht. 

Die Premium-Verfügbarkeit wird in den Diensttarifen „Premium“ und „Unternehmenskritisch“ von Azure SQL-Datenbank aktiviert und ist für hohe Workloads vorgesehen, bei denen keine Leistungsbeeinträchtigung aufgrund laufender Wartungsvorgänge toleriert werden kann.

Im Premium-Modell werden in Azure SQL-Datenbank der Compute- und Speicherbereich auf dem einzelnen Knoten integriert. Die Hochverfügbarkeit in diesem Architekturmodell wird durch die Replikation auf Compute- (SQL Server-Datenbank-Engine-Prozess) und Speicherebene (lokal angefügte SSD) erreicht, die in einem Cluster mit vier Knoten anhand einer ähnlichen Technologie wie [AlwaysOn-Verfügbarkeitsgruppen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) in SQL Server bereitgestellt werden.

![Cluster von Datenbank-Engine-Knoten](media/sql-database-managed-instance/business-critical-service-tier.png)

Der SQL-Datenbank-Engine-Prozess sowie die zugrunde liegenden MDF- und LDF-Dateien werden mit lokal angefügtem SSD-Speicher auf demselben Knoten platziert und bieten eine geringe Latenz für die Workload. Hochverfügbarkeit wird anhand einer ähnlichen Technologie wie [AlwaysOn-Verfügbarkeitsgruppen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) in SQL Server implementiert. Jede Datenbank ist ein Cluster von Datenbankknoten mit einer primären Datenbank, die für die Kundenworkload zugänglich ist, und drei sekundären Prozessen, die Kopien der Daten enthalten. Der primäre Knoten überträgt die Änderungen kontinuierlich zu sekundären Knoten, um sicherzustellen, dass die Daten auf sekundären Replikaten verfügbar sind, wenn der primäre Knoten aus bestimmten Gründen ausfällt. Ein Failover wird von der SQL Server-Datenbank-Engine verarbeitet: Ein sekundäres Replikat wird zum primären Knoten, und ein neues sekundäres Replikat wird erstellt, um sicherzustellen, dass ausreichend Knoten im Cluster vorhanden sind. Die Workload wird automatisch zum neuen primären Knoten umgeleitet.

Darüber hinaus umfasst der Cluster des Typs „Unternehmenskritisch“ eine integrierte Funktion [Horizontale Leseskalierung](sql-database-read-scale-out.md), die einen gebührenfreien, integrierten und schreibgeschützten Knoten bereitstellt, der zum Ausführen von schreibgeschützten Abfragen (z.B. Berichten) verwendet werden kann, die die Leistung der primären Workload nicht beeinträchtigen sollen.

## <a name="when-to-choose-this-service-tier"></a>Wann sollte dieser Diensttarif gewählt werden?

Der Diensttarif „Unternehmenskritisch“ ist für die Anwendungen gedacht, die Antworten mit geringer Latenz vom zugrunde liegenden SSD-Speicher (durchschnittlich 1 – 2 ms), schnelle Wiederherstellung bei einem Fehler der zugrunde liegenden Infrastruktur oder das Auslagern von Berichten, Analysen und schreibgeschützten Abfragen an das kostenlose lesbare sekundäre Replikat der primären Datenbank erfordern.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die Tarife [Universell](sql-database-service-tier-general-purpose.md) und [Hyperscale](sql-database-service-tier-hyperscale.md).
- Erfahren Sie mehr über [Service Fabric](../service-fabric/service-fabric-overview.md).
- Weitere Optionen zu Hochverfügbarkeit und Notfallwiederherstellung finden Sie unter [Geschäftskontinuität](sql-database-business-continuity.md).
