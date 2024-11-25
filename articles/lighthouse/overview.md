---
title: Azure Lighthouse とは
description: サービス プロバイダーは Azure Lighthouse を通じて、自動化と効率を大規模に高めたマネージド サービスを顧客に提供することができます。
ms.date: 11/02/2021
ms.topic: overview
ms.openlocfilehash: 6279ef69c60c7a6d76fe0dfbea68934394c23c06
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343118"
---
# <a name="what-is-azure-lighthouse"></a>Azure Lighthouse とは

Azure Lighthouse により、マルチテナントの管理が実現し、リソース間でのスケーラビリティや自動化の向上と統制機能の強化が可能になります。

Azure Lighthouse を使用すれば、[Azure プラットフォームに組み込まれた包括的かつ信頼性の高いツール](concepts/architecture.md)を使用して、サービス プロバイダーがマネージド サービスを提供できるようになります。 お客様は、自社のテナントにアクセスできるユーザー、そのユーザーがアクセスできるリソース、および実行可能な操作の制御を維持できます。 複数のテナント間でリソースを管理する[エンタープライズ組織](concepts/enterprise.md)では、管理タスクを効率化するために Azure Lighthouse を使用することもできます。

[テナント間の管理エクスペリエンス](concepts/cross-tenant-management-experience.md)により、[Azure Policy](how-to/policy-at-scale.md)、[Microsoft Sentinel](how-to/manage-sentinel-workspaces.md)、[Azure Arc](how-to/manage-hybrid-infrastructure-arc.md) などの Azure サービスで、より効率的に作業を行うことができます。 ユーザーは[アクティビティ ログ](how-to/view-service-provider-activity.md)から他のユーザーが加えた変更と誰が実行したかを参照できます。これらは顧客のテナントに格納され、また、管理テナントのユーザーが表示できます。

![Azure Lighthouse の概要図](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>メリット

Azure Lighthouse は、サービス プロバイダーがマネージド サービスを効率的に構築および提供するのに役立ちます。 利点は次のとおりです。

- **大規模な管理**: 顧客エンゲージメントとライフサイクル操作によって顧客リソースの管理が容易になり、スケーラビリティが向上します。 既存の API、管理ツール、ワークフローを、配置されているリージョンにかかわらず、Azure の外部でホストされているマシンを含む委任されたリソースと共に使用できます。
- **顧客から見た可視性と制御の向上**: 顧客は、管理のために委任するスコープと許可されるアクセス許可を細かく制御できます。 顧客は、[サービス プロバイダーのアクションを監査](how-to/view-service-provider-activity.md)し、アクセス権をいつでも完全に削除できます。
- **包括的で一元化されたプラットフォーム ツール**: Azure Lighthouse は、既存のツールと API、[Azure Managed Applications](concepts/managed-applications.md)、[クラウド ソリューション プロバイダー プログラム (CSP)](concepts/cloud-solution-provider.md) などのパートナー プログラムと連動します。 この柔軟性によって、EA、CSP、従量課金制などの複数のライセンス モデルを含む、サービス プロバイダーの主要なシナリオに対応します。 Azure Lighthouse を既存のワークフローとアプリケーションに統合し、[パートナー ID をリンクする](how-to/partner-earned-credit.md)ことで、顧客エンゲージメントへの影響を追跡できます。

## <a name="capabilities"></a>機能

Azure Lighthouse には、エンゲージメントと管理を効率化するさまざまな方法が用意されています。

- **Azure の委任されたリソース管理**: コンテキストやコントロール プレーンを切り替えることなく、[貴社独自のテナント内から顧客の Azure リソースを安全に管理する](concepts/architecture.md)ことができます。 顧客のサブスクリプションとリソース グループは、アクセス権を必要に応じて削除する権限と共に、管理テナント内の特定のユーザーとロールに委任することができます。
- **新しい Azure portal エクスペリエンス**: Azure portal の [ **[マイ カスタマー]** ページ](how-to/view-manage-customers.md)で、テナント間の情報を確認できます。 それに対応する [ **[サービス プロバイダー]** ページ](how-to/view-manage-service-providers.md)で、顧客はそのサービス プロバイダーのアクセスを確認し、管理することができます。
- **Azure Resource Manager テンプレート**: ARM テンプレートを使用して、[委任された顧客リソースをオンボードし](how-to/onboard-customer.md)、[テナント間の管理タスクを実行する](samples/index.md)ことができます。
- **Azure Marketplace のマネージド サービス オファー**:プライベート プランまたはパブリック プランを通じて [貴社のサービスを顧客に提供し](concepts/managed-services-offers.md)、Azure Lighthouse に顧客を自動的にオンボードすることができます。

> [!TIP]
> サービス プロバイダーは、同様のプラン ([Microsoft 365 Lighthouse](/microsoft-365/lighthouse/m365-lighthouse-overview)) を利用して、Microsoft 365 ユーザーのオンボード、監視、管理を大規模に行うことができます。 Microsoft 365 Lighthouse は現在プレビュー段階です。

## <a name="pricing-and-availability"></a>価格と可用性

Azure Lighthouse を使用して Azure リソースを管理することに関して、追加コストは発生しません。 すべての Azure のお客様およびパートナーは、Azure Lighthouse を使用できます。

## <a name="cross-region-and-cloud-considerations"></a>リージョン間とクラウド間の考慮事項

Azure Lighthouse は、リージョンの境界を越えたサービスです。 別の[リージョン](../availability-zones/az-overview.md#regions)にある委任されたリソースを管理することができます。 ただし、[各国のクラウド](../active-directory/develop/authentication-national-cloud.md)と Azure パブリック クラウドにわたって行われる、または 2 つの独立した国内クラウドにわたって行われるサブスクリプションの委任はサポートされていません。

## <a name="support-for-azure-lighthouse"></a>Azure Lighthouse のサポート

Azure Lighthouse の使用についてご不明な点があれば、Azure portal から[サポート リクエスト](..//azure-portal/supportability/how-to-create-azure-support-request.md)を送信してください。 **[問題の種類]** では、 **[技術]** を選択します。 サブスクリプションを選択し、 **[Lighthouse]** ( **[管理と監視]** の下) を選択します。

## <a name="next-steps"></a>次のステップ

- [技術レベルでの Azure Lighthouse のしくみ](concepts/architecture.md)について参照してください。
- [テナント間の管理エクスペリエンス](concepts/cross-tenant-management-experience.md)について調査します。
- [エンタープライズ内で Azure Lighthouse を使用する](concepts/enterprise.md)方法を確認します。
- Azure Lighthouse の[提供状況](https://azure.microsoft.com/global-infrastructure/services/?products=azure-lighthouse&regions=all)と [FedRAMP および DoD CC SRG 監査スコープ](../azure-government/compliance/azure-services-in-fedramp-auditscope.md)の詳細を確認します。
