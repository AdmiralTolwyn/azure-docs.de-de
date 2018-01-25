---
title: Verwenden von Azure Redis Cache mit Python | Microsoft Docs
description: Erste Schritte mit Azure Redis Cache und Python
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: v-lincan
ms.assetid: f186202c-fdad-4398-af8c-aee91ec96ba3
ms.service: cache
ms.devlang: python
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/10/2017
ms.author: wesmc
ms.openlocfilehash: 17a22dc7a18931e368c7f2e61c563e0d99c3a7ac
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-azure-redis-cache-with-python"></a>Verwenden von Azure Redis Cache mit Python
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Dieses Thema beschreibt die ersten Schritte mit Azure Redis Cache und Python.

## <a name="prerequisites"></a>Voraussetzungen
Installieren Sie [redis-py](https://github.com/andymccurdy/redis-py).

## <a name="create-a-redis-cache-on-azure"></a>Erstellen eines Redis-Caches in Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Abrufen des Hostnamens und der Zugriffsschlüssel
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="enable-the-non-ssl-endpoint"></a>Aktivieren des Nicht-SSL-Endpunkts
Einige Redis-Clients weisen keine Unterstützung für SSL auf, und der [Nicht-SSL-Port ist für neue Azure Redis Cache-Instanzen standardmäßig deaktiviert](cache-configure.md#access-ports). Zum Zeitpunkt der Erstellung dieses Dokuments wird SSL vom [redis-py](https://github.com/andymccurdy/redis-py) -Client nicht unterstützt. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]

## <a name="add-something-to-the-cache-and-retrieve-it"></a>Fügen Sie dem Cache Daten hinzu, und rufen Sie sie ab.
    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'


Ersetzen Sie `<name>` durch den Namen Ihres Caches und `key` durch Ihren Zugriffsschlüssel.

<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png
