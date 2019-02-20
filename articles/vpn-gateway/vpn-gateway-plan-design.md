---
title: 'Planung und Entwurf für standortübergreifende Verbindungen: Azure VPN Gateway | Microsoft-Dokumentation '
description: Erfahren Sie mehr über Planung und Entwurf von VPN-Gateways für standortübergreifende, Hybrid- und VNet-zu-VNet-Verbindungen.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/27/2017
ms.author: yushwang
ms.openlocfilehash: 7802061ba09a30ca34ed3804ace846118c5edb9b
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56235361"
---
# <a name="planning-and-design-for-vpn-gateway"></a>Planung und Entwurf für VPN Gateway

Die Planung und der Entwurf Ihrer standortübergreifenden und VNet-zu-VNet-Verbindungen sind je nach Ihren Netzwerkanforderungen entweder einfach oder kompliziert. Dieser Artikel erläutert die grundlegenden Aspekte der Planung und des Entwurfs.

## <a name="planning"></a>Planung

### <a name="compare"></a>Optionen der standortübergreifenden Konnektivität

Wenn Sie Ihre lokalen Standorte sicher mit einem virtuellen Netzwerk verbinden möchten, stehen Ihnen drei verschiedene Möglichkeiten zur Verfügung: Standort-zu-Standort, Punkt-zu-Standort und ExpressRoute.  Vergleichen Sie die verschiedenen verfügbaren standortübergreifenden Verbindungen. Die von Ihnen gewählte Option kann von verschiedenen Faktoren abhängen:

* Welche Form von Durchsatz benötigt Ihre Lösung?
* Soll die Kommunikation über das öffentliche Internet über ein sicheres VPN oder über eine private Verbindung erfolgen?
* Steht Ihnen eine öffentliche IP-Adresse zur Verfügung?
* Planen Sie den Einsatz eines VPN-Geräts? Wenn ja, ist es kompatibel?
* Stellen Sie nur eine Verbindung zwischen ein paar Computern her, oder möchten Sie eine dauerhafte Verbindung für Ihren Standort?
* Welche Art von VPN-Gateway ist für die Lösung erforderlich, die Sie erstellen möchten?
* Welche Gateway-SKU sollten Sie verwenden?

### <a name="planningtable"></a>Planungstabelle

Die folgende Tabelle kann Ihnen dabei helfen, die beste Verbindungsoption für Ihre Lösung zu finden.

