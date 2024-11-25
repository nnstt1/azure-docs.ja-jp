---
title: コストの計画と管理
description: Azure portal でコスト分析を使用して、Azure SQL Database のコストを計画および管理する方法について説明します。
author: MashaMSFT
ms.author: mathoma
ms.custom: subject-cost-optimization
ms.service: sql-database
ms.subservice: service-overview
ms.topic: how-to
ms.date: 06/30/2021
ms.openlocfilehash: 5f1464c6fb74bc04dacb230cd2a5993f7566e82a
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546882"
---
# <a name="plan-and-manage-costs-for-azure-sql-database"></a>Azure SQL Database のコストを計画および管理する

この記事では、Azure SQL Database のコストを計画および管理する方法について説明します。 

最初に、Azure 料金計算ツールを使用して Azure リソースを追加し、推定コストを確認します。 Azure SQL Database リソースの使用を開始した後、コスト管理機能を使用して、予算を設定し、コストを監視します。 また、予想コストを確認し、支出の傾向を特定して、対処が必要な領域を特定することもできます。Azure SQL Database のコストは、Azure で課金される月額料金の一部でしかありません。 この記事では、Azure SQL Database のコストを計画して管理する方法について説明しますが、課金は、Azure サブスクリプションで使用されるすべての Azure サービスとリソース (サードパーティのサービスを含む) に対して行われます。

