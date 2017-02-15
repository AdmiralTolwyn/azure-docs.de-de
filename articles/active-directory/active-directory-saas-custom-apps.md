---
title: "Konfigurieren des einmaligen Anmeldens für Anwendungen, die nicht im Azure Active Directory-Anwendungskatalog enthalten sind | Microsoft Docs"
description: Erfahren Sie, wie Sie Apps per Self-Service und mithilfe von SAML und kennwortbasiertem SSO mit Azure Active Directory verbinden.
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: femila
ms.assetid: 0d42eb0c-6d3f-4557-9030-e88e86709a19
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 8274e47e99998b735e7252ce72bb3ac7c2ecc672


---
# <a name="configuring-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Konfigurieren des einmaligen Anmeldens für Anwendungen, die nicht im Azure Active Directory-Anwendungskatalog enthalten sind
In diesem Artikel geht es um eine Funktion, die Administratoren das Konfigurieren der einmaligen Anmeldung bei Anwendungen, die im Azure Active Directory-App-Katalog nicht vorhanden sind, *ohne Schreiben von Code* ermöglicht. Diese Funktion wurde über die technische Vorschau am 18. November 2015 veröffentlicht und steht in [Azure Active Directory Premium](active-directory-editions.md) zur Verfügung. Wenn Sie stattdessen nach einer Anleitung für Entwickler zum Integrieren von benutzerdefinierten Apps in Azure AD mithilfe von Code suchen, finden Sie diese unter [Authentifizierungsszenarien für Azure AD](active-directory-authentication-scenarios.md).

Der Azure Active Directory-Anwendungskatalog enthält eine Liste der Anwendungen, die bekanntermaßen eine Form der einmaligen Anmeldung mit Azure Active Directory unterstützen, wie [in diesem Artikel](active-directory-appssoaccess-whatis.md)beschrieben. Wenn Sie (als IT-Spezialist oder Systemintegrator in Ihrer Organisation) die Anwendung gefunden haben, mit der Sie eine Verbindung herstellen möchten, können Sie mithilfe der Schritt-für-Schritt-Anleitungen im Azure-Verwaltungsportal das einmalige Anmelden aktivieren.

Kunden mit [Azure Active Directory Premium](active-directory-editions.md) -Lizenzen erhalten auch diese zusätzlichen Funktionen:

