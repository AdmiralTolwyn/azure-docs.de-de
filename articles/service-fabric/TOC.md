# [Service Fabric-Dokumentation](/azure/service-fabric)
# Übersicht
## [Was ist Service Fabric?](service-fabric-overview.md)

# Schnellstarts
## [Erstellen einer .NET-Anwendung](service-fabric-quickstart-dotnet.md)
## [Bereitstellen einer Linux-Containeranwendung](service-fabric-quickstart-containers-linux.md)
## [Bereitstellen einer Windows-Containeranwendung](service-fabric-quickstart-containers.md)

# Lernprogramme
## Bereitstellen einer .NET-App
### [1 – Erstellen einer .NET-Anwendung](service-fabric-tutorial-create-dotnet-app.md)
### [2 – Bereitstellen der Anwendung](service-fabric-tutorial-deploy-app-to-party-cluster.md)
### [3 – Konfigurieren der von CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
### [4 – Überwachen und Diagnostizieren](service-fabric-tutorial-monitoring-aspnet.md)

## Packen einer vorhandenen .NET-App in Container
### [1 – Erstellen eines sicheren Clusters in Azure](service-fabric-tutorial-create-cluster-azure-ps.md)
### [2 – Bereitstellen einer .NET-App mit Docker Compose](service-fabric-host-app-in-a-container.md)
### [3 – Monitor your container](service-fabric-tutorial-monitoring-wincontainers.md) (3 – Überwachen Ihres Containers)

## Erstellen einer Linux-Container-App
### [1 – Erstellen von Containerimages](service-fabric-tutorial-create-container-images.md)
### [2 – Package and deploy containers](service-fabric-tutorial-package-containers.md) (2 – Packen und Bereitstellen von Containern)
### [3- Fail over and scale](service-fabric-tutorial-containers-failover.md) (3 – Failover und Skalieren)

## Erstellen und Verwalten eines Clusters
### 1 – Erstellen eines Clusters in Azure
#### [1a – Erstellen eines sicheren Windows-Clusters in Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
#### [1b – Erstellen eines sicheren Linux-Clusters in Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
### [2 – Deploy API Management with Service Fabric](service-fabric-tutorial-deploy-api-management.md) (2 – Bereitstellen von API Management mit Service Fabric)

# Beispiele
## [Codebeispiele](https://azure.microsoft.com/resources/samples/?service=service-fabric)
## [Azure PowerShell](service-fabric-powershell-samples.md)
## [Service Fabric-Befehlszeilenschnittstelle](samples-cli.md)

# Konzepte
## [Grundlegendes zu Microservices](service-fabric-overview-microservices.md)
## [Übersicht](service-fabric-content-roadmap.md)
## [Anwendungsszenarien](service-fabric-application-scenarios.md)
## [Muster und Szenarien](service-fabric-patterns-and-scenarios.md)
## [Architektur](service-fabric-architecture.md)
## [Terminologie](service-fabric-technical-overview.md)

## Erstellen von Anwendungen und Diensten
### Unterstützte Programmiermodelle
#### [Übersicht](service-fabric-choose-framework.md)
#### Container
##### [Übersicht](service-fabric-containers-overview.md)
##### [Docker Compose (Vorschau)](service-fabric-docker-compose.md)
##### [Ressourcenkontrolle](service-fabric-resource-governance.md)
#### Reliable Services
##### [Übersicht](service-fabric-reliable-services-introduction.md)
##### [Reliable Services-Lebenszyklus – C#](service-fabric-reliable-services-lifecycle.md)
##### [Reliable Services-Lebenszyklus – Java](service-fabric-reliable-services-lifecycle-java.md)
##### [Zuverlässige Auflistungen](service-fabric-reliable-services-reliable-collections.md)
##### [Zuverlässige Auflistung – Richtlinien und Empfehlungen](service-fabric-reliable-services-reliable-collections-guidelines.md)
##### [Arbeiten mit Reliable Collections](service-fabric-work-with-reliable-collections.md)
##### [Transaktionen und Sperren](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
##### [Reliable Concurrent Queue](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [Reliable Collection-Serialisierung](service-fabric-reliable-services-reliable-collections-serialization.md)
##### [Reliable State Manager und Reliable Collection – ausführliche Informationen](service-fabric-reliable-services-reliable-collections-internals.md)
##### [Erweiterte Verwendung](service-fabric-reliable-services-advanced-usage.md)

