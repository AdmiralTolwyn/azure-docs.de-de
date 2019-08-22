---
title: Entwerfen Ihrer Azure Monitor-Protokollbereitstellung | Microsoft-Dokumentation
description: In diesem Artikel werden die Überlegungen und Empfehlungen für Kunden beschrieben, die sich auf die Bereitstellung eines Arbeitsbereichs in Azure Monitor vorbereiten.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: magoedte
ms.openlocfilehash: 1c2416d9fb1d45116bb6594b29863c1fe8f524a3
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883203"
---
# <a name="designing-your-azure-monitor-logs-deployment"></a>Entwerfen Ihrer Azure Monitor-Protokollbereitstellung

Azure Monitor speichert [Protokoll](data-platform-logs.md)daten in einem Log Analytics-Arbeitsbereich, bei dem es sich um eine Azure-Ressource und einen Container handelt, in dem Daten gesammelt und aggregiert werden und der als eine administrative Grenze dient. Sie können einen oder mehrere Arbeitsbereiche in Ihrem Azure-Abonnement bereitstellen, doch müssen Sie einige Aspekte beachten, um sicherzustellen, dass Ihre anfängliche Bereitstellung unseren Richtlinien entspricht und wir Ihnen so eine kosteneffiziente, verwaltbare und skalierbare Bereitstellung bieten können, die die Anforderungen Ihrer Organisation erfüllt.

Daten in einem Arbeitsbereich sind in Tabellen organisiert, von denen jede unterschiedliche Arten von Daten speichert und über mehrere eindeutige Eigenschaften verfügt, die auf der Ressource basieren, die die Daten generiert. Die meisten Datenquellen schreiben in ihre eigenen Tabellen in einem Log Analytics Arbeitsbereich.

![Beispiel eines Arbeitsbereichsdatenmodells](./media/design-logs-deployment/logs-data-model-01.png)

Ein Log Analytics-Arbeitsbereich bietet Folgendes:

