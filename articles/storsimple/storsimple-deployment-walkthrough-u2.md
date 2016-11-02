<properties 
   pageTitle="Bereitstellen von StorSimple-Geräten (Update 2) | Microsoft Azure"
   description="Beschreibt die Schritte und bewährten Methoden für die Bereitstellung des StorSimple Update 2-Geräts und -Diensts."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/11/2016"
   ms.author="alkohli" />


# <a name="deploy-your-on-premises-storsimple-device-(update-2)"></a>Bereitstellen lokaler StorSimple-Geräte (Update 2)

> [AZURE.SELECTOR]
- [Update 2](../articles/storsimple/storsimple-deployment-walkthrough-u2.md)
- [Update 1](../articles/storsimple/storsimple-deployment-walkthrough-u1.md)
- [GA-Version](../articles/storsimple/storsimple-deployment-walkthrough.md)

## <a name="overview"></a>Übersicht

Willkommen bei der exemplarischen Vorgehensweise für die Bereitstellung von Microsoft Azure StorSimple-Geräten. Diese Bereitstellungstutorials beziehen sich auf Update 2 der StorSimple 8000-Serie. Diese Reihe von Tutorials enthält eine Konfigurationsprüfliste, Konfigurationsvoraussetzungen und detaillierte Konfigurationsschritte für Ihre StorSimple-Geräte.

Bei den Informationen in diesen Tutorials wird davon ausgegangen, dass Sie die Sicherheitsvorkehrungen geprüft und Ihr StorSimple-Gerät ausgepackt, installiert und verkabelt haben. Wenn Sie diese Aufgaben noch ausführen müssen, beginnen Sie mit der Prüfung der [Sicherheitsvorkehrungen](storsimple-safety.md). Befolgen Sie die gerätespezifischen-Anweisungen zum Auspacken, Einbauen und Verkabeln des Geräts.

- [Auspacken, Einsetzen und Verkabeln – 8100](storsimple-8100-hardware-installation.md)
- [Auspacken, Einsetzen und Verkabeln – 8600](storsimple-8600-hardware-installation.md)

Sie benötigen Administratorrechte, um die Einrichtung und Konfiguration abzuschließen. Es wird empfohlen, dass Sie die Konfigurationsprüfliste überprüfen, bevor Sie beginnen. Die Bereitstellung und Konfiguration kann einige Zeit in Anspruch nehmen.

