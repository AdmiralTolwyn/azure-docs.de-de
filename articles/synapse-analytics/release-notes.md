---
title: Versionshinweise
description: Versionshinweise für Azure Synapse Analytics (Arbeitsbereiche)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 059e77c063d00ef850a171507ca2e06422ade426
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82191769"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Azure Synapse Analytics (Vorschauversion): Versionshinweise

In diesem Artikel werden Einschränkungen von und Probleme mit Azure Synapse Analytics (Arbeitsbereiche) beschrieben. Weitere Informationen finden Sie unter [Was ist Azure Synapse Analytics (Arbeitsbereiche)?](overview-what-is.md).

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure Synapse (Arbeitsbereiche) 

### <a name="azure-synapse-cli"></a>Azure Synapse-Befehlszeilenschnittstelle

- Problem und Kundenbeeinträchtigung: Mit dem SDK erstellte Arbeitsbereiche können Synapse Studio nicht starten.

- Problemumgehung: Führen Sie die folgenden Schritte aus: 
  1.    Erstellen Sie den Arbeitsbereich, indem Sie `az synapse workspace create` ausführen.
  2.    Extrahieren Sie die ID der verwalteten Identität, indem Sie `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` ausführen.
  3.    Fügen Sie einen Arbeitsbereich als Rolle zum Speicherkonto hinzu, indem Sie ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}` ausführen.
  4.    Fügen Sie eine Firewallregel hinzu, indem Sie ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` ausführen.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Arbeitsbereichs](quickstart-create-workspace.md)
* [Verwenden von Synapse Studio](quickstart-synapse-studio.md)
* [Erstellen eines SQL-Pools](quickstart-create-sql-pool.md)
* [Verwenden von SQL On-Demand](quickstart-sql-on-demand.md)
* [Erstellen eines Apache Spark-Pools](quickstart-create-apache-spark-pool.md)