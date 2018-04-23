---
title: Verwalten von Sicherungen mit der rollenbasierten Azure-Zugriffssteuerung | Microsoft-Dokumentation
description: Verwenden Sie die rollenbasierte Zugriffssteuerung zum Verwalten des Zugriffs auf Vorgänge der Sicherungsverwaltung im Recovery Services-Tresor.
services: backup
documentationcenter: ''
author: trinadhk
manager: shreeshd
editor: ''
ms.assetid: 3bd46b97-4b29-47a5-b5ac-ac174dd36760
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/22/2017
ms.author: trinadhk;markgal
ms.openlocfilehash: 442d998d8898dc40ee23ca541d35c340edf64dbd
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2018
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Verwenden der rollenbasierten Zugriffssteuerung zum Verwalten von Azure Backup-Wiederherstellungspunkten
Die rollenbasierte Access Control in Azure (RBAC) ermöglicht eine präzise Zugriffsverwaltung für Azure. Mithilfe von RBAC können Sie Aufgaben in Ihrem Team verteilen und Benutzern nur den Zugriff gewähren, den sie zur Ausführung ihrer Aufgaben benötigen.

> [!IMPORTANT]
> Rollen, die von Azure Backup bereitgestellt werden, sind auf Aktionen beschränkt, die im Azure-Portal oder mit PowerShell-Cmdlets für Recovery Services-Tresore ausgeführt werden können. Aktionen, die auf der Benutzeroberfläche des Azure Backup-Agent-Clients oder der Benutzeroberfläche von System Center Data Protection Manager oder der Benutzeroberfläche von Azure Backup Server ausgeführt werden, werden von diesen Rollen nicht gesteuert.

Azure Backup bietet drei integrierte Rollen, um Vorgänge der Sicherungsverwaltung zu steuern. Erfahren Sie mehr über [integrierte Rollen von Azure RBAC](../role-based-access-control/built-in-roles.md).

* [Mitwirkender für Sicherungen](../role-based-access-control/built-in-roles.md#backup-contributor): Diese Rolle verfügt über alle Berechtigungen zum Erstellen und Verwalten von Sicherungen. Mit dieser Rolle können aber keine Recovery Services-Tresore erstellt, und es kann anderen Personen kein Zugriff gewährt werden. Stellen Sie sich diese Rolle als Administrator der Sicherungsverwaltung vor, der alle Vorgänge der Sicherungsverwaltung ausführen kann.
* [Sicherungsoperator](../role-based-access-control/built-in-roles.md#backup-operator): Diese Rolle verfügt über Berechtigungen für alles, was ein Mitwirkender ausführen kann, außer Entfernen von Sicherungen und Verwalten von Sicherungsrichtlinien. Diese Rolle ist gleichbedeutend mit einem Mitwirkenden, es können damit jedoch keine destruktiven Vorgänge ausgeführt werden, z.B. Beenden einer Sicherung mit Löschung von Daten oder Entfernen die Registrierung von lokalen Ressourcen.
* [Sicherungsleser](../role-based-access-control/built-in-roles.md#backup-reader): Diese Rolle verfügt über Berechtigungen zum Anzeigen aller Vorgänge für die Sicherungsverwaltung. Stellen Sie sich diese Rolle für eine Überwachungsperson vor.

Wenn Sie Ihre eigenen Rollen definieren möchten, um eine noch bessere Kontrolle zu haben, helfen Ihnen die Informationen zum [Erstellen von benutzerdefinierten Rollen](../role-based-access-control/custom-roles.md) in Azure RBAC weiter.



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Zuordnen der integrierten Backup-Rollen zu Aktionen der Sicherungsverwaltung
In der folgenden Tabelle sind die Aktionen der Sicherungsverwaltung und die entsprechende minimale RBAC-Rolle aufgeführt, die zum Ausführen des jeweiligen Vorgangs erforderlich ist.

| Verwaltungsvorgang | Minimal erforderliche RBAC-Rolle |
| --- | --- |
| Erstellen eines Recovery Services-Tresors | Mitwirkender der Ressourcengruppe des Tresors |
| Aktivieren der Sicherung von virtuellen Azure-Computern | Sicherungsoperator für den Tresor, Mitwirkender für virtuelle Computer auf virtuellen Computern |
| Bedarfsgesteuerte Sicherung eines virtuellen Computers | Sicherungsoperator |
| Wiederherstellen eines virtuellen Computers | Sicherungsoperator, Mitwirkender der Ressourcengruppe, in der VM und VNETs bereitgestellt werden sollen |
| Wiederherstellen von Datenträgern, einzelnen Dateien aus VM-Sicherungen | Sicherungsoperator, Mitwirkender für virtuelle Computer auf virtuellen Computern |
| Erstellen einer Sicherungsrichtlinie für Azure-VM-Sicherungen | Mitwirkender für Sicherungen |
| Ändern der Sicherungsrichtlinie der Azure-VM-Sicherungen | Mitwirkender für Sicherungen |
| Löschen der Sicherungsrichtlinie der Azure-VM-Sicherungen | Mitwirkender für Sicherungen |
| Beenden der Sicherung (mit Beibehaltung oder Löschung von Daten) für VM-Sicherungen | Mitwirkender für Sicherungen |
| Registrieren einer lokalen Instanz von Windows Server/Client/SCDPM oder Azure Backup Server | Sicherungsoperator |
| Löschen der registrierten lokalen Instanz von Windows Server/Client/SCDPM oder Azure Backup Server | Mitwirkender für Sicherungen |

## <a name="next-steps"></a>Nächste Schritte
* [Rollenbasierte Zugriffssteuerung](../role-based-access-control/role-assignments-portal.md): Erste Schritte mit RBAC im Azure-Portal.
* Informationen zur Zugriffsverwaltung mit:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure-CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST-API](../role-based-access-control/role-assignments-rest.md)
* [Problembehandlung bei rollenbasierter Zugriffssteuerung:](../role-based-access-control/troubleshooting.md)Sehen Sie sich Vorschläge zur Behebung häufig auftretender Probleme an.
