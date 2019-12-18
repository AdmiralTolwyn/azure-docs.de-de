---
title: Planen einer Azure-Dateisynchronisierungsbereitstellung | Microsoft-Dokumentation
description: Erfahren Sie, was Sie beim Planen einer Azure Files-Bereitstellung berücksichtigen müssen.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bb75fd8aafdc886a8753fa2e6be30d9d7f83bb6f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927865"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planung für die Bereitstellung einer Azure-Dateisynchronisierung
Mit der Azure-Dateisynchronisierung können Sie die Dateifreigaben Ihrer Organisation in Azure Files zentralisieren, ohne auf die Flexibilität, Leistung und Kompatibilität eines lokalen Dateiservers verzichten zu müssen. Mit der Azure-Dateisynchronisierung werden Ihre Windows Server-Computer zu einem schnellen Cache für Ihre Azure-Dateifreigabe. Sie können ein beliebiges Protokoll verwenden, das unter Windows Server verfügbar ist, um lokal auf Ihre Daten zuzugreifen, z.B. SMB, NFS und FTPS. Sie können weltweit so viele Caches wie nötig nutzen.

Dieser Artikel beschreibt wichtige Überlegungen für die Bereitstellung der Azure-Dateisynchronisierung. Es wird empfohlen, dass Sie [Planung für eine Azure Files-Bereitstellung](storage-files-planning.md) lesen. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-file-sync-terminology"></a>Terminologie der Azure-Dateisynchronisierung
Bevor wir in die Details der Planung für eine Azure-Dateisynchronisierungsbereitstellung einsteigen, müssen Sie die Terminologie verstehen.

### <a name="storage-sync-service"></a>Speichersynchronisierungsdienst
Der Speichersynchronisierungsdienst ist die Azure-Ressource der obersten Ebene für die Azure-Dateisynchronisierung. Die Speichersynchronisierungsdienst-Ressource ist der Speicherkontoressource gleichgeordnet und kann in ähnlicher Weise in Azure-Ressourcengruppen bereitgestellt werden. Eine spezielle Ressource der obersten Ebene der Speicherkontoressource ist erforderlich, da der Speichersynchronisierungsdienst Synchronisierungsbeziehungen mit mehreren Speicherkonten über mehrere Synchronisierungsgruppen erstellen kann. In einem Abonnement können mehrere Speichersynchronisierungsdienst-Ressourcen bereitgestellt werden.

### <a name="sync-group"></a>Synchronisierungsgruppe
Eine Synchronisierungsgruppe definiert die Synchronisierungstopologie für einen Satz von Dateien. Endpunkte innerhalb einer Synchronisierungsgruppe bleiben miteinander synchron. Wenn Sie beispielsweise über zwei unterschiedliche Sätze von Dateien verfügen, die Sie mit der Azure-Dateisynchronisierung verwalten möchten, würden Sie zwei Synchronisierungsgruppen erstellen und beiden unterschiedliche Endpunkte hinzufügen. Ein Speichersynchronisierungsdienst kann beliebig viele Synchronisierungsgruppen hosten.  

### <a name="registered-server"></a>Registrierter Server
Das Objekt „Registrierter Server“ stellt eine Vertrauensstellung zwischen Ihrem Server (oder Cluster) und dem Speichersynchronisierungsdienst dar. Sie können beliebig viele Server bei einer Instanz des Speichersynchronisierungsdiensts registrieren. Allerdings kann ein Server (oder Cluster) zu einem beliebigen Zeitpunkt jeweils nur bei einem Speichersynchronisierungsdienst registriert sein.

### <a name="azure-file-sync-agent"></a>Azure-Dateisynchronisierungs-Agent
Der Azure-Dateisynchronisierungs-Agent ist ein herunterladbares Paket, mit dem ein Windows Server-Computer mit einer Azure-Dateifreigabe synchronisiert werden kann. Der Azure-Dateisynchronisierungs-Agent besteht aus drei Hauptkomponenten: 
- **FileSyncSvc.exe**: Der Windows-Hintergrunddienst, der für das Überwachen von Änderungen an Serverendpunkten und das Einleiten von Synchronisierungssitzungen mit Azure zuständig ist.
- **StorageSync.sys**: Der Dateisystemfilter der Azure-Dateisynchronisierung, der für das Tiering von Dateien nach Azure Files zuständig ist (wenn Cloudtiering aktiviert ist).
- **PowerShell-Verwaltungs-Cmdlets**: PowerShell-Cmdlets für die Interaktion mit dem Azure-Ressourcenanbieter „Microsoft.StorageSync“. Sie finden diese an folgenden Speicherorten (Standard):
    - C:\Programme\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Programme\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Serverendpunkt
