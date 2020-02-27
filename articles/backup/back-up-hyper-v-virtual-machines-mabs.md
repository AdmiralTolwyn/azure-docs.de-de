---
title: Sichern von virtuellen Hyper-V-Computern mit MABS
description: Dieser Artikel enthält die Verfahren zum Sichern und Wiederherstellen von virtuellen Computern mit Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 07/18/2019
ms.openlocfilehash: e23a3a5ad57e07f95958d8a21e091d663a5c1185
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586511"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Sichern von virtuellen Hyper-V-Computern mit Azure Backup Server

In diesem Artikel wird das Sichern von virtuellen Hyper-V-Computern mit Microsoft Azure Backup Server (MABS) beschrieben.

## <a name="supported-scenarios"></a>Unterstützte Szenarios

MABS kann virtuelle Computer, die auf Hyper-V-Hostservern ausgeführt werden, in den folgenden Szenarien sichern:

- **Virtuelle Computer mit lokalem oder direktem Speicher**: Sichern Sie virtuelle Computer, die auf eigenständigen Hyper-V-Hostservern gehostet werden und über lokale oder direkt angefügte Speicher verfügen. Beispiel: eine Festplatte, ein SAN- (Storage Area Network) oder ein NAS-Gerät (Network Attached Storage). Der MABS-Schutz-Agent muss auf allen Hosts installiert sein.

- **Virtuelle Computer in einem Cluster mit CSV-Speicher**: Sichern Sie virtuelle Computer, die auf einem Hyper-V-Cluster mit freigegebenem Clustervolumespeicher (CSV) gehostet werden. Der MABS-Schutz-Agent wird auf jedem Clusterknoten installiert.

## <a name="host-versus-guest-backup"></a>Sicherung auf Host- und Gastebene im Vergleich

MABS kann virtuelle Hyper-V-Computer auf Host- oder Gastebene sichern. Auf Hostebene ist der MABS-Schutz-Agent auf dem Hyper-V-Hostserver oder -Cluster installiert und schützt die gesamten VMs und Datendateien, die auf diesem Host ausgeführt werden.   Auf Gastebene wird der Agent auf allen virtuellen Computern installiert und schützt somit die auf der jeweiligen VM vorhandene Workload.

Beide Methoden haben Vor- und Nachteile:

- Sicherungen auf Hostebene sind flexibel, da Sie unabhängig von der Art des Betriebssystems auf den Gastcomputern arbeiten, und es ist nicht erforderlich, dass der MABS-Schutz-Agent auf jeder VM installiert ist. Wenn Sie Sicherungen auf Hostebene bereitstellen, stellen Sie einen kompletten virtuellen Computer oder Dateien und Ordner wieder her (Wiederherstellung auf Elementebene).

- Die Sicherung auf Gastebene ist für den Schutz bestimmter Workloads auf einem virtuellen Computer von Vorteil. Auf Hostebene können Sie eine gesamte VM oder bestimmte Dateien wiederherstellen, jedoch keine Daten im Kontext einer bestimmten Anwendung. Um beispielsweise bestimmte SharePoint-Elemente von einer gesicherten VM wiederherzustellen, sollten Sie eine Sicherung auf Gastebene dieser VM durchführen. Wenn Sie auf Pass-Through-Datenträgern gespeicherte Daten schützen möchten, müssen Sie die Sicherung auf Gastebene wählen. Die Pass-Through-Technologie ermöglicht dem virtuellen Computer den Direktzugriff auf das Speichergerät und speichert keine virtuellen Volumedaten in einer VHD-Datei.

## <a name="how-the-backup-process-works"></a>Funktionsweise des Sicherungsvorgangs

MABS führt die Sicherung mit VSS wie folgt aus. Die Schritte in dieser Beschreibung sind zur besseren Übersichtlichkeit nummeriert.

1. Die blockbasierte Synchronisierungs-Engine von MABS erstellt eine erste Kopie des geschützten virtuellen Computers und stellt sicher, dass diese Kopie vollständig und konsistent ist.

2. Nachdem die erste Kopie erstellt und verifiziert wurde, verwendet MABS den Hyper-V-VSS-Writer zum Erfassen von Sicherungen. Der VSS Writer bietet einen datenkonsistenten Satz von Datenträgerblöcken, die mit dem MABS-Server synchronisiert sind. Dieser Ansatz bietet den Vorteil einer „vollständigen Sicherung“ mit dem MABS-Server und minimiert gleichzeitig den Umfang der Sicherungsdaten, die über das Netzwerk übertragen werden müssen.

