---
title: Verwalten von Arbeitsbereichen in Azure Log Analytics und im OMS-Portal | Microsoft-Dokumentation
description: Arbeitsbereiche können in Azure Log Analytics und im OMS-Portal mithilfe verschiedener Verwaltungsaufgaben für Benutzer, Konten, Arbeitsbereiche und Azure-Konten verwaltet werden.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/16/2018
ms.author: magoedte
ms.openlocfilehash: d2480936ed54ec58ba289eae1ba605a16e27f0b3
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2018
ms.locfileid: "34271669"
---
# <a name="manage-workspaces"></a>Verwalten von Arbeitsbereichen

Zum Verwalten des Zugriffs auf Log Analytics führen Sie verschiedene Verwaltungsaufgaben für Arbeitsbereiche durch. Dieser Artikel enthält Informationen zu den bewährten Methoden und Verfahren für die Arbeitsbereichsverwaltung. Ein Arbeitsbereich ist im Wesentlichen ein Container, der Kontoinformationen und einfache Konfigurationsinformationen für das Konto enthält. Sie oder andere Mitglieder Ihrer Organisation können mehrere Arbeitsbereiche nutzen, um unterschiedliche Mengen von Daten zu verwalten, die in Ihrer gesamten IT-Infrastruktur oder Teilen davon erfasst werden.

Sie benötigen Folgendes, um einen Arbeitsbereich zu erstellen:

1. Ein Azure-Abonnement
2. Einen Namen für den Arbeitsbereich
3. Zuordnung des Arbeitsbereichs zum Abonnement
4. Ausgewählten geografischen Standort

## <a name="determine-the-number-of-workspaces-you-need"></a>Bestimmen der benötigten Anzahl von Arbeitsbereichen
Ein Arbeitsbereich ist eine Azure-Ressource. Es handelt sich hierbei um einen Container, in dem Daten gesammelt, aggregiert, analysiert und im Azure-Portal angezeigt werden.

Sie können mehrere Arbeitsbereiche pro Azure-Abonnement verwenden und über den Zugriff auf mehr als einen Arbeitsbereich mit einfacher Abfragemöglichkeit verfügen. In diesem Abschnitt wird beschrieben, wann es hilfreich sein kann, mehr als einen Arbeitsbereich zu erstellen.

Ein Arbeitsbereich bietet jetzt Folgendes:

* Einen geografischen Standort für die Speicherung von Daten
* Granularität für die Abrechnung
* Datenisolation
* Bereich für die Konfiguration

Auf der Grundlage der obigen Merkmale können Sie in folgenden Szenarien mehrere Arbeitsbereiche erstellen:

* Sie sind ein globales Unternehmen und müssen Daten aus Gründen der Datensouveränität bzw. aus Compliancegründen in bestimmten Regionen speichern.
* Sie nutzen Azure und möchten Gebühren für ausgehende Datenübertragungen vermeiden, indem Sie einen Arbeitsbereich in derselben Region wie die verwalteten Azure-Ressourcen nutzen.
* Sie möchten Gebühren basierend auf der Nutzung unterschiedlichen Abteilungen bzw. Geschäftseinheiten zuordnen. Wenn Sie einen Arbeitsbereich für jede Abteilung bzw. Geschäftseinheit erstellen, werden die Gebühren in Ihrer Azure-Rechnung und Nutzungsaufstellung für jeden Arbeitsbereich separat aufgeführt.
* Sie sind ein Dienstanbieter mit Verwaltung und müssen die Log Analytics-Daten für jeden Kunden, den Sie verwalten, von den Daten der anderen Kunden isolieren.
* Sie verwalten mehrere Kunden und möchten, dass den einzelnen Kunden/Abteilungen/Geschäftseinheiten jeweils nur die eigenen Daten angezeigt werden.

Wenn Sie Windows-Agents zum Sammeln von Daten verwenden, können Sie [jeden Agent so konfigurieren, dass er Informationen zu mindestens einem Arbeitsbereich meldet](log-analytics-windows-agents.md).

Bei Verwendung von System Center Operations Manager kann jede Operations Manager-Verwaltungsgruppe mit nur einem Arbeitsbereich verbunden werden. Sie können den Microsoft Monitoring Agent auf Computern installieren, die mit Operations Manager verwaltet werden, und den Agent so einrichten, dass er sowohl Daten an Operations Manager als auch an einen anderen Log Analytics-Arbeitsbereich liefert.

### <a name="workspace-information"></a>Informationen zum Arbeitsbereich

Sie können im Azure-Portal die Details zu Ihrem Arbeitsbereich anzeigen. Außerdem können Sie im OMS-Portal Details anzeigen.

#### <a name="view-workspace-information-in-the-azure-portal"></a>Anzeigen von Arbeitsbereichsinformationen im Azure-Portal

