---
title: "Azure App Service-IP-Einschränkungen | Microsoft-Dokumentation"
description: "Informationen zum Verwenden von IP-Einschränkungen mit Azure App Service"
author: btardif
manager: stefsch
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/23/2017
ms.author: byvinyal
ms.openlocfilehash: 22e05af889b4e792dcc6f6fc438e8a58674b9f0e
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2018
---
# <a name="azure-app-service-static-ip-restrictions"></a>Statische Azure App Service-IP-Einschränkungen #

Durch IP-Einschränkungen können Sie eine Liste von IP-Adressen definieren, die auf Ihre App zugreifen dürfen. Die Liste der zugelassenen IP-Adressen kann einzelne IP-Adressen oder einen Bereich von IP-Adressen enthalten, die von einer Subnetzmaske definiert werden.

Wenn von einem Client eine Anforderung an die App generiert wird, wird die IP-Adresse mit der Liste der zugelassenen IP-Adressen abgeglichen. Wenn die IP-Adresse nicht in der Liste enthalten ist, antwortet die App mit einem [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403)-Statuscode.

IP-Einschränkungen werden in der Datei „web.config“ definiert, die von Ihrer App zur Laufzeit verarbeitet wird. Unter bestimmten Umständen können einige Modul vor der IP-Einschränkungslogik in der HTTP-Pipeline ausgeführt werden. In diesem Fall tritt bei der Anforderung ein Fehler mit einem anderen HTTP-Fehlercode auf.

IP-Einschränkungen werden auf denselben App Service-Planinstanzen ausgewertet, die Ihrer App zugewiesen sind.

Um eine IP-Einschränkungsregel zu Ihrer App hinzuzufügen, wählen Sie im Menü **Netzwerk**>**IP-Einschränkungen**, und klicken Sie auf **IP-Einschränkungen konfigurieren**.

![IP-Einschränkungen] (media/app-service-ip-restrictions/ip-restrictions.png)

In dieser Ansicht können Sie die Liste der IP-Einschränkungsregeln überprüfen, die für Ihre App definiert sind.

![Liste der IP-Einschränkungen](media/app-service-ip-restrictions/browse-ip-restrictions.png)

Klicken Sie auf **[+] Hinzufügen**, um eine neue IP-Einschränkungsregel hinzuzufügen.

![Hinzufügen von IP-Einschränkungen](media/app-service-ip-restrictions/add-ip-restrictions.png)