Ein Serverendpunkt stellt einen bestimmten Speicherort auf einem registrierten Server dar, z. B. einen Ordner auf einem Servervolume. Mehrere Serverendpunkte können auf demselben Volume vorhanden sein, wenn sich deren Namespaces nicht überschneiden (z. B.`F:\sync1` und `F:\sync2`). Sie können Richtlinien für das Cloudtiering für jeden Serverendpunkt separat konfigurieren. 

Sie können einen Serverendpunkt über einen Bereitstellungspunkt erstellen. Beachten Sie, dass Bereitstellungspunkte innerhalb des Serverendpunkts übersprungen werden.  

Sie können einen Serverendpunkt auf dem Systemvolume erstellen, dabei gelten jedoch zwei Einschränkungen:
* Das Cloudtiering kann nicht aktiviert werden.
* Die schnelle Wiederherstellung des Namespace (bei dem der gesamte Namespace im System schnell heruntergefahren und dann der Inhalt abgerufen wird) wird nicht ausgeführt.


> [!Note]  
> Es werden nur nicht austauschbare Volumes unterstützt.  Laufwerke, die über eine Remotefreigabe zugeordnet werden, werden für einen Serverendpunktpfad nicht unterstützt.  Außerdem kann ein Serverendpunkt auch dann auf dem Windows-Systemvolume angeordnet sein, wenn das Cloudtiering auf dem Systemvolume nicht unterstützt wird.

Wenn Sie einen Serverspeicherort, an dem ein Satz Dateien vorhanden ist, einer Synchronisierungsgruppe als Serverendpunkt hinzufügen, werden diese Dateien mit anderen Dateien, die sich bereits auf anderen Endpunkten in der Synchronisierungsgruppe befinden, zusammengeführt.

### <a name="cloud-endpoint"></a>Cloudendpunkt
Ein Cloudendpunkt ist eine Azure-Dateifreigabe, die Teil einer Synchronisierungsgruppe ist. Die gesamte Azure-Dateifreigabe wird synchronisiert, und eine Azure-Dateifreigabe kann nur einem Cloudendpunkt angehören. Aus diesem Grund kann eine Azure-Dateifreigabe nur einer Synchronisierungsgruppe angehören. Wenn Sie eine Azure-Dateifreigabe, die über einen Satz vorhandener Dateien verfügt, einer Synchronisierungsgruppe als Cloudendpunkt hinzufügen, werden diese Dateien mit anderen Dateien zusammengeführt, die sich bereits auf anderen Endpunkten in der Synchronisierungsgruppe befinden.

