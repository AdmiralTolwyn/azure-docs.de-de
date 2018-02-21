---
title: Neuigkeiten in Azure Machine Learning | Microsoft-Dokumentation
description: "Neue Features, die in Azure Machine Learning verfügbar sind."
services: machine-learning
documentationcenter: 
author: garyericson
manager: raymondl
editor: 
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.author: raymondl
ms.openlocfilehash: 0e97a8906bf0e5ea790725efbef16b883138c87a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2018
---
# <a name="whats-new-in-azure-machine-learning"></a>Neuerungen in Azure Machine Learning

### <a name="the-march-2017-release-of-microsoft-azure-machine-learning-updates-provides-the-following-feature"></a>Die Updates für Microsoft Azure Machine Learning (ML) vom März 2017 bieten das folgende Feature:



* Dedizierte Kapazität für Azure Machine Learning-BES-Aufträge

    Die Machine Learning Batch-Pool-Verarbeitung verwendet den [Azure Batch-Dienst](../../batch/batch-technical-overview.md), um kundenverwaltete Skalierung für den Azure Machine Learning-Batchausführungsdienst zu ermöglichen. Die Batch Pool-Verarbeitung ermöglicht Ihnen das Erstellen von Azure Batch-Pools, an die Sie Batchaufträge senden und diese auf vorhersagbare Weise ausführen können.

    Weitere Informationen finden Sie unter [Azure Batch-Dienst für Machine Learning-Aufträge](dedicated-capacity-for-bes-jobs.md).


### <a name="the-august-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>Die Updates für Microsoft Azure Machine Learning (ML) vom August 2016 bieten die folgenden Features:
* Klassische Webdienste können nun im neuen [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/)-Portal verwaltet werden, das als Zentrale für die Verwaltung aller Aspekte Ihres Webdiensts fungiert.    
  * Ein Webdienst für [Nutzungsstatistik](manage-new-webservice.md) wird bereitgestellt.
  * Das Testen von Remote-Request-Aufrufen in Azure Machine Learning, die Beispieldaten verwenden, wird vereinfacht.
  * Es gibt eine neue Batch Execution Service-Testseite mit Beispieldaten und Auftragsübermittlungsverlauf.
  * Einfachere Verwaltung von Endpunkten.

### <a name="the-july-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>Die Updates für Microsoft Azure Machine Learning (ML) vom Juli 2016 bieten die folgenden Features:
* Webdienste werden jetzt als Azure-Ressourcen über [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) -Schnittstellen verwaltet, wodurch sich folgende Verbesserungen und Erweiterungen ergeben:
  * Es gibt neue [REST-APIs](https://msdn.microsoft.com/library/azure/Dn950030.aspx) zum Bereitstellen und Verwalten Ihrer Resource Manager-basierten Webdienste.
  * Es gibt ein neues [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/)-Portal, das als Zentrale zum Verwalten aller Aspekte Ihres Webdiensts fungiert.
* Es gibt ein neues abonnementbasiertes Bereitstellungsmodell für Webdienste für mehrere Regionen, das Resource Manager-basierte APIs verwendet, die den Resource Manager-Ressourcenanbieter für Webdienste nutzen.
* Es gibt neue [Preispläne](https://azure.microsoft.com/pricing/details/machine-learning/) und Planverwaltungsfunktionen, die den neuen Resource Manager-Ressourcenanbieter für die Abrechnung nutzen.
  * Sie können nun [Ihren Webdienst in mehreren Regionen bereitstellen](how-to-deploy-to-multiple-regions.md) , ohne für jede Region ein Abonnement erstellen zu müssen.
* Es wird ein Webdienst für [Verwendungsstatistiken](manage-new-webservice.md)bereitgestellt.
* Das Testen von Remote-Request-Aufrufen in Azure Machine Learning, die Beispieldaten verwenden, wird vereinfacht.
* Es gibt eine neue Batch Execution Service-Testseite mit Beispieldaten und Auftragsübermittlungsverlauf.

Darüber hinaus wurde Machine Learning Studio so aktualisiert, dass Sie die Bereitstellung gemäß dem neuen Webdienstmodell oder weiter gemäß dem klassischen Webdienstmodell vornehmen können. 

