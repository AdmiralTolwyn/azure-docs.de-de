---
title: "Überwachen eines Azure-Kubernetes-Clusters mit Datadog | Microsoft-Dokumentation"
description: "Überwachen eines Kubernetes-Clusters in Azure Container Service mithilfe von Datadog"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: b2f88f663745f1caef856e9b2b5803000c284cb9
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2017
---
# <a name="monitor-an-azure-container-service-cluster-with-datadog"></a>Überwachen eines Azure Container Service-Clusters mit DataDog

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Voraussetzungen
In dieser exemplarischen Vorgehensweise wird davon ausgegangen, dass Sie mit [Azure Container Service einen Kubernetes-Cluster erstellt haben](container-service-kubernetes-walkthrough.md).

Außerdem wird angenommen, dass die Tools `az` (Azure-Befehlszeilenschnittstelle) und `kubectl` installiert sind.

Führen Sie Folgendes aus, um zu prüfen, ob das Tool `az` installiert ist:

```console
$ az --version
```

Wenn das Tool `az` nicht installiert ist, finden Sie [hier](https://github.com/azure/azure-cli#installation) Anweisungen.

Führen Sie Folgendes aus, um zu prüfen, ob das Tool `kubectl` installiert ist:

```console
$ kubectl version
```

Wenn `kubectl` nicht installiert ist, können Sie folgenden Befehl ausführen:

```console
$ az acs kubernetes install-cli
```

## <a name="datadog"></a>Datadog
Datadog ist ein Überwachungsdienst, der Überwachungsdaten von den Containern innerhalb Ihres Azure-Containerdienstclusters sammelt. Datadog hat ein „Docker Integration Dashboard“, das bestimmte Metriken in Ihrem Container anzeigt. Aus Ihren Containern gesammelte Metriken werden nach CPU, Arbeitsspeicher, Netzwerk und E/A organisiert. Datadog teilt Metriken in Container und Images auf.

Sie müssen zuerst [ein Konto erstellen](https://www.datadoghq.com/lpg/).

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Installieren des Datadog-Agents mit einem DaemonSet
DaemonSets werden von Kubernetes verwendet, um auf jedem Host im Cluster eine einzelne Instanz eines Containers auszuführen.
Sie eignen sich perfekt zum Ausführen von Überwachungs-Agents.

Nachdem Sie sich bei Datadog angemeldet haben, können Sie gemäß der [Datadog-Anleitung](https://app.datadoghq.com/account/settings#agent/kubernetes) vorgehen, um Datadog-Agents mithilfe eines DaemonSets in Ihrem Cluster zu installieren.

## <a name="conclusion"></a>Zusammenfassung
Das ist alles! Wenn die Agents ausgeführt werden, sollten innerhalb weniger Minuten Daten in der Konsole angezeigt werden. Im integrierten [Kubernetes-Dashboard](https://app.datadoghq.com/screen/integration/kubernetes) finden Sie eine Zusammenfassung Ihres Clusters.
