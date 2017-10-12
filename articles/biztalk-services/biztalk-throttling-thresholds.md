---
title: Informationen zur Drosselung in BizTalk Services | Microsoft Docs
description: "Erfahren Sie mehr über Drosselungsschwellenwerte und das daraus resultierende Laufzeitverhalten für BizTalk Services. Die Drosselung basiert auf der Arbeitsspeicherauslastung und der Nachrichtenanzahl. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: f6663cf2-cda4-4bac-855e-27d2ad5c4fa4
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 145e7470bbc01c676a1fb5856c0f9a8726e667fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="biztalk-services-throttling"></a>BizTalk Services: Drosselung

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Azure BizTalk Services implementiert die Dienstdrosselung basierend auf zwei Bedingungen: Arbeitsspeicherauslastung und Anzahl der gleichzeitig verarbeiteten Nachrichten. In diesem Thema werden die Drosselungsschwellenwerte aufgelistet und das Laufzeitverhalten beim Auftreten einer Drosselungsbedingung beschrieben.

## <a name="throttling-thresholds"></a>Drosselungsschwellenwerte
In der folgenden Tabelle sind die Drosselungsquelle und die -schwellenwerte aufgelistet:

|  | Beschreibung | Niedriger Schwellenwert | Hoher Schwellenwert |
| --- | --- | --- | --- |
| Arbeitsspeicher |% des verfügbaren Gesamtsystemarbeitsspeichers/PageFileBytes. <p><p>Verfügbarer PageFileBytes-Gesamtwert beträgt etwa das Zweifache des RAM des Systems. |60 % |70 % |
| Nachrichtenverarbeitung |Anzahl der simultan verarbeiteten Nachrichten |40 * Anzahl der Kernspeicher |100 * Anzahl der Kernspeicher |

Wenn ein hoher Schwellenwert erreicht ist, beginnt Azure BizTalk Services mit der Drosselung. Die Drosselung wird beendet, wenn ein niedriger Schwellenwert erreicht wird. Der Dienst nutzt beispielsweise 65 % des Systemarbeitsspeichers. In dieser Situation führt der Dienst keine Drosselung durch. Der Dienst beginnt damit, wenn 70 % des Systemarbeitsspeichers genutzt werden. In dieser Situation führt der Dienst eine Drosselung durch und setzt diese fort, bis der Dienst 60 % (niedriger Schwellenwert) des Systemarbeitsspeichers nutzt.

Azure BizTalk Services verfolgen den Drosselungsstatus (normaler Status vs. gedrosselter Status) und die Drosselungsdauer.

## <a name="runtime-behavior"></a>Laufzeitverhalten
Wenn Azure BizTalk Services einen Drosselungsstatus erreichen, tritt Folgendes ein:

* Die Drosselung wird pro Rolleninstanz durchgeführt. Beispiel:<br/>
  RoleInstanceA steht für Drosselung. RoleInstanceB wird nicht gedrosselt. In dieser Situation werden die Nachrichten in RoleInstanceB erwartungsgemäß verarbeitet. Die Nachrichten in RoleInstanceA werden verworfen und schlagen mit dem folgenden Fehler fehl:<br/><br/>
  **Der Server ist ausgelastet. Bitte versuchen Sie es später noch einmal.**<br/><br/>
* Keine der Pullquellen ruft eine Nachricht ab oder lädt eine herunter. Beispiel:<br/>
  Eine Pipeline ruft Nachrichten per Pullaktion aus einer externen FTP-Quelle ab. Die Rolleninstanz, welche die Pullaktion durchführt, geht in einen Drosselungsstatus über. In dieser Situation setzt die Pipeline das Herunterladen zusätzlicher Nachrichten aus, bis die Rolleninstanz die Drosselung beendet.
* Eine Antwort wird an den Client gesendet, so dass dieser die Nachricht neu senden kann.
* Sie müssen solange warten, bis die Drosselung aufgelöst ist. Insbesondere müssen Sie warten, bis ein niedriger Schwellenwert erreicht ist.

## <a name="important-notes"></a>Wichtige Hinweise
* Die Drosselung kann nicht deaktiviert werden.
* Die Drosselungsschwellenwerte können nicht modifiziert werden.
* Die Drosselung ist systemweit implementiert.
* Der Azure SQL-Datenbankserver verfügt ebenfalls über eine integrierte Drosselung.

## <a name="additional-azure-biztalk-services-topics"></a>Zusätzliche Azure BizTalk Services-Themen
* [Installieren des Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Lernprogramme: Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Wie verwende ich das Azure BizTalk Services SDK?](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [BizTalk Services-Dokumentation](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Weitere Informationen
* [BizTalk Services: Editionsübersicht](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk Services: Bereitstellen mithilfe des klassischen Azure-Portals](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [BizTalk Services: Bereitstellungsstatusübersicht](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services: Sichern und Wiederherstellen](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services: Name und Schlüssel des Ausstellers](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>

