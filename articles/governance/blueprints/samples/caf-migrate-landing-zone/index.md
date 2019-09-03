---
title: 'Beispiele: CAF-Blaupause für die Migrationslandezone – Übersicht'
description: Übersicht und Architektur des CAF-Blaupausenbeispiels für die Migrationslandezone.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/20/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.custom: fasttrack-new
ms.openlocfilehash: dc30aeae842cf9c3cd39422517ed49773c524f1e
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232844"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-migration-landing-zone-blueprint-sample"></a>Übersicht über das Microsoft Cloud Adoption Framework-Blaupausenbeispiel für die Azure-Migrationslandezone

Bei der Microsoft Cloud Adoption Framework-Blaupause (CAF-Blaupause) für die Azure-Migrationslandezone handelt es sich um einen Infrastruktursatz, der Sie bei der Migration Ihrer ersten Workload sowie bei der CAF-konformen Verwaltung Ihrer Cloudressourcen unterstützt.

Das [CAF-Basisblaupausenbeispiel](../caf-foundation/index.md) baut auf diesem Beispiel auf.

## <a name="architecture"></a>Architecture

Das CAF-Blaupausenbeispiel für die Migrationslandezone stellt grundlegende Infrastrukturressourcen in Azure bereit, die von Organisationen verwendet werden können, um ihr Abonnement für die Migration virtueller Computer vorzubereiten. Darüber hinaus unterstützt es Sie bei der Einrichtung der Governancekontrollen, die für die Verwaltung Ihrer Cloudressourcen erforderlich sind. In diesem Beispiel werden Ressourcen, Richtlinien und Vorlagen bereitgestellt und erzwungen, die Organisationen einen problemlosen Einstieg in Azure ermöglichen.

![CAF-Migrationslandezone: Die Abbildung veranschaulicht, was im Rahmen des CAF-Leitfadens für die erste Landezone installiert wird. ](../../media/caf-blueprints/caf-migration-landing-zone-architecture.png)

Diese Umgebung umfasst mehrere Azure-Dienste, die für die Bereitstellung einer sicheren, vollständig überwachten und unternehmensgerechten Governancelösung genutzt werden. Diese Umgebung besteht aus den folgenden Komponenten:

- Eine [Azure Key Vault](../../../../key-vault/key-vault-whatis.md)-Instanz zum Hosten von Geheimnissen für die Zertifikate, Schlüssel und Geheimnisse, die in der Umgebung für gemeinsam genutzte Dienste bereitgestellt werden.
- [Log Analytics](../../../../azure-monitor/overview.md) wird bereitgestellt, um sicherzustellen, dass alle Aktionen und Dienste an einem zentralen Ort protokolliert werden, sobald Sie Ihre Migration starten.
- [Azure Security Center](../../../../security-center/security-center-intro.md) (Standardversion) schützt Ihre migrierten Workloads vor Bedrohungen.
- [Azure Virtual Network](../../../../virtual-network/virtual-networks-overview.md) bietet ein isoliertes Netzwerk sowie Subnetze für Ihren virtuellen Computer.
- [Azure Migrate-Projekt](../../../..//migrate/migrate-overview.md) dient zur Ermittlung und Bewertung. Wir fügen Tools für die Serverbewertung, Servermigration, Datenbankbewertung und Datenbankmigration hinzu.  


Für alle diese Elemente werden die bewährten Methoden befolgt, die unter [Azure Architecture Center: Referenzarchitekturen](/azure/architecture/reference-architectures/) veröffentlicht wurden.

> [!NOTE]
> Die CAF-Migrationsblaupause richtet eine Landezone für Ihre Workloads ein. Die Bewertung und Migration Ihrer virtuellen Computer/Datenbanken muss dann auf der Grundlage dieser Basisarchitektur durchgeführt werden.

Weitere Informationen finden Sie unter [Cloudmigration im Cloud Adoption Framework](/azure/architecture/cloud-adoption/migrate/).

## <a name="next-steps"></a>Nächste Schritte

Sie sind nun mit der Übersicht und Architektur des CAF-Blaupausenbeispiels für die Migrationslandezone vertraut.

> [!div class="nextstepaction"]
>  [Deploy the Microsoft Cloud Adoption Framework for Azure migrate landing zone blueprint sample](./deploy.md) (Bereitstellen des Microsoft Cloud Adoption Framework-Blaupausenbeispiels für die Azure-Migrationslandezone)

Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../../how-to/update-existing-assignments.md).