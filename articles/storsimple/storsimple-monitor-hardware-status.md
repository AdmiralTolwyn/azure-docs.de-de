---
title: StorSimple-Hardwarekomponenten und Status | Microsoft Docs
description: Hier erfahren Sie, wie Sie mithilfe des StorSimple Manager-Diensts die Hardwarekomponenten Ihres StorSimple-Geräts überwachen.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: alkohli

---
# Überwachen von Hardwarekomponenten und Status mithilfe des StorSimple Manager-Diensts
## Übersicht
Dieser Artikel beschreibt die verschiedenen physischen und logischen Komponenten Ihres lokalen StorSimple-Geräts. Darüber hinaus wird erläutert, wie Sie den Status der Gerätekomponenten auf der Seite **Wartung** des StorSimple Manager-Diensts überwachen.

Die Seite **Wartung** zeigt den Hardwarestatus aller StorSimple-Gerätekomponenten.

Unter der Liste mit den Komponenten für 8100 befinden sich drei Abschnitte, die Folgendes beschreiben:

* **Gemeinsam genutzte Komponenten**: Diese Komponenten sind nicht Teil der Controller. Hierbei handelt es sich beispielsweise um Laufwerke, Gehäuse, PCM-Komponenten und Fühler für PCM-Temperatur, -Strom und -Spannung.
* **Controller 0-Komponenten**: Die Komponenten von Controller 0. Hierzu zählen beispielsweise Controller, SAS-Erweiterung und -Connector, Controllertemperaturfühler und die verschiedenen Netzwerkschnittstellen.
* **Controller 1-Komponenten**: Die Komponenten von Controller 1 (ähnlich wie bei Controller 0).

Ein Gerät vom Typ 8600 verfügt über zusätzliche Komponenten für das EBOD-Gehäuse. Unter der Komponentenliste befinden sich fünf Abschnitte. Davon enthalten drei Abschnitte die Komponenten des primären Gehäuses. Diese entsprechen der Beschreibung für 8100. Die beiden zusätzlichen Abschnitte beziehen sich auf das EBOD-Gehäuse und beschreiben Folgendes:

* **Gemeinsam genutzte EBOD-Komponenten**: Die Komponenten im EBOD-Gehäuse und PCM, die nicht Teil des EBOD-Controllers sind.
* **EBOD-Controller 0-Komponenten**: Die Komponenten im EBOD-Gehäuse 0. Hierzu zählen beispielsweise EBOD-Controller, SAS-Erweiterung und -Connector sowie Controllertemperaturfühler.
* **EBOD-Controller 1-Komponenten**: Die Komponenten von EBOD-Gehäuse 1 (ähnlich wie bei EBOD-Gehäuse 0).

> [!NOTE]
> **Bei virtuellen StorSimple-Geräten (1100) ist auf der Wartungsseite kein Hardwarestatusbereich vorhanden.**
> 
> 

## Überwachen des Hardwarestatus
Führen Sie die folgenden Schritte aus, um den Hardwarestatus einer Gerätekomponente anzuzeigen:

