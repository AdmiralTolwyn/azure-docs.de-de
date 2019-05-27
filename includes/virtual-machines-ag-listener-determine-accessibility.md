---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66165455"
---
Sie müssen beachten, dass es zwei Methoden zum Konfigurieren eines Verfügbarkeitsgruppenlisteners in Azure gibt. Diese Methoden unterscheiden sich durch den Typ des Azure-Lastenausgleichs, den Sie verwenden, wenn Sie den Listener erstellen. In der folgenden Tabelle sind die Unterschiede beschrieben:

| Typ des Lastenausgleichs | Implementierung | Die Verwendung empfiehlt sich unter folgenden Voraussetzungen: |
| --- | --- | --- |
| **Extern** |Verwendet die *öffentliche virtuelle IP-Adresse* des Clouddiensts, der die virtuellen Computer (VMs) hostet. |Sie müssen auf den Listener von außerhalb des virtuellen Netzwerks zugreifen, auch über das Internet. |
| **Intern** |Verwendet einen *internen Lastenausgleich* mit einer privaten Adresse für den Listener. |Sie können auf den Listener nur im gleichen virtuellen Netzwerk zugreifen. Dieser Zugriff schließt Site-to-Site-VPNs in Hybridszenarien ein. |

> [!IMPORTANT]
> Für einen Listener, der die öffentliche VIP-Adresse des Clouddiensts (externer Lastenausgleich) verwendet, fallen keine Gebühren für ausgehende Datenübertragungen an, solange sich der Client, der Listener und die Datenbanken in der gleichen Azure-Region befinden. Andernfalls gelten alle über den Listener zurückgegebenen Daten als ausgehende Datenübertragungen und werden zu normalen Datenübertragungsraten berechnet. 
> 
> 

ILB kann nur für virtuelle Netzwerke mit regionalem Umfang konfiguriert werden. Vorhandene virtuelle Netzwerke, die für eine Affinitätsgruppe konfiguriert wurden, können ILB nicht verwenden. Weitere Informationen finden Sie unter [Übersicht über den internen Lastenausgleich](../articles/load-balancer/load-balancer-internal-overview.md).