[!INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="gwsku"></a>Gateway-SKUs

[!INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

### <a name="wf"></a>Workflow

Die folgende Liste beschreibt den allgemeinen Workflow für die Cloud-Konnektivität:

1. Entwerfen und planen Sie die Netzwerktopologie, und erstellen Sie eine Liste der Adressräume für alle Netzwerke, die Sie verbinden möchten.
2. Erstellen Sie ein virtuelles Azure-Netzwerk. 
3. Erstellen Sie ein VPN-Gateway für das virtuelle Netzwerk.
4. Erstellen und konfigurieren Sie Verbindungen mit lokalen Netzwerken oder anderen virtuellen Netzwerken (bei Bedarf).
5. Erstellen und konfigurieren Sie eine Punkt-zu-Standort-Verbindung für Ihr Azure-VPN-Gateway (bei Bedarf).

## <a name="design"></a>Entwurf
### <a name="topologies"></a>Verbindungstopologien

Sehen Sie sich zunächst die Diagramme im Artikel [Informationen zu VPN Gateway](vpn-gateway-about-vpngateways.md) an. Der Artikel enthält grundlegende Diagramme, die Bereitstellungsmodelle für jede Topologie sowie die Bereitstellungstools, die Sie zum Bereitstellen Ihrer Konfiguration verwenden können.

### <a name="designbasics"></a>Entwurfsgrundlagen

In den folgenden Abschnitten werden die Grundlagen von VPN-Gateways erläutert. 

#### <a name="servicelimits"></a>Grenzwerte für Netzwerkdienste

Scrollen Sie durch die Tabellen, um die [Einschränkungen für Netzwerkdienste](../azure-subscription-service-limits.md#networking-limits) einzusehen. Die aufgeführten Einschränkungen können sich auf Ihren Entwurf auswirken.

#### <a name="subnets"></a>Informationen zu Subnetzen

Beim Erstellen von Verbindungen müssen Sie die Adressbereiche für Subnetze berücksichtigen. Es dürfen keine überlappenden Adressbereiche für Subnetze vorhanden sein. Bei überlappenden Subnetzen enthalten ein virtuelles Netzwerk und ein lokaler Standort denselben Adressraum. Dies bedeutet, dass die Netzwerktechniker für Ihre lokalen Netzwerke einen Bereich zur Verwendung für Ihre Azure-IP-Adressräume/-Subnetze zuteilen müssen. Sie benötigen einen Adressraum, der nicht im lokalen Netzwerk verwendet wird.

Das Vermeiden von überlappenden Subnetzen ist auch bei der Arbeit mit VNet-zu-VNet-Verbindungen wichtig. Wenn die Subnetze überlappen und eine IP-Adresse sowohl im sendenden als auch im Ziel-VNet vorhanden ist, schlagen VNet-zu-VNet-Verbindungen fehl. Azure kann die Daten nicht an das andere VNet weiterleiten, da die Zieladresse Teil des sendenden VNet ist.

Für VPN-Gateways ist ein bestimmtes Subnetz erforderlich, ein sogenanntes Gatewaysubnetz. Alle Gatewaysubnetze müssen den Namen „GatewaySubnet“ haben, damit sie einwandfrei funktionieren. Achten Sie darauf, Ihrem Gatewaysubnetz keinen anderen Namen zu geben, und stellen Sie dem Gatewaysubnetz keine virtuellen Computer o. ä. bereit. Weitere Informationen finden Sie unter [Gatewaysubnetze](vpn-gateway-about-vpn-gateway-settings.md#gwsub).

#### <a name="local"></a>Informationen zu Gateways des lokalen Netzwerks

Mit dem Gateway des lokalen Netzwerks ist normalerweise Ihr lokaler Standort gemeint. Beim klassischen Bereitstellungsmodell wird das Gateway des lokalen Netzwerks als „Lokaler Netwerkstandort“ bezeichnet. Wenn Sie ein Gateway des lokalen Netzwerks konfigurieren, geben Sie ihm einen Namen, stellen die öffentliche IP-Adresse des lokalen VPN-Geräts bereit und geben die Adresspräfixe an, die sich am lokalen Standort befinden. Azure sucht unter den Zieladresspräfixen nach Netzwerkdatenverkehr, sieht in der Konfiguration nach, die Sie für das Gateway des lokalen Netzwerks angegeben haben, und leitet die Pakete entsprechend weiter. Sie können die Adresspräfixe bei Bedarf ändern. Weitere Informationen finden Sie unter [Gateways des lokalen Netzwerks](vpn-gateway-about-vpn-gateway-settings.md#lng).

#### <a name="gwtype"></a>Informationen zu Gatewaytypen

Das Auswählen des richtigen Gateways für Ihre Topologie ist wichtig. Wenn Sie den falschen Typ auswählen, funktioniert Ihr Gateway nicht ordnungsgemäß. Mit dem Gatewaytyp wird angegeben, wie die Verbindung für das Gateway selbst hergestellt wird. Es ist eine erforderliche Konfigurationseinstellung für das Resource Manager-Bereitstellungsmodell.

VPN-Gatewaytypen:

* VPN
* ExpressRoute

#### <a name="connectiontype"></a>Informationen zu Verbindungstypen

Für jede Konfiguration ist ein bestimmter Verbindungstyp erforderlich. Verbindungstypen:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

#### <a name="vpntype"></a>Informationen zu VPN-Typen

Für jede Konfiguration ist ein bestimmter VPN-Typ erforderlich. Wenn Sie zwei Konfigurationen kombinieren, z.B. das Erstellen einer Site-to-Site-Verbindung und einer Point-to-Site-Verbindung mit demselben VNET, müssen Sie einen VPN-Typ verwenden, mit dem beide Verbindungsanforderungen erfüllt werden.

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Die folgenden Tabellen zeigen VPN-Typen mit der jeweiligen Zuordnung zu einer Verbindungskonfiguration. Stellen Sie sicher, dass der VPN-Typ für Ihr Gateway mit der Konfiguration übereinstimmt, die Sie erstellen möchten. 

[!INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)]

### <a name="devices"></a>VPN-Geräte für Standort-zu-Standort-Verbindungen

Zum Konfigurieren einer Standort-zu-Standort-Verbindung benötigen Sie unabhängig vom Bereitstellungsmodell die folgenden Elemente:

* Ein mit Azure-VPN-Gateways kompatibles VPN-Gerät
* Eine öffentlich zugängliche IPv4-IP-Adresse, die sich nicht hinter einem NAT-Gerät befindet

Sie benötigen Erfahrungen beim Konfigurieren Ihres VPN-Geräts, oder Sie müssen eine Person kennen, die das Gerät für Sie konfigurieren kann.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="forcedtunnel"></a>Erwägen der Weiterleitung per Tunnelerzwingung

Für die meisten Konfigurationen können Sie die Tunnelerzwingung konfigurieren. Über die Tunnelerzwingung können Sie die Umleitung des gesamten Internetdatenverkehrs an Ihren lokalen Standort „erzwingen“. Sie verwenden dazu einen Standort-zu-Standort-VPN-Tunnel für die Kontrolle und Überwachung. Dies ist eine wichtige Sicherheitsvoraussetzung der IT-Richtlinien für die meisten Unternehmen. 

Ohne die Tunnelerzwingung wird der Internetdatenverkehr Ihrer virtuellen Computer in Azure immer direkt von der Azure-Netzwerkinfrastruktur an das Internet geleitet, ohne dass Sie die Möglichkeit haben, diesen zu überprüfen oder zu überwachen. Nicht autorisierter Zugriff auf das Internet kann potenziell zur Offenlegung von Informationen oder anderen Arten von Sicherheitsverletzungen führen.

Eine Verbindung mit erzwungenem Tunneling kann in beiden Bereitstellungsmodellen und mit unterschiedlichen Tools konfiguriert werden. Weitere Informationen finden Sie unter [Konfigurieren der Tunnelerzwingung](vpn-gateway-forced-tunneling-rm.md).

**Diagramm zur Tunnelerzwingung**

![Azure VPN Gateway – Diagramm von erzwungenem Tunneling](./media/vpn-gateway-plan-design/forced-tunneling-diagram.png)

## <a name="next-steps"></a>Nächste Schritte

In den Artikeln [Häufig gestellte Fragen zum VPN-Gateway](vpn-gateway-vpn-faq.md) und [Informationen zu VPN Gateway](vpn-gateway-about-vpngateways.md) finden Sie weitere Informationen zur Unterstützung bei Ihrem Entwurf.

Weitere Informationen zu bestimmten Gatewayeinstellungen finden Sie unter [Informationen zu VPN Gateway-Einstellungen](vpn-gateway-about-vpn-gateway-settings.md).