3. Der MABS-Schutz-Agent auf einem Server, auf dem Hyper-V ausgeführt wird, verwendet die vorhandenen Hyper-V-APIs, um festzustellen, ob ein geschützter virtueller Computer ebenfalls VSS unterstützt.

   - Wenn ein virtueller Computer die Anforderungen für eine Onlinesicherung erfüllt und die Hyper-V-Integrationsdienste installiert sind, leitet der Hyper-V-VSS Writer die VSS-Anforderungen rekursiv an alle VSS-fähigen Prozesse auf dem virtuellen Computer weiter. Dieser Vorgang wird ausgeführt, ohne dass der MABS-Schutz-Agent auf dem virtuellen Computer installiert ist. Mit der rekursiven VSS-Anforderung kann der Hyper-V-VSS Writer sicherstellen, dass die Schreibvorgänge auf dem Datenträger synchronisiert werden, sodass eine VSS-Momentaufnahme ohne Datenverlust erfasst wird.

     Die Hyper-V-Integrationsdienste rufen den Hyper-V-VSS Writer im Volumeschattenkopie-Dienste (VSS) auf virtuellen Computern auf, um sicherzustellen, dass sich ihre Anwendungsdaten in einem konsistenten Zustand befinden.

   - Wenn der virtuelle Computer nicht den Anforderungen für Onlinesicherungen entspricht, verwendet MABS automatisch die Hyper-V-APIs, um den virtuellen Computer anzuhalten, bevor Datendateien erfasst werden.

4. Nachdem die erste Baselinekopie des virtuellen Computers mit dem MABS-Server synchronisiert wurde, werden alle Änderungen, die an den Ressourcen des virtuellen Computers vorgenommen werden, in einem neuen Wiederherstellungspunkt erfasst. Der Wiederherstellungspunkt stellt den konsistenten Zustand des virtuellen Computers zu einem bestimmten Zeitpunkt dar. Wiederherstellungspunkte können mindestens einmal täglich erfasst werden. Wenn ein neuer Wiederherstellungspunkt erstellt wird, verwendet MABS die Replikation auf Blockebene in Verbindung mit dem Hyper-V-VSS-Writer, um festzustellen, welche Blöcke auf dem Server, auf dem Hyper-V ausgeführt wird, nach der Erstellung des letzten Wiederherstellungspunkts geändert wurden. Diese Datenblöcke werden dann an den MABS-Server übertragen und auf das Replikat der geschützten Daten angewendet.

5. Der MABS-Server verwendet VSS auf den Volumes, die Wiederherstellungsdaten hosten, sodass mehrere Schattenkopien verfügbar sind. Jede dieser Schattenkopien ermöglicht eine separate Wiederherstellung. VSS-Wiederherstellungspunkte werden auf dem MABS-Server gespeichert. Die temporäre Kopie, die auf dem Server mit Hyper-V erstellt wird, wird nur für die Dauer der MABS-Synchronisierung gespeichert.

>[!NOTE]
>
>Ab Windows Server 2016 verfügen virtuelle Hyper-V-Festplatten über eine integrierte Änderungsnachverfolgung, die als robuste Änderungsnachverfolgung (Resilient Change Tracking, RCT) bezeichnet wird. MABS verwendet RCT, die systemeigene Änderungsnachverfolgung in Hyper-V, wodurch zeitaufwändige Konsistenzprüfungen etwa bei VM-Abstürzen entfallen. RCT bietet eine bessere Resilienz als die Änderungsnachverfolgung, die von Sicherungen bereitgestellt wird, die auf VSS-Momentaufnahmen basieren. MABS V3 optimiert den Netzwerk- und Speicherverbrauch außerdem, indem bei Konsistenzprüfungen nur die geänderten Daten übertragen werden.

## <a name="backup-prerequisites"></a>Voraussetzungen für eine Sicherung

Dies sind die Voraussetzungen für die Sicherung virtueller Hyper-V-Computer mit MABS:

|Voraussetzung|Details|
|------------|-------|
|MABS-Voraussetzungen|- Wenn Sie eine Wiederherstellung auf Elementebene für virtuelle Computer (Dateien, Ordner, Volumes) durchführen möchten, müssen Sie die Hyper-V-Rolle auf dem MABS-Server installieren.  Wenn Sie eine Wiederherstellung auf Elementebene für virtuelle Computer (Dateien, Ordner, Volumes) durchführen möchten, müssen Sie die Hyper-V-Rolle auf dem MABS-Server installieren.<br />- Sie können bis zu 800 virtuelle Computer mit je 100 GB auf einem MABS-Server schützen und mehrere MABS-Server zulassen, die größere Cluster unterstützen.<br />- MABS schließt die Auslagerungsdatei von inkrementellen Sicherungen aus, um die Sicherungsleistung des virtuellen Computers zu verbessern.<br />- MABS kann einen Hyper-V-Server oder -Cluster in der gleichen Domäne sichern, in der sich auch der MABS-Server befindet, oder in einer Unter- oder vertrauenswürdigen Domäne. Wenn Sie Hyper-V in einer Arbeitsgruppe oder einer nicht vertrauenswürdigen Domäne sichern möchten, müssen Sie die Authentifizierung einrichten. Für einen einzelnen Hyper-V-Server können Sie NTLM oder die Zertifikatauthentifizierung verwenden. Bei einem Cluster können Sie nur die Zertifikatauthentifizierung verwenden.<br />- Die Verwendung von Sicherungen auf Hostebene zur Sicherung von Daten virtueller Computer auf Pass-Through-Datenträgern wird nicht unterstützt. In diesem Szenario empfehlen wir Ihnen, zum Sichern von VHD-Dateien eine Sicherung auf Hostebene und zum Sichern der anderen Daten, die auf dem Host nicht sichtbar sind, eine Sicherung auf Gastebene durchzuführen.<br />   \- Sie können virtuelle Computer sichern, die auf deduplizierten Volumes gespeichert sind.|
|Voraussetzungen für Hyper-V-VMs|Die Version der Integrationskomponenten, die auf dem virtuellen Computer ausgeführt wird, muss mit der Version des Hyper-V-Hosts identisch sein. <br />- Für jede Sicherung der virtuellen Computer benötigen Sie freien Speicherplatz auf dem Volume, auf dem sich die Dateien der virtuellen Festplatten befinden, damit Hyper-V während der Sicherung ausreichend Platz für differenzierende Datenträger (AVHD) hat. Der Speicherplatz muss mindestens der berechneten Fensterzeit **Anfängliche Datenträgergröße\*Änderungsrate\*Sicherung** entsprechen. Wenn Sie mehrere Sicherungen auf einem Cluster ausführen, benötigen Sie genügend Speicherkapazität für die AVHDs für jeden virtuellen Computer, der diese Berechnung verwendet.<br />- Um virtuelle Computer zu sichern, die auf Hyper-V-Hostservern mit Windows Server 2012 R2 ausgeführt werden, sollte für den virtuellen Computer ein SCSI-Controller angegeben sein, auch wenn dieser nicht mit einer Komponente verbunden ist. (Bei der Onlinesicherung von Windows Server 2012 R2 bindet der Hyper-V-Host eine neue VHD auf der VM ein und hebt die Einbindung später wieder auf. Nur der SCSI-Controller kann dies unterstützen. Daher ist er für die Onlinesicherung des virtuellen Computers erforderlich.  Ohne diese Einstellung wird die Ereignis-ID 10103 ausgegeben, wenn Sie versuchen, den virtuellen Computer zu sichern.)|
|Linux-Voraussetzungen|– Sie können virtuelle Linux-Computer mithilfe von MABS sichern. Es werden nur dateikonsistente Momentaufnahmen unterstützt.|
|Sichern von VMs mit CSV-Speicher|-Installieren Sie bei einem CSV-Speicher den Hardwareanbieter für  den Volumeschattenkopie-Dienst (VSS) auf dem Hyper-V-Server. Wenden Sie sich für Informationen zum VSS-Hardwareanbieter an Ihren Storage Area Network-Anbieter.<br />- Wenn ein einzelner Knoten in einem CSV-Cluster unerwartet heruntergefahren wird, führt MABS eine Konsistenzprüfung für die virtuellen Computer durch, die auf diesem Knoten ausgeführt wurden.<br />Wenn Sie einen Hyper-V-Server mit aktivierter BitLocker-Laufwerkverschlüsselung auf dem CSV-Cluster neu starten müssen, müssen Sie eine Konsistenzprüfung für die virtuelle Hyper-V-Computer durchführen.|
|Sichern von VMs mit SMB-Speicher|Aktivieren Sie auf dem Server, auf dem Hyper-V ausgeführt wird, die automatische Einbindung, um den Schutz für virtuelle Computer zu aktivieren.<br />   -Deaktivieren Sie die TCP Chimney Offload.<br />- Stellen Sie sicher, dass alle Hyper-V machine$-Konten über volle Berechtigungen für die spezifischen SMB-Remotedateifreigaben verfügen.<br />- Stellen Sie sicher, dass die Länge des Dateipfads für alle Komponenten des virtuellen Computers während der Wiederherstellung zum alternativen Speicherort weniger als 260 Zeichen beträgt. Andernfalls ist die Wiederherstellung möglicherweise erfolgreich, aber Hyper-V kann den virtuellen Computer nicht einbinden.<br />- Folgende Szenarios werden nicht unterstützt:<br />     Bereitstellungen, bei denen sich einige Komponenten des virtuellen Computers auf lokalen Volumes und einige Komponenten auf Remotevolumes befinden; eine IPv4- oder IPv6-Adresse für den Speicherort des Dateiservers und die Wiederherstellung eines virtuellen Computers auf einem Computer, der SMB-Remotefreigaben verwendet.<br />Sie müssen den Dateiserver-VSS-Agent-Dienst auf jedem SMB-Server aktivieren. Fügen Sie ihn in **Rollen und Funktionen hinzufügen** > **Serverrollen auswählen** > **Datei- und Speicherdienste** > **Dateidienste** > **Dateidienst** > **Dateiserver-VSS-Agenten-Dienst** hinzu.|

