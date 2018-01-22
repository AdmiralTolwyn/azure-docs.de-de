---
title: Erstellen einer Azure Cosmos DB-Node.js-Anwendung mit der Graph-API | Microsoft-Dokumentation
description: "Hier finden Sie ein Node.js-Codebeispiel, das Sie zum Verbinden mit Azure Cosmos DB und Senden entsprechender Abfragen verwenden können."
services: cosmos-db
documentationcenter: 
author: luisbosquez
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/08/2018
ms.author: lbosq
ms.openlocfilehash: 95c0ac43e468d3655cfddc7ae0de6cefb649131d
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2018
---
# <a name="azure-cosmos-db-build-a-nodejs-application-by-using-graph-api"></a>Azure Cosmos DB: Erstellen einer Node.js-Anwendung mit der Graph-API

Azure Cosmos DB ist der global verteilte Datenbankdienst von Microsoft mit mehreren Modellen. Sie können schnell Dokument-, Schlüssel/Wert- und Graph-Datenbanken erstellen und abfragen und dabei stets die Vorteile der globalen Verteilung und der horizontalen Skalierung nutzen, die Azure Cosmos DB zugrunde liegen. 

Dieser Schnellstartartikel veranschaulicht, wie Sie mithilfe des Azure-Portals ein Azure Cosmos DB-Konto für die Graph-API, eine Datenbank und einen Graph erstellen können. Anschließend erstellen Sie eine Konsolenanwendung mithilfe des Open-Source-Treibers [Gremlin Node.js](https://www.npmjs.com/package/gremlin) und führen diese aus.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie dieses Beispiel ausführen können, müssen folgende Voraussetzungen erfüllt sein:
* [Node.js](https://nodejs.org/en/) Version v0.10.29 oder höher
* [Git](http://git-scm.com/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Erstellen eines Datenbankkontos

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Hinzufügen eines Graphs

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Klonen Sie jetzt eine Graph-API-App aus GitHub, legen Sie die Verbindungszeichenfolge fest, und führen Sie diese aus. Sie werden feststellen, wie einfach Sie programmgesteuert mit Daten arbeiten können. 

1. Öffnen Sie ein Git-Terminalfenster, z.B. ein Git Bash, und wechseln Sie (über den `cd`-Befehl) in ein Arbeitsverzeichnis.

2. Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen: 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Öffnen Sie die Projektmappendatei in Visual Studio. 

## <a name="review-the-code"></a>Überprüfen des Codes

Es folgt ein kurzer Überblick zu dem, was in der App geschieht. Öffnen Sie die Datei `app.js`. Dort finden Sie folgende Codezeilen. 

* Der Gremlin-Client wird erstellt.

    ```nodejs
    const client = Gremlin.createClient(
        443, 
        config.endpoint, 
        { 
            "session": false, 
            "ssl": true, 
            "user": `/dbs/${config.database}/colls/${config.collection}`,
            "password": config.primaryKey
        });
    ```

  Die Konfigurationen befinden sich alle in `config.js`. Diese Datei wird im [folgenden Abschnitt](#update-your-connection-string) bearbeitet.

* Eine Reihe von Funktionen wird definiert, um verschiedene Gremlin-Vorgänge auszuführen. Hierzu zählt beispielsweise Folgendes:

    ```nodejs
    function addVertex1(callback)
    {
        console.log('Running Add Vertex1'); 
        client.execute("g.addV('person').property('id', 'thomas').property('firstName', 'Thomas').property('age', 44).property('userid', 1)", { }, (err, results) => {
          if (err) callback(console.error(err));
          console.log("Result: %s\n", JSON.stringify(results));
          callback(null)
        });
    }
    ```

* Jede Funktion führt eine Methode vom Typ `client.execute` mit einem Gremlin-Abfragezeichenfolgenparameter aus. Beispiel für die Ausführung von `g.V().count()`:

    ```nodejs
    console.log('Running Count'); 
    client.execute("g.V().count()", { }, (err, results) => {
        if (err) return console.error(err);
        console.log(JSON.stringify(results));
        console.log();
    });
    ```

* Am Ende der Datei werden dann alle Methoden mithilfe der Methode `async.waterfall()` aufgerufen. So werden sie nacheinander ausgeführt:

    ```nodejs
    try{
        async.waterfall([
            dropGraph,
            addVertex1,
            addVertex2,
            addEdge,
            countVertices
            ], finish);
    } catch(err) {
        console.log(err)
    }
    ```


## <a name="update-your-connection-string"></a>Aktualisieren der Verbindungszeichenfolge

1. Öffnen Sie die Datei „config.js“. 

2. Fügen Sie in der Datei „config.js“ den Schlüssel `config.endpoint` mit dem Wert für den **Gremlin-URI** von der Seite **Übersicht** des Azure-Portals ein. 

    `config.endpoint = "GRAPHENDPOINT";`

    ![Anzeigen und Kopieren eines Zugriffsschlüssels im Azure-Portal auf dem Blatt „Schlüssel“](./media/create-graph-nodejs/gremlin-uri.png)

   Wenn der Wert **Gremlin-URI** leer ist, können Sie den Wert auf der Seite **Schlüssel** im Portal generieren. Verwenden Sie den **URI**-Wert, entfernen Sie „https://“, und ändern Sie „documents“ in „gremlin.cosmosdb“. Falls Ihr Graphkonto vor dem 20. Dezember 2017 erstellt wurde, ändern Sie „documents“ in „graphs“. 

   Als Gremlin-Endpunkt muss nur der Hostname ohne Protokoll/Portnummer wie `mygraphdb.gremlin.cosmosdb.azure.com` (nicht `https://mygraphdb.gremlin.cosmosdb.azure.com` oder `mygraphdb.gremlin.cosmosdb.azure.com:433`) angegeben werden.

3. Fügen Sie in der Datei „config.js“ den config.primaryKey-Wert mit dem Wert für **Primärschlüssel** von der Seite **Schlüssel** des Azure-Portals ein. 

    `config.primaryKey = "PRIMARYKEY";`

   ![Azure-Portal-Blatt „Schlüssel“](./media/create-graph-nodejs/keys.png)

4. Geben Sie den Datenbanknamen und den Graphnamen (Container) für den Wert von „config.database“ und „config.collection“ ein. 

Hier sehen Sie ein Beispiel dafür, wie Ihre fertige Datei „config.js“ aussehen sollte:

```nodejs
var config = {}

// Note that this must not have HTTPS or the port number
config.endpoint = "testgraphacct.gremlin.cosmosdb.azure.com";
config.primaryKey = "Pams6e7LEUS7LJ2Qk0fjZf3eGo65JdMWHmyn65i52w8ozPX2oxY3iP0yu05t9v1WymAHNcMwPIqNAEv3XDFsEg==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>Ausführen der Konsolenanwendung

1. Öffnen Sie ein Terminalfenster, und wechseln Sie (über den `cd`-Befehl) zum Installationsverzeichnis der Datei „package.json“, die im Projekt enthalten ist.

2. Führen Sie `npm install` aus, um die erforderlichen npm-Module zu installieren, einschließlich `gremlin`.

3. Führen Sie `node app.js` in einem Terminal aus, um Ihre Node-Anwendung zu starten.

## <a name="browse-with-data-explorer"></a>Durchsuchen mit dem Daten-Explorer

Sie können nun zum Daten-Explorer im Azure-Portal zurückkehren, um mit den neuen Graph-Daten zu arbeiten und um diese anzuzeigen, abzufragen und zu ändern.

Im Daten-Explorer wird die neue Datenbank im **Diagrammbereich** angezeigt. Erweitern Sie die Datenbank und die Sammlung, und wählen Sie anschließend **Graph** aus.

Die von der Beispiel-App generierten Daten werden im nächsten Bereich auf der Registerkarte **Graph** angezeigt, wenn Sie **Filter anwenden** auswählen.

Vervollständigen Sie `g.V()` mit `.has('firstName', 'Thomas')`, um den Filter zu testen. Halten Sie sich dabei an die Groß-/Kleinschreibung des Werts.

## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-your-resources"></a>Bereinigen von Ressourcen

Wenn Sie nicht beabsichtigen, diese App weiterhin zu verwenden, löschen Sie alle Ressourcen, die Sie in diesem Artikel erstellt haben, wie folgt: 

1. Wählen Sie im Azure-Portal im linken Navigationsbereich die Option **Ressourcengruppen** aus. Wählen Sie dann den Namen der Ressource aus, die Sie erstellt haben. 

2. Wählen Sie auf der Ressourcengruppenseite die Option **Löschen** aus. Geben Sie den Namen der Ressource ein, die gelöscht werden soll, und wählen Sie dann **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie ein Azure Cosmos DB-Konto erstellen, einen Graph mit dem Daten-Explorer erstellen und eine App ausführen. Nun können Sie komplexere Abfragen erstellen und leistungsfähige Logik zum Durchlaufen von Diagrammen mit Gremlin implementieren. 

> [!div class="nextstepaction"]
> [Abfragen mit Gremlin](tutorial-query-graph.md)