> [!Important]  
> Die Azure-Dateisynchronisierung unterstützt direkte Änderungen an der Azure-Dateifreigabe. Allerdings müssen alle Änderungen, die Sie an der Azure-Dateifreigabe vornehmen, zuerst von einem Azure-Dateisynchronisierungsauftrag zum Erkennen von Änderungen erkannt werden. Ein Auftrag zum Erkennen von Änderungen für einen Cloudendpunkt wird nur einmal alle 24 Stunden gestartet. Darüber hinaus bewirken Änderungen, die über das REST-Protokoll an einer Azure-Dateifreigabe vorgenommen wurden, keine Aktualisierung der letzten SMB-Änderungszeit, und die Änderungen sind für eine Synchronisierung nicht zu sehen. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zu Azure Files](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Cloudtiering 
Cloudtiering ist ein optionales Feature der Azure-Dateisynchronisierung, bei dem häufig verwendete Dateien lokal auf dem Server zwischengespeichert werden, während alle anderen Dateien gemäß Richtlinieneinstellungen in Azure Files ausgelagert werden. Weitere Informationen finden Sie unter [Grundlegendes zum Cloudtiering](storage-sync-cloud-tiering.md).

## <a name="azure-file-sync-system-requirements-and-interoperability"></a>Systemanforderungen und Interoperabilität der Azure-Dateisynchronisierung 
Dieser Abschnitt behandelt die Systemanforderungen für den Azure-Dateisynchronisierungs-Agent und die Interoperabilität mit Windows Server-Features und -Rollen sowie Lösungen von Drittanbietern.

### <a name="evaluation-cmdlet"></a>Auswertungs-Cmdlet
Vor der Bereitstellung der Azure-Dateisynchronisierung müssen Sie mit dem Auswertungs-Cmdlet für die Azure-Dateisynchronisierung auswerten, ob Kompatibilität mit Ihrem System gegeben ist. Dieses Cmdlet prüft auf potenzielle Probleme mit Ihrem Dateisystem und Dataset, z. B. nicht unterstützte Zeichen oder eine nicht unterstützte Betriebssystemversion. Beachten Sie, dass mit diesem Tool die meisten – aber nicht alle – der unten genannten Features überprüft werden. Es wird empfohlen, dass Sie den verbleibenden Teil dieses Abschnitts sorgfältig durchgehen, um sicherzustellen, dass Ihre Bereitstellung reibungslos verläuft. 

Sie können das Auswertungs-Cmdlet installieren, indem Sie das PowerShell-Modul „Az“ anhand der folgenden Anweisungen installieren: [Installieren und Konfigurieren von Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Verwendung  
Sie können das Auswertungstool in unterschiedlicher Weise aufrufen: Sie können die Systemprüfungen, die Datasetprüfungen oder beide Prüfungen durchführen. So führen Sie sowohl die System- als auch die Datasetprüfungen durch: 

```powershell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

So prüfen Sie nur Ihr Dataset:
```powershell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
So testen Sie nur die Systemanforderungen:
```powershell
    Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
So zeigen Sie die Ergebnisse in einer CSV-Datei an:
```powershell
    $errors = Invoke-AzStorageSyncCompatibilityCheck […]
    $errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="system-requirements"></a>Systemanforderungen
- Ein Server, auf dem eine der folgenden Betriebssystemversionen ausgeführt wird:

    | Version | Unterstützte SKUs | Unterstützte Bereitstellungsoptionen |
    |---------|----------------|------------------------------|
    | Windows Server 2019 | Datacenter und Standard | Vollständig und Core |
    | Windows Server 2016 | Datacenter und Standard | Vollständig und Core |
    | Windows Server 2012 R2 | Datacenter und Standard | Vollständig und Core |
    | Windows Server IoT 2019 for Storage| Datacenter und Standard | Vollständig und Core |
    | Windows Storage Server 2016| Datacenter und Standard | Vollständig und Core |
    | Windows Storage Server 2012 R2| Datacenter und Standard | Vollständig und Core |

    Zukünftige Versionen von Windows Server werden hinzugefügt, sobald sie veröffentlicht werden.

    > [!Important]  
    > Es wird empfohlen, alle Servercomputer, die für die Azure-Dateisynchronisierung verwendet werden, mit den neuesten Updates von Windows Update auf dem neuesten Stand zu halten. 

- Ein Server mit mindestens 2 GiB Arbeitsspeicher

    > [!Important]  
    > Wenn der Server auf einem virtuellen Computer ausgeführt wird, für den dynamischer Arbeitsspeicher aktiviert ist, muss der virtuelle Computer mit mindestens 2.048 MiB Arbeitsspeicher konfiguriert werden.
    
- Ein lokal angeschlossenes Volume, das mit dem NTFS-Dateisystem formatiert ist

### <a name="file-system-features"></a>Features des Dateisystems

| Feature | Status der Unterstützung | Notizen |
|---------|----------------|-------|
| Zugriffssteuerungslisten (ACLs) | Vollständig unterstützt | Windows-Zugriffssteuerungslisten werden von der Azure-Dateisynchronisierung beibehalten und von Windows Server auf Serverendpunkten erzwungen. Windows-Zugriffssteuerungslisten werden (noch) nicht von Azure Files unterstützt, wenn direkt auf Dateien in der Cloud zugegriffen wird. |
| Feste Links | Übersprungen | |
| Symbolische Links | Übersprungen | |
| Bereitstellungspunkte | Teilweise unterstützt | Bereitstellungspunkte können der Stamm eines Serverendpunkts sein, aber sie werden übersprungen, wenn sie im Namespace des Serverendpunkts enthalten sind. |
| Verbindungen | Übersprungen | Beispiel: Die Ordner „DfrsrPrivate“ und „DFSRoots“ des verteilten Dateisystems. |
| Analysepunkte | Übersprungen | |
| NTFS-Komprimierung | Vollständig unterstützt | |
| Sparsedateien | Vollständig unterstützt | Sparsedateien werden synchronisiert (werden nicht blockiert), werden jedoch als vollständige Dateien in die Cloud synchronisiert. Wenn sich der Inhalt der Datei in der Cloud (oder auf einem anderen Server) ändert, handelt es sich bei der Datei nicht länger um eine Sparsedatei, sobald die Änderung heruntergeladen wird. |
| Alternative Datenströme (ADS) | Beibehalten, aber nicht synchronisiert | Beispielsweise werden Klassifizierungstags, die von der Dateiklassifizierungsinfrastruktur erstellt werden, nicht synchronisiert. Vorhandene Klassifizierungstags in Dateien auf den einzelnen Serverendpunkten bleiben hiervon unberührt. |

> [!Note]  
> Nur NTFS-Volumes werden unterstützt. ReFS, FAT, FAT32 und andere Dateisysteme werden nicht unterstützt.

### <a name="files-skipped"></a>Übersprungene Dateien

| Datei/Ordner | Hinweis |
|-|-|
| Desktop.ini | Systemspezifische Datei |
| ethumbs.db$ | Temporäre Datei für Miniaturansichten |
| ~$\*.\* | Temporäre Office-Datei |
| \*.tmp | Temporäre Datei |
| \*.laccdb | Access-DB-Sperrdatei|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Interne Synchronisierungsdatei|
| \\System Volume Information | Volumespezifischer Ordner |
| $RECYCLE.BIN| Ordner |
| \\SyncShareState | Ordner für die Synchronisierung |

### <a name="failover-clustering"></a>Failoverclustering
Windows Server-Failoverclustering wird von der Azure-Dateisynchronisierung für die Bereitstellungsoption „Dateiserver zur allgemeinen Verwendung“ unterstützt. Failoverclustering wird auf einem „Dateiserver mit horizontaler Skalierung für Anwendungsdaten“ (SOFS) oder auf freigegebenen Volumes (CSV) nicht unterstützt.

> [!Note]  
> Der Azure-Dateisynchronisierungs-Agent muss auf jedem Knoten in einem Failovercluster installiert sein, damit die Synchronisierung ordnungsgemäß funktioniert.

### <a name="data-deduplication"></a>Datendeduplizierung
**Windows Server 2016 und Windows Server 2019**   
Die Datendeduplizierung wird auf Volumes mit aktiviertem Cloudtiering unter Windows Server 2016 und Windows Server 2019 unterstützt. Durch das Aktivieren der Datendeduplizierung auf einem Volume mit aktiviertem Cloudtiering können Sie weitere Dateien lokal zwischenspeichern, ohne mehr Speicher bereitstellen zu müssen. 

Wenn die Datendeduplizierung für ein Volume mit aktiviertem Cloudtiering aktiviert ist, wird das Tiering von für die Deduplizierung optimierten Dateien innerhalb des Serverendpunkt-Speicherorts ähnlich wie bei einer normalen Datei basierend auf den Richtlinieneinstellungen für Cloudtiering ausgeführt. Nachdem das Tiering der für die Deduplizierung optimierten Dateien ausgeführt wurde, wird der Garbage Collection-Auftrag „Datendeduplizierung“ automatisch ausgeführt, um Speicherplatz freizugeben, indem unnötige Blöcke entfernt werden, die nicht mehr von anderen Dateien auf dem Volume referenziert werden.

Beachten Sie, dass die Volumeeinsparungen nur für den Server gelten. Ihre Daten in der Azure-Dateifreigabe werden nicht dedupliziert.

> [!Note]  
> Zur Unterstützung der Datendeduplizierung auf Volumes mit aktiviertem Cloudtiering unter Windows Server 2019 müssen Windows-Update [KB4520062](https://support.microsoft.com/help/4520062) und mindestens Version 9.0.0.0 des Agents für die Azure-Dateisynchronisierung installiert sein.

**Windows Server 2012 R2**  
Datendeduplizierung und Cloudtiering auf demselben Volume unter Windows Server 2012 R2 werden von der Azure-Dateisynchronisierung nicht unterstützt. Wenn die Datendeduplizierung auf einem Volume aktiviert wird, muss Cloudtiering deaktiviert werden. 

**Hinweise**
- Wenn die Datendeduplizierung vor dem Azure-Dateisynchronisierungs-Agent installiert wird, ist ein Neustart erforderlich, damit die Datendeduplizierung und das Cloudtiering auf demselben Volume unterstützt werden.
- Wenn die Datendeduplizierung nach dem Cloudtiering auf einem Volume aktiviert wird, werden durch den ersten Auftrag zur Optimierung der Deduplizierung Dateien auf dem Volume optimiert, für die noch kein Tiering erfolgt ist. Dies hat folgende Auswirkungen auf das Cloudtiering:
    - Die Richtlinie für die Freigabe von Speicherplatz bewirkt, dass Dateien entsprechend dem freien Speicherplatz auf dem Volume, der anhand des Wärmebilds ermittelt wird, weiterhin umgelagert werden.
    - Die Datumsrichtlinie bewirkt, dass das Tiering für Dateien übersprungen wird, die eigentlich infrage gekommen wären. Dies liegt daran, dass durch den Auftrag zur Optimierung der Deduplizierung auf die Dateien zugegriffen wird.
- Wenn die Datei nicht bereits umgelagert wurde, wird das Cloudtiering mit Datumsrichtlinie bei laufenden Aufträgen zur Optimierung der Deduplizierung verzögert. Dies liegt an der [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps)-Einstellung der Datendeduplizierung. 
    - Beispiel: Wenn die MinimumFileAgeDays-Einstellung 7 Tage beträgt und die Datumsrichtlinie für das Cloudtiering 30 Tage vorsieht, werden Dateien gemäß der Datumsrichtlinie nach 37 Tagen umgelagert.
    - Hinweis: Sobald eine Datei von der Azure-Dateisynchronisierung umgelagert wurde, wird sie vom Auftrag zur Optimierung der Deduplizierung übersprungen.
- Wenn ein Server unter Windows Server 2012 R2 mit installiertem Azure-Dateisynchronisierungs-Agent auf Windows Server 2016 oder Windows Server 2019 aktualisiert wird, sind die folgenden Schritte erforderlich, damit die Datendeduplizierung und das Cloudtiering auf demselben Volume unterstützt werden:  
    - Deinstallieren Sie den Azure-Dateisynchronisierungs-Agent für Windows Server 2012 R2, und starten Sie den Server neu.
    - Laden Sie den Azure-Dateisynchronisierungs-Agent für die neue Serverbetriebssystemversion (Windows Server 2016 oder Windows Server 2019) herunter.
    - Installieren Sie den Azure-Dateisynchronisierungs-Agent, und starten Sie den Server neu.  
    
    Hinweis: Bei der Deinstallation und Neuinstallation des Agents werden die Konfigurationseinstellungen der Azure-Dateisynchronisierung auf dem Server beibehalten.

### <a name="distributed-file-system-dfs"></a>Verteiltes Dateisystem (Distributed File System, DFS)
Die Azure-Dateisynchronisierung unterstützt die Interoperabilität mit DFS-Namespaces (DFS-N) und DFS-Replikation (DFS-R).

**DFS-Namespaces (DFS-N)** : Die Azure-Dateisynchronisierung wird auf DFS-N-Servern vollständig unterstützt. Sie können den Azure-Dateisynchronisierungs-Agent auf einem oder mehreren DFS-N-Membern installieren, um Daten zwischen den Serverendpunkten und dem Cloudendpunkt zu synchronisieren. Weitere Informationen finden Sie unter [Übersicht über DFS-Namespaces](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**DFS-Replikation (DFS-R)** : Da DFS-R und die Azure-Dateisynchronisierung beides Replikationslösungen sind, empfehlen wir in den meisten Fällen das Ersetzen von DFS-R durch die Azure-Dateisynchronisierung. Es gibt aber mehrere Szenarien, in denen die parallele Nutzung von DFS-R und Azure-Dateisynchronisierung sinnvoll sein kann:

- Sie migrieren von einer DFS-R-Bereitstellung zu einer Azure-Dateisynchronisierungsbereitstellung. Weitere Informationen finden Sie unter [Migrieren einer DFS-R-Bereitstellung (DFS-Replikation) zur Azure-Dateisynchronisierung](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Nicht jeder lokale Server, für den eine Kopie Ihrer Dateidaten erforderlich ist, kann direkt mit dem Internet verbunden werden.
- Bei Teilstrukturservern werden Daten auf einem einzelnen Hubserver zusammengefasst, für den Sie die Azure-Dateisynchronisierung verwenden möchten.

Für die parallele Nutzung von Azure-Dateisynchronisierung und DFS-R gilt Folgendes:

1. Das Azure-Dateisynchronisierungs-Cloudtiering muss auf Volumes mit replizierten DFS-R-Ordnern deaktiviert sein.
2. Serverendpunkte sollten nicht in schreibgeschützten DFS-R-Replikationsordnern konfiguriert werden.

Weitere Informationen finden Sie unter [Übersicht über DFS-Namespaces und DFS-Replikation](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Sysprep
Die Verwendung von Sysprep auf einem Server, für den der Azure-Dateisynchronisierungs-Agent installiert ist, wird nicht unterstützt und kann zu unerwarteten Ergebnissen führen. Die Agent-Installation und Serverregistrierung sollte nach der Bereitstellung des Serverimages und nach Abschluss des Mini-Setups für Sysprep erfolgen.

### <a name="windows-search"></a>Windows-Suche
Wenn auf einem Serverendpunkt Cloudtiering aktiviert ist, werden Tieringdateien in der Windows-Suche übersprungen und nicht indiziert. Dateien ohne Tiering werden ordnungsgemäß indiziert.

### <a name="antivirus-solutions"></a>Virenschutzlösungen
Da für den Virenschutz Dateien auf bekannte Schadsoftware überprüft werden müssen, kann ein Virenschutzprodukt den Rückruf von Tieringdateien verursachen. Ab Version 4.0 der Azure-Dateisynchronisierung-Agents ist für mehrstufige Dateien das sichere Windows-Attribut „FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS“ festgelegt. Es empfiehlt es sich, bei Ihrem Softwareanbieter nachzufragen, wie die Lösung so konfiguriert werden kann, dass das Lesen von Dateien mit diesem festgelegten Attribut übersprungen wird (bei vielen ist dies automatisch der Fall). 

Die internen Virenschutzlösungen von Microsoft – Windows Defender und System Center Endpoint Protection (SCEP) – überspringen beide automatisch das Lesen von Dateien, für die dieses Attribut festgelegt ist. Wir haben sie getestet und ein kleineres Problem identifiziert: Wenn Sie einer bestehenden Synchronisierungsgruppe einen Server hinzufügen, werden Dateien, die kleiner als 800 Byte sind, auf dem neuen Server zurückgerufen (heruntergeladen). Diese Dateien verbleiben auf dem neuen Server und werden nicht in Speicherebenen aufgeteilt, da sie nicht die Größenanforderungen für das Tiering (> 64 KB) erfüllen.

> [!Note]  
> Anbieter von Antivirensoftware können die Kompatibilität zwischen ihrem Produkt und der Azure-Dateisynchronisierung mithilfe der [Azure File Sync Antivirus Compatibility Test Suite](https://www.microsoft.com/download/details.aspx?id=58322) überprüfen, die aus dem Microsoft Download Center heruntergeladen werden kann.

### <a name="backup-solutions"></a>Sicherungslösungen
Wie Virenschutzlösungen können auch Sicherungslösungen den Rückruf von Tieringdateien verursachen. Es wird empfohlen, die Azure-Dateifreigabe mithilfe einer Cloudsicherungslösung anstelle eines lokalen Sicherungsprodukts zu sichern.

Wenn Sie eine lokale Sicherungslösung verwenden, sollten die Sicherungen auf einem Server in der Synchronisierungsgruppe ausgeführt werden, auf dem das Cloudtiering deaktiviert ist. Wenn Sie eine Wiederherstellung durchführen, verwenden Sie die Wiederherstellungsoptionen auf Volume- oder Dateiebene. Mithilfe der Wiederherstellungsoption auf Dateiebene wiederhergestellte Dateien werden auf allen Endpunkten in der Synchronisierungsgruppe synchronisiert. Dabei werden vorhandene Dateien durch die aus der Sicherung wiederhergestellte Version ersetzt.  Bei der Wiederherstellung auf Volumeebene werden die neueren Dateiversionen in der Azure-Dateifreigabe oder auf anderen Serverendpunkten nicht ersetzt.

> [!Note]  
> Bare-Metal-Recovery (BMR) kann zu unerwarteten Ergebnissen führen und wird derzeit nicht unterstützt.

> [!Note]  
> Bei Version 9 des Dateisynchronisierungs-Agents werden VSS-Momentaufnahmen (einschließlich der Registerkarte "Vorherige Versionen") jetzt auf Volumes mit aktiviertem Cloudtiering unterstützt. Allerdings müssen Sie die vorherige Versionskompatibilität über PowerShell aktivieren. [Weitere Informationen](storage-files-deployment-guide.md).

### <a name="encryption-solutions"></a>Verschlüsselungslösungen
Die Unterstützung von Verschlüsselungslösungen hängt davon ab, wie sie implementiert werden. Die Azure-Dateisynchronisierung funktioniert mit:

- BitLocker-Verschlüsselung
- Azure Information Protection, Azure Rights Management Services (Azure RMS) und Active Directory RMS

Die Azure-Dateisynchronisierung funktioniert nicht mit:

- NTFS EFS (Encrypted File System)

Im Allgemeinen sollte die Azure-Dateisynchronisierung Interoperabilität mit Verschlüsselungslösungen unterstützen, die unterhalb des Dateisystems ansetzen, wie etwa BitLocker, sowie mit Lösungen, die im Dateiformat implementiert sind, wie etwa Azure Information Protection. Es wurde keine besondere Interoperabilität für Lösungen implementiert, die oberhalb des Dateisystems ansetzen (wie etwa NTFS EFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Andere Lösungen für hierarchisches Speichermanagement (HSM)
Es sollten keine anderen HSM-Lösungen in Verbindung mit der Azure-Dateisynchronisierung verwendet werden.

## <a name="region-availability"></a>Regionale Verfügbarkeit
Die Azure-Dateisynchronisierung ist nur in den folgenden Regionen verfügbar:

| Region | Standort des Rechenzentrums |
|--------|---------------------|
| Australien (Osten) | Neusüdwales |
| Australien, Südosten | Victoria |
| Brasilien Süd | Sao Paulo, Bundesland |
| Kanada, Mitte | Toronto |
| Kanada, Osten | Quebec City |
| Indien, Mitte | Pune |
| USA (Mitte) | Iowa |
| Asien, Osten | Hongkong (SAR) |
| East US | Virginia |
| USA (Ost 2) | Virginia |
| Frankreich, Mitte | Paris |
| Frankreich, Süden* | Marseille |
| Korea, Mitte | Seoul |
| Korea, Süden | Busan |
| Japan, Osten | Tokio, Saitama |
| Japan, Westen | Osaka |
| USA Nord Mitte | Illinois |
| Nordeuropa | Irland |
| Südafrika, Norden | Johannesburg |
| Südafrika, Westen* | Kapstadt |
| USA Süd Mitte | Texas |
| Indien (Süden) | Chennai |
| Asien, Südosten | Singapur |
| UK, Süden | London |
| UK, Westen | Cardiff |
| US Gov Arizona | Arizona |
| US Gov Texas | Texas |
| US Government, Virginia | Virginia |
| Vereinigte Arabische Emirate, Norden | Dubai |
| VAE, Mitte* | Abu Dhabi |
| Europa, Westen | Niederlande |
| USA, Westen-Mitte | Wyoming |
| USA (Westen) | Kalifornien |
| USA, Westen 2 | Washington |

Die Azure-Dateisynchronisierung unterstützt nur die Synchronisierung mit einer Azure-Dateifreigabe in der gleichen Region wie der Speichersynchronisierungsdienst.

Für die mit Sternchen gekennzeichneten Regionen müssen Sie sich an den Azure-Support wenden, um Zugriff auf Azure Storage in diesen Regionen anzufordern. Der Prozess ist in [diesem Dokument](https://azure.microsoft.com/global-infrastructure/geographies/) beschrieben.

### <a name="azure-disaster-recovery"></a>Azure-Notfallwiederherstellung
Um vor dem Verlust einer Azure-Region zu schützen, integriert die Azure-Dateisynchronisierung die Option für [Redundanz durch georedundante Speicher](../common/storage-redundancy-grs.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (GRS). Der GRS funktioniert durch die Verwendung von asynchroner Blockreplikation zwischen Speichern in der primären Region, mit der Sie normalerweise interagieren, und Speichern in der gekoppelten sekundären Region. Bei einem Notfall, infolgedessen eine Azure-Region vorübergehend oder dauerhaft offline geschaltet wird, führt Microsoft für den Speicher ein Failover auf die gekoppelte Region durch. 

> [!Warning]  
> Wenn Sie Ihre Azure-Dateifreigabe als Cloudendpunkt in einem GRS-Speicherkonto verwenden, sollten Sie kein Failover des Speicherkontos einleiten. Dies würde das Funktionieren der Synchronisierung beenden und könnte außerdem bei neu einbezogenen Dateien zu unerwartetem Datenverlust führen. Im Fall des Ausfalls einer Azure-Region löst Microsoft das Failover des Speicherkontos auf eine Weise aus, die mit der Azure-Dateisynchronisierung kompatibel ist.

Um die Failoverintegration zwischen georedundantem Speicher und der Azure-Dateisynchronisierung zu unterstützen, werden alle Azure-Dateisynchronisierungsregionen mit einer sekundären Region gekoppelt, die der vom Speicher verwendeten sekundären Region entspricht. Bei diesen Paaren handelt es sich um Folgende:

| Primäre Region      | Regionspaar      |
|---------------------|--------------------|
| Australien (Osten)      | Australien, Südosten|
| Australien, Südosten | Australien (Osten)     |
| Brasilien Süd        | USA Süd Mitte   |
| Kanada, Mitte      | Kanada, Osten        |
| Kanada, Osten         | Kanada, Mitte     |
| Indien, Mitte       | Indien (Süden)        |
| USA (Mitte)          | USA (Ost) 2          |
| Asien, Osten           | Asien, Südosten     |
| East US             | USA (Westen)            |
| USA (Ost) 2           | USA (Mitte)         |
| Frankreich, Mitte      | Frankreich, Süden       |
| Frankreich, Süden        | Frankreich, Mitte     |
| Japan, Osten          | Japan, Westen         |
| Japan, Westen          | Japan, Osten         |
| Korea, Mitte       | Korea, Süden        |
| Korea, Süden         | Korea, Mitte      |
| Nordeuropa        | Europa, Westen        |
| USA Nord Mitte    | USA Süd Mitte   |
| Südafrika, Norden  | Südafrika, Westen  |
| Südafrika, Westen   | Südafrika, Norden |
| USA Süd Mitte    | USA Nord Mitte   |
| Indien (Süden)         | Indien, Mitte      |
| Asien, Südosten      | Asien, Osten          |
| UK, Süden            | UK, Westen            |
| UK, Westen             | UK, Süden           |
| US Gov Arizona      | US Gov Texas       |
| US Gov Iowa         | US Government, Virginia    |
| US Government, Virginia      | US Gov Texas       |
| Europa, Westen         | Nordeuropa       |
| USA, Westen-Mitte     | USA, Westen 2          |
| USA (Westen)             | East US            |
| USA, Westen 2           | USA, Westen-Mitte    |

## <a name="azure-file-sync-agent-update-policy"></a>Updaterichtlinie für den Azure-Dateisynchronisierungs-Agent
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Nächste Schritte
* [Berücksichtigen von Firewall- und Proxyeinstellungen](storage-sync-files-firewall-and-proxy.md)
* [Planung für eine Azure Files-Bereitstellung](storage-files-planning.md)
* [Bereitstellen von Azure Files](storage-files-deployment-guide.md)
* [Bereitstellen der Azure-Dateisynchronisierung](storage-sync-files-deployment-guide.md)
* [Überwachen der Azure-Dateisynchronisierung](storage-sync-files-monitoring.md)