## <a name="back-up-virtual-machines"></a>Sichern virtueller Computer

1. Richten Sie Ihren [MABS-Server](backup-azure-microsoft-azure-backup.md) und [Ihren Speicher](backup-mabs-add-storage.md) ein. Verwenden Sie bei der Einrichtung Ihres Speichers diese Richtlinien zur Speicherkapazität.
   - Durchschnittliche VM-Größe: 100 GB
   - Anzahl virtueller Computer pro MABS-Server: 800
   - Gesamtgröße von 800 VMs: 80 TB
   - Erforderlicher Speicherplatz für den Sicherungsspeicher: 80 TB

2. Richten Sie den MABS-Schutz-Agent auf dem Hyper-V-Server oder den Hyper-V-Clusterknoten ein. Wenn Sie eine Sicherung auf Gastebene durchführen, installieren Sie den Agent auf den VMs, die Sie auf Gastebene sichern möchten.

3. Klicken Sie in der MABS-Administratorkonsole auf **Schutz** > **Schutzgruppe erstellen**, um den Assistenten **Neue Schutzgruppe erstellen** zu öffnen.

4. Wählen Sie auf der Seite **Gruppenmitglieder auswählen** die VMs aus, die Sie vor den Hyper-V-Hostservern schützen möchten, auf denen sie sich befinden. Wir empfehlen Ihnen, alle VMs, die die gleiche Schutzrichtlinie verwenden, in einer Schutzgruppe zusammenzufassen. Um den Speicherplatz effizient zu nutzen, aktivieren Sie die Zusammenstellung. Mit der Zusammenstellung können Sie Daten aus verschiedenen Schutzgruppen auf demselben Datenträger oder Bandspeicher suchen, sodass mehrere Datenquellen ein einzelnes Replikat- und Wiederherstellungspunkt-Volume verwenden.

5. Geben Sie auf der Seite **Methode für die Datensicherheit auswählen** einen Namen für die Schutzgruppe ein. Wählen Sie **Ich möchte einen kurzfristigen Schutz mit Datenträger** und dann **Ich möchte Onlineschutz** aus, wenn Sie Daten mit dem Azure Backup-Dienst in Azure sichern möchten.

6. Geben Sie unter **Kurzfristige Ziele angeben** > **Aufbewahrungsdauer** an, wie lange Sie Datenträgerdaten aufbewahren möchten. Geben Sie unter **Synchronisierungsfrequenz** an, wie oft inkrementelle Sicherungen der Daten durchgeführt werden sollen. Alternativ können Sie anstelle der Auswahl eines Intervalls für inkrementelle Sicherungen auch **Direkt vor einen Wiederherstellungspunkt** aktivieren. Mit dieser Einstellung führt MABS eine vollständige Schnellsicherung unmittelbar vor jedem geplanten Wiederherstellungspunkt aus.

    > [!NOTE]
    >
    >Wenn Sie Anwendungsworkloads schützen, werden Wiederherstellungspunkte gemäß der Synchronisierungsfrequenz erstellt, sofern die Anwendung inkrementelle Sicherungen unterstützt. Wenn dies nicht der Fall ist, führt MABS anstelle einer inkrementellen Sicherung eine vollständige Schnellsicherung durch und erstellt Wiederherstellungspunkte gemäß dem Zeitplan für die Schnellsicherung.

7. Überprüfen Sie auf der Seite **Datenträgerzuordnungen überprüfen** den Speicherplatz im Speicherpool, der der Schutzgruppe zugeordnet ist.

   **Gesamtdatengröße** ist die Größe der Daten, die Sie sichern möchten. **Bereitzustellender Speicherplatz für MABS** ist der für die Schutzgruppe von MABS empfohlene Speicherplatz. MABS wählt auf Grundlage der Einstellungen das ideale Sicherungsvolume. Sie können jedoch in **Details zur Datenträgerzuordnung** das gewählte Sicherungsvolume ändern. Wählen Sie für die Workloads im Dropdownmenü den bevorzugten Speicher aus. Durch Ihre Änderungen verändern sich im Bereich **Verfügbarer Speicherplatz** die Werte für **Gesamtspeicher** und **Freier Speicher**. Nicht ausreichend bereitgestellter Speicherplatz ist die Menge an Speicher, die laut Vorschlag von MABS zum Sicherstellen künftiger reibungsloser Sicherungen dem Volume hinzugefügt werden sollte.

