---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6bdba561efa24093527d9a2e39e4650b0442b20c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599615"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Data Lake Analytics の診断設定をイベント ハブにデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4daddf25-4823-43d4-88eb-2419eb6dcc08) |Data Lake Analytics の診断設定をデプロイして、この診断設定がない Data Lake Analytics が作成または更新されたときにリージョンのイベント ハブにストリーミングします。 |DeployIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DataLakeAnalytics_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Data Lake Analytics の診断設定を Log Analytics ワークスペースにデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd56a5a7c-72d7-42bc-8ceb-3baf4c0eae03) |Data Lake Analytics の診断設定をデプロイして、この診断設定がない Data Lake Analytics が作成または更新されたときにリージョンの Log Analytics ワークスペースにストリーミングします。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DataLakeAnalytics_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Data Lake Analytics のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
