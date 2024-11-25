---
title: マイクロソフト エンタープライズ契約と Microsoft 顧客契約に適用される Azure の予約の使用状況について
description: マイクロソフト エンタープライズ契約と Microsoft 顧客契約の利用に対して Azure の予約がどのように適用されているかを、使用状況情報を読んで把握する方法について説明します。
author: bandersmsft
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 04/20/2021
ms.author: banders
ms.openlocfilehash: edc1a80687f768723a3a218ecaa1f6459cd484ed
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763561"
---
# <a name="get-enterprise-agreement-and-microsoft-customer-agreement-reservation-costs-and-usage"></a>マイクロソフト エンタープライズ契約と Microsoft 顧客契約の予約コストと使用状況を把握する

マイクロソフト エンタープライズ契約 (EA) と Microsoft 顧客契約 (MCA) の利用について、予約のコストと使用状況に関する増強されたデータを Cost management で入手できます。 この記事は次のことに役立ちます。

- 予約購入データの取得
- どのサブスクリプション、リソース グループ、またはリソースによって予約が使用されたかを把握する
- 予約の使用率のチャージバック
- 予約による割引の計算
- 使用率の低い予約データの取得
- 予約コストの償却

マーケットプレースの料金は使用状況データに統合されています。 1 つのデータ ソースから最初のパーティの使用状況、マーケットプレースの使用状況、および購入に対する請求金額を表示します。

## <a name="reservation-charges-in-azure-usage-data"></a>Azure の使用状況データの予約料金

データは 2 つのデータ セットに分割されます。_実際のコスト_ と _分散コスト_ です。 これら 2 つのデータセットの違いを次に示します。

**実際のコスト**: 月ごとの請求を調整するためのデータを提供します。 このデータには、予約購入コストと予約アプリケーションの詳細が含まれています。 このデータを使用して、特定の日にどのサブスクリプション、リソース グループ、またはリソースが予約割引を受けたかを確認できます。 予約割引を受ける使用量の EffectivePrice はゼロです。

**分散コスト** - このデータセットは、実際のコスト データセットと同様です。ただし、予約割引を受ける使用量の EffectivePrice は、予約の日割り計算です (ゼロではありません)。 これにより、サブスクリプション、リソース グループ、またはリソースによる予約使用量の金額を把握しやすくなり、内部での予約の使用率に応じたチャージバックに役立ちます。 このデータセットには未使用の予約時間も含まれます。 このデータセットに予約購入レコードはありません。

2 つのデータ セットの比較:

| Data | 実際のコストのデータ セット | 分散コストのデータ セット |
| --- | --- | --- |
| 予約購入 | このビューで使用できます。<br><br>  このデータを取得するには、ChargeType = &quot;Purchase&quot; でフィルター処理します。 <br><br> 料金がどの予約に対するものかを把握するには、ReservationID または ReservationName を参照します。  | このビューには適用されません。 <br><br> 購入コストが償却データでは提供されません。 |
| EffectivePrice | 値は、予約割引を受ける使用状況に対してはゼロです。 | 値は、予約割引がある使用状況に対する予約の 1 時間あたりの比例配分コストです。 |
| 未使用の予約 (1 日の使用されなかった予約の時間数と、無駄になった金額を提供します) | このビューには適用されません。 | このビューで使用できます。<br><br> このデータを取得するには、ChargeType = &quot;UnusedReservation&quot; でフィルター処理します。<br><br>  使用率が低かった予約を把握するには、ReservationID または ReservationName を参照します。 これがその日の無駄になった予約の量です。  |
| UnitPrice (価格シートのリソースの価格) | 利用可能 | 利用可能 |

Azure の使用状況データで使用できるその他の情報が変更されました。

