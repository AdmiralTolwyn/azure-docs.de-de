---
title: VM-Erweiterung für den Azure Network Watcher-Agent für Linux | Microsoft Docs
description: Stellen Sie den Network Watcher-Agent mithilfe einer VM-Erweiterung auf einem virtuellen Linux-Computer bereit.
services: virtual-machines-linux
documentationcenter: ''
author: dennisg
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: a51566148eec88f707b524346afc59606196e4da
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/23/2018
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>VM-Erweiterung für den Network Watcher-Agent für Linux

## <a name="overview"></a>Übersicht

[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/) ist ein Dienst zur Überwachung, Diagnose und Analyse der Netzwerkleistung, der die Überwachung von Azure-Netzwerken ermöglicht. Die VM-Erweiterung für den Network Watcher-Agent ist eine Voraussetzung für einige der Network Watcher-Features auf virtuellen Azure-Computern. Dies schließt das Erfassen des Netzwerkdatenverkehrs bei Bedarf und andere erweiterte Funktionen ein.

Dieses Dokument enthält ausführliche Informationen zu den unterstützten Plattformen und Bereitstellungsoptionen für die VM-Erweiterung für den Network Watcher-Agent für Linux.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="operating-system"></a>Betriebssystem

Die VM-Erweiterung für den Network Watcher-Agent kann für folgende Linux-Distributionen ausgeführt werden:

| Distribution | Version |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS und 12.04 LTS |
| Debian | 7 und 8 |
| RedHat | 6.x und 7.x |
| Oracle Linux | 7x |
| SUSE | 11 und 12 |
| openSUSE | 7.0 |
| CentOS | 7.0 |

Beachten Sie, dass CoreOS zurzeit nicht unterstützt wird.

### <a name="internet-connectivity"></a>Internetkonnektivität

Für einige Funktionen des Network Watcher-Agents muss der virtuelle Zielcomputer mit dem Internet verbunden sein. Ohne die Fähigkeit, ausgehende Verbindungen herzustellen, treten bei einigen Features des Network Watcher-Agents möglicherweise Fehler auf, oder sie sind nicht verfügbar. Weitere Informationen finden Sie in der [Dokumentation zu Network Watcher](https://review.docs.microsoft.com/azure/network-watcher/).

## <a name="extension-schema"></a>Erweiterungsschema

Der folgende JSON-Code zeigt das Schema für die Network Watcher-Agent-Erweiterung. Die Erweiterung erfordert oder unterstützt zurzeit keine vom Benutzer bereitgestellten Einstellungen, sondern verwendet ausschließlich die Standardkonfiguration.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentLinux",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>Eigenschaftswerte

| NAME | Wert/Beispiel |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| Herausgeber | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Bereitstellung von Vorlagen

Azure-VM-Erweiterungen können mithilfe von Azure Resource Manager-Vorlagen bereitgestellt werden. Das im vorherigen Abschnitt erläuterte JSON-Schema kann in einer Azure Resource Manager-Vorlage zum Ausführen der Erweiterung für den Network Watcher-Agent im Rahmen einer Azure Resource Manager-Bereitstellung verwendet werden.

## <a name="azure-cli-deployment"></a>Bereitstellung mithilfe der Azure-Befehlszeilenschnittstelle

Sie können die VM-Erweiterung für den Network Watcher-Agent mithilfe der Azure-Befehlszeilenschnittstelle auf einem vorhandenen virtuellen Computer bereitstellen.

```azurecli
azure vm extension set myResourceGroup1 myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="troubleshooting-and-support"></a>Problembehandlung und Support

### <a name="troubleshooting"></a>Problembehandlung

Daten zum Status von Erweiterungsbereitstellungen können über das Azure-Portal und mithilfe der Azure-Befehlszeilenschnittstelle abgerufen werden. Führen Sie über die Azure-Befehlszeilenschnittstelle den folgenden Befehl aus, um den Bereitstellungsstatus von Erweiterungen für einen bestimmten virtuellen Computer anzuzeigen.

```azurecli
azure vm extension get myResourceGroup1 myVM1
```

Die Ausgabe der Erweiterungsausführung wird in Dateien im folgenden Verzeichnis protokolliert:

`
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
`

### <a name="support"></a>Support

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie die Dokumentation zu Network Watcher lesen oder den Azure-Experten im [MSDN Azure-Forum oder im Stack Overflow-Forum](https://azure.microsoft.com/en-us/support/forums/) Fragen stellen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/en-us/support/options/) auf, und wählen Sie „Support erhalten“ aus. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/en-us/support/faq/).
