---
title: クイックスタート - Azure Cosmos DB SQL API リソースを管理する .NET コンソール アプリを構築する
description: このクイックスタートでは、Azure Cosmos DB SQL API アカウント リソースを管理する .NET コンソール アプリを構築する方法について説明します。
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/26/2021
ms.custom: devx-track-dotnet, devx-track-azurecli
ms.openlocfilehash: 1ef32d507107106ba6760ce1eed8cf80df9da02a
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117775"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>クイック スタート:Azure Cosmos DB SQL API リソースを管理する .NET コンソール アプリを構築する
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Spark v3 コネクタ](create-sql-api-spark.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

.NET 用の Azure Cosmos DB SQL API クライアント ライブラリの概要について説明します。 このドキュメントの手順に従って .NET パッケージをインストールし、アプリをビルドして、Azure Cosmos DB に格納されているデータに対する基本的な CRUD 操作のサンプル コードを試してみてください。 

Azure Cosmos DB は、あらゆる規模に対応する、オープン API を備えた Microsoft の高速 NoSQL データベースです。 Azure Cosmos DB を使用すると、キー/値データベース、ドキュメント データベース、およびグラフ データベースをすばやく作成し、クエリを実行できます。 .NET 用の Azure Cosmos DB SQL API クライアント ライブラリを使用して、次のことを行います。

* Azure Cosmos データベースとコンテナーを作成する
* コンテナーにサンプル データを追加する
* データにクエリを実行する 
* データベースを削除する

[API リファレンスのドキュメント](/dotnet/api/microsoft.azure.cosmos) | [ライブラリのソース コード](https://github.com/Azure/azure-cosmos-dotnet-v3) | [パッケージ (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料で作成する](https://azure.microsoft.com/free/)か、Azure サブスクリプションを使用せず、料金やコミットメントなしで、[Azure Cosmos DB を無料で試用](https://azure.microsoft.com/try/cosmosdb/)できます。 
* [.Net Core 2.1 SDK 以降](https://dotnet.microsoft.com/download/dotnet-core/2.1)。

## <a name="setting-up"></a>設定

このセクションでは、Azure Cosmos アカウントを作成し、.NET 用 Azure Cosmos DB SQL API クライアント ライブラリを使用してリソースを管理するプロジェクトを設定する手順について説明します。 この記事で説明するコード例では `FamilyDatabase` データベースと、そのデータベース内にファミリ メンバー (各ファミリ メンバーが項目) を作成します。 各ファミリ メンバーには `Id, FamilyName, FirstName, LastName, Parents, Children, Address,` などのプロパティがあります。 `LastName` プロパティは、コンテナーのパーティション キーとして使用されます。 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Azure Cosmos アカウントを作成する

[[Azure Cosmos DB を無料で試す]](https://azure.microsoft.com/try/cosmosdb/) オプションを使用して Azure Cosmos アカウントを作成する場合は、種類が **SQL API** の Azure Cosmos DB アカウントを作成する必要があります。 Azure Cosmos DB テスト アカウントは既に作成されています。 アカウントを明示的に作成する必要はないため、このセクションをスキップして次のセクションに進むことができます。

独自の Azure サブスクリプションを所有しているか、または無料のサブスクリプションを作成した場合は、Azure Cosmos アカウントを明示的に作成する必要があります。 次のコードでは、セッションの整合性を持つ Azure Cosmos アカウントを作成します。 アカウントは `South Central US` と `North Central US` でレプリケートされます。  

Azure Cloud Shell を使用して、Azure Cosmos アカウントを作成できます。 Azure Cloud Shell は、Azure リソースを管理するための、ブラウザーでアクセスできる対話形式の認証されたシェルです。 Bash または PowerShell どちらかのシェル エクスペリエンスを作業方法に合わせて柔軟に選択できます。 このクイックスタートでは、 **[Bash]** モードを選択します。 Azure Cloud Shell ではストレージ アカウントも必要です。これはプロンプトが表示されたら作成できます。

次のコードの横にある **[使ってみる]** ボタンを選択し、 **[Bash]** モードを選択します。 **[ストレージ アカウントを作成する]** を選択して Cloud Shell にログインします。 次に、次のコードをコピーして Azure Cloud Shell に貼り付けて実行します。 Azure Cosmos アカウント名はグローバルに一意である必要があります。必ず `mysqlapicosmosdb` 値を更新してからコマンドを実行してください。

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-region writes enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

Azure Cosmos アカウントの作成にはしばらく時間がかかります。操作が正常に終了したら、確認出力が表示されます。 コマンドが正常に終了したら、[Azure portal](https://portal.azure.com/) にサインインし、指定した名前の Azure Cosmos アカウントが存在することを確認します。 リソースの作成後に、[Azure Cloud Shell] ウィンドウを閉じることができます。 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>新しい .NET アプリを作成する

好みのエディターまたは IDE で、新しい .NET アプリケーションを作成します。 ローカル コンピューターから Windows コマンド プロンプトまたはターミナル ウィンドウを開きます。 次のセクションではコマンド プロンプトまたはターミナルからすべてのコマンドを実行します。  次の dotnet new コマンドを実行して、`todo` という名前の新しいアプリを作成します。 作成したプロジェクト ファイル内には、--langVersion パラメーターによって、LangVersion プロパティが設定されます。

```console
dotnet new console --langVersion 7.1 -n todo
```

新しく作成されたアプリ フォルダーにディレクトリを変更します。 次を使用してアプリケーションをビルドできます。

```console
cd todo
dotnet build
```

ビルドからの予想される出力は、次のようになります。

```console
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.dll
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Azure Cosmos DB パッケージをインストールする

アプリケーション ディレクトリ内で、dotnet add package コマンドを使用して .NET Core 用の Azure Cosmos DB クライアント ライブラリをインストールします。

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Azure portal から Azure Cosmos アカウントの資格情報をコピーする

サンプル アプリケーションは、Azure Cosmos アカウントに対する認証を行う必要があります。 認証するには、Azure Cosmos アカウントの資格情報をアプリケーションに渡す必要があります。 次の手順に従って、Azure Cosmos アカウントの資格情報を取得します。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. Azure Cosmos アカウントに移動します。

1. **[キー]** ウィンドウを開き、アカウントの **[URI]** と **[プライマリ キー]** をコピーします。 次の手順では、URI とキーの値を環境変数に追加します。

### <a name="set-the-environment-variables"></a>環境変数を設定する

アカウントの **[URI]** と **[プライマリ キー]** をコピーしたら、アプリケーションを実行しているローカル コンピューター上の新しい環境変数に保存します。 環境変数を設定するには、コンソール ウィンドウを開き、次のコマンドを実行します。 `<Your_Azure_Cosmos_account_URI>` と `<Your_Azure_Cosmos_account_PRIMARY_KEY>` の値は必ず置き換えてください。

**Windows**

```console
setx EndpointUrl "<Your_Azure_Cosmos_account_URI>"
setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**Linux**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**macOS**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

 ## <a name="object-model"></a><a id="object-model"></a>オブジェクト モデル

アプリケーションのビルドを開始する前に、Azure Cosmos DB のリソースの階層と、これらのリソースの作成とアクセスに使用されるオブジェクト モデルについて説明します。 Azure Cosmos DB によって、次の順序でリソースが作成されます。

* Azure Cosmos アカウント 
* データベース 
* Containers 
* アイテム

さまざまなエンティティの階層の詳細については、[Azure Cosmos DB のデータベース、コンテナー、および項目の操作](../account-databases-containers-items.md)に関する記事を参照してください。 これらのリソースとやり取りするには、以下の .NET クラスを使用します。

* [CosmosClient](/dotnet/api/microsoft.azure.cosmos.cosmosclient) - このクラスは、Azure Cosmos DB サービスのクライアント側の論理表現を提供します。 このクライアント オブジェクトは、サービスに対する要求の構成と実行に使用されます。

* [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync) - このメソッドによって、非同期操作としてデータベース リソースが (存在しない場合は) 作成されるか、(既に存在する場合は) 取得されます。 

* [CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync) - このメソッドによって、非同期操作としてコンテナーが (存在しない場合は) 作成されるか、(既に存在する場合は) 取得されます。 応答から状態コードを確認して、コンテナーが新しく作成された (201) か、既存のコンテナーが返された (200) かを判断できます。 
* [CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync) - このメソッドによって、コンテナー内に項目が作成されます。 

* [UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync) - このメソッドでは、アイテムがまだ存在しない場合、コンテナー内にアイテムが作成され、既に存在する場合、アイテムが置換されます。 

* [GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.GetItemQueryIterator) - このメソッドにより、SQL ステートメントとパラメーター化された値を利用して Azure Cosmos データベースのコンテナーの下でアイテムに対するクエリが作成されます。 

* [DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync) - 指定されたデータベースを Azure Cosmos アカウントから削除します。 `DeleteAsync` メソッドでは、データベースのみが削除されます。 `Cosmosclient` インスタンスの破棄は個別に行われる必要があります (DeleteDatabaseAndCleanupAsync メソッドで実行されます)。 

 ## <a name="code-examples"></a><a id="code-examples"></a>コード例

この記事で説明されているサンプル コードでは、Azure Cosmos DB でファミリ データベースを作成します。 ファミリ データベースには、名前、住所、場所、関連付けられている親、子供、ペットなどのファミリの詳細が含まれています。 データを Azure Cosmos アカウントに設定する前に、ファミリ項目のプロパティを定義します。 サンプル アプリケーションのルート レベルに `Family.cs` という名前の新しいクラスを作成し、次のコードを追加します。

```csharp
using Newtonsoft.Json;

namespace todo
{
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        // The ToString() method is used to format the output, it's used for demo purpose only. It's not required by Azure Cosmos DB
        public override string ToString()
        {
            return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }
}
```

### <a name="add-the-using-directives--define-the-client-object"></a>using ディレクティブの追加とクライアント オブジェクトの定義と

プロジェクト ディレクトリからエディターで `Program.cs` ファイルを開き、アプリケーションの先頭に次の using ディレクティブを追加します。

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

**Program.cs** ファイルに、前の手順で設定した環境変数を読み取るコードを追加します。 `CosmosClient`、`Database`、および `Container` オブジェクトを定義します。 次に、Azure Cosmos アカウント リソースを管理する `GetStartedDemoAsync` メソッドを呼び出すコードを main メソッドに追加します。 

```csharp
namespace todo
{
public class Program
{

    /// The Azure Cosmos DB endpoint for running this GetStarted sample.
    private string EndpointUrl = Environment.GetEnvironmentVariable("EndpointUrl");

    /// The primary key for the Azure DocumentDB account.
    private string PrimaryKey = Environment.GetEnvironmentVariable("PrimaryKey");

    // The Cosmos client instance
    private CosmosClient cosmosClient;

    // The database we will create
    private Database database;

    // The container we will create.
    private Container container;

    // The name of the database and container we will create
    private string databaseId = "FamilyDatabase";
    private string containerId = "FamilyContainer";

    public static async Task Main(string[] args)
    {
       try
       {
           Console.WriteLine("Beginning operations...\n");
           Program p = new Program();
           await p.GetStartedDemoAsync();

        }
        catch (CosmosException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine("{0} error occurred: {1}", de.StatusCode, de);
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: {0}", e);
        }
        finally
        {
            Console.WriteLine("End of demo, press any key to exit.");
            Console.ReadKey();
        }
    }
}
}
```

### <a name="create-a-database"></a>データベースを作成する 

`program.cs` クラス内に `CreateDatabaseAsync` メソッドを定義します。 このメソッドによって、`FamilyDatabase` が作成されます (まだ存在しない場合)。

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>コンテナーを作成する

`program.cs` クラス内に `CreateContainerAsync` メソッドを定義します。 このメソッドによって、`FamilyContainer` が作成されます (まだ存在しない場合)。 

```csharp
/// Create the container if it does not exist. 
/// Specifiy "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
private async Task CreateContainerAsync()
{
    // Create a new container
    this.container = await this.database.CreateContainerIfNotExistsAsync(containerId, "/LastName");
    Console.WriteLine("Created Container: {0}\n", this.container.Id);
}
```

### <a name="create-an-item"></a>項目を作成する

次のコードを使用して、`AddItemsToContainerAsync` メソッドを追加してファミリ項目を作成します。 `CreateItemAsync` または `UpsertItemAsync` のメソッドを使用し、アイテムを作成できます。

```csharp
private async Task AddItemsToContainerAsync()
{
    // Create a family object for the Andersen family
    Family andersenFamily = new Family
    {
        Id = "Andersen.1",
        LastName = "Andersen",
        Parents = new Parent[]
        {
           new Parent { FirstName = "Thomas" },
           new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
           new Child
            {
                FirstName = "Henriette Thaulow",
                Gender = "female",
                Grade = 5,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Fluffy" }
                }
            }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = false
    };

    try
    {
        // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen".
        ItemResponse<Family> andersenFamilyResponse = await this.container.CreateItemAsync<Family>(andersenFamily, new PartitionKey(andersenFamily.LastName));
        // Note that after creating the item, we can access the body of the item with the Resource property of the ItemResponse. We can also access the RequestCharge property to see the amount of RUs consumed on this request.
        Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
    }
    catch (CosmosException ex) when (ex.StatusCode == HttpStatusCode.Conflict)
    {
        Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamily.Id);                
    }
}

```

### <a name="query-the-items"></a>項目に対してクエリを実行する

項目を挿入した後、クエリを実行して "Andersen" ファミリの詳細を取得できます。 次のコードは、SQL クエリを直接使用してクエリを実行する方法を示しています。 "Anderson" ファミリの詳細を取得する SQL クエリは `SELECT * FROM c WHERE c.LastName = 'Andersen'` です。 `program.cs` クラス内で `QueryItemsAsync` メソッドを定義し、次のコードを追加します。


```csharp
private async Task QueryItemsAsync()
{
    var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";

    Console.WriteLine("Running query: {0}\n", sqlQueryText);

    QueryDefinition queryDefinition = new QueryDefinition(sqlQueryText);
    FeedIterator<Family> queryResultSetIterator = this.container.GetItemQueryIterator<Family>(queryDefinition);

    List<Family> families = new List<Family>();

    while (queryResultSetIterator.HasMoreResults)
    {
        FeedResponse<Family> currentResultSet = await queryResultSetIterator.ReadNextAsync();
        foreach (Family family in currentResultSet)
        {
            families.Add(family);
            Console.WriteLine("\tRead {0}\n", family);
        }
    }
}

```

### <a name="delete-the-database"></a>データベースを削除する 

最後に、次のコードを使用して `DeleteDatabaseAndCleanupAsync` メソッドを追加し、データベースを削除できます。

```csharp
private async Task DeleteDatabaseAndCleanupAsync()
{
   DatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
   // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

   Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

   //Dispose of CosmosClient
    this.cosmosClient.Dispose();
}
```

### <a name="execute-the-crud-operations"></a>CRUD 操作を実行する

すべての必要なメソッドを定義したら、`GetStartedDemoAsync` メソッド内でそれらを実行します。 メソッド `DeleteDatabaseAndCleanupAsync` は、メソッドが実行された場合にリソースが表示されないため、このコード内ではコメント アウトされています。 Azure portal で Azure Cosmos DB リソースが作成されたことを確認した後にコメントを解除できます。 

```csharp
public async Task GetStartedDemoAsync()
{
    // Create a new instance of the Cosmos Client
    this.cosmosClient = new CosmosClient(EndpointUrl, PrimaryKey);
    await this.CreateDatabaseAsync();
    await this.CreateContainerAsync();
    await this.AddItemsToContainerAsync();
    await this.QueryItemsAsync();
}
```

すべての必要なメソッドを追加したら、`Program.cs` ファイルを保存します。 

## <a name="run-the-code"></a>コードの実行

次に、アプリケーションをビルドして実行し、Azure Cosmos DB リソースを作成します。 新しいコマンド プロンプト ウィンドウを開き、環境変数の設定に使用したものと同じインスタンスを使用しないようにしてください。 環境変数が現在開いているウィンドウで設定されていないためです。 更新内容を表示するには、新しいコマンド プロンプトを開く必要があります。 

```console
dotnet build
```

```console
dotnet run
```

アプリケーションを実行すると、次の出力が生成されます。 また、Azure portal にサインインして、リソースが作成されたことを確認することもできます。

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

End of demo, press any key to exit.
```

データが作成されたことを確認するには、Azure portal にサインインし、Azure Cosmos アカウントで必要な項目を確認します。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、Azure CLI または Azure PowerShell を使用して、Azure Cosmos アカウントとそれに対応するリソース グループを削除できます。 次のコマンドは、Azure CLI を使用してリソース グループを削除する方法を示しています。

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Cosmos アカウントを作成し、.NET Core アプリを使用してデータベースとコンテナーを作成する方法を説明しました。 これで、次の記事の指示に従って Azure Cosmos アカウントに追加のデータをインポートできるようになりました。 

Azure Cosmos DB への移行のための容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
* 既存のデータベース クラスター内の仮想コアとサーバーの数のみがわかっている場合は、[仮想コア数または仮想 CPU 数を使用した要求ユニットの見積もり](../convert-vcore-to-request-unit.md)に関するページを参照してください 
* 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](estimate-ru-with-capacity-planner.md)に関するページを参照してください

> [!div class="nextstepaction"]
> [Azure Cosmos DB へのデータのインポート](../import-data.md)
