---
title: チュートリアル:CORS を使用して RESTful API をホストする
description: Azure App Service で CORS サポートを使用して RESTful API をホストする方法について説明します。 App Service は、フロントエンド Web アプリとバックエンド API の両方をホストできます。
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/28/2020
ms.custom: devx-track-csharp, mvc, devcenter, seo-javascript-september2019, seo-javascript-october2019, seodec18, devx-track-azurecli
ms.openlocfilehash: 43eaa0db5530483cae58ade96bb8ff65408790f1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730716"
---
# <a name="tutorial-host-a-restful-api-with-cors-in-azure-app-service"></a>チュートリアル:Azure App Service で CORS を使用して RESTful API をホストする

[Azure App Service](overview.md) は、非常にスケーラブルな、自己適用型の Web ホスティング サービスを提供します。 さらに、App Service には、RESTful API 用の[クロスオリジン リソース共有 (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing) の組み込みサポートがあります。 このチュートリアルでは、CORS サポートを使用して ASP.NET Core API アプリを App Service にデプロイする方法について説明します。 コマンドライン ツールを使用してアプリを構成し、Git を使用してアプリをデプロイします。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure CLI を使用して App Service リソースを作成する
> * Git を使用して RESTful API を Azure にデプロイする
> * App Service の CORS サポートを有効にする

このチュートリアルの手順は、macOS、Linux、Windows で実行できます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* <a href="https://git-scm.com/" target="_blank">Git をインストールする</a>
 * <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">最新の .NET Core 3.1 SDK をインストールする</a>

## <a name="create-local-aspnet-core-app"></a>ローカル ASP.NET Core アプリの作成

この手順では、ローカル ASP.NET Core プロジェクトを設定します。 App Service では、他の言語で記述された API についても同じワークフローがサポートされます。

### <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

1. ターミナル ウィンドウから、`cd` コマンドで作業ディレクトリに移動します。  

1. サンプル リポジトリを複製し、リポジトリ ルートに変更します。 

    ```bash
    git clone https://github.com/Azure-Samples/dotnet-core-api
    cd dotnet-core-api
    ```

    このリポジトリには、「[Swagger を使用する ASP.NET Core Web API のヘルプ ページ](/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio)」というチュートリアルに基づいて作成されたアプリが含まれています。 ここでは、Swagger ジェネレーターを使用して [Swagger UI](https://swagger.io/swagger-ui/) と Swagger JSON エンドポイントが提供されます。

1. 既定のブランチが `main` であることを確認します。

    ```bash
    git branch -m main
    ```
    
    > [!TIP]
    > App Service では、ブランチ名の変更は必要ありません。 ただし、多くのリポジトリで既定のブランチが `main` に変更されているため (「[デプロイ ブランチを変更する](deploy-local-git.md#change-deployment-branch)」を参照)、このチュートリアルでは、`main` からリポジトリをデプロイする方法も示します。

### <a name="run-the-application"></a>アプリケーションの実行

1. 次のコマンドを実行して、必要なパッケージをインストールし、データベースの移行を実行し、アプリケーションを起動します。

    ```bash
    dotnet restore
    dotnet run
    ```

1. ブラウザーで `http://localhost:5000/swagger` に移動して、Swagger UI を起動します。

    ![ローカルで実行される ASP.NET Core API](./media/app-service-web-tutorial-rest-api/azure-app-service-local-swagger-ui.png)

1. `http://localhost:5000/api/todo` に移動して、ToDo JSON 項目の一覧を確認します。

1. `http://localhost:5000` に移動して、ブラウザー アプリを起動します。 後で、ブラウザー アプリで App Service のリモート API を参照して、CORS 機能をテストします。 ブラウザー アプリのコードは、リポジトリの _wwwroot_ ディレクトリにあります。

1. 任意のタイミングで ASP.NET Core を停止するには、ターミナルで `Ctrl+C` キーを押します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>アプリを Azure にデプロイする

この手順では、SQL Database に接続された .NET Core アプリケーションを App Service にデプロイします。

### <a name="configure-local-git-deployment"></a>ローカル Git デプロイを構成する

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-a-resource-group"></a>リソース グループを作成する

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Web アプリを作成する

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="push-to-azure-from-git"></a>Git から Azure へのプッシュ

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

   <pre>
   Enumerating objects: 83, done.
   Counting objects: 100% (83/83), done.
   Delta compression using up to 8 threads
   Compressing objects: 100% (78/78), done.
   Writing objects: 100% (83/83), 22.15 KiB | 3.69 MiB/s, done.
   Total 83 (delta 26), reused 0 (delta 0)
   remote: Updating branch 'master'.
   remote: Updating submodules.
   remote: Preparing deployment for commit id '509236e13d'.
   remote: Generating deployment script.
   remote: Project file path: .\TodoApi.csproj
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
   remote: Deployment successful.
   To https://&lt;app_name&gt;.scm.azurewebsites.net/&lt;app_name&gt;.git
   * [new branch]      master -> master
   </pre>

### <a name="browse-to-the-azure-app"></a>Azure アプリを参照する

1. ブラウザーで `http://<app_name>.azurewebsites.net/swagger` に移動して、Swagger UI を起動します。

    ![Azure App Service で実行される ASP.NET Core API](./media/app-service-web-tutorial-rest-api/azure-app-service-browse-app.png)

1. `http://<app_name>.azurewebsites.net/swagger/v1/swagger.json` に移動して、デプロイされた API の _swagger.json_ を確認します。

1. `http://<app_name>.azurewebsites.net/api/todo` に移動して、デプロイされた API が機能していることを確認します。

## <a name="add-cors-functionality"></a>CORS 機能の追加

次に、API について App Service の組み込み CORS サポートを有効にします。

### <a name="test-cors-in-sample-app"></a>サンプル アプリで CORS をテストする

1. ローカル リポジトリで、_wwwroot/index.html_ を開きます。

1. 行 51 で `apiEndpoint` 変数を、デプロイされた API の URL (`http://<app_name>.azurewebsites.net`) に設定します。 _\<appname>_ は、App Service のアプリ名で置き換えます。

1. ローカル ターミナル ウィンドウでサンプル アプリをもう一度実行します。

    ```bash
    dotnet run
    ```

1. `http://localhost:5000` でブラウザー アプリに移動します。 ブラウザーで開発者ツール ウィンドウを開き (Windows 用の Chrome では `Ctrl`+`Shift`+`i`)、 **[Console]\(コンソール\)** タブを確認します。`No 'Access-Control-Allow-Origin' header is present on the requested resource` というエラー メッセージが表示されています。

    ![ブラウザー クライアントでの CORS エラー](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-error.png)

    ブラウザー アプリ (`http://localhost:5000`) とリモート リソース (`http://<app_name>.azurewebsites.net`) の間でドメインが一致しておらず、App Service の API が `Access-Control-Allow-Origin` ヘッダーを送信していないため、ブラウザー アプリでのクロスドメイン コンテンツの読み込みが、ブラウザーによって妨げられています。

    運用環境のブラウザー アプリでは localhost URL ではなくパブリック URL が使用されます。しかし、localhost URL に対して CORS を有効にする方法は、パブリック URL の場合と同じです。

### <a name="enable-cors"></a>CORS を有効にする 

Cloud Shell で [`az webapp cors add`](/cli/azure/webapp/cors#az_webapp_cors_add) コマンドを使用して、クライアントの URL に対して CORS を有効にします。 _&lt;app-name>_ プレースホルダーを置換します。

```azurecli-interactive
az webapp cors add --resource-group myResourceGroup --name <app-name> --allowed-origins 'http://localhost:5000'
```

`properties.cors.allowedOrigins` (`"['URL1','URL2',...]"`) で 2 つ以上のクライアント URL を設定できます。 また、`"['*']"` を使用して、すべてのクライアント URL を有効にすることもできます。

> [!NOTE]
> Cookie や認証トークンなどの資格情報をアプリで送信する必要がある場合、ブラウザーは、応答に `ACCESS-CONTROL-ALLOW-CREDENTIALS` ヘッダーを必要とします。 App Service でこれを有効にする場合は、CORS の構成で `properties.cors.supportCredentials` を `true` に設定してください。`allowedOrigins` に `'*'` が含まれているときに、これを有効にすることはできません。

> [!NOTE]
> `AllowAnyOrigin` と `AllowCredentials` を指定する構成は安全ではなく、クロスサイト リクエスト フォージェリが発生する可能性があります。 アプリが両方の方法で構成されている場合、CORS サービスは無効な CORS 応答を返します。

### <a name="test-cors-again"></a>CORS をもう一度テストする

`http://localhost:5000` でブラウザー アプリを更新します。 **[Console]\(コンソール\)** ウィンドウのエラー メッセージは消えており、デプロイされた API からのデータを確認して操作できます。 これで、ローカルで実行されているブラウザー アプリへの CORS がリモート API でサポートされました。 

![ブラウザー クライアントでの CORS の成功](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-success.png)

おめでとうございます。CORS サポートを使用して Azure App Service の API が実行されています。

## <a name="app-service-cors-vs-your-cors"></a>App Service CORS と独自の CORS

App Service CORS ではなく独自の CORS ユーティリティを使用して、柔軟性を高めることができます。 たとえば、異なるルートまたはメソッドに対して、別の許可されるオリジンを指定したい場合があります。 App Service CORS では、すべての API ルートとメソッドに 1 セットの許可されるオリジンを指定できます。そのため、独自の CORS コードを使用する必要があります。 ASP.NET Core でこれを実行する方法については、「[Enabling Cross-Origin Requests (CORS) (クロスオリジン要求の有効化 (CORS))](/aspnet/core/security/cors)」を参照してください。

> [!NOTE]
> App Service CORS と独自の CORS コードを一緒に使用しないでください。 一緒に使用すると、App Service CORS が優先され、独自の CORS コードは機能しません。
>
>

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>次のステップ

ここで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure CLI を使用して App Service リソースを作成する
> * Git を使用して RESTful API を Azure にデプロイする
> * App Service の CORS サポートを有効にする

次のチュートリアルに進み、ユーザーを認証および承認する方法を学習してください。

> [!div class="nextstepaction"]
> [チュートリアル:エンドツーエンドでのユーザーの認証と承認](tutorial-auth-aad.md)