#### Reliable Actors
##### [Übersicht](service-fabric-reliable-actors-introduction.md)
##### [Architektur](service-fabric-reliable-actors-platform.md)
##### [Lebenszyklus und Garbage Collection](service-fabric-reliable-actors-lifecycle.md)
##### [Zustandsverwaltung](service-fabric-reliable-actors-state-management.md)
##### [Polymorphie](service-fabric-reliable-actors-polymorphism.md)
##### [Eintrittsinvarianz](service-fabric-reliable-actors-reentrancy.md)
##### [Typserialisierung](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

### [Anwendungsmodell](service-fabric-application-model.md)
### [Hostingmodell](service-fabric-hosting-model.md)

### Dienste
#### [Dienstressourcen](service-fabric-service-manifest-resources.md)
#### [Dienststatus](service-fabric-concepts-state.md)
#### [Dienstpartitionierung](service-fabric-concepts-partitioning.md)
#### [Verfügbarkeit von Diensten](service-fabric-availability-services.md)
#### [Replicas and instances](service-fabric-concepts-replica-lifecycle.md) (Replikate und Instanzen)
#### [Reconfiguration](service-fabric-concepts-reconfiguration.md) (Neukonfiguration)
#### Dienstkommunikation
##### [Übersicht](service-fabric-connect-and-communicate-with-services.md)
##### [DNS-Dienst](service-fabric-dnsservice.md)
##### [Reverseproxy](service-fabric-reverseproxy.md)
##### [Konfigurieren eines Reverseproxys für eine sichere Kommunikation](service-fabric-reverseproxy-configure-secure-communication.md)
##### [Reverseproxydiagnose](service-fabric-reverse-proxy-diagnostics.md)
### [Skalierbarkeit von Anwendungen](service-fabric-concepts-scalability.md)
### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### [Planen der Anwendungskapazität](service-fabric-capacity-planning.md)

## Verwalten von Anwendungen
### [Übersicht](service-fabric-application-lifecycle.md)
### [Die ImageStoreConnectionString-Einstellung](service-fabric-image-store-connection-string.md)
### Anwendungsupgrade
#### [Übersicht](service-fabric-application-upgrade.md)
#### [Konfiguration](service-fabric-visualstudio-configure-upgrade.md)
#### [Parameter für Anwendungsupgrades](service-fabric-application-upgrade-parameters.md)
#### [Datenserialisierung bei Anwendungsupgrades](service-fabric-application-upgrade-data-serialization.md)
#### [Weiterführende Themen zu Anwendungsupgrades](service-fabric-application-upgrade-advanced.md)
### [Übersicht über die Fehleranalyse](service-fabric-testability-overview.md)

## Erstellen und Verwalten von Clustern
### [Übersicht](service-fabric-deploy-anywhere.md)
### Planen und Vorbereiten
#### [Kapazitätsplanung](service-fabric-cluster-capacity.md)
#### [Notfallwiederherstellung](service-fabric-disaster-recovery.md)
### [Beschreiben eines Clusters](service-fabric-cluster-resource-manager-cluster-description.md)
### [Clustersicherheit](service-fabric-cluster-security.md)
### [Funktionsunterschiede zwischen Linux und Windows](service-fabric-linux-windows-differences.md)
### Cluster in Azure
#### [Knotentypen und VM-Skalierungsgruppen](service-fabric-cluster-nodetypes.md)
#### [Clusternetzwerkmuster](service-fabric-patterns-networking.md)
### Clusterressourcen-Manager
#### [Übersicht](service-fabric-cluster-resource-manager-introduction.md)
#### [Architektur](service-fabric-cluster-resource-manager-architecture.md)
#### [Beschreiben eines Clusters](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Übersicht über Anwendungsgruppen](service-fabric-cluster-resource-manager-application-groups.md)
#### [Konfigurieren der Einstellungen des Clusterressourcen-Managers](service-fabric-cluster-resource-manager-configure-services.md)
#### [Metriken zum Ressourcenverbrauch](service-fabric-cluster-resource-manager-metrics.md)
#### [Nutzen der Dienstaffinität](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
#### [Richtlinien zur Dienstplatzierung](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
#### [Verwalten eines Clusters](service-fabric-cluster-resource-manager-management-integration.md)
#### [Clusterdefragmentierung](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
#### [Ausbalancieren eines Clusters](service-fabric-cluster-resource-manager-balancing.md)
#### [Drosselung](service-fabric-cluster-resource-manager-advanced-throttling.md)
#### [Dienstverschiebung](service-fabric-cluster-resource-manager-movement-cost.md)

