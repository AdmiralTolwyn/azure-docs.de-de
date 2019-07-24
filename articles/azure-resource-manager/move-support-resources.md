---
title: Unterstützung des Verschiebevorgangs je nach Azure-Ressourcentyp
description: Eine Liste von Azure-Ressourcentypen, die in eine neue Ressourcengruppe oder ein neues Abonnement verschoben werden können.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 7/9/2019
ms.author: tomfitz
ms.openlocfilehash: 22493ad7998e9014c88c79e6ac5eee3bf1216119
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226813"
---
# <a name="move-operation-support-for-resources"></a>Unterstützung des Verschiebevorgangs für Ressourcen
In diesem Artikel wird aufgeführt, für welche Azure-Ressourcentypen der Verschiebevorgang unterstützt wird. Außerdem finden Sie hier Informationen zu speziellen Bedingungen, die beim Verschieben einer Ressource berücksichtigt werden müssen.

Navigieren Sie direkt zu einem Ressourcenanbieter-Namespace:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Backup](#microsoftbackup)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [microsoft.dns](#microsoftdns)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domainservices | Nein | Nein |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| tenants | Nein | Nein |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| actionRules | Ja | Ja |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servers | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| service | Ja | Ja |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| configurationstores | Ja | Ja |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| apiapps | Nein | Nein |
| appidentities | Nein | Nein |
| gateways | Nein | Nein |

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for App Service resources](./move-limitations/app-service-move-limitations.md) (Anleitung zum Verschieben von App Service-Ressourcen).

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| policyassignments | Nein | Nein |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| automationaccounts | Ja | Ja |
| automationaccounts/configurations | Ja | Ja |
| automationaccounts/runbooks | Ja | Ja |

> [!IMPORTANT]
> Runbooks müssen sich in der gleichen Ressourcengruppe befinden wie das Automation-Konto.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| b2cdirectories | Ja | Ja |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| registrations | Ja | Ja |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| backupvault | Nein | Nein |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| batchaccounts | Ja | Ja |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| clusters | Nein | Nein |
| fileservers | Nein | Nein |
| jobs | Nein | Nein |
| workspaces | Nein | Nein |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| mapapis | Nein | Nein |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| biztalk | Ja | Ja |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| blockchainmembers | Ja | Ja |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| blueprintassignments | Nein | Nein |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| botservices | Ja | Ja |

## <a name="microsoftcache"></a>Microsoft.Cache
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| redis | Ja | Ja |

> [!IMPORTANT]
> Wenn die Azure Cache for Redis-Instanz mit einem virtuellen Netzwerk konfiguriert ist, kann die Instanz nicht in ein anderes Abonnement verschoben werden. Weitere Informationen finden Sie unter [Move guidance for virtual networks](./move-limitations/virtual-network-move-limitations.md) (Anleitung zum Verschieben virtueller Netzwerke).

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Ressourcentyp | Resource group | Abonnement |
| ------------- | ----------- | ---------- |
| profiles | JA | Ja |
| profiles/endpoints | Ja | Ja |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| certificateorders | Ja | Ja |

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for App Service resources](./move-limitations/app-service-move-limitations.md) (Anleitung zum Verschieben von App Service-Ressourcen).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domainnames | Ja | Nein |
| virtualmachines | Ja | Nein |

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for Classic deployment model resources](./move-limitations/classic-model-move-limitations.md) (Anleitung zum Verschieben von Ressourcen des klassischen Bereitstellungsmodells). Klassische Bereitstellungsressourcen können mithilfe eines szenariospezifischen Vorgangs zwischen Abonnements verschoben werden.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Nein | Nein |
| reservedips | Nein | Nein |
| virtualnetworks | Nein | Nein |

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for Classic deployment model resources](./move-limitations/classic-model-move-limitations.md) (Anleitung zum Verschieben von Ressourcen des klassischen Bereitstellungsmodells). Klassische Bereitstellungsressourcen können mithilfe eines szenariospezifischen Vorgangs zwischen Abonnements verschoben werden.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storageaccounts | Ja | Nein |

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for Classic deployment model resources](./move-limitations/classic-model-move-limitations.md) (Anleitung zum Verschieben von Ressourcen des klassischen Bereitstellungsmodells). Klassische Bereitstellungsressourcen können mithilfe eines szenariospezifischen Vorgangs zwischen Abonnements verschoben werden.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Ja | Ja |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| availabilitysets | Ja | Ja |
| disks | Ja | Ja |
| galleries | Nein | Nein |
| galleries/images | Nein | Nein |
| galleries/images/versions | Nein | Nein |
| hostgroups | Nein | Nein |
| hostgroups/hosts | Nein | Nein |
| images | Ja | Ja |
| proximityplacementgroups | Nein | Nein |
| restorepointcollections | Nein | Nein |
| sharedvmimages | Nein | Nein |
| sharedvmimages/versions | Nein | Nein |
| snapshots | Ja | Ja |
| virtualmachines | Ja | Ja |
| virtualmachines/extensions | Ja | Ja |
| virtualmachinescalesets | Ja | Ja |

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for virtual machines](./move-limitations/virtual-machines-move-limitations.md) (Anleitung zum Verschieben virtueller Computer).

