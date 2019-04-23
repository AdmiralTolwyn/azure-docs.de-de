---
title: Häufig gestellte Fragen zum Azure Backup-Agent
description: Antworten auf häufig gestellte Fragen zur Funktionsweise des Azure Backup-Agents sowie zu den Grenzwerten für Sicherung und Aufbewahrung.
services: backup
author: trinadhk
manager: shreeshd
keywords: Sicherung und Notfallwiederherstellung; Backup-Dienst
ms.service: backup
ms.topic: conceptual
ms.date: 8/6/2018
ms.author: trinadhk
ms.openlocfilehash: c1690fe6d0ce24bd319b042a3850bbfe487ffcfc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59426255"
---
# <a name="questions-about-the-azure-backup-agent"></a>Fragen zum Azure Backup-Agent
Dieser Artikel enthält Antworten auf häufig gestellte Fragen, damit Sie sich schnell mit den Komponenten des Azure Backup-Agents vertraut machen können. Einige Antworten enthalten Links zu Artikeln mit umfassenderen Informationen. Außerdem können Sie Fragen zum Azure Backup-Dienst im [Diskussionsforum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)stellen.

## <a name="configure-backup"></a>Konfigurieren der Sicherung
### <a name="where-can-i-download-the-latest-azure-backup-agent-br"></a>Wo kann ich den neuesten Azure Backup-Agent herunterladen? <br/>
Sie können den aktuellen Agent zum Sichern von Windows Server, System Center DPM oder des Windows-Clients [hier](https://aka.ms/azurebackup_agent)herunterladen. Verwenden Sie zum Sichern eines virtuellen Computers den VM-Agent (installiert automatisch die richtige Erweiterung). Der VM-Agent ist auf virtuellen Computern, die über den Azure-Katalog erstellt werden, bereits vorhanden.

### <a name="when-configuring-the-azure-backup-agent-i-am-prompted-to-enter-the-vault-credentials-do-vault-credentials-expire"></a>Beim Konfigurieren des Azure Backup-Agents werde ich aufgefordert, die Tresoranmeldedaten einzugeben. Laufen die Tresoranmeldeinformationen ab?
Ja. Die Tresoranmeldeinformationen laufen nach 48 Stunden ab. Wenn die Datei abläuft, melden Sie sich am Azure-Portal an, und laden Sie die Dateien mit den Tresoranmeldedaten aus Ihrem Tresor herunter.

### <a name="what-types-of-drives-can-i-back-up-files-and-folders-from-br"></a>Von welchen Laufwerkstypen kann ich Dateien und Ordner sichern? <br/>
Folgende Laufwerke/Volumes können nicht gesichert werden:

* Wechselmedien: Alle Quellen für Sicherungselemente müssen als festes Medium gemeldet werden.
* Schreibgeschützte Volumes: Das Volume muss beschreibbar sein, damit der Volumeschattenkopie-Dienst (VSS) funktioniert.
* Offlinevolumes: Das Volume muss online sein, damit der VSS funktioniert.
* Netzwerkfreigabe: Das Volume muss sich lokal auf dem Server befinden, damit es mit der Onlinesicherung gesichert werden kann.
* Mit BitLocker geschützte Volumes: Das Volume muss entsperrt werden, damit die Sicherung erfolgen kann.
* Dateisystemidentifikation: NTFS ist das einzige unterstützte Dateisystem.

### <a name="what-file-and-folder-types-can-i-back-up-from-my-serverbr"></a>Welche Datei- und Ordnertypen können von meinem Server gesichert werden?<br/>
Die folgenden Typen werden unterstützt:

* Verschlüsselt
* Komprimiert
* Platzsparend
* Komprimiert und geringe Dichte
* Feste Links: Nicht unterstützt, wird übersprungen
* Analysepunkt: Nicht unterstützt, wird übersprungen
* Verschlüsselt und geringe Dichte: Nicht unterstützt, wird übersprungen
* Komprimierter Datenstrom: Nicht unterstützt, wird übersprungen
* Platzsparender Datenstrom: Nicht unterstützt, wird übersprungen

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-already-backed-by-the-azure-backup-service-using-the-vm-extension-br"></a>Kann ich den Azure Backup-Agent auf einer Azure-VM installieren, die mithilfe der VM-Erweiterung bereits vom Azure Backup-Dienst gesichert wurde? <br/>
Absolut. Azure Backup ermöglicht die Sicherung auf VM-Ebene für Azure VMs mit der VM-Erweiterung. Installieren Sie den Azure Backup-Agent unter dem Windows-Gastbetriebssystem, um Dateien und Ordner unter dem Gastbetriebssystem zu schützen.

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-to-back-up-files-and-folders-present-on-temporary-storage-provided-by-the-azure-vm-br"></a>Kann ich den Azure Backup-Agent auf einer Azure-VM installieren, um Dateien und Ordner in temporärem Speicher zu schützen, der von der Azure-VM bereitgestellt wird? <br/>
Ja. Installieren Sie den Azure Backup-Agent auf dem Windows-Gastbetriebssystem, und sichern Sie Dateien und Ordner im temporären Speicher. Sicherungen können nicht erfolgreich durchgeführt werden, wenn Daten aus dem temporären Speicher entfernt werden. Wenn die temporären Speicherdaten gelöscht wurden, ist außerdem nur die Wiederherstellung in einem nicht volatilen Speicher möglich.

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder-br"></a>Welche Mindestgröße gilt für den Cacheordner? <br/>
Die Größe des Cacheordners bestimmt die Menge der Daten, die Sie sichern. Das Volume Ihres Cacheordners sollte im Vergleich zum Gesamtumfang der Sicherungsdaten mindestens 5-10 % freien Speicherplatz aufweisen. Wenn weniger als 5-10 % Speicherplatz zur Verfügung stehen, vergrößern Sie das Volume, oder [verschieben Sie den Cacheordner auf ein Volume mit ausreichend freiem Speicherplatz](backup-azure-file-folder-backup-faq.md#backup).

### <a name="how-do-i-register-my-server-to-another-datacenterbr"></a>Wie registriere ich meinen Server bei einem anderen Datencenter?<br/>
Die Sicherungsdaten werden an das Datencenter des Tresors gesendet, bei dem dieser registriert ist. Die einfachste Methode zum Ändern des Datencenters besteht darin, den Agent zu deinstallieren und neu zu installieren und den Server bei einem neuen Tresor zu registrieren, der zum gewünschten Datencenter gehört.

### <a name="does-the-azure-backup-agent-work-on-a-server-that-uses-windows-server-2012-deduplication-br"></a>Funktioniert der Azure Backup-Agent auf einem Server, der die Deduplizierung von Windows Server 2012 verwendet? <br/>
Ja. Der Agent-Dienst konvertiert die deduplizierten Daten bei der Vorbereitung des Sicherungsvorgangs in normale Daten. Anschließend optimiert er die Daten für die Sicherung, verschlüsselt sie und sendet die verschlüsselten Daten an den Onlinesicherungsdienst.

## <a name="prerequisites-and-dependencies"></a>Erforderliche Komponenten und Abhängigkeiten
### <a name="what-features-of-microsoft-azure-recovery-services-mars-agent-require-net-framework-452-and-higher"></a>Für welche Features des MARS-Agents (Microsoft Azure Recovery Services) ist .NET Framework 4.5.2 oder höher erforderlich?
Für das Feature [Sofortige Wiederherstellung](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine), mit dem einzelne Dateien und Ordner über den Assistenten *Daten wiederherstellen* wiederhergestellt werden können, ist .NET Framework 4.5.2 oder höher erforderlich.

## <a name="backup"></a>Backup
### <a name="how-do-i-change-the-cache-location-specified-for-the-azure-backup-agentbr"></a>Wie kann ich den Cachespeicherort für den Azure Backup-Agent ändern?<br/>
Verwenden Sie die folgende Liste, um den Cachespeicherort zu ändern.

1. Beenden Sie die Backup-Engine, indem Sie den folgenden Befehl an einer Eingabeaufforderung mit erhöhten Rechten ausführen:

    ```PS C:\> Net stop obengine```

2. Verschieben Sie die Dateien nicht. Kopieren Sie den Cacheordner stattdessen auf ein anderes Laufwerk mit ausreichend Speicherplatz. Der ursprüngliche Cachespeicher kann entfernt werden, nachdem bestätigt wurde, dass die Sicherungen mit dem neuen Cachespeicher funktionieren.
3. Aktualisieren Sie die folgenden Registrierungseinträge mit dem Pfad zum neuen Cacheordner.<br/>

    | Registrierungspfad | Registrierungsschlüssel | Wert |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Neuer Speicherort des Cacheordners* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Neuer Speicherort des Cacheordners* |

4. Starten Sie die Backup-Engine neu, indem Sie den folgenden Befehl an einer Eingabeaufforderung mit erhöhten Rechten ausführen:

    ```PS C:\> Net start obengine```

Nachdem die Erstellung der Sicherung am neuen Cachespeicherort erfolgreich abgeschlossen wurde, können Sie den ursprünglichen Cacheordner entfernen.


### <a name="where-can-i-put-the-cache-folder-for-the-azure-backup-agent-to-work-as-expectedbr"></a>Wo kann ich den Cacheordner platzieren, damit der Azure Backup-Agent erwartungsgemäß funktioniert?<br/>
Folgende Speicherorte werden für den Cacheordner nicht empfohlen:

* Netzwerkfreigabe oder Wechselmedium: Bei dem Cacheordner muss es sich um einen lokalen Ordner des Servers handeln, der mittels Onlinesicherung gesichert werden soll. Netzwerkspeicherorte oder Wechselmedien wie USB-Laufwerke werden nicht unterstützt
* Offlinevolumes: Der Cacheordner muss für die erwartete Sicherung über den Azure Backup-Agent online sein.

### <a name="are-there-any-attributes-of-the-cache-folder-that-are-not-supportedbr"></a>Gibt es Cacheordnerattribute, die nicht unterstützt werden?<br/>
Die folgenden Attribute oder Kombinationen dieser Attribute werden für den Cacheordner nicht unterstützt:

* Verschlüsselt
* Dedupliziert
* Komprimiert
* Platzsparend
* Analysepunkt

Der Cacheordner und die Metadaten-VHD verfügen nicht über die erforderlichen Attribute für den Azure Backup-Agent.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-by-the-backup-servicebr"></a>Gibt es eine Möglichkeit, die vom Backup-Dienst genutzte Bandbreite anzupassen?<br/>
  Ja. Verwenden Sie die Option **Eigenschaften ändern** im Backup-Agent, um die Bandbreite anzupassen. Sie können die Bandbreitenmenge und die Zeiten anpassen, zu denen Sie die Bandbreite nutzen. Eine Schritt-für-Schritt-Anleitung finden Sie unter **[Aktivieren der Netzwerkdrosselung](backup-configure-vault.md#enable-network-throttling)**.

## <a name="restore"></a>Restore

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Was geschieht, wenn ich einen laufenden Wiederherstellungsauftrag abbreche?
Wenn ein laufender Wiederherstellungsauftrag abgebrochen wird, stoppt der Wiederherstellungsvorgang, und alle vor dem Abbruch wiederhergestellten Dateien verbleiben ohne Rollbacks im konfigurierten Ziel (ursprünglicher oder alternativer Speicherort).


## <a name="manage-backups"></a>Verwalten von Sicherungen

### <a name="what-happens-if-i-rename-a-windows-server-that-is-backing-up-data-to-azurebr"></a>Was geschieht, wenn ich einen Windows-Server umbenenne, der Daten in Azure sichert?<br/>
Wenn Sie einen Server umbenennen, werden alle derzeit konfigurierten Sicherungen angehalten. Registrieren Sie den neuen Namen des Servers beim Backup-Tresor. Wenn Sie den neuen Namen beim Tresor registrieren, dann ist der erste Sicherungsvorgang ein *vollständiges* Backup. Falls Sie Daten wiederherstellen müssen, die unter dem alten Servernamen im Tresor gesichert wurden, können Sie im Assistenten **Daten wiederherstellen** die Option [**Anderer Server**](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) verwenden.

### <a name="what-is-the-maximum-file-path-length-that-can-be-specified-in-backup-policy-using-azure-backup-agent-br"></a>Wie lautet die maximale Dateipfadlänge, die in der Sicherungsrichtlinie mit dem Azure Backup-Agent angegeben werden kann? <br/>
Der Azure Backup-Agent basiert auf NTFS. Die [Angabe der Dateipfadlänge ist durch die Windows-API beschränkt](/windows/desktop/FileIO/naming-a-file#fully_qualified_vs._relative_paths). Falls der Dateipfad der zu schützenden Dateien die für die Windows-API zulässige Länge überschreitet, können Sie den übergeordneten Ordner oder das Laufwerk sichern.  

### <a name="what-characters-are-allowed-in-file-path-of-azure-backup-policy-using-azure-backup-agent-br"></a>Welche Zeichen sind im Dateipfad der Azure Backup-Richtlinie mit dem Azure Backup-Agent zulässig? <br>
 Der Azure Backup-Agent basiert auf NTFS. Für die Angabe der Datei können [von NTFS unterstützte Zeichen](/windows/desktop/FileIO/naming-a-file#naming_conventions) verwendet werden.

### <a name="i-receive-the-warning-azure-backups-have-not-been-configured-for-this-server-even-though-i-configured-a-backup-policy-br"></a>Ich erhalte eine Warnung mit dem Hinweis, dass für den Server noch keine Azure-Sicherungen konfiguriert wurden, obwohl ich eine Sicherungsrichtlinie konfiguriert habe. <br/>
Diese Warnung tritt auf, wenn die auf dem lokalen Server gespeicherten Sicherungszeitplaneinstellungen nicht den Einstellungen im Sicherungstresor entsprechen. Wenn für den Server oder die Einstellungen ein als funktionierend bekannter Zustand wiederhergestellt wurde, ist unter Umständen die Synchronisierung der Sicherungszeitpläne verloren gegangen. Wenn Sie diese Warnung erhalten, sollten Sie die [Sicherungsrichtlinie neu konfigurieren](backup-azure-manage-windows-server.md) und dann **Sicherung jetzt ausführen** verwenden, um den lokalen Server wieder mit Azure zu synchronisieren.
