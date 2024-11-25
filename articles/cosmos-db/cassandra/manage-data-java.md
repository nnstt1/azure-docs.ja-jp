---
title: Java 3.0 SDK を使用した Azure Cosmos DB Cassandra API による Java アプリ
description: このクイックスタートでは、Azure Cosmos DB Cassandra API を使用して Azure portal と Java 3.0 SDK でプロファイル アプリケーションを作成する方法を示します。
ms.service: cosmos-db
author: TheovanKraay
ms.author: thvankra
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: quickstart
ms.date: 07/17/2021
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 9fef5f438cc28c5e0fe3ae86d1d85b67af45a8ba
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005805"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-cassandra-api-data-v3-driver"></a>Azure Cosmos DB Cassandra API データを管理する Java アプリを作成する (v3 ドライバー)
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

このクイックスタートでは、Azure Cosmos DB Cassandra API アカウントを作成し、GitHub からクローンした Cassandra Java アプリと Java 用 [v3.x Apache Cassandra ドライバー](https://github.com/datastax/java-driver/tree/3.x)を使用して Cassandra データベースとコンテナーを作成します。 Azure Cosmos DB は、マルチモデル データベース サービスです。グローバルな分散と水平方向のスケーリング機能により、ドキュメント データベースやテーブル データベース、キーと値のデータベース、グラフ データベースをすばやく作成し、クエリを実行することができます。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 または、Azure サブスクリプションなしで、[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)こともできます。
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk)。 JDK のインストール先フォルダーを指すように `JAVA_HOME` 環境変数を設定してください。
- [Maven バイナリ アーカイブ](https://maven.apache.org/download.cgi)。 Ubuntu で `apt-get install maven` を実行して Maven をインストールします。
- [Git](https://www.git-scm.com/downloads). Ubuntu で `sudo apt-get install git` を実行して Git をインストールします。

> [!NOTE]
> これは、Java 用のオープンソース Apache Cassandra ドライバーの[バージョン 3](https://github.com/datastax/java-driver/tree/3.x) を使用するシンプルなクイック スタートです。 ほとんどの場合、既存の Apache Cassandra 依存の Java アプリケーションは、既存のコードを変更しなくても Azure Cosmos DB Cassandra API に接続できるはずです。 しかし、全体的なエクスペリエンスを向上させるために、[カスタム Java 拡張機能](https://github.com/Azure/azure-cosmos-cassandra-extensions/tree/feature/java-driver-3%2F1.0.0)を追加して、カスタムの再試行と負荷分散のポリシーを含めることをお勧めします。 これは、Azure Cosmos DB での[レート制限](./scale-account-throughput.md#handling-rate-limiting-429-errors)とアプリケーション レベルのフェールオーバーをそれぞれ処理するためです。 この拡張機能を実装している包括的なサンプルについては、[こちら](https://github.com/Azure-Samples/azure-cosmos-cassandra-extensions-java-sample)を参照してください。

## <a name="create-a-database-account"></a>データベース アカウントの作成

ドキュメント データベースを作成するには、Azure Cosmos DB を含んだ Cassandra アカウントを事前に作成しておく必要があります。

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

次は、コードを使った作業に移りましょう。 GitHub から Cassandra アプリのクローンを作成し、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started.git
    ```

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コードでデータベース リソースを作成する方法に関心がある場合は、次のスニペットで確認できます。 関心がない場合は、「[接続文字列の更新](#update-your-connection-string)」に進んでください。 これらのスニペットはすべて、*src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java* ファイルから取得されます。  

* Cassandra ホスト、ポート、ユーザー名、パスワード、および TLS/SSL オプションが設定されます。 接続文字列情報は、Azure Portal の [接続文字列] ページから取得されます。

  ```java
  cluster = Cluster.builder().addContactPoint(cassandraHost).withPort(cassandraPort).withCredentials(cassandraUsername, cassandraPassword).withSSL(sslOptions).build();
  ```

* `cluster` は Azure Cosmos DB Cassandra API に接続し、アクセスするセッションを返します。

  ```java
  return cluster.connect();
  ```

次のスニペットは、*src/main/java/com/azure/cosmosdb/cassandra/repository/UserRepository.java* ファイルからのものです。

* 新しいキースペースを作成します。

  ```java
  public void createKeyspace() {
      final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '3' } ";
      session.execute(query);
      LOGGER.info("Created keyspace 'uprofile'");
  }
  ```

* 新しいテーブルを作成します。

   ```java
   public void createTable() {
        final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table 'user'");
   }
   ```

* 準備されたステートメント オブジェクトを使用してユーザー エンティティを挿入します。

  ```java
  public PreparedStatement prepareInsertStatement() {
      final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name, user_bcity) VALUES (?,?,?)";
      return session.prepare(insertStatement);
  }

  public void insertUser(PreparedStatement statement, int id, String name, String city) {
      BoundStatement boundStatement = new BoundStatement(statement);
      session.execute(boundStatement.bind(id, name, city));
  }
  ```

* すべてのユーザー情報を取得するクエリを実行します。

  ```java
  public void selectAllUsers() {
      final String query = "SELECT * FROM uprofile.user";
      List<Row> rows = session.execute(query).all();

      for (Row row : rows) {
          LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
      }
  }
  ```

* 単一ユーザーの情報を取得するクエリを実行します。

  ```java
  public void selectUser(int id) {
      final String query = "SELECT * FROM uprofile.user where user_id = 3";
      Row row = session.execute(query).one();

      LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
  }
  ```

## <a name="update-your-connection-string"></a>接続文字列を更新する

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。 アプリはこの接続文字列の詳細によって、ホストされているデータベースと通信できます。

1. [Azure portal](https://portal.azure.com/) の Azure Cosmos DB アカウントで、 **[接続文字列]** を選択します。 

    :::image type="content" source="./media/manage-data-java/copy-username-connection-string-azure-portal.png" alt-text="Azure portal の [接続文字列] ページからユーザー名を表示してコピー":::

2. 画面右側の :::image type="icon" source="./media/manage-data-java/copy-button-azure-portal.png"::: ボタンを使用して [CONTACT POINT]\(コンタクト ポイント\) の値をコピーします。 

3. *C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples\src\main\resources* フォルダーから *config.properties* ファイルを開きます。 

3. 2 行目の `<Cassandra endpoint host>` にポータルのコンタクト ポイントの値を貼り付けます。

    *config.properties* の 2 行目は次のようになります。 

    `cassandra_host=cosmos-db-quickstart.cassandra.cosmosdb.azure.com`

3. ポータルに戻り、[ユーザー名] の値をコピーします。 4 行目の `<cassandra endpoint username>` にポータルの [ユーザー名] の値を貼り付けます。

    *config.properties* の 4 行目は次のようになります。 

    `cassandra_username=cosmos-db-quickstart`

4. ポータルに戻り、[パスワード] の値をコピーします。 5 行目の `<cassandra endpoint password>` にポータルの [パスワード] の値を貼り付けます。

    *config.properties* の 5 行目は次のようになります。 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. 特定の TLS/SSL 証明書を使用する場合は、6 行目の `<SSL key store file location>` を TLS/SSL 証明書の場所に置き換えます。 値が指定されない場合、<JAVA_HOME>/jre/lib/security/cacerts にインストールされている JDK 証明書が使用されます。 

6. 特定の TLS/SSL 証明書を使用するように 6 行目を変更した場合は、その証明書のパスワードを使用するように 7 行目を更新します。 

7. *config.properties* ファイルを保存します。

## <a name="run-the-java-app"></a>Java アプリを実行する

1. Git ターミナル ウィンドウで、`azure-cosmosdb-cassandra-java-getting-started` フォルダーに `cd` します。

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started"
    ```

2. Git ターミナル ウィンドウで、次のコマンドを使用して、`cosmosdb-cassandra-examples.jar` ファイルを生成します。

    ```git
    mvn clean install
    ```

3. git ターミナル ウィンドウで、次のコマンドを実行して Java アプリケーションを起動します。

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    ターミナル ウィンドウに、キースペースとテーブルが作成されたという通知が表示されます。 その後、テーブル内のすべてのユーザーが選択されて戻されます。次に、出力が表示され、ID で行が選択されて値が表示されます。  

    Ctrl + C キーを押してプログラムの実行を停止し、コンソール ウィンドウを閉じます。

4. Azure portal で **Data Explorer** を開き、この新しいデータのクエリ、変更、操作を行います。 

    :::image type="content" source="./media/manage-data-java/view-data-explorer-java-app.png" alt-text="データ エクスプローラーでデータを表示する - Azure Cosmos DB":::

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Cassandra API を使用して Azure Cosmos DB アカウントを作成する方法と、Cassandra データベースとコンテナーを作成する Cassandra Java アプリの実行方法について説明しました。 これで、Azure Cosmos DB アカウントに追加のデータをインポートできるようになりました。 

> [!div class="nextstepaction"]
> [Azure Cosmos DB への Cassandra データのインポート](migrate-data.md)