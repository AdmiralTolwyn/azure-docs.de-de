---
title: Brandingrichtlinien für Anwendungen | Microsoft Docs
description: Eine umfassende Anleitung zu entwicklerorientierten Ressourcen für Azure Active Directory
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 72f4e464-1352-4a49-a18f-c37f58e7d5c4
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: celested
ms.reviewer: skwan
ms.custom: aaddev
ms.openlocfilehash: 7794388a067cb8fb876d70b7186bc555e6ff2975
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2018
---
# <a name="branding-guidelines-for-applications"></a>Brandingrichtlinien für Anwendungen
Dieser Artikel behandelt die empfohlenen Brandingrichtlinien für die Entwicklung von Anwendungen mit Azure Active Directory (Azure AD). Die Richtlinien stellen sicher, dass sich Kunden besser zurechtfinden, die sich über ihr in Azure AD verwaltetes Geschäfts- oder Schulkonto (oder über ihr persönliches Konto) bei Ihrer Anwendung registrieren und anmelden möchten.

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Gegenüberstellung von persönlichen Konten und Geschäfts- oder Schulkonten von Microsoft
Microsoft verwaltet zwei Arten von Benutzerkonten:

* **Persönliche Konten** (ehemals Windows Live ID). Diese Konten stellen die Beziehung zwischen *einzelnen* Benutzern und Microsoft dar und werden für den Zugriff auf Verbrauchergeräte und -dienste von Microsoft verwendet. Konten dieser Art sind für den persönlichen Gebrauch vorgesehen.
* **Geschäfts- oder Schulkonten.** Diese Konten werden von Microsoft für Unternehmen verwaltet, die Azure Active Directory verwenden. Konten dieser Art dienen zur Anmeldung bei Office 365 und anderen Unternehmensdiensten von Microsoft.

Geschäfts- oder Schulkonten von Microsoft werden den Endbenutzern (Angestellten, Schülern/Studenten, Behördenmitarbeitern) üblicherweise von der jeweiligen Organisation (Unternehmen, Bildungseinrichtung, Behörde) zugewiesen. Diese Konten werden entweder direkt in der Cloud oder über die Azure AD-Plattform gemastert oder über ein lokales Verzeichnis (wie etwa Windows Server Active Directory) mit Azure AD synchronisiert. Microsoft fungiert als *Verwaltungsberechtigter* des Geschäfts- oder Schulkontos, die Konten gehören aber der Organisation und werden auch von dieser gesteuert.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Verweisen auf Azure AD-Konten in Ihrer Anwendung
Microsoft verwendet die Markennamen „Azure“ und „Active Directory“ nicht gegenüber Endbenutzern – eine Regel, an die auch Sie sich halten sollten.

* Nach der Anmeldung eines Benutzers sollten Sie möglichst häufig den Namen und das Logo der Organisation verwenden. Das ist besser als die Verwendung generischer Formulierungen wie „Ihre Organisation“.
* Wenn der Benutzer nicht angemeldet sind, sollten Sie dessen Konto als „Geschäfts- oder Schulkonto“ bezeichnen und durch die Verwendung des Microsoft-Logos deutlich machen, dass das Konto von Microsoft verwaltet wird. Verwenden Sie keine Begriffe wie „Unternehmenskonto“, „Business-Konto“ oder „Firmenkonto“, die den Benutzer verwirren können.

## <a name="user-account-pictogram"></a>Piktogramm für Benutzerkonten
In einer früheren Version dieser Richtlinien haben wir die Verwendung eines blauen Badge-Piktogramms empfohlen. Aufgrund von Benutzer- und Entwicklerfeedback empfehlen wir inzwischen aber stattdessen die Verwendung des Microsoft-Logos. Dadurch wird den Benutzern besser vermittelt, dass sie sich bei Ihrer App mit dem gleichen Konto anmelden können, das sie auch für Office 365 und andere Unternehmensdienste von Microsoft verwenden.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Registrieren und Anmelden mit Azure AD
Ihre Anwendung verwendet möglicherweise separate Vorgehensweisen für Registrierung und Anmeldung. Dies wird in den folgenden Abschnitten berücksichtigt.

**Ihre App unterstützt die Registrierung von Endbenutzern (beispielsweise bei einer kostenlosen Testversion oder bei einem Freemium-Modell)**: Sie können eine **Registrierungsschaltfläche** anzeigen, mit deren Hilfe Benutzer über ihr Geschäftskonto oder persönliches Konto auf Ihre App zugreifen können. Beim ersten Zugriff auf Ihre App zeigt Azure AD eine Zustimmungsaufforderung an.

**Ihre App benötigt Berechtigungen, die die Zustimmung eines Administrators voraussetzen, oder Ihre App muss von der Organisation lizenziert werden**: Administratorzustimmung und Benutzeranmeldung sollten getrennt werden. Die **Schaltfläche zum Abrufen der App** leitet Administratoren zur Anmeldung weiter und fordert sie auf, im Namen von Benutzern in der Organisation ihre Zustimmung zu geben. Dies hat den zusätzlichen Vorteil, dass Zustimmungsaufforderungen für Endbenutzer Ihrer App unterdrückt werden.

