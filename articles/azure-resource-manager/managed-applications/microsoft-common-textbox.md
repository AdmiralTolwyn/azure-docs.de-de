---
title: Benutzeroberflächenelement „TextBox“
description: Hier wird das Benutzeroberflächenelement Microsoft.Common.TextBox für das Azure-Portal beschrieben. Es wird zum Hinzufügen von unformatiertem Text verwendet.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: e9f084badda9ea1905e43c6f00b29aaf957a6dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75649758"
---
# <a name="microsoftcommontextbox-ui-element"></a>Benutzeroberflächenelement „Microsoft.Common.TextBox UI“

Ein Steuerelement, das zum Bearbeiten von unformatiertem Text verwendet werden kann.

## <a name="ui-sample"></a>Benutzeroberflächenbeispiel

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Example text box 1",
  "defaultValue": "my text value",
  "toolTip": "Use only allowed characters",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="sample-output"></a>Beispielausgabe

```json
"my text value"
```

## <a name="remarks"></a>Bemerkungen

- Wenn `constraints.required` auf **TRUE** festgelegt ist, muss das Textfeld für eine erfolgreiche Überprüfung einen Wert enthalten. Der Standardwert ist **false**.
- `constraints.regex` ist ein Muster für einen regulären JavaScript-Ausdruck. Wenn der Wert des Textfelds angegeben wird, muss er für eine erfolgreiche Überprüfung dem Muster entsprechen. Der Standardwert ist **NULL**.
- `constraints.validationMessage` ist eine Zeichenfolge, die angezeigt wird, wenn bei der Überprüfung des Werts des Textfelds ein Fehler auftritt. Wird kein Wert angegeben, werden die integrierten Überprüfungsnachrichten des Textfelds verwendet. Der Standardwert ist **NULL**.
- Für `constraints.regex` kann ein Wert angegeben werden, wenn `constraints.required` auf **false** festgelegt ist. In diesem Szenario ist für eine erfolgreiche Überprüfung des Textfelds kein Wert erforderlich. Wird ein Wert angegeben, muss er dem Muster des regulären Ausdrucks entsprechen.

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zum Erstellen von Benutzeroberflächendefinitionen finden Sie unter [Erste Schritte mit „CreateUiDefinition“](create-uidefinition-overview.md).
* Eine Beschreibung der allgemeinen Eigenschaften in Benutzeroberflächenelementen finden Sie unter [CreateUiDefinition-Elemente](create-uidefinition-elements.md).
