---
title: Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS) in Azure Cosmos DB
description: Dieser Artikel beschreibt das Konfigurieren der Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS) in Azure Cosmos DB im Azure-Portal und über Azure Resource Manager-Vorlagen.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2018
ms.author: dech
ms.openlocfilehash: 08cd8b332a34075d8e2f939aa9015aa1b0a33a07
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036153"
---
# <a name="cross-origin-resource-sharing-cors-in-azure-cosmos-db"></a>Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS) in Azure Cosmos DB 

CORS (Cross Origin Resource Sharing; Ressourcenfreigabe zwischen verschiedenen Ursprüngen) ist eine HTTP-Funktion, die einer Webanwendung, die in einer Domäne ausgeführt wird, den Zugriff auf Ressourcen in einer anderen Domäne ermöglicht. In Webbrowser ist eine Sicherheitseinschränkung implementiert, die als „Same Origin Policy“ bekannt ist und verhindert, dass eine Webseite APIs in einer anderen Domäne aufruft. CORS bietet jedoch eine sichere Möglichkeit, um der Ursprungsdomäne das Aufrufen von APIs in einer anderen Domäne zu erlauben. Die Kern-SQL-API in Azure Cosmos DB unterstützt jetzt über den Header „AllowedOrigins“ die Ressourcenfreigabe zwischen verschiedenen Ursprüngen (CORS). Nachdem Sie die CORS-Unterstützung für Ihr Azure Cosmos-Konto aktiviert haben, werden nur authentifizierte Anforderungen ausgewertet, um festzustellen, ob sie entsprechend den angegebenen Regeln zulässig sind.

Sie können die CORS-Einstellung im Azure-Portal oder über eine Azure Resource Manager-Vorlage konfigurieren. Die Kern-SQL-API in Azure Cosmos DB unterstützt eine JavaScript-Bibliothek, die sowohl mit Node.js als auch in browsergestützten Umgebungen verwendet werden kann. Diese Bibliothek kann nun bei Verwendung des Gateway-Modus die Vorteile der CORS-Unterstützung nutzen. Um dieses Feature zu verwenden, ist clientseitig keine Konfiguration erforderlich. Mit der CORS-Unterstützung können Ressourcen in einem Browser über die [JavaScript-Bibliothek](https://www.npmjs.com/package/@azure/cosmos) oder direkt über die [REST-API](https://docs.microsoft.com/rest/api/cosmos-db/) direkt auf Azure Cosmos DB zugreifen. Das vereinfacht die Vorgänge. 

## <a name="enable-cors-support-from-azure-portal"></a>Aktivieren der CORS-Unterstützung im Azure-Portal

Führen Sie die folgenden Schritte aus, um CORS im Azure-Portal zu aktivieren:

1. Navigieren Sie zu Ihrem Azure Cosmos DB-Konto. Öffnen Sie das Blatt **CORS**.

2. Geben Sie eine durch Kommas getrennte Liste von Ursprüngen an, die ursprungsübergreifende Aufrufe an Ihr Cosmos DB-Konto richten können. Beispiel: `https://www.mydomain.com`, `https://mydomain.com`, `https://api.mydomain.com`. Sie können auch einen Platzhalter „\*“ verwenden, um alle Ursprünge zuzulassen. Wählen Sie dann **Senden** aus. 

   > [!NOTE]
   > Derzeit können Sie Platzhalter nicht in Domänennamen verwenden. Das Format `https://*.mydomain.net` beispielsweise wird noch nicht unterstützt. 
   
   ![Aktivieren von CORS im Azure-Portal](./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png)
 
## <a name="enable-cors-support-from-resource-manager-template"></a>Aktivieren der CORS-Unterstützung über eine Azure Resource Manager-Vorlage

Zum Aktivieren von CORS mithilfe einer Azure Resource Manager-Vorlage fügen Sie einer beliebigen vorhandenen Vorlage den Abschnitt „cors“ mit der Eigenschaft „allowedOrigins“ hinzu. Das folgende JSON-Beispiel zeigt eine Vorlage, die ein neues Azure Cosmos-Konto erstellt, bei dem CORS aktiviert ist.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "name": "test",
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "apiVersion": "2015-04-08",
            "location": "East US 2",
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "Session",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "test-eastus2",
                        "failoverPriority": 0,
                        "locationName": "East US 2"
                    }
                ],
                "cors": [
                    {
                        "allowedOrigins": "*"
                    }
                ]
            },
            "dependsOn": [
            ]
        }
    ]
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>Verwenden der JavaScript-Bibliothek von Azure Cosmos DB in einem Browser

Zurzeit wird die JavaScript-Bibliothek von Azure Cosmos DB nur mit der CommonJS-Version der Bibliothek ausgeliefert. Wenn Sie die Bibliothek im Browser verwenden möchten, müssen Sie ein Tool wie Rollup oder Webpack verwenden, um eine browserkompatible Bibliothek zu erstellen. Für bestimmte Node.js-Bibliotheken sollten Browser-Mocks bereitgestellt werden. Im Folgenden finden Sie ein Beispiel für eine Webpack-Konfigurationsdatei mit den erforderlichen Mock-Einstellungen.

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.ts",
  devtool: "inline-source-map",
  node: {
    net: "mock",
    tls: "mock"
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist")
  }
};
```
 
Dieses [Codebeispiel](https://github.com/christopheranderson/cosmos-browser-sample) verwendet TypeScript und Webpack mit der JavaScript-SDK-Bibliothek von Azure Cosmos DB zum Erstellen einer Todo-App, die Echtzeit-Updates sendet, wenn neue Elemente erstellt werden.
Als bewährte Methode empfiehlt es sich, nicht den Primärschlüssel für die Kommunikation mit Azure Cosmos DB über den Browser zu verwenden. Verwenden Sie stattdessen Ressourcentoken. Weitere Informationen zu Ressourcentoken finden Sie unter [Sichern des Zugriffs auf Daten in Azure Cosmos DB](secure-access-to-data.md#resource-tokens).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu anderen Möglichkeiten zum Schutz Ihres Azure Cosmos-Kontos finden Sie in den folgenden Artikeln:

* [Konfigurieren einer Firewall für Azure Cosmos DB](how-to-configure-firewall.md)

* [Konfigurieren des VNET- und subnetzbasierten Zugriffs für Ihr Azure Cosmos DB-Konto](how-to-configure-vnet-service-endpoint.md)
    

