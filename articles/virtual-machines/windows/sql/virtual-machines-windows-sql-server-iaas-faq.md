---
title: "SQL Server auf virtuellen Azure-Computern – FAQ | Microsoft Docs"
description: "Dieser Artikel enthält Antworten auf häufig gestellte Fragen zur Ausführung von SQL Server auf Azure-VMs."
services: virtual-machines-windows
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/23/2017
ms.author: v-shysun
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 447ece9653a4cf69153f9c85e222e3ea4e8bae16
ms.contentlocale: de-de
ms.lasthandoff: 06/26/2017

---
# <a name="frequently-asked-questions-for-sql-server-on-azure-virtual-machines"></a>Häufig gestellte Fragen zu SQL Server auf virtuellen Azure-Computern
Dieses Thema bietet Antworten auf einige der häufigsten Fragen zur Ausführung von [SQL Server auf virtuellen Azure-Computern](https://azure.microsoft.com/services/virtual-machines/sql-server/).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

1. **Wie erstelle ich einen virtuellen Azure-Computer mit SQL Server?**

    Die einfachste Lösung ist die Erstellung eines virtuellen Computers, der SQL Server enthält. Ein Tutorial für die Anmeldung bei Azure und das Erstellen einer SQL-VM im Portal finden Sie unter [Bereitstellen eines virtuellen SQL Server-Computers im Azure-Portal](virtual-machines-windows-portal-sql-server-provision.md). Sie können ein virtuelles Computerimage auswählen, das eine SQL Server-Lizenzierung mit minutenbasierter Bezahlung verwendet, oder Sie verwenden ein Image, das die Nutzung Ihrer eigenen SQL Server-Lizenz zulässt. Sie können SQL Server auch manuell auf einem virtuellen Computer installieren und dafür eine lokale Lizenz wiederverwenden. Wenn Sie Ihre eigene Lizenz verwenden, benötigen Sie die [Lizenzmobilität durch Software Assurance für Azure](https://azure.microsoft.com/pricing/license-mobility/). Weitere Informationen finden Sie unter [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Preisinformationen für virtuelle Azure-Computer unter SQL Server).

1. **Was ist der Unterschied zwischen SQL-VMs und dem SQL-Datenbank-Dienst?**

    Im Prinzip unterscheidet sich die Ausführung von SQL Server auf virtuellen Azure-Computern nicht von der Ausführung in einem Remoterechenzentrum. Im Gegensatz dazu bietet [SQL-Datenbank](../../../sql-database/sql-database-technical-overview.md) Database-as-a-Service. Mit SQL-Datenbank haben Sie keinen Zugriff auf die Computer, die Ihre Datenbanken hosten. Einen vollständigen Vergleich finden Sie unter [Wählen Sie eine SQL Server-Cloudoption: Azure SQL-Datenbank (PaaS) oder SQL Server auf Azure-VMs (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Wie kann ich meine lokale SQL Server-Datenbank zur Cloud migrieren?**

    Erstellen Sie zuerst einen virtuellen Azure-Computer mit einer SQL Server-Instanz. Migrieren Sie dann Ihre lokalen Datenbanken zu dieser Instanz. Datenmigrationsstrategien finden Sie unter [Migrieren einer SQL Server-Datenbank zu SQL Server auf einem virtuellen Azure-Computer](virtual-machines-windows-migrate-sql.md).

1. **Kann ich eine zweite Instanz von SQL Server auf dem gleichen virtuellen Computer installieren? Kann ich die installierten Funktionen der Standardinstanz ändern?**

    Ja. Die SQL Server-Installationsmedien befinden sich in einem Ordner auf dem Laufwerk **C** . Führen Sie **Setup.exe** von diesem Speicherort aus, um neue SQL Server-Instanzen hinzuzufügen oder andere installierte Features von SQL Server auf dem Computer zu ändern. Beachten Sie, dass einige Funktionen wie „Automatisierte Sicherung“, „Automatisiertes Patchen“ und „Azure Key Vault-Integration“ nur für die Standardinstanz ausgeführt werden können.

1. **Kann ich die Standardinstanz von SQL Server deinstallieren?**

    Ja. Hierbei sind jedoch einige Punkte zu beachten. Wie bereits in der vorhergehenden Antwort erläutert, können Funktionen, die sich auf die [SQL Server-IaaS-Agenterweiterung](virtual-machines-windows-sql-server-agent-extension.md) stützen, nur für die Standardinstanz ausgeführt werden. Wenn Sie die Standardinstanz deinstallieren, sucht die Erweiterung weiterhin danach und generiert möglicherweise Fehler im Ereignisprotokoll. Diese Fehler stammen aus den folgenden zwei Quellen: **Microsoft SQL Server-Verwaltung von Anmeldeinformationen** und **Microsoft SQL Server-IaaS-Agent**. Einer der Fehler kann dem Folgenden ähneln:
    
        A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. 
        
    Wenn Sie sich entscheiden, die Standardinstanz zu deinstallieren, deinstallieren Sie auch die [SQL Server-IaaS-Agenterweiterung](virtual-machines-windows-sql-server-agent-extension.md).

1. **Wie aktualisiere ich auf eine neue Version/Edition von SQL Server auf einer Azure-VM?**

    Derzeit ist kein direktes Upgrade für SQL Server auf einer Azure-VM verfügbar. Erstellen Sie einen neuen virtuellen Azure-Computer mit der gewünschten Version/Edition von SQL Server, und migrieren Sie dann Ihre Datenbanken mit dem standardmäßigen [Datenmigrationsverfahren](virtual-machines-windows-migrate-sql.md) zu dem neuen Server.

1. **Wie kann ich meine lizenzierte Kopie von SQL Server auf einer Azure-VM installieren?**

    Es gibt hierbei zwei Möglichkeiten. Sie können eines der [virtuellen Computerimages bereitstellen, das Lizenzen unterstützt](virtual-machines-windows-sql-server-iaas-overview.md#BYOL). Eine weitere Möglichkeit ist das Kopieren der SQL Server-Installationsmedien auf einen virtuellen Windows Server-Computer und das anschließende Installieren von SQL Server auf dem virtuellen Computer. Aus Gründen der Lizenzierung benötigen Sie die [Lizenzmobilität durch Software Assurance für Azure](https://azure.microsoft.com/pricing/license-mobility/). Weitere Informationen finden Sie unter [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Preisinformationen für virtuelle Azure-Computer unter SQL Server).

1. **Kann ich einen virtuellen Computer so ändern, dass meine eigene SQL Server-Lizenz verwendet wird, wenn er mithilfe eines der Katalogimages mit nutzungsbasierter Bezahlung erstellt wurde?**

    Nr. Der Wechsel von Lizenzierung mit minutenbasierter Bezahlung zur Nutzung der eigenen Lizenz ist nicht möglich. Erstellen Sie mithilfe eines der [BYOL-Images](virtual-machines-windows-sql-server-iaas-overview.md#BYOL) einen neuen virtuellen Azure-Computer, und migrieren Sie dann Ihre Datenbanken mit dem standardmäßigen [Datenmigrationsverfahren](virtual-machines-windows-migrate-sql.md) zum neuen Server.

1. **Werden SQL Server-Failoverclusterinstanzen (FCI) auf Azure-VMs unterstützt?**

   Ja. Sie können [einen Windows-Failovercluster unter Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) erstellen und für die Speicherung des Clusters das Feature „Direkte Speicherplätze“ (S2D) verwenden. Alternativ können Sie, wie unter [Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions) beschrieben, Cluster- oder Speicherlösungen von Drittanbietern nutzen.

1. **Muss ich Lizenzgebühren für SQL Server auf einem virtuellen Azure-Computer bezahlen, wenn dieser nur für Standby/Failover verwendet wird?**

    Sie müssen keine Lizenzgebühren für einen SQL Server bezahlen, wenn dieser als passives sekundäres Replikat in einer Bereitstellung mit hoher Verfügbarkeit fungiert, sofern Sie über Software Assurance verfügen und Lizenzmobilität gemäß der Beschreibung in [FAQ zur Lizenzierung von virtuellen Computer](http://azure.microsoft.com/pricing/licensing-faq/) verwenden.

1. **Wie werden Updates und Servicepacks auf eine SQL Server-VM angewendet?**

    Virtuelle Computer bieten Ihnen die Kontrolle über die Hostcomputer, einschließlich des Zeitpunkts, zu dem Updates angewendet werden sollen, und der Art und Weise. Für das Betriebssystem können Sie manuell Windows-Updates anwenden oder einen Planungsdienst namens [Automatisiertes Patchen](virtual-machines-windows-sql-automated-patching.md) aktivieren. Automatisiertes Patchen installiert alle wichtigen Updates, einschließlich SQL Server-Updates in dieser Kategorie. Andere optionale Updates für SQL Server müssen manuell installiert werden.

1. **Ist es möglich, Konfigurationen einzurichten, die nicht im Katalog der virtuellen Computer gezeigt werden (z.B. Windows 2008 R2 + SQL Server 2012) ?**

    Nein. Für Katalogimages von virtuellen Computern, die SQL Server enthalten, müssen Sie eines der bereitgestellten Images auswählen.

1. **Wie installiere ich SQL Data-Tools auf meiner Azure-VM?**

     Laden Sie die SQL Data-Tools von [Microsoft SQL Server Data Tools – Business Intelligence für Visual Studio 2013](https://www.microsoft.com/en-us/download/details.aspx?id=42313) herunter, und installieren Sie sie.

## <a name="resources"></a>Ressourcen

Eine Übersicht über SQL Server auf virtuellen Azure-Computern bietet das Video [Azure VM is the best platform for SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016)(Azure VM ist die beste Plattform für SQL Server 2016). Eine gute Einführung erhalten Sie auch im Thema [Übersicht zu SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-sql-server-iaas-overview.md).

Zu weiteren Ressourcen zählen:

* [Bereitstellen eines virtuellen Computers mit SQL Server im Azure-Portal](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrieren einer Datenbank zu SQL Server auf einer Azure-VM](virtual-machines-windows-migrate-sql.md)
* [Hohe Verfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-sql-high-availability-dr.md)
* [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](virtual-machines-windows-sql-performance.md)
* [Anwendungsmuster und Entwicklungsstrategien für SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md) 
