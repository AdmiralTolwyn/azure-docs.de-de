---
title: "Azure-Abrechnungs-APIs für Unternehmen – Bilanz und Zusammenfassung | Microsoft-Dokumentation"
description: "Erfahren Sie mehr über die Azure-Abrechnungs-APIs für Nutzung und Gebührenkarte, mit denen Sie Einblicke in den Azure-Ressourcenverbrauch und damit verbundene Trends erlangen können."
services: 
documentationcenter: 
author: cwatson-cat
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
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: f6b149f0e656d2263705048aa5b644f5bb4a5712
ms.contentlocale: de-de
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---balance-and-summary"></a>Berichterstellungs-APIs für Unternehmenskunden – Bilanz und Zusammenfassung

Die API für Bilanz und Zusammenfassung bietet eine monatliche Übersicht über Informationen zu Bilanzen, neuen Käufen, Gebühren für den Azure Marketplace, Korrekturen und Überschreitungsgebühren.


##<a name="request"></a>Request 
Allgemeine Headereigenschaften, die hinzugefügt werden müssen, werden [hier](billing-enterprise-api.md) angegeben. Wenn kein Abrechnungszeitraum angegeben wurde, werden die Daten für den aktuellen Abrechnungszeitraum zurückgegeben.

|Methode | Anforderungs-URI|
|-|-|
|GET| https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/balancesummary|
|GET| https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/balancesummary|

> [!Note]
> Um die Vorschauversion der API zu verwenden, ersetzen Sie in der obigen URL v2 durch v1.
>

## <a name="response"></a>Antwort

        {
            "id": "enrollments/100/billingperiods/201507/balancesummaries",
            "billingPeriodId": 201507,
            "currencyCode": "USD",
            "beginningBalance": 0,
            "endingBalance": 1.1,
            "newPurchases": 1,
            "adjustments": 1.1,
            "utilized": 1.1,
            "serviceOverage": 1,
            "chargesBilledSeparately": 1,
            "totalOverage": 1,
            "totalUsage": 1.1,
            "azureMarketplaceServiceCharges": 1,
            "newPurchasesDetails": [
                {
                "name": "",
                "value": 1
                }
            ],
            "adjustmentDetails": [
                {
                "name": "Promo Credit",
                "value": 1.1
                },
                {
                "name": "SIE Credit",
                "value": 1.0
                }
            ]
        }


**Definitionen der Antworteigenschaft**

|Eigenschaftenname| Typ| Beschreibung
|-|-|-|
|id|string|Eindeutige ID für einen bestimmten Abrechnungszeitraum und die Registrierung|
|billingPeriodId|string |ID des Abrechnungszeitraums|
|currencyCode|string |Währungscode|
|beginningBalance|Decimal| Ausgangsbilanz für den Abrechnungszeitraum|
|endingBalance|Decimal| Endbilanz für den Abrechnungszeitraum (für offene Zeiträume wird diese täglich aktualisiert)|
|newPurchases|Decimal| Gesamtbetrag für neue Käufe|
|adjustments|Decimal| Gesamtanpassungsbetrag|
|utilized|Decimal| Gesamtverwendung der Verpflichtungen|
|serviceOverage|Decimal| Überschreitung für Azure-Dienste|
|chargesBilledSeparately|Decimal| Separat abgerechnete Gebühren|
|totalOverage|Decimal| serviceOverage + chargesBilledSeparately|
|totalUsage|Decimal| Azure-Dienstverpflichtung + Gesamtüberschreitung|
|azureMarketplaceServiceCharges|Decimal| Gesamtgebühren für Azure Marketplace|
|newPurchasesDetails|Array von JSON-Zeichenfolgen mit Name-Wert-Paaren|Liste neuer Käufe|
|adjustmentDetails|Array von JSON-Zeichenfolgen mit Name-Wert-Paaren|Liste der Anpassungen (Promo-Guthaben, SIE-Guthaben usw.) |


<br/>
## <a name="see-also"></a>Weitere Informationen

* [API für Abrechnungszeiträume](billing-enterprise-api-billing-periods.md)

* [API für Verwendungsdetails](billing-enterprise-api-usage-detail.md) 

* [API für Marketplace Store-Gebühren](billing-enterprise-api-marketplace-storecharge.md) 

* [Preisblatt-API](billing-enterprise-api-pricesheet.md)
