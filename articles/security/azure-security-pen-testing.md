---
title: Penetrationstests | Microsoft Docs
description: "Der Artikel bietet einen Überblick über den Penetrationstestprozess und dazu, wie Sie Penetrationstests an Ihren in der Azure-Infrastruktur ausgeführten Apps durchführen können."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: yurid
ms.openlocfilehash: 070e848f753452953b9e5dfe94799e7c0a314530
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/29/2017
---
# <a name="pen-testing"></a>Penetrationstests
Einer der größten Vorteile von Microsoft Azure bei Test und Bereitstellung von Anwendungen ist, dass Sie keine lokale Infrastruktur zum Entwickeln, Testen und Bereitstellen der Anwendungen zusammenstellen müssen. Die Microsoft Azure-Plattformdienste stellen die gesamte Infrastruktur bereit. Sie müssen sich keine Gedanken über Anforderung, Erwerb und Aufbau eigener lokaler Hardware machen.

Das ist hervorragend – Sie müssen jedoch noch sicherstellen, dass Sie mit angemessener Sorgfalt für Sicherheit sorgen. Sie müssen u.a. einen Penetrationstest der Anwendungen durchführen, die Sie in Azure bereitstellen.

Möglicherweise wissen Sie bereits, dass Microsoft einen [Penetrationstests der Azure-Umgebung](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)durchführt. Dies hilft uns, unsere Plattform zu verbessern, und ist wegweisend für unsere Aktionen im Hinblick auf die Verbesserung der Sicherheitskontrollen, die Einführung neuer Sicherheitskontrollen und die Verbesserung unserer Sicherheitsprozesse.

Wir führen keinen Penetrationstest Ihrer Anwendung durch, aber wir verstehen, dass Sie Penetrationstests Ihrer eigenen Anwendungen durchführen möchten und müssen. Das ist eine gute Sache, denn wenn Sie die Sicherheit Ihrer Anwendungen verbessern, machen Sie das gesamte Azure-Ökosystem sicherer.

Wenn Sie einen Penetrationstest Ihrer Anwendungen durchführen, kann dies von unserer Seite aus wie ein Angriff aussehen. Wir [überwachen kontinuierlich](http://blogs.msdn.com/b/azuresecurity/archive/2015/07/05/best-practices-to-protect-your-azure-deployment-against-cloud-drive-by-attacks.aspx), ob Angriffsmuster auftreten, und initiieren ggf. einen Incidentreaktionsprozess. Es hilft weder Ihnen noch uns, wenn wir aufgrund Ihrer im Rahmen angemessener Sorgfalt durchgeführten Penetrationstests einen Sicherheitsvorfall-Reaktionsprozess initiieren.

Vorgehensweise

Wenn Sie bereit sind, einen Penetrationstest für Ihre in Azure gehosteten Anwendungen durchzuführen, können Sie [uns darüber informieren](https://portal.msrc.microsoft.com/en-us/engage/pentest). Wenn wir wissen, dass Sie bestimmte Tests durchführen möchten, können wir sicherstellen, dass diese reibungslos ablaufen (und nicht beispielsweise die IP-Adresse blockiert wird, die Sie bei Ihrem Test verwenden) – vorausgesetzt, Ihre Tests erfüllen die unter [Microsoft Cloud Unified Penetration Testing Rules of Engagement](https://technet.microsoft.com/en-us/mt784683) (Microsoft Cloud: Einheitliche Verhaltensregeln bei Penetrationstests) beschriebenen Bedingungen für Azure-Penetrationstests.
Folgende Standardtests können Sie durchführen:

* Tests an Ihren Endpunkten, um die [wichtigsten 10 OWASP-Sicherheitsrisiken (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Fuzzing](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) Ihrer Endpunkte
* [Portüberwachung](https://en.wikipedia.org/wiki/Port_scanner) Ihrer Endpunkte

Ein Testtyp, den Sie nicht ausführen können, ist jegliche Form eines [Denial-of-Service-Angriffs (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack). Dies schließt einen selbst initiierten DoS-Angriff bzw. das Durchführen zugehöriger Tests ein, die einen DoS-Angriff bestimmen, demonstrieren oder simulieren könnten.

Sind Sie bereit für Penetrationstests Ihrer in Microsoft Azure gehosteten Anwendungen? Wenn dies der Fall ist, wechseln Sie zur [Penetrationstest-Übersichtsseite](https://technet.microsoft.com/library/mt784683.aspx) (und klicken Sie am unteren Rand der Seite auf die Schaltfläche „Create a Testing Request“ ). Außerdem finden Sie weitere Informationen zu den Bedingungen für Penetrationstests und nützliche Links dazu, wie Sie Sicherheitsprobleme im Zusammenhang mit Azure oder einem anderen Microsoft-Dienst berichten können.
