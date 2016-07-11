<properties 
   pageTitle="Azure SQL-Datenbank – Häufig gestellte Fragen" 
   description="Antworten auf häufig gestellte Fragen von Kunden zu Cloud-Datenbanken, zur Azure SQL-Datenbank, das Microsoft Managementsystem für relationale Datenbanken (RDBMS) und Database as a Service in der Cloud." 
   services="sql-database" 
   documentationCenter="" 
   authors="carlrabeler" 
   manager="jhubbard" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="05/25/2016"
   ms.author="sashan;carlrab"/>

# SQL-Datenbank – Häufig gestellte Fragen

## Wie wird die Nutzung der SQL-Datenbank auf meiner Rechnung ausgewiesen? 
Die Abrechnung der SQL-Datenbank erfolgt nach einer vorhersehbaren Stundenpauschale, basierend auf Dienst- und Leistungsebene für Einzeldatenbanken oder edTUs pro elastischen Datenbankpool. Der tatsächliche Verbrauch wird stündlich berechnet und anteilig aufgeführt, sodass auf Ihrer Rechnung auch Bruchteile einer Stunde auftauchen können. Wenn eine Datenbank beispielsweise innerhalb eines Monats 12 Stunden existiert, wird auf Ihrer Rechnung eine Nutzung von 0,5 Tagen aufgeführt. Zudem sind die Dienst- und Leistungsebenen sowie eDTUs pro Pool in der Rechnung aufgeschlüsselt, um die Anzahl der Datenbanktage einfacher zu erkennen, die jeweils in einem Monat angefallen sind.

## Was geschieht, wenn eine Einzeldatenbank weniger als eine Stunde aktiv ist oder für weniger als eine Stunde eine höhere Dienstebene verwendet?
Die Abrechnung erfolgt für jede Stunde, in der eine Datenbank die höchste in dieser Stunde angewandte Dienst- und Leistungsebene nutzt – unabhängig von der Verwendung der Datenbank und ob sie weniger als eine Stunde aktiv war. Falls Sie beispielsweise eine Einzeldatenbank erstellen und sie 5 Minuten später löschen, wird Ihnen eine volle Datenbankstunde in Rechnung gestellt.

Beispiele
	
- Wenn Sie eine Basic-Datenbank erstellen und unmittelbar danach auf Standard S1 aktualisieren, wird Ihnen für die erste Stunde der Standard S1-Satz berechnet.

- Wenn Sie um 22:00 Uhr für eine Datenbank ein Upgrade von Basic auf Premium durchführen und das Upgrade um 1:35 Uhr am nächsten Tag abgeschlossen ist, wird Ihnen der Premium-Satz erst ab 1:00 Uhr in Rechnung gestellt.

- Wenn Sie um 11:00 Uhr für eine Datenbank ein Downgrade von Premium auf Basic durchführen und das Downgrade um 14:15 Uhr abgeschlossen ist, wird Ihnen der Premium-Satz bis 15:00 Uhr berechnet. Danach erfolgt die Abrechnung zum Basic-Satz.

