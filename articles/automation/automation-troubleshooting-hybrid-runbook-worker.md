---
title: "Problembehandlung für Hybrid Runbook Worker in Azure Automation | Microsoft-Dokumentation"
description: "Hier werden die Symptome, Ursachen und Lösungen für die häufigsten Hybrid Runbook Worker-Probleme in Azure Automation beschrieben."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 02c6606e-8924-4328-a196-45630c2255e9
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: magoedte
ms.openlocfilehash: 75f4ac1bc940a2b1d8e4ac6aeac8b80c642489da
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2017
---
# <a name="troubleshooting-tips-for-hybrid-runbook-worker"></a>Problembehandlungstipps für Hybrid Runbook Worker

Dieser Artikel bietet Unterstützung bei der Problembehandlung von Fehlern, die bei Hybrid Runbook Workern in Automation auftreten können, und enthält mögliche Lösungen zum Beheben dieser Fehler.

## <a name="a-runbook-job-terminates-with-a-status-of-suspended"></a>Ein Runbookauftrag wird mit dem Status „Angehalten“ beendet

Kurz nachdem Sie versucht haben, Ihr Runbook dreimal auszuführen, wird es angehalten. Bestimmte Bedingungen können eine erfolgreiche Ausführung des Runbooks verhindern. Die Fehlermeldung enthält jedoch keine zusätzlichen Angaben zu den Gründen für den Abbruch. In diesem Artikel finden Sie Anweisungen, wie Sie Probleme bei der Runbookausführung mit Hybrid Runbook Worker lösen können.

Suchen Sie in den Azure-Foren zu [MSDN und Stack Overflow](https://azure.microsoft.com/support/forums/), falls Sie Ihr Azure-Problem mit diesem Artikel nicht beheben konnten. Sie können Ihr Problem in diesen Foren veröffentlichen oder auf [@AzureSupportTwitter](https://twitter.com/AzureSupport) an senden. Darüber hinaus können Sie eine Azure-Supportanfrage stellen, indem Sie auf der Website des **Azure-Supports** die Option [Support erhalten](https://azure.microsoft.com/support/options/) auswählen.

### <a name="symptom"></a>Symptom
Bei der Ausführung eines Runbooks tritt ein Fehler auf. Folgende Fehlermeldung wird ausgegeben: „Die Auftragsaktion ‚Aktivieren‘ kann nicht ausgeführt werden, weil der Prozess unerwartet beendet wurde. Es wurde 3 Mal versucht, die Auftragsaktion auszuführen.“

Es gibt mehrere mögliche Ursachen für den Fehler: 

1. Der Hybrid Worker befindet sich hinter einem Proxy oder einer Firewall.
2. Der Computer, auf dem der Hybrid Worker ausgeführt wird, erfüllt die [Mindestanforderungen für die Hardware](automation-offering-get-started.md#hybrid-runbook-worker) nicht vollständig.  
3. Die Runbooks können nicht mit lokalen Ressourcen authentifiziert werden.

#### <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>Ursache 1: Der Hybrid Runbook Worker befindet sich hinter einem Proxy oder einer Firewall
Der Computer, auf dem der Hybrid Runbook Worker ausgeführt wird, befindet sich hinter einer Firewall oder hinter einem Proxyserver, und ein ausgehender Netzwerkzugriff ist möglicherweise nicht zulässig oder nicht ordnungsgemäß konfiguriert.

#### <a name="solution"></a>Lösung
Stellen Sie sicher, dass der Computer über einen ausgehenden Zugriff auf „*.azure-automation.net“ über Port 443 verfügt. 

#### <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>Ursache 2: Der Computer erfüllt die Mindestanforderungen an die Hardware nicht
Computer, auf denen der Hybrid Runbook Worker ausgeführt werden soll, müssen die Mindestanforderungen an die Hardware erfüllen, damit sie als Host für dieses Feature infrage kommen. Andernfalls kommt es – je nach Ressourcennutzung durch andere Hintergrundprozesse und möglichen Konflikten bei der Runbookausführung – zu einer übermäßigen Auslastung des Computers. Dies verursacht Verzögerungen und Timeouts bei Runbookaufträgen. 

#### <a name="solution"></a>Lösung
Vergewissern Sie sich zuerst, dass der für die Ausführung des Hybrid Runbook Worker-Features vorgesehene Computer die Hardwaremindestanforderungen erfüllt.  Wenn dies der Fall ist, überwachen Sie die CPU- und Arbeitsspeicherauslastung, um Zusammenhänge zwischen der Leistung der Hybrid Runbook Worker-Prozesse und Windows zu erkennen.  Falls die Leistungsgrenzen des Arbeitsspeichers oder der CPU fast erreicht werden, deutet dies u.U. darauf hin, dass Sie ein Prozessorupgrade oder weitere Prozessoren oder mehr Speicher benötigen, um den Ressourcenengpass zu beseitigen und den Fehler zu beheben. Alternativ können Sie auch eine andere Computeressource auswählen, die die Mindestanforderungen erfüllt. Bei entsprechend großem Workload skalieren Sie sie nach Bedarf.         

#### <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>Ursache 3: Die Runbooks können nicht mit lokalen Ressourcen authentifiziert werden.

#### <a name="solution"></a>Lösung
Prüfen Sie, ob im Ereignisprotokoll **Microsoft-SMA** ein entsprechendes Ereignis mit der Beschreibung *Win32-Prozess wurde mit dem Code [4294967295] beendet*vorhanden ist.  Die Ursache dieses Fehlers liegt darin, dass Sie in Ihren Runbooks die Authentifizierung nicht konfiguriert haben oder dass Sie die „Ausführen als“-Anmeldeinformationen für die Hybrid Worker-Gruppe nicht angegeben haben.  Überprüfen Sie die [Runbookberechtigungen](automation-hrw-run-runbooks.md#runbook-permissions) , und vergewissern Sie sich, dass Sie die Authentifizierung für Ihre Runbooks ordnungsgemäß konfiguriert haben.  

## <a name="next-steps"></a>Nächste Schritte

Hilfe zur Behandlung anderer Probleme in Automation finden Sie unter [Tipps zur Fehlerbehandlung bei häufigen Fehlern in Azure Automation](automation-troubleshooting-automation-errors.md). 