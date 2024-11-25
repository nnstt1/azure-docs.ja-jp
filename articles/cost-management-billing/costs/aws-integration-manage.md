---
title: Azure Cost Management での AWS のコストと使用状況の管理
description: この記事では、Cost Management でのコスト分析と予算を使用して、AWS のコストと使用状況を管理する方法を理解できます。
author: bandersmsft
ms.author: banders
ms.date: 09/15/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: matrive
ms.custom: ''
ms.openlocfilehash: d64d0bead5df1aa8b91ae39a79c1255eaac51f27
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706177"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Azure での AWS のコストと使用状況の管理

Cost Management のための AWS のコストおよび使用レポートを設定して構成したら、AWS のコストと使用状況を開始する準備ができたことになります。 この記事では、Cost Management でのコスト分析と予算を使用して、AWS のコストと使用状況を管理する方法を理解できます。

統合をまだ構成していない場合は、「[AWS の使用状況レポート統合の設定と構成](aws-integration-set-up-configure.md)」を参照してください。

_開始する前に_:コスト分析に慣れていない場合は、「[コスト分析を使用したコストの調査と分析](quick-acm-cost-analysis.md)」クイックスタートを参照してください。 Azure での予算に慣れていない場合は、「[Azure の予算の作成と管理](tutorial-acm-create-budgets.md)」チュートリアルを参照してください。

## <a name="view-aws-costs-in-cost-analysis"></a>コスト分析での AWS のコストの表示

AWS のコストは、次のスコープのコストの分析で使用できます。

- 管理グループ下の AWS のリンクされたアカウント
- AWS のリンクされたアカウントのコスト
- AWS 統合アカウントのコスト

次のセクションでは、スコープを使用してそれぞれのコストと使用状況のデータを表示する方法について説明します。

### <a name="view-aws-linked-accounts-under-a-management-group"></a>管理グループ下の AWS のリンクされたアカウントの表示

さまざまな Azure サブスクリプションおよび AWS のリンクされたアカウントから集計されたコストを確認する唯一の方法は、管理グループのスコープを使用してコストを表示することです。 管理グループを使用すると、Azure と AWS のコストをまとめて表示するためのクラウド間ビューが提供されます。

コスト分析で、スコープ ピッカーを開き、AWS のリンクされたアカウントを保持する管理グループを選択します。 Azure portal での画像の例を示します。

:::image type="content" source="./media/aws-integration-manage/select-scope01.png" alt-text="管理グループの下のリンクされたアカウントを示す [スコープの選択] ビューの例" :::

プロバイダー (Azure と AWS) でグループ化された、コスト分析での管理グループのコストを示す例を次に示します。

:::image type="content" source="./media/aws-integration-manage/cost-analysis-aws-azure.png" alt-text="コスト分析での四半期の Azure と AWS のコストを示す例" lightbox="./media/aws-integration-manage/cost-analysis-aws-azure.png" :::

> [!NOTE]
> 管理グループは、Microsoft 顧客契約 (MCA) のお客様に対して現在サポートされていません。 MCA のお客様は、コネクタを作成し、ご自身の AWS データを表示できます。 ただし、MCA のお客様は、管理グループの下の Azure のコストと AWS のコストをまとめて表示することはできません。

### <a name="view-aws-linked-account-costs"></a>AWS のリンクされたアカウントのコストの表示

AWS のリンクされたアカウントのコストを表示するには、スコープ ピッカーを開き、AWS のリンクされたアカウントを選択します。 リンクされたアカウントは、AWS のコネクタでの定義に従って管理グループに関連付けられていることに注意してください。

AWS のリンクされたアカウントのスコープを選択する例を次に示します。

:::image type="content" source="./media/aws-integration-manage/select-scope02.png" alt-text="AWS のリンクされたアカウントを表示する [スコープの選択] ビューの例" :::

### <a name="view-aws-consolidated-account-costs"></a>AWS 統合アカウントのコストの表示

AWS 統合アカウントのコストを表示するには、スコープ ピッカーを開き、AWS 統合アカウントを選択します。 AWS 統合アカウントのスコープを選択する例を示します。

:::image type="content" source="./media/aws-integration-manage/select-scope03.png" alt-text="統合アカウントを示す [スコープの選択] ビューの例" :::

このスコープは、AWS 統合アカウントに関連付けられているすべての AWS のリンクされたアカウントの集計ビューを提供します。 サービス名別にグループ化された AWS 統合アカウントのコストの例を次に示します。

