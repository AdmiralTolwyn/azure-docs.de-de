---
title: "Visuelles Überwachen von Azure Data Factory | Microsoft-Dokumentation"
description: "Erfahren Sie, wie Sie Azure Data Factory visuell überwachen."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: 97a8fa1779cd11830781528527ddfe998e0548b6
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/23/2018
---
# <a name="visually-monitor-azure-data-factories"></a>Visuelles Überwachen von Azure Data Factory
Azure Data Factory ist ein cloudbasierter Datenintegrationsdienst, mit dem Sie datengesteuerte Workflows in der Cloud erstellen können, um Datenverschiebungen und Datentransformationen zu orchestrieren und zu automatisieren. Mit Azure Data Factory können Sie datengesteuerte Workflows (sogenannte Pipelines) erstellen und planen, die Daten aus unterschiedlichen Datenspeichern erfassen, diese Daten mithilfe von Compute Services wie Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics und Azure Machine Learning verarbeiten/transformieren und die Ausgabedaten für Datenspeicher wie Azure SQL Data Warehouse veröffentlichen, damit diese von Business Intelligence (BI)-Anwendungen genutzt werden können.
In diesem Schnellstart erfahren Sie, wie Sie Data Factory V2-Pipelines visuell überwachen, ohne eine einzige Codezeile schreiben zu müssen.
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

> [!NOTE]
> Dieser Artikel bezieht sich auf Version 2 von Data Factory, die zurzeit als Vorschau verfügbar ist. Wenn Sie Version 1 des Data Factory-Diensts in der allgemein verfügbaren Version (GA) verwenden, lesen Sie den Artikel [Überwachen und Verwalten von Azure Data Factory-Pipelines mit dem Azure-Portal und PowerShell](v1/data-factory-monitor-manage-app.md).

## <a name="monitor-data-factory-v2-pipelines"></a>Überwachen von Data Factory V2-Pipelines

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Navigieren Sie zum erstellten Data Factory-Blatt im Azure-Portal, und klicken Sie auf die Kachel „Überwachung und Verwaltung“. Dadurch wird die visuelle Überwachung von Azure Data Factory V2 gestartet.

## <a name="list-view-monitoring"></a>Listenansicht der Überwachung

Überwachen Sie die Pipeline und Aktivitätsausführungen in einer einfachen Benutzeroberfläche mit Listenansicht. Alle Ausführungen werden in der Zeitzone des lokalen Browsers angezeigt. Sie können die Zeitzone ändern. Dadurch werden alle Datums- und Zeitfelder auf die ausgewählte Zeitzone ausgerichtet.  

#### <a name="monitoring-pipeline-runs"></a>Überwachen von Pipelineausführungen
Die Listenansicht zeigt jede Pipelineausführung für Ihre Data Factory V2-Pipelines an. Enthaltene Spalten:

| **Spaltenname** | **Beschreibung** |
| --- | --- |
| Pipeline Name | Name der Pipeline. |
| Actions | Einzelne Aktionen sind verfügbar, um Aktivitätsausführungen anzuzeigen. |
| Run Start | Startdatum und -uhrzeit der Pipelineausführung (MM-TT-JJJJ, hh:mm:ss) |
| Duration | Dauer der Ausführung (hh:mm:ss) |
| Triggered By | Manueller Trigger, Zeitplantrigger |
| Status | Fehlerhaft, Erfolgreich, In Bearbeitung |
| Parameter | Parameter der Pipelineausführung (Name-Wert-Paare) |
| Error | Fehler bei der Pipelineausführung (falls vorhanden) |
| Run ID | ID der Pipelineausführung |

![Überwachen der Pipelineausführungen](media/monitor-visually/pipeline-runs.png)

#### <a name="monitoring-activity-runs"></a>Überwachen der Aktivitätsausführungen
Die Listenansicht zeigt die Aktivitätsausführungen an, die der jeweiligen Pipelineausführung entsprechen. Klicken Sie auf das Symbol **Aktivitätsausführungen** unter der Spalte **Aktionen**, um die Aktivitätsausführungen für jede Pipelineausführung anzuzeigen. Enthaltene Spalten:

