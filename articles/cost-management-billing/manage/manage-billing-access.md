---
title: Azure 課金情報へのアクセスの管理
description: Azure 課金情報へのアクセス権をチームのメンバーに付与する方法について説明します。
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 06/27/2021
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 5f325b0e8adb0a20389bfc09cc37f42fbddbd477
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450022"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Azure の課金情報へのアクセスの管理

Azure portal では自分のアカウントの課金情報へのアクセス権を他のユーザーに付与できます。 課金ロールの種類と課金情報へのアクセス権を付与するための手順は、請求先アカウントの種類によって異なります。 請求先アカウントの種類を確認するには、「[請求先アカウントの種類を確認する](#check-the-type-of-your-billing-account)」を参照してください。

この記事は、Microsoft オンライン サービス プログラム アカウントを使用するお客様に適用されます。 エンタープライズ契約 (EA) の Azure のお客様で、エンタープライズ管理者の場合は、Enterprise Portal で部門管理者とアカウント所有者にアクセス許可を付与できます。 詳細については、「[Understand Azure Enterprise Agreement administrative roles in Azure](understand-ea-roles.md)」(Azure の Azure Enterprise Agreement 管理者ロールを理解する) を参照してください。 Microsoft 顧客契約のお客様の場合、「[Azure での Microsoft 顧客契約の管理ロールを理解する](understand-mca-roles.md)」を参照してください。

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>Microsoft オンライン サービス プログラム アカウントのアカウント管理者

アカウント管理者は、Microsoft オンライン サービス プログラムの請求先アカウントの唯一の所有者です。 このロールは Azure にサインアップしたユーザーに割り当てられます。 アカウント管理者には、サブスクリプションの作成、請求書の表示、サブスクリプションの課金の変更など、さまざまな課金タスクを実行する権限があります。

## <a name="give-others-access-to-view-billing-information"></a>課金情報を表示するためのアクセス権を他のユーザーに付与する

アカウント管理者は、アカウントのサブスクリプションに次のいずれかのロールを割り当てることによって、Azure 課金情報へのアクセス権を他のユーザーに付与できます。

- サービス管理者
- 共同管理者
- 所有者
- Contributor
- Reader
- 請求閲覧者

これらのロールは、[Azure portal](https://portal.azure.com/) で課金情報にアクセスすることができます。 これらのロールが割り当てられているユーザーは、[Billing API](consumption-api-overview.md#usage-details-api) を使用して請求書と使用状況の詳細をプログラムで取得することもできます。

ロールを割り当てるには、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。

> [!note]
> EA のお客様の場合は、アカウント所有者は上記のロールをチームの他のユーザーに割り当てることができます。 ただし、これらのユーザーが課金情報を表示するには、エンタープライズ管理者は、Enterprise Portal で AO ビューの請求額を有効にする必要があります。


### <a name="allow-users-to-download-invoices"></a><a name="opt-in"></a> ユーザーに請求書のダウンロードを許可する

アカウント管理者は他のユーザーに適切なロールを割り当てた後、Azure portal 内で請求書をダウンロードするためのアクセスを有効にする必要があります。 2016 年 12 月よりも前の請求書を入手できるのはアカウント管理者のみです。

1. [Azure Portal](https://portal.azure.com/) にアカウント管理者としてサインインします。

1. **[コストの管理と請求]** で検索します。

    ![[サービス] セクションの [コストの管理と請求] が強調表示されているスクリーンショット。](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. 左側のウィンドウで、 **[サブスクリプション]** を選択します。 アクセス権によっては、課金スコープを選択してから、 **[サブスクリプション]** を選択する必要があります。

    ![サブスクリプションの選択を示すスクリーンショット。](./media/manage-billing-access/billing-select-subscriptions.png)

1. **[請求書]** 、 **[請求書へのアクセス]** の順に選択します。

    ![請求書へのアクセスの委任方法を示すスクリーンショット。](./media/manage-billing-access/aa-optin01.png)

1. **[オン]** を選択して、保存します。

    ![請求書へのアクセスの委任のオン/オフを示すスクリーンショット](./media/manage-billing-access/aa-optinallow01.png)

アカウント管理者は、請求書を電子メールで受け取るように構成することもできます。 詳細については、「[メールで請求書を入手する](download-azure-invoice-daily-usage-date.md)」を参照してください。

## <a name="give-read-only-access-to-billing"></a>課金情報への読み取り専用アクセス権の付与

サブスクリプションの課金情報への読み取り専用アクセス権は必要であっても、Azure サービスを管理または作成する権限は必要ないユーザーに、請求閲覧者ロールを割り当てます。 このロールが適しているのは、組織で Azure サブスクリプションの財務とコストの管理を担当しているユーザーです。

請求閲覧者機能はプレビュー段階であり、非グローバル クラウドはまだサポートされていません。

- 請求閲覧者ロールをサブスクリプション スコープでユーザーに割り当てます。  
     詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。

> [!NOTE]
> EA のお客様の場合は、アカウント所有者または部門管理者がチーム メンバーに請求閲覧者ロールを割り当てることができます。 ただし、その請求閲覧者が部門またはアカウントの課金情報を表示する場合は、エンタープライズ管理者が Enterprise Portal で **AO ビューの請求額** または **DA ビューの請求額** のポリシーを有効にする必要があります。

## <a name="check-the-type-of-your-billing-account"></a>請求先アカウントの種類を確認する
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- 所有者または共同作成者など他のロールのユーザーは、課金状況だけではなく、Azure サービスにもアクセスできます。 これらのロールを管理するには、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。
- ロールの詳細については、[Azure の組み込みロール](../../role-based-access-control/built-in-roles.md)に関するページを参照してください。