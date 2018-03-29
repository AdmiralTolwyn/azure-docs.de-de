---
title: Protokollierung für Machine Learning-Webdienste | Microsoft Docs
description: Erfahren Sie, wie Sie die Protokollierung für Machine Learning-Webdienste aktivieren können. Die Protokollierung stellt zusätzliche Informationen zur Problembehandlung von APIs bereit.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/15/2017
ms.openlocfilehash: 345c1b1bd72d3df19f8b087d4cbffd4e25e092ab
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/23/2018
---
# <a name="enable-logging-for-machine-learning-web-services"></a>Aktivieren der Protokollierung für Machine Learning-Webdienste
Dieses Dokument enthält Informationen zur Protokollierungsfunktion von Machine Learning-Webdiensten. Durch die Protokollierung erhalten Sie zusätzliche Informationen, die über die Fehlernummer und eine Meldung hinausgehen und die Ihnen helfen können, Probleme beim Aufrufen der Machine Learning-APIs zu beheben.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Aktivieren der Protokollierung für einen Webdienst

Aktivieren Sie die Protokollierung im [Azure Machine Learning Web Services-Portal](https://services.azureml.net). 

1. Melden Sie sich beim Azure Machine Learning-Webdienstportal unter [https://services.azureml.net](https://services.azureml.net) an. Bei einem klassischen Webdienst gelangen Sie auch zum Portal, indem Sie in Machine Learning Studio auf der Machine Learning-Webdienstseite auf **New Web Services Experience** (Neue Webdienstfunktion) klicken.

   ![Link für neue Webdienstfunktion](./media/web-services-logging/new-web-services-experience-link.png)

2. Klicken Sie auf der oberen Menüleiste bei einem neuen Webdienst auf **Webdienste** und bei einem klassischen Webdienst auf **Classic Web Services** (Klassische Webdienste).

   ![Auswählen neuer oder klassischer Webdienste](./media/web-services-logging/select-web-service.png)

3. Klicken Sie bei einem neuen Webdienst auf den Webdienstnamen. Klicken Sie bei einem klassischen Webdienst auf den Webdienstnamen, und klicken Sie anschließend auf der nächsten Seite auf den entsprechenden Endpunkt.

4. Klicken Sie auf der oberen Menüleiste auf **Konfigurieren**.

5. Legen Sie die Option **Protokollierung aktivieren** auf *Fehler* fest (um nur Fehler zu protokollieren) oder auf *Alle* (für eine vollständige Protokollierung).

   ![Auswählen des Protokolliergrads](./media/web-services-logging/enable-logging.png)

6. Klicken Sie auf **Speichern**.

7. Erstellen Sie für klassische Webdienste den Container **ml-diagnostics**.

   Alle Webdienstprotokolle werden in einem Blobcontainer namens **ml-diagnostics** in dem Speicherkonto aufbewahrt, das dem Webdienst zugeordnet ist. Bei neuen Webdiensten wird dieser Container erstellt, wenn Sie das erste Mal auf den Webdienst zugreifen. Bei klassischen Webdiensten müssen Sie den Container erstellen, wenn er nicht bereits vorhanden ist. 

   1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu dem Speicherkonto, das dem Webdienst zugeordnet ist.

   2. Klicken Sie unter **Blob-Dienst** auf **Container**.

   3. Wenn der Container **ml-diagnostics** nicht vorhanden ist, klicken Sie auf **+Container**, vergeben Sie für den neuen Container den Namen „ml-diagnostics“, und wählen Sie für **Zugriffstyp** die Option „Blob“ aus. Klicken Sie auf **OK**.

      ![Auswählen des Protokolliergrads](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Bei einem klassischen Webdienst finden Sie im Webdienstdashboard in Machine Learning Studio auch einen Schalter, mit dem die Protokollierung aktiviert werden kann. Da die Protokollierung jetzt aber über das Web Services-Portal verwaltet wird, müssen Sie sie wie in diesem Artikel beschrieben über das Portal aktivieren. Wenn Sie die Protokollierung in Studio bereits aktiviert haben, deaktivieren Sie sie im Web Services-Portal, und aktivieren Sie sie erneut.


## <a name="the-effects-of-enabling-logging"></a>Die Auswirkungen der Aktivierung der Protokollierung
Wenn die Protokollierung aktiviert ist, werden die Diagnoseergebnisse und Fehler vom Webdienst-Endpunkt im Blobcontainer **ml-diagnostics** in dem Azure Storage-Konto protokolliert, das mit dem Arbeitsbereich des Benutzers verknüpft ist. Dieser Container enthält die Diagnoseinformationen für alle Webdienst-Endpunkte für die Arbeitsbereiche, die diesem Speicherkonto zugeordnet sind.

Die Protokolle können mit jedem der zum Durchsuchen von Azure Storage-Konten verfügbaren Tools angezeigt werden. Am einfachsten ist es, im Azure-Portal zum Speicherkonto zu navigieren, und dann auf **Container** und anschließend auf **ml-diagnostics** zu klicken.  

## <a name="log-blob-detail-information"></a>Protokollieren von Detailinformationen zu Blobs
Jedes Blob im Container enthält die Diagnoseinformationen für genau eine der folgenden Aktionen:

* Eine Ausführung der Batch-Execution-Methode  
* Eine Ausführung der Request-Response-Methode  
* Initialisierung des Request-Response-Containers

Der Name der einzelnen Blobs weist ein Präfix in der folgenden Form auf: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Dabei hat _Protokolltyp_ einen der folgenden Werte:  

* Batch  
* score/requests  
* score/init  