* Self-Service-Integration für Anwendungen, die SAML 2.0-Identitätsanbieter unterstützen (SP-initiiert oder IdP-initiiert)
* Self-Service-Integration für Webanwendungen, die über eine HTML-basierte Anmeldeseite mit [kennwortbasierter einmaliger Anmeldung (Single Sign-On, SSO)](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
* Self-Service-Verbindung für Anwendungen, die das SCIM-Protokoll für die Benutzerbereitstellung verwenden ([wie hier beschrieben](active-directory-scim-provisioning.md))
* Möglichkeit zum Hinzufügen von Links zu Anwendungen im [Office 365-App-Startfeld](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) oder im [Azure AD-Zugriffsbereich](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)

Diese Möglichkeiten beziehen sich nicht nur SaaS-Anwendungen, die Sie bereits nutzen, die aber noch nicht per Onboarding in den Azure AD-Anwendungskatalog aufgenommen wurden, sondern auch auf Drittanbieter-Webanwendungen, die Ihre Organisation auf von Ihnen kontrollierten Servern in der Cloud oder lokal bereitgestellt hat.

Diese Funktionen, auch als *App-Integrationsvorlagen*bezeichnet, bieten standardbasierte Verbindungspunkte für Apps, die SAML, SCIM oder formularbasierte Authentifizierung unterstützen, und enthalten flexible Optionen und Einstellungen für die Kompatibilität mit einer umfassenden Reihe von Anwendungen. 

## <a name="adding-an-unlisted-application"></a>Hinzufügen einer nicht aufgeführten Anwendung
Zum Verbinden einer Anwendung mithilfe einer App-Integrationsvorlage melden Sie sich mit Ihrem Azure Active Directory-Administratorkonto beim Azure-Verwaltungsportal an, navigieren zum Bereich **Active Directory > [Verzeichnis] > Anwendungen** und klicken auf **Hinzufügen** und dann auf **Anwendung aus dem Katalog hinzufügen**. 

![][1]

Im App-Katalog können Sie eine nicht aufgeführte App hinzufügen, indem Sie links die Kategorie **Benutzerdefiniert** verwenden oder indem Sie auf den Link **Eine nicht aufgeführte Anwendung hinzufügen** klicken. Dieser Link wird in den Suchergebnissen angezeigt, wenn die gewünschte App nicht gefunden wurde. Nachdem Sie den Namen für Ihre Anwendung eingegeben haben, können Sie die Optionen für das einmalige Anmelden und das entsprechende Verhalten festlegen. 

**Tipp**: Es hat sich bewährt, zuerst mit der Suchfunktion zu prüfen, ob die Anwendung bereits im Anwendungskatalog vorhanden ist. Wenn die Anwendung gefunden wird und in der Beschreibung das einmalige Anmelden erwähnt wird, wird die einmalige Verbundanmeldung für die Anwendung bereits unterstützt. 

![][2]

Das Hinzufügen einer Anwendung mit dieser Methode gestaltet sich ähnlich benutzerfreundlich wie bei vorab integrierten Anwendungen. Zu Beginn wählen Sie **Einmaliges Anmelden konfigurieren**. Auf dem nächsten Bildschirm werden die folgenden drei Optionen zum Konfigurieren des einmaligen Anmeldens angeboten, die in den folgenden Abschnitten beschrieben werden.

![][3]

## <a name="azure-ad-single-sign-on"></a>Azure AD – einmaliges Anmelden
Wählen Sie diese Option aus, um die SAML-basierte Authentifizierung für die Anwendung zu konfigurieren. Hierfür ist es erforderlich, dass die Anwendung SAML 2.0 unterstützt, und Sie sollten sich vor dem Ausführen der nächsten Schritte informieren, wie die SAML-Funktionen der Anwendung genutzt werden können. Wenn Sie auf **Weiter**geklickt haben, werden Sie aufgefordert, drei URLs einzugeben, die den SAML-Endpunkten für die Anwendung entsprechen. 

![][4]

Diese sind wie folgt:

* **Anmelde-URL (nur SP-initiiert):** Hiermit melden sich Benutzer an dieser Anwendung an. Sofern die Anwendung für die Durchführung des vom Dienstanbieter (Service Provider, SP) initiierten einmaligen Anmeldens konfiguriert ist, passiert Folgendes, wenn ein Benutzer zu dieser URL navigiert: Der Dienstanbieter führt die Umleitung an Azure AD durch, um den Benutzer zu authentifizieren und anzumelden. Wenn dieses Feld ausgefüllt wird, verwendet Azure AD diese URL zum Starten der Anwendung aus Office 365 und über den Azure AD-Zugriffsbereich. Falls Sie dieses Feld leer lassen, führt Azure AD stattdessen eine vom Identitätsanbieter initiierte Anmeldung aus, wenn die App über Office 365, den Azure AD-Zugriffsbereich oder die Azure AD-URL für das einmalige Anmelden (kann von der Dashboard-Registerkarte kopiert werden) gestartet wird.
* **Aussteller-URL:** Mit der Aussteller-URL sollte die Anwendung, für die das einmalige Anmelden konfiguriert wird, eindeutig identifiziert werden. Dies ist der Wert, der von Azure AD als **Audience**-Parameter des SAML-Tokens zurück an die Anwendung gesendet wird, und von der Anwendung wird erwartet, dass sie ihn überprüft. Dieser Wert ist auch als **Entitäts-ID** in SAML-Metadaten enthalten, die von der Anwendung bereitgestellt werden. Die SAML-Dokumentation der Anwendung enthält Details dazu, wie die Entitäts-ID bzw. der Wert für „Audience“ lautet. Unten ist ein Beispiel dafür angegeben, wie die Audience-URL im SAML-Token angezeigt wird, das von der Anwendung zurückgegeben wird:

```
    <Subject>
    <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:unspecificed">chad.smith@example.com</NameID>
        <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
      </Subject>
      <Conditions NotBefore="2014-12-19T01:03:14.278Z" NotOnOrAfter="2014-12-19T02:03:14.278Z">
        <AudienceRestriction>
          <Audience>https://tenant.example.com</Audience>
        </AudienceRestriction>
      </Conditions>
```

* **Antwort-URL:** Unter der Antwort-URL erwartet die Anwendung den Empfang des SAML-Tokens. Sie wird auch als **Assertionsverbraucherdienst-URL** (Assertion Consumer Service, ACS) bezeichnet. In der SAML-Dokumentation der Anwendung finden Sie Details dazu, wie die Antwort-URL bzw. ACS-URL des SAML-Tokens lautet.
  Nachdem Sie diese Informationen eingegeben haben, klicken Sie auf **Weiter**, um den nächsten Bildschirm anzuzeigen. Auf diesem Bildschirm erhalten Sie Informationen darüber, was Sie auf Anwendungsseite konfigurieren müssen, damit diese ein SAML-Token von Azure AD akzeptiert. 

![][5]

Welche Werte erforderlich sind, variiert und hängt von der jeweiligen Anwendung ab. Prüfen Sie deshalb die SAML-Dokumentation der jeweiligen Anwendung. Sowohl die Dienst-URL für die **einmalige Anmeldung** als auch die Dienst-URL für die **einmalige Abmeldung** werden im gleichen Endpunkt aufgelöst, und zwar im SAML-Endpunkt zur Verarbeitung von Anforderungen für Ihre Azure AD-Instanz. Die Aussteller-URL ist der Wert, der in dem für die Anwendung ausgestellten SAML-Token als „Issuer“ angezeigt wird. 

Wenn Sie die Anwendung vollständig konfiguriert haben, klicken Sie auf die Schaltfläche **Weiter** und dann auf **Fertig stellen**, um das Dialogfeld zu schließen. 

## <a name="assigning-users-and-groups-to-your-saml-application"></a>Zuweisen von Benutzern und Gruppen zu Ihrer SAML-Anwendung
Nachdem Sie Ihre Anwendung zur Verwendung von Azure AD als SAML-Identitätsanbieter konfiguriert haben, ist sie fast zum Testen bereit. Als Sicherheitsmaßnahme stellt Azure AD nur dann ein Token aus, das den Benutzern die Anmeldung bei der Anwendung erlaubt, wenn ihnen zuvor mit Azure AD Zugriff gewährt wurde. Den Benutzern kann der Zugriff direkt oder über eine Gruppe gewährt werden, in der sie Mitglied sind. 

Um der Anwendung einen Benutzer oder eine Gruppe zuzuweisen, klicken Sie auf die Schaltfläche **Benutzer zuweisen** . Wählen Sie den gewünschten Benutzer oder die gewünschte Gruppe aus, und klicken Sie auf die Schaltfläche **Zuweisen** . 

![][6]

Nach dem Zuweisen eines Benutzers wird es für Azure AD möglich, ein Token für den Benutzer auszustellen und im Zugriffsbereich dieses Benutzers eine Kachel für die Anwendung anzuzeigen. Wenn der Benutzer Office 365 verwendet, wird die Anwendungskachel auch im Office 365-App-Startfeld angezeigt. 

Sie können ein Kachellogo für die Anwendung hochladen. Verwenden Sie hierzu die Schaltfläche **Logo hochladen** auf der Registerkarte **Konfigurieren** für die Anwendung. 

### <a name="customizing-the-claims-issued-in-the-saml-token"></a>Anpassen der Ansprüche, die in einem SAML-Token ausgegeben werden
Wenn sich ein Benutzer bei der Anwendung authentifiziert, stellt Azure AD ein SAML-Token für die App aus, das Informationen über den Benutzer (oder „Ansprüche“) enthält, die ihn eindeutig identifizieren. Dazu gehören normalerweise der Benutzername, die E-Mail-Adresse, der Vorname und der Nachname des Benutzers. 

Sie können die im SAML-Token an die Anwendung übermittelten Ansprüche auf der Registerkarte **Attribute** anzeigen oder bearbeiten. 

![][7]

Es kann aus zwei Gründen nötig werden, die im SAML-Token ausgegebenen Ansprüche zu bearbeiten: •Die Anwendung wurde so geschrieben, dass sie eine andere Gruppe von Anspruchs-URIs oder Anspruchswerten verlangt •Die Anwendung wurde so bereitgestellt, dass sie als NameIdentifier-Anspruch etwas anderes verlangt als den Benutzernamen (den Benutzerprinzipalnamen), der in Azure Active Directory gespeichert ist. 

Informationen zum Hinzufügen und Bearbeiten von Ansprüchen für solche Szenarien finden Sie in diesem [Artikel über das Anpassen von Ansprüchen](active-directory-saml-claims-customization.md). 

### <a name="testing-the-saml-application"></a>Testen der SAML-Anwendung
Nachdem die SAML-URLs und das Zertifikat in Azure AD und in der Anwendung konfiguriert, der Anwendung in Azure die Benutzer oder Gruppen zugewiesen und auch die Ansprüche überprüft und ggf. geändert wurden, kann sich der Benutzer bei der Anwendung anmelden. 

Um dies zu testen, melden Sie sich im Azure AD-Zugriffsbereich unter „https://myapps.microsoft.com“ mit einem Benutzerkonto an, das Sie der Anwendung zugewiesen haben. Dann klicken Sie auf die Kachel für die Anwendung, um den Vorgang für das einmalige Anmelden zu starten. Alternativ können Sie die Anmelde-URL für die Anwendung aufrufen und sich von dort aus anmelden. 

Hinweise zum Debuggen finden Sie in diesem [Artikel zum Debuggen der SAML-basierten einmaligen Anmeldung bei Anwendungen](active-directory-saml-debugging.md) 

## <a name="password-single-sign-on"></a>Einmaliges Anmelden per Kennwort
Wählen Sie diese Option aus, um das [kennwortbasierte einmalige Anmelden](active-directory-appssoaccess-whatis.md) für eine Webanwendung zu konfigurieren, die über eine HTML-Anmeldeseite verfügt. Das kennwortbasierte einmalige Anmelden (auch als „Password Vaulting“ oder „Kennworttresor“ bezeichnet) ermöglicht es Ihnen, den Benutzerzugriff und die Kennwörter für Webanwendungen zu verwalten, die keinen Identitätsverbund unterstützen. Es ist auch für Szenarien nützlich, in denen mehrere Benutzer ein Konto gemeinsam verwenden müssen, wie z. B. bei den App-Konten für die sozialen Medien Ihrer Organisation. 

Nachdem Sie auf **Weiter**geklickt haben, werden Sie aufgefordert, die URL für die webbasierte Anmeldeseite der Anwendung einzugeben. Beachten Sie, dass es sich hierbei um die Seite handeln muss, welche die Eingabefelder für Benutzername und Kennwort enthält. Nach der Eingabe startet Azure AD einen Prozess, bei dem die Anmeldeseite im Hinblick auf eine Benutzernamenseingabe und eine Kennworteingabe analysiert wird. Wenn dieser Prozess nicht erfolgreich ist, wird Ihnen eine Anleitung für einen alternativen Prozess angezeigt, bei dem Sie eine Browsererweiterung installieren (erfordert Internet Explorer, Chrome oder Firefox) und damit die Felder manuell erfassen.

Nachdem die Anmeldeseite erfasst ist, können Sie wie bei regulären [Apps mit einmaligem Anmelden per Kennwort](active-directory-appssoaccess-whatis.md)Benutzer und Gruppen zuweisen und Anmeldeinformationsrichtlinien festlegen.

Hinweis: Sie können ein Kachellogo für die Anwendung hochladen. Verwenden Sie hierzu die Schaltfläche **Logo hochladen** auf der Registerkarte **Konfigurieren** für die Anwendung. 

## <a name="existing-single-sign-on"></a>Vorhandenes einmaliges Anmelden
Wählen Sie diese Option, um im Azure AD-Zugriffsbereich oder im Office 365-Portal einen Link auf eine Anwendung hinzufügen. Sie können mit dieser Option Links auf benutzerdefinierte Web-Apps hinzufügen, die derzeit zur Authentifizierung nicht Azure AD verwenden, sondern Azure Active Directory-Verbunddienste (oder andere Verbunddienste). Oder Sie können Deep-Links auf spezifische SharePoint-Seiten oder andere Webseiten hinzufügen, die in den Zugriffsbereichen der betreffenden Benutzer angezeigt werden sollen. 

Nachdem Sie auf **Weiter** geklickt haben, werden Sie aufgefordert, die URL für die Anwendung einzugeben, für die der Link angezeigt werden soll. Wenn Sie dies abgeschlossen haben, können Sie der Anwendung Benutzer und Gruppen zuweisen. Dadurch wird die Anwendung im [Office 365-App-Startfeld](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) oder im [Azure AD-Zugriffsbereich](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) für die betreffenden Benutzer angezeigt.

Hinweis: Sie können ein Kachellogo für die Anwendung hochladen. Verwenden Sie hierzu die Schaltfläche **Logo hochladen** auf der Registerkarte **Konfigurieren** für die Anwendung.

## <a name="related-articles"></a>Verwandte Artikel
* [Artikelindex für die Anwendungsverwaltung in Azure Active Directory](active-directory-apps-index.md)
* [Gewusst wie: Anpassen ausgestellter Ansprüche im SAML-Token für bereits integrierte Apps](active-directory-saml-claims-customization.md)
* [Problembehandlung bei SAML-basiertem einmaligem Anmelden](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png



<!--HONumber=Feb17_HO2-->


