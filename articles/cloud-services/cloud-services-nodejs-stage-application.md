---
title: Staging einer Clouddienstbereitstellung (Node.js) | Microsoft Docs
description: "Hier erfahren Sie, wie Sie eine Azure-Anwendung durch einen Austausch der virtuellen IP (VIP) zu einer Stagingumgebung und anschließend zu einer Produktionsumgebung bereitstellen."
services: cloud-services
documentationcenter: nodejs
author: craigshoemaker
manager: routlaw
editor: 
ms.assetid: d65d26a6-b424-49cd-a88c-7ef46bb112a8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: cshoe
ms.openlocfilehash: e35bbd31f39722c2e95ce58772dc6bd5eaf839a0
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2017
---
# <a name="staging-an-application-in-azure"></a>Staging von Anwendungen in Azure
Gepackte Anwendungen können in der Stagingumgebung von Azure bereitgestellt werden, um diese zu testen, bevor sie in eine Produktionsumgebung mit Internetzugriff verschoben werden. Die Stagingumgebung entspricht genau der Produktionsumgebung, außer, dass Sie auf die Staginganwendung nur mit einer verborgenen URL zugreifen können, die von Azure generiert wird. Nachdem Sie die korrekte Funktionsweise Ihrer Anwendung geprüft haben, können Sie diese durch einen Austausch der virtuellen IP (VIP) in der Produktionsumgebung bereitstellen.

> [!NOTE]
> Die Schritte in diesem Beitrag gelten nur für Node-Anwendungen, die als Azure-Clouddienst gehostet werden.
> 
> 

## <a name="step-1-stage-an-application"></a>Schritt 1: Staging einer Anwendung
Dieser Abschnitt beschreibt das Staging einer Anwendung mit **Microsoft Azure PowerShell**.

1. Übergeben Sie beim Veröffentlichen eines Dienstes einfach den **-Slot**-Parameter an das **Publish-AzureServiceProject**-Cmdlet.
   
   ```powershell
   Publish-AzureServiceProject -Slot staging
   ```
2. Melden Sie sich am [klassischen Azure-Portal] an, und wählen Sie **Cloud Services**aus. Klicken Sie auf den Dienstnamen, nachdem der Clouddienst erstellt und der Status der **Staging**-Spalte zu **Running** geändert wurde.
   
   ![Portal mit einem laufenden Dienst][cloud-service]
3. Wählen Sie das **Dashboard** aus, und klicken Sie auf **Staging**.
   
   ![Clouddienst-Dashboard][cloud-service-dashboard]
4. Kopieren Sie den Wert unter **Site URL** auf der rechten Seite. Der DNS-Name ist eine versteckte, von Azure generierte interne ID.
   
    ![Site URL][cloud-service-staging-url]

Mit der URL der Staging-Website können Sie nun prüfen, ob Ihre Anwendung in der Stagingumgebung korrekt funktioniert.

## <a name="step-2-upgrade-an-application-in-production-by-swapping-vips"></a>Schritt 2: Upgrade einer Anwendung in die Produktionsumgebung durch Austausch der VIPs
Nachdem Sie die erweiterte Version einer Anwendung in der Stagingumgebung geprüft haben, können Sie diese schnell und einfach in der Produktionsumgebung verfügbar machen, indem Sie die virtuellen IPs (VIPs) von Staging- und Produktionsumgebung austauschen.

> [!NOTE]
> Dieser Schritt geht davon aus, dass Sie bereits eine Anwendung in der Produktionsumgebung bereitgestellt haben und dass sich eine erweiterte Version der Anwendung in der Stagingumgebung befindet.
> 
> 

1. Melden Sie sich beim [klassischen Azure-Portal]an, klicken Sie auf **Cloud Services** , und wählen Sie den Dienstnamen aus.
2. Wählen Sie im **Dashboard** die Option **Staging** aus, und klicken Sie am unteren Seitenrand auf **Austauschen**. Daraufhin öffnet sich der VIP Swap-Dialog.
   
   ![VIP-Swap-Dialogfeld][vip-swap-dialog]
3. Prüfen Sie die Informationen und klicken Sie auf **OK**. Die beiden Bereitstellungen werden aktualisiert, und die Staging-Bereitstellung wechselt in die Produktionsumgebung und umgekehrt.

Sie haben nun eine Bereitstellung in Staging getestet und eine Produktionsbereitstellung durch den Austausch der VIPs durch eine Stagingbereitstellung ersetzt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen
* [Bereitstellen eines Dienstupgrades in der Produktionsumgebung durch Austausch der VIPs in Azure]

[klassischen Azure-Portal]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
[cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
[vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
[Bereitstellen eines Dienstupgrades in der Produktionsumgebung durch Austausch der VIPs in Azure]: cloud-services-how-to-manage.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
