---
title: Übersicht über Azure-Sicherheitsgruppen
titlesuffix: Azure Virtual Network
description: Hier finden Sie Informationen zu Netzwerk- und Anwendungssicherheitsgruppen. Mithilfe von Sicherheitsgruppen können Sie den Netzwerkdatenverkehr zwischen Azure-Ressourcen filtern.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2018
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: ca4908e642644ccbf349841d143bfcc18e944025
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305838"
---
# <a name="security-groups"></a>Sicherheitsgruppen
<a name="network-security-groups"></a>

Sie können Netzwerkdatenverkehr von und zu Azure-Ressourcen in einem [virtuellen Azure-Netzwerk](virtual-networks-overview.md) mithilfe einer Netzwerksicherheitsgruppe filtern. Eine Netzwerksicherheitsgruppe enthält [Sicherheitsregeln](#security-rules), die eingehenden Netzwerkdatenverkehr an verschiedene Typen von Azure-Ressourcen oder ausgehenden Netzwerkdatenverkehr von diesen zulassen oder verweigern. Weitere Informationen über die Azure-Ressourcen, die in einem virtuellen Netzwerk bereitgestellt werden können und denen Netzwerksicherheitsgruppen zugeordnet sind, finden Sie unter [Integration virtueller Netzwerke für Azure-Dienste](virtual-network-for-azure-services.md). Für jede Regel können Sie die Quelle, das Ziel, den Port und das Protokoll angeben.

In diesem Artikel werden Konzepte für Netzwerksicherheitsgruppen erklärt, damit Sie diese effektiv verwenden können. Wenn Sie noch nie eine Netzwerksicherheitsgruppe erstellt haben, gehen Sie dieses kurze [Tutorial](tutorial-filter-network-traffic.md) durch, um sich mit dem Erstellen von Netzwerksicherheitsgruppen vertraut zu machen. Wenn Sie sich mit Netzwerksicherheitsgruppen auskennen und diese verwalten müssen, finden Sie unter [Erstellen, Ändern oder Löschen einer Netzwerksicherheitsgruppe](manage-network-security-group.md) weitere Informationen. Wenn Kommunikationsschwierigkeiten auftreten und Sie Probleme mit Netzwerksicherheitsgruppen beheben müssen, finden Sie unter [Diagnostizieren von Problemen mit dem Filter für Netzwerkdatenverkehr eines virtuellen Computers](diagnose-network-traffic-filter-problem.md) weitere Informationen. Sie können [Netzwerksicherheitsgruppen-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zum [Analysieren des Netzwerkdatenverkehrs](../network-watcher/traffic-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zu und von Ressourcen verwenden, denen eine Netzwerksicherheitsgruppe zugeordnet ist.

## <a name="security-rules"></a>Sicherheitsregeln

Eine Netzwerksicherheitsgruppe kann – innerhalb der [Grenzwerte](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) des Azure-Abonnements – null oder mehr Regeln enthalten. Für jede Regel werden die folgenden Eigenschaften angegeben:

|Eigenschaft  |Erklärung  |
|---------|---------|
|NAME|Ein eindeutiger Name in der Netzwerksicherheitsgruppe.|
|Priorität | Eine Zahl zwischen 100 und 4.096. Regeln werden in der Reihenfolge ihrer Priorität verarbeitet. Regeln mit niedrigeren Zahlen werden vor Regeln mit höheren Zahlen verarbeitet, weil die Priorität für niedrigere Zahlen höher ist. Nachdem sich für den Datenverkehr eine Übereinstimmung mit einer Regel ergibt, wird die Verarbeitung angehalten. Daher werden alle Regeln mit niedrigerer Priorität (höherer Zahl), die über die gleichen Attribute wie Regeln mit höheren Prioritäten verfügen, nicht verarbeitet.|
|Quelle oder Ziel| Beliebiges Element oder eine einzelne IP-Adresse, ein CIDR-Block (klassenloses domänenübergreifendes Routing, z.B. 10.0.0.0/24), ein [Diensttag](#service-tags) oder eine [Anwendungssicherheitsgruppe](#application-security-groups). Wenn Sie eine Adresse für eine Azure-Ressource angeben, geben Sie die private IP-Adresse an, die der Ressource zugewiesen ist. Netzwerksicherheitsgruppen werden verarbeitet, nachdem Azure eine öffentliche IP-Adresse in eine private IP-Adresse für eingehenden Datenverkehr übersetzt hat und bevor Azure eine private IP-Adresse in eine öffentliche IP-Adresse für ausgehenden Datenverkehr übersetzt. Erfahren Sie mehr über Azure-[IP-Adressen](virtual-network-ip-addresses-overview-arm.md). Durch das Angeben eines Bereichs, eines Diensttags oder einer Anwendungssicherheitsgruppe haben Sie die Möglichkeit, weniger Sicherheitsregeln zu erstellen. Die Option zum Angeben mehrerer einzelner IP-Adressen und Bereiche (die Angabe mehrerer Diensttags oder Anwendungsgruppen ist nicht zulässig) in einer Regel wird als [Ergänzte Sicherheitsregeln](#augmented-security-rules) bezeichnet. Ergänzte Sicherheitsregeln können nur in Netzwerksicherheitsgruppen erstellt werden, die mit dem Resource Manager-Bereitstellungsmodell erstellt wurden. Es ist nicht möglich, mehrere IP-Adressen und IP-Adressbereiche in Netzwerksicherheitsgruppen anzugeben, die mit dem klassischen Bereitstellungsmodell erstellt wurden. Erfahren Sie mehr über [Azure-Bereitstellungsmodelle](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|Protocol     | „TCP“, „UDP“, „ICMP“ oder „Beliebig“.|
|Direction| Gibt an, ob die Regel für ein- oder ausgehenden Datenverkehr gilt.|
|Portbereich     |Sie können einen einzelnen Port oder einen Bereich mit Ports angeben. Mögliche Angaben sind beispielsweise „80“ oder „10.000 - 10.005“. Das Angeben von Bereichen ermöglicht Ihnen die Erstellung von weniger Sicherheitsregeln. Ergänzte Sicherheitsregeln können nur in Netzwerksicherheitsgruppen erstellt werden, die mit dem Resource Manager-Bereitstellungsmodell erstellt wurden. In Netzwerksicherheitsgruppen, die mit dem klassischen Bereitstellungsmodell erstellt wurden, können Sie in derselben Sicherheitsregel nicht mehrere Ports oder Portbereiche angeben.   |
|Aktion     | Zulassen oder Verweigern        |

Netzwerksicherheitsgruppen-Sicherheitsregeln werden nach Priorität anhand der 5-Tupel-Informationen (Quelle, Quellport, Ziel, Zielport und Protokoll) ausgewertet, um den Datenverkehr zuzulassen oder zu verweigern. Für vorhandene Verbindungen wird ein Flussdatensatz erstellt. Die Kommunikation wird basierend auf dem Verbindungszustand der Flussdatensätze zugelassen oder verweigert. Der Flussdatensatz ermöglicht es, dass eine Netzwerksicherheitsgruppe zustandsbehaftet ist. Wenn Sie beispielsweise eine Sicherheitsregel für Datenverkehr in ausgehender Richtung an eine beliebige Adresse über Port 80 angeben, ist es nicht erforderlich, für die Antwort auf den ausgehenden Datenverkehr eine Sicherheitsregel für Datenverkehr in eingehender Richtung anzugeben. Sie müssen nur dann eine Sicherheitsregel für Datenverkehr in eingehender Richtung angeben, wenn die Kommunikation extern initiiert wird. Dies gilt auch für den umgekehrten Fall. Wenn eingehender Datenverkehr über einen Port zugelassen wird, ist es nicht erforderlich, für die Beantwortung des Datenverkehrs über den Port eine Sicherheitsregel für Datenverkehr in ausgehender Richtung anzugeben.
Vorhandene Verbindungen können nicht unterbrochen werden, wenn Sie eine Sicherheitsregel entfernen, die den Datenfluss ermöglicht hat. Datenverkehrsflüsse werden unterbrochen, wenn die Verbindungen beendet wurden und in beide Richtungen mindestens einige Minuten lang kein Datenverkehr besteht.

Es gibt Beschränkungen für die Anzahl von Sicherheitsregeln, die Sie in einer Netzwerksicherheitsgruppe erstellen können. Ausführliche Informationen finden Sie im Artikel zu den [Einschränkungen für Azure-Abonnements](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="augmented-security-rules"></a>Ergänzte Sicherheitsregeln

Mit ergänzten Sicherheitsregeln wird die Sicherheitsdefinition für virtuelle Netzwerke vereinfacht, da Sie umfangreichere und komplexe Netzwerksicherheitsregeln mit weniger Regeln definieren können. Sie können mehrere Ports und mehrere explizite IP-Adressen und Bereiche zu einer einzelnen Sicherheitsregel zusammenfassen, die leicht verständlich ist. Verwenden Sie ergänzte Regeln in den Feldern für die Quelle, das Ziel und den Port einer Regel. Kombinieren Sie ergänzte Sicherheitsregeln mit [Diensttags](#service-tags) oder [Anwendungssicherheitsgruppen](#application-security-groups), um die Wartung Ihrer Sicherheitsregeldefinition zu vereinfachen. Es gibt Beschränkungen für die Anzahl von Adressen, Bereichen und Ports, die Sie in einer Regel angeben können. Ausführliche Informationen finden Sie im Artikel zu den [Einschränkungen für Azure-Abonnements](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="service-tags"></a>Diensttags

Ein Diensttag steht für eine Gruppe von IP-Adressen und hat die Aufgabe, bei der Erstellung von Sicherheitsregeln die Komplexität zu verringern. Sie können kein eigenes Diensttag erstellen und auch nicht angeben, welche IP-Adressen in einem Tag enthalten sind. Microsoft verwaltet die Adresspräfixe, die mit dem Diensttag abgedeckt werden, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. 

Die folgenden Diensttags können in [Netzwerksicherheitsgruppen-Regeln](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) verwendet werden. Diensttags mit Sternchen am Ende (d. h. „AzureCloud*“) können auch in [Azure Firewall-Netzwerkregeln](https://docs.microsoft.com/azure/firewall/service-tags) verwendet werden. 

* **VirtualNetwork** (Resource Manager) (**VIRTUAL_NETWORK** für das klassische Bereitstellungsmodell): Dieses Tag enthält den VM-Adressraum (alle für das virtuelle Netzwerk definierten CIDR-Bereiche), alle verbundenen lokalen Adressräume und [per Peering verknüpfte](virtual-network-peering-overview.md) virtuelle Netzwerke oder virtuelle Netzwerke, die mit einem [Gateway des virtuellen Netzwerks](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) und Adresspräfixen verbunden sind, die für [benutzerdefinierte Routen](virtual-networks-udr-overview.md) verwendet werden. Denken Sie daran, dass dieses Tag die Standardroute enthalten kann. 
* **AzureLoadBalancer** (Resource Manager) (**AZURE_LOADBALANCER** für das klassische Bereitstellungsmodell): Dieses Tag gibt den Lastenausgleich der Azure-Infrastruktur an. Das Tag wird in eine [virtuelle IP-Adresse des Hosts](security-overview.md#azure-platform-considerations) (168.63.129.16) umgewandelt, die als Ausgangspunkt für die Integritätstests von Azure fungiert. Sie können diese Regel außer Kraft setzen, wenn Sie den Lastenausgleich von Azure nicht verwenden.
* **Internet** (Resource Manager) (**INTERNET** für das klassische Bereitstellungsmodell): Dieses Tag gibt den IP-Adressraum an, der außerhalb des virtuellen Netzwerks liegt und über das öffentliche Internet erreichbar ist. Der Adressbereich schließt den [Azure-eigenen öffentlichen IP-Adressraum](https://www.microsoft.com/download/details.aspx?id=41653) ein.
* **AzureCloud*** (nur Resource Manager): Dieses Tag gibt den IP-Adressraum für Azure an, einschließlich aller [öffentlichen IP-Adressen für das Datencenter](https://www.microsoft.com/download/details.aspx?id=41653). Wenn Sie *AzureCloud* als Wert angeben, wird der Datenverkehr für öffentliche Azure-IP-Adressen zugelassen oder verweigert. Falls Sie den Zugriff auf AzureCloud nur in einer bestimmten [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region im folgenden Format angeben: AzureCloud.[Name der Region]. Dieses Tag wird für ausgehende Sicherheitsregeln empfohlen. 
* **AzureTrafficManager*** (nur Resource Manager): Dieses Tag gibt den IP-Adressraum für die Test-IP-Adressen des Azure Traffic Manager an. Weitere Informationen zu Test-IP-Adressen von Traffic Manager finden Sie unter [Häufig gestellte Fragen (FAQ) zu Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). Dieses Tag wird für eingehende Sicherheitsregeln empfohlen.  
* **Storage*** (nur Resource Manager): Dieses Tag gibt den IP-Adressraum für den Azure Storage-Dienst an. Wenn Sie *Storage* als Wert angeben, wird der Datenverkehr für den Speicher zugelassen oder verweigert. Falls Sie den Zugriff auf Speicher nur in einer bestimmten [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region im folgenden Format angeben: Speicher.[Name der Region]. Das Tag steht für den Dienst, aber nicht für bestimmte Instanzen des Diensts. Beispielsweise steht das Tag für den Azure Storage-Dienst, aber nicht für ein bestimmtes Azure Storage-Konto. Dieses Tag wird für ausgehende Sicherheitsregeln empfohlen. 
* **Sql*** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe der Azure SQL-Datenbank-, Azure Database for MySQL-, Azure Database for PostgreSQL- und Azure SQL Data Warehouse-Dienste an. Wenn Sie *Sql* als Wert angeben, wird der Datenverkehr für Sql zugelassen oder verweigert. Falls Sie den Zugriff auf Sql nur in einer bestimmten [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region im folgenden Format angeben: Sql.[Name der Region]. Das Tag steht für den Dienst, aber nicht für bestimmte Instanzen des Diensts. Beispielsweise steht das Tag für den Azure SQL-Datenbank-Dienst, aber nicht für eine bestimmte SQL-Datenbank oder einen bestimmten SQL-Server. Dieses Tag wird für ausgehende Sicherheitsregeln empfohlen. 
* **AzureCosmosDB*** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Azure Cosmos DB-Diensts an. Wenn Sie *AzureCosmosDB* als Wert angeben, wird der Datenverkehr für AzureCosmosDB zugelassen oder verweigert. Falls Sie den Zugriff auf AzureCosmosDB nur in einer bestimmten [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region im folgenden Format angeben: AzureCosmosDB.[Name der Region]. Dieses Tag wird für ausgehende Sicherheitsregeln empfohlen. 
* **AzureKeyVault*** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Azure Key Vault-Diensts an. Wenn Sie *AzureKeyVault* als Wert angeben, wird der Datenverkehr für AzureKeyVault zugelassen oder verweigert. Falls Sie den Zugriff auf AzureKeyVault nur in einer bestimmten [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region im folgenden Format angeben: AzureKeyVault.[Name der Region]. Dieses Tag weist eine Abhängigkeit vom Tag **AzureActiveDirectory** auf. Dieses Tag wird für ausgehende Sicherheitsregeln empfohlen.  
* **EventHub*** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Azure Event Hub-Diensts an. Wenn Sie *EventHub* als Wert angeben, wird der Datenverkehr für EventHub zugelassen oder verweigert. Falls Sie den Zugriff auf EventHub nur in einer bestimmten [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region im folgenden Format angeben: EventHub.[Name der Region]. Dieses Tag wird für ausgehende Sicherheitsregeln empfohlen. 
* **ServiceBus*** (nur Resource Manager): Mit diesem Tag werden die Adresspräfixe des ServiceBus-Diensts von Azure bei Verwendung der Premium-Dienstebene angegeben. Wenn Sie *ServiceBus* als Wert angeben, wird der Datenverkehr für ServiceBus zugelassen oder verweigert. Falls Sie den Zugriff auf ServiceBus nur in einer bestimmten [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region im folgenden Format angeben: ServiceBus.[Name der Region]. Dieses Tag wird für ausgehende Sicherheitsregeln empfohlen. 
* **MicrosoftContainerRegistry*** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Microsoft Container Registry-Diensts an. Wenn Sie *MicrosoftContainerRegistry* als Wert angeben, wird der Datenverkehr für MicrosoftContainerRegistry zugelassen oder verweigert. Falls Sie den Zugriff auf MicrosoftContainerRegistry nur in einer bestimmten [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region im folgenden Format angeben: MicrosoftContainerRegistry.[Name der Region]. Dieses Tag wird für ausgehende Sicherheitsregeln empfohlen. 
* **AzureContainerRegistry*** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Azure Container Registry-Diensts an. Wenn Sie *AzureContainerRegistry* als Wert angeben, wird der Datenverkehr für AzureContainerRegistry zugelassen oder verweigert. Falls Sie den Zugriff auf AzureContainerRegistry nur in einer bestimmten [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region im folgenden Format angeben: AzureContainerRegistry.[Name der Region]. Dieses Tag wird für ausgehende Sicherheitsregeln empfohlen. 
* **AppService*** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Azure App Service-Diensts an. Wenn Sie *AppService* als Wert angeben, wird der Datenverkehr für AppService zugelassen oder verweigert. Falls Sie den Zugriff auf AppService nur in einer bestimmten [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region im folgenden Format angeben: AppService.[Name der Region]. Dieses Tag wird für ausgehende Sicherheitsregeln zu WebApps-Front-Ends empfohlen.  
* **AppServiceManagement*** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Verwaltungsdatenverkehrs für dedizierte App Service-Umgebungsbereitstellungen an. Wenn Sie *AppServiceManagement* als Wert angeben, wird der Datenverkehr für AppServiceManagement zugelassen oder verweigert. Dieses Tag wird für ein-/ausgehende Sicherheitsregeln empfohlen. 
* **ApiManagement*** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Verwaltungsdatenverkehrs für dedizierte APIM-Bereitstellungen an. Wenn Sie *ApiManagement* als Wert angeben, wird der Datenverkehr für ApiManagement zugelassen oder verweigert. Dieses Tag wird für ein-/ausgehende Sicherheitsregeln empfohlen. 
* **AzureConnectors*** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe der Logic Apps-Connectors für Test-/Back-End-Verbindungen an. Wenn Sie *AzureConnectors* als Wert angeben, wird der Datenverkehr für AzureConnectors zugelassen oder verweigert. Falls Sie den Zugriff auf AzureConnectors nur in einer bestimmten [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region im folgenden Format angeben: AzureConnectors.[Name der Region]. Dieses Tag wird für eingehende Sicherheitsregeln empfohlen. 
* **GatewayManager** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Verwaltungsdatenverkehrs für dedizierte VPN-/App Gateway-Bereitstellungen an. Wenn Sie *GatewayManager* als Wert angeben, wird der Datenverkehr für GatewayManager zugelassen oder verweigert. Dieses Tag wird für eingehende Sicherheitsregeln empfohlen. 
* **AzureDataLake*** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Azure Data Lake-Diensts an. Wenn Sie *AzureDataLake* als Wert angeben, wird der Datenverkehr für AzureDataLake zugelassen oder verweigert. Dieses Tag wird für ausgehende Sicherheitsregeln empfohlen. 
* **AzureActiveDirectory*** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Azure Active Directory-Diensts an. Wenn Sie *AzureActiveDirectory* als Wert angeben, wird der Datenverkehr für AzureActiveDirectory zugelassen oder verweigert. Dieses Tag wird für ausgehende Sicherheitsregeln empfohlen.
* **AzureMonitor*** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe der Log Analytics-, App Insights-, AzMon- und benutzerdefinierter Metriken (GiG-Endpunkte) an. Wenn Sie *AzureMonitor* als Wert angeben, wird der Datenverkehr für AzureMonitor zugelassen oder verweigert. Für Log Analytics weist dieses Tag eine Abhängigkeit von dem Tag **Storage** auf. Dieses Tag wird für ausgehende Sicherheitsregeln empfohlen.
* **ServiceFabric*** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des ServiceFabric-Diensts an. Wenn Sie *ServiceFabric* als Wert angeben, wird der Datenverkehr für ServiceFabric zugelassen oder verweigert. Dieses Tag wird für ausgehende Sicherheitsregeln empfohlen. 
* **AzureMachineLearning*** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des AzureMachineLearning-Diensts an. Wenn Sie *AzureMachineLearning* als Wert angeben, wird der Datenverkehr für AzureMachineLearning zugelassen oder verweigert. Dieses Tag wird für ausgehende Sicherheitsregeln empfohlen. 
* **BatchNodeManagement*** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Verwaltungsdatenverkehrs für dedizierte Azure Batch-Bereitstellungen an. Wenn Sie als Wert *BatchNodeManagement* angeben, wird Datenverkehr vom Batch-Dienst zu den Computeknoten zugelassen oder verweigert. Dieses Tag wird für ein-/ausgehende Sicherheitsregeln empfohlen. 
* **AzureBackup*** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des AzureBackup-Diensts an. Wenn Sie *AzureBackup* als Wert angeben, wird der Datenverkehr für AzureBackup zugelassen oder verweigert. Dieses Tag weist eine Abhängigkeit von den Tags **Storage** und **AzureActiveDirectory** auf. Dieses Tag wird für ausgehende Sicherheitsregeln empfohlen. 
* **AzureActiveDirectoryDomainServices*** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Verwaltungsdatenverkehrs für dedizierte Azure Active Directory Domain Services-Bereitstellungen an. Wenn Sie *AzureActiveDirectoryDomainServices* als Wert angeben, wird der Datenverkehr für AzureActiveDirectoryDomainServices zugelassen oder verweigert. Dieses Tag wird für ein-/ausgehende Sicherheitsregeln empfohlen.  
* **SqlManagement*** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Verwaltungsdatenverkehrs für dedizierte SQL-Bereitstellungen an. Wenn Sie *SqlManagement* als Wert angeben, wird der Datenverkehr für SqlManagement zugelassen oder verweigert. Dieses Tag wird für ein-/ausgehende Sicherheitsregeln empfohlen. 
* **CognitiveServicesManagement** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Datenverkehrs für Cognitive Services an. Wenn Sie *CognitiveServicesManagement* als Wert angeben, wird der Datenverkehr für CognitiveServicesManagement zugelassen oder verweigert. Dieses Tag wird für ausgehende Sicherheitsregeln empfohlen.  
* **Dynamics365ForMarketingEmail** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Marketing-E-Mail-Diensts von Dynamics 365 an. Wenn Sie *Dynamics365ForMarketingEmail* als Wert angeben, wird der Datenverkehr für Dynamics365ForMarketingEmail zugelassen oder verweigert. Falls Sie den Zugriff auf Dynamics365ForMarketingEmail nur in einer bestimmten [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region im folgenden Format angeben: Dynamics365ForMarketingEmail.[Name der Region].
* **AzurePlatformDNS** (nur Resource Manager): Dieses Tag gibt DNS, einen grundlegenden Infrastrukturdienst, an. Wenn Sie *AzurePlatformDNS* als Wert angeben, können Sie die standardmäßigen [Aspekte der Azure-Plattform](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) für DNS deaktivieren. Verwenden Sie dieses Tag mit Vorsicht. Es wird empfohlen, vor Verwendung dieses Tags Tests durchzuführen. 
* **AzurePlatformIMDS** (nur Resource Manager): Dieses Tag gibt IMDS, einen grundlegenden Infrastrukturdienst, an. Wenn Sie *AzurePlatformIMDS* als Wert angeben, können Sie die standardmäßigen [Aspekte der Azure-Plattform](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) für IMDS deaktivieren. Verwenden Sie dieses Tag mit Vorsicht. Es wird empfohlen, vor Verwendung dieses Tags Tests durchzuführen. 
* **AzurePlatformLKM** (nur Resource Manager): Dieses Tag gibt Windows-Lizenzierung oder den Schlüsselverwaltungsdienst an. Wenn Sie *AzurePlatformLKM* als Wert angeben, können Sie die standardmäßigen [Aspekte der Azure-Plattform](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) für die Lizenzierung deaktivieren. Verwenden Sie dieses Tag mit Vorsicht. Es wird empfohlen, vor Verwendung dieses Tags Tests durchzuführen. 

> [!NOTE]
> Mit Diensttags von Azure-Diensten werden die Adresspräfixe der spezifischen verwendeten Cloud angegeben. 

> [!NOTE]
> Wenn Sie einen [VNET-Dienstendpunkt](virtual-network-service-endpoints-overview.md) für einen Dienst implementieren, z. B. Azure Storage oder Azure SQL-Datenbank, fügt Azure eine [Route](virtual-networks-udr-overview.md#optional-default-routes) zu einem Subnetz des virtuellen Netzwerks für den Dienst hinzu. Die Adresspräfixe für die Route sind die gleichen Adresspräfixe bzw. CIDR-Bereiche wie für das entsprechende Diensttag.

### <a name="service-tags-in-on-premises"></a>Diensttags in lokalen Firewalls  
Sie können die Liste mit den Diensttags mit Präfixdetails über die folgenden wöchentlichen Veröffentlichungen für Azure-Clouds herunterladen und in eine lokale Firewall integrieren: [öffentlich](https://www.microsoft.com/download/details.aspx?id=56519), [US Government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) und [Deutschland](https://www.microsoft.com/download/details.aspx?id=57064).

Sie können diese Informationen auch mithilfe der **Diensttagermittlungs-API** (öffentliche Vorschau) – [REST](https://aka.ms/discoveryapi_rest), [Azure PowerShell](https://aka.ms/discoveryapi_powershell), und [Azure CLI](https://aka.ms/discoveryapi_cli) programmgesteuert abrufen. 

> [!NOTE]
> Die folgenden wöchentlichen Veröffentlichungen (alte Version) für die Azure-Clouds [Öffentlich](https://www.microsoft.com/en-us/download/details.aspx?id=41653), [China](https://www.microsoft.com/en-us/download/details.aspx?id=42064) und [Deutschland](https://www.microsoft.com/en-us/download/details.aspx?id=54770) werden am 30. Juni 2020 eingestellt. Beginnen Sie bitte mit der Verwendung der aktualisierten Veröffentlichungen wie oben beschrieben. 

## <a name="default-security-rules"></a>Standardsicherheitsregeln

Azure erstellt in jeder von Ihnen erstellten Netzwerksicherheitsgruppe die folgenden Standardregeln:

### <a name="inbound"></a>Eingehend

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|Priorität|`Source`|Quellports|Ziel|Zielports|Protocol|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0 - 65535|VirtualNetwork|0 - 65535|Beliebig|ZULASSEN|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Priorität|`Source`|Quellports|Ziel|Zielports|Protocol|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0 - 65535|0.0.0.0/0|0 - 65535|Beliebig|ZULASSEN|

#### <a name="denyallinbound"></a>DenyAllInbound

|Priorität|`Source`|Quellports|Ziel|Zielports|Protocol|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0 - 65535|0.0.0.0/0|0 - 65535|Beliebig|Verweigern|

### <a name="outbound"></a>Ausgehend

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Priorität|`Source`|Quellports| Ziel | Zielports | Protocol | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0 - 65535 | VirtualNetwork | 0 - 65535 | Beliebig | ZULASSEN |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Priorität|`Source`|Quellports| Ziel | Zielports | Protocol | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0 - 65535 | Internet | 0 - 65535 | Beliebig | ZULASSEN |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|Priorität|`Source`|Quellports| Ziel | Zielports | Protocol | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0 - 65535 | 0.0.0.0/0 | 0 - 65535 | Beliebig | Verweigern |

In den Spalten **Quelle** und **Ziel** handelt es sich bei *VirtualNetwork*, *AzureLoadBalancer* und *Internet* um [Diensttags](#service-tags) und nicht um IP-Adressen. In der Protokollspalte steht **Beliebig** für TCP, UDP und ICMP. Beim Erstellen einer Regel können Sie „TCP“, „UDP“, „ICMP“ oder „Beliebig“ angeben. *0.0.0.0/0* in den Spalten **Quelle** und **Ziel** steht für alle Adressen. Clients wie Azure-Portal, Azure-Befehlszeilenschnittstelle oder PowerShell können „*“ oder „any“ für diesen Ausdruck verwenden.
 
Sie können die Standardregeln nicht entfernen, aber Sie können sie außer Kraft setzen, indem Sie Regeln mit höheren Prioritäten erstellen.

## <a name="application-security-groups"></a>Anwendungssicherheitsgruppen

Mit Anwendungssicherheitsgruppen können Sie die Netzwerksicherheit als natürliche Erweiterung einer Anwendungsstruktur konfigurieren und virtuelle Computer gruppieren sowie auf der Grundlage dieser Gruppen Netzwerksicherheitsrichtlinien definieren. Sie können Ihre Sicherheitsrichtlinie nach Bedarf wiederverwenden, ohne dass Sie explizite IP-Adressen manuell warten müssen. Die Plattform übernimmt die komplexe Verarbeitung von expliziten IP-Adressen und mehreren Regelsätzen, damit Sie sich auf Ihre Geschäftslogik konzentrieren können. Das folgende Beispiel bietet Ihnen ein besseres Verständnis von Anwendungssicherheitsgruppen:

![Anwendungssicherheitsgruppen](./media/security-groups/application-security-groups.png)

In der Abbildung oben sind *NIC1* und *NIC2* Mitglieder der Anwendungssicherheitsgruppe *AsgWeb*. *NIC3* ist ein Mitglied der Anwendungssicherheitsgruppe *AsgLogic*. *NIC4* ist ein Mitglied der Anwendungssicherheitsgruppe *AsgDb*. Obwohl jede Netzwerkschnittstelle in diesem Beispiel Mitglied von nur einer Anwendungssicherheitsgruppe ist, kann eine Netzwerkschnittstelle Mitglied mehrerer Anwendungssicherheitsgruppen sein. Dabei gelten die [Einschränkungen für Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Keiner der Netzwerkschnittstellen ist eine Netzwerksicherheitsgruppe zugeordnet. *NSG1* ist beiden Subnetzen zugeordnet und enthält die folgenden Regeln:

### <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

Diese Regel ist erforderlich, um Datenverkehr aus dem Internet an die Webserver zuzulassen. Da eingehender Datenverkehr aus dem Internet durch die Standardsicherheitsregel [DenyAllInbound](#denyallinbound) verweigert wird, ist keine zusätzliche Regel für die Anwendungssicherheitsgruppen *AsgLogic* oder *AsgDb* erforderlich.

|Priorität|`Source`|Quellports| Ziel | Zielports | Protocol | Access |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | ZULASSEN |

### <a name="deny-database-all"></a>Deny-Database-All

Da die Standardsicherheitsregel [AllowVNetInBound](#allowvnetinbound) die gesamte Kommunikation zwischen Ressourcen im gleichen virtuellen Netzwerk erlaubt, ist diese Regel erforderlich, um den Datenverkehr von allen Ressourcen zu verweigern.

|Priorität|`Source`|Quellports| Ziel | Zielports | Protocol | Access |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Beliebig | Verweigern |

### <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

Diese Regel lässt Datenverkehr von der Anwendungssicherheitsgruppe *AsgLogic* zur Anwendungssicherheitsgruppe *AsgDb* zu. Die Priorität für diese Regel ist höher als die Priorität für die Regel *Deny-Database-All*. Daher wird diese Regel vor der Regel *Deny-Database-All* verarbeitet, sodass Datenverkehr von den Anwendungssicherheitsgruppen *AsgLogic* zulässig ist, während der andere Datenverkehr blockiert wird.

|Priorität|`Source`|Quellports| Ziel | Zielports | Protocol | Access |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | ZULASSEN |

Regeln, in denen eine Anwendungssicherheitsgruppe als Quelle oder Ziel angegeben ist, werden nur auf Netzwerkschnittstellen angewendet, bei denen es sich um Mitglieder der Anwendungssicherheitsgruppe handelt. Wenn die Netzwerkschnittstelle nicht Mitglied einer Anwendungssicherheitsgruppe ist, wird die Regel nicht auf die Netzwerkschnittstelle angewendet, auch wenn die Netzwerksicherheitsgruppe dem Subnetz zugeordnet ist.

Für Anwendungssicherheitsgruppen gelten folgende Einschränkungen:

-   Es gibt Beschränkungen für die Anzahl von Anwendungssicherheitsgruppen in einem Abonnement sowie in Bezug auf Anwendungssicherheitsgruppen. Ausführliche Informationen finden Sie im Artikel zu den [Einschränkungen für Azure-Abonnements](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Sie können eine einzelne Anwendungssicherheitsgruppe als Quelle und Ziel in einer Sicherheitsregel angeben. Sie können in der Quelle und im Ziel nicht mehrere Anwendungssicherheitsgruppen angeben.
- Alle Netzwerkschnittstellen, die einer Anwendungssicherheitsgruppe zugewiesen sind, müssen in selben virtuellen Netzwerk vorhanden sein, in dem sich die erste Netzwerkschnittstelle befindet, die der Anwendungssicherheitsgruppe zugewiesen wurde. Wenn sich die erste Netzwerkschnittstelle, die einer Anwendungssicherheitsgruppe mit dem Namen *AsgWeb* zugewiesen ist, im virtuellen Netzwerk *VNet1* befindet, müssen alle nachfolgenden Netzwerkschnittstellen, die *ASGWeb* zugewiesen werden, in *VNet1* enthalten sein. Einer Anwendungssicherheitsgruppe können keine Netzwerkschnittstellen aus verschiedenen virtuellen Netzwerken hinzugefügt werden.
- Wenn Sie eine Anwendungssicherheitsgruppe als Quelle und Ziel in einer Sicherheitsregel angeben, müssen sich die Netzwerkschnittstellen in beiden Anwendungssicherheitsgruppen im gleichen virtuellen Netzwerk befinden. Wenn also beispielsweise *AsgLogic* Netzwerkschnittstellen aus *VNet1* und *AsgDb* Netzwerkschnittstellen aus *VNet2* enthält, kann *AsgLogic* nicht als Quelle und *AsgDb* nicht als Ziel in einer Regel zugewiesen werden. Alle Netzwerkschnittstellen für die Quell- und Ziel-Anwendungssicherheitsgruppen müssen im selben virtuellen Netzwerk vorhanden sein.

> [!TIP]
> Planen Sie die erforderlichen Anwendungssicherheitsgruppen, und erstellen Sie Regeln nach Möglichkeit mithilfe von Diensttags oder Anwendungssicherheitsgruppen anstelle von individuellen IP-Adresse oder IP-Adressbereichen, um die Anzahl der erforderlichen Sicherheitsregeln und die Notwendigkeit von Regeländerungen zu minimieren.

## <a name="how-traffic-is-evaluated"></a>Auswertung des Datenverkehrs

Sie können Ressourcen von mehreren Azure-Diensten in einem virtuellen Azure-Netzwerk bereitstellen. Eine vollständige Liste finden Sie unter [Dienste, die in einem virtuellen Netzwerk bereitgestellt werden können](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Sie können jedem [Subnetz](virtual-network-manage-subnet.md#change-subnet-settings) eines virtuellen Netzwerks und jeder [Netzwerkschnittstelle](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) eines virtuellen Computers keine oder eine Netzwerksicherheitsgruppe zuordnen. Sie können dieselbe Netzwerksicherheitsgruppe beliebig vielen Subnetzen und Netzwerkschnittstellen zuordnen.

Die folgende Abbildung veranschaulicht verschiedene Szenarien dazu, wie Netzwerksicherheitsgruppen bereitgestellt werden können, um Netzwerkdatenverkehr zum und aus dem Internet über TCP-Port 80 zuzulassen:

![NSG-Verarbeitung](./media/security-groups/nsg-interaction.png)

Sehen Sie sich die Abbildung oben zusammen mit dem folgenden Text an, um weitere Informationen dazu zu erhalten, wie Azure ein- und ausgehende Regeln für Netzwerksicherheitsgruppen verarbeitet:

### <a name="inbound-traffic"></a>Eingehender Datenverkehr

Für eingehenden Datenverkehr verarbeitet Azure zuerst die Regeln in einer Netzwerksicherheitsgruppe, die einem Subnetz (sofern vorhanden) zugeordnet sind, und anschließend die Regeln in einer Netzwerksicherheitsgruppe, die der Netzwerkschnittstelle (sofern vorhanden) zugeordnet sind.

- **VM1**: Die Sicherheitsregeln in *NSG1* werden verarbeitet, da diese Netzwerksicherheitsgruppe *Subnet1* zugeordnet ist und *VM1* sich in *Subnet1* befindet. Sofern Sie keine Regel erstellt haben, die Port 80 als eingehenden Port zulässt, wird der Datenverkehr von der Standardsicherheitsregel [DenyAllInbound](#denyallinbound) abgelehnt und von *NSG2* nicht ausgewertet, da *NSG2* der Netzwerkschnittstelle zugeordnet ist. Wenn *NSG1* über eine Sicherheitsregel verfügt, die Port 80 zulässt, wird der Datenverkehr von *NSG2* verarbeitet. Damit Port 80 für den virtuellen Computer zulässig ist, müssen sowohl *NSG1* als auch *NSG2* über eine Regel verfügen, die Port 80 aus dem Internet zulässt.
- **VM2**: Die Regeln in *NSG1* werden verarbeitet, da *VM2* sich auch in *Subnet1* befindet. Da *VM2* über keine Netzwerksicherheitsgruppe verfügt, die seiner Netzwerkschnittstelle zugeordnet ist, empfängt er den gesamten zulässigen Datenverkehr über *NSG1*, oder der gesamte Datenverkehr wird durch *NSG1* verweigert. Datenverkehr wird für alle Ressourcen im selben Subnetz entweder zugelassen oder verweigert, wenn eine Netzwerksicherheitsgruppe einem Subnetz zugeordnet ist.
- **VM3**: Da *Subnet2* keine Netzwerksicherheitsgruppe zugeordnet ist, ist Datenverkehr in das Subnetz zulässig und wird von *NSG2* verarbeitet, da *NSG2* der Netzwerkschnittstelle zugeordnet ist, die *VM3* angefügt ist.
- **VM4**: Datenverkehr für *VM4* ist zulässig, da *Subnet3* oder der Netzwerkschnittstelle in der VM keine Netzwerksicherheitsgruppe zugeordnet ist. Der gesamte Netzwerkdatenverkehr ist über ein Subnetz und eine Netzwerkschnittstelle zulässig, wenn diesen keine Netzwerksicherheitsgruppe zugeordnet ist.

### <a name="outbound-traffic"></a>Ausgehender Datenverkehr

Für ausgehenden Datenverkehr verarbeitet Azure zuerst die Regeln in einer Netzwerksicherheitsgruppe, die einer Netzwerkschnittstelle (sofern vorhanden) zugeordnet sind, und anschließend die Regeln in einer Netzwerksicherheitsgruppe, die dem Subnetz (sofern vorhanden) zugeordnet sind.

- **VM1**: Die Sicherheitsregeln in *NSG2* werden verarbeitet. Sofern Sie keine Sicherheitsregel erstellen, die Port 80 als ausgehenden Port zum Internet zulässt, wird der Datenverkehr von der Standardsicherheitsregel [AllowInternetOutbound](#allowinternetoutbound) in *NSG1* sowie *NSG2* zugelassen. Wenn *NSG2* über eine Sicherheitsregel verfügt, die Port 80 ablehnt, wird der Datenverkehr abgelehnt und nicht von *NSG1* ausgewertet. Zum Verweigern von Port 80 vom virtuellen Computer oder beiden Netzwerksicherheitsgruppen ist eine Regel erforderlich, die Port 80 für das Internet verweigert.
- **VM2**: Der gesamte Datenverkehr wird über die Netzwerkschnittstelle an das Subnetz gesendet, da der Netzwerkschnittstelle, die an *VM2* angefügt ist, keine Netzwerksicherheitsgruppe zugeordnet ist. Die Regeln in *NSG1* werden verarbeitet.
- **VM3**: Wenn *NSG2* über eine Sicherheitsregel verfügt, die Port 80 verweigert, wird der Datenverkehr verweigert. Wenn *NSG2* über eine Sicherheitsregel verfügt, die Port 80 zulässt, dann ist Port 80 als ausgehender Port für das Internet zulässig, da *Subnet2* keine Netzwerksicherheitsgruppe zugeordnet ist.
- **VM4**: Der gesamte Netzwerkdatenverkehr von *VM4* ist zulässig, da der Netzwerkschnittstelle, die an die VM angefügt ist, oder *Subnet3* keine Netzwerksicherheitsgruppe zugeordnet ist.

Sie können die Aggregatregeln, die auf eine Netzwerkschnittstelle angewendet werden, leicht prüfen, indem Sie die [effektiven Sicherheitsregeln](virtual-network-network-interface.md#view-effective-security-rules) für eine Netzwerkschnittstelle anzeigen. Sie können auch in Azure Network Watcher die Funktion [Überprüfen des IP-Flusses](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) verwenden, um zu ermitteln, ob die Kommunikation für eine Netzwerkschnittstelle in ein- oder ausgehender Richtung zulässig ist. Die IP-Datenflussüberprüfung gibt an, ob die Kommunikation zugelassen oder verweigert wird und für welche Netzwerksicherheitsregel Datenverkehr zugelassen ist oder verweigert wird.

> [!NOTE]
> Netzwerksicherheitsgruppen sind bei der Bereitstellung im klassischen Bereitstellungsmodell Subnetzen oder virtuellen Computern und Clouddiensten zugeordnet sowie Subnetzen oder Netzwerkschnittstellen gemäß dem Resource Manager-Bereitstellungsmodell. Weitere Informationen zu den Azure-Bereitstellungsmodellen finden Sie unter [Azure Resource Manager-Bereitstellung im Vergleich zur klassischen Bereitstellung: Grundlegendes zu Bereitstellungsmodellen und zum Status von Ressourcen](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!TIP]
> Wir empfehlen Ihnen, eine Netzwerksicherheitsgruppe einem Subnetz oder einer Netzwerkschnittstelle zuzuordnen, aber nicht beiden, sofern kein zwingender Grund dafür vorliegt. Da Regeln in einer Netzwerksicherheitsgruppe, die einem Subnetz zugeordnet ist, unter Umständen mit Regeln in einer Netzwerksicherheitsgruppe, die einer Netzwerkschnittstelle zugeordnet sind, in Konflikt stehen, können unerwartete Kommunikationsprobleme auftreten, die behoben werden müssen.

## <a name="azure-platform-considerations"></a>Aspekte der Azure Platform

- **Virtuelle IP des Hostknotens**: Grundlegende Infrastrukturdienste wie DHCP, DNS, IMDS und die Systemüberwachung werden über die virtualisierten Host-IP-Adressen 168.63.129.16 und 169.254.169.254 bereitgestellt. Diese IP-Adressen gehören Microsoft und sind die einzigen virtuellen IP-Adressen, die in allen Regionen zu diesem Zweck verwendet werden.
- **Lizenzierung (Key Management Service)** : Die auf VMs ausgeführten Windows-Images müssen lizenziert werden. Zum Sicherstellen der Lizenzierung wird eine entsprechende Anforderung an die Hostserver des Schlüsselverwaltungsdiensts gesendet, die solche Abfragen verarbeiten. Die Anforderung wird in ausgehender Richtung über Port 1688 gesendet. Für Bereitstellungen mit einer Konfiguration der [Standardroute 0.0.0.0/0](virtual-networks-udr-overview.md#default-route) wird diese Plattformregel deaktiviert.
- **VMs in Pools mit Lastenausgleich**: Der angewendete Quellport und -adressbereich stammen vom Ausgangscomputer, nicht vom Lastenausgleich. Der Zielport und -adressbereich sind für den Zielcomputer bestimmt, nicht für den Lastenausgleich.
- **Azure-Dienstinstanzen**: Instanzen mehrerer Azure-Dienste (z. B. HDInsight, App Service-Umgebungen und Virtual Machine Scale Sets) werden in Subnetzen des virtuellen Netzwerks bereitgestellt. Eine vollständige Liste mit den Diensten, die Sie in virtuellen Netzwerken bereitstellen können, finden Sie unter [Virtuelles Netzwerk für Azure-Dienste](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Machen Sie sich auf jeden Fall mit den Portanforderungen für die einzelnen Dienste vertraut, bevor Sie eine Netzwerksicherheitsgruppe auf das Subnetz anwenden, in dem die Ressource bereitgestellt wurde. Wenn Sie den Datenverkehr für Ports verweigern, die für den Dienst benötigt werden, funktioniert der Dienst nicht richtig.
- **Senden von E-Mails in ausgehender Richtung**: Microsoft empfiehlt die Nutzung von authentifizierten SMTP-Relaydiensten (normalerweise über TCP-Port 587 verbunden, aber auch über andere Ports), um E-Mails von Azure Virtual Machines zu senden. SMTP-Relaydienste sind auf Absenderzuverlässigkeit ausgelegt, um die Wahrscheinlichkeit zu verringern, dass Nachrichten von E-Mail-Drittanbietern abgelehnt werden. Zu diesen SMTP-Relaydiensten gehören u.a. auch Exchange Online Protection und SendGrid. Die Nutzung von SMTP-Relaydiensten ist in Azure unabhängig von Ihrem Abonnementtyp auf keinerlei Weise eingeschränkt. 

  Wenn Sie Ihr Azure-Abonnement vor dem 15. November 2017 erstellt haben, können Sie nicht nur die SMTP-Relaydienste nutzen, sondern auch E-Mails direkt über TCP-Port 25 senden. Falls Sie Ihr Abonnement nach dem 15. November 2017 erstellt haben, ist es unter Umständen nicht möglich, E-Mails direkt über Port 25 zu senden. Das Verhalten der ausgehenden Kommunikation über Port 25 hängt wie folgt vom Typ Ihres Abonnements ab:

     - **Enterprise Agreement**: Die Kommunikation in ausgehender Richtung über Port 25 ist zulässig. Sie können ausgehende E-Mails direkt von virtuellen Computern an externe E-Mail-Anbieter senden, ohne dass Einschränkungen der Azure Platform bestehen. 
     - **Nutzungsbasierte Bezahlung:** Die Kommunikation in ausgehender Richtung über Port 25 wird für alle Ressourcen blockiert. Wenn Sie E-Mails von einem virtuellen Computer direkt an externe E-Mail-Anbieter senden müssen (ohne authentifiziertes SMTP-Relay), können Sie die Aufhebung der Einschränkung anfordern. Anfragen dieser Art werden von Microsoft geprüft und erst nach Durchführung von Betrugsprüfungen genehmigt. Erstellen Sie hierzu eine Supportanfrage mit einem Problemtyp der Art *Technisch*, *Konnektivität virtueller Netzwerke*, *E-Mail senden nicht möglich (SMTP/Port 25)* . Fügen Sie in die Supportanfrage Details dazu ein, warum für Ihr Abonnement das direkte Senden von E-Mails an E-Mail-Anbieter erforderlich ist, anstatt ein authentifiziertes SMTP-Relay zu verwenden. Wenn für Ihr Abonnement eine Ausnahmeregelung gewährt wird, können nur virtuelle Computer, die nach dem Startdatum der Ausnahmeregelung erstellt wurden, in ausgehender Richtung über Port 25 kommunizieren.
     - **MSDN, Azure Pass, Azure in Open, Education, BizSpark und kostenlose Testversion**: Die Kommunikation in ausgehender Richtung über Port 25 wird für alle Ressourcen blockiert. Es können keine Anfragen zur Beseitigung der Einschränkungen gesendet werden, da keine Ausnahmen gewährt werden. Wenn Sie über Ihren virtuellen Computer E-Mails senden müssen, müssen Sie einen SMTP-Relaydienst verwenden.
     - **Cloud-Dienstanbieter**: Kunden, die Azure-Ressourcen über einen Cloud-Dienstanbieter nutzen, können bei ihrem Cloud-Dienstanbieter eine Supportanfrage erstellen und anfordern, dass der Anbieter in ihrem Auftrag eine Anfrage zum Aufheben der Blockierung stellt, wenn ein sicheres SMTP-Relay nicht verwendet werden kann.

  Wenn für Sie in Azure das Senden von E-Mails über Port 25 zugelassen wird, kann von Microsoft nicht garantiert werden, dass E-Mail-Anbieter eingehende E-Mails von Ihrem virtuellen Computer akzeptieren. Falls ein bestimmter Anbieter E-Mails von Ihrem virtuellen Computer ablehnt, müssen Sie sich direkt an den Anbieter wenden, um Probleme mit der Nachrichtenzustellung oder Spamfilterung zu beheben, oder einen authentifizierten SMTP-Relaydienst verwenden.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer Netzwerksicherheitsgruppe](tutorial-filter-network-traffic.md)
