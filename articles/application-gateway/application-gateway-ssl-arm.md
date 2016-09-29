<properties
   pageTitle="Konfigurieren eines Anwendungsgateways für SSL-Auslagerung mit Azure Resource Manager | Microsoft Azure"
   description="Diese Seite enthält Anweisungen zum Erstellen eines Anwendungsgateways mit SSL-Auslagerung mit dem Azure-Ressourcen-Manager."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/09/2016"
   ms.author="gwallace"/>

# Konfigurieren eines Anwendungsgateways für die SSL-Auslagerung mit Azure Resource Manager

> [AZURE.SELECTOR]
-[Azure Portal](application-gateway-ssl-portal.md)
-[Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
-[Azure Classic PowerShell](application-gateway-ssl.md)

 Azure Application Gateway kann so konfiguriert werden, dass damit die Secure Sockets Layer-Sitzung (SSL) auf dem Gateway beendet wird. Auf diese Weise wird die aufwändige SSL-Entschlüsselung in der Webfarm vermieden. Die SSL-Auslagerung vereinfacht zudem die Einrichtung und Verwaltung der Webanwendung auf dem Front-End-Server.


## Voraussetzungen

1. Installieren Sie mit dem Webplattform-Installer die aktuelle Version der Azure PowerShell-Cmdlets. Sie können die neueste Version aus dem Abschnitt **Windows PowerShell** der [Downloadseite](https://azure.microsoft.com/downloads/) herunterladen und installieren.
2. Sie erstellen ein virtuelles Netzwerk und ein Subnetz für das Anwendungsgateway. Stellen Sie sicher, dass keine virtuellen Maschinen oder Cloudbereitstellungen das Subnetz verwenden. Das Anwendungsgateway muss sich allein im Subnetz eines virtuellen Netzwerks befinden.
3. Die Server, die Sie für die Verwendung des Anwendungsgateways konfigurieren, müssen vorhanden sein oder Endpunkte aufweisen, die im virtuellen Netzwerk erstellt wurden oder denen eine öffentliche IP-Adresse/VIP zugewiesen wurde.

## Was ist zum Erstellen eines Anwendungsgateways erforderlich?


- **Back-End-Serverpool:** Die Liste der IP-Adressen der Back-End-Server. Die aufgelisteten IP-Adressen sollten entweder dem Subnetz des virtuellen Netzwerks angehören oder eine öffentliche IP-Adresse/VIP sein.
- **Einstellungen für den Back-End-Serverpool:** Jeder Pool weist Einstellungen wie Port, Protokoll und cookiebasierte Affinität auf. Diese Einstellungen sind an einen Pool gebunden und gelten für alle Server innerhalb des Pools.
- **Front-End-Port:** Dieser Port ist der öffentliche Port, der im Application Gateway geöffnet ist. Datenverkehr erreicht diesen Port und wird dann an einen der Back-End-Server umgeleitet.
- **Listener:** Der Listener verfügt über einen Front-End-Port, ein Protokoll (Http oder Https, jeweils mit Beachtung der Groß-/Kleinschreibung) und den Namen des SSL-Zertifikats (falls SSL-Auslagerung konfiguriert wird).
- **Regel:** Mit der Regel werden der Listener und der Back-End-Serverpool gebunden, und es wird definiert, an welchen Back-End-Serverpool der Datenverkehr gesendet werden soll, wenn er einen bestimmten Listener erreicht. Derzeit wird nur die Regel *basic* unterstützt. Die Regel *basic* ist eine Round-Robin-Lastverteilung.

**Zusätzliche Konfigurationshinweise**

Für die Konfiguration von SSL-Zertifikaten sollte das Protokoll in **HttpListener** in *Https*(Groß-/Kleinschreibung beachten) geändert werden. Das Element **SslCertificate** wird **HttpListener** mit dem Variablenwert hinzugefügt, der für das SSL-Zertifikat konfiguriert wurde. Der Front-End-Port sollte auf 443 aktualisiert werden.

**So aktivieren Sie cookiebasierte Affinität** Ein Anwendungsgateway kann so konfiguriert werden, dass es sicherstellt, dass die Anforderung von einer Clientsitzung immer an denselben virtuellen Computer in der Webfarm weitergeleitet wird. Für dieses Szenario wird das Einfügen eines Sitzungscookies genutzt, damit das Gateway den Datenverkehr entsprechend weiterleiten kann. Legen Sie zum Aktivieren der cookiebasierten Affinität **CookieBasedAffinity** im **BackendHttpSettings**-Element auf *Enabled* fest.


## Erstellen eines Anwendungsgateways

Der Unterschied zwischen dem klassischen Azure-Bereitstellungsmodell und Azure Resource Manager besteht in der Reihenfolge, in der Sie ein Anwendungsgateway und die Elemente erstellen, die konfiguriert werden müssen.

Bei Resource Manager werden alle Komponenten eines Anwendungsgateways einzeln konfiguriert und dann zusammengefügt, um eine Anwendungsgatewayressource zu erstellen.


Hier sind die erforderlichen Schritte zum Erstellen eines Anwendungsgateways angegeben:

1. Erstellen einer Ressourcengruppe für den Resource Manager
2. Erstellen eines virtuelles Netzwerks, Subnetzes und einer öffentlichen IP-Adresse für das Anwendungsgateway
3. Erstellen eines Konfigurationsobjekts für das Anwendungsgateway
4. Erstellen einer Anwendungsgatewayressource


## Erstellen einer Ressourcengruppe für den Resource Manager

Stellen Sie sicher, dass Sie in den PowerShell-Modus wechseln, um die Azure-Ressourcen-Manager-Cmdlets zu verwenden. Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit Resource Manager](../powershell-azure-resource-manager.md).

### Schritt 1

	Login-AzureRmAccount

### Schritt 2

Überprüfen Sie die Abonnements für das Konto.

	Get-AzureRmSubscription

Sie werden zur Authentifizierung mit Ihren Anmeldeinformationen aufgefordert.<BR>

### Schritt 3

Wählen Sie aus, welches Azure-Abonnement Sie verwenden möchten.<BR>


	Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Schritt 4

Erstellen Sie eine Ressourcengruppe. (Überspringen Sie diesen Schritt, wenn Sie eine vorhandene Ressourcengruppe verwenden.)

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Der Azure Resource Manager erfordert, dass alle Ressourcengruppen einen Speicherort angeben. Diese Einstellung wird als Standardspeicherort für Ressourcen in dieser Ressourcengruppe verwendet. Stellen Sie sicher, dass alle Befehle, mit denen ein Anwendungsgateway erstellt wird, die gleiche Ressourcengruppe verwenden.

Im obigen Beispiel haben wir eine Ressourcengruppe namens „appgw-RG“ mit dem Standort „USA, Westen“ erstellt.

## Erstellen eines virtuellen Netzwerks und Subnetzes für das Anwendungsgateway

Das folgende Beispiel zeigt, wie Sie mit dem Ressourcen-Manager ein virtuelles Netzwerk erstellen:

### Schritt 1

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

In diesem Beispiel wird der Adressbereich 10.0.0.0/24 einer Subnetzvariablen zugewiesen, die zum Erstellen eines virtuellen Netzwerks verwendet wird.

### Schritt 2

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

In diesem Beispiel wird ein virtuelles Netzwerk mit dem Namen „appgwvnet“ in der Ressourcengruppe „appgw-rg“ für die Region „USA, Westen“ mit dem Präfix 10.0.0.0/16 und dem Subnetz 10.0.0.0/24 erstellt.

### Schritt 3

	$subnet = $vnet.Subnets[0]

In diesem Beispiel wird der Variablen „$subnet“ das Subnetzobjekt für die nächsten Schritte zugewiesen.

## Erstellen der öffentlichen IP-Adresse für die Front-End-Konfiguration

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

In diesem Beispiel wird die öffentliche IP-Ressource „publicIP01“ in der Ressourcengruppe „appgw-rg“ für die Region „USA, Westen“ erstellt.


## Erstellen eines Konfigurationsobjekts für das Anwendungsgateway

### Schritt 1

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

In diesem Beispiel wird eine IP-Konfiguration für das Anwendungsgateway mit dem Namen „gatewayIP01“ erstellt. Beim Starten des Anwendungsgateways wird eine IP-Adresse aus dem konfigurierten Subnetz ausgewählt, und der Netzwerkdatenverkehr wird an die IP-Adressen im Back-End-IP-Pool weitergeleitet. Beachten Sie, dass jede Instanz eine eigene IP-Adresse benötigt.

### Schritt 2

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

In diesem Beispiel wird der Back-End-IP-Adresspool „pool01“ mit den IP-Adressen 134.170.185.46, 134.170.188.221 und 134.170.185.50 erstellt. Diese Werte sind die IP-Adressen, die den Netzwerkdatenverkehr vom Front-End-IP-Endpunkt empfangen. Ersetzen Sie die IP-Adressen aus dem obigen Beispiel durch die IP-Adressen der Endpunkte Ihrer Webanwendung.

### Schritt 3

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

In diesem Beispiel wird die Anwendungsgatewayeinstellung „poolsetting01“ für den Lastenausgleich des Netzwerkdatenverkehrs im Back-End-Pool konfiguriert.

### Schritt 4

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

In diesem Beispiel wird der Front-End-IP-Port mit dem Namen „frontendport01“ für den öffentlichen IP-Adressendpunkt konfiguriert.

### Schritt 5

	$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

In diesem Beispiel wird das Zertifikat für SSL-Verbindungen konfiguriert. Das Zertifikat muss im PFX-Format vorliegen, und das Kennwort muss zwischen 4 und 12 Zeichen umfassen.

### Schritt 6

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

In diesem Beispiel wird die Front-End-IP-Adresskonfiguration mit dem Namen „fipconfig01“ erstellt, und die öffentliche IP-Adresse wird der Front-End-IP-Adresskonfiguration zugeordnet.

### Schritt 7

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


Dieses Beispiel dient zum Erstellen des Listeners „listener01“ und zum Zuordnen des Front-End-Ports zur Front-End-IP-Adresskonfiguration und zum Zertifikat.

### Schritt 8

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

In diesem Beispiel wird die Load Balancer-Routingregel mit dem Namen „rule01“ erstellt, mit der das Load Balancer-Verhalten konfiguriert wird.

### Schritt 9

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

In diesem Beispiel wird die Instanzgröße des Anwendungsgateways konfiguriert.

>[AZURE.NOTE]  Der Standardwert für *InstanceCount* ist 2, der Maximalwert ist 10. Der Standardwert für *GatewaySize* ist "Medium". Sie können zwischen „Standard\_Small“, „Standard\_Medium“ und „Standard\_Large“ wählen.

## Erstellen eines Anwendungsgateways mit dem New-AzureApplicationGateway-Cmdlet

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

In diesem Beispiel wird ein Anwendungsgateway mit allen Konfigurationselementen aus den vorherigen Schritten erstellt. Im Beispiel heißt das Anwendungsgateway „appgwtest“.

## Nächste Schritte

Wenn Sie ein Anwendungsgateway für die Verwendung mit einem internen Load Balancer (ILB) konfigurieren möchten, ist es ratsam, den Abschnitt [Erstellen eines Application Gateways mit einem internen Lastenausgleich (ILB)](application-gateway-ilb.md) zu lesen.

Weitere Informationen zu Lastenausgleichsoptionen im Allgemeinen finden Sie unter:

- [Azure-Lastenausgleich](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0921_2016-->