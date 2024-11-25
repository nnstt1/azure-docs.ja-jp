---
title: Azure AD B2C カスタム ドメインを有効にする
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C のリダイレクト URL でカスタム ドメインを有効にする方法について説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: edddf44509e6eeff6f50b4361fe8c40a7832a8a8
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130222876"
---
# <a name="enable-custom-domains-for-azure-active-directory-b2c"></a>Azure Active Directory B2C のカスタム ドメインを有効にする

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

この記事では、Azure Active Directory B2C (Azure AD B2C) のリダイレクト URL でカスタム ドメインを有効にする方法について説明します。 アプリケーションでカスタム ドメインを使用すると、よりシームレスなユーザー エクスペリエンスが得られます。 ユーザーの視点では、サインイン プロセス中、ユーザーは Azure AD B2C の既定ドメイン *&lt;tenant-name&gt;.b2clogin.com* にリダイレクトするのでなく、ドメインにとどまります。

![スクリーンショットでは、Azure AD B2C カスタム ドメインのユーザー エクスペリエンスを示しています。](./media/custom-domain/custom-domain-user-experience.png)

## <a name="custom-domain-overview"></a>カスタム ドメインの概要

[Azure Front Door](https://azure.microsoft.com/services/frontdoor/) を使用して Azure AD B2C のカスタム ドメインを有効にできます。 Azure Front Door は、Microsoft グローバル エッジ ネットワークを使用して、セキュリティで保護された高速でスケーラビリティの高い Web アプリを作成するためのグローバル エントリ ポイントです。 Azure Front Door の背後にある Azure AD B2C コンテンツをレンダリングして、Azure Front Door のオプションを構成し、アプリケーションの URL でカスタム ドメインを介してコンテンツを配信できます。

次の図は、Azure Front Door の統合を示しています。

1. ユーザーは、アプリケーションからサインイン ボタンを選択すると、Azure AD B2C サインイン ページに移動します。 このページでカスタム ドメイン名が指定されます。
1. Web ブラウザーによって、カスタム ドメイン名は Azure Front Door の IP アドレスに解決されます。 DNS 解決時に、カスタム ドメイン名の正規名 (CNAME) レコードが Front Door の既定フロントエンド ホスト (例: `contoso-frontend.azurefd.net`) を指します。 
1. カスタム ドメイン (例: `login.contoso.com`) 宛てのトラフィックは、指定された Front Door の既定のフロントエンド ホスト (`contoso-frontend.azurefd.net`) にルーティングされます。
1. Azure Front Door が Azure AD B2C の `<tenant-name>.b2clogin.com` 既定ドメインを使用して Azure AD B2C コンテンツを呼び出します。 Azure AD B2C エンドポイントに対する要求に、元のカスタム ドメイン名が含まれます。
1. Azure AD B2C は、関連するコンテンツと元のカスタム ドメインを表示して、要求に応答します。

![図には、カスタム ドメインのネットワーク フローが示されています。](./media/custom-domain/custom-domain-network-flow.png)

> [!IMPORTANT]
> ブラウザーと Azure Front Door 間の接続は、IPv6 でなく、常に IPv4 を使用する必要があります。

カスタム ドメインを使用するときは、次の点を考慮してください。

- 複数のカスタム ドメインを設定できます。 サポートされているカスタム ドメインの最大数については、Azure AD B2C の「[Azure AD サービスの制限と制約](../active-directory/enterprise-users/directory-service-limits-restrictions.md)」、および Azure Front Door の「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits)」を参照してください。
- Azure Front Door は別個の Azure サービスであるため、追加料金が発生します。 詳細については、「[Front Door の価格](https://azure.microsoft.com/pricing/details/frontdoor)」を参照してください。
- Azure Front Door の [Web Application Firewall](../web-application-firewall/afds/afds-overview.md) を使用するには、ファイアウォールの構成とルールが Azure AD B2C ユーザー フローで正しく動作することを確認する必要があります。
- カスタム ドメインを構成した後も、ユーザーは Azure AD B2C の既定ドメイン名 *&lt;tenant-name&gt;.b2clogin.com* にアクセスできます (カスタム ポリシーを使用して [アクセスがブロック](#block-access-to-the-default-domain-name)されている場合を除く)。
- 複数のアプリケーションがある場合は、それらすべてをカスタム ドメインに移行してください (ブラウザーが、現在使用されているドメイン名の下に Azure AD B2C セッションを格納するため)。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant"></a>手順 1. カスタム ドメイン名を Azure AD B2C テナントに追加する

新しい Azure AD B2C テナントにはすべて、&lt;domainname&gt;.onmicrosoft.com という初期ドメイン名が付きます。 初期ドメイン名は変更したり削除したりできませんが、カスタム ドメインを追加することができます。 

カスタムドメインを Azure AD B2C テナントに追加するには、次の手順に従います。

1. [Azure AD にカスタム ドメイン名を追加します](../active-directory/fundamentals/add-custom-domain.md#add-your-custom-domain-name-to-azure-ad)。

    > [!IMPORTANT]
    > これらの手順では、必ず **Azure AD B2C** テナントにサインインして **Azure Active Directory** サービスを選択してください。

1. [ドメイン レジストラーに DNS 情報を追加します](../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar)。 Azure AD にカスタム ドメイン名を追加した後、ドメインの DNS `TXT` または `MX` レコードを作成します。 ドメインのこの DNS レコードを作成することで、ドメイン名の所有権が検証されます。

    次の例は、*login.contoso.com* および *account.contoso.com* の TXT レコードを示しています。

    |名前 (ホスト名)  |種類  |データ  |
    |---------|---------|---------|
    |ログイン (login)   | TXT  | MS=ms12345678  |
    |account | TXT  | MS=ms87654321  |
    
    TXT レコードは、サブドメイン、またはドメインのホスト名に関連付けられている必要があります。 たとえば、*contoso.com* ドメインの *login* 部分です。 ホスト名が空または `@` の場合、Azure AD は追加したカスタム ドメインを確認できません。 次の例では、レコードは両方とも正しく構成されません。
    
    |名前 (ホスト名)  |種類  |データ  |
    |---------|---------|---------|
    | | TXT  | MS=ms12345678  |
    | @ | TXT  | MS=ms12345678  | 
    
    > [!TIP]
    > GoDaddy などの一般公開されている DNS サービスを使用して、カスタム ドメインを管理できます。 DNS サーバーがない場合、[Azure DNS ゾーン](../dns/dns-getstarted-portal.md)または [App Service ドメイン](../app-service/manage-custom-dns-buy-domain.md)を使用できます。

1. [カスタム ドメイン名を検証します](../active-directory/fundamentals/add-custom-domain.md#verify-your-custom-domain-name)。 使用する予定の各サブドメイン (ホスト名) を検証します。 たとえば、*login.contoso.com* と *account.contoso.com* でサインインできるようになるためには、最上位ドメイン *contoso.com* ではなく、両方のサブドメインを検証する必要があります。 

    ドメインが検証された後で、作成した DNS TXT レコードを **削除** します。

    
## <a name="step-2-create-a-new-azure-front-door-instance"></a>手順 2. 新しい Azure Front Door インスタンスを作成する

Azure AD B2C テナントの Front Door を作成するには、次の手順に従います。 詳細については、「[アプリケーションのフロントドアの作成](../frontdoor/quickstart-create-front-door.md#create-a-front-door-for-your-application)」を参照してください。
  

1. [Azure portal](https://portal.azure.com) にサインインします。
1. Azure AD B2C テナントが含まれるディレクトリ *ではなく* Azure Front Door に使用する Azure サブスクリプションが含まれるディレクトリを選択するため、ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[スイッチ]** を選択します。 
1. ホーム ページまたは Azure メニューから **[リソースの作成]** を選択します。 **[ネットワーク]**  >  **[すべて表示]**  >  **[フロントドア]** の順に選択します。
1. **[フロント ドアを作成する]** ページの **[基本]** タブで、次の情報を入力または選択してから、 **[次へ: 構成]** を選択します。

    | 設定 | 値 |
    | --- | --- |
    | **サブスクリプション** | Azure サブスクリプションを選択します。 |    
    | **リソース グループ** | 既存のリソース グループを選択するか、 **[新規作成]** を選択して新しく作成します。|
    | **リソース グループの場所** | リソース グループの場所を選択します。 たとえば **[米国中部]** です。 |

### <a name="21-add-frontend-host"></a>2.1 フロントエンド ホストを追加する

フロントエンド ホストは、アプリケーションで使用されるドメイン名です。 Front Door を作成すると、既定のフロントエンド ホストは `azurefd.net` のサブドメインになります。

Azure Front Door には、カスタム ドメインをフロントエンド ホストと関連付けるオプションが用意されています。 このオプションを使用して、Front Door に所有されているドメイン名の代わりに、URL 内のカスタム ドメインに Azure AD B2C のユーザー インターフェイスを関連付けます。 たとえば、「 `https://login.contoso.com` 」のように入力します。

フロントエンド ホストを追加するには、次の手順に従います。

1. **[Frontends/domains]\(フロントエンド/ドメイン\)** で、 **[+]** を選択して **[フロントエンド ホストの追加]** を開きます。
1. グローバルに一意のホスト名を **[ホスト名]** に入力します。 このホスト名はカスタム ドメインではありません。 この例では、*contoso-frontend* を使用します。 **[追加]** を選択します。

    ![スクリーンショットでは、フロントエンド ホストを追加する方法を示しています。](./media/custom-domain/add-frontend-host-azure-front-door.png)

### <a name="22-add-backend-and-backend-pool"></a>2.2 バックエンドとバックエンド プールを追加する

バックエンドとは、[Azure AD B2C テナント名](tenant-management.md#get-your-tenant-name) `tenant-name.b2clogin.com` を指します。 バックエンド プールを追加するには、次の手順に従います。

1. 引き続き **[フロント ドアを作成する]** の **[バックエンド プール]** で **[+]** を選択し、 **[バックエンド プールの追加]** を開きます。

1. **[名前]** を入力します。 たとえば、*myBackendPool* のように指定します。 **[バックエンドの追加]** を選択します。
    
    次のスクリーンショットでは、バックエンド プールを作成する方法を示しています。
    
    ![スクリーンショットでは、フロントエンド バックエンド プールを追加する方法を示しています。](./media/custom-domain/front-door-add-backend-pool.png)

1. **[バックエンドの追加]** ブレードで、次の情報を選択し、 **[追加]** を選択します。

    | 設定 | 値 |
    | --- | --- |
    | **バックエンド ホストの種類**| **[カスタム ホスト]** を選択します。| 
    | **バックエンド ホスト名**| [Azure AD B2C](tenant-management.md#get-your-tenant-name) の名前 `<tenant-name>.b2clogin.com` を選択します。 たとえば、「contoso.b2clogin.com」とします。|
    | **バックエンド ホスト ヘッダー**| **[バックエンド ホスト名]** で選択したものと同じ値を選択します。|
    
    "**他のフィールドはすべて既定値のままにします。* "
    
    次のスクリーンショットでは、Azure AD B2C テナントに関連付けられているカスタム ホスト バックエンドを作成する方法を示しています。
    
    ![スクリーンショットでは、カスタム ホスト バックエンドを追加する方法を示しています。](./media/custom-domain/add-a-backend.png)

1. バックエンド プールの構成を完了するには、 **[バックエンド プールの追加]** ブレードで **[追加]** を選択します。

1. **バックエンド** を **バックエンド プール** に追加した後で、**正常性プローブ** を無効にします。

    ![スクリーンショットでは、バックエンド プールを追加し、正常性プローブを無効にする方法を示しています。](./media/custom-domain/add-a-backend-pool.png)

### <a name="23-add-a-routing-rule"></a>2.3 ルーティング規則を追加する

最後に、ルーティング規則を追加します。 フロントエンド ホストは、ルーティング規則によってバックエンド プールにマップされます。 規則により、[フロントエンド ホスト](#21-add-frontend-host)の要求が Azure AD B2C [バックエンド](#22-add-backend-and-backend-pool)に転送されます。 ルーティング規則を追加するには、次の手順に従います。

1. **[規則を追加する]** の **[名前]** に「*LocationRule*」と入力します。 すべて既定値のままにし、 **[追加]** を選択してルーティング規則を追加します。
1. **[確認および作成]** 、 **[作成]** の順に選択します。
    
     ![スクリーンショットでは、Azure Front Door を作成する方法を示しています。](./media/custom-domain/configuration-azure-front-door.png)


## <a name="step-3-set-up-your-custom-domain-on-azure-front-door"></a>手順 3. Azure Front Door でカスタム ドメインを設定する

この手順では、[手順 1](#step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant) で登録したカスタム ドメインを Front Door に追加します。 

### <a name="31-create-a-cname-dns-record"></a>3.1 CNAME DNS レコードを作成する

カスタム ドメインを Front Door で使用するためには、最初に Front Door の既定のフロントエンド ホスト (contoso-frontend.azurefd.net など) を指す正規名 (CNAME) レコードをドメイン プロバイダーで作成する必要があります。

CNAME レコードは、ソース ドメイン名を宛先ドメイン名 (別名) にマップする DNS レコードの一種です。 Azure Front Door では、ソース ドメイン名はカスタム ドメイン名であり、宛先ドメイン名は[手順 2.1](#21-add-frontend-host) で構成した Front Door の既定のホスト名です。 

作成した CNAME レコードが Front Door によって検証されると、ソース カスタム ドメイン (login.contoso.com など) 宛てのトラフィックは、指定された宛先の Front Door 既定フロントエンド ホスト (`contoso-frontend.azurefd.net` など) にルーティングされます。 詳細については、「[Front Door にカスタム ドメインを追加する](../frontdoor/front-door-custom-domain.md)」を参照してください。 

カスタム ドメインの CNAME レコードを作成するには:

1. カスタム ドメインのドメイン プロバイダーの Web サイトにサインインします。

1. プロバイダーの資料を調べるか、 **[ドメイン名]** 、 **[DNS]** 、または **[ネームサーバー管理]** という名前のつけられた Web サイトの領域を探して、DNS レコードを管理するためのページを見つけます。 

1. カスタム ドメインの CNAME レコード エントリを作成し、次の表に示すようにフィールドを入力します (フィールド名は異なる場合があります)。

    | source          | Type  | 宛先           |
    |-----------------|-------|-----------------------|
    | `<login.contoso.com>` | CNAME | `contoso-frontend.azurefd.net` |

   - ソース: カスタム ドメイン名 (例: login.contoso.com) を入力します。

   - 型: 「*CNAME*」と入力します。

   - ターゲット: [手順 2.1](#21-add-frontend-host) で作成した既定の Front Door フロントエンド ホストを入力します。 名前は、 _&lt;ホスト名&gt;_ .azurefd.net の形式である必要があります。 たとえば、「 `contoso-frontend.azurefd.net` 」のように入力します。

1. 変更を保存します。

### <a name="32-associate-the-custom-domain-with-your-front-door"></a>3.2 カスタム ドメインを Front Door と関連付ける

カスタム ドメインを登録したら、それを Front Door に追加できます。
    
1. **[Front Door デザイナー]** ページの **[Frontends/domains]\(フロントエンド/ドメイン\)** の下で、 **+** を選択してカスタム ドメインを追加します。

    ![スクリーンショットでは、カスタム ドメインを追加する方法を示しています。](./media/custom-domain/azure-front-door-add-custom-domain.png) 
    
1. **[Frontend host]\(フロントエンド ホスト\)** には、CNAME レコードのターゲット ドメインとして使用するフロントエンド ホストが事前入力されていて、Front Door から派生されています ( *&lt;既定のホスト名&gt;* .azurefd.net)。 この値は変更しないでください。

1. **[カスタム ホスト名]** に、CNAME レコードのソース ドメインとして使用するカスタム ドメイン (サブドメインを含む) を入力します。 たとえば、login.contoso.com のように指定します。

    ![スクリーンショットでは、カスタム ドメインを検証する方法を示しています。](./media/custom-domain/azure-front-door-add-custom-domain-verification.png)

    入力したカスタム ドメイン名に対する CNAME レコードが存在するかどうかが Azure によって確認されます。 CNAME が正しければ、カスタム ドメインが検証されます。

    
1. カスタム ドメイン名が検証された後、 **[Custom domain name HTTPS]\(カスタム ドメイン名 HTTPS\)** で、 **[有効]** を選択します。 
    
    ![スクリーンショットでは、Azure Front Door 証明書を使用して HTTPS を有効にする方法を示しています。](./media/custom-domain/azure-front-door-add-custom-domain-https-settings.png)

1. **[証明書の管理の種類]** で、[[管理されているフロント ドア]](../frontdoor/front-door-custom-domain-https.md#option-1-default-use-a-certificate-managed-by-front-door) または [[自分の証明書を使用する]](../frontdoor/front-door-custom-domain-https.md#option-2-use-your-own-certificate) を選択します。 *[管理されているフロント ドア]* オプションを選択した場合、証明書が完全にプロビジョニングされるまで待ちます。

1. **[追加]** を選択します。

### <a name="33-update-the-routing-rule"></a>3.3 ルーティング規則を更新する

1. **[ルーティング規則]** で、[手順 2.3](#23-add-a-routing-rule) で作成したルーティング規則を選択します。

    ![スクリーンショットでは、ルーティング規則を選択する方法を示しています。](./media/custom-domain/select-routing-rule.png)

1. **[Frontends/domains]\(フロントエンド/ドメイン\)** で、カスタム ドメイン名を選択します。
    
    ![スクリーンショットでは、Azure Front Door のルーティング規則を更新する方法を示しています。](./media/custom-domain/update-routing-rule.png)

1. **[更新]** を選択します。
1. メイン ウィンドウで、 **[保存]** を選択します。

## <a name="step-4-configure-cors"></a>手順 4. CORS を構成する

HTML テンプレートを使用して [Azure AD B2C ユーザー インターフェイスをカスタマイズ](customize-ui-with-html.md)する場合は、カスタム ドメインを使用して [CORS を構成](customize-ui-with-html.md?pivots=b2c-user-flow.md#3-configure-cors)する必要があります。

Azure Blob Storage のクロスオリジン リソース共有を、次の手順で構成します。

1. [Azure portal](https://portal.azure.com) のストレージ アカウントに移動します。
1. メニューで **[CORS]** を選択します。
1. **[許可されるオリジン]** には、`https://your-domain-name` を入力します。 `your-domain-name` を自分のドメイン名に置き換えます。 たとえば、「 `https://login.contoso.com` 」のように入力します。 テナント名を入力するときは、すべて小文字を使用します。
1. **[許可されたメソッド]** に、`GET` と `OPTIONS` を両方選択します。
1. **[許可されたヘッダー]** に、アスタリスク (*) を入力します。
1. **[公開されるヘッダー]** に、アスタリスク (*) を入力します。
1. **[最長有効期間]** には「200」と入力します。
1. **[保存]** を選択します。

## <a name="test-your-custom-domain"></a>カスタム ドメインのテスト

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページの **[ディレクトリ名]** の一覧で自分の Azure AD B2C ディレクトリを見つけて、 **[切り替え]** を選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[ポリシー]** で **[ユーザー フロー (ポリシー)]** を選択します。
1. ユーザー フローを選択して、 **[ユーザー フローの実行]** を選択します。
1. **[アプリケーション]** で、以前に登録した *webapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フロー エンドポイントを実行]** の URL をコピーします。

    ![スクリーンショットでは、承認要求 URI をコピーする方法を示しています。](./media/custom-domain/user-flow-run-now.png)

1. カスタム ドメインを使用したサインインをシミュレートするには、Web ブラウザーを開き、コピーした URL を使用します。 Azure AD B2C ドメイン ( _&lt;tenant-name&gt;_ .b2clogin.com) を自分のカスタム ドメインに置き換えます。   

    たとえば、次の表記の代わりに、

    ```http
    https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login
    ```

    を使う代わりに、

    ```http
    https://login.contoso.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login    
    ```

1. Azure AD B2C が正しく読み込まれていることを確認します。 次に、ローカル アカウントでサインインします。
1. 残りのポリシーでテストを繰り返します。

## <a name="configure-your-identity-provider"></a>ID プロバイダーを構成する

ユーザーがソーシャル ID プロバイダーでのサインインを選択すると、Azure AD B2C が承認要求を開始し、ユーザーは選択した ID プロバイダーに移動してサインイン プロセスを完了します。 承認要求では、`redirect_uri` が Azure AD B2C の既定ドメイン名で指定されます。 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>/oauth2/authresp
```

外部 ID プロバイダーを使ってサインインできるようポリシーを構成した場合は、カスタム ドメインを使用して OAuth リダイレクト URI を更新します。 ほとんどの ID プロバイダーでは、複数のリダイレクト URI を登録できます。 リダイレクト URI を置き換えるのでなく追加して、Azure AD B2C の既定のドメイン名を使用するアプリケーションに影響を与えることなくカスタム ポリシーをテストできるようにすることをお勧めします。 

次のリダイレクト URI に対して、以下を行います。

```http
https://<custom-domain-name>/<tenant-name>/oauth2/authresp
``` 

- **&lt;custom-domain-name&gt;** を自分のカスタム ドメイン名に置き換えます。
- **&lt;tenant-name&gt;** を自分のテナント名、またはテナント ID に置き換えます。

次の例は、有効な OAuth リダイレクト URI を示しています。

```http
https://login.contoso.com/contoso.onmicrosoft.com/oauth2/authresp
```

[テナント ID](#optional-use-tenant-id) を使用した場合、有効な OAuth リダイレクト URI は次の例のようになります。

```http
https://login.contoso.com/11111111-1111-1111-1111-111111111111/oauth2/authresp
```

[SAML ID プロバイダー](saml-identity-provider-technical-profile.md)のメタデータは、次の例のようになります。

```http
https://<custom-domain-name>.b2clogin.com/<tenant-name>/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

## <a name="configure-your-application"></a>アプリケーションの作成 

カスタム ドメインを構成してテストしたら、Azure AD B2C ドメインでなくカスタム ドメインをホスト名として指定する URL を読み込むように、アプリケーションを更新できます。 

カスタム ドメイン統合は、ユーザーを認証するために Azure AD B2C ポリシー (ユーザー フローまたはカスタム ポリシー) を使用する認証エンドポイントに適用されます。 これらのエンドポイントは次の例のようになります。

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/v2.0/.well-known/openid-configuration</code>

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://\<custom-domain\>/<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

置換前のコード:
- **custom-domain** を自分のカスタム ドメインに置換
- **tenant-name** を自分のテナント名またはテナント ID に置換
- **policy-name** を自分のポリシー名に置換 [Azure AD B2C ポリシーの詳細については、こちらを参照してください](technical-overview.md#identity-experiences-user-flows-or-custom-policies)。 


[SAML サービス プロバイダー](./saml-service-provider.md)のメタデータは、次の例のようになります。 

```html
https://custom-domain-name/tenant-name/policy-name/Samlp/metadata
```

### <a name="optional-use-tenant-id"></a>(省略可能) テナント ID を使用する

URL の B2C テナント名を自分のテナント ID GUID に置き換えて、URL 内の "b2c" へのすべての参照が削除されるようにできます。 テナント ID GUID は、Azure portal の「B2C の概要」ページにあります。
たとえば、`https://account.contosobank.co.uk/contosobank.onmicrosoft.com/` を `https://account.contosobank.co.uk/<tenant ID GUID>/` に変更します

テナント名の代わりにテナント ID を使用する場合は、ID プロバイダーの **OAuth リダイレクト URI** をそれに合わせて更新してください。 詳しくは、「[ID プロバイダーを構成する](#configure-your-identity-provider)」をご覧ください。

### <a name="token-issuance"></a>トークン発行

トークン発行者名 (iss) 要求は、使用しているカスタム ドメインに基づいて変わります。 次に例を示します。

```http
https://<domain-name>/11111111-1111-1111-1111-111111111111/v2.0/
```

::: zone pivot="b2c-custom-policy"

## <a name="block-access-to-the-default-domain-name"></a>既定のドメイン名へのアクセスをブロックする

カスタム ドメインを追加してアプリケーションを構成した後も、ユーザーは &lt;tenant-name&gt;.b2clogin.com ドメインにアクセスできます。 アクセスできないようにするために、承認要求の「ホスト名」を許可されているドメイン一覧と照合するように、ポリシーを構成できます。 ホスト名は、URL に表示されるドメイン名です。 ホスト名は `{Context:HostName}` [要求リゾルバー](claim-resolver-overview.md)を通じて入手できます。 その後に、カスタム エラー メッセージを提示できます。 

1. [GitHub](https://github.com/azure-ad-b2c/samples/blob/master/policies/check-host-name) からホスト名を調べる条件付きアクセス ポリシーの例を取得します。
1. 各ファイル内で、文字列 `yourtenant` を、使用している Azure AD B2C テナントの名前に置き換えます。 たとえば、B2C テナントの名前が *contosob2c* であれば、`yourtenant.onmicrosoft.com` のすべてのインスタンスは `contosob2c.onmicrosoft.com` になります。
1. ポリシー ファイルは、`B2C_1A_TrustFrameworkExtensions_HostName.xml`、`B2C_1A_signup_signin_HostName.xml` の順序でアップロードします。

::: zone-end

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="azure-ad-b2c-returns-a-page-not-found-error"></a>Azure AD B2C で「ページが見つかりません」エラーが返される

- **現象** - カスタム ドメインを構成した後で、カスタム ドメインでサインインしようとすると、HTTP 404 エラー メッセージが表示されます。
- **考えられる原因** - この問題は、DNS 構成または Azure Front Door バックエンド構成に関連している可能性があります。 
- **解決方法**:  
    1. カスタム ドメインが Azure AD B2C テナントに[登録されていて正常に検証されている](#step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant)ことを確認します。
    1. [カスタム ドメイン](../frontdoor/front-door-custom-domain.md)が正しく構成されていることを確認します。 カスタム ドメインの `CNAME` レコードは、Azure Front Door の既定のフロントエンド ホスト (例: contoso-frontend.azurefd.net) をポイントしていなければなりません。
    1. [Azure Front Door バックエンド プール構成](#22-add-backend-and-backend-pool)が、カスタム ドメイン名が設定されていてユーザー フローまたはカスタム ポリシーが格納されているテナントをポイントしていることを確認します。


### <a name="azure-ad-b2c-returns-the-resource-you-are-looking-for-has-been-removed-had-its-name-changed-or-is-temporarily-unavailable"></a>Azure AD B2C で「探しているリソースは削除されたか、名前が変更されたか、または一時的に使用不可能になっています」エラーが返される。

- **現象** - カスタム ドメインを構成した後で、カスタム ドメインでサインインしようとすると、"*探しているリソースは削除されたか、名前が変更されたか、または一時的に使用不可能になっています*" というエラー メッセージが表示されます。
- **考えられる原因** - この問題は、Azure AD カスタム ドメインの検証に関連している可能性があります。 
- **解決方法**: カスタム ドメインが Azure AD B2C テナントに [登録されていて **正常に検証されている**](#step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant)ことを確認します。

### <a name="identify-provider-returns-an-error"></a>ID プロバイダーがエラーを返す

- **現象** -カスタム ドメインを構成した後に、ローカル アカウントでサインインできるようになります。 しかし、外部の[ソーシャルまたはエンタープライズ ID プロバイダー](add-identity-provider.md)の資格情報を使用してサインインすると、ID プロバイダーでエラー メッセージが表示されます。
- **考えられる原因** - Azure AD B2C でユーザーがフェデレーション ID プロバイダーを使用してサインインすると、リダイレクト URI が指定されます。 リダイレクト URI は、ID プロバイダーがトークンを返す場所のエンドポイントです。 リダイレクト URI は、アプリケーションが承認要求で使用するものと同じドメインです。 リダイレクト URI がまだ ID プロバイダーに登録されていない場合は、新しいリダイレクト URI は信頼されない可能性があり、その結果としてエラー メッセージが表示されます。 
- **解決方法** - 「[ID プロバイダーを構成する](#configure-your-identity-provider)」の手順に従って、新しいリダイレクト URI を追加します。 

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="can-i-use-azure-front-door-advanced-configuration-such-as-web-application-firewall-rules"></a>Azure Front Door の高度な構成、たとえば "*Web アプリケーション ファイアウォール規則*" を使用できますか。 
  
Azure Front Door の高度な構成設定は正式にサポートされていないため、ご自分の責任でご使用ください。 

### <a name="when-i-use-run-now-to-try-to-run-my-policy-why-i-cant-see-the-custom-domain"></a>[今すぐ実行] を使用して自分のポリシーを実行しようとすると、カスタム ドメインが表示されないのはなぜですか。

URL をコピーし、ドメイン名を手動で変更して、ブラウザーに貼り付けてください。

### <a name="which-ip-address-is-presented-to-azure-ad-b2c-the-users-ip-address-or-the-azure-front-door-ip-address"></a>Azure AD B2C に提示される IP アドレスはどちらですか。 ユーザーの IP アドレスですか。それとも Azure Front Door の IP アドレスですか。

Azure Front Door は、ユーザーの元の IP アドレスを渡します。 これは、監査レポートまたはカスタム ポリシーに表示される IP アドレスです。

### <a name="can-i-use-a-third-party-web-application-firewall-waf-with-b2c"></a>サードパーティ製の Web アプリケーション ファイアウォール (WAF) を B2C で使用できますか。

独自の Web アプリケーション ファイアウォールを Azure Front Door の前面で使用するには、すべてが Azure AD B2C のユーザー フローまたはカスタム ポリシーで正しく動作するよう構成して検証する必要があります。  

### <a name="can-my-azure-front-door-instance-be-hosted-in-a-different-subscription-than-my-azure-ad-b2c-tenant"></a>Azure Front Door インスタンスを Azure AD B2C テナントとは異なるサブスクリプションでホストすることはできますか。
    
はい。Azure Front Door は別のサブスクリプションにできます。
    
## <a name="next-steps"></a>次のステップ

[OAuth 承認要求](protocols-overview.md)について学習します。
