---
title: "Installieren von Update 3 auf dem StorSimple-Gerät | Microsoft Docs"
description: "Erfahren Sie, wie Sie Update 3 für die StorSimple 8000-Serie auf Ihrem StorSimple-Gerät der 8000-Serie installieren."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: c6c4634d-4f3a-4bc4-b307-a22bf18664e1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2b99b9cd52dd28f7f62b5d8d5ffe32339a67f82a
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2017
---
# <a name="install-update-3-on-your-storsimple-8000-series-device"></a>Installieren von Update 3 auf Geräten der StorSimple 8000-Serie

> [!NOTE]
> Das klassische Portal für StorSimple ist veraltet. Ihre StorSimple-Geräte-Manager werden gemäß dem Zeitplan für die Abschaltung automatisch in das neue Azure-Portal verschoben. Sie erhalten zu dieser Verschiebung eine E-Mail und eine Portalbenachrichtigung. Dieses Dokument wird ebenfalls bald entfernt. Fragen zu diesem Umzug finden Sie unter [Verschieben des StorSimple Device Manager-Diensts vom klassischen Portal in das Azure-Portal: häufig gestellte Fragen (FAQ)](storsimple-8000-move-azure-portal-faq.md).


## <a name="overview"></a>Übersicht

In diesem Tutorial wird beschrieben, wie Sie Update 3 auf einem StorSimple-Gerät mit einer früheren Softwareversion über das klassische Azure-Portal und mithilfe der Hotfixmethode ausführen. Die Hotfixmethode wird verwendet, wenn ein Gateway auf einer anderen Netzwerkschnittstelle als DATA 0 des StorSimple-Geräts konfiguriert ist und Sie versuchen, das Update von einer Softwareversion durchzuführen, die noch nicht Update 1 enthält.

Update 3 bietet Updates für die Gerätesoftware, LSI-Treiber und -Firmware, Storport und Spaceport. Wenn ausgehend von Update 2 oder einer früheren Version ein Update erfolgt, sind außerdem Updates von iSCSI, WMI und in bestimmten Fällen der Firmware von Datenträgern erforderlich. Die Fehlerbehebungen von Gerätesoftware, WMI, iSCSI, LSI-Treiber, Spaceport und Storport sind unterbrechungsfreie Updates. Diese Updates können über das klassische Azure-Portal angewendet werden. Die Updates für die Datenträgerfirmware führen zu einer Betriebsunterbrechung und können nur über die Windows PowerShell-Schnittstelle des Geräts angewendet werden.

> [!IMPORTANT]
> * Vor der Installation wird ein Satz manueller und automatischer Vorabprüfungen durchgeführt, mit denen die Geräteintegrität in Bezug auf Hardwarestatus und Netzwerkkonnektivität ermittelt wird. Diese Vorabprüfungen werden nur ausgeführt, wenn Sie die Updates aus dem klassischen Azure-Portal ausführen.
> * Wir empfehlen, die Software- und Treiberupdates über das klassische Azure-Portal zu installieren. Verwenden Sie die Windows PowerShell-Schnittstelle des Geräts (zum Installieren der Updates) nur, wenn die Vorabprüfung für das Gateway im Portal fehlschlägt. Abhängig von der Version, von der aus Sie aktualisieren, dauert die Installation des Updates 1,5-2,5 Stunden. Wartungsmodusupdates müssen über die Windows PowerShell-Schnittstelle des Geräts ausgeführt werden. Da Updates im Wartungsmodus den Betrieb unterbrechen, führen sie zu einer Ausfallzeit für Ihr Gerät.
> * Stellen Sie bei Ausführung des optionalen StorSimple Snapshot Manager sicher, dass Sie die Snapshot Manager-Version auf Update 2 aktualisiert haben, bevor Sie das Gerät aktualisieren.

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-3-via-the-azure-classic-portal"></a>Installieren von Update 3 über das klassische Azure-Portal
Führen Sie die folgenden Schritte aus, um Ihr Gerät auf [Update 3](storsimple-update3-release-notes.md)zu aktualisieren.

