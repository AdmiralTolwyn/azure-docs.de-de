---
title: Übersicht über VNET-Dienstendpunkte für Azure Database for MariaDB-Server | Microsoft-Dokumentation
description: Übersicht über die Funktionsweise von VNET-Dienstendpunkten für Ihren Azure Database for MariaDB-Server
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: fdf2d3aeea32beba0b8e95c1816a80140d7cf6be
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958914"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-mariadb"></a>Verwenden von VNET-Dienstendpunkten und -Regeln für Azure Database for MariaDB

*VNET-Regeln* sind eine Firewallsicherheitsfunktion, die steuert, ob Ihr Azure Database for MariaDB-Server Nachrichten zulässt, die von bestimmten Subnetzen in virtuellen Netzwerken gesendet werden. In diesem Artikel erfahren Sie, warum die VNET-Regelfunktion manchmal die sicherste Option darstellt, um Nachrichten an Ihren Azure Database for MariaDB-Server zuzulassen.

Sie benötigen ein [virtuelles Netzwerk][vm-virtual-network-overview] (VNET) und einen [VNET-Dienstendpunkt][vm-virtual-network-service-endpoints-overview-649d] für die Regel, auf die verwiesen wird, um eine VNET-Regel zu erstellen. Die folgende Abbildung zeigt, wie ein VNET-Dienstendpunkt mit Azure Database for MariaDB funktioniert:

![Beispielhafte Funktionsweise eines VNET-Dienstendpunkts](media/concepts-data-access-security-vnet/vnet-concept.png)

> [!NOTE]
> Dieses Feature steht in allen Regionen von Azure zur Verfügung, in denen Azure Database for MariaDB für universelle und arbeitsspeicheroptimierte Server bereitgestellt wird.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologie und Beschreibung

**Virtuelles Netzwerk:** Sie können Ihrem Azure-Abonnement virtuelle Netzwerke zuordnen.

**Subnetz:** Ein virtuelles Netzwerk enthält **Subnetze**. Ihre virtuellen Azure-Computer (VMs) sind Subnetzen zugewiesen. Ein Subnetz kann mehrere VMs oder andere Computeknoten enthalten. Computeknoten, die sich außerhalb Ihres virtuellen Netzwerks befinden, können nicht auf Ihr virtuelles Netzwerk zugreifen, es sei denn, Sie konfigurieren für sie den sicheren Zugriff.

**Dienstendpunkt im virtuellen Netzwerk:** Ein [Dienstendpunkt im virtuellen Netzwerk][vm-virtual-network-service-endpoints-overview-649d] ist ein Subnetz, dessen Eigenschaftswerte mindestens einen formalen Azure-Diensttypnamen enthalten. In diesem Artikel beschäftigen wir uns mit dem Typnamen **Microsoft.Sql**, der auf einen Azure-Dienst mit dem Namen „SQL-Datenbank“ verweist. Dieses Diensttag gilt auch für den Azure Database for MariaDB-, Azure Database for MySQL- und Azure Database for PostgreSQL-Dienst. Bitte beachten Sie: Wenn Sie das Diensttag **Microsoft.Sql** auf einen VNET-Dienstendpunkt anwenden, wird der Dienstendpunkt-Datenverkehr für alle Azure SQL-Datenbank-, Azure Database for MariaDB-, Azure Database for MySQL- und Azure Database for PostgreSQL-Server im Subnetz konfiguriert.

**Regel für virtuelles Netzwerk:** Bei einer Regel für das virtuelle Netzwerk für Ihren Azure Database for MariaDB-Server handelt es sich um ein Subnetz, das in der Zugriffssteuerungsliste Ihres Azure Database for MariaDB-Servers enthalten ist. Das Subnetz muss den Typnamen **Microsoft.Sql** enthalten, um in der Zugriffssteuerungsliste für Ihren Azure Database for MariaDB-Server aufgeführt zu werden.

Eine Regel für ein virtuelles Netzwerk weist Ihren Azure Database for MariaDB-Server an, Nachrichten von jedem Knoten im Subnetz anzunehmen.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Vorteile einer Regel für ein virtuelles Netzwerk

Die virtuellen Computer in Ihren Subnetzen können nicht mit Ihrem Azure Database for MariaDB-Server kommunizieren, bis Sie dies einrichten. Eine Aktion zum Herstellen der Kommunikation stellt die Erstellung einer Regel für virtuelle Netzwerke dar. Die Begründung der Entscheidung für eine VNET-Regel erfordert eine Erörterung der Vor- und Nachteile, die die von der Firewall gebotenen konkurrierenden Sicherheitsoptionen berücksichtigt.

### <a name="a-allow-access-to-azure-services"></a>A. Zugriff auf Azure-Dienste erlauben

