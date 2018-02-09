---
title: "Azure-Abrechnungs-APIs für Unternehmen | Microsoft-Dokumentation"
description: "Erhalten Sie Informationen zu den Berichterstellungs-APIs, mit denen Azure-Unternehmenskunden Verbrauchsdaten programmgesteuert abrufen können."
services: 
documentationcenter: 
author: anandedwin
manager: aedwin
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: aedwin
ms.openlocfilehash: f7a480c77c93035e655606433aea2547a1c105cc
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2018
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Überblick über Berichterstellungs-APIs für Unternehmenskunden
Die Berichterstellungs-APIs ermöglichen es Azure-Unternehmenskunden, die Verbrauchs- und Abrechnungsdaten in bevorzugte Datenanalysetools abzurufen. 

## <a name="enabling-data-access-to-the-api"></a>Aktivieren des Datenzugriffs für die API
* **Generieren bzw. Abrufen des API-Schlüssels**: Melden Sie sich beim Enterprise Portal an, und befolgen Sie das Tutorial unter „Hilfe – Berichterstellungs-APIs“. Im ersten Abschnitt unter diesem Artikel wird erläutert, wie der API-Schlüssel für die angegebene Registrierung generiert bzw. abgerufen wird.
* **Übergeben von Schlüsseln in der API**: Der API-Schlüssel muss für jeden Aufruf für die Authentifizierung und Autorisierung übergeben werden. Die folgende Eigenschaft muss für die HTTP-Header vorhanden sein

|Anforderungsheaderschlüssel | Wert|
|-|-|
|Autorisierung| Geben Sie den Wert im folgenden Format an: **bearer {API_SCHLÜSSEL}** <br/> Beispiel: bearer eyr....09|

## <a name="consumption-apis"></a>APIs zur Nutzung
Ein Swagger-Endpunkt ist [hier](https://consumption.azure.com/swagger/ui/index) für die unten beschriebenen APIs verfügbar, der eine einfache Introspektion der API und die Möglichkeit zum Generieren von Client-SDKs mithilfe von [AutoRest](https://github.com/Azure/AutoRest) oder [Swagger CodeGen](http://swagger.io/swagger-codegen/) ermöglichen sollte. Seit dem 1. Mai 2014 sind Daten über diese API verfügbar. 

* **Bilanz und Zusammenfassung**: Die [API für Bilanz und Zusammenfassung](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) bietet eine monatliche Übersicht über Informationen zu Bilanzen, neuen Käufen, Gebühren für den Azure Marketplace, Korrekturen und Überschreitungsgebühren.

* **Verwendungsdetails**: Die [API für Verwendungsdetails](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) bietet eine tägliche Aufschlüsselung der verbrauchten Mengen und durch eine Registrierung anfallenden geschätzten Kosten. Das Ergebnis umfasst auch Informationen zu Instanzen, Verbrauchseinheiten und Abteilungen. Die API kann nach Abrechnungszeitraum oder einem angegebenen Start- und Enddatum abgefragt werden. 

* **Marketplace Store-Gebühren**: Die [API für Marketplace Store-Gebühren](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) gibt die Aufschlüsselung der nutzungsbasierten Marketplace-Gebühren pro Tag für den angegebenen Abrechnungszeitraum oder gemäß angegebener Start- und Enddaten zurück (einmalige Gebühren sind nicht enthalten).

* **Preisblatt**: Die [Preisblatt-API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) stellt die zutreffende Rate für jede Verbrauchseinheit für den angegebenen Registrierungs- und Abrechnungszeitraum bereit. 

## <a name="data-freshness"></a>Datenaktualität
In der Antwort jeder der oben genannten APIs werden ETags zurückgegeben. Eine Änderung der ETags weist darauf hin, dass die Daten aktualisiert wurden.  Übergeben Sie in nachfolgenden Aufrufen der gleichen API mit den gleichen Parametern das erfasste ETag mit dem Schlüssel „If-None-Match“ im Header der HTTP-Anforderung. Wenn die Daten nicht weiter aktualisiert wurden, lautet der Statuscode der Antwort „NotModified“, und es werden keine Daten zurückgegeben. Bei jeder ETag-Änderung gibt die API das vollständige Dataset für den angeforderten Zeitraum zurück.

## <a name="helper-apis"></a>Hilfs-APIs
 **Liste der Abrechnungszeiträume**: Die [API für Abrechnungszeiträume](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) gibt eine Liste von Abrechnungszeiträumen zurück, die Verbrauchsdaten für die angegebene Registrierung in umgekehrter chronologischer Reihenfolge enthalten. Jeder Zeitraum enthält eine Eigenschaft, die auf die API-Route für die vier Datensätze verweist: BalanceSummary, UsageDetails, Marketplace Charges und PriceSheet.


## <a name="api-response-codes"></a>API-Antwortcodes  
|Statuscode der Antwort|Message|BESCHREIBUNG|
|-|-|-|
|200| OK|Kein Fehler|
|401| Nicht autorisiert| API-Schlüssel nicht gefunden, ungültig, abgelaufen usw.|
|404| Nicht verfügbar| Berichtsendpunkt nicht gefunden|
|400| Ungültige Anforderung| Ungültige Parameter – Datumsbereiche, EA-Nummern usw.|
|500| Serverfehler| Unerwarteter Fehler beim Verarbeiten der Anforderung| 









