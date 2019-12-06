---
title: Verhindern von Angriffen mithilfe von Smart Lockout – Azure Active Directory
description: Azure AD Smart Lockout trägt dazu bei, Ihre Organisation vor Brute-Force-Angriffen zu schützen, bei denen versucht wird, Kennwörter zu erraten.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 066c4cb598d9a8c14ab5d6ee893376266e104d15
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381532"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory Smart Lockout

Smart Lockout unterstützt Sie beim Sperren von Angreifern, die versuchen, Benutzerkennwörter zu erraten oder mithilfe von Brute-Force-Methoden in Ihr System einzudringen. Smart Lockout kann Anmeldungen gültiger Benutzer erkennen und anders behandeln als Anmeldungen von Angreifern und anderen unbekannten Quellen. Smart Lockout sperrt die Angreifer aus, während Ihre Benutzer weiterhin auf ihre Konten zugreifen und produktiv arbeiten können.

Nach zehn Fehlversuchen sperrt Smart Lockout das Konto standardmäßig eine Minute lang für Anmeldeversuche. Das Konto wird nach jedem weiteren fehlgeschlagenen Anmeldeversuch zuerst für eine Minute und bei anschließenden Versuchen länger gesperrt.

Smart Lockout verfolgt die letzten drei fehlerhaften Kennworthashes, um zu vermeiden, dass der Sperrungszähler für dasselbe Kennwort erhöht wird. Wenn eine Person mehrere Male das falsche Kennwort eingibt, wird dadurch keine Kontosperre verursacht.

 > [!NOTE]
 > Eine Funktion zur Hashnachverfolgung ist nicht für Kunden verfügbar, die die Pass-Through-Authentifizierung aktiviert haben, da die Authentifizierung lokal und nicht in der Cloud erfolgt.

Verbundbereitstellungen mit AD FS 2016 und AD FS 2019 können Ihnen mithilfe von [AD FS Extranet Lockout und Extranet Smart Lockout](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) ähnliche Vorteile verschaffen.

Smart Lockout ist für alle Azure AD-Kunden ständig aktiv und bietet standardmäßig die richtige Mischung aus Sicherheit und Nutzbarkeit. Wenn Sie die Smart Lockout-Einstellungen mit spezifischen Werten für Ihre Organisation konfigurieren möchten, benötigen Sie kostenpflichtige Azure AD-Lizenzen für Ihre Benutzer.

Die Verwendung von Smart Lockout garantiert nicht, dass ein echter Benutzer niemals gesperrt wird. Wenn ein Benutzerkonto durch Smart Lockout gesperrt wird, versuchen wir unser Bestes, nicht den echten Benutzer zu sperren. Der Sperrdienst versucht sicherzustellen, dass Angreifer keinen Zugriff auf die Konten echter Benutzer erhalten.  

* Sperren werden von den einzelnen Azure Active Directory-Rechenzentren unabhängig voneinander verfolgt. Ein Benutzer besitzt (Schwellenwert * Rechenzentrumsanzahl) Versuche, wenn der Benutzer auf die einzelnen Rechenzentren trifft.
* Ein böswilliger Benutzer und ein echter Benutzer werden von Smart Lockout über einen bekannten/unbekannten Standort unterschieden. Unbekannte und bekannte Standorte weisen jeweils separate Zähler für Sperren auf.

Smart Lockout ist in Hybridbereitstellungen integrierbar und kann mittels Kennworthashsynchronisierung oder Passthrough-Authentifizierung verhindern, dass lokale Active Directory-Konten von Angreifern gesperrt werden. Durch korrektes Festlegen von Smart Lockout-Richtlinien in Azure AD können Angriffe herausgefiltert werden, bevor sie die lokale Active Directory-Instanz erreichen.

Bei Verwendung der [Passthrough-Authentifizierung](../hybrid/how-to-connect-pta.md) muss Folgendes sichergestellt werden:

* Der Azure AD-Sperrschwellenwert ist **kleiner** als der Schwellenwert für eine Active Directory-Kontosperrung. Legen Sie die Werte so fest, dass der Schwellenwert für eine Active Directory-Kontosperrung mindestens das Zwei- oder Dreifache des Azure AD-Sperrschwellenwerts beträgt. 
* Die Azure AD-Sperrdauer muss länger als die Active Directory-Zurücksetzungsdauer des Kontosperrungszählers festgelegt sein. Beachten Sie, dass die Azure AD-Dauer in Sekunden und die AD-Dauer in Minuten festgelegt wird. 