> [!NOTE]
> Wenn Sie Update 2 oder höher anwenden (einschließlich Update 2.1), wird Microsoft zusätzliche Diagnoseinformationen vom Gerät abrufen können. Mit diesen Daten können StorSimple-Geräte mit Problemen identifiziert und Fehler diagnostiziert werden. Durch Akzeptieren von Update 2 oder höher gestatten Sie uns die Bereitstellung dieses proaktiven Supports.


[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

Stellen Sie sicher, dass auf Ihrem Gerät **Update 3 der StorSimple 8000-Serie (6.3.9600.17759)**ausgeführt wird. Das **Datum der letzten Aktualisierung** ändert sich. 
   - Bei einer Aktualisierung von einer älteren Version als Update 2 sehen Sie, dass die Wartungsmodusupdates verfügbar sind. Diese Meldung wird nach der Installation der Updates unter Umständen bis zu 24 Stunden lang angezeigt.
     Wartungsmodusupdates unterbrechen den Betrieb und führen zu einer Ausfallzeit des Geräts. Diese Updates können nur über die Windows PowerShell-Schnittstelle Ihres Geräts angewendet werden. In einigen Fällen ist die Datenträgerfirmware beim Ausführen von Update 1.2 unter Umständen bereits auf dem neuesten Stand, und Sie müssen keine Wartungsmodusupdates installieren.
   - Wenn ein Update ausgehend von Update 2 oder höher erfolgt ist, sollte Ihr Gerät nun auf dem neuesten Stand sein. Sie können den nächsten Schritt überspringen.

Laden Sie die Wartungsmodus-Updates herunter, indem Sie mithilfe der unter [Herunterladen von Hotfixes](#to-download-hotfixes) angegebenen Schritte nach KB3121899 suchen und es herunterladen, um Datenträger-Firmwareupdates zu installieren. (Die anderen Updates müssten inzwischen bereits installiert sein.) Führen Sie die unter [Installieren und Überprüfen von Wartungsmodus-Hotfixes](#to-install-and-verify-maintenance-mode-hotfixes) angegebenen Schritte aus, um die Wartungsmodus-Updates zu installieren. 

## <a name="install-update-3-as-a-hotfix"></a>Installieren von Update 3 als Hotfix
Gehen Sie wie folgt vor, falls beim Installieren über das klassische Azure-Portal ein Fehler bei der Gatewayprüfung auftritt. Die Überprüfung zeigt einen Fehler, weil Sie ein Gateway auf einer anderen Netzwerkschnittstelle als DATA 0 zugewiesen haben und auf Ihrem Gerät eine Softwareversion vor Update 1 ausgeführt wird.

Die Softwareversionen, die mithilfe dieser Methode aktualisiert werden können, sind:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2 

> [!IMPORTANT]
> * Wenn auf dem Gerät die Releaseversion (GA) ausgeführt wird, wenden Sie sich an den [Microsoft Support](storsimple-contact-microsoft-support.md) , um Hilfe bei diesem Update zu erhalten.
> 
> 

Das Hotfixverfahren umfasst die folgenden drei Schritte:

1. Laden Sie die Hotfixes aus dem Microsoft Update-Katalog herunter.
2. Installieren und überprüfen Sie die Hotfixes für den normalen Modus.
3. Installieren und überprüfen Sie den Wartungsmodus-Hotfix (nur, wenn Sie ein Update von der Software durchführen, die noch nicht Update 2 enthält).

#### <a name="download-updates-for-your-device"></a>Herunterladen von Updates für Ihr Gerät
**Wenn auf Ihrem Gerät Update 2.1 oder 2.2 ausgeführt wird**, müssen Sie die folgenden Hotfixes in der vorgeschriebenen Reihenfolge herunterladen und installieren:

| Reihenfolge | KB | Beschreibung | Updatetyp | Installationszeit |
| --- | --- | --- | --- | --- |
| 1. |KB3186843 |Softwareupdate &#42; |Regulär  <br></br>Unterbrechungsfrei |~ 45 Min. |
| 2. |KB3186859 |LSI-Treiber und -Firmware |Regulär  <br></br>Unterbrechungsfrei |~ 20 Min. |
| 3. |KB3121261 |Storport- und Spaceport-Fix  </br> Windows Server 2012 R2 |Regulär  <br></br>Unterbrechungsfrei |~ 20 Min. |

&#42; *Beachten Sie, dass das Softwareupdate aus zwei Binärdateien besteht: aus einem Update für die Gerätesoftware mit dem Präfix `all-hcsmdssoftwareupdate` und dem CIS- und MDS-Agent mit dem Präfix `all-cismdsagentupdatebundle`. Das Update für die Gerätesoftware muss vor dem CIS- und MDS-Agent installiert werden. Sie müssen auch den aktiven Controller über das `Restart-HcsController`-Cmdlet neu starten, nachdem Sie das Update für den CIS- und MDS-Agent angewendet haben (und bevor Sie die verbleibenden Updates anwenden).* 

**Wenn auf Ihrem Gerät Update 0.1, 0.2, 0.3, 1.0, 1.1, 1.2 oder 2.0 ausgeführt wird**, müssen Sie zusätzlich zu den Software-, LSI-Treiber- und -Firmware-Updates (in der vorherigen Tabelle gezeigt) die folgenden Hotfixes in der vorgeschriebenen Reihenfolge herunterladen und installieren:

| Reihenfolge | KB | Beschreibung | Updatetyp | Installationszeit |
| --- | --- | --- | --- | --- |
| 4. |KB3146621 |iSCSI-Paket |Regulär  <br></br>Unterbrechungsfrei |~ 20 Min. |
| 5. |KB3103616 |WMI-Paket |Regulär  <br></br>Unterbrechungsfrei |~ 12 Min. |

<br></br>

**Wenn auf Ihrem Gerät die Version 0.2, 0.3, 1.0, 1.1 und 1.2 ausführt wird**, müssen Sie zusätzlich zu den in den vorhergehenden Tabellen enthaltenen Updates auch Updates der Firmware von Datenträgern installieren. Sie können überprüfen, ob Sie Updates für die Datenträgerfirmware benötigen, indem Sie das Cmdlet `Get-HcsFirmwareVersion` ausführen. Wenn Sie die folgenden Firmwareversionen ausführen (`XMGG`, `XGEG`, `KZ50`, `F6C2`, `VR08`), müssen Sie diese Updates nicht installieren.

| Reihenfolge | KB | Beschreibung | Updatetyp | Installationszeit |
| --- | --- | --- | --- | --- |
| 6. |KB3121899 |Datenträgerfirmware |Wartung  <br></br>Mit Unterbrechung |~ 30 Min. |

<br></br>

> [!IMPORTANT]
> * Dieses Verfahren muss nur einmal ausgeführt werden, um Update 3 anzuwenden. Über das klassischen Azure-Portal können Sie nachfolgende Updates installieren.
> * Wenn Sie von Update 2.2 aktualisieren, dauert die gesamte Installation etwa 1,1 Stunden.
> * Stellen Sie vor dem Verwenden dieses Verfahrens zum Anwenden des Updates sicher, dass beide Gerätecontroller online und alle Hardwarekomponenten fehlerfrei sind.
> 
> 

Führen Sie die folgenden Schritte aus, um diese Datei herunterzuladen und die Hotfixes zu installieren.

[!INCLUDE [storsimple-install-update3-hotfix](../../includes/storsimple-install-update3-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter [Versionsanmerkungen zu Update 3](storsimple-update3-release-notes.md).

