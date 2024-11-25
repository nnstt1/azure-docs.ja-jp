---
title: 完全モードの削除
description: リソースの種類に応じて Azure Resource Manager テンプレートで完全モードがどのように処理されるかを示します。
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: ea3ea4de33a1680961f078e8e3cc8815da45956e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229901"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>完全モード デプロイでの Azure リソースの削除

この記事では、リソースの種類に応じて、完全モードにデプロイされているテンプレート以外での削除の方法がどのように異なるかを説明します。

**はい** とマークされたリソースの種類は、完全モードでデプロイされるテンプレート内にない場合、削除されます。

**いいえ** とマークされたリソースの種類は、テンプレート内になくても自動的に削除されることはありませんが、親リソースが削除された場合は削除されます。 動作の詳細については、「[Azure Resource Manager のデプロイ モード](deployment-modes.md)」をご覧ください。

[テンプレート内の複数のリソース グループ](./deploy-to-resource-group.md)にデプロイする場合、デプロイ操作で指定されたリソース グループ内のリソースは削除対象となります。 セカンダリ リソース グループ内のリソースは削除されません。

リソースは、リソース プロバイダーの名前空間ごとに一覧表示されます。 リソース プロバイダーの名前空間とその Azure サービス名を照合するには、「[Azure サービスのリソース プロバイダー](../management/azure-services-resource-providers.md)」を参照してください。

> [!NOTE]
> テンプレートを完全モードでデプロイする前に、必ず [what-if 操作](./deploy-what-if.md)を使用してください。 What-if では、作成、削除、または変更されるリソースが示されます。 リソースを誤って削除しないようにするには、what-if を使用します。

