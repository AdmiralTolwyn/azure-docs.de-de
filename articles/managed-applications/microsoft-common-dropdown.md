---
title: Benutzeroberflächenelement DropDown in Azure | Microsoft-Dokumentation
description: Hier wird das Benutzeroberflächenelement Microsoft.Common.DropDown für das Azure-Portal beschrieben.
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
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: 6c92304ae623807ffba05dcdbb576e1cef63b10c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/03/2018
---
# <a name="microsoftcommondropdown-ui-element"></a>Benutzeroberflächenelement „Microsoft.Common.DropDown“
Ein Auswahlsteuerelement mit einer Dropdownliste.

## <a name="ui-sample"></a>Benutzeroberflächenbeispiel
![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>Schema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Some drop down",
  "defaultValue": "my value",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ]
  },
  "visible": true
}
```

## <a name="remarks"></a>Anmerkungen
- Die Bezeichnung für `constraints.allowedValues` ist der Anzeigetext für ein Element, und sein Wert ist der Ausgabewert des Elements bei der Auswahl.
- Falls angegeben, muss der Standardwert einer in `constraints.allowedValues` vorhandenen Bezeichnung entsprechen. Ohne Angabe wird das erste Element in `constraints.allowedValues` ausgewählt. Der Standardwert lautet **null**.
- `constraints.allowedValues` muss mindestens ein Element enthalten.
- Dieses Element unterstützt die `constraints.required`-Eigenschaft nicht. Fügen Sie zum Emulieren dieses Verhaltens `constraints.allowedValues` ein Element mit einer Bezeichnung und dem Wert `""` (leere Zeichenfolge) hinzu.

## <a name="sample-output"></a>Beispielausgabe
```json
"Bar"
```

## <a name="next-steps"></a>Nächste Schritte
* Eine Einführung zum Erstellen von Benutzeroberflächendefinitionen finden Sie unter [Erste Schritte mit „CreateUiDefinition“](create-uidefinition-overview.md).
* Eine Beschreibung der allgemeinen Eigenschaften in Benutzeroberflächenelementen finden Sie unter [CreateUiDefinition-Elemente](create-uidefinition-elements.md).
