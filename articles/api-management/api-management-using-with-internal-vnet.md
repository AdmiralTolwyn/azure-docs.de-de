---
title: Verwenden von Azure API Management mit internen virtuellen Netzwerken | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Azure API Management für ein internes virtuelles Netzwerk eingerichtet und konfiguriert wird.
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: da27c772a0650a923068b3c519ef39494573f96a
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793117"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Verwenden von Azure API Management mit einem internen virtuellen Netzwerk
Mit virtuellen Azure-Netzwerken kann API Management APIs verwalten, auf die nicht über das Internet zugegriffen werden kann. Für die Verbindungsherstellung stehen verschiedene VPN-Technologien zur Verfügung. API Management kann in einem virtuellen Netzwerk in zwei Hauptmodi bereitgestellt werden:
* Extern
* Intern

Wenn API Management im Modus für ein internes virtuelles Netzwerk bereitgestellt wird, sind alle Dienstendpunkte (Gateway, Entwicklerportal, Azure-Portal, direkte Verwaltung und Git) nur in einem virtuellen Netzwerk sichtbar, für das Sie den Zugriff steuern. Keiner der Dienstendpunkte ist auf dem öffentlichen DNS-Server registriert.

Mit API Management im internen Modus sind folgende Szenarien möglich:

* APIs, die in Ihrem privaten Datencenter gehostet werden, können externen Dritten über Site-to-Site- oder Azure ExpressRoute-VPN-Verbindungen sicher zugänglich gemacht werden.
* Das Verfügbarmachen von cloudbasierten und lokalen APIs über ein gemeinsames Gateway ermöglicht Hybrid Cloud-Szenarien.
* APIs, die an mehreren geografischen Standorten gehostet werden, können mit einem einzelnen Gatewayendpunkt verwaltet werden.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Voraussetzungen

Zum Ausführen der in diesem Artikel beschriebenen Schritte benötigen Sie Folgendes:

+ **Ein aktives Azure-Abonnement.**

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Eine API Management-Instanz.** Weitere Informationen finden Sie unter [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>Einrichten von API Management in einem internen virtuellen Netzwerk
Der API Management-Dienst in einem internen virtuellen Netzwerk wird hinter einem [internen Lastenausgleich (klassisch)](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud) gehostet. Dies ist die einzige verfügbare Option und kann nicht geändert werden.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Aktivieren einer Verbindung mit einem virtuellen Netzwerk über das Azure-Portal

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer Azure API Management-Instanz.
2. Wählen Sie **Virtuelles Netzwerk** aus.
3. Konfigurieren Sie die API Management-Instanz für die Bereitstellung innerhalb eines virtuellen Netzwerks.

    ![Menü zum Einrichten einer Azure API Management-Instanz in einem internen virtuellen Netzwerk][api-management-using-internal-vnet-menu]

4. Wählen Sie **Speichern** aus.

Nach erfolgreicher Bereitstellung sollten die **private** virtuelle IP-Adresse und die **öffentliche** virtuelle IP-Adresse Ihres API Management-Diensts auf dem Blatt „Übersicht“ angezeigt werden. Die **private** virtuelle IP-Adresse ist eine IP-Adresse mit Lastenausgleich in dem von API Management delegierten Subnetz, über die auf die Endpunkte `gateway`, `portal`, `management` und `scm` zugegriffen werden kann. Die **öffentliche** virtuelle IP-Adresse wird **nur** für den Datenverkehr auf Steuerungsebene an den Endpunkt `management` über Port 3443 verwendet. Sie kann über das [ApiManagement][ServiceTags]-Diensttag gesperrt werden.

![API Management-Dashboard mit konfiguriertem internem virtuellen Netzwerk][api-management-internal-vnet-dashboard]

> [!NOTE]
> Die im Azure-Portal verfügbare Testkonsole funktioniert nicht bei **intern** bereitgestelltem VNET-Dienst, da der Gateway-URI nicht unter öffentlichem DNS registriert ist. Verwenden Sie stattdessen die im **Entwicklerportal** bereitgestellte Testkonsole.

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Aktivieren einer Verbindung mit einem virtuellen Netzwerk mithilfe von PowerShell-Cmdlets

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Verbindungen mit virtuellen Netzwerken können auch mithilfe von PowerShell-Cmdlets aktiviert werden.

* Erstellen eines API Management-Diensts in einem virtuellen Netzwerk: Verwenden Sie das Cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement), um einen Azure API Management-Dienst in einem virtuellen Netzwerk zu erstellen und so zu konfigurieren, dass als Typ ein internes virtuelles Netzwerk verwendet wird.

