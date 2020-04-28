---
title: Integrieren von Microsoft Azure mit Oracle Cloud Infrastructure | Microsoft-Dokumentation
description: Hier finden Sie Informationen zu Lösungen, die in Microsoft Azure ausgeführte Oracle-Apps mit Datenbanken in Oracle Cloud Infrastructure (OCI) integrieren.
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: borisb
ms.custom: ''
ms.openlocfilehash: e70eedcfcdf548965b79e4a48a3a8bfa643f0396
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687427"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure"></a>Oracle-Anwendungslösungen mit Integration von Microsoft Azure und Oracle Cloud Infrastructure

Microsoft und Oracle haben sich zusammengetan, um cloudübergreifende Konnektivität mit geringer Wartezeit und hohem Durchsatz bereitzustellen, sodass Sie von den Vorteilen beider Clouds profitieren können. 

Dank dieser cloudübergreifenden Konnektivität können Sie eine mehrschichtige Anwendung partitionieren, um Ihre Datenbankschicht in Oracle Cloud Infrastructure (OCI) und die Anwendung sowie andere Schichten in Microsoft Azure auszuführen. Dies ist vergleichbar mit der Ausführung des gesamten Lösungsstapels in einer einzelnen Cloud. 

Wenn Sie Oracle-Lösungen vollständig in der Azure-Infrastruktur bereitstellen möchten, lesen Sie [Oracle VM images and their deployment on Microsoft Azure](oracle-vm-solutions.md) (Oracle-VM-Images und deren Bereitstellung in Microsoft Azure).

## <a name="scenario-overview"></a>Übersicht über das Szenario

Mit cloudübergreifender Konnektivität können Sie die branchenführenden Anwendungen von Oracle sowie Ihre eigenen benutzerdefinierten Anwendungen auf virtuellen Azure-Computern ausführen und gleichzeitig von den Vorteilen gehosteter Datenbankdienste in OCI profitieren. 

> [!IMPORTANT]
> Oracle zertifiziert diese Anwendungen für die Ausführung in Azure, wenn die Azure/Oracle Cloud Interconnect-Lösung bis Mai 2020 verwendet wird.

Zu den Anwendungen, die Sie in einer cloudübergreifenden Konfiguration ausführen können, zählen unter anderem:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle Retail-Anwendungen
* Oracle Hyperion Financial Management

Das folgende Diagramm bietet einen allgemeinen Überblick über die verbundene Lösung. Der Einfachheit halber enthält das Diagramm nur eine Logik- und eine Datenschicht. Ihre Lösung kann je nach Anwendungsarchitektur auch weitere Schichten enthalten – etwa eine Webschicht in Azure. Weitere Informationen finden Sie in den folgenden Abschnitten.

