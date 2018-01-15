---
title: Anmelden bei einem klassischen virtuellen Azure-Computer | Microsoft Docs
description: Melden Sie sich im Azure-Portal bei einem virtuellen Windows-Computer an, der mit dem klassischen Bereitstellungsmodell erstellt wurde.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: c35bfeaa81f89dc5a636d67ed9a2a930d689c54a
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2018
---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-portal"></a>Anmelden bei einem virtuellen Windows-Computer über das Azure-Portal
Im Azure-Portal können Sie über die Schaltfläche **Verbinden** eine Remotedesktopsitzung starten und sich bei einem virtuellen Windows-Computer anmelden.

Möchten Sie eine Verbindung mit einem virtuellen Linux-Computer herstellen? Siehe [Anmelden bei einem virtuellen Computer, auf dem Linux ausgeführt wird](../../linux/mac-create-ssh-keys.md).

<!--
Deleting, but not 100% sure
Learn how to [perform these steps using new Azure portal](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

> [!IMPORTANT]
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Resource Manager- und klassische Bereitstellung](../../../resource-manager-deployment-model.md). Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells. Informationen zum Anmelden bei einem virtuellen Computer im Resource Manager-Modell finden Sie [hier](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="connect-to-the-virtual-machine"></a>Herstellen einer Verbindung mit dem virtuellen Computer
1. Melden Sie sich beim Azure-Portal an.
2. Klicken Sie auf den virtuellen Computer, auf den Sie zugreifen möchten. Der Name ist im Bereich **Alle Ressourcen** aufgeführt.

    ![Virtual-machine-locations](./media/connect-logon/azureportaldashboard.png)

3. Klicken Sie in der Befehlszeile oben im Dashboard des virtuellen Computers auf **Verbinden**.

    ![Symbol „Verbinden“ für den virtuellen Computer](./media/connect-logon/virtualmachine_dashboard_connect.png)

<!-- Don't know if this still applies
     I think we can zap this.
> [!TIP]
> If the **Connect** button isn't available, see the troubleshooting tips at the end of this article.
>
>
-->

## <a name="log-on-to-the-virtual-machine"></a>Anmelden beim virtuellen Computer
[!INCLUDE [virtual-machines-log-on-win-server](../../../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Nächste Schritte
* Wenn die Schaltfläche **Verbinden** inaktiv ist oder andere Probleme mit der Remotedesktopverbindung auftreten, versuchen Sie, die Konfiguration zurückzusetzen. Klicken Sie im Dashboard des virtuellen Computers auf **Remotezugriff zurücksetzen**.

    ![Reset-remote-access](./media/connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* Wenn Sie Probleme mit Ihrem Kennwort haben, setzen Sie es zurück. Klicken Sie links im Dashboard des virtuellen Computers unter **Support und Problembehandlung** auf **Kennwort zurücksetzen**.

    ![Reset-password](./media/connect-logon/virtualmachine_dashboard_reset_password.png)

Wenn diese Tipps nicht funktionieren oder nicht das Gesuchte sind, finden Sie weitere Informationen unter [Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Dieser Artikel führt Sie durch die Diagnose und Behebung von häufigen Problemen.
