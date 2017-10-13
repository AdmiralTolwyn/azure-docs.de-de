---
title: "Versionshinweise zu Update 0.2 von StorSimple 8000 | Microsoft Docs"
description: "Beschreibt die neuen Features und Problembehebungen sowie noch nicht behandelte Probleme und verfügbare Problemumgehungen für die Microsoft Azure StorSimple-Version vom Januar 2015 (Update 0.2)."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: d9684ae3-b38f-4678-9d70-e5dbc6b03350
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/16/2016
ms.author: v-sharos
ms.openlocfilehash: 2fc50f7faddb4b61ea5e7d328469fc3cc0eb6f3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-8000-series-update-02-release-notes---january-2015"></a>Versionsanmerkungen zu Update 0.2 der StorSimple 8000-Serie – Januar 2015
## <a name="overview"></a>Übersicht
Dieses Dokument beschreibt die wichtigsten offenen Fragen für die Microsoft Azure StorSimple-Version von Januar 2015. Es enthält auch eine Liste der StorSimple-Software- und -Firmware-Updates, die in dieser Version enthalten sind. Dies ist die zweite Version, nachdem die Freigabeversion der StorSimple 8000-Serie im Juli 2014 allgemein verfügbar gemacht wurde.

Dieses Update ändert die Softwareversion des physischen Geräts nach dem Oktober-Update nicht. Es bleibt weiterhin Version 6.3.9600.17312. Das vom Image des virtuellen Geräts verwendete Image wurde in dieser Version geändert. Daher wird für alle neuen nach dem 20.01.2015 erstellten virtuellen Geräte die Version 6.3.9600.17361 angezeigt.  

Lesen Sie die folgenden Informationen in den Versionshinweisen für das Update vom Januar 2015.

> [!IMPORTANT]
> * Dieses Update ist nicht über Windows Update verfügbar und kann nicht wie andere Updates installiert werden. Ihr Gerät erhält dieses Update selbst dann nicht, wenn Sie die Updates über das klassische Azure-Portal installiert haben. Dieses Update gilt nur für virtuelle Geräte, die nach dem 20. Januar 2015 erstellt wurden. 
> * Die Januar-Version von StorSimple enthält keine Updates des physischen StorSimple-Geräts. Sie können weiterhin alle verfügbaren Windows-Updates für das virtuelle Gerät, einschließlich der neuesten Sicherheitsupdates, anwenden, es wird aber keine Änderung der Version für das physische StorSimple-Gerät angezeigt.
> 
> 

