---
title: 事前購入を利用して Azure Databricks のコストを最適化する
description: コスト削減のために容量が予約された Azure Databricks 料金を前払いする方法について説明します。
author: bandersmsft
ms.reviewer: sapnakeshari
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/19/2021
ms.author: banders
ms.openlocfilehash: 72bb3403a49431d1dbc1cbd9d15ac37348818118
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130243830"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>事前購入を利用して Azure Databricks のコストを最適化する

1 年または 3 年間の Azure Databricks コミット ユニット (DBCU) を事前に購入すると、Azure Databricks ユニット (DBU) のコストを節約することができます。 購入済みの DBCU は、購入期間中はいつでも使用できます。 VM とは異なり、事前購入したユニットは 1 時間単位で期限切れにならず、購入期間中はいつでも使用できます。

Azure Databricks の使用状況はすべて、事前購入済み DBU から自動的に差し引かれます。 事前購入割引を受けるために、事前購入済みプランを DBU の使用状況の Azure Databricks ワークスペースに再デプロイしたり、割り当てたりする必要はありません。

事前購入割引は、DBU の使用状況にのみ適用されます。 コンピューティング、ストレージ、およびネットワーキングなどの他の料金は別途課金されます。

## <a name="determine-the-right-size-to-buy"></a>購入する適切なサイズの判断

Databricks の事前購入は、すべての Databricks ワークロードおよびレベルに適用されます。 事前購入は、前払いの Databricks コミット ユニットのプールと考えることができます。 ワークロードまたはレベルには関係なく、使用状況がプールから差し引かれます。 使用状況は、次の比率で差し引かれます。

| **[ワークロード]** | **DBU の適用率 - Standard レベル** | **DBU の適用率 - Premium レベル** |
| --- | --- | --- |
| データ分析 | 0.4 | 0.55 |
| Data Engineering | 0.15 | 0.30 |
| Data Engineering Light | 0.07 | 0.22 |

たとえば、Data Analytics — Standard レベルの数量が使用された場合、事前購入済み Databricks コミット ユニットは 0.4 ユニット差し引かれます。

購入する前に、さまざまなワークロードとレベルについて、消費される DBU の合計数量を計算します。 前述の比率を使用して DBCU に正規化し、今後 1 年または 3 年間の合計使用量の予測を実行します。

## <a name="purchase-databricks-commit-units"></a>Databricks コミット ユニットの購入

Databricks プランは [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D) 内で購入できます。 予約容量を購入するには、少なくとも 1 つのエンタープライズ サブスクリプションに対して所有者ロールを所持している必要があります。

- 次に対する所有者ロールに属している必要があります: 少なくとも 1 つの Enterprise Agreement (プラン番号: MS-AZR-0017P または MS-AZR-0148P) または Microsoft Customer Agreement または従来課金制の個々のサブスクリプション (プラン番号:MS-AZR-0003P または MS-AZR-0023P)。
- Enterprise サブスクリプションの場合、[EA ポータル](https://ea.azure.com/)で **[予約インスタンスを追加します]** を有効にする必要があります。 または、その設定が無効になっている場合、有効にするにはユーザーがサブスクリプションの EA 管理者である必要があります。 ダイレクト EA のお客様は、[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) で **予約インスタンス** の設定を更新できるようになりました。 [ポリシー] メニューに移動して、設定を変更します。


**購入方法:**

1. [Azure ポータル](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D)にアクセスします。
1. サブスクリプションを選択します。 **[サブスクリプション]** リストを使用して、予約容量の支払いに使用するサブスクリプションを選択します。 サブスクリプションの支払方法に対して、予約容量の初期コストが課金されます。 加入契約の Azure 前払い (旧称: 年額コミットメント) 残高から料金が差し引かれるか、超過料金として課金されます。
1. スコープを選択します。 **[スコープ]** リストを使用して、サブスクリプション スコープを選択します。
    - **単一のリソース グループのスコープ** - 選択されたリソース グループ内の一致するリソースにのみ、予約割引を適用します。
    - **単一サブスクリプション** - 選択されたサブスクリプションの一致するリソースに予約割引を適用します。
    - **共有スコープ** - 課金コンテキスト内にある有効なサブスクリプションの一致するリソースに予約割引を適用します。 マイクロソフト エンタープライズ契約のお客様の場合、課金コンテキストは登録です。
    - **管理グループ** - 管理グループと課金スコープの両方の一部であるサブスクリプションの一覧にある一致するリソースに、予約割引を適用します。
1. 購入する Azure Databricks コミット ユニット数を選択し、購入を完了します。


![Azure portal 内での Azure Databricks の購入を示す例](./media/prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>スコープと所有権の変更

購入後に予約に次の種類の変更を加えることができます。

- 予約スコープの更新
- Azure ロールベースのアクセス制御 (Azure RBAC)

Databricks コミット ユニットの事前購入を分割またはマージすることはできません。 予約の管理について詳しくは、[購入後の予約の管理](manage-reserved-vm-instance.md)に関する記事をご覧ください。

## <a name="cancellations-and-exchanges"></a>キャンセルと交換

Databricks の事前購入プランでは、キャンセルと交換はサポートされていません。 すべての購入は確定です。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次のステップ

- Azure の予約の詳細については、次の記事を参照してください。
  - [Azure の予約とは](save-compute-costs-reservations.md)
  - [Azure Databricks の事前購入 DBCU 割引が適用される方法を理解する](reservation-discount-databricks.md)
  - [エンタープライズ加入契約の予約使用量について](understand-reserved-instance-usage-ea.md)