Der Verbindungssicherheitsbereich hat eine **EIN/AUS**Schaltfläche mit der Bezeichnung **Zugriff auf Azure-Dienste erlauben**. Die Einstellung **EIN** lässt Nachrichten von allen Azure IP-Adressen und aus allen Azure-Subnetzen zu. Diese Azure-IP-Adressen oder -Subnetze gehören möglicherweise nicht Ihnen. Diese Einstellung **EIN** lässt wahrscheinlich einen umfassenderen Zugriff auf Ihre Azure Database for MariaDB-Datenbank zu, als von Ihnen gewünscht. Eine Regel für ein virtuelles Netzwerk ermöglicht eine präzisere Steuerung.

### <a name="b-ip-rules"></a>B: IP-Regeln

Mit der Azure Database for MariaDB-Firewall können Sie IP-Adressbereiche bestimmen, aus denen Nachrichten an die Azure Database for MariaDB-Datenbank gesendet werden dürfen. Dieser Ansatz eignet sich gut für statische IP-Adressen, die sich außerhalb des privaten Azure-Netzwerks befinden. Doch viele Knoten innerhalb des privaten Azure-Netzwerks sind mit *dynamischen* IP-Adressen konfiguriert. Dynamische IP-Adressen können sich ändern, z.B. wenn Ihre VM neu gestartet wird. Es wäre töricht, eine dynamische IP-Adresse in einer Firewallregel in einer Produktionsumgebung anzugeben.

