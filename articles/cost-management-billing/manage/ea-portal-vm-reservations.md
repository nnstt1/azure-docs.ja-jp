---
title: Azure EA VM 予約インスタンス
description: この記事では、VM 予約インスタンスに対する Azure 予約が、エンタープライズ登録にかかる金額の節約にどのように役立つかについて説明します。
author: bandersmsft
ms.author: banders
ms.date: 10/22/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: sapnakeshari
ms.openlocfilehash: feac3cf4e2418fc7d85ab3cdebbba1d08b112e95
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130250067"
---
# <a name="azure-ea-vm-reserved-instances"></a>Azure EA VM 予約インスタンス

この記事では、VM 予約インスタンスに対する Azure 予約が、エンタープライズ登録にかかる金額の節約にどのように役立つかについて説明します。 予約の詳細については、「[Azure の予約とは](../reservations/save-compute-costs-reservations.md)」を参照してください。

## <a name="reservation-exchanges-and-refunds"></a>予約の交換と返金

変化するニーズを満たすために、予約を同じ種類の別の予約と交換できます。 また、予約が不要になった場合は、最大で年間 50,000 米国ドルまでの払い戻しができます。 予約の交換または払い戻しを行うには、Azure portal を使用できます。 詳しくは、「[Azure の予約のセルフサービスによる交換と払戻](../reservations/exchange-and-refund-azure-reservations.md)」を参照してください。

### <a name="partial-refunds"></a>一部返金

EA のお客様が予約を返却した場合、Azure 前払い (旧称: 年額コミットメント) ではなく超過分を使用して購入金額の一部が返金されます。

EA Portal には、前月における負の調整として、また今月における正の調整として返金が表示されます。 予約の交換についても同様に表示されます。 クレジット メモでは、元の請求書番号が引用されます。したがって、当初の購入額をクレジット メモで調整したければ、元の請求書番号を参照してください。

直接 Enterprise のお客様は、Azure portal で返金の詳細を表示できます。 **予約トランザクション** メニューに移動し、予約の払い戻しを表示します。

## <a name="reservation-costs-and-usage"></a>予約のコストと使用状況

エンタープライズ契約のお客様は、Azure portal および REST API でコストと使用状況データを確認できます。 予約のコストと使用状況については、次のことができます。

- 予約購入データを取得する。
- どのサブスクリプション、リソース グループ、またはリソースによって予約が使用されたかを把握する。
- 予約の使用をチャージバックする。
- 予約による節約額を計算する。
- 使用率の低いデータの予約を取得する。
- 予約コストを償却する。

予約のコストと使用状況の詳細については、「[Enterprise Agreement の予約のコストと使用状況を取得する](../reservations/understand-reserved-instance-usage-ea.md)」を参照してください。

価格の詳細については、「[Linux Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)」または「[Windows Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)」を参照してください。

### <a name="reservation-prices"></a>予約価格

