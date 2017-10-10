---
title: 'Azure AD Connect: Problembehandlung beim nahtlosen einmaligen Anmelden | Microsoft-Dokumentation'
description: "In diesem Thema wird beschrieben, wie Sie Probleme beim nahtlosen einmaligen Anmelden in Azure AD (nahtlose SSO in Azure AD) behandeln können."
services: active-directory
keywords: "Was ist Azure AD Connect, Active Directory installieren, erforderliche Komponenten für Azure AD, SSO, Single Sign-On, einmaliges Anmelden"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 7eea3621a52bf13dc44e89c342c503905ff24a0d
ms.contentlocale: de-de
ms.lasthandoff: 09/28/2017

---

# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Problembehandlung beim nahtlosen einmaligen Anmelden mit Azure Active Directory

In diesem Artikel finden Sie Informationen zur Problembehandlung bei bekannten Problemen mit der nahtlosen einmaligen Anmeldung in Azure AD.

## <a name="known-issues"></a>Bekannte Probleme

- Die Aktivierung der nahtlosen einmaligen Anmeldung kann in seltenen Fällen bis zu 30 Minuten dauern.
- Edge-Browser wird nicht unterstützt.
- Bei der Lizenzaktivierung für Office-Clients, insbesondere in Szenarios mit freigegebenen Computern, werden Benutzer zur erneuten Anmeldung aufgefordert.
- Das nahtlose einmalige Anmelden funktioniert in Firefox nicht im privaten Modus. und 
- Dies gilt auch für den Internet Explorer, wenn der erweiterte Schutzmodus aktiviert ist.
- Das nahtlose einmalige Anmelden funktioniert nicht in mobilen Browsern unter iOS und Android.
- Wenn Sie 30 und mehr AD-Gesamtstrukturen synchronisieren, kann die nahtlose einmalige Anmeldung nicht mit Azure AD Connect aktiviert werden. Zur Problembehebung können Sie die Funktion auf Ihrem Mandanten [manuell aktivieren](#manual-reset-of-azure-ad-seamless-sso).
- Wenn Sie Dienst-URLs von Azure AD (https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net) statt zur Zone „Lokales Intranet“ zur Zone „Vertrauenswürdige Sites“ hinzufügen, **können sich Benutzer nicht anmelden**.

## <a name="check-status-of-the-feature"></a>Überprüfen des Status des Features

Stellen Sie sicher, dass das nahtlose einmalige Anmelden für Ihren Mandanten noch **Aktiviert** ist. Um den Status zu überprüfen, können Sie zum **Azure AD Connect**-Blatt im [Azure Active Directory-Admin Center](https://aad.portal.azure.com/) navigieren.

![Azure Active Directory-Admin Center – Azure AD Connect-Blatt](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Gründe für Anmeldefehler im Azure Active Directory-Admin Center (Premium-Lizenz erforderlich)

Wenn Ihrem Mandanten eine Azure AD Premium-Lizenz zugeordnet ist, können Sie sich im [Azure Active Directory-Admin Center](https://aad.portal.azure.com/) auch den [Bericht zu den Anmeldeaktivitäten](../active-directory-reporting-activity-sign-ins.md) ansehen.

![Azure Active Directory-Admin Center – Bericht zu Anmeldeaktivitäten](./media/active-directory-aadconnect-sso/sso9.png)

Navigieren Sie im [Azure Active Directory Admin Center](https://aad.portal.azure.com/) zu **Azure Active Directory** -> **Anmeldungen**, und klicken Sie auf die Anmeldeaktivität eines bestimmten Benutzers. Suchen Sie nach dem Feld **Code des Anmeldefehlers**. Ordnen Sie den Wert in diesem Feld mithilfe der folgenden Tabelle einer Ursache und einer Lösung zu:

|Anmeldefehler|Grund des Anmeldefehlers|Lösung
| --- | --- | ---
| 81001 | Das Kerberos-Ticket des Benutzers ist zu groß. | Reduzieren Sie die Gruppenmitgliedschaften des Benutzers aus, und versuchen Sie es erneut.
| 81002 | Das Kerberos-Ticket des Benutzers kann nicht überprüft werden. | Informationen hierzu finden Sie in der [Checkliste zur Problembehandlung](#troubleshooting-checklist).
| 81003 | Das Kerberos-Ticket des Benutzers kann nicht überprüft werden. | Informationen hierzu finden Sie in der [Checkliste zur Problembehandlung](#troubleshooting-checklist).
| 81004 | Fehler beim Versuch, die Kerberos-Authentifizierung durchzuführen. | Informationen hierzu finden Sie in der [Checkliste zur Problembehandlung](#troubleshooting-checklist).
| 81008 | Das Kerberos-Ticket des Benutzers kann nicht überprüft werden. | Informationen hierzu finden Sie in der [Checkliste zur Problembehandlung](#troubleshooting-checklist).
| 81009 | Das Kerberos-Ticket des Benutzers kann nicht überprüft werden. | Informationen hierzu finden Sie in der [Checkliste zur Problembehandlung](#troubleshooting-checklist).
| 81010 | Fehler beim nahtlosen einmaligen Anmelden, da das Kerberos-Ticket des Benutzers abgelaufen oder ungültig ist. | Der Benutzer muss sich über ein in eine Domäne eingebundenes Gerät in Ihrem Unternehmensnetzwerk anmelden.
| 81011 | Das Benutzerobjekt konnte anhand der Informationen im Kerberos-Ticket des Benutzers nicht gefunden werden. | Verwenden Sie Azure AD Connect, um Benutzerinformationen in Azure AD zu synchronisieren.
| 81012 | Der Benutzer, der versucht, sich an Azure AD anzumelden, unterscheidet sich von dem Benutzer, der am Gerät angemeldet ist. | Melden Sie sich über ein anderes Gerät an.
| 81013 | Das Benutzerobjekt konnte anhand der Informationen im Kerberos-Ticket des Benutzers nicht gefunden werden. |Verwenden Sie Azure AD Connect, um Benutzerinformationen in Azure AD zu synchronisieren. 

## <a name="troubleshooting-checklist"></a>Checkliste zur Problembehandlung

Verwenden Sie die folgende Checkliste zur Behebung von Problemen in Bezug auf die nahtlose einmalige Anmeldung:

- Überprüfen Sie, ob die Funktion zur nahtlosen einmaligen Anmeldung in Azure AD Connect aktiviert ist. Wenn Sie die Funktion (z.B. aufgrund eines blockierten Ports) nicht aktivieren können, vergewissern Sie sich, dass Sie alle [Voraussetzungen](active-directory-aadconnect-sso-quick-start.md#step-1-check-prerequisites) erfüllen.
- Prüfen Sie, ob beide Dienst-URLs von Azure AD (https://autologon.microsoftazuread-sso.com und https://aadg.windows.net.nsatc.net) Teil der Intranetzoneneinstellungen des Benutzers sind.
- Stellen Sie sicher, dass das Unternehmensgerät mit der AD-Domäne verknüpft ist.
- Stellen Sie sicher, dass der Benutzer mit einem AD-Domänenkonto beim Gerät angemeldet ist.
- Stellen Sie sicher, dass das Benutzerkonto aus einer AD-Gesamtstruktur stammt, in der das nahtlose einmalige Anmelden eingerichtet wurde.
- Stellen Sie sicher, dass das Gerät mit dem Unternehmensnetzwerk verbunden ist.
- Stellen Sie sicher, dass die Uhrzeit des Geräts mit der Uhrzeit von Active Directory und der des Domänencontrollers synchronisiert ist und diese nicht mehr als fünf Minuten voneinander abweichen.
- Listen Sie vorhandene Kerberos-Tickets auf dem Gerät mit dem Befehl **klist** über eine Eingabeaufforderung auf. Prüfen Sie, ob die für das Computerkonto `AZUREADSSOACCT` ausgestellten Tickets vorhanden sind. Die Kerberos-Tickets von Benutzern sind normalerweise 12 Stunden gültig. Sie haben diese Einstellung in Active Directory möglicherweise anders festgelegt.
- Löschen Sie vorhandene Kerberos-Tickets auf dem Gerät mit dem Befehl **klist purge**, und wiederholen Sie den Vorgang.
- Überprüfen Sie die Konsolenprotokolle des Browsers (unter „Entwicklertools“), um zu ermitteln, ob Probleme vorliegen, die sich auf JavaScript beziehen.
- Schauen Sie sich außerdem die [Domänencontrollerprotokolle](#domain-controller-logs) an.

### <a name="domain-controller-logs"></a>Domänencontrollerprotokolle

Wenn die erfolgreiche Überwachung auf Ihrem Domänencontroller aktiviert ist, wird bei jeder nahtlosen einmaligen Anmeldung durch einen Benutzer ein Sicherheitseintrag im Ereignisprotokoll erfasst. Sie finden diese Sicherheitsereignisse mithilfe der folgenden Abfrage (suchen Sie nach Ereignis **4769** in Verbindung mit dem Computerkonto **AzureADSSOAcc$**):

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Manuelle Zurücksetzung des Features

Wenn die Problembehandlung nicht hilft, können Sie die Funktion auf Ihrem Mandanten manuell zurücksetzen. Führen Sie diese Schritte auf dem lokalen Server durch, auf dem Azure AD Connect ausgeführt wird:

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Schritt 1: Importieren Sie das PowerShell-Modul „Nahtlose SSO“

1. Laden Sie zuerst den [Microsoft Online Services-Anmelde-Assistenten](http://go.microsoft.com/fwlink/?LinkID=286152) herunter, und installieren Sie ihn.
2. Laden Sie anschließend das [Azure Active Directory-Modul für Windows PowerShell (64 Bit)](http://go.microsoft.com/fwlink/p/?linkid=236297)herunter, und installieren Sie es.
3. Navigieren Sie zum Ordner `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Importieren Sie das PowerShell-Modul „Nahtlose SSO“ mit folgendem Befehl: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-ad-forests-on-which-seamless-sso-has-been-enabled"></a>Schritt 2: Rufen Sie die Liste der AD-Gesamtstrukturen ab, für die nahtlose einmaliges Anmelden aktiviert wurde.

1. Führen Sie PowerShell als Administrator aus. Rufen Sie in PowerShell `New-AzureADSSOAuthenticationContext` auf. Geben Sie die Anmeldeinformationen des globalen Administrators Ihres Mandanten an, wenn Sie dazu aufgefordert werden.
2. Rufen Sie `Get-AzureADSSOStatus` auf. Dadurch erhalten Sie die Liste der AD-Gesamtstrukturen (siehe die Liste „Domänen“), in denen diese Funktion aktiviert ist.

### <a name="step-3-disable-seamless-sso-for-each-ad-forest-that-it-was-set-it-up-on"></a>Schritt 3: Deaktivieren Sie die nahtlose SSO für jede AD-Gesamtstruktur, auf der sie eingerichtet wurde.

1. Rufen Sie `$creds = Get-Credential` auf. Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen des Domänenadministrators für die vorgesehene AD-Gesamtstruktur ein.
2. Rufen Sie `Disable-AzureADSSOForest -OnPremCredentials $creds` auf. Mit diesem Befehl wird das Computerkonto `AZUREADSSOACCT` vom lokalen Domänencontroller für diese spezifische AD-Gesamtstruktur entfernt.
3. Wiederholen Sie die oben stehenden Schritte für jede AD-Gesamtstruktur, für die Sie das Feature eingerichtet haben.

### <a name="step-4-enable-seamless-sso-for-each-ad-forest"></a>Schritt 4: Aktivieren Sie die nahtlose SSO für jede AD-Gesamtstruktur.

1. Rufen Sie `Enable-AzureADSSOForest` auf. Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen des Domänenadministrators für die vorgesehene AD-Gesamtstruktur ein.
2. Wiederholen Sie die oben stehenden Schritte für jede AD-Gesamtstruktur, für die Sie das Feature eingerichtet haben.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Schritt 5: Aktivieren Sie das Feature für Ihren Mandanten.

Rufen Sie `Enable-AzureADSSO` auf, und geben Sie „TRUE“ bei der Aufforderung `Enable: ` ein, um das Feature in Ihrem Mandanten zu aktivieren.

