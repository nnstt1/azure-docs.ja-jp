---
title: 拡張リソースの種類
description: 他の種類のリソースの機能を拡張する際に使用される Azure リソースの種類一覧です。
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: af72233f9e9f6dcbfb15a8b9ffc97b737bf5b924
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229939"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>他のリソースの機能を拡張するリソースの種類

拡張リソースとは、他のリソースに機能を追加するリソースです。 拡張リソースの例として、リソース ロックが挙げられます。 リソース ロックを他のリソースに適用することで、そのリソースが削除されたり改変されたりするのを防ぎます。 リソース ロックを単体で作成しても意味はありません。 拡張リソースは常に別のリソースに適用されます。

## <a name="microsoftadvisor"></a>Microsoft.Advisor

- Microsoft.Advisor/configurations
- Microsoft.Advisor/recommendations
- Microsoft.Advisor/suppressions

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

- Microsoft.AlertsManagement/alerts

## <a name="microsoftauthorization"></a>Microsoft.Authorization

- Microsoft.Authorization/batchResourceCheckAccess
- Microsoft.Authorization/denyAssignments
- Microsoft.Authorization/eligibleChildResources
- Microsoft.Authorization/locks
- Microsoft.Authorization/policyAssignments
- Microsoft.Authorization/policyDefinitions
- Microsoft.Authorization/policyExemptions
- Microsoft.Authorization/policySetDefinitions
- Microsoft.Authorization/privateLinkAssociations
- Microsoft.Authorization/roleAssignmentApprovals
- Microsoft.Authorization/roleAssignments
- Microsoft.Authorization/roleAssignmentScheduleInstances
- Microsoft.Authorization/roleAssignmentScheduleRequests
- Microsoft.Authorization/roleAssignmentSchedules
- Microsoft.Authorization/roleDefinitions
- Microsoft.Authorization/roleEligibilityScheduleInstances
- Microsoft.Authorization/roleEligibilityScheduleRequests
- Microsoft.Authorization/roleEligibilitySchedules
- Microsoft.Authorization/roleManagementPolicies
- Microsoft.Authorization/roleManagementPolicyAssignments

## <a name="microsoftautomanage"></a>Microsoft.Automanage

- Microsoft.Automanage/configurationProfileAssignmentIntents
- Microsoft.Automanage/configurationProfileAssignments

## <a name="microsoftbilling"></a>Microsoft.Billing

- Microsoft.Billing/billingPeriods
- Microsoft.Billing/billingPermissions
- Microsoft.Billing/billingRoleAssignments
- Microsoft.Billing/billingRoleDefinitions
- Microsoft.Billing/createBillingRoleAssignment

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

- Microsoft.Blueprint/blueprintAssignments
- Microsoft.Blueprint/blueprints

## <a name="microsoftcapacity"></a>Microsoft.Capacity

- Microsoft.Capacity/listSkus

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

- Microsoft.ChangeAnalysis/changes
- Microsoft.ChangeAnalysis/changeSnapshots
- Microsoft.ChangeAnalysis/computeChanges

## <a name="microsoftconsumption"></a>Microsoft.Consumption

- Microsoft.Consumption/AggregatedCost
- Microsoft.Consumption/Balances
- Microsoft.Consumption/Budgets
- Microsoft.Consumption/Charges
- Microsoft.Consumption/CostTags
- Microsoft.Consumption/credits
- Microsoft.Consumption/events
- Microsoft.Consumption/Forecasts
- Microsoft.Consumption/lots
- Microsoft.Consumption/Marketplaces
- Microsoft.Consumption/Pricesheets
- Microsoft.Consumption/products
- Microsoft.Consumption/ReservationDetails
- Microsoft.Consumption/ReservationRecommendationDetails
- Microsoft.Consumption/ReservationRecommendations
- Microsoft.Consumption/ReservationSummaries
- Microsoft.Consumption/ReservationTransactions

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

- Microsoft.ContainerInstance/serviceAssociationLinks

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

- Microsoft.CostManagement/Alerts
- Microsoft.CostManagement/Budgets
- Microsoft.CostManagement/CheckNameAvailability
- Microsoft.CostManagement/Dimensions
- Microsoft.CostManagement/Exports
- Microsoft.CostManagement/ExternalSubscriptions
- Microsoft.CostManagement/Forecast
- Microsoft.CostManagement/GenerateDetailedCostReport
- Microsoft.CostManagement/Insights
- Microsoft.CostManagement/OperationResults
- Microsoft.CostManagement/OperationStatus
- Microsoft.CostManagement/Query
- Microsoft.CostManagement/Reportconfigs
- Microsoft.CostManagement/Reports
- Microsoft.CostManagement/ScheduledActions
- Microsoft.CostManagement/Views

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

- Microsoft.CustomProviders/associations

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

- Microsoft.DataMigration/DatabaseMigrations

## <a name="microsoftdiagnostics"></a>Microsoft.Diagnostics

- Microsoft.Diagnostics/InsightDiagnostics
- Microsoft.Diagnostics/solutions

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

- Microsoft.GuestConfiguration/configurationProfileAssignments
- Microsoft.GuestConfiguration/guestConfigurationAssignments
- Microsoft.GuestConfiguration/software