## [Integration in API Management](service-fabric-api-management-overview.md)

## Überwachen und Diagnostizieren
### [Übersicht](service-fabric-diagnostics-overview.md)
### [Integritätsmodell](service-fabric-health-introduction.md)
### [Diagnose für zustandsbehaftete Reliable Services](service-fabric-reliable-services-diagnostics.md)
### [Diagnose in Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
### [Leistungsindikatoren für Reliable Services-Remoting](service-fabric-reliable-serviceremoting-diagnostics.md)

# Anleitungen
## Einrichten der Entwicklungsumgebung
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)

## [Einrichten der Service Fabric-Befehlszeilenschnittstelle](service-fabric-cli.md)

## Erstellen einer Anwendung
### [Erstellen Ihrer ersten C#-App in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
### Erstellen eines Diensts mit ausführbarer Gastdatei
#### [Hosten einer Node.js-Anwendung unter Windows](quickstart-guest-app.md)
#### [Bereitstellen einer ausführbaren Gastanwendungsdatei](service-fabric-deploy-existing-app.md)
#### [Bereitstellen mehrerer ausführbarer Gastanwendungsdateien](service-fabric-deploy-multiple-apps.md)
### Erstellen eines Containerdiensts
#### [Erstellen einer Windows-Containeranwendung](service-fabric-get-started-containers.md)
#### [Erstellen einer Linux-Containeranwendung](service-fabric-get-started-containers-linux.md)
#### [Containersicherheit](service-fabric-securing-containers.md)
#### [Docker Compose (Vorschau)](service-fabric-docker-compose.md)
#### [Ressourcenkontrolle für Container und Dienste](service-fabric-resource-governance.md)
#### [Volume- und Protokollierungstreiber](service-fabric-containers-volume-logging-drivers.md)
#### [Dienste innerhalb von Containern](service-fabric-services-inside-containers.md)
#### [Containernetzwerkmodi](service-fabric-networking-modes.md)

### Erstellen eines Reliable Services-Diensts
#### [Übersicht](service-fabric-reliable-services-introduction.md)
#### Konzepte
##### [Reliable Services-Lebenszyklus – C#](service-fabric-reliable-services-lifecycle.md)
##### [Reliable Services-Lebenszyklus – Java](service-fabric-reliable-services-lifecycle-java.md)

#### Zuverlässige Auflistungen
##### [Zuverlässige Auflistungen](service-fabric-reliable-services-reliable-collections.md)
##### [Zuverlässige Auflistung – Richtlinien und Empfehlungen](service-fabric-reliable-services-reliable-collections-guidelines.md)
##### [Arbeiten mit Reliable Collections](service-fabric-work-with-reliable-collections.md)
##### [Transaktionen und Sperren](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
##### [Reliable Concurrent Queue](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [Reliable Collection-Serialisierung](service-fabric-reliable-services-reliable-collections-serialization.md)
##### [Reliable State Manager und Reliable Collection – ausführliche Informationen](service-fabric-reliable-services-reliable-collections-internals.md)

#### Erste Schritte
##### [C# unter Windows](service-fabric-reliable-services-quick-start.md)
##### [Java unter Linux](service-fabric-reliable-services-quick-start-java.md)
##### [Erstellen einer C#-Anwendung unter Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
#### Kommunizieren mit Diensten
##### [Kommunizieren mit Reliable Services](service-fabric-reliable-services-communication.md)