8. Geben Sie auf der Seite **Replikaterstellungsmethode auswählen** an, wie die erste Replikation der Daten in der Schutzgruppe ausgeführt werden soll. Wenn Sie sich für die **automatische Replikation über das Netzwerk** entscheiden, sollten Sie hierfür eine Nebenzeit auswählen. Ziehen Sie bei großen Datenmengen oder nicht optimalen Netzwerkbedingungen die Auswahl der Option **Manuell** in Betracht, die Offlinereplikation der Daten mit Wechselmedien erfordert.

9. Legen Sie auf der Seite **Konsistenzprüfungsoptionen auswählen** fest, wie Konsistenzprüfungen automatisiert werden sollen. Sie können eine Überprüfung aktivieren, die nur bei inkonsistenten Replikatdaten oder gemäß einem festgelegten Zeitplan ausgeführt wird. Wenn Sie keine automatischen Konsistenzprüfungen konfigurieren möchten, können Sie jederzeit eine manuelle Überprüfung ausführen, indem Sie mit der rechten Maustaste auf die Schutzgruppe klicken und **Konsistenzprüfung ausführen** auswählen.

    Nachdem Sie die Schutzgruppe erstellt haben, erfolgt die erste Replikation der Daten in Übereinstimmung mit der von Ihnen ausgewählten Methode. Nach der ersten Replikation erfolgt jede Sicherung entsprechend den Einstellungen der Schutzgruppe. Für die Wiederherstellung gesicherter Daten müssen Sie Folgendes beachten:

## <a name="back-up-virtual-machines-configured-for-live-migration"></a>Sichern von für die Livemigration konfigurierte virtuelle Computer

Wenn virtuelle Computer an der Livemigration beteiligt sind, sind diese durch MABS weiterhin geschützt, solange der MABS-Schutz-Agent auf dem Hyper-V-Host installiert ist. Wie MABS die virtuellen Computer schützt, hängt von der Art der Livemigration ab.

**Livemigration innerhalb eines Clusters**: Wenn ein virtueller Computer innerhalb eines Clusters migriert wird, erkennt MABS die Migration und sichert den virtuellen Computer über den neuen Clusterknoten, ohne dass der Benutzer eingreifen muss. Da sich der Speicherort nicht geändert hat, fährt MABS mit einer vollständige Schnellsicherung fort.

**Livemigration außerhalb des Clusters**: Wenn ein virtueller Computer zwischen eigenständigen Servern, verschiedenen Clustern oder zwischen einem eigenständigen Server und einem Cluster migriert wird, erkennt MABS die Migration und kann den virtuellen Computer ohne Benutzereingriff sichern.

### <a name="requirements-for-maintaining-protection"></a>Anforderungen für die Aufrechterhaltung des Schutzes

Die folgenden Anforderungen gelten für die Aufrechterhaltung des Schutzes während der Livemigration:

- Die Hyper-V-Hosts für die virtuellen Computer müssen sich in einer System Center VMM-Cloud auf einem VMM-Server befinden und mindestens System Center 2012 mit SP1 ausführen.

- Der MABS-Schutz-Agent muss auf allen Hyper-V-Hosts installiert sein.

- MABS-Server müssen mit dem VMM-Server verbunden sein. Alle Hyper-V-Hostserver in der VMM-Cloud müssen auch mit den MABS-Servern verbunden sein. Dadurch kann MABS mit dem VMM-Server kommunizieren, um zu ermitteln, auf welchem Hyper-V-Hostserver der virtuelle Computer gerade ausgeführt wird, und um eine neue Sicherung von diesem Hyper-V-Server zu erstellen. Wenn keine Verbindung zum Hyper-V-Server hergestellt werden kann, schlägt die Sicherung mit der Meldung fehl, dass der MABS-Schutz-Agent nicht erreichbar ist.

- Alle MABS-Server, VMM-Server und Hyper-V-Hostserver müssen sich in derselben Domäne befinden.

### <a name="details-about-live-migration"></a>Details zur Livemigration

Für eine Sicherung während einer Livemigration ist Folgendes zu beachten:

- Wenn bei einer Livemigration Speicher übertragen wird, führt MABS eine vollständige Konsistenzprüfung des virtuellen Computers durch und fährt dann mit einer vollständigen Schnellsicherung fort. Wenn eine Livemigration des Speichers stattfindet, organisiert Hyper-V die virtuelle Festplatte (VHD) oder VHDX neu. Dies führt zu einem einmaligen Anstieg der Größe der MABS-Sicherungsdaten.

- Aktivieren Sie auf dem Host des virtuellen Computers die automatische Einbindung, um den virtuellen Schutz zu aktivieren, und deaktivieren Sie TCP Chimney Offload.

