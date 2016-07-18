<properties
	pageTitle="Überwachung und Problembehandlung für den Schutz von virtuellen Computern und physischen Servern | Microsoft Azure" 
	description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung virtueller Computer auf lokalen Servern zu Azure oder einem sekundären Rechenzentrum. Verwenden Sie diesen Artikel zur Überwachung und Problembehandlung für den Schutz von VMM- und Hyper-V-Standorten." 
	services="site-recovery" 
	documentationCenter="" 
	authors="anbacker" 
	manager="mkjain" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="07/06/2016" 
	ms.author="anbacker"/>
	
# Überwachung und Problembehandlung für den Schutz von virtuellen Computern und physischen Servern

In diesem Leitfaden zur Überwachung und Problembehandlung erhalten Sie Informationen zur Nachverfolgung der Replikationsintegrität und zu den Problembehandlungsverfahren für Azure Site Recovery.

## Grundlegendes zu den Komponenten

### Bereitstellung des VMM-/physichen Standorts für die Replikation zwischen lokalen Standorten und Azure.
Zum Einrichten der DR zwischen lokalem VMware-/physischem Computer müssen Konfigurationsserver, Masterziel- und Prozessserver konfiguriert sein. Beim Aktivieren des Schutzes für den Quellserver installiert Azure Site Recovery den Mobilitätsdienst. Nach einem lokalen Ausfall, sobald der Quellserver ein Failover zu Azure durchgeführt hat, müssen Kunden einen Prozessserver in Azure und einen Masterzielserver lokal einrichten, um den Quellserver wieder zu schützen, um lokal wiederherzustellen.

