---
title: Einrichten der Notfallwiederherstellung für Azure-VMs in eine sekundäre Region mit Azure Site Recovery (Vorschau)
description: Erfahren Sie, wie Sie die Notfallwiederherstellung für Azure-VMs in eine andere Region mit dem Dienst „Azure Site Recovery“ durchführen.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/02/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: ca1f8fcd3a69e3f2e287c3d627f41c0f493bea1f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2018
---
# <a name="set-up-disaster-recovery-for-azure-vms-to-a-secondary-azure-region-preview"></a>Einrichten einer Notfallwiederherstellung für Azure-VMs in eine sekundäre Azure-Region (Vorschau)

Der Dienst [Azure Site Recovery](site-recovery-overview.md) unterstützt Ihre Strategie zur Notfallwiederherstellung, indem Replikation, Failover und Failback von lokalen Computern und virtuellen Azure-Computern (Virtual Machines, VMs) verwaltet und orchestriert werden.

In diesem Tutorial wird erläutert, wie Sie die Notfallwiederherstellung in eine sekundäre Azure-Region für Azure-VMs einrichten. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Recovery Services-Tresors
> * Überprüfen der Zielressourceneinstellungen
> * Einrichten des ausgehenden Zugriffs für VMs
> * Aktivieren der Replikation für eine VM

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Stellen Sie sicher, dass Sie die [Architektur und die Komponenten des Szenarios](concepts-azure-to-azure-architecture.md) verstehen.
- Überprüfen Sie die [Supportanforderungen](site-recovery-support-matrix-azure-to-azure.md) für alle Komponenten.

## <a name="create-a-vault"></a>Erstellen eines Tresors

Erstellen Sie den Tresor in einer beliebigen Region außer der Quellregion.

