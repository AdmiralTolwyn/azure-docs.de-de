---
title: Aktivieren von Azure Monitor für VMs (Vorschauversion) für die Auswertung | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Monitor für VMs für einen einzelnen virtuellen Azure-Computer oder eine VM-Skalierungsgruppe auswerten.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: ec909bcd16f923bbd7036f6a69df2bbb07e561b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122509"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Aktivieren von Azure Monitor für VMs (Vorschauversion) für die Auswertung

Sie können Azure Monitor für VMs (Vorschauversion) für eine kleine Anzahl virtueller Azure-Computer (VMs), eine einzelne VM oder eine VM-Skalierungsgruppe auswerten. Die einfachste und direkteste Methode zum Aktivieren der Überwachung ist über das Azure-Portal. Ihr Ziel ist es, die VMs zu überwachen und Leistungs- oder Verfügbarkeitsprobleme zu ermitteln. 

Bevor Sie beginnen, sollten Sie die [Voraussetzungen](vminsights-enable-overview.md) überprüfen und sicherstellen, dass Ihr Abonnement und Ihre Ressourcen diese Anforderungen erfüllen.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Aktivieren der Überwachung für eine einzelne Azure-VM
So aktivieren Sie die Überwachung Ihrer Azure-VM:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie **Virtuelle Computer**.

1. Wählen Sie einen virtuellen Computer in der Liste aus.

1. Wählen Sie auf der Seite der VM im Abschnitt **Überwachung** den Eintrag **Insights (Vorschau)** aus.

1. Wählen Sie auf der Seite **Insights (Vorschau)** **Jetzt testen** aus.

    ![Aktivieren von Azure Monitor for VMs für eine VM](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. Wenn Sie im selben Abonnement einen Log Analytics-Arbeitsbereich haben, wählen Sie ihn auf der Seite **Azure Monitor Insights Onboarding** (Onboarding von Azure Monitor Insights) in der Dropdownliste aus.  

    In der Liste sind Standardarbeitsbereich und Speicherort, in dem die VM im Abonnement bereitgestellt wird, vorab ausgewählt. 

    >[!NOTE]
    >Informationen zum Erstellen eines neuen Log Analytics-Arbeitsbereichs für das Speichern der Überwachungsdaten aus der VM finden Sie unter [Erstellen eines Log Analytics-Arbeitsbereichs](../../azure-monitor/learn/quick-create-workspace.md). Der Log Analytics-Arbeitsbereich muss einer der [unterstützten Regionen](vminsights-enable-overview.md#log-analytics) angehören.

Nachdem Sie die Überwachung aktiviert haben, müssen Sie möglicherweise etwa 10 Minuten warten, bis die Integritätsmetriken für die VM angezeigt werden.

![Aktivieren von Azure Monitor for VMs – Verarbeiten der Überwachungsbereitstellung](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Aktivieren der Überwachung für eine einzelne VM-Skalierungsgruppe

So aktivieren Sie die Überwachung Ihrer Azure-VM-Skalierungsgruppe:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie **Virtual Machine Scale Sets** aus.

3. Wählen Sie aus der Liste eine VM-Skalierungsgruppe aus.

4. Wählen Sie auf der Seite der VM-Skalierungsgruppe im Abschnitt **Überwachung** den Eintrag **Insights (Vorschau)** aus.

5. Wenn Sie einen bereits vorhandenen Log Analytics-Arbeitsbereich verwenden möchten, wählen Sie ihn auf der Seite **Insights (Vorschau)** aus der Dropdownliste aus.

    In der Liste sind Standardarbeitsbereich und Speicherort, für den die VM im Abonnement bereitgestellt wird, vorab ausgewählt. 

    ![Aktivieren von Azure Monitor für VMs für eine VM-Skalierungsgruppe](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >Informationen zum Erstellen eines neuen Log Analytics-Arbeitsbereichs für das Speichern der Überwachungsdaten aus der VM-Skalierungsgruppe finden Sie unter [Erstellen eines Log Analytics-Arbeitsbereichs](../learn/quick-create-workspace.md). Der Log Analytics-Arbeitsbereich muss einer der [unterstützten Regionen](vminsights-enable-overview.md#log-analytics) angehören.

Nachdem Sie die Überwachung aktiviert haben, müssen Sie möglicherweise etwa 10 Minuten warten, bis die Überwachungsdaten für die Skalierungsgruppe angezeigt werden.

>[!NOTE]
>Wenn Sie ein manuelles Upgrademodell für Ihre Skalierungsgruppe verwenden, führen Sie ein Upgrade für die Instanzen durch, um die Einrichtung abzuschließen. Sie können die Upgrades über die Seite **Instanzen** im Abschnitt **Einstellungen** starten.

![Aktivieren von Azure Monitor for VMs – Verarbeiten der Überwachungsbereitstellung](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

Nachdem Sie die Überwachung für Ihre VM oder VM-Skalierungsgruppe aktiviert haben, stehen die Überwachungsinformationen für die Analyse in Azure Monitor für VMs bereit. 

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Verwenden des Integritätsfeatures finden Sie unter [Grundlegendes zur Integrität Ihrer Azure Monitor-VMs](vminsights-health.md). 
* Informationen zu ermittelten Anwendungsabhängigkeiten finden Sie unter [Verwenden der Zuordnung in Azure Monitor für VMs](vminsights-maps.md). 
* Informationen zum Erkennen von Engpässen, der Gesamtauslastung und der Leistung Ihrer VM finden Sie unter [Anzeigen der Leistung von Azure-VMs](vminsights-performance.md).