---
title: Bewährte Methoden für die Verwaltungslösung in Azure
description: In diesem Artikel erhalten Sie Tipps zum Erstellen einer Verwaltungslösungsdatei. Hier erfahren Sie, wie Sie mit Datenquellen, Runbooks, Ansichten und Warnungen arbeiten.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: 7cb300297336edcce4294b800520ad570b12bcde
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548160"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Bewährte Methoden für das Erstellen von Verwaltungslösungen in Azure (Vorschau)
> [!NOTE]
> Dies ist die vorläufige Dokumentation für das Erstellen von Verwaltungslösungen in Azure, die sich derzeit in der Vorschau befinden. Jedes unten beschriebene Schema kann sich ändern.  

Dieser Artikel enthält bewährte Methoden für das [Erstellen einer Verwaltungslösungsdatei](solutions-solution-file.md) in Azure.  Diese Informationen werden aktualisiert, sobald weitere bewährte Methoden ermittelt wurden.

## <a name="data-sources"></a>Datenquellen
- Datenquellen können [mit einer Resource Manager-Vorlage konfiguriert](../../azure-monitor/platform/template-workspace-configuration.md) werden, sie sollten jedoch nicht in einer Lösungsdatei enthalten sein.  Der Grund dafür ist, dass das Konfigurieren von Datenquellen derzeit nicht idempotent ist, was bedeutet, dass die vorhandene Konfiguration im Arbeitsbereich des Benutzers von Ihrer Lösung überschrieben werden könnte.<br><br>Beispielsweise werden für Ihre Lösung Warn- und Fehlerereignisse aus dem Anwendungsereignisprotokoll benötigt.  Wenn Sie dies als Datenquelle in Ihrer Lösung angeben, besteht das Risiko, dass Informationsereignisse entfernt werden, wenn der Benutzer dies in seinem Arbeitsbereich konfiguriert hat.  Wenn Sie alle Ereignisse einschließen, werden möglicherweise übermäßig viele Informationsereignisse im Arbeitsbereich des Benutzers erfasst.

- Wenn Ihre Lösung Daten aus einer der Standarddatenquellen erfordert, sollten Sie dies als Voraussetzung definieren.  Geben Sie in der Dokumentation an, dass der Kunde die Datenquelle selbst konfigurieren muss.  
- Fügen Sie allen Ansichten in Ihrer Lösung eine Meldung vom Typ [Datenflussüberprüfung](../../azure-monitor/platform/view-designer-tiles.md) hinzu, um die Benutzer auf Datenquellen hinzuweisen, die konfiguriert werden müssen, sodass die erforderlichen Daten gesammelt werden müssen.  Diese Meldung wird auf der Kachel der Ansicht angezeigt, wenn keine erforderlichen Daten gefunden wurden.


## <a name="runbooks"></a>Runbooks
- Fügen Sie einen [Automation-Zeitplan](../../automation/automation-schedules.md) für jedes Runbook in Ihrer Lösung hinzu, das nach einem Zeitplan ausgeführt werden muss.
- Schließen Sie das [IngestionAPI-Modul](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) in Ihre Lösung ein, das von Runbooks verwendet wird, die Daten in das Log Analytics-Repository schreiben.  Konfigurieren Sie die Lösung so, dass auf diese Ressource [verwiesen](solutions-solution-file.md#solution-resource) wird, damit sie erhalten bleibt, wenn die Lösung entfernt wird.  Auf diese Weise kann das Modul von mehreren Lösungen gemeinsam verwendet werden.
- Verwenden Sie [Automation-Variablen](../../automation/automation-schedules.md), um Werte für die Lösung bereitzustellen, die Benutzer möglicherweise später ändern möchten.  Selbst wenn die Lösung so konfiguriert ist, dass sie die Variable enthält, kann der entsprechende Wert trotzdem geändert werden.

## <a name="views"></a>Sichten
- Alle Lösungen sollten eine einzige Ansicht enthalten, die im Portal des Benutzers angezeigt wird.  Die Ansicht kann mehrere [Visualisierungskomponenten](../../azure-monitor/platform/view-designer-parts.md) zur Veranschaulichung unterschiedlicher Datasets enthalten.
- Fügen Sie allen Ansichten in Ihrer Lösung eine Meldung vom Typ [Datenflussüberprüfung](../../azure-monitor/platform/view-designer-tiles.md) hinzu, um die Benutzer auf Datenquellen hinzuweisen, die konfiguriert werden müssen, sodass die erforderlichen Daten gesammelt werden müssen.
- Konfigurieren Sie die Lösung so, dass sie die Ansicht [enthält](solutions-solution-file.md#solution-resource), damit diese beim Entfernen der Lösung entfernt wird.

## <a name="alerts"></a>Alerts
- Definieren Sie die Liste der Empfänger als Parameter in der Lösungsdatei, damit der Benutzer sie definieren kann, wenn er die Lösung installiert.
- Konfigurieren Sie die Lösung so, dass auf Warnungsregeln [verwiesen](solutions-solution-file.md#solution-resource) wird, damit die Benutzer ihre Konfiguration ändern können.  Möglicherweise möchten die Benutzer die Empfängerliste oder den Schwellenwert der Warnung ändern oder die Warnungsregel deaktivieren. 


## <a name="next-steps"></a>Nächste Schritte
* Durchlaufen Sie die grundlegenden Schritte zum [Entwerfen und Erstellen einer Verwaltungslösung](solutions-creating.md).
* Lesen Sie die Informationen zum [Erstellen einer Verwaltungslösungsdatei](solutions-solution-file.md).
* [Hinzufügen gespeicherter Suchvorgänge und Warnungen](solutions-resources-searches-alerts.md) zu Ihrer Verwaltungslösung
* [Hinzufügen von Ansichten](solutions-resources-views.md) zu Ihrer Verwaltungslösung
* [Hinzufügen von Automation-Runbooks und anderen Ressourcen](solutions-resources-automation.md) zu Ihrer Verwaltungslösung

