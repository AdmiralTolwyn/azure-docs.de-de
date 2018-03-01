# [ExpressRoute-Dokumentation](index.md)

# Übersicht
## [Was ist ExpressRoute?](expressroute-introduction.md)
## [ExpressRoute – FAQ](expressroute-faqs.md)
## [Konnektivitätsmodelle](expressroute-connectivity-models.md)
## [Verbindungen und Routingdomänen](expressroute-circuit-peerings.md)
## [Standorte und Partner](expressroute-locations.md)
### [Anbieter nach Standort](expressroute-locations-providers.md)
### [Standorte nach Anbieter](expressroute-locations.md)
## [Gateways für virtuelle Netzwerke für ExpressRoute](expressroute-about-virtual-network-gateways.md)

# Erste Schritte
## [Voraussetzungen](expressroute-prerequisites.md)
## [Workflows](expressroute-workflows.md)
## [Routinganforderungen](expressroute-routing.md)
## [QoS-Anforderungen](expressroute-qos.md)
## [Informationen zum Umstellen von Verbindungen vom klassischen Modell auf das Resource Manager-Modell](expressroute-move.md)

# Anleitung
## Erstellen und Ändern einer Verbindung
### [Azure-Portal](expressroute-howto-circuit-portal-resource-manager.md)
### [Azure PowerShell](expressroute-howto-circuit-arm.md)
### [Azure-CLI](howto-circuit-cli.md)
## Erstellen und Ändern einer Peeringkonfiguration
### [Azure-Portal](expressroute-howto-routing-portal-resource-manager.md)
### [Azure PowerShell](expressroute-howto-routing-arm.md)
### [Azure-CLI](howto-routing-cli.md)
## Verknüpfen eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung
### [Azure-Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
### [Azure PowerShell](expressroute-howto-linkvnet-arm.md)
### [Azure-CLI](howto-linkvnet-cli.md)
## [Konfigurieren eines Site-to-Site-VPNs über Microsoft-Peering](site-to-site-vpn-over-microsoft-peering.md)
## Konfigurieren eines Gateways für ein virtuelles Netzwerk für ExpressRoute
### [Azure portal](expressroute-howto-add-gateway-portal-resource-manager.md)
### [Azure PowerShell](expressroute-howto-add-gateway-resource-manager.md)
## [Konfigurieren von parallel bestehenden ExpressRoute- und Site-to-Site-Verbindungen](expressroute-howto-coexist-resource-manager.md)
## Konfigurieren von Routenfiltern für das Microsoft-Peering
### [Azure portal](how-to-routefilter-portal.md)
### [Azure PowerShell](how-to-routefilter-powershell.md)
### [Azure-CLI](how-to-routefilter-cli.md)
## [Umstellen von öffentlichem Peering auf Microsoft-Peering](how-to-move-peering.md)
## [Umstellen einer Verbindung vom klassischen Modell auf das Resource Manager-Modell](expressroute-howto-move-arm.md)
## [Migrieren von zugeordneten virtuellen Netzwerken vom klassischen Modell zu Resource Manager](expressroute-migration-classic-resource-manager.md)
## Konfigurieren eines Routers für ExpressRoute
### [Konfigurieren eines Routers](expressroute-config-samples-routing.md)
### [Beispiele für die Routerkonfiguration für NAT](expressroute-config-samples-nat.md)
## [Konfigurieren des Netzwerkleistungsmonitors für ExpressRoute](how-to-npm.md)
## Artikel zum klassischen Bereitstellungsmodell
### [Ändern einer Verbindung](expressroute-howto-circuit-classic.md)
### [Erstellen und Ändern einer Peeringkonfiguration](expressroute-howto-routing-classic.md)
### [Verknüpfen eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung](expressroute-howto-linkvnet-classic.md)
### [Konfigurieren von parallel bestehenden ExpressRoute- und S2S-Verbindungen](expressroute-howto-coexist-classic.md)
### [Hinzufügen eines Gateways zu einem VNET](expressroute-howto-add-gateway-classic.md)

## Bewährte Methoden
### [Bewährte Methoden für Netzwerksicherheit und Clouddienste](../best-practices-network-security.md)
### [Optimieren des Routings](expressroute-optimize-routing.md)
### [Asymmetrisches Routing](expressroute-asymmetric-routing.md)
### [NAT für ExpressRoute](expressroute-nat.md)

## Problembehandlung
### [Überprüfen der ExpressRoute-Konnektivität](expressroute-troubleshooting-expressroute-overview.md)
### [Beheben von Leistungsproblemen im Netzwerk](expressroute-troubleshooting-network-performance.md)
### [Zurücksetzen einer fehlerhaften Verbindung](reset-circuit.md)
### [Abrufen von ARP-Tabellen](expressroute-troubleshooting-arp-resource-manager.md)
### [Abrufen von ARP-Tabellen (klassisch)](expressroute-troubleshooting-arp-classic.md)

# Verweis
## [Azure PowerShell](/powershell/module/azurerm.network/?view=azurermps-4.0.0#expressroute)
## [Azure-CLI](/cli/azure/network/express-route)
## [REST](https://msdn.microsoft.com/library/azure/mt586720)
## [REST (klassisch)](https://msdn.microsoft.com/library/azure/dn606310)

# Verwandte Themen
## [Virtual Network](/azure/virtual-network/)
## [VPN Gateway](/azure/vpn-gateway/)
## [Virtuelle Computer](/azure/virtual-machines/)
## [Load Balancer](/azure/load-balancer/)
## [Traffic Manager](/azure/traffic-manager/)

# angeben
## [Azure-Roadmap](https://azure.microsoft.com/roadmap/?category=networking)
## [Fallstudien](https://customers.microsoft.com/Pages/advancedsearch.aspx?mrmcproducts=More%20Products)
## [Netzwerkblog](https://azure.microsoft.com/blog/topics/networking/)
## [Preise](https://azure.microsoft.com/pricing/details/expressroute/)
## [Preisrechner](https://azure.microsoft.com/pricing/calculator/)
## [Dienstupdates](https://azure.microsoft.com/updates/?product=expressroute)
## [SLA](https://azure.microsoft.com/support/legal/sla/)
## [Abonnements und Diensteinschränkungen](../azure-subscription-service-limits.md?toc=%2fazure%2fexpressroute%2ftoc.json)
## [ExpressRoute für Cloudlösungsanbieter (Cloud Solution Providers, CSPs)](expressroute-for-cloud-solution-providers.md)
## [Videos](https://azure.microsoft.com/documentation/videos/index/?services=expressroute)
### [Herstellen einer Verbindung mit einem Gateway für ein virtuelles Netzwerk](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit/)
### [Erstellen eines virtuellen Netzwerks für ExpressRoute](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-virtual-network/)
### [Erstellen eines Gateways für ein virtuelles Netzwerk für ExpressRoute](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network/)
### [Erstellen einer ExpressRoute-Verbindung](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit/)
### [Erweitern der Netzwerkinfrastruktur zur Optimierung der Konnektivität](https://go.microsoft.com/fwlink/p/?LinkId=615124)
### [Einrichten von privatem Peering für Verbindungen](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit/)
### [Partnerschaften für hybride Umgebungen: Umsetzen lokaler Szenarien](https://go.microsoft.com/fwlink/p/?LinkId=615125)
### [Einrichten von Microsoft-Peering für Verbindungen](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit/)
### [Einrichten von öffentlichem Peering für Verbindungen](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit/)
