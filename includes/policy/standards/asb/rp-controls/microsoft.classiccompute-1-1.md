---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 881aa8b3a148a2858ea09a3c77066d0c0b64ec93
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128908292"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[インターネットに接続する仮想マシンは、ネットワーク セキュリティ グループを使用して保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |ネットワーク セキュリティ グループ (NSG) を使用してアクセスを制限することにより、潜在的な脅威から仮想マシンを保護します。 NSG を使用してトラフィックを制御する方法の詳細については、[https://aka.ms/nsg-doc](../../../../../articles/virtual-network/network-security-groups-overview.md) を参照してください。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[仮想マシン上の IP 転送を無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |仮想マシンの NIC で IP 転送を有効にすると、そのマシンはその他の宛先へのトラフィックを受信できます。 IP 転送が必要な状況は (VM をネットワーク仮想アプライアンスとして使用する場合などに) 限られているため、ネットワーク セキュリティ チームはこのことを確認する必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[仮想マシンの管理ポートを閉じておく必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |リモート管理ポートが開かれているため、お使いの VM がインターネットベースの攻撃を受ける高レベルのリスクにさらされています。 これらの攻撃では、資格情報に対するブルート フォース攻撃を行ってマシンへの管理者アクセス権の取得を試みます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |