---
title: Tutorial zu Kubernetes in Azure – Vorbereiten von Apps
description: AKS-Tutorial – Vorbereiten der App
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 3e500ec0c7acbf8d8e10756c944516cd7e589610
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101009"
---
# <a name="tutorial-prepare-application-for-azure-kubernetes-service-aks"></a>Tutorial: Vorbereiten einer Anwendung für Azure Kubernetes Service (AKS)

In diesem Tutorial – Teil 1 von 7 – wird eine Anwendung mit mehreren Containern für die Verwendung in Kubernetes vorbereitet. Folgende Schritte werden ausgeführt:

> [!div class="checklist"]
> * Klonen der Anwendungsquelle von GitHub
> * Erstellen eines Containerimages aus der Anwendungsquelle
> * Testen der Anwendung in einer lokalen Docker-Umgebung

Nach Abschluss kann in Ihrer lokalen Entwicklungsumgebung auf die folgende Anwendung zugegriffen werden.

![Abbildung: Kubernetes-Cluster in Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

In den nachfolgenden Tutorials wird das Containerimage in eine Azure Container Registry-Instanz hochgeladen und anschließend in einem AKS-Cluster ausgeführt.

## <a name="before-you-begin"></a>Voraussetzungen

In diesem Tutorial wird vorausgesetzt, dass zentrale Docker-Begriffe wie Container und Containerimages sowie grundlegende Docker-Befehle bekannt sind. Eine Einführung in die Grundlagen der Container finden Sie bei Bedarf unter [Get started with Docker][docker-get-started] (Erste Schritte mit Docker).

Für dieses Tutorial ist eine Docker-Entwicklungsumgebung erforderlich. Für Docker sind Pakete erhältlich, mit denen Docker problemlos auf einem [Mac-][docker-for-mac], [Windows-][docker-for-windows] oder [Linux-][docker-for-linux]System konfiguriert werden kann.

Azure Cloud Shell umfasst keine Docker-Komponenten, die zum Abschließen der einzelnen Schritte dieses Tutorials erforderlich sind. Aus diesem Grund wird empfohlen, eine vollständige Docker-Entwicklungsumgebung zu verwenden.

## <a name="get-application-code"></a>Abrufen von Anwendungscode

Die in diesem Tutorial verwendete Beispielanwendung ist eine einfache Abstimmungs-App. Die Anwendung besteht aus einer Front-End-Webkomponente und einer Back-End-Redis-Instanz. Die Webkomponente wird in ein benutzerdefiniertes Containerimage gepackt. Die Redis-Instanz verwendet ein nicht modifiziertes Image aus Docker Hub.

Verwenden Sie Git, um eine Kopie der Anwendung in Ihre Entwicklungsumgebung herunterzuladen.

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Wechseln Sie in das Verzeichnis, sodass Sie im geklonten Verzeichnis arbeiten.

```console
cd azure-voting-app-redis
```

Im Verzeichnis befinden sich der Anwendungsquellcode, eine vorab erstellte Docker Compose-Datei und eine Kubernetes-Manifestdatei. Diese Dateien werden während des Tutorials verwendet.

## <a name="create-container-images"></a>Erstellen von Containerimages

[Docker Compose][docker-compose] kann verwendet werden, um die Erstellung von Containerimages und die Bereitstellung von Anwendungen mit mehreren Containern zu automatisieren.

Führen Sie die Datei `docker-compose.yaml` aus, um das Containerimage zu erstellen, das Redis-Image herunterzuladen und die Anwendung zu starten.

```console
docker-compose up -d
```

Verwenden Sie anschließend den Befehl [docker-images][docker-images], um die erstellten Images anzuzeigen.

```console
docker images
```

Beachten Sie, dass drei Images heruntergeladen oder erstellt wurden. Das `azure-vote-front`-Image enthält die Anwendung und verwendet das `nginx-flask`-Image als Grundlage. Das `redis`-Image wird zum Starten einer Redis-Instanz verwendet.

```
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Führen Sie den Befehl [docker ps][docker-ps] aus, um die ausgeführten Container anzuzeigen.

```console
docker ps
```

Ausgabe:

```
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Lokales Testen der Anwendung

Navigieren Sie zu http://localhost:8080, um die ausgeführte Anwendung anzuzeigen.

![Abbildung: Kubernetes-Cluster in Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nach der Überprüfung der Funktionsfähigkeit der Anwendung können die ausgeführten Container beendet und entfernt werden. Löschen Sie die Containerimages nicht. Das `azure-vote-front`-Image wird im nächsten Tutorial in eine Azure Container Registry-Instanz hochgeladen.

Führen Sie folgenden Befehl aus, um die ausgeführten Container zu beenden.

```console
docker-compose stop
```

Löschen Sie die beendeten Container und Ressourcen mit dem folgenden Befehl:

```console
docker-compose down
```

Nach Abschluss des Vorgangs verfügen Sie über ein Containerimage, das die Azure Voting-Anwendung enthält.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde eine Anwendung getestet und es wurden Containerimages für die Anwendung erstellt. Die folgenden Schritte wurden durchgeführt:

> [!div class="checklist"]
> * Klonen der Anwendungsquelle von GitHub
> * Erstellen eines Containerimages aus der Anwendungsquelle
> * Testen der Anwendung in einer lokalen Docker-Umgebung

Wechseln Sie zum nächsten Tutorial, und erfahren Sie, wie Containerimages in einer Azure Container Registry gespeichert werden.

> [!div class="nextstepaction"]
> [Übertragen von Images zu Azure Container Registry mithilfe von Push][aks-tutorial-prepare-acr]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-ps]: https://docs.docker.com/engine/reference/commandline/ps/

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md