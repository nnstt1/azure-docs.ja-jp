---
title: Microsoft コマーシャル マーケットプレースでの体験版の種類
description: コマーシャル マーケットプレースでの体験版の種類
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.author: trkeya
author: trkeya
ms.date: 10/26/2021
ms.openlocfilehash: 3bcd8b99ea7361e0af05cd55f96741b0d5ab5db8
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847434"
---
# <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager の体験版

この種類は、Azure Marketplace または AppSource にオファーがあっても、体験版は Azure リソースだけでビルドさせたい場合に使用します。 Azure Resource Manager (ARM) テンプレートは、ソリューションが最適に表現されるように設計する Azure リソースのコード化されたコンテナーです。 体験版では、提供された ARM テンプレートが取得されて、必要なすべてのリソースがリソース グループにデプロイされます。 これは、仮想マシンまたは Azure アプリのオファーの唯一の体験版オプションです。

ARM テンプレートについてよく知らない場合は、「[Azure Resource Manager とは](../azure-resource-manager/management/overview.md)」および「[ARM テンプレートの構造と構文について](../azure-resource-manager/templates/syntax.md)」を読み、独自のテンプレートをビルドしてテストする方法をよく理解してください。

**ホストされた** 体験版または **ロジック アプリ** の体験版については、「[体験版とは](what-is-test-drive.md)」を参照してください。

