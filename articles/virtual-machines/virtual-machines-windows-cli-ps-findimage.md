---
title: "Navigieren zu und Auswählen von Windows-VM-Images | Microsoft Docs"
description: "Erfahren Sie, wie Sie den Herausgeber, das Angebot und die SKU für Images ermitteln, wenn Sie mit dem Resource Manager-Bereitstellungsmodell einen virtuellen Windows-Computer erstellen."
services: virtual-machines-windows
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 78f3769cd38bbcc6dbe8ac6241d6d5c3a65ccd00


---
# <a name="navigate-and-select-windows-virtual-machine-images-in-azure-with-powershell-or-the-cli"></a>Navigieren zu und Auswählen von Images virtueller Windows-Computer in Azure mithilfe von PowerShell oder der Befehlszeilenschnittstelle
In diesem Thema wird beschrieben, wie Sie Herausgeber von VM-Images sowie entsprechende Angebote, SKUs und Versionen für jeden Ort finden, an dem Sie Bereitstellungen ins Auge fassen. Beispielsweise sind einige der häufig verwendeten Windows-VM-Images:

## <a name="table-of-commonly-used-windows-images"></a>Tabelle mit häufig verwendeten Windows-Images
| PublisherName | Angebot | Sku |
|:--- |:--- |:--- |:--- |
| MicrosoftDynamicsNAV |DynamicsNAV |2015 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2013 |
| MicrosoftSQLServer |SQL 2014 WS2012R2 |Enterprise-Optimized-for-DW |
| MicrosoftSQLServer |SQL 2014 WS2012R2 |Enterprise-Optimized-for-OLTP |
| MicrosoftWindowsServer |Windows Server |2012-R2-Datacenter |
| MicrosoftWindowsServer |Windows Server |2012-Datacenter |
| MicrosoftWindowsServer |Windows Server |2008-R2-SP1 |
| MicrosoftWindowsServer |Windows Server |Windows-Server-Technical-Preview |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |

[!INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]




<!--HONumber=Nov16_HO3-->


