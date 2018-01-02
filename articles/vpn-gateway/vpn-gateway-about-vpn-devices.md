---
title: "Informationen zu VPN-Geräten für standortübergreifende Azure-Verbindungen | Microsoft-Dokumentation"
description: "In diesem Artikel werden VPN-Geräte und IPsec-Parameter für standortübergreifende S2S-VPN Gateway-Verbindungen beschrieben. Er enthält Links zu Konfigurationsanleitungen und -beispielen."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: ba449333-2716-4b7f-9889-ecc521e4d616
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: yushwang
ms.openlocfilehash: bb6f9f4df9afa9d0c1a75fbb1166798a2aef4bb4
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/19/2017
---
# <a name="about-vpn-devices-and-ipsecike-parameters-for-site-to-site-vpn-gateway-connections"></a>Informationen zu VPN-Geräten und IPsec-/IKE-Parametern für VPN-Gatewayverbindungen zwischen Standorten.

Sie benötigen ein VPN-Gerät, um eine standortübergreifende S2S-VPN-Verbindung (Site-to-Site) per VPN Gateway zu konfigurieren. Standort-zu-Standort-Verbindungen können zum Erstellen einer Hybridlösung verwendet werden. Sie können sie auch zum Herstellen von sicheren Verbindungen zwischen Ihren lokalen Netzwerken und den virtuellen Netzwerken verwenden. Dieser Artikel enthält eine Liste mit überprüften VPN-Geräten und eine Liste mit IPsec-/IKE-Parametern für VPN-Gateways.

