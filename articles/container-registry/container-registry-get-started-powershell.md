---
title: 'Schnellstartanleitung: Erstellen einer privaten Docker-Registrierung in Azure mit PowerShell'
description: Hier lernen Sie, wie Sie schnell eine private Docker-Containerregistrierung mit PowerShell erstellen.
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quicksart
ms.date: 10/08/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: fbf643ad342d712452d39c71b8706b6213198512
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2017
---
# <a name="create-an-azure-container-registry-using-powershell"></a>Erstellen einer Azure Container Registry-Instanz mit PowerShell

Azure Container Registry ist ein verwalteter Docker-Containerregistrierungsdienst zum Speichern privater Docker-Containerimages. In diesem Leitfaden erfahren Sie, wie Sie mithilfe von PowerShell eine Azure Container Registry-Instanz erstellen.

Für diese Schnellstartanleitung ist das Azure PowerShell-Modul Version 3.6 oder höher erforderlich. Führen Sie `Get-Module -ListAvailable AzureRM` aus, um die Version zu finden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Installieren des Azure PowerShell-Moduls) Informationen dazu.

Darüber hinaus muss Docker lokal installiert sein. Für Docker sind Pakete erhältlich, mit denen Docker problemlos auf einem [Mac](https://docs.docker.com/docker-for-mac/)-, [Windows](https://docs.docker.com/docker-for-windows/)- oder [Linux](https://docs.docker.com/engine/installation/#supported-platforms)-System konfiguriert werden kann.

## <a name="log-in-to-azure"></a>Anmelden an Azure

Melden Sie sich mit dem Befehl `Login-AzureRmAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Ressourcengruppe erstellen

Erstellen Sie mit [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) eine Azure-Ressourcengruppe. Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container-registry"></a>Erstellen einer Containerregistrierung

Erstellen Sie mit dem Befehl [New-AzureRMContainerRegistry](/powershell/module/containerregistry/New-AzureRMContainerRegistry) eine ACR-Instanz.

Der Name der Registrierung **muss eindeutig sein**. Im folgenden Beispiel wird der Name *myContainerRegistry007* verwendet. Ersetzen Sie diesen Namen durch einen eindeutigen Wert.

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-acr"></a>Anmelden bei ACR

Bevor Sie Push- und Pullvorgänge für Containerimages ausführen können, müssen Sie sich bei der ACR-Instanz anmelden. Verwenden Sie zunächst den Befehl [Get-AzureRmContainerRegistryCredential](/powershell/module/containerregistry/get-azurermcontainerregistrycredential), um die Administratoranmeldeinformationen für die ACR-Instanz zu erhalten.

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Verwenden Sie anschließend den Befehl [docker login](https://docs.docker.com/engine/reference/commandline/login/), um sich bei der ACR-Instanz anzumelden.

```bash
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

Nach Abschluss des Vorgangs wird eine Erfolgsmeldung zurückgegeben.

## <a name="push-image-to-acr"></a>Übertragen eines Images an ACR mithilfe von Push

Um ein Image mithilfe von Push an Ihre Azure Container Registry-Instanz übertragen zu können, benötigen Sie zunächst ein Image. Führen Sie bei Bedarf den folgenden Befehl aus, um ein vorab erstelltes Image von Docker Hub abzurufen:

```bash
docker pull microsoft/aci-helloworld
```

Das Image muss mit dem ACR-Anmeldeservernamen gekennzeichnet sein. Führen Sie den Befehl [Get-AzureRmContainerRegistry](/powershell/module/containerregistry/Get-AzureRmContainerRegistry) aus, um den Anmeldeservernamen der ACR-Instanz zurückzugeben.

```powershell
Get-AzureRmContainerRegistry | Select Loginserver
```

Markieren Sie das Image mithilfe des Befehls [docker tag](https://docs.docker.com/engine/reference/commandline/tag/). Ersetzen Sie *acrLoginServer* durch den Anmeldeservernamen Ihrer ACR-Instanz.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Nun können Sie die Images mithilfe von [docker push](https://docs.docker.com/engine/reference/commandline/push/) an die ACR-Instanz übertragen. Ersetzen Sie *acrLoginServer* durch den Anmeldeservernamen Ihrer ACR-Instanz.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppe, die ACR-Instanz und alle Containerimages nicht mehr benötigen, können Sie sie mit dem Befehl [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) entfernen.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Azure Container Registry-Instanz über die Azure-Befehlszeilenschnittstelle erstellt. Wenn Sie Azure Container Registry mit Azure Container Instances verwenden möchten, fahren Sie mit dem Tutorial zu Azure Container Instances fort.

> [!div class="nextstepaction"]
> [Azure Container Instances-Tutorial](../container-instances/container-instances-tutorial-prepare-app.md)