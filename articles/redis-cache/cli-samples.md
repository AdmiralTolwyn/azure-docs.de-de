---
title: 'Azure CLI: Redis Cache-Beispiele | Microsoft-Dokumentation'
description: Azure CLI-Beispiele für Azure Redis Cache
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 8d2de145-50c0-4f76-bf8f-fdf679f03698
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.date: 04/14/2017
ms.author: wesmc
ms.openlocfilehash: 4450a8f7f16e3503626c9ee0ca125fc2b1ed9052
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/19/2018
ms.locfileid: "27909838"
---
# <a name="azure-cli-samples-for-azure-redis-cache"></a>Azure CLI-Beispiele für Azure Redis Cache

Die folgende Tabelle enthält Links zu Bash-Skripts, die mithilfe der Azure CLI erstellt wurden.

| | |
|---|---|
|**Erstellen eines Caches**||
| [Erstellen eines Caches](./scripts/create-cache.md) | Erstellt eine Ressourcengruppe und einen Azure Redis Cache im Basic-Tarif. |
| [Erstellen eines Premium-Caches mit Clustering](./scripts/create-premium-cache-cluster.md) | Erstellt eine Ressourcengruppe und einen Cache im Premium-Tarif mit aktiviertem Clustering.|
| [Abrufen von Cachedetails](./scripts/show-cache.md) | Ruft die Details einer Azure Redis Cache-Instanz samt Bereitstellungsstatus ab. |
| [Abrufen von Hostname, Ports und Schlüsseln](./scripts/cache-keys-ports.md) | Ruft den Hostnamen, die Ports und Schlüssel einer Azure Redis Cache-Instanz ab. |
|**Web-App plus Cache**||
| [Herstellen einer Verbindung einer Web-App mit einem Redis-Cache](./../app-service/scripts/app-service-cli-app-service-redis.md) | Erstellt eine Azure-Web-App und einen Redis Cache und fügt dann die Redis-Verbindungsdetails zu den App-Einstellungen hinzu. |
|**Löschen eines Caches**||
| [Löschen eines Caches](./scripts/delete-cache.md) | Löscht eine Azure Redis Cache-Instanz.  |
| | |

Weitere Informationen zu Azure CLI 2.0 finden Sie unter [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (Installieren von Azure CLI 2.0) und [Get started with Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) (Erste Schritte mit Azure CLI 2.0).