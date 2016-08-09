<properties 
   pageTitle="Informationen zu VPN-Geräten für VPN-Gatewayverbindungen zwischen Standorten in virtuellen Azure-Netzwerken | Microsoft Azure"
   description="Erfahren Sie mehr über VPN-Geräte und IPsec-Parameter für VPN-Gatewayverbindungen zwischen Standorten. Standort-zu-Standort-Verbindungen können für Hybridkonfigurationen verwendet werden. Dieser Artikel enthält Links zu Konfigurationsanweisungen und Vorlagen für VPN-Gatewaygeräte."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
  tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/28/2016"
   ms.author="cherylmc" />

# Informationen zu VPN-Geräten für VPN-Gatewayverbindungen zwischen Standorten

Ein VPN-Gerät ist erforderlich, um eine VPN-Verbindung zwischen Standorten (Site-to-Site, S2S) zu konfigurieren. Standort-zu-Standort-Verbindungen können zum Erstellen einer Hybridlösung verwendet werden. Sie können sie auch zum Herstellen einer sicheren Verbindung zwischen Ihrem lokalen Netzwerk und dem virtuellen Netzwerk verwenden. Dieser Artikel beschreibt kompatible VPN-Geräte und Konfigurationsparameter. Beachten Sie, dass beim Konfigurieren einer Standort-zu-Standort-Verbindung eine öffentliche IPv4-IP-Adresse für das VPN-Gerät erforderlich ist.

Wenn Ihr Gerät in der Tabelle mit den überprüften VPN-Geräten nicht aufgeführt ist, sehen Sie in diesem Artikel im Abschnitt „Nicht überprüfte VPN-Geräte“ nach. Es ist möglich, dass das Gerät dennoch mit Azure verwendet werden kann. Wenden Sie sich für Unterstützung im Hinblick auf das VPN-Gerät an den Gerätehersteller.

**Was Sie beim Anzeigen der Tabellen beachten sollten:**

- Die Terminologie für statisches und dynamisches Routing wurde geändert. Wahrscheinlich finden Sie beide Begriffe. Die Funktionalität hat sich nicht geändert, nur die Namen.
	- Statisches Routing = Richtlinienbasiert
	- Dynamisches Routing = Routenbasiert
- Die Spezifikationen für Hochleistungs-VPN-Gateways und routenbasierte VPN-Gateways bleiben dieselben, sofern nicht anders angegeben. Beispielsweise sind die überprüften VPN-Geräte, die mit den routenbasierten VPN-Gateways kompatibel sind, auch mit dem neuen Azure-Hochleistungs-VPN-Gateway kompatibel.


## Überprüfte VPN-Geräte 

Wir haben in Zusammenarbeit mit Geräteherstellern eine Reihe von VPN-Standardgeräten getestet. Alle Geräte der in der unten stehenden Liste aufgeführten Gerätefamilien sollten mit Azure-VPN-Gateways kompatibel sein. Lesen Sie den Artikel [VPN-Gateways](vpn-gateway-about-vpngateways.md), um den Gatewaytyp zu überprüfen, den Sie zum Konfigurieren der gewünschten Lösung erstellen müssen.

Hilfreiche Informationen zur Konfiguration des VPN-Geräts finden Sie unter den Links für die entsprechende Gerätefamilie.



