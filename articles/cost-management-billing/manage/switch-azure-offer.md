---
title: Azure サブスクリプション オファーの変更
description: Azure サブスクリプションを変更し、別のプランに切り替える方法について説明します。
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/27/2021
ms.author: banders
ms.openlocfilehash: 73f40b059e5577ba893b26ab715a8158e3a33482
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124836912"
---
# <a name="change-your-azure-subscription-to-a-different-offer"></a>Azure サブスクリプションを別のオファーに変更する

[従量課金制サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0003p/)をご利用のお客様は、Azure portal で、ご利用の Azure サブスクリプションを別のオファーに切り替えることができます。 たとえば、この機能を使えば、[Visual Studio サブスクライバー向けの月単位のクレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効活用できます。

お持ちの Visual Studio サブスクリプションの期限が切れた場合は、[従量課金制サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0003p/)に切り替えることができます。

**無料試用版からアップグレードしたいだけの場合:** [サブスクリプションのアップグレード](upgrade-azure-subscription.md)に関するページを参照してください。

## <a name="whats-supported"></a>サポートされる操作

従量課金制サブスクリプションを次のプランに切り替えることができます。

- [開発テスト用の従量課金制プラン](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)

また、期限が切れた [Visual Studio Enterprise サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0063p/)から[従量課金制サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0003p/)に切り替えることもできます。 

> [!NOTE]
> その他のプランの変更については、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

## <a name="switch-from-a-pay-as-you-go-subscription"></a>従量課金制サブスクリプションから切り替える

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[サブスクリプション]** に移動して、従量課金制サブスクリプションを選択します。
1. ページの上部で、 **[プランの切り替え]** を選択します。 このオプションは、従量課金制サブスクリプションをご利用の場合で、なおかつ最初の請求期間が完了している場合にのみ選択できます。  
    :::image type="content" source="./media/switch-azure-offer/switch-offer.png" alt-text="サブスクリプションの詳細と [プランの切り替え] オプションを示すスクリーンショット。" lightbox="./media/switch-azure-offer/switch-offer.png" :::
1. 現在のサブスクリプションから移行可能なプランの一覧から、目的のプランを選択します。 この一覧の内容はアカウントが関連付けられているメンバーシップに基づいて決まります。 何も表示されない場合は、[移行可能なプランの一覧](#whats-supported)をチェックして、適切なメンバーシップを持っていることを確認してください。 **[次へ]** を選択します。
    :::image type="content" source="./media/switch-azure-offer/select-offer.png" alt-text="切り替えるプランを選択する場所を示すスクリーンショット。" lightbox="./media/switch-azure-offer/select-offer.png" :::
    切り替え先のプランによっては、切り替えの影響についての注意が表示される場合があります。 続行する前にその一覧をよく確認し、その指示に従ってください。 また、電話番号を確認する必要があります。
1. 注意事項や電話番号を確認したら、 **[プランの切り替え]** を選択します。
1. これでサブスクリプションは新しいオファーに切り替わりました。

## <a name="convert-a-visual-studio-enterprise-subscription"></a>Visual Studio Enterprise サブスクリプションを変換する

Visual Studio Enterprise サブスクリプションの有効期限が切れると、無効になります。 そのサブスクリプションで Azure サービスを引き続き使用するには、それを従量課金制サブスクリプションに変換する必要があります。

>[!NOTE]
> - 従量課金制サブスクリプションにアップグレードすると、毎月のクレジットの受け取りが停止し、毎月の使用量に基づく月単位の支払い方法での課金が開始されます。
> - 従量課金制サブスクリプションに切り替えると、使用量と課金に関する情報が失われます。 プランを切り替える前に、[過去の請求書と使用状況データを保存する](download-azure-invoice-daily-usage-date.md)ことをお勧めします。

サブスクリプションを変換するには:

1. **[サブスクリプション]** に移動し、お使いの Visual Studio Enterprise サブスクリプションを選択します。
1. ページの上部に、`Your Visual Studio subscription is expired and has been disabled. To continue using this subscription, please click here to convert to a pay-as-you-go subscription` というバナーが表示されます。 そのバナーを選択します。  
    :::image type="content" source="./media/switch-azure-offer/visual-studio-expired-banner.png" alt-text="有効期限が切れたサブスクリプションとそれを変換するバナーを示すスクリーンショット。" lightbox="./media/switch-azure-offer/visual-studio-expired-banner.png" :::
1. [プランの切り替え] ページの [プランの選択] タブで、従量課金制オプションを選択して、 **[次へ]** を選択します。  
    :::image type="content" source="./media/switch-azure-offer/select-offer-pay-as-you-go.png" alt-text="[プランの切り替え] オプションを示すスクリーンショット。" lightbox="./media/switch-azure-offer/select-offer-pay-as-you-go.png" :::
1. [支払い方法の選択] タブで、 **[支払い方法の選択]** を選択し、支払い方法の情報を入力して、 **[次へ]** を選択します。  
1. [確認して変換] ページでは、必要に応じてサブスクリプション名を変更できます。 契約情報を確認し、 **[同意する]** オプションを選択して、ページの下部にある **[プランの切り替え]** を選択します。  
    :::image type="content" source="./media/switch-azure-offer/switch-offer-visual-studio-agreement.png" alt-text="サブスクリプション名と契約への同意が含まれる [確認して変換] タブを示すスクリーンショット。" lightbox="./media/switch-azure-offer/switch-offer-visual-studio-agreement.png" :::

**[サブスクリプション]** に移動して、変換された新しいサブスクリプションを表示します。


## <a name="frequently-asked-questions-for-pay-as-you-go-subscriptions"></a>従量課金制サブスクリプションに関してよく寄せられる質問

以下のセクションでは、従量課金制サブスクリプションからの切り替えに関してよく寄せられる質問に回答します。

### <a name="what-is-an-azure-offer"></a>Azure プランとは

Azure オファーとは、ご利用の Azure サブスクリプションの *種類* です。 たとえば、[従量課金制料金のサブスクリプション](https://azure.microsoft.com/offers/ms-azr-0003p/)、[Azure イン オープン プラン](https://azure.microsoft.com/offers/ms-azr-0111p/)、[Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) はすべて Azure プランです。 各サービスは異なる[使用条件](https://azure.microsoft.com/support/legal/offer-details/)を有し、特別な利点を持っています。 ご利用のサブスクリプションのプランは、サブスクリプションの詳細ページに表示されます。

:::image type="content" source="./media/switch-azure-offer/subscription-details.png" alt-text="プランの種類を表示するサブスクリプションの詳細ページ" lightbox="./media/switch-azure-offer/subscription-details.png" :::

### <a name="why-dont-i-see-the-switch-offer-option"></a>[プランの切り替え] オプションが表示されないのはなぜですか

次の場合、 **[プランの切り替え]** オプションは表示されません。

* [従量課金制料金のサブスクリプション](https://azure.microsoft.com/offers/ms-azr-0003p/)を持たない場合。 現時点では、別のプランに切り替えられるのは従量課金制料金のサブスクリプションのみです。
  * [無料試用版](https://azure.microsoft.com/free/)をご利用の場合は、[従量課金制にアップグレードする](upgrade-azure-subscription.md)方法を確認してください。
  * 別のサブスクリプションからオファーを切り替えるには、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
* 最初の請求期間中である場合、プランを切り替えるには、最初の請求期間が終わるまで待つ必要があります。

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>次の場合、"現在、お客様がお住まいのリージョンまたは国で利用できるプランはありません。" と表示されるのはなぜですか。

* プラン切り替えの対象ではない可能性があります。 [切り替え可能なプランの一覧](#whats-supported)をチェックして、Visual Studio または Bizspark で適切に特典をアクティブ化していることを確認してください。
* 国や地域によっては一部のプランをご利用いただけません。

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Azure のプランを切り替えると、サービスと請求はどうなりますか?

Azure のプランを切り替えるとどうなるかを以下で詳しく説明します。

#### <a name="no-service-downtime"></a>サービスのダウンタイムは発生しない

サブスクリプションに関連付けられているユーザーにサービスのダウンタイムは生じません。 ただし、切り替え先のプランによっては、制限がある場合があります。 たとえば、一部のオファーでは運用環境での使用が禁止されているため、該当する場合は、運用環境のリソースを別のサブスクリプションに移動する必要があります。

#### <a name="quota-increases-are-reset"></a>クォータの増加はリセットされる

プランを切り替えると、[既定の制限を上回る制限またはクォータの増加](../../azure-portal/supportability/regional-quota-requests.md)がリセットされます。 既定の制限を超えるリソースがあっても、サービスのダウンタイムはありません。 たとえば、サブスクリプションで 200 コアを使用しているとき、オファーを切り替えるとコア クォータが既定の 20 コアにリセットされます。 200 コアを使用している VM は影響を受けず、実行を継続します。 ただし、クォータ増加をもう一度要求しないのであれば、これ以上はコアをプロビジョニングできません。

#### <a name="billing"></a>課金

切り替えを行った日に、すべての未処理料金の請求書が生成されます。 その後、サブスクリプションは新しいオファーの価格条件に従って課金されます。 サブスクリプションの請求日はオファーを変更した日付に変更されます。 プラン変更前の使用状況と課金データは保持されないので、切り替え前にコピーをダウンロードしておくことをお勧めします。

### <a name="can-i-migrate-from-a-subscription-with-pay-as-you-go-rates-to-cloud-solution-provider-csp-or-enterprise-agreement-ea"></a>従量課金制料金のサブスクリプションからクラウド ソリューション プロバイダー (CSP) またはマイクロソフト エンタープライズ契約 (EA) に移行することはできますか?

* CSP に移行するには、「[サブスクライバーと CSP の間で Azure サブスクリプションを譲渡する](transfer-subscriptions-subscribers-csp.md)」を参照してください。
* 従量課金制サブスクリプション (Azure プラン ID MS-AZR-0003P) または従量課金制料金の Azure プラン (Azure プラン ID MS-AZR-0017G) を持っていて、EA 登録に移行したい場合は、登録管理者にアカウントを EA に追加してもらってください。 登録案内メールの指示に従って、サブスクリプションを EA 加入契約に移行します。 詳細については、「[Azure サブスクリプションまたはアカウントの所有権を変更する](ea-portal-administration.md#change-azure-subscription-or-account-ownership)」をご覧ください。

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>データとサービスを新しいサブスクリプションに移行できますか?

* リソースを直接、新しいサブスクリプションに移行することができます。「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。
* Azure サブスクリプションとそこに含まれるすべての所有権を別のユーザーに譲渡するには、「[Transferring ownership of an Azure subscription](billing-subscription-transfer.md)」 (Azure サブスクリプションの所有権を譲渡する) を参照してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- [コストの分析を開始する](../costs/quick-acm-cost-analysis.md)