* Einen geografischen Standort für die Speicherung von Daten.
* Datenisolation durch Gewähren von Zugriffsrechten für verschiedene Benutzer gemäß einer unserer empfohlenen Entwurfsstrategien.
* Bereich für die Konfiguration von Einstellungen wie [Tarif](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [Aufbewahrung](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) und [Datenobergrenzen](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap).

Dieser Artikel bietet eine ausführliche Übersicht über die Überlegungen zu Entwurf und Migration, eine Übersicht über die Zugriffssteuerung und Informationen zu den von uns empfohlenen Entwurfsimplementierungen für Ihre IT-Organisation.

## <a name="important-considerations-for-an-access-control-strategy"></a>Wichtige Überlegungen für eine Zugriffssteuerungsstrategie

Die Ermittlung der Anzahl benötigter Arbeitsbereiche wird durch eine oder mehrere der folgenden Anforderungen beeinflusst:

* Sie sind ein globales Unternehmen und müssen Protokolldaten aus Gründen der Datensouveränität bzw. aus Compliancegründen in bestimmten Regionen speichern.
* Sie nutzen Azure und möchten Gebühren für ausgehende Datenübertragungen vermeiden, indem Sie einen Arbeitsbereich in derselben Region wie die verwalteten Azure-Ressourcen nutzen.
* Sie verwalten mehrere Abteilungen oder Geschäftseinheiten und möchten, dass diesen nur jeweils die eigenen Daten angezeigt werden. Außerdem besteht keine geschäftliche Notwendigkeit einer konsolidierten abteilungs- oder geschäftseinheitsübergreifenden Ansicht.

IT-Organisationen werden heute entweder zentralisiert oder dezentralisiert oder in einer Hybridversion beider Strukturen modelliert. Infolgedessen werden häufig die folgenden Modelle der Arbeitsbereichsbereitstellung entsprechend einer dieser Organisationsstrukturen verwendet:

* **Zentralisiert**: Alle Protokolle werden in einem zentralen Arbeitsbereich gespeichert und von einem einzigen Team verwaltet, wobei Azure Monitor differenzierten Zugriff pro Team bietet. In diesem Szenario ist es einfach, Protokolle zu verwalten, ressourcenübergreifend zu durchsuchen und übergreifend zu korrelieren. Der Arbeitsbereich kann je nach Datenmenge, die von mehreren Ressourcen in Ihrem Abonnement gesammelt wird, erheblich anwachsen, und zusätzlichen Verwaltungsaufwand bei der Zugriffssteuerung für verschiedene Benutzer bedeuten.
* **Dezentralisiert**: Für jedes Team wird ein eigener Arbeitsbereich in einer Ressourcengruppe erstellt, den es besitzt und verwaltet, und die Protokolldaten werden nach Ressource getrennt. In diesem Szenario kann der Arbeitsbereich geschützt werden und die Zugriffssteuerung ist mit dem Ressourcenzugriff konsistent, doch ist es schwierig, Protokolle übergreifend zu korrelieren. Benutzer, die einen umfassenden Überblick über viele Ressourcen benötigen, können die Daten nicht auf sinnvolle Weise analysieren.
* **Hybrid**: Konformitätsanforderungen für die Sicherheitsüberwachung machen dieses Szenario noch komplizierter, da viele Organisationen beide Bereitstellungsmodelle parallel implementieren. Dies führt häufig zu einer komplexen, teuren und schwer zu verwaltenden Konfiguration mit Lücken in der Protokollabdeckung.

Wenn Sie die Log Analytics-Agents zum Sammeln von Daten verwenden, muss Ihnen zum Planen der Agentbereitstellung Folgendes bekannt sein:

* Um Daten von Windows-Agents zu sammeln, können Sie [jeden Agent so konfigurieren, dass er an einen oder mehrere Arbeitsbereiche berichtet](../../azure-monitor/platform/agent-windows.md), auch wenn er parallel an eine System Center Operations Manager-Verwaltungsgruppe berichtet. Der Windows-Agent kann an bis zu vier Arbeitsbereiche berichten.
* Der Linux-Agent bietet keine Unterstützung für Multihosting und kann nur an einen einzelnen Arbeitsbereich berichten.

Wenn Sie System Center Operations Manager 2012 R2 oder höher verwenden:

* Jede Operations Manager-Verwaltungsgruppe kann [nur mit einem Arbeitsbereich verbunden sein](../platform/om-agents.md). 
* Linux-Computer, die an eine Verwaltungsgruppe berichten, müssen so konfiguriert sein, dass Sie direkt an einen Log Analytics-Arbeitsbereich berichten. Wenn Ihre Linux-Computer bereits direkt an einen Arbeitsbereich berichten und Sie diese mit Operations Manager überwachen möchten, führen Sie die Schritte zum [Berichten an eine Operations Manager-Verwaltungsgruppe](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group) aus. 
* Sie können den Log Analytics-Windows-Agent auf dem Windows-Computer installieren und ihn sowohl an Operations Manager, der in einen Arbeitsbereich integriert ist, als auch an einen anderen Arbeitsbereich berichten lassen.

## <a name="access-control-overview"></a>Übersicht über die Zugriffssteuerung

Mit der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) können Sie Benutzern und Gruppen nur den Zugriff gewähren, den sie zum Arbeiten mit Überwachungsdaten in einem Arbeitsbereich benötigen. Dies ermöglicht Ihnen die Anpassung an das Betriebsmodell Ihrer IT-Organisation mit einem einzelnen Arbeitsbereich zum Speichern gesammelter Daten auf allen Ihren Ressourcen. Wenn Sie beispielsweise dem Team Zugriff gewähren, das für die auf virtuellen Azure-Computern (VMs) gehosteten Infrastrukturdienste zuständig ist, hat das Team nur Zugriff auf die Protokolle, die von den VMs generiert werden. Dies entspricht unserem neuen Ressourcenkontext-Protokollmodell. Die Grundlage für dieses Modell ist, dass jeder von einer Azure-Ressource ausgegebene Protokolldatensatz automatisch dieser Ressource zugeordnet wird. Protokolle werden an einen zentralen Arbeitsbereich weitergeleitet, der die Bereichsdefinition und RBAC basierend auf den Ressourcen beachtet.