## Wie wird die Nutzung des elastischen Datenbankpools auf meiner Rechnung ausgewiesen, und was passiert, wenn ich eDTUs pro Pool ändere?
Die Abrechnung der Gebühren für den elastischen Datenbankpool erfolgt auf Ihrer Rechnung als elastische DTUs (eDTUs) in den Schritten, die unter den eDTUs pro Pool auf [der Preisseite](https://azure.microsoft.com/pricing/details/sql-database/) ausgewiesen sind. Für elastische Datenbankpools erfolgt keine Abrechnung pro Datenbank. Die Abrechnung erfolgt für jede Stunde, in der ein Pool im höchsten eDTU existiert, unabhängig von der Verwendung oder ob der Pool weniger als eine Stunde aktiv war.

Beispiele

- Wenn Sie um 11:18 Uhr einen elastischen Datenbankpool mit 200 eDTUs erstellen und dann fünf Datenbanken zum Pool hinzufügen, werden Ihnen ab 11:00 Uhr für den restlichen Tag 200 eDTUs berechnet.
- An Tag 2 nutzt Datenbank 1 ab 5:05 Uhr den ganzen Tag über 50 eDTUs. Die Nutzung der Datenbanken 2-5 schwankt zwischen 0 und 80 eDTUs. Im Laufe des Tages fügen Sie fünf weitere Datenbanken hinzu, die im Laufe des Tages verschiedene eDTUs nutzen. An Tag 2 werden den ganzen Tag über 200 eDTU berechnet.
- An Tag 3 fügen Sie um 5 Uhr weitere 15 Datenbanken hinzu. Die Nutzung der Datenbank steigt im Laufe des Tages, bis Sie um 20:05 Uhr entscheiden, die eDTUs für den Pool von 200 auf 400 aufzustocken. Bis 20.00 Uhr werden Ihnen die Gebühren für 200 eDTUs berechnet. Für die verbleibenden 4 Stunden des Tages erfolgt die Abrechnung für 400 eDTUs.

## Wie wird die Nutzung der aktiven Georeplikation in einem Pool für elastische Datenbanken auf meiner Rechnung ausgewiesen?
Im Gegensatz zu Einzeldatenbanken hat die Nutzung der [aktiven Georeplikation](sql-database-geo-replication-overview.md) mit elastischen Datenbanken keine direkte Auswirkung auf die Abrechnung. Ihnen werden nur die für die einzelnen Pools (primärer Pool und sekundärer Pool) bereitgestellten eDTUs in Rechnung gestellt.

## Wie wirkt sich die Nutzung der Überwachungsfunktion auf meine Rechnung aus? 
Die Überwachungsfunktion ist kostenfrei in den SQL-Datenbankdienst integriert und für Basic-, Standard- und Premium-Datenbanken verfügbar. Um die Überwachungsprotokolle zu speichern, nutzt die Überwachungsfunktion ein Azure-Speicherkonto, und die Sätze für Tabellen und Warteschlangen im Azure-Speicher werden abhängig von der Größe Ihres Überwachungsprotokolls angewandt.

## Wie finde ich die richtige Dienst- und Leistungsebene für einzelne Datenbanken und elastische Datenbankpools? 
Ihnen stehen einige Tools zur Verfügung.

- Verwenden Sie für lokale Datenbanken den [DTU Sizing Advisor](http://dtucalculator.azurewebsites.net/), der erforderliche Datenbanken und DTUs empfiehlt und mehrere Datenbanken für flexible Datenbankpools bewertet.
- Wenn eine Einzeldatenbank von einem Pool profitieren würde, empfiehlt das intelligente Modul von Azure einen elastischen Datenbankpool, wenn es ein passendes Verwendungsmuster erkennt. Siehe [Überwachen und Verwalten eines Pools für elastische Datenbanken über das Azure-Portal](sql-database-elastic-pool-manage-portal.md) Weitere Informationen für eigene Berechnungen finden Sie unter [Überlegungen zum Preis und zur Leistung eines Pools für elastische Datenbanken](sql-database-elastic-pool-guidance.md).
- Ob ein Upgrade oder Downgrade für Ihre Einzeldatenbank erforderlich ist, erfahren Sie im [Leitfaden zur Leistung für einzelne Datenbanken](sql-database-performance-guidance.md).

## Wie oft kann ich die Dienst- oder Leistungsebene einer Einzeldatenbank ändern? 
Bei V12-Datenbanken können Sie die Dienstebene (Basic, Standard und Premium) oder die Leistungsebene innerhalb einer Dienstebene (z. B. S1 zu S2) beliebig oft ändern. Bei früheren Versionen der Datenbanken können Sie die Dienst- oder Leistungsebene innerhalb von 24 Stunden viermal ändern.

##Wie oft kann ich die eDTUs pro Pool anpassen? 
Beliebig oft.

## Wie lange dauert es, die Dienst- oder Leistungsebene einer Einzeldatenbank zu ändern oder eine Datenbank in einen elastischen Datenbankpool oder aus solch einem Pool heraus zu verschieben? 
Um die Dienstebene einer Datenbank zu ändern und sie in einen Pool und aus einem Pool heraus zu verschieben, muss die Datenbank als Hintergrundvorgang auf die Plattform kopiert werden. Je nach Größe der Datenbanken kann dieser Vorgang wenige Minuten oder mehrere Stunden dauern. In beiden Fällen bleiben die Datenbanken während des Verschiebens online und verfügbar. Weitere Informationen zum Ändern von Einzeldatenbanken finden Sie unter [Ändern der Dienstebene einer Datenbank](sql-database-scale-up.md).

## Wann sollten Einzeldatenbanken elastischen Datenbanken vorgezogen werden? 
Pools für elastische Datenbanken sind generell auf herkömmliche [SaaS-Anwendungsmuster (Software-as-a-Service)](sql-database-design-patterns-multi-tenancy-saas-applications.md) ausgelegt, in denen eine Datenbank pro Kunde oder Mandant vorhanden ist. Der Erwerb von Einzeldatenbanken und die Bereitstellung einer für den Normalfall zu großen Menge an Datenbankressourcen, um für jede Einzeldatenbank variierende Anforderungen oder Anforderungen zu Spitzenzeiten zu erfüllen, ist häufig keine kosteneffiziente Lösung. Mit Pools verwalten Sie die gesamte Leistung des Pools, und die Datenbanken werden automatisch nach oben und unten skaliert.

Das intelligente Modul von Azure empfiehlt einen Pool für Datenbanken, wenn es ein entsprechendes Nutzungsmuster erkennt. Weitere Details finden Sie unter [Tarifempfehlungen für SQL-Datenbank](sql-database-service-tier-advisor.md). Ausführliche Informationen zur Auswahl zwischen Einzeldatenbanken und elastischen Datenbanken finden Sie unter [Überlegungen zum Preis und zur Leistung eines Pools für elastische Datenbanken.](sql-database-elastic-pool-guidance.md).

## Was bedeutet es, bis zu 200 Prozent des maximal bereitgestellten Datenbankspeichers zur Sicherung zur Verfügung zu haben? 
Der Sicherungsspeicher ist der Speicher, der mit Ihren automatisierten Datenbanksicherungen verknüpft ist, die für [Point-in-Time-Wiederherstellung](sql-database-recovery-using-backups.md#-point-in-time-restore) und [Geowiederherstellung](sql-database-recovery-using-backups.md#geo-restore) verwendet werden. Microsoft Azure SQL-Datenbanken bieten bis zu 200 Prozent Ihres maximal bereitgestellten Sicherungsdatenbankspeichers ohne zusätzliche Kosten. Wenn Sie z. B. über eine Standard-DB-Instanz mit einer bereitgestellten Größe von 250 GB verfügen, werden Ihnen ohne zusätzliche Kosten 500 GB Sicherungsspeicher bereitgestellt. Wenn die maximale Größe Ihres bereitgestellten Sicherungsspeichers überschritten wird, können Sie sich entweder an den Azure-Support wenden, um den Aufbewahrungszeitraum zu verkürzen, oder zusätzlichen Sicherungsspeicher erwerben, für den die standardmäßigen Gebühren für geografisch redundanten Speicher mit Lesezugriff (Read-Access Geographically Redundant Storage, RA-GRS) anfallen. Weitere Informationen zur RA-GRS-Abrechnung finden Sie in der Preisübersicht für Speicher.

## Was muss ich beim Umstieg von Web/Business auf die neuen Dienstebenen beachten?
Azure SQL Web und Business-Datenbanken wurden eingestellt. Die Dienstebenen Basic, Standard, Premium und Elastic ersetzen die eingestellten Web- und Business-Datenbanken. Zu Ihrer Unterstützung im Übergangszeitraum haben wir die häufig gestellten Fragen ergänzt. [Häufig gestellte Fragen zur Einstellung von Web Edition und Business Edition](sql-database-web-business-sunset-faq.md)

## Wie groß ist die erwartete Replikationsverzögerung bei der geografischen Replikation einer Datenbank zwischen zwei Regionen innerhalb desselben Azure-Gebiets?  
Derzeit wird ein RPO von 5 Sekunden unterstützt. Die Replikationsverzögerung ist kleiner, solange die geografisch sekundäre Datenbank in der von Azure empfohlenen zugeordneten Region gehostet wird und die gleiche Dienstebene hat.

## Wie groß ist die erwartete Replikationsverzögerung, wenn die geografisch sekundäre Datenbank in der gleichen Region erstellt wird wie die primäre Datenbank?  
Auf Grundlage von empirischen Daten besteht nicht zu viel Unterschied zwischen der Replikationsverzögerung innerhalb von und zwischen Regionen, wenn die von Azure empfohlene zugeordnete Region verwendet wird.

## Wie funktioniert die Wiederholungslogik bei eingerichteter Georeplikation, wenn zwischen zwei Regionen ein Netzwerkfehler auftritt?  
Bei einer Trennung der Verbindung, versuchen wir alle 10 Sekunden die Verbindung erneut herzustellen.

## Was kann ich tun, um sicherzustellen, dass eine wichtige Änderung in der primären Datenbank repliziert wird?
Die geografisch sekundäre Datenbank ist ein asynchrones Replikat, und wir versuchen nicht, sie vollständig mit der primären Datenbank zu synchronisieren. Wir bieten jedoch eine Methode an, um die Synchronisierung zu erzwingen. Sie dient dazu, die Replikation von wichtigen Änderungen (z. B. Kennwortaktualisierung) sicherzustellen. Dies beeinträchtigt die Leistung, da der aufrufende Thread blockiert wird, bis alle durchgeführten Transaktionen repliziert wurden. Weitere Informationen finden Sie unter [sp\_wait\_for\_database\_copy\_sync](https://msdn.microsoft.com/library/dn467644.aspx).

## Welche Tools stehen zur Überwachung der Replikationsverzögerung zwischen der primären Datenbank und der geografisch sekundären Datenbank zur Verfügung?
Die Replikationsverzögerung zwischen der primären Datenbank und der geografisch sekundären wird über eine DMV verfügbar gemacht. Weitere Informationen finden Sie unter [sys.dm\_geo\_replication\_link\_status](https://msdn.microsoft.com/library/mt575504.aspx).

<!---HONumber=AcomDC_0629_2016-->