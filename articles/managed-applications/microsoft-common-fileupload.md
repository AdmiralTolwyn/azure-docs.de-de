---
title: "Benutzeroberflächenelement „FileUpload“ in verwalteten Azure-Anwendungen | Microsoft-Dokumentation"
description: "Hier wird das Benutzeroberflächenelement „Microsoft.Common.FileUpload“ für verwaltete Azure-Anwendungen beschrieben."
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
ms.openlocfilehash: 095880322ba801895a22efcf3476fa37d9e2ac3c
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcommonfileupload-ui-element"></a>Benutzeroberflächenelement „Microsoft.Common.FileUpload“
Ein Steuerelement, mit dem Benutzer hochzuladende Dateien angeben können. Dieses Element verwenden Sie beim [Erstellen einer verwalteten Azure-Anwendung](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Benutzeroberflächenbeispiel
![Microsoft.Common.FileUpload](./media/managed-application-elements/microsoft.common.fileupload.png)

## <a name="schema"></a>Schema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.FileUpload",
  "label": "Some file upload",
  "toolTip": "",
  "constraints": {
    "required": true,
    "accept": ".doc,.docx,.xml,application/msword"
  },
  "options": {
    "multiple": false,
    "uploadMode": "file",
    "openMode": "text",
    "encoding": "UTF-8"
  },
  "visible": true
}
```

## <a name="remarks"></a>Anmerkungen
- `constraints.accept` gibt die Typen von Dateien an, die im Dateidialogfeld des Browsers angezeigt werden. Zulässige Werte finden Sie in der [HTML5-Spezifikation](http://www.w3.org/TR/html5/forms.html#attr-input-accept). Der Standardwert lautet **null**.
- Wenn `options.multiple` auf **true** festgelegt ist, kann der Benutzer im Dateidialogfeld des Browsers mehr als eine Datei auswählen. Der Standardwert ist **false**.
- Dieses Element unterstützt das Hochladen von Dateien basierend auf dem Wert für `options.uploadMode` in zwei Modi. Wenn **file** angegeben ist, enthält die Ausgabe den Inhalt der Datei als Blob. Wenn **url** angegeben ist, wird die Datei an einen temporären Speicherort hochgeladen, und die Ausgabe enthält die URL des Blobs. Temporäre Blobs werden nach 24 Stunden endgültig gelöscht. Der Standardwert ist **file**.
- Der Wert für `options.openMode` bestimmt, wie die Datei gelesen wird. Wenn eine Nur-Text-Datei erwartet wird, geben Sie **text** an. Andernfalls geben Sie **binary** an. Der Standardwert lautet **text**.
- Wenn **file** für `options.uploadMode` und **binary** für `options.openMode` festgelegt ist, ist die Ausgabe Base64-codiert.
- `options.encoding` gibt die beim Lesen der Datei zu verwendende Codierung an. Der Standardwert ist **UTF-8**. Er wird nur verwenden, wenn für `options.openMode` der Wert **text** angegeben ist.

## <a name="sample-output"></a>Beispielausgabe
Wenn für „options.multiple“ der Wert „false“ und für „options.uploadMode“ der Wert „file“ angegeben ist, enthält die Ausgabe den Inhalt der Datei als JSON-Zeichenfolge:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Wenn für „options.multiple“ der Wert „true“ und für „options.uploadMode“ der Wert „file“ angegeben ist, enthält die Ausgabe den Inhalt der Dateien als JSON-Array:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Wenn für „options.multiple“ der Wert „false“ und für „options.uploadMode“ der Wert „url“ angegeben ist, enthält die Ausgabe eine URL als JSON-Zeichenfolge:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Wenn für „options.multiple“ der Wert „true“ und für „options.uploadMode“ der Wert „url“ angegeben ist, enthält die Ausgabe eine Liste der URLs als JSON-Array:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

Beim Testen eines CreateUiDefinition-Elements schneiden manche Browser (etwa Google Chrome) in der Browserkonsole URLs ab, die vom Microsoft.Common.FileUpload-Element erstellt wurden. Unter Umständen müssen Sie mit der rechten Maustaste auf einzelne Links klicken, um die vollständigen URLs zu kopieren.


## <a name="next-steps"></a>Nächste Schritte
* Eine Einführung in verwaltete Anwendungen finden Sie in der [Übersicht über verwaltete Azure-Anwendungen](overview.md).
* Eine Einführung zum Erstellen von Benutzeroberflächendefinitionen finden Sie unter [Erste Schritte mit „CreateUiDefinition“](create-uidefinition-overview.md).
* Eine Beschreibung der allgemeinen Eigenschaften in Benutzeroberflächenelementen finden Sie unter [CreateUiDefinition-Elemente](create-uidefinition-elements.md).