Wenn Sie beispielsweise möchten, dass Ihr Azure AD-Zähler höher als der AD-Wert ist, dann ist die Azure AD-Dauer 120 Sekunden (2 Minuten), während Ihr lokales AD auf 1 Minute (60 Sekunden) festgelegt ist.

> [!IMPORTANT]
> Derzeit können die Cloudkonten der Benutzer nicht von einem Administrator entsperrt werden, wenn sie von Smart Lockout gesperrt wurden. Der Administrator muss warten, bis die Sperrdauer abgelaufen ist. Der Benutzer kann das Konto jedoch von einem vertrauenswürdigen Gerät oder Standort aus mithilfe der Self-Service-Kennwortzurücksetzung (SSPR) entsperren.

## <a name="verify-on-premises-account-lockout-policy"></a>Überprüfen der lokalen Kontosperrungsrichtlinie

Gehen Sie wie folgt vor, um Ihre lokale Active Directory-Kontosperrungsrichtlinie zu überprüfen:

1. Öffnen Sie das Gruppenrichtlinienverwaltungstool.
2. Bearbeiten Sie die Gruppenrichtlinie, die die Kontosperrungsrichtlinie Ihrer Organisation enthält (beispielsweise die **Standarddomänenrichtlinie**).
3. Navigieren Sie zu **Computerkonfiguration** > **Richtlinien** > **Windows-Einstellungen** > **Sicherheitseinstellungen** > **Kontorichtlinien** > **Kontosperrungsrichtlinien**.
4. Überprüfen Sie Ihre Werte für **Kontensperrungsschwelle** und **Zurücksetzungsdauer des Kontosperrungszählers**.

![Ändern der lokalen Active Directory-Kontosperrungsrichtlinie](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Verwalten der Werte für Azure AD Smart Lockout

Abhängig von den Anforderungen Ihrer Organisation müssen die Werte für Smart Lockout ggf. angepasst werden. Wenn Sie die Smart Lockout-Einstellungen mit spezifischen Werten für Ihre Organisation konfigurieren möchten, benötigen Sie kostenpflichtige Azure AD-Lizenzen für Ihre Benutzer.

Gehen Sie wie folgt vor, um die Smart Lockout-Werte zu überprüfen und ggf. für Ihre Organisation anzupassen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach *Azure Active Directory*, und wählen Sie diese Option aus. Wählen Sie **Authentifizierungsmethoden** > **Kennwortschutz** aus.
1. Legen Sie **Schwellenwert für Sperre** auf die Anzahl nicht erfolgreicher Anmeldeversuche fest, nach der das Konto erstmals gesperrt werden soll. (Standardwert: 10.)
1. Legen unter **Sperrdauer in Sekunden** fest, wie viele Sekunden die Sperre jeweils dauern soll. Der Standardwert ist 60 Sekunden (eine Minute).

> [!NOTE]
> Ist der erste Anmeldeversuch nach einer Sperre wieder nicht erfolgreich, wird das Konto erneut sperrt. Bei wiederholter Kontosperrung erhöht sich die Sperrdauer.

![Anpassen Azure AD Smart Lockout-Richtlinie über das Azure-Portal](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Ermitteln, ob das Smart Lockout-Feature funktioniert

Wenn der Schwellenwert von Smart Lockout ausgelöst wird, wird das Konto gesperrt und die folgende Meldung angezeigt:

**Ihr Konto wurde vorübergehend gesperrt, um eine unbefugte Nutzung zu verhindern. Versuchen Sie es später noch mal. Wenden Sie sich an Ihren Administrator, wenn das Problem weiterhin besteht.**

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie mithilfe von Azure AD ungültige Kennwörter aus Ihrer Organisation verbannen.](howto-password-ban-bad.md)
* [Konfigurieren Sie die Self-Service-Kennwortzurücksetzung, damit Benutzer ihre Konten selbst entsperren können.](quickstart-sspr.md)