![Übersicht über die Azure-OCI-Lösung](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>Regionale Verfügbarkeit 

Die cloudübergreifende Konnektivität ist auf folgende Regionen beschränkt:
* Azure „USA, Osten“ (eastus) und OCI „Ashburn“ (USA, Osten)
* Azure „Vereinigtes Königreich, Süden“ (uksouth) und OCI „London“ (Vereinigtes Königreich, Süden)
* Azure „Kanada, Mitte“ (canadacentral) und OCI „Toronto“ (Kanada, Südosten)
* Azure „Europa, Westen“ (westeurope) und OCI „Amsterdam“ (Niederlande, Nordwesten)
* Azure „Japan, Osten“ (japaneast) und OCI „Tokyo“ (Japan, Osten)

## <a name="networking"></a>Netzwerk

Unternehmenskunden entscheiden sich aus verschiedenen geschäftlichen und betrieblichen Gründen oftmals für eine Diversifizierung und stellen Workloads über mehrere Clouds bereit. Zur Diversifizierung verbinden Kunden Cloudnetzwerke über das Internet, per IPSec-VPN oder mithilfe der Direktverbindungslösung des Cloudanbieters über Ihr lokales Netzwerk. Die Verbindungsherstellung zwischen Cloudnetzwerken kann einen erheblichen Zeit- und Kostenaufwand bedeuten und hohe Investitionen für Entwurf, Beschaffung, Installation, Tests und Betrieb erfordern. 

Aus diesem Grund haben Oracle und Microsoft eine integrierte Multi-Cloud-Umgebung entwickelt. Zur Schaffung eines cloudübergreifenden Netzwerks wird eine Verbindung zwischen [ExpressRoute](../../../expressroute/expressroute-introduction.md) in Microsoft Azure und [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) in OCI hergestellt. Diese Konnektivität ist möglich, wenn sich ein Azure ExpressRoute-Peeringstandort in der Nähe oder am gleichen Peeringstandort befindet wie OCI FastConnect. Dies ermöglicht eine sichere und schnelle Verbindung zwischen den beiden Clouds ohne zusätzlichen Dienstanbieter.

Mit ExpressRoute und FastConnect können Kunden mittels Peering ein virtuelles Netzwerk in Azure mit einem virtuellen Cloudnetzwerk in OCI kombinieren – vorausgesetzt, der private IP-Adressbereich überschneidet sich nicht. Dank des Peerings der beiden Netzwerke kann eine Ressource im virtuellen Netzwerk mit einer Ressource im virtuellen Cloudnetzwerk von OCI kommunizieren, als befänden sich beide im gleichen Netzwerk.

## <a name="network-security"></a>Netzwerksicherheit

Netzwerksicherheit ist ein wichtiger Bestandteil jeder Unternehmensanwendung und ein zentraler Aspekt dieser Multi-Cloud-Lösung. Sämtlicher Datenverkehr über ExpressRoute und FastConnect wird über ein privates Netzwerk abgewickelt. Diese Konfiguration ermöglicht die sichere Kommunikation zwischen einem virtuellen Azure-Netzwerk und einem virtuellen Cloudnetzwerk von Oracle. Sie müssen keine öffentliche IP-Adresse für einen virtuellen Computer in Azure angeben. Und Sie benötigen auch kein Internetgateway in OCI. Die gesamte Kommunikation erfolgt über die private IP-Adresse der Computer.

Darüber hinaus können Sie [Sicherheitslisten](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) für Ihr virtuelles Cloudnetzwerk in OCI sowie Sicherheitsregeln einrichten, die mit [Netzwerksicherheitsgruppen](../../../virtual-network/security-overview.md) in Azure verknüpft sind. Diese Regeln dienen zum Steuern des Datenverkehrsfluss zwischen Computern in den virtuellen Netzwerken. Netzwerksicherheitsregeln können auf Computerebene, auf Subnetzebene sowie auf der Ebene des virtuellen Netzwerks hinzugefügt werden.
 
## <a name="identity"></a>Identity

Identität ist einer der Grundpfeiler der Partnerschaft zwischen Microsoft und Oracle. Für die Integration von [Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) und [Azure Active Directory](../../../active-directory/index.yml) (Azure AD) wurde ein erheblicher Aufwand betrieben. Azure AD ist der cloudbasierte Identitäts- und Zugriffsverwaltungsdienst von Microsoft. Er unterstützt Ihre Benutzer bei der Anmeldung sowie beim Zugriff auf verschiedene Ressourcen. Darüber hinaus können Sie mithilfe von Azure AD Ihre Benutzer und deren Berechtigungen verwalten.

Diese Integration ermöglicht aktuell die Verwaltung an einem zentralen Ort (sprich: in Azure Active Directory). Azure AD synchronisiert sämtliche Änderungen im Verzeichnis mit dem entsprechenden Oracle-Verzeichnis und wird für einmaliges Anmelden bei cloudübergreifenden Oracle-Lösungen verwendet.

## <a name="next-steps"></a>Nächste Schritte

Erste Schritte mit einem [cloudübergreifenden Netzwerk](configure-azure-oci-networking.md) zwischen Azure und OCI. 

Weitere Informationen und Whitepaper zu OCI finden Sie in der [Dokumentation zu Oracle Cloud Infrastructure](https://docs.cloud.oracle.com/iaas/Content/home.htm).