1. Navigieren Sie zu **Geräte**, und wählen Sie ein bestimmtes StorSimple-Gerät aus. Klicken Sie im Menü auf Geräteebene auf **Wartung**.
2. Navigieren Sie zum Abschnitt **Hardwarestatus**, und wählen Sie eine der verfügbaren Komponenten aus, die weiter oben beschrieben wurden. Klicken Sie einfach auf den Pfeil vor einer Komponentenbezeichnung, um die Liste zu erweitern und den Status der verschiedenen Gerätekomponenten anzuzeigen. Weitere Informationen finden Sie in der [ausführlichen Komponentenliste für das primäre Gehäuse](#component-list-for-primary-enclosure-of-storsimple-device) sowie in der [ausführlichen Komponentenliste für das EBOD-Gehäuse](#component-list-for-ebod-enclosure-of-storsimple-device).
3. Der Komponentenstatus basiert auf dem folgenden Farbschema:
   
   * **Grünes Häkchen**: Die Komponente ist frei von Fehlern oder hat den Status **OK**.
   * **Gelb**: Für die Komponente liegt eine Warnung vor.
   * **Rotes Ausrufezeichen**: Die Komponente ist fehlerhaft oder hat den Status **Eingreifen erforderlich**.
   * **Weiß mit schwarzem Text**: Die Komponente ist nicht vorhanden.
4. Sollte sich eine Komponente nicht in einem fehlerfreien Zustand befinden, wenden Sie sich an den Support von Microsoft. Sind Warnungen auf dem Gerät aktiviert, werden Sie per E-Mail benachrichtigt. Informationen zum Austauschen fehlerhafter Hardwarekomponenten finden Sie unter [Austausch von StorSimple-Hardwarekomponenten](storsimple-hardware-component-replacement.md).

## Komponentenliste für das primäre Gehäuse eines StorSimple-Geräts
Die folgende Tabelle gibt Aufschluss über die physischen und logischen Komponenten im primären Gehäuse Ihres lokalen StorSimple-Geräts.

| Component | Modul | Typ | Ort | FRU (Field Replaceable Unit)? | Beschreibung |
| --- | --- | --- | --- | --- | --- |
| Laufwerk in Einschubfach [0-11] |Laufwerke |Physisch |Shared |Ja |Die einzelnen SSD- oder HDD-Laufwerke des primären Gehäuses werden jeweils in einer eigenen Zeile angegeben. |
| Ambiente-Temperatursensor |Gehäuse |Physisch |Shared |Nein |Misst die Temperatur im Gehäuse. |
| Midplane-Temperatursensor |Gehäuse |Physisch |Shared |Nein |Misst die Temperatur auf der mittleren Ebene. |
| Hörbarer Alarm |Gehäuse |Physisch |Shared |Nein |Gibt an, ob das Subsystem für den akustischen Alarm im Gehäuse funktionsfähig ist. |
| Gehäuse |Gehäuse |Physisch |Shared |Ja |Gibt an, dass ein Gehäuse vorhanden ist. |
| Gehäuseeinstellungen |Gehäuse |Physisch |Shared |Nein |Bezieht sich auf das vordere Bedienfeld des Gehäuses. |
| Spannungssensoren |PCM |Physisch |Shared |Nein |Der angezeigte Zustand zahlreicher Spannungssensoren gibt Aufschluss darüber, ob die gemessene Spannung innerhalb des Toleranzbereichs liegt. |
| Stromsensoren |PCM |Physisch |Shared |Nein |Der angezeigte Zustand zahlreicher Stromsensoren gibt Aufschluss darüber, ob die gemessene Stromstärke innerhalb des Toleranzbereichs liegt. |
| Temperatursensoren in PCM |PCM |Physisch |Shared |Nein |Der angezeigte Zustand zahlreicher Temperatursensoren (beispielsweise Einlass- und Hotspot-Sensoren) gibt Aufschluss darüber, ob die gemessene Temperatur innerhalb des Toleranzbereichs liegt. |
| Netzteil [0-1] |PCM |Physisch |Shared |Ja |Für die einzelnen Stromversorgungen in den beiden PCMs an der Geräterückseite wird jeweils eine eigene Zeile angezeigt. |
| Kühlung [0-1] |PCM |Physisch |Shared |Ja |Für die vier Lüfter in den beiden PCMs wird jeweils eine eigene Zeile angezeigt. |
| Pufferbatterie [0-1] |PCM |Physisch |Shared |Ja |Für die einzelnen Sicherungsakkumodule im PCM wird jeweils eine eigene Zeile angezeigt. |
| Metis |– |Logisch |Shared |N/V |Zeigt den Zustand der Akkus an und gibt Aufschluss über Ladebedarf und Haltbarkeit. |
| Cluster |N/V |Logisch |Shared |N/V |Zeigt den Zustand des Clusters, der zwischen den beiden integrierten Controller-Modulen erstellt wird. |
| Clusterknoten |– |Logisch |Shared |N/V |Gibt den Zustand des Controllers als Teil des Clusters an. |
| Clusterquorum |– |Logisch | |N/V |Gibt an, dass die Mehrheitsdatenträger-Mitgliedschaft im HDD-Speicherpool vorhanden ist. |
| HDD-Datenbereich |– |Logisch |Shared |– |Der Speicherplatz, der für Daten im HDD-Speicherpool verwendet wird. |
| HDD-Verwaltungsbereich |N/V |Logisch |Shared |N/V |Der Platz, der im HDD-Speicherpool für Verwaltungsaufgaben reserviert ist. |
| HDD-Quorumbereich |– |Logisch |Shared |N/V |Der Platz, der im HDD-Speicherpool für das Clusterquorum reserviert ist. |
| HDD-Ersetzungsbereich |N/V |Logisch |Shared |N/V |Der Platz, der im HDD-Speicherpool für die Controllerersetzung reserviert ist. |
| SSD-Datenbereich |– |Logisch |Shared |N/V |Der Speicherplatz, der im SSD-Speicherpool für Daten verwendet wird. |
| SSD-NVRAM-Bereich |N/V |Logisch |Shared |– |Der Speicherplatz im SSD-Speicherpool, der für die NVRAM-Logik reserviert ist. |
| HDD-Speicherpool |N/V |Logisch |Shared |N/V |Zeigt den Zustand des logischen Speicherpools an, der auf der Grundlage von Geräte-HDDs erstellt wird. |
| SSD-Speicherpool |N/V |Logisch |Shared |– |Zeigt den Zustand des logischen Speicherpools an, der auf der Grundlage von Geräte-SSDs erstellt wird. |
| Controller [0-1] [Status] |E/A |Physisch |Controller |Ja |Zeigt den Zustand des Controllers im Gehäuse und gibt Aufschluss darüber, ob er aktiv oder im Standbymodus ist. |
| Temperatursensoren im Controller |E/A |Physisch |Controller |Nein |Der angezeigte Zustand zahlreicher Temperatursensoren (etwa für das E/A-Modul, für die CPU-Temperatur sowie DIMM- und PCIe-Sensoren) gibt Aufschluss darüber, ob die Temperatur innerhalb des Toleranzbereichs liegt. |
| SAS-Erweiterung |E/A |Physisch |Controller |Nein |Gibt den Zustand der SAS-Erweiterung an, über die der integrierte Speicher mit dem Controller verbunden wird. |
| SAS-Anschluss [0-1] |E/A |Physisch |Controller |Nein |Gibt den Zustand der einzelnen SAS-Verbinder an, über die der integrierte Speicher mit der SAS-Erweiterung verbunden wird. |
| SBB-Midplane-Interconnect |E/A |Physisch |Controller |Nein |Gibt den Zustand des Midplane-Verbinders an, über den die einzelnen Controller mit der mittleren Ebene verbunden werden. |
| Prozessorkern |E/A |Physisch |Controller |Nein |Gibt den Zustand der Prozessorkerne in den einzelnen Controllern an. |
| Gehäuseelektronikleistung |E/A |Physisch |Controller |Nein |Gibt den Zustand der Stromversorgung des Gehäuses an. |
| Gehäuseelektronikdiagnose |E/A |Physisch |Controller |Nein |Gibt den Zustand der vom Controller bereitgestellten Diagnosesubsysteme an. |
| Baseboard-Verwaltungscontroller |E/A |Physisch |Controller |Nein |Gibt den Zustand des Baseboard-Verwaltungscontrollers (Baseboard Management Controller, BMC) an. Hierbei handelt es sich um einen speziellen Dienstprozessor, der das Hardwaregerät mithilfe von Sensoren überwacht und mit dem Systemadministrator über eine unabhängige Verbindung kommuniziert. |
| Ethernet |E/A |Physisch |Controller |Nein |Gibt den Zustand der einzelnen Netzwerkschnittstellen (Verwaltungs- und Datenports des Controllers) an. |
| NVRAM |E/A |Physisch |Controller |Nein |Gibt den Zustand des NVRAM (permanenter, vom Akku versorgter Arbeitsspeicher zur Bewahrung anwendungskritischer Informationen bei einem Stromausfall) an. |

## Komponentenliste für das EBOD-Gehäuse eines StorSimple-Geräts
Die folgende Tabelle gibt Aufschluss über die physischen und logischen Komponenten im EBOD-Gehäuse Ihres lokalen StorSimple-Geräts.

| Component | Modul | Typ | Ort | FRU? | Beschreibung |
| --- | --- | --- | --- | --- | --- |
| Laufwerk in Einschubfach [0-11] |Laufwerke |Physisch |Shared |Ja |Die einzelnen HDD-Laufwerke an der Vorderseite des EBOD-Gehäuses werden jeweils in einer eigenen Zeile angegeben. |
| Ambiente-Temperatursensor |Gehäuse |Physisch |Shared |Nein |Misst die Temperatur im Gehäuse. |
| Midplane-Temperatursensor |Gehäuse |Physisch |Shared |Nein |Misst die Temperatur auf der mittleren Ebene. |
| Hörbarer Alarm |Gehäuse |Physisch |Shared |Nein |Gibt an, ob das Subsystem für den akustischen Alarm im Gehäuse funktionsfähig ist. |
| Gehäuse |Gehäuse |Physisch |Shared |Ja |Gibt an, dass ein Gehäuse vorhanden ist. |
| Gehäuseeinstellungen |Gehäuse |Physisch |Shared |Nein |Bezieht sich auf das OPS oder vordere Bedienfeld des Gehäuses. |
| Spannungssensoren |PCM |Physisch |Shared |Nein |Der angezeigte Zustand zahlreicher Spannungssensoren gibt Aufschluss darüber, ob die gemessene Spannung innerhalb des Toleranzbereichs liegt. |
| Stromsensoren |PCM |Physisch |Shared |Nein |Der angezeigte Zustand zahlreicher Stromsensoren gibt Aufschluss darüber, ob die gemessene Stromstärke innerhalb des Toleranzbereichs liegt. |
| Temperatursensoren in PCM |PCM |Physisch |Shared |Nein |Der angezeigte Zustand zahlreicher Temperatursensoren (beispielsweise Einlass- und Hotspot-Sensoren) gibt Aufschluss darüber, ob die gemessene Temperatur innerhalb des Toleranzbereichs liegt. |
| Netzteil [0-1] |PCM |Physisch |Shared |Ja |Für die einzelnen Stromversorgungen in den beiden PCMs an der Geräterückseite wird jeweils eine eigene Zeile angezeigt. |
| Kühlung [0-1] |PCM |Physisch |Shared |Ja |Für die vier Lüfter in den beiden PCMs wird jeweils eine eigene Zeile angezeigt. |
| Lokaler Speicher [HDD] |N/V |Logisch |Shared |N/V |Zeigt den Zustand des logischen Speicherpools an, der auf der Grundlage von Geräte-HDDs erstellt wird. |
| Controller [0-1] [Status] |E/A |Physisch |Controller |Ja |Zeigt den Zustand der Controller im EBOD-Modul an. |
| Temperatursensoren in EBOD |E/A |Physisch |Controller |Nein |Der angezeigte Zustand zahlreicher Temperatursensoren der einzelnen Controller gibt Aufschluss darüber, ob die Temperatur innerhalb des Toleranzbereichs liegt. |
| SAS-Erweiterung |E/A |Physisch |Controller |Nein |Gibt den Zustand der SAS-Erweiterung an, über die der integrierte Speicher mit dem Controller verbunden wird. |
| SAS-Anschluss [0-2] |E/A |Physisch |Controller |Nein |Gibt den Zustand der einzelnen SAS-Verbinder an, über die der integrierte Speicher mit der SAS-Erweiterung verbunden wird. |
| SBB-Midplane-Interconnect |E/A |Physisch |Controller |Nein |Gibt den Zustand des Midplane-Verbinders an, über den die einzelnen Controller mit der mittleren Ebene verbunden werden. |
| Gehäuseelektronikleistung |E/A |Physisch |Controller |Nein |Gibt den Zustand der Stromversorgung des Gehäuses an. |
| Gehäuseelektronikdiagnose |E/A |Physisch |Controller |Nein |Gibt den Zustand der vom Controller bereitgestellten Diagnosesubsysteme an. |
| Verbindung mit Gerätecontroller |E/A |Physisch |Controller |Nein |Gibt den Zustand der Verbindung zwischen dem EBOD-E/A-Modul und dem Gerätecontroller an. |

## Nächste Schritte
* Weitere Informationen zum Verwalten Ihres Geräts mit dem StorSimple Manager-Diensts finden Sie unter [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple Manager-Diensts](storsimple-manager-service-administration.md).
* Informationen zum Behandeln von Problemen mit einer beeinträchtigten oder fehlerhaften Gerätekomponente finden Sie unter [StorSimple-Überwachungsindikatoren](storsimple-monitoring-indicators.md).
* Informationen zum Austauschen fehlerhafter Hardwarekomponenten finden Sie unter [Austausch von StorSimple-Hardwarekomponenten](storsimple-hardware-component-replacement.md).
* Sollten weiterhin Geräteprobleme auftreten, [wenden Sie sich an den Microsoft-Support](storsimple-contact-microsoft-support.md).

<!---HONumber=AcomDC_0831_2016-->