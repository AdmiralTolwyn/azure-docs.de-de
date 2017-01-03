---
title: "Automatische Geräteregistrierung bei Azure Active Directory für in Domänen eingebundene Windows-Geräte | Microsoft Docs"
description: "IT-Administratoren können Ihre in Domänen eingebundenen Windows-Geräte automatisch und im Hintergrund bei Azure Active Directory (Azure AD) registrieren."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 5c387c5355957fea0ccffe58e707fae3d2e77c34


---
# <a name="automatic-device-registration-with-azure-active-directory-for-windows-domain-joined-devices"></a>Automatische Geräteregistrierung bei Azure Active Directory für in Domänen eingebundene Windows-Geräte
Als IT-Administrator können Sie Ihre in Domänen eingebundenen Windows-Geräte automatisch und im Hintergrund bei Azure Active Directory (Azure AD) registrieren. Dies ist hilfreich, wenn Sie gerätebasierte Richtlinien für den bedingten Zugriff auf Office 365-Anwendungen oder Anwendungen, die lokal von AD FS verwaltet werden, konfiguriert haben. Weitere Informationen über die Geräteregistrierungsszenarien finden Sie in der [Übersicht zur Azure Active Directory-Geräteregistrierung](active-directory-conditional-access-device-registration-overview.md).

> HINWEIS: Aktuelle Anweisungen zum Einrichten der automatischen Geräteregistrierung finden Sie unter [Einrichten der automatischen Registrierung von in die Domäne eingebundenen Windows-Geräten bei Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).
> 
> 

Automatische Geräteregistrierung bei Azure Active Directory steht für Windows 7- und Windows 8.1-Computer zur Verfügung, die einer Active Directory-Domäne beigetreten sind. In der Regel sind dies Computer, die sich im Besitz von Unternehmen befinden und für Information-Worker zur Verfügung gestellt wurden.

Beachten Sie die folgenden Voraussetzungen, um Ihre in Domänen eingebundenen Windows-Geräte bei Azure AD zu registrieren. Nachdem Sie die Voraussetzungen erfüllt haben, konfigurieren Sie automatische Geräteregistrierung für Ihre in Domänen eingebundenen Windows-Geräte.

## <a name="prerequisites-for-automatic-device-registration-of-domain-joined-windows-devices-with-azure-active-directory"></a>Voraussetzungen für die automatische Geräteregistrierung von in Domänen eingebundenen Windows-Geräten bei Azure Active Directory
## <a name="deploy-ad-fs-and-connect-to-azure-active-directory-using-azure-active-directory-connect"></a>Bereitstellen von AD FS und Verbinden mit Azure Active Directory mittels Azure Active Directory Connect
1. Verwenden Sie Azure Active Directory, um Active Directory-Verbunddienste (AD FS) mit Windows Server 2012 R2 bereitzustellen, und richten Sie eine Verbundbeziehung mit Azure Active Directory ein.
2. Konfigurieren einer zusätzlichen Anspruchsregel für die Vertrauensstellung der vertrauenden Seite für Azure Active Directory
3. Öffnen Sie die AD FS-Verwaltungskonsole, und navigieren Sie zu **AD FS**>**Vertrauensstellungen > Vertrauensstellungen der vertrauenden Seite**. Klicken Sie mit der rechten Maustaste auf das Vertrauensstellungsobjekt der vertrauenden Seite, "Microsoft Office 365 Identity Platform", und wählen Sie dann **Anspruchsregeln bearbeiten…**
4. Wählen Sie auf der Registerkarte **Ausstellungstransformationsregeln** die Option **Regel hinzufügen** aus.
5. Wählen Sie im Vorlagendropdownfeld **Anspruchsregel** die Option **Ansprüche mit benutzerdefinierter Regel senden** aus. Wählen Sie **Weiter**.
6. Geben Sie *Authentifizierungsmethode Anspruchsregel* in das Textfeld **Anspruchsregelname:** ein.
7. Geben Sie die folgende Anspruchsregel in das Textfeld **Claim rule** ein:
   
        c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
        => issue(claim = c);
8. Klicken Sie zwei Mal auf **OK** , um das Dialogfeld zu schließen.

## <a name="configure-an-additional-azure-active-directory-relying-party-trust-authentication-class-reference"></a>Konfigurieren eines zusätzlichen Authentifizierungsklassenverweises für die Vertrauensstellung der vertrauenden Seite für Azure Active Directory
Öffnen Sie auf Ihrem Verbundserver ein Windows PowerShell-Befehlsfenster, und geben Sie dann Folgendes ein:

  `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

Dabei ist <RPObjectName> der Objektname der vertrauenden Seite für Ihr Azure Active Directory-Vertrauensstellungsobjekt der vertrauenden Seite. Dieses Objekt trägt normalerweise den Namen "Microsoft Office 365 Identity Platform".

## <a name="ad-fs-global-authentication-policy"></a>Globale Authentifizierungsrichtlinie für AD FS
Konfigurieren Sie die globale primäre Authentifizierungsrichtlinie für AD FS, um die integrierte Windows-Authentifizierung für das Intranet zuzulassen (Dies ist die Standardeinstellung).

## <a name="internet-explorer-configuration"></a>Internet Explorer-Konfiguration
Konfigurieren Sie die folgenden Einstellungen in Internet Explorer auf Ihren Windows-Geräten für die Sicherheitszone "Lokales Intranet":

* Fordern Sie nicht zur Clientzertifikatauswahl auf, wenn nur ein Zertifikat vorhanden ist: **Aktivieren**.
* Skripterstellung zulassen: **Aktivieren**
* Automatisches Anmelden nur in der Intranetzone: **Markiert**

Dies sind die Standardeinstellungen für die Sicherheitszone "Lokales Intranet" des Internet Explorers. Sie können diese Einstellungen im Internet Explorer anzeigen oder verwalten, indem Sie zu **Internetoptionen** > **Sicherheit** > „Lokales Intranet“ > „Stufe anpassen“ navigieren. Sie können diese Einstellungen auch mithilfe der Active Directory-Gruppenrichtlinie konfigurieren.

## <a name="network-connectivity"></a>Netzwerkverbindung
In Domänen eingebundene Windows-Gerät müssen über eine Verbindung zu AD FS und Active Directory-Domänencontroller verfügen, um sich automatisch bei Azure AD zu registrieren. Dies bedeutet normalerweise, dass der Computer mit dem Unternehmensnetzwerk verbunden sein muss. Dies kann eine Kabelverbindung, Wi-Fi-Verbindung, DirectAccess oder VPN einschließen.

## <a name="configure-azure-active-directory-device-registration-discovery"></a>Konfigurieren der Ermittlung für die Azure Active Directory-Geräteregistrierung
Windows 7 und Windows 8.1-Geräte ermitteln den Geräteregistrierungsserver, indem sie den Benutzerkontonamen mit dem Namen eines bekannten Geräteregistrierungsservers kombinieren. Sie müssen einen DNS CNAME-Eintrag erstellen, der auf den A-Eintrag verweist, der Ihrem Azure Active Directory-Geräteregistrierungsdienst zugeordnet ist. Der CNAME-Eintrag muss das bekannte Präfix **enterpriseregistration** verwenden, gefolgt vom UPN-Suffix, das von den Benutzerkonten in Ihrer Organisation verwendet wird. Wenn Ihre Organisation mehrere UPN-Suffixe verwendet, müssen in DNS mehrere CNAME-Einträge erstellt werden.

Wenn Sie beispielsweise in Ihrer Organisation zwei UPN-Suffixe namens @contoso.com und @region.contoso.com, verwenden, erstellen Sie die folgenden DNS-Einträge.

| Eintrag | Typ | Adresse |
| --- | --- | --- |
| enterpriseregistration.contoso.com |CNAME |enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com |CNAME |enterpriseregistration.windows.net |

## <a name="configure-automatic-device-registration-for-windows-7-and-windows-81-domain-joined-devices"></a>Konfigurieren der automatischen Geräteregistrierung für in eine Domäne eingebundene Windows 7- und Windows 8.1-Geräte
Konfigurieren Sie die automatische Geräteregistrierung für Ihre in eine Domäne eingebundene Windows 7- und Windows 8.1-Geräte anhand der folgenden Links. Stellen Sie sicher, dass Sie die oben genannten Voraussetzungen abgeschlossen haben, bevor Sie fortfahren.

* [Konfigurieren der automatischen Geräteregistrierung für in eine Domäne eingebundene Windows 8.1-Geräte](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
* [Konfigurieren der automatischen Geräteregistrierung für in eine Domäne eingebundene Windows 7-Geräte](active-directory-conditional-access-automatic-device-registration-windows7.md)
* [Automatische Geräteregistrierung bei Azure Active Directory für in Domänen eingebundene Windows 10-Geräte](active-directory-azureadjoin-devices-group-policy.md)

## <a name="additional-notes"></a>Zusätzliche Hinweise
Geräteregistrierung bei Azure AD bietet die breiteste Palette von Gerätefunktionen. Mit Azure AD-Geräteregistrierung können Sie persönliche mobile Geräte (BYOD) und in Domänen eingebundene firmeneigene Geräte registrieren. Die Geräte können mit beiden gehosteten Diensten, wie z. B. Office 365 und Diensten, die lokal mit AD FS verwaltet werden, verwendet werden.

Unternehmen, die sowohl mobile als auch herkömmliche Geräte oder Office 365, Azure AD oder andere Microsoft-Dienste verwenden, sollten Geräte bei Azure AD mithilfe des Azure AD-Geräteregistrierungsdienstes registrieren. Wenn Ihr Unternehmen keine mobilen Geräte und keine Microsoft-Dienste wie z. B. Office 365, Azure AD oder Microsoft Intune verwendet und stattdessen nur lokale Anwendungen hostet, dann können Sie Geräte mittels AD FS bei Active Directory registrieren.

[Hier](https://technet.microsoft.com/library/dn486831.aspx)erfahren Sie mehr über das Bereitstellen der Geräteregistrierung mit AD FS.

## <a name="additional-topics"></a>Weitere Themen
* [Azure Active Directory-Geräteregistrierung – Übersicht](active-directory-conditional-access-device-registration-overview.md)
* [Konfigurieren der automatischen Geräteregistrierung für in eine Domäne eingebundene Windows 7-Geräte](active-directory-conditional-access-automatic-device-registration-windows7.md)
* [Konfigurieren der automatischen Geräteregistrierung für in eine Domäne eingebundene Windows 8.1-Geräte](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
* [Automatische Geräteregistrierung bei Azure Active Directory für in Domänen eingebundene Windows 10-Geräte](active-directory-azureadjoin-devices-group-policy.md)




<!--HONumber=Dec16_HO4-->