## <a name="visual-guidance-for-app-acquisition"></a>Darstellungsleitfaden für den App-Erwerb
Über den Link zum Abrufen der App muss der Benutzer auf die Zugriffsgewährungs- bzw. Autorisierungsseite von Azure AD weitergeleitet werden, damit ein Organisationsadministrator Ihrer App den Zugriff auf die von Microsoft gehosteten Organisationsdaten gewähren kann. Ausführliche Informationen zum Anfordern von Zugriff finden Sie im Artikel [Integrieren von Anwendungen in Azure Active Directory](active-directory-integrating-applications.md).

Nach der Zustimmung des Administrators kann dieser entscheiden, ob Ihre App dem App-Startfeld von Office 365 hinzugefügt werden soll (zugänglich über das Waffel-Menü und [https://portal.office.com/myapps](https://portal.office.com/myapps)). Wenn Sie auf diese Funktion hinweisen möchten, können Sie beispielsweise eine Formulierung wie „App zur Organisation hinzufügen“ und die folgende Schaltfläche verwenden:

![Anwendungstypen und -szenarien](./media/active-directory-branding-guidelines/add-to-my-org.png)

Es empfiehlt sich jedoch, eine Erläuterung in Textform zu verwenden, anstatt nur auf Schaltflächen zu setzen. Beispiel: 

> *Wenn Sie bereits Office 365 oder andere Unternehmensdienste von Microsoft verwenden, können Sie <Name_Ihrer_App> einfach Zugriff auf die Daten Ihrer Organisation gewähren. Dadurch können Benutzer mit ihren bereits vorhandenen Arbeitskonten auf <Name_Ihrer_App> zugreifen.*
> 
> 

## <a name="visual-guidance-for-sign-in"></a>Darstellungsleitfaden für die Anmeldung
Ihre App sollte eine Anmeldeschaltfläche enthalten, über die der Benutzer zum Anmeldungsendpunkt für das Protokoll weitergeleitet wird, das Sie für die Azure AD-Integration verwenden. Im folgenden Abschnitt erfahren Sie, wie diese Schaltfläche auszusehen hat.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Piktogramm und „Bei Microsoft anmelden“
Durch die Kombination aus Microsoft-Logo und dem Text „Bei Microsoft anmelden“ hebt sich Azure AD von anderen Identitätsanbietern ab, die Ihre App möglicherweise unterstützt. Sollte für „Bei Microsoft anmelden“ nicht genügend Platz zur Verfügung stehen, kann der Text auch zu „Anmelden“ verkürzt werden.

![Anwendungstypen und -szenarien](./media/active-directory-branding-guidelines/sign-in-with-microsoft-light.png)

![Anwendungstypen und -szenarien](./media/active-directory-branding-guidelines/sign-in-light.png)

Für die Schaltflächen kann auch ein dunkles Farbschema verwendet werden.

![Anwendungstypen und -szenarien](./media/active-directory-branding-guidelines/sign-in-with-microsoft-dark.png)

![Anwendungstypen und -szenarien](./media/active-directory-branding-guidelines/sign-in-dark.png)

Um diese Images zur Verwendung in Ihrer App herunterzuladen, klicken Sie mit der rechten Maustaste auf das gewünschte Image, und speichern Sie es auf Ihrem Computer. 

## <a name="branding-dos-and-donts"></a>Brandingempfehlungen
**Verwenden Sie** „Geschäfts-, Schul- oder Unikonto“ in Kombination mit der Schaltfläche „Bei Microsoft anmelden“, um eine zusätzliche Erläuterung bereitzustellen, damit Endbenutzer sofort wissen, ob sie die Option verwenden können. **Nicht** Verwenden Sie keine Begriffe wie „Unternehmenskonto“, „Geschäftskonto“ oder „Firmenkonto“.

**Nicht** Verwenden Sie weder „Office 365-ID“ noch „Azure-ID“. Office 365 ist auch der Name eines Verbraucherangebots von Microsoft, bei dem Azure AD nicht für die Authentifizierung verwendet wird.

**Nicht** verändert werden.

**Nicht** Verwenden Sie die Markennamen „Azure“ und „Active Directory“ nicht gegenüber Endbenutzern. Gegenüber Entwicklern, IT-Experten und Administratoren können diese Begriffe dagegen problemlos verwendet werden.

## <a name="navigation-dos-and-donts"></a>Navigationsempfehlungen
Stellen Sie den Benutzern eine Funktion zur Verfügung, über die sie sich abmelden und das Benutzerkonto wechseln können. Die meisten Benutzer besitzen zwar nur ein einzelnes persönliches Konto von Microsoft/Facebook/Google/Twitter, gehören aber häufig mehreren Organisationen an. Die Unterstützung mehrerer angemeldeter Benutzer folgt in Kürze.

