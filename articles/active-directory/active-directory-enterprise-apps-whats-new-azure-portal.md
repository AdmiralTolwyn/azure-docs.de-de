---
title: Neuerungen bei der Verwaltung von Unternehmensanwendungen in Azure Active Directory | Microsoft-Dokumentation
description: Es wird beschrieben, welche Neuerungen es bei der Verwaltung von Unternehmensanwendungen in Azure Active Directory gibt.
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
editor: ''
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: asteen
ms.reviewer: asteen
ms.openlocfilehash: 30b3fbd415fac1e11a77b3905cb01e5935a62839
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350864"
---
# <a name="whats-new-in-enterprise-application-management-in-azure-active-directory"></a>Neuerungen bei der Verwaltung von Unternehmensanwendungen in Azure Active Directory 

Azure AD hat die Tools für die Verwaltung von Unternehmensanwendungen verbessert. Mit neuen Features und Funktionen ist die Verwaltung von Apps einfacher und effizient.

Hier sind einige Verbesserungen für Azure AD im [Azure-Portal](https://portal.azure.com) angegeben.

- Eine verbesserte Oberfläche für den Anwendungskatalog mit einem vereinfachten Modell für die Anwendungserstellung und Unterstützung für alle Anwendungstypen, die Sie bereits kennen. 
- Brandneue Schnellstartoberfläche, damit Sie schnell mit einer Pilotversion Ihrer Anwendung starten können 
- Konfigurieren von Self-Service-Richtlinien mit wenigen Klicks 
- Verbesserungen in Bezug auf den Anwendungsproxy, die Konfiguration des einmaligen Anmeldens und von „Bring Your Own Application“-Umgebungen, damit Sie mehr Arbeit in kürzerer Zeit erledigen können.

## <a name="improvements-to-the-azure-active-directory-application-gallery"></a>Verbesserungen im Azure Active Directory-Anwendungskatalog

Fügen Sie Ihre bevorzugten Anwendungen hinzu, egal ob diese aus dem [Anwendungskatalog](manage-apps/what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery) stammen, benutzerdefinierte Anwendungen sind, die Sie in die Cloud erweitern, oder neue Anwendungen, die Sie entwickeln.  Sie können diese neue Benutzeroberfläche aufrufen, indem Sie unter **Unternehmensanwendungen** oder **Alle Anwendungen** auf **Hinzufügen** klicken.
 
  ![Hinzufügen einer Anwendung](./media/active-directory-enterprise-apps-whats-new-azure-portal/01.png)

Im Katalog sind alle Anwendungen aufgeführt, für die die Benutzerbereitstellung unterstützt wird. Sie können die unterschiedlichen Kategorien durchsuchen, um Informationen zu den für Sie interessanten Anwendungen zu erhalten, oder Sie können die Suchfunktion nutzen, um schnell die Anwendungen zu ermitteln, die Sie integrieren möchten.

  ![Anwendungskatalog](./media/active-directory-enterprise-apps-whats-new-azure-portal/02.png)

## <a name="add-custom-applications-from-one-place"></a>Hinzufügen von benutzerdefinierten Anwendungen von einem Ort aus

Zusätzlich zum möglichen Hinzufügen von vorab integrierten Anwendungen aus dem Katalog sind alle benutzerdefinierten Oberflächen für die Konfiguration von Anwendungen, die Sie aus dem klassischen Verwaltungsportal kennen, jetzt auch im neuen Portal vorhanden. Alles ist von diesem zentralen Ort aus erreichbar: ob Sie eine Anwendung per Anwendungsproxy aus der lokalen Umgebung erweitern, Ihre eigene Anwendung mit einmaliger Kennwort- oder Verbundanmeldung integrieren oder eine brandneue Anwendung mit der Anwendungsregistrierung erstellen.

  ![Hinzufügen einer eigenen Anwendung](./media/active-directory-enterprise-apps-whats-new-azure-portal/03.png)

 
**Gehen Sie wie folgt vor, um mit dem Hinzufügen einer eigenen Anwendung zu beginnen**:

1. Klicken Sie oben im Anwendungskatalog auf den Link **oder fügen Sie eine eigene Anwendung hinzu**. 
2. Zwei Optionen werden angezeigt: **deploy an existing application** (Vorhandene Anwendung bereitstellen) und **develop a new application** (Neue Anwendung entwickeln). Lesen Sie weiter, um zu erfahren, wodurch sich die beiden Optionen unterscheiden und wie Sie sie verwenden.

### <a name="deploying-existing-applications"></a>Bereitstellen von vorhandenen Anwendungen

1. Wenn Sie eine Anwendung bereits ausführen und diese nur für das einmalige Anmelden oder die Bereitstellung in Azure AD integrieren möchten, wählen Sie die Option **Deploy an existing application** (Vorhandene Anwendung bereitstellen). Wählen Sie einen Namen für die Anwendung, und klicken Sie auf **Hinzufügen**.
2. Das ist alles! Sie müssen nicht mehr alle Details Ihrer Anwendung bereits kennen, sondern Sie können die Funktionsweise Ihrer neuen Anwendung jetzt einrichten, indem Sie im Menü auf der linken Seite navigieren und die Anwendung jeweils nach Ihren Wünschen konfigurieren.

  ![Hinzufügen einer vorhandenen Anwendung per Klick](./media/active-directory-enterprise-apps-whats-new-azure-portal/04.png)
 
### <a name="developing-new-applications"></a>Entwickeln neuer Anwendungen

1. Beim Entwickeln einer neuen Anwendung haben Sie eine einfache Möglichkeit zum Aufrufen der Anwendungsregistrierung direkt aus dem Katalog:
2. Klicken Sie im Anwendungskatalog auf die Option **oder fügen Sie eine eigene Anwendung hinzu.**, und wählen Sie **Vorhandene Anwendung entwickeln**. Es wird ein Direktlink zur Oberfläche zum Hinzufügen von Anwendungen angezeigt.

  ![Hinzufügen einer neu entwickelten Anwendung mit wenigen Klicks](./media/active-directory-enterprise-apps-whats-new-azure-portal/05.png)


>[!NOTE]
>Nachdem Sie eine Anwendung über die Anwendungsregistrierung hinzugefügt haben, wird sie in der Liste mit den Unternehmensanwendungen angezeigt. Darin können Sie das einmalige Anmelden konfigurieren und die Zugriffsrichtlinien für die neue Anwendung verwalten.

  ![Verwalten des Zugriffs auf Ihre neue Anwendung unter „Unternehmensanwendungen“](./media/active-directory-enterprise-apps-whats-new-azure-portal/06.png)


## <a name="quickstart-get-going-with-your-new-application-right-away"></a>Schnellstart: Direkter Einstieg in die Verwendung der neuen Anwendung 

Nach dem Hinzufügen einer Anwendung – ob vorab integriert oder eigene App – können Sie die neue Schnellstart-Benutzeroberfläche nutzen, um sich schnell mit der neuen Umgebung für Anwendungen vertraut zu machen. Wenn Sie die einzelnen Optionen systematisch durchlaufen, werden Sie durch die Benutzeroberfläche geführt, und es wird gezeigt, wie Sie so schnell wie möglich mit einer Pilotversion Ihrer neuen Anwendung starten können. 
 
  ![Neue Schnellstartbenutzeroberfläche für Anwendungen](./media/active-directory-enterprise-apps-whats-new-azure-portal/07.png)

 Sie können diese neue Schnellstartbenutzeroberfläche jederzeit und für jede Anwendung aufrufen, indem Sie im linken Navigationsmenü der Anwendung auf **Schnellstart** klicken.


## <a name="updated-application-proxy-configuration"></a>Aktualisierte Anwendungsproxykonfiguration
Angenommen, eine der von Ihnen hinzugefügten neuen Anwendungen wird in Ihrer lokalen Umgebung ausgeführt, und Sie möchten sie in Azure AD integrieren.  Eine nützliche neue Funktion der Konfigurationsoberfläche für neue Anwendungen im neuen Azure AD-Portal ist: Indem der Anmeldemodus der Anwendung von der Anwendungsproxykonfiguration getrennt wird, können Sie Kennwort-SSO- oder Verbundanwendungen, die in Ihrem Unternehmensnetzwerk ausgeführt werden, jetzt leicht direkt in der Cloud verfügbar machen, ohne mehrere Instanzen der Anwendung erstellen zu müssen.

Außerdem können Sie alle neuen hinzugefügten Anwendungen direkt über das neue Portal für die Verwendung mit dem Azure AD-Anwendungsproxy konfigurieren. Dies gilt auch für Anwendungen, die die native Windows-Authentifizierung unterstützen.

  ![Konfigurieren einer Anwendung für die Verwendung der Anmeldeoption „Integrierte Windows-Authentifizierung“](./media/active-directory-enterprise-apps-whats-new-azure-portal/08.png)
 

Gehen Sie wie folgt vor, um eine Anwendung mit nativer Windows-Authentifizierung mit dem Anwendungsproxy zu konfigurieren:
1. Klicken Sie auf das Navigationselement für das einmalige Anmelden, und wählen Sie unter den Einstellungen für einmaliges Anmelden die Option **Integrierte Windows-Authentifizierung**. Konfigurieren Sie die Einstellungen dann gemäß Ihren Anforderungen.
2. Zusätzlich zur Unterstützung dieser neuen Authentifizierungsmodi können Sie jetzt auch Zertifikate aus benutzerdefinierten Domänen hochladen, um Anwendungen zu unterstützen, die auf sicheren Endpunkten in Ihrer Organisation ausgeführt werden.  
 
   ![Hochladen eines Zertifikats für die Verwendung mit dem Anwendungsproxy](./media/active-directory-enterprise-apps-whats-new-azure-portal/09.png)

3. Klicken Sie zum Hochladen eines neuen Zertifikats für Ihre bevorzugte lokale Anwendung im Navigationsmenü auf der linken Seite auf die Option **Anwendungsproxy**, klicken Sie auf das Auswahlelement **Zertifikat**, und laden Sie eine Zertifikatsdatei hoch, die wir zum Verschlüsseln der Anforderungen auf dem Weg von unserem Cloudendpunkt zu Ihrer Anwendung verwenden können.

## <a name="advanced-federated-single-sign-on-configuration"></a>Erweiterte Konfiguration der einmaligen Verbundanmeldung

Für Benutzer, die derzeit Verbundanwendungen nutzen, enthält die Konfiguration des SAML-basierten einmaligen Anmeldens viele neue Features. Beispielsweise können Sie die vorhandenen Benutzerattribute, die als Ansprüche in SAML-Token ausgestellt werden, jetzt vollständig anpassen, hinzufügen, entfernen und zuordnen.
 
  ![Anpassen der SAML-Token-Benutzerattribute, die an eine Verbundanwendung übergeben werden](./media/active-directory-enterprise-apps-whats-new-azure-portal/10.png)


Gehen Sie wie folgt vor, um auf die neue Konfigurationsoberfläche für die einmalige Verbundanmeldung zuzugreifen:
1. Öffnen Sie über das linke Navigationsmenü **Einmaliges Anmelden** für eine Verbundanwendung, und stellen Sie sicher, dass der Modus *SAML-basierte Anmeldung* aktiviert ist. 
2. Aktivieren Sie dann unter der Überschrift **Benutzerattribute** das Kontrollkästchen, um alle Attribute zu ändern, die im an die Anwendung übergebenen SAML-Token enthalten sind.

Außerdem können Sie Zertifikate, die für die einmalige Verbundanmeldung verwendet werden, erstellen, verwalten und ein Rollover dafür durchführen. Sie können auch ändern, wer benachrichtigt wird, wenn das Zertifikat in Kürze abläuft. Diese neuen Optionen werden im selben Bereich „Einmaliges Anmelden“ unter der Überschrift **Zertifikate** angezeigt.
 
  ![Erstellen eines neuen Zertifikats und Anpassen der E-Mail zur Benachrichtigung über den Ablauf und der Optionen für die Signieroptionen](./media/active-directory-enterprise-apps-whats-new-azure-portal/11.png)

### <a name="relay-state-parameter"></a>Relayzustandsparameter
Schließlich haben wir die unterstützten SAML-URL-Parameter um den **Relayzustandparameter** erweitert. Auf dieser Seite landen Ihre Benutzer in einer Verbundanwendung, nachdem der Anmeldevorgang abgeschlossen ist. Die Konfiguration dieser Einstellung ist nützlich, wenn Sie Ihre Benutzer an einen bestimmten Ort innerhalb der Anwendung leiten möchten, damit sie schnell mit der Nutzung beginnen können.

  ![Festlegen des SAML-Relayzustandparameters](./media/active-directory-enterprise-apps-whats-new-azure-portal/12.png)
 
**Gehen Sie wie folgt vor, um den Relayzustandparameter festzulegen**:

1. Aktivieren Sie im Bereich für die Konfiguration des einmaligen Anmeldens unter der Überschrift **Domäne und URLs** das Kontrollkästchen **Erweiterte URL-Einstellungen anzeigen**. 
2. Es wird eine Gruppe von neuen URL-Eingabefeldern angezeigt, über die Sie diesen Parameter und andere SAML-URL-Einstellungen festlegen können.

## <a name="bring-your-own-password-sso-applications"></a>Verwenden eigener Kennwort-SSO-Anwendungen

Uns ist bewusst, dass nicht jede Anwendung standardmäßig den Verbund unterstützt. Es kann beispielsweise sein, dass eine der neuen Anwendungen, die Sie hinzugefügt haben, über einen benutzerdefinierten Anmeldebildschirm verfügt, über den sich Ihre Benutzer per Benutzername und Kennwort anmelden. Sie können diese Arten von Anwendungen trotzdem in Azure AD integrieren, indem Sie das Feature **Bring your own applications** (Nutzen Sie Ihre eigenen Anwendungen) verwenden, das Sie jetzt im neuen Portal konfigurieren können.
 
  ![Integrieren von benutzerdefinierten Password Vaulting-Anwendungen in Azure AD](./media/active-directory-enterprise-apps-whats-new-azure-portal/13.png)

**Gehen Sie wie folgt vor, um das Feature „Bring your own applications“ (Nutzen Sie Ihre eigenen Anwendungen) zu erkunden**:

1. Geben Sie nach dem Festlegen des Modus für das einmalige Anmelden für eine neue benutzerdefinierte Anwendung, die Sie unter **Kennwortbasierte Anmeldung** hinzugefügt haben, die URL des Orts ein, an dem die Anwendung den Anmeldebildschirm rendert. 
2. Klicken Sie auf **Speichern**.  
2. Diese URL wird von uns dann automatisch auf ein Eingabefeld für einen Benutzernamen und ein Kennwort untersucht, und Sie können Azure AD verwenden, um auf sichere Weise Kennwörter an diese Anwendung zu übertragen, indem Sie die Zugriffsbereich-Browsererweiterung verwenden.

## <a name="configure-self-service-application-access"></a>Konfigurieren des Self-Service-Anwendungszugriffs

Nachdem Sie viele neue Anwendungen hinzugefügt haben, kann es sein, dass Sie es Ihren Benutzern ermöglichen möchten, diese Anwendungen zu durchsuchen und ihren eigenen Zugriffsbereichen hinzuzufügen, ohne dass Sie als Administrator eingreifen müssen. Mit dieser neuesten Version können Sie den Self-Service-Anwendungszugriff jetzt direkt über das neue Portal konfigurieren und verwalten.

  ![Konfigurieren des Self-Service-Anwendungszugriffs für eine Kennwort-SSO-Anwendung](./media/active-directory-enterprise-apps-whats-new-azure-portal/14.png)
 
**Gehen Sie wie folgt vor, um den Self-Service-Anwendungszugriff zu konfigurieren und zu verwalten**:

1. Sie können im linken Navigationsmenü der Anwendung die Option **Self-Service** wählen und die Option **Allow users to request access to this application?** (Benutzern die Zugriffsanforderung für diese Anwendung erlauben?) auf **Ja** festlegen. 
2. So können Sie konfigurieren, wer den Zugriff auf diese Anwendung genehmigen darf und welche Gruppe von Self-Service-Benutzern hinzugefügt wird. Wenn die Anwendung für das einmalige Anmelden per Kennwort konfiguriert ist, wird noch eine weitere Option angezeigt, mit der Sie optional zulassen können, dass die Genehmiger die Kennwörter der Anwendung verwalten.

## <a name="feedback"></a>Feedback

Wir hoffen, Ihnen gefällt die verbesserte Azure AD-Benutzeroberfläche. Es wäre schön, wenn Sie uns weiter Feedback senden würden! Feedback und Verbesserungsvorschläge können Sie uns im [Feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal) über den Abschnitt **Verwaltungsportal** zukommen lassen.  Wir haben großen Spaß daran, jeden Tag neue Dinge zu entwickeln, und Ihr Feedback hilft uns, die nächsten Ziele anzugehen und zu definieren.

## <a name="next-steps"></a>Nächste Schritte

Ausführliche Informationen finden Sie unter [Verwalten von Anwendungen mit Azure Active Directory](manage-apps/what-is-application-management.md).



