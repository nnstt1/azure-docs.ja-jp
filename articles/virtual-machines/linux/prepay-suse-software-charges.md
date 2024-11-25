---
title: ソフトウェア プランに前払いする - Azure Reservations
description: ソフトウェア プランを前払いして、従量課金制のコストより費用を節約する方法について説明します。
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: banders
ms.openlocfilehash: c46215bde33f726aba61ad42bf27a14ac2edb4dc
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129216497"
---
# <a name="prepay-for-azure-software-plans"></a>Azure ソフトウェア プランへの前払い

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: フレキシブルなスケール セット 

Azure での SUSE および RedHat ソフトウェアの使用に対して前払いすると、従量課金制のコストより費用を節約できます。 この割引は SUSE および RedHat の測定にのみ適用され、仮想マシンの使用には適用されません。 仮想マシンの予約を別に購入することで、さらに節約できます。

SUSE および RedHat のソフトウェア プランは、Azure portal で購入できます。 プランを購入するには:

- 少なくとも 1 つのエンタープライズ サブスクリプションまたは従量課金制料金の個々のサブスクリプションで所有者ロールを持つ必要があります。
- エンタープライズ サブスクリプションの場合、[EA ポータル](https://ea.azure.com/)で **[予約インスタンスを追加します]** を有効にする必要があります。 その設定が無効になっている場合は、ユーザーはサブスクリプションの EA 管理者である必要があります。
- クラウド ソリューション プロバイダー (CSP) プログラムの場合、管理エージェントまたはセールス エージェントがソフトウェア プランを購入できます。

## <a name="buy-a-software-plan"></a>ソフトウェア プランを購入する

1. Azure portal にサインインし、[予約](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)に移動します。
2. **[追加]** をクリックして、購入するソフトウェア プランを選択します。
必須フィールドに必要事項を入力します。 購入する属性に一致するすべての SUSE Linux VM または RedHat VM に割引が適用されます。 割引が適用されるデプロイの実際の数は、選択したスコープと数量によって変わります。
3. サブスクリプションを選択します。 それがプランの支払いに使用されます。
サブスクリプションの支払方法に対して、予約の初期コストが課金されます。 サブスクリプションの種類は、マイクロソフト エンタープライズ契約 (プラン番号: MS-AZR-0017P または MS-AZR-0148P) または従量課金制料金の個々の契約 (プラン番号:MS-AZR-0003P または MS-AZR-0023P)。
    - Enterprise サブスクリプションの場合、登録の Azure 前払い (旧称: 年額コミットメント) の残高から料金が差し引かれるか、超過分として課金されます。
    - 従量課金制料金の個々のサブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。
4. スコープを選択します。 1 つのサブスクリプションまたは複数のサブスクリプション (共有スコープ) をスコープにすることができます。
    - [単一サブスクリプション] - プランの割引は、サブスクリプションでのマシンの使用に適用されます。
    - [共有] - プランの割引は、課金コンテキスト内のすべてのサブスクリプションでの一致するインスタンスに適用されます。 エンタープライズのお客様の場合、課金コンテキストが対象の登録であり、登録内のすべてのサブスクリプションが含まれます。 従量課金制料金の個別プランを利用されるお客様の場合、従量課金制料金サブスクリプションをアカウント管理者が作成したあらゆる個別プランで課金されます。
    - 管理グループ - 管理グループと課金スコープの両方に属するサブスクリプションのリストの中で、一致するリソースに予約割引を適用します。
    - 1 つのリソース グループ - 選択されたリソース グループ内の一致するリソースにのみ予約割引を適用します。
5. 製品を選択し、VM のサイズとイメージの種類を選択します。 割引は、選択した VM サイズにのみ適用されます。
6. 期間として 1 年または 3 年を選択します。
7. 数量を選択します。これは、課金の割引を受けられる前払いの VM インスタンス数です。
8. 製品をカートに追加し、確認して購入します。

予約割引が、前払いしたソフトウェア測定に自動的に適用されます。 VM の計算料金はプランの対象外です。 VM の予約を別途購入することができます。

## <a name="discount-applies-to-different-suse-vm-sizes"></a>異なる SUSE VM サイズに対する割引の適用

予約 VM インスタンスと同様に、SUSE Linux プランは、インスタンス サイズの柔軟性を提供します。 購入した SUSE プランとは異なるサイズの VM をデプロイする場合でも、割引が適用されます。 詳細については、[ソフトウェア プランの割引が適用されるしくみ](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)に関するページを参照してください。

## <a name="redhat-plan-discount"></a>RedHat プランの割引

プランは、Red Hat Enterprise Linux の仮想マシンに対してのみ使用できます。 RedHat Enterprise Linux SAP HANA VM または RedHat Enterprise Linux SAP Business Apps VM には、割引は適用されません。

RedHat プランの割引は、購入時に選択した VM サイズにのみ適用されます。 RHEL プランは、購入した後で返金または交換することはできません。


## <a name="cancellation-and-exchanges-not-allowed"></a>キャンセルと交換はできません

購入した SUSE または RedHat プランを、キャンセルまたは交換することはできません。 適切なプランを購入するためには、使用状況を確認してください。 購入するものを決めるときに役立つ情報については、[ソフトウェア プランの割引が適用されるしくみ](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)に関するページを参照してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次のステップ

予約を管理する方法については、「[Azure の予約の管理](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)」をご覧ください。

詳細については、以下の記事をお読みください。

- [Azure の予約とは](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure での予約の管理](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [SUSE の予約割引の適用方法について](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)
- [従量課金制サブスクリプションの予約使用量について](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