1. Melden Sie sich unter [Azure-Portal](https://portal.azure.com) > **Recovery Services** an.
2. Klicken Sie auf **Ressource erstellen** > **Überwachung und Verwaltung** > **Backup und Site Recovery**.
3. Geben Sie unter **Name**einen Anzeigenamen an, mit dem der Tresor identifiziert wird. Wenn Sie mehrere Abonnements haben, wählen Sie das gewünschte aus.
4. Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine vorhandene aus. Geben Sie eine Azure-Region an. Eine Liste mit den unterstützten Regionen finden Sie in den [Preisdetails zu Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/)unter „Geografische Verfügbarkeit“.
5. Um schnell über das Dashboard auf den Tresor zuzugreifen, klicken Sie auf **An Dashboard anheften** und anschließend auf **Erstellen**.

   ![Neuer Tresor](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   Der neue Tresor wird dem **Dashboard** unter **Alle Ressourcen** und der Hauptseite **Recovery Services-Tresore** hinzugefügt.

## <a name="verify-target-resources"></a>Überprüfen der Zielressourcen

1. Vergewissern Sie sich, dass Ihr Azure-Abonnement das Erstellen von VMs in der für die Notfallwiederherstellung verwendeten Zielregion zulässt. Wenden Sie sich an den Support, um das erforderliche Kontingent zu aktivieren.

2. Stellen Sie sicher, dass Ihr Abonnement über ausreichend Ressourcen verfügt, um VMs zu unterstützen, die so groß wie die Quell-VMs sind. Site Recovery wählt dieselbe oder eine möglichst ähnliche Größe für die Ziel-VM aus.

## <a name="configure-outbound-network-connectivity"></a>Konfigurieren der ausgehenden Netzwerkkonnektivität

Damit Site Recovery erwartungsgemäß funktioniert, müssen Sie für die VMs, die Sie replizieren möchten, einige Änderungen an der ausgehenden Netzwerkkonnektivität vornehmen.

- Site Recovery unterstützt die Verwendung eines Authentifizierungsproxys zur Steuerung der Netzwerkkonnektivität nicht.
- Wenn Sie einen Authentifizierungsproxy verwenden, kann die Replikation nicht aktiviert werden.

### <a name="outbound-connectivity-for-urls"></a>Ausgehende Konnektivität für URLs

Wenn Sie einen URL-basierten Firewallproxy zum Steuern der ausgehenden Konnektivität verwenden, sollten Sie den Zugriff auf die folgenden URLs zulassen, die von Site Recovery verwendet werden.

| **URL** | **Details** |
| ------- | ----------- |
| *.blob.core.windows.net | Ermöglicht das Schreiben von Daten aus der VM in das Cachespeicherkonto in der Quellregion |
| login.microsoftonline.com | Stellt die Autorisierung und Authentifizierung für Site Recovery-Dienst-URLs bereit. |
| *.hypervrecoverymanager.windowsazure.com | Ermöglicht die Kommunikation der VM mit Site Recovery |
| *.servicebus.windows.net | Ermöglicht es der VM, die Site Recovery-Überwachung und -Diagnosedaten zu schreiben |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Ausgehende Konnektivität für IP-Adressbereiche

Wenn Sie die Konnektivität in ausgehender Richtung mithilfe von IP-Adressen anstelle von URLs steuern möchten, setzen Sie die entsprechenden Datencenterbereiche, Office 365-Adressen und Dienstendpunkt-Adressen für IP-basierte Firewalls, Proxys oder NSG-Regeln auf die Whitelist.

  - [IP-Bereiche für Microsoft Azure-Rechenzentren](http://www.microsoft.com/en-us/download/details.aspx?id=41653)
  - [IP-Bereiche für Azure-Rechenzentren in Deutschland](http://www.microsoft.com/en-us/download/details.aspx?id=54770)
  - [IP-Bereiche für Azure-Rechenzentren in China](http://www.microsoft.com/en-us/download/details.aspx?id=42064)
  - [URLs und IP-Adressbereiche von Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [IP-Adressen des Site Recovery-Dienstendpunkts](https://aka.ms/site-recovery-public-ips)

Sie können die erforderlichen NSG-Regeln mit diesem [Skript](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702) erstellen.

## <a name="verify-azure-vm-certificates"></a>Überprüfen der Azure-VM-Zertifikate

Stellen Sie sicher, dass alle neuesten Stammzertifikate auf den Windows- oder Linux-VMs vorhanden sind, die Sie replizieren wollen. Wenn die neuesten Stammzertifikate nicht vorhanden sind, kann die VM aufgrund von Sicherheitseinschränkungen nicht bei Site Recovery registriert werden.

- Installieren Sie alle aktuellen Windows-Updates auf Windows-VMs, damit alle vertrauenswürdigen Stammzertifikate auf den Computern vorhanden sind. Führen Sie in einer nicht verbundenen Umgebung den Standardprozess für Windows Update und Zertifikatupdates in Ihrer Organisation durch.

- Befolgen Sie bei Linux-VMs die Anleitung Ihres Linux-Händlers, um die aktuellen vertrauenswürdigen Stammzertifikate und die Zertifikatssperrliste auf der VM abzurufen.

## <a name="set-permissions-on-the-account"></a>Festlegen von Berechtigungen für das Konto

Azure Site Recovery bietet drei integrierte Rollen zum Steuern von Site Recovery-Verwaltungsvorgängen.

- **Site-Recovery Contributor**: Diese Rolle verfügt über alle erforderlichen Berechtigungen zum Verwalten von Azure Site Recovery-Vorgängen in einem Tresor von Recovery Services. Ein Benutzer mit dieser Rolle kann jedoch keinen Tresor von Recovery Services erstellen oder löschen oder anderen Benutzern Zugriffsrechte zuweisen. Diese Rolle eignet sich optimal für Administratoren der Notfallwiederherstellung, die die Notfallwiederherstellung für Anwendungen oder gesamte Unternehmen aktivieren und verwalten können.

- **Site Recovery-Operator**: Diese Rolle verfügt über Berechtigungen zum Ausführen und Verwalten von Failover- und Failback-Vorgängen. Ein Benutzer, der über diese Rolle verfügt, kann die Replikation nicht aktivieren oder deaktivieren, keine Tresore erstellen oder löschen, keine neue Infrastruktur registrieren oder anderen Benutzern keine Zugriffsrechte zuweisen. Diese Rolle eignet sich optimal für einen Notfallwiederherstellungsexperten, der ein Failover für einen virtuellen Computer oder Anwendungen durchführen kann, wenn er dazu vom Besitzer der Anwendung oder IT-Administratoren angewiesen wird. Nachdem der Notfall behandelt wurde, kann der DR-Experte den virtuellen Computer erneut schützen und ein Failback durchführen.

- **Site Recovery-Leser**: Diese Rolle verfügt über Berechtigungen zum Anzeigen aller Site Recovery-Verwaltungsvorgänge. Diese Rolle eignet sich optimal für eine IT-Überwachungsführungskraft, die den aktuellen Schutzstatus überwachen und Supporttickets ausstellen kann.

Erfahren Sie mehr über [integrierte Rollen von Azure RBAC](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication"></a>Aktivieren der Replikation

### <a name="select-the-source"></a>Auswählen der Quelle

1. Klicken Sie unter „Recovery Services-Tresore“ auf den Tresornamen > **+Replizieren**.
2. Wählen Sie unter **Quelle** die Option **Azure – VORSCHAU**.
3. Wählen Sie unter **Quellstandort** die Azure-Quellregion aus, in der Ihre VMs derzeit ausgeführt werden.
4. Wählen Sie das **Azure-VM-Bereitstellungsmodell** für Ihre VMs: **Resource Manager** oder **Klassisch**.
5. Wählen Sie für Resource Manager-VMs die **Quellressourcengruppe** oder für klassische VMs den **Clouddienst**.
6. Klicken Sie auf **OK** , um die Einstellungen zu speichern.

### <a name="select-the-vms"></a>Auswählen der virtuellen Computer

Site Recovery ruft eine Liste der virtuellen Computer ab, die dem Abonnement und der Ressourcengruppe bzw. dem Clouddienst zugeordnet sind.

1. Wählen Sie unter **Virtuelle Computer** die VMs aus, die Sie replizieren möchten.
2. Klicken Sie auf **OK**.

### <a name="configure-replication-settings"></a>Konfigurieren der Replikationseinstellungen

Site Recovery erstellt Standardeinstellungen und Replikationsrichtlinien für die Zielregion. Sie können die Einstellungen abhängig von Ihren Anforderungen ändern.

1. Klicken Sie auf **Einstellungen**, um die Ziel- und Replikationseinstellungen anzuzeigen.
2. Um die Standardzieleinstellungen zu überschreiben, klicken Sie neben **Ressourcengruppe, Netzwerk, Speicher und Verfügbarkeitsgruppen** auf **Anpassen**.

  ![Konfigurieren von Einstellungen](./media/azure-to-azure-tutorial-enable-replication/settings.png)


- **Zielspeicherort**: Die Zielregion, die zur Notfallwiederherstellung verwendet wird. Der Zielspeicherort sollte mit dem Speicherort des Site Recovery-Tresors übereinstimmen.

- **Zielressourcengruppe**: Die Ressourcengruppe in der Zielregion, zu der Azure-VMs nach einem Failover gehören. Site Recovery erstellt standardmäßig in der Zielregion eine neue Ressourcengruppe mit dem Suffix „asr“. Als Ressourcengruppenstandort der Zielressourcengruppe kann eine beliebige Region ausgewählt werden, mit Ausnahme der Region, in der die virtuellen Quellcomputer gehostet werden. 

- **Virtuelles Zielnetzwerk**: Das Netzwerk in der Zielregion, in dem sich Azure-VMs nach einem Failover befinden.
  Site Recovery erstellt standardmäßig in der Zielregion ein neues virtuelles Netzwerk (und Subnetze) mit dem Suffix „asr“.

- **Cachespeicherkonten**: Site Recovery verwendet ein Speicherkonto in der Quellregion. Änderungen an Quell-VMs werden vor der Replikation am Zielspeicherort an dieses Konto gesendet.

- **Zielspeicherkonten (wenn die Quell-VM keine verwalteten Datenträger verwendet)**: Standardmäßig erstellt Site Recovery ein neues Speicherkonto in der Zielregion, um das Quell-VM-Speicherkonto zu spiegeln.

- **Verwaltete Replikatdatenträger (wenn die Quell-VM verwaltete Datenträger verwendet)**: Site Recovery erstellt verwaltete Replikatdatenträger in der Zielregion, um die verwalteten Datenträger der Quell-VM zu spiegeln. Dabei wird der gleiche Speichertyp (Standard oder Premium) verwendet wie für die verwalteten Datenträger der Quell-VM.

- **Zielverfügbarkeitsgruppen**: Standardmäßig erstellt Site Recovery in der Zielregion eine neue Verfügbarkeitsgruppe mit dem Suffix „asr“. Sie können Verfügbarkeitsgruppen nur dann hinzufügen, wenn virtuelle Computer zu einer Gruppe in der Quellregion gehören.

Um die Einstellungen für die Standardreplikationsrichtlinie zu überschreiben, klicken Sie neben **Replikationsrichtlinie** auf **Anpassen**.  

- **Replikationsrichtlinienname**: Name der Richtlinie.

- **Aufbewahrungszeitraum des Wiederherstellungspunkts**: Standardmäßig behält Site Recovery Wiederherstellungspunkte 24 Stunden lang bei. Sie können einen Wert zwischen 1 und 72 Stunden konfigurieren.

- **App-konsistente Momentaufnahmenhäufigkeit**: Standardmäßig erstellt Site Recovery alle vier Stunden eine App-konsistente Momentaufnahme. Sie können einen Wert zwischen 1 und 12 Stunden konfigurieren. Eine App-konsistente Momentaufnahme ist eine Zeitpunkt-Momentaufnahme der Anwendungsdaten innerhalb der VM. VSS (Volume Shadow Copy Service, Volumeschattenkopie-Dienst) stellt sicher, dass Apps zum Zeitpunkt der Momentaufnahme konsistent sind.

- **Replikationsgruppe**: Wenn für Ihre Anwendung VM-übergreifende Konsistenz mehrerer virtueller Computer erforderlich ist, können Sie eine Replikationsgruppe für diese VMs erstellen. Standardmäßig sind die ausgewählten VMs nicht Teil einer Replikationsgruppe.

  Klicken Sie neben **Replikationsrichtlinie** auf **Anpassen**, und wählen Sie **Ja** zum Aktivieren der Konsistenz mehrerer virtueller Computer, um die VMs einer Replikationsgruppe hinzuzufügen. Sie können eine neue Replikationsgruppe erstellen oder eine vorhandene Replikationsgruppe verwenden. Wählen Sie die VMs aus, die der Replikationsgruppe angehören sollen, und klicken Sie auf **OK**.

> [!IMPORTANT]
  Alle Computer in einer Replikationsgruppe verfügen beim Failover über absturz- und anwendungskonsistente Wiederherstellungspunkte. Das Aktivieren der Konsistenz mehrerer virtueller Computer kann sich auf die Leistung der Workload auswirken und sollte nur verwendet werden, wenn Computer dieselbe Workload ausführen und eine Konsistenz erforderlich ist.

> [!IMPORTANT]
  Wenn Sie die Multi-VM-Konsistenz aktivieren, kommunizieren Computer in der Replikationsgruppe über den Port 20004 miteinander. Stellen Sie sicher, dass die interne Kommunikation zwischen den VMs über Port 20004 nicht durch eine Firewallappliance blockiert wird. Wenn Sie Linux-VMs in eine Replikationsgruppe einschließen möchten, stellen Sie sicher, dass der ausgehende Datenverkehr auf Port 20004 gemäß den Anweisungen für die jeweilige Linux-Version manuell geöffnet wird.

### <a name="track-replication-status"></a>Nachverfolgen des Replikationsstatus

1. Klicken Sie unter **Einstellungen**  auf **Aktualisieren**, um den aktuellen Status abzurufen.

2. Sie können den Fortschritt des Auftrags **Schutz aktivieren** unter **Einstellungen** > **Aufträge** > **Site Recovery-Aufträge** verfolgen.

3. Unter **Einstellungen** > **Replizierte Elemente** können Sie den Status der VMs und den der ersten Replikation einsehen. Klicken Sie auf die VM, um ein Drilldown auf die zugehörigen Einstellungen auszuführen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die Notfallwiederherstellung für eine Azure-VM konfiguriert. Als Nächstes wird Ihre Konfiguration getestet.

> [!div class="nextstepaction"]
> [Durchführen eines Notfallwiederherstellungsverfahrens](azure-to-azure-tutorial-dr-drill.md)
