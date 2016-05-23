<properties
   pageTitle="Geschäftskontinuität für die Cloud – Datenbankwiederherstellung | Microsoft Azure"
   description="Erfahren Sie, wie Azure SQL-Datenbank die Geschäftskontinuität in der Cloud sowie die Datenbankwiederherstellung unterstützt und dafür sorgt, dass geschäftskritische Cloudanwendungen in Betrieb gehalten werden."
   keywords="Geschäftskontinuität,Geschäftskontinuität in der Cloud,Notfallwiederherstellung von Datenbanken,Datenbankwiederherstellung"
   services="sql-database"
   documentationCenter=""
   authors="elfisher"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="05/10/2016"
   ms.author="elfish"/>

# Übersicht: Geschäftskontinuität für die Cloud und Notfallwiederherstellung für Datenbanken mit SQL-Datenbank

Bei Geschäftskontinuität geht es um das Entwerfen, Bereitstellen und Ausführen von Anwendungen mit Flexibilität gegenüber geplanten oder ungeplanten Unterbrechungen, die zu einem dauerhaften oder vorübergehenden Ausfall der von der Anwendung ausgeführten Geschäftsfunktion führen können. Ungeplante Ereignisse reichen von Benutzerfehlern über dauerhafte oder zeitweise Ausfälle bis hin zu regionalen Katastrophen, die weitreichende Verluste an Einrichtungen in einer bestimmten Azure-Region verursachen können. Zu geplanten Ereignissen gehören die erneute Bereitstellung der Anwendung in einer anderen Region, Anwendungsupgrades usw. Das Ziel der Geschäftskontinuität ist die Fortsetzung der Anwendung während solcher Ereignisse mit minimalen Auswirkungen auf die Geschäftsfunktion.

Für die Erörterung der Geschäftskontinuitätslösungen für die Cloud sollten Sie mit einigen Konzepten vertraut sein.

* **Notfallwiederherstellung:** ein Vorgang zur Wiederherstellung der normalen Geschäftsfunktionen der Anwendung

* **Geschätzte Wiederherstellungszeit:** die geschätzte Dauer bis zur vollständigen Verfügbarkeit der Datenbank nach einer Wiederherstellungs- oder Failover-Anforderung.

* **RTO (Recovery Time Objective)**: die maximal zulässige Zeitspanne bis zur vollständigen Wiederherstellung der Anwendung nach der Störung. RTO misst den maximalen Verlust der Verfügbarkeit während des Ausfalls.

* **RPO (Recovery Point Objective)**: die maximale Anzahl von letzten Updates (Zeitraum), die für die Anwendung bis zum Moment der vollständigen Wiederherstellung nach dem Ausfall verloren gehen dürfen. RPO misst den maximalen Datenverlust während des Ausfalls.


## Szenarien für die Geschäftskontinuität in der Cloud

Nachfolgend werden die wichtigsten Szenarien beschrieben, die beim Planen von Geschäftskontinuität und Datenbankwiederherstellung zu berücksichtigen sind.

###Entwerfen von Anwendungen für Geschäftskontinuität

Die Anwendung, die ich erstelle, ist entscheidend für mein Unternehmen. Ich möchte sie so entwickeln und konfigurieren, dass sie einen regionalen Ausfall oder Totalausfall des Diensts überstehen kann. Ich kenne die Anforderungen für RPO und RTO für meine Anwendung und wähle eine Konfiguration, die diese Anforderungen erfüllt.

###Wiederherstellen nach Benutzerfehlern

Ich habe über Administratorrechte Zugriff auf die Produktionsversion der Anwendung. Ich habe bei der regelmäßigen Wartung einen Fehler gemacht und einige wichtige Daten in der Produktionsumgebung gelöscht. Ich möchte die Daten schnell wiederherstellen, um die Auswirkungen des Fehlers zu minimieren.

###Wiederherstellen nach einem Ausfall

Ich verwende meine Anwendung in der Produktion und werde in einer Warnung über einen größeren Ausfall in der Region, in der sie bereitgestellt wird, informiert. Ich möchte die Wiederherstellung initiieren, um sie in einer anderen Region wieder in Betrieb zu nehmen und die Auswirkungen auf das Unternehmen zu minimieren.

###Ausführen von Notfallwiederherstellungsübungen

Da die Datenschicht der Anwendung bei der Wiederherstellung nach einem Ausfall in eine andere Region verschoben wird, möchte ich in regelmäßigen Abständen den Wiederherstellungsprozess testen und die Auswirkungen auf die Bereitstellung der Anwendung bewerten.

###Anwendungsupgrades ohne Ausfallzeiten

Ich gebe ein größeres Upgrade meiner Anwendung frei. Es umfasst Änderungen am Datenbankschema, die Bereitstellung zusätzlicher gespeicherter Prozeduren usw. Für diesen Vorgang muss der Benutzerzugriff auf die Datenbank beendet werden. Gleichzeitig möchte ich sicherstellen, dass das Upgrade keine wesentliche Unterbrechungen der Geschäftsabläufe verursacht.