## <a name="microsofthybridconnectivity"></a>Microsoft.HybridConnectivity

- Microsoft.HybridConnectivity/endpoints

## <a name="microsoftinsights"></a>microsoft.insights

- microsoft.insights/baseline
- microsoft.insights/dataCollectionRuleAssociations
- microsoft.insights/diagnosticSettings
- microsoft.insights/diagnosticSettingsCategories
- microsoft.insights/eventtypes
- microsoft.insights/extendedDiagnosticSettings
- microsoft.insights/guestDiagnosticSettingsAssociation
- microsoft.insights/logDefinitions
- microsoft.insights/logs
- microsoft.insights/metricbaselines
- microsoft.insights/metricDefinitions
- microsoft.insights/metricNamespaces
- microsoft.insights/metrics
- microsoft.insights/myWorkbooks
- microsoft.insights/topology
- microsoft.insights/transactions

## <a name="microsoftiotsecurity"></a>Microsoft.IoTSecurity

- Microsoft.IoTSecurity/sensors
- Microsoft.IoTSecurity/sites

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

- Microsoft.KubernetesConfiguration/extensions
- Microsoft.KubernetesConfiguration/fluxConfigurations
- Microsoft.KubernetesConfiguration/sourceControlConfigurations

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

- Microsoft.Maintenance/applyUpdates
- Microsoft.Maintenance/configurationAssignments
- Microsoft.Maintenance/updates

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

- Microsoft.ManagedIdentity/Identities

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

- Microsoft.ManagedServices/registrationAssignments
- Microsoft.ManagedServices/registrationDefinitions

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

- Microsoft.OperationalInsights/storageInsightConfigs

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

- Microsoft.OperationsManagement/managementassociations

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

- Microsoft.PolicyInsights/attestations
- Microsoft.PolicyInsights/eventGridFilters
- Microsoft.PolicyInsights/policyEvents
- Microsoft.PolicyInsights/policyStates
- Microsoft.PolicyInsights/policyTrackedResources
- Microsoft.PolicyInsights/remediations

## <a name="microsoftquota"></a>Microsoft.Quota

- Microsoft.Quota/operationsStatus
- Microsoft.Quota/quotaRequests
- Microsoft.Quota/quotas
- Microsoft.Quota/usages

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

- Microsoft.RecoveryServices/backupProtectedItems
- Microsoft.RecoveryServices/replicationEligibilityResults

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

- Microsoft.ResourceHealth/childResources
- Microsoft.ResourceHealth/events
- Microsoft.ResourceHealth/impactedResources

## <a name="microsoftresources"></a>Microsoft.Resources

- Microsoft.Resources/links
- Microsoft.Resources/tags

## <a name="microsoftsecurity"></a>Microsoft.Security

- Microsoft.Security/adaptiveNetworkHardenings
- Microsoft.Security/advancedThreatProtectionSettings
- Microsoft.Security/antiMalwareSettings
- Microsoft.Security/assessmentMetadata
- Microsoft.Security/assessments
- Microsoft.Security/Compliances
- Microsoft.Security/dataCollectionAgents
- Microsoft.Security/deviceSecurityGroups
- Microsoft.Security/InformationProtectionPolicies
- Microsoft.Security/insights
- Microsoft.Security/jitPolicies
- Microsoft.Security/serverVulnerabilityAssessments
- Microsoft.Security/sqlVulnerabilityAssessments

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

- Microsoft.SecurityInsights/aggregations
- Microsoft.SecurityInsights/alertRules
- Microsoft.SecurityInsights/alertRuleTemplates
- Microsoft.SecurityInsights/automationRules
- Microsoft.SecurityInsights/bookmarks
- Microsoft.SecurityInsights/cases
- Microsoft.SecurityInsights/dataConnectors
- Microsoft.SecurityInsights/dataConnectorsCheckRequirements
- Microsoft.SecurityInsights/enrichment
- Microsoft.SecurityInsights/entities
- Microsoft.SecurityInsights/entityQueryTemplates
- Microsoft.SecurityInsights/incidents
- Microsoft.SecurityInsights/listrepositories
- Microsoft.SecurityInsights/metadata
- Microsoft.SecurityInsights/onboardingStates
- Microsoft.SecurityInsights/settings
- Microsoft.SecurityInsights/sourceControls
- Microsoft.SecurityInsights/threatIntelligence
- Microsoft.SecurityInsights/watchlists

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

- Microsoft.SerialConsole/serialPorts

## <a name="microsoftservicelinker"></a>Microsoft.ServiceLinker

- Microsoft.ServiceLinker/linkers

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

- Microsoft.SoftwarePlan/hybridUseBenefits

## <a name="microsoftsubscription"></a>Microsoft.Subscription

- Microsoft.Subscription/policies

## <a name="microsoftsupport"></a>microsoft.support

- microsoft.support/supporttickets

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

- Microsoft.WorkloadMonitor/monitors

## <a name="next-steps"></a>次のステップ

- Azure Resource Manager テンプレートで拡張リソースのリソース ID を取得するには、[extensionResourceId](../templates/template-functions-resource.md#extensionresourceid) を使用します。
- テンプレートで拡張リソースを作成する例については、[Event Grid のイベント サブスクリプション](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions)に関するページを参照してください。
