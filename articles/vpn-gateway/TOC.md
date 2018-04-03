# [VPN Gateway-Dokumentation](index.md)

# Übersicht
## [Informationen zu VPN Gateway](vpn-gateway-about-vpngateways.md)
## [Häufig gestellte Fragen zum VPN-Gateway](vpn-gateway-vpn-faq.md)
## [Abonnements und Diensteinschränkungen](../azure-subscription-service-limits.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)

# Erste Schritte
## [Erstellen eines routenbasierten VPN-Gateways – Azure-Portal](create-routebased-vpn-gateway-portal.md)
## [Erstellen eines routenbasierten VPN-Gateways – PowerShell](create-routebased-vpn-gateway-powershell.md)

# Konzepte
## [Planung und Entwurf für VPN Gateway](vpn-gateway-plan-design.md)
## [Informationen zu VPN Gateway-Einstellungen](vpn-gateway-about-vpn-gateway-settings.md)
## [Informationen zu VPN-Geräten](vpn-gateway-about-vpn-devices.md)
## [Informationen zu kryptografischen Anforderungen](vpn-gateway-about-compliance-crypto.md)
## [Informationen zu BGP und VPN Gateway](vpn-gateway-bgp-overview.md)
## [Informationen zu hoch verfügbaren Verbindungen](vpn-gateway-highlyavailable.md)
## [Informationen zu Point-to-Site-Verbindungen](point-to-site-about.md)

# Anleitung
## Konfigurieren von Site-to-Site-Verbindungen
### [Azure-Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
### [Azure-CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
### [Azure-Portal (klassisch)](vpn-gateway-howto-site-to-site-classic-portal.md)

## [Herunterladen von Konfigurationsskripts für ein VPN-Gerät](vpn-gateway-download-vpndevicescript.md)

## Konfigurieren von Point-to-Site-Verbindungen – native Azure-Zertifikatauthentifizierung
### Konfigurieren einer P2S-VPN-Verbindung
#### [Azure-Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
#### [Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
#### [Azure-Portal (klassisch)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
### Generieren selbstsignierter Zertifikate
#### [Azure PowerShell](vpn-gateway-certificates-point-to-site.md)
#### [Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
### [Erstellen und Installieren von Konfigurationsdateien für VPN-Clients](point-to-site-vpn-client-configuration-azure-cert.md)
### [Installieren von Clientzertifikaten](point-to-site-how-to-vpn-client-install-azure-cert.md)

## Konfigurieren von Point-to-Site-Verbindungen – RADIUS-Authentifizierung
### Konfigurieren einer P2S-VPN-Verbindung
#### [Azure PowerShell](point-to-site-how-to-radius-ps.md)
### [Erstellen und Installieren von Konfigurationsdateien für VPN-Clients](point-to-site-vpn-client-configuration-radius.md)
### [Integrieren der P2S-VPN-RADIUS-Authentifizierung mit NPS-Server](vpn-gateway-radiuis-mfa-nsp.md)

