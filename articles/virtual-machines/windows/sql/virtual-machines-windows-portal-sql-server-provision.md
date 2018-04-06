---
title: Bereitstellungsleitfaden für virtuelle Windows-Computer mit SQL Server im Azure-Portal | Microsoft-Dokumentation
description: In dieser Anleitung werden die Optionen zum Erstellen von virtuellen Windows-Computern mit SQL Server 2017 im Azure-Portal beschrieben.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 02/15/2018
ms.author: jroth
ms.openlocfilehash: 33b7c82f08f63199cd128055bc497f61cb30fc4a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Bereitstellen eines virtuellen Windows-Computers mit SQL Server im Azure-Portal

In diesem Leitfaden werden die verschiedenen Optionen erläutert, die beim Erstellen eines virtuellen Windows-Computers mit SQL Server im Azure-Portal zur Auswahl stehen. Dieser Artikel umfasst mehr Konfigurationsoptionen als der [Schnellstart für virtuelle SQL Server-Computer](quickstart-sql-vm-create-portal.md), der eher eine mögliche Bereitstellung abhandelt. 

Verwenden Sie diesen Leitfaden, um Ihre eigenen virtuellen SQL Server-Computer zu erstellen. Sie können ihn auch als Referenz für die verfügbaren Optionen im Azure-Portal verwenden.

> [!TIP]
> Falls Sie Fragen zu SQL Server-VMs haben, finden Sie in den [häufig gestellten Fragen](virtual-machines-windows-sql-server-iaas-faq.md) weitere Informationen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a id="select"></a> Images im SQL Server-VM-Katalog

Beim Erstellen eines virtuellen SQL Server-Computers können Sie eines von zahlreichen vorkonfigurierten Images aus dem VM-Katalog auswählen. Die folgenden Schritte veranschaulichen die Auswahl eines SQL Server 2017-Images.

