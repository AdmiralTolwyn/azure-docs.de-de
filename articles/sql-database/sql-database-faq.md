---
title: "Häufig gestellte Fragen zu Azure SQL-Datenbank | Microsoft-Dokumentation"
description: "Antworten auf häufig gestellte Fragen von Kunden zu Cloud-Datenbanken, zur Azure SQL-Datenbank, das Microsoft Managementsystem für relationale Datenbanken (RDBMS) und Database as a Service in der Cloud."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 1da12abc-0646-43ba-b564-e3b049a6487f
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 02/12/2018
ms.author: carlrab
ms.openlocfilehash: 4efa053afd26bde208441c4b841c5d02142a2d18
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2018
---
# <a name="sql-database-faq"></a>SQL-Datenbank – Häufig gestellte Fragen

## <a name="what-is-the-current-version-of-sql-database"></a>Wie lautet die aktuelle Version von SQL-Datenbank?
Die aktuelle Version von SQL-Datenbank ist V12. Version V11 wurde eingestellt.

## <a name="what-is-the-sla-for-sql-database"></a>Wie lautet die Vereinbarung zum Servicelevel (SLA) für SQL-Datenbank?
Für die Tarife „Basic“, „Standard“ und „Premium“ von Microsoft Azure SQL-Datenbank (Einzeldatenbank oder elastische Datenbank) garantieren wir eine Konnektivität von mindestens 99,99 Prozent zwischen der Datenbank und unserem Internetgateway. Weitere Informationen finden Sie unter [SLA](http://azure.microsoft.com/support/legal/sla/).

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>Wie setze ich das Kennwort für den Serveradministrator zurück?
Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Computer mit SQL Server**, wählen Sie in der Liste den Server aus, und klicken Sie auf **Kennwort zurücksetzen**.

## <a name="how-do-i-manage-databases-and-logins"></a>Wie verwalte ich Datenbanken und Anmeldungen?
Informationen hierzu finden Sie unter [Steuern und Gewähren von Datenbankzugriff](sql-database-manage-logins.md).

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>Wie stelle ich sicher, dass nur autorisierte IP-Adressen auf einen Server zugreifen dürfen?
Siehe [Konfigurieren der Firewalleinstellungen für Azure SQL-Datenbank](sql-database-configure-firewall-settings.md).

## <a name="how-does-the-usage-of-sql-database-show-up-on-my-bill"></a>Wie wird die Nutzung der SQL-Datenbank auf meiner Rechnung ausgewiesen?
Die Abrechnung der SQL-Datenbank erfolgt nach einer vorhersehbaren Stundenpauschale basierend auf Dienst- und Leistungsebene für Einzeldatenbanken oder eDTUs pro Pool für elastische Datenbanken. Der tatsächliche Verbrauch wird stündlich berechnet und anteilig aufgeführt, sodass auf Ihrer Rechnung auch Bruchteile einer Stunde auftauchen können. Wenn eine Datenbank beispielsweise innerhalb eines Monats 12 Stunden existiert, wird auf Ihrer Rechnung eine Nutzung von 0,5 Tagen aufgeführt. Zudem sind die Dienst- und Leistungsebenen sowie eDTUs pro Pool in der Rechnung aufgeschlüsselt, um die Anzahl der Datenbanktage einfacher zu erkennen, die jeweils in einem Monat angefallen sind.

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>Was geschieht, wenn eine Einzeldatenbank weniger als eine Stunde aktiv ist oder für weniger als eine Stunde eine höhere Dienstebene verwendet?
Die Abrechnung erfolgt für jede Stunde, in der eine Datenbank die höchste in dieser Stunde angewandte Dienst- und Leistungsebene nutzt – unabhängig von der Verwendung der Datenbank und ob sie weniger als eine Stunde aktiv war. Wenn Sie beispielsweise eine Einzeldatenbank erstellen und diese fünf Minuten später löschen, wird Ihnen eine volle Datenbankstunde in Rechnung gestellt. 

Beispiele:

* Wenn Sie eine Basic-Datenbank erstellen und unmittelbar danach auf Standard S1 aktualisieren, wird Ihnen für die erste Stunde der Standard S1-Satz berechnet.
* Wenn Sie für eine Datenbank um 22:00 Uhr ein Upgrade von Basic auf Premium durchführen und das Upgrade um 01:35 Uhr am folgenden Tag abgeschlossen ist, wird Ihnen die Premium-Rate ab 01:00 Uhr berechnet. 
* Wenn Sie für eine Datenbank um 11:00 Uhr ein Downgrade von Premium auf Basic durchführen und der Vorgang um 14:15 Uhr abgeschlossen ist, wird für die Datenbank bis 15:00 Uhr die Premium-Rate berechnet. Danach werden die Basic-Raten berechnet.

## <a name="how-does-elastic-pool-usage-show-up-on-my-bill-and-what-happens-when-i-change-edtus-per-pool"></a>Wie wird die Nutzung des Pools für elastische Datenbanken auf meiner Rechnung ausgewiesen, und was passiert, wenn ich eDTUs pro Pool ändere?
Die Abrechnung der Gebühren für den Pool für elastische Datenbanken erfolgt auf Ihrer Rechnung als elastische DTUs (eDTUs) in den Abstufungen, die unter den eDTUs pro Pool auf der [Preisseite](https://azure.microsoft.com/pricing/details/sql-database/) ausgewiesen sind. Für Pools für elastische Datenbanken erfolgt keine Abrechnung pro Datenbank. Die Abrechnung erfolgt für jede Stunde, in der ein Pool im höchsten eDTU existiert, unabhängig von der Verwendung oder ob der Pool weniger als eine Stunde aktiv war. 

Beispiele:

* Wenn Sie um 11:18 Uhr einen Standard-Pool für elastische Datenbanken mit 200 eDTUs erstellen und dem Pool fünf Datenbanken hinzufügen, werden Ihnen ab 11:00 Uhr für den Rest des Tages die 200 eDTUs für die ganze Stunde berechnet.
* An Tag 2 nutzt Datenbank 1 ab 5:05 Uhr den ganzen Tag über 50 eDTUs. Die Nutzung der Datenbanken 2-5 schwankt zwischen 0 und 80 eDTUs. Im Laufe des Tages fügen Sie fünf weitere Datenbanken hinzu, die im Laufe des Tages verschiedene eDTUs nutzen. An Tag 2 werden den ganzen Tag über 200 eDTU berechnet. 
* An Tag 3 um 05:00 Uhr fügen Sie weitere 15 Datenbanken hinzu. Die Nutzung der Datenbank steigt im Laufe des Tages, bis Sie um 20:05 Uhr entscheiden, die eDTUs für den Pool von 200 auf 400 aufzustocken. Bis 20.00 Uhr werden Ihnen die Gebühren für 200 eDTUs berechnet. Für die verbleibenden vier Stunden des Tages erfolgt die Abrechnung für 400 eDTUs. 

## <a name="elastic-pool-billing-and-pricing-information"></a>Abrechnungs- und Preisinformationen für Pools für elastische Datenbanken
Pools für elastische Datenbanken werden anhand der folgenden Merkmale abgerechnet:

* Ein Pool für elastische Datenbanken wird bei seiner Erstellung abgerechnet, auch wenn keine Datenbanken im Pool vorhanden sind.
* Ein Pool für elastische Datenbanken wird stündlich abgerechnet. Dies ist die gleiche Häufigkeit wie für Leistungsstufen von Einzeldatenbanken.
* Wenn ein Pool für elastische Datenbanken auf die Größe einer neuen Anzahl von eDTUs geändert wird, wird der Pool erst dann gemäß den neuen eDTUs berechnet, wenn die Größenänderung abgeschlossen ist. Dies erfolgt nach dem gleichen Muster wie beim Ändern der Leistungsstufe von Einzeldatenbanken.
* Der Preis für einen Pool für elastische Datenbanken basiert auf der Anzahl von eDTUs des Pools. Der Preis eines Pools für elastische Datenbanken hängt nicht von der Anzahl und Auslastung der darin enthaltenen elastischen Datenbanken ab.
* Der Preis wird wie folgt berechnet: (Anzahl von Pool-eDTUs) x (Einzelpreis pro eDTU).

Der eDTU-Einzelpreis für einen Pool für elastische Datenbanken ist höher als der DTU-Stückpreis für eine Einzeldatenbank derselben Dienstebene. Weitere Informationen finden Sie unter [SQL-Datenbank Preise](https://azure.microsoft.com/pricing/details/sql-database/). 

Grundlegende Informationen zu eDTUs und Dienstebenen finden Sie unter [SQL-Datenbankoptionen und -leistung](sql-database-service-tiers.md).

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>Wie wird die Nutzung der aktiven Georeplikation in einem Pool für elastische Datenbanken auf meiner Rechnung ausgewiesen?
Im Gegensatz zu Einzeldatenbanken hat die Nutzung der [aktiven Georeplikation](sql-database-geo-replication-overview.md) mit elastischen Datenbanken keine direkte Auswirkung auf die Abrechnung.  Ihnen werden nur die für die einzelnen Pools (primärer Pool und sekundärer Pool) bereitgestellten eDTUs in Rechnung gestellt.

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>Wie wirkt sich die Nutzung der Überwachungsfunktion auf meine Rechnung aus?
Die Überwachungsfunktion ist kostenfrei in den SQL-Datenbankdienst integriert und für Basic-, Standard- und Premium-Datenbanken verfügbar. Um die Überwachungsprotokolle zu speichern, nutzt die Überwachungsfunktion ein Azure-Speicherkonto, und die Sätze für Tabellen und Warteschlangen im Azure-Speicher werden abhängig von der Größe Ihres Überwachungsprotokolls angewandt.

## <a name="how-do-i-find-the-right-service-tier-and-performance-level-for-single-databases-and-elastic-pools"></a>Wie finde ich die richtige Dienst- und Leistungsebene für Einzeldatenbanken und Pools für elastische Datenbanken?
Ihnen stehen einige Tools zur Verfügung: 

* Verwenden Sie für lokale Datenbanken den [Ratgeber für DTU-Größen](http://dtucalculator.azurewebsites.net/), mit dem erforderliche Datenbanken und DTUs empfohlen und mehrere Datenbanken für Pools für elastische Datenbanken bewertet werden.
* Wenn eine Einzeldatenbank von einem Pool profitieren würde, empfiehlt die intelligente Engine von Azure einen Pool für elastische Datenbanken, sofern es ein passendes Verwendungsmuster erkennt. Weitere Informationen finden Sie unter [Überwachen und Verwalten eines Pools für elastische Datenbanken über das Azure-Portal](sql-database-elastic-pool-manage-portal.md). Weitere Informationen für eigene Berechnungen finden Sie unter [Überlegungen zum Preis und zur Leistung eines Pools für elastische Datenbanken](sql-database-elastic-pool.md).
* Ob ein Upgrade oder Downgrade für Ihre Einzeldatenbank erforderlich ist, erfahren Sie im [Leitfaden zur Leistung für einzelne Datenbanken](sql-database-performance-guidance.md).

## <a name="how-often-can-i-change-the-service-tier-or-performance-level-of-a-single-database"></a>Wie oft kann ich die Dienst- oder Leistungsebene einer Einzeldatenbank ändern?
Sie können die Dienstebene bzw. den Tarif (Basic, Standard und Premium) oder die Leistungsstufe innerhalb des Tarifs (z.B. von S1 in S2) beliebig oft ändern. Bei früheren Versionen der Datenbanken können Sie die Dienst- oder Leistungsebene innerhalb von 24 Stunden viermal ändern.

## <a name="how-often-can-i-adjust-the-edtus-per-pool"></a>Wie oft kann ich die eDTUs pro Pool anpassen?
Beliebig oft.

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>Wie lange dauert es, die Dienst- oder Leistungsebene einer Einzeldatenbank zu ändern oder eine Datenbank in einen Pool für elastische Datenbanken oder aus solch einem Pool heraus zu verschieben?
Um die Dienstebene einer Datenbank zu ändern und sie in einen Pool und aus einem Pool heraus zu verschieben, muss die Datenbank als Hintergrundvorgang auf die Plattform kopiert werden. Je nach Größe der Datenbanken kann das Ändern der Dienstebene wenige Minuten oder mehrere Stunden dauern. In beiden Fällen bleiben die Datenbanken während des Verschiebens online und verfügbar. Weitere Informationen zum Ändern von Einzeldatenbanken finden Sie unter [Ändern der Dienstebene für eine Datenbank](sql-database-service-tiers.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Wann sollten Einzeldatenbanken elastischen Datenbanken vorgezogen werden?
Pools für elastische Datenbanken sind generell auf herkömmliche [SaaS-Anwendungsmuster (Software-as-a-Service)](sql-database-design-patterns-multi-tenancy-saas-applications.md) ausgelegt, in denen eine Datenbank pro Kunde oder Mandant vorhanden ist. Der Erwerb von Einzeldatenbanken und die Bereitstellung einer für den Normalfall zu großen Menge an Datenbankressourcen, um für jede Einzeldatenbank variierende Anforderungen oder Anforderungen zu Spitzenzeiten zu erfüllen, ist häufig keine kosteneffiziente Lösung. Mit Pools verwalten Sie die gesamte Leistung des Pools, und die Datenbanken werden automatisch nach oben und unten skaliert. Die intelligente Engine von Azure empfiehlt einen Pool für Datenbanken, wenn es ein entsprechendes Nutzungsmuster erkennt. Details finden Sie in der [Anleitung zu Pools für elastische Datenbanken](sql-database-elastic-pool.md).

## <a name="what-does-it-mean-to-have-up-to-200-of-your-maximum-provisioned-database-storage-for-backup-storage"></a>Was bedeutet es, bis zu 200 Prozent des maximal bereitgestellten Datenbankspeichers zur Sicherung zur Verfügung zu haben?
Der Sicherungsspeicher ist der Speicher, der mit Ihren automatisierten Datenbanksicherungen verknüpft ist, die für [Point-in-Time-Wiederherstellung](sql-database-recovery-using-backups.md#point-in-time-restore) und [Geowiederherstellung](sql-database-recovery-using-backups.md#geo-restore) verwendet werden. Microsoft Azure SQL-Datenbanken bieten bis zu 200 Prozent Ihres maximal bereitgestellten Sicherungsdatenbankspeichers ohne zusätzliche Kosten. Wenn Sie z.B. über eine Standard-Datenbankinstanz mit einer bereitgestellten Größe von 250 GB verfügen, werden Ihnen ohne zusätzliche Kosten 500 GB Sicherungsspeicher bereitgestellt. Wenn die maximale Größe Ihres bereitgestellten Sicherungsspeichers überschritten wird, können Sie sich entweder an den Azure-Support wenden, um den Aufbewahrungszeitraum zu verkürzen, oder zusätzlichen Sicherungsspeicher erwerben, für den die standardmäßigen Gebühren für geografisch redundanten Speicher mit Lesezugriff (Read-Access Geographically Redundant Storage, RA-GRS) anfallen. Weitere Informationen zur RA-GRS-Abrechnung finden Sie in der Preisübersicht für Speicher.

## <a name="im-moving-from-webbusiness-to-the-new-service-tiers-what-do-i-need-to-know"></a>Was muss ich beim Umstieg von Web/Business auf die neuen Dienstebenen beachten?
Azure SQL Web und Business-Datenbanken wurden eingestellt. Die Dienstebenen „Basic“, „Standard“, „Premium“ und „Elastic“ ersetzen die eingestellten Web- und Business-Datenbanken. 

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>Wie groß ist die erwartete Replikationsverzögerung bei der geografischen Replikation einer Datenbank zwischen zwei Regionen innerhalb derselben Azure-Geografie?
Derzeit wird eine RPO von fünf Sekunden unterstützt. Die Replikationsverzögerung ist geringer, solange die geografisch sekundäre Datenbank in der von Azure empfohlenen zugeordneten Region gehostet wird und die gleiche Dienstebene hat.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>Wie groß ist die erwartete Replikationsverzögerung, wenn die geografisch sekundäre Datenbank in der gleichen Region erstellt wird wie die primäre Datenbank?
Auf Grundlage von empirischen Daten besteht nicht zu viel Unterschied zwischen der Replikationsverzögerung innerhalb von und zwischen Regionen, wenn die von Azure empfohlene zugeordnete Region verwendet wird. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>Wie funktioniert die Wiederholungslogik bei eingerichteter Georeplikation, wenn zwischen zwei Regionen ein Netzwerkfehler auftritt?
Bei einer Trennung der Verbindung, versuchen wir alle 10 Sekunden die Verbindung erneut herzustellen.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>Was kann ich tun, um sicherzustellen, dass eine wichtige Änderung in der primären Datenbank repliziert wird?
Die geografisch sekundäre Datenbank ist ein asynchrones Replikat, und wir versuchen nicht, sie vollständig mit der primären Datenbank zu synchronisieren. Wir stellen aber eine Methode zum Erzwingen der Synchronisierung bereit, um die Replikation kritischer Änderungen sicherzustellen (z.B. Kennwortaktualisierungen). Die erzwungene Synchronisierung beeinträchtigt die Leistung, da der aufrufende Thread blockiert wird, bis alle durchgeführten Transaktionen repliziert wurden. Weitere Informationen finden Sie unter [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>Welche Tools stehen zur Überwachung der Replikationsverzögerung zwischen der primären Datenbank und der geografisch sekundären Datenbank zur Verfügung?
Die Replikationsverzögerung zwischen der primären Datenbank und der geografisch sekundären wird über eine DMV verfügbar gemacht. Weitere Informationen finden Sie unter [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>So verschieben Sie eine Datenbank auf einen anderen Server im selben Abonnement
Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **SQL-Datenbanken**, wählen Sie in der Liste eine Datenbank aus, und klicken Sie dann auf **Kopieren**. Weitere Details finden Sie unter [Kopieren einer Azure SQL-Datenbank](sql-database-copy.md) .

## <a name="to-move-a-database-between-subscriptions"></a>So verschieben Sie eine Datenbank zwischen Abonnements
Klicken Sie im [Azure-Portal](https://portal.azure.com)auf **SQL-Server**, und wählen Sie in der Liste den Server aus, der die Datenbank hostet. Klicken Sie auf **Verschieben**, und wählen Sie die zu verschiebenden Ressourcen und das Abonnement aus, in das dieser Vorgang erfolgen soll.
