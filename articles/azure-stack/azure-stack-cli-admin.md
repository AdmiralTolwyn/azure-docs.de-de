---
title: Aktivieren der Azure CLI für Azure Stack-Benutzer | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die plattformübergreifende Befehlszeilenschnittstelle (Command-Line Interface, CLI) verwenden, um Ressourcen in Azure Stack zu verwalten und bereitzustellen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2018
ms.author: mabrigg
ms.openlocfilehash: e9309f8cb46b31ded46b705308465ac6f6c89204
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585185"
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Aktivieren der Azure CLI für Azure Stack-Benutzer

*Gilt für: integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können das CA-Stammzertifikat Benutzern in Azure Stack zur Verfügung stellen, damit sie die Azure CLI auf ihren Entwicklungscomputern verwenden können. Ihre Benutzer benötigen das Zertifikat, um Ressourcen über die CLI zu verwalten.

* Das **Azure Stack-Zertifizierungsstellen-Stammzertifikat** ist erforderlich, wenn Benutzer die CLI auf einer Arbeitsstation außerhalb von Azure Stack Development Kit verwenden.  

* Der **Endpunkt der VM-Aliase** stellt einen Alias (z.B. „UbuntuLTS“ oder „Win2012Datacenter“) bereit, der beim Bereitstellen von VMs als einzelner Parameter auf einen Herausgeber, ein Angebot, eine SKU und die Version eines Image verweist.  

In den folgenden Abschnitten wird beschrieben, wie Sie diese Werte abrufen.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Exportieren des Azure Stack-Zertifizierungsstellen-Stammzertifikats

Sie finden das Azure Stack CA-Stammzertifikat im Development Kit und auf einem virtuellen Mandantencomputer, der innerhalb der Development Kit-Umgebung ausgeführt wird. Um das Azure Stack-Stammzertifikat im PEM-Format zu exportieren, melden Sie sich bei Ihrem Development Kit oder dem virtuellen Mandantencomputer an, und führen Sie das folgende Skript aus:

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Log-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Einrichten des Endpunkts der VM-Aliase

Azure Stack-Betreiber sollten einen öffentlich zugänglichen Endpunkt einrichten, der eine VM-Aliasdatei hostet. Bei der VM-Aliasdatei handelt es sich um eine JSON-Datei, die einen allgemeinen Namen für ein Image bereitstellt. Dieser Name wird später bei der Bereitstellung einer VM als Azure CLI-Parameter angegeben.  

Stellen Sie vor dem Hinzufügen eines Eintrags zu einer Aliasdatei sicher, dass Sie [Images aus dem Azure Marketplace heruntergeladen](azure-stack-download-azure-marketplace-item.md) oder [ein eigenes benutzerdefiniertes Image veröffentlicht](azure-stack-add-vm-image.md) haben. Wenn Sie ein benutzerdefiniertes Image veröffentlichen, notieren Sie sich die während der Veröffentlichung angegebenen Informationen zu Herausgeber, Angebot, SKU und Version. Wenn es sich um ein Image aus dem Marketplace handelt, können Sie die Informationen mithilfe des ```Get-AzureVMImage```-Cmdlets anzeigen.  

Eine [Aliasbeispieldatei](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) mit zahlreichen allgemeinen Imagealiasen ist verfügbar. Sie können diese als Ausgangspunkt verwenden. Hosten Sie diese Datei an einem Ort, auf den die CLI-Clients zugreifen können. Eine Möglichkeit besteht darin, die Datei in einem Blob Storage-Konto zu hosten und die URL für Ihre Benutzer freizugeben:

1. Laden Sie die [Beispieldatei](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) von GitHub herunter.
2. Erstellen Sie ein neues Speicherkonto in Azure Stack. Erstellen Sie anschließend einen neuen Blobcontainer. Legen Sie die Zugriffsrichtlinie auf „Öffentlich“ fest.  
3. Laden Sie die JSON-Datei in den neuen Container hoch. Anschließend können Sie die URL des Blobs anzeigen, indem Sie den Blobnamen auswählen und dann die URL aus den Blobeigenschaften auswählen.

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen von Vorlagen mit der Azure CLI](azure-stack-deploy-template-command-line.md)
- [Verbinden mit PowerShell](azure-stack-connect-powershell.md)
- [Verwalten von Benutzerberechtigungen](azure-stack-manage-permissions.md)
