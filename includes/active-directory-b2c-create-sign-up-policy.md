---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: 511b05e6cae769a5b39ae81a3e67efd05d374511
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134137"
---
Wenn Sie nur die Registrierung bei Ihrer Anwendung ermöglichen möchten, verwenden Sie eine Richtlinie für die **Registrierung**. Diese Richtlinie beschreibt die Benutzeroberflächen, die Kunden bei der Registrierung durchlaufen, und den Inhalt der Token, die die Anwendung bei erfolgreichen Registrierungen erhält.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Klicken Sie auf **Registrierungsrichtlinien**.

Klicken Sie oben auf dem Blatt auf **+Hinzufügen** .

Der **Name** bestimmt den Namen der Registrierungsrichtlinie, die von der Anwendung verwendet wird. Geben Sie beispielsweise **SiUp** ein.

Klicken Sie auf **Identitätsanbieter**, und wählen Sie **E-Mail-Registrierung** aus. Optional können Sie auch soziale Netzwerke als Identitätsanbieter auswählen, sofern bereits konfiguriert. Klicken Sie auf **OK**.

Klicken Sie auf **Registrierungsattribute**. Hier wählen Sie die Attribute aus, die der Kunde bei der Registrierung angeben soll. Wählen Sie z.B. **Land/Region**, **Angezeigter Name** und **Postleitzahl** aus. Klicken Sie auf **OK**.

Klicken Sie auf **Anwendungsansprüche**. Hier wählen Sie die Ansprüche aus, die in den Token nach einer erfolgreichen Registrierung an die Anwendung zurückgegeben werden sollen. Wählen Sie z.B. **Angezeigter Name**, **Identitätsanbieter**, **Postleitzahl**, **User is new** (Benutzer ist neu) und **User's Object ID** (Objekt-ID des Benutzers) aus.

Klicken Sie auf **Create**. Die erstellte Richtlinie wird als **B2C_1_SiUp** (das Fragment **B2C\_1\_** wird automatisch hinzugefügt) auf dem Blatt **Registrierungsrichtlinien** angezeigt.

Öffnen Sie die Richtlinie, indem Sie auf **B2C_1_SiUp** klicken.

Wählen Sie in der Dropdownliste **Anwendungen** die App **Contoso B2C** und `https://localhost:44321/` in der Dropdownliste **Reply URL/Redirect URI** aus.

Klicken Sie auf **Jetzt ausführen**. Eine neue Browserregisterkarte wird geöffnet, und Sie können die Benutzeroberfläche für die Registrierung bei Ihrer Anwendung durchgehen.

> [!NOTE]
> Es dauert bis zu einer Minute, bis die Erstellung und Aktualisierung von Richtlinien wirksam wird.
>