<properties
	pageTitle="Failover in Site Recovery | Microsoft Azure" 
	description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung virtueller Computer und physischer Server. Informieren Sie sich über das Failover zu Azure oder zu einem sekundären Datencenter." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="07/12/2016" 
	ms.author="raynew"/>

# Failover in Site Recovery

Der Dienst Azure Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem Replikation, Failover und Wiederherstellung virtueller Computer und physischer Server aufeinander abgestimmt werden. Computer können in Azure oder in einem sekundären lokalen Datencenter repliziert werden. Eine kurze Übersicht über das Gesamtthema finden Sie unter [Was ist Azure Site Recovery?](site-recovery-overview.md)

## Übersicht

Dieser Artikel enthält Informationen und Anweisungen zur Wiederherstellung (Failover und Failback) virtueller Computer und physischer Server, die mit Site Recovery geschützt sind.

Kommentare oder Fragen können Sie am Ende dieses Artikels oder im [Forum zu Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) veröffentlichen.


## Failover-Arten

Nachdem der Schutz für virtuelle Computer und physische Server eingerichtet wurde und diese repliziert werden, können Sie bei Bedarf Failover durchführen. Site Recovery unterstützt eine Reihe von Failover-Arten:

**Failover** | **Zweck** | **Details** | **Prozess**
---|---|---|---
**Test-Failover** | Überprüfung der Replikationsstrategie oder Durchführung einer Notfallwiederherstellungsübung | Kein Datenverlust und keine Ausfallzeiten<br/><br/>Keine Auswirkungen auf die Replikation<br/><br/>Keine Auswirkungen auf die Produktionsumgebung | Starten Sie das Failover.<br/><br/>Geben Sie an, wie Testcomputer nach dem Failover mit Netzwerken verbunden werden sollen.<br/><br/>Überwachen Sie den Fortschritt auf der Registerkarte **Aufträge**. Testcomputer werden erstellt und am sekundären Standort gestartet.<br/><br/>Azure: Stellen Sie im Azure-Portal eine Verbindung mit dem Computer her.<br/><br/>Sekundärer Standort: Greifen Sie auf den Computer auf dem gleichen Host und in der gleichen Cloud zu.<br/><br/>Schließen Sie den Test ab, und führen Sie eine automatische Bereinigung der Einstellungen für das Test-Failover durch.
**Geplantes Failover** | Erfüllung von Compliance-Anforderungen<br/><br/>Geplante Wartung<br/><br/>Failover von Daten, um Workloads vor bekannten Ausfällen (etwa im Falle eines zu erwartenden Stromausfalls oder Unwetters) zu schützen<br/><br/>Failback nach einem Failover vom primären zum sekundären Standort | Kein Datenverlust<br/><br/>Ausfallzeit entspricht der Zeit, die es dauert, um den virtuellen Computer am primären Standort herunterzufahren und am sekundären Standort verfügbar zu machen.<br/><br/>Auswirkungen auf virtuelle Computer: Zielcomputer werden nach dem Failover zu Quellcomputern. | Starten Sie das Failover.<br/><br/>Überwachen Sie den Fortschritt auf der Registerkarte **Aufträge**. Die Quellcomputer werden heruntergefahren.<br/><br/>Die Replikatcomputer werden am sekundären Standort gestartet.<br/><br/>Azure: Stellen Sie im Azure-Portal eine Verbindung mit dem Replikatcomputer her.<br/><br/>Sekundärer Standort: Greifen Sie auf den Computer auf dem gleichen Host und in der gleichen Cloud zu.<br/><br/>Führen Sie ein Commit für das Failover aus.
**Nicht geplantes Failover** | Reaktives Failover, falls aufgrund eines unerwarteten Vorfalls (beispielsweise ein Stromausfall oder ein Virusangriff) nicht mehr auf einen primären Standort zugegriffen werden kann<br/><br/> Ein nicht geplantes Failover kann auch durchgeführt werden, wenn der primäre Standort nicht verfügbar ist. | Datenverluste abhängig von der konfigurierten Replikationshäufigkeit<br/><br/>Daten sind jeweils auf dem Stand der letzten Synchronisierung | Starten Sie das Failover.<br/><br/>Überwachen Sie den Fortschritt auf der Registerkarte **Aufträge**. Optional: Fahren Sie die virtuellen Computer herunter, und synchronisieren Sie die aktuellen Daten.<br/><br/>Die Replikatcomputer werden am sekundären Standort gestartet.<br/><br/>Azure: Stellen Sie im Azure-Portal eine Verbindung mit dem Replikatcomputer her.<br/><br/>Sekundärer Standort: Greifen Sie auf den Computer auf dem gleichen Host und in der gleichen Cloud zu.<br/><br/>Führen Sie ein Commit für das Failover aus.


Welche Failover konkret unterstützt werden, hängt von Ihrem Bereitstellungsszenario ab.