1. Melden Sie sich mit Ihrem Konto beim [Azure-Portal](https://portal.azure.com) an.

1. Klicken Sie im Azure-Portal auf **Ressource erstellen**. Das Portal wird mit dem Fenster **Neu** geöffnet.

1. Klicken Sie im Fenster **Neu** auf **Compute** und anschließend auf **Alle anzeigen**.

   ![Fenster „Neu berechnen“](./media/virtual-machines-windows-portal-sql-server-provision/azure-new-compute-blade.png)

1. Geben Sie in das Suchfeld die Zeichenfolge **SQL Server 2017** ein, und drücken Sie die EINGABETASTE.

1. Klicken Sie anschließend auf das Symbol **Filter**.

1. Überprüfen Sie im Fenster „Filter“ das Vorhandensein der Unterkategorie **Windows-basiert** und von **Microsoft** als Anbieter. Klicken Sie anschließend auf **Fertig**, um die Ergebnisse nach von Microsoft veröffentlichten Windows SQL Server-Images zu filtern.

   ![Fenster für Azure Virtual Machines](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-blade2.png)

1. Überprüfen Sie die verfügbaren SQL Server-Images. Für jedes Image sind eine SQL Server-Version und ein Betriebssystem angegeben.

1. Wählen Sie das Image **Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016** aus.

   > [!TIP]
   > In dieser exemplarischen Vorgehensweise wird die Developer Edition verwendet, da es sich um eine kostenlose Edition von SQL Server mit vollem Funktionsumfang zu Testzwecken in der Entwicklung handelt. Sie zahlen nur für die Ausführung der VM. Sie können für diese exemplarische Vorgehensweise jedoch ein beliebiges Image auswählen. Eine Beschreibung der verfügbaren Images finden Sie unter der [Übersicht über SQL Server-Windows-VMs](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

   > [!TIP]
   > Die Lizenzierungskosten für SQL Server sind in den Preisen pro Sekunde des erstellten virtuellen Computers enthalten und variieren je nach Edition und Kernen. Die SQL Server Developer-Edition ist jedoch für die Entwicklung bzw. für Tests (nicht für die Produktion) kostenlos, und SQL Express ist für einfache Workloads (weniger als 1 GB Arbeitsspeicher, weniger als 10 GB Speicherplatz) kostenlos. Sie können auch Ihre eigene Lizenz nutzen (Bring Your Own License, BYOL) und nur für die VM zahlen. Diese Imagenamen haben das Präfix {BYOL}. 
   >
   > Weitere Informationen zu diesen Optionen finden Sie unter [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Preisinformationen für virtuelle Azure-Computer unter SQL Server).

1. Stellen Sie unter **Bereitstellungsmodell auswählen** sicher, dass **Resource Manager** ausgewählt ist. Das Resource Manager-Bereitstellungsmodell ist das empfohlene Bereitstellungsmodell für neue virtuelle Computer. 

1. Klicken Sie auf **Create**.

    ![SQL-VM mit Resource Manager erstellen](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-sql-deployment-model.png)

## <a id="configure"></a> Konfigurationsoptionen
Es sind fünf Fenster zum Konfigurieren eines virtuellen SQL Server-Computers vorhanden.

| Schritt | BESCHREIBUNG |
| --- | --- |
| **Grundlagen** |[Grundeinstellungen konfigurieren](#1-configure-basic-settings) |
| **Größe** |[VM-Größe auswählen](#2-choose-virtual-machine-size) |
| **Einstellungen** |[Optionale Features konfigurieren](#3-configure-optional-features) |
| **SQL Server-Einstellungen** |[SQL Server-Einstellungen konfigurieren](#4-configure-sql-server-settings) |
| **Zusammenfassung** |[Zusammenfassung prüfen](#5-review-the-summary) |

## <a name="1-configure-basic-settings"></a>1. Grundeinstellungen konfigurieren

Geben Sie im Fenster **Grundeinstellungen** die folgenden Informationen an:

* Geben Sie einen eindeutigen **Namen**für den virtuellen Computer ein.

* Wählen als VM-Datenträgertyp die Option **SSD** aus, um eine optimale Leistung zu erzielen.

* Geben Sie einen **Benutzernamen** für das lokale Administratorkonto auf der VM ein. Dieses Konto wird auch der festen SQL Server-Serverrolle **sysadmin** hinzugefügt.

* Geben Sie ein sicheres **Kennwort**an.

* Wenn Sie über mehrere Abonnements verfügen, müssen Sie überprüfen, ob das Abonnement für die neue VM korrekt ist.

* Geben Sie im Feld **Ressourcengruppe** einen Namen für eine neue Ressourcengruppe ein. Klicken Sie alternativ auf **Vorhandene verwenden**, um eine vorhandene Ressourcengruppe zu verwenden. Bei einer Ressourcengruppe handelt es sich um eine Sammlung verwandter Ressourcen in Azure (virtuelle Computer, Speicherkonten, virtuelle Netzwerke usw.).

  > [!NOTE]
  > Die Verwendung einer neuen Ressourcengruppe ist hilfreich, wenn Sie SQL Server-Bereitstellungen in Azure testen oder sich gerade damit vertraut machen. Löschen Sie nach Beendigung des Tests die Ressourcengruppe, um die VM und alle Ressourcen, die dieser Ressourcengruppe zugeordnet sind, automatisch zu löschen. Weitere Informationen zu Ressourcengruppen finden Sie unter [Azure Resource Manager – Übersicht](../../../azure-resource-manager/resource-group-overview.md).

* Wählen Sie einen **Standort** für die Azure-Region aus, in der die Bereitstellung gehostet wird.

* Klicken Sie auf **OK** , um die Einstellungen zu speichern.

    ![Fenster mit SQL-Grundeinstellungen](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-basic.png)

## <a name="2-choose-virtual-machine-size"></a>2. VM-Größe auswählen

Wählen Sie beim Schritt **Größe** im Fenster **Größe auswählen** eine Größe für den virtuellen Computer aus. Im Fenster werden zuerst die empfohlenen Computergrößen angezeigt. Dies erfolgt basierend auf dem von Ihnen ausgewählten Image.

> [!IMPORTANT]
> Bei den voraussichtlichen monatlichen Kosten im Fenster **Größe auswählen** sind SQL Server-Lizenzierungskosten nicht berücksichtigt. Bei dieser Schätzung handelt es sich ausschließlich um die Kosten für den virtuellen Computer. Bei der Express Edition und der Developer Edition von SQL Server umfasst die Schätzung die voraussichtlichen Gesamtkosten. Bei anderen Editionen können Sie auf der Seite [Virtuelle Windows-Computer – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) Ihre Zieledition von SQL Server auswählen. Siehe auch die [Preisinformationen für virtuelle Azure-Computer unter SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md).

![Optionen für die Größe der SQL-VM](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-choose-a-size.png)

Informationen zu Produktionsworkloads finden Sie in den Empfehlungen für die Computergröße und -konfiguration unter [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](virtual-machines-windows-sql-performance.md). Wenn Sie eine Computergröße benötigen, die hier nicht aufgeführt ist, klicken Sie auf die Schaltfläche **Alle anzeigen**.

> [!NOTE]
> Weitere Informationen zu den Größen von virtuellen Computern finden Sie unter [Größen für virtuelle Computer](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Wählen Sie Ihre Computergröße aus, und klicken Sie dann auf **Auswählen**.

## <a name="3-configure-optional-features"></a>3. Optionale Features konfigurieren

Konfigurieren Sie im Fenster **Einstellungen** Azure-Speicher, Netzwerk und Überwachung für den virtuellen Computer.

* Wählen Sie unter **Speicher** > **Managed Disks verwenden** die Option **Ja** aus.

   > [!NOTE]
   > Microsoft empfiehlt Managed Disks für SQL Server. Managed Disks verwaltet den Speicher im Hintergrund. Wenn sich virtuelle Computer mit verwalteten Datenträgern in derselben Verfügbarkeitsgruppe befinden, verteilt Azure darüber hinaus die Speicherressourcen so, dass eine ausreichende Redundanz bereitgestellt wird. Weitere Informationen finden Sie in der [Übersicht über Azure Managed Disks][../managed-disks-overview.md). Genauere Informationen zu verwalteten Datenträgern in einer Verfügbarkeitsgruppe finden Sie unter [Verwenden von verwalteten Datenträgern für virtuelle Computer in einer Verfügbarkeitsgruppe](../manage-availability.md).

* Unter **Netzwerk**können Sie die automatisch angegebenen Werte übernehmen. Sie können auch auf jedes Feature klicken, um **Virtuelles Netzwerk**, **Subnetz**, **Öffentliche IP-Adresse** und **Netzwerksicherheitsgruppe** manuell zu konfigurieren. Behalten Sie für diese exemplarische Vorgehensweise die Standardwerte bei.

* In Azure ist die **Überwachung** mit demselben Speicherkonto, das für den virtuellen Computer angegeben wurde, standardmäßig aktiviert. Sie können diese Einstellungen hier ändern.

* Für diese exemplarische Vorgehensweise können Sie unter **Verfügbarkeitsgruppe** den Standardwert **Keine** beibehalten. Falls Sie die Einrichtung von SQL AlwaysOn-Verfügbarkeitsgruppen planen, können Sie die Verfügbarkeit konfigurieren, um die Neuerstellung der virtuellen Maschine zu vermeiden.  Weitere Informationen finden Sie unter [Verwalten der Verfügbarkeit virtueller Computer](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Klicken Sie auf **OK**, wenn Sie mit dem Konfigurieren der Einstellungen fertig sind.

## <a name="4-configure-sql-server-settings"></a>4. SQL Server-Einstellungen konfigurieren
Konfigurieren Sie im Fenster **SQL Server-Einstellungen** die speziellen Einstellungen und Optimierungen für SQL Server. Die Einstellungen, die Sie für SQL Server konfigurieren können, lauten wie folgt:

| Einstellung |
| --- |
| [Konnektivität](#connectivity) |
| [Authentifizierung](#authentication) |
| [Speicherkonfiguration](#storage-configuration) |
| [Automatisiertes Patchen](#automated-patching) |
| [Automatisierte Sicherung](#automated-backup) |
| [Azure-Schlüsseltresor-Integration](#azure-key-vault-integration) |
| [SQL Server Machine Learning Services](#sql-server-machine-learning-services) |

### <a name="connectivity"></a>Konnektivität

Geben Sie unter **SQL-Konnektivität**den Zugriffstyp an, den Sie für die SQL Server-Instanz auf dieser VM verwenden möchten. Wählen Sie für diese exemplarische Vorgehensweise die Option **Öffentlich (Internet)** aus, um für Computer oder Dienste im Internet Verbindungen mit SQL Server zuzulassen. Wenn diese Option aktiviert ist, konfiguriert Azure die Firewall und die Netzwerksicherheitsgruppe automatisch, um Datenverkehr über Port 1433 zuzulassen.

![SQL – Konnektivitätsoptionen](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-connectivity-alt.png)

> [!TIP]
> Standardmäßig lauscht SQL Server am bekannten Port **1433**. Ändern Sie den Port im vorherigen Dialogfeld, sodass an einem nicht standardmäßigen Port (beispielsweise 1401) gelauscht wird, um die Sicherheit zu erhöhen. Wenn Sie den Port ändern, muss die Verbindung in allen Clienttools (etwa SSMS) über diesen Port hergestellt werden.

Um über das Internet eine Verbindung mit SQL Server herzustellen, müssen Sie auch die SQL Server-Authentifizierung aktivieren. Dies ist im nächsten Abschnitt beschrieben.

Falls Sie Verbindungen mit der Datenbank-Engine über das Internet nicht aktivieren möchten, wählen Sie eine der folgenden Optionen:

* **Lokal (nur innerhalb der VM)** , um Verbindungen zu SQL Server nur von innerhalb des virtuellen Computers zuzulassen.
* **Privat (innerhalb des Virtual Network)** , um Verbindungen zu SQL Server von Computern oder Diensten in demselben virtuellen Netzwerk zuzulassen.

Generell sollten Sie die Sicherheit erhöhen, indem Sie die restriktivste Konnektivität wählen, die für Ihr Szenario zulässig ist. Bei allen Optionen können Sie aber Netzwerksicherheitsgruppen-Regeln und die SQL-/Windows-Authentifizierung verwenden, um für Sicherheit zu sorgen. Sie können die Netzwerksicherheitsgruppe bearbeiten, nachdem der virtuelle Computer erstellt wurde. Weitere Informationen finden Sie unter [Sicherheitsüberlegungen für SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-sql-security.md).

### <a name="authentication"></a>Authentifizierung

Wenn Sie die SQL Server-Authentifizierung benötigen, klicken Sie unter **Aktivieren** under **Aktivieren**besuchen.

![SQL Server-Authentifizierung](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-authentication.png)

> [!NOTE]
> Wenn Sie über das Internet auf SQL Server zugreifen möchten (Konnektivitätsoption „Öffentlich“), müssen Sie die SQL-Authentifizierung hier aktivieren. Für den öffentlichen Zugriff auf SQL Server muss die SQL-Authentifizierung verwendet werden.

Geben Sie beim Aktivieren der SQL Server-Authentifizierung **Anmeldename** und **Kennwort** an. Dieser Benutzername ist als Anmeldung für die SQL Server-Authentifizierung konfiguriert und ist Mitglied der festen Serverrolle **sysadmin** . Weitere Informationen zu Authentifizierungsmodi finden Sie unter [Auswählen eines Authentifizierungsmodus](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

Wenn Sie die SQL Server-Authentifizierung nicht aktivieren, können Sie das lokale Administratorkonto auf der VM verwenden, um die Verbindung mit der SQL Server-Instanz herzustellen.

### <a name="storage-configuration"></a>Speicherkonfiguration

Klicken Sie auf **Speicherkonfiguration** , um die Speicheranforderungen anzugeben.

![SQL – Speicherkonfiguration](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-storage.png)

> [!NOTE]
> Wenn Sie Ihren virtuellen Computer manuell für die Verwendung von Standardspeicher konfiguriert haben, ist diese Option nicht verfügbar. Die automatische Speicheroptimierung ist nur für Storage Premium verfügbar.

> [!TIP]
> Die Anzahl von Unterteilungen sowie die Obergrenze der einzelnen Schieberegler sind abhängig von der Größe des gewählten virtuellen Computers. Größere und leistungsfähigere virtuelle Computer können weiter zentral hochskaliert werden.

Sie können die Anforderungen als Eingabe-/Ausgabevorgänge pro Sekunde (IOPS), Durchsatz in MB/s und Gesamtspeichergröße angeben. Konfigurieren Sie diese Werte mit den Schiebereglern. Sie können diese Speichereinstellungen basierend auf der Workload ändern. Auf der Grundlage dieser Anforderungen berechnet das Portal automatisch die Anzahl anzufügender und zu konfigurierender Datenträger.

Wählen Sie unter **Speicher optimiert für**eine der folgenden Optionen:

* **Allgemein** ist die Standardeinstellung und bietet Unterstützung für die meisten Workloads.
* **Transaktional** wird der Speicher für herkömmliche OLTP-Datenbankworkloads optimiert.
* **Data Warehousing** wird der Speicher für Analyse- und Berichterstellungsworkloads optimiert.

### <a name="automated-patching"></a>Automatisiertes Patchen

**Automatisiertes Patchen** ist standardmäßig aktiviert. Beim automatisierten Patchen kann Azure automatisch Patches für SQL Server und das Betriebssystem anwenden. Geben Sie einen Wochentag, eine Uhrzeit und eine Dauer für das Wartungsfenster an. Azure führt das Patchen in diesem Wartungsfenster durch. Für die Zeitplanung des Wartungsfensters wird die Uhrzeit des VM-Gebietsschemas verwendet. Wenn Sie nicht möchten, dass SQL Server und das Betriebssystem automatisch gepatcht werden, klicken Sie auf **Deaktivieren**.  

![SQL – Automatisiertes Patchen](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-patching.png)

Weitere Informationen finden Sie unter [Automatisches Patchen für SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Automatisierte Sicherung

Aktivieren Sie automatische Datenbanksicherungen für alle Datenbanken unter **Automatisierte Sicherung**. Die automatisierte Sicherung ist standardmäßig deaktiviert.

Wenn Sie die automatisierte SQL-Sicherung aktivieren, können Sie folgende Einstellungen konfigurieren:

* Aufbewahrungszeitraum (Tage) für Sicherungen
* Verwendetes Speicherkonto für Sicherungen
* Verschlüsselungsoption und Kennwort für Sicherungen
* Sichern von Systemdatenbanken
* Konfigurieren des Sicherungszeitplans

Klicken Sie auf **Aktivieren**, um die Sicherung zu verschlüsseln. Geben Sie dann das **Kennwort**an. Azure erstellt ein Zertifikat zum Verschlüsseln der Sicherungen und verwendet das angegebene Kennwort, um das Zertifikat zu schützen.

![SQL – Automatisierte Sicherung](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-autobackup2.png)

 Weitere Informationen finden Sie unter [Automatisierte Sicherung für SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).

### <a name="azure-key-vault-integration"></a>Azure-Schlüsseltresor-Integration

Klicken Sie zum Speichern von Sicherheitsgeheimnissen für die Verschlüsselung in Azure auf **Azure Key Vault-Integration** und dann auf **Aktivieren**.

![SQL – Azure-Schlüsseltresor-Integration](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-akv.png)

In der folgenden Tabelle sind die Parameter aufgeführt, die zum Konfigurieren der Azure-Schlüsseltresor-Integration erforderlich sind.

| PARAMETER | Beschreibung | BEISPIEL |
| --- | --- | --- |
| **Schlüsseltresor-URL** |Gibt den Speicherort des Schlüsseltresors an. |https://contosokeyvault.vault.azure.net/ |
| **Prinzipalname** |Gibt den Namen des Azure Active Directory-Dienstprinzipals an. Dieser Name wird auch als Client-ID bezeichnet. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Geheimer Schlüssel des Prinzipals** |Der geheime Schlüssel des Azure Active Directory-Dienstprinzipals. Dieser geheime Schlüssel wird auch als geheimer Clientschlüssel bezeichnet. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Name der Anmeldeinformationen** |**Anmeldeinformationsname**: Die Azure-Schlüsseltresor-Integration erstellt Anmeldeinformationen in SQL Server, damit der virtuelle Computer Zugriff auf den Schlüsseltresor hat. Wählen Sie einen Namen für diese Anmeldeinformation. |mycred1 |

Weitere Informationen finden Sie unter [Konfigurieren der Azure-Schlüsseltresor-Integration für SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-ps-sql-keyvault.md).

### <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning Services

Sie können [SQL Server Machine Learning Services](https://msdn.microsoft.com/library/mt604845.aspx) aktivieren. Diese Option ermöglicht die Verwendung der erweiterten Analyse mit SQL Server 2017. Klicken Sie im Fenster **SQL Server-Einstellungen** auf **Aktivieren**.

![Aktivieren von SQL Server Machine Learning Services](./media/virtual-machines-windows-portal-sql-server-provision/azure-vm-sql-server-r-services.png)

Klicken Sie auf **OK**, wenn Sie mit dem Konfigurieren der SQL Server-Einstellungen fertig sind.

## <a name="5-review-the-summary"></a>5. Zusammenfassung prüfen

Überprüfen Sie im Fenster **Zusammenfassung** die angezeigten Informationen, und klicken Sie auf **Kaufen**, um SQL Server, die Ressourcengruppe und die für diesen virtuellen Computer angegebenen Ressourcen zu erstellen.

Sie können die Bereitstellung über das Azure-Portal überwachen. Auf der Schaltfläche **Benachrichtigungen** oben auf der Seite wird der grundlegende Status der Bereitstellung angezeigt.

> [!NOTE]
> Damit Sie sich einen Eindruck von Bereitstellungszeiten verschaffen können, habe ich eine SQL-VM für die Region „USA, Osten“ mit Standardeinstellungen bereitgestellt. Die Durchführung dieser Testbereitstellung dauerte ungefähr 12 Minuten. Je nach Region und den gewählten Einstellungen kann es aber sein, dass die Bereitstellung bei Ihnen schneller oder langsamer geht.

## <a id="remotedesktop"></a>Öffnen der VM mit Remotedesktop

Führen Sie die folgenden Schritte aus, um mithilfe von Remotedesktop eine Verbindung mit dem virtuellen SQL Server-Computer herzustellen:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Nachdem Sie eine Verbindung mit dem virtuellen SQL Server-Computer hergestellt haben, können Sie SQL Server Management Studio starten und mit Ihren Anmeldeinformationen für den lokalen Administrator eine Verbindung mit der Windows-Authentifizierung herstellen. Wenn Sie die SQL Server-Authentifizierung aktiviert haben, können Sie die Verbindung auch per SQL-Authentifizierung herstellen, indem Sie den während der Bereitstellung konfigurierten SQL-Benutzernamen und das Kennwort verwenden.

Durch den Zugriff auf den Computer können Sie die Computer- und SQL Server-Einstellungen je nach Ihren Anforderungen direkt ändern. Beispielsweise können Sie die Firewalleinstellungen konfigurieren oder die SQL Server-Konfigurationseinstellungen ändern.

## <a id="connect"></a>Herstellen einer Remoteverbindung mit SQL Server

In dieser exemplarischen Vorgehensweise haben Sie den Zugriffstyp **Öffentlich** für den virtuellen Computer und die **SQL Server-Authentifizierung** ausgewählt. Mit diesen Einstellungen wurde der virtuelle Computer automatisch so konfiguriert, dass SQL Server-Verbindungen von jedem Client über das Internet zulässig sind (vorausgesetzt, diese verfügen über die richtige SQL-Anmeldung).

> [!NOTE]
> Wenn Sie während der Bereitstellung nicht „Öffentlich“ ausgewählt haben, können Sie die SQL-Konnektivitätseinstellungen nach der Bereitstellung über das Portal ändern. Weitere Informationen finden Sie unter [Verbinden mit SQL Server-Instanzen auf virtuellen Azure-Maschinen (Ressourcen-Manager)](virtual-machines-windows-sql-connect.md#change).

In den folgenden Abschnitten wird gezeigt, wie Sie über das Internet eine Verbindung mit Ihrer SQL Server-VM-Instanz herstellen.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von SQL Server in Azure finden Sie unter [SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-sql-server-iaas-overview.md) und [Häufig gestellte Fragen](virtual-machines-windows-sql-server-iaas-faq.md).