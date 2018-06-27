---
title: Aktualisieren und Skalieren einer Azure API Management-Instanz | Microsoft-Dokumentation
description: In diesem Thema wird beschrieben, wie Sie eine Azure API Management-Instanz aktualisieren und skalieren.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.openlocfilehash: ca32c72b1582b2a09f9f1754ad778cf1b682a1c2
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293311"
---
# <a name="upgrade-and-scale-an-api-management-instance"></a>Aktualisieren und Skalieren einer API Management-Instanz  

Kunden können eine APIM-Instanz (API Management) skalieren, indem sie Einheiten hinzufügen und entfernen. Eine **Einheit** umfasst dedizierte Azure-Ressourcen und verfügt über eine bestimmte Lastkapazität, die als Anzahl von API-Aufrufen pro Monat ausgedrückt wird. Diese stellt nicht die maximale Anzahl von Aufrufen, sondern eher einen maximalen Durchsatzwert für die grobe Kapazitätsplanung dar. Der tatsächliche Durchsatz und die Latenz variieren aufgrund der folgenden Faktoren erheblich: Anzahl und Übertragungsrate von parallelen Verbindungen, Anzahl und Art von konfigurierten Richtlinien, Umfang von Anforderungen und Antworten sowie Back-End-Latenzzeiten.

Die Kapazität und der Preis der einzelnen Einheiten richtet sich nach dem **Tarif** der Einheit. Sie können zwischen vier Tarifen wählen: **Developer**, **Basic**, **Standard** und **Premium**. Wenn Sie die Kapazität für einen Dienst eines Tarifs erhöhen müssen, sollten Sie eine Einheit hinzufügen. Falls der Tarif, der in Ihrer APIM-Instanz derzeit ausgewählt ist, das Hinzufügen von weiteren Einheiten nicht zulässt, müssen Sie ein Upgrade auf einen höheren Tarif durchführen.

Der Preis jeder Einheit und die verfügbaren Features (z.B. Bereitstellung in mehreren Regionen) richten sich nach dem Tarif, den Sie für Ihre APIM-Instanz gewählt haben. Im Artikel zu den [Preisdetails](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erfahren Sie, welchen Preis pro Einheit und welche Features Sie im jeweiligen Tarif erhalten. 

>[!NOTE]
>Im Artikel zu den [Preisdetails](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) sind ungefähre Werte für die Einheitenkapazität der einzelnen Tarife angegeben. Um noch genauere Werte zu erhalten, sollten Sie sich ein realistisches Szenario für Ihre APIs ansehen. Weitere Informationen finden Sie im Artikel [Kapazität einer Azure API Management-Instanz](api-management-capacity.md).

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie den Schritten in diesem Artikel folgen können, benötigen Sie folgende Komponenten:

+ Ein aktives Azure-Abonnement

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Eine APIM-Instanz Weitere Informationen finden Sie unter [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md).

+ Lernen Sie das Konzept von [Kapazität einer Azure API Management-Instanz] (api-Management-capacity.md) verstehen.

## <a name="upgrade-and-scale"></a>Aktualisieren und Skalieren  

Sie können zwischen vier Tarifen wählen: **Developer**, **Basic**, **Standard** und **Premium**. Der Tarif **Developer** ist zum Evaluieren des Diensts und nicht zur Verwendung in der Produktion bestimmt. Der Tarif **Developer** verfügt nicht über eine Vereinbarung zum Servicelevel (SLA) und ist nicht skalierbar (Einheiten hinzufügen/entfernen). 

Bei **Basic**, **Standard** und **Premium** handelt es sich um Tarife für die Produktion, die über eine Vereinbarung zum Servicelevel (SLA) verfügen und skaliert werden können. Der Tarif **Basic** ist der niedrigste Tarif mit SLA und er kann auf bis zu zwei Einheiten skaliert werden, während der Tarif **Standard** auf bis zu vier Einheiten skaliert werden kann. Im Tarif **Premium** können Sie eine beliebige Anzahl von Einheiten hinzufügen.

Bei Verwendung des Tarifs **Premium** können Sie eine einzelne API Management-Instanz auf eine beliebige Anzahl von Azure-Regionen verteilen. Wenn Sie einen API Management-Dienst erstellen, enthält die Instanz nur eine Einheit und ist nur in einer Azure-Region angeordnet. Diese erste Region wird als **primäre** Region bezeichnet. Zusätzliche Regionen können leicht hinzugefügt werden. Beim Hinzufügen einer Region geben Sie die Anzahl von Einheiten an, die Sie zuordnen möchten. Beispielsweise können Sie eine Einheit in der **primären** Region und fünf Einheiten in einer anderen Region verwenden. Sie können die Anzahl der Einheiten an den Datenverkehr anpassen, die Sie in jeder Region verzeichnen. Weitere Informationen finden Sie unter [Bereitstellen einer Azure API Management-Dienstinstanz für mehrere Azure-Regionen](api-management-howto-deploy-multi-region.md).

Sie können für alle Tarife ein Upgrade oder ein Downgrade durchführen. Beim Upgrade oder Downgrade können ggf. einige Features wegfallen, z.B. VNETs oder die Bereitstellung in mehreren Regionen bei Downgrades vom Premium-Tarif auf den Standard- oder Basic-Tarif.

>[!NOTE]
>Es kann zwischen 15 und 45 Minuten dauern, bis der Upgrade- bzw. Skalierungsprozess abgeschlossen ist. Sie erhalten nach Abschluss des Prozesses eine Benachrichtigung.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Verwenden des Azure-Portals zum Durchführen von Upgrades und Skalierungen

![Skalieren von APIM im Azure-Portal](./media/upgrade-and-scale/portal-scale.png)

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer APIM-Instanz.
2. Wählen Sie aus dem Menü die Option **Skalierung und Preise** aus.
3. Wählen Sie den gewünschten Tarif aus.
4. Geben Sie die Anzahl von **Einheiten** an, die Sie hinzufügen möchten. Sie können entweder den Schieberegler verwenden oder die Anzahl von Einheiten eingeben.  
    Wenn Sie den Tarif **Premium** wählen, müssen Sie zuerst eine Region auswählen.
5. Klicken Sie auf **Speichern**.

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen einer Azure API Management-Dienstinstanz für mehrere Azure-Regionen](api-management-howto-deploy-multi-region.md)