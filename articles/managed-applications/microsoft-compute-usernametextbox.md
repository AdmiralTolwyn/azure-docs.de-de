---
title: "Benutzeroberflächenelement „UserNameTextBox“ in verwalteten Azure-Anwendungen | Microsoft-Dokumentation"
description: "Hier wird das Benutzeroberflächenelement „Microsoft.Compute.UserNameTextBox“ für verwaltete Azure-Anwendungen beschrieben."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 6a395915af274750eb57a085ee51b55fdd392615
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Benutzeroberflächenelement „Microsoft.Compute.UserNameTextBox“
Ein Textfeldsteuerelement mit integrierter Überprüfung für Windows- und Linux-Benutzernamen. Dieses Element verwenden Sie beim [Erstellen einer verwalteten Azure-Anwendung](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Benutzeroberflächenbeispiel
![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

## <a name="schema"></a>Schema
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="remarks"></a>Anmerkungen
- Wenn `constraints.required` auf **true** festgelegt ist, muss das Textfeld für eine erfolgreiche Überprüfung einen Wert enthalten. Der Standardwert lautet **true**.
- `osPlatform` muss angegeben werden. Mögliche Optionen: **Windows** oder **Linux**.
- `constraints.regex` ist ein Muster für einen regulären JavaScript-Ausdruck. Wenn der Wert des Textfelds angegeben wird, muss er für eine erfolgreiche Überprüfung dem Muster entsprechen. Der Standardwert lautet **null**.
- `constraints.validationMessage` ist eine Zeichenfolge, die angezeigt wird, wenn bei der durch `constraints.regex` angegebenen Überprüfung des Werts des Textfelds ein Fehler auftritt. Wird kein Wert angegeben, werden die integrierten Überprüfungsnachrichten des Textfelds verwendet. Der Standardwert lautet **null**.
- Dieses Element verfügt über eine integrierte Überprüfung, die auf dem für `osPlatform` angegebenen Wert basiert. Die integrierte Überprüfung kann zusammen mit einem benutzerdefinierten regulären Ausdruck verwendet werden.
Wenn ein Wert für `constraints.regex` angegeben ist, werden die integrierte und die benutzerdefinierte Überprüfung ausgelöst.

## <a name="sample-output"></a>Beispielausgabe
```json
"tabrezm"
```

## <a name="next-steps"></a>Nächste Schritte
* Eine Einführung in verwaltete Anwendungen finden Sie in der [Übersicht über verwaltete Azure-Anwendungen](overview.md).
* Eine Einführung zum Erstellen von Benutzeroberflächendefinitionen finden Sie unter [Erste Schritte mit „CreateUiDefinition“](create-uidefinition-overview.md).
* Eine Beschreibung der allgemeinen Eigenschaften in Benutzeroberflächenelementen finden Sie unter [CreateUiDefinition-Elemente](create-uidefinition-elements.md).