予約割引が交渉されている場合であっても、それは EA ポータルの価格表に表示されません。 以前は、EA ポータルで割引料金を利用できましたが、その機能は削除されました。 予約価格の割引を交渉している場合、現在のところ、価格リストを入手する唯一の方法は [Azure サポート リクエスト](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を作成することです。

予約価格が小売価格と EA の間で同じになるとは限りません。 同じになることもありますが、割引を交渉しているなら、料金は異なるでしょう。

[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)と[小売価格 API](/rest/api/cost-management/retail-prices/azure-retail-prices) に表示される価格は同じです。 API にクエリを実行することが、すべての価格を一度に表示する最善の方法です。

## <a name="reserved-instances-api-support"></a>予約インスタンス API サポート

Azure API を使用して、Azure サービスまたはソフトウェアの予約に関する組織の情報をプログラムで取得します。 たとえば、次の用途に API を使用します。

- 購入する予約を検索する
- 予約の購入
- 購入した予約を表示する
- 予約の利用を表示および管理する
- 予約を分割または統合する
- 予約の範囲を変更する

詳細については、「[APIs for Azure reservation automation](../reservations/reservation-apis.md)」(Azure の予約自動化の API) を参照してください。

## <a name="azure-reserved-virtual-machine-instances"></a>Azure 予約仮想マシン インスタンス

予約インスタンスにより、すべての VM で仮想マシンのコストを従量課金制価格に比べて最大 72% 削減できます。 また、予約インスタンスと Azure ハイブリッド特典を組み合わせると、最大 82% 節約できます。 予約インスタンスで 1 年分または 3 年分を前払いすることにより、ワークロード、予算、予測が管理しやすくなります。 ビジネス ニーズの変化に応じて予約を交換またはキャンセルすることができます。

### <a name="how-to-buy-reserved-virtual-machine-instances"></a>予約仮想マシン インスタンスを購入する方法

Azure 予約仮想マシンインスタンスを購入するには、エンタープライズ Azure 加入契約管理者が _[予約インスタンス]_ 購入オプションを有効にする必要があります。 このオプションは、[Azure EA Portal](https://ea.azure.com/) の _[加入契約]_ タブの _[加入契約の詳細]_ セクションにあります。

EA 加入契約を有効にして、予約インスタンスを追加すると、EA 加入契約に関連付けられたアクティブなサブスクリプションを持つすべてのアカウント所有者は、[Azure portal](https://aka.ms/reservations) で予約仮想マシン インスタンスを購入できます。 詳細については、[予約仮想マシン インスタンスによる仮想マシンの使用料の前払いとコスト削減](../../virtual-machines/prepay-reserved-vm-instances.md)に関するページを参照してください。

### <a name="how-to-view-reserved-instance-purchase-details"></a>予約インスタンスの購入の詳細を表示する方法

[Azure portal](https://aka.ms/reservations) の左側にある _[加入契約]_ メニューを使用するか、または [Azure EA Portal](https://ea.azure.com/) を使用して、予約インスタンスの購入の詳細を表示することができます。 左側のメニューで **[レポート]** を選択し、 _[使用状況の概要]_ タブで _[Charges by Services]\(サービス別料金\)_ セクションまで下にスクロールします。このセクションの一番下までスクロールすると、一覧の最後に、予約インスタンスの購入と使用状況が表示されます。予約インスタンスは、サービス名の横に `1 year` または `3 years` が示されます。たとえば、`Standard_DS1_v2 eastus 1 year` または `Standard_D2s_v3 eastus2 3 years` と表示されます。

### <a name="how-can-i-change-the-subscription-associated-with-reserved-instance-or-transfer-my-reserved-instance-benefits-to-a-subscription-under-the-same-account"></a>予約インスタンスと関連付けられたサブスクリプションを変更するか、予約インスタンス特典を同じアカウントのサブスクリプションに譲渡するにはどうすればよいですか?

予約インスタンス特典を受けるサブスクリプションを変更するには、次の手順を行います。

- [Azure portal](https://aka.ms/reservations) にサインインします。
- 同じアカウントの別のサブスクリプションを関連付けて、提供されるサブスクリプションのスコープを更新します。

予約のスコープの変更の詳細については、「[予約のスコープの変更](../reservations/manage-reserved-vm-instance.md#change-the-reservation-scope)」を参照してください。

### <a name="how-to-view-reserved-instance-usage-details"></a>予約インスタンスの使用量の詳細を表示する方法

予約インスタンスの使用量の詳細は、[Azure portal](https://aka.ms/reservations) に表示することができます。または、[Azure EA portal](https://ea.azure.com/) で、 _[レポート]_  >  _[使用状況の概要]_  > 、 _[Charges by Services]\(サービス別料金\)_ の順に移動して表示することもできます (課金情報を表示するアクセス権限を持つ EA のお客様の場合)。 予約インスタンスは、"Reservation" を含むサービス名で識別できます。たとえば、`Reservation-Base VM or Virtual Machines Reservation-Windows Svr (1 Core)` と表示されます。

使用状況の詳細と詳細レポートのダウンロード用 CSV には、予約インスタンスの使用量に関する追加情報が含まれます。 _[追加情報]_ フィールドは、予約インスタンスの使用量を確認するのに役立ちます。

Azure ハイブリッド特典を使用せずに Azure 予約 VM インスタンスを購入した場合、予約インスタンスによって 2 つのメーター (ハードウェアとソフトウェア) が生成されます。 Azure ハイブリッド特典を使用して予約インスタンスを購入した場合、予約インスタンスの使用量の詳細にソフトウェア メーターは表示されません。

### <a name="reserved-instance-billing"></a>予約インスタンスの課金

エンタープライズ カスタマーの場合は、Azure 前払いを利用して Azure 予約 VM インスタンスを購入します。 加入契約の Azure 前払いに、予約インスタンスを購入できる十分な残高が残っている場合、予約インスタンスの金額は Azure 前払いの残高から差し引かれ、購入分の請求書は送付されません。

Azure EA のお客様がお持ちの Azure 前払いが全額使用されている場合でも、予約インスタンスを購入することができます。この購入に対しては、次回の超過分請求で請求されます。 予約インスタンスの超過分がある場合は、通常の超過分請求に含まれます。

### <a name="reserved-instance-expiration"></a>予約インスタンスの有効期限

予約の有効期限の 30 日前と有効期限が切れたときに、メール通知が届きます。 予約の期限が切れても、デプロイされている VM は稼働し続け、従量課金制で課金されます。 詳細については、[予約仮想マシン インスタンス プラン](https://azure.microsoft.com/pricing/reserved-vm-instances/)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- Azure の予約の詳細については、「[Azure の予約とは](../reservations/save-compute-costs-reservations.md)」を参照してください。
- エンタープライズ予約のコストと使用状況の詳細については、「[Enterprise Agreement の予約のコストと使用状況を取得する](../reservations/understand-reserved-instance-usage-ea.md)」を参照してください。
- 価格の詳細については、「[Linux Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)」または「[Windows Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)」を参照してください。
