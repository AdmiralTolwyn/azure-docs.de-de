---
title: Einführung in die Web Application Firewall (WAF) für Azure Application Gateway
description: Dieser Artikel enthält eine Übersicht über die Web Application Firewall (WAF) für Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 11/16/2018
ms.author: amsriva
ms.openlocfilehash: 9bccc9258a6bd9a6fef4956d0f32cb00dd3c542d
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56454258"
---
# <a name="web-application-firewall-waf"></a>Web Application Firewall (WAF)

Web Application Firewall (WAF) ist ein Feature von Application Gateway, das zentralisierten Schutz Ihrer Webanwendungen vor allgemeinen Exploits und Sicherheitsrisiken bietet.

Webanwendungen sind zunehmend Ziele böswilliger Angriffe, die allgemein bekannte Sicherheitslücken ausnutzen. Zu diesen Sicherheitslücken (Exploits) gehören üblicherweise Angriffe durch Einschleusung von SQL-Befehlen und Angriffe durch websiteübergreifende Skripts, um nur einige zu nennen. 

Das Verhindern solcher Angriffe im Anwendungscode ist oft schwierig und erfordert strenge Wartung, Patching und Überwachung auf verschiedenen Ebenen der Anwendungstopologie. Eine zentrale Web Application Firewall vereinfacht die Sicherheitsverwaltung erheblich und bietet Anwendungsadministratoren einen besseren Schutz vor Bedrohungen und Angriffen. Mit einer WAF-Lösung können Sie ebenfalls schneller auf ein Sicherheitsrisiko reagieren, da eine bekannte Schwachstelle an einem zentralen Ort gepatcht wird, statt jede einzelne Webanwendung separat zu sichern. Vorhandene Anwendungsgateways lassen sich problemlos in ein Anwendungsgateway mit Web Application Firewall konvertieren.

Der WAF liegen Regeln aus den [OWASP-Kernregelsätzen](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) (3.0 oder 2.2.9) zugrunde. Sie wird automatisch aktualisiert und bietet Schutz vor neuen Sicherheitsrisiken, ohne dass zusätzliche Konfiguration erforderlich ist.

![imageURLroute](./media/waf-overview/WAF1.png)

Application Gateway fungiert als Controller für die Anwendungsbereitstellung (Application Delivery Controller, ADC) und bietet SSL-Beendigung, cookiebasierte Sitzungsaffinität, Lastverteilung per Roundrobin, inhaltsbasiertes Routing, die Möglichkeit zum Hosten mehrerer Websites sowie Sicherheitsverbesserungen.

Zu den von Application Gateway bereitgestellten Sicherheitsverbesserungen gehören die SSL-Gruppenrichtlinienverwaltung sowie umfassende SSL-Unterstützung. Die Anwendungssicherheit wird nun durch die direkt in das ADC-Angebot integrierte WAF (Web Application-Firewall) gestärkt. So erhalten Sie einen einfach konfigurierbaren zentralen Ort zum Verwalten und Schützen Ihrer Webanwendungen vor gängigen Sicherheitsrisiken im Web.

## <a name="benefits"></a>Vorteile

Application Gateway und Web Application Firewall bieten folgende zentrale Vorteile:

### <a name="protection"></a>Schutz

* Schützen Sie Ihre Webanwendung vor Sicherheitsrisiken und Angriffen im Web, ohne den Back-End-Code zu verändern.

* Schützen Sie mehrere Webanwendungen gleichzeitig hinter einem Anwendungsgateway. Das Anwendungsgateway unterstützt das Hosten von bis zu 20 Websites hinter einem einzelnen Gateway, die alle mit der WAF vor Webangriffen geschützt werden können.

### <a name="monitoring"></a>Überwachung

* Überwachen Sie Ihre Webanwendung zum Schutz vor Angriffen mithilfe eines WAF-Echtzeitprotokolls. Dieses Protokoll ist in [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) integriert und dient zum Nachverfolgen von WAF-Warnungen und -protokollen sowie zum Überwachen von Trends.

* WAF ist in Azure Security Center integriert. Mit Azure Security Center können Sie sich an einem zentralen Ort über den Sicherheitsstatus sämtlicher Azure-Ressourcen informieren.

### <a name="customization"></a>Anpassung

* WAF-Regeln und -Regelgruppen können an Ihre individuellen Anwendungsanforderungen angepasst werden, um falsch positive Ergebnisse zu vermeiden.

## <a name="features"></a>Features

- Schutz vor Einschleusung von SQL-Befehlen
- Schutz vor websiteübergreifenden Skripts
- Schutz vor allgemeinen Webangriffen wie Befehlseinschleusung, HTTP Request Smuggling, HTTP Response Splitting und Remote File Inclusion
- Schutz vor Verletzungen des HTTP-Protokolls
- Schutz vor HTTP-Protokollanomalien, z.B. fehlende user-agent- und accept-Header des Hosts
- Verhinderung des Einsatzes von Bots, Crawlern und Scannern
- Erkennung häufiger Fehler bei der Anwendungskonfiguration (z.B. Apache, IIS usw.)