> [!TIP]
> コマーシャル マーケットプレースでの体験版の顧客のビューを表示するには、「[Azure Marketplace とは](/marketplace/azure-marketplace-overview#take-action-on-a-listing)」や「[Microsoft AppSource とは](/marketplace/appsource-overview)」を参照してください。

## <a name="technical-configuration"></a>技術的な構成

デプロイ テンプレートには、ソリューションを構成しているすべての Azure リソースが含まれています。 このシナリオに適合するのは、Azure リソースしか使用されていない製品です。 パートナー センターで次のプロパティを設定します。

- **リージョン** (必須) – 現在、体験版を利用可能にできるサポート対象の Azure リージョンは 26 か所です。 最適なパフォーマンスを得るために、顧客の数が最も多いと思われるリージョンを選択することをお勧めします。 選択中の各リージョンで必要なすべてのリソースのデプロイが自分のサブスクリプションで許可されていることを確認する必要があります。

- **インスタンス** – 使用可能なインスタンスの種類 (ホットまたはコールド) と数を選択します。この数に、ご自身のオファーが利用可能なリージョンの数が乗算されます。

  - **[ホット]** – この種類のインスタンスは選択したリージョンごとにデプロイされ、アクセスを待ちます。 顧客は、デプロイを待つことなく体験版の "*ホット*" インスタンスにすぐにアクセスできます。 トレードオフとして、これらのインスタンスは Azure サブスクリプションで常に実行しているので、大きな稼働時間コストが発生します。 使用できる "*ホット*" インスタンスがない場合、ほとんどの顧客がフル デプロイを待つのを望まず、その結果顧客の利用が減るので、少なくとも 1 つの "*ホット*" インスタンスを用意することを強くお勧めします。

  - **コールド** – この種類のインスタンスは、リージョンごとにデプロイできる可能性があるインスタンスの総数を表します。 コールド インスタンスでは、顧客が体験版を要求したときのデプロイ用に体験版の完全な Resource Manager テンプレートが必要です。そのため、"*コールド*" インスタンスでは、読み込みが "*ホット*" インスタンスよりもはるかに遅くなります。 トレードオフとして、体験版の期間のみの支払いで済みます。"*ホット*" インスタンスのように Azure サブスクリプションで常に実行されていることは "*ありません*"。

- **体験版の Azure Resource Manager テンプレート** – 目的の Azure Resource Manager テンプレートが含まれている .zip をアップロードします。 Azure Resource Manager テンプレートの作成については、記事「[クイック スタート: Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)」を参照してください。

    > [!note]
    > 正常に公開するには、ARM テンプレートのフォーマットを検証することが重要です。 これを行うには、(1) [オンライン API ツール](/rest/api/resources/deployments/validate)を使用するか、(2) [テスト配置](../azure-resource-manager/templates/deploy-portal.md)を使用するという 2 つの方法があります。

- **[体験版の期間]** (必須) – 体験版が有効である時間数を入力します。 この期間が終わると、体験版は自動的に終了します。 整数のみを使用します (たとえば、"2" 時間は有効ですが、"1.5" は無効です)。

## <a name="write-the-test-drive-template"></a>体験版テンプレートを作成する

必要なリソースのパッケージを設計したら、体験版の ARM テンプレートを作成してビルドします。 体験版では完全自動モードでデプロイが実行されるので、体験版テンプレートにはいくつかの制限があります。

### <a name="parameters"></a>パラメーター

ほとんどのテンプレートには、リソース名、リソースのサイズ (ストレージ アカウントの種類や仮想マシンのサイズなど)、ユーザー名とパスワード、DNS 名などを定義するパラメーターのセットがあります。 Azure portal を使用してソリューションをデプロイするときは、手動でこれらすべてのパラメーターを設定し、使用可能な DNS 名やストレージ アカウント名などを選択できます。

![Azure Resource Manager のパラメーターの一覧](media/test-drive/resource-manager-parameters.png)

ただし、体験版は自動的に動作し、人間が介入しないので、サポートされるパラメーター カテゴリのセットに制限があります。 体験版の ARM テンプレートのパラメーターがサポートされているカテゴリのどれにも該当しない場合は、このパラメーターを変数または定数値に置き換える必要があります。

任意の有効なパラメーター名を使用できます。体験版では、メタデータ型の値を使用してパラメーターのカテゴリが認識されます。 すべてのテンプレート パラメーターに対してメタデータ型を指定します。そうしないと、テンプレートは検証に合格しません。

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

> [!NOTE]
> すべてのパラメーターは省略可能であるため、使用したくない場合は、使用する必要はありません。

### <a name="accepted-parameter-metadata-types"></a>使用できるパラメーター メタデータの種類

| メタデータの種類   | パラメーターの型  | 説明     | 値の例    |
|---|---|---|---|
| **baseuri**     | string          | デプロイ パッケージのベース URI| `https://<..>.blob.core.windows.net/<..>` |
| **username**    | string          | 新しいランダムなユーザー名。| admin68876      |
| **password**    | secure string    | 新しいランダムなパスワード | Lp!ACS\^2kh     |
| **session id**   | string          | 一意の体験版セッション ID (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>baseuri

体験版では、このパラメーターがデプロイ パッケージの **ベース URI** で初期化されるので、このパラメーターを使用して、パッケージに含まれるファイルの URI を作成できます。

```JSON
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

体験版のデプロイ パッケージからファイルの URI を作成するには、テンプレート内でこのパラメーターを使用します。 次の例では、リンクされたテンプレートの URI を作成する方法を示します。

```JSON
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>username

体験版では、このパラメーターは新しいランダムなユーザー名で初期化されます。

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

値の例: `admin68876`

ソリューションではランダムなユーザー名または固定のユーザー名を使用できます。

#### <a name="password"></a>password

体験版では、このパラメーターは新しいランダムなパスワードで初期化されます。

```JSON
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

値の例: `Lp!ACS^2kh`

ソリューションではランダムなパスワードまたは固定のパスワードを使用できます。

#### <a name="session-id"></a>session ID

体験版では、このパラメーターは体験版のセッション ID を表す一意の GUID で初期化されます。

```JSON
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique test drive session id."
    }
  },
  ...
}
```

値の例: `b8c8693e-5673-449c-badd-257a405a6dee`

必要な場合は、このパラメーターを使用して体験版のセッションを一意に識別できます。

### <a name="unique-names"></a>一意の名前

ストレージ アカウントや DNS 名などの一部の Azure リソースには、グローバルに一意の名前が必要です。 つまり、体験版で ARM テンプレートがデプロイされるたびに、そのすべてのリソースに対して一意の名前で新しいリソース グループが作成されます。 したがって、リソース グループ ID では [uniquestring](../azure-resource-manager/templates/template-functions.md) 関数と変数名を連結して、ランダムな一意の値を生成する必要があります。

```JSON
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

パラメーターおよび変数文字列 (`contosovm`) と一意の文字列出力 (`resourceGroup().id`) を必ず連結します。このようにすると、各変数の一意性と信頼性が保証されます。

たとえば、ほとんどのリソース名は数字で始めることができませんが、一意文字列関数は数字で始まる文字列を返す可能性があります。 そのため、一意の文字列出力をそのまま使用すると、デプロイは失敗します。

リソースの名前付け規則と制限に関する追加情報については、[こちらの記事](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)を参照してください。

### <a name="deployment-location"></a>デプロイの場所

体験版を異なる Azure リージョンで使用できるようにすることができます。

体験版でラボのインスタンスが作成されると必ず、ユーザーが選択したリージョンのいずれかにリソース グループが作成され、そのグループのコンテキストでデプロイ テンプレートが実行されます。 そのため、テンプレートでは、リソース グループからデプロイの場所を選択する必要があります。

```JSON
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

その後は、特定のラボ インスタンスに対するすべてのリソースでこの場所を使用します。

```JSON
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

選択した各リージョンで必要なすべてのリソースのデプロイが、お使いのサブスクリプションで許可されていることを確認します。 また、有効にしようとするすべてのリージョンで、仮想マシンのイメージが使用可能であることを確認します。使用できない場合は、デプロイ テンプレートが一部のリージョンで機能しません。

