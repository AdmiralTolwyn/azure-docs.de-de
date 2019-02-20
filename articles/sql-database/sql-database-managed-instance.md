---
title: Azure SQL-Datenbank – Advanced Data Security | Microsoft-Dokumentation
description: In diesem Thema wird die Advanced Data Security einer Azure SQL-Datenbank und deren Verwendung beschrieben sowie erläutert, wie sie sich von einer Einzeldatenbank oder einer in einem Pool zusammengefassten in Azure SQL-Datenbank unterscheidet.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, vanto
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: d8959e25280a9d1dd62549c698f7b2b6b98d6154
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964150"
---
# <a name="use-sql-database-advanced-data-security-with-virtual-networks-and-near-100-compatibility"></a>Verwenden der Advanced Data Security einer Azure SQL-Datenbank mit virtuellen Netzwerken und nahezu 100iger % Kompatibilität

Eine verwaltete Instanz ist eine neue Bereitstellungsoption von Azure SQL-Datenbank, die für nahezu uneingeschränkte Kompatibilität mit der aktuellen lokalen SQL Server-Datenbank-Engine (Enterprise Edition) sorgt. Darüber hinaus bietet sie eine native Implementierung eines [virtuellen Netzwerks (VNET)](../virtual-network/virtual-networks-overview.md) zur Behebung allgemeiner Sicherheitsrisiken sowie ein vorteilhaftes [Geschäftsmodell](https://azure.microsoft.com/pricing/details/sql-database/) für Kunden mit lokaler SQL Server-Instanz. Mit dem Bereitstellungsmodell für die verwaltete Instanz können bestehende SQL Server-Kunden ihre lokalen Anwendungen mit minimalen Änderungen an den Anwendungen und Datenbanken per Lift & Shift zur Cloud migrieren. Gleichzeitig behält die Bereitstellungsoption für die verwaltete Instanz (automatisches Patchen und automatische Versionsupdates, [automatische Sicherungen](sql-database-automated-backups.md), [Hochverfügbarkeit](sql-database-high-availability.md)) bei, die den Verwaltungsaufwand und die Gesamtkosten drastisch reduzieren.

> [!IMPORTANT]
> Eine Liste der Regionen, in denen die Bereitstellungsoption für eine verwaltete Instanzen derzeit verfügbar sind, finden Sie unter [Unterstützte Regionen](sql-database-managed-instance-resource-limits.md#supported-regions).

In der folgenden Abbildung sind die wichtigsten Features der verwalteten Instanz dargestellt:

![Wichtigste Features](./media/sql-database-managed-instance/key-features.png)

Das Bereitstellungsmodell für die verwaltete Instanz ist für Kunden konzipiert, die eine große Anzahl von Apps aus einer selbst erstellten oder über ISVs bereitgestellten lokalen Umgebung oder IaaS-Umgebung mit möglichst geringem Migrationsaufwand in eine vollständig verwaltete PaaS-Cloudumgebung migrieren möchten. Unter Verwendung des vollständig automatisierten [Data Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) in Azure können Kunden ihren lokalen SQL Server per Lift & Shift zu einer verwalteten Instanz migrieren, die Kompatibilität mit dem lokalen SQL Server und vollständige Isolation von Kundeninstanzen mit nativer VNET-Unterstützung bietet.  Mit Software Assurance können Sie die vorhandenen Lizenzen der Kunden mit dem [Azure-Hybridvorteil für SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) zu ermäßigten Preisen für eine verwaltete Instanz austauschen.  Eine verwaltete Instanz ist das beste Migrationsziel in der Cloud für SQL Server-Instanzen, die hohe Sicherheit und eine umfangreiche Programmieroberfläche erfordern.

Das Bereitstellungsmodell für die verwaltete Instanz strebt durch einen gestaffelten Releaseplan eine nahezu 100%ige Oberflächenkompatibilität mit dem neuesten lokalen SQL Server-Release an.

Anhaltspunkte für die Entscheidung zwischen den verschiedenen Bereitstellungsoptionen für die Azure SQL-Datenbank zwischen Einzeldatenbank, einer im Pool zusammengefassten Datenbank, einer verwalteten Instanz und SQL Server auf einem virtuellen Computer finden Sie unter [Auswählen der richtigen Version von SQL Server in Azure](sql-database-paas-vs-sql-server-iaas.md).

## <a name="key-features-and-capabilities"></a>Wichtige Features und Funktionen

Eine verwaltete Instanz kombiniert die besten Features, die sowohl in Azure SQL-Datenbank als auch SQL Server-Datenbank-Engine verfügbar sind.

> [!IMPORTANT]
> Eine verwaltete Instanz wird mit allen Features der neuesten Version von SQL Server ausgeführt – einschließlich Onlinevorgängen, automatischer Plankorrekturen und anderer Leistungsverbesserungen für Unternehmen. Einen Vergleich der verfügbaren Features finden Sie unter [Funktionsvergleich: Azure SQL-Datenbank und SQL Server](sql-database-features.md).

| **PaaS-Vorteile** | **Geschäftskontinuität** |
| --- | --- |
|Kein Kauf und keine Verwaltung von Hardware <br>Kein zusätzlicher Aufwand für die Verwaltung der zugrunde liegenden Infrastruktur <br>Schnelle Bereitstellung und Dienstskalierung <br>Automatische Patches und Versionsupgrades <br>Integration in andere PaaS-Datendienste |Betriebszeit-SLA von 99,99 %  <br>Integrierte [Hochverfügbarkeit](sql-database-high-availability.md) <br>Schutz der Daten durch [automatische Sicherungen](sql-database-automated-backups.md) <br>Vom Kunden konfigurierbare Aufbewahrungsdauer für Sicherungen <br>Vom Benutzer initiierte [Sicherungen](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>Funktion für [Point-in-Time-Datenbankwiederherstellung](sql-database-recovery-using-backups.md#point-in-time-restore) |
|**Sicherheit und Konformität** | **Verwaltung**|
|Isolierte Umgebung ([VNET-Integration](sql-database-managed-instance-connectivity-architecture.md), Dienst mit einzelnem Mandanten, dedizierte Compute- und Speicherressourcen) <br>[Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure AD-Authentifizierung](sql-database-aad-authentication.md), Unterstützung für einmaliges Anmelden <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD-Anmeldungen</a> (**öffentliche Vorschau**) <br>Gleiche Kompatibilitätsstandards wie für Azure SQL-Datenbank <br>[SQL-Überwachung](sql-database-managed-instance-auditing.md) <br>[Bedrohungserkennung](sql-database-managed-instance-threat-detection.md) |Azure Resource Manager-API zur Automatisierung der Dienstbereitstellung und -skalierung <br>Funktionen des Azure-Portals für die manuelle Dienstbereitstellung und -skalierung <br>Data Migration Service

Die wichtigsten Features der verwalteten Instanz sind in der folgenden Tabelle angegeben:

|Feature | BESCHREIBUNG|
|---|---|
| SQL Server-Version/-Build | SQL Server-Datenbank-Engine (letzte stabile Version) |
| Verwaltete automatisierte Sicherungen | Ja |
| Integrierte Überwachung und Metriken von Instanzen und Datenbanken | Ja |
| Automatische Softwarepatches | Ja |
| Neueste Features der Datenbank-Engine | Ja |
| Anzahl der Datendateien (ROWS) pro Datenbank | Mehrere |
| Anzahl der Protokolldateien (LOG) pro Datenbank | 1 |
| VNET – Azure Resource Manager-Bereitstellung | Ja |
| VNET – Klassisches Bereitstellungsmodell | Nein  |
| Portal-Unterstützung | Ja|
| Integrierter Integrationsdienst (SSIS) | Nein – SSIS ist Teil von [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Integrierter Analysedienst (SSAS) | Nein – SSAS ist ein separater [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| Integrierter Berichterstellungsdienst (SSRS) | Nein – Power BI oder SSRS IaaS verwenden |
|||

## <a name="vcore-based-purchasing-model"></a>Auf virtuellen Kernen basierendes Erwerbsmodell

Das [V-Kern-basierte Kaufmodell](sql-database-service-tiers-vcore.md) für verwaltete Instanzen bietet Ihnen mehr Flexibilität, Kontrolle und Transparenz sowie eine unkomplizierte Möglichkeit, Ihre lokalen Workloadanforderungen in der Cloud zu realisieren. Mit diesem Modell können Sie Computeressourcen, Arbeitsspeicher und Speicher entsprechend Ihren Workloadanforderungen ändern. Das V-Kern-Modell ermöglicht mit dem [Azure-Hybridvorteil für SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) zudem Einsparungen von bis zu 30 Prozent.

Beim V-Kern-Modell können Sie verschiedene Hardwaregenerationen auswählen.

- **Gen4**: Logische CPUs basierend auf Intel-Prozessoren vom Typ E5-2673 v3 (Haswell) mit 2,4 GHz, angefügte SSD, physische Kerne, 7 GB RAM pro Kern und Computegrößen zwischen 8 und 24 virtuellen Kernen.
- **Gen5**: Logische CPUs basierend auf Intel-Prozessoren vom Typ E5-2673 v4 (Broadwell) mit 2,3 GHz,schnelle NVMe SSD, logischer Kern mit Hyperthreading und Computegrößen zwischen 8 und 80 Kernen.

Weitere Informationen zu den Unterschieden zwischen Hardwaregenerationen finden Sie unter [Ressourceneinschränkungen für verwaltete Instanzen](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).

## <a name="managed-instance-service-tiers"></a>Dienstebenen für eine verwaltete Instanz

Eine verwaltete Instanz ist in zwei Ebenen verfügbar:

- **Universell**: Entwickelt für Anwendungen mit typischen Leistungs- und E/A-Latenzanforderungen.
- **Unternehmenskritisch**: Entwickelt für Anwendungen mit niedrigen E/A-Latenzanforderungen und minimalen Auswirkungen auf zugrunde liegende Wartungsvorgänge für die Workload.

Beide Tarife garantieren eine Verfügbarkeit von 99,99% und ermöglichen es Ihnen, die Speichergröße und Computekapazität unabhängig voneinander auszuwählen. Weitere Informationen zur Hochverfügbarkeitsarchitektur von Azure SQL-Datenbank finden Sie unter [Hochverfügbarkeit und Azure SQL-Datenbank](sql-database-high-availability.md).

### <a name="general-purpose-service-tier"></a>Universelle Dienstebene

Folgende wichtige Merkmale kennzeichnen die universelle Dienstebene:

- Entwurf für den Großteil der Geschäftsanwendungen mit typischen Leistungsanforderungen
- Azure Blob Storage mit hoher Leistung (8 TB)
- Integrierte [Hochverfügbarkeit](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) basierend auf zuverlässigem Azure Blob Storage und [Azure Service Fabric](../service-fabric/service-fabric-overview.md)

Weitere Informationen finden Sie unter [Speicherebene im Tarif „Universell“](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) und [Bewährte Methoden und Aspekte der Speicherleistung für verwaltete Instanz (Universell)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

Weitere Informationen zu den Unterschieden zwischen Diensttarifen finden Sie unter [Ressourceneinschränkungen für verwaltete Instanzen](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Diensttarif „Unternehmenskritisch“

Die unternehmenskritische Dienstebene wurde für Anwendungen mit hohen E/A-Anforderungen konzipiert. Sie bietet höchste Resilienz gegenüber Ausfällen durch mehrere isolierte Replikate.

Folgende wichtige Merkmale kennzeichnen die Dienstebene „Unternehmenskritisch“:

- Konzipiert für Geschäftsanwendungen mit höchster Leistung und Hochverfügbarkeitsanforderungen
- Enthält einen äußerst schnellen lokalen SSD-Speicher (bis zu 1 TB bei Gen 4 und bis zu 4 TB bei Gen 5)
- Integrierte [Hochverfügbarkeit](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) basierend auf [Always On-Verfügbarkeitsgruppen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) und [Azure Service Fabric](../service-fabric/service-fabric-overview.md)
- Integriertes zusätzliches [Datenbankreplikat mit Schreibschutz](sql-database-read-scale-out.md), das für Berichte und andere schreibgeschützte Workloads verwendet werden kann
- [In-Memory-OLTP](sql-database-in-memory.md), das für Workloads mit hohen Leistungsanforderungen verwendet werden kann  

Weitere Informationen zu den Unterschieden zwischen Diensttarifen finden Sie unter [Ressourceneinschränkungen für verwaltete Instanzen](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="advanced-security-and-compliance"></a>Erweiterte Sicherheit und Konformität

Eine Bereitstellungsoption für die verwaltete Instanz kombiniert erweiterte Sicherheitsfeatures, die von der Azure-Cloud und SQL Server-Datenbank-Engine bereitgestellt werden.

### <a name="managed-instance-security-isolation"></a>Sicherheitsisolation bei verwalteten Instanzen

Eine verwaltete Instanz bietet zusätzliche Sicherheitsisolation von anderen Mandanten in der Azure-Cloud. Die Sicherheitsisolation umfasst Folgendes:

- [Native Implementierung von und Konnektivität mit virtuellen Netzwerken](sql-database-managed-instance-connectivity-architecture.md) in der lokalen Umgebung unter Verwendung von Azure ExpressRoute oder VPN Gateway.
- Der SQL-Endpunkt wird nur über eine private IP-Adresse verfügbar gemacht, sodass sichere Verbindungen von privaten Azure-Netzwerken oder hybriden Netzwerken möglich sind.
- Einzelner Mandant mit dedizierter zugrunde liegender Infrastruktur (Compute, Speicher).

Das folgende Diagramm zeigt verschiedene Konnektivitätsoptionen für Ihre Anwendungen:

![Hochverfügbarkeit](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Weitere Informationen zur VNET-Integration und Durchsetzung von Netzwerkrichtlinien auf Subnetzebene finden Sie unter [VNET-Architektur für eine verwaltete Instanz](sql-database-managed-instance-connectivity-architecture.md) und [Herstellen einer Verbindung zwischen einer Anwendung und einer verwalteten Instanz](sql-database-managed-instance-connect-app.md).

> [!IMPORTANT]
> Platzieren Sie mehrere verwaltete Instanzen im gleichen Subnetz, wo immer dies durch Ihre Sicherheitsanforderungen erlaubt ist, da Sie dadurch zusätzliche Vorteile erzielen können. Das Zusammenstellen von Instanzen im gleichen Subnetz wird die Wartung der Netzwerkinfrastruktur erheblich vereinfachen und die Zeit für die Instanzbereitstellung verkürzen, da eine lange Bereitstellungsdauer mit den Kosten für die Bereitstellung der ersten verwalteten Instanz in einem Subnetz verbunden ist.

### <a name="azure-sql-database-security-features"></a>Sicherheitsfeatures für Azure SQL-Datenbank

Azure SQL-Datenbank bietet eine Reihe von erweiterten Sicherheitsfeatures, die zum Schutz Ihrer Daten verwendet werden können.

- Die [Überwachung verwalteter Instanzen](sql-database-managed-instance-auditing.md) verfolgt Datenbankereignisse nach und schreibt sie in eine Überwachungsprotokolldatei in Ihrem Azure-Speicherkonto. Die Überwachung kann dabei helfen, gesetzliche Bestimmungen einzuhalten, die Datenbankaktivität zu verstehen und Einblicke in Abweichungen und Anomalien zu erhalten, die auf geschäftliche Probleme oder mutmaßliche Sicherheitsverstöße hinweisen können.
- Datenverschlüsselung in Aktion: Eine verwaltete Instanz schützt Ihre Daten durch die Verschlüsselung für Daten während der Übertragung mit Transport Layer Security. Neben Transport Layer Security bietet eine Bereitstellungsoption für die verwaltete Instanz mit [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) einen Schutz für sensible Daten bei der Übertragung, im Ruhezustand und während der Abfrageverarbeitung. Always Encrypted ist das branchenweit erste System, das einen beispiellosen Schutz von Daten gegen Sicherheitsverletzungen wie dem Diebstahl wichtiger Daten bietet. Beispielsweise werden mit Always Encrypted Kreditkartennummern immer verschlüsselt in der Datenbank gespeichert, selbst während der Abfrageverarbeitung. Gleichzeitig wird autorisierten Mitarbeitern oder Anwendungen, die diese Daten verarbeiten müssen, die Entschlüsselung zum Zeitpunkt der Verwendung erlaubt.
- Mit der [Bedrohungserkennung](sql-database-managed-instance-threat-detection.md) von wird die [Überwachung](sql-database-managed-instance-auditing.md) vervollständigt, indem der Dienst um eine zusätzliche Security Intelligence-Ebene erweitert wird, die ungewöhnliche und eventuell schädliche Versuche, auf Datenbanken zuzugreifen oder diese zu missbrauchen, erkennt. Sie werden vor verdächtigen Aktivitäten, potenziellen Sicherheitsrisiken sowie Angriffen mit Einschleusung von SQL-Befehlen und anomalen Datenbankzugriffsmustern gewarnt. Warnungen der Bedrohungserkennung können in [Azure Security Center](https://azure.microsoft.com/services/security-center/) angezeigt werden und bieten Detailinformationen zu verdächtigen Aktivitäten sowie Empfehlungen, wie die Bedrohung untersucht und abgewendet werden kann.  
- Die [dynamische Datenmaskierung](/sql/relational-databases/security/dynamic-data-masking) schränkt die Offenlegung sensibler Daten ein, indem sie für nicht berechtigte Benutzer maskiert werden. Mit der dynamischen Datenmaskierung können Sie mit minimalen Auswirkungen auf die Anwendungsschicht festlegen, wie viel von den sensiblen Daten offengelegt werden soll. Dies trägt zur Verhinderung des unbefugten Zugriffs auf sensible Daten bei. Es handelt sich um eine richtlinienbasierte Sicherheitsfunktion, die die sensiblen Daten im Resultset einer Abfrage in festgelegten Datenbankfeldern ausblendet, ohne dass die Daten in der Datenbank geändert werden.
- Bei der [Sicherheit auf Zeilenebene](/sql/relational-databases/security/row-level-security) können Sie den Zugriff auf Zeilen in einer Datenbanktabelle basierend auf den Merkmalen des Benutzers steuern, der eine Abfrage ausführt (z.B. Gruppenmitgliedschaft oder Ausführungskontext). Die Sicherheit auf Zeilenebene (Row-Level Security, RLS) vereinfacht das Entwerfen und Programmieren der Sicherheit in Ihrer Anwendung. Mit RLS können Sie den Zugriff auf Datenzeilen einschränken. So können Sie beispielsweise sicherstellen, dass Mitarbeiter nur auf die Datenzeilen zugreifen können, die für ihre Abteilung relevant sind, oder den Datenzugriff ausschließlich auf die relevanten Daten beschränken.
- [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) verschlüsselt Datendateien einer verwalteten Instanz. Dies wird als Verschlüsselung ruhender Daten bezeichnet. TDE führt die E/A-Verschlüsselung und -Entschlüsselung der Daten- und Protokolldateien in Echtzeit durch. Die Verschlüsselung verwendet einen Datenbank-Verschlüsselungsschlüssel (DEK), der im Startdatensatz der Datenbank gespeichert wird und während der Wiederherstellung zur Verfügung steht. Sie können alle Ihre Datenbanken in der verwalteten Instanz mit transparenter Datenverschlüsselung schützen. TDE ist die bewährte SQL Server-Verschlüsselungstechnologie für ruhende Daten, die für viele Konformitätsstandards zum Schutz vor Diebstahl von Speichermedien erforderlich ist.

Die Migration einer verschlüsselten Datenbank zur verwalteten Instanz wird über den Azure Database Migration Service (DMS) oder die native Wiederherstellung unterstützt. Wenn Sie planen, eine verschlüsselte Datenbank mittels nativer Wiederherstellung zu migrieren, ist die Migration des vorhandenen TDE-Zertifikats vom lokalen SQL-Server oder von SQL Server in einen virtuellen Computer zur verwalteten Instanz erforderlich. Weitere Informationen zu den Migrationsoptionen finden Sie unter [Migration einer SQL Server-Instanz zu einer verwalteten Instanz](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Azure Active Directory-Integration

Die Bereitstellungsoption für die verwaltete Instanz unterstützt herkömmliche SQL Server-Datenbank-Engine-Anmeldungen und in Azure Active Directory (AAD) integrierte Anmeldungen. AAD-Anmeldungen (**öffentliche Vorschau**) sind die Azure-Cloudversion von lokalen Datenbankanmeldungen, die Sie in Ihrer lokalen Umgebung verwenden. AAD-Anmeldungen ermöglichen Ihnen das Angeben von Benutzern und Gruppen von Ihrem Azure Active Directory-Mandanten als Prinzipale, die auch tatsächlich im Bereich der Instanz liegen. So können Sie jeden Vorgang auf Instanzebene ausführen – auch datenbankübergreifende Abfragen innerhalb derselben verwalteten Instanz.

Eine neue Syntax wird eingeführt, um AAD-Anmeldungen zu erstellen (**öffentliche Vorschau**), **FROM EXTERNAL PROVIDER**. Weitere Informationen zur Syntax finden Sie unter <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>, und lesen Sie auch den Artikel [Bereitstellen eines Azure Active Directory-Administrators für Ihre verwaltete SQL-Datenbank-Instanz](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-Integration und Multi-Factor Authentication

Die Bereitstellungsoption für die verwaltete Instanz ermöglicht über die [Azure Active Directory-Integration](sql-database-aad-authentication.md) eine zentrale Verwaltung von Identitäten der Datenbankbenutzer und anderer Microsoft-Dienste. Diese Funktion vereinfacht die Berechtigungsverwaltung und erhöht die Sicherheit. Azure Active Directory unterstützt [Multi-Factor Authentication](sql-database-ssms-mfa-authentication-configure.md) (MFA), um die Daten- und Anwendungssicherheit zu erhöhen, während gleichzeitig einmaliges Anmelden unterstützt wird.

### <a name="authentication"></a>Authentication

Die Authentifizierung einer verwalteten Instanz bezieht sich darauf, wie Benutzer ihre Identität beim Herstellen der Verbindung mit der Datenbank nachweisen. SQL-Datenbank unterstützt zwei Arten der Authentifizierung:  

- **SQL-Authentifizierung**:

  Diese Authentifizierungsmethode verwendet einen Benutzernamen und ein Kennwort.
- **Azure Active Directory-Authentifizierung**:

  Diese Authentifizierungsmethode verwendet von Azure Active Directory verwaltete Identitäten und wird für verwaltete und integrierte Domänen unterstützt. Verwenden Sie immer die Active Directory-Authentifizierung (integrierte Sicherheit), [sofern dies möglich ist](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

### <a name="authorization"></a>Autorisierung

Autorisierung bezieht sich darauf, welche Aufgaben ein Benutzer innerhalb einer Azure SQL-Datenbank ausführen kann. Dies wird durch datenbankbezogene Rollenmitgliedschaften und Objektebenenberechtigungen Ihres Benutzerkontos gesteuert. Eine verwaltete Instanz enthält die gleichen Autorisierungsfunktionen wie SQL Server 2017.

## <a name="database-migration"></a>Datenbankmigration

Die Bereitstellungsoption für die verwaltete Instanz zielt auf Benutzerszenarien ab, die eine Massenmigration der Datenbank von lokalen oder IaaS-Datenbankimplementierungen erfordern. Verwaltete Instanzen unterstützen mehrere Optionen zur Datenbankmigration:

### <a name="back-up-and-restore"></a>Sichern und Wiederherstellen  

Beim Migrationsansatz werden SQL-Sicherungen in Azure Blob Storage genutzt. In Azure Blob Storage gespeicherte Sicherungen können mithilfe des [T-SQL RESTORE-Befehls](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current) direkt in der verwalteten Instanz gespeichert werden.

- Eine Schnellstartanleitung zum Wiederherstellen der Standardsicherungsdatei für die Wide World Importers-Datenbank finden Sie unter [Wiederherstellen einer Datenbanksicherung in einer verwalteten Azure SQL-Datenbank-Instanz](sql-database-managed-instance-get-started-restore.md). In diesem Schnellstart wird gezeigt, wie eine Sicherungsdatei in den Azure Blob Storage hochgeladen und mit einem SAS-Schlüssel (Shared Access Signature) geschützt wird.
- Weitere Informationen zur Wiederherstellung über eine URL finden Sie unter [Native RESTORE FROM URL-Option](sql-database-managed-instance-migrate.md#native-restore-from-url).

> [!IMPORTANT]
> Sicherungen von einer verwalteten Instanz können nur in einer anderen verwalteten Instanz wiederhergestellt werden. Sie können nicht auf einem lokalen SQL Server oder in einer Einzeldatenbank/einem Pool für elastische Datenbanken wiederhergestellt werden.

### <a name="data-migration-service"></a>Data Migration Service

Azure Database Migration Service ist ein vollständig verwalteter Dienst, der die nahtlose Migration von mehreren Datenbankquellen zu Azure-Datenplattformen mit minimaler Downtime ermöglicht. Dieser Dienst optimiert die Aufgaben, die erforderlich sind, um bestehende Drittanbieter- und SQL Server-Datenbanken nach Azure SQL-Datenbank (Einzeldatenbank, Pools für elastische Datenbanken und Instanzdatenbanken in einer verwalteten Instanz) und SQL Server auf einer Azure-VM zu verschieben. Siehe [Migrieren von SQL Server zu einer verwalteten Azure SQL-Datenbank-Instanz](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Unterstützte SQL-Features

Die Bereitstellungsoption für die verwaltete Instanz strebt für verwaltete Instanzen in mehreren Stufen eine nahezu 100%ige Oberflächenkompatibilität mit der lokalen SQL Server-Version an. Eine Liste der Funktionen und einen Funktionsvergleich finden Sie unter [Funktionsvergleich: Azure SQL-Datenbank und SQL Server](sql-database-features.md) und eine Liste der T-SQL-Unterschiede in verwalteten Instanzen im Vergleich zu SQL Server finden Sie unter [T-SQL-Unterschiede zwischen einer verwalteten Azure SQL-Datenbank-Instanz und SQL Server](sql-database-managed-instance-transact-sql-information.md).

Die Bereitstellungsoption für die verwaltete Instanz unterstützt die Abwärtskompatibilität mit SQL 2008-Datenbanken. Die direkte Migration von SQL 2005-Datenbankservern wird unterstützt, der Kompatibilitätsgrad für migrierte SQL 2005-Datenbanken wird auf SQL 2008 aktualisiert.
  
In der folgenden Abbildung ist die Oberflächenkompatibilität in einer verwalteten Instanz aufgeführt:  

![Migration](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>Hauptunterschiede zwischen einer lokalen SQL Server-Instanz und einer verwalteten Instanz

Die Bereitstellungsoption für die verwaltete Instanz ist in der Cloud immer auf dem neuesten Stand, d.h., einige Features in der lokalen SQL Server-Instanz sind möglicherweise entweder veraltet oder außer Kraft gesetzt oder weisen Alternativen auf. In spezifischen Fällen müssen Tools erkennen, dass ein bestimmtes Feature auf leicht abweichende Weise ausgeführt wird oder dass ein Dienst in einer Umgebung nicht ausgeführt wird, die Sie nicht vollständig steuern können:

- Hochverfügbarkeit ist anhand einer ähnlichen Technologie wie [AlwaysOn-Verfügbarkeitsgruppen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) integriert und vorkonfiguriert.
- Automatisierte Sicherungen und Point-in-Time-Wiederherstellung. Kunden können `copy-only`-Sicherungen initiieren, die die automatische Sicherungskette nicht beeinträchtigen.
- In einer verwalteten Instanz können keine vollständigen physischen Pfade angegeben werden. Daher müssen alle entsprechenden Szenarien unterschiedlich unterstützt werden: RESTORE DB unterstützt WITH MOVE nicht; CREATE DB lässt keine physischen Pfade zu; BULK INSERT kann nur mit Azure-Blobs ausgeführt werden usw.
- Eine verwaltete Instanz unterstützt die [Azure AD-Authentifizierung](sql-database-aad-authentication.md) als Cloudalternative zur Windows-Authentifizierung.
- Eine verwaltete Instanz verwaltet automatisch XTP-Dateigruppen und -Dateien für Datenbanken mit In-Memory-OLTP-Objekten.
- Eine verwaltete Instanz unterstützt SQL Server Integration Services (SSIS) und kann den SSIS-Katalog (SSISDB) hosten, in dem SSIS-Pakete gespeichert werden, sie wird jedoch in einer verwalteten Azure SSIS Integration Runtime (IR) in Azure Data Factory (ADF) ausgeführt. Siehe dazu [Erstellen der Azure SSIS Integration Runtime in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Informationen zum Vergleich der SSIS-Features in SQL-Datenbank finden Sie unter [Vergleich zwischen Azure SQL-Datenbank-Einzeldatenbanken/Pools für elastische Datenbanken und einer verwalteten Instanz](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance).

### <a name="managed-instance-administration-features"></a>Features zur Verwaltung einer verwalteten Instanz

Dank der Bereitstellungsoption für die verwaltete Instanz muss ein Systemadministrator weniger Zeit für Verwaltungsaufgaben aufwenden, da der SQL-Datenbank-Dienst diese für ihn ausführt oder diese Aufgaben vereinfacht. Beispiele: [Installation und Patches des Betriebssystems und des RDBMS](sql-database-high-availability.md), [dynamische Größenänderung und Konfiguration von Instanzen](sql-database-single-database-scale.md), [Sicherungen](sql-database-automated-backups.md), [Datenbankreplikation](replication-with-sql-database-managed-instance.md) (einschließlich Systemdatenbanken), [Konfiguration der Hochverfügbarkeit](sql-database-high-availability.md) und der Integrität und [Leistungsüberwachung](../azure-monitor/insights/azure-sql.md) der Datenströme.

> [!IMPORTANT]
> Eine Liste der unterstützten, teilweise unterstützten und nicht unterstützten Features finden Sie unter [Features von Azure SQL-Datenbank](sql-database-features.md). Eine Liste der T-SQL-Unterschiede in verwalteten Instanzen im Vergleich zu SQL Server finden Sie unter [T-SQL-Unterschiede zwischen einer verwalteten Azure SQL-Datenbank-Instanz und SQL Server](sql-database-managed-instance-transact-sql-information.md).

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Programmgesteuertes Identifizieren einer verwalteten Instanz

In der folgenden Tabelle sind verschiedene Eigenschaften aufgeführt, auf die über Transact-SQL zugegriffen werden kann und über die Sie ermitteln können, ob Ihre Anwendung mit der verwalteten Instanz ausgeführt wird, und mit denen Sie wichtige Eigenschaften abrufen können.

|Eigenschaft|Wert|Comment|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation|Dieser Wert ist mit dem Wert in SQL-Datenbank identisch.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|Dieser Wert ist mit dem Wert in SQL-Datenbank identisch.|
|`SERVERPROPERTY('EngineEdition')`|8|Durch diesen Wert wird eine verwaltete Instanz eindeutig identifiziert.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Vollständiger DNS-Instanzname im folgenden Format: `<instanceName>`.`<dnsPrefix>`.database.windows.net, wobei `<instanceName>` der vom Kunden angegebene Name und `<dnsPrefix>` der automatisch generierte Teil des Namens ist, der die Eindeutigkeit des globalen DNS-Namens gewährleistet (z.B. „wcus17662feb9ce98“)|Beispiel: meine-verwaltete-instanz.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Nächste Schritte

- Im [Schnellstarthandbuch](sql-database-managed-instance-get-started.md) erfahren Sie, wie Sie Ihre erste verwaltete Instanz erstellen.
- Eine Liste der Features und einen Funktionsvergleich finden Sie unter [Allgemeine SQL-Features](sql-database-features.md).
- Weitere Informationen zur VNET-Konfiguration finden Sie unter [Konfigurieren eines VNET für eine verwaltete Azure SQL-Datenbank-Instanz](sql-database-managed-instance-connectivity-architecture.md).
- Eine Schnellstartanleitung, in der eine verwaltete Instanz erstellt und eine Datenbank von einer Sicherungsdatei wiederhergestellt wird, finden Sie unter [Erstellen einer verwalteten Azure SQL-Datenbank-Instanz](sql-database-managed-instance-get-started.md).
- Ein Tutorial mit Verwendung des Azure Database Migration Service (DMS) für die Migration finden Sie unter [Migrieren von SQL Server zu einer verwalteten Azure SQL-Datenbank-Instanz](../dms/tutorial-sql-server-to-managed-instance.md).
- Informationen zur erweiterten Überwachung der Datenbankleistung verwalteter Instanzen mit integrierten Problembehandlungsfunktionen finden Sie unter [Überwachen von Azure SQL-Datenbank mithilfe von Azure SQL-Analyse (Vorschauversion)](../azure-monitor/insights/azure-sql.md).
- Preisinformationen finden Sie unter [Preise – verwaltete Azure SQL-Datenbank-Instanzen ](https://azure.microsoft.com/pricing/details/sql-database/managed/).
