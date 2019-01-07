---
title: Kennwortkomplexität in Azure Active Directory B2C | Microsoft-Dokumentation
description: Konfigurieren der Komplexitätsanforderungen für Kennwörter, die von Kunden in Azure Active Directory B2C bereitgestellt werden
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 88609f4daac176f082e7f4962d557267946ab98c
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724433"
---
# <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C: Konfigurieren von Komplexitätsanforderungen für Kennwörter

> [!NOTE]
> **Dieses Feature befindet sich in der öffentlichen Vorschau.**

Azure Active Directory B2C (Azure AD B2C) unterstützt das Ändern der Komplexitätsanforderungen für Kennwörter, die von einem Endbenutzer beim Erstellen eines Kontos bereitgestellt werden.  Standardmäßig verwendet Azure AD B2C `Strong` Kennwörter.  Azure AD B2C unterstützt außerdem Konfigurationsoptionen zur Steuerung der Komplexität von Kennwörtern, die Kunden verwenden können.

## <a name="when-password-rules-are-enforced"></a>Wann werden Kennwortregeln erzwungen?

Bei der Registrierung oder dem Zurücksetzen des Kennworts muss ein Endbenutzer ein Kennwort angeben, das die Regeln für die Komplexität erfüllt.  Kennwortkomplexitätsregeln werden pro Benutzerflow erzwungen.  Es ist möglich, dass ein Benutzerflow eine vierstellige PIN zur Registrierung fordert, ein anderer Benutzerflow hingegen eine Zeichenfolge von acht Zeichen.  Sie können z.B. einen Benutzerflow mit unterschiedlicher Kennwortkomplexität für Erwachsene und Kinder verwenden.

Kennwortkomplexität wird nie während der Anmeldung erzwungen.  Benutzer werden nie während der Anmeldung aufgefordert, ihr Kennwort zu ändern, da es die aktuellen Komplexitätsanforderungen nicht erfüllt.

In folgenden Typen von Benutzerflows kann die Kennwortkomplexität konfiguriert werden:

* Benutzerflow für die Registrierung oder Anmeldung
* Benutzerflow für die Kennwortzurücksetzung
* Benutzerdefinierte Richtlinie ([Konfigurieren der Kennwortkomplexität in benutzerdefinierten Richtlinien](active-directory-b2c-reference-password-complexity-custom.md))

## <a name="how-to-configure-password-complexity"></a>Gewusst wie: Konfigurieren der Kennwortkomplexität

1. Öffnen Sie **Benutzerflows**.
2. Wählen Sie einen Benutzerflow aus, und klicken Sie auf **Eigenschaften**.
3. Ändern Sie unter **Kennwortkomplexität** die Kennwortkomplexität für diesen Benutzerflow in **Einfach**, **Stark** oder **Benutzerdefiniert**.

### <a name="comparison-chart"></a>Vergleichsdiagramm

| Komplexität | BESCHREIBUNG |
| --- | --- |
| Einfach | Ein Kennwort mit mindestens 8 bis 64 Zeichen. |
| STARK (Strong) | Ein Kennwort mit mindestens 8 bis 64 Zeichen. Es sind 3 der 4 Zeichentypen Ziffern, Groß- und Kleinbuchstaben oder Symbole erforderlich. |
| Benutzerdefiniert | Diese Option bietet die bestmögliche Steuerung der Kennwortkomplexitätsregeln.  Sie ermöglicht das Konfigurieren einer benutzerdefinierten Länge.  Darüber hinaus ermöglicht sie das ausschließliche Akzeptieren von Kennwörtern, die nur aus Ziffern (PINs) bestehen. |

## <a name="options-available-under-custom"></a>Unter „Benutzerdefiniert“ verfügbare Optionen

### <a name="character-set"></a>Zeichensatz

Ermöglicht Ihnen, wahlweise nur Ziffern (PIN) oder den vollständigen Zeichensatz zu akzeptieren.

* **Nur Ziffern** lässt nur Ziffern (0-9) bei der Kennworteingabe zu.
* **Alle** lässt beliebige Buchstaben, Ziffern oder Symbole zu.

### <a name="length"></a>Länge

Ermöglicht Ihnen, die Längenanforderungen des Kennworts zu steuern.

* **Mindestlänge** muss mindestens 4 sein.
* **Maximale Länge** muss größer oder gleich der Mindestlänge und darf höchstens 64 Zeichen lang sein.

### <a name="character-classes"></a>Zeichenklassen

Ermöglicht Ihnen, die verschiedenen im Kennwort verwendeten Zeichentypen zu steuern.

* **2 von 4: Kleinbuchstaben, Großbuchstaben, Ziffern (0–9), Symbole** stellt sicher, dass das Kennwort mindestens zwei Zeichentypen enthält. Beispielsweise eine Ziffer und einen Kleinbuchstaben.
* **3 von 4: Kleinbuchstaben, Großbuchstaben, Ziffern (0–9), Symbole** stellt sicher, dass das Kennwort mindestens drei Zeichentypen enthält. Beispielsweise eine Ziffer, einen Kleinbuchstaben und einen Großbuchstaben.
* **4 von 4: Kleinbuchstaben, Großbuchstaben, Ziffern (0–9), Symbole** stellt sicher, dass das Kennwort alle vier Zeichentypen enthält.

    > [!NOTE]
    > **4 von 4** zu fordern, kann beim Endbenutzer für Frustration sorgen. Studien haben gezeigt, dass diese Anforderung die Kennwortentropie nicht verbessert. Siehe [NIST Password Guidelines](https://pages.nist.gov/800-63-3/sp800-63b.html#appA) (NIST-Kennwortrichtlinien).
