---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 684b212ca771af6c336cf6239e18ea367f2da5ce
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045092"
---
In diesem Artikel werden PowerShell-Cmdlets verwendet. Zum Ausführen der Cmdlets können Sie Azure Cloud Shell verwenden, eine interaktive Shell-Umgebung, die in Azure gehostet und über den Browser verwendet wird. In Azure Cloud Shell sind die Azure PowerShell-Cmdlets bereits vorinstalliert.

Um in Azure Cloud Shell Code aus diesem Artikel auszuführen, öffnen Sie eine Cloud Shell-Sitzung, verwenden Sie für einen Codeblock die Schaltfläche **Kopieren**, um Code zu kopieren, und fügen Sie ihn mit __STRG+UMSCHALT+V__ (Windows und Linux) oder __BEFEHL+UMSCHALT+V__ (macOS) in die Cloud Shell-Sitzung ein. Eingefügter Code wird nicht automatisch ausgeführt. Sie müssen zum Ausführen von Code die **EINGABETASTE** drücken.

Sie können Azure Cloud Shell wie folgt starten:

|  |   |
|-----------------------------------------------|---|
| Klicken Sie in der rechten oberen Ecke eines Codeblocks auf **Ausprobieren**. Dadurch wird __nicht__ automatisch Text in Cloud Shell kopiert. | ![Beispiel für „Testen Sie es.“ für Azure Cloud Shell](./media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| Öffnen Sie [shell.azure.com](https://shell.azure.com) in einem Browser. | [![Schaltfläche zum Starten von Azure Cloud Shell](./media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Klicken Sie im [Azure-Portal](https://portal.azure.com) rechts oben im Menü auf die Schaltfläche **Cloud Shell**: | ![Cloud Shell-Schaltfläche im Azure-Portal](./media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

**Lokales Ausführen von PowerShell**

Sie können die Azure PowerShell-Cmdlets auch lokal auf Ihrem Computer installieren und ausführen. PowerShell-Cmdlets werden regelmäßig aktualisiert. Wenn Sie nicht die neueste Version verwenden, können die in den Anweisungen angegebenen Werte fehlschlagen. Verwenden Sie das Cmdlet `Get-Module -ListAvailable Az`, um die auf Ihrem Computer installierten Azure PowerShell-Versionen zu ermitteln. Informationen zum Installieren oder Aktualisieren finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps).

Wenn Sie PowerShell lokal ausführen, müssen Sie auch „Connect-AzAccount“ ausführen, um die Verbindung zu Azure zu erstellen.