---
title: "MongoDB-Verbindungszeichenfolge für ein Azure Cosmos DB-Konto | Microsoft-Dokumentation"
description: In diesem Artikel erfahren Sie, wie Sie Ihre MongoDB-App mithilfe einer MongoDB-Verbindungszeichenfolge mit einem Azure Cosmos DB-Konto verbinden.
keywords: MongoDB-Verbindungszeichenfolge
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: e36f7375-9329-403b-afd1-4ab49894f75e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: anhoh
ms.openlocfilehash: 41c0b360b54f9a9dcd2458ce5bdc05911c4f73a5
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/23/2018
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Verbinden einer MongoDB-Anwendung mit Azure Cosmos DB
In diesem Artikel erfahren Sie, wie Sie Ihre MongoDB-App mithilfe einer MongoDB-Verbindungszeichenfolge mit einem Azure Cosmos DB-Konto verbinden. Anschließend können Sie eine Azure Cosmos DB-Datenbank als Datenspeicher für Ihre MongoDB-App verwenden. 

Dieses Tutorial zeigt zwei Möglichkeiten, um Informationen zur Verbindungszeichenfolge abzurufen:

- [Die Schnellstartmethode](#QuickstartConnection) zur Verwendung mit .NET-, Node.js-, MongoDB Shell-, Java- und Python-Treibern
- [Die Methode mit benutzerdefinierten Verbindungszeichenfolgen](#GetCustomConnection) zur Verwendung mit anderen Treibern

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto. Wenn Sie noch kein Azure-Konto besitzen, erstellen Sie jetzt ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/). 
- Ein Azure Cosmos DB-Konto. Weitere Anweisungen finden Sie unter [Erstellen einer Web-App mit einer MongoDB-API durch .NET und das Azure-Portal](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Abrufen der MongoDB-Verbindungszeichenfolge mithilfe der Schnellstartmethode
1. Melden Sie sich über einen Internetbrowser beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie auf dem Blatt **Azure Cosmos DB** die API für das MongoDB-Konto aus. 
3. Klicken Sie im linken Bereich des Kontoblatts auf **Schnellstart**. 
4. Wählen Sie Ihre Plattform aus (**.NET**, **Node.js**, **MongoDB Shell**, **Java**, **Python**). Wenn der gewünschte Treiber oder das gewünschte Tool nicht aufgeführt wird, machen Sie sich keine Sorgen: Wir stellen kontinuierlich weitere Ausschnitte von Verbindungscodes zur Verfügung. Tragen Sie im unteren Bereich ein, welche Inhalte Sie sehen möchten. Informationen darüber, wie Sie eine eigene Verbindung erstellen können, finden Sie unter [Abrufen der Informationen zur Verbindungszeichenfolge für das Konto](#GetCustomConnection).
5. Kopieren Sie den Codeausschnitt, und fügen Sie ihn in Ihre MongoDB-App ein.

    ![Blatt „Schnellstart“](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Abrufen der MongoDB-Verbindungszeichenfolge zum Anpassen
1. Melden Sie sich über einen Internetbrowser beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie auf dem Blatt **Azure Cosmos DB** die API für das MongoDB-Konto aus. 
3. Klicken Sie im linken Bereich des Kontoblatts auf **Verbindungszeichenfolge**. 
4. Das Blatt **Verbindungszeichenfolge** wird geöffnet. Es enthält alle erforderlichen Informationen, um mithilfe eines Treibers für MongoDB eine Verbindung mit dem Konto herzustellen, einschließlich einer vorab erstellten Verbindungszeichenfolge.

    ![Blatt „Verbindungszeichenfolge“](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Anforderungen an die Verbindungszeichenfolge
> [!Important]
> Für Azure Cosmos DB gelten strenge Sicherheitsanforderungen und -standards. Azure Cosmos DB-Konten erfordern eine Authentifizierung und eine sichere Kommunikation über *SSL*. 
>
>

Azure Cosmos DB unterstützt das standardmäßige URI-Format für MongoDB-Verbindungszeichenfolgen. Es gelten jedoch einige besondere Anforderungen: Azure Cosmos DB-Konten erfordern eine Authentifizierung und eine sichere Kommunikation über SSL. Folglich sieht das Format der Verbindungszeichenfolge wie folgt aus:

    mongodb://username:password@host:port/[database]?ssl=true

Die Werte dieser Zeichenfolge werden auf dem zuvor gezeigten Blatt **Verbindungszeichenfolge** angezeigt:

* Benutzername (erforderlich): Name des Azure Cosmos DB-Kontos
* Kennwort (erforderlich): Kennwort des Azure Cosmos DB-Kontos
* Host (erforderlich): FQDN des Azure Cosmos DB-Kontos
* Port (erforderlich): 10255
* Datenbank (optional): Die Datenbank, die die Verbindung verwendet. Wenn keine Datenbank angegeben wird, wird standardmäßig die Datenbank „test“ verwendet.
* ssl=true (erforderlich)

Sehen Sie sich beispielsweise das Konto auf dem Blatt **Verbindungszeichenfolge** an. Eine gültige Verbindungszeichenfolge ist:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie, wie Sie [Studio 3T (MongoChef)](mongodb-mongochef.md) mit einer Azure Cosmos DB-API für das MongoDB-Konto verwenden.
* Machen Sie sich anhand von [Beispielen](mongodb-samples.md) mit der Azure Cosmos DB-API für MongoDB vertraut.
