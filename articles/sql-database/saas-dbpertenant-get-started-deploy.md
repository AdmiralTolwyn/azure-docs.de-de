---
title: "Tutorial für mehrinstanzenfähige SaaS-Anwendungen – Azure SQL-Datenbank | Microsoft-Dokumentation"
description: "Es wird beschrieben, wie Sie die mehrinstanzenfähige Wingtip Tickets-SaaS-Anwendung bereitstellen und kennenlernen, mit der Datenbanken pro Mandant und andere SaaS-Muster per Azure SQL-Datenbank dargestellt werden."
keywords: Tutorial zur SQL-Datenbank
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: sstein
ms.openlocfilehash: f91ddff81e51e7cc3d1561dc799013764530924b
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/28/2017
---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Bereitstellen und Untersuchen einer mehrinstanzenfähigen SaaS-Anwendung, die das Muster mit einer Datenbank pro Mandant mit Azure SQL-Datenbank verwendet

In diesem Tutorial stellen Sie die Anwendung Wingtip Tickets SaaS Database Per Tenant bereit und machen sich mit dieser vertraut. Die Anwendung verwendet ein Muster mit einer Datenbank pro Mandant, um die Daten für mehrere Mandanten zu speichern. Die Anwendung ist dafür ausgelegt, Features von Azure SQL-Datenbank zu veranschaulichen, mit denen die Aktivierung von SaaS-Szenarien vereinfacht wird.

Innerhalb von fünf Minuten, nachdem Sie unten auf die Schaltfläche *Bereitstellung in Azure* geklickt haben, verfügen Sie über eine funktionstüchtige mehrinstanzenfähige SaaS-Anwendung in der Cloud, die SQL-Datenbank verwendet. Die Anwendung wird mit drei Beispielmandanten mit jeweils einer eigenen Datenbank bereitgestellt. Diese werden alle in einem Pool für elastische SQL-Datenbanken angeordnet. Die App wird in Ihrem Azure-Abonnement bereitgestellt, wodurch Sie uneingeschränkten Zugriff erhalten und die einzelnen Anwendungskomponenten untersuchen und nutzen können. Der Quellcode der Anwendung und die Verwaltungsskripts sind im GitHub-Repository „WingtipTicketsSaaS-DbPerTenant“ verfügbar.


In diesem Tutorial lernen Sie Folgendes kennen:

> [!div class="checklist"]

> * Bereitstellen der Wingtip-SaaS-Anwendung
> * Abrufen des Quellcodes der Anwendung und der Verwaltungsskripts
> * Die Server, Pools und Datenbanken, aus denen sich die App zusammensetzt
> * Zuordnen von Mandanten zu den zugehörigen Daten mithilfe des *Katalogs*
> * Bereitstellen eines neuen Mandanten
> * Überwachen der Mandantenaktivität in der App

Zum Kennenlernen diverser SaaS-Entwurfs- und -Verwaltungsmuster ist eine [Reihe entsprechender Tutorials](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) verfügbar, die auf dieser Bereitstellung aufbauen. Machen Sie sich beim Durcharbeiten der Tutorials mit den bereitgestellten Skripts vertraut, und erfahren Sie, wie die verschiedenen SaaS-Muster implementiert werden. Arbeiten Sie die Skripts in jedem Tutorial schrittweise durch, um ein besseres Verständnis davon zu erlangen, wie Sie die vielen SQL-Datenbankfunktionen implementieren, die das Entwickeln von SaaS-Anwendungen erleichtern.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie zum Durchführen dieses Tutorials sicher, dass die folgenden Voraussetzungen erfüllt sind:

