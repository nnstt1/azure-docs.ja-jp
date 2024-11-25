---
title: クイック スタート:Cassandra API と .NET Core - Azure Cosmos DB
description: このクイックスタートでは、Azure Cosmos DB の Cassandra API を使用して、Azure portal と .NET Core でプロファイル アプリケーションを作成する方法を示します
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
author: TheovanKraay
ms.author: thvankra
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/01/2020
ms.custom: devx-track-dotnet
ms.openlocfilehash: 60b60a0dcd8e7578f9adbde1e6a509516815e8c6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131045256"
---
# <a name="quickstart-build-a-cassandra-app-with-net-core-and-azure-cosmos-db"></a>クイック スタート:.NET Core と Azure Cosmos DB を使用して Cassandra アプリを構築する
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](manage-data-dotnet.md)
> * [.NET Core](manage-data-dotnet-core.md)
> * [Java v3](manage-data-java.md)
> * [Java v4](manage-data-java-v4-sdk.md)
> * [Node.js](manage-data-nodejs.md)
> * [Python](manage-data-python.md)
> * [Golang](manage-data-go.md)
>  

このクイックスタートでは、GitHub から例をクローンすることで、.NET Core と Azure Cosmos DB の [Cassandra API](cassandra-introduction.md) を使用してプロファイル アプリを作成する方法を示します。 このクイック スタートでは、Web ベースの Azure portal を使用して Azure Cosmos DB アカウントを作成する方法も示します。

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバルな分散と水平方向のスケール機能を利用して、ドキュメント、テーブル、キーと値、およびグラフ データベースをすばやく作成およびクエリできます。 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]または、Azure サブスクリプションを使わず、課金も契約もなしで [Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)ことができます。

さらに、次のものが必要です。 
* まだ Visual Studio 2019 をインストールしていない場合は、**無料** の [Visual Studio 2019 Community エディション](https://www.visualstudio.com/downloads/)をダウンロードして使用できます。 Visual Studio のセットアップ中に、必ず **[Azure の開発]** を有効にしてください。
* [Git](https://www.git-scm.com/) をインストールして例をクローンします。

<a id="create-account"></a>
## <a name="create-a-database-account"></a>データベース アカウントの作成

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../includes/cosmos-db-create-dbaccount-cassandra.md)]


## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

次は、コードを使った作業に移りましょう。 GitHub から Cassandra API アプリをクローンし、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。 

1. コマンド プロンプトを開きます。 `git-samples` という名前の新しいフォルダーを作成します。 その後、コマンド プロンプトを閉じます。

    ```bash
    md "C:\git-samples"
    ```

2. git bash などの git ターミナル ウィンドウを開いて、`cd` コマンドを使用して、サンプル アプリをインストールする新しいフォルダーに変更します。

    ```bash
    cd "C:\git-samples"
    ```