### <a name="public-preview-features"></a>Features der öffentlichen Vorschau

Die aktuelle WAF-SKU (öffentliche Vorschau) beinhaltet die folgenden Funktionen:

- **Grenzwerte für die Anforderungsgröße**: Die Web Application Firewall ermöglicht Benutzern das Konfigurieren von Größenlimits mit Ober- und Untergrenzen.
- **Ausschlusslisten**: Mit WAF-Ausschlusslisten können Benutzer bestimmte Anforderungsattribute in einer WAF-Auswertung weglassen. Ein gängiges Beispiel sind von Active Directory eingefügte Token, die für Authentifizierungs- oder Kennwortfelder verwendet werden.

Weitere Informationen über die öffentliche Vorschau von WAF finden Sie unter [Web Application Firewall – Grenzwerte für die Anforderungsgröße und Ausschlusslisten (öffentliche Vorschau)](application-gateway-waf-configuration.md).





### <a name="core-rule-sets"></a>Kernregelsätze

Application Gateway unterstützt zwei Regelsätze: CRS 3.0 und CRS 2.2.9. Bei diesen Kernregelsätzen handelt es sich jeweils um eine Sammlung von Regeln, die Ihre Webanwendungen vor schädlichen Aktivitäten schützen.

Die Web Application Firewall ist standardmäßig mit CRS 3.0 vorkonfiguriert, Sie können aber auch 2.2.9 verwenden. Verglichen mit 2.2.9 liefert CRS 3.0 weniger falsch positive Ergebnisse. Sie können [Regeln an Ihre individuellen Anforderungen anpassen](application-gateway-customize-waf-rules-portal.md). Die Web Application Firewall schützt unter anderem vor folgenden gängigen Sicherheitslücken im Web:

- Schutz vor Einschleusung von SQL-Befehlen
- Schutz vor websiteübergreifenden Skripts
- Schutz vor allgemeinen Webangriffen wie Befehlseinschleusung, HTTP Request Smuggling, HTTP Response Splitting und Remote File Inclusion
- Schutz vor Verletzungen des HTTP-Protokolls
- Schutz vor HTTP-Protokollanomalien, z.B. fehlende user-agent- und accept-Header des Hosts
- Verhinderung des Einsatzes von Bots, Crawlern und Scannern
- Erkennung häufiger Fehler bei der Anwendungskonfiguration (z.B. Apache, IIS usw.)

