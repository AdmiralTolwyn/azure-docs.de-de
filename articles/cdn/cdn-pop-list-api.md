---
title: Abrufen der aktuellen Verizon-POP-Liste für Azure CDN | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe der REST-API die aktuelle Verizon POP-Liste abrufen.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: v-deasim
ms.custom: ''
ms.openlocfilehash: 9605b352755933b37819527cecbc4e1ccc30e8aa
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579070"
---
# <a name="retrieve-the-current-verizon-pop-list-for-azure-cdn"></a>Abrufen der aktuellen Verizon-POP-Liste für Azure CDN

Mit der REST-API können Sie den Satz von IP-Adressen für Point of Presence (POP)-Server von Verizon abrufen. Diese POP-Server führen Anforderung an Ursprungsserver durch, die Azure Content Delivery Network (CDN)-Endpunkten in einem Verizon-Profil (**Azure CDN Standard von Verizon** oder **Azure CDN Premium von Verizon**) zugeordnet sind. Beachten Sie, dass sich dieser Satz von IP-Adressen von den IP-Adressen unterscheidet, die dem Client bei Anforderungen an die POPs angezeigt werden würden. 

Informationen zur Syntax des REST-API-Vorgangs zum Abrufen der POP-Liste finden Sie unter [Edgeknoten – Liste](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## <a name="typical-use-case"></a>Typischer Anwendungsfall

Aus Sicherheitsgründen können Sie diese IP-Liste verwenden, um zu erzwingen, dass Anfragen an Ihren Ursprungsserver nur von einem gültigen Verizon-POP gestellt werden. Wenn ein Benutzer zum Beispiel den Hostnamen oder die IP-Adresse für den Ursprungsserver eines CDN-Endpunkts ermittelt hat, können Anfragen direkt an den Ursprungsserver gerichtet und so die Skalierungs- und Sicherheitsfunktionen von Azure CDN umgangen werden. Dieses Szenario kann verhindert werden, indem Sie die IPs in der zurückgegebenen Liste als die einzigen erlaubten IPs auf einem Ursprungsserver festlegen. Um sicherzustellen, dass Sie mit der aktuellen POP-Liste arbeiten, rufen Sie diese mindestens 1x täglich ab. 

## <a name="next-steps"></a>Nächste Schritte

Informationen zur REST-API finden Sie unter [Azure CDN-REST-APIs](https://docs.microsoft.com/rest/api/cdn/).