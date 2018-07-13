---
title: Verbinden einer MongoDB-App mit Azure Cosmos DB mithilfe von Node.js | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine vorhandene Node.js MongoDB-App mit Azure Cosmos DB verbinden
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.custom: quick start connect, mvc, devcenter
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/19/2017
ms.author: sngun
ms.openlocfilehash: ff6712a70138a2178a2bddb4af467371192fdea3
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38582220"
---
# <a name="azure-cosmos-db-migrate-an-existing-nodejs-mongodb-web-app"></a>Azure Cosmos DB: Migrieren einer vorhandenen Web-App von Node.js MongoDB 

Azure Cosmos DB ist der global verteilte Microsoft-Datenbankdienst mit mehreren Modellen. Sie können schnell Dokument-, Schlüssel/Wert- und Graph-Datenbanken erstellen und abfragen und dabei stets die Vorteile der globalen Verteilung und der horizontalen Skalierung nutzen, die Azure Cosmos DB zugrunde liegen. 

Dieser Schnellstart veranschaulicht, wie Sie eine vorhandene, in Node.js geschriebene MongoDB-App verwenden und sie mit Ihrer Azure Cosmos DB-Datenbank verbinden, die MongoDB-Clientverbindungen unterstützt, indem die [MongoDB-API](mongodb-introduction.md) verwendet wird. Das heißt, dass die Node.js-Anwendung nur weiß, dass sie mithilfe von MongoDB-APIs eine Verbindung mit einer Datenbank herstellt. Für die Anwendung ist es ersichtlich, dass die Daten in Azure Cosmos DB gespeichert sind.

Wenn Sie fertig sind, wird eine MEAN-Anwendung (MongoDB, Express, Angular und Node.js) in [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) ausgeführt. 

![In Azure App Service ausgeführte MEAN.js-App](./media/create-mongodb-nodejs/meanjs-in-azure.png)


[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittstelle lokal installieren und verwenden möchten, müssen Sie für dieses Thema die Azure CLI in Version 2.0 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0]( /cli/azure/install-azure-cli) Informationen dazu. 