![Bereitstellung des VMM-/physichen Standorts für die Replikation zwischen lokalen Standorten und Azure.](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### Bereitstellung des VMM-Standorts für die Replikation zwischen lokalen Standorten.

Als Bestandteil der Einrichtung der Notfallwiederherstellung zwischen zwei lokalen Standorten muss Azure Site Recovery Provider heruntergeladen und auf dem VMM-Server installiert werden. Für Azure Site Recovery Provider muss eine Internetverbindung bestehen, um sicherzustellen, dass alle über das Azure-Portal ausgelösten Vorgänge in lokale Vorgänge umgewandelt werden, z. B. Aktivieren des Schutzes, Herunterfahren primärer virtueller Computer bei Failovers usw.

![Bereitstellung des VMM-Standorts für die Replikation zwischen lokalen Standorten.](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### Bereitstellung des VMM-Standorts für die Replikation zwischen lokalen Standorten und Azure.

Als Bestandteil der Einrichtung der Notfallwiederherstellung zwischen lokalen Standorten und Azure muss Azure Site Recovery Provider heruntergeladen und auf dem VMM-Server installiert werden. Zudem muss der Azure Recovery Services-Agent auf jedem Hyper-V-Host installiert werden. Weitere Informationen finden Sie unter [Grundlegendes zum Schutz von Standorten in Azure](./site-recovery-understanding-site-to-azure-protection.md).

![Bereitstellung des VMM-Standorts für die Replikation zwischen lokalen Standorten und Azure.](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### Bereitstellung des Hyper-V-Standorts für die Replikation zwischen lokalen Standorten und Azure.

Dies entspricht der Bereitstellung des VMM-Standorts mit der einzigen Ausnahme, dass der Anbieter und die Agents auf dem Hyper-V-Host selbst installiert werden. Weitere Informationen finden Sie unter [Grundlegendes zum Schutz von Standorten in Azure](./site-recovery-understanding-site-to-azure-protection.md).

## Überwachen von Konfigurations-, Schutz- und Wiederherstellungsvorgängen

Jeder Vorgang in ASR wird überwacht und auf der Registerkarte "Aufträge" nachverfolgt. Navigieren Sie bei Fehlern bei Konfigurations-, Schutz- oder Wiederherstellungsvorgängen zur Registerkarte "Aufträge", um zu prüfen, ob dort Fehler angezeigt werden.

![Überwachen von Konfigurations-, Schutz- und Wiederherstellungsvorgängen](media/site-recovery-monitoring-and-troubleshooting/image3.png)

Wenn in der Ansicht "Aufträge" Fehler angezeigt werden, wählen Sie den entsprechenden Auftrag aus, und klicken Sie auf "Fehlerdetails" für diesen Auftrag.

![Überwachen von Konfigurations-, Schutz- und Wiederherstellungsvorgängen](media/site-recovery-monitoring-and-troubleshooting/image4.png)

Anhand der Fehlerdetails können Sie die mögliche Ursache ermitteln und erhalten Empfehlungen zum Beheben des Problems.

![Überwachen von Konfigurations-, Schutz- und Wiederherstellungsvorgängen](media/site-recovery-monitoring-and-troubleshooting/image5.png)

Im Fall oben scheint ein anderer aktiver Vorgang die Ursache dafür zu sein, dass bei der Konfiguration des Schutzes Fehler auftreten. Beheben Sie das Problem entsprechend der Empfehlung, und klicken Sie anschließend auf "Neu starten", um den Vorgang erneut zu starten.

![Überwachen von Konfigurations-, Schutz- und Wiederherstellungsvorgängen](media/site-recovery-monitoring-and-troubleshooting/image6.png)

Die Option "Neu starten" ist nicht für alle Vorgänge verfügbar. Bei den Vorgängen, für die diese Option nicht verfügbar ist, müssen Sie zurück zum Objekt navigieren und den Vorgang wiederholen. Jeder Auftrag kann während der Ausführung jederzeit über die Schaltfläche "Abbrechen" abgebrochen werden.

![Überwachen von Konfigurations-, Schutz- und Wiederherstellungsvorgängen](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## Überwachen der Replikationsintegrität für virtuelle Computer

ASR stellt für alle geschützten Entitäten eine zentrale und Remoteüberwachung über das Azure-Portal bereit. Navigieren Sie zu den geschützten Elementen, und wählen Sie dann VMM-Clouds oder Schutzgruppen aus. Die Registerkarte "VMM-Clouds" steht nur für VMM-basierte Bereitstellungen zur Verfügung. Bei allen anderen Szenarios sind die geschützten Entitäten auf der Registerkarte "Schutzgruppen" aufgeführt.

![Überwachen der Replikationsintegrität für virtuelle Computer](media/site-recovery-monitoring-and-troubleshooting/image8.png)

Wählen Sie dann die geschützte Entität in der entsprechenden Cloud oder Schutzgruppe aus. Nach Auswahl der geschützten Einheit werden alle zulässigen Vorgänge im unteren Bereich angezeigt.

![Überwachen der Replikationsintegrität für virtuelle Computer](media/site-recovery-monitoring-and-troubleshooting/image9.png)

Im Beispiel oben ist die Integrität des virtuellen Computers gefährdet. Sie können auf die Schaltfläche "Fehlerdetails" im unteren Bereich klicken, um den Fehler anzuzeigen. Beheben Sie das Problem basierend auf den erwähnten möglichen Ursachen und Empfehlungen.

![Überwachen der Replikationsintegrität für virtuelle Computer](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![Überwachen der Replikationsintegrität für virtuelle Computer](media/site-recovery-monitoring-and-troubleshooting/image11.png)

Hinweis: Wenn aktive Vorgänge ausgeführt werden oder fehlgeschlagen sind, navigieren Sie entsprechend der Beschreibung weiter oben zur Ansicht "Aufträge", um den Fehler für den spezifischen Auftrag anzuzeigen.

## Behandlung von lokalen Hyper-V-Problemen

Stellen Sie eine Verbindung mit der lokalen Hyper-V-Manager-Konsole her, wählen Sie den virtuellen Computer aus, und prüfen Sie die Replikationsintegrität.

![Behandlung von lokalen Hyper-V-Problemen](media/site-recovery-monitoring-and-troubleshooting/image12.png)

In diesem Fall wird die *Replikationsintegrität* als "Kritisch" angegeben. Klicken Sie auf *Replikationsstatus anzeigen*, um Details anzuzeigen.

![Behandlung von lokalen Hyper-V-Problemen](media/site-recovery-monitoring-and-troubleshooting/image13.png)

Falls die Replikation für den virtuellen Computer angehalten wird, klicken Sie mit der rechten Maustaste auf *Replikation*->*Replikation fortsetzen*. ![Behandlung von lokalen Hyper-V-Problemen](media/site-recovery-monitoring-and-troubleshooting/image19.png)

Falls der virtuelle Computer einen neuen Hyper-V-Host (im Cluster oder auf einem eigenständigen Computer) migriert, der über ASR konfiguriert wurde, ist die Replikation des virtuellen Computers nicht betroffen. Stellen Sie sicher, dass der neue Hyper-V-Host alle Voraussetzungen erfüllt und mit ASR konfiguriert wird.

### Ereignisprotokoll

| Ereignisquellen | Details |
|-------------------------	|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| **Anwendungs- und Dienstprotokolle/Microsoft/VirtualMachineManager/Server/Admin** (VMM-Server) | Dies bietet nützliche Protokollierung für die Problembehandlung vieler unterschiedlicher VMM-Probleme. |
| **Anwendungs- und Dienstprotokolle/MicrosoftAzureRecoveryServices/Replikation** (Hyper-V-Host) | Dies bietet nützliche Protokollierung für die Problembehandlung vieler Probleme, die es mit dem Microsoft Azure Recovery Services Agent geben kann. <br/> ![Ereignisquelle für Hyper-V-Host](media/site-recovery-monitoring-and-troubleshooting/eventviewer03.png) |
| **Anwendungs- und Dienstprotokolle/Microsoft/Azure Site Recovery/Anbieter/Betriebsbereit** (Hyper-V-Host) | Dies bietet nützliche Protokollierung für die Problembehandlung vieler Probleme, die es mit dem Microsoft Azure Site Recovery-Dienst geben kann. <br/> ![Ereignisquelle für Hyper-V-Host](media/site-recovery-monitoring-and-troubleshooting/eventviewer02.png) |
| **Anwendungs- und Dienstprotokolle/Microsoft/Windows/Hyper-V-VMMS/Admin** (Hyper-V-Host) | Dies bietet nützliche Protokollierung für die Problembehandlung vieler Probleme, die bei der Verwaltung eines virtuellen Hyper-V-Computers auftreten können. <br/> ![Ereignisquelle für Hyper-V-Host](media/site-recovery-monitoring-and-troubleshooting/eventviewer01.png) |


### Protokollierungsoptionen für die Hyper-V-Replikation

Alle Ereignisse für Hyper-V Replica werden im Protokoll "Hyper-V-VMMS\\Admin" unter **Applications and Services Logs\\Microsoft\\Windows** protokolliert. Darüber hinaus kann ein analytisches Protokoll für Hyper-V-VMMS aktiviert werden. Um dieses Protokoll zu aktivieren, müssen Sie zunächst die analytischen und Debugprotokolle in der Ereignisanzeige einblenden. Öffnen Sie die Ereignisanzeige, und klicken Sie im Menü **Ansicht** auf **Analytische und Debugprotokolle einblenden**.

![Behandlung von lokalen Hyper-V-Problemen](media/site-recovery-monitoring-and-troubleshooting/image14.png)

Unter "Hyper-V-VMMS" wird ein analytisches Protokoll angezeigt.

![Behandlung von lokalen Hyper-V-Problemen](media/site-recovery-monitoring-and-troubleshooting/image15.png)

Klicken Sie im Bereich **Aktionen** auf **Protokoll aktivieren**. Nach der Aktivierung wird das Protokoll in **Systemmonitor** als Ereignisablaufverfolgungssitzung unter **Datensammlersätze** angezeigt.

![Behandlung von lokalen Hyper-V-Problemen](media/site-recovery-monitoring-and-troubleshooting/image16.png)

Beenden Sie zum Anzeigen der gesammelten Informationen zunächst die Ablaufverfolgungssitzung, indem Sie das Protokoll deaktivieren. Speichern Sie dann das Protokoll, und öffnen Sie es in der Ereignisanzeige, oder konvertieren Sie es mithilfe anderer Tools in das gewünschte Format.



## Microsoft-Support

### Protokollsammlung

Im Hinblick auf den Schutz von VMM-Standorten finden Sie Informationen zum Sammeln der erforderlichen Protokolle unter [ASR Log Collection using Support Diagnostics Platform (SDP) Tool](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx) (in englischer Sprache).

Laden Sie für den Schutz von Hyper-V-Standorten dieses [Tool](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) herunter, und führen Sie es auf dem Hyper-V-Host aus, um die Protokolle zu sammeln.

Informationen zum Sammeln der erforderlichen Protokolle für physische oder VMware-Szenarios finden Sie unter [Azure Site Recovery Log Collection for VMware and Physical site protection](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) (in englischer Sprache).

Das Tool sammelt die Protokolle lokal in einem zufällig benannten Unterordner unter **%LocalAppData%\\ElevatedDiagnostics**.

![Beispielschritte aus dem Hyper-V-Standortschutz](media/site-recovery-monitoring-and-troubleshooting/animate01.gif)

### Öffnen eines Supporttickets

Um ein Supportticket für ASR zu öffnen, wenden Sie sich an den Azure-Support unter der URL <http://aka.ms/getazuresupport>.

## KB-Artikel

-   [Beibehalten des Laufwerkbuchstabens für geschützte virtuelle Computer, für die ein Failover ausgeführt oder die zu Azure migriert werden](http://support.microsoft.com/kb/3031135)
-   [Verwalten der Netzwerkbandbreitennutzung für den Schutz lokaler Elemente in Azure](https://support.microsoft.com/kb/3056159)
-   [ASR: Der Fehler „Die Clusterressource wurde nicht gefunden.“ wird angezeigt, wenn Sie den Schutz für einen virtuellen Computer aktivieren möchten.](http://support.microsoft.com/kb/3010979)
-   [Handbuch mit Grundlagen und Informationen zur Problembehandlung für Hyper-V-Replikate](http://www.microsoft.com/en-in/download/details.aspx?id=29016)

## Häufige Fehler bei der automatischen Systemwiederherstellung und deren Lösungen

Im Folgenden werden häufige Fehler aufgeführt, die passieren können, sowie deren Lösungen. Jeder Fehler wird auf einer separaten Wikiseite dokumentiert.

### Allgemein
-   <span style="color:green;">NEU</span> [Aufträge mit dem Fehler „Ein Vorgang wird ausgeführt.“ Fehler 505, 514, 532](http://social.technet.microsoft.com/wiki/contents/articles/32190.azure-site-recovery-jobs-failing-with-error-an-operation-is-in-progress-error-505-514-532.aspx)
-   <span style="color:green;">NEU</span> [Aufträge mit dem Fehler „Server ist nicht mit dem Internet verbunden“. Fehler 25018](http://social.technet.microsoft.com/wiki/contents/articles/32192.azure-site-recovery-jobs-failing-with-error-server-isn-t-connected-to-the-internet-error-25018.aspx)

### Einrichtung
-   [Der VMM-Server kann aufgrund eines internen Fehlers nicht registriert werden. In der Auftragsansicht im Site Recovery-Portal finden Sie weitere Details zu diesem Fehler. Führen Sie den Setup erneut aus, um den Server zu registrieren.](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)
-   [Mit dem Hyper-V-Wiederherstellungs-Manager-Tresor kann keine Verbindung hergestellt werden. Überprüfen Sie die Proxyeinstellungen oder versuchen Sie es später erneut.](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### Konfiguration
-   [Die Schutzgruppe konnte nicht erstellt werden: Fehler beim Abrufen der Serverliste.](http://blogs.technet.com/b/somaning/archive/2015/08/12/unable-to-create-the-protection-group-in-azure-site-recovery-portal.aspx)
-   [Der Hyper-V-Hostcluster enthält mindestens einen statischen Netzwerkadapter, oder keine verbundenen Adapter sind für die Verwendung von DHCP konfiguriert.](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)
-   [VMM besitzt keine Berechtigungen zum Ausführen einer Aktion.](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)
-   [Das Speicherkonto im Abonnement kann beim Konfigurieren des Schutzes nicht ausgewählt werden.](http://social.technet.microsoft.com/wiki/contents/articles/32027.can-t-select-the-storage-account-within-the-subscription-while-configuring-protection.aspx)

### Schutz
- <span style="color:green;">NEU</span> [„Schutz aktivieren“ führt zu dem Fehler „Der Schutz für den virtuellen Computer konnte nicht konfiguriert werden“. Fehler 60007, 40003](http://social.technet.microsoft.com/wiki/contents/articles/32194.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-configured-for-the-virtual-machine-error-60007-40003.aspx)
- <span style="color:green;">NEU</span> [„Schutz aktivieren“ führt zu dem Fehler „Der Schutz für den virtuellen Computer konnte nicht aktiviert werden“. Fehler 70094](http://social.technet.microsoft.com/wiki/contents/articles/32195.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-enabled-for-the-virtual-machine-error-70094.aspx)
- <span style="color:green;">NEU</span> [Livemigrationsfehler 23848: Der virtuelle Computer wird mithilfe von Typ „Live“ verschoben. Dadurch kann der Wiederherstellungsschutzstatus des virtuellen Computers unterbrochen werden.](http://social.technet.microsoft.com/wiki/contents/articles/32021.live-migration-error-23848-the-virtual-machine-is-going-to-be-moved-using-type-live-this-could-break-the-recovery-protection-status-of-the-virtual-machine.aspx)
- [Aktivieren des Schutzes fehlgeschlagen, da der Agent nicht auf dem Hostcomputer installiert ist.](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)
- [Ein geeigneter Host für den replizierten virtuellen Computer wurde nicht gefunden. Grund: geringe Compute-Ressourcen.](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)
- [Ein geeigneter Host für den replizierten virtuellen Computer wurde nicht gefunden. Grund: Es ist kein logisches Netzwerk angeschlossen.](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)
- [Keine Verbindung mit dem replizierten Host-Computer – Verbindung konnte nicht hergestellt werden.](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)


### Wiederherstellen
- VMM kann den Host-Vorgang nicht abschließen –
    -   [Ein Failover auf den ausgewählten Wiederherstellungspunkt für den virtuellen Computer: Allgemeiner Zugriffsfehler.](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)
    -   [Hyper-V konnte kein Failover zum ausgewählten Wiederherstellungspunkt für virtuelle Computer ausführen: Der Vorgang wurde abgebrochen. Versuchen Sie es mit einem neueren Wiederherstellungspunkt. (0x80004004)](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)
    -   Eine Verbindung mit dem Server konnte nicht hergestellt werden (0x00002EFD).
        -   [Fehler von Hyper-V beim Aktivieren der umgekehrten Replikation für den virtuellen Computer](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)
        -   [Fehler von Hyper-V beim Aktivieren der Replikation für den virtuellen Computer](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)
    -   [Failover für virtuellen Computer konnte nicht übergeben werden.](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)
-   [Der Wiederherstellungsplan enthält virtuelle Computer, die für ein geplantes Failover nicht bereit sind.](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)
-   [Der virtuelle Computer ist nicht bereit für ein geplantes Failover.](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   [Der virtuelle Computer wird nicht ausgeführt und ist nicht ausgeschaltet.](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)
-   [Out-of-Band-Vorgang auf einem virtuellen Computer und Fehler bei Übergabe des Failovers](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   Test-Failover
    -   [Failover konnte nicht initiiert werden, da das Testfailover ausgeführt wird.](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)
-   <span style="color:green;">NEU</span> Beim Failover tritt aufgrund der Konfigurationseigenschaften der Netzwerksicherheitsgruppe, die dem virtuellen Computer oder dem Subnetz für den virtuellen Computer zugeordnet ist, ein Timeout mit der Meldung „PreFailoverWorkflow task WaitForScriptExecutionTask timed out“ (Timeout bei der PreFailoverWorkflow-Aufgabe „WaitForScriptExecutionTask“) auf. Ausführlichere Informationen finden Sie unter [PreFailoverWorkflow task WaitForScriptExecutionTask timed out](https://aka.ms/troubleshoot-nsg-issue-azure-site-recovery) (Timeout bei der PreFailoverWorkflow-Aufgabe „WaitForScriptExecutionTask“).


### Konfigurationsserver, Prozessserver, Masterziel
Konfigurationsserver (Configuration Server, CS), Prozessserver (Process Server, PS), Masterziel (Master Target, MT)
-   [Der ESXi-Host, auf dem der PS/CS als virtueller Computer gehostet wird, schlägt mit einem violetten Bildschirm fehl.](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

### Remotedesktop-Problembehandlung nach einem Failover
-   Viele Kunden haben Probleme beim Herstellen einer Verbindung mit dem virtuellen Computer in Azure festgestellt, für den ein Failover ausgeführt wurde. [Stellen Sie anhand des Dokuments für die Problembehandlung eine RDP-Verbindung mit dem virtuellen Computer her.](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)

<!---HONumber=AcomDC_0706_2016-->