**Failover-Richtung** | **Test-Failover** | **Geplantes Failover** | **Nicht geplantes Failover**
---|---|---|---
Primärer VMM-Standort zu sekundärem VMM-Standort | Unterstützt | Unterstützt | Unterstützt 
Sekundärer VMM-Standort zu primärem VMM-Standort | Unterstützt | Unterstützt | Unterstützt
Cloudintern (einzelner VMM-Server) | Unterstützt | Unterstützt | Unterstützt
VMM-Standort zu Azure | Unterstützt | Unterstützt | Unterstützt 
Azure zu VMM-Standort | Nicht unterstützt | Unterstützt | Nicht unterstützt 
Hyper-V-Standort zu Azure | Unterstützt | Unterstützt | Unterstützt
Azure zu Hyper-V-Standort | Nicht unterstützt | Unterstützt | Nicht unterstützt
VMware-Standort zu Azure | Unterstützt (erweitertes Szenario)<br/><br/> Nicht unterstützt (älteres Szenario) |Nicht unterstützt | Unterstützt
Physischer Server zu Azure | Unterstützt (erweitertes Szenario)<br/><br/> Nicht unterstützt (älteres Szenario) | Nicht unterstützt | Unterstützt

## Failover und Failback

Als Failoverziel für virtuelle Computer wird abhängig von der verwendeten Bereitstellung entweder ein lokaler sekundärer Standort oder Azure verwendet. Ein Computer, für den ein Failover zu Azure durchgeführt wird, wird als virtueller Azure-Computer erstellt. Failover können für einen einzelnen virtuellen Computer oder physischen Server oder für einen Wiederherstellungsplan durchgeführt werden. Wiederherstellungspläne enthalten mindestens eine sortierte Gruppe mit geschützten virtuellen Computern oder physischen Servern. Sie dienen zur Orchestrierung des Failovers mehrerer Computer, deren Failover dann gemäß der jeweiligen Gruppe durchgeführt wird. Weitere Informationen zu Wiederherstellungsplänen finden Sie [hier](site-recovery-create-recovery-plans.md).

Wenn das Failover abgeschlossen ist und die Computer an einem sekundären Standort in Betrieb sind, gilt Folgendes:

- Nach einem Failover zu Azure sind die Computer am primären oder sekundären Standort nicht geschützt und werden auch nicht repliziert. In Azure werden die virtuellen Computer in geografisch repliziertem Speicher gespeichert. Dies sorgt zwar für Resilienz, nicht aber für Replikation.
- Nach einem nicht geplanten Failover zu einem sekundären Standort sind die Computer am sekundären Standort nicht geschützt und werden nicht repliziert.
- Nach einem geplanten Failover zu einem sekundären Standort sind die Computer am sekundären Standort geschützt.
 

### Failback vom sekundären Standort

Beim Failback von einem sekundären zu einem primären Standort wird der gleiche Prozess verwendet wie beim umgekehrten Failover. Wenn für das Failover vom primären zum sekundären Datencenter ein Commit ausgeführt wurde und das Failover abgeschlossen ist, können Sie die umgekehrte Replikation initiieren, sobald Ihr primärer Standort wieder verfügbar ist. Bei der umgekehrten Replikation wird die Replikation zwischen dem sekundären und dem primären Standort initiiert, indem die geänderten Daten synchronisiert werden. Die umgekehrte Replikation versetzt die virtuellen Computer in einen geschützten Zustand, das sekundäre Datencenter bleibt aber weiterhin aktiv. Initiieren Sie ein geplantes Failover vom sekundären zum primären Standort, und führen Sie anschließend eine erneute umgekehrte Replikation durch, um den primären Standort zum aktiven Standort zu machen.

### Failback von Azure

Wenn Sie ein Failover zu Azure durchgeführt haben, werden Ihre virtuellen Computer durch die Azure-Resilienzfeatures für virtuelle Computer geschützt. Führen Sie ein geplantes Failover durch, um den ursprünglichen primären Standort zum aktiven Standort zu machen. Als Ziel für das Failback können Sie den ursprünglichen Standort oder auch einen anderen Standort verwenden, falls der ursprüngliche Standort nicht verfügbar ist. Initiieren Sie eine umgekehrte Replikation, um nach dem Failback die Replikation zum primären Standort zu reaktivieren.

### Überlegungen zum Failover