## <a name="prerequisites"></a>Voraussetzungen 
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Zusätzlich zur Azure CLI müssen Sie [Node.js](https://nodejs.org/) und [Git](http://www.git-scm.com/downloads) lokal installieren, um `npm`- und `git`-Befehle auszuführen.

Sie sollten über ausreichende Kenntnisse zu Node.js verfügen. Dieser Schnellstart ist nicht dafür vorgesehen, Ihnen bei der Entwicklung von Node.js-Anwendungen im Allgemeinen zu helfen.

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Führen Sie die folgenden Befehle aus, um das Beispielrepository zu klonen. Dieses Beispielrepository enthält eine [MEAN.js](http://meanjs.org/)-Standardanwendung.

1. Öffnen Sie eine Eingabeaufforderung, erstellen Sie einen neuen Ordner namens „git-samples“, und schließen Sie die Eingabeaufforderung.

    ```bash
    md "C:\git-samples"
    ```

2. Öffnen Sie ein Git-Terminalfenster (z. B. git bash), und verwenden Sie den Befehl `cd`, um in den neuen Ordner zu gelangen und dort die Beispiel-App zu installieren.

    ```bash
    cd "C:\git-samples"
    ```

3. Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer. 

    ```bash
    git clone https://github.com/prashanthmadi/mean
    ```

## <a name="run-the-application"></a>Ausführen der Anwendung

Installieren Sie die erforderlichen Pakete, und starten Sie die Anwendung.

```bash
cd mean
npm install
npm start
```
Die Anwendung versucht, eine Verbindung mit einer MongoDB-Quelle herzustellen. Dabei tritt ein Fehler auf. Beenden Sie die Anwendung, wenn die Ausgabe „[MongoError: connect ECONNREFUSED 127.0.0.1:27017]“ zurückgibt.

## <a name="log-in-to-azure"></a>Anmelden bei Azure

Wenn Sie eine installierte Azure CLI verwenden, melden Sie sich mit dem Befehl [az login](/cli/azure/reference-index#az_login) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm. Sie können diesen Schritt überspringen, wenn Sie Azure Cloud Shell verwenden.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>Hinzufügen des Azure Cosmos DB-Moduls

Wenn Sie eine installierte Azure CLI verwenden, überprüfen Sie, ob die `cosmosdb`-Komponente bereits installiert ist, indem Sie den `az`-Befehl ausführen. Wenn `cosmosdb` in der Liste der Basisbefehle enthalten ist, fahren Sie mit dem nächsten Befehl fort. Sie können diesen Schritt überspringen, wenn Sie Azure Cloud Shell verwenden.

Wenn `cosmosdb` nicht in der Liste der Basisbefehle enthalten ist, installieren Sie [Azure CLI 2.0]( /cli/azure/install-azure-cli) erneut.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group#az_group_create) eine [Ressourcengruppe](../azure-resource-manager/resource-group-overview.md). Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen wie Web-Apps, Datenbanken und Speicherkonten bereitgestellt und verwaltet werden. 

Im folgenden Beispiel wird eine Ressourcengruppe in der Region „Europa, Westen“ erstellt. Wählen Sie einen eindeutigen Namen für die Ressourcengruppe aus.

Wenn Sie Azure Cloud Shell verwenden, klicken Sie auf **Ausprobieren**, befolgen Sie die Anweisungen auf dem Bildschirm für die Anmeldung, und kopieren Sie dann den Befehl in die Befehlszeile.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Erstellen eines Azure Cosmos DB-Kontos

Erstellen Sie mit dem Befehl [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) ein Azure Cosmos DB-Konto.

Ersetzen Sie im folgenden Befehl den Platzhalter `<cosmosdb-name>` durch Ihren eigenen eindeutigen Azure Cosmos DB-Kontonamen. Da dieser eindeutige Name als Teil des Azure Cosmos DB-Endpunkts (`https://<cosmosdb-name>.documents.azure.com/`) verwendet wird, muss er für alle Azure Cosmos DB-Konten in Azure eindeutig sein. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

Der `--kind MongoDB`-Parameter ermöglicht MongoDB-Clientverbindungen.

Nach dem Erstellen des Azure Cosmos DB-Kontos zeigt die Azure-CLI ähnliche Informationen wie im folgenden Beispiel an. 

> [!NOTE]
> In diesem Beispiel wird JSON als Azure CLI-Ausgabeformat (Standardeinstellung) verwendet. Informationen zur Verwendung anderer Ausgabeformate finden Sie unter [Ausgabeformate für Azure CLI 2.0-Befehle](https://docs.microsoft.com/cli/azure/format-output-azure-cli).

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>Verbinden der Node.js-Anwendung mit der Datenbank

In diesem Schritt verbinden Sie die MEAN.js-Beispielanwendung mithilfe einer MongoDB-Verbindungszeichenfolge mit der soeben erstellten Azure Cosmos DB-Datenbank. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>Konfigurieren der Verbindungszeichenfolge in der Node.js-Anwendung

Öffnen Sie in Ihrem MEAN.js.Repository `config/env/local-development.js`.

Ersetzen Sie den Inhalt dieser Datei durch den folgenden Code. Achten Sie auch darauf, dass Sie die beiden `<cosmosdb-name>`-Platzhalter durch den Namen Ihres Azure Cosmos DB-Kontos ersetzen.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>Abrufen des Schlüssels

Um eine Verbindung mit der Azure Cosmos DB-Datenbank herstellen zu können, benötigen Sie den Datenbankschlüssel. Rufen Sie den Primärschlüssel mit dem Befehl [az cosmosdb list-keys](/cli/azure/cosmosdb#list-keys) ab.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Die Azure-CLI gibt Informationen ähnlich wie im folgenden Beispiel aus. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

Kopieren Sie den Wert von `primaryMasterKey`. Fügen Sie ihn anstelle von `<primary_master_key>` in `local-development.js` ein.

Speichern Sie die Änderungen.

### <a name="run-the-application-again"></a>Erneutes Ausführen der Anwendung

Führen Sie `npm start` erneut aus. 

```bash
npm start
```

Eine Konsolenmeldung sollte Sie darüber informieren, dass die Entwicklungsumgebung ausgeführt wird. 

Navigieren Sie in einem Browser zu `http://localhost:3000`. Klicken Sie oben im Menü auf **Sign Up** (Registrieren), und versuchen Sie, zwei Dummy-Benutzer zu erstellen. 

Die MEAN.js-Beispielanwendung speichert Benutzerdaten in der Datenbank. Wenn der Vorgang erfolgreich ist und Sie von MEAN.js automatisch mit dem erstellten Benutzer angemeldet werden, funktioniert Ihre Azure Cosmos DB-Verbindung. 

![Erfolgreiche Verbindung zwischen MEAN.js und MongoDB](./media/create-mongodb-nodejs/mongodb-connect-success.png)

## <a name="view-data-in-data-explorer"></a>Anzeigen von Daten im Daten-Explorer

Durch Azure Cosmos DB gespeicherte Daten können angezeigt und abgefragt werden und Geschäftslogik im Azure-Portal ausführen.

Melden Sie sich im [Azure-Portal](https://portal.azure.com) in Ihrem Webbrowser an, um die im vorherigen Schritt erstellten Benutzerdaten anzuzeigen, abzufragen und mit ihnen zu arbeiten.

Geben Sie im Suchfeld oben Azure Cosmos DB ein. Wenn sich das Blatt Ihres Cosmos DB-Kontos öffnet, wählen Sie Ihr Cosmos DB-Konto aus. Klicken Sie im linken Navigationsbereich auf Daten-Explorer. Erweitern Sie Ihre Sammlung im Bereich „Sammlungen“. Anschließend können Sie die Dokumente in der Sammlung anzeigen, die Daten abfragen und sogar gespeicherte Prozeduren, Trigger und UDFs erstellen und ausführen. 

![Daten-Explorer im Azure-Portal](./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png)


## <a name="deploy-the-nodejs-application-to-azure"></a>Bereitstellen der Node.js-Anwendung in Azure

In diesem Schritt stellen Sie die mit MongoDB verbundene Node.js-Anwendung in Azure Cosmos DB bereit.

Sie haben vielleicht festgestellt, dass die Konfigurationsdatei, die Sie zuvor geändert haben, für die Entwicklungsumgebung ist (`/config/env/local-development.js`). Wenn Sie die Anwendung in App Service bereitstellen, wird sie standardmäßig in der Produktionsumgebung ausgeführt. Nun müssen Sie die gleiche Änderung an der jeweiligen Konfigurationsdatei vornehmen.

Öffnen Sie in Ihrem MEAN.js.Repository `config/env/production.js`.

Ersetzen Sie im `db`-Objekt den Wert von `uri` wie im folgenden Beispiel gezeigt. Achten Sie darauf, die Platzhalter wie zuvor zu ersetzen.

```javascript
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> Die Option `ssl=true` ist wichtig, da [für Azure Cosmos DB SSL erforderlich ist](connect-mongodb-account.md#connection-string-requirements). 
>
>

Committen Sie im Terminal alle Änderungen in Git. Sie können beide Befehle kopieren, um sie gemeinsam auszuführen.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie gelernt, wie Sie ein Azure Cosmos DB-Konto und mit dem Daten-Explorer eine MongoDB-Sammlung erstellen. Sie können jetzt die MongoDB-Daten zu Azure Cosmos DB migrieren.  

> [!div class="nextstepaction"]
> [Import MongoDB data into Azure Cosmos DB (Importieren von Daten aus MongoDB in Azure Cosmos DB)](mongodb-migrate.md)
