---
title: API キー認証
titleSuffix: Azure Cognitive Search
description: API キーは、サービス エンドポイントへの受信アクセスを制御します。 管理者キーは、書き込みアクセス権を付与します。 読み取り専用アクセスのために、クエリ キーを作成できます。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/25/2021
ms.openlocfilehash: f452aa6ababd338ccc86b7c7c40854367ed46e41
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114460626"
---
# <a name="use-api-keys-for-azure-cognitive-search-authentication"></a>Azure Cognitive Search の認証に API キーを使用する

Cognitive Search の主要な認証方法として API キーが使用されています。 インデックスの作成やクエリを実行する要求など、検索サービスへの受信要求の場合、API キーが唯一の認証オプションになります。 一部の送信要求シナリオ、特にインデクサーを含む要求の場合、Azure Active Directory の ID とロールを使用できます。

API キーは、サービスの作成時に生成されます。 要求に有効な API キーを渡すことは、その要求が承認済みクライアントからのものであることの証明と見なされます。 キーには 2 つの種類があります。 "*管理者キー*" は、サービスに対する書き込みアクセス許可を伝達し、システム情報のクエリを実行する権限を付与するものです。 "*クエリ キー*" は、読み取りアクセス許可を伝達し、特定のインデックスのクエリを実行するためにアプリで使用できるものです。 

> [!NOTE]
> Azure のロールベースのアクセス制御 (RBAC) を使用したデータ プレーン操作の承認は、現在プレビュー段階にあります。 このプレビュー機能を使用すると、API キーを[検索用の Azure ロール](search-security-rbac.md)で補完または置き換えることができます。 

## <a name="using-api-keys-in-search"></a>検索での API キーの使用

検索サービスに接続する場合は、対象のサービス用に特別に生成された API キーがすべての要求に含まれている必要があります。

+ [REST ソリューション](search-get-started-rest.md)では、通常、API キーは要求のヘッダーで指定されます

+ [.NET ソリューション](search-howto-dotnet-sdk.md)では、キーが多くの場合に構成設定として指定され、[AzureKeyCredential](/dotnet/api/azure.azurekeycredential) として渡されます