##Funktionen der Geschäftskontinuität

Die folgende Tabelle zeigt die Unterschiede zwischen den Funktionen der Geschäftskontinuität für die Cloud in den verschiedenen Diensttarifen:

| Funktion | Basic-Tarif | Standard-Tarif |Premium-Tarif
| --- |--- | --- | ---
| Zeitpunktwiederherstellung | Jeder Wiederherstellungspunkt innerhalb von 7 Tagen | Jeder Wiederherstellungspunkt innerhalb von 14 Tagen | Jeder Wiederherstellungspunkt innerhalb von 35 Tagen
| Geografische Wiederherstellung | Geschätzte Wiederherstellungszeit < 12 h, RPO < 1 h | Geschätzte Wiederherstellungszeit < 12 h, RPO < 1 h | Geschätzte Wiederherstellungszeit < 12 h, RPO < 1 h
| Aktive Georeplikation | Geschätzte Wiederherstellungszeit < 30 s, RPO < 5 s | Geschätzte Wiederherstellungszeit < 30 s, RPO < 5 s | Geschätzte Wiederherstellungszeit < 30 s, RPO < 5 s

Diese Funktionen werden für die zuvor beschriebenen Szenarios bereitgestellt. Im Abschnitt [Entwurf für Geschäftskontinuität](sql-database-business-continuity-design.md) finden Sie Anleitungen zum Auswählen der jeweiligen Funktion.

> [AZURE.NOTE] Die Werte für die geschätzte Wiederherstellungszeit und RPO sind technische Ziele und dienen lediglich als Leitfaden. Sie sind nicht Bestandteil des [SLA für die SQL-Datenbank](https://azure.microsoft.com/support/legal/sla/sql-database/v1_0/).


###Zeitpunktwiederherstellung

Mit der [Point-in-Time-Wiederherstellung](sql-database-point-in-time-restore.md) können Sie die Datenbank in den Zustand zu einem früheren Zeitpunkt zurückführen. Dazu werden Datenbanksicherungen, inkrementelle Sicherungen und Transaktionsprotokollsicherungen verwendet, die der Dienst automatisch für jede Benutzerdatenbank verwaltet. Diese Funktion steht für alle Dienstebenen zur Verfügung. Bei Basic können Sie 7 Tage wiederherstellen, bei Standard 14 Tage und bei Premium 35 Tage. Weitere Informationen zum Verwenden der Zeitpunktwiederherstellung finden Sie unter [Wiederherstellen nach Benutzerfehlern](sql-database-user-error-recovery.md).

###Geografische Wiederherstellung

Die [geografische Wiederherstellung](sql-database-geo-restore.md) steht ebenfalls für Basic-, Standard- und Premium-Datenbanken zur Verfügung. Sie stellt die Standardoption für die Wiederherstellung dar, wenn auch die Datenbank aufgrund eines Vorfalls in der Region, in dem die Datenbank gehostet wird, nicht verfügbar ist. Ähnlich wie die Zeitpunktwiederherstellung ist die geografische Wiederherstellung von Datenbanksicherungen in geografisch redundantem Azure-Speicher abhängig. Die Wiederherstellung erfolgt aus der geografisch replizierten Sicherungskopie und ist daher in Bezug auf Speicherausfälle in der primären Region flexibel. Weitere Informationen zum Verwenden von geografischer Wiederherstellung finden Sie unter [Wiederherstellen nach einem Ausfall](sql-database-disaster-recovery.md).

###Aktive Georeplikation

Die [aktive Georeplikation](sql-database-geo-replication-overview.md) ist für alle Datenbanktarife verfügbar. Sie ist für Anwendungen vorgesehen, für die umfangreichere Wiederherstellungsanforderungen erforderlich sind, als die Geowiederherstellung bieten kann. Bei der aktiven Georeplikation können Sie bis zu vier lesbare sekundäre Replikate auf Servern in verschiedenen Regionen erstellen. Sie können ein Failover auf ein beliebiges sekundäres Replikat initiieren. Darüber hinaus kann die aktive Georeplikation verwendet werden, um Anwendungsupgrades oder die räumliche Verlegung von Anwendungen zu unterstützen, sowie als Lastenausgleich für schreibgeschützte Arbeitsauslastungen. Unter [Entwerfen für Geschäftskontinuität](sql-database-business-continuity-design.md) finden Sie Informationen zur [Konfiguration der Georeplikation](sql-database-geo-replication-portal.md) und zum [Failover zur sekundären Datenbank](sql-database-geo-replication-failover-portal.md). Details zur Implementierung von Anwendungsupgrades ohne Ausfallzeit finden Sie unter [Anwendungsupgrades ohne Ausfallzeiten](sql-database-business-continuity-application-upgrade.md).

<!---HONumber=AcomDC_0511_2016-->