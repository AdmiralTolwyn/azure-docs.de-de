---
title: Berichte zu Überwachungsaktivitäten im Azure Active Directory-Portal | Microsoft-Dokumentation
description: Enthält eine Einführung in die Berichte zu Überwachungsaktivitäten im Azure Active Directory-Portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 5950b8bac89a8a6c0ed7979228b8296f34e43364
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2018
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Berichte zu Überwachungsaktivitäten im Azure Active Directory-Portal 

Mit der Berichterstellungsfunktion in Azure Active Directory (Azure AD) können Sie alle Informationen abrufen, die Sie zum Ermitteln des Zustands Ihrer Umgebung benötigen.

Die Architektur für die Berichterstellung in Azure AD umfasst die folgenden Komponenten:

- **Aktivität** 
    - **Anmeldeaktivitäten** : Informationen zur Nutzung von verwalteten Anwendungen und Aktivitäten der Benutzeranmeldung
    - **Überwachungsprotokolle:** Ermöglichen die Nachverfolgung sämtlicher Änderungen, die von verschiedenen Features in Azure AD vorgenommen wurden. Hierzu zählen unter anderem Änderungen an Ressourcen in Azure AD wie etwa Benutzer, Apps, Gruppen, Rollen, Richtlinien und Authentifizierungen.
- **Sicherheit** 
    - **Riskante Anmeldungen:** Eine riskante Anmeldung ist ein Indikator für einen Anmeldeversuch von einem Benutzer, der nicht der rechtmäßige Besitzer eines Benutzerkontos ist. Weitere Informationen finden Sie unter „Riskante Anmeldungen“.
    - **Benutzer mit Risikomarkierung:** Ein Benutzer mit Risikomarkierung ist ein Indikator für ein möglicherweise kompromittiertes Benutzerkonto. Weitere Informationen finden Sie unter „Benutzer mit Risikomarkierung“.

In diesem Thema erhalten Sie einen Überblick über die Überwachungsaktivitäten.
 
## <a name="who-can-access-the-data"></a>Wer kann auf die Daten zugreifen?
* Benutzer mit den Rollen „Sicherheitsadministrator“ oder der Berechtigung „Sicherheit lesen“
* Globale Administratoren
* Einzelne Benutzer (Nicht-Administratoren) können eigene Aktivitäten anzeigen.


## <a name="audit-logs"></a>Überwachungsprotokolle

Die Überwachungsprotokolle in Azure Active Directory enthalten Datensätze mit Systemaktivitäten, die zum Nachweisen der Konformität verwendet werden können.  
Ihr erster Einstiegspunkt für alle Überwachungsdaten ist die Option **Überwachungsprotokolle** im Abschnitt **Aktivität** von **Azure Active Directory**.

![Überwachungsprotokolle](./media/active-directory-reporting-activity-audit-logs/61.png "Überwachungsprotokolle")

Ein Überwachungsprotokoll enthält eine Standardlistenansicht mit folgenden Informationen:

- Datum und Uhrzeit des Auftretens
- Initiator/Akteur einer Aktivität (*Wer*) 
- Aktivität (*Was*) 
- Ziel

![Überwachungsprotokolle](./media/active-directory-reporting-activity-audit-logs/18.png "Überwachungsprotokolle")

Sie können die Listenansicht anpassen, indem Sie auf der Symbolleiste auf **Spalten** klicken.

![Überwachungsprotokolle](./media/active-directory-reporting-activity-audit-logs/19.png "Überwachungsprotokolle")

So können Sie weitere Felder anzeigen oder bereits angezeigte Felder entfernen.

![Überwachungsprotokolle](./media/active-directory-reporting-activity-audit-logs/21.png "Überwachungsprotokolle")


Wenn Sie in der Listenansicht auf einen Eintrag klicken, werden die dazu verfügbaren Details angezeigt.

![Überwachungsprotokolle](./media/active-directory-reporting-activity-audit-logs/22.png "Überwachungsprotokolle")


## <a name="filtering-audit-logs"></a>Filtern von Überwachungsprotokollen

Sie können die Überwachungsdaten mit den folgenden Feldern filtern, um die gemeldeten Daten gemäß Ihren Bedürfnissen einzugrenzen:

- Datumsbereich
- Initiiert von (Akteur)
- Category (Kategorie)
- Aktivitätsressourcentyp
- Aktivität

![Überwachungsprotokolle](./media/active-directory-reporting-activity-audit-logs/23.png "Überwachungsprotokolle")


Mit dem Filter **Datumsbereich** können Sie einen Zeitrahmen für die zurückgegebenen Daten festlegen.  
Mögliche Werte:

- 1 Monat
- 7 Tage
- 24 Stunden
- Benutzerdefiniert

Beim Auswählen eines benutzerdefinierten Zeitraums können Sie eine Startzeit und eine Endzeit konfigurieren.

Bei Verwendung des Filters **Initiiert von** können Sie den Namen eines Akteurs oder dessen Benutzerprinzipalnamen (User Principal Name, UPN) definieren.

