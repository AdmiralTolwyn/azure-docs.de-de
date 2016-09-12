<properties
   pageTitle="Anwendungslebenszyklus in Service Fabric | Microsoft Azure"
   description="Beschreibt das Entwickeln, Bereitstellen, Testen, Aktualisieren, Verwalten und Entfernen von Service Fabric-Anwendungen."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>


<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>


# Lebenszyklus der Service Fabric-Anwendung
Ähnlich wie auf anderen Plattformen durchläuft eine Anwendung auf Azure Service Fabric normalerweise die folgenden Phasen: Entwurf, Entwicklung, Test, Bereitstellung, Update, Wartung und Deinstallation. Service Fabric bietet erstklassige Unterstützung für den gesamten Anwendungslebenszyklus von Cloudanwendungen: von der Entwicklung über die Bereitstellung, die tägliche Verwaltung und die Wartung bis zur endgültigen Außerbetriebnahme. Das Dienstmodell ermöglicht die unabhängige Beteiligung verschiedener Rollen am Anwendungslebenszyklus. Dieser Artikel bietet eine Übersicht über die APIs und wie sie von den verschiedenen Rollen während der Phasen des Service Fabric-Anwendungslebenszyklus verwendet werden.

## Rollen des Dienstmodells
Es gibt folgende Rollen im Dienstmodell:

- **Dienstentwickler**: Entwickelt modulare und generische Dienste, die für neue Zwecke in mehreren Anwendungen des gleichen Typs oder in anderen Anwendungstypen wiederverwendet werden können. Ein Warteschlangendienst kann beispielsweise zum Erstellen einer Ticketing-Anwendung (Helpdesk) oder einer E-Commerce-Anwendung (Warenkorb) verwendet werden.

- **Anwendungsentwickler**: Erstellt Anwendungen durch Integrieren einer Sammlung von Diensten, um bestimmte Anforderungen zu erfüllen oder bestimmte Szenarien abzudecken. Beispielsweise können „Zustandsloser Front-End-Dienst für JSON“, „Zustandsbehafteter Dienst für Auktion“ und „Zustandsbehafteter Dienst für Warteschlange“ in einer E-Commerce-Website integriert werden, um eine Auktionslösung zu erstellen.

- **Anwendungsadministrator**: Trifft Entscheidungen zur Anwendungskonfiguration (Ausfüllen der Konfigurationsvorlagenparameter), zur Bereitstellung (Zuordnung zu den verfügbaren Ressourcen) und zur Servicequalität. Ein Anwendungsadministrator legt z. B. das Sprachgebietsschema (Englisch für die USA oder Japanisch für Japan) der Anwendung fest. Für eine andere bereitgestellte Anwendung können andere Einstellungen festgelegt werden.

- **Operator**: Stellt Anwendungen entsprechend der Anwendungskonfiguration und den vom Anwendungsadministrator angegebenen Anforderungen bereit. Ein Operator stellt z. B. die Anwendung bereit und stellt sicher, dass sie in Azure ausgeführt wird. Operatoren überwachen den Anwendungszustand und die Leistungsinformationen und verwalten bei Bedarf die physische Infrastruktur.


## Entwickeln
1. Ein *Dienstentwickler* entwickelt unter Verwendung des Programmiermodells [Reliable Actors](service-fabric-reliable-actors-introduction.md) oder [Reliable Services](service-fabric-reliable-services-introduction.md) verschiedene Diensttypen.
2. Ein *Dienstentwickler* beschreibt die entwickelten Diensttypen deklarativ in einer Dienstmanifestdatei, die aus einem oder mehreren Code-, Konfigurations- und Datenpaketen besteht.
3. Ein *Anwendungsentwickler* erstellt dann mit verschiedenen Diensttypen eine Anwendung.
4. Der *Anwendungsentwickler* beschreibt den Anwendungstyp deklarativ in einem Anwendungsmanifest, indem er auf die Dienstmanifeste der zugehörigen Dienste verweist und verschiedene Konfigurations- und Bereitstellungseinstellungen der zugehörigen Dienste entsprechend überschreibt und parametrisiert.

