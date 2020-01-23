---
title: 'Azure Active Directory: Vorgänge und Berichte zum Kennwortschutz'
description: 'Azure AD-Kennwortschutz: Vorgänge und Berichterstellung nach der Bereitstellung'
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8602a9c5b69c47c2f663dab461394ef5e1a9bed
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75762857"
---
# <a name="azure-ad-password-protection-operational-procedures"></a>Verfahren des Azure AD-Kennwortschutzes

Nachdem Sie die [Installation des Azure AD-Kennwortschutzes](howto-password-ban-bad-on-premises-deploy.md) vor Ort abgeschlossen haben, müssen einige Elemente im Azure-Portal konfiguriert werden.

## <a name="configure-the-custom-banned-password-list"></a>Konfigurieren der Liste benutzerdefinierter gesperrter Kennwörter

Befolgen Sie die Anweisungen im Artikel [Konfigurieren der Liste benutzerdefinierter gesperrter Kennwörter](howto-password-ban-bad-configure.md), um die Liste benutzerdefinierter Kennwörter für Ihre Organisation anzupassen.

## <a name="enable-password-protection"></a>Aktivieren des Kennwortschutzes

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Authentifizierungsmethoden** > **Kennwortschutz**.
1. Legen Sie **Kennwortschutz für Windows Server Active Directory aktivieren** auf **Ja** fest.
1. Wie im [Bereitstellungshandbuch](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy) bereits erwähnt, wird empfohlen, den **Modus** zunächst auf **Überwachung** festzulegen.
   * Wenn Sie mit dem Feature vertraut sind, können Sie zum **Modus** **Erzwingen** wechseln.
1. Klicken Sie unten auf der Seite auf **Speichern**.

![Aktivieren der Komponenten des Azure AD-Kennwortschutzes im Azure-Portal](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Überwachungsmodus

Der Überwachungsmodus ist als Möglichkeit gedacht, die Software in einem „Was-wäre-wenn“-Modus auszuführen. Jeder DC-Agentendienst wertet ein eingehendes Kennwort entsprechend der aktuell aktiven Richtlinie aus. Wenn die aktuelle Richtlinie im Überwachungsmodus konfiguriert ist, führen „unzulässige“ Kennwörter zu Ereignismeldungen, werden aber akzeptiert. Dies ist der einzige Unterschied zwischen dem Überwachungs-- und dem Erzwingungsmodus; alle anderen Vorgänge laufen gleich ab.

> [!NOTE]
> Microsoft empfiehlt, dass die anfängliche Bereitstellung und das Testen immer im Überwachungsmodus beginnen. Ereignisse im Ereignisprotokoll sollten dann überwacht werden, um vorauszusehen, ob bestehende Betriebsvorgänge gestört werden würden, sobald der Erzwingungsmodus aktiviert ist.

## <a name="enforce-mode"></a>Erzwingungsmodus

Der Erzwingungsmodus dient als endgültigen Konfiguration. Genauso wie beim oben aufgeführten Überwachungsmodus wertet jeder DC-Agentendienst eingehende Kennwörter entsprechend der aktuell aktiven Richtlinie aus. Ist allerdings der Erzwingungsmodus aktiviert, wird ein Kennwort, das entsprechend der Richtlinie als unsicher angesehen wird, abgelehnt.

Wenn ein Kennwort im Erzwingungsmodus vom DC-Agenten des Azure AD Kennwortschutzes abgelehnt wird, ist die sichtbare Auswirkung für den Endbenutzer identisch mit dem, was er sehen würde, wenn sein Kennwort von der herkömmlichen lokalen Kennwortkomplexitätserzwingung abgelehnt werden würde. Zum Beispiel könnte ein Benutzer die folgende herkömmliche Fehlermeldung im Windows-Bildschirm zum Anmelden bzw. Ändern des Kennworts sehen:

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

Diese Meldung ist nur ein Beispiel von mehreren möglichen Ergebnissen. Die spezifische Fehlermeldung kann je nach Software oder Szenario, das versucht, ein unsicheres Kennwort festzulegen, variieren.

Betroffene Endbenutzer müssen sich möglicherweise bei ihren IT-Mitarbeitern über die neuen Anforderungen informieren, um sichere Kennwörter wählen zu können.

> [!NOTE]
> Der Kennwortschutz für Azure AD hat keine Kontrolle über die speziellen Fehlermeldungen, die beim Ablehnen eines schwachen Kennworts vom Clientcomputer angezeigt werden.

## <a name="enable-mode"></a>Modus aktivieren

Diese Einstellung sollte im standardmäßig aktivierten Zustand (Ja) bleiben. Wenn Sie diese Einstellung auf „deaktiviert“ (Nein) festlegen, führt dies dazu, dass alle bereitgestellten DC-Agents für den Azure AD-Kennwortschutz in einen Ruhemodus wechseln, in dem alle Kennwörter vorbehaltlos akzeptiert werden und keinerlei Validierungsaktivitäten ausgeführt werden (z.B. werden nicht einmal Überwachungsereignisse ausgelöst).

## <a name="next-steps"></a>Nächste Schritte

[Überwachung beim Kennwortschutz für Azure AD](howto-password-ban-bad-on-premises-monitor.md)
