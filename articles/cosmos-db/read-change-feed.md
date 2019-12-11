---
title: Zugreifen auf den Änderungsfeed in Azure Cosmos DB
description: Dieser Artikel beschreibt die verschiedenen Optionen, die für das Lesen des Änderungsfeeds und den Zugriff darauf in Azure Cosmos DB zur Verfügung stehen.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: fc7e78a44d03af8952c1e178a3e92b1ee0c6fe66
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688129"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Lesen des Azure Cosmos DB-Änderungsfeeds

Für die Arbeit mit dem Azure Cosmos DB-Änderungsfeed sind folgende Optionen verfügbar:

* Verwenden von Azure Functions
* Verwenden der Change Feed Processor-Bibliothek
* Verwenden des SQL-API SDK von Azure Cosmos DB

## <a name="using-azure-functions"></a>Verwenden von Azure Functions

Azure Functions ist die einfachste und empfohlene Option. Wenn Sie einen Azure Functions-Trigger für Cosmos DB erstellen, können Sie den Container auswählen, mit dem eine Verbindung hergestellt werden soll. Die Azure-Funktion wird immer dann ausgelöst, wenn eine Änderung am Container erfolgt. Trigger können im Azure Functions-Portal, im Azure Cosmos DB-Portal oder programmgesteuert über SDKs erstellt werden. Visual Studio und VS Code bieten Unterstützung für das Schreiben von Azure-Funktionen, und Sie können sogar die Azure Functions-CLI zur plattformübergreifenden Entwicklung verwenden. Sie können den Code auf Ihrem Desktop schreiben und debuggen und dann die Funktion mit einem Klick bereitstellen. Weitere Informationen finden Sie in den Artikeln [Serverloses Datenbankcomputing mithilfe von Azure Functions](serverless-computing-database.md) und [Verwenden von Änderungsfeeds mit Azure Functions](change-feed-functions.md).

## <a name="using-the-change-feed-processor-library"></a>Verwenden der Change Feed Processor-Bibliothek

Mit der Änderungsfeed-Verarbeitungsbibliothek sind die Vorgänge weniger komplex. Dennoch bietet sie Ihnen vollständige Kontrolle über den Änderungsfeed. Die Bibliothek folgt dem Beobachtermuster, wobei Ihre Verarbeitungsfunktion von der Bibliothek aufgerufen wird. Wenn Ihr Änderungsfeed einen hohen Durchsatz aufweist, können Sie zum Lesen des Änderungsfeeds mehrere Clients instanziieren. Da Sie die Änderungsfeed-Verarbeitungsbibliothek verwenden, wird die Last automatisch zwischen den verschiedenen Clients aufgeteilt, ohne dass Sie diese Logik implementieren müssen. Die gesamte Komplexität wird von der Bibliothek verarbeitet. Wenn Sie ein eigenes Lastenausgleichsmodul verwenden möchten, können Sie `IPartitionLoadBalancingStrategy` implementieren und eine benutzerdefinierte Partitionsstrategie zum Verarbeiten des Änderungsfeeds verwenden. Weitere Informationen finden Sie unter [Verwenden der Änderungsfeed-Verarbeitungsbibliothek](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Verwenden des SQL-API SDK von Azure Cosmos DB

Das SDK bietet eine detaillierte Steuerung des Änderungsfeeds. Sie können den Prüfpunkt verwalten, auf einen bestimmten Schlüssel für eine logische Partition zugreifen usw. Wenn Sie über mehrere Reader verfügen, können Sie mit `ChangeFeedOptions` die Leselast auf verschiedene Threads oder Clients verteilen.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Änderungsfeed in APIs für Cassandra und MongoDB

Die Funktionen des Änderungsfeeds werden in der MongoDB-API als Änderungsdatenstrom und in der Cassandra-API über Prädikate verfügbar gemacht. Weitere Informationen zu den Implementierungsdetails für die MongoDB-API finden Sie unter [Änderungsdatenströme in der Azure Cosmos DB-API für MongoDB](mongodb-change-streams.md).

Natives Apache Cassandra bietet Change Data Capture (CDC), einen Mechanismus zum Markieren bestimmter Tabellen für die Archivierung sowie zum Ablehnen von Schreibvorgängen in diesen Tabellen, nachdem eine konfigurierbare Größe auf dem Datenträger für das CDC-Protokoll erreicht wurde. Der Änderungsfeed in der Azure Cosmos DB-API für Cassandra verbessert die Möglichkeit, Änderungen mit Prädikaten über CQL abzufragen. Weitere Informationen zu den Implementierungsdetails finden Sie unter [Änderungsfeed in der Azure Cosmos DB-API für Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln erfahren Sie mehr über Änderungsfeeds:

* [Übersicht über Änderungsfeeds](change-feed.md)
* [Verwenden des Änderungsfeeds in Azure Functions](change-feed-functions.md)
* [Verwenden der Änderungsfeed-Verarbeitungsbibliothek](change-feed-processor.md)