Beispiele finden Sie unter [Get started with Reliable Actors](service-fabric-reliable-actors-get-started.md) und [Get started with Reliable Services](service-fabric-reliable-services-quick-start.md) (beide in englischer Sprache).

## Bereitstellen
1. Ein *Anwendungsadministrator* passt den Anwendungstyp in einer bestimmten Anwendung so an, dass sie in einem Service Fabric-Cluster bereitgestellt wird, indem er die entsprechenden Parameter des **ApplicationType**-Elements im Anwendungsmanifest angibt.

2. Ein *Operator* lädt das Anwendungspaket mit der [**CopyApplicationPackage**-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) oder dem [**Copy-ServiceFabricApplicationPackage**-Cmdlet](https://msdn.microsoft.com/library/azure/mt125905.aspx) in den ImageStore-Cluster hoch. Das Anwendungspaket enthält das Anwendungsmanifest und die Auflistung der Dienstpakete. Service Fabric stellt Anwendungen aus dem Anwendungspaket bereit, das im ImageStore gespeichert ist, bei dem es sich um einen Azure-Blobspeicher oder den Service Fabric-Systemdienst handeln kann.

3. Der *Operator* stellt dann mit der [**ProvisionApplicationAsync**-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), dem [**Register-ServiceFabricApplicationType**-Cmdlet](https://msdn.microsoft.com/library/azure/mt125958.aspx) oder dem [REST-Vorgang **Anwendung bereitstellen**](https://msdn.microsoft.com/library/azure/dn707672.aspx) den Anwendungstyp aus dem hochgeladenen Anwendungspaket im Zielcluster bereit.

4. Nach der Bereitstellung der Anwendung startet ein *Operator* die Anwendung mit den Parametern, die unter Verwendung der [**CreateApplicationAsync**-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync.aspx), des [**New-ServiceFabricApplication**-Cmdlets](https://msdn.microsoft.com/library/azure/mt125913.aspx) oder des [REST-Vorgangs **Anwendung erstellen**](https://msdn.microsoft.com/library/azure/dn707676.aspx) vom *Anwendungsadministrator* angegeben wurden.

5. Nachdem die Anwendung bereitgestellt wurde, erstellt ein *Operator* mit der [**CreateServiceAsync**-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.createserviceasync.aspx), dem [**New-ServiceFabricService**-Cmdlet](https://msdn.microsoft.com/library/azure/mt125874.aspx) oder dem [REST-Vorgang **Dienst erstellen**](https://msdn.microsoft.com/library/azure/dn707657.aspx) auf Grundlage der verfügbaren Diensttypen neue Dienstinstanzen für die Anwendung.

6. Die Anwendung wird nun im Service Fabric-Cluster ausgeführt.

Beispiele finden Sie unter [Deploy an application](service-fabric-deploy-remove-applications.md) (in englischer Sprache).

## Test
1. Nach der Bereitstellung im lokalen Entwicklungscluster oder in einem Testcluster führt ein *Dienstentwickler* das integrierte Failovertestszenario mithilfe der Klassen [**FailoverTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenarioparameters.aspx) und [**FailoverTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenario.aspx) oder des [**Invoke-ServiceFabricFailoverTestScenario**-Cmdlets](https://msdn.microsoft.com/library/azure/mt644783.aspx) aus. Im Failovertestszenario wird ein bestimmter Dienst über wichtige Übergänge und Failover ausgeführt, um sicherzustellen, dass er weiterhin verfügbar und aktiv ist.

2. Der *Dienstentwickler* führt dann das integrierte Chaostestszenario mithilfe der Klassen [**ChaosTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenarioparameters.aspx) und [**ChaosTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenario.aspx) oder des [**Invoke-ServiceFabricChaosTestScenario**-Cmdlets](https://msdn.microsoft.com/library/azure/mt644774.aspx) aus. Im Chaostestszenario werden nach dem Zufallsprinzip mehrere Knoten-, Codepaket- und Replikatfehler im Cluster ausgelöst.

3. Der *Dienstentwickler* [testet die Dienst-zu-Dienst-Kommunikation](service-fabric-testability-scenarios-service-communication.md) durch das Erstellen von Testszenarien, die primäre Replikate im Cluster verschieben.

Weitere Informationen finden Sie unter [Testability – Übersicht](service-fabric-testability-overview.md).

## Upgrade
1. Ein *Dienstentwickler* aktualisiert die zugehörigen Dienste der instanziierten Anwendung und/oder behebt Fehler und stellt eine neue Version des Dienstmanifests bereit.

2. Ein *Anwendungsentwickler* überschreibt und parametrisiert die Konfigurations- und Bereitstellungseinstellungen der zugehörigen Dienste und stellt eine neue Version des Anwendungsmanifests bereit. Der Anwendungsentwickler bindet dann die neuen Versionen der Dienstmanifeste in der Anwendung ein und stellt eine neue Version des Anwendungstyps in einem aktualisierten Anwendungspaket bereit.

3. Ein *Anwendungsadministrator* bindet durch Aktualisieren der entsprechenden Parameter die neue Version des Anwendungstyps in der Zielanwendung ein.

5. Ein *Operator* lädt das aktualisierte Anwendungspaket mit der [**CopyApplicationPackage**-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) oder dem [**Copy-ServiceFabricApplicationPackage**-Cmdlet](https://msdn.microsoft.com/library/azure/mt125905.aspx) in den ImageStore-Cluster hoch. Das Anwendungspaket enthält das Anwendungsmanifest und die Auflistung der Dienstpakete.

6. Ein *Operator* stellt die neue Version der Anwendung mit der [**ProvisionApplicationAsync**-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), dem [**Register-ServiceFabricApplicationType**-Cmdlet](https://msdn.microsoft.com/library/azure/mt125958.aspx) oder dem [REST-Vorgang **Anwendung bereitstellen**](https://msdn.microsoft.com/library/azure/dn707672.aspx) im Zielcluster bereit.

7. Ein *Operator* führt mit der [**UpgradeApplicationAsync**-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.upgradeapplicationasync.aspx), dem [Cmdlet **Start ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125975.aspx) oder dem [REST-Vorgang **Upgrade einer Anwendung ausführen**](https://msdn.microsoft.com/library/azure/dn707633.aspx) ein Upgrade der Zielanwendung auf die neue Version aus.

8. Ein *Operator* überprüft den Status des Upgrades mithilfe der [**GetApplicationUpgradeProgressAsync**-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.getapplicationupgradeprogressasync.aspx), des [Cmdlets **Get-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125988.aspx) oder des [REST-Vorgangs **Fortschritt des Anwendungsupgrades abrufen**](https://msdn.microsoft.com/library/azure/dn707631.aspx).

9. Bei Bedarf ändert der *Operator* die Parameter des aktuellen Anwendungsupgrades mit der [**UpdateApplicationUpgradeAsync**-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.updateapplicationupgradeasync.aspx), dem [Cmdlet **Update-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt126030.aspx) oder dem [REST-Vorgang **Anwendungsupgrade aktualisieren**](https://msdn.microsoft.com/library/azure/mt628489.aspx) und wendet sie erneut an.

10. Bei Bedarf setzt der *Operator* das aktuelle Anwendungsupgrade mit der [**RollbackApplicationUpgradeAsync**-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.rollbackapplicationupgradeasync.aspx), dem [Cmdlet **Start-ServiceFabricApplicationRollback**](https://msdn.microsoft.com/library/azure/mt125833.aspx) oder dem [REST-Vorgang **Anwendungsupgrade zurücksetzen**](https://msdn.microsoft.com/library/azure/mt628494.aspx) zurück.

11. Service Fabric aktualisiert die im Cluster ausgeführte Zielanwendung ohne Verlust der Verfügbarkeit der einzelnen zugehörigen Dienste.

Beispiele finden Sie im [Tutorial für Anwendungsupgrades](service-fabric-application-upgrade-tutorial.md).

## Verwalten
1. Bei Upgrades und Patches des Betriebssystems wird Service Fabric mit der Azure-Infrastruktur verbunden, um die Verfügbarkeit aller im Cluster ausgeführten Anwendungen zu gewährleisten.

2. Bei Upgrades und Patches der Service Fabric-Plattform aktualisiert sich Service Fabric selbst ohne Verlust der Verfügbarkeit aller im Cluster ausgeführten Anwendungen.

3. Ein *Anwendungsadministrator* genehmigt das Hinzufügen oder Entfernen von Knoten in einem Cluster nach der Analyse der verlaufsbezogenen Kapazität der Verwendungsdaten und des voraussichtlichen zukünftigen Bedarfs.

4. Ein *Operator* fügt die vom *Anwendungsadministrator* angegebenen Knoten hinzu oder entfernt sie.

5. Wenn neue Knoten im Cluster hinzugefügt oder vorhandene Knoten aus dem Cluster entfernt werden, nimmt Service Fabric einen Lastenausgleich der in allen Knoten des Clusters ausgeführten Anwendungen vor, um eine optimale Leistung zu erzielen.

## Spalten
1. Ein *Operator* kann mithilfe der [**DeleteServiceAsync**-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync.aspx), des [Cmdlets **Remove-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt126033.aspx) oder des [REST-Vorgangs **Dienst löschen**](https://msdn.microsoft.com/library/azure/dn707687.aspx) eine bestimmte Instanz eines ausgeführten Diensts im Cluster löschen, ohne die gesamte Anwendung zu entfernen.

2. Ein *Operator* kann mithilfe der [**DeleteApplicationAsync**-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync.aspx), des [Cmdlets **Remove-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125914.aspx) oder des [REST-Vorgangs **Anwendung löschen**](https://msdn.microsoft.com/library/azure/dn707651.aspx) auch eine Anwendungsinstanz und alle ihre Dienste löschen.

3. Wenn die Anwendung und die Dienste beendet wurden, kann der *Operator* die Bereitstellung des Anwendungstyps mithilfe der [**UnprovisionApplicationAsync**-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync.aspx), des [Cmdlets **Unregister-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125885.aspx) oder des [REST-Vorgangs **Bereitstellung einer Anwendung aufheben**](https://msdn.microsoft.com/library/azure/dn707671.aspx) aufheben. Beim Aufheben der Bereitstellung des Anwendungstyps wird das Anwendungspaket nicht aus dem ImageStore entfernt. Sie müssen das Anwendungspaket manuell entfernen.

4. Ein *Operator* entfernt das Anwendungspaket mit der [**RemoveApplicationPackage**-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage.aspx) oder dem [Cmdlet **Remove-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt163532.aspx) aus dem ImageStore.

Beispiele finden Sie unter [Deploy an application](service-fabric-deploy-remove-applications.md) (in englischer Sprache).

## Nächste Schritte

Weitere Informationen zum Entwickeln, Testen und Verwalten von Service Fabric-Anwendungen und Service Fabric-Diensten:

- [Reliable Actors](service-fabric-reliable-actors-introduction.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)
- [Bereitstellen von Anwendungen](service-fabric-deploy-remove-applications.md)
- [Anwendungsupgrade](service-fabric-application-upgrade.md)
- [Testability – Übersicht](service-fabric-testability-overview.md)
- [REST-basierter Anwendungslebenszyklus – Beispiel](service-fabric-rest-based-application-lifecycle-sample.md)

<!---HONumber=AcomDC_0831_2016-->