| **Hersteller** | **Gerätefamilie** | **Betriebssystemversion (Min.)** | **Richtlinienbasiert** | **Routenbasiert** |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Allied Telesis | VPN-Router der AR-Serie | 2\.9.2 | In Kürze verfügbar | Nicht kompatibel |
| Barracuda Networks, Inc. | Barracuda NextGen Firewall F-Serie | Richtlinienbasiert: 5.4.3, weiterleitungsbasiert: 6.2.0 | [Konfigurationsanweisungen](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) | [Konfigurationsanweisungen](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW) |
| Barracuda Networks, Inc. | Barracuda NextGen Firewall X-Serie | Barracuda Firewall 6.5 | [Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | Nicht kompatibel |
| Brocade | 5400 Vyatta vRouter | Virtual Router 6.6R3 GA | [Konfigurationsanweisungen](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) | Nicht kompatibel |
| Check Point | Sicherheitsgateway | R75.40, R75.40VS | [Konfigurationsanweisungen](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) | [Konfigurationsanweisungen](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco | ASA | 8\.3 | [Cisco-Beispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA) | Nicht kompatibel |
| Cisco | ASR | IOS 15.1 (richtlinienbasiert), IOS 15.2 (routenbasiert) | [Cisco-Beispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) | [Cisco-Beispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |
| Cisco | ISR | IOS 15.0 (richtlinienbasiert), IOS 15.1 (routenbasiert*) | [Cisco-Beispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) | [Cisco-Beispiele*](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |
| Citrix | CloudBridge-MPX-Gerät oder virtuelles VPX-Gerät | N/V | [Integrationsanweisungen](https://www.citrix.com/welcome.html?resource=%2Fdownloads%2Fcloudbridge%2Fbetas-and-tech-previews%2Fcloudbridge-azure-integration) | Nicht kompatibel |
| Dell SonicWALL | TZ-Serie, NSA-Serie, SuperMassive-Serie, E-Class-NSA-Serie | SonicOS 5.8.x, [SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850), [SonicOS 6.x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646) | [Anleitung: SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Anleitung: SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) | [Anleitung: SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Anleitung: SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |
| F5 | BIG-IP-Serie | N/V | [Konfigurationsanweisungen](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) | Nicht kompatibel |
| Fortinet | FortiGate | FortiOS 5.2.7 | [Konfigurationsanweisungen](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure) | [Konfigurationsanweisungen](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure) |
| Internet Initiative Japan (IIJ) | SEIL-Serie | SEIL / X 4.60, SEIL/B1 4.60, SEIL/x86 3.20 | [Konfigurationsanweisungen](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) | Nicht kompatibel |
| Juniper | SRX | JunOS 10.2 (richtlinienbasiert), JunOS 11.4 (routenbasiert) | [Juniper-Beispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) | [Juniper-Beispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |
| Juniper | J-Serie | JunOS 10.4r9 (richtlinienbasiert), JunOS 11.4 (routenbasiert) | [Juniper-Beispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) | [Juniper-Beispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |
| Juniper | ISG | ScreenOS 6.3 (richtlinienbasiert und routenbasiert) | [Juniper-Beispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) | [Juniper-Beispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |
| Juniper | SSG | ScreenOS 6.2 (richtlinienbasiert und routenbasiert) | [Juniper-Beispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) | [Juniper-Beispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |
| Microsoft | Routing- und RAS-Dienst | Windows Server 2012 | Nicht kompatibel | [Microsoft-Beispiele](http://go.microsoft.com/fwlink/p/?LinkId=717761) |
| Open Systems AG | Mission Control Security Gateway | N/V | [Installationshandbuch](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) | [Installationshandbuch](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan | Openswan | 2\.6.32 | (In Kürze verfügbar) | Nicht kompatibel |
| Palo Alto Networks | Alle Geräte mit PAN-OS 5.0 | PAN-OS 6.1.5 oder höher (richtlinienbasiert), PAN-OS 7.0.5 oder höher (routenbasiert) | [Konfigurationsanweisungen](https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065) | [Konfigurationsanweisungen](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340) |
| Watchguard | Alle | Fireware XTM v11.x | [Konfigurationsanweisungen](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/) | Nicht kompatibel |

(*) Router der Serie ISR 7200 unterstützen nur richtlinienbasierte VPNs.

## Nicht überprüfte VPN-Geräte

Auch wenn Ihr Gerät in der Tabelle überprüfter VPN-Geräte (siehe oben) nicht aufgeführt wird, kann es möglicherweise für einer Standort-zu-Standort-Verbindung eingesetzt werden. Stellen Sie sicher, dass Ihr VPN-Gerät die Mindestanforderungen erfüllt, die im Abschnitt mit den Gatewayanforderungen im Artikel [Informationen zu VPN-Gateways](vpn-gateway-about-vpngateways.md#gateway-requirements) aufgeführt sind. Geräte, die die Mindestanforderungen erfüllen, sollten problemlos mit VPN-Gateways verwendet werden können. Zusätzliche Unterstützung und Konfigurationsanweisungen erhalten Sie vom Gerätehersteller.


## Bearbeiten der Gerätekonfigurationsvorlagen

Nachdem Sie die bereitgestellte Konfigurationsvorlage für das VPN-Gerät heruntergeladen haben, müssen Sie einige der Werte entsprechend den Einstellungen Ihrer Umgebung ersetzen.

**So bearbeiten Sie eine Vorlage:**

1. Öffnen Sie die Vorlage im Editor.
1. Suchen und ersetzen Sie alle <*text*>-Zeichenfolgen mit den Werten, die für Ihre Umgebung gelten. Schließen Sie dabei unbedingt „<“ und „>“ mit ein. Wenn ein Name angegeben ist, sollte der ausgewählte Name eindeutig sein. Wenn ein Befehl nicht funktioniert, lesen Sie zunächst die Dokumentation des Geräteherstellers.

| **Vorlagentext** | **Ändern in** |
|----------------------------------|----------------------------------------------------------------------------------------------------------------------|
| &lt;RP\_OnPremisesNetwork&gt; | Der von Ihnen gewählte Name für dieses Objekt. Beispiel: meinLokalesNetzwerk |
| &lt;RP\_AzureNetwork&gt; | Der von Ihnen gewählte Name für dieses Objekt. Beispiel: meinAzureNetzwerk |
| &lt;RP\_AccessList&gt; | Der von Ihnen gewählte Name für dieses Objekt. Beispiel: meineAzureZugriffsliste |
| &lt;RP\_IPSecTransformSet&gt; | Der von Ihnen gewählte Name für dieses Objekt. Beispiel: meinIPSecTransformSet |
| &lt;RP\_IPSecCryptoMap&gt; | Der von Ihnen gewählte Name für dieses Objekt. Beispiel: meineIPSecKryptoMap |
| &lt;SP\_AzureNetworkIpRange&gt; | Geben Sie den Bereich an. Beispiel: 192.168.0.0 |
| &lt;SP\_AzureNetworkSubnetMask&gt; | Geben Sie die Subnetzmaske an. Beispiel: 255.255.0.0 |
| &lt;SP\_OnPremisesNetworkIpRange&gt; | Geben Sie den lokalen Bereich an. Beispiel: 10.2.1.0 |
| &lt;SP\_OnPremisesNetworkSubnetMask&gt; | Geben Sie die lokale Subnetzmaske an. Beispiel: 255.255.255.0 |
| &lt;SP\_AzureGatewayIpAddress&gt; | Diese Informationen gelten nur für Ihr virtuelles Netzwerk und befinden sich im Verwaltungsportal unter **Gateway-IP-Adresse**. |
| &lt;SP\_PresharedKey&gt; | Diese Informationen gelten nur für Ihr virtuelles Netzwerk und befinden sich im Verwaltungsportal unter "Schlüssel verwalten". |



## IPsec-Parameter

>[AZURE.NOTE] Die im Folgenden aufgelisteten Werte werden zwar vom Azure VPN Gateway unterstützt, derzeit ist es jedoch nicht möglich, eine bestimmte Kombination vom Azure VPN Gateway festzulegen oder auszuwählen. Alle Einschränkungen müssen Sie vom lokalen VPN-Gerät aus angeben. Darüber hinaus müssen Sie MSS mit 1350 verknüpfen.

### IKE Phase 1-Einrichtung

| **Eigenschaft** | **Richtlinienbasiert** | **Routenbasiertes und Standard- oder Hochleistungs-VPN-Gateway** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| IKE-Version | IKEv1 | IKEv2 |
| Diffie-Hellman-Gruppe | Gruppe 2 (1024 Bit) | Gruppe 2 (1024 Bit) |
| Authentifizierungsmethode | Vorab ausgetauschter Schlüssel | Vorab ausgetauschter Schlüssel |
| Verschlüsselungsalgorithmen | AES256 AES128 3DES | AES256 3DES |
| Hashalgorithmus | SHA1(SHA128) | SHA1(SHA128), SHA2(SHA256) |
| Phase 1 Sicherheitszuordnung (SA) Lebensdauer (Zeit) | 28\.800 Sekunden | 10\.800 Sekunden |


### IKE Phase 2-Einrichtung

| **Eigenschaft** | **Richtlinienbasiert** | **Routenbasiertes und Standard- oder Hochleistungs-VPN-Gateway** |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| IKE-Version | IKEv1 | IKEv2 |
| Hashalgorithmus | SHA1(SHA128) | SHA1(SHA128) |
| Phase 2 Sicherheitszuordnung (SA) Lebensdauer (Zeit) | 3\.600 Sekunden | 3\.600 Sekunden |
| Phase 2 Sicherheitszuordnung (SA) Lebensdauer (Durchsatz) | 102.400.000 KB | - | | IPsec-SA-Verschlüsselung und Authentifizierungsangebote (Rangfolge) | 1. ESP-AES256 2. ESP-AES128 3. ESP-3DES 4. N/V | Siehe *IPsec-Sicherheitszuordnungsangebote (SA) für routenbasierte Gateways* (unten) | | Perfect Forward Secrecy (PFS) | Nein | Ja (DH-Gruppe 1, 2, 5, 14, 24) | | Erkennung inaktiver Peers | Nicht unterstützt | Unterstützt |

### IPsec-Sicherheitszuordnungsangebote (SA) für routenbasierte Gateways

Die folgende Tabelle listet die IPSec-SA-Verschlüsselungs und -Authentifizierungsangebote auf. Angebote werden in der Reihenfolge ihrer Priorität aufgeführt, in der das Angebot dargeboten oder akzeptiert wurde.

| **IPsec-SA-Verschlüsselungs- und -Authentifizierungsangebote** | **Azure-Gateway als Initiator** | **Azure-Gateway als Antwortender** |
|---------------------------------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| 1 | ESP AES\_256 SHA | ESP AES\_128 SHA |
| 2 | ESP AES\_128 SHA | ESP 3\_DES MD5 |
| 3 | ESP 3\_DES MD5 | ESP 3\_DES SHA |
| 4 | ESP 3\_DES SHA | AH SHA1 mit ESP AES\_128 mit Null HMAC |
| 5 | AH SHA1 mit ESP AES\_256 mit Null HMAC | AH SHA1 mit ESP 3\_DES mit Null HMAC |
| 6 | AH SHA1 mit ESP AES\_128 mit Null HMAC | AH MD5 mit ESP 3\_DES mit Null HMAC, keine vorgesehene Lebensdauer |
| 7 | AH SHA1 mit ESP 3\_DES mit Null HMAC | AH SHA1 mit ESP 3\_DES SHA1, keine Lebensdauer |
| 8 | AH MD5 mit ESP 3\_DES mit Null HMAC, keine vorgesehene Lebensdauer | AH MD5 mit ESP 3\_DES MD5, keine Lebensdauer |
| 9 | AH SHA1 mit ESP 3\_DES SHA1, keine Lebensdauer | ESP DES MD5 |
| 10 | AH MD5 mit ESP 3\_DES MD5, keine Lebensdauer | ESP DES SHA1, keine Lebensdauer |
| 11 | ESP DES MD5 | AH SHA1 mit ESP DES Null HMAC, keine vorgesehene Lebensdauer |
| 12 | ESP DES SHA1, keine Lebensdauer | AH MD5 mit ESP DES Null HMAC, keine vorgesehene Lebensdauer |
| 13 | AH SHA1 mit ESP DES Null HMAC, keine vorgesehene Lebensdauer | AH SHA1 mit ESP DES SHA1, keine Lebensdauer |
| 14 | AH MD5 mit ESP DES Null HMAC, keine vorgesehene Lebensdauer | AH MD5 mit ESP DES MD5, keine Lebensdauer |
| 15 | AH SHA1 mit ESP DES SHA1, keine Lebensdauer | ESP SHA, keine Lebensdauer |
| 16 | AH MD5 mit ESP DES MD5, keine Lebensdauer | ESP MD5, keine Lebensdauer |
| 17 | - | AH SHA, keine Lebensdauer || 18 | - | AH MD5, keine Lebensdauer |


- Sie können IPsec-ESP-NULL-Verschlüsselung mit routenbasierten und High-Performance-VPN Gateways angeben. Verschlüsselung auf Basis von NULL bietet keinen Schutz der Daten während der Übertragung und sollte nur verwendet werden, wenn maximaler Durchsatz und minimale Latenz erforderlich sind. Clients können diese in Szenarios mit VNET-zu-VNET-Kommunikation oder bei Anwendung der Verschlüsselung an anderer Stelle in der Lösung verwenden.

- Verwenden Sie für standortübergreifende Konnektivität über das Internet die Standardeinstellungen für Azure-VPN-Gateways mit Verschlüsselung und Hashalgorithmen, die in der Tabelle oben aufgelistet werden, um die Sicherheit Ihrer kritischen Kommunikation zu gewährleisten.

<!---HONumber=AcomDC_0803_2016-->