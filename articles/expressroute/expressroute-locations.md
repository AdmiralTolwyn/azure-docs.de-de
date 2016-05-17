<properties
   pageTitle="ExpressRoute-Standorte | Microsoft Azure"
   description="Dieser Artikel enthält eine ausführliche Übersicht über die Standorte, an denen Dienste angeboten werden, sowie Informationen darüber, wie Sie eine Verbindung mit den Azure-Regionen herstellen können."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/19/2016"
   ms.author="cherylmc" />

# ExpressRoute-Partner und Peeringstandorte

In den Tabellen in diesem Artikel finden Sie Informationen zu ExpressRoute-Konnektivitätsanbietern, zum geografischen Geltungsbereich von ExpressRoute, zu Microsoft-Clouddiensten, die über ExpressRoute unterstützt werden, und zu ExpressRoute-Systemintegratoren (SIs).

## <a name="partners"></a>ExpressRoute-Konnektivitätsanbieter

ExpressRoute wird in allen Azure-Regionen und an allen Standorten unterstützt. Die folgende Karte zeigt die Azure-Regionen und ExpressRoute-Standorte. ExpressRoute-Standorte beziehen sich auf jene Orte, an denen Microsoft eine Peeringkooperation mit mehreren Service Providern bietet.

![](./media/expressroute-locations/expressroute-locations-map.png)

Wenn Sie mit mindestens einem ExpressRoute-Standort innerhalb der geopolitischen Region eine Verbindung hergestellt haben, haben Sie Zugriff auf die Azure-Dienste in allen Regionen dieser geopolitischen Region. Die folgende Tabelle bietet eine Übersicht über die Azure-Regionen mit ExpressRoute-Standorten in einer geopolitischen Region.

|**Geopolitische Region**|**Azure-Regionen**|**ExpressRoute-Standorte**|
|---|---|---|
|**Nordamerika**|USA, Osten; USA, Westen; USA, Osten 2; USA, Mitte; USA, Süden-Mitte; USA, Norden-Mitte; Kanada, Mitte; Kanada, Osten|Atlanta, Chicago, Dallas, Las Vegas+, Los Angeles, New York, Seattle, Silicon Valley, Washington DC, Montreal+, Toronto|
|**Südamerika**|Brasilien Süd|Sao Paulo|
|**Europa**|Nordeuropa, Westeuropa|Amsterdam, Dublin, London|
|**Asien**|Ostasien, Südostasien|Hongkong, Singapur|
|**Japan**|Japan West, Japan Ost|Osaka, Tokio|
|**Australien**|Südostaustralien, Ostaustralien|Melbourne, Sydney|
|**Indien**|Indien, Westen, Indien, Mitte, Indien, Süden|Chennai, Mumbai|



In der folgenden Tabelle finden Sie Informationen zu Regionen und geopolitischen Grenzen für nationale Clouds.

|**Geopolitische Region**|**Azure-Regionen**|**ExpressRoute-Standorte**|
|---|---|---|---|
|**US-Government Cloud**|US-Behörde Iowa, US-Behörde Virginia|Chicago, New York+, Washington DC|
|**China**|China (Norden), China (Osten)|Peking, Shanghai|
|**Deutschland**|Deutschland, Mitte, Deutschland, Ost|Berlin+, Frankfurt|


Konnektivität zwischen geopolitischen Regionen wird bei der ExpressRoute-Standard-SKU nicht unterstützt. Sie müssen das ExpressRoute Premium-Add-On aktivieren, um Unterstützung für globale Konnektivität zu erhalten. Verbindungen mit nationalen Cloudumgebungen werden nicht unterstützt. Wenden Sie sich an Ihren Konnektivitätsanbieter, wenn Sie derartige Verbindungen implementieren möchten.


## Standorte von Konnektivitätsanbietern

### Azure-Produktionsumgebungen

