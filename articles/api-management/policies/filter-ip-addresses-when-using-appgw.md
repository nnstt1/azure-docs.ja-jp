---
title: API Management ポリシーのサンプル - Application Gateway を使用する際に IP アドレスでフィルター処理する
titleSuffix: Azure API Management
description: Azure API Management ポリシーのサンプル - アプリケーション ゲートウェイを使用する際に、要求 IP アドレスでフィルター処理する方法を示します。
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: joscot
ms.custom: fasttrack-new
ms.openlocfilehash: 0308d8ee4b2b23c578a804c981613338d682a9ae
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128590641"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>アプリケーション ゲートウェイを使用する際に要求 IP アドレスでフィルター処理する

この記事では、Azure API Management ポリシーのサンプルを示します。これは、アプリケーション ゲートウェイまたはその他の仲介手段を通じて API Management インスタンスにアクセスする際に、要求 IP アドレスでフィルター処理する方法を示します。 ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-reference.md)に関するページをご覧ください。

## <a name="policy"></a>ポリシー

コードを **inbound** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>次のステップ

APIM ポリシーの詳細については、以下をご覧ください。

+ [アクセス制限ポリシー](../api-management-access-restriction-policies.md)
+ [ポリシーのサンプル](../policy-reference.md)