* Azure PowerShell ist installiert. Weitere Informationen hierzu finden Sie unter [Erste Schritte mit Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Bereitstellen des Wingtip Tickets SaaS-Anwendung

Stellen Sie die App bereit:

1. Wenn Sie auf die Schaltfläche **Bereitstellung in Azure** klicken, wird das Azure-Portal mit der Bereitstellungsvorlage für Wingtip Tickets SaaS Database Per Tenant geöffnet. Für die Vorlage sind zwei Parameterwerte erforderlich: ein Name für eine neue Ressourcengruppe und ein Benutzername, mit dem diese Bereitstellung von anderen Bereitstellungen der App Wingtip Tickets SaaS Database Per Tenant unterschieden wird. Im nächsten Schritt sind Details zum Festlegen dieser Werte angegeben.

   Notieren Sie sich auf jeden Fall die genauen Werte, die Sie verwenden, da Sie sie später in eine Konfigurationsdatei eingeben müssen.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Geben Sie die erforderlichen Parameterwerte für die Bereitstellung ein:

    > [!IMPORTANT]
    > Die Sicherheit einiger Authentifizierungs- und Serverfirewalls wurde zu Vorführungszwecken absichtlich aufgehoben. **Erstellen einer neuen Ressourcengruppe** Verwenden Sie keine vorhandenen Ressourcengruppen, Server oder Pools. Verwenden Sie diese Anwendung, die Skripts oder damit bereitgestellten Ressourcen nicht für die Produktion. Wenn Sie sich umfassend mit der Anwendung vertraut gemacht haben, löschen Sie diese Ressourcengruppe, um die zugehörige Abrechnung einzustellen.

    * **Ressourcengruppe:** Wählen Sie **Neu erstellen** aus, und geben Sie einen **Namen** für die Ressourcengruppe an. 
    * **Speicherort:** Wählen Sie in der Dropdownliste einen **Speicherort** aus.
    * **Benutzer**: Für einige Ressourcen sind Namen erforderlich, die global eindeutig sind. Geben Sie zur Sicherstellung der Eindeutigkeit bei jeder Bereitstellung der Anwendung einen Wert an, um die von Ihnen erstellten Ressourcen von den Ressourcen zu unterscheiden, die von anderen Bereitstellungen der Wingtip-Anwendung erstellt wurden. Es empfiehlt sich, einen kurzen **Benutzernamen** anzugeben, z.B. Ihre Initialen mit einer Ziffer (wie *af1*), und diesen dann im Namen der Ressourcengruppe zu verwenden (z.B. *wingtip-af1*). Der Parameter **User** darf nur Buchstaben, Zahlen und Bindestriche (keine Leerstellen) enthalten. Beim ersten und letzten Zeichen muss es sich um einen Buchstaben oder eine Ziffer handeln (es wird empfohlen, ausschließlich Kleinbuchstaben zu verwenden).


1. **Stellen Sie die Anwendung bereit**.

    * Klicken Sie auf die entsprechende Option, um den Geschäftsbedingungen zuzustimmen.
    * Klicken Sie auf **Kaufen**.

1. Überwachen Sie den Bereitstellungsstatus, indem Sie auf **Benachrichtigungen** klicken (das Glockensymbol rechts neben dem Suchfeld). Das Bereitstellen der Wingtip Tickets SaaS-App dauert ungefähr fünf Minuten.

   ![Bereitstellung erfolgreich](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Herunterladen und Entsperren der Verwaltungsskripts für Wingtip Tickets

Laden Sie den Quellcode und die Verwaltungsskripts herunter, während die Anwendung bereitgestellt wird.

> [!IMPORTANT]
> Ausführbare Inhalte (Skripts, DLLs) können durch Windows blockiert werden, wenn ZIP-Dateien aus einer externen Quelle heruntergeladen und extrahiert werden. Führen Sie bei der Extraktion der Skripts aus einer ZIP-Datei die nachfolgenden Schritte aus, um die Blockierung der ZIP-Datei vor der Extraktion aufzuheben. Hierdurch wird sichergestellt, dass die Skripts ausgeführt werden dürfen.

1. Navigieren Sie zum GitHub-Repository [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant).
1. Klicken Sie auf **Klonen oder herunterladen**.
1. Klicken Sie auf **ZIP herunterladen**, und speichern Sie die Datei.
1. Klicken Sie mit der rechten Maustaste auf die Datei **WingtipTicketsSaaS-DbPerTenant-master.zip**, und wählen Sie **Eigenschaften** aus.
1. Wählen Sie auf der Registerkarte **Allgemein** die Option **Unblock** (Zulassen), und klicken Sie auf **Übernehmen**.
1. Klicken Sie auf **OK**.
1. Extrahieren Sie die Dateien.

Skripts befinden sich im Ordner „*...\\WingtipTicketsSaaS-DbPerTenant-master\\Learning Modules*“.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Aktualisieren der Benutzerkonfigurationsdatei für diese Bereitstellung

Ändern Sie vor dem Ausführen von Skripts die Werte *resource group* und *user* in der Datei **UserConfig.psm1**. Legen Sie diese Variablen auf die Werte fest, die Sie während der Bereitstellung angegeben haben.

1. Öffnen Sie „...\\Learning Modules\\*UserConfig.psm1*“ in der *PowerShell ISE*. 
1. Aktualisieren Sie *ResourceGroupName* und *Name* mit den jeweiligen Werten für Ihre Bereitstellung (nur in den Zeilen 10 und 11).
1. Speichern Sie die Änderungen!

Auf diese Werte wird in fast jedem Skript verwiesen.

## <a name="run-the-application"></a>Ausführen der Anwendung

Diese App richtet sich an Veranstaltungsorte wie Konzerthallen, Jazzclubs, Sportstadien usw., in denen Veranstaltungen stattfinden. Die Veranstalter registrieren sich als Kunden (oder Mandanten) von Wingtip Tickets und verfügen damit über eine einfache Möglichkeit, Veranstaltungen anzukündigen und Tickets zu verkaufen. Jeder Veranstalter erhält eine personalisierte Website zum Verwalten und Auflisten seiner Veranstaltungen und zum Verkaufen von Tickets. Diese ist unabhängig und abgegrenzt von denen anderer Mandanten. Für jeden Mandanten wird im Hintergrund eine SQL-Datenbank in einem Pool für elastische SQL-Datenbanken bereitgestellt.

Auf einer zentralen **Event Hub**-Seite wird eine Liste mit Links zu den Mandanten in Ihrer Bereitstellung aufgeführt.

1. Öffnen Sie den _Event Hub_ in Ihrem Webbrowser: http://events.wingtip-dpt.&lt;BENUTZER&gt;.trafficmanager.net (ersetzen Sie &lt;BENUTZER&gt; durch den Benutzerwert Ihrer Bereitstellung):

    ![Events Hub](media/saas-dbpertenant-get-started-deploy/events-hub.png)

1. Klicken Sie im **Ereignis-Hub** auf *Fabrikam Jazz Club*.

   ![Ereignisse](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)


Zum Steuern der Verteilung eingehender Anforderungen nutzt die App [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md). Für die Ereignisseiten, die mandantenspezifisch sind, müssen Mandantennamen in die URLs eingeschlossen sein. Alle Mandanten-URLs enthalten Ihren speziellen Wert für *Benutzer* und weisen das folgende Format auf: http://events.wingtipp-dpt.&lt;BENUTZER&gt;.trafficmanager.net/*fabrikamjazzclub*. Die Veranstaltungs-App analysiert den Mandantennamen in der URL und erstellt damit einen Schlüssel für den Zugriff auf einen Katalog mithilfe der [*Shard-Zuordnungsverwaltung*](sql-database-elastic-scale-shard-map-management.md). Im Katalog wird der Schlüssel dem Datenbankspeicherort für den Mandanten zugeordnet. Der **Ereignis-Hub** ruft mit erweiterten Metadaten im Katalog den Namen des Mandanten ab, der der jeweiligen Datenbank zugeordnet ist, um die Liste mit URLs anzugeben.

In einer Produktionsumgebung würde typischerweise ein CNAME-DNS-Eintrag zum [*Zuordnen einer Unternehmensinternetdomäne*](../traffic-manager/traffic-manager-point-internet-domain.md) zum Traffic Manager-Profil erstellt werden.

## <a name="start-generating-load-on-the-tenant-databases"></a>Generieren von Last in den Mandantendatenbanken

Nachdem die App bereitgestellt wurde, können wir sie nutzen. Mit dem PowerShell-Skript *Demo-LoadGenerator* wird eine Workload gestartet, die für alle Mandantendatenbanken ausgeführt wird. Die Auslastung vieler echter SaaS-Apps ist häufig sporadischer Art und nicht vorhersagbar. Um diese Art von Auslastung zu simulieren, erzeugt der Generator eine auf alle Mandanten verteilte Auslastung, bei der es auf jedem Mandanten in zufälligen Intervallen zu zufälligen Auslastungsspitzen kommt. Aus diesem Grund dauert es mehrere Minuten, bis das Auslastungsmuster erreicht ist. Es ist also am besten, den Generator mindestens drei oder vier Minuten laufen zu lassen, bevor die Auslastung überwacht wird.

1. Öffnen Sie in der *PowerShell ISE* das Skript „...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1*“.
1. Drücken Sie **F5**, um das Skript auszuführen und den Last-Generator zu starten (übernehmen Sie zunächst die Standard-Parameterwerte).

> [!IMPORTANT]
> Öffnen Sie ein neues PowerShell ISE-Fenster, um andere Skripts auszuführen. Der Lastengenerator wird als eine Reihe von Einzelvorgängen in der lokalen PowerShell-Sitzung ausgeführt. Das Skript *Demo-LoadGenerator.ps1* löst das eigentliche Skript für den Auslastungsgenerator aus. Hierbei werden eine Vordergrundaufgabe sowie einige Hintergrundaufgaben zur Auslastungsgenerierung ausgeführt. Für jede Datenbank, die im Katalog registriert ist, wird ein Auftrag zur Auslastungsgenerierung aufgerufen. Die Aufträge werden in Ihrer lokalen PowerShell-Sitzung ausgeführt, sodass mit dem Schließen der PowerShell-Sitzung alle Aufträge beendet werden. Wenn Sie Ihren Computer anhalten, wird die Auslastungsgenerierung unterbrochen, und sie wird wieder aufgenommen, wenn Sie die Ausführung des Computers fortsetzen.

Nachdem der Auslastungsgenerator für jeden Mandanten die Aufträge zur Generierung der Auslastung aufgerufen hat, verbleibt die Vordergrundaufgabe in einem Zustand zum Aufrufen von Aufträgen. In diesem Zustand werden zusätzliche Hintergrundaufgaben für alle neuen Mandanten gestartet, die nachfolgend bereitgestellt werden. Sie können *STRG+C* drücken oder die Schaltfläche *Beenden* verwenden, um die Vordergrundaufgabe zu beenden, aber für vorhandene Hintergrundaufgaben wird die Generierung der Auslastung auf den einzelnen Datenbanken fortgesetzt. Wenn Sie die Hintergrundaufgaben überwachen und steuern müssen, können Sie *Get-Job*, *Receive-Job* und *Stop-Job* verwenden. Während der Ausführung der Vordergrundaufgabe können Sie dieselbe PowerShell-Sitzung nicht zum Ausführen von anderen Skripts verwenden. Öffnen Sie ein neues PowerShell ISE-Fenster, um andere Skripts auszuführen.

Wenn Sie die Auslastungsgenerator-Sitzung neu starten möchten, z.B. mit anderen Parametern, können Sie die Vordergrundaufgabe beenden und dann das Skript *Demo-LoadGenerator.ps1* erneut ausführen. Durch das erneute Ausführen von *Demo-LoadGenerator.ps1* werden zuerst alle derzeit ausgeführten Aufträge beendet, und anschließend wird der Generator neu gestartet. Hierdurch wird eine neue Gruppe von Aufträgen ausgelöst, für die die aktuellen Parameter verwendet werden.

Lassen Sie den Auslastungsgenerator vorerst im Zustand für das Aufrufen von Aufträgen laufen.


## <a name="provision-a-new-tenant"></a>Bereitstellen eines neuen Mandanten

Bei der anfänglichen Bereitstellung werden drei Beispielmandanten erstellt. Wir erstellen aber einen weiteren Mandanten, um zu ermitteln, welche Auswirkungen dies auf die bereitgestellte Anwendung hat. Der Wingtip Tickets SaaS-Workflow zum Bereitstellen von Mandanten wird im [Tutorial zur Bereitstellung und zum Katalog](saas-dbpertenant-provision-and-catalog.md) ausführlich beschrieben. In diesem Schritt erstellen Sie schnell einen neuen Mandanten.

1. Öffnen Sie „...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1*“ in der *PowerShell ISE*.
1. Drücken Sie **F5**, um das Skript auszuführen (lassen Sie die Standardwerte zunächst unverändert).

   > [!NOTE]
   > Für viele Wingtip SaaS-Skripts wird *$PSScriptRoot* für die Navigation in Ordnern zum Aufrufen von Funktionen in anderen Skripts verwendet. Diese Variable wird nur ausgewertet, wenn das vollständige Skript durch das Drücken von **F5** ausgeführt wird.  Das Markieren und Ausführen einer Auswahl (**F8**) kann zu Fehlern führen. Drücken Sie daher zum Ausführen von Skripts **F5**.

Die neue Mandantendatenbank wird in einem SQL-Pool für elastische Datenbanken erstellt, initialisiert und im Katalog registriert. Nach der erfolgreichen Bereitstellung wird die Website für *Veranstaltungen* des neuen Mandanten in Ihrem Browser angezeigt:

![Neuer Mandant](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Aktualisieren Sie den *Ereignis-Hub*. Der neue Mandant wird jetzt in der Liste aufgeführt.


## <a name="explore-the-servers-pools-and-tenant-databases"></a>Untersuchen der Server, Pools und Mandantendatenbanken

Nachdem Sie für die Sammlung der Mandanten jetzt die Ausführung einer Auslastung gestartet haben, sehen wir uns einige der bereitgestellten Ressourcen an:

1. Browsen Sie im [Azure-Portal](http://portal.azure.com) zu Ihrer Liste der SQL-Server, und öffnen Sie den Server **catalog-dpt-&lt;BENUTZER&gt;**. Der Katalogserver enthält die beiden Datenbanken **tenantcatalog** und **basetenantdb** (eine Datenbank, die kopiert wird, um neue Mandanten zu erstellen).

   ![Datenbanken](./media/saas-dbpertenant-get-started-deploy/databases.png)

1. Wechseln Sie zurück zu Ihrer Liste von SQL-Servern, und öffnen Sie den Server **tenants1-dpt-&lt;BENUTZER&gt;**, auf dem sich die Mandantendatenbanken befinden. Bei jeder Mandantendatenbank handelt es sich um eine _elastische standardmäßige_ Datenbank in einem Standardpool mit 50 eDTUs. Beachten Sie auch, dass eine Datenbank _Red Maple Racing_ vorhanden ist. Dies ist die zuvor von Ihnen bereitgestellte Mandantendatenbank.

   ![server](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Überwachen des Pools

Wenn der Last-Generator mehrere Minuten ausgeführt wurde, sollte eine ausreichende Datenmenge verfügbar sein, um einige der in Pools und Datenbanken integrierten Überwachungsfunktionen zu betrachten.

Navigieren Sie zum Server **tenants1-dpt-&lt;BENUTZER&gt;**, und klicken Sie auf **Pool1**, um die Ressourcenverwendung für den Pool anzuzeigen (Auslastungsgenerator wurde für die folgenden Diagramme eine Stunde lang ausgeführt):

   ![Überwachen des Pools](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

Das obere Diagramm zeigt die eDTU-Nutzung im Pool, während das untere Diagramm die eDTU-Nutzung der fünf am häufigsten verwendeten Datenbanken im Pool zeigt.  Diese beide Abbildungen veranschaulichen vorzüglich, wie gut sich Pools für elastische Datenbanken und SQL-Datenbank für Workloads von SaaS-Anwendungen eignen. Vier Datenbanken mit Bursts auf bis zu 40 eDTUs werden in einem 50 eDTU-Pool problemlos unterstützt. Wenn sie als eigenständige Datenbanken bereitgestellt wurden, muss es sich hierbei jeweils um eine S2-Datenbank (50 DTUs) handeln, damit Bursts unterstützt werden. Die Kosten für vier eigenständige S2-Datenbanken entsprechen fast dem Dreifachen des Preises für den Pool, und der Pool verfügt noch über viel Luft für viele weitere Datenbanken. In der Praxis betreiben SQL-Datenbank-Kunden derzeit bis zu 500 Datenbanken in Pools mit 200 eDTUs. Weitere Informationen finden Sie im [Tutorial zur Leistungsüberwachung](saas-dbpertenant-performance-monitoring.md).


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]

> * Bereitstellen der Wingtip Tickets SaaS-Anwendung
> * Die Server, Pools und Datenbanken, aus denen sich die App zusammensetzt
> * Mandanten werden den zugehörigen Daten mithilfe des *Katalogs* zugeordnet
> * Vorgehensweise beim Bereitstellen neuer Mandanten
> * Anzeigen der Poolnutzung zum Überwachen der Mandantenaktivität
> * Löschen von Beispielressourcen, um die zugehörige Abrechnung einzustellen

Arbeiten Sie nun das [Tutorial zum Bereitstellen und zum Katalog](saas-dbpertenant-provision-and-catalog.md) durch.



## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Weitere Tutorials, die auf der ersten Anwendung Wingtip Tickets SaaS Database Per Tenant aufbauen](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* Weitere Informationen zu Pools für elastische Datenbanken finden Sie unter [*Was ist ein Pool für elastische Azure SQL-Datenbanken*](sql-database-elastic-pool.md)
* Weitere Informationen zu elastischen Aufträgen finden Sie unter [*Verwalten horizontal hochskalierter Clouddatenbanken*](sql-database-elastic-jobs-overview.md)
* Weitere Informationen zu mehrinstanzenfähigen SaaS-Anwendungen finden Sie unter [*Entwurfsmuster für mehrinstanzenfähige SaaS-Anwendungen*](saas-tenancy-app-design-patterns.md)
