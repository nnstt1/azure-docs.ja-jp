---
title: クラウドにおける共同責任 - Microsoft Azure
description: 共同責任モデル、クラウド プロバイダーが処理するセキュリティ タスク、およびお客様が処理するタスクについて説明します。
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: na
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2021
ms.author: terrylan
ms.openlocfilehash: 14d53d3f58c1bbdb03a5ac510f6a2e44157906f9
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272115"
---
# <a name="shared-responsibility-in-the-cloud"></a>クラウドにおける共同責任

パブリック クラウド サービスを検討して評価する際には、共同責任モデル、クラウド プロバイダーが処理するセキュリティ タスク、およびお客様が処理するタスクを理解することが重要です。 ワークロードの責任は、ワークロードがサービスとしてのソフトウェア (SaaS)、サービスとしてのプラットフォーム (PaaS)、サービスとしてのインフラストラクチャ (IaaS)、またはオンプレミスのデータセンターのいずれでホストされるかによって異なります。

## <a name="division-of-responsibility"></a>責任の分担
オンプレミスのデータセンターでは、お客様がスタック全体を所有します。 クラウドに移行すると、責任の一部は Microsoft に移譲されます。 次の図は、スタックのデプロイの種類に応じて、お客様と Microsoft の間の責任の範囲を示しています。

:::image type="content" source="media/shared-responsibility/shared-responsibility.svg" alt-text="責任ゾーンを示す図。" border="false":::

すべてのクラウド デプロイの種類において、データと ID を所有するのはあなたです。 お客様にはデータと ID、オンプレミス リソース、お客様が制御するクラウド コンポーネント (サービスの種類によって異なります) を保護する責任があります。

デプロイの種類に関係なく、次の責任はお客様が必ず負います。

- データ
- エンドポイント
- Account
- アクセス管理

## <a name="cloud-security-advantages"></a>クラウド セキュリティの利点
クラウドは、長年の情報セキュリティの課題を解決するための大きな利点を提供します。 オンプレミス環境では、組織は責任を果たしきれず、セキュリティに投資できるリソースが限られていることが多いため、攻撃者がすべての階層で脆弱性を悪用できる環境が生まれています。

次の図は、リソースが限られているために多くのセキュリティ責任が満たされていない従来のアプローチを示しています。 クラウド対応のアプローチでは、日々のセキュリティ責任をクラウド プロバイダーにシフトし、お客様のリソースを再割り当てすることができます。

:::image type="content" source="media/shared-responsibility/cloud-enabled-security.svg" alt-text="クラウド時代のセキュリティ上の利点を示す図。" border="false":::

クラウド対応のアプローチでは、クラウドベースのセキュリティ機能を活用して有効性を高め、クラウド インテリジェンスを使用して脅威の検出と応答時間を向上させることもできます。 クラウド プロバイダーに責任をシフトすることで、セキュリティの適用範囲を広げることができるため、これまでセキュリティに費やしてきたリソースと予算を、事業のその他の優先事項に割り当てることができます。

## <a name="next-steps"></a>次のステップ
SaaS、PaaS、IaaS のデプロイにおけるお客様と Microsoft との責任の分担の詳細については、「[クラウド コンピューティングについての責任共有](https://azure.microsoft.com/resources/shared-responsibility-for-cloud-computing/)」を参照してください。