### <a name="outputs"></a>出力

通常、Resource Manager テンプレートでは、出力を生成せずにデプロイを実行できます。 これは、テンプレート パラメーターの設定に使用するすべての値がわかっており、常に任意のリソースのプロパティを手動で検査できるためです。

ただし、体験版の Resource Manager テンプレートでは、すべての情報を体験版に返すことが重要です。これは、ラボにアクセスするために必要です (Web サイトの URI、仮想マシンのホスト名、ユーザー名、パスワード)。 これらの変数は顧客に表示されるため、すべての出力名が判読可能であることを確認します。

テンプレートの出力に関する制限はありません。 体験版ではすべての出力値が文字列に変換されるので、出力にオブジェクトを送信した場合、ユーザーには JSON 文字列が表示されます。

例:

```JSON
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>サブスクリプションの制限

サブスクリプションとサービスの制限について忘れないでください。 たとえば、最大 10 個の 4 コア仮想マシンをデプロイする場合、ラボに使用するサブスクリプションで 40 コアの使用が許可されていることを確認する必要があります。 Azure のサブスクリプションとサービスの制限について詳しくは、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md)」を参照してください。 複数の体験版が同時に使用される可能性があるので、コアの数に同時実行できる体験版の合計数を掛けた値を、サブスクリプションで処理できることを確認します。

### <a name="what-to-upload"></a>アップロードするもの

体験版の ARM テンプレートは zip ファイルとしてアップロードされ、それにはさまざまなデプロイ成果物を含めることができますが、`main-template.json` という名前のファイルが 1 つ含まれている必要があります。 これは、体験版でラボをインスタンス化するために使用される ARM デプロイ テンプレートです。 このファイル以外の追加リソースがある場合は、テンプレートの内部でそれを外部リソースとして参照するか、または zip ファイルにそれを含めることができます。

証明書を発行する間に、体験版によってデプロイ パッケージが解凍され、その内容が体験版の内部 BLOB コンテナーに格納されます。 コンテナーの構造は、デプロイ パッケージの構造を反映しています。

| package.zip                       | 体験版 BLOB コンテナー         |
|---|---|
| `main-template.json`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json`  |
| `templates/solution.json`           | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/templates/solution.json` |
| `scripts/warmup.ps1`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/scripts/warmup.ps1`  |
|||

この BLOB コンテナーの URI をベース URI と呼びます。 ラボのすべてのリビジョンには固有の BLOB コンテナーがあるので、ラボのすべてのリビジョンは固有のベース URI を持っています。 体験版では、テンプレート パラメーターを使用して、解凍されたデプロイ パッケージのベース URI をテンプレートに渡すことができます。

### <a name="transform-template-examples-for-test-drive"></a>体験版のテンプレート変換の例

