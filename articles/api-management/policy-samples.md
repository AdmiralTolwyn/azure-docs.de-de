---
title: Azure API Management-Richtlinienbeispiele | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Richtlinien, die für die Verwendung in Azure API Management verfügbar sind.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: sample
ms.date: 10/31/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 3f1d9a96888732e6221722102a687e09f37333c0
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/10/2018
---
# <a name="api-management-policy-samples"></a>API Management-Richtlinienbeispiele

[Richtlinien](api-management-howto-policies.md) sind eine leistungsfähige Funktion des Systems, mit der Herausgeber das Verhalten der API über eine Konfiguration ändern können. Richtlinien sind eine Sammlung von Anweisungen, die sequenziell bei Anfragen oder Antworten einer API ausgeführt werden. Die folgende Tabelle enthält Links zu Beispielen und liefert eine kurze Beschreibung für jedes Beispiel.

|||
|---|---|
|**Eingehende Richtlinien**||
|[Hinzufügen eines Forwarded-Headers, um der Back-End-API das Erstellen geeigneter URLs zu ermöglichen](./policies/set-header-to-enable-backend-to-construct-urls.md?toc=api-management/toc.json) |Veranschaulicht, wie Sie einen Forwarded-Header in die eingehende Anforderung einfügen, damit die Back-End-API die richtigen URLs erstellen kann.|
|[Hinzufügen eines Headers mit eine Korrelations-ID](./policies/add-correlation-id.md?toc=api-management/toc.json) |Veranschaulicht, wie Sie der eingehenden Anforderung einen Header mit einer Korrelations-ID hinzufügen.|
|[Hinzufügen von Funktionen zu einem Back-End-Dienst und Zwischenspeichern der Antwort](./policies/cache-response.md?toc=api-management/toc.json) |Veranschaulicht das Hinzufügen von Funktionen zu einem Back-End-Dienst. Beispielsweise können Sie in einer API zur Wettervorhersage anstelle von Längen- und Breitengrad einen Ortsnamen akzeptieren.|
|[Autorisieren des Zugriffs basierend auf JWT-Ansprüchen](./policies/authorize-request-based-on-jwt-claims.md?toc=api-management/toc.json) |Veranschaulicht, wie Sie den Zugriff auf bestimmte HTTP-Methoden in einer API basierend auf JWT-Ansprüchen autorisieren.|
|[Autorisieren des Zugriffs mithilfe von Google-OAuth-Token](./policies/use-google-as-oauth-token-provider.md?toc=api-management/toc.json) |Veranschaulicht, wie Sie den Zugriff auf Ihre Endpunkte mithilfe von Google als OAuth-Tokenanbieter autorisieren.|
|[Generieren von Shared Access Signatures und Weiterleiten von Anforderungen an Azure Storage](./policies/generate-shared-access-signature.md?toc=api-management/toc.json) |Veranschaulicht, wie Sie [Shared Access Signatures](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) mithilfe von Ausdrücken generieren und die Anforderung mithilfe der rewrite-uri-Richtlinie an Azure Storage weiterleiten. |
|[Abrufen von OAuth2-Zugriffstoken aus AAD und Weiterleiten des Tokens an das Back-End](./policies/use-oauth2-for-authorization.md?toc=api-management/toc.json) |Stellt ein Beispiel zur Verwendung von OAuth2 für die Autorisierung zwischen dem Gateway und einem Back-End bereit. Dieses zeigt, wie Sie ein Zugriffstoken aus AAD abrufen und an das Back-End weiterleiten.|
|[Abrufen von X-CSRF-Token aus einem SAP-Gateway mit einer Richtlinie zum Senden einer Anforderung](./policies/get-x-csrf-token-from-sap-gateway.md?toc=api-management/toc.json) |Veranschaulicht, wie Sie das von vielen APIs verwendete X-CSRF-Muster implementieren. Dieses Beispiel bezieht sich speziell auf das SAP-Gateway. |
|[Weiterleiten der Anforderung basierend auf der Größe des Textkörpers](./policies/route-requests-based-on-size.md?toc=api-management/toc.json) |Veranschaulicht, wie Sie Anforderungen basierend auf der Größe des Textkörpers weiterleiten.|
|[Senden von Anforderungskontextinformationen an den Back-End-Dienst](./policies/send-request-context-info-to-backend-service.md?toc=api-management/toc.json) |Veranschaulicht, wie Kontextinformationen zum Zweck der Protokollierung oder Verarbeitung an den Back-End-Dienst gesendet werden.|
|[Festlegen der Aufbewahrungsdauer von Antworten im Cache](./policies/set-cache-duration.md?toc=api-management/toc.json) |Veranschaulicht, wie Sie die Aufbewahrungsdauer von Antworten im Cache mit dem maxAge-Wert in dem vom Back-End gesendeten Cache-Control-Header festlegen.|
|**Ausgehende Richtlinien**||
|[Filtern des Antwortinhalts](./policies/filter-response-content.md?toc=api-management/toc.json) | Veranschaulicht, wie Sie Datenelemente aus der Antwortnutzlast basierend auf dem der Anforderung zugeordneten Produkt filtern.|
|**Richtlinien bei Fehler**||
|[Fehler in Stackify protokollieren](./policies/log-errors-to-stackify.md?toc=api-management/toc.json) |Veranschaulicht, wie Sie eine Richtlinie für die Protokollierung von Fehlern hinzufügen, um diese Fehler für die Protokollierung an Stackify zu senden.|
