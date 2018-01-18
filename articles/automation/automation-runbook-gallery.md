---
title: "Runbook und Modulkataloge für Azure Automation | Microsoft Docs"
description: "Ihnen stehen Runbooks und Module von Microsoft und der Community zur Verfügung, die Sie in Ihrer Azure Automation-Umgebung installieren und verwenden können.  Dieser Artikel beschreibt, wie Sie auf diese Ressourcen zugreifen und Ihre Runbooks im Katalog bereitstellen können."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: d3fee7b4-630a-4c10-8425-9bf51d7c9e58
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte;bwren
ms.openlocfilehash: d6a950d69e5279c3cc19d8e457bfa23c2b40f515
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2018
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Runbook und Modulkataloge für Azure Automation
Statt eigene Runbooks und Module in Azure Automation zu erstellen, können Sie eine Vielzahl von Szenarien nutzen, die bereits von Microsoft und der Community entwickelt wurden.  Sie können diese Szenarien ohne Anpassung verwenden oder als Ausgangspunkt nutzen und für Ihre spezifischen Anforderungen bearbeiten.

Sie können Runbooks aus dem [Runbook-Katalog](#runbooks-in-runbook-gallery) und Module aus dem [PowerShell-Katalog](#modules-in-powerShell-gallery) abrufen.  Sie können auch etwas zur Community beitragen, indem Sie von Ihnen entwickelte Szenarien zur Verfügung stellen.

## <a name="runbooks-in-runbook-gallery"></a>Runbooks im Runbook-Katalog
Der [Runbook-Katalog](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=RootCategory&f\[0\].Value=WindowsAzure&f\[1\].Type=SubCategory&f\[1\].Value=WindowsAzure_automation&f\[1\].Text=Automation) bietet zahlreiche Runbooks von Microsoft und der Community, die Sie in Azure Automation importieren können. Sie können entweder ein Runbook aus dem Katalog herunterladen, der im [TechNet Script Center](https://gallery.technet.microsoft.com/scriptcenter/site/upload) gehostet ist, oder Sie können Runbooks direkt aus dem Katalog über das Azure-Portal importieren.

Das direkte Importieren aus dem Runbook-Katalog ist nur über das Azure-Portal möglich. Diese Funktion kann nicht mithilfe von Windows PowerShell ausgeführt werden.

> [!NOTE]
> Überprüfen Sie unbedingt den Inhalt der aus dem Runbook-Katalog heruntergeladenen Runbooks, und seien Sie äußerst vorsichtig, wenn Sie sie in einer Produktionsumgebung installieren und ausführen.|
> 
> 

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>So importieren Sie mit dem Azure-Portal ein Runbook aus dem Runbook-Katalog
1. Öffnen Sie im Azure-Portal Ihr Automation-Konto.
2. Klicken Sie auf die Kachel **Runbooks** , um die Liste mit den Runbooks zu öffnen.
3. Klicken Sie auf die Schaltfläche **Katalog durchsuchen** .
   
    ![Schaltfläche „Katalog durchsuchen“](media/automation-runbook-gallery/browse-gallery-button.png)
4. Suchen Sie das gewünschte Katalogelement, und wählen Sie es zum Anzeigen der Details aus.
   
    ![Katalog durchsuchen](media/automation-runbook-gallery/browse-gallery.png)
5. Klicken Sie auf **Quellprojekt anzeigen** , um den Artikel im [TechNet Script Center](http://gallery.technet.microsoft.com/)anzuzeigen.
6. Klicken Sie zum Importieren eines Artikels auf den Artikel, um seine Details anzuzeigen, und klicken Sie anschließend auf die Schaltfläche **Importieren** .
   
    ![Schaltfläche „Importieren“](media/automation-runbook-gallery/gallery-item-detail.png)
7. Ändern Sie optional den Namen des Runbooks, und klicken Sie zum Importieren des Runbooks auf **OK** .
8. Das Runbook wird auf der Registerkarte **Runbooks** des Automation-Kontos angezeigt.

### <a name="adding-a-runbook-to-the-runbook-gallery"></a>Hinzufügen eines Runbooks zum Runbook-Katalog
Microsoft empfiehlt, Runbooks aus dem Runbook-Katalog hinzuzufügen, die für andere Kunden nützlich sein könnten.  Sie können ein Runbook durch [Hochladen ins Script Center](http://gallery.technet.microsoft.com/site/upload) hinzufügen. Berücksichtigen Sie dabei Folgendes:

* Damit das Runbook im Assistenten angezeigt wird, geben Sie als **Kategorie** die Option *Windows Azure* und als **Unterkategorie** die Option *Automation* ein.  
* Laden Sie eine einzelne Datei vom Typ „.ps1“ oder „.graphrunbook“ hoch.  Sind für das Runbook Module, untergeordnete Runbooks oder Objekte erforderlich, führen Sie diese in der Beschreibung der Übermittlung und im Kommentarbereich des Runbooks auf.  Falls für Ihr Szenario mehrere Runbooks erforderlich sind, laden Sie sie einzeln hoch, und führen Sie die Namen der zugehörigen Runbooks in den jeweiligen Beschreibungen auf. Achten Sie darauf, die gleichen Tags zu verwenden, damit die Runbooks in derselben Kategorie angezeigt werden. Dass zur Verwendung des Szenarios weitere Runbooks erforderlich sind, erfährt der Benutzer in der Beschreibung.
* Fügen Sie das Tag „GraphicalPS“ hinzu, wenn Sie ein **grafisches Runbook** (keinen grafisches Workflow) veröffentlichen. 
* Fügen Sie der Beschreibung einen PowerShell- oder PowerShell-Workflow- Codeausschnitt mithilfe des Symbols **Codeabschnitt einfügen** hinzu.
* Die Zusammenfassung für den Upload wird in den Ergebnissen des Runbook-Katalogs angezeigt. Geben Sie daher ausführliche Informationen ein, die dem Benutzer die Funktionen des Runbooks verdeutlichen.
* Weisen Sie dem Upload bis zu drei der folgenden Tags zu:  Das Runbook wird im Assistenten unter den Kategorien aufgeführt, die mit seinen Tags übereinstimmen.  Nicht in dieser Liste enthaltene Tags werden vom Assistenten ignoriert. Wenn Sie keine übereinstimmenden Tags angeben, wird das Runbook unter der Kategorie „Andere“ aufgeführt.
  
  * Backup 
  * Capacity Management
  * Änderungssteuerung
  * Compliance
  * Entwicklungs-/Testumgebungen
  * Notfallwiederherstellung.
  * Überwachung
  * Patching
  * Bereitstellung
  * Wiederherstellung
  * Lebenszyklusverwaltung für virtuelle Computer
* Der Katalog wird von Automation stündlich aktualisiert. Ihre Beiträge werden daher unter Umständen nicht sofort angezeigt.

## <a name="modules-in-powershell-gallery"></a>Module im PowerShell-Katalog
PowerShell-Module enthalten Cmdlets, die Sie in Ihren Runbooks verwenden können. Vorhandene Module, die Sie in Azure Automation installieren können, sind im [PowerShell-Katalog](http://www.powershellgallery.com) verfügbar.  Sie starten diesen Katalog über das Azure-Portal und installieren die Module direkt in Azure Automation. Sie können sie auch herunterladen und manuell installieren.  

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>So importieren Sie mit dem Azure-Portal ein Modul aus dem Automation-Modulkatalog
1. Öffnen Sie im Azure-Portal Ihr Automation-Konto.
2. Wählen Sie **Module** unter **Freigegebene Ressourcen** aus, um die Liste der Module zu öffnen.
4. Klicken Sie oben auf der Seite auf **Katalog durchsuchen**.
   
    ![Modulkatalog](media/automation-runbook-gallery/modules-blade.png) <br>
5. Auf der Seite **Katalog durchsuchen** können Sie nach den folgenden Feldern suchen:
   
   * Modulname
   * Tags
   * Autor
   * Cmdlet-/DSC-Ressourcenname
6. Suchen Sie das gewünschte Modul, und wählen Sie es aus, um seine Details anzuzeigen.  
   Wenn Sie einen Drilldown in einem bestimmten Modul durchführen, können Sie weitere Informationen zum Modul anzeigen. Dazu gehören beispielsweise ein Link zurück zum PowerShell-Katalog, erforderliche Abhängigkeiten und alle Cmdlets und/oder DSC-Ressourcen, die das Modul enthält.
   
    ![PowerShell-Moduldetails](media/automation-runbook-gallery/gallery-item-details-blade.png) <br>
7. Um das Modul direkt in Azure Automation zu installieren, klicken Sie auf die Schaltfläche **Importieren** .
   
    ![Schaltfläche „Modul importieren“](media/automation-runbook-gallery/module-import-button.png)
8. Wenn Sie auf die Schaltfläche **Importieren** klicken, wird der Name des Moduls angezeigt, das Sie importieren möchten. Wenn alle Abhängigkeiten installiert sind, ist die Schaltfläche **OK** aktiv. Falls Abhängigkeiten fehlen, müssen diese importiert werden, bevor das Modul importiert werden kann.
9. Klicken Sie auf **OK**, um das Modul zu importieren. Wenn Azure Automation ein Modul in Ihr Konto importiert, werden Metadaten zum Modul und den Cmdlets extrahiert.
   
    ![Importieren der Modul-Seite](media/automation-runbook-gallery/module-import-blade.png)
   
    Dies kann einige Minuten dauern, da jede Aktivität extrahiert werden muss.
10. Sie erhalten jeweils eine Benachrichtigung, wenn das Modul bereitgestellt wird und wenn der Vorgang abgeschlossen ist.
11. Nachdem das Modul importiert wurde, werden die verfügbaren Aktivitäten angezeigt, und Sie können die zugehörigen Ressourcen in Ihren Runbooks und in der Konfiguration für den gewünschten Zustand verwenden.

## <a name="requesting-a-runbook-or-module"></a>Anfordern eines Runbooks oder eines Moduls
Sie können Anforderungen an [User Voice](https://feedback.azure.com/forums/246290-azure-automation/)senden.  Wenn Sie Hilfe beim Schreiben eines Runbooks benötigen oder eine Frage zu PowerShell haben, stellen Sie eine entsprechende Anfrage in unserem [Forum](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Nächste Schritte
* Informationen zum Einstieg in die Arbeit mit Runbooks finden Sie unter [Erstellen oder Importieren eines Runbooks in Azure Automation](automation-creating-importing-runbook.md)
* Informationen zu den Unterschieden zwischen PowerShell- und PowerShell-Workflow bei der Arbeit mit Runbooks finden Sie unter [Grundlagen des PowerShell-Workflows](automation-powershell-workflow.md)

