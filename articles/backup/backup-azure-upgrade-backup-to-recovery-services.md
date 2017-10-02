---
title: "Durchführen eines Upgrades für einen Sicherungstresor auf einen Recovery Services-Tresor (Vorschau) | Microsoft-Dokumentation"
description: "Befehle und Supportinformationen zur Durchführung eines Upgrades für Ihren Sicherungstresor in Azure auf einen Recovery Services-Tresor."
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
ms.assetid: 228fef19-2f6b-4067-acc3-fb6e501afb88
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/03/2017
ms.author: sogup;markgal;arunak
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 28e8b0be0b69f279685109d611fbfb369b96101a
ms.contentlocale: de-de
ms.lasthandoff: 09/20/2017

---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>Durchführen eines Upgrades für einen Sicherungstresor auf einen Recovery Services-Tresor

In diesem Artikel wird beschrieben, wie ein Upgrade für einen Sicherungstresor auf einen Recovery Services-Tresor durchgeführt wird. Der Upgradevorgang hat keinerlei Auswirkungen auf derzeit ausgeführte Sicherungsaufträge. Zudem gehen keine Sicherungsdaten verloren. Für die Durchführung eines Upgrades für einen Sicherungstresor auf einen Recovery Services-Tresor gibt es folgende primäre Gründe:
- Alle Funktionen eines Sicherungstresors werden in einem Recovery Services-Tresor beibehalten.
- Recovery Services-Tresore weisen mehr Funktionen als Sicherungstresore auf, z.B. höhere Sicherheit, integrierte Überwachung, schnellere Wiederherstellungen und Wiederherstellungen auf Elementebene.
- Verwalten Sie Sicherungselemente über ein verbessertes, vereinfachtes Portal.
- Neue Funktionen gelten nur für Recovery Services-Tresore.

## <a name="impact-to-operations-during-upgrade"></a>Auswirkungen auf Vorgänge während des Upgrades

Die Durchführung eines Upgrades für einen Sicherungstresor auf einen Recovery Services-Tresor hat keine Auswirkungen auf Ihre Vorgänge in der Datenebene. Alle Sicherungsaufträge werden weiterhin wie gewohnt ausgeführt, und alle aktiven Wiederherstellungsaufträge werden ohne Unterbrechungen fortgesetzt. Während des Upgrades kommt es zu einer kurzen Ausfallzeit bei Verwaltungsvorgängen. Zudem können Sie neue Elemente nicht schützen oder Ad-hoc-Sicherungsaufträge erstellen. Während des Upgrades werden keine Wiederherstellungsaufträge für IaaS-VMs ausgeführt. Das Tresorupgrade dauert maximal eine Stunde. Anschließend wird der Sicherungstresor durch einen Recovery Services-Tresor ersetzt.

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>Änderungen an der Automatisierung und an den Tools nach dem Upgrade

Bei der Vorbereitung Ihrer Infrastruktur auf das Tresorupgrade müssen Sie Ihre vorhandene Automatisierung bzw. Ihre vorhandenen Tools aktualisieren, um sicherzustellen, dass diese nach dem Upgrade weiterhin funktionieren.
Ziehen Sie hierzu die Referenzen zu PowerShell-Cmdlets für das [Service Manager-Bereitstellungsmodell](backup-client-automation-classic.md) und das [Resource Manager-Bereitstellungsmodell](backup-client-automation.md) zu Rate.


## <a name="before-you-upgrade"></a>Vor dem Upgrade

Überprüfen Sie vor dem Upgrade Ihrer Sicherungstresore auf Recovery Service-Tresore folgende Punkte.