| **Service Provider** |**Microsoft Azure** | **Office 365 und CRM Online** | **Standorte** |
|-----------------------|--------------------|----------------|---------------|
| **[Aryaka Networks](http://www.aryaka.com/)** | Unterstützt | Unterstützt | Amsterdam, Silicon Valley, Singapore, Tokyo, Washington DC |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Unterstützt | Unterstützt | Amsterdam, Chicago, Dallas, London, Silicon Valley, Singapore, Sydney, Washington DC |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | Unterstützt | Unterstützt | Amsterdam, Hongkong, London, Silicon Valley, Singapur, Tokio, Washington, D.C. |
|**CenturyLink** | In Kürze verfügbar | In Kürze verfügbar| Silicon Valley |
|**China Telecom Global** | Unterstützt | Nicht unterstützt | Hongkong |
|**Cologix** | Unterstützt | In Kürze verfügbar | Montreal+, Toronto |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Unterstützt | Unterstützt | Amsterdam, Dublin, London |
| **Comcast** | Unterstützt | Unterstützt | Silicon Valley, Washington, D.C. |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** | Unterstützt | Unterstützt | Los Angeles | 
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Unterstützt | Unterstützt | Amsterdam, Atlanta, Chicago, Dallas, Hong Kong, London, Los Angeles, Melbourne, New York, Osaka, Sao Paulo, Seattle, Silicon Valley, Singapore, Sydney, Tokyo, Toronto, Washington DC |
| **euNetworks** | Unterstützt | Unterstützt | Amsterdam |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** | Unterstützt | Unterstützt | Osaka, Tokio |
| **[InterCloud](https://www.intercloud.com/)** | Unterstützt | Unterstützt | Amsterdam, London, Singapur, Washington, D.C. |
| **Internet Solutions – Cloud Connect** | Unterstützt | Unterstützt | Amsterdam, London |
| **Interxion** | Unterstützt | Unterstützt | Amsterdam, London |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Unterstützt | Unterstützt | Amsterdam, Chicago, Dallas, Las Vegas+, London, Seattle, Silicon Valley, Washington DC |
| **Megaport** | Unterstützt | Unterstützt | Dallas, Las Vegas+, Los Angeles, Melbourne, New York, Seattle, Singapur, Sydney, Washington DC |
| **MTN** | Unterstützt | Unterstützt | London |
| **NEXTDC** | Unterstützt | Unterstützt | Melbourne, Sydney |
| **NTT Communications** | Unterstützt | Unterstützt | London, Osaka, Tokyo |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** | Unterstützt | Unterstützt | Amsterdam, Hongkong, London, Silicon Valley, Singapur, Washington, D.C. |
| **PCCW Global Limited** | Unterstützt | Unterstützt | Hongkong |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** | Unterstützt | Nicht unterstützt | Singapur |
| **SoftBank** | Unterstützt | Unterstützt | Osaka, Tokio | 
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | Unterstützt | Unterstützt | Amsterdam, Chennai, Hongkong, London, Mumbai, Silicon Valley, Singapur, Washington DC |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | Unterstützt | Unterstützt | Amsterdam, London |
| **Telenor** | Unterstützt | Unterstützt | Amsterdam, London |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Unterstützt | Nicht unterstützt | Melbourne, Sydney |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** | Unterstützt | Unterstützt | Amsterdam, Hongkong, London, Silicon Valley, Singapur, Sydney, Tokio, Washington DC |
| **Vodafone** | Unterstützt | Nicht unterstützt | London | 
| **[Zayo Group](http://www.zayo.com/solutions/industries/connect-to-cloud-data-centers/cloud-connectivity/microsoft-expressroute/)** | Unterstützt | Unterstützt | Chicago, Los Angeles, New York, Silicon Valley, Toronto, Washington DC |

 **+** steht für "In Kürze"

### Nationale Cloudumgebungen

#### US-Government Cloud

| **Service Provider** |**Microsoft Azure** | **Office 365** | **Standorte** |
|-----------------------|--------------------|----------------|---------------|
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Unterstützt | Unterstützt | Chicago, Washington DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Unterstützt | Unterstützt | Chicago, New York+, Washington DC |
| **[Level 3 Communications - IPVPN](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Unterstützt | In Kürze verfügbar | Chicago, New York+, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | Unterstützt | Unterstützt | Chicago, New York+, Washington DC |

#### China

| **Service Provider** |**Microsoft Azure** | **Office 365** | **Standorte** |
|-----------------------|--------------------|----------------|---------------|
| **China Telecom** | Unterstützt | Nicht unterstützt | Peking, Shanghai|
Weitere Informationen finden Sie unter [ExpressRoute in China](http://www.windowsazure.cn/home/features/expressroute/).

#### Deutschland

| **Service Provider** |**Microsoft Azure** | **Office 365** | **Standorte** |
|-----------------------|--------------------|----------------|---------------|
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | In Kürze verfügbar | Nicht unterstützt | Berlin+, Frankfurt+|
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | In Kürze verfügbar | Nicht unterstützt | Frankfurt+|
| **e-shelter** | In Kürze verfügbar | Nicht unterstützt | Berlin+|
| **Interxion** | Unterstützt | Nicht unterstützt | Frankfurt|

## <a name="nonpartners"></a>Konnektivität über nicht aufgeführte Service Provider

Wenn Ihr Konnektivitätsanbieter nicht in den vorherigen Abschnitten enthalten ist, können Sie dennoch eine Verbindung erstellen.

- Fragen Sie Ihren Konnektivitätsanbieter, ob eine Verbindung mit einem der Exchange-Standorte aus der vorherigen Tabelle bereitgestellt werden kann. Sie können auch die folgenden Links überprüfen, um weitere Informationen über die von den Exchange-Anbietern angebotenen Dienste zu erhalten. Viele Konnektivitätsanbieter sind bereits mit Ethernet-Exchanges verbunden.

	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
	- [InterXion](http://www.interxion.com/)
	- [NextDC](http://www.nextdc.com/)
	- [CoreSite](http://www.coresite.com/)
- Bitten Sie Ihren Konnektivitätsanbieter, Ihr Netzwerk auf den Peeringstandort Ihrer Wahl zu erweitern.
	- Stellen Sie sicher, dass Ihr Konnektivitätsanbieter Ihre Konnektivität mit hoher Verfügbarkeit erweitert, sodass es keine einzelnen Fehlerquellen mehr gibt.
- Bestellen Sie eine ExpressRoute-Verbindung mit der Exchange als Konnektivitätsanbieter für die Verbindung mit Microsoft.
	- Führen Sie die Schritte in [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-classic.md) aus, um die Verbindung einzurichten.

|**Konnektivitätsanbieter**|**Exchange**|**Peeringstandorte**|
|---|---|---|
|**Alaska Communications**|Equinix|Seattle|
|**[XO Communications](http://www.xo.com/)**|Equinix|Silicon Valley|

## ExpressRoute-Systemintegratoren

Je nach Ihrer Netzwerkgröße kann das Aktivieren privater Verbindungen für Ihre Anforderungen problematisch sein. Alle Systemintegratoren, die in der folgenden Tabelle aufgeführt sind, können Ihnen beim Integrieren von ExpressRoute helfen.

|**Systemintegrator**|**Kontinent**|
|---|---|
|**[Avanade Inc.](http://www.avanade.com/)**| Asien, Europa, USA |
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**| Europa |
|**[Nimbo](http://www.nimbo.com/)**|US||
|**[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | Asien |
|**[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | US |
|**[Projektleitung](http://www.projectleadership.net/azure)** | US |

## Nächste Schritte

- Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).
- Stellen Sie sicher, dass alle Voraussetzungen erfüllt sind. Informationen finden Sie unter [ExpressRoute-Voraussetzungen](expressroute-prerequisites.md).

<!---HONumber=AcomDC_0511_2016-->