---
title: Erstellen eines Azure Search-Diensts im Portal | Microsoft-Dokumentation
description: Stellen Sie einen Azure Search-Dienst über das Portal bereit.
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.topic: quickstart
ms.date: 11/07/2017
ms.author: heidist
ms.openlocfilehash: 9c0b698b68820c2696c95099bc5ff4a466686797
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/18/2018
---
# <a name="create-an-azure-search-service-in-the-portal"></a>Erstellen eines Azure Search-Diensts im Portal

Erfahren Sie, wie Sie einen Azure Search-Dienst im Portal erstellen bzw. bereitstellen. 

Bevorzugen Sie PowerShell? Verwenden Sie die Azure Resource Manager-[Dienstvorlage](https://azure.microsoft.com/resources/templates/101-azure-search-create/). Hilfe zu den ersten Schritten mit Hintergrundinformationen finden Sie unter [Verwalten von Azure Search mit PowerShell](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Abonnieren (kostenlos oder kostenpflichtig)

[Erstellen Sie ein kostenloses Azure-Konto](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), und verwenden Sie das kostenlose Guthaben, um unsere kostenpflichtigen Azure-Dienste zu testen. Wenn das Guthaben aufgebraucht ist, können Sie das Konto behalten und weiterhin kostenlose Azure-Dienste wie z.B. Websites verwenden. Ihre Kreditkarte wird nur dann belastet, wenn Sie Ihre Einstellungen explizit ändern und mit der Berechnung von Gebühren einverstanden sind.

Alternativ dazu können Sie Ihre [Vorteile für MSDN-Abonnenten aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Ihr MSDN-Abonnement beinhaltet ein monatliches Guthaben, das Sie für kostenpflichtige Azure-Dienste verwenden können. 

## <a name="find-azure-search"></a>Aufrufen von Azure Search
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Klicken Sie in der oberen linken Ecke auf das Pluszeichen („+“).
3. Wählen Sie **Web + Mobile** > **Azure Search** aus.

![](./media/search-create-service-portal/find-search3.png)

## <a name="name-the-service-and-url-endpoint"></a>Benennen des Diensts und des URL-Endpunkts

Ein Dienstname ist Teil eines URL-Endpunkts, für den API-Aufrufe ausgegeben werden: `https://your-service-name.search.windows.net`. Geben Sie den Dienstnamen im Feld **URL** ein. 

Anforderungen an Dienstnamen:
   * Er muss innerhalb des Namespaces „search.windows.net“ eindeutig sein
   * Zwischen 2 und 60 Zeichen lang
   * Verwenden Sie Kleinbuchstaben, Ziffern oder Bindestriche („-“)
   * Vermeiden Sie Bindestriche („-“) in den ersten beiden und im letzten Zeichen
   * Verwenden Sie an keiner Stelle aufeinander folgende Bindestriche („--“)

## <a name="select-a-subscription"></a>Auswählen eines Abonnements
Wenn Sie über mehrere Abonnements verfügen, wählen Sie ein Abonnement aus, in dem sich Daten- oder Dateispeicherdienste befinden. Azure Search kann die Dienste Azure Table Storage, Blob Storage, SQL-Datenbank und Azure Cosmos DB automatisch erkennen, um sie über [*Indexer*](search-indexer-overview.md) zu indizieren. Dies gilt jedoch nur für Dienste im gleichen Abonnement.

## <a name="select-a-resource-group"></a>Auswählen einer Ressourcengruppe
Eine Ressourcengruppe ist eine Sammlung von Azure-Diensten und -Ressourcen, die zusammen verwendet werden. Wenn Sie beispielsweise mit Azure Search eine SQL-Datenbank indizieren, müssen beide Dienste der gleichen Ressourcengruppe angehören.

> [!TIP]
> Wenn eine Ressourcengruppe gelöscht wird, werden auch die darin befindlichen Dienste gelöscht. Bei Prototypprojekten, die mehrere Dienste verwenden, sollten Sie all diese Dienste in die gleiche Ressourcengruppe platzieren, um das Bereinigen nach Abschluss des Projekts zu vereinfachen. 

## <a name="select-a-hosting-location"></a>Auswählen eines Hostingstandorts 
Als Azure-Dienst kann Azure Search in Rechenzentren auf der ganzen Welt gehostet werden. Beachten Sie, dass sich die [Preise je nach geografischer Lage unterscheiden können](https://azure.microsoft.com/pricing/details/search/).

## <a name="select-a-pricing-tier-sku"></a>Auswählen eines Tarifs (SKU)
[Azure Search wird derzeit in mehreren Tarifen angeboten](https://azure.microsoft.com/pricing/details/search/): Free, Basic oder Standard. Jeder Tarif verfügt über eigene [Kapazitäten und Grenzwerte](search-limits-quotas-capacity.md). Anleitungen finden Sie unter [Auswählen einer SKU oder eines Tarifs für Azure Search](search-sku-tier.md) .

In dieser exemplarischen Vorgehensweise haben wir den Standard-Tarif für unseren Dienst gewählt.

Ein Tarif kann nicht geändert werden, nachdem der Dienst erstellt wurde. Wenn Sie später einen höheren oder niedrigeren Tarif benötigen, müssen Sie den Dienst neu erstellen.

## <a name="create-your-service"></a>Erstellen des Diensts

Denken Sie daran, Ihren Dienst an das Dashboard anzuheften, damit Sie nach jeder Anmeldung sofort darauf zugreifen können.

![](./media/search-create-service-portal/new-service3.png)

## <a name="scale-your-service"></a>Skalieren des Diensts
Die Erstellung eines Diensts kann einige Minuten dauern (je nach Tarif 15 Minuten oder länger). Nach der Bereitstellung Ihres Diensts können Sie ihn Ihren Anforderungen entsprechend skalieren. Da Sie für Ihren Azure Search-Dienst den Standard-Tarif ausgewählt haben, können Sie den Dienst in zwei Dimensionen skalieren: Replikate und Partitionen. Wenn Sie den Basic-Tarif auswählen, können Sie nur Replikate hinzufügen. Wenn Sie den kostenlosen Dienst bereitstellen, ist keine Skalierung verfügbar.

***Partitionen*** ermöglichen Ihrem Dienst das Speichern und Durchsuchen weiterer Dokumente.

***Replikate*** ermöglichen Ihrem Dienst, eine größere Menge von Suchabfragen zu verarbeiten.

> [!Important]
> Ein Dienst benötigt [2 Replikate für schreibgeschützte SLAs und 3 Replikate für SLAs mit Lese-/Schreibzugriff](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Wechseln Sie im Azure-Portal zur Seite Ihres Suchdiensts.
2. Wählen Sie im linken Navigationsbereich die Optionen **Einstellungen** > **Skalierung** aus.
3. Verwenden Sie den Schiebregler, um Replikate oder Partitionen hinzuzufügen.

![](./media/search-create-service-portal/settings-scale.png)

> [!Note] 
> Jeder Tarif bietet unterschiedliche [Grenzwerte](search-limits-quotas-capacity.md) für die Gesamtzahl der in einem einzelnen Dienst zulässigen Sucheinheiten (Replikate × Partitionen = Sucheinheiten gesamt).

## <a name="when-to-add-a-second-service"></a>Wann ein zweiter Dienst hinzugefügt werden sollte

Die überwiegende Mehrheit der Kunden verwenden nur einen Dienst, der auf einer Ebene bereitgestellt wird, die das [richtige Gleichgewicht von Ressourcen](search-sku-tier.md) bietet. Ein Dienst kann mehrere Indizes hosten, die der [Obergrenze der von Ihnen ausgewählten Ebene](search-capacity-planning.md) unterliegt, wobei jeder Index vom anderen isoliert ist. In Azure Search können Anforderungen nur an einen Index geleitet werden, was das versehentliche oder vorsätzliche Datenabrufrisiko von anderen Indizes im selben Dienst verringert.

Obwohl die meisten Kunden nur einen Dienst nutzen, kann die Dienstredundanz womöglich nötig sein, wenn die operativen Anforderungen Folgendes enthalten:

+ Notfallwiederherstellung (Ausfall des Rechenzentrums). Azure Search bietet kein sofortiges Failover bei einem Ausfall. Empfehlungen und Anleitungen finden Sie unter [Dienstverwaltung für Azure Search im Azure-Portal](search-manage.md).
+ Ihre Untersuchung der mehrinstanzenfähigen Modellierung hat ergeben, dass zusätzliche Dienste die optimale Designlösung sind. Weitere Informationen finden Sie unter [Entwurfsmuster für mehrinstanzenfähige SaaS-Anwendungen und Azure Search](search-modeling-multitenant-saas-applications.md).
+ Für global bereitgestellte Anwendungen benötigen Sie möglicherweise eine Azure Search-Instanz in mehreren Regionen, um die Wartezeit des internationalen Datenverkehrs Ihrer Anwendung zu minimieren.

> [!NOTE]
> In Azure Search können Sie die Index- und Abfrageworkloads nicht aufteilen. Deshalb würden Sie auch nie mehrere Dienste für aufgeteilte Workloads erstellen. Ein Index wird immer auf dem Dienst, in dem er erstellt wurde, abgefragt (Sie können keinen Index in einem Dienst erstellen und ihn in einen anderen kopieren).
>

Ein zweiter Dienst ist für Hochverfügbarkeit nicht vonnöten. Hochverfügbarkeit für Abfragen wird erreicht, wenn Sie zwei oder mehr Replikate im gleichen Dienst verwenden. Replikatupdates sind sequenziell. Das bedeutet, dass mindestens eines betriebsfähig ist, wenn ein Dienstupdate ausgeführt wird. Weitere Informationen zur Verfügbarkeit finden Sie unter [Vereinbarungen zum Servicelevel](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Nächste Schritte
Nach der Bereitstellung eines Azure Search-Diensts können Sie einen [Index definieren](search-what-is-an-index.md), damit Sie Ihre Daten hochladen und durchsuchen können. 

> [!div class="nextstepaction"]
> [Verwenden von Azure Search aus einer .NET-Anwendung](search-howto-dotnet-sdk.md)
