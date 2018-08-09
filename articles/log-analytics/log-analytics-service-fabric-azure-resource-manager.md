---
title: Bewerten von Service Fabric-Anwendungen mit Log Analytics über das Azure-Portal | Microsoft-Dokumentation
description: Mit der Service Fabric-Lösung in Log Analytics können Sie über das Azure-Portal die Risiken und Integrität Ihrer Service Fabric-Anwendungen, -Microservices, -Knoten und -Cluster bewerten.
services: log-analytics
documentationcenter: ''
author: niniikhena
manager: jochan
editor: ''
ms.assetid: 9c91aacb-c48e-466c-b792-261f25940c0c
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: nini
ms.component: na
ms.openlocfilehash: 9bc1f7d9eab73a086e664dcc520ecf26befbbf0e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432890"
---
# <a name="assess-service-fabric-applications-and-micro-services-with-the-azure-portal"></a>Bewerten von Service Fabric-Anwendungen und -Microservices über das Azure-Portal

> [!div class="op_single_selector"]
> * [Resource Manager](log-analytics-service-fabric-azure-resource-manager.md)
> * [PowerShell](log-analytics-service-fabric.md)
>
>

![Symbol Service Fabric](./media/log-analytics-service-fabric/service-fabric-assessment-symbol.png)

In diesem Artikel wird beschrieben, wie Sie die Service Fabric-Lösung in Log Analytics nutzen können, um Probleme im gesamten Service Fabric-Cluster zu identifizieren und beheben.

Die Service Fabric-Lösung verwendet Azure-Diagnosedaten aus den Service Fabric-VMs, die aus den Azure Diagnostics (WAD)-Tabellen gesammelt werden. Anschließend werden von Log Analytics Ereignisse des Service Fabric-Frameworks gelesen, einschließlich **Reliable Service-Ereignissen**, **Actor-Ereignissen**, **Betriebsereignissen** und **benutzerdefinierter ETW-Ereignisse**. Mit dem Lösungsdashboard können Sie wichtige Probleme und relevante Ereignisse in der Service Fabric-Umgebung anzeigen.

Um mit der Verwendung der Lösung zu beginnen, müssen Sie das Service Fabric-Cluster mit einem Log Analytics-Arbeitsbereich verbinden. Dabei sind drei Szenarien möglich:

1. Wenn Sie den Service Fabric-Cluster nicht bereitgestellt haben, führen Sie die Schritte in ***Bereitstellen eines Service Fabric-Clusters, der mit einem Log Analytics-Arbeitsbereich verbunden ist*** aus, um einen neuen Cluster bereitzustellen und für das Erstellen von Berichten an Log Analytics zu konfigurieren.
1. Wenn Sie Leistungsindikatoren von den Hosts erfassen müssen, um im Service Fabric-Cluster andere Verwaltungslösungen, z.B. Sicherheit, zu verwenden, führen Sie die unter ***Bereitstellen eines Service Fabric-Clusters, der mit einem Log Analytics-Arbeitsbereich mit installierter VM-Erweiterung verbunden ist*** erklärten Schritte aus.
1. Wenn Sie den Service Fabric-Cluster bereits bereitgestellt haben und ihn mit Log Analytics verbinden möchten, führen Sie die Schritte in ***Hinzufügen eines vorhandenen Speicherkontos zu Log Analytics*** aus.

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>Bereitstellen eines Service Fabric-Clusters und Erstellen eines Log Analytics-Arbeitsbereichs für dessen Überwachung
Mit dieser Vorlage werden die folgenden Aktionen ausgeführt:

1. Ein bereits mit einem Log Analytics-Arbeitsbereich verbundener Azure Service Fabric-Cluster wird bereitgestellt. Sie können während des Bereitstellens der Vorlage einen neuen Arbeitsbereich erstellen oder den Namen eines bereits vorhandenen Log Analytics-Arbeitsbereichs eingeben.
1. Dem Log Analytics-Arbeitsbereich wird das Diagnosespeicherkonto hinzugefügt.
1. Die Service Fabric-Lösung wird im Log Analytics-Arbeitsbereich aktiviert.

