---
title: 予約容量を使用して計算を前払いする - Azure Database for MySQL
description: 予約容量を使用して Azure Database for MySQL 計算リソースを前払いする
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 10/06/2021
ms.openlocfilehash: fb679c4cfdcda3a34ea43bace8a9aa546e542acf
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658153"
---
# <a name="prepay-for-azure-database-for-mysql-compute-resources-with-reserved-instances"></a>予約インスタンスを使用して Azure Database for MySQL 計算リソースを前払いする

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Azure Database for MySQL では計算リソースを前払いすることで、従量課金制よりコストを節約できるようになりました。 Azure Database for MySQL の予約インスタンスを使用すると、MySQL サーバーを 1 年分または 3 年分前払いすることで計算コストを大幅に引き下げることができます。 Azure Database for MySQL の予約容量を購入するには、Azure リージョン、デプロイの種類、パフォーマンス レベル、および期間を指定する必要があります。 </br>

## <a name="how-does-the-instance-reservation-work"></a>インスタンスの予約はどのように動作しますか。
特定の Azure Database for MySQL サーバーに予約を割り当てる必要はありません。 既に実行している Azure Database for MySQL または新しくデプロイされたものには、予約価格の特典が自動的に適用されます。 予約を購入すると、計算コストを 1 年間または 3 年間分前払いすることになります。 予約を購入するとすぐに、予約の属性に一致する Azure Database for MySQL のコンピューティング料金は従量課金制で課金されなくなります。 予約には、MySQL データベース サーバーに関連するソフトウェア、ネットワーク、またはストレージの料金は含まれません。 予約期間が満了した時点で、課金特典の有効期限は切れ、従量課金料金が Azure Database for MySQL に適用されます。 予約は自動更新されません。 価格の詳細については、[Azure Database for MySQL の予約容量オファー](https://azure.microsoft.com/pricing/details/mysql/)に関するページを参照してください。 </br>

Azure Database for MySQL の予約容量は、[Azure portal](https://portal.azure.com/) で購入できます。 予約の支払いは、[前払いまたは月払い](../cost-management-billing/reservations/prepare-buy-reservation.md)で行います。 予約容量を購入するには:

* 少なくとも 1 つのエンタープライズ サブスクリプションまたは従量課金制料金の個々のサブスクリプションで所有者ロールである必要があります。
* Enterprise サブスクリプションの場合、[EA ポータル](https://ea.azure.com/)で **[予約インスタンスを追加します]** を有効にする必要があります。 または、その設定が無効になっている場合は、ユーザーはサブスクリプションの EA 管理者である必要があります。
* クラウド ソリューション プロバイダー (CSP) プログラムの場合、管理者エージェントまたはセールス エージェントのみが Azure Database for MySQL の予約容量を購入できます。 </br>

エンタープライズおよび従量課金制のお客様に対する予約購入の課金方法の詳細については、「[エンタープライズ加入契約に適用される Azure の予約の使用状況について](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)」および「[従量課金制サブスクリプションに適用される Azure の予約の使用状況について](../cost-management-billing/reservations/understand-reserved-instance-usage.md)」をご覧ください。

## <a name="reservation-exchanges-and-refunds"></a>予約の交換と返金

予約は同じ種類の別の予約に交換できます。また、Azure Database for MySQL - 単一サーバーの予約をフレキシブル サーバーと交換できます。 また、予約が不要になった場合は払い戻しができます。 予約の交換または払い戻しを行うには、Azure portal を使用できます。 詳しくは、「[Azure の予約のセルフサービスによる交換と払戻](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)」を参照してください。

## <a name="reservation-discount"></a>予約割引

予約インスタンスを使用すると、コンピューティング コストを最大 67% 節約できます。 適用される割引を見つけるには、Azure portal で [[予約]](https://aka.ms/reservations) ブレードにアクセスし、価格レベル別とリージョン別の節約を選択してください。 予約インスタンスで 1 年分または 3 年分を前払いすることにより、ワークロード、予算、予測が管理しやすくなります。 ビジネス ニーズの変化に応じて予約を交換またはキャンセルすることができます。


## <a name="determine-the-right-database-size-before-purchase"></a>購入する前に適切なデータベース サイズを決定する

予約のサイズは、既存のまたはすぐにデプロイされる予定のサーバー (特定のリージョン内で同じパフォーマンス階層とハードウェア世代を使用するもの) で使用される計算量の合計に基づいて決める必要があります。</br>

たとえば、1 つの汎用の Gen5 - 32 仮想コア MySQL データベースと、2 つのメモリ最適化済みの Gen5 - 16 仮想コア MySQL データベースを実行しているとします。 さらに、来月中に汎用の Gen5 - 32 仮想コア データベース サーバーを 1 つと、メモリ最適化済みの Gen5 - 16 仮想コア データベース サーバーを 1 つデプロイする予定だとします。 少なくとも 1 年間はこれらのリソースが必要になることがわかっているとします。 この場合、単一データベースの汎用 - Gen5 用に 64 (2x32) 個の仮想コア 1 年予約分と、単一データベース メモリ最適化済み - Gen5 用に 48 (2x16 + 16) 個の仮想コア 1 年予約分を購入する必要があります。


## <a name="buy-azure-database-for-mysql-reserved-capacity"></a>Azure Database for MySQL の予約容量を購入する

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[すべてのサービス]**  >  **[予約]** を選択します。
3. **[追加]** を選択し、[購入予約] ペインで **[Azure Database for MySQL]** を選択して MySQL データベースの新しい予約を購入します。
4. 必須フィールドに入力します。 選択した属性と一致する既存または新規のデータベースが、予約容量の割引を受けられます。 割引を受ける Azure Database for MySQL サーバーの実際の数は、選択したスコープと数量によって変わります。


:::image type="content" source="media/concepts-reserved-pricing/mysql-reserved-price.png" alt-text="予約価格の概要":::


次の表で、必須フィールドについて説明します。

| フィールド | 説明 |
| :------------ | :------- |
| サブスクリプション   | Azure Database for MySQL の予約容量の予約の支払いに使用するサブスクリプション。 サブスクリプションの支払方法に対して、Azure Database for MySQL の予約容量の予約の前払いコストが課金されます。 サブスクリプションの種類は、マイクロソフト エンタープライズ契約 (プラン番号:MS-AZR-0017P または MS-AZR-0148P) または従量課金制料金の個々の契約 (プラン番号:MS-AZR-0003P または MS-AZR-0023P)。 Enterprise サブスクリプションの場合、登録の Azure 前払い (旧称: 年額コミットメント) の残高から料金が差し引かれるか、超過分として課金されます。 従量課金制料金の個々のサブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。
| Scope | 1 つのサブスクリプションまたは複数のサブスクリプション (共有スコープ) を仮想コアの予約のスコープにすることができます。 以下を選択した場合: </br></br> **共有** - 仮想コアの予約割引は、課金のコンテキスト内にある任意のサブスクリプションで実行されている Azure Database for MySQL サーバーに適用されます。 エンタープライズのお客様の場合、共有スコープが対象の登録であり、登録内のすべてのサブスクリプションが含まれます。 従量課金制のお客様の場合、共有スコープは、アカウント管理者が作成するすべての従量課金制サブスクリプションです。</br></br> **単一サブスクリプション** - 仮想コアの予約割引はこのサブスクリプションの Azure Database for MySQL サーバーに適用されます。 </br></br> **1 つのリソース グループ** - 予約割引は、選択したサブスクリプションおよびそのサブスクリプション内の選択したリソース グループ内の Azure Database for MySQL サーバーに適用されます。
| リージョン | Azure Database for MySQL 予約容量の予約の対象となる Azure リージョン。
| デプロイの種類 | 予約を購入する Azure Database for MySQL リソースの種類。
| パフォーマンス レベル | Azure Database for MySQL サーバーのサービス レベル。
| 期間 | 1 年
| Quantity | Azure Database for MySQL の予約容量の予約内で購入される計算リソース数。 この数量は、予約し、請求時に割り引きを受ける、選択された Azure リージョンとパフォーマンス レベルに含まれる仮想コアの数です。 たとえば、米国東部リージョンで、合計コンピューティング容量を Gen5 仮想コア 16 個とする Azure Database for MySQL サーバーを実行している場合、あるいは実行する予定の場合、すべてのサーバーを最大限に活用するため、数量に 16 を指定します。

## <a name="reserved-instances-api-support"></a>予約インスタンス API サポート

Azure API を使用して、Azure サービスまたはソフトウェアの予約に関する組織の情報をプログラムで取得します。 たとえば、次の用途に API を使用します。

- 購入する予約を検索する
- 予約の購入
- 購入した予約を表示する
- 予約の利用を表示および管理する
- 予約を分割または統合する
- 予約の範囲を変更する

詳細については、「[APIs for Azure reservation automation](../cost-management-billing/reservations/reservation-apis.md)」(Azure の予約自動化の API) を参照してください。

## <a name="vcore-size-flexibility"></a>仮想コアのサイズの柔軟性

同じパフォーマンス レベルとリージョン内であれば、予約容量のベネフィットを失うことなく、仮想コアのサイズを柔軟にスケールアップまたはスケールダウンできます。 

## <a name="how-to-view-reserved-instance-purchase-details"></a>予約インスタンスの購入の詳細を表示する方法

[Azure portal の左側にある [予約] メニュー](https://aka.ms/reservations)を使用して予約インスタンスの購入詳細を表示できます。 詳細については、「[Azure Database for MySQL に対する予約割引の適用方法](../cost-management-billing/reservations/understand-reservation-charges-mysql.md)」を参照してください。

## <a name="reserved-instance-expiration"></a>予約インスタンスの有効期限

予約の有効期限の 30 日前と有効期限が切れたときに、メール通知が届きます。 予約の期限が切れても、デプロイされている VM は稼働し続け、従量課金制で課金されます。 詳細については、[Azure Database for MySQL の予約インスタンス](../cost-management-billing/reservations/understand-reservation-charges-mysql.md)に関するページを参照してください。

## <a name="need-help--contact-us"></a>ヘルプが必要ですか? お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください

## <a name="next-steps"></a>次のステップ

仮想コアの予約割引は、Azure Database for MySQL の予約容量の予約スコープと属性に一致する複数の Azure Database for MySQL サーバーに自動的に適用されます。 Azure Database for MySQL の予約容量の予約スコープは、Azure portal、PowerShell、CLI、または API で更新できます。 </br></br>
Azure Database for MySQL の予約容量を管理する方法については、Azure Database for MySQL の予約容量の管理に関するページを参照してください。

Azure の予約の詳細については、次の記事を参照してください。

* [Azure の予約とは](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Azure の予約の管理](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Azure の予約割引を理解する](../cost-management-billing/reservations/understand-reservation-charges.md)
* [従量課金制サブスクリプションの予約使用量について](../cost-management-billing/reservations/understand-reservation-charges-mysql.md)
* [エンタープライズ加入契約の予約使用量について](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [パートナー センターのクラウド ソリューション プロバイダー (CSP) プログラムでの Azure の予約](/partner-center/azure-reservations)