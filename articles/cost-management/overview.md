---
title: Übersicht über Azure Cost Management | Microsoft-Dokumentation
description: Azure Cost Management ist eine Multicloud-Kostenverwaltungslösung, die Ihnen das Verwenden von Azure und anderen Cloudressourcen erleichtert.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: overview
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 05e53688e1350052fdbbc61451df8a51dc3349cd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32162975"
---
# <a name="what-is-azure-cost-management"></a>Was ist Azure-Kostenverwaltung?

Azure Cost Management (lizenziert von Cloudyn, einer Tochtergesellschaft von Microsoft) ermöglicht Ihnen das Nachverfolgen der Cloudnutzung und der Ausgaben für Ihre Azure-Ressourcen sowie andere Cloudanbieter, einschließlich AWS und Google. Leicht verständliche Dashboardberichte unterstützen Sie bei der Kostenzuteilung sowie bei Showbacks und verbrauchsbasierten Kostenzuteilungen. Die Kostenverwaltung unterstützt Sie beim Optimieren der Ausgaben für Ihre Cloud, indem nicht ausgelastete Ressourcen ermittelt werden, die Sie dann verwalten und anpassen können.

Ein Einführungsvideo finden Sie unter [Introduction to Azure Cost Management](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo) (Einführung in Azure Cost Management).

## <a name="monitor-usage-and-spending"></a>Überwachen der Nutzung und Ausgaben

Das Überwachen Ihrer Nutzung und Ausgaben ist für Cloudinfrastrukturen ausgesprochen wichtig, da Organisationen für die Ressourcen zahlen, die Sie im Lauf der Zeit verwenden. Wenn die Nutzung die Schwellenwerte der Vereinbarung übersteigt, kann schnell eine unerwartete Überschreitung der Kosten auftreten. Einige wichtige Faktoren können die Ad-hoc-Überwachung erschweren. Erstens wird beim Projizieren von Kosten basierend auf der durchschnittlichen Nutzung davon ausgegangen, dass Ihre Nutzung über einen bestimmten Abrechnungszeitraum konsistent bleibt. Zweitens ist es wichtig, dass Sie proaktiv Benachrichtigungen erhalten, um Ihre Ausgaben anzupassen, wenn die Kosten sich Ihrem Budget annähern oder dieses überschreiten. Zudem bieten Clouddienstanbieter nicht notwendigerweise Berichte für die Kostenprojektion im Vergleich mit den Schwellenwerten oder für einen Vergleich von Zeiträumen an.

Berichte unterstützen Sie bei der Überwachung Ihrer Ausgaben, um die Cloudnutzung, Kosten und Trends zu analysieren und nachzuverfolgen. Durch das Verwenden von Berichten für den Zeitverlauf können Sie Anomalien erkennen, die von den normalen Trends abweichen. Ineffizienzen in Ihrer Cloudbereitstellung werden in Optimierungsberichten angezeigt. Sie können Ineffizienzen auch in Kostenanalyseberichten feststellen.

![Bericht für die Kosten im Lauf der Zeit](media\overview\cost-over-time-rpt.png)


## <a name="manage-costs"></a>Verwalten von Kosten

Verlaufsdaten können Sie beim Verwalten der Kosten unterstützen, wenn Sie die Nutzung und die Kosten im Lauf der Zeit analysieren, um Trends zu erkennen. Trends werden dann verwendet, um zukünftige Ausgaben vorherzusagen. Die Kostenverwaltung enthält ebenfalls nützliche projizierte Kostenberichte.

Die Kostenzuteilung verwaltet Kosten, indem Ihre Kosten basierend auf Ihrer Tagrichtlinie analysiert werden. Sie können Tags für Ihre benutzerdefinierten Konten, Ressourcen und Entitäten verwenden, um die Kostenzuteilung zu optimieren. Der Kategorie-Manager organisiert Ihre Tags, um zusätzliche Kontrolle zu ermöglichen. Sie verwenden die Kostenzuteilung ebenfalls für Showback und die verbrauchsbasierte Kostenzuteilung, um die Ressourcennutzung und die zugehörigen Kosten anzuzeigen und das Nutzungsverhalten zu beeinflussen oder diese Mandantenkunden in Rechnung zu stellen.

Die Zugriffssteuerung unterstützt Sie beim Verwalten der Kosten, indem sichergestellt wird, dass Benutzer und Teams nur auf die Daten der Kostenverwaltung zugreifen können, die sie benötigen. Sie verwenden eine Entitätsstruktur, die Benutzerverwaltung und geplante Berichte mit Empfängerlisten, um den Zugriff zuzuweisen.

Warnungen unterstützen Sie beim Verwalten der Kosten, indem Sie automatisch benachrichtigt werden, wenn ungewöhnliche Ausgaben oder eine Budgetüberschreitung auftreten. Durch Warnungen können auch andere Beteiligte automatisch über Anomalien bei den Ausgaben und das Risiko einer Budgetüberschreitung benachrichtigt werden. Verschiedene Berichte unterstützen Warnungen, die auf dem Budget und auf Kostenschwellenwerten basieren. Für Konten oder Abonnements von CSP-Partnern werden Warnungen jedoch derzeit nicht unterstützt.

## <a name="improve-efficiency"></a>Verbessern der Effizienz

Mit der Kostenverwaltung können Sie die optimale Nutzung der VMs bestimmen, VMs im Leerlauf identifizieren oder VMs im Leerlauf und nicht angefügte Datenträger entfernen. Mithilfe der Informationen in den Berichten zu Größenempfehlungen und Ineffizienzen können Sie einen Plan erstellen, um die Größe von VMs im Leerlauf zu reduzieren oder diese zu entfernen. Für Konten oder Abonnements von CSP-Partnern werden Optimierungsberichte jedoch derzeit nicht unterstützt.

![Größenempfehlungen](.\media\overview\sizing.png)

Wenn Sie für AWS reservierte Instanzen bereitgestellt haben, können Sie die Nutzung Ihrer reservierten Instanzen mit Optimierungsberichten verbessern, in denen Sie Kaufempfehlungen einsehen, ungenutzte Reservierungen ändern und Bereitstellungen planen können.

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun mit der Kostenverwaltung vertraut sind, sind die nächsten Schritte das Registrieren Ihrer Cloudumgebung und das Untersuchen Ihrer Daten.

- [Register an individual Azure subscription (Registrieren eines einzelnen Azure-Abonnements)](quick-register-azure-sub.md)