> [AZURE.NOTE] Die auf der Microsoft Azure-Website veröffentlichten StorSimple-Bereitstellungsinformationen gelten nur für Geräte der StorSimple 8000-Reihe. Vollständige Informationen zu Geräten der Serie 7000 finden Sie unter: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Bereitstellungsinformationen zur Serie 7000 finden Sie in der [Schnellstartanleitung zum StorSimple-System](http://onlinehelp.storsimple.com/111_Appliance/)(in englischer Sprache).

## <a name="deployment-steps"></a>Bereitstellungsschritte

Führen Sie die folgenden erforderlichen Schritte zum Konfigurieren Ihres StorSimple-Geräts und zum Verbinden des Geräts mit dem StorSimple Manager-Dienst aus. Zusätzlich zu diesen erforderlichen Schritten müssen Sie während der Bereitstellung möglicherweise optionale Schritte ausführen. Die schrittweise Bereitstellungsanleitung gibt auch an, wann Sie die folgenden optionalen Schritte jeweils ausführen sollten.


| Schritt                                                                                   | Beschreibung                                                                                                                                                   |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **VORAUSSETZUNGEN**                                                                      | Diese Schritte müssen zur Vorbereitung der Bereitstellung ausgeführt werden.                                                                                        |
| [Konfigurationsprüfliste für die Bereitstellung](#deployment-configuration-checklist)                                                     | Verwenden Sie diese Prüfliste, um vor und während der Bereitstellung Informationen zu erfassen und aufzuzeichnen.                                                                       |
| [Voraussetzungen für die Bereitstellung](#deployment-prerequisites)                                                               | Mit diesen Schritten überprüfen Sie, ob die Umgebung für die Bereitstellung vorbereitet ist.                                                                                                     |
|                                                                                        |                                                                                                                                                               |
| **SCHRITTWEISE BEREITSTELLUNG**                                                                   | Diese Schritte sind zur Bereitstellung Ihres StorSimple-Geräts in einer Produktionsumgebung erforderlich.                                                                                      |
| [Schritt 1: Erstellen eines neuen Diensts](#step-1-create-a-new-service)                                                         | Richten Sie Cloudverwaltung und -speicher für Ihr StorSimple-Gerät ein. *Überspringen Sie diesen Schritt, wenn Sie bereits über einen Dienst für andere StorSimple-Geräte verfügen*.                |
| [Schritt 2: Abrufen des Dienstregistrierungsschlüssels](#step-2-get-the-service-registration-key)                                               | Verwenden Sie diesen Schlüssel zum Registrieren Ihres StorSimple-Geräts sowie zum Herstellen einer Verbindung mit dem Verwaltungsdienst.                                                                         |
| [Schritt 3: Konfigurieren und Registrieren des Geräts über Windows PowerShell für StorSimple](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)    | Verwenden Sie den Verwaltungsdienst, um das Gerät zum Abschluss der Einrichtung mit Ihrem Netzwerk zu verbinden und in Azure zu registrieren.                                            |
| [Schritt 4: Abschließen der minimalen Geräteinstallation](#step-4-complete-minimum-device-setupd)</br>[Optional: Aktualisieren Sie Ihr StorSimple-Gerät.](#scan-for-and-apply-updates)      | Verwenden Sie den Verwaltungsdienst, um die Gerätekonfiguration abzuschließen und es für die Bereitstellung von Speicher zu aktivieren.                                                                      |
| [Schritt 5: Erstellen eines Volumecontainers](#step-5-create-a-volume-container)                                                      | Erstellen Sie einen Container zum Bereitstellen von Volumes. Ein Volumecontainer verfügt über Einstellungen für das Speicherkonto, die Bandbreite und die Verschlüsselung für alle enthaltenen Volumes.    |
| [Schritt 6: Erstellen eines Volumes](#step-6-create-a-volume)                                                                | Stellen Sie ein oder mehrere Speichervolumes für Ihre Server auf dem StorSimple-Gerät bereit.                                                                                        |
| [Schritt 7: Bereitstellen, Initialisieren und Formatieren eines Volumes](#step-7-mount-initialize-and-format-a-volume)</br>[Optional: Konfigurieren Sie MPIO.](storsimple-configure-mpio-windows-server.md)            | Verbinden Sie Ihre Server mit dem vom Gerät bereitgestellten iSCSI-Speicher. Konfigurieren Sie optional MPIO, um sicherzustellen, dass Ihre Server Link-, Netzwerk- und Schnittstellenfehler tolerieren können.                                                                                                                                                              |
| [Schritt 8: Erstellen einer Sicherung](#step-8-take-a-backup)                                                                  | Richten Sie Ihre Sicherungsrichtlinie zum Schutz Ihrer Daten ein.                                                                                                                 |
|                                                                                        |                                                                                                                                                               |
| **WEITERE VERFAHREN**                                                                   | Möglicherweise müssen Sie beim Bereitstellen Ihrer Lösung auf diese Prozeduren zurückgreifen.                                                                                        |
| [Konfigurieren eines neuen Speicherkontos für den Dienst](#configure-a-new-storage-account-for-the-service)                                      |                                                                                                                                                               |
| [Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole](#use-putty-to-connect-to-the-device-serial-console)                                    |                                                                                                                                                               |
| [Abrufen des IQN eines Windows Server-Hosts](#get-the-iqn-of-a-windows-server-host)                                                   |                                                                                                                                                               |
| [Erstellen einer manuellen Sicherung](#create-a-manual-backup)                                                                 | 


## <a name="deployment-configuration-checklist"></a>Konfigurationsprüfliste für die Bereitstellung

Bevor Sie Ihr Gerät bereitstellen, müssen Sie Informationen zum Konfigurieren der Software auf Ihrem StorSimple-Gerät sammeln. Wenn Sie einige dieser Informationen im Voraus erfassen, beschleunigt dies die Bereitstellung des StorSimple-Geräts in Ihrer Umgebung. Laden Sie die Prüfliste herunter und verwenden Sie sie, um während der Bereitstellung des Geräts die Konfigurationsdetails zu notieren.

- [Konfigurationsprüfliste für StorSimple-Bereitstellung herunterladen (in englischer Sprache)](http://www.microsoft.com/download/details.aspx?id=49159)


## <a name="deployment-prerequisites"></a>Voraussetzungen für die Bereitstellung

In den folgenden Abschnitten werden die Konfigurationsvoraussetzungen für den StorSimple Manager-Dienst und das StorSimple-Gerät beschrieben.

### <a name="for-the-storsimple-manager-service"></a>Für den StorSimple-Manager-Dienst

Stellen Sie Folgendes sicher, bevor Sie beginnen:

- Sie besitzen ein Microsoft-Konto mit Anmeldeinformationen für den Zugriff.

- Sie besitzen ein Microsoft Azure-Speicherkonto mit Anmeldeinformationen für den Zugriff.

- Ihr Microsoft Azure-Abonnement ist für den StorSimple Manager-Dienst aktiviert. Ihr Abonnement sollten Sie über das [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)erwerben.

- Sie haben Zugriff auf Terminalemulationssoftware wie PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Für das Gerät im Datencenter

Stellen Sie vor der Gerätekonfiguration sicher, dass das Gerät vollständig ausgepackt, in einem Rack installiert und für Stromversorgung, Netzwerk- und seriellen Zugriff verkabelt wurde, wie in folgenden Themen beschrieben:

-  [Auspacken, Einsetzen und Verkabeln – 8100-Gerät](storsimple-8100-hardware-installation.md)
-  [Auspacken, Einsetzen und Verkabeln – 8600-Gerät](storsimple-8600-hardware-installation.md)


### <a name="for-the-network-in-the-datacenter"></a>Für das Netzwerk im Datencenter

Stellen Sie Folgendes sicher, bevor Sie beginnen:

- Die Ports in der Datencenter-Firewall werden geöffnet, um iSCSI- und Clouddatenverkehr zu ermöglichen, wie in [Netzwerkanforderungen für das StorSimple-Gerät](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)beschrieben.


## <a name="step-by-step-deployment"></a>SCHRITTWEISE BEREITSTELLUNG

Verwenden Sie die folgenden schrittweisen Anweisungen, um Ihr StorSimple-Gerät im Datencenter bereitzustellen.

## <a name="step-1:-create-a-new-service"></a>Schritt 1: Erstellen eines neuen Diensts

Ein StorSimple Manager-Dienst kann mehrere StorSimple-Geräte verwalten. Führen Sie die folgenden Schritte aus, um einen neuen StorSimple-Manager-Dienst zu erstellen.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [AZURE.IMPORTANT] Wenn Sie nicht die automatische Erstellung eines Speicherkontos mit Ihrem Dienst aktiviert haben, müssen Sie mindestens ein Speicherkonto erstellen, nachdem Sie einen Dienst erstellt haben. Dieses Speicherkonto wird beim Erstellen eines Volumecontainers verwendet. 
>
> * Wenn Sie nicht automatisch ein Speicherkonto erstellt haben, finden Sie unter [Konfigurieren eines neuen Speicherkontos für den Dienst](#configure-a-new-storage-account-for-the-service) ausführliche Anweisungen. 
> * Wenn Sie die automatische Erstellung eines Speicherkontos aktiviert haben, fahren Sie mit [Schritt 2: Abrufen des Dienstregistrierungsschlüssels](#step-2-get-the-service-registration-key)fort.

## <a name="step-2:-get-the-service-registration-key"></a>Schritt 2: Abrufen des Dienstregistrierungsschlüssels

Nachdem der StorSimple Manager-Dienst eingerichtet wurde und ausgeführt wird, müssen Sie den Dienstregistrierungsschlüssel abrufen. Dieser Schlüssel wird zum Registrieren Ihres StorSimple-Geräts sowie zum Herstellen einer Verbindung mit dem Dienst verwendet.

Führen Sie die folgenden Schritte im Verwaltungsportal aus.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]


## <a name="step-3:-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Schritt 3: Konfigurieren und Registrieren des Geräts über Windows PowerShell für StorSimple

Verwenden Sie Windows PowerShell für StorSimple zum Abschließen der anfänglichen Installation Ihres StorSimple-Geräts wie im folgenden Verfahren erläutert. Sie müssen eine Terminalemulationssoftware verwenden, um diesen Schritt auszuführen. Weitere Informationen finden Sie unter [Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device-u1](../../includes/storsimple-configure-and-register-device-u1.md)]

## <a name="step-4:-complete-minimum-device-setup"></a>Schritt 4: Schließen Sie die minimale Gerätekonfiguration ab.

Sie müssen für die Gerätemindestkonfiguration des StorSimple-Geräts die folgenden Aufgaben ausführen: 

- Einrichten des sekundären DNS-Servers
- Aktivieren von iSCSI an mindestens einer Netzwerkschnittstelle
- Zuweisen fester IP-Adressen an beide Controller

Führen Sie die folgenden Schritte im Verwaltungsportal aus, um das mindestens erforderliche Setup für das Gerät auszuführen:

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## <a name="step-5:-create-a-volume-container"></a>Schritt 5: Erstellen eines Volumecontainers

Ein Volumecontainer verfügt über Einstellungen für das Speicherkonto, die Bandbreite und die Verschlüsselung für alle darin enthaltenen Volumes. Sie müssen einen Volumecontainer erstellen, bevor Sie mit der Bereitstellung von Volumes auf Ihrem StorSimple-Gerät beginnen können. 

Führen Sie die folgenden Schritte im Verwaltungsportal aus, um einen Volumecontainer zu erstellen.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6:-create-a-volume"></a>Schritt 6: Erstellen eines Volumes

Nach dem Erstellen eines Volumecontainers können Sie ein Speichervolume auf dem StorSimple-Gerät für Ihre Server bereitstellen. Führen Sie die folgenden Schritte im Verwaltungsportal aus, um ein Volume zu erstellen:

> [AZURE.IMPORTANT] Mit StorSimple Manager können sowohl schlanke als auch vollständig bereitgestellte Volumes erstellt werden. Sie können jedoch keine teilweise bereitgestellten Volumes erstellen. 

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume-u2.md)]

## <a name="step-7:-mount,-initialize,-and-format-a-volume"></a>Schritt 7: Bereitstellen, Initialisieren und Formatieren eines Volumes

Die folgenden Schritte werden auf dem Windows Server-Host ausgeführt. 


> [AZURE.IMPORTANT]

> - Um die hohe Verfügbarkeit Ihrer StorSimple-Lösung sicherzustellen, empfiehlt es sich, vor der Konfiguration von iSCSI auf Ihren Hostservern optional MPIO auf diesen Hosts zu konfigurieren. Die MPIO-Konfiguration auf Hostservern sorgt dafür, dass die Server Fehler bei Links, dem Netzwerk oder einzelnen Schnittstellen tolerieren können.

> - Installations- und Konfigurationsanweisungen für MPIO und iSCSI auf einem Windows Server-Host finden Sie unter [Konfigurieren von MPIO für Ihr StorSimple-Gerät](storsimple-configure-mpio-windows-server.md). Diese enthalten auch die Schritte zum Bereitstellen, Initialisieren und Formatieren von StorSimple-Volumes.

> - Installations- und Konfigurationsanweisungen für MPIO und iSCSI auf einem Linux-Host finden Sie unter [Konfigurieren von MPIO für Ihren StorSimple-Linux-Host](storsimple-configure-mpio-on-linux.md)

Wenn Sie MPIO nicht konfigurieren, führen Sie die folgenden Schritte aus, um Ihre StorSimple-Volumes auf einem Windows Server-Host bereitzustellen, zu initialisieren und zu formatieren.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8:-take-a-backup"></a>Schritt 8: Erstellen einer Sicherung

Sicherungen stellen Zeitpunktschutz für Volumes zur Verfügung und verbessern die Wiederherstellbarkeit bei gleichzeitiger Minimierung der Wiederherstellungszeiten. Für Ihr StorSimple-Gerät können zwei Arten von Sicherungen angefertigt werden: lokale Momentaufnahmen und Cloudmomentaufnahmen. Jeder dieser Sicherungstypen kann **geplant** sein oder **manuell** erfolgen. 

Führen Sie die folgenden Schritte im Verwaltungsportal aus, um eine geplante Sicherung zu erstellen.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Sie können jederzeit eine manuelle Sicherung vornehmen. Anweisungen dazu finden Sie unter [Erstellen einer manuellen Sicherung](#create-a-manual-backup). 

## <a name="configure-a-new-storage-account-for-the-service"></a>Konfigurieren eines neuen Speicherkontos für den Dienst

Dies ist ein optionaler Schritt, den Sie nur dann ausführen müssen, wenn Sie nicht die automatische Erstellung eines Speicherkontos mit Ihrem Dienst aktiviert haben. Ein Microsoft Azure-Speicherkonto ist erforderlich, um einen StorSimple-Volumecontainer zu erstellen.

Wenn Sie ein Azure-Speicherkonto in einer anderen Region erstellen müssen, finden Sie unter [Informationen zu Azure-Speicherkonten](../storage/storage-create-storage-account.md) schrittweise Anweisungen.

Führen Sie die folgenden Schritte im Verwaltungsportal auf der Seite für den **StorSimple-Manager-Dienst** aus.

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole

Zum Herstellen einer Verbindung mit Windows PowerShell für StorSimple benötigen Sie eine Terminalemulationssoftware wie etwa PuTTY. Sie können PuTTY verwenden, wenn Sie über die serielle Konsole direkt auf das Gerät zugreifen oder eine Telnet-Sitzung von einem Remotecomputer aus öffnen.

[AZURE.INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]


## <a name="scan-for-and-apply-updates"></a>Suchen nach und Anwenden von Updates

Die Aktualisierung eines Geräts kann mehrere Stunden dauern. Führen Sie die folgenden Schritte aus, um nach Updates für Ihr Gerät zu suchen und diese anzuwenden.
<!--can take 1-4 hours--> 

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### <a name="to-update-your-device"></a>So aktualisieren Sie Ihr Gerät

1.  Klicken Sie auf der Seite **Schnellstart** für das Gerät auf **Geräte**. Wählen Sie das physische Gerät aus, und klicken Sie auf **Wartung** und dann auf **Updates scannen**.  

2.  Für die Suche nach verfügbaren Updates wird ein Auftrag erstellt. Wenn Updates verfügbar sind, ändert sich die Option **Updates scannen** in **Updates installieren**. Klicken Sie auf **Updates installieren**. 

3.  Es wird ein Updateauftrag erstellt. Überwachen Sie den Status Ihres Updates, indem Sie zu **Aufträge**wechseln.

    > [AZURE.NOTE] Wenn der Updateauftrag startet, wird der Status sofort mit 50 Prozent angezeigt. Der Status ändert sich erst in 100 Prozent, wenn der Updateauftrag abgeschlossen ist. Für den Updateprozess gibt es keinen Echtzeitstatus.

4.  Falls die Netzwerkschnittstellen DATA 2 und DATA 3 deaktiviert wurden, aktivieren Sie diese nach der erfolgreichen Aktualisierung des Geräts wieder.

<!-- In step 2, you may be requested to disable Data 2 and Data 3 prior to installing the updates. You must disable these network interfaces or the updates may fail.-->

## <a name="get-the-iqn-of-a-windows-server-host"></a>Abrufen des IQN eines Windows Server-Hosts

Führen Sie die folgenden Schritte aus, um den IQN (iSCSI Qualified Name) eines Windows-Hosts abzurufen, auf dem Windows Server® 2012 ausgeführt wird.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Erstellen einer manuellen Sicherung

Führen Sie die folgenden Schritte im Verwaltungsportal aus, um nach Bedarf eine manuelle Sicherung eines einzelnen Volumes auf dem StorSimple-Gerät zu erstellen.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]


## <a name="next-steps"></a>Nächste Schritte

- Konfigurieren eines [virtuellen Geräts](storsimple-virtual-device-u2.md)

- Verwenden des [StorSimple-Manager-Diensts](storsimple-manager-service-administration.md) für das Verwalten Ihres StorSimple-Geräts
 



<!--HONumber=Oct16_HO2-->