Eine ausführlichere Liste mit Regeln und Informationen zum entsprechenden Schutz finden Sie in den [Kernregelsätzen](#core-rule-sets).


#### <a name="owasp30"></a>OWASP_3.0

Version 3.0 des Kernregelsatzes umfasst 13 Regelgruppen, wie in der folgenden Tabelle zu sehen. Jede dieser Regelgruppen enthält mehrere deaktivierbare Regeln.

|Regelgruppe|BESCHREIBUNG|
|---|---|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|Enthält Regeln zum Sperren von Methoden (PUT, PATCH).|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**| Enthält Regeln zum Schutz vor Port- und Umgebungsscannern.|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|Enthält Regeln zum Schutz vor Protokoll- und Codierungsproblemen.|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|Enthält Regeln zum Schutz vor Header Injection, Request Smuggling und Response Splitting.|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|Enthält Regeln zum Schutz vor Datei- und Pfadangriffen.|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|Enthält Regeln zum Schutz vor RFI (Remote File Inclusion).|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|Enthält Regeln zum Schutz vor der Remoteausführung von Code.|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|Enthält Regeln zum Schutz vor Angriffen mit PHP-Einschleusung.|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|Enthält Regeln zum Schutz vor Cross-Site Scripting.|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|Enthält Regeln zum Schutz vor Angriffen mit Einschleusung von SQL-Befehlen.|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|Enthält Regeln zum Schutz vor Session Fixation-Angriffen.|

#### <a name="owasp229"></a>OWASP_2.2.9

Version 2.2.9 des Kernregelsatzes umfasst zehn Regelgruppen, wie in der folgenden Tabelle zu sehen. Jede dieser Regelgruppen enthält mehrere deaktivierbare Regeln.

|Regelgruppe|BESCHREIBUNG|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|Enthält Regeln zum Schutz vor Protokollverletzungen (etwa durch ungültige Zeichen, GET mit einem Anforderungstext und Ähnliches).|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|Enthält Regeln zum Schutz vor falschen Headerinformationen.|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|Enthält Regeln zum Schutz vor Argumenten oder Dateien mit Grenzwertüberschreitung.|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|Enthält Regeln zum Schutz vor eingeschränkten Methoden, Headern und Dateitypen. |
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|Enthält Regeln zum Schutz vor Webcrawlern und -scannern.|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|Enthält Regeln zum Schutz vor generischen Angriffen (Session Fixation, Remote File Inclusion, PHP-Einschleusung usw.).|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|Enthält Regeln zum Schutz vor Angriffen mit Einschleusung von SQL-Befehlen.|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|Enthält Regeln zum Schutz vor Cross-Site Scripting.|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|Enthält eine Regel zum Schutz vor Path Traversal-Angriffen.|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|Enthält Regeln zum Schutz vor Hintertürtrojanern.|

### <a name="waf-modes"></a>WAF-Modi

Die Application Gateway-WAF kann für die Ausführung in den folgenden beiden Modi konfiguriert werden:

* **Erkennungsmodus**: Wenn sie für die Ausführung im Erkennungsmodus konfiguriert ist, überwacht die Application Gateway-WAF alle Bedrohungswarnungen und protokolliert sie in einer Protokolldatei. Im Abschnitt **Diagnose** muss die Protokollierung von Diagnosedaten für Application Gateway aktiviert werden. Zudem müssen Sie sicherstellen, dass das WAF-Protokoll ausgewählt und aktiviert ist. Im Erkennungsmodus werden eingehende Anforderungen von der Web Application Firewall nicht blockiert.
* **Schutzmodus** : Wenn die WAF für die Ausführung im Schutzmodus konfiguriert ist, blockiert Application Gateway aktiv Eindringlinge und Angriffe, die von den Regeln erkannt werden. Der Angreifer erhält eine Ausnahme 403 (nicht autorisierter Zugriff), und die Verbindung wird beendet. Der Schutzmodus protokolliert solche Angriffe weiterhin in den WAF-Protokollen.

### <a name="application-gateway-waf-reports"></a>WAF-Überwachung

Die Integrität Ihres Anwendungsgateways sollte unbedingt überwacht werden. Die Überwachung der Integrität Ihrer Web Application Firewall und der durch sie geschützten Anwendungen wird mittels Protokollierung und Integration in Azure Monitor, Azure Security Center und Azure Monitor-Protokolle erreicht.

![Diagnose](./media/waf-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Jedes Anwendungsgatewayprotokoll ist in [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) integriert.  Dadurch können Sie Diagnoseinformationen einschließlich WAF-Warnungen und -Protokolle nachverfolgen.  Diese Funktion wird innerhalb der Application Gateway-Ressource im Portal auf der Registerkarte **Diagnose** oder direkt über den Azure Monitor-Dienst bereitgestellt. Weitere Informationen zum Aktivieren von Diagnoseprotokollen für das Anwendungsgateway finden Sie unter [Back-End-Integrität, Diagnoseprotokollierung und Metriken für Application Gateway](application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../security-center/security-center-intro.md) unterstützt Sie beim Verhindern, Erkennen und Beheben von Bedrohungen durch größere Transparenz und bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen. Das Anwendungsgateway ist jetzt [in Azure Security Center integriert](application-gateway-integration-security-center.md). Azure Security Center scannt die Umgebung, um ungeschützte Webanwendungen zu ermitteln. Es kann jetzt eine WAF für das Anwendungsgateway empfohlen werden, um diese ungeschützten Ressourcen zu schützen. Sie können die WAF für das Anwendungsgateway direkt aus dem Azure Security Center erstellen.  Diese WAF-Instanzen sind in Azure Security Center integriert und senden Warnungen und Integritätsinformationen für die Berichtserstellung zurück an Azure Security Center.

![Abbildung 1](./media/waf-overview/figure1.png)

#### <a name="logging"></a>Protokollierung

Die Application Gateway-WAF bietet detaillierte Berichte zu jeder erkannten Bedrohung. Die Protokollierung ist in Azure Diagnostics-Protokolle integriert, und Warnungen werden in einem JSON-Format erfasst. Diese Protokolle können in [Azure Monitor-Protokolle](../azure-monitor/insights/azure-networking-analytics.md) integriert werden.

![imageURLroute](./media/waf-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>SKU-Preise für die Application Gateway-WAF

Die Web Application Firewall ist unter einer neuen WAF-SKU erhältlich. Diese SKU steht nur im Rahmen des Azure Resource Manager-Bereitstellungsmodells zur Verfügung. Für das klassische Bereitstellungsmodell ist sie nicht verfügbar. Darüber hinaus ist die WAF-SKU nur in den Anwendungsgateway-Instanzgrößen „Medium“ und „Large“ erhältlich. Alle Grenzwerte für das Anwendungsgateway gelten auch für die WAF-SKU.

Der Preis basiert auf einer Gatewayinstanzgebühr pro Stunde sowie auf einer Datenverarbeitungsgebühr. Der stundenbasierte Gatewaypreis für die WAF-SKU entspricht nicht den Gebühren für die Standard-SKU und ist unter [Application Gateway – Preise](https://azure.microsoft.com/pricing/details/application-gateway/) zu finden. Die Datenverarbeitungsgebühren bleiben unverändert. Es fallen keine regel- oder regelgruppenbasierten Kosten an. Sie können mehrere Webanwendungen hinter der gleichen Web Application Firewall schützen, und die Unterstützung mehrerer Anwendungen ist mit keinerlei Zusatzgebühren verbunden.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie im Anschluss an diese Informationen zu WAF [Konfigurieren der Web Application Firewall auf Application Gateway](tutorial-restrict-web-traffic-powershell.md).