## <a name="microsoftcontainer"></a>Microsoft.Container
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containergroups | Nein | Nein |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containergroups | Nein | Nein |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| registries | Ja | Ja |
| registries/buildtasks | Ja | Ja |
| registries/replications | Ja | Ja |
| registries/tasks | Ja | Ja |
| registries/webhooks | Ja | Ja |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containerservices | Nein | Nein |
| managedclusters | Nein | Nein |
| openshiftmanagedclusters | Nein | Nein |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Ressourcentyp | Resource group | Abonnement |
| ------------- | ----------- | ---------- |
| applications | JA | Ja |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Nein | Nein |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Ressourcentyp | Resource group | Abonnement |
| ------------- | ----------- | ---------- |
| connectors | JA | Ja |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hubs | Ja | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| jobs | Nein | Nein |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| databoxedgedevices | Nein | Nein |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspaces | Nein | Nein |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| catalogs | Ja | Ja |
| datacatalogs | Nein | Nein |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| connectionmanagers | Nein | Nein |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| packages | Nein | Nein |
| plans | Nein | Nein |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datafactories | Ja | Ja |
| factories | Ja | Ja |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datalakeaccounts | Nein | Nein |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Ja | Ja |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Ja | Ja |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Nein | Nein |
| services/projects | Nein | Nein |
| slots | Nein | Nein |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servers | Ja | Ja |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servers | Ja | Ja |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servergroups | Nein | Nein |
| servers | Ja | Ja |
| serversv2 | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| artifactsources | Ja | Ja |
| rollouts | Ja | Ja |
| servicetopologies | Ja | Ja |
| servicetopologies/services | Ja | Ja |
| servicetopologies/services/serviceunits | Ja | Ja |
| steps | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| elasticpools | Nein | Nein |
| elasticpools/iothubtenants | Nein | Nein |
| iothubs | Ja | Ja |
| provisioningservices | Ja | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Controller | Nein | Nein |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| labcenters | Nein | Nein |
| labs | Ja | Nein |
| labs/environments | Ja | Ja |
| labs/servicerunners | Ja | Ja |
| labs/virtualmachines | Ja | Nein |
| schedules | Ja | Ja |

## <a name="microsoftdns"></a>microsoft.dns
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dnszones | Nein | Nein |
| dnszones/a | Nein | Nein |
| dnszones/aaaa | Nein | Nein |
| dnszones/cname | Nein | Nein |
| dnszones/mx | Nein | Nein |
| dnszones/ptr | Nein | Nein |
| dnszones/srv | Nein | Nein |
| dnszones/txt | Nein | Nein |
| trafficmanagerprofiles | Nein | Nein |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| databaseaccounts | Ja | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domains | Ja | Ja |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Ja | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domains | Ja | Ja |
| topics | Ja | Ja |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| clusters | Ja | Ja |
| namespaces | Ja | Ja |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Nein | Nein |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hanainstances | Ja | Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| clusters | Ja | Ja |

