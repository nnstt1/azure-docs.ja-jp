---
title: Azure Cost Management のコストのアラートで使用量と支出を監視する
description: この記事では、Cost Management のコストのアラートが使用量と支出の監視にどのように役立つかについて説明します。
author: bandersmsft
ms.author: banders
ms.date: 10/07/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 547dac5984ecc88c452913f406196ade27e74fcd
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705938"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>コストのアラートを使用して使用量と支出を監視する

この記事は、Cost Management のアラートによる Azure の使用量と支出の監視について理解して使用するのに役立ちます。 コストのアラートは、Azure リソースが消費されると自動的に生成されます。 アラートでは、コスト管理と課金に関するすべてのアクティブなアラートがまとめて 1 か所に表示されます。 使用量が指定されているしきい値に達すると、Cost Management でアラートが生成されます。 コストのアラートには、予算アラート、クレジット アラート、部署課金クォータ アラートの 3 種類があります。

## <a name="budget-alerts"></a>予算アラート

予算アラートでは、使用量とコストに基づいて、[予算のアラート条件](tutorial-acm-create-budgets.md)で定義されている額に支出が達するか、それを超えると、通知されます。 Cost Management の予算は、Azure portal または [Azure Consumption](/rest/api/consumption) API を使用して作成します。

Azure portal では、予算はコストによって定義されます。 Azure Consumption API を使用する場合は、コストまたは消費量を使用して予算を定義します。 予算アラートでは、コスト ベースと使用量ベースの両方の予算がサポートされています。 予算アラートは、予算アラート条件が満たされると常に自動的に生成されます。 Azure portal では、すべてのコストのアラートを表示できます。 アラートが生成されるたびに、コストのアラートに表示されます。 予算のアラート受信者一覧のユーザーに、アラート メールも送信されます。

Budget API を使用して、別の言語で電子メール アラートを送信できます。 詳細については、「[予算のアラート メールでサポートされているロケール](manage-automation.md#supported-locales-for-budget-alert-emails)」を参照してください。

## <a name="credit-alerts"></a>クレジット アラート

Azure 前払い (旧称: 年額コミットメント) が消費されるとクレジット アラートによって通知されます。 Azure 前払いは、マイクロソフト エンタープライズ契約を使用する組織用です。 クレジット アラートは、Azure 前払いクレジットの使用量が 90% および 100% になると自動的に生成されます。 アラートが生成されるたびに、コストのアラートと、アカウント所有者に送信される電子メールに反映されます。

## <a name="department-spending-quota-alerts"></a>部署課金クォータ アラート

部署課金クォータ アラートでは、部門の課金がクォータの固定しきい値に達すると通知されます。 課金クォータは EA ポータルで構成されます。 しきい値に達すると、部署の所有者に対して電子メールが生成され、コストのアラートに表示されます。 たとえば、クォータの 50% や 75% などです。

## <a name="supported-alert-features-by-offer-categories"></a>プランのカテゴリ別のサポートされているアラート機能

サポートされているアラートの種類は、使用している Azure アカウントの種類 (Microsoft プラン) によって異なります。 次の表は、Microsoft の各種オファーでサポートされているアラート機能を示します。 Microsoft オファーの詳細な一覧については、「[Understand Cost Management data (Cost Management データの概要)](understand-cost-mgt-data.md)」で確認できます。

| アラートの種類 | Enterprise Agreement | Microsoft 顧客契約 | Web ダイレクト/従量課金制 |
|---|---|---|---|
| 予算 | ✔ | ✔ | ✔ |
| クレジット | ✔ |✘ | ✘ |
| 部署課金クォータ | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>コストのアラートを表示する

コストのアラートを表示するには、Azure portal で目的のスコープを開き、メニューの **[予算]** を選択します。 別のスコープに切り替えるには、**[スコープ]** ピルを使用します。 メニューの **[コストのアラート]** を選択します。 スコープの詳細については、「[Understand and work with scopes (スコープを理解して使用する)](understand-work-scopes.md)」を参照してください。

![Cost Management に表示されるアラートの例の画像](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

コストのアラート ページに、アクティブなアラートと無視したアラートの合計数が表示されます。

すべてのアラートには、アラートの種類が表示されます。 予算アラートには、生成された理由と、適用される予算の名前が表示されます。 各アラートには、生成された日付、状態、およびアラートが適用されるスコープ (サブスクリプションまたは管理グループ) が表示されます。

表示される可能性のある状態は、**アクティブ** と **無視** です。 アクティブ状態は、アラートが引き続き関連性があることを示します。 無視状態は、他のユーザーがアラートをマークして、関連性がなくなったものとして設定したことを示します。

詳細を表示するアラートを一覧から選択してください。 アラートの詳細には、アラートに関する詳細情報が表示されます。 予算アラートには、予算へのリンクが含まれます。 予算アラートに対する推奨事項がある場合は、推奨事項へのリンクも表示されます。 予算、クレジット、部署課金クォータの各アラートには、アラートのスコープについてコストを調べることができるコスト分析へのリンクがあります。 次の例では、アラートの詳細で部門の支出が示されています。

![アラートの詳細で部門の支出が示されている例の画像](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

無視したアラートの詳細を表示したとき、手動アクションが必要な場合は、再度アクティブにすることができます。 次のイメージは一例を示しています。

![無視と再アクティブ化のオプションが示されている例の画像](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>こちらもご覧ください

- 予算をまだ作成していない場合、または予算のアラート条件をまだ設定していない場合は、「[Azure の予算を作成して管理する](tutorial-acm-create-budgets.md)」チュートリアルを行ってください。