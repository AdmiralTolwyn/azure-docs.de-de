---
title: Bewährte Methoden für die Azure-Netzwerksicherheit | Microsoft Docs
description: Dieser Artikel enthält eine Reihe von bewährten Methoden für die Netzwerksicherheit unter Verwendung der integrierten Azure-Funktionen.
services: security
documentationcenter: na
author: TomShinder
manager: mbaldwin
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 5ebeadd9c0805ac5f6ac543a49cb9ff63d8ded3f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34367323"
---
# <a name="azure-network-security-best-practices"></a>Bewährte Methoden für die Azure-Netzwerksicherheit
Mit Microsoft Azure können Sie virtuelle Computer und Appliances mit anderen Geräten im Netzwerk verbinden, indem Sie sie in Azure Virtual Networks anordnen. Ein Azure Virtual Network ist ein Konstrukt, mit dem Sie virtuelle Netzwerkschnittstellenkarten mit einem virtuellen Netzwerk verbinden können, um die TCP/IP-basierte Kommunikation zwischen netzwerkfähigen Geräten zu ermöglichen. Virtuelle Azure-Computer, die mit einem virtuellen Azure-Netzwerk verbunden sind, können eine Verbindung mit Geräten im selben virtuellen Azure-Netzwerk, anderen virtuellen Azure-Netzwerken, im Internet oder sogar in eigenen lokalen Netzwerken herstellen.

In diesem Artikel werden die bewährten Methoden für die Azure-Netzwerksicherheit beschrieben. Diese empfohlenen Vorgehensweisen sind aus unseren Erfahrungen mit dem Azure-Netzwerk und den Erfahrungen von Kunden wie Ihnen abgeleitet.

In diesem Artikel wird für jede bewährte Methode Folgendes erläutert:

* Wobei es bei der bewährten Methode geht
* Warum Sie die bewährte Methode nutzen sollten
* Was die Folge sein könnte, wenn Sie die bewährte Methode nicht aktivieren
* Mögliche Alternativen zur bewährten Methode
* Wie Sie erfahren können, wie Sie die empfohlenen Vorgehensweisen aktivieren

Dieser Artikel zu den bewährten Methoden für die Azure-Netzwerksicherheit basiert auf einer Konsensmeinung und den Fähigkeiten und Funktionssätzen der Azure-Plattform, wie sie zum Erstellungszeitpunkt dieses Artikels existierten. Meinungen und Technologien ändern sich im Laufe der Zeit. Dieser Artikel wird daher regelmäßig aktualisiert, um diese Änderungen widerzuspiegeln.

Bewährte Methoden zur Azure-Netzwerksicherheit, die in diesem Artikel beschrieben werden:

* Logische Segmentsubnetze
* Steuern des Routingverhaltens
* Aktivieren der Tunnelerzwingung
* Verwenden virtueller Network Appliances
* Bereitstellen von DMZs für Sicherheitszonen
* Vermeiden der Offenlegung gegenüber dem Internet mit dedizierten WAN-Links
* Optimieren der Betriebszeit und Leistung
* Verwenden des globalen Lastenausgleichs
* Deaktivieren des RDP-Zugriffs auf Azure Virtual Machines
* Aktivieren von Azure Security Center
* Erweitern des Rechenzentrums auf Azure