## Konfigurieren von VNet-zu-VNet-Verbindungen
### [Azure-Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
### [Azure-CLI](vpn-gateway-howto-vnet-vnet-cli.md)
### [Azure-Portal (klassisch)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
## Konfigurieren einer VNet-zu-VNet-Verbindung zwischen Bereitstellungsmodellen
### [Azure-Portal](vpn-gateway-connect-different-deployment-models-portal.md)
### [Azure PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
## Konfigurieren von parallel bestehenden Site-to-Site- und ExpressRoute-Verbindungen
### [Azure PowerShell](../expressroute/expressroute-howto-coexist-resource-manager.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)
## Mehrere Site-to-Site-Verbindungen konfigurieren
### [Azure-Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
### [Azure PowerShell (klassisch)](vpn-gateway-multi-site.md)
## Verbinden mehrerer richtlinienbasierter VPN-Geräte
### [Azure PowerShell](vpn-gateway-connect-multiple-policybased-rm-ps.md)
## Konfigurieren von IPsec-/IKE-Richtlinien für Verbindungen
### [Azure PowerShell](vpn-gateway-ipsecikepolicy-rm-powershell.md)
## Konfigurieren hochverfügbarer Aktiv/Aktiv-Verbindungen
### [Azure PowerShell](vpn-gateway-activeactive-rm-powershell.md)
## Konfigurieren von BGP für ein VPN-Gateway
### [Azure PowerShell](vpn-gateway-bgp-resource-manager-ps.md)
### [Azure-CLI](bgp-how-to-cli.md)
## Konfigurieren der Tunnelerzwingung
### [Azure PowerShell](vpn-gateway-forced-tunneling-rm.md)
### [Azure PowerShell (klassisch)](vpn-gateway-about-forced-tunneling.md)
## Ändern der Einstellungen des lokalen Netzwerkgateways
### [Azure-Portal](vpn-gateway-modify-local-network-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-modify-local-network-gateway.md)
### [Azure-CLI](vpn-gateway-modify-local-network-gateway-cli.md)
## [Überprüfen einer VPN Gateway-Verbindung](vpn-gateway-verify-connection-resource-manager.md)
## [Zurücksetzen einer VPN Gateway-Instanz](vpn-gateway-resetgw-classic.md)
## Löschen eines VPN-Gateways
### [Azure-Portal](vpn-gateway-delete-vnet-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
### [Azure PowerShell (klassisch)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
## [Konfigurieren einer VPN Gateway-Instanz (klassisch)](vpn-gateway-configure-vpn-gateway-mp.md)
## [Gateway-SKUs (alt)](vpn-gateway-about-skus-legacy.md)
## Konfigurieren von VPN-Geräten von Drittanbietern
### [Übersicht und Azure-Konfiguration](vpn-gateway-3rdparty-device-config-overview.md)
### [Beispiel: Cisco ASA-Gerät (IKEv2/kein BGP)](vpn-gateway-3rdparty-device-config-cisco-asa.md)
## [Migrieren vom klassischen Modell zum Resource Manager-Modell](vpn-gateway-classic-resource-manager-migration.md)
## [Problembehandlung](vpn-gateway-troubleshoot.md)
### [Überprüfen des VPN-Durchsatzes an ein VNET](vpn-gateway-validate-throughput-to-vnet.md)
### [Von der Community vorgeschlagene VPN- oder Firewallgeräteeinstellungen](vpn-gateway-third-party-settings.md)
### [Point-to-Site-Verbindungsprobleme](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
#### [Point-to-Site-Verbindungsprobleme – Mac OS X-VPN-Client](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
### [Site-to-Site-Verbindungsprobleme](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md)
#### [Site-to-Site-Verbindung wird zeitweilig getrennt](vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently.md)
### [Konfigurieren und Überprüfen von VNET- oder VPN-Verbindungen](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections)

# Verweis
## [Azure PowerShell](/powershell/module/azurerm.network/?view=azurermps-4.0.0#vpn)
## [Azure PowerShell (klassisch)](/powershell/module/azure/?view=azuresmps-3.7.0#networking)
## [REST](/rest/api/network/virtualnetworkgateways)
## [REST (klassisch)](https://msdn.microsoft.com/library/jj154113)
## [Azure-CLI](/cli/azure/network/vnet-gateway)

# Verwandte Themen
## [Virtual Network](/azure/virtual-network/)
## [Application Gateway](/azure/application-gateway/)
## [Azure DNS](/azure/dns/)
## [Traffic Manager](/azure/traffic-manager/)
## [Load Balancer](/azure/load-balancer/)
## [ExpressRoute](/azure/expressroute/)

# angeben
## [Azure-Roadmap](https://azure.microsoft.com/roadmap/?category=networking)
## [Blog](https://azure.microsoft.com/blog/topics/networking)
## [Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Preise](https://azure.microsoft.com/pricing/details/vpn-gateway)
## [Preisrechner](https://azure.microsoft.com/pricing/calculator/)
## [SLA](https://azure.microsoft.com/support/legal/sla)
## [Videos](https://azure.microsoft.com/documentation/videos/index/?services=vpn-gateway)