:::image type="content" source="./media/aws-integration-manage/cost-analysis-aws-consolidated.png" alt-text="コスト分析で AWS 統合コストを示す例" lightbox="./media/aws-integration-manage/cost-analysis-aws-consolidated.png" :::

### <a name="dimensions-available-for-filtering-and-grouping"></a>フィルター処理やグループ化に使用できるディメンション

次の表では、コスト分析でのグループ化やフィルター処理に使用できるディメンションについて説明します。

| Dimension | Amazon CUR ヘッダー | スコープ | 説明 |
| --- | --- | --- | --- |
| 可用性ゾーン | lineitem/AvailabilityZone | All |   |
| 場所 | product/Region | All |   |
| 測定 |   | All |   |
| メーター カテゴリ | lineItem/ProductCode | All |   |
| Meter subcategory | lineitem/UsageType | All |   |
| Operation | lineItem/Operation | All |   |
| リソース | lineItem/ResourceId | All |   |
| リソースの種類 | product/instanceType | All | product/instanceType が null の場合は、UsageType が使用されます。 |
| ResourceGuid | 該当なし | All | Azure メーターの GUID。 |
| [サービス名] | product/ProductName | All | product/ProductName が null の場合は、lineItem/ProductCode が使用されます。 |
| サービス レベル |   |   |   |
| サブスクリプション ID | lineItem/UsageAccountId | 統合アカウントおよび管理グループ |   |
| サブスクリプション名 | 該当なし | 統合アカウントおよび管理グループ | アカウント名は、AWS 組織 API を使用して収集されます。 |
| タグ | resourceTags | All | _user:_ プレフィックスは、クラウドの間のタグを許可するユーザー定義タグから削除されます。 _aws:_ プレフィックスはそのまま残されます。 |
| 請求先アカウント ID | bill/PayerAccountId | 管理グループ |   |
| 請求先アカウント名 | 該当なし | 管理グループ | アカウント名は、AWS 組織 API を使用して収集されます。 |
| プロバイダー | 該当なし | 管理グループ | AWS または Azure のいずれか。 |

## <a name="set-budgets-on-aws-scopes"></a>AWS のスコープで予算を設定

予算を使用して、組織において積極的にコストを管理し、説明責任を推進します。 予算は、AWS 統合アカウントとAWS のリンクされたアカウントのスコープに対して設定されます。 Cost Management で示される、AWS 統合アカウントの予算の例を次に示します。

:::image type="content" source="./media/aws-integration-manage/budgets-aws-consolidated-account01.png" alt-text="AWS 統合アカウントの予算を示す例" :::

## <a name="aws-data-collection-process"></a>AWS データ コレクション処理

AWS コネクタの設定後、データ収集と探索のプロセスを開始します。 すべての使用状況データを収集するには、いくらかの時間がかかる場合があります。 期間は以下に応じて異なります。

- AWS の S3 バケット内にある CUR ファイルを処理するために必要な時間。
- AWS 統合アカウントとAWS のリンクされたアカウントのスコープを作成するために必要な時間。
- S3 バケットのコストと使用状況レポートのファイルに AWS が書き込む時間と頻度

## <a name="aws-integration-pricing"></a>AWS の統合の価格

AWS の各コネクタには、90 日間の無料試用期間があります。

定額は、AWS の月額の 1% です。 各月の課金は、前の月の請求コストに基づいています。

AWS API にアクセスすると、AWS で追加のコストが発生する場合があります。

## <a name="aws-integration-limitations"></a>AWS 統合の制限事項

- Cost Management の予算では、複数の通貨を持つ管理グループはサポートされていません。 複数の通貨を持つ管理グループには、予算評価が表示されません。 予算を作成するときに、複数の通貨を持つ管理グループを選択すると、エラー メッセージが表示されます。
- クラウド コネクタは、AWS GovCloud (US)、AWS Gov、または AWS China はサポートしていません。
- Cost Management は、AWS _使用状況コスト_ のみを示します。 税、サポート、返金、RI、クレジット、その他の料金タイプはまだサポートされていません。

## <a name="troubleshooting-aws-integration"></a>AWS 統合のトラブルシューティング

一般的な問題を解決するには、次のトラブルシューティング情報を使用します。

### <a name="no-permission-to-aws-linked-accounts"></a>AWS のリンクされたアカウントへのアクセス許可がない

