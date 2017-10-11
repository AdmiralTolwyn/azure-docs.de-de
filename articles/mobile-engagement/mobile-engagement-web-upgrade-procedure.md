---
title: "Upgradeverfahren für das Azure Mobile Engagement Web SDK | Microsoft Docs"
description: "Die neuesten Updates und Verfahren für das Web SDK für Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a20529b4-ec8d-4503-8ae9-09b5f0846d5b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
ms.openlocfilehash: afa8037dcb7a53042fa606e2c4014b442d4be326
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2017
---
# <a name="azure-mobile-engagement-web-sdk-upgrade-procedures"></a>Upgradeverfahren für das Azure Mobile Engagement Web SDK
Wenn Sie bereits eine frühere Version des Azure Mobile Engagement Web SDK in Ihre Webanwendung integriert haben, müssen Sie bei einem Upgrade des SDK die folgenden Punkte beachten.

Wenn Sie mehrere Versionen des Mobile Engagement Web SDK übersprungen haben, müssen Sie während des Upgrades ggf. mehrere Verfahren ausführen. Wenn Sie beispielsweise von 1.4.0 zu 1.6.0 migrieren, führen Sie erst die Verfahren für das Upgrade von 1.4.0 auf 1.5.0 durch. Führen Sie anschließend die Verfahren für das Upgrade von 1.5.0 auf 1.6.0 durch.

Ersetzen Sie unabhängig von der Ausgangsversion des Upgrades alle früheren Versionen der Datei „azure-engagement.js“ durch die neueste Version der Datei.

## <a name="upgrade-from-121-to-200"></a>Upgrade von 1.2.1 auf 2.0.0
In diesem Abschnitt wird beschrieben, wie Sie die Migration des Mobile Engagement Web SDK vom Capptain-Dienst, die von Capptain SAS angeboten wird, in eine Azure Mobile Engagement-App durchführen. Wenn eine Migration von einer früheren Version erfolgt, sehen Sie auf der Capptain-Website nach, wie zunächst eine Migration auf Version 1.2.1 erfolgt. Führen Sie anschließend die folgenden Verfahren aus.

Diese Version des Mobile Engagement Web SDK unterstützt nicht Samsung Smart TV, Opera TV, webOS und das „Reach“-Feature.

> [!IMPORTANT]
> Capptain und Azure Mobile Engagement sind nicht im selben Dienst. Das folgende Verfahren veranschaulicht nur das Migrieren der Client-App. Bei der Migration des Mobile Engagement Web SDK in die App werden Ihre Daten nicht von einem Capptain-Server auf einen Mobile Engagement-Server migriert.
> 
> 

### <a name="javascript-files"></a>JavaScript-Dateien
Ersetzen Sie die Datei „capptain-sdk.js“ durch die Datei „Azure-engagement.js“, und aktualisieren Sie Ihre Skriptimporte entsprechend.

### <a name="remove-capptain-reach"></a>Entfernen von Capptain Reach
Diese Version des Mobile Engagement Web SDK unterstützt nicht das „Reach“-Feature. Wenn Sie Capptain Reach in Ihre Anwendung integriert haben, müssen Sie dieses Feature entfernen.

Entfernen Sie den Reach-CSS-Import von Ihrer Seite, und löschen Sie die zugehörige CSS-Datei (standardmäßig „capptain-reach.css“).

Löschen Sie die folgenden Reach-Ressourcen: Das Symbol „Schließen“ (standardmäßig „capptain-close.png“) und das Markensymbol (standardmäßig „capptain-notification-icon“).

Entfernen Sie die Reach-Benutzeroberfläche für In-App-Benachrichtigungen. Das Standardlayout sieht folgendermaßen aus:

    <!-- capptain notification -->
    <div id="capptain_notification_area" class="capptain_category_default">
      <div class="icon">
        <img src="capptain-notification-icon.png" alt="icon" />
      </div>
      <div class="content">
        <div class="title" id="capptain_notification_title"></div>
        <div class="message" id="capptain_notification_message"></div>
      </div>
      <div id="capptain_notification_image"></div>
      <div>
        <button id="capptain_notification_close">Close</button>
      </div>
    </div>

Entfernen Sie die Reach-Benutzeroberfläche für Text- und Webankündigungen und Umfragen. Das Standardlayout sieht folgendermaßen aus:

    <div id="capptain_overlay" class="capptain_category_default">
      <button id="capptain_overlay_close">x</button>
      <div id="capptain_overlay_title"></div>
      <div id="capptain_overlay_body"></div>
      <div id="capptain_overlay_poll"></div>
      <div id="capptain_overlay_buttons">
        <button id="capptain_overlay_exit"></button>
        <button id="capptain_overlay_action"></button>
      </div>
    </div>

Entfernen Sie das `reach` -Objekt aus Ihrer Konfiguration, sofern vorhanden. Es sieht folgendermaßen aus:

    window.capptain = {
      [...]
      reach: {
        [...]
      }
    }

Entfernen Sie andere Reach-Anpassungen, z.B. Kategorien.

### <a name="remove-deprecated-apis"></a>Entfernen veralteter APIs
Einige der APIs aus Capptain sind im Mobile Engagement Web SDK veraltet.

Entfernen Sie alle Aufrufe der folgenden APIs: `agent.connect`, `agent.disconnect`, `agent.pause` und `agent.sendMessageToDevice`.

Entfernen Sie allen Vorkommen der folgenden Rückrufe aus Ihrer Capptain-Konfiguration: `onConnected`, `onDisconnected`, `onDeviceMessageReceived` und `onPushMessageReceived`.

### <a name="configuration"></a>Konfiguration
Mobile Engagement verwendet eine Verbindungszeichenfolge zum Konfigurieren der SDK-Bezeichner wie beispielsweise dem Anwendungsbezeichner.

Ersetzen Sie die Anwendungs-ID durch Ihre Verbindungszeichenfolge. Beachten Sie, dass sich das globale Objekt für die SDK-Konfiguration von `capptain` in `azureEngagement` ändert.

Vor der Migration:

    window.capptain = {
      appId: ...,
      [...]
    };

Nach der Migration:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      [...]
    };

Die Verbindungszeichenfolge für Ihre Anwendung wird im Azure-Portal angezeigt.

### <a name="javascript-apis"></a>JavaScript-APIs
Das globale JavaScript-Objekt `window.capptain` wurde in `window.azureEngagement` umbenannt. Sie können aber den `window.engagement`-Alias für API-Aufrufe verwenden. Sie können den Alias nicht zum Definieren der SDK-Konfiguration verwenden.

Beispiel: `capptain.deviceId` wird `engagement.deviceId`, `capptain.agent.startActivity` wird `engagement.agent.startActivity` usw.

