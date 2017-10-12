---
title: "Azure CLI-Skriptbeispiel: Hinzufügen einer Anwendung in Batch | Microsoft-Dokumentation"
description: "Azure CLI-Skriptbeispiel: Hinzufügen einer Anwendung in Batch"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: antisch
ms.openlocfilehash: cbfe8ab565ecf7f298a9a6c0f0c8298c675f178c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="adding-applications-to-azure-batch-with-azure-cli"></a>Hinzufügen von Anwendungen in Azure Batch mit der Azure-Befehlszeilenschnittstelle

Dieses Skript veranschaulicht die Einrichtung einer Anwendung für die Verwendung mit einem Azure Batch-Pool oder -Task. Für das Einrichten einer Anwendung fassen Sie die ausführbare Datei mit sämtlichen Abhängigkeiten in einer ZIP-Datei zusammen. In diesem Beispiel hat die ausführbare ZIP-Datei den Namen „my-application-exe.zip“.

## <a name="prerequisites"></a>Voraussetzungen

- Installieren Sie Azure CLI gemäß den Anweisungen im [Azure CLI-Installationshandbuch](https://docs.microsoft.com/cli/azure/install-azure-cli), wenn dies noch nicht erfolgt ist.
- Erstellen Sie ein Batch-Konto, falls Sie noch keins besitzen. Ein Beispielskript zum Erstellen eines Kontos finden Sie unter [Erstellen eines Batch-Kontos mit der Azure-Befehlszeilenschnittstelle (CLI)](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account).

## <a name="sample-script"></a>Beispielskript

[!code-azurecli[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-application"></a>Bereinigen der Anwendung

Nachdem Sie das oben aufgeführte Beispielskript ausgeführt haben, führen Sie die folgenden Befehle aus, um die Anwendung und alle hochgeladenen Anwendungspakete zu entfernen.

```azurecli
az batch application package delete -g myresourcegroup -n mybatchaccount --application-id myapp --version 1.0 --yes
az batch application delete -g myresourcegroup -n mybatchaccount --application-id myapp --yes
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Dieses Skript verwendet die folgenden Befehle für das Erstellen einer Anwendung und das Hochladen eines Anwendungspakets.
Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Befehl | Hinweise |
|---|---|
| [az batch application create](https://docs.microsoft.com/cli/azure/batch/application#az_batch_application_create) | Erstellt eine Anwendung  |
| [az batch application set](https://docs.microsoft.com/cli/azure/batch/application#az_batch_application_set) | Aktualisiert die Eigenschaften einer Anwendung  |
| [az batch application package create](https://docs.microsoft.com/cli/azure/batch/application/package#az_batch_application_package_create) | Fügt der angegebenen Anwendung ein Anwendungspaket hinzu  |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](https://docs.microsoft.com/cli/azure/overview).

Zusätzliche Batch-CLI-Skriptbeispiele finden Sie in der [Dokumentation zur Azure Batch-CLI](../batch-cli-samples.md).
