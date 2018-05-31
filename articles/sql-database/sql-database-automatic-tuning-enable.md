---
title: Aktivieren der automatischen Optimierung für Azure SQL-Datenbank | Microsoft-Dokumentation
description: Sie können ganz einfach die automatische Optimierung für Ihre Azure SQL-Datenbank aktivieren.
services: sql-database
author: danimir
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: vvasic
ms.openlocfilehash: e4c3a2c1f21bf14bfc75f20dd18cefca68fd2067
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364470"
---
# <a name="enable-automatic-tuning"></a>Aktivieren der automatischen Optimierung

Azure SQL-Datenbank ist ein automatisch verwalteter Datendienst, der kontinuierlich Ihre Abfragen überwacht und Ihnen mitteilt, wie Sie die Leistung Ihrer Workload verbessern können. Sie können Empfehlungen prüfen und manuell anwenden oder Azure SQL-Datenbank die Maßnahmen automatisch anwenden lassen. Letzteres wird als **automatischer Optimierungsmodus** bezeichnet. Die automatische Optimierung kann auf der Server- oder auf der Datenbankebene aktiviert werden.

## <a name="enable-automatic-tuning-on-server"></a>Aktivieren der automatischen Optimierung für den Server
Auf Serverebene kann auf Wunsch die Konfiguration der automatischen Optimierung von „Azure-Standardwerte“ geerbt werden. In den Azure-Standardwerten sind FORCE_LAST_GOOD_PLAN und CREATE_INDEX aktiviert und DROP_INDEX deaktiviert.

### <a name="azure-portal"></a>Azure-Portal
Wenn Sie die automatische Optimierung für den Azure SQL-Datenbank-**Server** aktivieren möchten, navigieren Sie im Azure-Portal zum entsprechenden Server, und aktivieren Sie im Menü die Option **Automatische Optimierung**. Wählen Sie die gewünschten Optionen für die automatische Optimierung und anschließend **Übernehmen** aus.

![Server](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Beachten Sie, dass die Option **DROP_INDEX** zurzeit nicht kompatibel mit Anwendungen ist, die Partitionswechsel und Indexhinweise verwenden. In diesen Fällen sollte sie nicht aktiviert werden.
>

Die Optionen für die automatische Optimierung des Servers werden auf alle Datenbanken auf dem Server angewendet. Standardmäßig erben alle Datenbanken die Konfiguration von ihrem übergeordneten Server, dies kann jedoch außer Kraft gesetzt und einzeln für jede Datenbank angegeben werden.

### <a name="rest-api"></a>REST-API
Weitere Informationen zum Aktivieren der automatischen Optimierung auf Serverebene mithilfe der REST-API finden Sie [hier](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Aktivieren der automatischen Optimierung für eine einzelne Datenbank

Über Azure SQL-Datenbank können Sie die Konfiguration der automatischen Optimierung für einzelne Datenbanken angeben. Auf Datenbankebene kann auf Wunsch die Konfiguration der automatischen Optimierung vom übergeordneten Server oder von „Azure-Standardwerte“ geerbt werden. In den Azure-Standardwerten ist „FORCE_LAST_GOOD_PLAN“ aktiviert, „CREATE_INDEX“ aktiviert und „DROP_INDEX“ deaktiviert.

> [!NOTE]
> Es empfiehlt sich im Allgemeinen, die automatische Optimierung auf der Serverebene zu verwalten, damit für jede Datenbank automatisch die gleichen Konfigurationseinstellungen angewendet werden können. Konfigurieren Sie die automatische Optimierung für eine einzelne Datenbank, wenn diese sich von anderen Datenbanken auf dem gleichen Server unterscheidet.
>

### <a name="azure-portal"></a>Azure-Portal

Wenn Sie die automatische Optimierung für eine **einzelne Datenbank** aktivieren möchten, navigieren Sie im Azure-Portal zur entsprechenden Datenbank, und wählen Sie **Automatische Optimierung** aus. Durch Aktivieren der Option können Sie eine einzelne Datenbank so konfigurieren, dass sie die Einstellungen des Servers übernimmt. Sie können aber auch eine individuelle Konfiguration für eine Datenbank angeben.

![Datenbank](./media/sql-database-automatic-tuning-enable/database.png)

Klicken Sie nach der Wahl der passenden Konfiguration auf **Übernehmen**.

Beachten Sie, dass die Option DROP_INDEX zurzeit nicht kompatibel mit Anwendungen ist, die Partitionswechsel und Indexhinweise verwenden. In diesen Fällen sollte sie nicht aktiviert werden.

### <a name="rest-api"></a>REST-API
Weitere Informationen zum Aktivieren der automatischen Optimierung für eine einzelne Datenbank mithilfe der REST-API finden Sie [hier](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Wenn Sie die automatische Optimierung für eine einzelne Datenbank mithilfe von T-SQL aktivieren möchten, stellen Sie eine Verbindung mit der Datenbank her, und führen Sie die folgende Abfrage aus:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
   ```
   
Wenn Sie die automatische Optimierung auf „AUTO“ festlegen, werden die Azure-Standardwerte verwendet. Bei Verwendung von „INHERIT“ wird die Konfiguration der automatischen Optimierung vom übergeordneten Server geerbt. Bei Verwendung von „CUSTOM“ muss die automatische Optimierung manuell konfiguriert werden.

Wenn Sie über T-SQL einzelne Optionen der automatischen Optimierung konfigurieren möchten, stellen Sie eine Verbindung mit der Datenbank her, und führen Sie eine Abfrage wie die folgende aus:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
   ```
   
Wenn Sie eine Optimierungsoption auf „ON“ festlegen, werden ggf. geerbte Einstellungen der Datenbank überschrieben, und die entsprechende Optimierungsoption wird aktiviert. Gleiches gilt für „OFF“: Auch hier werden ggf. geerbte Einstellungen der Datenbank überschrieben, und die entsprechende Optimierungsoption wird deaktiviert. Bei Verwendung der automatischen Optimierungsoption „DEFAULT“ wird die Konfiguration aus der Einstellung für die automatische Optimierung auf Datenbankebene geerbt.  

## <a name="disabled-by-the-system"></a>Vom System deaktiviert
Die automatische Optimierung überwacht alle Aktionen, die sie für die Datenbank ausführt, und stellt manchmal unter Umständen fest, dass die automatische Optimierung für die Datenbank nicht ordnungsgemäß funktioniert. In diesem Fall wird die Optimierungsoption vom System deaktiviert. Das liegt meistens daran, dass der Abfragespeicher nicht aktiviert oder für eine bestimmte Datenbank schreibgeschützt ist.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Konfigurieren der automatischen Optimierung von E-Mail-Benachrichtigungen

Siehe [Automatische Optimierung von E-Mail-Benachrichtigungen](sql-database-automatic-tuning-email-notifications.md)

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zur automatischen Optimierung sowie zu ihrer Rolle bei der Verbesserung der Leistung finden Sie in [diesem Artikel](sql-database-automatic-tuning.md).
* Eine Übersicht über die Leistungsempfehlungen von Azure SQL-Datenbank finden Sie [hier](sql-database-advisor.md).
* Unter [Query Performance Insight](sql-database-query-performance.md) erfahren Sie, wie Sie die Auswirkungen Ihrer wichtigsten Abfragen auf die Leistung untersuchen können.
