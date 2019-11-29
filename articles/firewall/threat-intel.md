---
title: Threat Intelligence-gestütztes Filtern für Azure Firewall
description: Das Filtern auf Basis von Threat Intelligence kann für Ihre Firewall aktiviert werden, damit diese Sie bei Datenverkehr von und zu bekannten schädlichen IP-Adressen oder Domänen warnt und diesen verweigert.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: c291dbe9c1eb37e68174a2353e296a376c7d0896
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168678"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Threat Intelligence-gestütztes Filtern für Azure Firewall

Das Filtern auf Basis von Threat Intelligence kann für Ihre Firewall aktiviert werden, damit diese Sie bei Datenverkehr von und zu bekannten schädlichen IP-Adressen oder Domänen warnt und diesen verweigert. Die IP-Adressen und Domänen stammen aus dem Microsoft Threat Intelligence-Feed. Microsoft Threat Intelligence basiert auf [Intelligent Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence) und wird von mehreren Diensten einschließlich Azure Security Center verwendet.

![Threat Intelligence für die Firewall](media/threat-intel/firewall-threat.png)

Wenn Sie das Threat Intelligence-gestützte Filtern aktiviert haben, werden die zugehörigen Regeln vor allen anderen Regeln (NAT-Regeln, Netzwerkregeln oder Anwendungsregeln) verarbeitet.

Beim Auslösen einer Regel können Sie wahlweise nur eine Warnung protokollieren oder den Modus „Warnen und Verweigern“ verwenden.

Für das Threat Intelligence-gestützte Filtern ist standardmäßig der Warnmodus aktiviert. Bis die Portalschnittstelle in Ihrer Region verfügbar wird, können Sie diese Funktion nicht deaktivieren oder den Modus ändern.

![Portalschnittstelle für Threat Intelligence-gestütztes Filtern](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Protokolle

Der folgende Protokollausschnitt zeigt eine ausgelöste Regel:

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Testen

- **Testen bei ausgehendem Datenverkehr**: Warnungen bei ausgehendem Datenverkehr sollten selten auftreten, da dies bedeutet, dass Ihre Umgebung kompromittiert ist. Um zu testen, dass Warnungen bei ausgehendem Datenverkehr funktionieren, wurde ein FQDN erstellt, der eine Warnung auslöst. Verwenden Sie **testmaliciousdomain.eastus.cloudapp.azure.com** für die Tests bei ausgehendem Datenverkehr.

- **Testen bei eingehendem Datenverkehr**: Sie können davon ausgehen, das Warnungen bei eingehendem Datenverkehr angezeigt werden, wenn DNAT-Regeln in der Firewall konfiguriert sind. Dies gilt selbst dann, wenn nur bestimmte Quellen in der DNAT-Regel zulässig sind und der sonstige Datenverkehr verweigert wird. Azure Firewall gibt keine Warnungen für alle bekannten Portscanner aus, sondern nur für Scanner, die für ihre gelegentliche Beteiligung an schädlichen Aktivitäten bekannt sind.

## <a name="next-steps"></a>Nächste Schritte

- Siehe [Log Analytics-Beispiele für Azure Firewall](log-analytics-samples.md)
- Erfahren Sie, wie Sie eine [Azure Firewall bereitstellen und konfigurieren](tutorial-firewall-deploy-portal.md).
- Lesen Sie den [Microsoft Security Intelligence Report](https://www.microsoft.com/en-us/security/operations/security-intelligence-report).