リソース プロバイダーの名前空間に移動します。
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AgFoodPlatform](#microsoftagfoodplatform)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.AnyBuild](#microsoftanybuild)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppAssessment](#microsoftappassessment)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automanage](#microsoftautomanage)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureArcData](#microsoftazurearcdata)
> - [Microsoft.AzureCIS](#microsoftazurecis)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzurePercept](#microsoftazurepercept)
> - [Microsoft.AzureSphere](#microsoftazuresphere)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.BackupSolutions](#microsoftbackupsolutions)
> - [Microsoft.BareMetalInfrastructure](#microsoftbaremetalinfrastructure)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BillingBenefits](#microsoftbillingbenefits)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cascade](#microsoftcascade)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClusterStor](#microsoftclusterstor)
> - [Microsoft.CodeSigning](#microsoftcodesigning)
> - [Microsoft.Codespaces](#microsoftcodespaces)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.ConfidentialLedger](#microsoftconfidentialledger)
> - [Microsoft.ConnectedCache](#microsoftconnectedcache)
> - [Microsoft.ConnectedVehicle](#microsoftconnectedvehicle)
> - [Microsoft.ConnectedVMwarevSphere](#microsoftconnectedvmwarevsphere)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.D365CustomerInsights](#microsoftd365customerinsights)
> - [Microsoft.Dashboard](#microsoftdashboard)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DelegatedNetwork](#microsoftdelegatednetwork)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DeviceUpdate](#microsoftdeviceupdate)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.Diagnostics](#microsoftdiagnostics)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EdgeOrder](#microsoftedgeorder)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Fidalgo](#microsoftfidalgo)
> - [Microsoft.FluidRelay](#microsoftfluidrelay)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.Graph](#microsoftgraph)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthBot](#microsofthealthbot)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HpcWorkbench](#microsofthpcworkbench)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridConnectivity](#microsofthybridconnectivity)
> - [Microsoft.HybridContainerService](#microsofthybridcontainerservice)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSecurity](#microsoftiotsecurity)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceNotifications](#microsoftmarketplacenotifications)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.MobileNetwork](#microsoftmobilenetwork)
> - [Microsoft.Monitor](#microsoftmonitor)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.NetworkFunction](#microsoftnetworkfunction)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.Notebooks](#microsoftnotebooks)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PlayFab](#microsoftplayfab)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.PowerPlatform](#microsoftpowerplatform)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Purview](#microsoftpurview)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.Quota](#microsoftquota)
> - [Microsoft.RecommendationsService](#microsoftrecommendationsservice)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceConnector](#microsoftresourceconnector)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scom](#microsoftscom)
> - [Microsoft.ScVmm](#microsoftscvmm)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.ServiceLinker](#microsoftservicelinker)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Singularity](#microsoftsingularity)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TestBase](#microsofttestbase)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VideoIndexer](#microsoftvideoindexer)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | DomainServices | はい |
> | DomainServices / oucontainer | いいえ |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | supportProviders | いいえ |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | aadsupportcases | いいえ |
> | addsservices | いいえ |
> | agents | いいえ |
> | anonymousapiusers | いいえ |
> | configuration | いいえ |
> | logs | いいえ |
> | reports | いいえ |
> | servicehealthmetrics | いいえ |
> | services | いいえ |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | advisorScore | いいえ |
> | configuration | いいえ |
> | generateRecommendations | いいえ |
> | metadata | いいえ |
> | recommendations | いいえ |
> | suppressions | いいえ |

## <a name="microsoftagfoodplatform"></a>Microsoft.AgFoodPlatform

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | farmBeats | はい |
> | farmBeats / eventGridFilters | いいえ |
> | farmBeats / extensions | いいえ |
> | farmBeatsExtensionDefinitions | いいえ |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | actionRules | はい |
> | alerts | いいえ |
> | alertsList | いいえ |
> | alertsMetaData | いいえ |
> | alertsSummary | いいえ |
> | alertsSummaryList | いいえ |
> | migrateFromSmartDetection | いいえ |
> | resourceHealthAlertRules | はい |
> | smartDetectorAlertRules | はい |
> | smartGroups | いいえ |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | servers | はい |

## <a name="microsoftanybuild"></a>Microsoft.AnyBuild

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | clusters | いいえ |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | deletedServices | いいえ |
> | getDomainOwnershipIdentifier | いいえ |
> | reportFeedback | いいえ |
> | サービス (service) | はい |
> | service / eventGridFilters | いいえ |
> | validateServiceName | いいえ |

## <a name="microsoftappassessment"></a>Microsoft.AppAssessment

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | migrateProjects | いいえ |
> | migrateProjects / assessments | いいえ |
> | migrateProjects / assessments / assessedApplications | いいえ |
> | migrateProjects / assessments / assessedApplications / machines | いいえ |
> | migrateProjects / assessments / assessedMachines | いいえ |
> | migrateProjects / assessments / assessedMachines / applications | いいえ |
> | migrateProjects / assessments / machinesToAssess | いいえ |
> | migrateProjects / sites | いいえ |
> | migrateProjects / sites / applianceConfigurations | いいえ |
> | migrateProjects / sites / machines | いいえ |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | configurationStores | はい |
> | configurationStores / eventGridFilters | いいえ |
> | configurationStores / keyValues | いいえ |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | Spring | はい |
> | Spring / apps | いいえ |
> | Spring / apps / deployments | いいえ |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | attestationProviders | はい |
> | defaultProviders | いいえ |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accessReviewScheduleDefinitions | いいえ |
> | accessReviewScheduleSettings | いいえ |
> | batchResourceCheckAccess | いいえ |
> | classicAdministrators | いいえ |
> | dataAliases | いいえ |
> | dataPolicyManifests | いいえ |
> | denyAssignments | いいえ |
> | diagnosticSettings | いいえ |
> | diagnosticSettingsCategories | いいえ |
> | elevateAccess | いいえ |
> | eligibleChildResources | いいえ |
> | findOrphanRoleAssignments | いいえ |
> | locks | いいえ |
> | 権限 | いいえ |
> | policyAssignments | いいえ |
> | policyDefinitions | いいえ |
> | policyExemptions | いいえ |
> | policySetDefinitions | いいえ |
> | privateLinkAssociations | いいえ |
> | providerOperations | いいえ |
> | resourceManagementPrivateLinks | はい |
> | roleAssignmentApprovals | いいえ |
> | roleAssignments | いいえ |
> | roleAssignmentScheduleInstances | いいえ |
> | roleAssignmentScheduleRequests | いいえ |
> | roleAssignmentSchedules | いいえ |
> | roleAssignmentsUsageMetrics | いいえ |
> | roleDefinitions | いいえ |
> | roleEligibilityScheduleInstances | いいえ |
> | roleEligibilityScheduleRequests | いいえ |
> | roleEligibilitySchedules | いいえ |
> | roleManagementPolicies | いいえ |
> | roleManagementPolicyAssignments | いいえ |

## <a name="microsoftautomanage"></a>Microsoft.Automanage

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい |
> | bestPractices | いいえ |
> | bestPractices / versions | いいえ |
> | configurationProfileAssignmentIntents | いいえ |
> | configurationProfileAssignments | いいえ |
> | configurationProfilePreferences | はい |
> | configurationProfiles | はい |
> | configurationProfiles / versions | はい |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | automationAccounts | はい |
> | automationAccounts / configurations | はい |
> | automationAccounts / hybridRunbookWorkerGroups | いいえ |
> | automationAccounts / hybridRunbookWorkerGroups / hybridRunbookWorkers | いいえ |
> | automationAccounts / jobs | いいえ |
> | automationAccounts / privateEndpointConnectionProxies | いいえ |
> | automationAccounts / privateEndpointConnections | いいえ |
> | automationAccounts / privateLinkResources | いいえ |
> | automationAccounts / runbooks | はい |
> | automationAccounts / softwareUpdateConfigurations | いいえ |
> | automationAccounts / webhooks | いいえ |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | privateClouds | はい |
> | privateClouds / addons | いいえ |
> | privateClouds / authorizations | いいえ |
> | privateClouds / cloudLinks | いいえ |
> | privateClouds / clusters | いいえ |
> | privateClouds / clusters / datastores | いいえ |
> | privateClouds / clusters / placementPolicies | いいえ |
> | privateClouds / clusters / virtualMachines | いいえ |
> | privateClouds / globalReachConnections | いいえ |
> | privateClouds / hcxEnterpriseSites | いいえ |
> | privateClouds / scriptExecutions | いいえ |
> | privateClouds / scriptPackages | いいえ |
> | privateClouds / scriptPackages / scriptCmdlets | いいえ |
> | privateClouds / workloadNetworks | いいえ |
> | privateClouds / workloadNetworks / dhcpConfigurations | いいえ |
> | privateClouds / workloadNetworks / dnsServices | いいえ |
> | privateClouds / workloadNetworks / dnsZones | いいえ |
> | privateClouds / workloadNetworks / gateways | いいえ |
> | privateClouds / workloadNetworks / portMirroringProfiles | いいえ |
> | privateClouds / workloadNetworks / publicIPs | いいえ |
> | privateClouds / workloadNetworks / segments | いいえ |
> | privateClouds / workloadNetworks / virtualMachines | いいえ |
> | privateClouds / workloadNetworks / vmGroups | いいえ |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | 環境 | いいえ |
> | environments / accounts | いいえ |
> | environments / accounts / namespaces | いいえ |
> | environments / accounts / namespaces / configurations | いいえ |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | b2cDirectories | はい |
> | b2ctenants | いいえ |
> | guestUsages | はい |

## <a name="microsoftazurearcdata"></a>Microsoft.AzureArcData

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | DataControllers | いいえ |
> | DataWarehouseInstances | いいえ |
> | PostgresInstances | いいえ |
> | SqlManagedInstances | いいえ |
> | SqlServerInstances | いいえ |

## <a name="microsoftazurecis"></a>Microsoft.AzureCIS

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | autopilotEnvironments | いいえ |
> | dstsServiceAccounts | いいえ |
> | dstsServiceClientIdentities | いいえ |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | sqlServerRegistrations | はい |
> | sqlServerRegistrations / sqlServers | いいえ |

## <a name="microsoftazurepercept"></a>Microsoft.AzurePercept

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | いいえ |
> | accounts / devices | いいえ |

## <a name="microsoftazuresphere"></a>Microsoft.AzureSphere

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | catalogs | いいえ |
> | catalogs / certificates | いいえ |
> | catalogs / deployments | いいえ |
> | catalogs / devices | いいえ |
> | catalogs / images | いいえ |
> | catalogs / products | いいえ |
> | catalogs / products / devicegroups | いいえ |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | cloudManifestFiles | いいえ |
> | linkedSubscriptions | はい |
> | registrations | はい |
> | registrations / customerSubscriptions | いいえ |
> | registrations / products | いいえ |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | clusters | いいえ |
> | clusters / arcsettings | いいえ |
> | clusters / arcsettings / extensions | いいえ |
> | galleryImages | いいえ |
> | networkInterfaces | いいえ |
> | virtualHardDisks | いいえ |
> | virtualmachines | いいえ |
> | virtualmachines/extensions | いいえ |
> | virtualmachines / hybrididentitymetadata | いいえ |
> | virtualNetworks | いいえ |

## <a name="microsoftbackupsolutions"></a>Microsoft.BackupSolutions

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | VMwareApplications | はい |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft.BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | bareMetalInstances | はい |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | batchAccounts | はい |
> | batchAccounts/certificates | いいえ |
> | batchAccounts / pools | いいえ |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | billingAccounts | いいえ |
> | billingAccounts / agreements | いいえ |
> | billingAccounts / appliedReservationOrders | いいえ |
> | billingAccounts / billingPermissions | いいえ |
> | billingAccounts / billingProfiles | いいえ |
> | billingAccounts / billingProfiles / billingPermissions | いいえ |
> | billingAccounts / billingProfiles / billingRoleAssignments | いいえ |
> | billingAccounts / billingProfiles / billingRoleDefinitions | いいえ |
> | billingAccounts / billingProfiles / billingSubscriptions | いいえ |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | いいえ |
> | billingAccounts / billingProfiles / customers | いいえ |
> | billingAccounts / billingProfiles / instructions | いいえ |
> | billingAccounts / billingProfiles / invoices | いいえ |
> | billingAccounts / billingProfiles / invoices / pricesheet | いいえ |
> | billingAccounts / billingProfiles / invoices / transactions | いいえ |
> | billingAccounts / billingProfiles / invoiceSections | いいえ |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | いいえ |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | いいえ |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | いいえ |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | いいえ |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | いいえ |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | いいえ |
> | billingAccounts / billingProfiles / invoiceSections / products | いいえ |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | いいえ |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | いいえ |
> | billingAccounts / billingProfiles / invoiceSections / transactions | いいえ |
> | billingAccounts / billingProfiles / invoiceSections / transfers | いいえ |
> | billingAccounts / billingProfiles / invoiceSections / validateDeleteInvoiceSectionEligibility | いいえ |
> | billingAccounts / BillingProfiles / patchOperations | いいえ |
> | billingAccounts / billingProfiles / paymentMethodLinks | いいえ |
> | billingAccounts / billingProfiles / paymentMethods | いいえ |
> | billingAccounts / billingProfiles / policies | いいえ |
> | billingAccounts / billingProfiles / pricesheet | いいえ |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | いいえ |
> | billingAccounts / billingProfiles / products | いいえ |
> | billingAccounts / billingProfiles / reservations | いいえ |
> | billingAccounts / billingProfiles / transactions | いいえ |
> | billingAccounts / billingProfiles / validateDeleteBillingProfileEligibility | いいえ |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | いいえ |
> | billingAccounts / billingRoleAssignments | いいえ |
> | billingAccounts / billingRoleDefinitions | いいえ |
> | billingAccounts / billingSubscriptions | いいえ |
> | billingAccounts / billingSubscriptions / elevateRole | いいえ |
> | billingAccounts / billingSubscriptions / invoices | いいえ |
> | billingAccounts / createBillingRoleAssignment | いいえ |
> | billingAccounts / createInvoiceSectionOperations | いいえ |
> | billingAccounts / customers | いいえ |
> | billingAccounts / customers / billingPermissions | いいえ |
> | billingAccounts / customers / billingSubscriptions | いいえ |
> | billingAccounts / customers / initiateTransfer | いいえ |
> | billingAccounts / customers / policies | いいえ |
> | billingAccounts / customers / products | いいえ |
> | billingAccounts / customers / transactions | いいえ |
> | billingAccounts / customers / transfers | いいえ |
> | billingAccounts / customers / transferSupportedAccounts | いいえ |
> | billingAccounts / departments | いいえ |
> | billingAccounts / departments / billingPermissions | いいえ |
> | billingAccounts / departments / billingRoleAssignments | いいえ |
> | billingAccounts / departments / billingRoleDefinitions | いいえ |
> | billingAccounts / departments / billingSubscriptions | いいえ |
> | billingAccounts / departments / enrollmentAccounts | いいえ |
> | billingAccounts / enrollmentAccounts | いいえ |
> | billingAccounts / enrollmentAccounts / billingPermissions | いいえ |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | いいえ |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | いいえ |
> | billingAccounts / enrollmentAccounts / billingSubscriptions | いいえ |
> | billingAccounts / invoices | いいえ |
> | billingAccounts / invoices / transactions | いいえ |
> | billingAccounts / invoices / transactionSummary | いいえ |
> | billingAccounts / invoiceSections | いいえ |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | いいえ |
> | billingAccounts / invoiceSections / billingSubscriptions | いいえ |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | いいえ |
> | billingAccounts / invoiceSections / elevate | いいえ |
> | billingAccounts / invoiceSections / initiateTransfer | いいえ |
> | billingAccounts / invoiceSections / patchOperations | いいえ |
> | billingAccounts / invoiceSections / productMoveOperations | いいえ |
> | billingAccounts / invoiceSections / products | いいえ |
> | billingAccounts / invoiceSections / products / transfer | いいえ |
> | billingAccounts / invoiceSections / products / updateAutoRenew | いいえ |
> | billingAccounts / invoiceSections / transactions | いいえ |
> | billingAccounts / invoiceSections / transfers | いいえ |
> | billingAccounts / lineOfCredit | いいえ |
> | billingAccounts / patchOperations | いいえ |
> | billingAccounts / payableOverage | いいえ |
> | billingAccounts / paymentMethods | いいえ |
> | billingAccounts / payNow | いいえ |
> | billingAccounts / policies | いいえ |
> | billingAccounts / products | いいえ |
> | billingAccounts / promotionalCredits | いいえ |
> | billingAccounts / reservations | いいえ |
> | billingAccounts / savingsPlanOrders | いいえ |
> | billingAccounts / savingsPlanOrders / savingsPlans | いいえ |
> | billingAccounts / savingsPlans | いいえ |
> | billingAccounts / transactions | いいえ |
> | billingPeriods | いいえ |
> | billingPermissions | いいえ |
> | billingProperty | いいえ |
> | billingRoleAssignments | いいえ |
> | billingRoleDefinitions | いいえ |
> | createBillingRoleAssignment | いいえ |
> | departments | いいえ |
> | enrollmentAccounts | いいえ |
> | invoices | いいえ |
> | paymentMethods | いいえ |
> | promotionalCredits | いいえ |
> | 上位変換 | いいえ |
> | transfers | いいえ |
> | transfers / acceptTransfer | いいえ |
> | transfers / declineTransfer | いいえ |
> | transfers / operationStatus | いいえ |
> | transfers / validateTransfer | いいえ |
> | validateAddress | いいえ |

## <a name="microsoftbillingbenefits"></a>Microsoft.BillingBenefits

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | savingsPlanOrderAliases | いいえ |
> | validate | いいえ |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | blockchainMembers | はい |
> | cordaMembers | はい |
> | watchers | はい |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | TokenServices | はい |
> | TokenServices / BlockchainNetworks | いいえ |
> | TokenServices / Groups | いいえ |
> | TokenServices / Groups / Accounts | いいえ |
> | TokenServices / TokenTemplates | いいえ |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | blueprintAssignments | いいえ |
> | blueprintAssignments / assignmentOperations | いいえ |
> | blueprintAssignments / operations | いいえ |
> | blueprints | いいえ |
> | blueprints / artifacts | いいえ |
> | blueprints / versions | いいえ |
> | blueprints / versions / artifacts | いいえ |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | botServices | はい |
> | botServices / channels | いいえ |
> | botServices / connections | いいえ |
> | botServices / privateEndpointConnectionProxies | いいえ |
> | botServices / privateEndpointConnections | いいえ |
> | botServices / privateLinkResources | いいえ |
> | hostSettings | いいえ |
> | languages | いいえ |
> | テンプレート | いいえ |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | Redis | はい |
> | Redis / EventGridFilters | いいえ |
> | Redis / privateEndpointConnectionProxies | いいえ |
> | Redis / privateEndpointConnectionProxies / validate | いいえ |
> | Redis / privateEndpointConnections | いいえ |
> | Redis / privateLinkResources | いいえ |
> | redisEnterprise | はい |
> | redisEnterprise / databases | いいえ |
> | RedisEnterprise / privateEndpointConnectionProxies | いいえ |
> | RedisEnterprise / privateEndpointConnectionProxies / validate | いいえ |
> | RedisEnterprise / privateEndpointConnections | いいえ |
> | RedisEnterprise / privateLinkResources | いいえ |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | appliedReservations | いいえ |
> | autoQuotaIncrease | いいえ |
> | calculateExchange | いいえ |
> | calculatePrice | いいえ |
> | calculatePurchasePrice | いいえ |
> | catalogs | いいえ |
> | commercialReservationOrders | いいえ |
> | exchange | いいえ |
> | ownReservations | いいえ |
> | placePurchaseOrder | いいえ |
> | reservationOrders | いいえ |
> | reservationOrders / calculateRefund | いいえ |
> | reservationOrders / merge | いいえ |
> | reservationOrders / reservations | いいえ |
> | reservationOrders / reservations / revisions | いいえ |
> | reservationOrders / return | いいえ |
> | reservationOrders / split | いいえ |
> | reservationOrders / swap | いいえ |
> | reservations | いいえ |
> | resourceProviders | いいえ |
> | resources | いいえ |
> | validateReservationOrder | いいえ |

## <a name="microsoftcascade"></a>Microsoft.Cascade

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | sites | いいえ |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | いいえ |
> | CdnWebApplicationFirewallPolicies | はい |
> | edgenodes | いいえ |
> | profiles | はい |
> | profiles / afdendpoints | はい |
> | profiles / afdendpoints / routes | いいえ |
> | profiles / customdomains | いいえ |
> | profiles/endpoints | はい |
> | profiles / endpoints / customdomains | いいえ |
> | profiles / endpoints / origingroups | いいえ |
> | profiles / endpoints / origins | いいえ |
> | profiles / origingroups | いいえ |
> | profiles / origingroups / origins | いいえ |
> | profiles / rulesets | いいえ |
> | profiles / rulesets / rules | いいえ |
> | profiles / secrets | いいえ |
> | profiles / securitypolicies | いいえ |
> | validateProbe | いいえ |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | certificateOrders | はい |
> | certificateOrders / certificates | いいえ |
> | validateCertificateRegistrationInformation | いいえ |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | 変更点 | いいえ |
> | changeSnapshots | いいえ |
> | computeChanges | いいえ |
> | profile | いいえ |
> | resourceChanges | いいえ |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | capabilities | いいえ |
> | domainNames | はい |
> | domainNames / capabilities | いいえ |
> | domainNames / internalLoadBalancers | いいえ |
> | domainNames / serviceCertificates | いいえ |
> | domainNames / slots | いいえ |
> | domainNames / slots / roles | いいえ |
> | domainNames / slots / roles / metricDefinitions | いいえ |
> | domainNames / slots / roles / metrics | いいえ |
> | moveSubscriptionResources | いいえ |
> | operatingSystemFamilies | いいえ |
> | operatingSystems | いいえ |
> | quotas | いいえ |
> | resourceTypes | いいえ |
> | validateSubscriptionMoveAvailability | いいえ |
> | virtualMachines | はい |
> | virtualMachines / diagnosticSettings | いいえ |
> | virtualMachines / metricDefinitions | いいえ |
> | virtualMachines / metrics | いいえ |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | classicInfrastructureResources | いいえ |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | capabilities | いいえ |
> | expressRouteCrossConnections | いいえ |
> | expressRouteCrossConnections / peerings | いいえ |
> | gatewaySupportedDevices | いいえ |
> | networkSecurityGroups | はい |
> | quotas | いいえ |
> | reservedIps | はい |
> | virtualNetworks | はい |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | いいえ |
> | virtualNetworks / virtualNetworkPeerings | いいえ |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | capabilities | いいえ |
> | disks | いいえ |
> | images | いいえ |
> | osImages | いいえ |
> | osPlatformImages | いいえ |
> | publicImages | いいえ |
> | quotas | いいえ |
> | storageAccounts | はい |
> | storageAccounts / blobServices | いいえ |
> | storageAccounts / fileServices | いいえ |
> | storageAccounts / metricDefinitions | いいえ |
> | storageAccounts / metrics | いいえ |
> | storageAccounts / queueServices | いいえ |
> | storageAccounts / services | いいえ |
> | storageAccounts / services / diagnosticSettings | いいえ |
> | storageAccounts / services / metricDefinitions | いいえ |
> | storageAccounts / services / metrics | いいえ |
> | storageAccounts / tableServices | いいえ |
> | storageAccounts / vmImages | いいえ |
> | vmImages | いいえ |

## <a name="microsoftclusterstor"></a>Microsoft.ClusterStor

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | nodes | はい |

## <a name="microsoftcodesigning"></a>Microsoft.CodeSigning

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | codeSigningAccounts | いいえ |
> | codeSigningAccounts / certificateProfiles | いいえ |

## <a name="microsoftcodespaces"></a>Microsoft.Codespaces

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | plans | はい |
> | registeredSubscriptions | いいえ |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい |
> | accounts / privateEndpointConnectionProxies | いいえ |
> | accounts / privateEndpointConnections | いいえ |
> | accounts / privateLinkResources | いいえ |
> | deletedAccounts | いいえ |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | availabilitySets | はい |
> | cloudServices | はい |
> | cloudServices / networkInterfaces | いいえ |
> | cloudServices / publicIPAddresses | いいえ |
> | cloudServices / roleInstances | いいえ |
> | cloudServices / roleInstances / networkInterfaces | いいえ |
> | cloudServices / roles | いいえ |
> | diskAccesses | はい |
> | diskEncryptionSets | はい |
> | disks | はい |
> | galleries | はい |
> | galleries / applications | いいえ |
> | galleries / applications / versions | いいえ |
> | galleries/images | はい |
> | galleries/images/versions | はい |
> | hostGroups | はい |
> | hostGroups / hosts | はい |
> | images | はい |
> | proximityPlacementGroups | はい |
> | restorePointCollections | はい |
> | restorePointCollections / restorePoints | いいえ |
> | sharedVMExtensions | はい |
> | sharedVMExtensions / versions | いいえ |
> | sharedVMImages | はい |
> | sharedVMImages / versions | いいえ |
> | スナップショット | はい |
> | sshPublicKeys | はい |
> | virtualMachines | はい |
> | virtualMachines / extensions | はい |
> | virtualMachines / metricDefinitions | いいえ |
> | virtualMachines / runCommands | はい |
> | virtualMachineScaleSets | はい |
> | virtualMachineScaleSets / extensions | いいえ |
> | virtualMachineScaleSets / networkInterfaces | いいえ |
> | virtualMachineScaleSets / publicIPAddresses | いいえ |
> | virtualMachineScaleSets / virtualMachines | いいえ |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | いいえ |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | RateCard | いいえ |
> | UsageAggregates | いいえ |

## <a name="microsoftconfidentialledger"></a>Microsoft.ConfidentialLedger

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | Ledgers | いいえ |

## <a name="microsoftconnectedcache"></a>Microsoft.ConnectedCache

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | CacheNodes | いいえ |

## <a name="microsoftconnectedvehicle"></a>Microsoft.ConnectedVehicle

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | platformAccounts | いいえ |
> | registeredSubscriptions | いいえ |

## <a name="microsoftconnectedvmwarevsphere"></a>Microsoft.ConnectedVMwarevSphere

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | クラスター | いいえ |
> | データストア | いいえ |
> | Hosts | いいえ |
> | ResourcePools | いいえ |
> | VCenters | いいえ |
> | VCenters / InventoryItems | いいえ |
> | VirtualMachines | いいえ |
> | VirtualMachines / Extensions | はい |
> | VirtualMachines / GuestAgents | いいえ |
> | VirtualMachines / HybridIdentityMetadata | いいえ |
> | VirtualMachineTemplates | いいえ |
> | VirtualNetworks | いいえ |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | AggregatedCost | いいえ |
> | Balances | いいえ |
> | Budgets | いいえ |
> | Charges | いいえ |
> | CostTags | いいえ |
> | credits | いいえ |
> | events | いいえ |
> | Forecasts | いいえ |
> | lots | いいえ |
> | Marketplaces | いいえ |
> | Pricesheets | いいえ |
> | products | いいえ |
> | ReservationDetails | いいえ |
> | ReservationRecommendationDetails | いいえ |
> | ReservationRecommendations | いいえ |
> | ReservationSummaries | いいえ |
> | ReservationTransactions | いいえ |
> | タグ | いいえ |
> | tenants | いいえ |
> | 用語 | いいえ |
> | UsageDetails | いいえ |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | containerGroups | はい |
> | serviceAssociationLinks | いいえ |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | registries | はい |
> | registries / agentPools | はい |
> | registries / builds | いいえ |
> | registries / builds / cancel | いいえ |
> | registries / builds / getLogLink | いいえ |
> | registries / buildTasks | はい |
> | registries / buildTasks / steps | いいえ |
> | registries / connectedRegistries | いいえ |
> | registries / connectedRegistries / deactivate | いいえ |
> | registries / eventGridFilters | いいえ |
> | registries / exportPipelines | いいえ |
> | registries / generateCredentials | いいえ |
> | registries / getBuildSourceUploadUrl | いいえ |
> | registries / GetCredentials | いいえ |
> | registries / importImage | いいえ |
> | registries / importPipelines | いいえ |
> | registries / pipelineRuns | いいえ |
> | registries / privateEndpointConnectionProxies | いいえ |
> | registries / privateEndpointConnectionProxies / validate | いいえ |
> | registries / privateEndpointConnections | いいえ |
> | registries / privateLinkResources | いいえ |
> | registries / queueBuild | いいえ |
> | registries / regenerateCredential | いいえ |
> | registries / regenerateCredentials | いいえ |
> | registries/replications | はい |
> | registries / runs | いいえ |
> | registries / runs / cancel | いいえ |
> | registries / scheduleRun | いいえ |
> | registries / scopeMaps | いいえ |
> | registries / taskRuns | いいえ |
> | registries/tasks | はい |
> | registries / tokens | いいえ |
> | registries / updatePolicies | いいえ |
> | registries/webhooks | はい |
> | registries / webhooks / getCallbackConfig | いいえ |
> | registries / webhooks / ping | いいえ |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | containerServices | はい |
> | managedClusters | はい |
> | ManagedClusters / eventGridFilters | いいえ |
> | openShiftManagedClusters | はい |
> | スナップショット | はい |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | 警告 | いいえ |
> | BenefitUtilizationSummaries | いいえ |
> | BillingAccounts | いいえ |
> | Budgets | いいえ |
> | calculatePrice | いいえ |
> | CloudConnectors | いいえ |
> | Connectors | はい |
> | costAllocationRules | いいえ |
> | Departments | いいえ |
> | Dimensions | いいえ |
> | EnrollmentAccounts | いいえ |
> | Exports | いいえ |
> | ExternalBillingAccounts | いいえ |
> | ExternalBillingAccounts / Alerts | いいえ |
> | ExternalBillingAccounts / Dimensions | いいえ |
> | ExternalBillingAccounts / Forecast | いいえ |
> | ExternalBillingAccounts / Query | いいえ |
> | ExternalSubscriptions | いいえ |
> | ExternalSubscriptions / Alerts | いいえ |
> | ExternalSubscriptions / Dimensions | いいえ |
> | ExternalSubscriptions / Forecast | いいえ |
> | ExternalSubscriptions / Query | いいえ |
> | fetchPrices | いいえ |
> | Forecast | いいえ |
> | GenerateDetailedCostReport | いいえ |
> | GenerateReservationDetailsReport | いいえ |
> | 洞察 | いいえ |
> | クエリ | いいえ |
> | registrations | いいえ |
> | Reportconfigs | いいえ |
> | Reports | いいえ |
> | ScheduledActions | いいえ |
> | 設定 | いいえ |
> | showbackRules | いいえ |
> | ビュー | いいえ |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | DisableLockbox | いいえ |
> | EnableLockbox | いいえ |
> | requests | いいえ |
> | TenantOptedIn | いいえ |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | associations | いいえ |
> | resourceProviders | はい |

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | instances | はい |

## <a name="microsoftdashboard"></a>Microsoft.Dashboard

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | grafana | いいえ |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | jobs | はい |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | はい |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | workspaces | はい |
> | workspaces / dbWorkspaces | いいえ |
> | workspaces / virtualNetworkPeerings | いいえ |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | catalogs | はい |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | dataFactories | はい |
> | dataFactories / diagnosticSettings | いいえ |
> | dataFactories / metricDefinitions | いいえ |
> | dataFactorySchema | いいえ |
> | factories | はい |
> | factories / integrationRuntimes | いいえ |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい |
> | accounts / dataLakeStoreAccounts | いいえ |
> | accounts / storageAccounts | いいえ |
> | accounts / storageAccounts / containers | いいえ |
> | accounts / transferAnalyticsUnits | いいえ |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい |
> | accounts / eventGridFilters | いいえ |
> | accounts / firewallRules | いいえ |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | DatabaseMigrations | いいえ |
> | services | はい |
> | services/projects | はい |
> | SqlMigrationServices | はい |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | BackupVaults | はい |
> | ResourceGuards | はい |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい |
> | accounts / shares | いいえ |
> | accounts / shares / datasets | いいえ |
> | accounts / shares / invitations | いいえ |
> | accounts / shares / providersharesubscriptions | いいえ |
> | accounts / shares / synchronizationSettings | いいえ |
> | accounts / sharesubscriptions | いいえ |
> | accounts / sharesubscriptions / consumerSourceDataSets | いいえ |
> | accounts / sharesubscriptions / datasetmappings | いいえ |
> | accounts / sharesubscriptions / triggers | いいえ |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | servers | はい |
> | servers / advisors | いいえ |
> | servers / keys | いいえ |
> | servers / privateEndpointConnectionProxies | いいえ |
> | servers / privateEndpointConnections | いいえ |
> | servers / privateLinkResources | いいえ |
> | servers / queryTexts | いいえ |
> | servers / recoverableServers | いいえ |
> | servers / resetQueryPerformanceInsightData | いいえ |
> | servers / start | いいえ |
> | servers / stop | いいえ |
> | servers / topQueryStatistics | いいえ |
> | servers / virtualNetworkRules | いいえ |
> | servers / topQueryStatistics | いいえ |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | flexibleServers | はい |
> | getPrivateDnsZoneSuffix | いいえ |
> | servers | はい |
> | servers / advisors | いいえ |
> | servers / keys | いいえ |
> | servers / privateEndpointConnectionProxies | いいえ |
> | servers / privateEndpointConnections | いいえ |
> | servers / privateLinkResources | いいえ |
> | servers / queryTexts | いいえ |
> | servers / recoverableServers | いいえ |
> | servers / resetQueryPerformanceInsightData | いいえ |
> | servers / start | いいえ |
> | servers / stop | いいえ |
> | servers / topQueryStatistics | いいえ |
> | servers / upgrade | いいえ |
> | servers / virtualNetworkRules | いいえ |
> | servers / topQueryStatistics | いいえ |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | flexibleServers | はい |
> | getPrivateDnsZoneSuffix | いいえ |
> | serverGroups | はい |
> | serverGroupsv2 | はい |
> | servers | はい |
> | servers / advisors | いいえ |
> | servers / keys | いいえ |
> | servers / privateEndpointConnectionProxies | いいえ |
> | servers / privateEndpointConnections | いいえ |
> | servers / privateLinkResources | いいえ |
> | servers / queryTexts | いいえ |
> | servers / recoverableServers | いいえ |
> | servers / resetQueryPerformanceInsightData | いいえ |
> | servers / topQueryStatistics | いいえ |
> | servers / virtualNetworkRules | いいえ |
> | servers / topQueryStatistics | いいえ |
> | serversv2 | はい |

## <a name="microsoftdelegatednetwork"></a>Microsoft.DelegatedNetwork

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | コントローラー | はい |
> | delegatedSubnets | はい |
> | orchestrators | はい |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | artifactSources | はい |
> | rollouts | はい |
> | serviceTopologies | はい |
> | serviceTopologies / services | はい |
> | serviceTopologies / services / serviceUnits | はい |
> | steps | はい |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | applicationgroups | はい |
> | applicationgroups / applications | いいえ |
> | applicationgroups / desktops | いいえ |
> | applicationgroups / startmenuitems | いいえ |
> | hostpools | はい |
> | hostpools / msixpackages | いいえ |
> | hostpools / sessionhosts | いいえ |
> | hostpools / sessionhosts / usersessions | いいえ |
> | hostpools / usersessions | いいえ |
> | scalingplans | はい |
> | workspaces | はい |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | ElasticPools | はい |
> | ElasticPools / IotHubTenants | はい |
> | ElasticPools / IotHubTenants / securitySettings | いいえ |
> | IotHubs | はい |
> | IotHubs / eventGridFilters | いいえ |
> | IotHubs / failover | いいえ |
> | IotHubs / securitySettings | いいえ |
> | ProvisioningServices | はい |
> | usages | いいえ |

## <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | いいえ |
> | accounts / instances | いいえ |
> | registeredSubscriptions | いいえ |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | pipelines | はい |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | controllers | はい |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | labcenters | はい |
> | labs | はい |
> | labs/environments | はい |
> | labs / serviceRunners | はい |
> | labs / virtualMachines | はい |
> | schedules | はい |

## <a name="microsoftdiagnostics"></a>Microsoft.Diagnostics

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | AzureKB | いいえ |
> | InsightDiagnostics | いいえ |
> | solutions | いいえ |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | digitalTwinsInstances | はい |
> | digitalTwinsInstances / endpoints | いいえ |
> | digitalTwinsInstances / timeSeriesDatabaseConnections | いいえ |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | cassandraClusters | はい |
> | databaseAccountNames | いいえ |
> | databaseAccounts | はい |
> | restorableDatabaseAccounts | いいえ |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | domains | はい |
> | domains / domainOwnershipIdentifiers | いいえ |
> | generateSsoRequest | いいえ |
> | topLevelDomains | いいえ |
> | validateDomainRegistrationInformation | いいえ |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | lcsprojects | いいえ |
> | lcsprojects / clouddeployments | いいえ |
> | lcsprojects / connectors | いいえ |

## <a name="microsoftedgeorder"></a>Microsoft.EdgeOrder

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | 住所 | はい |
> | orderItems | はい |
> | 注文 | いいえ |
> | productFamiliesMetadata | いいえ |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | services | はい |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | domains | はい |
> | domains / topics | いいえ |
> | eventSubscriptions | いいえ |
> | extensionTopics | いいえ |
> | partnerNamespaces | はい |
> | partnerNamespaces / eventChannels | いいえ |
> | partnerRegistrations | はい |
> | partnerTopics | はい |
> | partnerTopics / eventSubscriptions | いいえ |
> | systemTopics | はい |
> | systemTopics / eventSubscriptions | いいえ |
> | topics | はい |
> | topicTypes | いいえ |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | clusters | はい |
> | namespaces | はい |
> | namespaces / authorizationrules | いいえ |
> | namespaces / disasterrecoveryconfigs | いいえ |
> | namespaces / eventhubs | いいえ |
> | namespaces / eventhubs / authorizationrules | いいえ |
> | namespaces / eventhubs / consumergroups | いいえ |
> | namespaces / networkrulesets | いいえ |
> | namespaces / privateEndpointConnections | いいえ |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | experimentWorkspaces | はい |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | namespaces | はい |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | featureConfigurations | いいえ |
> | featureProviderNamespaces | いいえ |
> | featureProviders | いいえ |
> | features | いいえ |
> | providers | いいえ |
> | subscriptionFeatureRegistrations | いいえ |

## <a name="microsoftfidalgo"></a>Microsoft.Fidalgo

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | devcenters | いいえ |
> | devcenters / catalogs | いいえ |
> | devcenters / catalogs / items | いいえ |
> | devcenters / environmentTypes | いいえ |
> | devcenters / mappings | いいえ |
> | machinedefinitions | いいえ |
> | networksettings | いいえ |
> | projects | いいえ |
> | projects / catalogItems | いいえ |
> | projects / environments | いいえ |
> | projects / environments / deployments | いいえ |
> | projects / environmentTypes | いいえ |
> | projects / pools | いいえ |

## <a name="microsoftfluidrelay"></a>Microsoft.FluidRelay

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | fluidRelayServers | いいえ |
> | fluidRelayServers / fluidRelayContainers | いいえ |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | enroll | いいえ |
> | galleryitems | いいえ |
> | generateartifactaccessuri | いいえ |
> | myareas | いいえ |
> | myareas / areas | いいえ |
> | myareas / areas / areas | いいえ |
> | myareas / areas / areas / galleryitems | いいえ |
> | myareas / areas / galleryitems | いいえ |
> | myareas / galleryitems | いいえ |
> | registrations | いいえ |
> | resources | いいえ |
> | retrieveresourcesbyid | いいえ |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい |

## <a name="microsoftgraph"></a>Microsoft.Graph

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | AzureAdApplication | いいえ |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | autoManagedAccounts | はい |
> | autoManagedVmConfigurationProfiles | はい |
> | configurationProfileAssignments | いいえ |
> | guestConfigurationAssignments | いいえ |
> | software | いいえ |
> | softwareUpdateProfile | いいえ |
> | softwareUpdates | いいえ |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | hanaInstances | はい |
> | sapMonitors | はい |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | dedicatedHSMs | はい |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | clusterPools | はい |
> | clusterPools / clusters | はい |
> | clusterPools / clusters / instanceViews | いいえ |
> | clusterPools / clusters / serviceConfigs | いいえ |
> | clusterPools / clusters / sessionClusters | はい |
> | clusterPools / clusters / sessionClusters / instanceViews | いいえ |
> | clusterPools / clusters / sessionClusters / serviceConfigs | いいえ |
> | clusters | はい |
> | clusters/applications | いいえ |

## <a name="microsofthealthbot"></a>Microsoft.HealthBot

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | healthBots | いいえ |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | services | はい |
> | services / iomtconnectors | いいえ |
> | services / iomtconnectors / connections | いいえ |
> | services / iomtconnectors / mappings | いいえ |
> | services / privateEndpointConnectionProxies | いいえ |
> | services / privateEndpointConnections | いいえ |
> | services / privateLinkResources | いいえ |
> | workspaces | はい |
> | workspaces / dicomservices | はい |
> | workspaces / eventGridFilters | いいえ |
> | workspaces / fhirservices | はい |
> | workspaces / iotconnectors | はい |
> | workspaces / iotconnectors / destinations | いいえ |
> | workspaces / iotconnectors / fhirdestinations | いいえ |
> | workspaces / privateEndpointConnectionProxies | いいえ |
> | workspaces / privateEndpointConnections | いいえ |
> | workspaces / privateLinkResources | いいえ |

## <a name="microsofthpcworkbench"></a>Microsoft.HpcWorkbench

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | instances | いいえ |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | machines | はい |
> | machines / assessPatches | いいえ |
> | machines / extensions | はい |
> | machines / installPatches | いいえ |
> | machines / privateLinkScopes | いいえ |
> | privateLinkScopes | はい |
> | privateLinkScopes / privateEndpointConnectionProxies | いいえ |
> | privateLinkScopes / privateEndpointConnections | いいえ |

## <a name="microsofthybridconnectivity"></a>Microsoft.HybridConnectivity

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | エンドポイント | いいえ |

## <a name="microsofthybridcontainerservice"></a>Microsoft.HybridContainerService

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | provisionedClusters | いいえ |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | dataManagers | はい |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | devices | いいえ |
> | networkFunctions | いいえ |
> | networkFunctionVendors | いいえ |
> | registeredSubscriptions | いいえ |
> | vendors | いいえ |
> | vendors / vendorSkus | いいえ |
> | vendors / vendorskus / previewSubscriptions | いいえ |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | components | はい |
> | networkScopes | はい |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | jobs | はい |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | diagnosticSettings | いいえ |
> | diagnosticSettingsCategories | いいえ |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | appTemplates | いいえ |
> | IoTApps | はい |

## <a name="microsoftiotsecurity"></a>Microsoft.IoTSecurity

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | alertTypes | いいえ |
> | defenderSettings | いいえ |
> | onPremiseSensors | いいえ |
> | recommendationTypes | いいえ |
> | sensors | いいえ |
> | sites | いいえ |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | グラフ | はい |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | deletedManagedHSMs | いいえ |
> | deletedVaults | いいえ |
> | hsmPools | はい |
> | managedHSMs | はい |
> | vaults | はい |
> | vaults / accessPolicies | いいえ |
> | vaults / eventGridFilters | いいえ |
> | vaults / keys | いいえ |
> | vaults / keys / versions | いいえ |
> | vaults / secrets | いいえ |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | connectedClusters | いいえ |
> | registeredSubscriptions | いいえ |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | 拡張機能 | いいえ |
> | fluxConfigurations | いいえ |
> | sourceControlConfigurations | いいえ |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | clusters | はい |
> | clusters / attacheddatabaseconfigurations | いいえ |
> | clusters / databases | いいえ |
> | clusters / databases / dataconnections | いいえ |
> | clusters / databases / eventhubconnections | いいえ |
> | clusters / databases / principalassignments | いいえ |
> | clusters / databases / scripts | いいえ |
> | clusters / dataconnections | いいえ |
> | clusters / principalassignments | いいえ |
> | clusters / sharedidentities | いいえ |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | labaccounts | はい |
> | labplans | はい |
> | labs | はい |
> | users | いいえ |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | hostingEnvironments | はい |
> | integrationAccounts | はい |
> | integrationServiceEnvironments | はい |
> | integrationServiceEnvironments / managedApis | はい |
> | isolatedEnvironments | はい |
> | workflows | はい |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | commitmentPlans | はい |
> | webServices | はい |
> | Workspaces | はい |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | aisysteminventories | はい |
> | virtualclusters | はい |
> | workspaces | はい |
> | workspaces / batchEndpoints | はい |
> | workspaces / batchEndpoints / deployments | はい |
> | workspaces / batchEndpoints / deployments / jobs | いいえ |
> | workspaces / batchEndpoints / jobs | いいえ |
> | workspaces / codes | いいえ |
> | workspaces / codes / versions | いいえ |
> | workspaces / components | いいえ |
> | workspaces / components / versions | いいえ |
> | workspaces / computes | いいえ |
> | workspaces / data | いいえ |
> | workspaces / datasets | いいえ |
> | workspaces / datastores | いいえ |
> | workspaces / environments | いいえ |
> | workspaces / eventGridFilters | いいえ |
> | workspaces / jobs | いいえ |
> | workspaces / labelingJobs | いいえ |
> | workspaces / linkedServices | いいえ |
> | workspaces / models | いいえ |
> | workspaces / models / versions | いいえ |
> | workspaces / onlineEndpoints | はい |
> | workspaces / onlineEndpoints / deployments | はい |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | applyUpdates | いいえ |
> | configurationAssignments | いいえ |
> | maintenanceConfigurations | はい |
> | publicMaintenanceConfigurations | いいえ |
> | updates | いいえ |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | Identities | いいえ |
> | userAssignedIdentities | はい |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | いいえ |
> | registrationAssignments | いいえ |
> | registrationDefinitions | いいえ |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | getEntities | いいえ |
> | managementGroups | いいえ |
> | managementGroups / settings | いいえ |
> | resources | いいえ |
> | startTenantBackfill | いいえ |
> | tenantBackfillStatus | いいえ |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい |
> | accounts / creators | はい |
> | accounts / eventGridFilters | いいえ |
> | accounts / privateAtlases | はい |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | macc | いいえ |
> | offers | いいえ |
> | offerTypes | いいえ |
> | offerTypes / publishers | いいえ |
> | offerTypes / publishers / offers | いいえ |
> | offerTypes / publishers / offers / plans | いいえ |
> | offerTypes / publishers / offers / plans / agreements | いいえ |
> | offerTypes / publishers / offers / plans / configs | いいえ |
> | offerTypes / publishers / offers / plans / configs / importImage | いいえ |
> | privategalleryitems | いいえ |
> | privateStoreClient | いいえ |
> | privateStores | いいえ |
> | privateStores / AdminRequestApprovals | いいえ |
> | privateStores / billingAccounts | いいえ |
> | privateStores / bulkCollectionsAction | いいえ |
> | privateStores / collections | いいえ |
> | privateStores / collections / offers | いいえ |
> | privateStores / collections / transferOffers | いいえ |
> | privateStores / collectionsToSubscriptionsMapping | いいえ |
> | privateStores / offers | いいえ |
> | privateStores / offers / acknowledgeNotification | いいえ |
> | privateStores / queryApprovedPlans | いいえ |
> | privateStores / queryNotificationsState | いいえ |
> | privateStores / queryOffers | いいえ |
> | privateStores / RequestApprovals | いいえ |
> | privateStores / requestApprovals / query | いいえ |
> | privateStores / requestApprovals / withdrawPlan | いいえ |
> | products | いいえ |
> | publishers | いいえ |
> | publishers / offers | いいえ |
> | publishers / offers / amendments | いいえ |
> | registrations | いいえ |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | classicDevServices | はい |
> | updateCommunicationPreference | いいえ |

## <a name="microsoftmarketplacenotifications"></a>Microsoft.MarketplaceNotifications

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | reviewsnotifications | いいえ |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | agreements | いいえ |
> | offertypes | いいえ |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | mediaservices | はい |
> | mediaservices / accountFilters | いいえ |
> | mediaservices / assets | いいえ |
> | mediaservices / assets / assetFilters | いいえ |
> | mediaservices / contentKeyPolicies | いいえ |
> | mediaservices / eventGridFilters | いいえ |
> | mediaservices / graphInstances | いいえ |
> | mediaservices / graphTopologies | いいえ |
> | mediaservices / liveEventOperations | いいえ |
> | mediaservices / liveEvents | はい |
> | mediaservices / liveEvents / liveOutputs | いいえ |
> | mediaservices / liveOutputOperations | いいえ |
> | mediaservices / mediaGraphs | いいえ |
> | mediaservices / privateEndpointConnectionOperations | いいえ |
> | mediaservices / privateEndpointConnectionProxies | いいえ |
> | mediaservices / privateEndpointConnections | いいえ |
> | mediaservices / streamingEndpointOperations | いいえ |
> | mediaservices / streamingEndpoints | はい |
> | mediaservices / streamingLocators | いいえ |
> | mediaservices / streamingPolicies | いいえ |
> | mediaservices / transforms | いいえ |
> | mediaservices / transforms / jobs | いいえ |
> | videoAnalyzers | はい |
> | videoAnalyzers / accessPolicies | いいえ |
> | videoAnalyzers / edgeModules | いいえ |
> | videoAnalyzers / livePipelines | いいえ |
> | videoAnalyzers / pipelineJobs | いいえ |
> | videoAnalyzers / pipelineTopologies | いいえ |
> | videoAnalyzers / videos | いいえ |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | assessmentProjects | はい |
> | migrateprojects | はい |
> | moveCollections | はい |
> | projects | はい |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | はい |
> | objectAnchorsAccounts | はい |
> | objectUnderstandingAccounts | はい |
> | remoteRenderingAccounts | はい |
> | spatialAnchorsAccounts | はい |

## <a name="microsoftmobilenetwork"></a>Microsoft.MobileNetwork

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | mobileNetworks | いいえ |
> | mobileNetworks / dataNetworks | いいえ |
> | mobileNetworks / services | いいえ |
> | mobileNetworks / simPolicies | いいえ |
> | mobileNetworks / sites | いいえ |
> | mobileNetworks / slices | いいえ |
> | networks | いいえ |
> | networks / sites | いいえ |
> | packetCoreControlPlanes | いいえ |
> | packetCoreControlPlanes / packetCoreDataPlanes | いいえ |
> | packetCoreControlPlanes / packetCoreDataPlanes / attachedDataNetworks | いいえ |
> | packetCores | いいえ |
> | sims | いいえ |
> | sims / simProfiles | いいえ |

## <a name="microsoftmonitor"></a>Microsoft.Monitor

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | netAppAccounts | はい |
> | netAppAccounts / accountBackups | いいえ |
> | netAppAccounts / capacityPools | はい |
> | netAppAccounts / capacityPools / volumes | はい |
> | netAppAccounts / capacityPools / volumes / snapshots | いいえ |
> | netAppAccounts / capacityPools / volumes / subvolumes | いいえ |
> | netAppAccounts / snapshotPolicies | はい |
> | netAppAccounts / volumeGroups | いいえ |

## <a name="microsoftnetworkfunction"></a>Microsoft.NetworkFunction

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | azureTrafficCollectors | はい |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | applicationGateways | はい |
> | applicationGatewayWebApplicationFirewallPolicies | はい |
> | applicationSecurityGroups | はい |
> | azureFirewallFqdnTags | いいえ |
> | azureFirewalls | はい |
> | bastionHosts | はい |
> | bgpServiceCommunities | いいえ |
> | connections | はい |
> | ddosCustomPolicies | はい |
> | ddosProtectionPlans | はい |
> | dnsOperationStatuses | いいえ |
> | dnszones | はい |
> | dnszones / A | いいえ |
> | dnszones / AAAA | いいえ |
> | dnszones / all | いいえ |
> | dnszones / CAA | いいえ |
> | dnszones / CNAME | いいえ |
> | dnszones / MX | いいえ |
> | dnszones / NS | いいえ |
> | dnszones / PTR | いいえ |
> | dnszones / recordsets | いいえ |
> | dnszones / SOA | いいえ |
> | dnszones / SRV | いいえ |
> | dnszones / TXT | いいえ |
> | expressRouteCircuits | はい |
> | expressRouteCrossConnections | はい |
> | expressRouteGateways | はい |
> | expressRoutePorts | はい |
> | expressRouteServiceProviders | いいえ |
> | firewallPolicies | はい |
> | frontdoors | はい |
> | frontdoorWebApplicationFirewallManagedRuleSets | いいえ |
> | frontdoorWebApplicationFirewallPolicies | はい |
> | getDnsResourceReference | いいえ |
> | internalNotify | いいえ |
> | ipGroups | はい |
> | loadBalancers | はい |
> | localNetworkGateways | はい |
> | natGateways | はい |
> | networkIntentPolicies | はい |
> | networkInterfaces | はい |
> | networkProfiles | はい |
> | networkSecurityGroups | はい |
> | networkWatchers | はい |
> | networkWatchers / connectionMonitors | はい |
> | networkWatchers / flowLogs | はい |
> | networkWatchers / lenses | はい |
> | networkWatchers / pingMeshes | はい |
> | p2sVpnGateways | はい |
> | privateDnsOperationStatuses | いいえ |
> | privateDnsZones | はい |
> | privateDnsZones / A | いいえ |
> | privateDnsZones / AAAA | いいえ |
> | privateDnsZones / all | いいえ |
> | privateDnsZones / CNAME | いいえ |
> | privateDnsZones / MX | いいえ |
> | privateDnsZones / PTR | いいえ |
> | privateDnsZones / SOA | いいえ |
> | privateDnsZones / SRV | いいえ |
> | privateDnsZones / TXT | いいえ |
> | privateDnsZones / virtualNetworkLinks | はい |
> | privateEndpoints | はい |
> | privateLinkServices | はい |
> | publicIPAddresses | はい |
> | publicIPPrefixes | はい |
> | routeFilters | はい |
> | routeTables | はい |
> | serviceEndpointPolicies | はい |
> | trafficManagerGeographicHierarchies | いいえ |
> | trafficmanagerprofiles | はい |
> | trafficmanagerprofiles / heatMaps | いいえ |
> | trafficManagerUserMetricsKeys | いいえ |
> | virtualHubs | はい |
> | virtualNetworkGateways | はい |
> | virtualNetworks | はい |
> | virtualNetworks / subnets | いいえ |
> | virtualNetworkTaps | はい |
> | virtualWans | はい |
> | vpnGateways | はい |
> | vpnSites | はい |
> | webApplicationFirewallPolicies | はい |

## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | NotebookProxies | いいえ |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | namespaces | はい |
> | namespaces / notificationHubs | はい |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | osNamespaces | いいえ |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | HyperVSites | はい |
> | ImportSites | はい |
> | MasterSites | はい |
> | ServerSites | はい |
> | VMwareSites | はい |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | clusters | はい |
> | deletedWorkspaces | いいえ |
> | linkTargets | いいえ |
> | querypacks | はい |
> | storageInsightConfigs | いいえ |
> | workspaces | はい |
> | workspaces / dataExports | いいえ |
> | workspaces / dataSources | いいえ |
> | workspaces / linkedServices | いいえ |
> | workspaces / linkedStorageAccounts | いいえ |
> | workspaces / metadata | いいえ |
> | workspaces / query | いいえ |
> | workspaces / scopedPrivateLinkProxies | いいえ |
> | workspaces / storageInsightConfigs | いいえ |
> | workspaces / tables | いいえ |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | managementassociations | いいえ |
> | managementconfigurations | はい |
> | solutions | はい |
> | views | はい |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | cdnPeeringPrefixes | いいえ |
> | legacyPeerings | いいえ |
> | lookingGlass | いいえ |
> | peerAsns | いいえ |
> | peerings | はい |
> | peeringServiceCountries | いいえ |
> | peeringServiceProviders | いいえ |
> | peeringServices | はい |

## <a name="microsoftplayfab"></a>Microsoft.PlayFab

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | PlayerAccountPools | いいえ |
> | タイトル | いいえ |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | attestations | いいえ |
> | eventGridFilters | いいえ |
> | policyEvents | いいえ |
> | policyMetadata | いいえ |
> | policyStates | いいえ |
> | policyTrackedResources | いいえ |
> | remediations | いいえ |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | consoles | いいえ |
> | dashboards | はい |
> | tenantconfigurations | いいえ |
> | userSettings | いいえ |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | privateLinkServicesForPowerBI | はい |
> | tenants | はい |
> | tenants / workspaces | いいえ |
> | workspaceCollections | はい |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | autoScaleVCores | はい |
> | capacities | はい |
> | servers | はい |

## <a name="microsoftpowerplatform"></a>Microsoft.PowerPlatform

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい |
> | enterprisePolicies | はい |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい |
> | deletedAccounts | いいえ |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | providerRegistrations | いいえ |
> | providerRegistrations / customRollouts | いいえ |
> | providerRegistrations / defaultRollouts | いいえ |
> | providerRegistrations / resourceActions | いいえ |
> | providerRegistrations / resourceTypeRegistrations | いいえ |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい |
> | deletedAccounts | いいえ |
> | getDefaultAccount | いいえ |
> | removeDefaultAccount | いいえ |
> | setDefaultAccount | いいえ |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | Workspaces | いいえ |

## <a name="microsoftquota"></a>Microsoft.Quota

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | quotaRequests | いいえ |
> | quotas | いいえ |
> | usages | いいえ |

## <a name="microsoftrecommendationsservice"></a>Microsoft.RecommendationsService

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | いいえ |
> | accounts / modeling | いいえ |
> | accounts / serviceEndpoints | いいえ |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | backupProtectedItems | いいえ |
> | vaults | はい |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | OpenShiftClusters | はい |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | namespaces | はい |
> | namespaces / authorizationrules | いいえ |
> | namespaces / hybridconnections | いいえ |
> | namespaces / hybridconnections / authorizationrules | いいえ |
> | namespaces / privateEndpointConnections | いいえ |
> | namespaces / wcfrelays | いいえ |
> | namespaces / wcfrelays / authorizationrules | いいえ |

## <a name="microsoftresourceconnector"></a>Microsoft.ResourceConnector

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | appliances | はい |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | Query | はい |
> | resourceChangeDetails | いいえ |
> | resourceChanges | いいえ |
> | resources | いいえ |
> | resourcesHistory | いいえ |
> | subscriptionsStatus | いいえ |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | availabilityStatuses | いいえ |
> | childAvailabilityStatuses | いいえ |
> | childResources | いいえ |
> | emergingissues | いいえ |
> | events | いいえ |
> | impactedResources | いいえ |
> | metadata | いいえ |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | deployments | いいえ |
> | deployments / operations | いいえ |
> | deploymentScripts | はい |
> | deploymentScripts / logs | いいえ |
> | deploymentStacks | いいえ |
> | deploymentStacks / snapshots | いいえ |
> | リンク | いいえ |
> | providers | いいえ |
> | resourceGroups | いいえ |
> | subscriptions | いいえ |
> | templateSpecs | はい |
> | templateSpecs / versions | はい |
> | tenants | いいえ |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | applications | はい |
> | resources | はい |
> | saasresources | いいえ |

## <a name="microsoftscom"></a>Microsoft.Scom

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | managedInstances | いいえ |

## <a name="microsoftscvmm"></a>Microsoft.ScVmm

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | clouds | いいえ |
> | VirtualMachines | いいえ |
> | VirtualMachineTemplates | いいえ |
> | VirtualNetworks | いいえ |
> | vmmservers | いいえ |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | resourceHealthMetadata | いいえ |
> | searchServices | はい |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | いいえ |
> | advancedThreatProtectionSettings | いいえ |
> | alerts | いいえ |
> | alertsSuppressionRules | いいえ |
> | allowedConnections | いいえ |
> | antiMalwareSettings | いいえ |
> | applicationWhitelistings | いいえ |
> | assessmentMetadata | いいえ |
> | assessments | いいえ |
> | 割り当て | はい |
> | autoDismissAlertsRules | いいえ |
> | automations | はい |
> | AutoProvisioningSettings | いいえ |
> | Compliances | いいえ |
> | connectedContainerRegistries | いいえ |
> | connectors | いいえ |
> | customAssessmentAutomations | はい |
> | customEntityStoreAssignments | はい |
> | dataCollectionAgents | いいえ |
> | deviceSecurityGroups | いいえ |
> | discoveredSecuritySolutions | いいえ |
> | externalSecuritySolutions | いいえ |
> | InformationProtectionPolicies | いいえ |
> | ingestionSettings | いいえ |
> | insights | いいえ |
> | iotSecuritySolutions | はい |
> | iotSecuritySolutions / analyticsModels | いいえ |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | いいえ |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | いいえ |
> | iotSecuritySolutions / iotAlerts | いいえ |
> | iotSecuritySolutions / iotAlertTypes | いいえ |
> | iotSecuritySolutions / iotRecommendations | いいえ |
> | iotSecuritySolutions / iotRecommendationTypes | いいえ |
> | jitNetworkAccessPolicies | いいえ |
> | jitPolicies | いいえ |
> | policies | いいえ |
> | pricings | いいえ |
> | regulatoryComplianceStandards | いいえ |
> | regulatoryComplianceStandards / regulatoryComplianceControls | いいえ |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | いいえ |
> | secureScoreControlDefinitions | いいえ |
> | secureScoreControls | いいえ |
> | secureScores | いいえ |
> | secureScores / secureScoreControls | いいえ |
> | securityConnectors | はい |
> | securityContacts | いいえ |
> | securitySolutions | いいえ |
> | securitySolutionsReferenceData | いいえ |
> | securityStatuses | いいえ |
> | securityStatusesSummaries | いいえ |
> | serverVulnerabilityAssessments | いいえ |
> | settings | いいえ |
> | sqlVulnerabilityAssessments | いいえ |
> | 規格 | はい |
> | subAssessments | いいえ |
> | tasks | いいえ |
> | topologies | いいえ |
> | workspaceSettings | いいえ |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | diagnosticSettings | いいえ |
> | diagnosticSettingsCategories | いいえ |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | aggregations | いいえ |
> | alertRules | いいえ |
> | alertRuleTemplates | いいえ |
> | automationRules | いいえ |
> | bookmarks | いいえ |
> | cases | いいえ |
> | dataConnectors | いいえ |
> | dataConnectorsCheckRequirements | いいえ |
> | enrichment | いいえ |
> | entities | いいえ |
> | entityQueries | いいえ |
> | entityQueryTemplates | いいえ |
> | incidents | いいえ |
> | metadata | いいえ |
> | officeConsents | いいえ |
> | onboardingStates | いいえ |
> | settings | いいえ |
> | sourceControls | いいえ |
> | threatIntelligence | いいえ |
> | watchlists | いいえ |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | consoleServices | いいえ |
> | serialPorts | いいえ |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | namespaces | はい |
> | namespaces / authorizationrules | いいえ |
> | namespaces / disasterrecoveryconfigs | いいえ |
> | namespaces / eventgridfilters | いいえ |
> | namespaces / networkrulesets | いいえ |
> | namespaces / privateEndpointConnections | いいえ |
> | namespaces / queues | いいえ |
> | namespaces / queues / authorizationrules | いいえ |
> | namespaces / topics | いいえ |
> | namespaces / topics / authorizationrules | いいえ |
> | namespaces / topics / subscriptions | いいえ |
> | namespaces / topics / subscriptions / rules | いいえ |
> | premiumMessagingRegions | いいえ |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | applications | はい |
> | clusters | はい |
> | clusters/applications | いいえ |
> | containerGroups | はい |
> | containerGroupSets | はい |
> | edgeclusters | はい |
> | edgeclusters / applications | いいえ |
> | managedclusters | はい |
> | managedclusters / applications | いいえ |
> | managedclusters / applications / services | いいえ |
> | managedclusters / applicationTypes | いいえ |
> | managedclusters / applicationTypes / versions | いいえ |
> | managedclusters / nodetypes | いいえ |
> | networks | はい |
> | secretstores | はい |
> | secretstores / certificates | いいえ |
> | secretstores / secrets | いいえ |
> | volumes | はい |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | applications | はい |
> | containerGroups | はい |
> | gateways | はい |
> | networks | はい |
> | secrets | はい |
> | volumes | はい |

## <a name="microsoftservicelinker"></a>Microsoft.ServiceLinker

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | linkers | いいえ |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | providerRegistrations | いいえ |
> | providerRegistrations / resourceTypeRegistrations | いいえ |
> | rollouts | はい |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | SignalR | はい |
> | SignalR / eventGridFilters | いいえ |
> | WebPubSub | はい |

## <a name="microsoftsingularity"></a>Microsoft.Singularity

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい |
> | accounts / accountQuotaPolicies | いいえ |
> | accounts / groupPolicies | いいえ |
> | accounts / jobs | いいえ |
> | accounts / models | いいえ |
> | accounts / storageContainers | いいえ |
> | images | いいえ |
> | quotas | いいえ |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | hybridUseBenefits | いいえ |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | applicationDefinitions | はい |
> | applications | はい |
> | jitRequests | はい |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | managedInstances | はい |
> | managedInstances / databases | はい |
> | managedInstances / databases / backupShortTermRetentionPolicies | いいえ |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | いいえ |
> | managedInstances / databases / vulnerabilityAssessments | いいえ |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | いいえ |
> | managedInstances / encryptionProtector | いいえ |
> | managedInstances / keys | いいえ |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | いいえ |
> | managedInstances / vulnerabilityAssessments | いいえ |
> | servers | はい |
> | servers / administrators | いいえ |
> | servers / communicationLinks | いいえ |
> | servers/databases | はい |
> | servers / encryptionProtector | いいえ |
> | servers / firewallRules | いいえ |
> | servers / keys | いいえ |
> | servers / restorableDroppedDatabases | いいえ |
> | servers / serviceobjectives | いいえ |
> | servers / tdeCertificates | いいえ |
> | virtualClusters | いいえ |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | はい |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | いいえ |
> | SqlVirtualMachines | はい |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | deletedAccounts | いいえ |
> | storageAccounts | はい |
> | storageAccounts / blobServices | いいえ |
> | storageAccounts / encryptionScopes | いいえ |
> | storageAccounts / fileServices | いいえ |
> | storageAccounts / queueServices | いいえ |
> | storageAccounts / services | いいえ |
> | storageAccounts / services / metricDefinitions | いいえ |
> | storageAccounts / tableServices | いいえ |
> | usages | いいえ |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | amlFilesystems | はい |
> | caches | はい |
> | caches / storageTargets | いいえ |
> | usageModels | いいえ |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | replicationGroups | いいえ |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | storageSyncServices | はい |
> | storageSyncServices / registeredServers | いいえ |
> | storageSyncServices / syncGroups | いいえ |
> | storageSyncServices / syncGroups / cloudEndpoints | いいえ |
> | storageSyncServices / syncGroups / serverEndpoints | いいえ |
> | storageSyncServices / workflows | いいえ |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | managers | はい |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | clusters | はい |
> | clusters / privateEndpoints | いいえ |
> | streamingjobs | はい |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | acceptChangeTenant | いいえ |
> | acceptOwnership | いいえ |
> | acceptOwnershipStatus | いいえ |
> | aliases | いいえ |
> | cancel | いいえ |
> | changeTenantRequest | いいえ |
> | changeTenantStatus | いいえ |
> | CreateSubscription | いいえ |
> | 有効化 (enable) | いいえ |
> | policies | いいえ |
> | rename | いいえ |
> | SubscriptionDefinitions | いいえ |
> | SubscriptionOperations | いいえ |
> | subscriptions | いいえ |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | kustoOperations | いいえ |
> | privateLinkHubs | はい |
> | workspaces | はい |
> | workspaces / bigDataPools | はい |
> | workspaces / kustoPools | はい |
> | workspaces / kustoPools / attacheddatabaseconfigurations | いいえ |
> | workspaces / kustoPools / databases | いいえ |
> | workspaces / kustoPools / databases / dataconnections | いいえ |
> | workspaces / operationStatuses | いいえ |
> | workspaces / sqlDatabases | はい |
> | workspaces / sqlPools | はい |

## <a name="microsofttestbase"></a>Microsoft.TestBase

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | testBaseAccounts | いいえ |
> | testBaseAccounts / customerEvents | いいえ |
> | testBaseAccounts / emailEvents | いいえ |
> | testBaseAccounts / flightingRings | いいえ |
> | testBaseAccounts / packages | いいえ |
> | testBaseAccounts / packages / favoriteProcesses | いいえ |
> | testBaseAccounts / packages / osUpdates | いいえ |
> | testBaseAccounts / testSummaries | いいえ |
> | testBaseAccounts / testTypes | いいえ |
> | testBaseAccounts / usages | いいえ |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | 環境 | はい |
> | environments / accessPolicies | いいえ |
> | environments/eventsources | はい |
> | environments / privateEndpointConnectionProxies | いいえ |
> | environments / privateEndpointConnections | いいえ |
> | environments / privateLinkResources | いいえ |
> | environments / referenceDataSets | はい |

## <a name="microsoftvideoindexer"></a>Microsoft.VideoIndexer

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | いいえ |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | imageTemplates | はい |
> | imageTemplates / runOutputs | いいえ |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | arczones | いいえ |
> | resourcepools | いいえ |
> | vcenters | いいえ |
> | VCenters / InventoryItems | いいえ |
> | virtualmachines | いいえ |
> | virtualmachinetemplates | いいえ |
> | virtualnetworks | いいえ |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | dedicatedCloudNodes | はい |
> | dedicatedCloudServices | はい |
> | virtualMachines | はい |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい |
> | plans | はい |
> | registeredSubscriptions | いいえ |
## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | apiManagementAccounts | いいえ |
> | apiManagementAccounts / apiAcls | いいえ |
> | apiManagementAccounts / apis | いいえ |
> | apiManagementAccounts / apis / apiAcls | いいえ |
> | apiManagementAccounts / apis / connectionAcls | いいえ |
> | apiManagementAccounts / apis / connections | いいえ |
> | apiManagementAccounts / apis / connections / connectionAcls | いいえ |
> | apiManagementAccounts / apis / localizedDefinitions | いいえ |
> | apiManagementAccounts / connectionAcls | いいえ |
> | apiManagementAccounts / connections | いいえ |
> | billingMeters | いいえ |
> | certificates | はい |
> | connectionGateways | はい |
> | connections | はい |
> | customApis | はい |
> | deletedSites | いいえ |
> | functionAppStacks | いいえ |
> | generateGithubAccessTokenForAppserviceCLI | いいえ |
> | hostingEnvironments | はい |
> | hostingEnvironments / eventGridFilters | いいえ |
> | hostingEnvironments / multiRolePools | いいえ |
> | hostingEnvironments / workerPools | いいえ |
> | kubeEnvironments | はい |
> | publishingUsers | いいえ |
> | recommendations | いいえ |
> | resourceHealthMetadata | いいえ |
> | runtimes | いいえ |
> | serverFarms | はい |
> | serverFarms / eventGridFilters | いいえ |
> | serverFarms / firstPartyApps | いいえ |
> | serverFarms / firstPartyApps / keyVaultSettings | いいえ |
> | sites | はい |
> | sites/config  | いいえ |
> | sites / eventGridFilters | いいえ |
> | sites / hostNameBindings | いいえ |
> | sites / networkConfig | いいえ |
> | sites/premieraddons | はい |
> | sites/slots | はい |
> | sites / slots / eventGridFilters | いいえ |
> | sites / slots / hostNameBindings | いいえ |
> | sites / slots / networkConfig | いいえ |
> | sourceControls | いいえ |
> | staticSites | はい |
> | validate | いいえ |
> | verifyHostingEnvironmentVnet | いいえ |
> | webAppStacks | いいえ |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | diagnosticSettings | いいえ |
> | diagnosticSettingsCategories | いいえ |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | multipleActivationKeys | はい |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | DeviceServices | はい |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | migrationAgents | いいえ |
> | workloads | いいえ |
> | workloads / instances | いいえ |
> | workloads / versions | いいえ |
> | workloads / versions / artifacts | いいえ |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | monitors | いいえ |

## <a name="next-steps"></a>次のステップ

コンマ区切りの値のファイルと同じデータを取得するには、[complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv) をダウンロードします。