<properties
    pageTitle="Automatisches Konfigurieren der AlwaysOn-Verfügbarkeitsgruppe auf virtuellen Azure-Computern – Resource Manager"
    description="Erstellen Sie eine AlwaysOn-Verfügbarkeitsgruppe mit Azure-VMs im Azure Resource Manager-Modus. In diesem Tutorial wird die Benutzeroberfläche in erster Linie zum automatischen Erstellen der gesamten Lösung verwendet."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/20/2016"
    ms.author="MikeRayMSFT" />


# <a name="configure-always-on-availability-group-in-azure-vm-automatically---resource-manager"></a>Automatisches Konfigurieren der AlwaysOn-Verfügbarkeitsgruppe auf virtuellen Azure-Computern – Resource Manager

> [AZURE.SELECTOR]
- [Resource Manager: Vorlage](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Resource Manager: manuell](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Klassisch: Benutzeroberfläche](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Klassisch: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

Dieses End-to-End-Tutorial zeigt Ihnen, wie Sie eine SQL Server-Verfügbarkeitsgruppe mit Azure-Ressourcen-Manager-VMs erstellen. Das Tutorial verwendet Azure-Blätter zum Konfigurieren einer Vorlage. Während Sie dieses Tutorial absolvieren, überprüfen Sie Standardeinstellungen, geben erforderliche Einstellungen ein und aktualisieren die Blätter im Portal.

Am Ende des Tutorials besteht Ihre SQL Server-Verfügbarkeitsgruppenlösung in Azure aus folgenden Elementen:

- Einem virtuellen Netzwerk, das mehrere Subnetze enthält, einschließlich einem Front-End- und Back-End-Subnetz

- Zwei Domänencontroller mit einer Active Directory-Domäne (AD)

- Zwei virtuellen SQL Server-Computern, die im Back-End-Subnetz bereitgestellt und der AD-Domäne beigetreten sind

- Einem WSFC-Cluster aus 3 Knoten mit dem Knotenmehrheit-Quorummodell

- Einer Verfügbarkeitsgruppe mit zwei Replikaten einer Verfügbarkeitsdatenbank mit synchronem Commit

Die folgende Abbildung ist eine grafische Darstellung der Lösung.

![Test Lab-Architektur für AG in Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Alle Ressourcen in dieser Lösung gehören zu einer einzigen Ressourcengruppe.

In diesem Tutorial wird Folgendes vorausgesetzt:

- Sie besitzen bereits ein Azure-Abonnement. Falls Sie noch keines besitzen, können Sie sich für ein [Testkonto registrieren](http://azure.microsoft.com/pricing/free-trial/).

- Sie wissen bereits, wie ein virtueller SQL Server-Computer mithilfe der GUI aus dem virtuellen Computerkatalog bereitgestellt wird. Weitere Informationen finden Sie unter [Bereitstellen eines virtuellen Computers mit SQL Server in Azure](virtual-machines-windows-portal-sql-server-provision.md)

- Sie verfügen bereits über solide Kenntnisse über Verfügbarkeitsgruppen. Weitere Informationen finden Sie unter [AlwaysOn-Verfügbarkeitsgruppen (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx).

>[AZURE.NOTE] Wenn Sie an der Verwendung von Verfügbarkeitsgruppen mit SharePoint interessiert sind, finden Sie Informationen hierzu unter [Konfigurieren von SQL Server 2012 AlwaysOn-Verfügbarkeitsgruppen für SharePoint 2013](http://technet.microsoft.com/library/jj715261.aspx).

In diesem Tutorial verwenden Sie das Azure-Portal zu folgenden Zwecken:

- Auswählen der AlwaysOn-Vorlage aus dem Portal

- Überprüfen der Vorlageneinstellungen und Aktualisieren einiger Konfigurationseinstellungen für Ihre Umgebung

- Überwachen von Azure beim Erstellen der gesamten Umgebung

- Verbinden mit einem der Domänencontroller und dann mit einer der SQL Server-Instanzen

[AZURE.INCLUDE [availability-group-template](../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]


## <a name="provision-the-cluster-from-the-gallery"></a>Bereitstellen des Clusters über den Katalog

Azure bietet ein Katalogimage für die gesamte Lösung. Um die Vorlage zu suchen:

1.  Melden Sie sich mit Ihrem Konto beim Azure-Portal an.
1.  Klicken Sie im Azure-Portal auf **+Neu**. Im Portal wird das Blatt „Neu“ geöffnet.
1.  Suchen Sie auf dem Blatt „Neu“ die Option **AlwaysOn**.
![Suchen der AlwaysOn-Vorlage](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
1.  Suchen Sie in den Suchergebnissen nach **SQL Server AlwaysOn-Cluster**.
![AlwaysOn-Vorlage](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
1.  Wählen Sie unter **Bereitstellungsmodell auswählen** die Option **Resource Manager**.

### <a name="basics"></a>Grundlagen

Klicken Sie auf **Grundlagen** , und konfigurieren Sie Folgendes:

- **Administratorbenutzername** ist ein Benutzerkonto mit Domänenadministratorberechtigungen und ein Mitglied der festen SQL Server-Rolle „Sysadmin“ für beide Instanzen von SQL Server. Verwenden Sie für dieses Tutorial **DomainAdmin**.

- **Kennwort** ist das Kennwort für das Domänenadministratorkonto. Verwenden Sie ein komplexes Kennwort. Bestätigen Sie das Kennwort.

- **Abonnement** ist das Abonnement, das von Azure für die Ausführung aller für die Verfügbarkeitsgruppe bereitgestellten Ressourcen belastet wird. Sie können ein anderes Abonnement angeben, wenn Ihr Konto über mehrere Abonnements verfügt.

- **Ressourcengruppe** ist der Name der Gruppe, zu der alle von diesem Tutorial erstellten Azure-Ressourcen gehören sollen. Verwenden Sie für dieses Tutorial **SQL-HA-RG**. Weitere Informationen finden Sie unter (Azure-Ressourcen-Manager-Übersicht) [resource-group-overview.md/#resource-groups].

- **Standort** ist die Azure-Region, in der die Ressourcen für dieses Tutorial erstellt werden. Wählen Sie eine Azure-Region zum Hosten der Infrastruktur.

Das Blatt **Grundlagen** sieht wie folgt aus:

![Grundlagen](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

- Klicken Sie auf **OK**.

### <a name="domain-and-network-settings"></a>Domänen- und Netzwerkeinstellungen

Diese Azure-Katalog-Vorlage erstellt eine neue Domäne mit neuen Domänencontrollern. Außerdem werden ein neues Netzwerk und zwei Subnetze erstellt. Die Vorlage ermöglicht nicht das Erstellen der Server in einer vorhandenen Domäne oder einem virtuellen Netzwerk. Im nächsten Schritt werden die Domänen- und Netzwerkeinstellungen konfiguriert.

Überprüfen Sie auf dem Blatt **Domänen- und Netzwerkeinstellungen** die voreingestellten Werte für die Domänen- und Netzwerkeinstellungen:

- **Gesamtstruktur-Stammdomänenname** ist der Domänenname, der für die AD-Domäne verwendet wird, die den Cluster hostet. Verwenden Sie für das Tutorial **contoso.com**.

- **Name des virtuellen Netzwerks** ist der Netzwerkname für das virtuelle Azure-Netzwerk. Verwenden Sie für dieses Tutorial **autoHAVNET**.

- **Domänencontroller-Subnetzname** ist der Name eines Teils des virtuellen Netzwerks, der den Domänencontroller hostet. Verwenden Sie für dieses Tutorial **subnet-1**. Dieses Subnetz verwendet das Adresspräfix **10.0.0.0/24**.

- **SQL Server-Subnetzname** ist der Name eines Teils des virtuellen Netzwerks, der die SQL Server-Instanzen und den Dateifreigabenzeugen hostet. Verwenden Sie für dieses Tutorial **subnet-2**. Dieses Subnetz verwendet das Adresspräfix **10.0.1.0/26**.

Weitere Informationen zu virtuellen Netzwerken in Azure finden Sie unter [Virtuelle Netzwerke im Überblick](../virtual-network/virtual-networks-overview.md).  

Die **Domänen- und Netzwerkeinstellungen** sollten wie folgt aussehen:

![Domänen- und Netzwerkeinstellungen](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Bei Bedarf können Sie diese Werte ändern. Für dieses Tutorial verwenden wir die voreingestellten Werte.

- Überprüfen Sie die Einstellungen, und klicken Sie auf **OK**.

###<a name="availability-group-settings"></a>Einstellungen der Verfügbarkeitsgruppe

Überprüfen Sie unter **Einstellungen der Verfügbarkeitsgruppe** die voreingestellten Werte für Verfügbarkeitsgruppe und Listener.

- **Name der Verfügbarkeitsgruppe** ist der Name der Clusterressource für die Verfügbarkeitsgruppe. Verwenden Sie für dieses Tutorial **Contoso-ag**.

- **Listenername der Verfügbarkeitsgruppe** wird vom Cluster und dem internen Load Balancer verwendet. Clients, die sich mit SQL Server verbinden, können diesen Namen für die Verbindung mit dem entsprechenden Replikat der Datenbank verwenden. Verwenden Sie für dieses Tutorial **Contoso-listener**.

-  **Port des Verfügbarkeitsgruppenlisteners** gibt den TCP-Port an, den der SQL Server-Listener verwendet. Verwenden Sie für dieses Tutorial den Standardport **1433**.

Bei Bedarf können Sie diese Werte ändern. Verwenden Sie für dieses Tutorial die voreingestellten Werte.  

![Einstellungen der Verfügbarkeitsgruppe](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

- Klicken Sie auf **OK**.

###<a name="vm-size,-storage-settings"></a>Größe des virtuellen Computers, Einstellungen des Speichers

Wählen Sie unter **Größe des virtuellen Computers, Einstellungen des Speichers** eine SQL Server-VM-Größe aus, und überprüfen Sie die anderen Einstellungen.

- **SQL Server-VM-Größe** ist die Größe des virtuellen Azure-Computers für beide SQL Server-Instanzen. Wählen Sie eine angemessene VM-Größe für Ihre Workload. Wenn Sie diese Umgebung für das Tutorial erstellen, verwenden Sie **DS2**. Wählen Sie für Produktionsworkloads eine VM-Größe, die die Workload unterstützen kann. Viele Produktionsworkloads benötigen mindestens **DS4** . Die Vorlage erstellt zwei virtuelle Computer dieser Größe und installiert auf jedem SQL Server. Weitere Informationen finden Sie unter [Größen für virtuelle Computer](virtual-machines-linux-sizes.md).

>[AZURE.NOTE]Azure installiert die Enterprise Edition von SQL Server. Die Kosten hängen von der Edition und der Größe des virtuellen Computers ab. Ausführliche Informationen zu aktuellen Kosten finden Sie unter [Virtual Machines – Preise](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

- **Domänencontroller-VM-Größe** ist die Größe des virtuellen Computers für die Domänencontroller. Verwenden Sie für dieses Tutorial **D2**.

- **Dateifreigabenzeugen-VM-Größe** ist die Größe des virtuellen Computers für den Dateifreigabenzeugen. Verwenden Sie für dieses Tutorial **A1**.

- **SQL-Speicherkonto** ist der Name des Speicherkontos, auf dem die SQL Server-Daten und Betriebssystemdatenträger gespeichert werden. Verwenden Sie für dieses Tutorial **alwaysonsql01**.

- **DC-Speicherkonto** ist der Name des Speicherkontos für die Domänencontroller. Verwenden Sie für dieses Tutorial **alwaysondc01**.

- **Größe des Datenträgers für SQL Server-Daten in TB** ist die Größe des SQL Server-Datenträgers in TB. Geben Sie eine Zahl von 1 bis 4 ein. Dies ist die Größe des Datenträgers, der jeder SQL Server-Instanz zugeordnet werden soll. Verwenden Sie für dieses Tutorial **1**.

- **Speicheroptimierung** legt bestimmte Speicherkonfigurationseinstellungen basierend auf dem Workloadtyp für die SQL Server-VMs fest. Alle SQL Server-Instanzen in diesem Szenario verwenden Storage Premium, wobei der Azure-Datenträger-Hostcache als schreibgeschützt festgelegt ist. Darüber hinaus können Sie SQL Server-Einstellungen durch Auswahl einer der folgenden drei Einstellungen für die Workload optimieren:

    - **Allgemeine Workload** legt keine bestimmten Konfigurationseinstellungen fest.

    - **Transaktionsverarbeitung** legt die Ablaufverfolgungsflags 1117 und 1118 fest.

    - **Data Warehousing** legt die Ablaufverfolgungsflags 1117 und 610 fest.

Verwenden Sie für dieses Tutorial **Allgemeine Workload**.

![VM-Größe, Speichereinstellungen](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

- Überprüfen Sie die Einstellungen, und klicken Sie auf **OK**.

####<a name="a-note-about-storage"></a>Ein Hinweis zum Speicher

Weitere Optimierungen hängen von der Größe der SQL Server-Datenträger ab. Für jedes Terabyte des Datenträgers fügt Azure 1 TB Storage Premium (SSD) hinzu. Wenn ein Server 2 TB oder mehr benötigt, erstellt die Vorlage einen Speicherpool auf jeder SQL-Server-Instanz. Ein Speicherpool ist eine Form der Speichervirtualisierung, bei der mehrere Datenträger konfiguriert werden, um höhere Kapazität, Stabilität und Leistung bereitzustellen.  Die Vorlage erstellt dann einen Speicherplatz im Speicherpool und stellt diesen gegenüber dem Betriebssystem als einen einzelnen Datenspeicher dar. Die Vorlage bestimmt diesen Datenträger als Datenträger für SQL Server-Daten. Die Vorlage optimiert den Speicherpool für SQL Server mit den folgenden Einstellungen:

- Die Stripesetgröße ist die Interleaveeinstellung für den virtuellen Datenträger. Für Transaktionsworkloads beträgt die Einstellung 64 KB. Für Data Warehousing-Workloads beträgt die Einstellung 256 KB.

- Resilienz ist einfach (keine Resilienz).

>[AZURE.NOTE] Azure Storage Premium ist lokal redundant und behält drei Kopien der Daten innerhalb einer Region, sodass keine zusätzliche Resilienz beim Speicherpool erforderlich ist.

- Die Anzahl der Spalten entspricht der Anzahl von Datenträgern im Speicherpool.

Weitere Informationen zu Speicherplatz und Speicherpools finden Sie unter:

- [Speicherplätze – Übersicht](http://technet.microsoft.com/library/hh831739.aspx)

- [Windows Server-Sicherung und -Speicherpools](http://technet.microsoft.com/library/dn390929.aspx)

Weitere Informationen zu optimalen Verfahren für die SQL Server-Konfiguration finden Sie unter [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](virtual-machines-windows-sql-performance.md)


###<a name="sql-server-settings"></a>SQL Server-Einstellungen

Überprüfen und ändern Sie unter **SQL Server-Einstellungen** das Präfix des SQL Server-VM-Namens, die SQL Server-Version, das SQL Server-Dienstkonto und -Kennwort sowie den Wartungszeitplan für automatisches SQL-Patchen.

- **SQL Server-Namenspräfix** wird verwendet, um einen Namen für jede SQL Server-Instanz zu erstellen. Verwenden Sie für dieses Tutorial **Contoso-ag**. Die SQL Server-Namen sind *Contoso-ag-0* und *Contoso-ag-1*.

- **SQL Server-Version** ist die Version von SQL Server. Verwenden Sie für dieses Tutorial **SQL Server 2014**. Sie können auch **SQL Server 2012** oder **SQL Server 2016** wählen.

- **SQL Server-Dienstkonto-Benutzername** ist der Domänenkontoname für den SQL Server-Dienst. Verwenden Sie für dieses Tutorial **sqlservice**.

- **Kennwort** ist das Kennwort für das SQL Server-Dienstkonto.  Verwenden Sie ein komplexes Kennwort. Bestätigen Sie das Kennwort.

- **Wartungszeitplan für automatisches SQL-Patchen** gibt den Wochentag an, an dem Azure automatisch Patches für die SQL Server-Instanzen aufspielt. Geben Sie für dieses Tutorial **Sunday**ein.

- **Wartungsstartzeit für automatisches SQL-Patchen** ist die Uhrzeit für die Azure-Region, zu der das automatische Patchen beginnt.

>[AZURE.NOTE]Die Patchfenster der einzelnen VMs sind im Abstand von einer Stunde gestaffelt. Es wird nur jeweils ein virtueller Computer gepatcht, um eine Unterbrechung der Dienste zu vermeiden.

![SQL Server-Einstellungen](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Überprüfen Sie die Einstellungen, und klicken Sie auf **OK**.

###<a name="summary"></a>Zusammenfassung

Auf der Zusammenfassungsseite überprüft Azure die Einstellungen. Sie können auch die Vorlage herunterladen. Überprüfen Sie die Zusammenfassung. Klicken Sie auf **OK**.

###<a name="buy"></a>Kaufen

Dieses letzte Blatt enthält **Nutzungsbedingungen** und die **Datenschutzrichtlinie**. Überprüfen Sie diese Informationen. Wenn Sie zum Erstellen der virtuellen Computer und aller anderen erforderlichen Ressourcen für die Verfügbarkeitsgruppe in Azure bereit sind, klicken Sie auf **Erstellen**.

Das Azure-Portal erstellt die Ressourcengruppe und alle Ressourcen.

##<a name="monitor-deployment"></a>Überwachen der Bereitstellung

Überwachen Sie den Fortschritt der Bereitstellung über das Azure-Portal. Ein Symbol für die Bereitstellung wird automatisch an das Dashboard des Azure-Portals geheftet.

![Azure-Dashboard](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

##<a name="connect-to-sql-server"></a>Herstellen einer Verbindung mit SQL Server

Die neuen Instanzen von SQL Server werden auf virtuellen Computern ausgeführt, die nicht mit dem Internet verbunden sind. Die Domänencontroller besitzen jedoch eine Internetverbindung. Um die SQL Server-Instanzen mit Remotedesktop zu verbinden, stellen Sie zuerst eine RDP-Verbindung mit einem der Domänencontroller her. Stellen Sie von dem Domänencontroller aus eine zweite RDP-Verbindung mit der SQL Server-Instanz her.

Um eine RDP-Verbindung mit dem primären Domänencontroller herzustellen, gehen Sie folgendermaßen vor:

1.  Vergewissern Sie sich am Dashboard des Azure-Portals, dass die Bereitstellung erfolgreich war.

1.  Klicken Sie auf **Ressourcen**.

1.  Klicken Sie auf dem Blatt **Ressourcen** auf **ad-primary-dc** – dies ist der Computername des virtuellen Computers für den primären Domänencontroller.

1.  Klicken Sie auf dem Blatt **ad-primary-dc** click **Verbinden**. Ihr Browser fragt, ob Sie das Remoteverbindungsobjekt öffnen oder speichern möchten. Klicken Sie auf **Öffnen**.
![Verbindung mit DC herstellen](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/13-ad-primary-dc-connect.png)
1.  **Remotedesktopverbindung** werden Sie möglicherweise darauf hingewiesen, dass der Herausgeber dieser Remoteverbindung nicht identifiziert werden kann. Klicken Sie auf **Verbinden**.

1.  Die Windows-Sicherheit fordert Sie auf, zur Verbindung mit der IP-Adresse des primären Domänencontrollers Ihre Anmeldeinformationen einzugeben. Klicken Sie auf **Anderes Konto verwenden**. Geben Sie unter **Benutzername** die Zeichenfolge **contoso\DomainAdmin** ein. Dieses Konto haben Sie für den Administratorbenutzernamen ausgewählt. Verwenden Sie das komplexe Kennwort, das Sie beim Konfigurieren der Vorlage ausgewählt haben.

1.  **Remotedesktop** werden Sie möglicherweise darauf hingewiesen, dass der Remotecomputer aufgrund von Problemen mit dem Sicherheitszertifikat nicht authentifiziert werden konnte. Der Name des Sicherheitszertifikats wird angezeigt. Wenn Sie das Tutorial ausgeführt haben, lautet der Name **ad-primary-dc.contoso.com**. Klicken Sie auf **Ja**.

Sie sind jetzt mit dem primären Domänencontroller verbunden. Gehen Sie folgendermaßen vor, um eine RDP-Verbindung mit der SQL Server-Instanz herzustellen:

1.  Öffnen Sie auf dem Domänencontroller die **Remotedesktopverbindung**.

1.  Geben Sie für **Computer**den Namen einer der SQL Server-Instanzen ein. Geben Sie für dieses Tutorial **sqlserver-0**ein.

1.  Verwenden Sie das gleiche Benutzerkonto und Kennwort wie beim Herstellen der RDP-Verbindung mit dem Domänencontroller.

Sie sind jetzt über eine RDP-Verbindung mit der SQL Server-Instanz verbunden. Sie können jetzt das SQL Server-Management Studio öffnen, eine Verbindung mit der Standardinstanz von SQL Server herstellen und sicherstellen, dass die Verfügbarkeitsgruppe konfiguriert ist.





<!--HONumber=Oct16_HO2-->