| **Spaltenname** | **Beschreibung** |
| --- | --- |
| Activity Name | Name der Aktivität innerhalb der Pipeline |
| Activity Type | Art der Aktivität, z.B. Kopieren, HDInsightSpark, HDInsightSpark usw. |
| Run Start | Startdatum und -uhrzeit der Aktivitätsausführung (MM-TT-JJJJ, hh:mm:ss) |
| Duration | Dauer der Ausführung (hh:mm:ss) |
| Status | Fehlerhaft, Erfolgreich, In Bearbeitung |
| Eingabe | Ein JSON-Array, das die Aktivitätseingaben beschreibt. |
| Output | Ein JSON-Array, das die Aktivitätsausgaben beschreibt. |
| Error | Fehler bei der Aktivitätsausführung (falls vorhanden) |

![Überwachung der Aktivitätsausführungen](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Sie müssen im oberen Bereich auf das Symbol **Aktualisieren** klicken, um die Liste der Pipelines und Aktivitätsausführungen zu aktualisieren. Ein automatisches Update wird derzeit nicht unterstützt.
>

![Aktualisieren](media/monitor-visually/refresh.png)

## <a name="features"></a>Features

#### <a name="rich-ordering-and-filtering"></a>Umfangreiches Sortieren und Filtern

Sortieren Sie Pipelineausführungen auf- oder absteigend nach Start der Ausführung, und filtern Sie Pipelineausführungen nach folgenden Spalten:

| **Spaltenname** | **Beschreibung** |
| --- | --- |
| Pipeline Name | Name der Pipeline. Die Optionen enthalten Schnellfilter für „Letzte 24 Stunden“, „Letzte Woche“, „Letzte 30 Tage“ oder einen benutzerdefinierten Zeitraum. |
| Run Start | Startdatum und -uhrzeit der Pipelineausführung |
| Run Status | Filtern von Ausführungen nach Status, z.B. Erfolgreich, Fehlgeschlagen, In Bearbeitung |

![Filter](media/monitor-visually/filter.png)

#### <a name="addremove-columns-to-list-view"></a>Hinzufügen oder Entfernen von Spalten zur oder aus der Listenansicht
Klicken Sie mit der rechten Maustaste auf den Header der Listenansicht, und wählen Sie die Spalten aus, die in der Listenansicht angezeigt werden sollen.

![Spalten](media/monitor-visually/columns.png)

#### <a name="reorder-column-widths-in-list-view"></a>Neuanordnen der Spaltenbreiten in der Listenansicht
Erhöhen oder verringern Sie die Spaltenbreiten in der Listenansicht, indem Sie mit der Maus auf die Spaltenüberschrift zeigen.

#### <a name="select-data-factory"></a>Data Factory auswählen
Zeigen Sie auf das Symbol „Data Factory“ im oberen linken Bereich. Klicken Sie auf das Pfeilsymbol, um eine Liste der Azure-Abonnements und Data Factorys anzuzeigen, die Sie überwachen können.

![Data Factory auswählen](media/monitor-visually/select-datafactory.png)

#### <a name="guided-tours"></a>Führungen
Klicken Sie auf das Symbol „Informationen“ im unteren linken Bereich und dann auf „Führungen“, um eine ausführliche Anleitung zum Überwachen Ihrer Pipeline und Aktivitätsausführungen zu erhalten.

![Führungen](media/monitor-visually/guided-tours.png)

#### <a name="feedback"></a>Feedback
Klicken Sie auf das Symbol „Feedback“, um uns Ihr Feedback zu den verschiedenen Features oder zu möglichen Problemen zu übermitteln.

![Feedback](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>Nächste Schritte

Im Artikel [Monitor and manage pipelines programmatically (Programmgesteuertes Überwachen und Verwalten von Pipelines)](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) erfahren Sie mehr zum Überwachen und Verwalten von Pipelines.