1. Melden Sie sich mit Ihrem Azure-Abonnement beim [Azure-Portal](https://portal.azure.com) an, sofern Sie noch nicht angemeldet sind.
2. Klicken Sie im Menü **Hub** auf **Weitere Dienste**, und geben Sie in der Liste mit den Ressourcen **Log Analytics** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Klicken Sie auf **Log Analytics**.  
    ![Azure-Hub](./media/log-analytics-manage-access/hub.png)  
3. Wählen Sie auf dem Blatt mit den Log Analytics-Abonnements einen Arbeitsbereich aus.
4. Auf dem Blatt zum Arbeitsbereich werden Details zum Arbeitsbereich und Links zu weiteren Informationen angezeigt.  
    ![Informationen zum Arbeitsbereich](./media/log-analytics-manage-access/workspace-details.png)  


## <a name="manage-accounts-and-users"></a>Verwalten von Konten und Benutzern
Jedem Arbeitsbereich können mehrere Konten zugeordnet sein, wobei jedes Konto (Microsoft- oder Organisationskonto) Zugriff auf mehrere Arbeitsbereiche hat.

Standardmäßig wird der Besitzer des Microsoft- oder Organisationskontos, mit dem der Arbeitsbereich erstellt wird, zum Administrator des Arbeitsbereichs.

Es gibt zwei Berechtigungsmodelle, mit denen der Zugriff auf einen Log Analytics-Arbeitsbereich gesteuert wird:

1. Ältere Log Analytics-Benutzerrollen
2. [Rollenbasierter Zugriff in Azure](../active-directory/role-based-access-control-configure.md)

In der folgenden Tabelle sind die Zugriffsmöglichkeiten aufgeführt, die für die einzelnen Berechtigungsmodelle festgelegt werden können:

|                          | Log Analytics-Portal | Azure-Portal | API (einschließlich PowerShell) |
|--------------------------|----------------------|--------------|----------------------------|
| Log Analytics-Benutzerrollen | Ja                  | Nein            | Nein                          |
| Rollenbasierter Zugriff in Azure  | Ja                  | Ja          | Ja                        |

> [!NOTE]
> Für Log Analytics wird eine Umstellung auf die Verwendung des rollenbasierten Zugriffs in Azure als Berechtigungsmodell durchgeführt, und die Log Analytics-Benutzerrollen werden hierdurch ersetzt.
>
>

Mit den älteren Log Analytics-Benutzerrollen wird nur der Zugriff auf Aktivitäten gesteuert, die im [Log Analytics-Portal](https://mms.microsoft.com) durchgeführt werden.

Für die folgenden Aktivitäten sind ebenfalls Azure-Berechtigungen erforderlich:

| anzuzeigen.                                                          | Azure-Berechtigungen erforderlich | Notizen |
|-----------------------------------------------------------------|--------------------------|-------|
| Hinzufügen und Entfernen von Verwaltungslösungen                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | |
| Ändern des Tarifs                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Anzeigen von Daten auf den Kacheln der *Backup*- und *Site Recovery*-Lösungen | Administrator/Co-Administrator | Zugriff auf Ressourcen, die mit dem klassischen Bereitstellungsmodell bereitgestellt werden |
| Erstellen eines Arbeitsbereichs im Azure-Portal                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>Verwalten des Zugriffs auf Log Analytics mit Azure-Berechtigungen
Führen Sie die Schritte unter [Verwenden von Rollenzuweisungen zum Verwalten Ihrer Azure-Abonnementressourcen](../active-directory/role-based-access-control-configure.md) aus, um den Zugriff auf den Log Analytics-Arbeitsbereich mit Azure-Berechtigungen zu gewähren.

Azure verfügt über zwei integrierte Benutzerrollen für Log Analytics:
- Log Analytics-Leser
- Log Analytics-Mitwirkender

Mitglieder der Rolle *Log Analytics-Leser* können folgende Aktionen ausführen:
- Anzeigen und Durchsuchen aller Überwachungsdaten 
- Anzeigen von Überwachungseinstellungen (einschließlich der Konfiguration von Azure-Diagnosen für alle Azure-Ressourcen)

| Typ    | Berechtigung | BESCHREIBUNG |
| ------- | ---------- | ----------- |
| anzuzeigen. | `*/read`   | Anzeigen aller Ressourcen und der Ressourcenkonfiguration, einschließlich: <br> VM-Erweiterungsstatus <br> Konfiguration von Azure-Diagnosen für Ressourcen <br> Sämtliche Eigenschaften und Einstellungen aller Ressourcen |
| anzuzeigen. | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Ausführen von Protokollsuchabfragen (v2) |
| anzuzeigen. | `Microsoft.OperationalInsights/workspaces/search/action` | Ausführen von Protokollsuchabfragen (v1) |
| anzuzeigen. | `Microsoft.Support/*` | Öffnen von Supportfällen |
|Keine Aktion | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Verhindert das Lesen des Arbeitsbereichsschlüssels, der zum Verwenden der Datensammlungs-API und zum Installieren von Agents erforderlich ist |


Mitglieder der Rolle *Log Analytics-Mitwirkender* können folgende Aktionen ausführen:
- Lesen sämtlicher Überwachungsdaten 
- Erstellen und Konfigurieren von Automation-Konten
- Hinzufügen und Entfernen von Verwaltungslösungen
- Lesen von Speicherkontoschlüsseln 
- Konfigurieren der Sammlung von Protokollen aus Azure Storage
- Bearbeiten von Überwachungseinstellungen für Azure-Ressourcen, einschließlich:
  - Hinzufügen der VM-Erweiterung zu virtuellen Computern
  - Konfigurieren von Azure-Diagnosen für alle Azure-Ressourcen

> [!NOTE] 
> Durch Hinzufügen einer VM-Erweiterung zu einem virtuellen Computer können Sie die vollständige Kontrolle über einen virtuellen Computer erlangen.

| Berechtigung | BESCHREIBUNG |
| ---------- | ----------- |
| `*/read`     | Anzeigen aller Ressourcen und der Ressourcenkonfiguration, einschließlich: <br> VM-Erweiterungsstatus <br> Konfiguration von Azure-Diagnosen für Ressourcen <br> Sämtliche Eigenschaften und Einstellungen aller Ressourcen |
| `Microsoft.Automation/automationAccounts/*` | Erstellen und Konfigurieren von Azure Automation-Konten (einschließlich Hinzufügen und Bearbeiten von Runbooks) |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Hinzufügen, Aktualisieren und Entfernen von VM-Erweiterungen (einschließlich Microsoft Monitoring Agent und des OMS-Agents für Linux) |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Anzeigen des Speicherkontoschlüssels. Erforderlich, um Log Analytics für das Lesen von Protokollen aus Azure-Speicherkonten zu konfigurieren |
| `Microsoft.Insights/alertRules/*` | Hinzufügen, Aktualisieren und Entfernen von Warnungsregeln |
| `Microsoft.Insights/diagnosticSettings/*` | Hinzufügen, Aktualisieren und Entfernen von Diagnoseeinstellungen für Azure-Ressourcen |
| `Microsoft.OperationalInsights/*` | Hinzufügen, Aktualisieren und Entfernen der Konfiguration für Log Analytics-Arbeitsbereiche |
| `Microsoft.OperationsManagement/*` | Hinzufügen und Entfernen von Verwaltungslösungen |
| `Microsoft.Resources/deployments/*` | Erstellen und Löschen von Bereitstellungen. Erforderlich für das Hinzufügen und Entfernen von Lösungen, Arbeitsbereichen und Automation-Konten |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Erstellen und Löschen von Bereitstellungen. Erforderlich für das Hinzufügen und Entfernen von Lösungen, Arbeitsbereichen und Automation-Konten |

Wenn Sie Benutzer einer Benutzerrolle hinzufügen oder daraus entfernen möchten, müssen Sie über die Berechtigungen `Microsoft.Authorization/*/Delete` und `Microsoft.Authorization/*/Write` verfügen.

Mit diesen Rollen können Sie Benutzern Zugriff auf verschiedenen Ebenen gewähren:
- Abonnement: Zugriff auf alle Arbeitsbereiche im Abonnement
- Ressourcengruppe: Zugriff auf alle Arbeitsbereiche in der Ressourcengruppe
- Ressource: Nur Zugriff auf den angegebenen Arbeitsbereich

Verwenden Sie [benutzerdefinierte Rollen](../active-directory/role-based-access-control-custom-roles.md), um Rollen mit spezifischen Berechtigungen zu erstellen.

### <a name="azure-user-roles-and-log-analytics-portal-user-roles"></a>Azure-Benutzerrollen und Benutzerrollen des Log Analytics-Portals
Wenn Sie für den Log Analytics-Arbeitsbereich mindestens über die Azure-Leseberechtigung verfügen, können Sie das Log Analytics-Portal öffnen, indem Sie im Log Analytics-Arbeitsbereich auf die Aufgabe **OMS-Portal** klicken.

Beim Öffnen des Log Analytics-Portals wechseln Sie zu den bisher verwendeten Log Analytics-Benutzerrollen. Falls Sie im Log Analytics-Portal nicht über eine Rollenzuweisung verfügen, [überprüft der Dienst Ihre Azure-Berechtigungen für den Arbeitsbereich](https://docs.microsoft.com/rest/api/authorization/permissions#Permissions_ListForResource).
Ihre Rollenzuweisung im Log Analytics-Portal wird wie folgt ermittelt:

| Bedingungen                                                   | Zugewiesene Log Analytics-Benutzerrolle | Notizen |
|--------------------------------------------------------------|----------------------------------|-------|
| Ihr Konto gehört zu einer älteren Log Analytics-Benutzerrolle.     | Angegebene Log Analytics-Benutzerrolle | |
| Ihr Konto gehört nicht zu einer älteren Log Analytics-Benutzerrolle. <br> Vollständige Azure-Berechtigungen für den Arbeitsbereich (Berechtigung `*` <sup>1</sup>) | Administrator ||
| Ihr Konto gehört nicht zu einer älteren Log Analytics-Benutzerrolle. <br> Vollständige Azure-Berechtigungen für den Arbeitsbereich (Berechtigung `*` <sup>1</sup>). <br> *Keine Aktionen* von `Microsoft.Authorization/*/Delete` und `Microsoft.Authorization/*/Write` | Mitwirkender ||
| Ihr Konto gehört nicht zu einer älteren Log Analytics-Benutzerrolle. <br> Azure-Leseberechtigung | Nur Leseberechtigung ||
| Ihr Konto gehört nicht zu einer älteren Log Analytics-Benutzerrolle. <br> Azure-Berechtigungen werden nicht verstanden. | Nur Leseberechtigung ||
| Für per Cloud Solution Provider (CSP) verwaltete Abonnements. <br> Das Konto, mit dem Sie angemeldet sind, befindet sich unter der Azure Active Directory-Instanz, die mit dem Arbeitsbereich verknüpft ist. | Administrator | Normalerweise der Kunde eines CSP |
| Für per Cloud Solution Provider (CSP) verwaltete Abonnements. <br> Das Konto, mit dem Sie angemeldet sind, befindet sich nicht unter der Azure Active Directory-Instanz, die mit dem Arbeitsbereich verknüpft ist. | Mitwirkender | Normalerweise der CSP |

<sup>1</sup> Weitere Informationen zu Rollendefinition finden Sie unter [Erstellen von benutzerdefinierten Rollen für die rollenbasierte Zugriffssteuerung in Azure](../active-directory/role-based-access-control-custom-roles.md). Beim Auswerten von Rollen ist die Aktion `*` nicht äquivalent zu `Microsoft.OperationalInsights/workspaces/*`.

Wichtige Punkte zum Azure-Portal:

* Wenn Sie sich über http://mms.microsoft.com beim OMS-Portal anmelden, wird die Liste **Arbeitsbereich auswählen** angezeigt. Diese Liste enthält nur Arbeitsbereiche, für die Sie über eine Log Analytics-Benutzerrolle verfügen. Zum Anzeigen der Arbeitsbereiche, auf die Sie mit Azure-Abonnements zugreifen können, müssen Sie als Teil der URL einen Mandanten angeben. Beispiel: `mms.microsoft.com/?tenant=contoso.com`. Die Mandanten-ID ist häufig dieser letzte Teil der E-Mail-Adresse, die Sie bei der Anmeldung verwenden.
* Wenn Sie direkt zu einem Portal navigieren möchten, auf das Sie mit Azure-Berechtigungen Zugriff haben, müssen Sie die Ressource als Teil der URL angeben. Es ist möglich, diese URL mit PowerShell abzurufen.

  Beispiel: `(Get-AzureRmOperationalInsightsWorkspace).PortalUrl`.

  Die URL sieht wie folgt aus: `https://eus.mms.microsoft.com/?tenant=contoso.com&resource=%2fsubscriptions%2faaa5159e-dcf6-890a-a702-2d2fee51c102%2fresourcegroups%2fdb-resgroup%2fproviders%2fmicrosoft.operationalinsights%2fworkspaces%2fmydemo12`

### <a name="managing-users-in-the-oms-portal"></a>Verwalten von Benutzern im OMS-Portal
Sie verwalten Benutzer und Gruppen auf der Registerkarte **Benutzer verwalten** unter der Registerkarte **Konten** auf der Seite „Einstellungen“.   

![Verwalten von Benutzern](./media/log-analytics-manage-access/setup-workspace-manage-users.png)


#### <a name="add-a-user-to-an-existing-workspace"></a>Hinzufügen eines Benutzers zu einem vorhandenen Arbeitsbereich
Gehen Sie wie folgt vor, um einen Benutzer oder eine Gruppe einem Arbeitsbereich hinzuzufügen.

1. Klicken Sie im OMS-Portal auf die Kachel **Einstellungen**.
2. Klicken Sie auf die Registerkarte **Konten** und dann auf die Registerkarte **Benutzer verwalten**.
3. Wählen Sie im Abschnitt **Benutzer verwalten** den hinzuzufügenden Kontotyp aus: **Unternehmenskonto**, **Microsoft-Konto** oder **Microsoft-Support**.

   * Wenn Sie „Microsoft-Konto“ auswählen, geben Sie die E-Mail-Adresse des Benutzers ein, der dem Microsoft-Konto zugeordnet ist.
   * Geben Sie einen Teil des Benutzer- oder Gruppennamens bzw. des E-Mail-Alias ein, wenn Sie „Unternehmenskonto“ auswählen. Anschließend wird in einem Dropdownfeld eine Liste mit passenden Benutzern und Gruppen angezeigt. Wählen Sie einen Benutzer oder eine Gruppe aus.
   * Verwenden Sie „Microsoft-Support“, um einem Microsoft-Supporttechniker oder einem anderen Microsoft-Mitarbeiter vorübergehenden Zugriff auf Ihren Arbeitsbereich zu gewähren, damit er bei der Problembehandlung behilflich sein kann.

     > [!NOTE]
     > Damit Sie eine optimale Leistung erzielen, sollten Sie die Anzahl von Active Directory-Gruppen, die einem einzelnen OMS-Konto zugeordnet sind, auf drei begrenzen – eines für Administratoren, eines für Mitwirkende und eines für ReadOnly-Benutzer. Mehr Gruppen können sich auf die Leistung von Log Analytics auswirken.
     >
     >
4. Wählen Sie den hinzuzufügenden Benutzer- oder Gruppentyp aus: **Administrator**, **Mitwirkender** oder **ReadOnly-Benutzer**.  
5. Klicken Sie auf **Hinzufügen**.

   Wenn Sie ein Microsoft-Konto hinzufügen, wird eine Einladung zur Teilnahme am Arbeitsbereich an die angegebene E-Mail-Adresse gesendet. Nachdem der Benutzer die Anweisungen in der Einladung zur Teilnahme an OMS befolgt hat, kann er auf den Arbeitsbereich zugreifen.
   Wenn Sie ein Organisationskonto hinzufügen, kann der Benutzer sofort auf Log Analytics zugreifen.  

#### <a name="edit-an-existing-user-type"></a>Bearbeiten eines vorhandenen Benutzertyps
Sie können die Kontorolle eines Benutzers ändern, der Ihrem OMS-Konto zugeordnet ist. Sie haben die folgenden Rollenoptionen:

* *Administrator*: Kann Benutzer verwalten, alle Warnungen anzeigen und darauf reagieren sowie Server hinzufügen und entfernen
* *Mitwirkender*: Kann alle Warnungen anzeigen und darauf reagieren sowie Server hinzufügen und entfernen
* *ReadOnly-Benutzer*: Als schreibgeschützt gekennzeichnete Benutzer können folgende Aktionen nicht ausführen:

  1. Lösungen hinzufügen/entfernen Der Lösungskatalog ist ausgeblendet
  2. Kacheln unter **Mein Dashboard** hinzufügen/ändern/entfernen
  3. Seiten unter **Einstellungen** anzeigen Die Seiten sind ausgeblendet.
  4. In der Suchansicht: PowerBI-Konfiguration, gespeicherte Suchen und Warnungsaufgaben sind ausgeblendet

#### <a name="to-edit-an-account"></a>So bearbeiten Sie ein Konto
1. Klicken Sie im OMS-Portal auf die Kachel **Einstellungen**.
2. Klicken Sie auf die Registerkarte **Konten** und dann auf die Registerkarte **Benutzer verwalten**.
3. Wählen Sie die Rolle für den Benutzer, den Sie ändern möchten.
4. Klicken Sie im Bestätigungsdialogfeld auf **Ja**.

### <a name="remove-a-user-from-a-workspace"></a>Entfernen eines Benutzers aus einem Arbeitsbereich
Führen Sie die unten angegebenen Schritte aus, um einen Benutzer aus einem Arbeitsbereich zu entfernen. Durch Entfernen des Benutzers wird der Arbeitsbereich nicht geschlossen. Stattdessen wird die Zuordnung zwischen diesem Benutzer und dem Arbeitsbereich aufgehoben. Wenn ein Benutzer mehreren Arbeitsbereichen zugeordnet ist, kann er sich weiterhin bei OMS anmelden und seine anderen Arbeitsbereiche anzeigen.

1. Klicken Sie im OMS-Portal auf die Kachel **Einstellungen**.
2. Klicken Sie auf die Registerkarte **Konten** und dann auf die Registerkarte **Benutzer verwalten**.
3. Klicken Sie neben dem Namen des Benutzers, den Sie entfernen möchten, auf **Entfernen**.
4. Klicken Sie im Bestätigungsdialogfeld auf **Ja**.

### <a name="add-a-group-to-an-existing-workspace"></a>Hinzufügen einer Gruppe zu einem vorhandenen Arbeitsbereich
1. Führen Sie die Schritte 1 bis 4 des obigen Abschnitts „So fügen Sie einen Benutzer einem vorhandenen Arbeitsbereich hinzu“ aus.
2. Wählen Sie unter **Benutzer/Gruppe auswählen** die Option **Gruppe**.  
   ![add a group to an existing workspace](./media/log-analytics-manage-access/add-group.png)
3. Geben Sie den Anzeigenamen oder die E-Mail-Adresse für die Gruppe ein, die Sie hinzufügen möchten.
4. Wählen Sie in der Liste die gewünschte Gruppe aus, und klicken Sie auf **Hinzufügen**.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Verknüpfen eines vorhandenen Arbeitsbereichs mit einem Azure-Abonnement
Ab dem 26. September 2016 müssen alle Arbeitsbereiche bei der Erstellung mit einem Azure-Abonnement verknüpft werden. Vor diesem Datum erstellte Arbeitsbereiche müssen bei der Anmeldung mit einem Abonnement verknüpft werden. Wenn Sie den Arbeitsbereich über das Azure-Portal erstellen oder den Arbeitsbereich mit einem Azure-Abonnement verknüpfen, wird Azure Active Directory als Organisationskonto verknüpft.

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-oms-portal"></a>So verknüpfen Sie einen Arbeitsbereich mit einem Azure-Abonnement im OMS-Portal

- Wenn Sie sich beim OMS-Portal anmelden, werden Sie aufgefordert, ein Azure-Abonnement auszuwählen. Wählen Sie das Abonnement aus, das Sie mit Ihrem Arbeitsbereich verknüpfen möchten, und klicken Sie anschließend auf **Verknüpfen**.  
    ![Verknüpfen des Azure-Abonnements](./media/log-analytics-manage-access/required-link.png)

    > [!IMPORTANT]
    > Damit Sie einen Arbeitsbereich verknüpfen können, muss Ihr Azure-Konto bereits Zugriff auf den zu verknüpfenden Arbeitsbereich haben.  Anders ausgedrückt: Das Konto, das Sie für den Zugriff auf das Azure-Portal verwenden, muss **identisch** mit dem Konto sein, mit dem Sie auf den Arbeitsbereich zugreifen. Ist dies nicht der Fall, lesen Sie unter [Hinzufügen eines Benutzers zu einem vorhandenen Arbeitsbereich](#add-a-user-to-an-existing-workspace) weiter.

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>So verknüpfen Sie einen Arbeitsbereich mit einem Azure-Abonnement im Azure-Portal
1. Melden Sie sich beim [Azure-Portal](http://portal.azure.com) an.
2. Suchen Sie nach **Log Analytics**, und wählen Sie diese Option aus.
3. Ihre Liste mit den vorhandenen Arbeitsbereichen wird angezeigt. Klicken Sie auf **Hinzufügen**.  
   ![Liste der Arbeitsbereiche](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4. Klicken Sie unter **OMS Workspace** (OMS-Arbeitsbereich) auf **Or link existing** (Oder vorhandenen einfügen).  
   ![Vorhandene verknüpfen](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5. Klicken Sie auf **Erforderliche Einstellungen konfigurieren**.  
   ![configure required settings](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6. Daraufhin wird eine Liste mit Arbeitsbereichen angezeigt, die noch nicht mit Ihrem Azure-Konto verknüpft sind. Wählen Sie einen Arbeitsbereich aus.  
   ![Auswählen von Arbeitsbereichen](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7. Bei Bedarf können Sie die Werte für die folgenden Elemente ändern:
   * Abonnement
   * Ressourcengruppe
   * Speicherort
   * Tarif  
     ![Ändern von Werten](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8. Klicken Sie auf **OK**. Der Arbeitsbereich ist jetzt mit Ihrem Azure-Konto verknüpft.

> [!NOTE]
> Wenn der Arbeitsbereich, den Sie verknüpfen möchten, hier nicht angezeigt wird, hat Ihr Azure-Abonnement keinen Zugriff auf den Arbeitsbereich, den Sie mit dem OMS-Portal erstellt haben.  Unter [Hinzufügen eines Benutzers zu einem vorhandenen Arbeitsbereich](#add-a-user-to-an-existing-workspace) erfahren Sie, wie Sie über das OMS-Portal Zugriff auf dieses Konto gewähren.
>
>

## <a name="upgrade-a-workspace-to-a-paid-plan"></a>Upgraden eines Arbeitsbereichs auf einen kostenpflichtigen Plan
Für OMS stehen drei Arten von Arbeitsbereichsplänen zur Verfügung: **Free**, **Eigenständig** und **OMS**.  Bei der Planoption *Free* ist das an Log Analytics gesendete Datenvolumen auf 500 MB pro Tag beschränkt.  Wenn Sie diese Menge überschreiten, müssen Sie Ihren Arbeitsbereich auf einen kostenpflichtigen Plan upgraden, um zu vermeiden, dass die über diesen Grenzwert hinausgehenden Daten nicht erfasst werden. Der Plantyp kann jederzeit geändert werden.  Weitere Informationen zu den Preisen für OMS finden Sie unter [Preise](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-pricing).

### <a name="using-entitlements-from-an-oms-subscription"></a>Verwenden von Berechtigungen aus einem OMS-Abonnement
Wenn Sie die Berechtigungen nutzen möchten, die Sie durch den Kauf von OMS E1, OMS E2 oder des OMS-Add-Ons für System Center erwerben, wählen Sie die Planoption *OMS* von OMS Log Analytics aus.

Beim Kauf eines OMS-Abonnements werden die Berechtigungen Ihrem Enterprise Agreement hinzugefügt. Die Berechtigungen stehen für alle Azure-Abonnements zur Verfügung, die im Rahmen dieses Vertrags erstellt werden. Verwenden Sie für alle Arbeitsbereiche in diesen Abonnements die OMS-Berechtigungen.

Gehen Sie wie folgt vor, um sicherzustellen, dass die Verwendung eines Arbeitsbereichs auf Ihre Berechtigungen des OMS-Abonnements angewendet wird:

1. Erstellen Sie Ihren Arbeitsbereich unter einem Azure-Abonnement, das zum Enterprise Agreement mit dem OMS-Abonnement gehört.
2. Wählen Sie für den Arbeitsbereich die Planoption *OMS* aus.

> [!NOTE]
> Wenn Ihr Arbeitsbereich vor dem 26. September 2016 erstellt wurde und Ihr Log Analytics-Preisplan *Premium* lautet, verwendet dieser Arbeitsbereich Berechtigungen aus dem OMS-Add-On für System Center. Die Berechtigungen können auch durch einen Wechsel zum Tarif *OMS* genutzt werden.
>
>

Die OMS-Abonnementberechtigungen werden im Azure- oder im OMS-Portal nicht angezeigt. Die Berechtigungen und die Nutzung können im Enterprise Portal angezeigt werden.  

Wenn Sie das Azure-Abonnement ändern möchten, mit dem Ihr Arbeitsbereich verknüpft ist, können Sie das Azure PowerShell-Cmdlet [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) verwenden.

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>Verwenden von Azure Commitment von einem Enterprise Agreement
Wenn Sie über kein OMS-Abonnement verfügen, wird jede OMS-Komponente separat abgerechnet, und die Nutzung wird auf Ihrer Azure-Rechnung ausgewiesen.

Wenn Sie über einen monetären Azure-Verpflichtungsbetrag für die Unternehmensanmeldung verfügen, mit der Ihre Azure-Abonnements verknüpft sind, wird die Nutzung von Log Analytics automatisch mit dem verbleibenden monetären Verpflichtungsbetrag verrechnet.

Zum Ändern des Azure-Abonnements, mit dem der Arbeitsbereich verknüpft ist, können Sie das Azure PowerShell-Cmdlet [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) verwenden.  

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-azure-portal"></a>Umstellen eines Arbeitsbereichs auf einen kostenpflichtigen Tarif über das Azure-Portal
1. Melden Sie sich beim [Azure-Portal](http://portal.azure.com) an.
2. Suchen Sie nach **Log Analytics**, und wählen Sie diese Option aus.
3. Ihre Liste mit den vorhandenen Arbeitsbereichen wird angezeigt. Wählen Sie einen Arbeitsbereich aus.  
4. Klicken Sie auf dem Blatt für den Arbeitsbereich unter **Allgemein** auf **Tarif**.  
5. Klicken Sie unter **Tarif** auf einen Tarif und anschließend auf **Auswählen**.  
    ![Plan auswählen](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6. Wenn Sie die Ansicht im Azure-Portal aktualisieren, sehen Sie, dass der **Tarif** mit dem ausgewählten Plan aktualisiert wurde.  
    ![Aktualisierter Tarif](./media/log-analytics-manage-access/manage-access-change-plan04.png)

> [!NOTE]
> Falls Ihr Arbeitsbereich mit einem Automation-Konto verknüpft ist und Sie den Tarif *Standalone (Per GB)* (Eigenständig (pro GB)) auswählen möchten, müssen Sie zuvor alle Lösungen vom Typ **Automation & Control** löschen und die Verknüpfung mit dem Automation-Konto aufheben. Klicken Sie auf dem Blatt für den Arbeitsbereich unter **Allgemein** auf **Lösungen**, um die Lösungen anzuzeigen und zu löschen. Klicken Sie zum Aufheben der Verknüpfung mit dem Automation-Konto auf dem Blatt **Tarif** auf den Namen des Automatisierungskontos.
>
>

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-oms-portal"></a>Umstellen eines Arbeitsbereichs auf einen kostenpflichtigen Tarif über das OMS-Portal

Sie müssen über ein Azure-Abonnement verfügen, um den Tarif mithilfe des OMS-Portals zu ändern.

1. Klicken Sie im OMS-Portal auf die Kachel **Einstellungen**.
2. Klicken Sie auf die Registerkarte **Konten****Azure Subscription & Data Plan** (Azure-Abonnement und -Datentarif).
3. Klicken Sie auf den Tarif, den Sie verwenden möchten.
4. Klicken Sie auf **Speichern**.  
   ![Abonnement und Datentarife](./media/log-analytics-manage-access/subscription-tab.png)

Der neue Datentarif wird oben auf der Webseite im Menüband des OMS-Portals angezeigt.

![OMS-Menüband](./media/log-analytics-manage-access/data-plan-changed.png)


## <a name="change-how-long-log-analytics-stores-data"></a>Ändern des Speicherzeitraums für Daten in Log Analytics

Im Tarif „Free“ werden in Log Analytics die Daten der letzten sieben Tage verfügbar gemacht.
Im Tarif „Standard“ werden in Log Analytics die Daten der letzten 30 Tage verfügbar gemacht.
Im Tarif „Premium“ werden in Log Analytics die Daten der letzten 365 Tage verfügbar gemacht.
In den Tarifen „Standalone“ und „OMS“ werden in Log Analytics standardmäßig die Daten der letzten 31 Tage verfügbar gemacht.

Bei Verwendung der Tarife „Standalone“ und „OMS“ können Sie die Daten von bis zu zwei Jahren (730 Tage) aufbewahren. Wenn Daten länger als die standardmäßig verfügbaren 31 Tage gespeichert werden, wird eine Gebühr für die Aufbewahrung der Daten berechnet. Weitere Informationen zu Preisen finden Sie unter [Überschreitungsgebühren](https://azure.microsoft.com/pricing/details/log-analytics/).

Informationen zum Ändern der Dauer der Datenaufbewahrung finden Sie unter [Verwalten der Kosten durch die Steuerung der Datenmenge und -aufbewahrung in Log Analytics](log-analytics-manage-cost-storage.md).

## <a name="change-an-azure-active-directory-organization-for-a-workspace"></a>Ändern einer Azure Active Directory-Organisation für einen Arbeitsbereich

Sie können die Azure Active Directory-Organisation eines Arbeitsbereichs ändern. Wenn Sie die Azure Active Directory-Organisation ändern, können Sie dem Arbeitsbereich Benutzer und Gruppen aus diesem Verzeichnis hinzufügen.

### <a name="to-change-the-azure-active-directory-organization-for-a-workspace"></a>So ändern Sie eine Azure Active Directory-Organisation für einen Arbeitsbereich

1. Klicken Sie im OMS-Portal auf der Seite mit den Einstellungen auf **Konten** und anschließend auf die Registerkarte **Benutzer verwalten**.  
2. Überprüfen Sie die Informationen zu Organisationskonten, und klicken Sie dann auf **Organisation ändern**.  
    ![Organisation ändern](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Geben Sie die Identitätsinformationen des Administrators Ihrer Azure Active Directory-Domäne ein. Anschließend wird bestätigt, dass Ihr Arbeitsbereich mit Ihrer Azure Active Directory-Domäne verknüpft ist.  
    ![Verknüpfte Arbeitsbereichsbestätigung](./media/log-analytics-manage-access/manage-access-add-adorg02.png)


## <a name="delete-a-log-analytics-workspace"></a>Löschen eines Log Analytics-Arbeitsbereichs
Wenn Sie einen Log Analytics-Arbeitsbereich löschen, werden alle damit zusammenhängenden Daten innerhalb von 30 Tagen aus dem Log Analytics-Dienst gelöscht.

Wenn Sie Administrator sind und mehrere Benutzer mit dem Arbeitsbereich verknüpft sind, wird die Zuordnung zwischen den Benutzern und dem Arbeitsbereich aufgehoben. Wenn die Benutzer anderen Arbeitsbereichen zugeordnet sind, können sie Log Analytics mit diesen Arbeitsbereichen weiter nutzen. Wenn sie jedoch keinen anderen Arbeitsbereichen zugeordnet sind, müssen sie einen Arbeitsbereich erstellen, um den Dienst verwenden zu können. Informationen zum Löschen eines Arbeitsbereichs finden Sie unter [Löschen eines Log Analytics-Arbeitsbereichs mit dem Azure-Portal](log-analytics-manage-del-workspace.md).

## <a name="next-steps"></a>Nächste Schritte
* Informationen zum Sammeln von Daten von Computern in Ihrem Datencenter oder einer anderen Cloudumgebung finden Sie unter [Sammeln von Daten von Computern in Ihrer Umgebung mit Log Analytics](log-analytics-concept-hybrid.md).
* Informationen zum Konfigurieren der Datensammlung von virtuellen Azure-Computern finden Sie unter [Sammeln von Daten über virtuelle Azure-Computer](log-analytics-quick-collect-azurevm.md).  
* [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md) (Hinzufügen von Log Analytics-Lösungen aus dem Lösungskatalog) beschreibt das Hinzufügen von Funktionen und das Sammeln von Daten.