- **IP-Adresse nach dem Failover**: Standardmäßig besitzt ein Computer nach einem Failover eine andere IP-Adresse als der Quellcomputer. So können Sie die IP-Adresse beibehalten:
	- **Sekundärer Standort**: Wenn Sie ein Failover zu einem sekundären Standort durchführen und eine IP-Adresse beibehalten möchten, lesen Sie [diesen Artikel](http://blogs.technet.com/b/scvmm/archive/2014/04/04/retaining-ip-address-after-failover-using-hyper-v-recovery-manager.aspx). Eine öffentliche IP-Adresse können Sie beibehalten, wenn Ihr Internet-Service Provider dies unterstützt.
	- **Azure**: Im Falle eines Failovers zu Azure können Sie in den Eigenschaften des virtuellen Computers auf der Registerkarte **Konfigurieren** die IP-Adresse angeben, die Sie zuweisen möchten. Öffentliche IP-Adressen können nach einem Failover zu Azure nicht beibehalten werden. Als interne Adressen verwendete RFC 1918-fremde Adressräume können beibehalten werden.
- **Partielles Failover**: Wenn ein Failover nur für einen Teil des Standorts durchgeführt werden soll, gilt Folgendes:
	- **Sekundärer Standort**: Wenn Sie für einen Teil eines primären Standorts ein Failover zu einem sekundären Standort durchführen und wieder eine Verbindung mit dem primären Standort herstellen möchten, verwenden Sie eine VPN-Verbindung zwischen den beiden Standorten, um eine Verbindung zwischen Failover-Anwendungen am sekundären Standort und Infrastrukturkomponenten am primären Standort herzustellen. Beim Failover eines gesamten Subnetzes können Sie die IP-Adressen der virtuellen Computer beibehalten. Beim partiellen Failover eines Subnetzes kann die IP-Adresse des virtuellen Computers nicht beibehalten werden, da Subnetze nicht aufgeteilt werden können.
	- **Azure**: Wenn Sie für einen Teil eines Standorts ein Failover zu Azure durchführen und wieder eine Verbindung mit dem primären Standort herstellen möchten, können Sie eine VPN-Verbindung zwischen den Standorten verwenden, um eine Verbindung zwischen einer Failover-Anwendung in Azure und Infrastrukturkomponenten am primären Standort herzustellen. Bei einem Failover des gesamten Subnetzes können Sie die IP-Adresse des virtuellen Computers beibehalten. Beim partiellen Failover eines Subnetzes kann die IP-Adresse des virtuellen Computers nicht beibehalten werden, da Subnetze nicht aufgeteilt werden können.
 
- **Laufwerkbuchstabe**: Wenn Sie nach dem Failover den Laufwerkbuchstaben des virtuellen Computers beibehalten möchten, können Sie die SAN-Richtlinie für den virtuellen Computer aktivieren. Die Datenträger von virtuellen Computern werden automatisch online geschaltet. [Weitere Informationen](https://technet.microsoft.com/library/gg252636.aspx)
- **Weiterleiten von Clientanforderungen**: Site Recovery verwendet Azure Traffic Manager, um Clientanforderungen nach dem Failover an Ihre Anwendung weiterzuleiten.




## Durchführen eines Test-Failovers

Beim einem Test-Failover werden Sie zum Auswählen von Netzwerkeinstellungen für die Testreplikatcomputer aufgefordert. Hierbei stehen mehrere Optionen zur Verfügung:

**Option für das Test-Failover** | **Beschreibung** | **Failoverprüfung** | **Details**
---|---|---|---
**Failover zu Azure (ohne Netzwerk)** | Wählen Sie kein Azure-Zielnetzwerk aus. | Beim Failover wird geprüft, ob der virtuelle Testcomputer erwartungsgemäß in Azure startet. | Alle virtuellen Testcomputer in einem Wiederherstellungsplan werden in einem einzelnen Clouddienst hinzugefügt und können untereinander Verbindungen herstellen.<br/><br/>Die Computer sind nach dem Failover nicht mit einem Azure-Netzwerk verbunden.<br/><br/>Benutzer können über eine öffentliche IP-Adresse eine Verbindung mit den Testcomputern herstellen.
**Failover zu Azure (mit Netzwerk)** | Wählen Sie ein Azure-Zielnetzwerk aus. | Beim Failover wird geprüft, ob die Testcomputer mit dem Netzwerk verbunden sind. | Erstellen Sie ein Azure-Netzwerk, das von Ihrem Azure-Produktionsnetzwerk isoliert ist, und richten Sie die Infrastruktur ein, damit der virtuelle Replikatcomputer erwartungsgemäß funktioniert.<br/><br/>Das Subnetz des virtuellen Testcomputers basiert auf dem Subnetz, in dem der virtuelle Computer nach einem geplanten oder nicht geplanten Failover eine Verbindung herstellt.
**Failover zu einem sekundären VMM-Standort (ohne Netzwerk)** | Wählen Sie kein VM-Netzwerk aus. | Beim Failover wird überprüft, ob die Testcomputer erstellt werden.<br/><br/>Der virtuelle Testcomputer wird auf dem Host erstellt, auf dem sich auch der virtuelle Replikatcomputer befindet. Er wird nicht der Cloud hinzugefügt, in der sich der replizierte virtuelle Computer befindet. | <p>Der Computer ist nach dem Failover mit keinem Netzwerk verbunden.<br/><br/>Der virtuelle Computer kann nach der Erstellung mit einem VM-Netzwerk verbunden werden.
**Failover zu einem sekundären VMM-Standort (mit Netzwerk)** | Wählen Sie ein vorhandenes VM-Netzwerk aus. | Beim Failover wird geprüft, ob virtuelle Computer erstellt werden. | Der virtuelle Computer für den Test wird auf dem Host erstellt, auf dem auch der replizierte virtuelle Computer vorhanden ist. Er wird nicht der Cloud hinzugefügt, in der sich der virtuelle Replikatcomputer befindet.<br/><br/>Erstellen Sie ein vom Produktionsnetzwerk isoliertes VM-Netzwerk.<br/><br/>Bei Verwendung eines VLAN-basierten Netzwerks empfiehlt es sich, hierzu in VMM ein separates logisches Netzwerk zu erstellen, das nicht in der Produktionsumgebung verwendet wird. Dieses logische Netzwerk dient zum Erstellen von VM-Netzwerken für das Testfailover.<br/><br/>Das logische Netzwerk muss mindestens einem der Netzwerkadapter aller Hyper-V-Server zugeordnet werden, die virtuelle Computer hosten.<br/><br/>Bei logischen VLAN-basierten Netzwerken müssen die hinzugefügten Netzwerkstandorte isoliert sein.<br/><br/>Bei Verwendung eines logischen Netzwerks, das auf der Windows-Netzwerkvirtualisierung basiert, erstellt Azure Site Recovery automatisch isolierte VM-Netzwerke.
**Failover zu einem sekundären VMM-Standort (mit Netzwerkerstellung)** | Auf der Grundlage der Einstellung, die Sie unter **Logisches Netzwerk** angeben, und den zugehörigen Netzwerkstandorten wird automatisch ein temporäres Testnetzwerk erstellt. | Beim Failover wird geprüft, ob virtuelle Computer erstellt werden. | Verwenden Sie diese Option, wenn für den Wiederherstellungsplan mehr als ein VM-Netzwerk verwendet wird. Im Falle von Netzwerken, die auf der Windows-Netzwerkvirtualisierung basieren, kann diese Option im Netzwerk des virtuellen Replikatcomputers automatisch VM-Netzwerke mit den gleichen Einstellungen (Subnetze und IP-Adresspools) erstellen. Diese VM-Netzwerke werden nach dem Test-Failover automatisch bereinigt.</p><p>Der virtuelle Testcomputer wird auf dem gleichen Host erstellt wie der Host, auf dem sich der virtuelle Replikatcomputer befindet. Er wird nicht der Cloud hinzugefügt, in der sich der replizierte virtuelle Computer befindet.

>[AZURE.NOTE] Die einem virtuellen Computer bei einem Testfailover zugewiesene IP-Adresse ist mit der IP-Adresse identisch, die ihm bei einem geplanten oder nicht geplanten Failover zugewiesen wird (sofern die IP-Adresse im Testfailovernetzwerk verfügbar ist). Wenn im Testfailovernetzwerk nicht dieselbe IP-Adresse verfügbar ist, wird dem virtuellen Computer eine andere IP-Adresse zugewiesen, die im Testfailovernetzwerk verfügbar ist.



### Durchführen eines Test-Failovers (lokal zu Azure)

Hier erfahren Sie, wie Sie ein Test-Failover für einen Wiederherstellungsplan durchführen. Alternativ können Sie das Failover über die Registerkarte **Virtuelle Computer** auch für einen einzelnen Computer durchführen.

1. Wählen Sie **Wiederherstellungspläne** > *<Name des Wiederherstellungsplans>*. Klicken Sie auf **Failover** > **Test-Failover**.
2. Geben Sie auf der Seite **Test-Failover bestätigen** an, wie die Replikatcomputer nach dem Failover mit einem Azure-Netzwerk verbunden werden sollen.
3. Wenn Sie ein Failover zu Azure durchführen und die Datenverschlüsselung für die Cloud aktiviert ist, wählen Sie unter **Verschlüsselungsschlüssel** das Zertifikat aus, das beim Aktivieren der Datenverschlüsselung im Rahmen der Anbieterinstallation ausgestellt wurde.
4. Verfolgen Sie den Verlauf des Failovers auf der Registerkarte **Aufträge**. Der Test-Replikatcomputer wird im Azure-Portal angezeigt.
5. Sie können an Ihrem lokalen Standort über eine RDP-Verbindung auf Replikatcomputer in Azure zugreifen. Dabei muss am Endpunkt für den virtuellen Computer der Port 3389 geöffnet sein.
5. Klicken Sie anschließend auf **Test abschließen**, wenn das Failover die Endphase des Tests erreicht.
5. Erfassen und speichern Sie unter **Notizen** alle Beobachtungen im Zusammenhang mit dem Test-Failover.
8. Klicken Sie auf **Das Test-Failover ist abgeschlossen.**, um eine automatische Bereinigung der Testumgebung durchzuführen. Nach Abschluss dieses Vorgangs wird für das Test-Failover der Status **Abgeschlossen** angezeigt.

> [AZURE.NOTE] Sollte das Test-Failover länger als zwei Wochen bestehen bleiben, wird der Abschluss des Vorgangs erzwungen. Alle Elemente und virtuellen Computer, die automatisch im Zuge des Test-Failovers erstellt wurden, werden gelöscht.
  

### Durchführen eines Testfailovers von einem primären lokalen Standort zu einem sekundären lokalen Standort

Für ein Testfailover müssen einige Schritte durchgeführt werden. So müssen Sie unter anderem eine Kopie des Domänencontrollers erstellen und Ihre Testumgebung mit einem DHCP- und DNS-Testserver versehen. Dazu haben Sie mehrere Möglichkeiten:

- Wenn Sie ein Test-Failover mit einem vorhandenen Netzwerk durchführen möchten, bereiten Sie Active Directory, DHCP und DNS in diesem Netzwerk vor.
- Wenn Sie ein Test-Failover mit der Option für die automatische Erstellung von VM-Netzwerken durchführen möchten, fügen Sie vor der ersten Gruppe des Wiederherstellungsplans, den Sie für das Test-Failover verwenden möchten, einen manuellen Schritt ein, und fügen Sie dem automatisch erstellten Netzwerk dann vor der Durchführung des Test-Failovers die Infrastrukturressourcen hinzu.

#### Was Sie beachten sollten

- Bei einer Replikation an einen sekundären Standort muss der Typ des vom Replikatcomputer verwendeten Netzwerks nicht dem Typ des logischen Netzwerks für das Test-Failover entsprechen. Einige Kombinationen funktionieren aber unter Umständen nicht. Wenn das Replikat DHCP und eine VLAN-basierte Isolation verwendet, benötigt das VM-Netzwerk für das Replikat keinen statischen IP-Adresspool. In diesem Fall könnte für das Test-Failover also keine Windows-Netzwerkvirtualisierung verwendet werden, da keine Adresspools verfügbar sind. Das Test-Failover funktioniert auch nicht, wenn das Replikatnetzwerk nicht isoliert ist und im Testnetzwerk die Windows-Netzwerkvirtualisierung verwendet wird. Der Grund: Das nicht isolierte Netzwerk verfügt nicht über die Subnetze, die zum Erstellen eines Netzwerks mit Windows-Netzwerkvirtualisierung erforderlich sind.
- Welche Verbindung nach dem Failover zwischen virtuellen Replikatcomputern und zugeordneten VM-Netzwerken besteht, hängt davon ab, wie das VM-Netzwerk in der VMM-Konsole konfiguriert ist:
	- **VM-Netzwerk ohne Isolation oder VLAN-Isolation**: Bei definiertem DHCP für das VM-Netzwerk wird der virtuelle Replikatcomputer mit der VLAN-ID verbunden. Hierbei kommen die Einstellungen zur Anwendung, die für den Netzwerkstandort im zugeordneten logischen Netzwerk angegeben sind. Der virtuelle Computer erhält seine IP-Adresse vom verfügbaren DHCP-Server. Für das VM-Zielnetzwerk muss kein statischer IP-Adresspool definiert sein. Bei Verwendung eines statischen IP-Adresspools für das VM-Netzwerk wird der virtuelle Replikatcomputer mit der VLAN-ID verbunden. Hierbei kommen die Einstellungen zur Anwendung, die für den Netzwerkstandort im zugeordneten logischen Netzwerk angegeben sind. Der virtuelle Computer erhält seine IP-Adresse aus dem für das VM-Netzwerk definierten Adresspool. Ist für das VM-Zielnetzwerk kein statischer IP-Adresspool definiert, ist die IP-Adresszuweisung nicht erfolgreich. Der IP-Adresspool muss sowohl auf dem Quell- als auch auf dem Ziel-VMM-Server erstellt werden, den Sie für den Schutz und die Wiederherstellung verwenden möchten.
	- **VM-Netzwerk mit Windows-Netzwerkvirtualisierung**: Wenn ein VM-Netzwerk mit dieser Einstellung konfiguriert ist, muss für das VM-Zielnetzwerk ein statischer Adresspool definiert werden, und zwar unabhängig davon, ob für das VM-Quellnetzwerk die Verwendung von DHCP oder die Verwendung eines statischen IP-Adresspools konfiguriert ist. Bei Verwendung von DHCP fungiert der VMM-Zielserver als DHCP-Server und stellt eine IP-Adresse aus dem für das VM-Zielnetzwerk definierten Pool bereit. Ist für den Quellserver die Verwendung eines statischen IP-Adresspools definiert, weist der VMM-Zielserver eine IP-Adresse aus dem Pool zu. In beiden Fällen ist die IP-Adresszuweisung nicht erfolgreich, wenn kein statischer IP-Adresspool definiert ist.

#### Durchführen des Tests

Hier erfahren Sie, wie Sie ein Test-Failover für einen Wiederherstellungsplan durchführen. Alternativ können Sie das Failover über die Registerkarte **Virtuelle Computer** auch für einen einzelnen virtuellen Computer oder physischen Server durchführen.

1. Wählen Sie **Wiederherstellungspläne** > *<Name des Wiederherstellungsplans>*. Klicken Sie auf **Failover** > **Test-Failover**.
2. Geben Sie auf der Seite **Test-Failover bestätigen** an, wie die virtuellen Computer nach dem Test-Failover mit Netzwerken verbunden werden sollen.
3. Verfolgen Sie den Verlauf des Failovers auf der Registerkarte **Aufträge**. Klicken Sie auf **Test abschließen**, wenn das Failover die **Endphase des Tests** erreicht.
4. Klicken Sie auf **Notizen**, um alle Beobachtungen im Zusammenhang mit dem Test-Failover aufzuzeichnen und zu speichern.
4. Vergewissern Sie sich anschließend, dass die virtuellen Computer erfolgreich starten.
5. Schließen Sie nach der Prüfung, ob die virtuellen Computer erfolgreich starten, das Test-Failover ab, um die isolierte Umgebung zu bereinigen. Wenn Sie die automatische Erstellung von VM-Netzwerken ausgewählt haben, werden bei der Bereinigung alle virtuellen Testcomputer und -netzwerke gelöscht.

> [AZURE.NOTE] Sollte das Test-Failover länger als zwei Wochen bestehen bleiben, wird der Abschluss des Vorgangs erzwungen. Alle Elemente und virtuellen Computer, die automatisch im Zuge des Test-Failovers erstellt wurden, werden gelöscht.


#### Vorbereiten von DHCP

Wenn die am Test-Failover beteiligten virtuellen Computer DHCP nutzen, muss innerhalb des für das Test-Failover erstellten isolierten Netzwerks ein DHCP-Testserver erstellt werden.


### Vorbereiten von Active Directory
Wenn Sie ein Test-Failover durchführen möchten, um eine Anwendung zu testen, muss in der Testumgebung eine Kopie der Active Directory-Produktionsumgebung vorhanden sein. Unter [Überlegungen zum Testfailover für Active Directory](site-recovery-active-directory.md#considerations-for-test-failover) finden Sie weitere Details.


### Vorbereiten von DNS

Gehen Sie wie folgt vor, um einen DNS-Server für das Test-Failover vorzubereiten:

- **DHCP**: Wenn virtuelle Computer DHCP nutzen, muss die IP-Adresse des Test-DNS auf dem DHCP-Testserver aktualisiert werden. Bei Verwendung der Windows-Netzwerkvirtualisierung fungiert der VMM-Server als DHCP-Server. Daher sollte die IP-Adresse des DNS im Testfailovernetzwerk aktualisiert werden. In diesem Fall werden die virtuellen Computer automatisch beim relevanten DNS-Server registriert.
- **Statische Adresse**: Wenn virtuelle Computer eine statische IP-Adresse verwenden, muss die IP-Adresse des DNS-Testservers im Testfailovernetzwerk aktualisiert werden. Das DNS muss eventuell mit der IP-Adresse der virtuellen Testcomputer aktualisiert werden. Hierzu können Sie das folgende Beispielskript verwenden:

	    Param(
	    [string]$Zone,
	    [string]$name,
	    [string]$IP
	    )
	    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
	    $newrecord = $record.clone()
	    $newrecord.RecordData[0].IPv4Address  =  $IP
	    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## Durchführen eines geplanten Failovers (primär zu sekundär)

 Hier erfahren Sie, wie Sie ein geplantes Failover für einen Wiederherstellungsplan durchführen. Alternativ können Sie das Failover über die Registerkarte **Virtuelle Computer** auch für einen einzelnen virtuellen Computer durchführen.

1. Vergewissern Sie sich zunächst, dass bei allen virtuellen Computern, für die ein Failover durchgeführt werden soll, die erste Replikation abgeschlossen ist.
2. Wählen Sie **Wiederherstellungspläne** > *<Name des Wiederherstellungsplans>*. Klicken Sie auf **Failover** > **Geplantes Failover**.
3. Wählen Sie auf der Seite **Geplantes Failover bestätigen** den Quell- und Zielort aus. Beachten Sie die Failover-Richtung.

	- Wenn bisherige Failover wie erwartet funktioniert haben und sich alle virtuellen Servercomputer entweder am Quell- oder am Zielort befinden, dienen die Angaben zur Failover-Richtung nur zu Informationszwecken.
	- Wenn virtuelle Computer sowohl am Quell- als auch am Zielort aktiv sind, erscheint die Schaltfläche **Richtung ändern**. Mit dieser Schaltfläche können Sie die Richtung für das Failover ändern.

5. Wenn Sie ein Failover zu Azure durchführen und die Datenverschlüsselung für die Cloud aktiviert ist, wählen Sie unter **Verschlüsselungsschlüssel** das Zertifikat aus, das beim Aktivieren der Datenverschlüsselung im Rahmen der Anbieterinstallation auf dem VMM-Server ausgestellt wurde.
6. Zu Beginn eines geplantes Failovers wird zur Vermeidung von Datenverlusten zunächst der virtuelle Computer heruntergefahren. Der Fortschritt des Failovers wird auf der Registerkarte **Aufträge** angezeigt. Tritt während des Failovers ein Fehler auf (entweder auf einem virtuellen Computer oder in einem Skript aus dem Wiederherstellungsplan), wird das geplante Failover des Wiederherstellungsplans beendet. Sie können das Failover erneut initiieren.
8. Nach der Erstellung der virtuellen Replikatcomputer weist deren Status darauf hin, dass ein Commit aussteht. Klicken Sie auf **Commit**, um ein Commit für das Failover auszuführen.
9. Nach Abschluss der Replikation werden die virtuellen Computer am sekundären Standort gestartet.

## Durchführen eines nicht geplanten Failovers

Hier erfahren Sie, wie Sie ein nicht geplantes Failover für einen Wiederherstellungsplan durchführen. Alternativ können Sie das Failover über die Registerkarte **Virtuelle Computer** auch für einen einzelnen virtuellen Computer oder physischen Server durchführen.

1. Wählen Sie **Wiederherstellungspläne** > *<Name des Wiederherstellungsplans>*. Klicken Sie auf **Failover** > **Nicht geplantes Failover**.
3. Wählen Sie auf der Seite **Nicht geplantes Failover bestätigen** den Quell- und Zielort aus. Beachten Sie die Failover-Richtung.

	- Wenn bisherige Failover wie erwartet funktioniert haben und sich alle virtuellen Servercomputer entweder am Quell- oder am Zielort befinden, dienen die Angaben zur Failover-Richtung nur zu Informationszwecken.
	- Wenn virtuelle Computer sowohl am Quell- als auch am Zielort aktiv sind, erscheint die Schaltfläche **Richtung ändern**. Mit dieser Schaltfläche können Sie die Richtung für das Failover ändern.

4. Wenn Sie ein Failover zu Azure durchführen und die Datenverschlüsselung für die Cloud aktiviert ist, wählen Sie unter **Verschlüsselungsschlüssel** das Zertifikat aus, das beim Aktivieren der Datenverschlüsselung im Rahmen der Anbieterinstallation auf dem VMM-Server ausgestellt wurde.
5. Wählen Sie **Virtuelle Computer herunterfahren und die aktuellen Daten synchronisieren** aus, damit Site Recovery die geschützten virtuellen Computer herunterfährt und die Daten synchronisiert, sodass das Failover mit der neuesten Version der Daten erfolgt. Wenn Sie diese Option nicht auswählen oder der Vorgang nicht erfolgreich ausgeführt werden kann, wird das Failover für den neuesten verfügbaren Wiederherstellungspunkt des virtuellen Computers durchgeführt.
6. Der Fortschritt des Failovers wird auf der Registerkarte **Aufträge** angezeigt. Beachten Sie, dass der Wiederherstellungsplan bei einem nicht geplanten Failover auch dann bis zum Abschluss ausgeführt wird, wenn Fehler auftreten.
7. Nach dem Failover weist der Status der virtuellen Computer darauf hin, dass ein Commit aussteht. Klicken Sie auf **Commit**, um ein Commit für das Failover auszuführen.
8. Wenn Sie für die Replikation die Verwendung mehrerer Wiederherstellungspunkte konfigurieren, können Sie unter „Wiederherstellungspunkt ändern“ einen Wiederherstellungspunkt auswählen, bei dem es sich nicht um den neuesten (standardmäßig verwendeten) Wiederherstellungspunkt handelt. Nach dem Commit werden die zusätzlichen Wiederherstellungspunkte entfernt.
9. Nach Abschluss der Replikation werden die virtuellen Computer am sekundären Standort gestartet und ausgeführt. Sie sind jedoch nicht geschützt und werden auch nicht repliziert. Wenn der primäre Standort mit der gleichen zugrunde liegenden Infrastruktur wieder verfügbar ist, klicken Sie auf **Umgekehrt replizieren**, um die umgekehrte Replikation zu starten. Dadurch wird sichergestellt, dass alle Daten wieder an den primären Standort repliziert werden und der virtuelle Computer für ein erneutes Failover bereit ist. Bei der umgekehrten Replikation nach einem nicht geplanten Failover fällt ein gewisser zusätzlicher Datenübertragungsaufwand an. Bei der Übertragung wird die gleiche Methode verwendet, die auch in den Einstellungen der ersten Replikation für die Cloud konfiguriert ist.

## Failback (sekundär zu primär)

 Nach dem Failover vom primären zum sekundären Standort sind die virtuellen Replikatcomputer nicht durch Site Recovery geschützt, und der sekundäre Standort fungiert nun als primärer Standort. Gehen Sie wie folgt vor, um ein Failback zum ursprünglichen primären Standort durchzuführen. Hier erfahren Sie, wie Sie ein geplantes Failover für einen Wiederherstellungsplan durchführen. Alternativ können Sie das Failover über die Registerkarte **Virtuelle Computer** auch für einen einzelnen virtuellen Computer durchführen.

1. Wählen Sie **Wiederherstellungspläne** > *<Name des Wiederherstellungsplans>*. Klicken Sie auf **Failover** > **Geplantes Failover**.
2. Wählen Sie auf der Seite **Geplantes Failover bestätigen** den Quell- und Zielort aus. Beachten Sie die Failover-Richtung. Wenn das Failover vom primären Standort erwartungsgemäß funktioniert hat und sich alle virtuellen Computer am sekundären Standort befinden, dient diese Angabe nur zu Informationszwecken.
3. Wählen Sie bei einem Failback von Azure Einstellungen unter **Datensynchronisierung** aus:

	- **Daten vor dem Failover synchronisieren (nur Deltaänderungen synchronisieren)**: Diese Option minimiert die Ausfallzeiten der virtuellen Computer, da diese für die Synchronisierung nicht heruntergefahren werden. Die Option bewirkt Folgendes:
		- Phase 1: Sie erstellt eine Momentaufnahme des virtuellen Computers in Azure und kopiert sie auf den lokalen Hyper-V-Host. Der Computer wird weiterhin in Azure ausgeführt.
		- Phase 2: Sie fährt den virtuellen Computer in Azure herunter, damit keine neuen Änderungen vorgenommen werden. Die letzten Änderungen werden an den lokalen Server übertragen, und der lokale virtuelle Computer wird gestartet.
	

	- **Daten nur während Failover synchronisieren (vollständiger Download)**: Verwenden Sie diese Option, wenn Sie über einen längeren Zeitraum Azure verwendet haben. Diese Option ist schneller, da erwartet wird, dass die meisten Daten auf dem Datenträger geändert wurden, und keine Zeit mit der Prüfsummenberechnung verschwendet werden soll. Mit der Option wird ein Download des Datenträgers durchgeführt. Sie ist auch nützlich, wenn der lokale virtuelle Computer gelöscht wurde.
	
	> [AZURE.NOTE] Es wird empfohlen, diese Option zu verwenden, wenn Sie Azure seit einer Weile nutzen (einen Monat oder länger) oder wenn der lokale virtuelle Computer gelöscht wurde. Mit dieser Option werden keine Prüfsummenberechnungen durchgeführt.
	
5. Wenn Sie ein Failover zu Azure durchführen und die Datenverschlüsselung für die Cloud aktiviert ist, wählen Sie unter **Verschlüsselungsschlüssel** das Zertifikat aus, das beim Aktivieren der Datenverschlüsselung im Rahmen der Anbieterinstallation auf dem VMM-Server ausgestellt wurde.
5. Standardmäßig wird der letzte Wiederherstellungspunkt verwendet. Unter **Wiederherstellungspunkt ändern** können Sie bei Bedarf einen anderen Wiederherstellungspunkt angeben.
6. Klicken Sie auf das Häkchen, um das Failback zu starten. Der Fortschritt des Failovers wird auf der Registerkarte **Aufträge** angezeigt.
7. Falls Sie die Option ausgewählt haben, mit der die Daten vor dem Failover synchronisiert werden, klicken Sie auf **Aufträge** > <Auftragsname des geplanten Failovers> **Failover abschließen**, wenn die erste Datensynchronisierung abgeschlossen ist und Sie zum Herunterfahren der virtuellen Computer in Azure bereit sind. Dadurch wird der Azure-Computer heruntergefahren, die neuesten Änderungen werden an den lokalen virtuellen Computer übertragen, und der lokale virtuelle Computer wird gestartet.
8. Anschließend können Sie sich bei dem virtuellen Computer anmelden und sich vergewissern, dass er wie erwartet verfügbar ist.
9. Der Status des virtuellen Computers weist darauf hin, dass ein Commit aussteht. Klicken Sie auf **Commit**, um ein Commit für das Failover auszuführen.
10. Klicken Sie anschließend auf **Umgekehrt replizieren**, um das Failback abzuschließen und den virtuellen Computer am primären Standort zu schützen.



## Failback zu einem alternativen Standort

Wenn Sie den Schutz zwischen einem [Hyper-V-Standort und Azure](site-recovery-hyper-v-site-to-azure.md) bereitgestellt haben, können Sie ein Failback von Azure zu einem alternativen lokalen Standort durchführen. Dies ist hilfreich, wenn Sie neue lokale Hardware einrichten müssen. Gehen Sie hierzu wie folgt vor:

1. Wenn Sie neue Hardware einrichten möchten, installieren Sie Windows Server 2012 R2 und die Hyper-V-Rolle auf dem Server.
2. Erstellen Sie einen virtuellen Netzwerkswitch mit dem gleichen Namen wie auf dem ursprünglichen Server.
3. Wählen Sie **Geschützte Elemente** > **Schutzgruppe** > <Schutzgruppenname> > <Name des virtuellen Computers> für das Failback, und wählen Sie anschließend **Geplantes Failover**.
4. Wählen Sie auf der Bestätigungsseite für das geplante Failover die Option für die Erstellung des virtuellen Computers aus, falls dieser noch nicht vorhanden ist.
5. Wählen Sie unter **Hostname** den neuen Hyper-V-Hostserver aus, auf dem sich der virtuelle Computer befinden soll.
6. Für die Datensynchronisierung empfiehlt sich die Option **Daten vor dem Failover synchronisieren**. Diese Option minimiert die Ausfallzeiten der virtuellen Computer, da diese für die Synchronisierung nicht heruntergefahren werden. Die Option bewirkt Folgendes:

	- Phase 1: Sie erstellt eine Momentaufnahme des virtuellen Computers in Azure und kopiert sie auf den lokalen Hyper-V-Host. Der Computer wird weiterhin in Azure ausgeführt.
	- Phase 2: Sie fährt den virtuellen Computer in Azure herunter, damit keine neuen Änderungen vorgenommen werden. Die letzten Änderungen werden an den lokalen Server übertragen, und der lokale virtuelle Computer wird gestartet.
	
7. Klicken Sie auf das Häkchen, um das Failover (Failback) zu starten.
8. Klicken Sie auf **Aufträge** > <Auftragsname des geplanten Failovers> > **Failover abschließen**, wenn die erste Synchronisierung abgeschlossen ist und Sie zum Herunterfahren des virtuellen Computers in Azure bereit sind. Dadurch wird der Azure-Computer heruntergefahren, die neuesten Änderungen werden an den lokalen virtuellen Computer übertragen, und der lokale virtuelle Computer wird gestartet.
9. Sie können sich bei dem lokalen virtuellen Computer anmelden und sich vergewissern, das alles wie erwartet funktioniert. Klicken Sie anschließend auf **Commit**, um das Failover abzuschließen.
10. Klicken Sie auf **Umgekehrt replizieren**, um den Schutz des lokalen virtuellen Computers zu starten.

	>[AZURE.NOTE] Wenn Sie den Failbackauftrag während der Datensynchronisierung abbrechen, wird der lokale virtuelle Computer als beschädigt angezeigt. Das liegt daran, dass bei der Datensynchronisierung die aktuellen Daten von den Datenträgern virtueller Azure-Computer auf die lokalen Datenträger kopiert werden. Bis zum Abschluss der Synchronisierung befinden sich die Datenträgerdaten unter Umständen nicht in einem konsistenten Zustand. Wenn der lokale virtuelle Computer nach dem Abbruch der Datensynchronisierung gestartet wird, ist das Starten unter Umständen nicht möglich. Lösen Sie zum Abschließen der Datensynchronisierung das Failover erneut aus.
 

<!---HONumber=AcomDC_0720_2016-->