> [!IMPORTANT]
> Sie können HDInsight-Cluster in ein neues Abonnement oder eine neue Ressourcengruppe verschieben. Dagegen können die mit dem HDInsight-Cluster verknüpften Netzwerkressourcen (z.B. virtuelles Netzwerk, NIC oder Load Balancer) nicht zwischen Abonnements verschoben werden. Darüber hinaus kann eine NIC, die an einen virtuellen Computer für den Cluster angefügt ist, nicht in eine neue Ressourcengruppe verschoben werden.
>
> Beim Verschieben eines HDInsight-Clusters in ein neues Abonnement sollten Sie zunächst andere Ressourcen (z.B. das Speicherkonto) verschieben. Verschieben Sie erst anschließend den HDInsight-Cluster.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Ja | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| machines | Nein | Nein |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datamanagers | Ja | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| jobs | Ja | Ja |

## <a name="microsoftinsights"></a>microsoft.insights
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Nein | Nein |
| actiongroups | Ja | Ja |
| activitylogalerts | Nein | Nein |
| alertrules | Ja | Ja |
| autoscalesettings | Ja | Ja |
| components | Ja | Ja |
| guestdiagnosticsettings | Nein | Nein |
| metricalerts | Nein | Nein |
| notificationgroups | Nein | Nein |
| notificationrules | Nein | Nein |
| scheduledqueryrules | Ja | Ja |
| webtests | Ja | Ja |
| workbooks | Ja | Ja |

> [!IMPORTANT]
> Berücksichtigen Sie beim Verschieben in ein neues Abonnement die [Abonnementkontingente](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| iotapps | Ja | Ja |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| checknameavailability | Ja | Ja |
| graph | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hsmpools | Nein | Nein |
| vaults | Ja | Ja |

> [!IMPORTANT]
> Für die Datenträgerverschlüsselung verwendete Schlüsseltresore können nicht in einer Ressourcengruppe im gleichen Abonnement oder zwischen Abonnements verschoben werden.

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| clusters | Ja | Ja |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| labaccounts | Nein | Nein |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Ja | Ja |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Nein | Nein |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hostingenvironments | Nein | Nein |
| integrationaccounts | Ja | Ja |
| integrationserviceenvironments | Nein | Nein |
| isolatedenvironments | Nein | Nein |
| workflows | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| commitmentplans | Ja | Ja |
| webservices | Ja | Nein |
| workspaces | Ja | Ja |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Ja | Ja |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Nein | Nein |
| accounts/workspaces | Nein | Nein |
| accounts/workspaces/projects | Nein | Nein |
| teamaccounts | Nein | Nein |
| teamaccounts/workspaces | Nein | Nein |
| teamaccounts/workspaces/projects | Nein | Nein |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Ja | Ja |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hostingaccounts | Nein | Nein |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspaces | Nein | Nein |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| userassignedidentities | Nein | Nein |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Ja | Ja |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| classicdevservices | Nein | Nein |

## <a name="microsoftmedia"></a>Microsoft.Media
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| mediaservices | Ja | Ja |
| mediaservices/liveevents | Ja | Ja |
| mediaservices/streamingendpoints | Ja | Ja |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| assessmentprojects | Nein | Nein |
| migrateprojects | Nein | Nein |
| projects | Nein | Nein |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| netappaccounts | Nein | Nein |
| netappaccounts/capacitypools | Nein | Nein |
| netappaccounts/capacitypools/volumes | Nein | Nein |
| netappaccounts/capacitypools/volumes/mounttargets | Nein | Nein |
| netappaccounts/capacitypools/volumes/snapshots | Nein | Nein |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applicationgateways | Nein | Nein |
| applicationgatewaywebapplicationfirewallpolicies | Nein | Nein |
| applicationsecuritygroups | Ja | Ja |
| azurefirewalls | Ja | Ja |
| bastionhosts | Nein | Nein |
| connections | Ja | Ja |
| ddoscustompolicies | Ja | Ja |
| ddosprotectionplans | Nein | Nein |
| dnszones | Ja | Ja |
| expressroutecircuits | Nein | Nein |
| expressroutecrossconnections | Nein | Nein |
| expressroutegateways | Nein | Nein |
| expressrouteports | Nein | Nein |
| frontdoors | Nein | Nein |
| frontdoorwebapplicationfirewallpolicies | Nein | Nein |
| loadbalancers | Ja: Basic-SKU<br>Nein: Standard-SKU | Ja: Basic-SKU<br>Nein: Standard-SKU |
| localnetworkgateways | Ja | Ja |
| natgateways | Ja | Ja |
| networkintentpolicies | Ja | Ja |
| networkinterfaces | Ja | Ja |
| networkprofiles | Nein | Nein |
| networksecuritygroups | Ja | Ja |
| networkwatchers | Ja | Ja |
| networkwatchers/connectionmonitors | Ja | Ja |
| networkwatchers/lenses | Ja | Ja |
| networkwatchers/pingmeshes | Ja | Ja |
| p2svpngateways | Nein | Nein |
| privatednszones | Ja | Ja |
| privatednszones/virtualnetworklinks | Ja | Ja |
| privateendpoints | Nein | Nein |
| privatelinkservices | Nein | Nein |
| publicipaddresses | Ja: Basic-SKU<br>Nein: Standard-SKU | Ja: Basic-SKU<br>Nein: Standard-SKU |
| publicipprefixes | Ja | Ja |
| routefilters | Nein | Nein |
| routetables | Ja | Ja |
| securegateways | Ja | Ja |
| serviceendpointpolicies | Ja | Ja |
| trafficmanagerprofiles | Ja | Ja |
| virtualhubs | Nein | Nein |
| virtualnetworkgateways | Ja | Ja |
| virtualnetworks | Ja | Ja |
| virtualnetworktaps | Nein | Nein |
| virtualwans | Nein | Nein |
| vpngateways | Nein | Nein |
| vpnsites | Nein | Nein |
| webapplicationfirewallpolicies | Ja | Ja |

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for virtual networks](./move-limitations/virtual-network-move-limitations.md) (Anleitung zum Verschieben virtueller Netzwerke).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| namespaces | Ja | Ja |
| namespaces/notificationhubs | Ja | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspaces | Ja | Ja |

