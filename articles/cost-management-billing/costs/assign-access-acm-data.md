---
title: Azure Cost Management のデータへのアクセス許可を割り当てる
description: この記事では、Cost Management のデータに対するアクセス許可をさまざまなアクセス スコープで割り当てる方法を説明します。
author: bandersmsft
ms.author: banders
ms.date: 11/02/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: sapnakeshari
ms.custom: secdec18
ms.openlocfilehash: d467fd2ac2ecb01d4a933573603360f5382d5efb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457983"
---
# <a name="assign-access-to-cost-management-data"></a>Cost Management のデータへのアクセス許可を割り当てる

Azure Enterprise Agreement を使用するユーザーの場合は、Azure portal とエンタープライズ (EA) ポータルで付与されたアクセス許可の組み合わせによって、Cost Management のデータへのアクセス レベルが定義されます。 他の種類の Azure アカウントを使用するユーザーの場合、Cost Management のデータに対するユーザーのアクセス レベルの定義は、Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、もっと簡単に行うことができます。 この記事では、Cost Management のデータにアクセス許可を割り当てる方法について説明します。 アクセス許可の組み合わせが割り当てられると、ユーザーにはそのアクセス スコープと、Azure portal で選択したスコープに基づく Cost Management のデータが表示されます。

ユーザーが選択したスコープは、データの統合とコスト情報へのアクセスの制御のために、Cost Management 全体を通して使用されます。 スコープを使用する場合、ユーザーはスコープの複数選択を行いません。 代わりに、子スコープがロール アップするよりも大きなスコープを選択してから、表示したい内容をフィルターで絞り込みます。 一部のユーザーは子スコープがロール アップする親スコープにアクセスすべきではないため、データの統合について理解しておくことが重要です。

