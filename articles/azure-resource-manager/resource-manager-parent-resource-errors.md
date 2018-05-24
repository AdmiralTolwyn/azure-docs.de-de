---
title: Übergeordnete Ressourcenfehler in Azure | Microsoft-Dokumentation
description: Beschreibt, wie bei der Arbeit mit einer übergeordneten Ressource Fehler behoben werden können
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: c996a644f206051cb58522065f87f95a4058cdee
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2018
---
# <a name="resolve-errors-for-parent-resources"></a>Beheben von Fehlern bei übergeordneten Ressourcen

In diesem Artikel werden die Fehler beschrieben, die bei der Bereitstellung einer Ressource auftreten können, die von einer übergeordneten Ressource abhängig ist.

## <a name="symptom"></a>Symptom

Wenn Sie eine Ressource bereitstellen, die einer anderen Ressource untergeordnet ist, kann der folgende Fehler auftreten:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Ursache

Wenn eine Ressource einer anderen untergeordnet ist, muss die übergeordnete Ressource vor dem Erstellen der untergeordneten Ressource bereits vorhanden sein. Der Name der untergeordneten Ressource enthält den Namen der übergeordneten Ressource. Zum Beispiel könnte eine SQL-Datenbank wie folgt definiert werden:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Wenn Sie jedoch auf dem Server keine Abhängigkeit angeben, beginnt die Datenbankbereitstellung möglicherweise vor der Bereitstellung des Servers.

## <a name="solution"></a>Lösung

Um diesen Fehler aufzulösen, geben Sie eine Abhängigkeit an.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

Weitere Informationen finden Sie unter [Definieren der Reihenfolge für die Bereitstellung von Ressourcen in Azure Resource Manager-Vorlagen](resource-group-define-dependencies.md).