- **Minimale Agent-Version:** Stellen Sie für das Upgrade Ihres Tresors sicher, dass der MARS-Agent (Microsoft Azure Recovery Services) mindestens die Version 2.0.9083.0 aufweist. Wenn der MARS-Agent älter ist als 2.0.9083.0, aktualisieren Sie den Agent, bevor Sie den Upgradevorgang starten.
- **Instanzbasiertes Abrechnungsmodell**: Recovery Service-Tresore unterstützen nur das instanzbasierte Abrechnungsmodell. Wenn Sie einen Sicherungstresor mit dem veralteten speicherbasierten Abrechnungsmodell verwenden, konvertieren Sie das Abrechnungsmodell beim Upgrade.
- **Keine Konfigurationsvorgänge für laufende Sicherungen**: Während des Upgrades ist der Zugriff auf die Verwaltungsebene eingeschränkt. Schließen Sie alle Aktionen auf Verwaltungsebene ab, und starten Sie dann das Upgrade.

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>Durchführen von Upgrades für Ihre Tresore mithilfe von PowerShell-Skripts

Mithilfe von PowerShell-Skripts können Sie ein Upgrade für Ihre Sicherungstresore auf Recovery Services-Tresore durchführen. Stellen Sie sicher, dass Sie über die erforderlichen PowerShell-Komponenten zum Auslösen des Tresorupgrades verfügen. PowerShell-Skripts für Sicherungstresore funktionieren nicht bei Recovery Services-Tresoren. So bereiten Sie Ihre Umgebung auf das Upgrade der Tresore vor:

1. Installieren Sie [Windows Management Framework (WMF) Version 5](https://www.microsoft.com/download/details.aspx?id=50395) oder höher, oder führen Sie ein Upgrade durch.
2. [Installieren von Azure PowerShell MSI](https://github.com/Azure/azure-powershell/releases/download/v3.8.0-April2017/azure-powershell.3.8.0.msi).
3. Laden Sie für das Upgrade Ihrer Tresore das [PowerShell-Skript](https://aka.ms/vaultupgradescript2) herunter.

### <a name="run-the-powershell-script"></a>Ausführen des PowerShell-Skripts

Mithilfe des folgenden Skripts können Sie ein Upgrade für Ihre Tresore durchführen. Das folgende Beispielskript enthält Erläuterungen zu den Parametern.

RecoveryServicesVaultUpgrade-1.0.2.ps1 **-SubscriptionID** `<subscriptionID>` **-VaultName** `<vaultname>` **-Location** `<location>` **-ResourceType** `BackupVault` **-TargetResourceGroupName** `<rgname>`

**SubscriptionID**: Die Abonnement-ID-Nummer des Tresors, für den das Upgrade durchgeführt wird.<br/>
**VaultName**: Der Name des Sicherungstresors, für den das Upgrade durchgeführt wird.<br/>
**Location**: Der Speicherort des Tresors, für den das Upgrade durchgeführt wird.<br/>
**ResourceType**: Enthält „BackupVault“.<br/>
**TargetResourceGroupName**: Legen Sie eine Ressourcengruppe fest, da Sie für den Tresor ein Upgrade auf eine Resource Manager-basierte Bereitstellung durchführen. Sie können eine vorhandene Ressourcengruppe verwenden oder eine erstellen, indem Sie einen neuen Namen angeben. Wenn Sie den Namen einer Ressourcengruppe falsch schreiben, können Sie eine neue Ressourcengruppe erstellen. Weitere Informationen zu Ressourcengruppen finden Sie in der [Übersicht über Ressourcengruppen](../azure-resource-manager/resource-group-overview.md#resource-groups).

>[!NOTE]
> Für Namen von Ressourcengruppen gelten Beschränkungen. Befolgen Sie die nachstehenden Anweisungen. Anderenfalls könnten Tresorupgrades fehlschlagen.
>
>Kunden der **Azure US-Regierung** müssen die Umgebung während der Ausführung des Skripts auf „AzureUSGovernment“ festlegen.

Der folgende Codeausschnitt ist ein Beispiel dafür, wie Ihr PowerShell-Befehl aussehen könnte:

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

Sie können das Skript auch ohne Parameter ausführen. In diesem Fall werden Sie aufgefordert, Eingaben für alle erforderlichen Parameter vorzunehmen.

Das PowerShell-Skript fordert Sie zur Eingabe Ihrer Anmeldeinformationen auf. Geben Sie zweimal Ihre Anmeldeinformationen ein: einmal für das Service Manager-Konto und ein zweites Mal für das Resource Manager-Konto.

### <a name="pre-requisites-checking"></a>Überprüfung der Voraussetzungen
Wenn Sie Ihre Azure-Anmeldeinformationen eingegeben haben, überprüft Azure, ob Ihre Umgebung die folgenden Voraussetzungen erfüllt:

- **Minimale Agent-Version:** Für Upgrades von Sicherungstresoren auf Recovery Services-Tresore muss der MARS-Agent mindestens die Version 2.0.9083.0 aufweisen. Wenn Elemente bei einem Sicherungstresor mit einem Agent registriert sind, der eine ältere Version als 2.0.9083.0 aufweist, führt die Überprüfung der Voraussetzungen zu einem Fehler. Wenn die Überprüfung der Voraussetzungen fehlschlägt, aktualisieren Sie den Agent, und wiederholen Sie das Upgrade des Tresors. Die neueste Agent-Version kann unter folgendem Link heruntergeladen werden: [http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe](http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).
- **Laufende Konfigurationsaufträge**: Wenn ein Benutzer einen Auftrag für einen Sicherungstresor konfiguriert, für den ein Upgrade durchgeführt werden soll, oder ein Element registriert, schlägt die Überprüfung der Voraussetzungen fehl. Schließen Sie die Konfiguration ab, oder stellen Sie die Registrierung des Elements fertig, und starten Sie dann den Vorgang für das Tresorupgrade.
- **Speicherbasiertes Abrechnungsmodell**: Recovery Services-Tresore unterstützen das instanzbasierte Abrechnungsmodell. Wenn Sie das Tresorupgrade in einem Sicherungstresor durchführen, der das speicherbasierte Abrechnungsmodell verwendet, werden Sie aufgefordert, ein Upgrade für Ihr Abrechnungsmodell zusammen mit dem Tresor durchzuführen. Anderenfalls können Sie zuerst Ihr Abrechnungsmodell aktualisieren, und anschließend das Tresorupgrade durchführen.
- Identifizieren Sie eine Ressourcengruppe für den Recovery Services-Tresor. Um von den Resource Manager-Bereitstellungsfunktionen profitieren zu können, müssen Sie einen Recovery Services-Tresor in eine Ressourcengruppe einfügen. Wenn Sie nicht wissen, welche Ressourcengruppe Sie verwenden sollen, geben Sie einen Namen an. Die Ressourcengruppe wird dann automatisch beim Upgradevorgang für Sie erstellt. Beim Upgradevorgang wird der Tresor auch mit der neuen Ressourcengruppe verknüpft.

Nachdem die Voraussetzungen während des Upgradevorgangs überprüft wurden, werden Sie aufgefordert, das Tresorupgrade zu starten. Nachdem Sie dies bestätigt haben, dauert der Upgradevorgang je nach Größe Ihres Tresors in der Regel ca. 15-20 Minuten. Bei einem großen Tresor kann das Upgrade bis zu 90 Minuten dauern.

## <a name="managing-your-recovery-services-vaults"></a>Verwalten von Recovery Services-Tresoren

Auf den folgenden Bildschirmen wird ein neuer Recovery Services-Tresor angezeigt, für den ein Upgrade vom Sicherungstresor im Azure-Portal durchgeführt wurde. Auf dem ersten Bildschirm wird das Tresordashboard mit den Hauptentitäten für den Tresor angezeigt.

![Beispiel für einen Recovery Services-Tresor, für den ein Upgrade von einem Sicherungstresor durchgeführt wurde](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

Auf dem zweiten Bildschirm werden verfügbare Hilfelinks angezeigt, die Sie beim Einstieg in die Verwendung des Recovery Services-Tresors unterstützen sollen.

![Hilfelinks auf dem Blatt „Schnellstart“](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Schritte nach dem Upgrade
Der Recovery Services-Tresor unterstützt das Angeben von Zeitzoneninformationen in den Sicherungsrichtlinien. Nachdem der Tresor erfolgreich aktualisiert wurde, rufen Sie die Sicherungsrichtlinien im Menü „Tresoreinstellungen“ auf, und aktualisieren Sie die Zeitzoneninformationen für jede Richtlinie, die im Tresor konfiguriert wurde. Dieser Bildschirm zeigt bereits die Zeit des Sicherungszeitplans gemäß der Ortszeit an, die verwendet wurde, als Sie die Richtlinie erstellt haben. 

## <a name="enhanced-security"></a>Erweiterte Sicherheit

Wenn ein Sicherungstresor auf einen Recovery Services-Tresor aktualisiert wird, werden die Sicherheitseinstellungen für diesen Tresor automatisch aktiviert. Wenn die Sicherheitseinstellungen eingeschaltet sind, erfordern bestimmte Vorgänge wie z.B. das Löschen von Sicherungen oder das Ändern einer Passphrase einen [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)-PIN. Weitere Informationen zur erhöhten Sicherheit finden Sie im Artikel [Sicherheitsfeatures für den Schutz von Hybridsicherungen](backup-azure-security-feature.md). 

Wenn die erhöhte Sicherheit aktiviert ist, werden Daten bis zu 14 Tage beibehalten, nachdem die Wiederherstellungspunktinformationen aus dem Tresor gelöscht wurden. Den Kunden wird das Speichern dieser Sicherheitsdaten in Rechnung gestellt. Die Beibehaltung der Sicherheitsdaten gilt für Wiederherstellungspunkt, die für den Azure Backup-Agent, den Azure Backup Server und den System Center Data Protection-Manager aufgestellt wurden. 

## <a name="gather-data-on-your-vault"></a>Sammeln von Daten in Ihrem Tresor

Konfigurieren Sie nach einem Upgrade auf einen Recovery Services-Tresor Berichte für die Azure-Sicherung (für IaaS-VMs und Microsoft Azure Recovery Services (MARS)), und verwenden Sie Power BI, um auf die Berichte zuzugreifen. Weitere Informationen zum Sammeln von Daten finden Sie im Artikel [Konfigurieren von Azure Backup-Berichten](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**Wirkt sich der Upgradeplan auf meine laufenden Sicherungen aus?**</br>
Nein. Laufende Sicherungen werden während und nach dem Upgrade ohne Unterbrechung durchgeführt.

**Was geschieht mit meinen Tresoren, wenn ich kein Upgrade durchführe?**</br>
Da alle neuen Funktionen nur für Recovery Services-Tresore gelten, wird dringend empfohlen, Ihre Tresore zu aktualisieren. Microsoft wird nach und nach das klassische Azure-Portal einstellen. Ab dem 1. September 2017 wird von Microsoft automatisch ein Upgrade von Sicherungstresoren auf Recovery Services-Tresore gestartet. Nach dem 1. November 2017 wird Microsoft den Upgradevorgang abschließen. Der Tresor kann jederzeit zwischen September und Oktober automatisch aktualisiert werden. Es empfiehlt sich, dass Sie Ihren Tresor so bald wie möglich upgraden.

**Was bedeutet dieser Upgradeplan für meine vorhandenen Tools?**</br>
Updaten Sie auf das Resource Manager-Bereitstellungsmodell. Recovery Services-Tresore wurden für die Verwendung im Resource Manager-Bereitstellungsmodell erstellt. Die Planung für das Resource Manager-Bereitstellungsmodell und das Berücksichtigen der Unterschiede in Ihren Tresoren ist dabei wichtig. 

**Kommt es während des Upgrades zu hohen Ausfallzeiten?**</br>
Dies hängt von der Anzahl der Ressourcen ab, für die ein Upgrade durchgeführt werden soll. Bei kleineren Bereitstellungen (einigen Dutzend geschützten Instanzen) nimmt das gesamte Upgrade voraussichtlich maximal 20 Minuten in Anspruch. Bei größeren Bereitstellungen kann es bis zu eine Stunde dauern.

**Kann ich nach dem Upgrade ein Rollback ausführen?**</br>
Nein. Ein Rollback wird nicht unterstützt, nachdem erfolgreich ein Upgrade für die Ressourcen durchgeführt wurde.

**Kann ich mein Abonnement oder meine Ressourcen überprüfen, um zu ermitteln, ob sie für das Upgrade geeignet sind?**</br>
Ja. Der erste Schritt beim Upgrade ist die Prüfung der Ressourcen hinsichtlich ihrer Eignung für das Upgrade. Falls bei der Überprüfung der Voraussetzungen ein Fehler auftritt, erhalten Sie Meldungen über alle Gründe, aus denen das Upgrade nicht durchgeführt werden kann.

**Über welche Berechtigungen muss ich zum Auslösen des Tresorupgrades verfügen?**</br>
Um ein Tresorupgrade durchführen zu können, müssen Sie im klassischen Azure-Portal als Co-Administrator für das Abonnement hinzugefügt werden. Dies ist auch erforderlich, wenn Sie im Azure-Portal bereits als Besitzer hinzugefügt wurden. Versuchen Sie, einen Co-Administrator für das Abonnement im klassischen Azure-Portal hinzuzufügen, um herauszufinden, ob Sie Co-Administrator für das Abonnement sind. Wenn Sie keinen Co-Administrator hinzufügen können, wenden Sie sich an einen Dienstadministrator oder Co-Administrator für das Abonnement, um sich als Co-Administrator hinzufügen zu lassen.

**Kann ich ein Upgrade für meinen CSP-basierten Sicherungstresor durchführen?**</br>
Nr. Derzeit kann kein Upgrade für CSP-basierte Sicherungstresore durchgeführt werden. In den nächsten Versionen wird eine Unterstützung für das Upgrade von CSP-basierten Sicherungstresoren verfügbar sein.

**Kann ich meinen klassischen Tresor nach dem Upgrade anzeigen?**</br>
Nein. Sie können den klassischen Tresor nach dem Upgrade weder anzeigen noch verwalten. Sie können lediglich das neue Azure-Portal für alle Verwaltungsaktionen im Tresor verwenden.

**Mein Upgrade ist fehlgeschlagen, aber der Computer, der den Agent enthielt, der die Aktualisierung verlangt, ist nicht mehr vorhanden. Wie gehe ich in einem solchen Fall vor?**</br>
Wenn Sie die Sicherungen von diesem Computer langfristig aufbewahren müssen, können Sie kein Upgrade des Tresors durchführen. In zukünftigen Versionen werden wir Unterstützung für das Upgrade eines solchen Tresors hinzufügen.
Wenn Sie die Sicherungen dieses Computers nicht mehr speichern müssen, dann heben Sie die Registrierung für diesen Computer beim Tresor auf, und versuchen Sie dann erneut, das Upgrade durchzuführen.

**Warum kann ich die Auftragsinformationen für meine Ressourcen nach dem Upgrade nicht finden?**</br>
Die Überwachung für Sicherungen (MARS-Agent und IaaS) ist ein neues Feature, von dem Sie profitieren, wenn Sie Ihren Sicherungstresor auf den Recovery Services-Tresor upgraden. Es dauert 12 Stunden, bis die Überwachungsinformationen mit dem Dienst synchronisiert werden.

**Wie melde ich ein Problem?**</br>
Wenn irgendein Teil des Tresorupgrades fehlschlägt, beachten Sie die in der Fehlermeldung aufgeführte OperationID. Der Microsoft-Support wird proaktiv daran arbeiten, das Problem zu beheben. Bei Fragen oder Vorschlägen, senden Sie eine E-Mail an rsvaultupgrade@service.microsoft.com, unter Angabe Ihrer Abonnement-ID, des Tresornamens und der Vorgangs-ID. Es wird versucht, das Problem so schnell wie möglich zu beheben. Wiederholen Sie den Vorgang nicht, es sei denn, Sie werden von Microsoft explizit dazu aufgefordert.


## <a name="next-steps"></a>Nächste Schritte
In den folgenden Artikeln erhalten Sie weitere Informationen:</br>
[Sichern einer IaaS-VM](backup-azure-arm-vms-prepare.md)</br>
[Sichern von Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Sichern von Windows Server](backup-configure-vault.md).