リソースのアーキテクチャを体験版の Resource Manager テンプレートに変換するプロセスは、難しい場合があります。 詳細については、「[体験版とは](what-is-test-drive.md#transforming-examples)」で、現在のデプロイ テンプレートを変換する最適な方法の例を参照してください。

## <a name="test-drive-deployment-subscription-details"></a>体験版のデプロイ サブスクリプションの詳細

最後に行うセクションでは、Azure サブスクリプションと Azure Active Directory (AD) を接続することで、体験版を自動的にデプロイできるようにします。

![体験版のデプロイ サブスクリプションの詳細](media/test-drive/deployment-subscription-details.png)

1. **Azure サブスクリプション ID** を取得します。 Azure サービスと Azure portal へのアクセス権を付与します。 このサブスクリプションで、リソースの使用状況が報告され、サービスが課金されます。 体験版専用の Azure サブスクリプションがまだない場合は、作成します。 Azure サブスクリプション ID (例: `1a83645ac-1234-5ab6-6789-1h234g764ghty1`) は、Azure portal にサインインし、左側のナビゲーション メニューで **[サブスクリプション]** を選択することによって確認できます。

   ![Azure サブスクリプション](media/test-drive/azure-subscriptions.png)

2. **Azure AD テナント ID** を取得します。 使用できるテナント ID が既にある場合は、 **[Azure Active Directory]**  >  **[プロパティ]**  >  **[ディレクトリ ID]** で確認できます。

   ![Azure Active Directory のプロパティ](media/test-drive/azure-active-directory-properties.png)

   テナント ID を持っていない場合は、Azure Active Directory で新しく作成します。 テナントの設定については、「[クイック スタート: テナントを設定する](../active-directory/develop/quickstart-create-new-tenant.md)」を参照してください。

3. Microsoft Test-Drive アプリケーションをテナントにプロビジョニングします。 このアプリケーションを使用して、体験版リソースでの操作を実行します。
    1. [Azure Az PowerShell モジュール](/powershell/azure/install-az-ps)をまだお持ちではない場合はインストールしてください。
    1. Microsoft Test-Drive アプリケーションのサービス プリンシパルを追加します。
        1. `Connect-AzAccount` を実行して、Azure アカウントにサインインするための資格情報を指定します。これには、Azure Active Directory の **グローバル管理者**[組み込みロール](../active-directory/roles/permissions-reference.md#global-administrator)が必要です。 
        1. 新しいサービス プリンシパルを作成します: `New-AzADServicePrincipal -ApplicationId d7e39695-0b24-441c-a140-047800a05ede -DisplayName 'Microsoft TestDrive' -SkipAssignment`。
        1. サービス プリンシパルが作成されたことを確認します: `Get-AzADServicePrincipal -DisplayName 'Microsoft TestDrive'`。
      ![サービス プリンシパルを確認するコードを示す](media/test-drive/commands-to-verify-service-principal.png)

4. **[Azure AD アプリ ID]** には次のアプリケーション ID を貼り付けます: `d7e39695-0b24-441c-a140-047800a05ede`。
5. **[Azure AD アプリ キー]** には、シークレットが不要なため、"no-secret" などのダミー シークレットを挿入します。
6. サブスクリプションにデプロイするためにこのアプリケーションを使用しているので、Azure portal か PowerShell から、そのアプリケーションをサブスクリプションでの共同作成者として追加する必要があります。

   1. Azure ポータルで次の手順を実行します。

       1. 体験版に使用している **サブスクリプション** を選択します。
       1. **[アクセス制御 (IAM)]** を選択します。<br>

          ![新しいアクセス制御 (IAM) 共同作成者の追加](media/test-drive/access-control-principal.png)

       1. **[ロールの割り当て]** タブ、次いで **[+ ロールの割り当ての追加]** を選択します。

          ![[アクセス制御 (IAM)] ウィンドウの選択で、[ロールの割り当て] タブを選択し、[+ ロールの割り当ての追加] を選択する方法を示す。](media/test-drive/access-control-principal-add-assignments.jpg)

       1. 次の Azure AD アプリケーション名を入力します: `Microsoft TestDrive`。 **共同作成者** ロールを割り当てるアプリケーションを選択します。

          ![共同作成者ロールの割り当て方法を示します](media/test-drive/access-control-permissions.jpg)

       1. **[保存]** を選択します。
   1. PowerShell を使用する場合:
      1. 次を実行して、ServicePrincipal object-id を取得します: `(Get-AzADServicePrincipal -DisplayName 'Microsoft TestDrive').id`。
      1. その ObjectId とサブスクリプション ID を指定して、次を実行します: `New-AzRoleAssignment -ObjectId <objectId> -RoleDefinitionName Contributor -Scope /subscriptions/<subscriptionId>`。

> [!NOTE]
> 元の appID を削除する前に、Azure portal に移動し、 **[リソース グループ]** に移動して、`CloudTry_` を検索します。 **[イベント開始者]** 列を確認します。
>
> :::image type="content" source="media/test-drive/event-initiated-by-field.png" lightbox="media/test-drive/event-initiated-by-field.png" alt-text="&quot;イベント開始者&quot; フィールドを示す":::
>
> 少なくとも 1 つのリソース ( **[操作の名前]** ) が **Microsoft TestDrive** に設定されていない限り、元の appID を削除しないでください。
>
> appID を削除するには、左側のナビゲーション メニューで **[Azure Active Directory]**  >  **[アプリの登録]** の順に選択し、次に **[すべてのアプリケーション]** タブを選択します。アプリケーションを選択し、 **[削除]** を選択します。

## <a name="republish"></a>再発行

これで、体験版のすべてのフィールドを設定したので、オファーを **再発行** します。 体験版が認定に合格したら、オファーのプレビューで顧客エクスペリエンスをテストします。

1. UI で体験版を開始します。
1. Azure portal で Azure サブスクリプションを開きます。
1. 体験版が正しくデプロイされることを確認します。

   ![Azure portal](media/test-drive/azure-portal.png)

顧客用にプロビジョニングされた体験版のインスタンスを削除しないでください。顧客がその使用を終了すると、体験版サービスによってこれらのリソース グループは自動的にクリーンアップされます。

プレビューのオファリングに問題がなければ、**稼働** を開始します。 エンド ツー エンドのエクスペリエンス全体を再確認する最終的なレビュー プロセスがあります。 オファーが却下された場合は、修正が必要な点を説明するメールがエンジニアリング担当者に送信されます。

## <a name="next-steps"></a>次のステップ

- パートナー センターでオファーを作成する手順に従った場合は、戻る矢印を使用してそのトピックに戻ります。
- 「[体験版とは](what-is-test-drive.md)」で、他の種類の体験版の詳細を確認してください。