Azure のロールベースのアクセス制御 (Azure RBAC) を使用してコストと料金を表示するためにアクセスを割り当てる方法については、[Cost Management のアクセス制御](https://www.youtube.com/watch?v=_uQzQ9puPyM)に関するビデオをご覧ください。 他の動画を視聴するには、[Cost Management の YouTube チャンネル](https://www.youtube.com/c/AzureCostManagement)にアクセスしてください。

>[!VIDEO https://www.youtube.com/embed/_uQzQ9puPyM]

## <a name="cost-management-scopes"></a>Cost Management のスコープ

Cost Management では、さまざまな Azure アカウントの種類がサポートされています。 サポートされているアカウントの種類の完全な一覧については、「[Understand Cost Management data (Cost Management データの概要)](understand-cost-mgt-data.md)」を参照してください。 アカウントの種類により、使用可能なスコープが決まります。

### <a name="azure-ea-subscription-scopes"></a>Azure EA サブスクリプションのスコープ

Azure EA サブスクリプションのコスト データを表示するには、次に示す 1 つ以上のスコープへの読み取りアクセス許可が必要です。

| **スコープ** | **定義場所** | **データの表示に必要なアクセス許可** | **前提条件となる EA 設定** | **データの統合先** |
| --- | --- | --- | --- | --- |
| 課金アカウント<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | エンタープライズ管理者 | なし | マイクロソフト エンタープライズ契約のすべてのサブスクリプション |
| 部署 | [https://ea.azure.com](https://ea.azure.com/) | 部門管理者 | **DA ビューの請求額** の有効化 | 部署にリンクされている、登録アカウントに属しているすべてのサブスクリプション |
| 登録アカウント<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | アカウント所有者 | **AO ビューの請求額** の有効化 | 登録アカウントのすべてのサブスクリプション |
| 管理グループ | [https://portal.azure.com](https://portal.azure.com/) | Cost Management 閲覧者 (または共同作成者) | **AO ビューの請求額** の有効化 | 管理グループ下のすべてのサブスクリプション |
| サブスクリプション | [https://portal.azure.com](https://portal.azure.com/) | Cost Management 閲覧者 (または共同作成者) | **AO ビューの請求額** の有効化 | サブスクリプションに含まれているすべてのリソース/リソース グループ |
| Resource group | [https://portal.azure.com](https://portal.azure.com/) | Cost Management 閲覧者 (または共同作成者) | **AO ビューの請求額** の有効化 | リソース グループに含まれるすべてのリソース |

<sup>1</sup> 請求先アカウントも、"マイクロソフト エンタープライズ契約" または "登録" と呼ばれます。

<sup>2</sup> 登録アカウントも、"アカウント所有者" と呼ばれます。

直接エンタープライズ管理者は、[Azure portal](https://portal.azure.com/) から課金アカウント、部門、および登録アカウントのスコープを割り当てることができます。 詳細については、「[Azure portal での直接 Enterprise Agreement の管理](../manage/direct-ea-administration.md)」を参照してください。

## <a name="other-azure-account-scopes"></a>その他の Azure アカウントのスコープ

他の Azure サブスクリプションのコスト データを表示するには、次に示す 1 つ以上のスコープへの読み取りアクセス許可が必要です。

- 管理グループ
- サブスクリプション
- Resource group

パートナーによって顧客が Microsoft 顧客契約にオンボードされた後は、さまざまなスコープが利用可能です。 CSP のお客様は、CSP パートナーによって有効化された場合は Cost Management の機能を使用できます。 詳細については、「[パートナー向け Cost Management の概要](get-started-partners.md)」を参照してください。

## <a name="enable-access-to-costs-in-the-azure-portal"></a>Azure portal でのコストへのアクセスを有効にする

部署スコープを使用するには、 **[部署管理者は料金を表示できる]** (DA ビューの請求額) オプションが **[オン]** に設定されている必要があります。 このオプションは、Azure portal または EA Portal で構成します。 その他すべてのスコープでは、 **[アカウント所有者は料金を表示できます]** (AO ビューの請求額) オプションが **[オン]** に設定されている必要があります。

Azure portal でオプションを有効にするには、次の手順に従います。

1. Azure portal (https://portal.azure.com ) にエンタープライズ管理者アカウントでサインインします。
1. **[コストの管理と請求]** メニュー項目を選択します。
1. **[課金スコープ]** を選択して、使用可能な課金スコープと課金アカウントを一覧表示します。
1. 使用可能な課金アカウントの一覧から **[課金アカウント]** を選択します。
1. **[設定]** の **[ポリシー]** メニュー項目を選択して、その設定を構成します。  
    ![請求金額の表示オプションを表示する課金スコープのポリシー](./media/assign-access-acm-data/azure-portal-policies-view-charges.png)

ビューの請求額オプションを有効にすると、Azure portal で、ほとんどのスコープについて Azure のロールベースのアクセス制御 (Azure RBAC) のアクセス許可の構成も必要になります。

## <a name="enable-access-to-costs-in-the-ea-portal"></a>EA ポータルでのコストへのアクセスを有効にする

部署のスコープの場合、EA ポータルで **[DA ビューの請求額]** オプションを **有効化する** 必要があります。 このオプションは、Azure portal または EA Portal で構成します。 それ以外のすべてのスコープでは、EA ポータルで **AO ビューの請求額** オプションを **有効化する** 必要があります。

EA Portal でオプションを有効にするには、次の手順に従います。

1. EA ポータル ([https://ea.azure.com](https://ea.azure.com)) にエンタープライズ管理者アカウントでサインインします。
2. 左側のウィンドウで、 **[管理]** を選択します。
3. アクセスを許可するコスト管理のスコープで、請求額オプションを **[DA ビューの請求額]** と **[AO ビューの請求額]** のどちらかに設定します。  
    ![DA ビューと AO ビューの請求額オプションを示す [登録] タブ](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

ビューの請求額オプションを有効にすると、Azure portal で、ほとんどのスコープについて Azure のロールベースのアクセス制御 (Azure RBAC) のアクセス許可の構成も必要になります。

## <a name="enterprise-administrator-role"></a>エンタープライズ管理者のロール

既定では、エンタープライズ管理者は、請求先アカウント (マイクロソフト エンタープライズ契約またはマイクロソフト エンタープライズ加入契約) と、子スコープであるその他すべてのスコープにアクセスできます。 他のユーザーのスコープへのアクセス許可は、エンタープライズ管理者が割り当てます。 ビジネス継続性のためのベスト プラクティスとして、エンタープライズ管理者のアクセス許可を持つユーザーは常に 2 人必要です。 次のセクションは、エンタープライズ管理者が他のユーザーのスコープへのアクセス許可を割り当てる方法の例です。

## <a name="assign-billing-account-scope-access"></a>請求先アカウント スコープのアクセス許可を割り当てる

請求先アカウントのスコープへのアクセスには、EA ポータルでのエンタープライズ管理者権限が必要です。 エンタープライズ管理者は、EA の登録または複数の登録全体のコストを表示することができます。 Azure portal では請求先アカウントのスコープに対するアクションは不要です。

1. EA ポータル ([https://ea.azure.com](https://ea.azure.com)) にエンタープライズ管理者アカウントでサインインします。
2. 左側のウィンドウで、 **[管理]** を選択します。
3. **[登録]** タブで、管理する登録を選択します。  
    ![EA ポータルで登録を選択する](./media/assign-access-acm-data/ea-portal.png)
4. **[+ 管理者の追加]** を選択します。
5. [管理者の追加] ボックスで、認証の種類を選択し、ユーザーの電子メール アドレスを入力します。
6. ユーザーにコストと使用量のデータへの読み取り専用アクセスが必要な場合は、 **[読み取り専用]** の下で **[はい]** を選択します。  それ以外の場合は、 **[いいえ]** を選択します。
7. **[追加]** を選択してアカウントを作成します。  
    ![[管理者の追加] ボックスに示された情報の例](./media/assign-access-acm-data/add-admin.png)

新しいユーザーが Cost Management のデータにアクセスできるまで最大 30 分かかる場合があります。

### <a name="assign-department-scope-access"></a>部署のスコープへのアクセス許可を割り当てる

部署スコープにアクセスするには、EA ポータルでの、部署管理者 (DA ビューの請求額) のアクセス許可が必要です。 部署管理者は、部署 (複数可) に関連するコストと使用量のデータを表示することができます。 部署のデータには、部署にリンクされている、登録アカウントに属しているすべてのサブスクリプションが含まれます。 Azure portal でのアクションは不要です。

1. EA ポータル ([https://ea.azure.com](https://ea.azure.com)) にエンタープライズ管理者アカウントでサインインします。
2. 左側のウィンドウで、 **[管理]** を選択します。
3. **[登録]** タブで、管理する登録を選択します。
4. **[部署]** タブを選択し、 **[管理者の追加]** を選択します。
5. [部署管理者の追加] ボックスで、認証の種類を選択し、ユーザーの電子メール アドレスを入力します。
6. ユーザーにコストと使用量のデータへの読み取り専用アクセスが必要な場合は、 **[読み取り専用]** の下で **[はい]** を選択します。  それ以外の場合は、 **[いいえ]** を選択します。
7. 部署管理者のアクセス許可を付与する部署を選択します。
8. **[追加]** を選択してアカウントを作成します。  
    ![[部署管理者を追加する] ボックスに必須情報を入力する](./media/assign-access-acm-data/add-depart-admin.png)
    
直接エンタープライズ管理者は、Azure portal で部門管理者のアクセス許可を割り当てることができます。 詳細については、[Azure portal で部門管理者を追加する](../manage/direct-ea-administration.md#add-a-department-administrator)方法に関するページを参照してください。

## <a name="assign-enrollment-account-scope-access"></a>登録アカウントのスコープへのアクセス許可を割り当てる

登録アカウントのスコープにアクセスするには、EA ポータルでの、アカウント所有者 (AO ビューの請求額) のアクセス許可が必要です。 アカウント所有者は、登録アカウントから作成されたサブスクリプションに関連付けられているコストと使用量のデータを表示できます。 Azure portal でのアクションは不要です。

1. EA ポータル ([https://ea.azure.com](https://ea.azure.com)) にエンタープライズ管理者アカウントでサインインします。
2. 左側のウィンドウで、 **[管理]** を選択します。
3. **[登録]** タブで、管理する登録を選択します。
4. **[アカウント]** タブを選択し、 **[アカウントの追加]** を選択します。
5. [アカウントの追加] ボックスで、アカウントが関連付けられている **部署** を選択するか、未割り当てのままにしておきます。
6. 認証の種類を選択し、アカウント名を入力します。
7. ユーザーの電子メール アドレスを入力し、必要に応じてコスト センターを入力します。
8. **[追加]** を選択してアカウントを作成します。  
    ![登録アカウント用の [アカウントの追加] ボックスに必須情報を入力する](./media/assign-access-acm-data/add-account.png)

上記の手順を完了すると、ユーザー アカウントがエンタープライズ ポータルの登録アカウントになり、サブスクリプションを作成できます。 ユーザーは、作成したサブスクリプションのコストや使用量のデータにアクセスできます。

直接エンタープライズ管理者は、Azure portal でアカウント オーナーのアクセス許可を割り当てることができます。 詳細については、[Azure portal でアカウント所有者を追加する](../manage/direct-ea-administration.md#add-an-account-and-account-owner)方法に関するセクションを参照してください。

## <a name="assign-management-group-scope-access"></a>管理グループのスコープへのアクセス許可を割り当てる

管理グループのスコープを表示するアクセスには、少なくとも Cost Management 閲覧者 (または閲覧者) のアクセス許可が必要です。 管理グループへのアクセス許可は、Azure portal で構成できます。 他のユーザーのアクセスを許可するには、管理グループに対して少なくともユーザー アクセス管理者 (または所有者) のアクセス許可を持っている必要があります。 さらに、Azure EA アカウントの場合は、EA ポータルで **[AO ビューの請求額]** の設定も有効にしておく必要があります。

- 管理グループ スコープで Cost Management 閲覧者 (または閲覧者) ロールをユーザーに割り当てます。  
     詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。

## <a name="assign-subscription-scope-access"></a>サブスクリプションのスコープへのアクセス権を割り当てる

サブスクリプションにアクセスするには、少なくとも Cost Management 閲覧者 (または閲覧者) のアクセス許可が必要です。 サブスクリプションへのアクセス許可は、Azure portal で構成できます。 他のユーザーのアクセスを許可するには、サブスクリプションに対して少なくともユーザー アクセス管理者 (または所有者) のアクセス許可を持っている必要があります。 さらに、Azure EA アカウントの場合は、EA ポータルで **[AO ビューの請求額]** の設定も有効にしておく必要があります。

- サブスクリプション スコープで Cost Management 閲覧者 (または閲覧者) ロールをユーザーに割り当てます。  
     詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。

## <a name="assign-resource-group-scope-access"></a>リソース グループのスコープへのアクセス許可を割り当てる

リソース グループにアクセスするには、少なくとも Cost Management 閲覧者 (または閲覧者) のアクセス許可が必要です。 リソース グループへのアクセス許可は、Azure portal で構成できます。 他のユーザーのアクセスを許可するには、リソース グループに対して少なくともユーザー アクセス管理者 (または所有者) のアクセス許可を持っている必要があります。 さらに、Azure EA アカウントの場合は、EA ポータルで **[AO ビューの請求額]** の設定も有効にしておく必要があります。


- リソース グループ スコープで Cost Management 閲覧者 (または閲覧者) ロールをユーザーに割り当てます。  
     詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。

## <a name="cross-tenant-authentication-issues"></a>テナント間の認証の問題

現在、Cost Management では、テナント間の認証のサポートが制限されています。 テナント間で認証しようとした場合、状況によっては、コスト分析で **アクセス拒否** エラーが表示される場合があります。 この問題は、別のテナントのサブスクリプションに Azure のロールベースのアクセス制御 (Azure RBAC) を構成した後でコストのデータを表示しようとした場合に発生する可能性があります。

"*回避策は次のとおりです*": テナント間の Azure RBAC を構成した後、1 時間待機します。 その後、コスト分析でコストを表示するか、両方のテナントのユーザーに Cost Management へのアクセス権を付与します。  


## <a name="next-steps"></a>次のステップ

- Cost Management の最初のクイック スタートをまだ完了していない場合は、[コスト分析の開始](quick-acm-cost-analysis.md)に関する記事をご覧ください。
