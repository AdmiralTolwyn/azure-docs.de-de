---
title: Problembehandlung in BizTalk Services mithilfe von Vorgangsprotokollen | Microsoft Docs
description: Problembehandlung in BizTalk Services mithilfe von Vorgangsprotokollen. MABS, WABS
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 1081a9c6-58cc-4a76-8ac8-11e5e7a6ea27
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: c0c83361f94ffd9c30d7fcc551ff4b85ad7d6fa5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="biztalk-services-troubleshoot-using-operation-logs"></a>BizTalk Services: Fehlerbehebung mit Vorgangsprotokollen

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

## <a name="what-are-the-operation-logs"></a>Was sind Vorgangsprotokolle?
Vorgangsprotokolle sind eine Verwaltungsdienstfunktion im klassischen Azure-Portal, mit denen Sie Verlaufsprotokolle zu Vorgängen anzeigen können, die in Ihren Azure-Diensten ausgeführt wurden, einschließlich BizTalk Services. So können Sie die Verlaufsdaten zu Verwaltungsvorgängen für Ihr BizTalk Services-Abonnement bis zu 180 Tage lang anzeigen.

> [!NOTE]
> Diese Funktion erfasst nur Protokolle für Verwaltungsvorgänge in BizTalk Services, beispielsweise wann ein Dienst gestartet oder gesichert wurde usw. Solche Vorgänge werden unabhängig von der Tatsache nachverfolgt, ob sie über das klassische Azure-Portal oder unter Verwendung der [BizTalk Service-REST-APIs](http://msdn.microsoft.com/library/azure/dn232347.aspx)ausgeführt wurden. Eine vollständige Liste der Vorgänge, die mithilfe der Verwaltungsdienste nachverfolgt werden, finden Sie unter [Vorgänge, die mit Azure-Verwaltungsdiensten nachverfolgt werden](#bizops)ausgeführt wurden.<br/><br/>
> Dies umfasst nicht die Protokolle für Aktivitäten bezüglich der BizTalk Services-Laufzeit (beispielsweise Nachrichten, die von Brücken verarbeitet werden usw.). Verwenden Sie im BizTalk Services-Portal die Ansicht "Nachverfolgung", um diese Protokolle anzuzeigen. Weitere Informationen finden Sie unter [Nachverfolgen von Nachrichten](http://msdn.microsoft.com/library/azure/hh949805.aspx).
> 
> 

## <a name="view-biztalk-services-operation-logs"></a>Anzeigen von BizTalk Services-Vorgangsprotokollen
1. Wählen Sie im klassischen Azure-Portal die Option **Verwaltungsdienste** und anschließend die Registerkarte **Vorgangsprotokolle**.
2. Sie können die Protokolle anhand verschiedener Parameter filtern, beispielsweise nach Abonnement, Datumsbereich, Diensttyp (zum Beispiel BizTalk Services), Dienstname oder Vorgangsstatus (erfolgreich oder mit Fehler).
3. Klicken Sie auf das Häkchen, um die gefilterte Liste anzuzeigen. Die folgende Abbildung zeigt Aktivitäten bezüglich testbiztalkservice: ![Anzeigen von Operationsprotokollen][ViewLogs] 
4. Wenn Sie weitere Informationen zu einem bestimmten Vorgang anzeigen möchten, wählen Sie die entsprechende Zeile aus, und klicken Sie unten in der Taskleiste auf **Details** .

## <a name="bizops"></a>Vorgänge, die mit Azure-Verwaltungsdiensten nachverfolgt werden
In der folgenden Tabelle finden Sie Vorgänge, die mit den Azure-Verwaltungsdiensten nachverfolgt werden:

| Vorgangsname | Task |
| --- | --- |
| CreateBizTalkService |Vorgang zur Erstellung eines neuen BizTalk-Dienstes |
| DeleteBizTalkService |Vorgang zum Löschen eines BizTalk-Dienstes |
| RestartBizTalkService |Vorgang zum Neustart eines BizTalk-Dienstes |
| StartBizTalkService |Vorgang zum Starten eines BizTalk-Dienstes |
| StopBizTalkService |Vorgang zum Beenden eines BizTalk-Dienstes |
| DisableBizTalkService |Vorgang zum Deaktivieren eines BizTalk-Dienstes |
| EnableBizTalkService |Vorgang zum Aktivieren eines BizTalk-Dienstes |
| BackupBizTalkService |Vorgang zum Sichern eines BizTalk-Dienstes |
| RestoreBizTalkService |Vorgang zur Wiederherstellung eines BizTalk-Dienstes aus einer bestimmten Sicherungskopie |
| SuspendBizTalkService |Vorgang zum Anhalten eines BizTalk-Dienstes |
| ResumeBizTalkService |Vorgang zum Fortsetzen eines BizTalk-Dienstes |
| ScaleBizTalkService |Vorgang zur Skalierung eines BizTalk-Dienstes nach oben oder unten |
| ConfigUpdateBizTalkService |Vorgang zur Aktualisierung der Konfiguration eines BizTalk-Dienstes |
| ServiceUpdateBizTalkService |Vorgang für ein Upgrade oder Downgrade eines BizTalk-Dienstes auf eine andere Version |
| PurgeBackupBizTalkService |Vorgang zum Bereinigen von Sicherungskopien eines BizTalk-Dienstes nach der Aufbewahrungszeit |

## <a name="see-also"></a>Weitere Informationen
* [Sichern von BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Wiederherstellen von BizTalk Services aus einer Sicherung](http://go.microsoft.com/fwlink/p/?LinkID=325582)
* [BizTalk Services: Editionsübersicht](http://go.microsoft.com/fwlink/p/?LinkID=302279)
* [BizTalk Services: Bereitstellen mithilfe des klassischen Azure-Portals](http://go.microsoft.com/fwlink/p/?LinkID=302280)
* [BizTalk Services: Bereitstellungsstatusübersicht](http://go.microsoft.com/fwlink/p/?LinkID=329870)
* [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"](http://go.microsoft.com/fwlink/p/?LinkID=302281)
* [BizTalk Services: Drosselung](http://go.microsoft.com/fwlink/p/?LinkID=302282)
* [BizTalk Services: Name und Schlüssel des Ausstellers](http://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Wie verwende ich das Azure BizTalk Services SDK?](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png

