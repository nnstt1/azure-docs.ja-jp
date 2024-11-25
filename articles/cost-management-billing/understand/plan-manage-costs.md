---
title: Azure のコストの管理を計画する
description: Azure のコストを管理する計画を立て、Azure アカウントのコスト追跡および管理機能を使用する方法について説明します。
author: bandersmsft
ms.reviewer: sapnakeshari
tags: billing
ms.service: cost-management-billing
ms.subservice: common
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 63542b241d26964d1f24fbe6744b4cbd6bccd6c1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215728"
---
# <a name="plan-to-manage-azure-costs"></a>Azure のコストの管理を計画する

この記事では、Azure のコスト管理を計画する方法の基本について説明します。 Azure にサインアップしたら、支出を把握するために行えることがいくつかあります。

- サービスを追加する前に[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)や Azure 価格シートを使用することで、また、サービスを追加している間は Azure portal で推定コストを把握します。
- [予算](../costs/tutorial-acm-create-budgets.md)、[アラート](../costs/cost-mgt-alerts-monitor-usage-spending.md)、[コスト分析](../costs/quick-acm-cost-analysis.md)を使用してコストを監視します。
- 請求書に記載されている請求金額について、[詳しい使用状況ファイル](../manage/download-azure-invoice-daily-usage-date.md)と比較して確認します。
- [Billing](/rest/api/billing/) API と [Consumption](/rest/api/consumption/) API を使用して課金およびコストのデータを独自のレポート システムに統合します。
- マイクロソフト エンタープライズ契約 (EA)、クラウド ソリューション プロバイダー (CSP)、Azure スポンサー プランのお客様向けに別途用意されているリソースおよびツールを使用します。
- [Azure 無料アカウント](https://azure.microsoft.com/free/)で利用できる[最も人気の高い Azure サービスを 12 か月間無料](../manage/create-free-services.md)で活用します。 下記の推奨事項に加えて、[無料アカウントの課金の回避](../manage/avoid-charges-free-account.md)に関する記事もご覧ください。

Azure サブスクリプションを取り消す必要がある場合は、「[Azure サブスクリプションの取り消し](../manage/cancel-azure-subscription.md)」を参照してください。

## <a name="get-estimated-costs-before-adding-azure-services"></a>Azure サービスを追加する前にコストを見積もる

次のいずれかのツールを使用して、Azure サービスの使用にかかるコストを見積もります。
- Azure 料金計算ツール
- Azure 価格シート
- Azure portal

次のセクションの画像は、米ドルでの価格の例を示しています。 上記の価格は一例です。 実際のコストを示すためのものではありません。 

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>料金計算ツールを使用してオンラインでコストを見積もる

[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用して、追加したいサービスにかかる毎月の推定コストを把握します。 通貨を変更することにより、お住まいの地域の通貨で見積もり額を表示することができます。

![料金計算ツール メニューのスクリーンショット](./media/plan-manage-costs/pricing-calc.png)

ファースト パーティの Azure サービスについては、すべて推定コストを確認できます。 たとえば、次のスクリーンショットで、A1 Windows 仮想マシン (VM) を常に実行したままにしておく場合、コンピューティング時間での推定月額料金は 66.96 ドルになります。

![A1 Windows VM の推定月額料金を示す料金計算ツールのスクリーンショット](./media/plan-manage-costs/pricing-calc-vm.png)

上記の価格は一例です。 実際のコストを示すためのものではありません。

料金設定の詳細については、[料金に関する FAQ](https://azure.microsoft.com/pricing/faq/) を参照してください。 Azure 営業担当者と話したい場合は、FAQ ページの上部に表示されている電話番号までご連絡ください。

### <a name="review-prices"></a>価格を確認する

マイクロソフト エンタープライズ契約 (EA) または Microsoft 顧客契約 (MCA) を通じて Azure にアクセスできる場合は、お使いの Azure アカウントの価格シートを表示してダウンロードすることができます。 この価格シートは、すべての Azure サービスの価格が記載された Excel ファイルです。 詳細については、[Azure 価格の表示とダウンロード](../manage/ea-pricing.md)に関するページを参照してください。

他のサブスクリプションの種類の場合は、[Azure Retail Price API](/rest/api/cost-management/retail-prices/azure-retail-prices) を使用して標準の小売価格を取得できます。

### <a name="review-estimated-costs-in-the-azure-portal"></a>Azure portal で推定料金を検討する

サービスを追加している間は、1 か月あたりの推定コストを Azure portal で表示できます。 たとえば、Windows VM のサイズを選択すると、コンピューティング時間の推定月額料金が表示されます。

![例: 推定月額料金を示す A1 Windows VM](./media/plan-manage-costs/vm-size-cost.png)

上記の価格は一例です。 実際のコストを示すためのものではありません。

## <a name="monitor-costs-when-using-azure-services"></a>Azure サービス使用時にコストを監視する
次のツールを使用して、コストを監視できます。

- 予算とコストのアラート
- コスト分析

### <a name="track-costs-with-budgets-and-cost-alerts"></a>予算とコストのアラートを使用してコストを追跡する

[予算](../costs/tutorial-acm-create-budgets.md)を作成して、コストを管理し、異常な支出や浪費について、関係者に自動的に通知する[アラート](../costs/cost-mgt-alerts-monitor-usage-spending.md)を作成できます。

### <a name="explore-and-analyze-costs-with-cost-analysis"></a><a name="costs"></a> コスト分析を使用してコストを調査および分析する

Azure サービスを実行状態に移した後は、定期的にコストをチェックして Azure の利用状況を追跡してください。 コスト分析を使用することで、Azure の利用に関するコストの発生源を把握することができます。

[Azure portal で [コストの管理と請求] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade)に移動します。

画面の左側の **[コスト分析]** をクリックすると、サービス、場所、サブスクリプションなど、さまざまな角度から最新のコスト明細が表示されます。 サービスを追加するか購入したら、データが表示されるまで 24 時間待ちます。 既定では、コスト分析には、閲覧しているスコープのコストが表示されます。 たとえば、以下のスクリーンショットでは、Contoso の課金アカウントのコストが表示されています。 コスト分析で別のスコープに切り替えるには、[スコープ] ピルを使用します。 スコープの詳細については、「[スコープを理解して使用する](../costs/understand-work-scopes.md#scopes)」を参照してください。

![Azure Portal のコスト分析ビューのスクリーンショット](./media/plan-manage-costs/cost-analysis.png)

タグ、リソースの種類、期間など、さまざまなプロパティでフィルター処理することができます。 **[フィルターの追加]** をクリックして、プロパティのフィルターを追加し、フィルター処理する値を選択します。 コンマ区切り値 (.csv) ファイルにビューをエクスポートするには、 **[エクスポート]** を選択します。

さらに、グラフのラベルをクリックすると、そのラベルの日単位の支出履歴を表示できます。 たとえば、次のスクリーンショットでは、仮想マシンを選択したときに、VM を実行するための 1 日のコストが表示されています。

:::image type="content" source="./media/plan-manage-costs/cost-history.png" alt-text="Azure Portal の支出の履歴ビューのスクリーンショット" lightbox="./media/plan-manage-costs/cost-history.png" :::

## <a name="optimize-and-reduce-costs"></a>コストの最適化と削減

コスト管理の原則をよく理解していない場合は、「[Azure Cost Management を使用してクラウドへの投資を最適化する方法](../costs/cost-mgt-best-practices.md)」をお読みください。

Azure portal で、VM の自動シャットダウンと Advisor の推奨事項を使用して、Azure のコストを最適化し、削減することもできます。

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>VM の自動シャットダウンなどのコスト削減機能を検討する

シナリオによっては、Azure portal で VM の自動シャットダウンを構成できます。 詳しくは、[Azure Resource Manager を使用した VM の自動シャットダウン](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)に関する記事をご覧ください。

![ポータルの自動シャットダウン オプションのスクリーンショット](./media/plan-manage-costs/auto-shutdown.png)

自動シャットダウンは、電源オプションを使用して VM 内でシャットダウンする場合とは異なります。 自動シャットダウンでは、VM を停止して割り当てを解除することで課金を停止します。 詳細については、[Linux VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) および [Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) の料金に関する FAQ で VM の状態についての質問をご覧ください。

開発およびテスト環境のコスト削減機能については、[Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) のページをご覧ください。

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Azure Advisor の推奨事項を有効にして確認する

[Azure Advisor](../../advisor/advisor-overview.md) を使うと、使用量の少ないリソースを特定してコストを削減できます。 Azure portal で **Advisor** を検索します。

![Azure Portal の [Azure Advisor] ボタンのスクリーンショット](./media/plan-manage-costs/advisor-button.png)

左側から **[コスト]** を選択します。 アクションにつながる推奨情報が **[コスト]** タブに表示されます。

![Advisor のコストに関する推奨事項の例を示すスクリーンショット](./media/plan-manage-costs/advisor-action.png)

上記の価格は一例です。 実際のコストを示すためのものではありません。

コスト節約のための Advisor の推奨事項に関するガイド付きチュートリアルについては、「[推奨事項に従ってコストを最適化する](../costs/tutorial-acm-opt-recommendations.md)」をご覧ください。

## <a name="integrate-with-billing-and-consumption-apis"></a>Billing および Consumption API と統合する

Azure の [Billing](/rest/api/billing/) と [Consumption](/rest/api/consumption/) API を使用して、請求データとコスト データをプログラムから入手することもできます。 課金対象の使用状況を取得するには、RateCard API と Usage API を組み合わせて使用します。 詳細については、「[Microsoft Azure リソースの消費を把握する](../manage/consumption-api-overview.md#usage-details-api)」をご覧ください。

## <a name="additional-resources-and-special-cases"></a><a name="other-offers"></a> その他のリソースと特殊なケース

### <a name="ea-csp-and-sponsorship-customers"></a>EA、CSP、およびスポンサープランのお客様
使用を開始するには、アカウント マネージャーまたは Azure パートナーに連絡してください。

| プラン | リソース |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise Agreement (EA) | [EA ポータル](https://ea.azure.com/)、[ヘルプ ドキュメント](https://ea.azure.com/helpdocs)、および [Power BI レポート](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Cloud Solution Provider (CSP) | プロバイダーに連絡してください |
| Azure スポンサー プラン | [スポンサー プラン ポータル](https://www.microsoftazuresponsorships.com/) |

大規模な組織の IT を管理している場合は、[Azure エンタープライズ スキャフォールディング](/azure/architecture/cloud-adoption-guide/subscription-governance)に関する記事と、[エンタープライズ IT に関するホワイト ペーパー](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (.pdf をダウンロード、英語のみ) を読むことをお勧めします。

### <a name="enterprise-agreement-cost-views-in-the-azure-portal"></a><a name="EA"></a>Azure portal での Enterprise Agreement のコスト ビュー

Enterprise コスト ビューは、現在、パブリック プレビューの中にあります。 注意する項目:

- サブスクリプションのコストは、使用量に基づいて計算されており、前払い額、超過、含まれる数量、調整、および税は含まれていません。 実際の料金は、登録レベルで計算されます。
- Azure portal に表示される金額が、エンタープライズ ポータルに表示されるものとは異なっている場合があります。 エンタープライズ ポータルでの更新による変更が Azure portal に表示されるまで、数分かかる可能性があります。
- コストが表示されない場合は、次の理由のいずれかが原因として考えられます。
    - サブスクリプション レベルでアクセス許可がない。 エンタープライズ コスト ビューを表示するには、課金データ閲覧者、閲覧者、共同作業者、所有者のいずれかのサブスクリプション レベルである必要があります。
    - お客様はアカウント所有者であり、[AO ビューの請求金額] 設定が登録管理者によって無効にされている。  登録管理者に問い合わせて、コストにアクセスできるように設定してもらってください。
    - お客様は部門管理者であり、 **[DA ビューの請求金額]** 設定が登録管理者によって無効にされている。  登録管理者に問い合わせて、アクセスできるように設定してもらってください。
    - チャネル パートナーを通して Azure を購入したが、パートナーが価格情報を公開していない。  
- Enterprise Portal でコストのアクセスに関連する設定を更新した場合、変更が Azure portal に表示されるまで数分の遅延があります。
- ダイレクト EA のお客様は、[Azure portal](https://portal.azure.com/) でコスト関連の設定を更新できます。 [ポリシー] メニューに移動して、設定を変更します。
- 使用制限と請求書ガイダンスは、EA サブスクリプションには適用されません。

### <a name="check-your-subscription-and-access"></a>サブスクリプションとアクセスを確認する

コストを表示するには、コストまたは課金の情報へのアカウント レベルまたはサブスクリプション レベルのアクセス権が必要です。 課金アカウントの種類によってアクセス権は異なります。 課金アカウントの詳細および課金アカウントの種類の確認については、「[Azure portal での課金アカウントの表示](../manage/view-all-accounts.md)」を参照してください。

Microsoft Online Services プログラム (MOSP) の課金アカウント経由で Azure にアクセスできる場合は、「[Azure の課金情報へのアクセスの管理](../manage/manage-billing-access.md)」を参照してください。

マイクロソフト エンタープライズ契約 (EA) の課金アカウント経由で Azure にアクセスできる場合は、「[Azure の Azure Enterprise Agreement 管理者ロールを理解する](../manage/understand-ea-roles.md)」を参照してください。

Microsoft 顧客契約 (MCA) の課金アカウント経由で Azure にアクセスできる場合は、「[Azure での Microsoft 顧客契約の管理ロールを理解する](../manage/understand-mca-roles.md)」を参照してください。

### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>サービス インシデントに対するサービス レベル アグリーメントの控除を要求する

サービス レベル アグリーメント (SLA) では、稼働時間と接続に関する Microsoft の確約内容について説明しています。 Azure サービスで稼働時間や接続に影響を与える問題 (しばしば "*停止*" と呼ばれる) が発生すると、サービス インシデントが報告されます。 SLA に記載されている各サービスのサービス レベルが達成および維持されていない場合は、月額サービス料金の一部が控除対象になる場合があります。

控除を要求するには、次の手順を実行します。

[Azure portal](https://portal.azure.com/) にサインインします。 複数のアカウントをお持ちの場合は、Azure のダウンタイムの影響を受けたアカウントを必ず使用してください。 次に、新しいサポート リクエストを作成します。

**[問題の種類]** で **[課金]** を選択し、 **[問題タイプ]** で **[Refund Request]\(返金のリクエスト\)** を選択します。

SLA の控除を求めていることを指定する詳細を追加し、日付、時刻、タイムゾーン、および影響を受けたサービス (VM、Web サイトなど) を記載します。

最後に、連絡先情報の詳細を確認し、 **[作成]** を選択して要求を送信します。

一部のサービスには、SLA を適用するための前提条件があります。 たとえば、仮想マシンでは、2 つ以上のインスタンスが同じ可用性セットにデプロイされている必要があります。

詳細については、「[サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)」および「[Azure サービスの SLA 概要](https://azure.microsoft.com/support/legal/sla/summary/)」ドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ
- 予算オーバーを防ぐために、[使用制限](../manage/spending-limit.md)の使い方を確認します。
- [Azure コストの分析](../costs/quick-acm-cost-analysis.md)を開始します。