3. 次のコマンドを実行して、サンプル レポジトリをクローンします。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-dotnet-core-getting-started.git
    ```

4. 次に、Visual Studio で CassandraQuickStartSample ソリューション ファイルを開きます。 

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コードでデータベース リソースを作成する方法に関心がある場合は、次のスニペットで確認できます。 スニペットは、すべて `C:\git-samples\azure-cosmos-db-cassandra-dotnet-core-getting-started\CassandraQuickStart` フォルダーにインストールされている `Program.cs` ファイルの `async Task ProcessAsync()` メソッド内から取得されたものです。 関心がない場合は、「[接続文字列の更新](#update-your-connection-string)」に進んでください。

* Cassandra クラスター エンドポイントに接続することによって、セッションを初期化します。 Azure Cosmos DB の Cassandra API は、TLSv1.2 のみをサポートしています。 

  ```csharp
      var options = new Cassandra.SSLOptions(SslProtocols.Tls12, true, ValidateServerCertificate);
      options.SetHostNameResolver((ipAddress) => CASSANDRACONTACTPOINT);
      Cluster cluster = Cluster
          .Builder()
          .WithCredentials(USERNAME, PASSWORD)
          .WithPort(CASSANDRAPORT)
          .AddContactPoint(CASSANDRACONTACTPOINT)
          .WithSSL(options)
          .Build()
      ;
      ISession session = await cluster.ConnectAsync();
   ```

* キースペースが既に存在する場合は、削除します。

    ```csharp
    await session.ExecuteAsync(new SimpleStatement("DROP KEYSPACE IF EXISTS uprofile")); 
    ```

* 新しいキースペースを作成します。

    ```csharp
    await session.ExecuteAsync(new SimpleStatement("CREATE KEYSPACE uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 };"));
    ```

* 新しいテーブルを作成します。

  ```csharp
  await session.ExecuteAsync(new SimpleStatement("CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"));
  ```

* uprofile キースペースに接続する新しいセッションで IMapper オブジェクトを使用し、ユーザーのエンティティを挿入します。

  ```csharp
  await mapper.InsertAsync<User>(new User(1, "LyubovK", "Dubai"));
  ```

* すべてのユーザーの情報を取得するクエリを実行します。

  ```csharp
  foreach (User user in await mapper.FetchAsync<User>("Select * from user"))
  {
      Console.WriteLine(user);
  }
  ```

* 単一ユーザーの情報を取得するクエリを実行します。

  ```csharp
  mapper.FirstOrDefault<User>("Select * from user where user_id = ?", 3);
  ```

## <a name="update-your-connection-string"></a>接続文字列を更新する

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。 アプリはこの接続文字列情報によって、ホストされているデータベースと通信できます。

1. [Azure portal](https://portal.azure.com/) で **[接続文字列]** を選択します。

1. 画面の右側にある :::image type="icon" source="./media/manage-data-dotnet/copy.png"::: ボタンを使用して USERNAME の値をコピーします。

   :::image type="content" source="./media/manage-data-dotnet/keys.png" alt-text="Azure portal の [接続文字列] ページでアクセス キー名を表示してコピー":::

1. Visual Studio で、Program.cs ファイルを開きます。 

1. 13 行目の `<PROVIDE>` にポータルのユーザー名の値を貼り付けます。

    Program.cs の 13 行目は次のようになります。 

    `private const string UserName = "cosmos-db-quickstart";`

    同様に 15 行目の `<PROVIDE>` に [コンタクト ポイント] の値を貼り付けます。

    `private const string CassandraContactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com"; //  DnsName`

1. ポータルに戻り、[パスワード] の値をコピーします。 14 行目の `<PROVIDE>` にポータルのパスワードの値を貼り付けます。

    Program.cs の 14 行目は次のようになります。 

    `private const string Password = "2Ggkr662ifxz2Mg...==";`

1. ポータルに戻り、コンタクト ポイントの値をコピーします。 16 行目の `<PROVIDE>` にポータルのコンタクト ポイントの値を貼り付けます。

    Program.cs の 16 行目は次のようになります。 

    `private const string CASSANDRACONTACTPOINT = "quickstart-cassandra-api.cassandra.cosmos.azure.com";`

1. Program.cs ファイルを保存します。
    
## <a name="run-the-net-core-app"></a>.NET Core アプリを実行する

1. Visual Studio で、 **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択します。

2. コマンド プロンプトで、次のコマンドを使って .NET ドライバーの NuGet パッケージをインストールします。 

    ```cmd
    Install-Package CassandraCSharpDriver
    ```
3. Ctrl + F5 キーを押してアプリケーションを実行します。 コンソール ウィンドウにアプリが表示されます。 

    :::image type="content" source="./media/manage-data-dotnet/output.png" alt-text="出力を表示して検証する":::

    Ctrl + C キーを押してプログラムの実行を停止し、コンソール ウィンドウを閉じます。 
    
4. Azure portal で **Data Explorer** を開き、この新しいデータのクエリ、変更、操作を行います。

    :::image type="content" source="./media/manage-data-dotnet/data-explorer.png" alt-text="データ エクスプローラーでのデータの表示":::

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure Cosmos DB アカウントを作成し、データ エクスプローラーを使用してコンテナーを作成し、Web アプリを実行する方法を説明しました。 これで、Cosmos DB アカウントに追加のデータをインポートできます。

> [!div class="nextstepaction"]
> [Azure Cosmos DB への Cassandra データのインポート](migrate-data.md)
