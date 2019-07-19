---
title: Leistungsempfehlungen in Azure Database for MariaDB
description: In diesem Artikel wird das Feature „Leistungsempfehlung“ in Azure Database for MariaDB beschrieben.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: a2f9b7597022822272692d20976e1da654b9d524
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462057"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Leistungsempfehlungen in Azure Database for MariaDB

**Anwendungsbereich:** Azure Database for MariaDB 10.2

> [!NOTE]
> Die Leistungsempfehlungen befinden sich in der Vorschauphase.

Mit dem Feature „Leistungsempfehlungen“ werden Ihre Datenbanken analysiert, um angepasste Vorschläge für eine verbesserte Leistung zu erstellen. Um die Empfehlungen zu generieren, untersucht die Analyse verschiedene Datenbankmerkmale einschließlich des Schemas. Aktivieren Sie den [Abfragespeicher](concepts-query-store.md) auf Ihren Server, um das Feature „Leistungsempfehlungen“ vollständig nutzen zu können. Wenn das Leistungsschema auf AUS festgelegt ist, werden durch das Einschalten des Abfragespeichers „performance_schema“ und ein Teil der Leistungsschemainstrumente aktiviert, die für das Feature benötigt werden. Nach der Implementierung von Leistungsempfehlungen sollten Sie die Leistung testen, um die Auswirkungen dieser Änderungen auszuwerten.

## <a name="permissions"></a>Berechtigungen

Zum Ausführen einer Analyse mit dem Leistungsempfehlungen-Feature sind die Berechtigungen **Besitzer** oder **Mitwirkender** erforderlich.

## <a name="performance-recommendations"></a>Empfehlungen zur Leistung

Das Feature [Leistungsempfehlungen](concepts-performance-recommendations.md) analysiert Workloads auf Ihrem Server zum Identifizieren von Indizes mit der Möglichkeit zur Verbesserung der Leistung.

Öffnen Sie im Abschnitt **Intelligente Leistung** in der Menüleiste der Azure-Portalseite für Ihren MariaDB-Server die Option **Leistungsempfehlungen**.

![Leistungsempfehlungen-Startseite](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Wählen Sie **Analysieren** aus, und wählen Sie eine Datenbank aus, mit der die Analyse beginnt. Je nach Workload kann die Analyse einige Minuten dauern. Wenn die Analyse abgeschlossen ist, wird im Portal eine Benachrichtigung angezeigt. Die Analyse führt eine umfassende Untersuchung der Datenbank aus. Es wird empfohlen, Analysen während der Nebenzeiten durchzuführen.

Im Fenster **Empfehlungen** wird eine Liste mit Empfehlungen angezeigt, falls welche gefunden wurden, sowie die zugehörige Abfrage-ID, mit der diese Empfehlung generiert wurde. Mit der Abfrage-ID können Sie die Ansicht [mysql.query_store](concepts-query-store.md#mysqlquery_store) verwenden, um weitere Informationen zur Abfrage zu erhalten.

![Neue Seite „Leistungsempfehlungen“](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Empfehlungen werden nicht automatisch angewendet. Kopieren Sie zum Anwenden der Empfehlung den Abfragetext, und führen Sie ihn auf dem gewünschten Client aus. Denken Sie daran, die implementierte Empfehlung zu testen und zu überwachen, um sie auszuwerten.

## <a name="recommendation-types"></a>Empfehlungstypen

Derzeit werden nur Empfehlungen vom Typ *Index erstellen* unterstützt.

### <a name="create-index-recommendations"></a>Empfehlungen zum Erstellen eines Index

Empfehlungen des Typs *Index erstellen* schlagen neue Indizes vor, um die am häufigsten ausgeführten oder zeitaufwändigen Abfragen in der Workload zu beschleunigen. Für diese Art von Empfehlung muss der [Abfragespeicher](concepts-query-store.md) aktiviert sein. Der Abfragespeicher erfasst Abfrageinformationen und stellt die detaillierten Statistiken zur Laufzeit und Häufigkeit der Abfrage zur Verfügung, die die Analyse verwendet, um die Empfehlung abzugeben.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Überwachung und Optimierung](concepts-monitoring.md) in Azure Database for MariaDB.