- 製品とメーター情報: Azure では、最初に消費されたメーターが ReservationId と ReservationName で置き換えられません。以前に置き換えられているからです。
- ReservationId と ReservationName: これらはデータ内の独自のフィールドです。 以前は、AdditionalInfo でのみ使用することができました。
- ProductOrderId: 独自のフィールドとして追加された予約注文 ID。
- ProductOrderName: 購入済みの予約の製品名。
- Term: 12 か月間または 36 か月間。
- RINormalizationRatio: AdditionalInfo で使用できます。 これは、予約が使用状況レコードに適用される比率です。 予約でインスタンス サイズの柔軟性が有効になっている場合は、他のサイズに適用できます。 この値は、予約が使用状況レコードに適用された比率を示します。

[フィールド定義を参照してください](/rest/api/consumption/usagedetails/list#definitions)

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>API を使用して Azure の従量課金と予約の使用状況データを取得する

データは API を使用して取得することも、Azure portal からダウンロードすることもできます。

新しいデータを取得するには、[使用状況の詳細 API](/rest/api/consumption/usagedetails/list) を呼び出します。 用語に関する詳細については、[利用料金の用語](../understand/understand-usage.md)のページを参照してください。

Usage Details API への呼び出しの例を次に示します。

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

{billingPeriodId} と {enrollmentId} の詳細については、「[Usage Details – List](/rest/api/consumption/usagedetails/list)」 (使用方法の詳細 – 一覧) の API 記事を参照してください。

メトリックとフィルターに関する次の表の情報は、予約の一般的な問題の解決に役立ちます。

| **API データの種類** | API 呼び出しアクション |
| --- | --- |
| **すべての料金 (使用状況と購入)** | {metric} を ActualCost で置き換える |
| **予約割引を取得する使用状況** | {metric} を ActualCost で置き換える<br><br>{filter} を properties/reservationId%20ne%20 で置き換える |
| **予約割引を取得しなかった使用状況** | {metric} を ActualCost で置き換える<br><br>{filter} を properties/reservationId%20eq%20 で置き換える |
| **請求済み料金 (使用状況と購入)** | {metric} を AmortizedCost で置き換える |
| **未使用の予約レポート** | {metric} を AmortizedCost で置き換える<br><br>{filter} を properties/ChargeType%20eq%20'UnusedReservation' で置き換える |
| **予約購入** | {metric} を ActualCost で置き換える<br><br>{filter} を properties/ChargeType%20eq%20'Purchase' で置き換える  |
| **返金** | {metric} を ActualCost で置き換える<br><br>{filter} を properties/ChargeType%20eq%20'Refund' で置き換える |

## <a name="download-the-usage-csv-file-with-new-data"></a>新しいデータを含む使用状況の CSV ファイルをダウンロードする

EA 管理者の場合、Azure portal から新しい使用状況データを含む CSV ファイルをダウンロードできます。 このデータは、EA ポータル (ea.azure.com) では確認できません。新しいデータを確認するには、Azure portal (portal.azure.com) から使用状況ファイルをダウンロードする必要があります。

Azure portal で [[コストの管理と請求]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts) に移動します。

1. 請求先アカウントを選択します。
2. **[使用量 + 請求金額]** をクリックします。
3. **[Download]** をクリックします。  
![Azure portal で使用状況データの CSV ファイルをダウンロードする場所を示す例](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
4. **[Usage Details Version 2]** の下の **[使用量と請求金額をダウンロードする]** で、 **[All Charges (usage and purchases)]\(すべての料金 (使用状況と購入)\)** を選択し、[ダウンロード] をクリックします。 **請求済み料金 (使用状況と購入)** を繰り返します。


## <a name="common-cost-and-usage-tasks"></a>共通コストと使用状況タスク

以降のセクションは、ほとんどの人が自身の予約コストと使用状況データを表示するために使用する一般的なタスクです。

### <a name="get-reservation-purchase-costs"></a>予約購入データを取得する

予約購入コストは、実際のコスト データで利用できます。 _ChargeType = Purchase_ でフィルター処理します。 購入する予約注文を判断するには、ProductOrderID を参照してください。

### <a name="get-underutilized-reservation-quantity-and-costs"></a>使用率が低い予約数量とコストを取得する

分散コスト データを取得し、_ChargeType_ _= UnusedReservation_ でフィルター処理します。 毎日の未使用の予約数量とコストを取得します。 予約または予約注文のデータは、_ReservationId_ フィールドと _ProductOrderId_ フィールドをそれぞれ使用してフィルター処理できます。 予約の使用率が 100% だった場合は、レコードの数量は 0 になります。

### <a name="amortize-reservation-costs"></a>予約コストの償却

分散コスト データを取得し、_ProductOrderID_ を使用して予約注文をフィルター処理して、予約の毎日の分散コストを取得します。

### <a name="chargeback-for-a-reservation"></a>予約のチャージバック

サブスクリプション、リソース グループ、またはタグごとに、予約の使用を他の組織にチャージバックすることができます。 分散コスト データは、予約の使用率の金額を次のデータの種類で提供します。

- リソース (VM など)
- Resource group
- Tags
- サブスクリプション

### <a name="get-the-blended-rate-for-chargeback"></a>チャージバックのブレンド価格を取得する

ブレンド価格を決定するには、分散コスト データを取得してコストの合計を集計します。 VM の場合は、AdditionalInfo JSON データから MeterName または ServiceType のいずれかの情報を使用できます。 コストの合計を使用された量で除算して、ブレンド価格を取得します。

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>インスタンス サイズの柔軟性のため、最適な予約の使用を監査する

数量を AdditionalInfo からの _RINormalizationRatio_ と乗算します。 結果には、使用状況記録に適用された予約の使用時間が示されます。

### <a name="determine-reservation-savings"></a>予約による割引を決定する

分散コスト データを取得し、予約インスタンスのデータをフィルター処理します。 その後、以下を実行します。

1. 従量課金制の概算コストを取得します。 予約割引が使用状況に適用されなかった場合は、_UnitPrice_ の値と _Quantity_ の値を乗算して従量課金制の概算コストを取得します。
2. 予約コストを取得します。 _Cost_ の値を合計して、予約インスタンスに支払った金額を取得します。 これには、予約の使用済みコストと未使用コストが含まれます。
3. 従量課金制の概算コストから予約コストを減算して、削減されるコストの推定額を取得します。

予約の使用率が低い場合は、_ChargeType_ の _UnusedReservation_ エントリが考慮すべき要素になることに注意してください。 予約が十分に活用されていると、最大限の節約が可能になります。 _UnusedReservation_ の数量があると、削減額が減少します。

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>予約購入と償却コストの分析

予約コストは、[コスト分析](https://aka.ms/costanalysis)で確認できます。 既定では、コスト分析には請求書に記載されるコストである **[実際のコスト]** が表示されます。 予約購入の内訳や特典を使用したリソースとの関連を確認するには、**[分散コスト]** に切り替えます。

![コスト分析で分散コストを選択する場所を示す例](./media/understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

料金タイプ別にグループ化して、利用状況、購入、および返金の内訳を確認します。または、予約別にグループ化して、予約およびオンデマンド コストの内訳を確認します。 実際のコストを表示したときに確認できる予約コストは購入のみですが、分散コストを表示したときに確認できるコストは、特典を使用した個々のリソースに割り当てられた状態となります。 また、分散コストを表示したときは、新しい **UnusedReservation** 料金タイプも確認できます。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

Azure の予約の詳細については、次の記事を参照してください。

- [Azure の予約とは](save-compute-costs-reservations.md)
- [Azure Reserved VM Instances による仮想マシンの前払い](../../virtual-machines/prepay-reserved-vm-instances.md)
- [予約割引の適用方法について](../manage/understand-vm-reservation-charges.md)
- [予約に含まれない Windows ソフトウェアのコスト](reserved-instance-windows-software-costs.md)
- [サービス プリンシパルを使用してコスト データを取得する](../manage/assign-roles-azure-service-principals.md)
- [コスト管理のエクスポートを使用する](../costs/tutorial-export-acm-data.md)