> [!div class="nextstepaction"]
> [Azure SQL を改善するためのアンケート](https://aka.ms/AzureSQLSurveyNov2021)

## <a name="prerequisites"></a>前提条件

コスト分析では、ほとんどの種類の Azure アカウントがサポートされますが、すべてではありません。 サポートされているアカウントの種類の完全な一覧については、「[Understand Cost Management data (Cost Management データの概要)](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)」を参照してください。 コスト データを表示するには、少なくとも Azure アカウントの読み取りアクセス許可が必要です。 

Azure Cost Management データに対するアクセス権の割り当てについては、[データへのアクセス許可の割り当て](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関するページを参照してください。


## <a name="sql-database-initial-cost-considerations"></a>SQL Database の初期コストに関する考慮事項

Azure SQL Database を使用する場合、考慮する必要があるコスト節約機能がいくつかあります。

### <a name="vcore-or-dtu-purchasing-models"></a>仮想コアまたは DTU 購入モデル 

Azure SQL Database では、仮想コアと DTU という 2 つの購入モデルがサポートされています。 課金の方法は購入モデルによって異なります。そのため、コストを計画して検討する際には、実際のワークロードに最適なモデルを理解することが重要です。 仮想コアおよび DTU 購入モデルの詳細については、「[仮想コアと DTU のどちらかの購入モデルを選択する](purchasing-models.md)」を参照してください。

### <a name="provisioned-or-serverless"></a>プロビジョニング済みまたはサーバーレス

仮想コア購入モデルでは、Azure SQL Database は 2 種類のコンピューティング レベル (プロビジョニング済みスループットとサーバーレス) もサポートします。 課金の方法はコンピューティング レベルによって異なります。そのため、コストを計画して検討する際には、実際のワークロードに最適なレベルを理解することが重要です。 詳細については、「[仮想コア モデルの概要 - コンピューティング レベル](service-tiers-sql-database-vcore.md#compute-tiers)」を参照してください。

仮想コアベースの購入モデルのプロビジョニング済みコンピューティング レベルでは、既存のライセンスを割引料金で交換できます。 詳細については、「[Azure ハイブリッド特典 (AHB)](../azure-hybrid-benefit.md)」を参照してください。

### <a name="elastic-pools"></a>エラスティック プール

複数のデータベースがある環境で、その使用の需要が変化し、予測できない場合には、エラスティック プールを使用すると、同数の単一データベースをプロビジョニングする場合と比較してコストを削減できます。 詳細については、「[エラスティック プール](elastic-pool-overview.md)」を参照してください。

## <a name="estimate-azure-sql-database-costs"></a>Azure SQL Database のコストを見積もる

[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用すると、さまざまな Azure SQL Database 構成のコストを見積もることができます。 詳細については、「[Azure SQL Database の価格](https://azure.microsoft.com/pricing/details/azure-sql-database/)」を参照してください。 

次の画像内の情報と価格は、あくまでも例です。 

:::image type="content" source="media/cost-management/pricing-calc.png" alt-text="Azure SQL Database 料金計算ツールの例":::

また、さまざまなアイテム保持ポリシー オプションがコストにどのように影響するかを見積もることもできます。 次の画像内の情報と価格は、あくまでも例です。

:::image type="content" source="media/cost-management/backup-storage.png" alt-text="ストレージに対する Azure SQL Database 料金計算ツールの例":::


## <a name="understand-the-full-billing-model-for-azure-sql-database"></a>Azure SQL Database の詳細な課金モデルを理解する

Azure SQL Database は、Azure インフラストラクチャで実行されます。そのコストが、Azure SQL Database の分と併せて、新しいリソースをデプロイする際に発生します。 追加のインフラストラクチャでコストが発生する可能性があることを理解しておくことが重要です。  

Azure SQL Database (サーバーレスを除く) は、予測可能な時間単位の料金で課金されます。 SQL データベースのアクティブ期間が 1 時間未満の場合でも、指定のサービス レベルのうちで最も高いものと、この時間内に適用されたプロビジョニング ストレージおよび IO に対して課金されます。使用量や、データベースのアクティブ期間が 1 時間未満であったかどうかは加味されません。

課金は製品の SKU、SKU のハードウェア世代、測定カテゴリによって異なります。 Azure SQL Database には次の SKU があります。

- Basic (B)
- Standard (S)
- Premium (P)
- General Purpose (GP)
- Business Critical (BC)
- ストレージ: geo 冗長ストレージ (GRS)、ローカル冗長ストレージ (LRS)、ゾーン冗長ストレージ (ZRS)
- 非推奨リソース プランの非推奨 SKU もあります。

詳細については、[サービス レベル](service-tiers-general-purpose-business-critical.md)に関するページを参照してください。 

次の表は、**単一データベース** に関して最も一般的な課金メーターと使用可能な SKU を示したものです。 

| Measurement| 使用可能な SKU | 説明 | 
| :----|:----|:----|
| バックアップ\* | GP、BC、HS | バックアップに使用されるストレージの消費量を測定します。1 か月あたりに使用された記憶域の容量 (GB 単位) で課金されます。 | 
| バックアップ (LTR) | GRS、LRS、ZRS、GF | 長期保有によって構成された長期的なバックアップに使用されるストレージの消費量を測定します。使用された記憶域の容量で課金されます。 | 
| コンピューティング  | B、S、P、GP、BC  | 1 時間あたりのコンピューティング リソースの消費量を測定します。 | 
| コンピューティング (プライマリ、名前付きレプリカ) | HS | プライマリ HS レプリカの 1 時間あたりのコンピューティング リソースの消費量を測定します。 
| コンピューティング (HA レプリカ)             | HS | セカンダリ HS レプリカの 1 時間あたりのコンピューティング リソースの消費量を測定します。 | 
| コンピューティング (ZR アドオン)              | GP | ゾーン冗長で追加されたレプリカの 1 分あたりのコンピューティング リソースの消費量を測定します。 | 
| コンピューティング (サーバーレス)             | GP | 1 分あたりのサーバーレス コンピューティング リソースの消費量を測定します。  | 
| ライセンス | GP、BC、HS | 1 か月に計上される SQL Server ライセンスの課金。 | 
| 記憶域 | B、S\*、P\*、G、BC、HS | 1 時間に格納されたデータの量を基準に毎月課金されます。 |

\* DTU 購入モデルでは、データ用とバックアップ用の初期ストレージ一式が追加費用なしで提供されます。 ストレージのサイズは、選択したサービス レベルによって異なります。 Standard レベルおよび Premium レベルでは、データ ストレージを追加購入できます。 詳細については、「[Azure SQL Database の価格](https://azure.microsoft.com/pricing/details/azure-sql-database/)」を参照してください。 

次の表は、**エラスティック プール** に関して最も一般的な課金メーターと使用可能な SKU を示したものです。 

| Measurement| 使用可能な SKU | 説明 | 
|:----|:----|:----|
| バックアップ\* | GP、BC  | バックアップに使用されるストレージの消費量を測定します。毎月、1 時間あたりの GB 単位で課金されます。 | 
| コンピューティング  | B、S、P、GP、BC | 仮想コアやメモリ、DTU など、コンピューティング リソースの 1 時間あたりの消費量を測定します。 | 
| ライセンス | GP、BC | 1 か月に計上される SQL Server ライセンスの課金。 | 
| 記憶域 | B、S\*、P\*、GP、HS | ドライブ上の記憶領域を使用して格納された 1 時間あたりのデータの量と MBPS (MegaBytes Per Second) スループットの両方を基準に毎月課金されます。 |

\* DTU 購入モデルでは、データ用とバックアップ用の初期ストレージ一式が追加費用なしで提供されます。 ストレージのサイズは、選択したサービス レベルによって異なります。 Standard レベルおよび Premium レベルでは、データ ストレージを追加購入できます。 詳細については、「[Azure SQL Database の価格](https://azure.microsoft.com/pricing/details/azure-sql-database/)」を参照してください。 

### <a name="using-monetary-credit-with-azure-sql-database"></a>Azure SQL Database で年額クレジットを使用する

Azure SQL Database の料金は、Azure 前払い (旧称: 年額コミットメント) のクレジットを使用して支払うことができます。 ただし、Azure 前払いのクレジットを使用して、サードパーティの製品やサービス (Azure Marketplace からのものを含む) の料金を支払うことはできません。

## <a name="review-estimated-costs-in-the-azure-portal"></a>Azure portal で推定料金を検討する

Azure SQL Database の作成プロセスを進めると、コンピューティング レベルの構成中に推定コストを確認できます。 

この画面にアクセスするには、 **[SQL データベースの作成]** ページの **[基本]** タブで **[データベースの構成]** を選択します。 次の画像内の情報と価格は、あくまでも例です。

  :::image type="content" source="media/cost-management/cost-estimate.png" alt-text="Azure portal でコスト見積もりを表示する例":::

Azure サブスクリプションに使用制限がある場合は、Azure により、クレジット額を超える支出が防止されます。 Azure リソースを作成して使用するときに、クレジットが使用されます。 クレジットの上限に達すると、その請求期間の残りの期間は、デプロイしたリソースが無効にされます。 クレジットの上限は変更できませんが、上限を取り除くことは可能です。 使用制限の詳細については、「[Azure の使用制限](../../cost-management-billing/manage/spending-limit.md)」を参照してください。

## <a name="monitor-costs"></a>コストを監視する

Azure SQL Database の使用を開始すると、ポータルで推定コストを確認できます。 次のステップに従ってコスト見積もりを確認します。

1. Azure portal にサインインし、Azure SQL データベースのリソース グループに移動します。 リソース グループを見つけるには、データベースに移動し、 **[概要]** セクションで **[リソース グループ]** を選択します。
1. メニューで **[コスト分析]** を選択します。
1. **[累積コスト]** を確認し、下部にあるグラフを **[サービス名]** に設定します。 このグラフは、現在の SQL Database コストの見積もりを示します。 ページ全体のコストを Azure SQL Database に絞り込むには、 **[フィルターの追加]** を選択し、 **[Azure SQL Database]** を選択します。 次の画像内の情報と価格は、あくまでも例です。

   :::image type="content" source="media/cost-management/cost-analysis.png" alt-text="Azure portal で累積コストを表示する例":::

ここでは、コストを自分で調べることができます。 さまざまなコスト分析設定の詳細については、「[コストの分析を開始する](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)」を参照してください。

## <a name="create-budgets"></a>予算を作成する

[予算](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成して、コストを管理し、異常な支出や浪費のリスクについて、関係者に自動的に通知する[アラート](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成できます。 アラートは、予算とコストのしきい値と比較した支出に基づきます。 予算とアラートは、Azure サブスクリプションとリソース グループに対して作成されるため、全体的なコスト監視戦略の一環として役立ちます。 

監視の粒度をさらに細かく示す必要がある場合は、Azure の特定のリソースまたはサービスに対するフィルターを使用して予算を作成できます。 フィルターを使用すると、新しいリソースが誤って作成されないようにすることができます。 予算を作成するときのフィルター オプションの詳細については、[グループとフィルターのオプション](../../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関する記事を参照してください。

## <a name="export-cost-data"></a>コスト データのエクスポート

また、ストレージ アカウントに[コスト データをエクスポート](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)することもできます。 これは、自分がコストに関する追加のデータ分析を行う必要がある場合に便利です。 たとえば、財務チームは、Excel や Power BI を使用してデータを分析できます。 日単位、週単位、または月単位のスケジュールでコストをエクスポートし、カスタムの日付範囲を設定することができます。 コスト データのエクスポートは、推奨されるコスト データセット取得方法です。

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-sql-database"></a>Azure SQL Database のコストの管理と削減のためのその他の方法

Azure SQL Database では、アプリケーションのニーズに基づいてリソースをスケールアップまたはスケールダウンしてコストを制御することもできます。 詳細については、[データベース リソースの動的スケーリング](scale-resources.md)に関する記事を参照してください。

1 年から 3 年間のコンピューティング リソースの予約に同意することでコストを節約できます。 詳細については、「[予約容量を使用してリソースのコストを節約する](reserved-capacity-overview.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

- [Azure Cost Management を使用してクラウドへの投資を最適化する](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)方法について説明します。
- [コスト分析](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を使用してコストを管理する方法について詳細に説明します。
- [予期しないコストを回避](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)する方法について説明します。
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) のガイド付き学習コースを受講します。