* Aktualisieren einer Bereitstellung eines API Management-Diensts in einem virtuellen Netzwerk: Verwenden Sie das Cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion), um einen vorhandenen API Management-Dienst in ein virtuelles Netzwerk zu verschieben und so zu konfigurieren, dass als Typ ein internes virtuelles Netzwerk verwendet wird.

## <a name="apim-dns-configuration"></a>DNS-Konfiguration
Wenn sich API Management im Modus eines externen virtuellen Netzwerks befindet, wird DNS von Azure verwaltet. Beim Modus eines internen virtuellen Netzwerks müssen Sie das Routing selbst verwalten.

> [!NOTE]
> Der API Management-Dienst lauscht nicht auf von IP-Adressen stammende Anforderungen. Er reagiert nur auf Anforderungen für den Hostnamen, der für seine Dienstendpunkte konfiguriert ist. Zu diesen Endpunkten zählen das Gateway, das Azure-Portal und das Entwicklerportal, der Endpunkt für die direkte Verwaltung und Git.

### <a name="access-on-default-host-names"></a>Zugreifen über Standardhostnamen
Wenn Sie beispielsweise einen API Management-Dienst namens „contosointernalvnet“ erstellen, werden standardmäßig die folgenden Dienstendpunkte konfiguriert:

   * Gateway oder Proxy: contosointernalvnet.azure-api.net

   * Azure-Portal und Entwicklerportal: contosointernalvnet.portal.azure-api.net

   * Endpunkt für die direkte Verwaltung: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

Für den Zugriff auf diese API Management-Dienstendpunkte können Sie einen virtuellen Computer in einem Subnetz erstellen, das mit dem virtuellen Netzwerk verbunden ist, in dem API Management bereitgestellt wird. Wenn die interne virtuelle IP-Adresse für Ihren Dienst 10.1.0.5 lautet, können Sie die Hostdatei (%SystemDrive%\drivers\etc\hosts) wie folgt zuordnen:

   * 10.1.0.5     contosointernalvnet.azure-api.net

   * 10.1.0.5     contosointernalvnet.portal.azure-api.net

   * 10.1.0.5     contosointernalvnet.management.azure-api.net

   * 10.1.0.5     contosointernalvnet.scm.azure-api.net

Anschließend können Sie über den virtuellen Computer, den Sie erstellt haben, auf alle Dienstendpunkte zugreifen.
Wenn Sie einen benutzerdefinierten DNS-Server in einem virtuellen Netzwerk verwenden, können Sie auch DNS-A-Einträge erstellen und auf diese Endpunkte von überall in Ihrem virtuellen Netzwerk zugreifen.

### <a name="access-on-custom-domain-names"></a>Zugreifen über benutzerdefinierte Domänennamen

1. Wenn Sie auf den API Management-Dienst nicht mit den Standardhostnamen zugreifen möchten, können Sie benutzerdefinierte Domänennamen für alle Dienstendpunkte einrichten, wie in der folgenden Abbildung zu sehen:

   ![Einrichten einer benutzerdefinierten Domäne für API Management][api-management-custom-domain-name]

2. Anschließend können Sie A-Einträge auf Ihrem DNS-Server erstellen, um auf die Endpunkte zuzugreifen, auf die nur über das virtuelle Netzwerk zugegriffen werden kann.

## <a name="routing"> </a> Routing
+ Eine private virtuelle IP-Adresse mit Lastenausgleich aus dem Subnetzbereich wird reserviert und für den Zugriff auf die API Management-Dienstendpunkte aus dem VNet verwendet.
+ Eine öffentliche IP-Adresse (VIP) mit Lastenausgleich wird auch reserviert, um den Zugriff auf den Verwaltungsdienst-Endpunkt nur über Port 3443 zu ermöglichen.
+ Eine IP-Adresse aus einem Subnetz-IP-Bereich (DIP) wird für den Zugriff auf Ressourcen innerhalb des VNet und eine öffentliche IP-Adresse (VIP) für den Zugriff auf Ressourcen außerhalb des VNet verwendet.
+ Öffentliche und private IP-Adressen mit Lastenausgleich finden Sie auf dem Blatt „Übersicht/Essentials“ im Azure-Portal.

## <a name="related-content"></a>Verwandte Inhalte
Weitere Informationen finden Sie in den folgenden Artikeln:
* [Gängige Probleme mit der Netzwerkkonfiguration][Common network configuration problems]
* [Azure Virtual Network – häufig gestellte Fragen](../virtual-network/virtual-networks-faq.md)
* [Managing DNS Records](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10)) (Verwalten von DNS-Einträgen)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/security-overview.md#service-tags