Die Daten, auf die ein Benutzer Zugriff hat, werden durch eine Kombination von Faktoren bestimmt, die in der folgenden Tabelle aufgeführt sind. Jeder dieser Faktoren wird in den folgenden Abschnitten beschrieben.

| Faktor | BESCHREIBUNG |
|:---|:---|
| [Zugriffsmodus](#access-mode) | Die Methode, die der Benutzer verwendet, um auf den Arbeitsbereich zuzugreifen.  Definiert den Bereich der verfügbaren Daten und den Zugriffssteuerungsmodus, der angewendet wird. |
| [Zugriffssteuerungsmodus](#access-control-mode) | Eine Einstellung für den Arbeitsbereich, die definiert, ob Berechtigungen auf der Arbeitsbereich- oder Ressourcenebene angewendet werden. |
| [Berechtigungen](manage-access.md#manage-accounts-and-users) | Berechtigungen, die auf einzelne Benutzer oder Gruppen von Benutzern für den Arbeitsbereich oder die Ressource angewendet werden. Definiert, auf welche Daten der Benutzer zugreifen kann. |
| [RBAC auf Tabellenebene](manage-access.md#table-level-rbac) | Optionale präzise Berechtigungen, die für alle Benutzer unabhängig von deren Zugriffs- oder Zugriffssteuerungsmodus gelten. Definiert die Datentypen, auf die ein Benutzer zugreifen kann. |

## <a name="access-mode"></a>Zugriffsmodus

Der *Zugriffsmodus* bezieht sich darauf, wie ein Benutzer auf einen Log Analytics-Arbeitsbereich zugreift. Er definiert den Umfang der Daten, auf die zugegriffen werden kann. 

Benutzer haben zwei Möglichkeiten, auf die Daten zuzugreifen:

* **Arbeitsbereichskontext**: Sie können alle Protokolle in dem Arbeitsbereich anzeigen, für den Sie die Berechtigung besitzen. Abfragen in diesem Modus beziehen sich auf alle Daten in allen Tabellen im Arbeitsbereich. Dies ist der Zugriffsmodus, der verwendet wird, wenn auf Protokolle mit dem Arbeitsbereich als Bereich zugegriffen wird, z.B. wenn Sie **Protokolle** aus dem Menü **Azure Monitor** im Azure-Portal auswählen.

    ![Log Analytics-Kontext vom Arbeitsbereich](./media/design-logs-deployment/query-from-workspace.png)

* **Ressourcenkontext**: Wenn Sie auf den Arbeitsbereich für eine bestimmte Ressource, eine Ressourcengruppe oder ein Abonnement zugreifen (etwa, wenn Sie **Protokolle** aus einem Ressourcenmenü im Azure-Portal auswählen), können Sie Protokolle nur für diese Ressource in allen Tabellen anzeigen, auf die Sie Zugriff besitzen. Abfragen in diesem Modus beziehen sich auf nur Daten, die dieser Ressource zugeordnet sind. Dieser Modus ermöglicht außerdem eine differenzierte rollenbasierte Zugriffssteuerung (RBAC).

    ![Log Analytics-Kontext von der Ressource](./media/design-logs-deployment/query-from-resource.png)

    > [!NOTE]
    > Protokolle sind für Abfragen im Ressourcenkontext nur dann verfügbar, wenn sie der jeweiligen Ressource ordnungsgemäß zugeordnet wurden. Zurzeit gelten für die folgenden Ressourcen Einschränkungen:
    > - Computer außerhalb von Azure
    > - Service Fabric
    > - Application Insights
    >
    > Sie können testen, ob Protokolle ordnungsgemäß ihrer Ressource zugeordnet wurden, indem Sie eine Abfrage ausführen und die Datensätze überprüfen, an denen Sie interessiert sind. Wenn sich die richtige Ressourcen-ID in der Eigenschaft [_ResourceId](log-standard-properties.md#_resourceid) befindet, stehen Daten für ressourcenbezogene Abfragen zur Verfügung.

Azure Monitor bestimmt automatisch den richtigen Modus je nach Kontext, aus dem Sie die Protokollsuche ausführen. Der Bereich wird immer im oberen linken Abschnitt von Log Analytics angezeigt.

### <a name="comparing-access-modes"></a>Vergleichen von Zugriffsmodi

Die Zugriffsmodi werden in der folgenden Tabelle zusammengefasst:

| | Arbeitsbereichskontext | Ressourcenkontext |
|:---|:---|:---|
| Für wen ist das jeweilige Modell vorgesehen? | Zentraladministration. Administratoren, die die Datensammlung konfigurieren müssen, und Benutzer, die Zugriff auf eine Vielzahl von Ressourcen benötigen. Zurzeit auch erforderlich für Benutzer, die Zugriff auf Protokolle für Ressourcen außerhalb von Azure benötigen. | Anwendungsteams. Administratoren von Azure-Ressourcen, die überwacht werden. |
| Was ist für einen Benutzer erforderlich, um Protokolle anzuzeigen? | Berechtigungen für den Arbeitsbereich. Weitere Informationen finden Sie unter **Arbeitsbereichberechtigungen** in [Verwalten von Konten und Benutzern](manage-access.md#manage-accounts-and-users). | Lesezugriff auf die Ressource. Weitere Informationen finden Sie unter **Ressourcenberechtigungen** in [Verwalten von Konten und Benutzern](manage-access.md#manage-accounts-and-users). Berechtigungen können vererbt (z.B. aus der enthaltenden Ressourcengruppe) oder der Ressource direkt zugeordnet werden. Die Berechtigung für die Protokolle für die Ressource wird automatisch zugewiesen. |
| Welchen Geltungsbereich haben Berechtigungen? | Den Arbeitsbereich. Benutzer mit Zugriff auf den Arbeitsbereich können alle Protokolle in diesem Arbeitsbereich aus Tabellen abfragen, für die sie über Berechtigungen verfügen. Siehe [Tabellenzugriffssteuerung](manage-access.md#table-level-rbac). | Die Azure-Ressource. Der Benutzer kann Protokolle für bestimmte Ressourcen, Ressourcengruppen oder ein Abonnement, auf die bzw. das er Zugriff hat, aus jedem Arbeitsbereich abfragen, aber nicht Protokolle für andere Ressourcen. |
| Wie kann der Benutzer auf Protokolle zugreifen? | <ul><li>Starten von **Protokolle** im **Azure Monitor**-Menü</li></ul> <ul><li>Starten von **Protokolle** unter **Log Analytics-Arbeitsbereiche**</li></ul> <ul><li>Über Azure Monitor-[Arbeitsmappen](../visualizations.md#workbooks)</li></ul> | <ul><li>Starten von **Protokolle** im Menü für die Azure-Ressource</li></ul> <ul><li>Starten von **Protokolle** im **Azure Monitor**-Menü</li></ul> <ul><li>Starten von **Protokolle** unter **Log Analytics-Arbeitsbereiche**</li></ul> <ul><li>Über Azure Monitor-[Arbeitsmappen](../visualizations.md#workbooks)</li></ul> |

## <a name="access-control-mode"></a>Zugriffssteuerungsmodus

Der *Zugriffssteuerungsmodus* ist eine Einstellung für jeden Arbeitsbereich, die definiert, wie Berechtigungen für den Arbeitsbereich bestimmt werden.

* **Arbeitsbereichsberechtigungen erforderlich**: Dieser Steuerungsmodus ermöglicht keine differenzierte rollenbasierte Zugriffssteuerung (RBAC). Damit ein Benutzer auf den Arbeitsbereich zugreifen kann, müssen ihm Berechtigungen für den Arbeitsbereich oder für bestimmte Tabellen gewährt werden.

    Wenn ein Benutzer auf den Arbeitsbereich gemäß dem Arbeitsbereichskontextmodus zugreift, besitzt er Zugriff auf alle Daten in allen Tabellen, für die ihm Zugriff erteilt wurde. Wenn ein Benutzer auf den Arbeitsbereich gemäß dem Ressourcenkontextmodus zugreift, besitzt er nur Zugriff auf Daten für diese Ressource in Tabellen, für die ihm Zugriff erteilt wurde.

    Dies ist die Standardeinstellung für alle Arbeitsbereiche, die vor März 2019 erstellt wurden.

* **Ressourcen- oder Arbeitsbereichsberechtigungen verwenden**: Dieser Steuerungsmodus ermöglicht differenzierte rollenbasierte Zugriffssteuerung (RBAC). Benutzern kann nur Zugriff auf Daten gewährt werden, die den Ressourcen zugeordnet sind, die aufgrund der Zuweisung der Azure-Berechtigung `read` von den Benutzern angezeigt werden können. 

    Wenn ein Benutzer auf den Arbeitsbereich im Arbeitsbereichskontextmodus zugreift, gelten Arbeitsbereichsberechtigungen. Wenn ein Benutzer im Ressourcenkontextmodus auf den Arbeitsbereich zugreift, werden nur die Ressourcenberechtigungen überprüft, und die Arbeitsbereichsberechtigungen werden ignoriert. Aktivieren Sie RBAC für einen Benutzer, indem Sie ihn aus den Arbeitsbereichberechtigungen entfernen und zulassen, dass seine Ressourcenberechtigungen erkannt werden.

    Dies ist die Standardeinstellung für alle Arbeitsbereiche, die nach März 2019 erstellt werden.

    > [!NOTE]
    > Wenn ein Benutzer nur über Ressourcenberechtigungen für den Arbeitsbereich verfügt, kann er nur über den Ressourcenkontextmodus auf den Arbeitsbereich zugreifen, sofern der Arbeitsbereichszugriffsmodus auf das **Verwenden von Ressourcen- oder Arbeitsbereichsberechtigungen** festgelegt ist.

Informationen zum Ändern des Zugriffssteuerungsmodus über das Portal, mit PowerShell oder mithilfe einer Resource Manager-Vorlage finden Sie unter [Verwalten von Protokolldaten und Arbeitsbereichen in Azure Monitor](manage-access.md#define-access-control-mode).

## <a name="recommendations"></a>Empfehlungen

![Beispiel für den Entwurf im Ressourcenkontext](./media/design-logs-deployment/workspace-design-resource-context-01.png)

In diesem Szenario wird der Entwurf eines einzelnen Arbeitsbereichs im Abonnement Ihrer IT-Organisation behandelt, das nicht durch die Datensouveränität oder Einhaltung gesetzlicher Bestimmungen eingeschränkt ist oder den Regionen zugeordnet werden muss, in denen Ihre Ressourcen bereitgestellt werden. Es ermöglicht den Sicherheits- und IT-Administratorenteams Ihrer Organisation, die verbesserte Integration der Azure-Zugriffsverwaltung und eine sicherere Zugriffssteuerung zu nutzen.

Alle Ressourcen, Überwachungslösungen und Insights (z.B. Application Insights und Azure Monitor für VMs), die die Infrastruktur und Anwendungen unterstützen, die von den verschiedenen Teams verwaltet werden, sind so konfiguriert, dass die gesammelten Protokolldaten an den zentralen gemeinsamen Arbeitsbereich der IT-Organisation weitergeleitet werden. Benutzer in jedem Team erhalten Zugriff auf Protokolle für Ressourcen, auf die Sie Zugriff haben.

Nachdem Sie die Arbeitsbereichsarchitektur bereitgestellt haben, können Sie diese für Azure-Ressourcen mit [Azure Policy](../../governance/policy/overview.md) erzwingen. Dies bietet eine Möglichkeit, Richtlinien zu definieren und Konformität mit Ihren Azure-Ressourcen zu gewährleisten, damit alle Diagnoseprotokolle an einen bestimmten Arbeitsbereich gesendet werden. Beispielsweise können Sie bei virtuellen Azure-Computern oder Skalierungsgruppen für virtuelle Computer vorhandene Richtlinien verwenden, mit denen die Arbeitsbereichskompatibilität ausgewertet und Ergebnisse gemeldet werden, oder zur Behebung von Nichtkonformität Anpassungen vornehmen.  

## <a name="workspace-consolidation-migration-strategy"></a>Migrationsstrategie für die Arbeitsbereichskonsolidierung

Kunden, die bereits mehrere Arbeitsbereiche bereitgestellt haben und an einer Konsolidierung in Form des Zugriffsmodells für den Ressourcenkontext interessiert sind, wird empfohlen, einen inkrementellen Ansatz für die Migration zum empfohlenen Zugriffsmodell zu verwenden, und nicht zu versuchen, dies in schneller oder aggressiver Weise umzusetzen. Durch eine Umsetzung in Phasen zum Planen, Migrieren, Überprüfen und Außerbetriebsetzen in einem angemessenen Zeitrahmen können nicht geplante Vorfälle oder unerwartete Auswirkungen auf Ihren Cloudbetrieb vermieden werden. Wenn Sie nicht aus Kompatibilitäts- oder Geschäftsgründen über eine Datenaufbewahrungsrichtlinie verfügen, müssen Sie den entsprechenden Zeitraum für die Aufbewahrung von Daten in dem Arbeitsbereich beurteilen, von dem aus Sie die Migration durchführen. Obwohl Sie Ressourcen neu konfigurieren, damit sie an den gemeinsamen Arbeitsbereich berichten, können Sie die Daten weiterhin im ursprünglichen Arbeitsbereich nach Bedarf analysieren. Wenn Sie nach Abschluss der Migration gezwungen sind, Daten bis zum Ablauf der Aufbewahrungsdauer im ursprünglichen Arbeitsbereich zu belassen, löschen Sie diese nicht.

Beachten Sie beim Planen der Migration zu diesem Modell die folgenden Punkte:

* Informieren Sie sich, welche Branchenvorschriften und internen Richtlinien hinsichtlich der Datenaufbewahrung befolgt werden müssen.
* Stellen Sie sicher, dass Ihre Anwendungsteams innerhalb der vorhandenen Ressourcenkontextfunktion arbeiten können.
* Ermitteln Sie den gewährten Zugriff auf Ressourcen für Ihre Anwendungsteams, und führen Sie vor der Implementierung in der Produktionsumgebung Tests in einer Entwicklungsumgebung durch.
* Konfigurieren Sie den Arbeitsbereich für die **Verwendung von Ressourcen- oder Arbeitsbereichsberechtigungen**.
* Heben Sie die Berechtigung der Anwendungsteams zum Lesen und Abfragen des Arbeitsbereichs auf.
* Aktivieren und konfigurieren Sie alle Überwachungslösungen und Insights (z.B. Azure Monitor für Container und/oder Azure Monitor für VMs), Ihre Automation-Konten und Verwaltungslösungen (z.B. Updateverwaltung, Starten und Beenden von VMs usw.), die im ursprünglichen Arbeitsbereich bereitgestellt waren.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Implementieren der in diesem Leitfaden empfohlenen Sicherheitsberechtigungen und -kontrollen finden Sie unter [Verwalten des Zugriffs auf Protokolle](manage-access.md).