## <a name="logically-segment-subnets"></a>Logische Segmentsubnetze
[Azure Virtual Networks](https://azure.microsoft.com/documentation/services/virtual-network/) ähneln einem LAN in Ihrem lokalen Netzwerk. Die Idee hinter einem virtuellen Azure-Netzwerk ist, dass Sie ein Netzwerk mit nur einem privaten IP-Adressraum erstellen, in dem Sie alle [virtuellen Azure-Computer](https://azure.microsoft.com/services/virtual-machines/) anordnen können. Die verfügbaren privaten IP-Adressräume liegen in den Bereichen der Klasse A (10.0.0.0/8), Klasse B (172.16.0.0/12) und Klasse C (192.168.0.0/16).

Sie sollten ähnlich wie in Ihrem lokalen Netzwerk vorgehen und die größeren Adressräume in Subnetze segmentieren. Sie können [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)-basierte Subnetzprinzipien verwenden, um die Subnetze zu erstellen.

Das Routing zwischen den Subnetzen wird automatisch durchgeführt, und es ist nicht erforderlich, Routingtabellen manuell zu konfigurieren. Die Standardeinstellung ist aber, dass keine Netzwerkzugriffssteuerung zwischen den Subnetzen erfolgt, die Sie im Azure Virtual Network erstellen. Um zwischen Subnetzen Netzwerkzugriffssteuerungen zu erstellen, müssen Sie zwischen die Subnetze Elemente einfügen.

Eine Möglichkeit zur Erreichung dieses Ziels ist die Nutzung einer [Netzwerksicherheitsgruppe](../virtual-network/security-overview.md) (NSG). Bei NSGs handelt es sich um einfache zustandsbehaftete Paketuntersuchungsgeräte, für die der 5-Tupel-Ansatz (Quell-IP, Quellport, Ziel-IP, Zielport und Layer-4-Protokoll) verwendet wird, um Zulassen/Verweigern-Regeln für den Netzwerkdatenverkehr zu erstellen. Sie können Datenverkehr für eine einzelne IP-Adresse, mehrere IP-Adressen und gesamte Subnetze in beiden Richtungen zulassen oder verweigern.

Die Verwendung von NSGs für die Netzwerkzugriffssteuerung zwischen Subnetzen ermöglicht Ihnen das Anordnen von Ressourcen, die zu derselben Sicherheitszone oder Rolle gehören, in eigenen Subnetzen. Stellen Sie sich beispielsweise eine einfache Anwendung mit drei Ebenen vor, z.B. einer Webebene, einer Ebene für die Anwendungslogik und einer Datenbankebene. Sie ordnen virtuelle Computer, die zu diesen Ebenen gehören, jeweils in eigenen Subnetzen an. Anschließend verwenden Sie Netzwerksicherheitsgruppen, um den Datenverkehr zwischen den Subnetzen zu steuern:

* Virtuelle Computer der Webebene können nur Verbindungen mit den Computern der Anwendungslogik initiieren und nur Verbindungen aus dem Internet akzeptieren.
* Virtuelle Computer der Anwendungslogik können nur Verbindungen mit der Datenbankebene initiieren und nur Verbindungen der Webebene akzeptieren.
* Virtuelle Computer der Datenbankebene können keine Verbindung mit Elementen außerhalb ihres eigenen Subnetzes initiieren und nur Verbindungen von der Ebene für die Anwendungslogik akzeptieren.

Weitere Informationen zu Netzwerksicherheitsgruppen und deren Verwendung zum logischen Segmentieren von virtuellen Azure-Netzwerken finden Sie unter [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](../virtual-network/security-overview.md).

## <a name="control-routing-behavior"></a>Steuern des Routingverhaltens
Wenn Sie einen virtuellen Computer in einem Azure Virtual Network anordnen, werden Sie merken, dass der virtuelle Computer eine Verbindung mit jedem anderen virtuellen Computer in demselben Azure Virtual Network herstellen kann. Dies gilt auch, wenn sich die anderen virtuellen Computer in unterschiedlichen Subnetzen befinden. Dies ist möglich, weil standardmäßig aktivierte Systemrouten vorhanden sind, mit denen diese Art der Kommunikation durchgeführt werden kann. Diese Standardrouten ermöglichen virtuellen Computern in demselben Azure Virtual Network die Initiierung von Verbindungen untereinander und mit dem Internet (gilt nur für ausgehende Kommunikation mit dem Internet).

Die standardmäßigen Systemrouten sind zwar für viele Bereitstellungsszenarien nützlich, aber es kann auch vorkommen, dass Sie die Routingkonfiguration für Ihre Bereitstellungen anpassen möchten. Diese Anpassungen ermöglichen Ihnen die Konfiguration der nächsten Hopadresse, um bestimmte Ziele zu erreichen.

Wir empfehlen Ihnen, beim Bereitstellen einer Sicherheitsappliance für ein virtuelles Netzwerk benutzerdefinierte Routen zu konfigurieren. Dies wird unter einer anderen bewährten Methode noch beschrieben.

> [!NOTE]
> Benutzerdefinierte Routen sind nicht erforderlich, und die Standardsystemrouten funktionieren in den meisten Instanzen.
>
>

Weitere Informationen zu benutzerdefinierten Routen und deren Konfiguration finden Sie im Artikel [Routing von Datenverkehr für virtuelle Netzwerke](../virtual-network/virtual-networks-udr-overview.md).

## <a name="enable-forced-tunneling"></a>Aktivieren der Tunnelerzwingung
Zum besseren Verständnis der Tunnelerzwingung ist es hilfreich, wenn Sie wissen, um was es bei „getrennten Tunneln“ geht.
Das häufigste Beispiel für getrennte Tunnels tritt bei VPN-Verbindungen auf. Stellen Sie sich vor, dass Sie von Ihrem Hotelzimmer aus eine VPN-Verbindung mit dem Netzwerk Ihres Unternehmens herstellen. Über diese Verbindung können Sie auf Unternehmensressourcen zugreifen, und die gesamte Kommunikation mit Ihrem Unternehmensnetzwerk verläuft durch den VPN-Tunnel.

Was passiert, wenn Sie eine Verbindung mit Ressourcen im Internet herstellen möchten? Wenn getrennte Tunnel aktiviert sind, verlaufen diese Verbindungen direkt ins Internet und nicht über den VPN-Tunnel. Einige Sicherheitsexperten betrachten dies als Sicherheitsrisiko und empfehlen aus diesem Grund, getrenntes Tunneln sowie alle Verbindungen zu deaktivieren. Internetverbindungen und Verbindungen für Unternehmensressourcen sollten über den VPN-Tunnel hergestellt werden. Der Vorteil besteht hierbei darin, dass Verbindungen ins Internet dann die Sicherheitsgeräte des Unternehmensnetzwerks durchlaufen müssen. Dies wäre nicht der Fall, wenn sich der mit dem Internet verbundene VPN-Client außerhalb des VPN-Tunnels befinden würde.

Wir wenden uns nun wieder virtuellen Computern in einem Azure Virtual Network zu. Die Standardrouten für ein Azure Virtual Network ermöglichen virtuellen Computern das Initiieren eines Datenverkehraustauschs mit dem Internet. Auch dies kann ein Sicherheitsrisiko darstellen, da diese ausgehenden Verbindungen die Angriffsfläche eines virtuellen Computers vergrößern und von Angreifern ausgenutzt werden können.
Aus diesem Grund wird empfohlen, die Tunnelerzwingung auf Ihren virtuellen Computern zu aktivieren, wenn Sie eine standortübergreifende Konnektivität zwischen Ihrem Azure Virtual Network und dem lokalen Netzwerk verwenden. Die standortübergreifende Konnektivität wird später in diesem Dokument zu den bewährten Methoden für Azure-Netzwerke beschrieben.

Falls Sie keine standortübergreifende Verbindung verwenden, sollten Sie sicherstellen, dass Sie Netzwerksicherheitsgruppen (bereits beschrieben) oder Sicherheitsappliances für Azure Virtual Networks (werden als Nächstes beschrieben) nutzen. So können Sie ausgehende Verbindungen mit dem Internet für Ihre Azure Virtual Machines verhindern.

Weitere Informationen zur Tunnelerzwingung und deren Aktivierung finden Sie unter [Konfigurieren der Tunnelerzwingung mit dem Azure Resource Manager-Bereitstellungsmodell](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md).

## <a name="use-virtual-network-appliances"></a>Verwenden virtueller Network Appliances
Netzwerksicherheitsgruppen und benutzerdefiniertes Routing können zwar ein gewisses Maß an Netzwerksicherheit in der Vermittlungs- und Transportschicht des [OSI-Modells](https://en.wikipedia.org/wiki/OSI_model) bieten, aber es kann auch zu Situationen kommen, in denen Sie die Sicherheit für die höheren Schichten des Stapels aktivieren möchten bzw. müssen. In diesen Situationen ist es ratsam, von Azure-Partnern angebotene Appliances für die Sicherheit virtueller Netzwerke bereitzustellen.

Sicherheitsappliances für Azure-Netzwerke können gegenüber den Steuerungen auf Netzwerkebene bessere Sicherheitsstufen bieten. Beispiele für Netzwerksicherheitsfunktionen, die von Sicherheitsappliances für virtuelle Netzwerke bereitgestellt werden:

* Firewall
* Angriffserkennung/Eindringschutz
* Verwaltung von Sicherheitsrisiken
* Anwendungssteuerung
* Netzwerkbasierte Erkennung von Anomalien
* Webfilterung
* Virenschutz
* Botnet-Schutz

Wenn Sie eine höhere Netzwerksicherheitsstufe benötigen, als mit der Zugriffssteuerung auf Netzwerkebene möglich ist, empfehlen wir Ihnen, sich mit Sicherheitsappliances für Azure Virtual Networks zu beschäftigen und diese bereitzustellen.

Die für virtuelle Azure-Netzwerke verfügbaren Sicherheitsappliances und die dazugehörigen Funktionen finden Sie im [Azure Marketplace](https://azure.microsoft.com/marketplace/). Suchen Sie nach den Begriffen „Sicherheit“ und „Netzwerksicherheit“.

## <a name="deploy-dmzs-for-security-zoning"></a>Bereitstellen von DMZs für Sicherheitszonen
Eine DMZ bzw. ein „Umkreisnetzwerk“ ist ein physisches oder logisches Netzwerksegment, das dafür ausgelegt ist, als zusätzliche Sicherheitsebene zwischen Ihren Ressourcen und dem Internet zu dienen. Das Ziel besteht bei der DMZ darin, am Rand des DMZ-Netzwerks spezielle Geräte für die Netzwerkzugriffssteuerung anzuordnen, sodass nur der gewünschte Datenverkehr über das Netzwerksicherheitsgerät in Ihr Azure Virtual Network gelangen kann.

DMZs sind nützlich, da Sie sich bei der Verwaltung, Überwachung, Protokollierung und Berichterstellung für die Netzwerkzugriffssteuerung auf die Geräte am Rand des virtuellen Azure-Netzwerks konzentrieren können. Normalerweise aktivieren Sie DDoS-Verhinderung, Angriffserkennungs-/Eindringschutzsysteme, Firewallregeln und -richtlinien, Webfilterung, Antischadsoftware für das Netzwerk usw. Die Geräte für die Netzwerksicherheit sind zwischen dem Internet und Ihrem Azure Virtual Network angeordnet und verfügen in beiden Netzwerken über eine Schnittstelle.

Dies ist das grundlegende Design einer DMZ, aber es gibt noch viele andere DMZ-Designs, z.B. Back-to-Back, Tri-Homed, Multi-Homed usw.

Wir empfehlen Ihnen, für alle Bereitstellungen mit hohen Sicherheitsanforderungen, für die Sie die Nutzung einer DMZ erwägen, die Netzwerksicherheitsstufe für Ihre Azure-Ressourcen zu erhöhen.

Weitere Informationen zu DMZs und deren Bereitstellung in Azure finden Sie unter [Microsoft Cloud Services und Netzwerksicherheit](../best-practices-network-security.md).

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Vermeiden der Offenlegung gegenüber dem Internet mit dedizierten WAN-Links
Viele Organisationen haben sich für die Hybrid-IT-Route entschieden. Bei Hybrid-IT befinden sich einige Datenressourcen des Unternehmens in Azure, während andere weiterhin lokal gespeichert sind. In vielen Fällen werden einige Komponenten eines Diensts in Azure ausgeführt, während andere Komponenten weiterhin lokal vorhanden sind.

Beim Hybrid-IT-Szenario wird normalerweise eine Art von standortübergreifender Konnektivität verwendet. Diese standortübergreifende Konnektivität ermöglicht es dem Unternehmen, seine lokalen Netzwerke mit Azure Virtual Networks zu verbinden. Zwei Lösungen für standortübergreifende Konnektivität sind verfügbar:

* Standort-zu-Standort-VPN-Verbindung
* ExpressRoute

Eine [Site-to-Site-VPN-Verbindung](../vpn-gateway/vpn-gateway-site-to-site-create.md) ist eine virtuelle private Verbindung zwischen Ihrem lokalen Netzwerk und einem virtuellen Azure-Netzwerk. Diese Verbindung wird über das Internet hergestellt und ermöglicht Ihnen das Übertragen von Informationen in einem „Tunnel“ innerhalb eines verschlüsselten Links zwischen Ihrem Netzwerk und Azure. Eine Site-to-Site-VPN-Verbindung ist eine sichere, ausgereifte Technologie, die seit Jahrzehnten von Unternehmen aller Größen eingesetzt wird. Die Tunnelverschlüsselung wird mit dem [IPSec-Tunnelmodus](https://technet.microsoft.com/library/cc786385.aspx) durchgeführt.

Bei einer Site-to-Site-VPN-Verbindung handelt es sich zwar um eine vertrauenswürdige, zuverlässige und etablierte Technologie, aber der Datenverkehr im Tunnel durchläuft das Internet. Außerdem ist die Bandbreite mit einem Maximum von ca. 200 MBit/s relativ begrenzt.

Falls bei Ihnen eine außergewöhnlich hohe Sicherheitsstufe oder Leistung für standortübergreifende Verbindungen erforderlich ist, empfehlen wir Ihnen, Azure ExpressRoute für Ihre standortübergreifende Konnektivität zu verwenden. ExpressRoute ist ein dedizierter WAN-Link zwischen Ihrem lokalen Standort und einem Exchange-Hostinganbieter. Da dies eine Telekommunikationsverbindung ist, werden Ihre Daten nicht über das Internet übertragen und unterliegen daher auch nicht den potenziellen Risiken der Internetkommunikation.

Informationen zur Funktionsweise von Azure ExpressRoute und zur Bereitstellung finden Sie unter [ExpressRoute – Technische Übersicht](../expressroute/expressroute-introduction.md).

## <a name="optimize-uptime-and-performance"></a>Optimieren der Betriebszeit und Leistung
Vertraulichkeit, Integrität und Verfügbarkeit (Confidentiality, Integrity, Availability – kurz CIA) sind die drei Säulen des einflussreichsten Sicherheitsmodells der heutigen Zeit. Bei der Vertraulichkeit geht es um Verschlüsselung und Datenschutz. Bei der Integrität soll sichergestellt werden, dass Daten nicht von unbefugten Personen geändert werden. Das Ziel bei der Verfügbarkeit besteht darin, dass autorisierte Personen auf die Informationen zugreifen können, für die sie die Autorisierung erhalten haben. Fehler in diesen Bereichen stellen eine potenzielle Sicherheitsverletzung dar.

Die Verfügbarkeit kann auch unter dem Aspekt der Betriebszeit und Leistung betrachtet werden. Wenn ein Dienst ausgefallen ist, kann nicht auf Informationen zugegriffen werden. Wenn die Leistung so schlecht ist, dass die Daten nicht genutzt werden können, werden die Daten als nicht verfügbar angesehen. Aus Gründen der Sicherheit müssen wir alles unternehmen, um sicherzustellen, dass für unsere Dienste eine optimale Betriebszeit und Leistung gewährleistet ist.
Eine beliebte und effektive Methode zum Verbessern der Verfügbarkeit und Leistung ist die Nutzung des Lastenausgleichs. Der Lastenausgleich ist ein Verfahren zum Verteilen von Netzwerkdatenverkehr auf Server, die Teil eines Diensts sind. Wenn Sie im Rahmen Ihres Diensts beispielsweise Front-End-Webserver verwenden, können Sie den Lastenausgleich nutzen, um Datenverkehr auf die verschiedenen Front-End-Webserver zu verteilen.

Die Verteilung von Datenverkehr erhöht die Verfügbarkeit, weil Folgendes passiert, wenn einer der Webserver nicht mehr verfügbar ist: Das Lastenausgleichsmodul beendet das Senden von Datenverkehr an den Server und leitet ihn an die Server um, die noch online sind. Außerdem trägt der Lastenausgleich zur Steigerung der Leistung bei, da der Mehraufwand für den Prozessor, das Netzwerk und den Arbeitsspeicher zum Verarbeiten von Anforderungen auf alle Server mit Lastenausgleich verteilt wird.

Wir empfehlen Ihnen, den Lastenausgleich nach Möglichkeit immer zu nutzen, wenn diese Vorgehensweise für Ihre Dienste geeignet ist. Wir behandeln die Eignung in folgenden Abschnitten: Auf Azure Virtual Network-Ebene bietet Azure Ihnen drei Hauptoptionen für den Lastenausgleich:

* HTTP-basierter Lastenausgleich
* Externer Lastenausgleich
* Interner Lastenausgleich

## <a name="http-based-load-balancing"></a>HTTP-basierter Lastenausgleich
Beim HTTP-basierten Lastenausgleich werden Entscheidungen darüber, an welchen Server Verbindungen gesendet werden, anhand der Merkmale des HTTP-Protokolls getroffen. Azure verfügt über einen HTTP-Lastenausgleich, der als Application Gateway bezeichnet wird.

Es wird empfohlen, Azure Application Gateway in folgenden Fällen zu verwenden:

* Anwendungen, bei denen Anforderungen einer Benutzer-/Clientsitzung den gleichen virtuellen Back-End-Computer erreichen müssen. Beispiele hierfür wären Einkaufswagen-Apps und Web-E-Mail-Server.
* Anwendungen, bei denen der Mehraufwand für die SSL-Beendigung für Webserverfarmen beseitigt werden soll, indem das Feature [SSL-Auslagerung](https://f5.com/glossary/ssl-offloading) von Application Gateway genutzt wird.
* Anwendungen, wie z.B. ein Content Delivery Network, für die mehrere HTTP-Anforderungen über die gleiche lange bestehende TCP-Verbindung an verschiedene Back-End-Server weitergeleitet werden bzw. für die dort ein Lastenausgleich erfolgen muss.

Weitere Informationen zur Funktionsweise von Azure Application Gateway und zur Verwendung in Ihren Bereitstellungen finden Sie unter [Übersicht über Application Gateway](../application-gateway/application-gateway-introduction.md).

## <a name="external-load-balancing"></a>Externer Lastenausgleich
Der externe Lastenausgleich findet statt, wenn für eingehende Verbindungen aus dem Internet ein Lastenausgleich auf mehrere Server in einem virtuellen Azure-Netzwerk durchgeführt wird. Das Azure-Modul für den externen Lastenausgleich enthält diese Funktion. Wir empfehlen Ihnen, diese Funktion zu verwenden, wenn Sie keine persistenten Sitzungen oder SSL-Auslagerungen benötigen.

Im Gegensatz zum HTTP-basierten Lastenausgleich werden für den externen Lastenausgleich Informationen auf der Vermittlungs- und Transportschicht des OSI-Netzwerkmodells verwendet, um Entscheidungen darüber zu treffen, für welchen Server der Lastenausgleich der Verbindung durchgeführt werden soll.

Wir empfehlen Ihnen die Nutzung des externen Lastenausgleichs, wenn Sie über [zustandslose Anwendungen](http://whatis.techtarget.com/definition/stateless-app) verfügen, die eingehende Anforderungen aus dem Internet akzeptieren.

Weitere Informationen zur Funktionsweise des Azure-Moduls für den externen Lastenausgleich und zur Bereitstellung finden Sie unter [Erstellen eines öffentlichen Lastenausgleichs im Resource Manager mithilfe von PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="internal-load-balancing"></a>Interner Lastenausgleich
Der interne Lastenausgleich ähnelt dem externen Lastenausgleich, und es wird der gleiche Mechanismus genutzt, um den Lastenausgleich für Verbindungen mit den jeweiligen Servern durchzuführen. Der einzige Unterschied besteht darin, dass der Lastenausgleich in diesem Fall Verbindungen von virtuellen Computern akzeptiert, die nicht im Internet angeordnet sind. In den meisten Fällen werden die Verbindungen, die für den Lastenausgleich akzeptiert werden, von Geräten in einem Azure Virtual Network initiiert.

Wir empfehlen Ihnen die Verwendung des internen Lastenausgleichs für Szenarien, die von dieser Funktion profitieren, z.B. wenn Sie den Lastenausgleich für Verbindungen mit SQL Server-Instanzen oder internen Webservern durchführen müssen.

Weitere Informationen zur Funktionsweise des internen Lastenausgleich von Azure und zur Bereitstellung finden Sie unter [Erstellen eines internen Lastenausgleichs mit dem Azure PowerShell-Modul](../load-balancer/load-balancer-get-started-ilb-arm-ps.md).

## <a name="use-global-load-balancing"></a>Verwenden des globalen Lastenausgleichs
Das Public Cloud Computing ermöglicht die Bereitstellung global verteilter Anwendungen, deren Komponenten in Rechenzentren weltweit verteilt sind. Dies ist unter Microsoft Azure aufgrund der globalen Präsenz der Azure-Rechenzentren möglich. Im Gegensatz zu den zuvor erwähnten Technologien für den Lastenausgleich ist es beim globalen Lastenausgleich möglich, Dienste auch dann bereitzustellen, wenn ganze Rechenzentren nicht mehr verfügbar sind.

Sie können diese Art von globalem Lastenausgleich in Azure nutzen, indem Sie den [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/) verwenden. Traffic Manager ermöglicht Ihnen den Lastenausgleich für Verbindungen mit Ihren Diensten basierend auf dem Standort des Benutzers.

Wenn ein Benutzer Ihren Dienst beispielsweise von einem EU-Standort aus anfordert, wird die Verbindung an Ihre Dienste umgeleitet, die in einem EU-Rechenzentrum angeordnet sind. Mit diesem Teil des globalen Lastenausgleichs von Traffic Manager können Sie die Leistung verbessern, da die Herstellung der Verbindung mit dem nächsten Rechenzentrum schneller als die Verbindung mit weit entfernten Rechenzentren möglich ist.

Im Hinblick auf die Verfügbarkeit wird beim globalen Lastenausgleich sichergestellt, dass Ihr Dienst auch dann verfügbar ist, wenn ein gesamtes Rechenzentrum ausfällt.

Wenn ein Azure-Rechenzentrum beispielsweise aus umweltbezogenen Gründen oder aufgrund von Ausfällen (z.B. Ausfall des Netzwerks in einer Region) nicht mehr verfügbar ist, werden Verbindungen mit Ihrem Dienst an das nächste Rechenzentrum umgeleitet, das online ist. Dieser globale Lastenausgleich wird erreicht, indem DNS-Richtlinien genutzt werden, die Sie in Traffic Manager erstellen können.

Wir empfehlen Ihnen die Nutzung von Traffic Manager für alle von Ihnen entwickelten Cloud-Lösungen, die über mehrere Regionen verteilt sind und für die eine Betriebszeit mit möglichst wenig Unterbrechungen erforderlich ist.

Informationen zum Azure Traffic Manager und seiner Bereitstellung finden Sie unter [Was ist Traffic Manager?](../traffic-manager/traffic-manager-overview.md).

## <a name="disable-rdpssh-access-to-azure-virtual-machines"></a>Deaktivieren des RDP/SSH-Zugriffs auf Azure Virtual Machines
Es ist möglich, mit dem [Remotedesktopprotokoll](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) und den [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell)-Protokollen (SSH) eine Verbindung mit Azure Virtual Machines herzustellen. Mit diesen Protokollen können Sie virtuelle Computer von Remotestandorten verwalten. Sie werden für das Computing in Rechenzentren standardmäßig eingesetzt.

Das potenzielle Sicherheitsproblem bei der Verwendung dieser Protokolle über das Internet besteht darin, dass Angreifer verschiedene [Brute-Force](https://en.wikipedia.org/wiki/Brute-force_attack)-Verfahren einsetzen können, um Zugriff auf Azure Virtual Machines zu erlangen. Nachdem sich die Angreifer Zugang verschafft haben, können sie Ihren virtuellen Computer als Ausgangspunkt für die Kompromittierung anderer Computer im Azure Virtual Network nutzen oder sogar Netzwerkgeräte außerhalb von Azure angreifen.

Aus diesem Grund empfehlen wir Ihnen, den direkten RDP- und SSH-Zugriff auf Ihre Azure Virtual Machines über das Internet zu deaktivieren. Nachdem der direkte RDP- und SSH-Zugriff über das Internet deaktiviert wurde, haben Sie andere Möglichkeiten, um für die Durchführung der Remoteverwaltung auf diese virtuellen Computer zuzugreifen:

* Point-to-Site-VPN-Verbindung
* Standort-zu-Standort-VPN-Verbindung
* ExpressRoute

[Point-to-Site-VPN-Verbindung](../vpn-gateway/vpn-gateway-point-to-site-create.md) ist ein anderer Ausdruck für eine VPN-Client/Server-Verbindung mit Remotezugriff. Bei einer Point-to-Site-VPN-Verbindung kann ein Benutzer eine Verbindung mit einem Azure Virtual Network über das Internet herstellen. Nachdem die Point-to-Site-Verbindung hergestellt wurde, kann der Benutzer per RDP oder SSH eine Verbindung mit allen virtuellen Computern im Azure Virtual Network herstellen, mit denen der Benutzer per Point-to-Site-VPN-Verbindung verbunden ist. Hierbei wird davon ausgegangen, dass der Benutzer dazu berechtigt ist, auf diese virtuellen Computer zuzugreifen.

Eine Point-to-Site-VPN-Verbindung ist sicherer als direkte RDP- oder SSH-Verbindungen, da sich der Benutzer zweimal authentifizieren muss, bevor er die Verbindung mit einem virtuellen Computer herstellen kann. Zuerst muss sich der Benutzer authentifizieren (und autorisiert werden), um die Point-to-Site-VPN-Verbindung herzustellen. Anschließend muss sich der Benutzer authentifizieren (und autorisiert werden), um die RDP- oder SSH-Sitzung einzurichten.

Über eine [Site-to-Site-VPN-Verbindung](../vpn-gateway/vpn-gateway-site-to-site-create.md) wird ein gesamtes Netzwerk über das Internet mit einem anderen Netzwerk verbunden. Sie können eine Site-to-Site-VPN-Verbindung verwenden, um Ihr lokales Netzwerk mit einem Azure Virtual Network zu verbinden. Wenn Sie eine Site-to-Site-VPN-Verbindung bereitstellen, können Benutzer in Ihrem lokalen Netzwerk eine Verbindung mit virtuellen Computern in Ihrem Azure Virtual Network herstellen, indem sie das RDP- oder SSH-Protokoll über die Site-to-Site-VPN-Verbindung verwenden. Hierfür ist es nicht erforderlich, dass Sie den direkten RDP- oder SSH-Zugriff über das Internet zulassen.

Sie können auch eine dedizierte WAN-Verbindung verwenden, um Funktionalität bereitzustellen, die der Site-to-Site-VPN-Verbindung ähnelt. Im Folgenden werden die Hauptunterschiede erläutert: 1. Die dedizierte WAN-Verbindung verläuft nicht über das Internet. 2. Dedizierte WAN-Verbindungen sind in der Regel stabiler und leistungsfähiger. In Azure wird eine Lösung für eine dedizierte WAN-Verbindung in Form von [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) bereitgestellt.

## <a name="enable-azure-security-center"></a>Aktivieren von Azure Security Center
Azure Security Center unterstützt Sie beim Verhindern, Erkennen und Beheben von Bedrohungen und sorgt für eine größere Transparenz und bessere Kontrolle in Bezug auf die Sicherheit Ihrer Azure-Ressourcen. Es bietet integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Azure-Abonnements, hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem breiten Spektrum an Sicherheitslösungen verwendet werden.

Mit Azure Security Center können Sie die Netzwerksicherheit wie folgt optimieren und überwachen:

* Angeben von Empfehlungen zur Netzwerksicherheit
* Überwachen des Zustands Ihrer Konfiguration der Netzwerksicherheit
* Warnen von netzwerkbasierten Bedrohungen auf Endpunkt- und Netzwerkebene

Wir empfehlen Ihnen dringend, Azure Security Center für alle Azure-Bereitstellungen zu aktivieren.

Weitere Informationen zum Azure Security Center und zur Aktivierung für Ihre Bereitstellungen finden Sie unter [Einführung in das Azure Security Center](../security-center/security-center-intro.md).

## <a name="securely-extend-your-datacenter-into-azure"></a>Sicheres Erweitern des Rechenzentrums auf Azure
Viele IT-Abteilungen von Unternehmen setzen auf die Erweiterung in die Cloud, anstatt ihre lokalen Rechenzentren weiter auszubauen. Dieser Schritt ist mit einer Verlagerung vorhandener IT-Infrastruktur in die öffentliche Cloud verbunden. Indem Sie die Optionen für die standortübergreifende Konnektivität nutzen, können Sie Ihre Azure Virtual Networks wie ein weiteres Subnetz in der lokalen Netzwerkinfrastruktur behandeln.

Zuerst müssen aber Planungs- und Entwurfsprobleme gelöst werden. Dies ist besonders in Bezug auf die Netzwerksicherheit wichtig. Sie können die Herangehensweise an ein Entwurfsproblem dieser Art am besten verstehen, indem Sie sich ein Beispiel ansehen.

Microsoft hat das [Datacenter Extension Reference Architecture Diagram (Diagramm: Referenzarchitektur zur Rechenzentrumserweiterung)](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84#content) und das dazugehörige Begleitmaterial erstellt, damit Sie besser verstehen, wie eine Rechenzentrumserweiterung aussieht. Es handelt sich um ein Beispiel für eine Referenzimplementierung, die Sie verwenden können, um für Ihr Unternehmen eine sichere Rechenzentrumserweiterung in die Cloud zu planen und zu entwerfen. Wir empfehlen Ihnen, sich dieses Dokument durchzulesen, um sich über die wichtigsten Komponenten einer sicheren Lösung zu informieren.

Weitere Informationen zur sicheren Erweiterung Ihres Rechenzentrums auf Azure erhalten Sie im Video [Extending your Datacenter to Microsoft Azure (Erweitern des Rechenzentrums auf Azure)](https://www.youtube.com/watch?v=Th1oQQCb2KA).