Bei Verwendung des Filters **Kategorie** können Sie eine der folgenden Filteroptionen auswählen:

- Alle
- Core category (Kernkategorie)
- Core directory (Kernverzeichnis)
- Self-service password management (Self-Service-Kennwortverwaltung)
- Self-Service-Gruppenverwaltung
- Kontobereitstellung: Automated password rollover (Automatisiertes Kennwortrollover)
- Invited Users (Eingeladene Benutzer)
- MIM service (MIM-Dienst)
- Schutz der Identität (Identity Protection)
- B2C

Bei Verwendung des Filters **Aktivitätsressourcentyp** können Sie eine der folgenden Filteroptionen auswählen:

- Alle 
- Gruppe
- Verzeichnis
- Benutzer
- Anwendung
- Richtlinie
- Gerät
- Andere

Wenn Sie als **Aktivitätsressourcentyp** die Option **Gruppe** auswählen, erhalten Sie eine zusätzliche Filterkategorie zur Angabe einer **Quelle**:

- Azure AD
- O365


Der Filter **Aktivität** basiert auf der getroffenen Auswahl für „Kategorie“ und „Aktivitätsressourcentyp“. Sie können entweder eine bestimmte Aktivität verwenden oder alle auswählen. 

Sie können die Liste aller Überwachungsaktivitäten mit der Graph-API https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta abrufen. Dabei ist „$tenantdomain“ Ihr Domänenname. Alternativ können Sie die Informationen im Artikel [Berichte zu Überwachungsaktivitäten im Azure Active Directory-Portal](active-directory-reporting-audit-events.md) lesen.


## <a name="audit-logs-shortcuts"></a>Verknüpfungen für Überwachungsprotokolle

Neben **Azure Active Directory** bietet das Azure-Portal noch zwei weitere Einstiegspunkte für Überwachungsdaten:

- Benutzer und Gruppen
- Unternehmensanwendungen

### <a name="users-and-groups-audit-logs"></a>Überwachungsprotokolle für Benutzer und Gruppen

Mit Überwachungsberichten, die auf Benutzern und Gruppen basieren, können Sie beispielsweise Antworten auf folgende Fragen erhalten:

- Welche Arten von Updates wurden von den Benutzern angewendet?

- Wie viele Benutzer wurden geändert?

- Wie viele Kennwörter wurden geändert?

- Welche Schritte hat ein Administrator in einem Verzeichnis ausgeführt?

- Welche Gruppen wurden hinzugefügt?

- Sind Gruppen mit Änderungen der Mitgliedschaft vorhanden?

- Haben sich die Besitzer der Gruppe geändert?

- Welche Lizenzen wurden einer Gruppe oder einem Benutzer zugewiesen?

Wenn Sie nur Überwachungsdaten überprüfen möchten, die sich auf Benutzer und Gruppen beziehen, können Sie die gefilterte Ansicht unter **Überwachungsprotokolle** im Abschnitt **Aktivität** der Option **Benutzer und Gruppen** verwenden. Bei diesem Einstiegspunkt ist für **Aktivitätsressourcentyp** bereits vorab die Option **Benutzer und Gruppen** ausgewählt.

![Überwachungsprotokolle](./media/active-directory-reporting-activity-audit-logs/93.png "Überwachungsprotokolle")

### <a name="enterprise-applications-audit-logs"></a>Überwachungsprotokolle für Unternehmensanwendungen

Mit Überwachungsberichten, die auf Anwendungen basieren, können Sie beispielsweise Antworten auf folgende Fragen erhalten:

* Welche Anwendungen wurden hinzugefügt oder aktualisiert?
* Welche Anwendungen wurden entfernt?
* Hat sich ein Dienstprinzip für eine Anwendung geändert?
* Haben sich die Namen von Anwendungen geändert?
* Wer hat die Zustimmung zu einer Anwendung erteilt?

Wenn Sie nur Überwachungsdaten überprüfen möchten, die sich auf Ihre Anwendungen beziehen, können Sie die gefilterte Ansicht unter **Überwachungsprotokolle** im Abschnitt **Aktivität** des Blatts **Unternehmensanwendungen** verwenden. Bei diesem Einstiegspunkt ist für **Aktivitätsressourcentyp** bereits vorab die Option **Unternehmensanwendungen** ausgewählt.

![Überwachungsprotokolle](./media/active-directory-reporting-activity-audit-logs/134.png "Überwachungsprotokolle")

Dieser Ansicht kann weiter nach **Gruppen** oder **Benutzer** gefiltert werden.

![Überwachungsprotokolle](./media/active-directory-reporting-activity-audit-logs/25.png "Überwachungsprotokolle")



## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Berichterstellung finden Sie unter [Azure Active Directory-Berichterstellung](active-directory-reporting-azure-portal.md).

- Eine vollständige Liste mit allen Überwachungsaktivitäten finden Sie in der [Referenz zu Überwachungsaktivitäten von Azure AD](active-directory-reporting-activity-audit-reference.md).