Sie können die IP-Option weiter nutzen, indem Sie eine *statische* IP-Adresse für Ihre VM abrufen. Einzelheiten finden Sie unter [Konfigurieren von privaten IP-Adressen für einen virtuellen Computer über das Azure-Portal][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Der Ansatz mit statischen IP-Adressen kann jedoch schwierig zu handhaben und aufwendig sein, wenn er in großem Maßstab befolgt wird. Regeln für ein virtuelles Netzwerk sind einfacher einzurichten und zu verwalten.

### <a name="c-cannot-yet-have-azure-database-for-mariadb-on-a-subnet-without-defining-a-service-endpoint"></a>C. Zuweisen von Azure Database for MariaDB zu einem Subnetz ohne Festlegen eines Dienstendpunkts noch nicht möglich

Würde sich Ihr **Microsoft.Sql**-Server auf einem Knoten in einem Subnetz in Ihrem virtuellen Netzwerk befinden, könnten alle Knoten innerhalb des virtuellen Netzwerks mit Ihrem Azure Database for MariaDB-Server kommunizieren. Dann könnten auch Ihre virtuellen Computer mit der Azure Database for MariaDB-Instanz kommunizieren – ohne VNET- oder IP-Regeln.

Allerdings gehört der Azure Database for MariaDB-Dienst noch nicht zu den Diensten, die einem Subnetz direkt zugewiesen werden können (Stand August 2018).

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Details zu Regeln für ein virtuelles Netzwerk

In diesem Abschnitt werden verschiedene Details zu Regeln für ein virtuelles Netzwerk beschrieben.

### <a name="only-one-geographic-region"></a>Nur eine geografische Region

Jeder Dienstendpunkt eines virtuellen Netzwerks gehört nur zu einer Azure-Region. Der Endpunkt ermöglicht anderen Regionen nicht das Akzeptieren von Nachrichten aus dem Subnetz.

Eine Regel für ein virtuelles Netzwerk ist auf die Region beschränkt, zu der der zugrunde liegende Endpunkt gehört.

### <a name="server-level-not-database-level"></a>Auf Serverebene, nicht auf Datenbankebene

Jede VNET-Regel gilt für den gesamten Azure Database for MariaDB-Server und nicht nur für eine bestimmte Datenbank auf dem Server. Das heißt, dass Regeln für ein virtuelles Netzwerk auf Server- und nicht auf Datenbankebene gelten.

### <a name="security-administration-roles"></a>Sicherheitsverwaltungsrollen

Bei der Verwaltung der Dienstendpunkte des virtuellen Netzwerks erfolgt eine Trennung von Sicherheitsrollen. Die folgenden Rollen müssen Aktionen ausführen:

- **Netzwerkadministrator:** &nbsp;Aktivieren des Endpunkts.
- **Datenbankadministrator:** &nbsp;Aktualisieren der Zugriffssteuerungsliste, um das angegebene Subnetz dem Azure Database for MariaDB-Server hinzuzufügen.

*Alternative zur rollenbasierten Zugriffssteuerung:*

Die Rollen „Netzwerkadministrator“ und „Datenbankadministrator“ haben mehr Zugriffsrechte, als für die Verwaltung von Regeln für ein virtuelles Netzwerk erforderlich ist. Es wird nur eine Teilmenge der Zugriffsrechte benötigt.

Sie können mit der [rollenbasierten Zugriffssteuerung (RBAC)][rbac-what-is-813s] in Azure arbeiten, um eine einzelne benutzerdefinierte Sicherheitsrolle zu erstellen, die nur über die benötigte Teilmenge von Zugriffsrechten verfügt. Die benutzerdefinierte Rolle kann definiert werden, anstatt den Netzwerk- oder Datenbankadministrator einzubeziehen. Die auf die Sicherheit bezogene Angriffsfläche ist kleiner, wenn Sie einen Benutzer einer benutzerdefinierte Rolle hinzufügen und ihn nicht den beiden anderen wichtigen Administratorrollen hinzufügen.

> [!NOTE]
> In einigen Fällen befinden sich Azure Database for MariaDB und das VNET-Subnetz in unterschiedlichen Abonnements. In diesen Fällen müssen Sie folgende Konfigurationen sicherstellen:
> - Beide Abonnements müssen demselben Azure Active Directory-Mandanten zugeordnet sein.
> - Der Benutzer muss über die erforderlichen Berechtigungen zum Initiieren der Vorgänge verfügen. Dazu gehören z.B. das Aktivieren von Dienstendpunkten und das Hinzufügen eines VNET-Subnetzes auf dem angegebenen Server.

## <a name="limitations"></a>Einschränkungen

Bei Azure Database for MariaDB gelten für VNET-Regeln folgende Einschränkungen:

- In der Firewall für Azure Database for MariaDB verweist jede VNET-Regel auf ein Subnetz. Alle diese Subnetze müssen in derselben geografischen Region gehostet werden wie Azure Database for MariaDB.

- Jeder Azure Database for MariaDB-Server kann für ein beliebiges virtuelles Netzwerk maximal 128 Einträge in der Zugriffssteuerungsliste enthalten.

- Regeln für ein virtuelles Netzwerk gelten nur für virtuelle Netzwerke gemäß dem Azure Resource Manager-Modell und nicht gemäß dem [klassischen Bereitstellungsmodell][resource-manager-deployment-model-568f].

- Wenn Sie die VNET-Dienstendpunkte für Azure Database for MariaDB mit dem Diensttag **Microsoft.Sql** aktivieren, werden auch die Endpunkte für alle Azure-Datenbankdienste aktiviert: Azure Database for MariaDB, Azure Database for MySQL, Azure Database for PostgreSQL, Azure SQL-Datenbank und Azure SQL Data Warehouse.

- VNET-Dienstendpunkte werden nur für Server vom Typ „Universell“ und „Arbeitsspeicheroptimiert“ unterstützt.

- In der Firewall gelten zwar IP-Adressbereiche für die folgenden Netzwerkelemente, Regeln für virtuelle Netzwerke jedoch nicht:
    - [Virtuelles privates Netzwerk zwischen Standorten][vpn-gateway-indexmd-608y]
    - Lokal über [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Wenn Ihr Netzwerk über [ExpressRoute][expressroute-indexmd-744v] mit Ihrem Azure-Netzwerk verbunden ist, wird jede Verbindung mit zwei öffentlichen IP-Adressen im Microsoft-Edgebereich konfiguriert. Die beiden IP-Adressen werden zum Herstellen der Verbindung mit Microsoft-Diensten, z.B. Azure Storage, mithilfe von öffentlichem Azure-Peering verwendet.

Sie müssen IP-Netzwerkregeln für die öffentlichen IP-Adressen Ihrer Verbindungen erstellen, um die Kommunikation zwischen Ihrer Verbindung und Azure Database for MariaDB zu ermöglichen. Öffnen Sie über das Azure-Portal ein Supportticket für ExpressRoute, um die öffentlichen IP-Adressen Ihrer ExpressRoute-Verbindung zu ermitteln.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Hinzufügen einer VNET-Firewallregel zu Ihrem Server ohne Aktivierung von VNET-Dienstendpunkten

Allein das Festlegen einer Firewallregel trägt nicht zur Sicherung des Servers im VNET bei. Sie müssen auch VNET-Dienstendpunkte **aktivieren** , damit der Server gesichert wird. Wenn Sie Dienstendpunkte **aktivieren** , fällt das VNET-Subnetz solange aus, bis der Übergang von **„deaktiviert“** zu **„aktiviert“** abgeschlossen ist. Dies gilt vor allem für sehr umfangreiche VNETs. Mithilfe des Flags **IgnoreMissingServiceEndpoint** können Sie die Ausfallzeit während des Übergangs reduzieren bzw. vermeiden.

Verwenden Sie die Azure CLI oder das Azure-Portal, um das Flag **IgnoreMissingServiceEndpoint** festzulegen.

## <a name="related-articles"></a>Verwandte Artikel
- [Virtuelles Azure-Netzwerk][vm-virtual-network-overview]
- [Dienstendpunkte von virtuellen Netzwerken (Vorschauversion)][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Nächste Schritte
Hier finden Sie weitere Artikel zum Erstellen von VNET-Regeln:
- [Create and manage Azure Database for MariaDB VNet service endpoints and VNet rules by using the Azure portal](howto-manage-vnet-portal.md) (Erstellen und Verwalten von Azure Database for MariaDB-VNET-Dienstendpunkten und VNET-Regeln im Azure-Portal)
 
<!--
- [Create and manage Azure Database for MariaDB VNet rules using Azure CLI](howto-manage-vnet-using-cli.md)
-->

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml
