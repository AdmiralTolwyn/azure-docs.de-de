---
title: Konfigurieren von sicherem LDAP (LDAPS) in Azure AD Domain Services | Microsoft Docs
description: Konfigurieren von sicherem LDAP (LDAPS) für eine durch Azure AD Domain Services verwaltete Domäne
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: maheshu
ms.openlocfilehash: 81986fdd9cbfbeb41c794e2364bf7bfea1069742
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211253"
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Konfigurieren von sicherem LDAP (LDAPS) für eine durch Azure AD Domain Services verwaltete Domäne

## <a name="before-you-begin"></a>Voraussetzungen
Stellen Sie sicher, dass Sie [Aufgabe 2: Exportieren der Zertifikate für sicheres LDAP in eine PFX-Datei](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md) fertiggestellt haben.


## <a name="task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>Aufgabe 3: Aktivieren von sicherem LDAP für die verwaltete Domäne über das Azure-Portal
Führen Sie die folgenden Konfigurationsschritte aus, um sicheres LDAP zu aktivieren:

1. Navigieren Sie zum **[Azure-Portal](https://portal.azure.com)**.

2. Suchen Sie nach „Domänendienste“ im Suchfeld **Search resources** (Ressourcen suchen). Wählen Sie aus dem Suchergebnis **Azure AD Domain Services** aus. Die Seite **Azure AD Domain Services** listet Ihre verwaltete Domäne auf.

    ![Suchen der bereitgestellten verwalteten Domäne](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Klicken Sie auf den Namen der verwalteten Domäne (z.B. „contoso100.com“), um weitere Details zu der Domäne zu erhalten.

    ![Domänendienste – Bereitstellungsstatus](./media/getting-started/domain-services-provisioning-state.png)

3. Klicken Sie im Navigationsbereich auf **Secure LDAP**.

    ![Domänendienste – „Secure LDAP“-Seite](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. Standardmäßig ist der sichere LDAP-Zugriff auf Ihre verwaltete Domäne deaktiviert. Ändern Sie die Einstellung für **Secure LDAP** in **Aktivieren**.

    ![Aktivieren von sicherem LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. Standardmäßig ist der sichere LDAP-Zugriff auf Ihre verwaltete Domäne über das Internet deaktiviert. Ändern Sie, falls gewünscht, die Einstellung für **Sicheren LDAP-Zugriff über das Internet aktivieren** in **Aktivieren**. 

    > [!WARNING]
    > Wenn Sie sicheren LDAP-Zugriff über das Internet aktivieren, ist Ihre Domäne anfällig für über das Internet ausgeführte Brute-Force-Angriffe mit Kennwörtern. Daher sollten Sie eine NSG einrichten, um den Zugriff auf erforderliche Quell-IP-Adressbereiche zu sperren. Beachten Sie die Anweisungen unter [Aufgabe 5: LDAPS-Zugriff auf Ihre verwaltete Domäne über das Internet sperren](#task-5---lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet).
    >

6. Klicken Sie auf das Ordnersymbol hinter der **PFX-Datei mit sicherem LDAP-Zertifikat**. Geben Sie den Pfad zu der PFX-Datei mit dem Zertifikat für den sicheren LDAP-Zugriff auf die verwaltete Domäne an.

7. Geben Sie das **Kennwort zum Entschlüsseln der PFX-Datei** an. Geben Sie das gleiche Kennwort an, das Sie beim Exportieren des Zertifikats in die PFX-Datei verwendet haben.

8. Wenn Sie fertig sind, klicken Sie auf die Schaltfläche **Speichern**.

9. Sie sehen eine Benachrichtigung, die Sie darüber informiert, dass sicheres LDAP für die verwaltete Domäne konfiguriert wird. Bis dieser Vorgang abgeschlossen ist, können Sie andere Einstellungen der Domäne nicht ändern.

    ![Konfigurieren von sicherem LDAP für die verwaltete Domäne](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> Es dauert ungefähr 10 bis 15 Minuten, bis sicheres LDAP für die verwaltete Domäne aktiviert ist. Wenn das bereitgestellte Zertifikat für sicheres LDAP nicht den erforderlichen Kriterien entspricht, wird LDAPS nicht für Ihr Verzeichnis aktiviert, und Sie sehen einen Fehler. Mögliche Fehlerursachen wären etwa ein falscher Domänenname oder ein bereits abgelaufenes oder bald ablaufendes Zertifikat. Wiederholen Sie in diesem Fall den Vorgang mit einem gültigen Zertifikat.
>
>

<br>

## <a name="task-4---configure-dns-to-access-the-managed-domain-from-the-internet"></a>Aufgabe 4: Konfigurieren von DNS für den Zugriff auf die verwaltete Domäne über das Internet
> [!NOTE]
> **Optionale Aufgabe** : Wenn Sie nicht über eine Internetverbindung mit LDAPS auf die verwaltete Domäne zugreifen möchten, überspringen Sie diese Konfigurationsaufgabe.
>
>

Vor dem Ausführen dieser Aufgabe müssen Sie die in [Aufgabe 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview)beschriebenen Schritte vollständig ausgeführt haben.

Nachdem Sie den Zugriff auf Ihre verwaltete Domäne über sicheres LDAP und eine Internetverbindung aktiviert haben, müssen Sie das DNS aktualisieren, damit die Clientcomputer die verwaltete Domäne finden können. Am Ende von Aufgabe 3 wird auf der Registerkarte **Eigenschaften** im Feld **EXTERNE IP-ADRESSE FÜR LDAPS-ZUGRIFF** eine externe IP-Adresse angezeigt.

Konfigurieren Sie Ihren externen DNS-Anbieter so, dass der DNS-Name der verwalteten Domäne (beispielsweise „ldaps.contoso100.com“) auf diese externe IP-Adresse verweist. Erstellen Sie beispielsweise den folgenden DNS-Eintrag:

    ldaps.contoso100.com  -> 52.165.38.113

Das ist schon alles. Sie können jetzt über das Internet und sicheres LDAP eine Verbindung mit der verwalteten Domäne herstellen.

> [!WARNING]
> Denken Sie daran, dass die Clientcomputer dem Aussteller des Zertifikats für LDAPS vertrauen müssen, damit die Verbindung mit der verwalteten Domäne über LDAPS erfolgreich hergestellt werden kann. Wenn Sie eine für die Öffentlichkeit vertrauenswürdige Zertifizierungsstelle verwenden, ist kein weiterer Schritt erforderlich, da Clientcomputer diesen Zertifikatausstellern vertrauen. Wenn Sie ein selbstsigniertes Zertifikat verwenden, müssen Sie den öffentlichen Teil des selbstsignierten Zertifikats im vertrauenswürdigen Zertifikatpeicher auf den Clientcomputern installieren.
>
>


## <a name="task-5---lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>Aufgabe 5: Sperren des sicheren LDAP-Zugriffs auf Ihre verwaltete Domäne über das Internet
> [!NOTE]
> Wenn Sie den LDAPS-Zugriff auf die verwaltete Domäne über das Internet nicht aktiviert haben, können Sie diese Konfigurationsaufgabe überspringen.
>
>

Vor dem Ausführen dieser Aufgabe müssen Sie die in [Aufgabe 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview)beschriebenen Schritte vollständig ausgeführt haben.

Das Verfügbarmachen Ihrer verwalteten Domäne für LDAPS-Zugriff über das Internet stellt ein Sicherheitsrisiko dar. Die verwaltete Domäne ist aus dem Internet über den für das sichere LDAP verwendeten Port erreichbar (d.h. Port 636). Aus diesem Grund können Sie sich entscheiden, den Zugriff auf die verwaltete Domäne auf bestimmte bekannte IP-Adressen zu beschränken. Erstellen Sie zur Verbesserung der Sicherheit eine Netzwerksicherheitsgruppe (NSG), und ordnen Sie diese dem Subnetz zu, in dem Sie Azure AD Domain Services aktiviert haben.

Die folgende Tabelle zeigt ein Beispiel einer NSG, die Sie konfigurieren können, um den sicheren LDAP-Zugriff über das Internet zu sperren. Die NSG enthält einen Satz von Regeln, die den eingehenden sicheren LDAP-Zugriff über TCP-Port 636 nur aus einer angegebenen Gruppe von IP-Adressen zulassen. Die standardmäßige Regel „DenyAll“ gilt für sämtlichen eingehenden Datenverkehr aus dem Internet. Die NSG-Regel zum Zulassen des LDAPS-Zugriffs über das Internet von angegebenen IP-Adressen hat eine höhere Priorität als die NSG-Regel „DenyAll“.

![Beispiel-NSG zum Schutz des sicheren LDAPS-Zugriffs über das Internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Weitere Informationen** - [Netzwerksicherheitsgruppen](../virtual-network/security-overview.md).

<br>


## <a name="troubleshooting"></a>Problembehandlung
Wenn Sie Probleme beim Herstellen einer Verbindung mit der verwalteten Domäne über sicheres LDAP haben, führen Sie die folgenden Schritte zur Problembehandlung aus:
* Stellen Sie sicher, dass die Ausstellerkette des sicheren LDAP-Zertifikats auf dem Client als vertrauenswürdig eingestuft wird. Sie könnten die Stammzertifizierungsstelle dem vertrauenswürdigen Stammzertifikatspeicher auf dem Client hinzufügen, um die Vertrauensstellung einzurichten.
* Stellen Sie sicher, dass der LDAP-Client (z.B. „ldp.exe“) eine Verbindung mit dem sicheren LDAP-Endpunkt mit einem DNS-Namen herstellt, nicht mit der IP-Adresse.
* Stellen Sie sicher, dass der DNS-Name, mit dem der LDAP-Client eine Verbindung herstellt, in die öffentliche IP-Adresse für sicheres LDAP in der verwalteten Domäne aufgelöst wird.
* Stellen Sie sicher, dass das sichere LDAP-Zertifikat für die verwaltete Domäne den DNS-Namen im Attribut für den Antragsteller oder den alternativen Antragstellernamen aufweist.
* Wenn Sie über das Internet über sicheres LDAP eine Verbindung herstellen, vergewissern Sie sich, dass die NSG-Einstellungen für das virtuelle Netzwerk den Datenverkehr aus dem Internet an Port 636 zulassen.

Wenn Sie weiterhin Probleme beim Herstellen einer Verbindung mit der verwalteten Domäne über sicheres LDAP haben, [bitten Sie das Produktteam](active-directory-ds-contact-us.md) um Hilfe. Nehmen Sie dabei die folgende Informationen auf, damit das Problem besser diagnostiziert werden kann:
* Einen Screenshot von „ldp.exe“ beim Herstellen einer Verbindung, die fehlschlägt.
* Die ID des Azure AD-Mandanten und den DNS-Domänennamen Ihrer verwalteten Domäne.
* Den genauen Benutzernamen, den Sie verwenden möchten.


## <a name="related-content"></a>Verwandte Inhalte
* [Erste Schritte mit Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Verwalten einer durch Azure AD Domain Services verwalteten Domäne](active-directory-ds-admin-guide-administer-domain.md)
* [Verwalten von Gruppenrichtlinien in einer durch Azure Active Directory Domain Services verwalteten Domäne](active-directory-ds-admin-guide-administer-group-policy.md)
* [Netzwerksicherheitsgruppen](../virtual-network/security-overview.md)
* [Erstellen einer Netzwerksicherheitsgruppe](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)
