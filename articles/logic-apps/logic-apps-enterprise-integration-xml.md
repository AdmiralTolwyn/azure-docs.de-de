---
title: "Arbeiten mit XML-Nachrichten in Ihren Workflows – Azure Logic Apps| Microsoft-Dokumentation"
description: "Verarbeiten, Überprüfen, Transformieren und Anreichern von XML-Nachrichten in Logik-Apps und „Business-to“-Szenarien mithilfe des Enterprise Integration Packs"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 02/27/2017
ms.author: LADocs; mandia
ms.openlocfilehash: 3fec4935f5317be4bf8c9e05f1c24a7c05381b1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="validate-and-transform-xml-encode-and-decode-flat-files-and-enrich-messages-features-in-logic-apps"></a>Überprüfen und Transformieren von XML-Nachrichten, Codieren und Decodieren von Flatfiles und Anreichern von Nachrichtenfeatures in Logik-Apps

Mit Logik-Apps können Sie XML-Nachrichten verarbeiten, die Sie senden und empfangen. Dieses Feature ist im Enterprise Integration Pack enthalten. Für Benutzer mit einem BizTalk Server-Hintergrund bietet Ihnen das Enterprise Integration Pack ähnliche Möglichkeiten zum Transformieren und Überprüfen von Nachrichten, Arbeiten mit Flatfiles und sogar Verwenden von XPath, um eine Nachricht mit bestimmten Eigenschaften anzureichern oder bestimmte Eigenschaften aus einer Nachricht zu extrahieren. 

Diese Features erweitern die Art und Weise, in der Sie Nachrichten innerhalb Ihres Workflows für Benutzer verarbeiten, für die dieser Bereich neu ist. Wenn Sie beispielsweise in einem Business-to-Business-Szenario mit bestimmten XML-Schemas arbeiten, können Sie das Enterprise Integration Pack verwenden, um die Art der Verarbeitung dieser Nachrichten durch Ihr Unternehmen zu verbessern. 

Das Enterprise Integration Pack enthält: 

* [XML-Überprüfung](logic-apps-enterprise-integration-xml-validation.md "Informationen zur Überprüfung von XML-Nachrichten") – Überprüfen Sie eine ein- oder ausgehende XML-Nachricht anhand eines bestimmten Schemas.
* [XML-Transformation](../logic-apps/logic-apps-enterprise-integration-transform.md "Informationen zu XML-Nachrichtentransformationen und Zuordnungen") – Konvertieren Sie eine XML-Nachricht basierend auf Ihren Anforderungen oder den Anforderungen eines Partners, bzw. passen Sie sie an.
* [Flatfilecodierung und -decodierung](logic-apps-enterprise-integration-flatfile.md "Informationen zur Flatfilecodierung und -decodierung") – Codieren oder decodieren Sie eine Flatfile. SAP akzeptiert und sendet z.B. IDOC-Dateien im Flatfileformat. Viele Integrationsplattformen einschließlich Logic Apps erstellen XML-Nachrichten. Daher können Sie eine Logik-App erstellen, die mit dem Flatfileencoder XML-Dateien in Flatfiles „konvertiert“. 
* [XPath](https://msdn.microsoft.com/library/mt643789.aspx) – Reichern Sie eine Nachricht an, und extrahieren Sie bestimmte Eigenschaften aus der Nachricht. Mithilfe der extrahierten Eigenschaften können Sie die Nachricht an einen Ziel- oder Zwischenendpunkt weiterleiten.

## <a name="try-it-out"></a>Ausprobieren
[Stellen Sie eine voll funktionsfähige Logik-App](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (GitHub-Beispiel) mit den XML-Features von Azure Logic Apps bereit.

## <a name="learn-more"></a>Weitere Informationen
[Weitere Informationen zum Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informationen zum Enterprise Integration Pack")