> [!IMPORTANT]
> Berücksichtigen Sie beim Verschieben in ein neues Abonnement die [Abonnementkontingente](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| managementconfigurations | Ja | Ja |
| solutions | Ja | JA |
| views | JA | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| peerings | Nein | Nein |

## <a name="microsoftportal"></a>Microsoft.Portal
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dashboards | Ja | Ja |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| rootresources | Nein | Nein |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspacecollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| capacities | Ja | Ja |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Nein | Nein |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| vaults | Ja | Ja |

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Verschieben eines Recovery Services-Tresors zwischen Azure-Abonnements und Ressourcengruppen](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| namespaces | Ja | Ja |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Ressourcentyp | Resource group | Abonnement |
| ------------- | ----------- | ---------- |
| applications | JA | Nein |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| flows | Ja | Ja |
| jobcollections | Ja | Ja |

## <a name="microsoftsearch"></a>Microsoft.Search
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| searchservices | Ja | Ja |

> [!IMPORTANT]
> Es ist nicht möglich, mehrere Search-Ressourcen in verschiedenen Regionen gleichzeitig zu verschieben. Verschieben Sie diese stattdessen in mehreren Vorgängen.

## <a name="microsoftsecurity"></a>Microsoft.Security
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| iotsecuritysolutions | Ja | Ja |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| gateways | Nein | Nein |
| nodes | Nein | Nein |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| namespaces | Ja | Ja |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Ressourcentyp | Resource group | Abonnement |
| ------------- | ----------- | ---------- |
| applications | Nein | Nein |
| clusters | Ja | Ja |
| containergroups | Nein | Nein |
| containergroupsets | Nein | Nein |
| edgeclusters | Nein | Nein |
| networks | Nein | Nein |
| secretstores | Nein | Nein |
| volumes | Nein | Nein |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Ressourcentyp | Resource group | Abonnement |
| ------------- | ----------- | ---------- |
| applications | JA | Ja |
| containergroups | Nein | Nein |
| gateways | Ja | JA |
| networks | Ja | Ja |
| secrets | Ja | Ja |
| volumes | Ja | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| signalr | Ja | Ja |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| siterecoveryvault | Nein | Nein |

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Verschieben eines Recovery Services-Tresors zwischen Azure-Abonnements und Ressourcengruppen](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| appliancedefinitions | Nein | Nein |
| appliances | Nein | Nein |
| applicationdefinitions | Nein | Nein |
| applications | Nein | Nein |
| jitrequests | Nein | Nein |

## <a name="microsoftsql"></a>Microsoft.Sql
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| instancepools | Nein | Nein |
| managedinstances | Nein | Nein |
| managedinstances/databases | Nein | Nein |
| servers | Ja | Ja |
| servers/databases | Ja | Ja |
| servers/elasticpools | Ja | Ja |
| virtualclusters | Ja | Ja |

> [!IMPORTANT]
> Datenbank und Server müssen sich in der gleichen Ressourcengruppe befinden. Wenn Sie eine SQL Server-Instanz verschieben, werden auch alle ihre Datenbanken verschoben. Dieses Verhalten gilt für Azure SQL-Datenbank und Azure SQL Data Warehouse-Datenbanken.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Ja | Ja |
| sqlvirtualmachines | Ja | Ja |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dwvm | Nein | Nein |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storageaccounts | Ja | Ja |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| caches | Nein | Nein |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Ja | Ja |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Nein | Nein |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Nein | Nein |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| managers | Nein | Nein |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| streamingjobs | Ja | Ja |

> [!IMPORTANT]
> Stream Analytics-Aufträge können nicht verschoben werden, während sie ausgeführt werden.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| environments | Nein | Nein |
| environments/eventsources | Nein | Nein |
| instances | Nein | Nein |
| instances/environments | Nein | Nein |
| instances/environments/eventsources | Nein | Nein |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| providerregistrations | Nein | Nein |
| ressourcen | Nein | Nein |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| environments | Ja | Ja |
| environments/eventsources | Ja | Ja |
| environments/referencedatasets | Ja | Ja |

## <a name="microsofttoken"></a>Microsoft.Token
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| stores | Nein | Nein |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| imagetemplates | Nein | Nein |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Ressourcentyp | Resource group | Abonnement |
| ------------- | ----------- | ---------- |
| account | JA | Ja |
| account/extension | Ja | Ja |
| account/project | Ja | Ja |

> [!IMPORTANT]
> Informationen zum Ändern des Abonnements für Azure DevOps finden Sie unter [Change or remove the Azure subscription that your organization uses for billing](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json) (Ändern oder Entfernen des Azure-Abonnements, das von Ihrer Organisation für die Abrechnung verwendet wird).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dedicatedcloudnodes | Ja | Ja |
| dedicatedcloudservices | Ja | Ja |
| virtualmachines | Ja | Ja |

## <a name="microsoftweb"></a>Microsoft.Web
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| certificates | Nein | Ja |
| connectiongateways | Ja | Ja |
| connections | Ja | Ja |
| customapis | Ja | Ja |
| hostingenvironments | Nein | Nein |
| serverfarms | Ja | Ja |
| sites | Ja | Ja |
| sites/premieraddons | Ja | Ja |
| sites/slots | Ja | Ja |

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for App Service resources](./move-limitations/app-service-move-limitations.md) (Anleitung zum Verschieben von App Service-Ressourcen).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| deviceservices | Nein | Nein |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop
| Ressourcentyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applicationgroups | Nein | Nein |
| hostpools | Nein | Nein |
| workspaces | Nein | Nein |

## <a name="third-party-services"></a>Dienste von Drittanbietern

Derzeit wird der Verschiebevorgang nicht für Drittanbieterdienste unterstützt.

## <a name="next-steps"></a>Nächste Schritte
Befehle zum Verschieben von Ressourcen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](resource-group-move-resources.md).

Um die Daten als Datei mit durch Trennzeichen getrennten Werten abzurufen, laden Sie [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv) herunter.
