---
title: "Erstellen einer Android-App für mobile Azure App Service-Apps | Microsoft Docs"
description: "Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung mobiler Azure-App-Back-Ends für die Android-Entwicklung."
services: app-service\mobile
documentationcenter: android
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34a9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 418a5229a084d570bc6cab5925dbd8d30945a3c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-android-app"></a>Erstellen einer Android-App
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Übersicht
Dieses Lernprogramm zeigt Ihnen, wie Sie einen cloudbasierten Back-End-Dienst mithilfe eines mobilen Azure-App-Back-Ends zu einer mobilen Android-App hinzufügen.  Sie erstellen sowohl ein neues Mobile App-Back-End als auch eine einfache Android-App des Typs *Aufgabenliste*, die App-Daten in Azure speichert.

Das Absolvieren dieses Lernprogramms ist Voraussetzung für alle anderen Android-Lernprogramme zur Verwendung des Features "Mobile Apps" von Azure App Service.

## <a name="prerequisites"></a>Voraussetzungen
Für dieses Tutorial benötigen Sie Folgendes:

* Die [Android Developer Tools](https://developer.android.com/sdk/index.html) mit der integrierten Entwicklungsumgebung Android Studio sowie der neuesten Android-Plattform
* Azure Mobile Android SDK, das automatisch als Teil des heruntergeladenen Schnellstartprojekts referenziert ist
* Ein [aktives Azure-Konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-new-azure-mobile-app-backend"></a>Erstellen eines neuen mobilen Azure-App-Back-Ends
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Konfigurieren des Serverprojekts
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-android-app"></a>Herunterladen und Ausführen der Android-App
[!INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