- MABS verwendet Port 6070 als Standardport für das Hosting des DPM-VMM-Hilfsprogrammdiensts. So ändern Sie die Registrierung:

    1. Navigieren Sie zu **HKLM\Software\Microsoft\Microsoft Data Protection Manager\Configuration**.
    2. Erstellen Sie einen 32-Bit-DWORD-Wert: „DpmVmmHelperServicePort“, und schreiben Sie die aktualisierte Portnummer als Teil des Registrierungsschlüssels.
    3. Öffnen Sie ```<Install directory>\Azure Backup Server\DPM\DPM\VmmHelperService\VmmHelperServiceHost.exe.config``` und tauschen Sie die Portnummer 6070 gegen die neue Portnummer aus. Beispiel: ```<add baseAddress="net.tcp://localhost:6080/VmmHelperService/" />```
    4. Starten Sie den DPM-VMM-Hilfsprogrammdienst und den DPM-Dienst neu.

### <a name="set-up-protection-for-live-migration"></a>Einrichten des Schutzes für die Livemigration

So richten Sie den Schutz für die Livemigration ein:

1. Richten Sie den MABS-Server und seinen Speicher ein, und installieren Sie den MABS-Schutz-Agent auf jedem Hyper-V-Hostserver oder Clusterknoten in der VMM-Cloud. Wenn Sie SMB-Speicher in einem Cluster verwenden, installieren Sie den MABS-Schutz-Agent auf allen Clusterknoten.

2. Installieren Sie die VMM-Konsole als Clientkomponente auf dem MABS-Server, damit MABS mit dem VMM-Server kommunizieren kann. Die Version der Konsole muss mit der Version übereinstimmen, die auf dem VMM-Server ausgeführt wird.

3. Weisen Sie das MABSMachineName$-Konto als schreibgeschütztes Administratorkonto auf dem VMM-Verwaltungsserver zu.

4. Verbinden Sie alle Hyper-V-Hostserver mithilfe des PowerShell-Cmdlets `Set-DPMGlobalProperty` mit allen MABS-Servern. Das Cmdlet akzeptiert mehrere MABS-Servernamen. Verwenden Sie das folgende Format: `Set-DPMGlobalProperty -dpmservername <MABSservername> -knownvmmservers <vmmservername>`. Weitere Informationen finden Sie unter [Set-DPMGlobalProperty](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmglobalproperty?view=systemcenter-ps-2019).

5. Wenn alle virtuellen Computer, die auf den Hyper-V-Hosts in den VMM-Clouds ausgeführt werden, in VMM erkannt werden, richten Sie eine Schutzgruppe ein, und fügen Sie die virtuellen Computer hinzu, die Sie schützen möchten. Für den Schutz im Rahmen von Mobilitätsszenarien für virtuelle Computer sollten automatische Konsistenzprüfungen auf der Ebene der Schutzgruppen aktiviert werden.

6. Wenn die Einstellungen konfiguriert sind und eine virtuelle Maschine von einem Cluster zum anderen migriert, werden alle Sicherungen wie erwartet fortgesetzt. Sie können wie folgt überprüfen, ob die Livemigration wie erwartet aktiviert ist:

   1. Überprüfen Sie, ob der DPM-VMM-Hilfsprogrammdienst ausgeführt wird. Wenn dies nicht der Fall ist, starten Sie ihn.

   2. Öffnen Sie Microsoft SQL Server Management Studio, und stellen Sie eine Verbindung mit der Instanz her, die die MABS-Datenbank (DPMDB) hostet. Führen Sie auf der DPMDB die folgende Abfrage aus: `SELECT TOP 1000 [PropertyName] ,[PropertyValue] FROM[DPMDB].[dbo].[tbl_DLS_GlobalSetting]`.

      Diese Abfrage enthält eine Eigenschaft mit dem Namen `KnownVMMServer`. Dieser Wert muss derselbe sein, den Sie mit dem Cmdlet `Set-DPMGlobalProperty` angegeben haben.

   3. Führen Sie die folgende Abfrage aus, um den Parameter *VMMIdentifier* in `PhysicalPathXML` für einen bestimmten virtuellen Computer zu überprüfen. Ersetzen Sie `VMName` durch den Namen des virtuellen Computers.

      ```sql
      select cast(PhysicalPath as XML) from tbl_IM_ProtectedObject where DataSourceId in (select datasourceid from tbl_IM_DataSource   where DataSourceName like '%<VMName>%')
      ```

   4. Öffnen Sie die von dieser Abfrage zurückgegebene .xml-Datei und überprüfen Sie, ob das Feld *VMMIdentifier* einen Wert hat.

### <a name="run-manual-migration"></a>Ausführen der manuellen Migration

Nachdem Sie die Schritte in den vorherigen Abschnitten ausgeführt haben und der MABS Summary Manager-Auftrag abgeschlossen ist, ist die Migration aktiviert. Standardmäßig wird dieser Auftrag um Mitternacht gestartet und jeden Morgen ausgeführt. Wenn Sie eine manuelle Migration durchführen möchten, gehen Sie wie folgt vor, um sicherzustellen, dass alles wie erwartet funktioniert:

1. Öffnen Sie SQL Server Management Studio, und stellen Sie eine Verbindung mit der Instanz her, die die MABS-Datenbank hostet.

