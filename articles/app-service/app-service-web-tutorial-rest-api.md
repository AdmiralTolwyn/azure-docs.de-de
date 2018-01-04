---
title: Node.js-API-App in Azure App Service | Microsoft Docs
description: "Es wird beschrieben, wie Sie eine Node.js-RESTful-API erstellen und für eine API-App in Azure App Service bereitstellen."
services: app-service\api
documentationcenter: node
author: bradygaster
manager: erikre
editor: 
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.service: app-service-api
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/13/2017
ms.author: rachelap
ms.custom: mvc, devcenter
ms.openlocfilehash: 2170ac7df3b894c8d19b432abdcfef5c7fd75ff4
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2017
---
# <a name="build-a-nodejs-restful-api-and-deploy-it-to-an-api-app-in-azure"></a>Erstellen einer Node.js-RESTful-API und Bereitstellen für eine API-App in Azure

In dieser Schnellstartanleitung wird veranschaulicht, wie Sie eine REST-API erstellen, die mit Node.js [Express](http://expressjs.com/) geschrieben wird. Hierzu verwenden Sie eine [Swagger](http://swagger.io/)-Definition und stellen sie in Azure bereit. Sie erstellen die App mithilfe von Befehlszeilentools, konfigurieren Ressourcen über die [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) und stellen die App mit Git bereit.  Am Ende verfügen Sie über eine Beispiel-REST-API, die in Azure ausgeführt wird.

## <a name="prerequisites"></a>Voraussetzungen

* [Git-Client](https://git-scm.com/)
* [Node.js und npm](https://nodejs.org/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittstelle lokal installieren und verwenden möchten, müssen Sie für dieses Thema die Azure CLI in Version 2.0 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0]( /cli/azure/install-azure-cli) Informationen dazu. 

## <a name="prepare-your-environment"></a>Vorbereiten der Umgebung

1. Führen Sie in einem Terminalfenster den folgenden Befehl aus, um das Beispiel auf Ihrem lokalen Computer zu klonen.

    ```bash
    git clone https://github.com/Azure-Samples/app-service-api-node-contact-list
    ```

2. Navigieren Sie zum Verzeichnis mit dem Beispielcode.

    ```bash
    cd app-service-api-node-contact-list
    ```

3. Installieren Sie [Swaggerize](https://www.npmjs.com/package/swaggerize-express) auf dem lokalen Computer. Swaggerize ist ein Tool, das Node.js-Code für Ihre REST-API über eine Swagger-Definition generiert.

    ```bash
    npm install -g yo
    npm install -g generator-swaggerize
    ```

## <a name="generate-nodejs-code"></a>Generieren von Node.js-Code 

In diesem Abschnitt des Tutorials wird ein API-Entwicklungsworkflow modelliert, in dem Sie zuerst Swagger-Metadaten erstellen und diese dann verwenden, um das Servercodegerüst (automatische Generierung) für die API zu erstellen. 

Ändern Sie das Verzeichnis in den Ordner *start*, und führen Sie dann `yo swaggerize` aus. Swaggerize erstellt über die Swagger-Definition in der Datei *api.json* ein Node.js-Projekt für Ihre API.

```bash
cd start
yo swaggerize --apiPath api.json --framework express
```

Wenn Sie von Swaggerize zur Eingabe eines Projektnamens aufgefordert werden, verwenden Sie *ContactList*.
   
   ```bash
   Swaggerize Generator
   Tell us a bit about your application
   ? What would you like to call this project: ContactList
   ? Your name: Francis Totten
   ? Your github user name: fabfrank
   ? Your email: frank@fabrikam.net
   ```
   
## <a name="customize-the-project-code"></a>Anpassen des Projektcodes

1. Kopieren Sie den Ordner *lib* in den Ordner *ContactList*, der durch `yo swaggerize` erstellt wurde. Ändern Sie das Verzeichnis anschließend in *ContactList*.

    ```bash
    cp -r lib/ ContactList/
    cd ContactList
    ```

2. Installieren Sie die npm-Module `jsonpath` und `swaggerize-ui`. 

    ```bash
    npm install --save jsonpath swaggerize-ui
    ```

3. Ersetzen Sie den Code in *handlers/contacts.js* durch folgenden Code: 
    ```javascript
    'use strict';

    var repository = require('../lib/contactRepository');

    module.exports = {
        get: function contacts_get(req, res) {
            res.json(repository.all())
        }
    };
    ```
    Für diesen Code werden die JSON-Daten verwendet, die in *lib/contacts.json* gespeichert sind und durch *lib/contactRepository.js* unterstützt werden. Der neue Code in *contacts.js* gibt alle Kontakte im Repository als JSON-Nutzlast zurück. 

4. Ersetzen Sie den Code in der Datei **handlers/contacts/{id}.js** durch folgenden Code:

    ```javascript
    'use strict';

    var repository = require('../../lib/contactRepository');

    module.exports = {
        get: function contacts_get(req, res) {
            res.json(repository.get(req.params['id']));
        }    
    };
    ```

    Mit diesem Code können Sie eine Pfadvariable verwenden, um nur den Kontakt mit einer bestimmten ID zurückzugeben.

5. Ersetzen Sie den Code in der Datei **server.js** durch folgenden Code:

    ```javascript
    'use strict';

    var port = process.env.PORT || 8000; 

    var http = require('http');
    var express = require('express');
    var bodyParser = require('body-parser');
    var swaggerize = require('swaggerize-express');
    var swaggerUi = require('swaggerize-ui'); 
    var path = require('path');
    var fs = require("fs");
    
    fs.existsSync = fs.existsSync || require('path').existsSync;

    var app = express();

    var server = http.createServer(app);

    app.use(bodyParser.json());

    app.use(swaggerize({
        api: path.resolve('./config/swagger.json'),
        handlers: path.resolve('./handlers'),
        docspath: '/swagger' 
    }));

    // change four
    app.use('/docs', swaggerUi({
        docs: '/swagger'  
    }));

    server.listen(port, function () { 
    });
    ```   

    Mit diesem Code werden kleine Änderungen vorgenommen, sodass er mit Azure App Service verwendet werden kann und eine interaktive Weboberfläche für Ihre API bereitgestellt wird.

### <a name="test-the-api-locally"></a>Lokales Testen der API

1. Starten Sie die Node.js-App.
    ```bash
    npm start
    ```
    
2. Browsen Sie zu http://localhost:8000/contacts, um den JSON-Code für die gesamte Kontaktliste anzuzeigen.
   
   ```json
    {
        "id": 1,
        "name": "Barney Poland",
        "email": "barney@contoso.com"
    },
    {
        "id": 2,
        "name": "Lacy Barrera",
        "email": "lacy@contoso.com"
    },
    {
        "id": 3,
        "name": "Lora Riggs",
        "email": "lora@contoso.com"
    }
   ```

3. Browsen Sie zu http://localhost:8000/contacts/2, um den Kontakt mit der `id` 2 anzuzeigen.
   
    ```json
    { 
        "id": 2,
        "name": "Lacy Barrera",
        "email": "lacy@contoso.com"
    }
    ```

4. Testen Sie die API über die Swagger-Weboberfläche unter http://localhost:8000/docs.
   
    ![Swagger-Weboberfläche](media/app-service-web-tutorial-rest-api/swagger-ui.png)

## <a id="createapiapp"></a> Erstellen einer API-App

In diesem Abschnitt erstellen Sie über Azure CLI 2.0 die Ressourcen zum Hosten der API in Azure App Service. 

1.  Melden Sie sich mit dem Befehl [az login](/cli/azure/?view=azure-cli-latest#az_login) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

    ```azurecli-interactive
    az login
    ```

2. Wenn Sie über mehrere Azure-Abonnements verfügen, ändern Sie das Standardabonnement in das gewünschte Abonnement.

    ````azurecli-interactive
    az account set --subscription <name or id>
    ````

3. [!INCLUDE [Create resource group](../../includes/app-service-api-create-resource-group.md)] 

4. [!INCLUDE [Create app service plan](../../includes/app-service-api-create-app-service-plan.md)]

5. [!INCLUDE [Create API app](../../includes/app-service-api-create-api-app.md)] 


## <a name="deploy-the-api-with-git"></a>Bereitstellen der API mit Git

Stellen Sie Ihren Code für die API-App bereit, indem Sie Commits per Pushvorgang vom lokalen Git-Repository in Azure App Service übertragen.

1. [!INCLUDE [Configure your deployment credentials](../../includes/configure-deployment-user-no-h.md)] 

2. Initialisieren Sie ein neues Repository im Verzeichnis *ContactList*. 

    ```bash
    git init .
    ```

3. Schließen Sie das Verzeichnis *node_modules* aus, das durch npm in einem früheren Schritt im Tutorial erstellt wurde. Erstellen Sie eine neue `.gitignore`-Datei im aktuellen Verzeichnis, und fügen Sie den folgenden Text in einer neuen Zeile an beliebiger Stelle in der Datei ein.

    ```
    node_modules/
    ```
    Überprüfen Sie mit `git status`, ob der Ordner `node_modules` ignoriert wird.

4. Führen Sie einen Commit für die Änderungen im Repository aus.
    ```bash
    git add .
    git commit -m "initial version"
    ```

5. [!INCLUDE [Push to Azure](../../includes/app-service-api-git-push-to-azure.md)]  
 
## <a name="test-the-api--in-azure"></a>Testen der API in Azure

1. Öffnen Sie in einem Browser http://app_name.azurewebsites.net/contacts. Es wird der gleiche JSON-Code zurückgegeben wie bei der Anforderung, die Sie weiter oben im Tutorial lokal durchgeführt haben.

   ```json
   {
       "id": 1,
       "name": "Barney Poland",
       "email": "barney@contoso.com"
   },
   {
       "id": 2,
       "name": "Lacy Barrera",
       "email": "lacy@contoso.com"
   },
   {
       "id": 3,
       "name": "Lora Riggs",
       "email": "lora@contoso.com"
   }
   ```

2. Navigieren Sie im Browser zum Endpunkt `http://app_name.azurewebsites.net/docs`, um die in Azure ausgeführte Swagger-Benutzeroberfläche zu testen.

    ![Swagger-Benutzeroberfläche](media/app-service-web-tutorial-rest-api/swagger-azure-ui.png)

    Nun können Sie Updates für die Beispiel-API in Azure bereitstellen, indem Sie einfach Commits per Pushvorgang an das Azure-Git-Repository übertragen.

## <a name="clean-up"></a>Bereinigen

Führen Sie den folgenden Azure CLI-Befehl aus, um die in diesem Schnellstart erstellten Ressourcen zu bereinigen:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-step"></a>Nächster Schritt 
> [!div class="nextstepaction"]
> [Zuordnen eines vorhandenen benutzerdefinierten DNS-Namens zu Azure-Web-Apps](app-service-web-tutorial-custom-domain.md)

