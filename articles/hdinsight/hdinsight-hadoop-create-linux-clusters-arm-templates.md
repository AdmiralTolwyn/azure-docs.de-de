---
title: Erstellen von Hadoop-Clustern mithilfe von Vorlagen – Azure HDInsight | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Cluster für HDInsight mit Azure Resource Manager-Vorlagen erstellen.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00a80dea-011f-44f0-92a4-25d09db9d996
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: ae5085eb52210844b364113a436fa033da9daac7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2018
---
# <a name="create-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Erstellen von Hadoop-Clustern in HDInsight mit Azure Resource Manager-Vorlagen
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

In diesem Artikel lernen Sie verschiedene Möglichkeiten zum Erstellen von Azure HDInsight-Clustern mit Azure Resource Manager-Vorlagen. Weitere Informationen finden Sie unter [Bereitstellen einer Anwendung mit einer Azure-Resource Manager-Vorlage](../azure-resource-manager/resource-group-template-deploy.md). Informationen über weitere Tools und Features zur Clustererstellung finden Sie, indem Sie oben auf dieser Seite auf die Registerkartenauswahl klicken, oder unter [Methoden zur Clustererstellung](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

## <a name="prerequisites"></a>Voraussetzungen
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Um die Anweisungen in diesem Artikel befolgen zu können, benötigen Sie:

* Ein [Azure-Abonnement](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell und/oder Azure CLI.

### <a name="resource-manager-templates"></a>Resource Manager-Vorlagen
Eine Resource Manager-Vorlage vereinfacht das Erstellen der folgenden Ressourcen für Ihre Anwendung in einem einzelnen koordinierten Vorgang:
* HDInsight-Cluster und ihre abhängigen Ressourcen (z.B. das Standardspeicherkonto)
* Andere Ressourcen (z.B. Azure SQL-Datenbank zur Verwendung von Apache Sqoop)

In der Vorlage definieren Sie die Ressourcen, die für die Anwendung erforderlich sind. Sie geben außerdem die Bereitstellungsparameter zur Eingabe von Werten für unterschiedliche Umgebungen an. Die Vorlage besteht aus JSON-Code und Ausdrücken, mit denen Sie Werte für Ihre Bereitstellung erstellen.

Sie finden Beispiele für HDInsight-Vorlagen unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?term=hdinsight). Verwenden Sie den plattformübergreifenden [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) mit der [Resource Manager-Erweiterung](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) oder einem Text-Editor, um die Vorlage in einer Datei auf Ihrer Arbeitsstation zu speichern. 

Weitere Informationen über Resource Manager-Vorlagen finden Sie in den folgenden Artikeln:

* [Erstellen von Azure Resource-Manager-Vorlagen](../azure-resource-manager/resource-group-authoring-templates.md)
* [Bereitstellen einer Anwendung mit Azure Resource Manager-Vorlagen](../azure-resource-manager/resource-group-template-deploy.md)

## <a name="generate-templates"></a>Generieren von Vorlagen

Mit Resource Manager können Sie eine Resource Manager-Vorlage aus vorhandenen Ressourcen in Ihrem Abonnement mithilfe unterschiedlicher Tools exportieren. Auf der Grundlage dieser generierten Vorlage können Sie sich über die Vorlagensyntax informieren oder ggf. die erneute Bereitstellung Ihrer Lösung automatisieren.

- Azure-Portal: Weitere Informationen finden Sie unter [Exportieren einer Azure Resource Manager-Vorlage aus vorhandenen Ressourcen](../azure-resource-manager/resource-manager-export-template.md).
- Azure PowerShell: Weitere Informationen finden Sie unter [Exportieren von Azure Resource Manager-Vorlagen mit PowerShell](../azure-resource-manager/resource-manager-export-template-powershell.md).
- Azure-Befehlszeilenschnittstelle: Weitere Informationen finden Sie unter [Exportieren von Azure Resource Manager-Vorlagen mit der Azure-Befehlszeilenschnittstelle](../azure-resource-manager/resource-manager-export-template-cli.md).


## <a name="deploy-using-the-portal"></a>Bereitstellen mit dem Portal

Sie können eine Resource Manager-Vorlage über das Azure-Portal bereitstellen. Weitere Informationen finden Sie unter [Bereitstellen von Ressourcen mithilfe einer benutzerdefinierten Vorlage](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Bereitstellen mit PowerShell

Sie können eine Resource Manager-Vorlage mithilfe von Azure PowerShell bereitstellen. Weitere Informationen finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) und [Bereitstellen privater Resource Manager-Vorlagen mit SAS-Token und Azure PowerShell](../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="deploy-using-cli"></a>Bereitstellen mit der Befehlszeilenschnittstelle

Sie können eine Resource Manager-Vorlage mit der Azure-Befehlszeilenschnittstelle bereitstellen. Weitere Informationen finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und der Azure-Befehlszeilenschnittstelle](../azure-resource-manager/resource-group-template-deploy-cli.md) und [Bereitstellen privater Resource Manager-Vorlagen mit SAS-Token und der Azure-Befehlszeilenschnittstelle](../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Bereitstellen mit der REST-API
Sie können eine Resource Manager-Vorlage mit der REST-API bereitstellen. Weitere Informationen finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Resource Manager-REST-API](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Bereitstellen mit Visual Studio 2013
 Verwenden Sie Visual Studio, um ein Ressourcengruppenprojekt zu erstellen und über die Benutzeroberfläche in Azure bereitzustellen. Sie wählen den Typ der Ressourcen aus, die in Ihr Projekt einbezogen werden sollen. Diese Ressourcen werden automatisch der Resource Manager-Vorlage hinzugefügt. Das Projekt enthält auch ein PowerShell-Skript zum Bereitstellen der Vorlage.

Eine Einführung in die Verwendung von Visual Studio mit Ressourcengruppen finden Sie unter [Erstellen und Bereitstellen von Azure-Ressourcengruppen über Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="troubleshoot"></a>Problembehandlung

Falls beim Erstellen von HDInsight-Clustern Probleme auftreten, sehen Sie sich die [Voraussetzungen für die Zugriffssteuerung](hdinsight-administer-use-portal-linux.md#create-clusters) an.

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie mehrere Möglichkeiten zum Erstellen von HDInsight-Clustern kennengelernt. Weitere Informationen finden Sie in den folgenden Artikeln:

* Weitere HDInsight-bezogene Vorlagen finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Ein Beispiel für die Bereitstellung von Ressourcen über die .NET-Clientbibliothek finden Sie unter [Bereitstellen von Ressourcen mithilfe von .NET-Bibliotheken und einer Vorlage](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Ein ausführliches Beispiel für die Bereitstellung einer Anwendung finden Sie unter [Vorhersagbares Bereitstellen von Microservices in Azure](../app-service/app-service-deploy-complex-application-predictably.md).
* Informationen zum Bereitstellen der Lösung in andere Umgebungen finden Sie unter [Entwicklungs- und Testumgebungen in Microsoft Azure](../solution-dev-test-environments.md).
* Informationen zu den Abschnitten der Azure Resource Manager-Vorlage finden Sie unter [Erstellen von Vorlagen](../azure-resource-manager/resource-group-authoring-templates.md).
* Unter [Vorlagenfunktionen](../azure-resource-manager/resource-group-template-functions.md)finden Sie eine Liste der Funktionen, die Sie in einer Azure Resource Manager-Vorlage verwenden können.