2. Führen Sie die folgende Abfrage aus: `select * from tbl_SCH_ScheduleDefinition where JobDefinitionID='9B30D213-B836-4B9E-97C2-DB03C3EB39D7'`. Mit dieser Abfrage wird die **ScheduleID** zurückgegeben. Notieren Sie sich diese ID, da Sie sie im nächsten Schritt verwenden.

3. Erweitern Sie im SQL Server Management Studio **SQL Server-Agent** und dann **Aufträge**. Klicken Sie mit der rechten Maustaste auf **ScheduleID** die Sie notiert haben, und wählen Sie **Auftrag starten bei Schritt...** aus.

Wenn der Auftrag ausgeführt wird, hat dies Einfluss auf die Sicherungsleistung. Die Größe und die Skalierung Ihrer Bereitstellung bestimmen, wie lange die Ausführung des Auftrags dauert.

## <a name="back-up-replica-virtual-machines"></a>Sichern von virtuellen Replikatcomputern

Wenn MABS unter Windows Server 2012 R2 oder höher ausgeführt wird, können Sie virtuelle Replikatcomputer sichern. Dies kann aus verschiedenen Gründen nützlich sein:

**Verringert die Auswirkungen von Sicherungen auf die laufende Workload**: Das Erstellen einer Sicherung eines virtuellen Computers verursacht zusätzlichen Aufwand, wenn eine Momentaufnahme erstellt wird. Durch das Abladen des Sicherungsvorgangs an einen sekundären Remotestandort wird die laufende Workload nicht mehr durch den Sicherungsvorgang beeinträchtigt. Dies gilt nur für Bereitstellungen, bei denen die Sicherungskopie an einem Remotestandort gespeichert ist. Beispielsweise können Sie tägliche Sicherungen erstellen und Daten lokal speichern, um schnelle Wiederherstellungszeiten zu gewährleisten, aber monatliche oder vierteljährliche Sicherungen von virtuellen Replikaten durchführen, die zur langfristigen Aufbewahrung remote gespeichert sind.

**Spart Bandbreite**: In einer typischen Bereitstellung mit Remotezweigstelle/Zentrale benötigen Sie eine angemessene Menge an bereitgestellter Bandbreite, um Sicherungsdaten zwischen Standorten zu übertragen. Wenn Sie zusätzlich zu Ihrer Datensicherungsstrategie eine Replikations- und Failoverstrategie entwickeln, können Sie die Menge der über das Netzwerk gesendeten redundanten Daten reduzieren. Indem Sie die Daten des virtuellen Replikatcomputers anstelle der Daten des primären Computers sichern, sparen Sie den Aufwand für das Senden der gesicherten Daten über das Netzwerk.

**Ermöglicht Hostersicherung**: Sie können ein gehostetes Rechenzentrum als Replikatstandort verwenden und benötigen kein sekundäres Rechenzentrum. In diesem Fall erfordert die SLA des Hosters eine konsistente Sicherung von virtuellen Replikatcomputern.

Eine virtueller Replikatcomputer wird deaktiviert, bis ein Failover eingeleitet wird, und VSS kann keine anwendungskonsistente Sicherung für einen virtuelle Replikatcomputer garantieren. Daher ist die Sicherung einer virtuellen Replikatcomputers nur absturzkonsistent. Wenn die Absturzkonsistenz nicht gewährleistet werden kann, schlägt die Sicherung fehl. Dies kann bei einer Reihe von Bedingungen passieren:

- Der virtuelle Replikatcomputer ist nicht fehlerfrei und befindet sich in einem kritischen Zustand.

- Der virtuelle Replikatcomputer wird neu synchronisiert (im Status „Neusynchronisierung wird ausgeführt“ oder „Neusynchronisierung erforderlich“).

- Die erste Replikation zwischen dem primären und dem sekundären Standort wird ausgeführt oder steht für den virtuellen Computer aus.

- HRL-Protokolle werden auf den virtuellen Replikatcomputer angewendet, oder eine vorherige Aktion zum Anwenden der HRL-Protokolle auf den virtuellen Datenträger ist fehlgeschlagen oder wurde abgebrochen oder unterbrochen.

- Migration oder Failover des virtuellen Replikatcomputers wird ausgeführt.

## <a name="recover-backed-up-virtual-machines"></a>Wiederherstellen von gesicherten virtuellen Computern

Wenn Sie einen gesicherten virtuellen Computer wiederherstellen können, wählen Sie den virtuellen Computer und den jeweiligen Wiederherstellungspunkt über den Wiederherstellungsassistenten aus. So öffnen Sie den Wiederherstellungs-Assistenten und stellen einen virtuellen Computer wieder her:

1. Geben Sie in der MABS-Administratorkonsole den Namen des virtuellen Computers ein, oder erweitern Sie die Liste der geschützten Elemente, und wählen Sie die VM aus, die Sie wiederherstellen möchten.

