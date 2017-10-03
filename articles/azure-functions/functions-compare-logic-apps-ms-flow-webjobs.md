---
title: Auswahl zwischen Flow, Logic Apps, Functions und WebJobs | Microsoft Docs
description: "Anhand eines Vergleichs und einer Gegenüberstellung der vier Cloudintegrationsdienste von Microsoft können Sie ermitteln, welche am besten für Sie geeignet sind."
services: functions,app-service\logic
documentationcenter: na
author: ggailey777
manager: wpickett
tags: 
keywords: Microsoft Flow, Flow, Logic Apps, Azure Functions, Functions, Azure WebJobs, WebJobs, Ereignisverarbeitung, dynamisches Compute, serverlose Architektur
ms.assetid: e9ccf7ad-efc4-41af-b9d3-584957b1515d
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/07/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: cec9660ee068b33a114748813f0c7ffa3821d973
ms.contentlocale: de-de
ms.lasthandoff: 09/28/2017

---
# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Auswahl zwischen Flow, Logic Apps, Functions und WebJobs
In diesem Artikel werden die folgenden Dienste in der Microsoft Cloud verglichen und gegenübergestellt, die alle zur Behebung von Integrationsproblemen und zur Automatisierung von Geschäftsprozessen dienen:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure-Funktionen](https://azure.microsoft.com/services/functions/)
* [WebJobs in Azure App Service](../app-service/web-sites-create-web-jobs.md)

Alle diese Dienste sind nützlich, wenn verschiedenartige Systeme miteinander verbunden werden. Jeder der Dienste kann Eingaben, Aktionen, Bedingungen und Ausgaben definieren. Sie können die Dienste individuell nach einem Zeitplan oder auslöserbasiert ausführen. Jeder Dienst hat jedoch individuelle Vorteile, und der Vergleich basiert nicht auf der Frage „Welcher Dienst ist der beste?“, sondern auf der Frage „Welcher Dienst ist für diese Situation am besten geeignet?“. Eine Kombination dieser Dienste ist häufig die beste Möglichkeit, um schnell eine skalierbare Integrationslösung mit vollem Funktionsumfang zu erstellen.

<a name="flow"></a>

## <a name="flow-vs-logic-apps"></a>Flow im Vergleich zu Logik-Apps
Wir können Microsoft Flow und Azure Logic Apps zusammen erörtern, da beide *Configuration-First*-Integrationsdienste sind. Sie erleichtern das Erstellen von Prozessen und Workflows und werden in verschiedene SaaS- und Enterprise-Anwendungen integriert. 

* Flow baut auf Logic Apps auf
* Sie haben den gleichen Workflow-Designer
* [Connectors](../connectors/apis-list.md) sind immer mit beiden Diensten kompatibel

Flow ermöglicht allen Mitarbeitern im Büro das Ausführen einfacher Integrationen (z.B. SMS für wichtige E-Mails) ohne Umweg über Entwickler oder IT. Auf der anderen Seite ermöglicht Logic Apps erweiterte oder unternehmenskritische Integrationen (z.B. B2B-Prozesse), die DevOps und Sicherheitsvorkehrungen auf Unternehmensebene erfordern. In der Regel werden Geschäftsworkflows im Laufe der Zeit immer komplexer. Entsprechend können Sie zunächst mit einem Datenfluss beginnen und diesen dann nach Bedarf in eine Logik-App konvertieren.

Anhand der folgenden Tabelle können Sie ermitteln, ob Flow oder Logic Apps für eine bestimmte Integration am besten geeignet ist.

|  | Flow | Logik-Apps |
| --- | --- | --- |
| Zielgruppe |Büroangestellte, geschäftliche Benutzer |IT-Experten, Entwickler |
| Szenarios |Self-Service |Unternehmenskritisch |
| Designtool |Im Browser und in der mobilen App, nur über die Benutzeroberfläche |Im Browser und [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [Codeansicht](../logic-apps/logic-apps-author-definitions.md) verfügbar |
| DevOps |Ad-hoc-Entwicklung in der Produktion |Quellcodeverwaltung, Tests, Support, Automatisierung und Verwaltung in [Azure Resource Management](../logic-apps/logic-apps-arm-provision.md) |
| Administratorerfahrung |[https://flow.microsoft.com](https://flow.microsoft.com) |[https://portal.azure.com](https://portal.azure.com) |
| Sicherheit |Standardmethoden: [Datensouveränität](https://wikipedia.org/wiki/Technological_Sovereignty), [Verschlüsselung ruhender Daten](https://wikipedia.org/wiki/Data_at_rest#Encryption) für vertrauliche Daten usw. |Sicherheitsgarantie von Azure: [Azure Security](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Security Center](https://azure.microsoft.com/services/security-center/), [Überwachungsprotokolle](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) und vieles mehr. |

<a name="function"></a>

## <a name="functions-vs-webjobs"></a>Functions im Vergleich zu WebJobs
Azure Functions und WebJobs in Azure App Service können gemeinsam beschrieben werden, da es sich bei beiden um Integrationsdienste mit *Vorabcodierung* handelt, die für Entwickler entworfen wurden. Sie können damit ein Skript oder eine Codepassage als Reaktion auf verschiedene Ereignisse ausführen, z.B. [neue Speicherblobs](functions-bindings-storage.md) oder [eine Webhookanforderung](functions-bindings-http-webhook.md). Ähnlichkeiten zwischen den Diensten: 

* Beide basieren auf [Azure App Service](../app-service/app-service-web-overview.md) und verfügen über Funktionen wie [Quellcodeverwaltung](../app-service/app-service-continuous-deployment.md), [Authentifizierung](../app-service/app-service-authentication-overview.md) und [Überwachung](../app-service/web-sites-monitor.md).
* Beide sind entwicklerorientiert.
* Beide unterstützen standardmäßige Skript- und Programmiersprachen.
* Beide verfügen über NuGet- und NPM-Unterstützung.

Functions ist die natürliche Weiterentwicklung von WebJobs, wobei die größten Vorteile von WebJobs übernommen und verbessert werden. Die Verbesserungen umfassen: 

* Optimierte Entwicklung, Tests und Codeausführung direkt im Browser.
* Standardmäßige Integration in mehr Azure-Dienste und Drittanbieterdienste wie [GitHub WebHooks](https://developer.github.com/webhooks/creating/).
* Nutzungsbasierte Bezahlung; es fallen keine Kosten für einen [App Service-Plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)an.
* Automatische, [dynamische Skalierung](functions-scale.md).
* Für bestehende Kunden von App Service ist die Ausführung nach einem App Service-Plan weiterhin möglich (zur Nutzung von nicht ausgelasteten Ressourcen).
* Integration in Logic Apps.

In der folgenden Tabelle werden die Unterschiede zwischen Functions und WebJobs erläutert:

|  | Functions | WebJobs |
| --- | --- | --- |
| Skalieren |Skalierung ohne Konfiguration |Skalierung mit App Service-Plan |
| Preise |Nutzungsbasierte Bezahlung oder als Teil eines App Service-Plans |Teil eines App Service-Plans |
| Ausführungstyp |ausgelöst, geplant (durch Trigger mit Timer) |ausgelöst, fortlaufend, geplant |
| Auslösende Ereignisse |[Timer](functions-bindings-timer.md), [Azure Cosmos DB](functions-bindings-documentdb.md), [Azure Event Hubs](functions-bindings-event-hubs.md), [HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md), [Mobile App Service-Apps von Azure](functions-bindings-mobile-apps.md), [Azure Notification Hubs](functions-bindings-notification-hubs.md), [Azure Service Bus](functions-bindings-service-bus.md), [Azure Storage](functions-bindings-storage-blob.md) |[Azure Storage](functions-bindings-storage-blob.md), [Azure Service Bus](functions-bindings-service-bus.md) |
| Entwicklung im Browser |Unterstützt | Nicht unterstützt |
| Windows-Skripts (.cmd, .bat) |experimentell |Unterstützt |
| PowerShell |experimentell |Unterstützt |
| C# |Unterstützt |Unterstützt |
| F# |Unterstützt |Nicht unterstützt |
| Bash |experimentell |Unterstützt |
| PHP |experimentell |Unterstützt |
| Python |experimentell |Unterstützt |
| JavaScript |Unterstützt |Unterstützt |

Ob Sie Functions oder WebJobs verwenden sollten, hängt letztendlich davon ab, wofür Sie App Service bereits nutzen. Wenn Sie eine App Service-App haben, für die Sie Codeausschnitte ausführen und diese gemeinsam in der gleichen DevOps-Umgebung verwalten möchten, verwenden Sie WebJobs. Verwenden Sie in den folgenden Szenarien Functions.

* Sie möchten Codeausschnitte für andere Azure-Dienste oder Drittanbieter-Apps ausführen.
* Sie möchten Ihren Integrationscode unabhängig von Ihren App Service-Apps verwalten.
* Sie möchten Ihre Codeausschnitte aus einer Logik-App aufrufen. 

<a name="together"></a>

## <a name="flow-logic-apps-and-functions-together"></a>Kombination aus Flow, Logic Apps und Functions
Wie bereits erwähnt hängt es von der jeweiligen Situation ab, welcher Dienst für Sie am besten geeignet ist. 

* Verwenden Sie für eine einfache Geschäftsoptimierung Flow.
* Wenn Ihr Integrationsszenario zu komplex für Flow ist, oder wenn Sie DevOps-Funktionen und -Sicherheitskonformität benötigen, dann verwenden Sie Logic Apps.
* Wenn ein Schritt in Ihrem Integrationsszenario eine stark benutzerdefinierte Transformation oder speziellen Code erfordert, schreiben Sie eine Funktion und lösen sie dann als Aktion in Ihrer Logik-App aus.

Sie können eine Logik-App in einem Fluss aufrufen. Sie können auch eine Funktion in einer Logik-App und eine Logik-App in einer Funktion aufrufen. Die Integration zwischen Flow, Logic Apps und Functions wird im Laufe der Zeit weiter verbessert. Sie können etwas in einem Dienst erstellen und in den anderen Diensten verwenden. Daher lohnt sich jede Investition in diese drei Technologien.

## <a name="next-steps"></a>Nächste Schritte
Machen Sie sich mit den einzelnen Diensten vertraut, indem Sie Ihren ersten Fluss, eine Logik-App, eine Funktionen-App oder einen Webauftrag erstellen. Klicken Sie auf einen der folgenden Links:

* [Erste Schritte mit Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Erstellen einer Logik-App](../logic-apps/logic-apps-create-a-logic-app.md)
* [Erstellen Sie Ihre erste Funktion in Azure Functions](functions-create-first-azure-function.md)
* [Bereitstellen von Webaufträgen mit Visual Studio](../app-service/websites-dotnet-deploy-webjobs.md)

Weitere Informationen zu diesen Integrationsdiensten finden Sie auch unter den folgenden Links:

* [Leveraging Azure Functions & Azure App Service for integration scenarios (Nutzung von Azure Functions und Azure App Service für Integrationsszenarien) von Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Integrations Made Simple (Integrationen leicht gemacht) von Charles Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Logic Apps-Livewebcast](http://aka.ms/logicappslive)
* [Häufig gestellte Fragen zu Microsoft Flow](https://flow.microsoft.com/documentation/frequently-asked-questions/)