##### [Dienstremoting – C#](service-fabric-reliable-services-communication-remoting.md)
##### [Dienstremoting – Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [Sichere Kommunikation – C#](service-fabric-reliable-services-secure-communication.md)
##### [Sichere Kommunikation – Java](service-fabric-reliable-services-secure-communication-java.md)

#### [Konfigurieren](service-fabric-reliable-services-configuration.md)
#### [Senden von Benachrichtigungen](service-fabric-reliable-services-notifications.md)
#### [Sichern und Wiederherstellen](service-fabric-reliable-services-backup-restore.md)

### Erstellen eines Reliable Actors-Diensts
#### Erste Schritte
##### [C# unter Windows](service-fabric-reliable-actors-get-started.md)
##### [Java unter Linux](service-fabric-reliable-actors-get-started-java.md)
##### [Java-Akteur unter Linux](service-fabric-create-your-first-linux-application-with-java.md)
#### [Senden von Benachrichtigungen](service-fabric-reliable-actors-events.md)
#### [Festlegen von Timern und Erinnerungen](service-fabric-reliable-actors-timers-reminders.md)
#### [Konfigurieren von „KvsActorStateProvider“](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Konfigurieren von Kommunikationseinstellungen](service-fabric-reliable-actors-fabrictransportsettings.md)
#### [Konfigurieren von „ReliableDictionaryActorStateProvider“](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

### [Migrieren alter Java-Anwendungen für die Unterstützung von Maven](service-fabric-migrate-old-javaapp-to-use-maven.md)

### [Konfigurieren eines Reverseproxys für eine sichere Kommunikation](service-fabric-reverseproxy-configure-secure-communication.md)

### [Erstellen eines ASP.NET Core-Diensts](service-fabric-add-a-web-frontend.md)

### Konfigurieren der Sicherheit
#### [Verwalten von Anwendungsgeheimnissen](service-fabric-application-secret-management.md)  
#### [Konfigurieren von Sicherheitsrichtlinien für Ihre Anwendung](service-fabric-application-runas-security.md)

## Arbeiten in einer Windows-Entwicklungsumgebung
### [Verwalten von Anwendungen in Visual Studio](service-fabric-manage-application-in-visual-studio.md)
### [Konfigurieren sicherer Verbindungen in Visual Studio](service-fabric-visualstudio-configure-secure-connections.md)
### [Konfigurieren Ihrer Anwendung für mehrere Umgebungen](service-fabric-manage-multiple-environment-app-configuration.md)
### [Debuggen eines .NET-Diensts in VS](service-fabric-debugging-your-application.md)
### [Allgemeine Fehler und Ausnahmen](service-fabric-errors-and-exceptions.md)
### [Lokales Überwachen und Diagnostizieren](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## Arbeiten in einer Linux-Entwicklungsumgebung
### [Erste Schritte mit dem Eclipse-Plug-In für die Entwicklung mit Java](service-fabric-get-started-eclipse.md)
### [Debuggen eines Java-Diensts in Eclipse](service-fabric-debugging-your-application-java.md)
### [Lokales Überwachen und Diagnostizieren](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

## [Bereitstellen von API Management und Service Fabric in Azure](service-fabric-api-management-quick-start.md)

## Migrieren aus Cloud Services
### [Unterschiede zwischen Cloud Services und Service Fabric](service-fabric-cloud-services-migration-differences.md)
### [Migrieren zu Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
### [Empfohlene Vorgehensweisen](/azure/architecture/service-fabric/migrate-from-cloud-services)

## Verwalten des Anwendungslebenszyklus
### [Packen einer Anwendung](service-fabric-package-apps.md)

### Bereitstellen oder Entfernen von Anwendungen
#### [Bereitstellen von Anwendungen in einem lokalen Cluster](service-fabric-get-started-with-a-local-cluster.md)
#### [Azure PowerShell](service-fabric-deploy-remove-applications.md)
#### [Service Fabric-Befehlszeilenschnittstelle](service-fabric-application-lifecycle-sfctl.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
#### [FabricClient-APIs](service-fabric-deploy-remove-applications-fabricclient.md)

