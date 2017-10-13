---
title: "Erstellen von Schleifen und Bereichen bzw. Auflösen von Datenbatches in Workflows – Azure Logic Apps | Microsoft-Dokumentation"
description: "Erstellen Sie Schleifen zum Durchlaufen von Daten, gruppieren Sie Aktionen in Bereiche, oder lösen Sie Datenbatches auf, um mehr Workflows in Azure Logic Apps zu starten."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 413a2ba9107ca259ed577825bf0a17ff5622f1ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="logic-apps-loops-scopes-and-debatching"></a>Schleifen, Bereiche und Auflösen von Batches in Logik-Apps
  
Logic Apps bietet eine Reihe von Verfahren zum Arbeiten mit Arrays, Sammlungen, Batches und Schleifen innerhalb eines Workflows.
  
## <a name="foreach-loop-and-arrays"></a>ForEach-Schleife und -Arrays
  
Logic Apps erlaubt Schleifen über einen Satz von Daten und das Ausführen von Aktionen für jedes Element.  Dies wird über die `foreach`-Aktion ermöglicht.  Sie können im Designer eine foreach-Schleife hinzufügen.  Nachdem Sie das Array ausgewählt haben, das Sie durchlaufen möchten, können Sie mit dem Hinzufügen von Aktionen beginnen.  Zurzeit können Sie pro foreach-Schleife nur eine Aktion angeben, diese Beschränkung wird jedoch in den nächsten Wochen aufgehoben.  Innerhalb der Schleife können Sie dann angeben, was bei jedem Wert des Arrays passieren soll.

Wenn Sie die Codeansicht verwenden, können Sie eine foreach-Schleife wie unten dargestellt angeben.  Dies ist ein Beispiel für eine foreach-Schleife, die eine E-Mail-Nachricht für jede E-Mail-Adresse sendet, die „microsoft.com“ enthält:

``` json
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')"
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                },
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  Eine `foreach`-Aktion kann Arrays mit bis zu 5.000 Zeilen durchlaufen.  Standardmäßig wird jede Iteration gleichzeitig ausgeführt.  

### <a name="sequential-foreach-loops"></a>Sequenzielle ForEach-Schleifen

Zum Aktivieren der sequenziellen Ausführung einer ForEach-Schleife muss der Vorgang `Sequential` hinzugefügt werden.

``` json
"forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "operationOptions": "Sequential",
        "..."
}
```
  
## <a name="until-loop"></a>Until-Schleife
  
  Sie können eine Aktion oder eine Reihe von Aktionen ausführen, bis eine Bedingung erfüllt ist.  Das häufigste Szenario hierfür ist der Aufruf eines Endpunkts, bis Sie die gewünschte Antwort erhalten.  Sie können im Designer eine until-Schleife hinzufügen.  Sie können nach dem Hinzufügen von Aktionen innerhalb der Schleife die Beendigungsbedingung sowie die Schleifenlimits festlegen.  Es gibt eine einminütige Verzögerung zwischen Schleifendurchläufen.
  
  Wenn Sie die Codeansicht verwenden, können Sie eine until-Schleife wie unten dargestellt angeben.  Dies ist ein Beispiel für den Aufruf eines HTTP-Endpunkts bis der Antworttext den Wert „Completed“ hat.  Die Schleife wird beendet, wenn 
  
  * die HTTP-Antwort den Status „Completed“ aufweist
  * sie für 1 Stunde ausgeführt wurde
  * sie 100-mal durchlaufen wurde.
  
  ``` json
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed')",
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn und Auflösen von Batches

Mitunter empfängt ein Trigger ein Array von Elementen, die Sie aus dem Batch lösen möchten, um einen Workflow pro Element zu starten.  Dies kann über den Befehl `spliton` erreicht werden.  Wenn Ihr Trigger-Swagger eine Nutzlast angibt, die ein Array ist, wird standardmäßig ein `spliton` hinzugefügt und eine Ausführung pro Element gestartet.  SplitOn kann nur einem Trigger hinzugefügt werden.  Dies kann manuell konfiguriert oder in der Codeansicht mit der Definition überschrieben werden.  Derzeit kann SplitOn Arrays mit bis zu 5.000 Elementen auflösen.  Sie können `spliton` nicht zusammen mit einer Implementierung des synchronisierten Antwortmusters verwenden.  Jeder aufgerufene Workflow, der eine `response`-Aktion sowie ein `spliton`-Element umfasst, wird asynchron ausgeführt und sendet sofort eine `202 Accepted`-Antwort.  

SplitOn kann in der Codeansicht wie im folgenden Beispiel angegeben werden.  Damit wird ein Array von Elementen empfangen und zeilenweise aufgelöst.

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequencey": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>Bereiche

Es ist möglich, eine Reihe von Aktionen zusammen in einem Bereich zu gruppieren.  Dies ist besonders nützlich für die Implementierung der Ausnahmebehandlung.  Sie können im Designer einen neuen Bereich hinzufügen und in diesem mit dem Hinzufügen von Aktionen beginnen.  Sie können Bereiche in der Codeansicht wie folgt definieren:


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```