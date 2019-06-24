---
title: Bereitstellen einer VM-Skalierungsgruppe in Visual Studio | Microsoft Docs
description: Bereitstellen von VM-Skalierungsgruppen mit Visual Studio und einer Resource Manager-Vorlage
services: virtual-machine-scale-sets
ms.custom: H1Hack27Feb2017
ms.workload: na
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: manayar
ms.openlocfilehash: 3d472aeaae7e7f02eba58aadea1df042d6c0f27b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62108053"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Erstellen einer VM-Skalierungsgruppe mit Visual Studio
In diesem Artikel erfahren Sie, wie eine Azure-VM-Skalierungsgruppe über eine Visual Studio-Ressourcengruppenbereitstellung bereitgestellt wird.

[Azure-VM-Skalierungsgruppen](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) ist eine Azure Compute-Ressource zur Bereitstellung und Verwaltung einer Sammlung ähnlicher virtueller Computer mit automatischer Skalierung und automatischem Lastenausgleich. Sie können VM-Skalierungsgruppen über [Azure Resource Manager-Vorlagen](https://github.com/Azure/azure-quickstart-templates) bereitstellen. Azure Resource Manager-Vorlagen können mithilfe der Azure-Befehlszeilenschnittstelle, mit PowerShell, REST und auch direkt über Visual Studio bereitgestellt werden. Visual Studio bietet eine Reihe von Beispielvorlagen, die als Teil eines Azure-Projekts zur Ressourcengruppenbereitstellung bereitgestellt werden können.

Azure-Ressourcengruppenbereitstellungen bieten eine Möglichkeit, mehrere zusammengehörige Azure-Ressourcen in einem einzelnen Bereitstellungsvorgang zu gruppieren und zu veröffentlichen. Weitere Informationen zu diesen Bereitstellungen finden Sie hier: [Erstellen und Bereitstellen von Azure-Ressourcengruppen mit Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="pre-requisites"></a>Voraussetzungen
Um mit der Bereitstellung von VM-Skalierungsgruppen in Visual Studio zu beginnen, benötigen Sie Folgendes:

* Visual Studio 2013 oder höher
* Azure SDK 2.7, 2.8 oder 2.9

>[!NOTE]
>In diesen Anweisungen wird davon ausgegangen, dass Sie Visual Studio mit [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) verwenden.

## <a name="creating-a-project"></a>Erstellen eines Projekts
1. Erstellen Sie ein neues Projekt in Visual Studio durch Auswählen von **Datei | Neu | Projekt**.
   
    ![Datei - Neu][file_new]

2. Wählen Sie unter **Visual C# | Cloud** die Option **Azure Resource Manager** aus, um ein Projekt zur Bereitstellung einer Azure Resource Manager-Vorlage zu erstellen.
   
    ![Projekt erstellen][create_project]

3. Wählen Sie aus der Liste der Vorlagen die VM-Skalierungsgruppenvorlage für Linux oder Windows aus.
   
   ![Vorlage auswählen][select_Template]

4. Nachdem das Projekt erstellt wurde, werden PowerShell-Bereitstellungsskripts, eine Azure Resource Manager-Vorlage und eine Parameterdatei für die VM-Skalierungsgruppe angezeigt.
   
    ![Projektmappen-Explorer][solution_explorer]

## <a name="customize-your-project"></a>Anpassen Ihres Projekts
Jetzt können Sie die Vorlage an die Anforderungen Ihrer Anwendung anpassen, beispielsweise VM-Erweiterungseigenschaften hinzufügen oder Lastenausgleichsregeln bearbeiten. Die VM-Skalierungsgruppenvorlagen sind standardmäßig so konfiguriert, dass die AzureDiagnostics-Erweiterung bereitgestellt wird, mit der ganz einfach Regeln für die automatische Skalierung hinzugefügt werden können. Außerdem stellen die Vorlagen einen Load Balancer mit einer öffentlichen IP-Adresse bereit, der mit NAT-Eingangsregeln konfiguriert ist. 

Der Load Balancer ermöglicht es Ihnen, Verbindungen mit den VM-Instanzen über SSH (Linux) oder RDP (Windows) herzustellen. Die Front-End-Portbereich beginnt bei 50000. Für Linux heißt das, dass Sie an den Port 22 des ersten virtuellen Computers in der Skalierungsgruppe weitergeleitet werden, wenn Sie über SSH eine Verbindung mit Port 50000 herstellen. Bei Herstellen einer Verbindung mit Port 50001 werden Sie an Port 22 des zweiten virtuellen Computers weitergeleitet usw.

 Eine gute Möglichkeit zum Bearbeiten Ihrer Vorlagen mit Visual Studio besteht darin, die JSON-Gliederung zum Organisieren der Parameter, Variablen und Ressourcen einzusetzen. Mit Kenntnis des Schemas kann Visual Studio Fehler in der Vorlage aufzeigen, bevor Sie sie bereitstellen.

![JSON-Explorer][json_explorer]

## <a name="deploy-the-project"></a>Bereitstellen des Projekts
1. Stellen Sie die Azure Resource Manager-Vorlage bereit, um die VM-Skalierungsgruppenressource zu erstellen. Klicken Sie mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Bereitstellen | Neue Bereitstellung** aus.
   
    ![Vorlage bereitstellen][5deploy_Template]
    
2. Wählen Sie Ihr Abonnement im Dialogfeld „Für Ressourcengruppe bereitstellen“.
   
    ![Vorlage bereitstellen][6deploy_Template]

3. Von hier aus können Sie eine Azure-Ressourcengruppe zum Bereitstellen Ihrer Vorlage erstellen.
   
    ![Neue Ressourcengruppe][new_resource]

4. Klicken Sie nun auf **Parameter bearbeiten**, um Parameter einzugeben, die an Ihre Vorlage übergeben werden. Geben Sie den Benutzernamen und das Kennwort für das Betriebssystem ein, das zum Erstellen der Bereitstellung erforderlich ist. Falls die PowerShell-Tools für Visual Studio nicht installiert sind, empfiehlt es sich, das Kontrollkästchen **Kennwörter speichern** zu aktivieren, um eine ausgeblendete PowerShell-Eingabeaufforderung zu vermeiden. Alternativ können Sie auch die [Key Vault-Unterstützung](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/) verwenden.
   
    ![Parameter bearbeiten][edit_parameters]

5. Klicken Sie jetzt auf **Bereitstellen**. Das Fenster **Ausgabe** zeigt den Fortschritt der Bereitstellung. Beachten Sie, dass bei der Aktion das Skript **Deploy-AzureResourceGroup.ps1** ausgeführt wird.
   
   ![Ausgabefenster][output_window]

## <a name="exploring-your-virtual-machine-scale-set"></a>Auswerten der VM-Skalierungsgruppe
Nachdem die Bereitstellung abgeschlossen ist, sehen Sie die neue VM-Skalierungsgruppe im **Cloud-Explorer** von Visual Studio (aktualisieren Sie die Liste). Mit dem Cloud-Explorer können Sie Azure-Ressourcen in Visual Studio verwalten und gleichzeitig Anwendungen entwickeln. Sie können außerdem Ihre VM-Skalierungsgruppe im [Azure-Portal](https://portal.azure.com) und im [Azure-Ressourcen-Explorer](https://resources.azure.com/) anzeigen.

![Cloud-Explorer][cloud_explorer]

 Das Portal bietet die beste Möglichkeit, Ihre Azure-Infrastruktur mit einem Webbrowser visuell zu verwalten, während der Azure-Ressourcen-Explorer eine einfache Möglichkeit zum Untersuchen und Debuggen von Azure-Ressourcen bietet. Er bietet Einblicke in die „Instanzsicht“ und führt PowerShell-Befehle für die Ressourcen auf, die Sie gerade anzeigen.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie VM-Skalierungsgruppen erfolgreich über Visual Studio bereitgestellt haben, können Sie Ihr Projekt den Anforderungen der Anwendung entsprechend weiter anpassen. Beispielsweise können Sie die automatische Skalierung konfigurieren, indem Sie eine **Insights**-Ressource hinzufügen, Ihrer Vorlage eine Infrastruktur wie z. B. eigenständige VMs hinzufügen oder Anwendungen über die benutzerdefinierte Skripterweiterung bereitstellen. Eine gute Quelle für Beispielvorlagen ist das GitHub-Repository für [Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates) (suchen Sie nach „vmss“).

[file_new]: ./media/virtual-machine-scale-sets-vs-create/1-FileNew.png
[create_project]: ./media/virtual-machine-scale-sets-vs-create/2-CreateProject.png
[select_Template]: ./media/virtual-machine-scale-sets-vs-create/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machine-scale-sets-vs-create/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machine-scale-sets-vs-create/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/6-DeployTemplate.png
[new_resource]: ./media/virtual-machine-scale-sets-vs-create/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machine-scale-sets-vs-create/8-EditParameter.png
[output_window]: ./media/virtual-machine-scale-sets-vs-create/9-Output.png
[cloud_explorer]: ./media/virtual-machine-scale-sets-vs-create/12-CloudExplorer.png