### Upgrade von Anwendungen
#### [Upgrade mithilfe von Azure PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
#### [Upgrade mithilfe von Visual Studio](service-fabric-application-upgrade-tutorial.md)
#### [Problembehandlung bei Anwendungsupgrades](service-fabric-application-upgrade-troubleshooting.md)

### Testen von Anwendungen und Diensten
#### [Testen der Kommunikation zwischen Diensten](service-fabric-testability-scenarios-service-communication.md)
#### Simulieren von Ausfällen
##### [Verwenden des kontrollierten Chaos](service-fabric-controlled-chaos.md)
##### [Verwenden von Testaktionen](service-fabric-testability-actions.md)
##### [Bei Ausführung von Workloads](service-fabric-testability-workload-tests.md)
##### [Verwenden von Testszenarien](service-fabric-testability-scenarios.md)
##### [Verwenden der Knotenübergang-APIs](service-fabric-node-transition-apis.md)
#### [Durchführen eines Auslastungstests für Ihre Anwendung](service-fabric-vso-load-test.md)

### Einrichten von Continuous Integration
#### [Einrichten von Continuous Integration mit VSTS](service-fabric-set-up-continuous-integration.md)
#### [Bereitstellen einer Linux-Java-Anwendung mithilfe von Jenkins](service-fabric-cicd-your-linux-java-application-with-jenkins.md)

## Erstellen und Verwalten von Clustern
### Cluster in Azure
#### Erstellen
##### [Erstellen Ihres ersten Clusters in Azure](service-fabric-get-started-azure-cluster.md)
##### [Azure-Portal](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
#### Skalieren
##### [Manuell](service-fabric-cluster-scale-up-down.md)
##### [Programmgesteuert](service-fabric-cluster-programmatic-scaling.md)
#### [Upgrade](service-fabric-cluster-upgrade.md)
#### [Festlegen der Zugriffssteuerung](service-fabric-cluster-security-roles.md)
#### [Konfigurieren](service-fabric-cluster-fabric-settings.md)
#### [Öffnen eines Ports im Lastenausgleichsmodul](create-load-balancer-rule.md)
#### [Verwalten von Clusterzertifikaten](service-fabric-cluster-security-update-certs-azure.md)
#### [Löschen](service-fabric-cluster-delete.md)

### Eigenständige Cluster
#### [Planen und Vorbereiten der Bereitstellung](service-fabric-cluster-standalone-deployment-preparation.md)
#### Erstellen
##### [Erstellen Ihres ersten eigenständigen Clusters](service-fabric-get-started-standalone-cluster.md)
##### [Lokales Erstellen](service-fabric-cluster-creation-for-windows-server.md)
##### [Schützen mithilfe von Zertifikaten](service-fabric-windows-cluster-x509-security.md)  
##### [Schützen mithilfe der Windows-Sicherheit](service-fabric-windows-cluster-windows-security.md)
##### [Inhalt des eigenständigen Pakets](service-fabric-cluster-standalone-package-contents.md)
#### [Skalieren](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Festlegen der Zugriffssteuerung](service-fabric-cluster-security-roles.md)
#### [Konfigurieren](service-fabric-cluster-manifest.md)
#### [Upgrade](service-fabric-cluster-upgrade-windows-server.md)

### [Visualisieren eines Clusters](service-fabric-visualizing-your-cluster.md)
### [Herstellen einer Verbindung mit einem sicheren Cluster](service-fabric-connect-to-secure-cluster.md)
### [Patchen von Clusterknoten](service-fabric-patch-orchestration-application.md)

### Verwalten und Orchestrieren von Clusterressourcen
#### [Übersicht über den Clusterressourcen-Manager](service-fabric-cluster-resource-manager-introduction.md)
#### [Architektur des Clusterressourcen-Managers](service-fabric-cluster-resource-manager-architecture.md)
#### [Beschreiben eines Clusters](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Übersicht über Anwendungsgruppen](service-fabric-cluster-resource-manager-application-groups.md)
#### [Konfigurieren der Einstellungen des Clusterressourcen-Managers](service-fabric-cluster-resource-manager-configure-services.md)
#### [Metriken zum Ressourcenverbrauch](service-fabric-cluster-resource-manager-metrics.md)
#### [Nutzen der Dienstaffinität](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
#### [Richtlinien zur Dienstplatzierung](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
#### [Verwalten eines Clusters](service-fabric-cluster-resource-manager-management-integration.md)
#### [Clusterdefragmentierung](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
#### [Ausbalancieren eines Clusters](service-fabric-cluster-resource-manager-balancing.md)
#### [Drosselung](service-fabric-cluster-resource-manager-advanced-throttling.md)
#### [Dienstverschiebung](service-fabric-cluster-resource-manager-movement-cost.md)

