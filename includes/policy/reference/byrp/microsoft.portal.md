---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 15e8728d83d55d79e5913c2b37d566b66dcfbd54
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593965"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[インライン コンテンツを含むマークダウン タイルを共有ダッシュボードに含めることはできない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |インライン コンテンツを含む共有ダッシュボードを Markdown タイルで作成することを禁止し、オンラインでホストされている Markdown ファイルとしてコンテンツを保存するようにします。 Markdown タイルでインライン コンテンツを使用した場合は、コンテンツの暗号化を管理できません。 独自のストレージを構成することによって、暗号化や二重暗号化を行うだけでなく、独自のキーを使用することもできます。 このポリシーを有効にすると、ユーザーは、2020-09-01-preview 以降のバージョンの共有ダッシュボード REST API を使用するよう制限されます。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
