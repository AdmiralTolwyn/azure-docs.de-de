---
title: 'Azure Machine Learning-Webdienste: Bereitstellung und Nutzung | Microsoft Docs'
description: Ressourcen zum Bereitstellen und Nutzen von Webdiensten.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 47635376-d1f4-4ea4-a6af-bd1f99f69a69
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: 2d64b007b68db4df652bde4308760400f4de6dbc
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-machine-learning-web-services-deployment-and-consumption"></a>Azure Machine Learning-Webdienste: Bereitstellung und Nutzung
Mit Azure Machine Learning können Sie Machine Learning-Workflows und -Modelle als Webdienste bereitstellen. Diese Webdienste können dann verwendet werden, um die Machine Learning-Modelle in Anwendungen über das Internet aufzurufen und Vorhersagen im Echtzeit- oder Batchmodus zu nutzen. Da die Webdienste RESTful sind, können Sie sie über verschiedene Programmiersprachen und Plattformen wie etwa .NET und Java sowie über Anwendungen wie Excel aufrufen.

Die nächsten Abschnitte enthalten Links zu exemplarischen Vorgehensweisen, Code und Dokumentationen, die Ihnen beim Einstieg helfen.

## <a name="deploy-a-web-service"></a>Bereitstellen eines Webdiensts

### <a name="with-azure-machine-learning-studio"></a>Mit Azure Machine Learning Studio
Mit Machine Learning Studio und dem Microsoft Azure Machine Learning-Webdiensteportal können Sie einen Webdienst bereitstellen und verwalten, ohne Code schreiben zu müssen.

Unter den folgenden Links finden Sie allgemeine Informationen zur Bereitstellung eines neuen Webdiensts:

* Eine Übersicht über das Bereitstellen eines neuen Azure Resource Manager-basierten Webdiensts finden Sie unter [Bereitstellen eines neuen Webdiensts](publish-a-machine-learning-web-service.md).
* Eine exemplarische Vorgehensweise zum Bereitstellen eines Webdiensts finden Sie unter [Bereitstellen eines Azure Machine Learning-Webdiensts](publish-a-machine-learning-web-service.md).
* Eine umfassende exemplarische Vorgehensweise zum Erstellen und Bereitstellen eines Webdiensts finden Sie unter [Exemplarische Vorgehensweise, Schritt 1: Erstellen eines Machine Learning-Arbeitsbereichs](walkthrough-1-create-ml-workspace.md).
* Spezifische Beispiele für das Bereitstellen eines Webdiensts finden Sie hier:

  * [Anleitung Schritt 5: Bereitstellen des Azure Machine Learning-Webdiensts](walkthrough-5-publish-web-service.md)
  * [Gewusst wie: Bereitstellen eines Webdiensts in mehreren Regionen](how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Mit Webdienste-Ressourcenanbieter-APIs (Azure Resource Manager-APIs)
Der Azure Machine Learning-Ressourcenanbieter für Webdienste ermöglicht die Bereitstellung und Verwaltung von Webdiensten mithilfe von REST-API-Aufrufen. Weitere Informationen finden Sie in der [Machine Learning-Webdienst](/rest/api/machinelearning/index)-Referenz (REST).

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->


### <a name="with-powershell-cmdlets"></a>Mit PowerShell-Cmdlets
Der Azure Machine Learning-Ressourcenanbieter für Webdienste ermöglicht die Bereitstellung und Verwaltung von Webdiensten mithilfe von PowerShell-Cmdlets.

Um die Cmdlets verwenden zu können, müssen Sie sich innerhalb Ihrer PowerShell-Umgebung zunächst mithilfe des Cmdlets [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) bei Ihrem Azure-Konto anmelden. Wenn Sie mit dem Aufrufen Resource Manager-basierter PowerShell-Befehle nicht vertraut sind, nutzen Sie die Informationen unter [Verwenden von Azure PowerShell mit Azure Resource Manager](../../azure-resource-manager/powershell-azure-resource-manager.md).

Verwenden Sie [diesen Beispielcode](https://github.com/ritwik20/AzureML-WebServices), um Ihr Vorhersageexperiment zu exportieren. Nachdem Sie die EXE-Datei auf der Grundlage des Codes erstellt haben, können Sie Folgendes eingeben:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Durch Ausführen der Anwendung wird eine JSON-Webdienstvorlage erstellt. Fügen Sie folgende Informationen hinzu, um mithilfe dieser Vorlage einen Webdienst bereitzustellen:

* Speicherkontoname und -schlüssel

    Den Namen und Schlüssel des Speicherkontos können Sie über das [Azure-Portal](https://portal.azure.com/) abrufen.
* Vertragsplan-ID

    Die Plan-ID können Sie über das [Azure Machine Learning-Webdiensteportal](https://services.azureml.net) ermitteln, indem Sie sich anmelden und auf einen Plannamen klicken.

Fügen Sie der JSON-Vorlage die Werte als untergeordnete Elemente des Knotens *Eigenschaften* auf der Ebene des Knotens *MachineLearningWorkspace* hinzu.

Hier sehen Sie ein Beispiel:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Ausführlichere Informationen finden Sie in den folgenden Artikeln sowie im Beispielcode:

* [Azure Machine Learning-Cmdlets](https://msdn.microsoft.com/library/azure/mt767952.aspx) auf MSDN
* [Exemplarische Vorgehensweise](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) auf GitHub

## <a name="consume-the-web-services"></a>Nutzen von Webdiensten
### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Über die Benutzeroberfläche der Azure Machine Learning-Webdienste (Testen)
Sie können Ihren Webdienst über das Azure Machine Learning-Webdiensteportal testen. Dies schließt das Testen der Schnittstellen des Request-Response Service (RRS) und des Stapelausführungsdiensts (Batch Execution Service, BES) ein.

* [Bereitstellen eines neuen Webdiensts](publish-a-machine-learning-web-service.md)
* [Bereitstellen eines Azure Machine Learning-Webdiensts](publish-a-machine-learning-web-service.md)
* [Anleitung Schritt 5: Bereitstellen des Azure Machine Learning-Webdiensts](walkthrough-5-publish-web-service.md)

### <a name="from-excel"></a>Über Excel
Sie können eine Excel-Vorlage zur Nutzung des Webdiensts herunterladen:

* [Verwenden eines Azure Machine Learning-Webdiensts aus Excel](consuming-from-excel.md)
* [Excel-Add-In für Azure Machine Learning-Webdienste](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Über einen REST-basierten Client
Azure Machine Learning-Webdienste sind RESTful-APIs. Diese APIs können über verschiedene Plattformen (.NET, Python, R, Java usw.) genutzt werden. Im [Microsoft Azure Machine Learning-Webdiensteportal](https://services.azureml.net) steht auf der **Nutzungsseite** Ihres Webdiensts Beispielcode zur Verfügung, der Ihnen den Einstieg erleichtert. Weitere Informationen finden Sie unter [Nutzen eines Azure Machine Learning-Webdiensts](consume-web-services.md).