**エラー コード:** _Unauthorized_

AWS のリンクされたアカウントのコストへのアクセス許可を取得するには、次の 2 つの方法があります。

- AWS のリンクされたアカウントのある管理グループへのアクセスを取得する。
- AWS のリンクされたアカウントへのアクセス権を誰かに付与してもらう。

既定では、AWS コネクタの作成者は、コネクタが作成したすべてのオブジェクトの所有者です。 AWS 統合アカウントと AWS のリンクされたアカウントが含まれます。

コネクタの設定を確認するには、少なくとも共同作成者ロールが必要であり、閲覧者がコネクタの設定を確認することはできません。

### <a name="collection-failed-with-assumerole"></a>AssumeRole での収集の失敗

**エラー コード:** _FailedToAssumeRole_

このエラーは、Cost Management が AWS AssumeRole API を呼び出すことができないことを意味します。 この問題は、ロールの定義の問題が原因で発生する可能性があります。 次の条件が該当することを確認します。

- 外部 ID は、ロールの定義とコネクタの定義のものと同じです。
- ロールの種類は、**ユーザーまたはサード パーティに属する別の AWS アカウントに設定されます。**
- **[MFA を要求]** 選択項目はクリアされます。
- AWS ロールの信頼される AWS アカウントは、_432263259397_ です。

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>アクセス拒否で収集が失敗 - CUR レポート定義

**エラー コード:** _AccessDeniedReportDefinitions_

このエラーは、Cost Management でコストと使用状況レポートの定義を表示できないことを意味しています。 このアクセス許可は、Cost Management が想定するとおりに CUR が定義されていることを検証するために使用されます。 「[AWS でコストと使用状況レポートを作成する](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws)」を参照してください。

### <a name="collection-failed-with-access-denied---list-reports"></a>アクセス拒否で収集が失敗 - レポートの一覧表示

**エラー コード:** _AccessDeniedReportDefinitions_

このエラーは、Cost Management で、CUR が配置されている S3 バケット内のオブジェクトを一覧表示できないことを意味します。 AWS IAM ポリシーでは、バケットおよびバケット内のオブジェクトに対するアクセス許可が必要です。 「[AWS でロールとポリシーを作成する](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws)」を参照してください。

### <a name="collection-failed-with-access-denied---download-report"></a>アクセス拒否で収集が失敗 - レポートのダウンロード

**エラー コード:** _AccessDeniedDownloadReport_

このエラーは、Cost Management で Amazon S3 バケットに格納されている CUR ファイルにアクセスおよびダウンロードできないことを意味します。 ロールに関連付けられている AWS JSON ポリシーが、「[AWS でのロールとポリシーの作成](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws)」セクションの下部に示されている例と似たものであることを確認してください。

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>コストと使用状況レポートが見つからないための収集の失敗

**エラー コード:** _FailedToFindReport_

このエラーは、Cost Management がコネクタで定義されているコストと使用状況レポートを見つけられないことを意味します。 それが削除されておらず、ロールに関連付けられている AWS JSON ポリシーが、「[AWS でのロールとポリシーの作成](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws)」セクションの下部に示されている例と似たものであることを確認してください。

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>コストと使用状況レポートの定義が一致しないため、コネクタを作成または確認できない

**エラー コード:** _ReportIsNotValid_

このエラーは、AWS のコストと使用状況レポートの定義に関連しています。このレポートには固有の設定が必要です。[AWS でのコストと使用状況レポートの作成](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws)に記載されている要件を参照してください。

### <a name="internal-error-when-creating-connector"></a>コネクタを作成する際の内部エラー

**エラー コード:** "_Create connector - Failed to create connector &lt;ConnectorName&gt;. (コネクタの作成 - コネクタ <コネクタ名> を作成できませんでした。)理由:内部エラーです。Please verify that the correct AWS properties were provided. (正しい AWS プロパティが指定されていることを確認してください。)_ "

AWS コネクタとサブスクリプションが別々の管理グループに存在すると、このエラーが発生することがあります。 AWS コネクタとサブスクリプションは同じ管理グループに存在している必要があります。

## <a name="next-steps"></a>次のステップ

- Azure 環境を管理グループでまだ構成していない場合は、「[管理グループの初期セットアップ](../../governance/management-groups/overview.md#initial-setup-of-management-groups)」を参照してください。
