---
title: "Übersicht über SQL Server auf virtuellen Azure Windows-Computern | Microsoft-Dokumentation"
description: "Hier erfahren Sie, wie Sie vollwertige SQL Server-Editionen auf virtuellen Azure Windows-Computern ausführen. Außerdem finden Sie hier direkte Links zu allen SQL Server-VM-Images sowie zu verwandten Inhalten."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/02/2017
ms.author: jroth
ms.openlocfilehash: b10c995fdd8e241d354c62537a0600b393795c1b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-windows"></a>Übersicht über SQL Server auf virtuellen Azure-Computern (Windows)

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)

In diesem Thema werden die Optionen im Zusammenhang mit der Ausführung von SQL Server auf virtuellen Azure Windows-Computern (VMs) beschrieben. Außerdem enthält es [Links zu Portal-Images](#option-1-create-a-sql-vm-with-per-minute-licensing) sowie eine Übersicht über [allgemeine Aufgaben](#manage-your-sql-vm).

> [!NOTE]
> Wenn Sie bereits mit SQL Server vertraut sind und nur Informationen zum Bereitstellen eines virtuellen Windows-Computers mit SQL Server benötigen, helfen Ihnen die Informationen unter [Bereitstellen eines virtuellen Computers mit SQL Server über das Azure-Portal](virtual-machines-windows-portal-sql-server-provision.md) weiter. Informationen zur Erstellung eines virtuellen Linux-Computers mit SQL Server finden Sie unter [Provision a Linux SQL Server VM in the Azure](../../linux/sql/provision-sql-server-linux-virtual-machine.md) (Bereitstellen eines virtuellen Linux-Computers mit SQL Server in Azure).

Wenn Sie ein Datenbankadministrator oder Entwickler sind, stellen Azure-VMs eine Möglichkeit zum Verschieben Ihrer lokalen SQL Server-Workloads und -Anwendungen in die Cloud dar.

## <a name="scenarios"></a>Szenarien
Es gibt viele Gründe, die für das Hosten von Daten in Azure sprechen. Wenn Sie Ihre Anwendung nach Azure verschieben, wird die Leistung verbessert, indem Sie auch die Daten verschieben. Aber es gibt noch weitere Vorteile. Sie haben automatisch Zugriff auf mehrere Rechenzentren, um weltweite Präsenz und die Notfallwiederherstellung zu ermöglichen. Außerdem sind die Daten dauerhaft stark geschützt.

Die Ausführung von SQL Server auf Azure-VMs ist eine Möglichkeit zum Speichern Ihrer relationalen Daten in Azure. Dies eignet sich ideal für verschiedene Szenarios. Beispielsweise kann es sein, dass Sie die Azure-VM so konfigurieren möchten, dass sie einem lokalen SQL Server-Computer möglichst genau ähnelt. Unter Umständen möchten Sie auch weitere Anwendungen und Dienste auf demselben Datenbankserver ausführen. Es gibt zwei wichtige Ressourcen, die noch weitere Szenarios und Überlegungen bereitstellen:

* [SQL Server auf virtuellen Azure-Computern](https://azure.microsoft.com/services/virtual-machines/sql-server/) enthält eine Übersicht über die besten Szenarien für die Verwendung von SQL Server auf Azure-VMs. 
* Einen vollständigen Vergleich zwischen SQL-Datenbank und SQL Server auf einer VM finden Sie unter [Auswählen einer SQL Server-Cloudoption: Azure SQL-Datenbank (PaaS) oder SQL Server auf Azure-VMs (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

## <a name="create-a-new-sql-vm"></a>Erstellen einer neuen SQL-VM
Die folgenden Abschnitte enthalten direkte Links zum Azure-Portal für die Katalogimages für virtuelle SQL Server-Computer. Je nach ausgewähltem Image fallen entweder Kosten für die SQL Server-Lizenzierung pro Minute an, oder Sie nutzen eine eigene Lizenz (Bring Your Own License, BYOL).

Eine Schritt-für-Schritt-Anleitung zum Erstellen eines neuen virtuellen SQL-Computers finden Sie im Tutorial [Bereitstellen eines virtuellen Computers mit SQL Server im Azure-Portal](virtual-machines-windows-portal-sql-server-provision.md). Sehen Sie sich auch den Artikel [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](virtual-machines-windows-sql-performance.md) an. Darin wird beschrieben, wie Sie bei der Bereitstellung die richtige Computergröße und andere verfügbare Features auswählen.

## <a name="option-1-create-a-sql-vm-with-per-minute-licensing"></a>Option 1: Erstellen einer SQL-VM mit minutenbezogener Lizenzierung
Die folgende Tabelle enthält eine Matrix der aktuellen SQL Server-Images im Katalog der virtuellen Computer. Klicken Sie auf einen beliebigen Link, um mit dem Erstellen einer neuen SQL-VM mit Ihren Angaben zu Version, Edition und Betriebssystem zu beginnen. 

> [!TIP]
> Informationen zu den VM- und SQL-Preisen für diese Images finden Sie unter [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Preisinformationen für virtuelle Azure-Computer unter SQL Server).

| Version | Betriebssystem | Edition |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP3** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2) |

> [!NOTE]
> Informationen zu den verfügbaren Images für virtuelle Linux-Computer mit SQL Server finden Sie unter [Overview of SQL Server on Azure Virtual Machines (Linux)](../../linux/sql/sql-server-linux-virtual-machines-overview.md) (Übersicht über SQL Server auf virtuellen Azure-Computern (Linux)).

## <a id="BYOL"></a> Option 2: Erstellen einer SQL-VM mit einer vorhandenen Lizenz
Sie können auch Ihre eigene Lizenz nutzen (BYOL). In diesem Fall zahlen Sie nur für die VM, ohne dass zusätzliche Gebühren für die SQL Server-Lizenzierung anfallen. Nutzen Sie unten die Matrix mit SQL Server-Versionen, -Editionen und Betriebssystemen, wenn Sie Ihre eigene Lizenz verwenden möchten. Im Portal haben diese Imagenamen das Präfix **{BYOL}**.

> [!TIP]
> Durch die Nutzung einer eigenen Lizenz können Sie im Laufe der Zeit Geld für kontinuierliche Produktionsworkloads sparen. Weitere Informationen finden Sie unter [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Preisinformationen für virtuelle Azure-Computer unter SQL Server).

| Version | Betriebssystem | Edition |
| --- | --- | --- |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2) |

Neben dieser Liste sind noch andere Kombinationen von SQL Server-Versionen und Betriebssystemen verfügbar. Weitere Images finden Sie über eine Marketplace-Suche im Azure-Portal (suchen Sie nach „{BYOL} SQL Server“).

> [!IMPORTANT]
> Zum Verwenden von BYOL-VM-Images müssen Sie über ein Enterprise Agreement mit [Lizenzmobilität durch Software Assurance für Azure](https://azure.microsoft.com/pricing/license-mobility/) verfügen. Außerdem benötigen Sie eine gültige Lizenz für die Version/Edition von SQL Server, die Sie verwenden möchten. Sie müssen [gegenüber Microsoft die erforderlichen BYOL-Informationen angeben](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf). Dies muss innerhalb von **zehn Tagen** ab Bereitstellung Ihres virtuellen Computers geschehen. 

> [!NOTE]
> Es ist nicht möglich, das Lizenzierungsmodell einer SQL Server-VM mit minutenbasierter Bezahlung zu ändern und eine eigene Lizenz zu verwenden. In diesem Fall müssen Sie einen neuen virtuellen BYOL-Computer erstellen und die Datenbanken zu diesem neuen virtuellen Computer migrieren. 

## <a name="manage-your-sql-vm"></a>Verwalten der SQL-VM
Nach der Bereitstellung der SQL Server-VM stehen mehrere optionale Verwaltungsaufgaben an. In vielerlei Hinsicht konfigurieren und verwalten Sie SQL Server genauso wie eine lokale SQL Server-Instanz. Einige Aufgaben gelten aber speziell für Azure. In den folgenden Abschnitten wird auf einige dieser Bereiche mit Links zu weiteren Informationen näher eingegangen.

### <a name="connect-to-the-vm"></a>Herstellen der Verbindung zur VM
Einer der grundlegendsten Verwaltungsschritte ist die Herstellung einer Verbindung mit der SQL Server-VM über Tools, z.B. SQL Server Management Studio (SSMS). Eine Anleitung zum Herstellen der Verbindung mit Ihrer neuen SQL Server-VM finden Sie unter [Verbinden mit SQL Server-Instanzen auf virtuellen Azure-Computern](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Migrieren Ihrer Daten
Wenn Sie über eine vorhandene Datenbank verfügen, empfiehlt sich die Verschiebung auf die neu bereitgestellte SQL-VM. Eine Liste mit Migrationsoptionen und Anleitungen finden Sie unter [Migrieren einer SQL Server-Datenbank zu SQL Server auf einem virtuellen Azure-Computer](virtual-machines-windows-migrate-sql.md).

### <a name="configure-high-availability"></a>Konfigurieren hoher Verfügbarkeit
Wenn Sie hohe Verfügbarkeit benötigen, sollten Sie SQL Server-Verfügbarkeitsgruppen konfigurieren. Dies beinhaltet mehrere Azure-VMs in einem virtuellen Netzwerk. Das Azure-Portal bietet eine Vorlage, die diese Konfiguration für Sie einrichtet. Weitere Informationen finden Sie unter [Konfigurieren einer AlwaysOn-Verfügbarkeitsgruppe auf Azure Resource Manager-VMs (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Wenn Sie Ihre Verfügbarkeitsgruppe und den ihr zugeordneten Listener manuell konfigurieren möchten, helfen Ihnen die Informationen im Artikel [Konfigurieren von AlwaysOn-Verfügbarkeitsgruppen in einem virtuellen Azure-Computer (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)weiter.

Weitere Informationen finden Sie unter [Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-sql-high-availability-dr.md).

### <a name="back-up-your-data"></a>Sichern Ihrer Daten
Für virtuelle Azure-Computer kann die [automatisierte Sicherung](virtual-machines-windows-sql-automated-backup.md)verwendet werden, bei der regelmäßig Sicherungen Ihrer Datenbank im Blobspeicher erstellt werden. Sie können dieses Verfahren auch manuell verwenden. Weitere Informationen finden Sie unter [Verwenden von Azure Storage für SQL Server-Sicherung und -Wiederherstellung](virtual-machines-windows-use-storage-sql-server-backup-restore.md). Eine Übersicht über alle Sicherungs- und Wiederherstellungsoptionen finden Sie unter [Sicherung und Wiederherstellung für SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-sql-backup-recovery.md).

### <a name="automate-updates"></a>Automatisieren von Updates
Für virtuelle Azure-Computer kann das [automatisierte Patchen](virtual-machines-windows-sql-automated-patching.md) verwendet werden, um ein Wartungsfenster zum automatischen Installieren wichtiger Windows- und SQL Server-Updates zu planen.

### <a name="customer-experience-improvement-program-ceip"></a>Programm zur Verbesserung der Benutzerfreundlichkeit (Customer Experience Improvement Program, CEIP)
Das Programm zur Verbesserung der Benutzerfreundlichkeit (Customer Experience Improvement Program, CEIP) ist standardmäßig aktiviert. Es sendet in regelmäßigen Abständen Berichte an Microsoft, damit die Nutzung von SQL Server verbessert werden kann. Für CEIP ist nur dann eine Verwaltungsaufgabe erforderlich, wenn Sie das Programm nach der Bereitstellung deaktivieren möchten. Sie können CEIP anpassen oder deaktivieren, indem Sie eine Verbindung mit der VM per Remotedesktop herstellen. Führen Sie anschließend das SQL Server-Hilfsprogramm **Fehler- und Verwendungsberichterstellung** aus. Befolgen Sie die Anleitung, um die Berichterstellung zu deaktivieren. 

Weitere Informationen zur Datensammlung finden Sie unter [Datenschutzbestimmungen für SQL Server](https://www.microsoft.com/EN-US/privacystatement/SQLServer/Default.aspx). 

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Preisgestaltung finden Sie unter [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Preisinformationen für virtuelle Azure-Computer unter SQL Server) und [Virtuelle Windows-Computer – Preise ](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Wählen Sie in der Liste **Betriebssystem/Software** Ihre Zieledition von SQL Server aus. Zeigen Sie dann die Preise für virtuelle Computern unterschiedlicher Größe an.

Haben Sie noch eine Frage? Lesen Sie sich zunächst [SQL Server auf virtuellen Azure-Computern – FAQ](virtual-machines-windows-sql-server-iaas-faq.md)durch. Darüber hinaus können Sie Ihre Fragen und Kommentare auch am Ende eines SQL-VM-Themas hinzufügen, um mit Microsoft und der Community zu interagieren.