## Überwachen und Diagnostizieren
### [Überwachen und Diagnostizieren von Anwendungen](service-fabric-diagnostics-overview.md)
### Generieren von Ereignissen
#### [Generieren von Ereignissen auf Plattformebene](service-fabric-diagnostics-event-generation-infra.md)
##### [Betriebskanal](service-fabric-diagnostics-event-generation-operational.md)
##### [Reliable Services-Ereignisse](service-fabric-reliable-services-diagnostics.md)
##### [Reliable Actors-Ereignisse](service-fabric-reliable-actors-diagnostics.md)
##### [Leistungsmetriken](service-fabric-diagnostics-event-generation-perf.md)
#### [Generieren von Ereignissen auf Anwendungsebene](service-fabric-diagnostics-event-generation-app.md)
### Untersuchen der Anwendungs- und Clusterintegrität
#### [Überwachen der Service Fabric-Integrität](service-fabric-health-introduction.md)
#### [Melden und Überprüfen der Dienstintegrität](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
#### [Hinzufügen von benutzerdefinierten Berichten zur Integrität](service-fabric-report-health.md)
#### [Behandeln von Problemen anhand von Berichten zur Systemintegrität](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
#### [Anzeigen von Integritätsberichten](service-fabric-view-entities-aggregated-health.md)
#### [Überwachen von Windows Server-Containern](service-fabric-diagnostics-containers-windowsserver.md)
### Aggregieren von Ereignissen
#### [Aggregieren von Ereignissen mit EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)
#### Aggregieren von Ereignissen mit der Azure-Diagnose
##### [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
##### [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
### Analysieren von Ereignissen
#### [Analysieren von Ereignissen mit Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
#### [Analysieren von Ereignissen mit OMS](service-fabric-diagnostics-event-analysis-oms.md)
### [Behandeln von Problemen mit dem lokalen Cluster](service-fabric-troubleshoot-local-cluster-setup.md)

# Referenz
## [Azure PowerShell](/powershell/module/azurerm.servicefabric/)
## [PowerShell](/powershell/module/servicefabric/?view=azureservicefabricps)
## [Azure-CLI](/cli/azure/sf)
## [Service Fabric-Befehlszeilenschnittstelle](service-fabric-sfctl.md)
### [sfctl application](service-fabric-sfctl-application.md)
### [sfctl chaos](service-fabric-sfctl-chaos.md)
### [sfctl cluster](service-fabric-sfctl-cluster.md)
### [sfctl compose](service-fabric-sfctl-compose.md)
### [sfctl is](service-fabric-sfctl-is.md)
### [sfctl node](service-fabric-sfctl-node.md)
### [sfctl partition](service-fabric-sfctl-partition.md)
### [sfctl replica](service-fabric-sfctl-replica.md)
### [sfctl rpm](service-fabric-sfctl-rpm.md)
### [sfctl service](service-fabric-sfctl-service.md)
### [sfctl store](service-fabric-sfctl-store.md)
## [Java-API](/java/api/overview/azure/servicefabric)
## [.NET](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet)
## [REST](/rest/api/servicefabric)

# Ressourcen
## [Azure-Roadmap](https://azure.microsoft.com/roadmap/)
## [Häufig gestellte Fragen](service-fabric-common-questions.md)
## [Lernpfad](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [MSDN-Forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [Preise](https://azure.microsoft.com/pricing/details/service-fabric/)
## [Preisrechner](https://azure.microsoft.com/pricing/calculator/)
## [Beispielcode](http://aka.ms/servicefabricsamples)
## [Supportoptionen](service-fabric-support.md)
## [Dienstupdates](https://azure.microsoft.com/updates/?product=service-fabric)
## [Videos](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)
