---
title: Azure PowerShell-Skriptbeispiel – Erstellen einer Web-App mit Continuous Deployment über GitHub | Microsoft-Dokumentation
description: Azure PowerShell-Skriptbeispiel – Erstellen einer Web-App mit Continuous Deployment über GitHub
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 42f901f8-02f7-4869-b22d-d99ef59f874c
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 8185edf1176f5215065b8154e584703069d371a2
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/19/2018
---
# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>Erstellen einer Web-App mit Continuous Deployment über GitHub

Dieses Beispielskript erstellt eine Web-App in App Service mit den zugehörigen Ressourcen und richtet dann Continuous Deployment über ein GitHub-Repository ein. Informationen zur GitHub-Bereitstellung ohne Continuous Deployment finden Sie unter [Erstellen einer Web-App und Bereitstellen von Code über GitHub](app-service-powershell-deploy-github.md).

Installieren Sie bei Bedarf Azure PowerShell anhand der Anleitung im [Azure PowerShell-Handbuch](/powershell/azure/overview), und führen Sie dann `Connect-AzureRmAccount` aus, um eine Verbindung mit Azure herzustellen. Stellen Sie darüber hinaus Folgendes sicher:

- Eine Verbindung mit Azure wurde mit dem Befehl `az login` hergestellt.
- Der Anwendungscode befindet sich in einem öffentlichen oder privaten GitHub-Repository, das Sie besitzen.
- Sie haben [ein Zugriffstoken in Ihrem GitHub-Konto erstellt](https://help.github.com/articles/creating-an-access-token-for-command-line-use/).

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../powershell_scripts/app-service/deploy-github-continuous/deploy-github-continuous.ps1?highlight=1-2 "Create a web app with continuous deployment from GitHub")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung 

Nach dem Ausführen des Skriptbeispiels können mit dem folgenden Befehl die Ressourcengruppe, die Web-App und alle zugehörigen Ressourcen entfernt werden.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Erstellt einen App Service-Plan. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Erstellt die Web-App. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Ändert eine Ressource in einer Ressourcengruppe. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure PowerShell-Modul finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).

Zusätzliche Azure PowerShell-Beispiele für Azure App Service-Web-Apps finden Sie unter [Azure PowerShell-Beispiele](../app-service-powershell-samples.md).
