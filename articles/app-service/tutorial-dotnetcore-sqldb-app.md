---
title: チュートリアル:Azure SQL Database を使用した ASP.NET Core
description: Azure SQL Database に接続された .NET Core アプリを Azure App Service で動作させる方法について説明します。
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 10/06/2021
ms.custom: devx-track-csharp, mvc, cli-validate, seodec18, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 5db5a4a1d390164cff0f4acee56ca49687ebddfb
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658437"
---
# <a name="tutorial-build-an-aspnet-core-and-azure-sql-database-app-in-azure-app-service"></a>チュートリアル:Azure App Service での ASP.NET Core および Azure SQL Database アプリの作成

::: zone pivot="platform-windows"  

[Azure App Service](overview.md) は、Azure での高度にスケーラブルな自己適用型の Web ホスティング サービスを提供します。 このチュートリアルでは、ASP.NET Core アプリを作成して SQL Database に接続する方法について説明します。 完了すると、.NET MVC アプリが App Service on Windows で実行されます。

::: zone-end

::: zone pivot="platform-linux"

[Azure App Service](overview.md) は、Linux オペレーティング システムを使用する、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供します。 このチュートリアルでは、ASP.NET Core アプリを作成して SQL Database に接続する方法について説明します。 完了すると、ASP.NET Core MVC アプリが App Service on Linux で実行されます。

::: zone-end

