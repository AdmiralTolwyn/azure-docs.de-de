---
title: "Häufig gestellte Fragen zu Azure Active Directory-Berichten | Microsoft-Dokumentation"
description: "Häufig gestellte Fragen zu Azure Active Directory-Berichten."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: e1c1b8bdf94104c0047e367f67a29d557fcc8df9
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2017
---
# <a name="azure-active-directory-reporting-faq"></a>Häufig gestellte Fragen zu Azure Active Directory-Berichten

Dieser Artikel enthält Antworten auf häufig gestellte Fragen zu Azure Active Directory-Berichten (Azure AD). Weitere Informationen finden Sie unter [Azure Active Directory-Berichterstellung](active-directory-reporting-azure-portal.md). 

**F: Ich verwende die Endpunkt-APIs von https://graph.windows.net/&lt;Mandantenname&gt;/reports/ zum programmgesteuerten Abrufen der Azure AD-Überwachungsberichte und integrierten Anwendungsnutzungsberichte in unsere Berichtssysteme. Welche Umstellung sollte ich vornehmen?**

**A:** In der [API-Referenzdokumentation](https://developer.microsoft.com/graph/) finden Sie Informationen dazu, wie Sie die neuen APIs für den Zugriff auf [Aktivitätsberichte](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) verwenden können. Dieser Endpunkt verfügt über zwei Berichte (Überwachung und Anmeldevorgänge), die alle Daten des alten API-Endpunkts enthalten. Dieser neue Endpunkt verfügt auch über einen Bericht zu Anmeldeaktivitäten für die Azure AD Premium-Lizenz, über den Sie Informationen zur App-Nutzung, Gerätenutzung und Benutzeranmeldung abrufen können.


--- 

**F: Ich verwende die Endpunkt-APIs von https://graph.windows.net/&lt;Mandantenname&gt;/reports/ zum programmgesteuerten Abrufen der Azure AD-Sicherheitsberichte (bestimmte Erkennungstypen, z.B. kompromittierte Anmeldeinformationen oder Anmeldungen über anonyme IP-Adressen) in unsere Berichtssysteme. Welche Umstellung sollte ich vornehmen?**

**A:** Sie können die [API für Identity Protection-Risikoereignisse](active-directory-identityprotection-graph-getting-started.md) für den Zugriff auf Sicherheitserkennungen über Microsoft Graph verwenden. Dieses neue Format ermöglicht höhere Flexibilität beim Abfragen von Daten mit erweiterter Filterung, Feldauswahl und vielem mehr, und standardisiert Risikoereignisse in einem Typ zur einfacheren Integration in SIEMs und andere Tools zur Datensammlung. Da die Daten in einem anderen Format vorliegen, können Sie Ihre alten Abfragen nicht durch eine neue Abfrage ersetzen. Allerdings [verwendet die neue API Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), den Microsoft-Standard für diese APIs wie z.B. Office 365 oder Azure AD. So können Sie entweder Ihre aktuellen MS Graph-Investitionen erweitern oder die Umstellung auf diese neue Standardplattform beginnen.

--- 

**F: Wie lange werden Aktivitätsprotokolldaten (Überwachung und Anmeldevorgänge) im Azure-Portal aufbewahrt?** 

**A:** 7 Tage für unsere Kunden im Tarif „Free“. Durch Erwerb einer Azure AD Premium 1- oder Premium 2-Lizenz können Sie bis zu 30 Tage auf Daten zugreifen. Weitere Informationen zur Aufbewahrung von Berichten finden Sie unter [Aufbewahrungsrichtlinien für Azure Active Directory-Berichte](active-directory-reporting-retention.md).

--- 

**F: Wie lange dauert es, bis nach Abschluss einer Aufgabe Aktivitätsdaten angezeigt werden?**

**A:** Für Aktivitätsüberwachungsprotokolle fällt eine Wartezeit von 15 Minuten bis zu einer Stunde an. Aktivitätsprotokolle für Anmeldevorgänge können von 15 Minuten bis zu 2 Stunden bei einigen Datensätzen in Anspruch nehmen.

---

**F: Muss ich ein globaler Administrator sein, um die Anmeldeaktivitäten im Azure-Portal anzuzeigen oder die Daten über die API abzurufen?**

**A:** Nein. Sie müssen ein **Benutzer mit Leseberechtigung für Sicherheitsfunktionen**, **Sicherheitsadministrator** oder **globaler Administrator** sein, um Berichtsdaten im Azure-Portal oder über die API abrufen zu können.

---

**F: Kann ich Office 365-Aktivitätsprotokollinformationen über das Azure Portal abrufen?**

**A:** Obwohl Office 365- und Azure AD-Aktivitätsprotokolle einen Großteil der Verzeichnisressourcen gemeinsam nutzen, müssen Sie, wenn Sie eine vollständige Ansicht der Office 365-Aktivitätsprotokolle wünschen, das Office 365 Admin Center besuchen, um Office 365-Aktivitätsprotokollinformationen abzurufen.

---


**F: Mit welchen APIs kann ich Informationen zu Office 365-Aktivitätsprotokollen abrufen?**

**A:** Über die Office 365-Verwaltungs-APIs können Sie auf die [Office 365-Aktivitätsprotokolle](https://msdn.microsoft.com/office-365/office-365-managment-apis-overview) zugreifen.

---

**F: Wie viele Datensätze kann ich aus dem Azure-Portal herunterladen?**

**A:** Sie können bis zu 120.000 Datensätze aus dem Azure-Portal herunterladen. Die Datensätze werden nach *Aktualität* sortiert, und standardmäßig erhalten Sie die neuesten 120.000 Datensätze. 

---

**F: Wie viele Datensätze kann ich über die Aktivitäten-API abfragen?**

**A:** Sie können bis zu 1 Million Datensätze abfragen (wenn Sie nicht den Operator verwenden, der die Datensätze nach Aktualität sortiert). Wenn Sie diesen Operator verwenden, können Sie bis zu 500.000 Datensätze abfragen. [Hier](active-directory-reporting-api-getting-started.md) finden Sie Beispielabfragen, die die Verwendung der API veranschaulichen.

---

**F: Wie erhalte ich eine Premium-Lizenz?**

**A:** Die Antwort auf diese Frage finden Sie unter [Erste Schritte mit Azure Active Directory Premium](active-directory-get-started-premium.md).

---

**F: Wie schnell werden nach Erwerb einer Premium-Lizenz Daten zu Aktivitäten angezeigt?**

**A:** Wenn Ihnen bereits mit einer Lizenz im Tarif „Free“ Daten zu Aktivitäten angezeigt werden, sehen Sie dieselben Daten. Wenn Sie noch keine Daten haben, dauert es ein bis zwei Tage.

---

**F:Werden nach Erwerb einer Azure AD-Premium-Lizenz die Daten des letzten Monats angezeigt?**

**A:** Wenn Sie vor Kurzem zu einer Premium-Version (einschließlich einer Testversion) gewechselt sind, werden Ihnen anfänglich Daten von bis zu 7 Tagen angezeigt. Sobald sich Daten angesammelt haben, werden Daten von bis zu 30 Tagen angezeigt.

---

**F: Es gibt ein Risikoereignis in Identity Protection, aber ich sehe keine entsprechende Anmeldung unter „Alle Anmeldungen“. Entspricht dies dem erwarteten Verhalten?**

**A:** Ja, Identity Protection beurteilt das Risiko für alle Authentifizierungsflows, egal ob diese interaktiv oder nicht interaktiv sind. „Alle Anmeldungen“ führt jedoch nur die interaktiven Anmeldungen auf.

---

**F: Wie kann ich den Bericht „Benutzer mit Risikokennzeichnung“ im Azure-Portal herunterladen?**

**A:** Die Möglichkeit zum Herunterladen des Berichts *Benutzer mit Risikokennzeichnung* wird bald hinzugefügt.

---

**F: Wie erfahre ich, warum eine Anmeldung oder ein Benutzer im Azure-Portal als riskant gekennzeichnet wurde?**

**A:** Kunden der Premium Edition können mehr über die zugrunde liegenden Risikoereignisse erfahren, indem Sie auf den Benutzer unter „Benutzer mit Risikokennzeichnung“ oder auf „riskante Anmeldevorgänge“ klicken. Kunden der kostenlosen und der Basic Edition können die riskanten Benutzer und Anmeldungen ohne die zugrunde liegenden Risikoereignisse sehen.

---

**F: Wie werden IP-Adressen im Bericht zu Anmeldungen und riskanten Anmeldungen berechnet?**

**A:** IP-Adressen werden so ausgestellt, dass es keine definitive Verbindung zwischen einer IP-Adresse und dem physischen Standort des Computers mit dieser Adresse gibt. Dies wird durch Faktoren wie Mobilfunkanbieter und VPNs verkompliziert, die IP-Adressen aus zentralen Pools zuweisen, die häufig sehr weit von den Orten entfernt sind, an denen das Clientgerät tatsächlich verwendet wird. Bei Berücksichtigung dieser Vorgaben erfolgt das Konvertieren einer IP-Adresse in einen physischen Standort basierend auf Ablaufverfolgungen, Registrierungsdaten, Reverse-Lookups und anderen Informationen. 

---

**F: Was bedeutet das Risikoereignis „Anmeldung mit erhöhtem Risiko erkannt“?**

**A:** Das Risikoereignis „Anmeldung mit erhöhtem Risiko erkannt“ wird für Anmeldungen angezeigt, die aufgrund exklusiver Erkennungen für Azure AD Identity Protection-Abonnenten als riskant eingestuft werden, um Ihnen einen Einblick in sämtliche riskante Anmeldungen in Ihrer Umgebung zu geben.

---