API キーを表示および管理するには、[Azure portal](https://portal.azure.com) を使用するか、[PowerShell](/powershell/module/az.search)、[Azure CLI](/cli/azure/search)、または [REST API](/rest/api/searchmanagement/)　経由で行います。

:::image type="content" source="media/search-manage/azure-search-view-keys.png" alt-text="ポータル ページ、設定の取得、キー セクション" border="false":::

## <a name="what-is-an-api-key"></a>API キーとは

API キーとは、要求ごとに検索サービスに渡される、ランダムに生成された数字と文字で構成される一意の文字列です。 要求自体とキーの両方が有効な場合、サービスは要求を受け入れます。 

検索サービスへのアクセスには、管理者 (読み取り/書き込み) とクエリ (読み取り専用) の 2 種類のキーが使用されます。

|Key|説明|制限|  
|---------|-----------------|------------|  
|[Admin]|サービスの管理のほか、インデックス、インデクサー、データ ソースの作成と削除など、すべての操作に対する完全な権限を付与します。<br /><br /> *プライマリ* キーおよび *セカンダリ* キーと呼ばれる、ポータルの 2 つの管理者キーは、サービスの作成時に生成され、要求に応じて個別に再生成できます。 キーが 2 つあることで、サービスへの継続的なアクセスに 1 つのキーを使用している間に、もう 1 つのキーをロールオーバーできます。<br /><br /> 管理者キーは、HTTP 要求ヘッダーでのみ指定されます。 管理者 API キーを URL に加えることはできません。|最大でサービスあたり 2 つ|  
|クエリ|インデックスとドキュメントに対する読み取り専用アクセスを付与するものであり、通常は、検索要求を発行するクライアント アプリケーションに配布されます。<br /><br /> クエリ キーは要求に応じて作成されます。<br /><br /> クエリ キーは、検索、推奨、または参照の操作に使用するために HTTP 要求ヘッダーで指定できます。 または、クエリ キーは URL 上のパラメーターとして渡すことができます。 クライアント アプリケーションが要求を作成する方法によっては、キーをクエリ パラメーターとして渡すほうが簡単な場合があります。<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2020-06-30&api-key=[query key]`|サービスあたり 50 個|  

 管理者キーとクエリ キーに見た目の違いはありません。 どちらのキーも、ランダムに生成された 32 個の英数字からなる文字列です。 アプリケーションで指定されているキーの種類がわからなくなった場合、[ポータルでキーの値を確認](https://portal.azure.com)できます。  

> [!NOTE]  
> `api-key` などの機密データを要求 URI で渡すことは、セキュリティ上推奨されません。 そのため、Azure Cognitive Search はクエリ キーをクエリ文字列の `api-key` としてのみ受け入れます。また、インデックスのコンテンツを公開する必要がない限り、そうすることは避けてください。 一般的なルールとして、`api-key` は要求ヘッダーとして渡すことをお勧めします。  

## <a name="find-existing-keys"></a>既存のキーを見つける

アクセス キーはポータルで取得するか、[PowerShell](/powershell/module/az.search)、[Azure CLI](/cli/azure/search)、または [REST API](/rest/api/searchmanagement/) 経由で取得できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. サブスクリプションの[検索サービス](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)を一覧表示します。
1. サービスを選択し、[概要] ページで **[設定]**  > **[キー]** の順に移動して管理者キーとクエリ キーを表示します。

   :::image type="content" source="media/search-security-overview/settings-keys.png" alt-text="ポータル ページ、設定の表示、キー セクション" border="false":::

## <a name="create-query-keys"></a>クエリ キーを作成する

クエリ キーは、ドキュメント コレクションをターゲットとする操作のために、インデックス内のドキュメントへの読み取り専用アクセスに使用されます。 検索、フィルター、および推奨クエリは、いずれもクエリ キーを取得する操作です。 インデックス定義やインデクサーの状態など、システム データまたはオブジェクト定義を返す読み取り専用操作には、管理者キーが必要です。

クライアント アプリでアクセスと操作を制限することは、サービスで検索アセットを保護するために不可欠です。 クライアント アプリから発信されたクエリには、常に管理者キーではなくクエリ キーを使用します。

1. [Azure portal](https://portal.azure.com) にサインインする
2. サブスクリプションの[検索サービス](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)を一覧表示します。
3. サービスを選択し、[概要]ページで、 **[設定]**  > **[キー]** をクリックします。
4. **[クエリ キーの管理]** をクリックします。
5. サービス用に既に生成されているクエリ キーを使用するか、最大 50 個の新しいクエリ キーを作成します。 既定のクエリ キーには名前はありませんが、追加のクエリ キーには、管理を容易にするために名前を付けることができます。

   :::image type="content" source="media/search-security-overview/create-query-key.png" alt-text="クエリ キーを作成または使用する" border="false":::

> [!Note]
> 「[DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)」に、クエリ キーの使用法を示すコード例があります。

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>管理者キーを再生成する

ビジネス継続性のためにセカンダリ キーを使用してプライマリ キーをローテーションできるように、サービスごとに 2 つの管理キーが作成されます。

1. **[設定]**  > **[キー]** ページで、セカンダリ キーをコピーします。
2. すべてのアプリケーションについて、セカンダリ キーを使用するように API キーの設定を更新します。
3. プライマリ キーを再生成します。
4. 新しいプライマリ キーを使用するように、すべてのアプリケーションを更新します。

誤って両方のキーを同時に再生成すると、それらのキーを使用するすべてのクライアント要求が HTTP 403 Forbidden で失敗します。 ただし、コンテンツは削除されず、永続的にロックアウトされることはありません。 

引き続き、ポータルまたはプログラムを使用してサービスにアクセスできます。 管理機能は、サービス API キーではなくサブスクリプション ID を使用して操作するため、自分の API キーがない場合でも使用できます。 

ポータルまたは管理レイヤーを介して新しいキーを作成した後に、新しいキーを入手し、要求に応じてそのキーを提供すると、アクセスがコンテンツ (インデックス、インデクサー、データ ソース、シノニム マップ) に復元されます。

## <a name="secure-api-keys"></a>API キーをセキュリティ保護する

[ロールの割り当て](search-security-rbac.md)によって、キーの読み取りと管理を実行できるユーザーが決まります。 次のロールのメンバーは、キーの表示と再生成が可能です: 所有者、共同作成者、[Search Service 共同作成者](../role-based-access-control/built-in-roles.md#search-service-contributor)。 閲覧者ロールは API キーにアクセスできません。

サブスクリプション管理者はすべての API キーを表示および再生成できます。 用心のために、ロールの割り当てを調べて、管理キーへのアクセス権を持つユーザーを確認してください。

1. Azure portal で検索サービス ページを開きます。
1. 左のナビゲーション ペインで、 **[アクセス制御 (IAM)]** を選択し、 **[ロールの割り当て]** タブを選択します。
1. **[スコープ]** を **[このリソース]** に設定し、サービスのロールの割り当てを表示します。

## <a name="see-also"></a>関連項目

+ [Azure Cognitive Search のセキュリティ](search-security-overview.md)
+ [Azure Cognitive Search での Azure ロール ベースのアクセス制御](search-security-rbac.md)
+ [PowerShell を使用した管理](search-manage-powershell.md) 