![App Service で実行されるアプリ](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure で SQL データベースを作成する
> * ASP.NET Core アプリを SQL Database に接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure から診断ログをストリーミングする
> * Azure Portal でアプリを管理する

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- <a href="https://git-scm.com/" target="_blank">Git をインストールする</a>
- <a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">最新の .NET 5.0 SDK をインストールする</a>

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-local-aspnet-core-app"></a>ローカル ASP.NET Core アプリの作成

この手順では、ローカル ASP.NET Core プロジェクトを設定します。

### <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

1. ターミナル ウィンドウから、`cd` コマンドで作業ディレクトリに移動します。

1. 次のコマンドを実行してサンプル リポジトリを複製し、ルートに移動します。

    ```bash
    git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
    cd dotnetcore-sqldb-tutorial
    ```

    サンプル プロジェクトには、[Entity Framework Core](/ef/core/) を使用した基本的な CRUD (作成、読み取り、更新、削除) アプリが含まれています。

1. 既定のブランチが `main` であることを確認します。

    ```bash
    git branch -m main
    ```
    
    > [!TIP]
    > App Service では、ブランチ名の変更は必要ありません。 ただし、多くのリポジトリで既定のブランチが `main` に変更されているため (「[デプロイ ブランチを変更する](deploy-local-git.md#change-deployment-branch)」を参照)、このチュートリアルでは、`main` からリポジトリをデプロイする方法も示します。

### <a name="run-the-application"></a>アプリケーションの実行

1. 次のコマンドを実行して、必要なパッケージをインストールし、データベースの移行を実行し、アプリケーションを起動します。

    ```bash
    dotnet tool install -g dotnet-ef
    dotnet ef database update
    dotnet run
    ```

1. ブラウザーで `http://localhost:5000` にアクセスします。 **[新規作成]** リンクを選択し、いくつかの _To Do_ アイテムを作成します。

    ![SQL Database に正常に接続](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

1. 任意のタイミングで ASP.NET Core を停止するには、ターミナルで `Ctrl+C` キーを押します。

## <a name="create-production-sql-database"></a>運用 SQL Database を作成する

この手順では、Azure に SQL Database を作成します。 アプリを Azure にデプロイすると、このクラウド データベースがアプリで使用されます。

SQL Database については、このチュートリアルでは [Azure SQL Database](/azure/sql-database/) を使用します。

### <a name="create-a-resource-group"></a>リソース グループを作成する

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>SQL Database 論理サーバーを作成する

Cloud Shell で、[`az sql server create`](/cli/azure/sql/server#az_sql_server_create) コマンドを使用して SQL Database 論理サーバーを作成します。

*\<server-name>* プレースホルダーは、"*一意の*" SQL Database 名に置き換えてください。 この名前は、グローバルに一意の SQL Database エンドポイント `<server-name>.database.windows.net` の一部として使用されます。 有効な文字は `a`-`z`、`0`-`9`、`-` です。 また、 *\<db-username>* と *\<db-password>* は、選択したユーザー名とパスワードに置き換えます。 


```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

SQL Database 論理サーバーが作成されると、Azure CLI によって、次の例のような情報が表示されます。

<pre>
{
  "administratorLogin": "&lt;db-username&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;server-name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/&lt;server-name&gt;",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "&lt;server-name&gt;",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
</pre>

### <a name="configure-a-server-firewall-rule"></a>サーバーのファイアウォール規則の構成

1. [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)コマンドを使用して、[Azure SQL Database のサーバー レベルのファイアウォール規則](../azure-sql/database/firewall-configure.md)を作成します。 開始 IP と終了 IP の両方が 0.0.0.0 に設定されている場合、ファイアウォールは他の Azure リソースに対してのみ開かれます。 

    ```azurecli-interactive
    az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
    ```
    
    > [!TIP] 
    > [アプリで使用する送信 IP アドレスのみを使用する](overview-inbound-outbound-ips.md#find-outbound-ips)ことで、ファイアウォール規則による制限をさらに厳しくすることができます。
    >

1. Cloud Shell 内で *\<your-ip-address>* を [ローカル IPv4 IP アドレス](https://www.whatsmyip.org/)に置き換えてコマンドを再び実行し、ローカル コンピューターからアクセスできるようにします。

    ```azurecli-interactive
    az sql server firewall-rule create --name AllowLocalClient --server <server-name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
    ```

### <a name="create-a-database"></a>データベースを作成する

[`az sql db create`](/cli/azure/sql/db#az_sql_db_create) コマンドで [S0 パフォーマンス レベル](../azure-sql/database/service-tiers-dtu.md)のデータベースをサーバーに作成します。

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="retrieve-connection-string"></a>接続文字列を取得する

[`az sql db show-connection-string`](/cli/azure/sql/db#az_sql_db_show_connection_string) コマンドを使用して、接続文字列を取得します。

```azurecli-interactive
az sql db show-connection-string --client ado.net --server <server-name> --name coreDB
```

コマンド出力の *\<username>* と *\<password>* は、前に使用したデータベース管理者の資格情報に置き換えてください。

これは ASP.NET Core アプリの接続文字列です。 後で使用するためコピーします。

### <a name="configure-app-to-connect-to-production-database"></a>運用データベースに接続するようにアプリを構成する

ローカル リポジトリで、Startup.cs を開き、次のコードを検索します。

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

これを次のコードに置き換えます。

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
```

> [!IMPORTANT]
> スケールアウトする必要がある運用アプリの場合は、「[運用環境で移行を適用する](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production)」のベスト プラクティスに従ってください。
> 

### <a name="run-database-migrations-to-the-production-database"></a>運用データベースへのデータベースの移行を実行する

現在、対象のアプリはローカルの Sqlite データベースに接続しています。 Azure SQL Database を構成したので、これをターゲットとする最初の移行を再作成します。 

リポジトリのルートから、次のコマンドを実行します。 *\<connection-string>* は、先ほど作成した接続文字列で置き換えます。

```
# Delete old migrations
rm -r Migrations
# Recreate migrations with UseSqlServer (see previous snippet)
dotnet ef migrations add InitialCreate

# Set connection string to production database
# PowerShell
$env:ConnectionStrings:MyDbConnection="<connection-string>"
# CMD (no quotes)
set ConnectionStrings:MyDbConnection=<connection-string>
# Bash (no quotes)
export ConnectionStrings__MyDbConnection=<connection-string>

# Run migrations
dotnet ef database update
```

### <a name="run-app-with-new-configuration"></a>新しい構成でアプリを実行する

1. データベースの移行が運用データベースで実行されるようになったら、以下を実行してアプリをテストします。

    ```
    dotnet run
    ```

1. ブラウザーで `http://localhost:5000` にアクセスします。 **[新規作成]** リンクを選択し、いくつかの _To Do_ アイテムを作成します。 これで、アプリは、運用データベースに対してデータの読み取りと書き込みを行うようになりました。

1. ローカルの変更をコミットし、それを Git リポジトリにコミットします。 

    ```bash
    git add .
    git commit -m "connect to SQLDB in Azure"
    ```

これでコードをデプロイする準備が整いました。

## <a name="deploy-app-to-azure"></a>アプリを Azure にデプロイする

この手順では、SQL Database に接続された ASP.NET Core アプリケーションを App Service にデプロイします。

### <a name="configure-local-git-deployment"></a>ローカル Git デプロイを構成する

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

### <a name="create-a-web-app"></a>Web アプリを作成する

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

::: zone-end

### <a name="configure-connection-string"></a>接続文字列を構成する

Azure アプリの接続文字列を設定するには、Cloud Shell で [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) コマンドを使用します。 次のコマンドの *\<app-name>* および *\<connection-string>* パラメーターは、先ほど作成した接続文字列に置き換えてください。

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app-name> --settings MyDbConnection='<connection-string>' --connection-string-type SQLAzure
```

ASP.NET Core では、*appsettings.json* で指定される接続文字列のように、標準パターンを使用して、この名前付き接続文字列 (`MyDbConnection`) を使用できます。 この場合、`MyDbConnection` は、*appsettings.json* でも定義されています。 App Service で実行する場合、App Service で定義された接続文字列は、*appsettings.json* で定義された接続文字列よりも優先されます。 コードは、ローカル開発中は *appsettings.json* 値を使用し、同じコードがデプロイ時には App Service 値を使用します。

コード内で接続文字列がどのように参照されるかについては、「[運用データベースに接続するようにアプリを構成する](#configure-app-to-connect-to-production-database)」を参照してください。

### <a name="push-to-azure-from-git"></a>Git から Azure へのプッシュ

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

::: zone pivot="platform-windows"  

   <pre>
   Enumerating objects: 268, done.
   Counting objects: 100% (268/268), done.
   Compressing objects: 100% (171/171), done.
   Writing objects: 100% (268/268), 1.18 MiB | 1.55 MiB/s, done.
   Total 268 (delta 95), reused 251 (delta 87), pack-reused 0
   remote: Resolving deltas: 100% (95/95), done.
   remote: Updating branch 'main'.
   remote: Updating submodules.
   remote: Preparing deployment for commit id '64821c3558'.
   remote: Generating deployment script.
   remote: Project file path: .\DotNetCoreSqlDb.csproj
   remote: Generating deployment script for ASP.NET MSBuild16 App
   remote: Generated deployment script files
   remote: Running deployment command...
   remote: Handling ASP.NET Core Web Application deployment with MSBuild16.
   remote: .
   remote: .
   remote: .
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Triggering recycle (preview mode disabled).
   remote: App container will begin restart within 10 seconds.
   To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
    * [new branch]      main -> main
   </pre>

::: zone-end

::: zone pivot="platform-linux"

   <pre>
   Enumerating objects: 273, done.
   Counting objects: 100% (273/273), done.
   Delta compression using up to 4 threads
   Compressing objects: 100% (175/175), done.
   Writing objects: 100% (273/273), 1.19 MiB | 1.85 MiB/s, done.
   Total 273 (delta 96), reused 259 (delta 88)
   remote: Resolving deltas: 100% (96/96), done.
   remote: Deploy Async
   remote: Updating branch 'main'.
   remote: Updating submodules.
   remote: Preparing deployment for commit id 'cccecf86c5'.
   remote: Repository path is /home/site/repository
   remote: Running oryx build...
   remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
   remote: You can report issues at https://github.com/Microsoft/Oryx/issues
   remote: .
   remote: .
   remote: .
   remote: Done.
   remote: Running post deployment command(s)...
   remote: Triggering recycle (preview mode disabled).
   remote: Deployment successful.
   remote: Deployment Logs : 'https://&lt;app-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/cccecf86c56493ffa594e76ea1deb3abb3702d89/log'
   To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
    * [new branch]      main -> main
   </pre>

::: zone-end

### <a name="browse-to-the-azure-app"></a>Azure アプリを参照する

1. Web ブラウザーを使用して、デプロイされたアプリを参照します。

    ```bash
    http://<app-name>.azurewebsites.net
    ```

1. いくつかの To Do アイテムを追加します。

    ![App Service で実行されるアプリ](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**お疲れさまでした。** App Service でデータ主導型の ASP.NET Core アプリが実行されています。

## <a name="update-locally-and-redeploy"></a>ローカルで更新して再デプロイする

この手順では、データベース スキーマに変更を加えて Azure に発行します。

### <a name="update-your-data-model"></a>データ モデルを更新する

コード エディターで _Models/Todo.cs_ を開きます。 `ToDo` クラスに次のプロパティを追加します。

```csharp
public bool Done { get; set; }
```

### <a name="rerun-database-migrations"></a>データベースの移行を再実行する

いくつかのコマンドを実行して、運用データベースを更新します。

```bash
dotnet ef migrations add AddProperty
dotnet ef database update
```

> [!NOTE]
> 新しいターミナル ウィンドウを開いた場合、「[運用データベースへのデータベースの移行を実行する](#run-database-migrations-to-the-production-database)」で実行したのと同様の手順に従い、ターミナルで実稼働データベースへの接続文字列を設定する必要があります。
>

### <a name="use-the-new-property"></a>新しいプロパティを使用する

<ph id="ph1">`Done`</ph> プロパティを使用するために、コードにいくつかの変更を加えます。 このチュートリアルでは、わかりやすくするために `Index` ビューと `Create` ビューのみを変更して、実際のプロパティを確認します。

1. _Controllers/TodosController.cs_ を開きます。

1. `Create([Bind("ID,Description,CreatedDate")] Todo todo)` メソッドを探し、`Bind` 属性内のプロパティの一覧に `Done` を追加します。 完了すると、`Create()` メソッドのシグネチャは次のコードのようになります。

    ```csharp
    public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
    ```

1. _Views/Todos/Create.cshtml_ を開きます。

1. Razor コードに、`Description` の `<div class="form-group">` 要素と、`CreatedDate` の別の `<div class="form-group">` 要素が表示されます。 この 2 つの要素の直後に、`Done` の別の `<div class="form-group">` 要素を追加します。

    ```csharp
    <div class="form-group">
        <label asp-for="Done" class="col-md-2 control-label"></label>
        <div class="col-md-10">
            <input asp-for="Done" class="form-control" />
            <span asp-validation-for="Done" class="text-danger"></span>
        </div>
    </div>
    ```

1. _Views/Todos/Index.cshtml_ を開きます。

1. 空の `<th></th>` 要素を探します。 この要素のすぐ上に、次の Razor コードを追加します。

    ```csharp
    <th>
        @Html.DisplayNameFor(model => model.Done)
    </th>
    ```

1. `asp-action` タグ ヘルパーを含む `<td>` 要素を探します。 この要素のすぐ上に、次の Razor コードを追加します。

    ```csharp
    <td>
        @Html.DisplayFor(modelItem => item.Done)
    </td>
    ```

これだけで、`Index` ビューと `Create` ビューの変更を確認できます。

### <a name="test-your-changes-locally"></a>変更をローカルでテストする

1. アプリをローカルで実行します。

    ```bash
    dotnet run
    ```

    > [!NOTE]
    > 新しいターミナル ウィンドウを開いた場合、「[運用データベースへのデータベースの移行を実行する](#run-database-migrations-to-the-production-database)」で実行したのと同様の手順に従い、ターミナルで実稼働データベースへの接続文字列を設定する必要があります。
    >

1. ブラウザーで `http://localhost:5000/` にアクセスします。 これで、To Do 項目を追加し、 **[完了]** チェック ボックスをオンにすることができるようになります。 そうすると、完了済みの項目としてホームページに表示されます。 `Edit` ビューを変更していないため、`Edit` ビューには `Done` フィールドが表示されないことに注意してください。

### <a name="publish-changes-to-azure"></a>Azure に変更を発行する

1. 変更を Git にコミットし、ご自分の App Service アプリにプッシュします。

    ```bash
    git add .
    git commit -m "added done field"
    git push azure main
    ```

1. `git push` が完了したら、App Service アプリに移動し、To Do 項目を追加してみてから **[完了]** をオンにします。

    ![Code First Migration の手順後の Azure アプリ](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

既存のすべての To Do 項目がまだ表示されています。 ASP.NET Core アプリを再発行しても、SQL Database 内の既存のデータは消失しません。 また、Entity Framework Core Migrations によって変更されるのはデータ スキーマのみであり、既存のデータはそのまま残されます。

## <a name="stream-diagnostic-logs"></a>診断ログをストリーミングする

Azure App Service で ASP.NET Core アプリが稼動している間、コンソールのログをパイプ処理で Cloud Shell に渡すことができます。 このようにすると、アプリケーション エラーのデバッグに役立つ同じ診断メッセージを取得できます。

サンプル プロジェクトは既に、[Azure App Service ログ プロバイダー](/dotnet/core/extensions/logging-providers#azure-app-service)に関するページのガイダンスに従っています。ただし、次の 2 つの変更を構成に加えています。

- *DotNetCoreSqlDb.csproj* で `Microsoft.Extensions.Logging.AzureAppServices` への参照を追加しています。
- *Program.cs* 内の `loggerFactory.AddAzureWebAppDiagnostics()` を呼び出します。

1. App Service で ASP.NET Core の[ログ レベル](/dotnet/core/extensions/logging#log-level)を、既定のレベルである `Error` から `Information` に設定するには、Cloud Shell から [`az webapp log config`](/cli/azure/webapp/log#az_webapp_log_config) コマンドを使用します。

    ```azurecli-interactive
    az webapp log config --name <app-name> --resource-group myResourceGroup --application-logging filesystem --level information
    ```

    > [!NOTE]
    > プロジェクトのログ レベルは、*appsettings.json* で、あらかじめ `Information` に設定されています。

1. ログのストリーミングを開始するには、Cloud Shell で [`az webapp log tail`](/cli/azure/webapp/log#az_webapp_log_tail) コマンドを使用します。

    ```azurecli-interactive
    az webapp log tail --name <app-name> --resource-group myResourceGroup
    ```

1. ログのストリーミングが開始されたら、ブラウザーで Azure アプリを最新の情報に更新して、Web トラフィックを取得します。 ターミナルにパイプされたコンソール ログが表示されます。 コンソール ログがすぐに表示されない場合は、30 秒以内にもう一度確認します。

1. 任意のタイミングでログのストリーミングを停止するには、`Ctrl`+`C` と入力します。

ASP.NET Core のログのカスタマイズの詳細については、「[.NET でのログ記録](/dotnet/core/extensions/logging)」を参照してください。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>次のステップ

ここで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure で SQL データベースを作成する
> * ASP.NET Core アプリを SQL データベースに接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure からターミナルにログをストリーミングする
> * Azure Portal でアプリを管理する

次のチュートリアルに進んで、カスタム DNS 名をアプリにマップする方法を確認してください。

> [!div class="nextstepaction"]
> [チュートリアル:カスタム DNS 名をアプリにマップする](app-service-web-tutorial-custom-domain.md)

または、他のリソースを参照してください。

> [!div class="nextstepaction"]
> [ASP.NET Core アプリの構成](configure-language-dotnetcore.md)