[![Bereitstellen in Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)

Nachdem Sie oben auf die Schaltfläche zum Bereitstellen geklickt haben, wird das Azure-Portal geöffnet, in dem Sie Parameter bearbeiten können. Sie müssen eine neue Ressourcengruppe erstellen, wenn Sie einen neuen Log Analytics-Arbeitsbereichsnamen eingeben:

![Service Fabric](./media/log-analytics-service-fabric/2.png)

![Service Fabric](./media/log-analytics-service-fabric/3.png)

Akzeptieren Sie die rechtlichen Bedingungen, und klicken Sie auf **Erstellen**, um die Bereitstellung zu starten. Sobald die Bereitstellung abgeschlossen ist, werden der neu erstellte Arbeitsbereich und Cluster angezeigt, und die Tabellen „WADServiceFabric*Event“, „WADWindowsEventLogs“ und „WADETWEvent“ wurden hinzugefügt:

![Service Fabric](./media/log-analytics-service-fabric/4.png)

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace-with-vm-extension-installed"></a>Bereitstellen eines Service Fabric-Clusters, der mit einem Log Analytics-Arbeitsbereich mit installierter VM-Erweiterung verbunden ist.

Mit dieser Vorlage werden die folgenden Aktionen ausgeführt:

1. Ein bereits mit einem Log Analytics-Arbeitsbereich verbundener Azure Service Fabric-Cluster wird bereitgestellt. Sie können einen neuen Arbeitsbereich erstellen oder einen vorhandenen Arbeitsbereich verwenden.
1. Dem Log Analytics-Arbeitsbereich werden die Diagnosespeicherkonten hinzugefügt.
1. Die Service Fabric-Lösung wird im Log Analytics-Arbeitsbereich aktiviert.
1. In jeder VM-Skalierungsgruppe des Service Fabric-Clusters wird die Microsoft Monitoring Agent-Erweiterung installiert. Wenn Microsoft Monitoring Agent installiert ist, können Sie die Leistungsmetriken der Knoten anzeigen.

[![In Azure bereitstellen](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)

Geben Sie mit den gleichen Schritten wie oben die erforderlichen Parameter ein, und starten Sie eine Bereitstellung. Erneut werden der neue Arbeitsbereich, der neue Cluster und die neuen WAD-Tabellen angezeigt, die erstellt wurden:

![Service Fabric](./media/log-analytics-service-fabric/5.png)

### <a name="viewing-performance-data"></a>Anzeigen von Leistungsdaten

So zeigen Sie die Leistungsdaten der Knoten an:


[!INCLUDE [log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

- Starten Sie im Azure-Portal den Log Analytics-Arbeitsbereich.
  ![Service Fabric](./media/log-analytics-service-fabric/6.png)
- Navigieren Sie im linken Bereich zu „Einstellungen“, und wählen Sie „Daten“ >> „Windows-Leistungsindikatoren“ >> „Ausgewählte Leistungsindikatoren hinzufügen“ aus: ![Service Fabric](./media/log-analytics-service-fabric/7.png)
- Verwenden Sie in „Protokollsuche“ die folgenden Abfragen, um die wichtigsten Metriken der Knoten zu untersuchen:

    a. Vergleichen Sie die durchschnittliche CPU-Auslastung sämtlicher Knoten während der letzten Stunde, um zu erkennen, auf welchen Knoten Probleme auftreten und in welchem Zeitintervall auf einem Knoten eine Spitzenauslastung aufgetreten ist:

    ```
    Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/10.png)

    b. Mit dieser Abfrage zeigen Sie ähnliche Liniendiagramme für den verfügbaren Speicher auf jedem Knoten an:

    ```
    Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    Verwenden Sie diese Abfrage, um eine Liste aller Knoten mit dem genauen Durchschnittswert der verfügbaren Megabytes für jeden Knoten anzuzeigen:

    ```
    Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/11.png)

    c. Falls Sie einen Drilldown auf einen bestimmten Knoten ausführen möchten, indem Sie die durchschnittliche, minimale, maximale und 75.-Perzentil-CPU-Nutzung pro Stunde untersuchen, können Sie hierzu diese Abfrage verwenden (ersetzen Sie den Wert im Feld „Computer“):

    ```
    Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/12.png)

Weitere Informationen zu den Leistungsmetriken in Log Analytics finden Sie im [Operations Management Suite-Blog](https://blogs.technet.microsoft.com/msoms/tag/metrics/).


## <a name="adding-an-existing-storage-account-to-log-analytics"></a>Hinzufügen eines vorhandenen Speicherkontos zu Log Analytics

Mit dieser Vorlage werden einem neuen oder vorhandenen Log Analytics-Arbeitsbereich die vorhandenen Speicherkonten hinzugefügt.

[![In Azure bereitstellen](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

> [!NOTE]
> Wenn Sie mit einem bereits vorhandenen Log Analytics Arbeitsbereich arbeiten, aktivieren Sie beim Auswählen einer Ressourcengruppe „Vorhandene verwenden“, und suchen Sie die Ressourcengruppe, die den Log Analytics-Arbeitsbereich enthält. Erstellen Sie andernfalls eine neue Ressourcengruppe.
> ![Service Fabric](./media/log-analytics-service-fabric/8.png)
>
>

Nachdem diese Vorlage bereitgestellt wurde, wird das Speicherkonto angezeigt, das mit dem Log Analytics Arbeitsbereich verbunden ist. In diesem Fall habe ich dem Exchange-Arbeitsbereich, den ich oben erstellt habe, ein weiteres Speicherkonto hinzugefügt.
![Service Fabric](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>Anzeigen von Service Fabric-Ereignissen

Nachdem die Bereitstellungen abgeschlossen wurden und die Service Fabric-Lösung im Arbeitsbereich aktiviert wurde, wählen Sie im Log Analytics-Portal die Kachel **Service Fabric** aus, um das Service Fabric-Dashboard zu öffnen. Das Dashboard enthält die Spalten, die in der folgenden Tabelle angegeben sind. In jeder Spalte sind die zehn häufigsten Warnungen entsprechend den Kriterien der Spalte für den angegebenen Zeitbereich aufgeführt. Sie können eine Protokollsuche durchführen, mit der die gesamte Liste ausgegeben wird, indem Sie rechts unten in jeder Spalte auf **Alle anzeigen** oder auf die Spaltenüberschrift klicken.

| **Service Fabric-Ereignis** | **description** |
| --- | --- |
| Relevante Probleme |Eine Anzeige von Problemen, z. B. „RunAsyncFailures“, „RunAsyncCancellations“ und Knotenausfälle. |
| Betriebsereignisse |Relevante Betriebsereignisse, z. B. Anwendungsupgrades und Bereitstellungen. |
| Reliable Service-Ereignisse |Relevante Reliable Service-Ereignisse, z. B. „RunAsyncInvocations“. |
| Actor-Ereignisse |Relevante Actor-Ereignisse, die von den Microservices generiert wurden, z. B. Actor-Aktivierungen und -Deaktivierungen, durch eine Actor-Methode ausgelöste Ausnahmen usw. |
| Anwendungsereignisse |Alle benutzerdefinierten ETW-Ereignisse, die von den Anwendungen generiert wurden. |

![Service Fabric-Dashboard](./media/log-analytics-service-fabric/sf3.png)

![Service Fabric-Dashboard](./media/log-analytics-service-fabric/sf4.png)

Die folgende Tabelle enthält die Datensammlungsmethoden und andere Details dazu, wie Daten für Service Fabric erfasst werden.

| Plattform | Direkt-Agent | Operations Manager-Agent | Azure Storage | Operations Manager erforderlich? | Daten vom Operations Manager-Agent über Verwaltungsgruppe gesendet | Sammlungshäufigkeit |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |  |  | &#8226; |  |  |10 Minuten |

> [!NOTE]
> Sie können den Bereich dieser Ereignisse in der Service Fabric-Lösung ändern, indem Sie am oberen Rand des Dashboards auf **Daten basierend auf „Letzte 7 Tage“** klicken. Sie können auch Ereignisse anzeigen, die innerhalb der letzten 7 Tage, innerhalb des letzten Tags oder innerhalb der letzten sechs Stunden generiert wurden. Oder wählen Sie **Benutzerdefiniert** aus, um einen benutzerdefinierten Datumsbereich anzugeben.
>
>

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie die [Protokollsuche in Log Analytics](log-analytics-log-searches.md), um ausführliche Service Fabric-Ereignisdaten anzuzeigen.
