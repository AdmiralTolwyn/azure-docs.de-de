<properties 
   pageTitle="Tarifempfehlungen für Azure SQL-Datenbank" 
   description="Beim Ändern von Tarifen im Azure-Portal werden Tarifempfehlungen bereitgestellt, die den am besten geeigneten Tarif für die Ausführung des Workloads einer vorhandenen Azure SQL-Datenbank empfehlen. Tarife beschreiben die Dienstebene und die Leistungsebene einer SQL-Datenbank." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="08/08/2016"
   ms.author="sstein"/>

# Tarifempfehlungen für SQL-Datenbank

 Tarifempfehlungen schlagen die Dienstebene und die Leistungsebene vor, die am besten zum Ausführen der Workload einer vorhandenen Azure SQL-Datenbank geeignet sind.

> [AZURE.NOTE] Tarifempfehlungen sind nur für Web- und Business-Datenbanken sowie für Pools für elastische Datenbanken verfügbar – und sie sind nur im [Azure-Portal](https://portal.azure.com/) verfügbar.


Sie erhalten Tarifempfehlungen während der folgenden Aufgaben:

- [Ändern der Dienstebene und Leistungsstufe (Tarif) einer SQL-Datenbank](sql-database-scale-up.md)
- [Upgraden von Azure SQL-Server auf V12](sql-database-upgrade-server-portal.md)
- Navigieren Sie zu Ihrem V12-Server. Weitere Informationen finden Sie unter [Tarifempfehlungen für SQL-Datenbank](sql-database-service-tier-advisor.md).
- [Erstellen eines elastischen Datenbankpools](sql-database-elastic-pool.md#elastic-database-pool-pricing-tier-recommendations)





## Übersicht

Der SQL-Datenbak-Dienst analysiert die aktuellen Leistungs- und Featureanforderungen durch Bewertung der historischen Ressourcennutzung für eine SQL-Datenbank. Darüber hinaus wird der minimal zulässige Tarif basierend auf der Größe der Datenbank und der aktivierten Funktionen für die [Geschäftskontinuität](sql-database-business-continuity.md) bestimmt.

Diese Informationen werden analysiert, und der Tarif und die Leistungsstufe, die für die Ausführung des typischen Workloads der Datenbank und die Aufrechterhaltung ihrer aktuellen Featuregruppe am besten geeignet sind, werden empfohlen.

- Der Dienst überprüft die historischen Daten der letzten 15 bis 30 Tage (Ressourcennutzung, Datenbankgröße und Datenbankaktivität) und führt einen Vergleich zwischen der Menge der verbrauchten Ressourcen und den tatsächlichen Einschränkungen der aktuell verfügbaren Dienstebenen und Leistungsstufen durch.
- Daten werden in Intervallen von 15 Sekunden analysiert, wobei das Resultset jedes Intervalls in die vorhandene Dienstebene und Leistungsstufe kategorisiert wird, welche für die Handhabung des Workloads dieses Resultsets am besten geeignet sind.
- Diese 15-Sekunden-Beispiele werden dann in die breitere Analyse von 15 bis 30 Tagen aggregiert, und die Dienstebene und Leistungsstufe, die 95 % des historischen Workloads optimal bewältigen können, werden empfohlen.

### Empfehlungen

Basierend auf Ihrer Datenbanknutzung können derzeit 2 Kategorien von Empfehlungen auftreten:


| Empfehlung | Beschreibung |
| :--- | :--- |
| Upgrade | Führen Sie ein Upgrade auf eine neue Ebene durch. |
| Nicht verfügbar | Für eine Datenbank sind eine minimale Workload oder ungefähr 35 Tage Aktivität erforderlich. Es sind nicht genügend Daten vorhanden, um eine gültige Empfehlung abzugeben. |

## Abrufen von Tarifempfehlungen

Zum Abrufen von Tarifempfehlungen wählen Sie eine vorhandene Web- oder Business-Datenbank aus, klicken Sie auf **Alle Einstellungen** und dann auf **Tarif (DTUs skalieren)**. (Tarifempfehlungen sind auch verfügbar, wenn Sie die [Schritte für das Upgrade auf Azure SQL-Datenbank V12](sql-database-upgrade-server-portal.md) ausführen.)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Klicken auf **DURCHSUCHEN** > **SQL-Datenbanken**.
4. Klicken Sie im Blatt **SQL-Datenbanken** auf die Datenbank, für die Sie eine Empfehlung anzeigen möchten:

    ![Datenbank auswählen][1]

5. Wählen Sie auf dem Datenbankblatt **Alle Einstellungen** und dann **Tarif (DTUs skalieren)**.


7. **Empfohlene Tarife** wird angezeigt. Dort können Sie auf den empfohlenen Tarif klicken und dann auf die Schaltfläche **Auswählen**, um zu diesem Tarif zu wechseln.

    ![Anmelden für die Vorschau][4]

8. Optional können Sie auf **Nutzungsdetails anzeigen** klicken, um das Blatt **Tarifempfehlungen Details** zu öffnen. Dort können Sie den empfohlenen Tarif für die Datenbank, einen Featurevergleich zwischen aktuellen und empfohlenen Tarifen sowie ein Diagramm für die Analyse der historischen Ressourcennutzung anzeigen.

    ![Anmelden für die Vorschau][5]



## Zusammenfassung

Tarifempfehlungen bieten eine automatisierte Möglichkeit zum Erfassen von Telemetriedaten für jede SQL-Datenbank und zum Empfehlen der besten Kombination von Dienstebene/Leistungsstufe basierend auf den tatsächlichen Leistungs- und Featureanforderungen einer Datenbank. Klicken Sie auf dem Blatt „Einstellungen“ auf **Tarif (DTUs skalieren)**, um Tarifempfehlungen für alle Web- und Business-Datenbanken anzuzeigen.



## Nächste Schritte

Abhängig von den Details Ihrer speziellen Datenbank erfolgt die Durchführung eines Upgrades oder Downgrades in der Regel nicht sofort. Das Portal stellt während des Übergangs der Datenbank auf einen neuen Tarif Benachrichtigungen bereit. Sie können den Upgradestatus jedoch auch überwachen, indem Sie die Sicht [Sys.dm\_operation\_status (Azure SQL-Datenbank)](https://msdn.microsoft.com/library/dn270022.aspx) in der Master-Datenbank des SQL-Datenbankservers abfragen.


<!--Image references-->
[1]: ./media/sql-database-service-tier-advisor/select-database.png
[4]: ./media/sql-database-service-tier-advisor/choose-pricing-tier.png
[5]: ./media/sql-database-service-tier-advisor/usage-details.png


 

<!---HONumber=AcomDC_0810_2016-->