---
title: Benutzeroberflächenelement PasswordBox in Azure | Microsoft-Dokumentation
description: Hier wird das Benutzeroberflächenelement Microsoft.Common.PasswordBox für das Azure-Portal beschrieben.
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
ms.openlocfilehash: 19c027819b83f10a7a3de714d690964507311da0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/03/2018
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Benutzeroberflächenelement „Microsoft.Common.PasswordBox“
Ein Steuerelement, das zum Angeben und Bestätigen eines Kennworts verwendet werden kann.

## <a name="ui-sample"></a>Benutzeroberflächenbeispiel
![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>Schema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "",
    "validationMessage": ""
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Anmerkungen
- Dieses Element unterstützt die `defaultValue`-Eigenschaft nicht.
- Implementierungsdetails zu `constraints` finden Sie unter [Microsoft.Common.TextBox](microsoft-common-textbox.md).
- Wenn `options.hideConfirmation` auf **true** festgelegt ist, wird das zweite Textfeld zum Bestätigen des Benutzerkennworts ausgeblendet. Der Standardwert ist **false**.

## <a name="sample-output"></a>Beispielausgabe
```json
"p4ssw0rd"
```

## <a name="next-steps"></a>Nächste Schritte
* Eine Einführung zum Erstellen von Benutzeroberflächendefinitionen finden Sie unter [Erste Schritte mit „CreateUiDefinition“](create-uidefinition-overview.md).
* Eine Beschreibung der allgemeinen Eigenschaften in Benutzeroberflächenelementen finden Sie unter [CreateUiDefinition-Elemente](create-uidefinition-elements.md).