2. Klicken Sie im Bereich **Wiederherstellungspunkte für** im Kalender auf ein beliebiges Datum, um die verfügbaren Wiederherstellungspunkte anzuzeigen. Wählen Sie dann im Bereich **Pfad** den Wiederherstellungspunkt aus, den Sie im Wiederherstellungs-Assistenten verwenden möchten.

3. Klicken Sie im Menü **Aktionen** auf **Wiederherstellen**, um den Wiederherstellungs-Assistenten zu öffnen.

    Die ausgewählte VM und der ausgewählte Wiederherstellungspunkt werden auf dem Bildschirm **Wiederherstellungsauswahl überprüfen** angezeigt. Klicken Sie auf **Weiter**.

4. Wählen Sie auf dem Bildschirm **Wiederherstellungstyp** aus, wo Sie die Daten wieder wiederherstellen möchten, und klicken Sie dann auf **Weiter**.

    - **In ursprünglicher Instanz wiederherstellen**: Wenn die Wiederherstellung in die ursprüngliche Instanz ausgeführt wird, wird die ursprüngliche VHD gelöscht. MABS stellt die VHD und andere Konfigurationsdateien mithilfe von Hyper-V-VSS Writer am ursprünglichen Speicherort wieder her. Am Ende des Wiederherstellungsprozesses sind virtuelle Computer weiterhin hoch verfügbar.
        Die Ressourcengruppe muss für der Wiederherstellung vorhanden sein. Wenn Sie nicht verfügbar ist, führen Sie die Wiederherstellung an einem anderen Speicherort durch, und machen Sie den virtuellen Computer anschließend hoch verfügbar.

    - **Als virtuellen Computer für beliebigen Host wiederherstellen**: MABS unterstützt die Wiederherstellung an einem alternativen Speicherort (Alternate Location Recovery, ALR), mit der ein geschützter virtueller Hyper-V-Computer unabhängig von der Prozessorarchitektur nahtlos auf einem anderen Hyper-V-Host wiederhergestellt wird. Virtuelle Hyper-V-Computer, die in einem Clusterknoten wiederhergestellt werden, sind nicht hoch verfügbar. Wenn Sie diese Option auswählen, zeigt der Wiederherstellungs-Assistent einen zusätzlichen Bildschirm zum Angeben des Ziel und des Zielpfads an.

    - **In einen Netzwerkordner kopieren**: MABS unterstützt die Wiederherstellung auf Elementebene (ILR), mit der Sie Dateien, Ordner, Volumes und virtuelle Festplatten (VHDs) auf Elementebene von einer Sicherung auf Hostebene von virtuellen Hyper-V-Computern auf eine Netzwerkfreigabe oder ein Volume auf einem mit MABS geschützten Server wiederherstellen können. Der MABS-Schutz-Agent muss für eine Wiederherstellung auf Elementebene nicht auf dem Gastbetriebssystem installiert sein. Wenn Sie diese Option auswählen, zeigt der Wiederherstellungs-Assistent einen zusätzlichen Bildschirm zum Angeben des Ziel und des Zielpfads an.

5. Konfigurieren Sie unter **Wiederherstellungsoptionen angeben** die Wiederherstellungsoptionen, und klicken Sie auf **Weiter**:

    - Klicken Sie für **Netzwerk-Bandbreiteneinschränkung** auf **Ändern**, wenn Sie eine VM über eine geringe Bandbreite wiederherstellen. Nachdem Sie die Einschränkungsoption aktiviert haben, können Sie die Menge an Bandbreite, die Sie zur Verfügung stellen möchten, und den Zeitpunkt angeben, zu dem diese Bandbreite verfügbar ist.
    - Wählen Sie **SAN-basierte Wiederherstellung mithilfe von Hardwaremomentaufnahmen aktivieren**, wenn Sie Ihr Netzwerk konfiguriert haben.
    - Wählen Sie **Eine E-Mail senden, wenn die Wiederherstellung abgeschlossen ist**, und geben Sie dann die E-Mail-Adressen an, wenn nach Abschluss des Wiederherstellungsprozesses E-Mail-Benachrichtigungen gesendet werden sollen.

6. Vergewissern Sie sich auf dem Bildschirm „Zusammenfassung“, dass alle Details richtig sind. Wenn die Details nicht richtig sind oder Sie eine Änderung vornehmen möchten, klicken Sie auf **Zurück**. Wenn Sie mit den Einstellungen zufrieden sind, klicken Sie auf **Wiederherstellen**, um den Wiederherstellungsprozess zu starten.

7. Der Bildschirm **Wiederherstellungsstatus** enthält Informationen zum Wiederherstellungsauftrag.

## <a name="next-steps"></a>Nächste Schritte

[Wiederherstellen von Daten von Azure Backup Server](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server)