## <a name="whats-new-in-the-january-release"></a>Neuigkeiten in der Januar-Version
Dieses Update behebt ein Problem, durch das die Volumes auf dem virtuellen Gerät offline geschaltet werden. (Siehe [In dieser Version behobene Probleme](#issues-fixed-in-the-january-release).)  

Das Update enthält keine neuen Features oder Funktionen.  

## <a name="issues-fixed-in-the-january-release"></a>In der Januar-Version behobene Probleme
Die folgende Tabelle beschreibt das Problem, das in diesem Update behoben wurde.

| Nr. | Funktion | Problem | Gilt für das physische Gerät | Gilt für das virtuelle Gerät |
| --- | --- | --- | --- | --- |
| 1 |Wechsel von Volumes in den Offlinemodus |Wenn Cloud-Latenzen für mehrere Minuten andauern, wechseln die Volumes des virtuellen StorSimple-Geräts auf den Hosts in den Offlinemodus. Dieses Update erhöht den Schwellenwert für die Cloud-Latenzen und minimiert dadurch die Situationen, in denen die Volumes auf Hosts offline gehen. |Nein |Ja |

## <a name="known-issues-in-the-january-release"></a>Bekannte Probleme in der Januar-Version
Die folgende Tabelle enthält eine Zusammenfassung der bekannten Probleme in dieser Version.

| Nr. | Funktion | Problem | Kommentare/Problemumgehung | Gilt für das physische Gerät | Gilt für das virtuelle Gerät |
| --- | --- | --- | --- | --- | --- |
| 1 |Zurücksetzen auf Werkseinstellungen |Unter bestimmten Umständen kann das StorSimple-Gerät beim Zurücksetzen auf die Werkseinstellungen nicht mehr reagieren und die folgende Nachricht anzeigen: **Zurücksetzung auf Werkseinstellungen wird ausgeführt (Phase 8)**. Dies kann vorkommen, wenn Sie STRG+C drücken, während das Cmdlet ausgeführt wird. |Drücken Sie STRG+C nicht nach dem Einleiten einer Zurücksetzung auf die Werkseinstellungen. Wenn dies bereits erfolgt ist, wenden Sie sich an den Microsoft-Support, um Informationen zu den nächsten Schritten zu erhalten. |Ja |Nein |
| 2 |Datenträgerquorum |In seltenen Fällen kann der Speicherpool offline geschaltet werden, wenn der Großteil der Datenträger im EBOD-Gehäuse eines 8600-Geräts getrennt wird, sodass kein Datenträgerquorum verfügbar ist. Der Speicherpool bleibt offline, auch wenn die Verbindung zu den Datenträgern wiederhergestellt wird. |Sie müssen das Gerät neu starten. Wenn das Problem weiterhin auftritt, wenden Sie sich an den Microsoft-Support, um Informationen zu den nächsten Schritten zu erhalten. |Ja |Nein |
| 3 |Fehler bei der Cloud-Momentaufnahme |In seltenen Fällen tritt ggf. ein Fehler **Maximales Sicherungslimit erreicht** einer Cloudmomentaufnahme auf. Dies kann vorkommen, wenn die Anzahl von 255 Onlineklonen auf demselben Gerät überschritten wird, die von demselben ursprünglichen Volume stammen, das gelöscht wurde. | |Ja |Ja |
| 4 |Falsche Controller-ID |Beim Austausch eines Controllers kann es vorkommen, dass Controller 0 als Controller 1 angezeigt wird. Während des Controlleraustauschs kann die Controller-ID anfänglich als ID des Peercontrollers angezeigt werden, wenn das Image vom Peerknoten geladen wurde.  In seltenen Fällen kann dieses Verhalten auch nach einem Neustart des Systems auftreten. |Es ist keine Benutzeraktion erforderlich. Dieses Problem löst sich von selbst, nachdem der Controlleraustausch abgeschlossen ist. |Ja |Nein |
| 5 |Geräteüberwachungsdiagramme |Im StorSimple-Manager-Dienst funktionieren die Geräteüberwachungsdiagramme nicht, wenn in der Proxyserverkonfiguration für das Gerät die Standard- oder NTLM-Authentifizierung aktiviert ist. |Ändern Sie die Webproxykonfiguration für das beim StorSimple-Manager-Dienst registrierte Gerät so, dass für die Authentifizierung die Option KEINE festgelegt ist. Führen Sie zu diesem Zweck das Windows PowerShell für StorSimple-Cmdlet "Set-HcsWebProxy" aus. |Ja |Ja |
| 6 |Speicherkonten |Das Verwenden des Speicherdiensts zum Löschen des Speicherkontos wird nicht unterstützt. Dies führt dazu, dass keine Benutzerdaten abgerufen werden können. | |Ja |Ja |
| 7 |Gerätefailover |Mehrere Failover eines Volumecontainers von demselben Quellgerät auf verschiedene Zielgeräte werden nicht unterstützt. |Das Failover von einem einzelnen nicht reagierenden Gerät auf mehrere Geräte führt dazu, dass die Volumecontainer auf dem ersten Gerät mit erfolgtem Failover die Dateneigentümerschaft verlieren. Wenn Sie diese Volumecontainer nach einem solchen Failover im klassischen Azure-Portal betrachten, werden sie anders angezeigt oder verhalten sie sich anders. |Ja |Nein |
| 8 |Installation |Während der Installation von StorSimple-Adapter für SharePoint müssen Sie die IP-Adresse eines Geräts angeben, damit die Installation erfolgreich abgeschlossen wird. | |Ja |Nein |
| 9 |Webproxy |Wenn Ihre Webproxykonfiguration das Protokoll "HTTPS" verwendet, ist die Kommunikation zwischen dem Gerät und dem Dienst beeinträchtigt, und das Gerät wird offline geschaltet. Supportpakete werden bei diesem Vorgang ebenfalls generiert. Sie beanspruchen auf Ihrem Gerät erhebliche Ressourcen. |Stellen Sie sicher, dass "HTTP" als Protokoll für die Webproxy-URL angegeben ist. Weitere Informationen finden Sie unter [Konfigurieren des Webproxys für Ihr Gerät](storsimple-configure-web-proxy.md). |Ja |Nein |
| 10 |Webproxy |Wenn Sie den Webproxy für ein registriertes Gerät konfigurieren und aktivieren, müssen Sie den aktiven Controller auf Ihrem Gerät neu starten. | |Ja |Nein |
| 11 |Hohe Cloud-Latenzen und hohe E/A-Arbeitsauslastung |Wenn Ihr StorSimple-Gerät mit einer Kombination aus sehr hohen Cloud-Latenzen (mehrere Sekunden) und hoher E/A-Arbeitsauslastung konfrontiert wird, verschlechtert sich die Leistung der Gerätevolumes, und es tritt ggf. der E/A-Fehler "Gerät nicht bereit" auf. |Sie müssen die Gerätecontroller manuell neu starten oder ein Gerätefailover ausführen, um dieses Problem zu beheben. |Ja |Nein |

## <a name="physical-device-updates-in-the-january-release"></a>Updates für das physische Gerät in der Januar-Version
Dieses Update enthält keine anderen Änderungen am StorSimple-Gerät.

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-january-release"></a>Updates des SAS-Controllers (Serial Attached SCSI) und der Firmware in der Januar-Version
Diese Version enthält keine Updates für den SAS-Controller (Serial Attached SCSI) oder die Firmware. Das Treiberupdate wurde in der Version vom Oktober 2014 durchgeführt. 

## <a name="virtual-device-updates-in-the-january-release"></a>Updates für virtuelle Geräte in der Version vom Januar
Diese Version enthält ein aktualisiertes Image für das virtuelle Gerät. Für alle nach dem 20.01.2015 erstellten virtuellen Geräte wird die Version 6.3.9600.17361 angezeigt.

