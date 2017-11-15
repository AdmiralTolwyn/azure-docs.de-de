---
title: "Ändern der StorSimple-Gerätekonfiguration | Microsoft Docs"
description: "Beschreibt, wie der StorSimple Manager-Dienst dazu verwendet werden kann, ein StorSimple-Gerät neu zu konfigurieren, das bereits bereitgestellt wurde."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: bb679264-8d46-429c-9ef7-630dc3b4a423
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 5025dc2643f9e57c0d8fa919b7d601f184d74431
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-modify-your-storsimple-device-configuration"></a>Verwenden des StorSimple Manager-Diensts, um eine StorSimple-Gerätekonfiguration zu ändern
> [!NOTE]
> Das klassische Portal für StorSimple ist veraltet. Ihre StorSimple-Geräte-Manager werden gemäß dem Zeitplan für die Abschaltung automatisch in das neue Azure-Portal verschoben. Sie erhalten zu dieser Verschiebung eine E-Mail und eine Portalbenachrichtigung. Dieses Dokument wird ebenfalls bald entfernt. Die Version dieses Artikel für das neue Azure-Portal finden Sie unter [Verwenden des StorSimple-Geräte-Manager-Diensts zum Ändern der StorSimple-Gerätekonfiguration](storsimple-8000-modify-device-config.md). Antworten auf Fragen zu dieser Verschiebung finden Sie unter [Verschieben des StorSimple Device Manager-Diensts vom klassischen Portal in das Azure-Portal: häufig gestellte Fragen (FAQ)](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Übersicht
Die Seite **Konfigurieren** im klassischen Azure-Portal enthält alle Geräteparameter, die Sie für ein StorSimple-Gerät neu konfigurieren können, das über einen StorSimple Manager-Dienst verwaltet wird. In diesem Tutorial wird erklärt, wie Sie über die Seite **Konfigurieren** die folgenden gerätebezogenen Aufgaben ausführen:

* Ändern von Geräteeinstellungen 
* Ändern von Zeiteinstellungen 
* Ändern von DNS-Einstellungen 
* Ändern von Netzwerkschnittstellen
* Austauschen oder Neuzuweisen von IP-Adressen

## <a name="modify-device-settings"></a>Ändern von Geräteeinstellungen
Die Geräteeinstellungen umfassen den Anzeigenamen des Geräts und die Gerätebeschreibung.

> [!NOTE] 
> Der Gerätename kann im klassischen Azure-Portal nicht geändert werden. Das Umbenennen des Geräts wird nicht unterstützt.

Einem StorSimple-Gerät, das mit dem StorSimple-Manager-Dienst verbunden ist, ist ein Standardname zugewiesen. Der Standardname basiert üblicherweise auf der Seriennummer des Geräts. Beispielsweise kennzeichnet ein aus 15 Zeichen bestehender Standardgerätename, etwa 8600-SHX0991003G44HT, Folgendes:

* **8600** – Kennzeichnet das Gerätemodell.
* **SHX** – Kennzeichnet den Fertigungsstandort.
* **0991003** – Kennzeichnet ein bestimmtes Produkt.
* **G44HT**– Die letzten 5 Ziffern werden inkrementiert, um eindeutige Seriennummern zu erstellen. Dies ist möglicherweise keine sequenzielle Reihe.

Sie können eine Gerätebeschreibung angeben. Eine Gerätebeschreibung erleichtert es üblicherweise, den Besitzer und den physischen Standort des Geräts zu identifizieren. Das Beschreibungsfeld muss weniger als 256 Zeichen enthalten.

## <a name="modify-time-settings"></a>Ändern von Zeiteinstellungen
Ihr Gerät muss die Zeit synchronisieren, damit es sich beim Cloudspeicher-Dienstanbieter authentifizieren kann. Wählen Sie eine Zeitzone in der Dropdownliste aus, und geben Sie bis zu zwei NTP-Server (Network Time Protocol) an. Der primäre NTP-Server ist erforderlich und wird angegeben, wenn Sie das Gerät über Windows PowerShell für StorSimple konfigurieren. Sie können den standardmäßigen Windows-Server **time.windows.com** als Ihren NTP-Server angeben. Sie können die Konfiguration des primären NTP-Servers zwar über das klassische Azure-Portal anzeigen, wenn Sie die Konfiguration ändern möchten, müssen Sie aber die Windows PowerShell-Schnittstelle verwenden.

Die Konfiguration des sekundären NTP-Servers ist optional. Sie können mit dem klassischen Azure-Portal einen sekundären NTP-Server konfigurieren. 

Wenn Sie den NTP-Server konfigurieren, müssen Sie sich vergewissern, dass Ihr Netzwerk zulässt, dass der NTP-Datenverkehr von Ihrem Rechenzentrum in das Internet fließt. Wenn Sie einen öffentlichen NTP-Server angeben, müssen Sie sicherstellen, dass Ihre Netzwerkfirewalls und anderen Sicherheitseinrichtungen so konfiguriert sind, dass NTP-Datenverkehr in das externe und aus dem externen Netzwerk fließen kann. Wenn bidirektionaler NTP-Datenverkehr nicht zugelassen ist, müssen Sie einen internen NTP-Server verwenden (ein Windows-Domänencontroller bietet diese Funktion). Wenn Ihr Gerät die Zeit nicht synchronisieren kann, kann es möglicherweise nicht mit Ihrem Cloudspeicheranbieter kommunizieren.

Um eine Liste der öffentlichen NTP-Server anzuzeigen, wechseln Sie zu [NTP.Servers Web](http://support.ntp.org/bin/view/Servers/WebHome). 

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Was passiert, wenn das Gerät in einer anderen Zeitzone bereitgestellt wird?
Wenn das Gerät in einer anderen Zeitzone bereitgestellt wird, wird die Zeitzone des Geräts geändert. Angesichts der Tatsache, dass alle Sicherungsrichtlinien die Zeitzone des Geräts verwenden, werden die Sicherungsrichtlinien automatisch entsprechend der neuen Zeitzone angepasst. Es ist kein Benutzereingriff erforderlich.

## <a name="modify-dns-settings"></a>Ändern von DNS-Einstellungen
Ein DNS-Server wird verwendet, wenn das Gerät versucht, mit dem Cloudspeicher-Dienstanbieter zu kommunizieren. Für eine hohe Verfügbarkeit müssen bei der ersten Gerätebereitstellung sowohl den primären als auch den sekundäre DNS-Server konfigurieren. Um den primären DNS-Server neu zu konfigurieren, müssen Sie Windows PowerShell-Schnittstelle auf dem StorSimple-Gerät verwenden.

Um den sekundären DNS-Server zu ändern, können Sie das klassische Azure-Portal verwenden.

## <a name="modify-network-interfaces"></a>Ändern von Netzwerkschnittstellen
Ihr Gerät hat sechs Netzwerkschnittstellen, von denen vier 1-GbE- und zwei 10-GbE-Schnittstellen sind. Diese Schnittstellen sind mit DATA 0 bis DATA 5 gekennzeichnet. DATA 0, DATA 1, DATA 4 und DATA 5 sind 1-GbE-Netzwerkschnittstellen, DATA 2 und DATA 3 sind 10-GbE-Netzwerkschnittstellen.

Konfigurieren Sie die **Netzwerkschnittstelleneinstellungen** für jede der zu verwendenden Schnittstellen. Um hohe Verfügbarkeit zu gewährleisten, sollte Ihr Gerät mindestens zwei iSCSI-Schnittstellen und zwei cloudfähige Schnittstellen haben. Es ist zwar nicht erforderlich, empfiehlt sich aber, nicht verwendete Schnittstellen zu deaktivieren.

Wenn Sie eine der Netzwerkschnittstellen konfigurieren, müssen Sie eine virtuelle IP-Adresse (VIP) konfigurieren.

DATA 0 ist standardmäßig cloudfähig. Wenn Sie DATA 0 konfigurieren, müssen Sie auch zwei feste IP-Adressen konfigurieren, eine für jeden Controller. Diese festen IP-Adressen können verwendet werden, um direkt auf die Gerätecontroller zuzugreifen, und sind nützlich, wenn Sie Updates auf dem Gerät installieren oder zur Problembehandlung auf die Controller zugreifen möchten.

In StorSimple 8000 Series Update 1 ist die Routingmetrik von DATA 0 auf den niedrigsten Wert festgelegt. Daher wird, wenn Ihr Gerät unter StorSimple 8000 Series Update 1 ausgeführt wird, der gesamte Clouddatenverkehr über DATA 0 weitergeleitet. Merken Sie sich dies, wenn Ihr StorSimple-Gerät mehrere cloudfähige Netzwerkschnittstellen hat.

> [!NOTE]
> Die festen IP-Adressen für den Controller werden dazu verwendet, die Updates für das Gerät vorzunehmen. Aus diesem Grund müssen die festen IP-Adressen routingfähig sein und eine Verbindung mit dem Internet herstellen können.
> 
> 

Für jede Netzwerkschnittstelle werden die folgenden Parameter angezeigt:

* **Geschwindigkeit** – Dieser Parameter ist nicht benutzerkonfigurierbar. DATA 0, DATA 1, DATA 4 und DATA 5 sind immer 1-GbE-Schnittstellen, und DATA 2 und DATA 3 sind 10-GbE-Schnittstellen.
  
  > [!NOTE]
  > Geschwindigkeit und Duplexmodus werden immer automatisch ausgehandelt. Großrahmen werden nicht unterstützt.
  > 
  > 
* **Schnittstellenstatus** – Eine Schnittstelle kann aktiviert oder deaktiviert sein. Wenn sie aktiviert ist, versucht das Gerät, die Schnittstelle zu verwenden. Es wird empfohlen, dass nur die Schnittstellen aktiviert werden, die mit dem Netzwerk verbunden sind und verwendet werden. Deaktivieren Sie alle Schnittstellen, die Sie nicht verwenden.
* **Schnittstellentyp** – Über diesen Parameter können Sie iSCSI-Datenverkehr und Cloudspeicher-Datenverkehr voneinander trennen. Der Parameter kann eine der folgenden Einstellungen haben:
  
  * **Cloud aktiviert** – Ist diese Einstellung aktiviert, verwendet das Gerät diese Schnittstelle, um mit der Cloud zu kommunizieren.
  * **iSCSI-aktiviert** – Ist diese Einstellung aktiviert, verwendet das Gerät diese Schnittstelle, um mit dem iSCSI-Host zu kommunizieren.
    
    Es wird empfohlen, dass Sie iSCSI-Datenverkehr und Cloudspeicher-Datenverkehr voneinander trennen. Beachten Sie auch, Sie kein Gateway zuweisen müssen, wenn sich der Host und Ihr Gerät im selben Subnetz befinden. Befindet sich der Host aber in einem anderen Subnetz als Ihr Gerät, müssen Sie ein Gateway zuweisen.
* **IP-Adresse** – Diese kann IPv4 oder IPv6 oder beides sein. Für die Netzwerkschnittstellen eines Geräts werden sowohl die IPv4- als auch die IPv6-Adressenfamilie unterstützt. Wenn Sie IPv4 verwenden, geben Sie eine 32-Bit-IP-Adresse (*xxx.xxx.xxx.xxx*) in Dezimalschreibweise mit Punkten an. Wenn Sie IPv6 verwenden, geben Sie einfach ein Präfix aus vier Ziffern an. Ausgehend von diesem Präfix wird dann automatisch eine 128-Bit-Adresse für die Netzwerkschnittstelle Ihres Geräts generiert.
* **Subnetz** – Dieser Parameter bezieht sich auf die Subnetzmaske und wird über die Windows PowerShell-Schnittstelle konfiguriert.
* **Gateway** – Dies ist das Standardgateway, das von dieser Schnittstelle verwendet werden muss, wenn sie versucht, mit Knoten zu kommunizieren, die sich nicht im selben IP-Adressbereich (Subnetz) befinden. Das Standardgateway muss sich im selben Adressbereich (Subnetz) wie die durch die Subnetzmaske bestimmte IP-Adresse der Schnittstelle befinden.
* **Feste IP-Adresse** – Dieses Feld steht nur beim Konfigurieren der DATA 0-Schnittstelle zur Verfügung. Für Vorgänge wie etwa Updates oder Problembehandlung für das Gerät müssen Sie möglicherweise eine direkte Verbindung mit dem Gerätecontroller herstellen. Die feste IP-Adresse kann dazu verwendet werden, sowohl auf den aktiven als auch auf den passiven Controller auf Ihrem Gerät zuzugreifen.

Sie können Controller 0 und Controller 1 über das klassische Azure-Portal neu konfigurieren.

> [!NOTE]
> * Um ordnungsgemäßen Betrieb zu gewährleisten, sollten Sie die Schnittstellengeschwindigkeit und den Duplexmodus an dem Switch überprüfen, mit dem jede Geräteschnittstelle verbunden ist. Switchschnittstellen müssen entweder Gigabit-Ethernet (1000 MBit/s) aushandeln oder für Gigabit-Ethernet konfiguriert sein und im Vollduplexmodus arbeiten. Schnittstellen, die mit niedrigeren Geschwindigkeiten oder im Halbduplexmodus arbeiten, führen zu Leistungsproblemen.
> * Um Störungen und Ausfallzeiten zu minimieren, sollten Sie „Portfast“ für jeden der Switch-Ports aktivieren, mit denen die iSCSI-Netzwerkschnittstelle Ihres Geräts verbunden werden soll. Dadurch wird sichergestellt, dass die Netzwerkverbindungen im Fall eines Failovers schnell hergestellt werden können.
> 
> 

## <a name="swap-or-reassign-ips"></a>Austauschen oder Neuzuweisen von IP-Adressen
Derzeit führt ein Controller, wenn einer Netzwerkschnittstelle auf ihm eine VIP-Adresse zugeordnet ist, die verwendet wird (durch dasselbe Gerät oder ein anderes Gerät im Netzwerk), ein Failover aus. Daher müssen Sie die ordnungsgemäße Vorgehensweise befolgen, wenn Sie virtuelle IP-Adressen (VIPs) für die Netzwerkschnittstelle eines Geräts austauschen, denn andernfalls ergibt sich eine Situation mit doppelten IP-Adressen.

Führen Sie die folgenden Schritte aus, wenn Sie die VIPs für eine der Netzwerkschnittstellen austauschen oder neu zuweisen möchten:

#### <a name="to-reassign-ips"></a>So weisen Sie IP-Adressen zu
1. Löschen Sie die IP-Adresse für beide Schnittstellen.
2. Nachdem die IP-Adressen gelöscht sind, weisen Sie die neuen IP-Adressen den entsprechenden Schnittstellen zu.

## <a name="next-steps"></a>Nächste Schritte
* Informationen zum [Konfigurieren von MPIO für Ihr StorSimple-Gerät](storsimple-configure-mpio-windows-server.md).
* Informationen zum [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple Manager-Diensts](storsimple-manager-service-administration.md).