> [!IMPORTANT]
> Bei Verbindungsproblemen zwischen Ihren lokalen VPN-Geräten und VPN-Gateways helfen Ihnen die Informationen unter [Bekannte Probleme mit der Gerätekompatibilität](#known) weiter.
>

### <a name="items-to-note-when-viewing-the-tables"></a>Was Sie beim Anzeigen der Tabellen beachten sollten:

* Die Terminologie für Azure-VPN-Gateways wurde geändert. Die Änderung betrifft nur die Namen. Dies ist nicht mit einer Änderung der Funktionalität verbunden.
  * Statisches Routing = Richtlinienbasiert (PolicyBased)
  * Dynamisches Routing = Routenbasiert (RouteBased)
* Die Spezifikationen für Hochleistungs-VPN-Gateways und routenbasierte VPN-Gateways bleiben unverändert, sofern nicht anders angegeben. Beispielsweise sind die überprüften VPN-Geräte, die mit den routenbasierten VPN-Gateways kompatibel sind, auch mit dem Hochleistungs-VPN-Gateway kompatibel.

## <a name="devicetable"></a>Überprüfte VPN-Geräte und Konfigurationshandbücher für Geräte

> [!NOTE]
> Beim Konfigurieren einer Standort-zu-Standort-Verbindung ist eine öffentliche IPv4-IP-Adresse für das VPN-Gerät erforderlich.
>

Wir haben in Zusammenarbeit mit Geräteherstellern eine Reihe von VPN-Standardgeräten getestet. Alle Geräte der in der folgenden Liste aufgeführten Gerätefamilien sollten mit VPN-Gateways kompatibel sein. Informationen zur Verwendung des VPN-Typs (richtlinienbasiert oder routenbasiert) für die VPN-Gatewaylösung, die Sie konfigurieren möchten, finden Sie unter [Informationen zu VPN Gateway-Einstellungen](vpn-gateway-about-vpn-gateway-settings.md#vpntype).

Hilfreiche Informationen zur Konfiguration des VPN-Geräts finden Sie unter den Links für die entsprechende Gerätefamilie. Die Links zu den Konfigurationsanleitungen werden nach bestem Wissen bereitgestellt. Support für VPN-Geräte erhalten Sie vom jeweiligen Gerätehersteller.

|**Hersteller**          |**Gerätefamilie**     |**Betriebssystemversion (Min.)** |**PolicyBased-Konfiguration – Anleitung** |**RouteBased-Konfiguration – Anleitung** |
| ---                | ---                  | ---                   | ---            | ---           |
| A10 Networks, Inc. |Thunder CFW           |ACOS 4.1.1             |Nicht kompatibel  |[Konfigurationshandbuch](https://www.a10networks.com/resources/deployment-guides/a10-thunder-cfw-ipsec-vpn-interoperability-azure-vpn-gateways)|
| Allied Telesis     |VPN-Router der AR-Serie |2.9.2                  |In Kürze verfügbar     |Nicht kompatibel  |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall F-Serie |PolicyBased: 5.4.3<br>RouteBased: 6.2.0 |[Konfigurationshandbuch](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) |[Konfigurationshandbuch](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW) |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall X-Serie |Barracuda Firewall 6.5 |[Konfigurationshandbuch](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) |Nicht kompatibel |
| Brocade            |5400 Vyatta vRouter   |Virtual Router 6.6R3 GA|[Konfigurationshandbuch](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) |Nicht kompatibel |
| Check Point |Sicherheitsgateway |R77.30 |[Konfigurationshandbuch](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Konfigurationshandbuch](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco              |ASA       |8.3<br>8.4+ (IKEv2* ) |[Konfigurationsbeispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA) |[Konfigurationshandbuch * ](vpn-gateway-3rdparty-device-config-cisco-asa.md) |
| Cisco |ASR |PolicyBased: IOS 15.1<br>RouteBased: IOS 15.2 |[Konfigurationsbeispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |[Konfigurationsbeispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |
| Cisco |ISR |PolicyBased: IOS 15.0<br>RouteBased* : IOS 15.1 |[Konfigurationsbeispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |[Konfigurationsbeispiele ** ](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |
| Citrix |NetScaler MPX, SDX, VPX |ab 10.1 |[Konfigurationshandbuch](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |Nicht kompatibel |
| F5 |BIG-IP-Serie |12.0 |[Konfigurationshandbuch](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Konfigurationshandbuch](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.6 |  |[Konfigurationshandbuch](http://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-56/) |
| Internet Initiative Japan (IIJ) |SEIL-Serie |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[Konfigurationshandbuch](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |Nicht kompatibel |
| Juniper |SRX |PolicyBased: JunOS 10.2<br>Routebased: JunOS 11.4 |[Konfigurationsbeispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |[Konfigurationsbeispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |
| Juniper |J-Serie |PolicyBased: JunOS 10.4r9<br>RouteBased: JunOS 11.4 |[Konfigurationsbeispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |[Konfigurationsbeispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |
| Juniper |ISG |ScreenOS 6.3 |[Konfigurationsbeispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |[Konfigurationsbeispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |
| Juniper |SSG |ScreenOS 6.2 |[Konfigurationsbeispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |[Konfigurationsbeispiele](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |
| Microsoft |Routing- und RAS-Dienst |Windows Server 2012 |Nicht kompatibel |[Konfigurationsbeispiele](http://go.microsoft.com/fwlink/p/?LinkId=717761) |
| Open Systems AG |Mission Control Security Gateway |– |[Konfigurationshandbuch](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |Nicht kompatibel |
| Palo Alto Networks |Alle Geräte mit PAN-OS 5.0 |PAN-OS<br>PolicyBased: 6.1.5 oder höher<br>RouteBased: 7.1.4 |[Konfigurationshandbuch](https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065) |[Konfigurationshandbuch](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340) |
| ShareTech | UTM der nächsten Generation (NU-Serie) | 9.0.1.3 | Nicht kompatibel | [Konfigurationshandbuch](http://www.sharetech.com.tw/images/file/Solution/NU_UTM/S2S_VPN_with_Azure_Route_Based_en.pdf) |
| SonicWall |TZ-Serie, NSA-Serie<br>SuperMassive-Serie<br>E-Class-NSA-Serie |SonicOS 5.8.x<br>SonicOS 5.9.x<br>SonicOS 6.x |Nicht kompatibel |[Konfigurationshandbuch](https://www.sonicwall.com/support/knowledge-base/170505320011694) |
| Sophos | XG-Firewall der nächsten Generation | XG v17 | | [Konfigurationshandbuch](https://community.sophos.com/kb/127546) |
| WatchGuard |Alle |Fireware XTM<br> PolicyBased: v11.11.x<br>RouteBased: v11.12.x |[Konfigurationshandbuch](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[Konfigurationshandbuch](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|

> [!NOTE]
>
> ( * ) Die Cisco ASA-Version 8.4 und höhere Versionen fügen IKEv2-Unterstützung hinzu und können mithilfe der benutzerdefinierten IPsec-/IKE-Richtlinie mit der Option „UsePolicyBasedTrafficSelectors“ eine Verbindung mit Azure VPN Gateway herstellen. Informationen finden Sie in [diesem](vpn-gateway-connect-multiple-policybased-rm-ps.md) Artikel mit Anleitungen.
>
> ( ** ) Router der Serie ISR 7200 unterstützen nur richtlinienbasierte VPNs.

## <a name="additionaldevices"></a>Nicht überprüfte VPN-Geräte

Falls Ihr Gerät nicht in der Tabelle mit den überprüften VPN-Geräten enthalten ist, kann es unter Umständen für eine Site-to-Site-Verbindung verwendet werden. Zusätzliche Unterstützung und Konfigurationsanweisungen erhalten Sie vom Gerätehersteller.

## <a name="editing"></a>Bearbeiten von Gerätekonfigurationsbeispielen

Nachdem Sie die bereitgestellte Konfigurationsvorlage für das VPN-Gerät heruntergeladen haben, müssen Sie einige der Werte entsprechend den Einstellungen Ihrer Umgebung ersetzen.

### <a name="to-edit-a-sample"></a>So bearbeiten Sie eine Vorlage:

1. Öffnen Sie die Vorlage im Editor.
2. Suchen und ersetzen Sie alle <*text*>-Zeichenfolgen mit den Werten, die für Ihre Umgebung gelten. Schließen Sie dabei unbedingt „<“ und „>“ mit ein. Wenn ein Name angegeben ist, sollte der ausgewählte Name eindeutig sein. Sehen Sie zuerst in der Dokumentation des Geräteherstellers nach, falls ein Befehl nicht funktioniert.

| **Vorlagentext** | **Ändern in** |
| --- | --- |
| &lt;RP_OnPremisesNetwork&gt; |Der von Ihnen gewählte Name für dieses Objekt. Beispiel: meinLokalesNetzwerk |
| &lt;RP_AzureNetwork&gt; |Der von Ihnen gewählte Name für dieses Objekt. Beispiel: meinAzureNetzwerk |
| &lt;RP_AccessList&gt; |Der von Ihnen gewählte Name für dieses Objekt. Beispiel: meineAzureZugriffsliste |
| &lt;RP_IPSecTransformSet&gt; |Der von Ihnen gewählte Name für dieses Objekt. Beispiel: meinIPSecTransformSet |
| &lt;RP_IPSecCryptoMap&gt; |Der von Ihnen gewählte Name für dieses Objekt. Beispiel: meineIPSecKryptoMap |
| &lt;SP_AzureNetworkIpRange&gt; |Geben Sie den Bereich an. Beispiel: 192.168.0.0 |
| &lt;SP_AzureNetworkSubnetMask&gt; |Geben Sie die Subnetzmaske an. Beispiel: 255.255.0.0 |
| &lt;SP_OnPremisesNetworkIpRange&gt; |Geben Sie den lokalen Bereich an. Beispiel: 10.2.1.0 |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; |Geben Sie die lokale Subnetzmaske an. Beispiel: 255.255.255.0 |
| &lt;SP_AzureGatewayIpAddress&gt; |Diese Informationen gelten nur für Ihr virtuelles Netzwerk und befinden sich im Verwaltungsportal unter **Gateway-IP-Adresse**. |
| &lt;SP_PresharedKey&gt; |Diese Informationen gelten nur für Ihr virtuelles Netzwerk und befinden sich im Verwaltungsportal unter "Schlüssel verwalten". |

## <a name="ipsec"></a>IPsec-/IKE-Parameter

> [!IMPORTANT]
> 1. Die folgenden Tabellen enthalten die Kombinationen aus Algorithmen und Parametern, die Azure-VPN-Gateways in einer Standardkonfiguration verwenden. Für routenbasierte VPN-Gateways, die mit dem Azure Resource Manager-Bereitstellungsmodell erstellt wurden, können Sie für jede einzelne Verbindung eine benutzerdefinierte Richtlinie angeben. Ausführliche Anweisungen finden Sie unter [Konfigurieren der IPsec/IKE-Richtlinie für S2S-VPN- oder VNet-zu-VNet-Verbindungen](vpn-gateway-ipsecikepolicy-rm-powershell.md).
>
> 2. Darüber hinaus müssen Sie TCP **MSS** mit **1350** verknüpfen. Wenn Ihre VPN-Geräte MSS-Clamping nicht unterstützen, können Sie stattdessen alternativ den **MTU**-Wert der Tunnelschnittstelle auf **1400** Byte festlegen.
>

Für die folgenden Tabellen gilt:

* SA = Sicherheitszuordnung
* IKE-Phase 1 wird auch als „Hauptmodus“ bezeichnet.
* IKE-Phase 2 wird auch als „Schnellmodus“ bezeichnet.

### <a name="ike-phase-1-main-mode-parameters"></a>Parameter der IKE-Phase 1 (Hauptmodus)

| **Eigenschaft**          |**PolicyBased**    | **RouteBased**    |
| ---                   | ---               | ---               |
| IKE-Version           |IKEv1              |IKEv2              |
| Diffie-Hellman-Gruppe  |Gruppe 2 (1024 Bit) |Gruppe 2 (1024 Bit) |
| Authentifizierungsmethode |Vorab ausgetauschter Schlüssel     |Vorab ausgetauschter Schlüssel     |
| Verschlüsselung und Hashalgorithmen |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |1. AES256, SHA1<br>2. AES256, SHA256<br>3. AES128, SHA1<br>4. AES128, SHA256<br>5. 3DES, SHA1<br>6. 3DES, SHA256 |
| SA-Gültigkeitsdauer           |28.800 Sekunden     |28.800 Sekunden     |

### <a name="ike-phase-2-quick-mode-parameters"></a>Parameter der IKE-Phase 2 (Schnellmodus)

| **Eigenschaft**                  |**PolicyBased**| **RouteBased**                              |
| ---                           | ---           | ---                                         |
| IKE-Version                   |IKEv1          |IKEv2                                        |
| Verschlüsselung und Hashalgorithmen |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |[SA-Angebote für RouteBased QM](#RouteBasedOffers) |
| SA-Gültigkeitsdauer (Zeit)            |3.600 Sekunden  |27.000 Sekunden                                |
| SA-Gültigkeitsdauer (Bytes)           |102.400.000 KB | -                                           |
| Perfect Forward Secrecy (PFS) |Nein             |[SA-Angebote für RouteBased QM](#RouteBasedOffers) |
| Dead Peer Detection (DPD)     |Nicht unterstützt  |Unterstützt                                    |


### <a name ="RouteBasedOffers"></a>RouteBased-VPN-Angebote der IPsec Security Association (IKE-Schnellmodus-SA)

Die folgende Tabelle enthält IPsec-SA-Angebote (IKE-Schnellmodus). Angebote werden in der Reihenfolge ihrer Priorität aufgeführt, in der das Angebot dargeboten oder akzeptiert wurde.

#### <a name="azure-gateway-as-initiator"></a>Azure-Gateway als Initiator

|-  |**Verschlüsselung**|**Authentifizierung**|**PFS-Gruppe**|
|---| ---          |---               |---          |
| 1 |GCM AES256    |GCM (AES256)      |Keine         |
| 2 |AES256        |SHA1              |Keine         |
| 3 |3DES          |SHA1              |Keine         |
| 4 |AES256        |SHA256            |Keine         |
| 5 |AES128        |SHA1              |Keine         |
| 6 |3DES          |SHA256            |Keine         |

#### <a name="azure-gateway-as-responder"></a>Azure-Gateway als Antwortender

|-  |**Verschlüsselung**|**Authentifizierung**|**PFS-Gruppe**|
|---| ---          | ---              |---          |
| 1 |GCM AES256    |GCM (AES256)      |Keine         |
| 2 |AES256        |SHA1              |Keine         |
| 3 |3DES          |SHA1              |Keine         |
| 4 |AES256        |SHA256            |Keine         |
| 5 |AES128        |SHA1              |Keine         |
| 6 |3DES          |SHA256            |Keine         |
| 7 |DES           |SHA1              |Keine         |
| 8 |AES256        |SHA1              |1            |
| 9 |AES256        |SHA1              |2            |
| 10|AES256        |SHA1              |14           |
| 11|AES128        |SHA1              |1            |
| 12|AES128        |SHA1              |2            |
| 13|AES128        |SHA1              |14           |
| 14|3DES          |SHA1              |1            |
| 15|3DES          |SHA1              |2            |
| 16|3DES          |SHA256            |2            |
| 17|AES256        |SHA256            |1            |
| 18|AES256        |SHA256            |2            |
| 19|AES256        |SHA256            |14           |
| 20|AES256        |SHA1              |24           |
| 21|AES256        |SHA256            |24           |
| 22|AES128        |SHA256            |Keine         |
| 23|AES128        |SHA256            |1            |
| 24|AES128        |SHA256            |2            |
| 25|AES128        |SHA256            |14           |
| 26|3DES          |SHA1              |14           |

* Sie können IPsec-ESP-NULL-Verschlüsselung mit routenbasierten VPN-Gateways und Hochleistungs-VPN-Gateways angeben. Verschlüsselung auf Basis von NULL bietet keinen Schutz der Daten während der Übertragung und sollte nur verwendet werden, wenn maximaler Durchsatz und minimale Latenz erforderlich sind. Clients können diese in Szenarien mit VNet-zu-VNet-Kommunikation oder bei Anwendung der Verschlüsselung an anderer Stelle in der Lösung verwenden.
* Verwenden Sie für standortübergreifende Konnektivität über das Internet die Standardeinstellungen für Azure-VPN-Gateways mit Verschlüsselung und Hashalgorithmen, die in der Tabelle oben aufgelistet werden, um die Sicherheit Ihrer kritischen Kommunikation zu gewährleisten.

## <a name="known"></a>Bekannte Probleme mit der Gerätekompatibilität

> [!IMPORTANT]
> Hier finden Sie bekannte Kompatibilitätsprobleme zwischen Drittanbieter-VPN-Geräten und Azure-VPN-Gateways. Das Azure-Team arbeitet zusammen mit den Anbietern aktiv an der Lösung der hier angegebenen Probleme. Nachdem die Probleme behoben wurden, wird diese Seite mit den neuesten Informationen aktualisiert. Es ist also ratsam, diese Seite regelmäßig aufzurufen.
>
>

### <a name="feb-16-2017"></a>16. Februar 2017

**Palo Alto Networks-Geräte mit älteren Versionen als 7.1.4** für routenbasiertes Azure-VPN: Führen Sie die folgenden Schritte aus, wenn Sie VPN-Geräte von Palo Alto Networks mit einer älteren PAN-OS-Version als 7.1.4 verwenden und Konnektivitätsprobleme mit routenbasierten Azure-VPN-Gateways auftreten:

1. Überprüfen Sie die Firmwareversion Ihres Palo Alto Networks-Geräts. Führen Sie ein Upgrade auf Version 7.1.4 durch, falls Ihre PAN-OS-Version älter als 7.1.4 ist.
2. Ändern Sie auf dem Palo Alto Networks-Gerät die Phase 2 SA-Lebensdauer (bzw. Quick Mode SA) in 28.800 Sekunden (8 Stunden), wenn Sie die Verbindung mit dem Azure-VPN-Gateway herstellen.
3. Erstellen Sie über das Azure-Portal eine Supportanfrage, falls weiterhin Probleme mit der Konnektivität auftreten.
