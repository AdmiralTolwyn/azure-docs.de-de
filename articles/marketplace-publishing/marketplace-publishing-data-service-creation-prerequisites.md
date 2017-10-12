---
title: "Technische Voraussetzungen für das Erstellen eines Datendiensts für den Marketplace | Microsoft Docs"
description: "Grundlagen zu den Anforderungen für das Erstellen eines Datendiensts für die Bereitstellung und den Verkauf im Azure Marketplace"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: aaff609a-1cd1-4146-98f4-d04166b0fce0
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: 52827723477677bc292c645e2390c435fbad3ee4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="technical-pre-requisites-for-creating-a-data-service-offer-for-the-azure-marketplace"></a>Technische Voraussetzungen für das Erstellen eines Datendienstangebots für den Azure Marketplace
> [!IMPORTANT]
> **Derzeit integrieren wir keine neuen Herausgeber von Datendiensten mehr. Für neue Datendienste wird keine Auflistung genehmigt.** Wenn Sie eine SaaS-Geschäftsanwendung haben, die Sie auf AppSource veröffentlichen möchten, finden Sie [hier](https://appsource.microsoft.com/partners) weitere Informationen. Wenn Sie eine IaaS-Anwendung oder einen Dienst für Entwickler auf Azure Marketplace veröffentlichen möchten, finden Sie [hier](https://azure.microsoft.com/marketplace/programs/certified/) weitere Informationen.
> 
> 

Lesen Sie die Informationen zum Prozess vor Beginn sorgfältig durch, um nachvollziehen zu können, wo und warum die einzelnen Schritte ausgeführt werden. Bereiten Sie nach Möglichkeit Ihre Unternehmensinformationen und andere Daten vor, laden Sie die erforderlichen Tools herunter, und/oder erstellen Sie technische Komponenten, bevor Sie mit der Angebotserstellung beginnen.

Sie sollten die folgenden Aufgaben ausgeführt haben, bevor Sie mit dem hier beschriebenen Verfahren beginnen:

## <a name="make-a-decision-on-what-technology-will-be-used-to-publish-your-data-service-offer"></a>Auswählen der Technologie für das Veröffentlichen des Datendienstangebots
Herausgeber können zwischen mehreren Technologien für das Veröffentlichen von Datendiensten in Azure Marketplace auswählen. Die wichtigsten unterstützten Technologien werden unten beschrieben. Unabhängig davon, welche Technologie zum Veröffentlichen des Datendiensts verwendet wird, nutzt der Endbenutzer die Daten über den **OData-Feed** , der vom Azure Marketplace-Dienst verfügbar gemacht wird. Ausführliche Informationen zum OData-Dienst finden Sie unter [http://www.odata.org/](http://www.odata.org/)

## <a name="sql-azure-database"></a>Azure SQL-Datenbank
Das Bereitstellen des Datasets in Azure SQL-Datenbank ist Aufgabe des Herausgebers. Sie müssen Azure abonnieren, eine ausreichend große Datenbank bereitstellen und Ihre Daten in Azure SQL-Datenbank hochladen. Der Herausgeber ist auch dafür verantwortlich, seine Daten stets auf dem neuesten Stand zu halten. Weitere Informationen zum Abonnieren von Azure-Diensten finden Sie unter [https://azure.microsoft.com/services/sql-database/](https://azure.microsoft.com/services/sql-database/)

Beim Verschieben von Daten nach Azure SQL-Datenbank kann der Azure Marketplace Tabellen und Sichten verfügbar machen. Der Herausgeber kann angeben, welche Tabellen bzw. Sichten und Spalten für den Endbenutzer verfügbar gemacht werden. Darüber hinaus kann der Inhaltsanbieter festlegen, welche Spalten vom Endbenutzer abgefragt werden können und welche nur in der Nutzlast zurückgegeben werden. Dies bietet ein hohes Maß an Flexibilität für den Grad der Verfügbarkeit von Daten in der Datenbank. Spalten, für die Abfragen durchgeführt werden können, müssen durch mindestens einen Datenbankindex unterstützt werden.

## <a name="rest-based-web-service"></a>REST-basierter Webdienst
Unterstütztes Protokoll: **nur HTTPS**

Vorhandene REST-basierte Dienste können über den Azure Marketplace verfügbar gemacht werden. Da das Dataset immer als OData-Feed für Endbenutzer verfügbar gemacht wird, muss der Azure Marketplace-Dienst in der Lage sein, den Dienst einem OData-basierten Dienst zuzuordnen. Zu diesem Zweck müssen die REST-basierten Endpunkte alle Parameter als HTTP-Parameter verfügbar machen.

Die Nutzlast muss in einem Format vorliegen, das eine Zuordnung zu einer ATOM-Antwort ermöglicht. Daher muss die Antwort vom Dienst ein XML-Format aufweisen. Sie darf auch nur ein sich wiederholendes Element enthalten, das die Nutzlastwerte (z. B. Datensatzgruppe) enthält. Der Azure Marketplace-Dienst ordnet den wiederholten Knoten dem Eingangsknoten in ATOM und die Nutzlastwerte Eigenschaftenknoten im Eingangsknotens zu.

Autorisierungsinformationen (z. B. API-Schlüssel, Authentifizierungstoken usw.) müssen als ein HTTP-Parameter oder im HTTP-Header (Schlüssel-Wert-Paar) bereitgestellt werden – die Standardauthentifizierung wird ebenfalls unterstützt. Es muss ein gültiger Schlüssel bereitgestellt werden, der für alle Anfragen über den Azure Marketplace verwendet werden muss. Die Überwachung und Abrechnung der Benutzer erfolgt auf Ebene des Azure Marketplace.

Vom Dienst zurückgegebene Fehler müssen HTTP-Statuscodes zugeordnet werden. Wenn der Dienst XML-Code zurückgibt, der den Fehler enthält, wird er vom Azure Marketplace-Dienst den HTTP-Statuscodes zugeordnet.

## <a name="soap-based-web-services"></a>SOAP-basierter Webdienst
Protokoll: **nur HTTPS**

Die Anforderungen sind identisch mit denen im Abschnitt zum REST-basierten Dienst. Der einzige Unterschied ist, dass die Parameter auch in XML-Text bereitgestellt werden können, der bei jeder Anforderung im Azure Marketplace über den Dienst des Herausgebers veröffentlicht wird. Dies bedeutet, dass die vom Benutzer am Front-End bereitgestellten HTTP-Parameter in XML-Elemente eines XML-Dokuments übersetzt werden, das mit der Anforderung im Webdienst des Inhaltsanbieters veröffentlicht wird.

## <a name="odata-based-web-services"></a>OData-basierter Webdienst
Protokoll: **nur HTTPS**

Daten können als OData-Dienst im Azure Marketplace verfügbar gemacht werden. Das System übergibt den Dienst und ersetzt den Stamm des Diensts durch den Stamm des Azure Marketplace-Diensts, damit alle nachfolgenden Aufrufe über den Azure Marketplace erfolgen.

OData-Dienste müssen nicht ausschließlich über eine Datenbank im Back-End erfolgen. OData unterstützt jede Art von Speicher oder Geschäftslogik für den Dienst.

## <a name="next-steps"></a>Nächste Schritte
Sie haben die Voraussetzungen überprüft und die erforderlichen Aufgaben ausgeführt und können nun mit dem Erstellen eines Datendienstangebots fortfahren. Ausführliche Informationen finden Sie unter [Leitfaden zum Veröffentlichen von Datendiensten](marketplace-publishing-data-service-creation.md).

Wenn Sie den gesamten Prozess und die entsprechenden Artikel für die einzelnen Veröffentlichungsphasen überprüfen möchten, lesen Sie den Artikel [Erste Schritte: Veröffentlichen eines Angebots im Azure Marketplace](marketplace-publishing-getting-started.md).

[link-acct]:marketplace-publishing-accounts-creation-registration.md
