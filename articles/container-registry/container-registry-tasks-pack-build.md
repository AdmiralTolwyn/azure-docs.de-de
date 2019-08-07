---
title: Erstellen eines Azure Container Registry-Images aus einer App
description: Verwenden Sie den Befehl „az acr pack build“, um ein Containerimage aus einer App zu erstellen und ohne Dockerfile an Azure Container Registry zu überführen.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/22/2019
ms.author: danlep
ms.openlocfilehash: 5100418651e24d74ad747e8c436ffce53c899a92
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68500893"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Erstellen und Übertragen eines Images aus einer App mithilfe eines Cloud Native-Buildpacks

Der Azure CLI-Befehl `az acr pack build` verwendet das [`pack`](https://github.com/buildpack/pack)-CLI-Tool von [Buildpacks](https://buildpacks.io/), um eine App zu erstellen und das Image mithilfe von Push in eine Azure-Containerregistrierung zu übertragen. Diese Funktion bietet eine Option für das schnelle Erstellen eines Containerimages aus dem Quellcode Ihrer Anwendung in Node.js, Java und anderen Sprachen ohne Dockerfile-Definition.

Sie können die Azure Cloud Shell oder eine lokale Installation der Azure CLI verwenden, um die Beispiele in diesem Artikel auszuführen. Wenn Sie es lokal verwenden möchten, ist Version 2.0.70 oder höher erforderlich. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI][azure-cli-install].

> [!IMPORTANT]
> Diese Funktion steht derzeit als Vorschau zur Verfügung. Vorschauversionen werden Ihnen zur Verfügung gestellt, wenn Sie die [zusätzlichen Nutzungsbedingungen][terms-of-use] akzeptieren. Einige Aspekte dieses Features werden bis zur allgemeinen Verfügbarkeit unter Umständen noch geändert.

## <a name="use-the-build-command"></a>Verwenden des „build“-Befehls

Führen Sie den Befehl [az acr pack build][az-acr-pack-build] aus, um ein Containerimage mithilfe eines Cloud Native-Buildpacks zu erstellen und mithilfe von Push zu übertragen. Während der Befehl [az acr build][az-acr-build] ein Image aus einer Dockerfile-Quelle und zugehörigem Code erstellt und mithilfe von Push überträgt, geben Sie mit `az acr pack build` direkt eine Anwendungsquellstruktur an.

Geben Sie beim Ausführen des Befehls `az acr pack build` mindestens Folgendes an:

* Eine Azure-Containerregistrierung, in der Sie den Befehl ausführen
* Ein Imagename und -tag für das neu erstellte Image
* Einen der [unterstützten Kontextspeicherorte](container-registry-tasks-overview.md#quick-task) für ACR Tasks (z. B. ein lokales Verzeichnis, ein GitHub-Repository oder ein Remotetarball)
* Den Namen eines Buildpack-Generatorimages (z. B. `cloudfoundry/cnb:bionic`)  

Der Befehl `az acr pack build` unterstützt weitere Funktionen von ACR Tasks-Befehlen (einschließlich [Run-Variablen](container-registry-tasks-reference-yaml.md#run-variables) und gestreamte und für den späteren Abruf gespeicherte [Ausführungsprotokolle für Aufgaben](container-registry-tasks-overview.md#view-task-logs)).

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Beispiel: Erstellen eines Node.js-Images mit dem Cloud Foundry-Generator

Im folgenden Beispiel wird mithilfe des Generators `cloudfoundry/cnb:bionic` ein Containerimage aus der Node.js-App im Repository [Azure-Samples/nodejs-docs-hello-world](https://github.com/Azure-Samples/nodejs-docs-hello-world) erstellt:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:bionic \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

In diesem Beispiel wird das `node-app`-Image mit dem `1.0`-Tag erstellt und mithilfe von Push an die *myregistry*-Containerregistrierung übertragen. Hier wird der Name der Zielcontainerregistrierung dem Namen des Image explizit vorangestellt. Wenn dieser nicht angegeben ist, wird die Registrierungs-URL automatisch dem Imagenamen vorangestellt.

Die Befehlsausgabe zeigt den Status der Erstellung und Übertragung des Images mithilfe von Push an. 

Nachdem das Image erfolgreich erstellt wurde, können Sie es mit Docker ausführen, sofern es installiert ist. Melden Sie sich zuerst bei Ihrer Containerregistrierung an:

```azurecli
az acr login --name myregistry
```

Führen Sie das Image aus:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Navigieren Sie in Ihrem bevorzugten Browser zu `localhost:1337`, um die Beispiel-Web-App anzuzeigen. Drücken Sie `[Ctrl]+[C]` (STRG+C), um den Container anzuhalten.

## <a name="example-build-java-image-with-heroku-builder"></a>Beispiel: Erstellen eines Java-Images mit dem Heroku-Generator

Im folgenden Beispiel wird mithilfe des Generators `heroku/buildpacks:18` ein Containerimage aus der Java-App im Repository [buildpack/sample-java-app](https://github.com/buildpack/sample-java-app) erstellt.

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

In diesem Beispiel wird das mit der Ausführungs-ID des Befehls markierte `java-app`-Image erstellt und mithilfe von Push an die Containerregistrierung *myregistry* übertragen.

Der Parameter `--pull` gibt an, dass der Befehl das neueste Generatorimage pullt. Dies ist erforderlich, da das Heroku-Generatorimage nicht von ACR Tasks zwischengespeichert wird.

Die Befehlsausgabe zeigt den Status der Erstellung und Übertragung des Images mithilfe von Push an. 

Nachdem das Image erfolgreich erstellt wurde, können Sie es mit Docker ausführen, sofern es installiert ist. Melden Sie sich zuerst bei Ihrer Containerregistrierung an:

```azurecli
az acr login --name myregistry
```

Führen Sie das Image aus, und ersetzen Sie das Imagetag durch *runid*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Navigieren Sie in Ihrem bevorzugten Browser zu `localhost:8080`, um die Beispiel-Web-App anzuzeigen. Drücken Sie `[Ctrl]+[C]` (STRG+C), um den Container anzuhalten.


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie mithilfe des Befehls `az acr pack build` ein Containerimage erstellt und mithilfe von Push übertragen haben, können Sie es wie jedes andere Image für ein Ziel Ihrer Wahl bereitstellen. Zu den Azure-Bereitstellungsoptionen gehört unter anderem die Ausführung in [App Service](../app-service/containers/tutorial-custom-docker-image.md) oder [Azure Kubernetes Service](../aks/tutorial-kubernetes-deploy-cluster.md).

Weitere Informationen zu den ACR Tasks-Funktionen finden Sie unter [Automate container image builds and maintenance with ACR Tasks (Automatisieren der Erstellung und der Wartung von Containerimages mit ACR Tasks)](container-registry-tasks-overview.md).


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
