---
title: Erstellen der ersten Funktion in Azure mit Java und Maven | Microsoft-Dokumentation
description: Erstellen und veröffentlichen Sie mit Java und Maven eine einfache durch HTTP ausgelöste Funktion in Azure.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure Functions, Funktionen, Ereignisverarbeitung, Compute, serverlose Architektur
ms.service: azure-functions
ms.devlang: java
ms.topic: quickstart
ms.date: 08/10/2018
ms.author: routlaw, glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: a72d6b180db35f3e0f0e0527e8ae0f544a585b25
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822964"
---
# <a name="create-your-first-function-with-java-and-maven"></a>Erstellen Ihrer ersten Funktion mit Java und Maven

Dieser Artikel führt Sie durch die Verwendung des Maven-Befehlszeilentools zum Erstellen und Veröffentlichen einer Java-Funktion in Azure Functions. Wenn Sie fertig sind, wird Ihr Funktionscode für den [Verbrauchstarif](functions-scale.md#consumption-plan) in Azure ausgeführt und kann mithilfe einer HTTP-Anforderung ausgelöst werden.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Um Funktionen mit Java zu entwickeln, muss Folgendes installiert sein:

- [Java Developer Kit](https://www.azul.com/downloads/zulu/), Version 8.
- [Apache Maven](https://maven.apache.org), Version 3.0 oder höher
- [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure)
- [Azure Functions Core Tools](functions-run-local.md#v2) (erfordert **.NET Core 2.x-SDK**)

> [!IMPORTANT]
> Damit Sie diesen Schnellstart durchführen können, muss die Umgebungsvariable JAVA_HOME auf den Installationsspeicherort des JDK festgelegt sein.

## <a name="generate-a-new-functions-project"></a>Generieren eines neuen Functions-Projekts

Führen Sie in einem leeren Ordner den folgenden Befehl aus, um das Functions-Projekt über einen [Maven-Archetyp](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html) zu generieren.

### <a name="linuxmacos"></a>Linux/macOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

### <a name="windows-cmd"></a>Windows (CMD)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype
```

Maven fordert Sie zur Eingabe der erforderlichen Werte für die Projektgenerierung auf. Informationen zu den Werten _groupId_, _artifactId_ und _version_ finden Sie in der Referenz [Maven-Benennungskonventionen](https://maven.apache.org/guides/mini/guide-naming-conventions.html). Der Wert _appName_ muss in Azure eindeutig sein. Deshalb generiert Maven standardmäßig einen App-Namen basierend auf den zuvor eingegebenen Wert _artifactId_. Der Wert _packageName_ bestimmt das Java-Paket für den generierten Funktionscode.

Die weiter unten angegebenen Bezeichner `com.fabrikam.functions` und `fabrikam-functions` dienen als Beispiele und verbessern die Lesbarkeit der weiteren Schritte in dieser Schnellstartanleitung. Sie können in diesem Schritt aber auch gerne eigene Werte für Maven angeben.

```Output
Define value for property 'groupId': com.fabrikam.functions
Define value for property 'artifactId' : fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Confirm properties configuration: Y
```

Maven erstellt die Projektdateien in einem neuen Ordner mit dem Namen _artifactId_ (in diesem Beispiel: `fabrikam-functions`). Bei dem generierten ausführungsbereiten Code im Projekt handelt es sich um eine einfache, [durch HTTP ausgelöste](/azure/azure-functions/functions-bindings-http-webhook) Funktion, die den Text der Anforderung ausgibt:

```java
public class Function {
    /**
     * This function listens at endpoint "/api/hello". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/hello
     * 2. curl {your host}/api/hello?name=HTTP%20Query
     */
    @FunctionName("hello")
    public HttpResponseMessage<String> hello(
            @HttpTrigger(name = "req", methods = {"get", "post"}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");

        // Parse query parameter
        String query = request.getQueryParameters().get("name");
        String name = request.getBody().orElse(query);

        if (name == null) {
            return request.createResponse(400, "Please pass a name on the query string or in the request body");
        } else {
            return request.createResponse(200, "Hello, " + name);
        }
    }
}

```

## <a name="run-the-function-locally"></a>Lokales Ausführen der Funktion

Ändern Sie das Verzeichnis in den neu erstellten Projektordner. Erstellen Sie dann mit Maven die Funktion, und führen Sie sie aus:

```
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]
> Sollte bei Ihnen mit Java 9 die Ausnahme `javax.xml.bind.JAXBException` auftreten, sehen Sie sich die Problemumgehung auf [GitHub](https://github.com/jOOQ/jOOQ/issues/6477) an.

Wenn die Funktion lokal auf Ihrem System ausgeführt wird und für HTTP-Anforderungen bereit ist, wird die folgende Ausgabe angezeigt:

```Output
Listening on http://localhost:7071
Hit CTRL-C to exit...

Http Functions:

   hello: http://localhost:7071/api/hello
```

Lösen Sie die Funktion über die Befehlszeile aus. Verwenden Sie dazu cURL in einem neuen Terminalfenster:

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/hello
```

```Output
Hello LocalFunction!
```

Verwenden Sie `Ctrl-C` im Terminal, um den Funktionscode anzuhalten.

## <a name="deploy-the-function-to-azure"></a>Bereitstellen der Funktion in Azure

Bei dem Bereitstellungsprozess in Azure Functions werden die Anmeldeinformationen aus der Azure CLI verwendet. [Melden Sie sich zunächst über die Azure-Befehlszeilenschnittstelle an.](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

```azurecli
az login
```

Stellen Sie Ihren Code mit `azure-functions:deploy` als Maven-Ziel in einer neuen Funktionen-App bereit.

```
mvn azure-functions:deploy
```

Wenn die Bereitstellung abgeschlossen ist, wird die URL angezeigt, mit der Sie auf Ihre Azure-Funktionen-App zugreifen können:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

Testen Sie die in Azure ausgeführte Funktionen-App mithilfe von `cURL`. Ersetzen Sie die URL im folgenden Beispiel durch die bereitgestellte URL für Ihre eigene Funktions-App aus dem vorherigen Schritt.

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/hello -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>Vornehmen von Änderungen und erneutes Bereitstellen

Bearbeiten Sie die Quelldatei `src/main.../Function.java` im erstellten Projekt, um den von Ihrer Funktions-App zurückgegebenen Text zu ändern. Ändern Sie diese Zeile:

```java
return request.createResponse(200, "Hello, " + name);
```

in Folgendes:

```java
return request.createResponse(200, "Hi, " + name);
```

Speichern Sie die Änderungen, und führen Sie eine erneute Bereitstellung durch, indem Sie wie zuvor `azure-functions:deploy` über das Terminal ausführen. Die Funktions-App wird aktualisiert, und die folgende Anforderung:

```bash
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

hat die folgende aktualisierte Ausgabe:

```Output
Hi, AzureFunctionsTest
```

## <a name="next-steps"></a>Nächste Schritte

Sie haben eine Java-Funktionen-App mit einem einfachen HTTP-Trigger erstellt und für Azure Functions bereitgestellt.

- Weitere Informationen zum Entwickeln von Java-Funktionen finden Sie im [Azure Functions-Java-Entwicklerhandbuch](functions-reference-java.md).
- Fügen Sie mit `azure-functions:add` als Maven-Ziel zusätzliche Funktionen mit verschiedenen Triggern zu Ihrem Projekt hinzu.
- Schreiben und debuggen Sie Funktionen lokal mit [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) und [Eclipse](functions-create-maven-eclipse.md). 
- Debuggen Sie in Azure bereitgestellte Funktionen mit Visual Studio Code. Eine entsprechende Anleitung finden Sie in der Visual Studio Code-Dokumentation für [serverlose Java-Anwendungen](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud).
