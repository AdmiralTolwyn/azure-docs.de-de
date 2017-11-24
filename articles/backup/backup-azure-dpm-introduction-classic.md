---
title: Sichern von DPM-Workloads im klassischen Azure-Portal | Microsoft-Dokumentation
description: "Eine Einführung in die Sicherung von DPM-Servern mithilfe des Azure Backup-Diensts"
services: backup
documentationcenter: 
author: Nkolli1
manager: shreeshd
editor: 
keywords: System Center Data Protection Manager, Data Protection Manager, DPM-Sicherung
ms.assetid: 8f23972b-d167-4231-b331-e198db3b18b4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: nkolli;giridham;markgal
ms.openlocfilehash: 7f13739c2d3f7fba700b649f27e02913481ca5e5
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2017
---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Vorbereiten der Sicherung von Workloads in Azure mit DPM
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
> * [Azure Backup Server (klassisch)](backup-azure-microsoft-azure-backup-classic.md)
> * [SCDPM (klassisch)](backup-azure-dpm-introduction-classic.md)
>
>

Dieser Artikel enthält eine Einführung zur Verwendung von Microsoft Azure Backup zum Schutz von DPM-Servern  und -Workloads (System Center Data Protection Manager). In diesem Artikel lernen Sie Folgendes:

* Funktionsweise der Azure DPM-Serversicherung
* Voraussetzungen für eine reibungslose Sicherung
* Typische Fehler und ihre Behandlung
* Unterstützte Szenarien

System Center DPM sichert Datei- und Anwendungsdaten. Daten, die in DPM gesichert werden, können auf Band, auf Festplatte gespeichert oder in Azure mit Microsoft Azure Backup gesichert werden. DPM interagiert mit Azure Backup wie folgt:

* **DPM als physischer Server oder auf einem lokalen virtuellen Computer bereitgestellt** – Wenn DPM als physischer Server oder als lokaler Hyper-V-Computer bereitgestellt wird, können Sie Daten im Azure Backup-Tresor zusätzlich zur Festplatten- und Bandsicherung sichern.
* **DPM als virtueller Azure-Computer bereitgestellt** – Ab System Center 2012 R2 mit Update 3 kann DPM als virtueller Azure-Computer bereitgestellt werden. Wenn DPM als virtueller Azure-Computer bereitgestellt wird, können Sie Daten auf Azure-Festplatten sichern, die an den virtuellen Azure DPM-Computer angeschlossen sind, oder Sie können die Datenspeicherung durch Sichern in einem Azure Backup-Tresor auslagern.

## <a name="why-backup-your-dpm-servers"></a>Warum sollten Sie DPM-Server sichern?
Vorteile der Verwendung von Azure Backup für die Sicherung von DPM-Servern:

* Für die lokale DPM-Bereitstellung können Sie Azure Backup als Alternative zur langfristigen Bereitstellung auf Band verwenden.
* Bei DPM-Bereitstellungen in Azure ermöglicht Azure Backup das Auslagern des Speichers vom Azure-Datenträger, sodass Sie vertikal skalieren können, indem Sie ältere Daten in Azure Backup und neue Daten auf dem Datenträger speichern.

## <a name="how-does-dpm-server-backup-work"></a>Funktionsweise der DPM-Serversicherung
Um einen virtuellen Computer zu sichern, wird zuerst eine Zeitpunkt-Momentaufnahme der Daten benötigt. Der Azure Backup-Dienst initiiert den Sicherungsauftrag zur geplanten Zeit und löst die Sicherungserweiterung zum Erstellen einer Momentaufnahme aus. Die Sicherungserweiterung wird zum Zweck der Konsistenz mit dem VSS-Gastdienst abgestimmt und ruft die Blob-Momentaufnahme-API des Azure Storage-Diensts auf, wenn Konsistenz erzielt wurde. Dadurch kann eine konsistente Momentaufnahme der Datenträger des virtuellen Computers erstellt werden, ohne dass dieser heruntergefahren werden muss.

Nachdem die Momentaufnahme erstellt wurde, werden die Daten vom Azure Backup-Dienst in den Sicherungstresor übertragen. Der Dienst identifiziert und überträgt nur die Datenblöcke, die sich seit der letzten Sicherung geändert haben, sodass die Sicherungen effizient gespeichert und das Netzwerk effizient genutzt werden. Wenn die Datenübertragung abgeschlossen ist, wird die Momentaufnahme entfernt und ein Wiederherstellungspunkt erstellt. Dieser Wiederherstellungspunkt kann im klassischen Azure-Portal angezeigt werden.

> [!NOTE]
> Für virtuelle Linux-Computer können nur dateikonsistente Sicherungen durchgeführt werden.
>
>

## <a name="prerequisites"></a>Voraussetzungen
Bereiten Sie Azure Backup wie folgt zum Sichern von DPM-Daten vor:

1. **Erstellen eines Sicherungstresors**. Wenn Sie in Ihrem Abonnement keinen Sicherungstresor erstellt haben, sehen Sie die Azure-Portal-Version dieses Artikels ein: [Vorbereiten der Sicherung von Workloads in Azure mit DPM](backup-azure-dpm-introduction.md).

  > [!IMPORTANT]
  > Ab März 2017 können im klassischen Portal keine Sicherungstresore mehr erstellt werden.
  > Sie können nun ein Upgrade für Ihre Sicherungstresore auf Recovery Services-Tresore durchführen. Detaillierte Informationen finden Sie im Artikel [Durchführen eines Upgrades für einen Sicherungstresor auf einen Recovery Services-Tresor](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft empfiehlt, ein Upgrade für Ihre Sicherungstresore auf Recovery Services-Tresore durchzuführen.<br/> Nach dem 30. November 2017 können mit PowerShell keine Sicherungstresore mehr erstellt werden. **Ab dem 30. November 2017 gilt Folgendes:**
  >- Für alle verbleibenden Sicherungstresore wird automatisch ein Upgrade auf Recovery Services-Tresore durchgeführt.
  >- Der Zugriff auf Ihre Sicherungsdaten im klassischen Portal wird nicht möglich sein. Verwenden Sie stattdessen das Azure-Portal, um auf Ihre Sicherungsdaten in Recovery Services-Tresoren zuzugreifen.
  >

2. **Herunterladen von Tresoranmeldedaten** – Laden Sie in Azure Backup das Verwaltungszertifikat hoch, das Sie für den Tresor erstellt haben.
3. **Installieren des Azure Backup-Agents und Registrieren des Servers** – Installieren Sie von Azure Backup aus, den Agent auf jedem DPM-Server, und registrieren Sie den DPM-Server im Sicherungstresor.

[!INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[!INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## <a name="requirements-and-limitations"></a>Anforderungen (und Einschränkungen)
* DPM kann als physischer Server oder als virtueller Hyper-V-Computer, der auf System Center 2012 SP1 oder System Center 2012 R2 installiert ist, ausgeführt werden. DPM kann auch als virtueller Azure-Computer unter System Center 2012 R2 mit mindestens DPM 2012 R2 Update Rollup 3 oder auf einem virtuellen Windows-Computer in VMWare in System Center 2012 R2 mit mindestens Update Rollup 5 ausgeführt werden.
* Wenn Sie DPM mit System Center 2012 SP1 ausführen, müssen Sie Update Rollup 2 für System Center Data Protection Manager SP1 installieren. Dies ist erforderlich, bevor Sie den Azure Backup-Agent installieren können.
* Auf dem DPM-Server muss Azure PowerShell und .Net Framework 4.5 installiert sein.
* DPM kann die meisten Workloads in Azure Backup sichern. Eine vollständige Liste der Unterstützungen finden Sie in den Azure Backup-Elementen weiter unten.
* Mit der Option "Kopieren auf Band" können Daten, die in Azure Backup gespeichert sind, nicht wiederhergestellt werden.
* Sie benötigen ein Azure-Konto mit aktivierter Azure Backup-Funktion. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Erfahren Sie mehr über [Preisgestaltung von Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
* Für das Verwenden von Azure Backup muss der Azure Backup-Agent auf den Servern installiert werden, die Sie sichern möchten. Jeder Server muss mindestens zehn Prozent der zu sichernden Datengröße als freien lokalen Speicher bereitstellen. Beispielsweise erfordert das Sichern von 100 GB an Daten mindestens 10 GB freien Speicherplatz im Scratchverzeichnis. Obwohl der Mindestbetrag 10 % ist, werden 15 % freier lokaler Speicherplatz für den Cachespeicherort empfohlen.
* Daten werden im Azure-Tresorspeicher gespeichert. Es gibt keine Beschränkung der Datenmenge, die Sie in einem Azure Backup-Tresor sichern, aber die Größe einer Datenquelle (beispielsweise ein virtueller Computer oder eine Datenbank) darf 54.400 GB nicht überschreiten.

Diese Dateitypen werden für die Sicherung in Azure unterstützt:

* Verschlüsselt (nur vollständige Sicherungen)
* Komprimiert (inkrementelle Sicherungen werden unterstützt)
* Platzsparend (inkrementelle Sicherungen werden unterstützt)
* Komprimiert und platzsparend (als platzsparend behandelt)

Diese werden nicht unterstützt:

* Server auf Dateisystemen, die nach Groß-/Kleinschreibung unterscheiden, werden nicht unterstützt.
* Feste Links (übersprungen)
* Analysepunkte (übersprungen)
* Verschlüsselt und komprimiert (übersprungen)
* Verschlüsselt und platzsparend (übersprungen)
* Komprimierter Stream
* Platzsparender Stream

> [!NOTE]
> Ab System Center 2012 DPM mit SP1 können Sie Workloads, die von DPM geschützt werden, in Azure mithilfe von Microsoft Azure Backup sichern.
>
>
