---
title: Verwenden des Salesforce-Connectors in Ihren Logik-Apps | Microsoft Docs
description: Erstellen Sie Logik-Apps mit Azure App Service. Der Salesforce-Connector bietet eine API zum Verwenden von Salesforce-Objekten.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/05/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 4278837bb5653b66223374aa728bdc81b279fff7
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38237300"
---
# <a name="get-started-with-the-salesforce-connector"></a>Erste Schritte mit dem Salesforce-Connector
Der Salesforce-Connector bietet eine API zum Verwenden von Salesforce-Objekten.

Wenn Sie [einen Connector](apis-list.md) verwenden möchten, müssen Sie zuerst eine Logik-App erstellen. Erstellen Sie daher erst einmal eine Logik-App, wie [hier](../logic-apps/quickstart-create-first-logic-app-workflow.md) beschrieben.

## <a name="connect-to-salesforce-connector"></a>Herstellen einer Verbindung mit dem Salesforce-Connector
Damit Ihre Logik-App überhaupt auf einen Dienst zugreifen kann, müssen Sie zunächst eine *Verbindung* mit dem Dienst herstellen. Eine [Verbindung](connectors-overview.md) stellt den Kontakt zwischen einer Logik-App und einem anderen Dienst her.  

### <a name="create-a-connection-to-salesforce-connector"></a>Erstellen einer Verbindung mit dem Salesforce-Connector
> [!INCLUDE [Steps to create a connection to Salesforce Connector](../../includes/connectors-create-api-salesforce.md)]
> 
> 

## <a name="use-a-salesforce-connector-trigger"></a>Verwenden eines Triggers des Salesforce-Connectors
Ein Trigger ist ein Ereignis, mit dem ein in einer Logik-App definierter Workflow gestartet werden kann. Weitere Informationen zu Triggern finden Sie [hier](../logic-apps/logic-apps-overview.md#logic-app-concepts).

> [!INCLUDE [Steps to create a Salesforce trigger](../../includes/connectors-create-api-salesforce-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Bedingung hinzufügen
> [!INCLUDE [Steps to create a Salesforce condition](../../includes/connectors-create-api-salesforce-condition.md)]
> 
> 

## <a name="use-a-salesforce-connector-action"></a>Verwenden einer Aktion des Salesforce-Connectors
Eine Aktion ist ein Vorgang, der durch den in einer Logik-App definierten Workflow ausgeführt wird. Weitere Informationen zu Aktionen finden Sie [hier](../logic-apps/logic-apps-overview.md#logic-app-concepts).

> [!INCLUDE [Steps to create a Salesforce action](../../includes/connectors-create-api-salesforce-action.md)]
> 
> 

## <a name="connector-specific-details"></a>Connectorspezifische Details

Zeigen Sie die in Swagger definierten Trigger und Aktionen sowie mögliche Beschränkungen in den [Connectordetails](/connectors/salesforce/) an. 

## <a name="next-steps"></a>Nächste Schritte
[Erstellen einer Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md)

