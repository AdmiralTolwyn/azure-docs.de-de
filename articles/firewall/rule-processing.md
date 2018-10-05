---
title: Logik für die Azure Firewall-Regelverarbeitung
description: Enthält eine Beschreibung der Logik für die Azure Firewall-Regelverarbeitung.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/27/2018
ms.author: victorh
ms.openlocfilehash: 542682037a932a2e3b08c71b38b64b2694ad40f3
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228098"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logik für die Azure Firewall-Regelverarbeitung
Azure Firewall verfügt über NAT-Regeln, Netzwerkregeln und Anwendungsregeln. Die Regeln werden gemäß dem Regeltyp verarbeitet.


## <a name="network-rules-and-applications-rules"></a>Netzwerkregeln und Anwendungsregeln 
Zuerst werden die Netzwerkregeln angewendet, und dann die Anwendungsregeln. Die Regeln können zur Beendigung von Vorgängen führen. Wenn sich für eine Netzwerkregel eine Übereinstimmung ergibt, werden die Anwendungsregeln also nicht verarbeitet.  Wenn sich keine Übereinstimmung für eine Netzwerkregel ergibt und als Paketprotokoll HTTP/HTTPS verwendet wird, wird das Paket von den Anwendungsregeln ausgewertet. Falls sich immer noch keine Übereinstimmung ergibt, wird das Paket von der Regelsammlung der Infrastruktur ausgewertet. Wenn sich auch hierbei keine Übereinstimmung ergibt, wird das Paket standardmäßig abgelehnt.

## <a name="nat-rules"></a>NAT-Regeln
Eingehende Konnektivität kann aktiviert werden, indem wie unter [Tutorial: Filtern von eingehendem Datenverkehr per Azure Firewall-DNAT im Azure-Portal](tutorial-firewall-dnat.md) beschrieben DNAT (Destination Network Address Translation) konfiguriert wird. DNAT-Regeln werden zuerst angewendet. Wenn sich eine Übereinstimmung ergibt, wird eine implizite entsprechende Netzwerkregel hinzugefügt, um den übersetzten Datenverkehr zuzulassen. Sie können dieses Verhalten außer Kraft setzen, indem Sie explizit eine Netzwerkregelsammlung mit Ablehnungsregeln hinzufügen, die für den übersetzten Datenverkehr geeignet sind. Für diese Verbindungen werden keine Anwendungsregeln angewendet.


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie eine [Azure Firewall bereitstellen und konfigurieren](tutorial-firewall-deploy-portal.md).