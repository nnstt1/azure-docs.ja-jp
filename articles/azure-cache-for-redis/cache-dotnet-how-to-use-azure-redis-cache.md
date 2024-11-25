---
title: 'クイックスタート: .NET Framework で Azure Cache for Redis を使用する'
description: このクイック スタートでは、.NET アプリから Azure Cache for Redis にアクセスする方法について説明します
author: curib
ms.author: cauribeg
ms.service: cache
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp, mvc
ms.date: 06/18/2020
ms.openlocfilehash: acc3fb68c7d03a8e456088401e87d6858e7e9970
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455399"
---
# <a name="quickstart-use-azure-cache-for-redis-in-net-framework"></a>クイックスタート: .NET Framework で Azure Cache for Redis を使用する

このクイック スタートでは、Azure 内の任意のアプリケーションからアクセスできるセキュリティで保護された専用キャッシュにアクセスするために、Azure Cache for Redis を .NET Framework アプリに組み込みます。 具体的には、.NET コンソール アプリで [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) クライアントと C# コードを使用します。

## <a name="skip-to-the-code-on-github"></a>GitHub のコードにスキップする

この記事をスキップしてすぐにコードをご覧になりたい方は、GitHub にある [.NET Framework のクイックスタート](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/dotnet)を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://www.visualstudio.com/downloads/)
- [.NET Framework 4 以降](https://dotnet.microsoft.com/download/dotnet-framework)。これは StackExchange.Redis クライアントで必要です。

## <a name="create-a-cache"></a>キャッシュの作成
[!INCLUDE [redis-cache-create](includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](includes/redis-cache-access-keys.md)]

ご自分のコンピューター上に *CacheSecrets.config* という名前のファイルを作成し、サンプル アプリケーションのソース コードでチェックインされない場所に置きます。 このクイック スタートでは、*CacheSecrets.config* ファイルを *C:\AppSecrets\CacheSecrets.config* に配置します。

*CacheSecrets.config* ファイルを編集し、次の内容を追加します。

```xml
<appSettings>
    <add key="CacheConnection" value="<host-name>,abortConnect=false,ssl=true,allowAdmin=true,password=<access-key>"/>
</appSettings>
```

`<host-name>` は実際のキャッシュ ホスト名に置き換えます。

`<access-key>` は、実際のキャッシュのプライマリ キーに置き換えます。


## <a name="create-a-console-app"></a>コンソール アプリを作成する

Visual Studio で、 **[ファイル]**  >  **[新規作成]**  >  **[プロジェクト]** の順に選択します。

**[コンソール アプリ (.NET Framework)]** を選択し、 **[次へ]** を選択してアプリを構成します。 **プロジェクト名** を入力し、 **.NET Framework 4.6.1** 以降が選択されていることを確認してから、 **[作成]** を選択して新しいコンソール アプリケーションを作成します。

<a name="configure-the-cache-clients"></a>

## <a name="configure-the-cache-client"></a>キャッシュ クライアントを構成する

このセクションでは、.NET に [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) クライアントを使用するようにコンソール アプリを構成します。

Visual Studio で、 **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択し、[パッケージ マネージャー コンソール] ウィンドウから次のコマンドを実行します。

```powershell
Install-Package StackExchange.Redis
```

インストールが完了すると、*StackExchange.Redis* キャッシュ クライアントをプロジェクトに使用できるようになります。


## <a name="connect-to-the-cache"></a>キャッシュに接続する

Visual Studio で *App.config* ファイルを開き、*CacheSecrets.config* ファイルを参照する `appSettings` `file` 属性を含むように更新します。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>

    <appSettings file="C:\AppSecrets\CacheSecrets.config"></appSettings>
</configuration>
```

ソリューション エクスプローラーで、 **[参照]** を右クリックしてから **[参照の追加]** をクリックします。 **System.Configuration** アセンブリへの参照を追加します。

次の `using` ステートメントを *Program.cs* に追加します。

```csharp
using StackExchange.Redis;
using System.Configuration;
```

Azure Cache for Redis への接続には、`ConnectionMultiplexer` クラスを使用します。 このクラスは、クライアント アプリケーション全体で共有して再利用する必要があります。 操作ごとに新しい接続を作成しないでください。 

ソース コード内に資格情報を保存することは絶対に避けてください。 このサンプルを単純にするために、外部のシークレット構成ファイルのみを使用しています。 実際には [Azure Key Vault と証明書](/rest/api/keyvault/certificate-scenarios)を使用することをお勧めします。

*Program.cs* で、次のメンバーをコンソール アプリの `Program` クラスに追加します。

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = CreateConnection();

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}

private static Lazy<ConnectionMultiplexer> CreateConnection()
{
    return new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });
}
```

アプリで `ConnectionMultiplexer` インスタンスを共有するこの方法では、接続されたインスタンスを返す静的プロパティを使用します。 このコードにより、接続された 1 つの `ConnectionMultiplexer` インスタンスだけがスレッドセーフな方法で初期化されます。 `abortConnect` は false に設定されており、Azure Cache for Redis への接続が確立されていない場合でも呼び出しが成功します。 `ConnectionMultiplexer` の主な機能の 1 つは、ネットワーク問題などの原因が解決されると、キャッシュへの接続が自動的に復元されることです。

*CacheConnection* appSetting の値は、Azure Portal からパスワード パラメーターとしてキャッシュ接続文字列を参照するために使用されます。

## <a name="handle-redisconnectionexception-and-socketexception-by-reconnecting"></a>再接続によって RedisConnectionException と SocketException を処理する

`ConnectionMultiplexer` のメソッドを呼び出すときに推奨されるベスト プラクティスは、接続を閉じて再確立することによって、`RedisConnectionException` と `SocketException` の例外を自動的に解決しようとすることです。

次の `using` ステートメントを *Program.cs* に追加します。

```csharp
using System.Net.Sockets;
using System.Threading;
```

*Program.cs* で、次のメンバーを `Program` クラスに追加します。

```csharp
private static long _lastReconnectTicks = DateTimeOffset.MinValue.UtcTicks;
private static DateTimeOffset _firstErrorTime = DateTimeOffset.MinValue;
private static DateTimeOffset _previousErrorTime = DateTimeOffset.MinValue;
private static SemaphoreSlim _reconnectSemaphore = new SemaphoreSlim(initialCount: 1, maxCount: 1);
private static SemaphoreSlim _initSemaphore = new SemaphoreSlim(initialCount: 1, maxCount: 1);
private static ConnectionMultiplexer _connection;
private static bool _didInitialize = false;
// In general, let StackExchange.Redis handle most reconnects,
// so limit the frequency of how often ForceReconnect() will
// actually reconnect.
public static TimeSpan ReconnectMinInterval => TimeSpan.FromSeconds(60);
// If errors continue for longer than the below threshold, then the
// multiplexer seems to not be reconnecting, so ForceReconnect() will
// re-create the multiplexer.
public static TimeSpan ReconnectErrorThreshold => TimeSpan.FromSeconds(30);
public static TimeSpan RestartConnectionTimeout => TimeSpan.FromSeconds(15);
public static int RetryMaxAttempts => 5;
public static ConnectionMultiplexer Connection { get { return _connection; } }
private static async Task InitializeAsync()
{
    if (_didInitialize)
    {
        throw new InvalidOperationException("Cannot initialize more than once.");
    }
    _connection = await CreateConnectionAsync();
    _didInitialize = true;
}
// This method may return null if it fails to acquire the semaphore in time.
// Use the return value to update the "connection" field
private static async Task<ConnectionMultiplexer> CreateConnectionAsync()
{
    if (_connection != null)
    {
        // If we already have a good connection, let's re-use it
        return _connection;
    }
    try
    {
        await _initSemaphore.WaitAsync(RestartConnectionTimeout);
    }
    catch
    {
        // We failed to enter the semaphore in the given amount of time. Connection will either be null, or have a value that was created by another thread.
        return _connection;
    }
    // We entered the semaphore successfully.
    try
    {
        if (_connection != null)
        {
            // Another thread must have finished creating a new connection while we were waiting to enter the semaphore. Let's use it
            return _connection;
        }
        // Otherwise, we really need to create a new connection.
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return await ConnectionMultiplexer.ConnectAsync(cacheConnection);
    }
    finally
    {
        _initSemaphore.Release();
    }
}
private static async Task CloseConnectionAsync(ConnectionMultiplexer oldConnection)
{
    if (oldConnection == null)
    {
        return;
    }
    try
    {
        await oldConnection.CloseAsync();
    }
    catch (Exception)
    {
        // Ignore any errors from the oldConnection
    }
}
/// <summary>
/// Force a new ConnectionMultiplexer to be created.
/// NOTES:
///     1. Users of the ConnectionMultiplexer MUST handle ObjectDisposedExceptions, which can now happen as a result of calling ForceReconnectAsync().
///     2. Call ForceReconnectAsync() for RedisConnectionExceptions and RedisSocketExceptions. You can also call it for RedisTimeoutExceptions,
///         but only if you're using generous ReconnectMinInterval and ReconnectErrorThreshold. Otherwise, establishing new connections can cause
///         a cascade failure on a server that's timing out because it's already overloaded.
///     3. The code will:
///         a. wait to reconnect for at least the "ReconnectErrorThreshold" time of repeated errors before actually reconnecting
///         b. not reconnect more frequently than configured in "ReconnectMinInterval"
/// </summary>
public static async Task ForceReconnectAsync()
{
    var utcNow = DateTimeOffset.UtcNow;
    long previousTicks = Interlocked.Read(ref _lastReconnectTicks);
    var previousReconnectTime = new DateTimeOffset(previousTicks, TimeSpan.Zero);
    TimeSpan elapsedSinceLastReconnect = utcNow - previousReconnectTime;
    // If multiple threads call ForceReconnectAsync at the same time, we only want to honor one of them.
    if (elapsedSinceLastReconnect < ReconnectMinInterval)
    {
        return;
    }
    try
    {
        await _reconnectSemaphore.WaitAsync(RestartConnectionTimeout);
    }
    catch
    {
        // If we fail to enter the semaphore, then it is possible that another thread has already done so.
        // ForceReconnectAsync() can be retried while connectivity problems persist.
        return;
    }
    try
    {
        utcNow = DateTimeOffset.UtcNow;
        elapsedSinceLastReconnect = utcNow - previousReconnectTime;
        if (_firstErrorTime == DateTimeOffset.MinValue)
        {
            // We haven't seen an error since last reconnect, so set initial values.
            _firstErrorTime = utcNow;
            _previousErrorTime = utcNow;
            return;
        }
        if (elapsedSinceLastReconnect < ReconnectMinInterval)
        {
            return; // Some other thread made it through the check and the lock, so nothing to do.
        }
        TimeSpan elapsedSinceFirstError = utcNow - _firstErrorTime;
        TimeSpan elapsedSinceMostRecentError = utcNow - _previousErrorTime;
        bool shouldReconnect =
            elapsedSinceFirstError >= ReconnectErrorThreshold // Make sure we gave the multiplexer enough time to reconnect on its own if it could.
            && elapsedSinceMostRecentError <= ReconnectErrorThreshold; // Make sure we aren't working on stale data (e.g. if there was a gap in errors, don't reconnect yet).
        // Update the previousErrorTime timestamp to be now (e.g. this reconnect request).
        _previousErrorTime = utcNow;
        if (!shouldReconnect)
        {
            return;
        }
        _firstErrorTime = DateTimeOffset.MinValue;
        _previousErrorTime = DateTimeOffset.MinValue;
        ConnectionMultiplexer oldConnection = _connection;
        await CloseConnectionAsync(oldConnection);
        _connection = null;
        _connection = await CreateConnectionAsync();
        Interlocked.Exchange(ref _lastReconnectTicks, utcNow.UtcTicks);
    }
    finally
    {
        _reconnectSemaphore.Release();
    }
}
// In real applications, consider using a framework such as
// Polly to make it easier to customize the retry approach.
private static async Task<T> BasicRetryAsync<T>(Func<T> func)
{
    int reconnectRetry = 0;
    int disposedRetry = 0;
    while (true)
    {
        try
        {
            return func();
        }
        catch (Exception ex) when (ex is RedisConnectionException || ex is SocketException)
        {
            reconnectRetry++;
            if (reconnectRetry > RetryMaxAttempts)
                throw;
            await ForceReconnectAsync();
        }
        catch (ObjectDisposedException)
        {
            disposedRetry++;
            if (disposedRetry > RetryMaxAttempts)
                throw;
        }
    }
}
public static Task<IDatabase> GetDatabaseAsync()
{
    return BasicRetryAsync(() => Connection.GetDatabase());
}
public static Task<System.Net.EndPoint[]> GetEndPointsAsync()
{
    return BasicRetryAsync(() => Connection.GetEndPoints());
}
public static Task<IServer> GetServerAsync(string host, int port)
{
    return BasicRetryAsync(() => Connection.GetServer(host, port));
}
```

## <a name="executing-cache-commands"></a>キャッシュ コマンドの実行

コンソール アプリの `Program` クラスの `Main` プロシージャに次のコードを追加します。

```csharp
static void Main(string[] args)
{
    IDatabase cache = GetDatabase();

    // Perform cache operations using the cache object...

    // Simple PING command
    string cacheCommand = "PING";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    Console.WriteLine("Cache response : " + cache.Execute(cacheCommand).ToString());

    // Simple get and put of integral data types into the cache
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    cacheCommand = "SET Message \"Hello! The cache is working from a .NET console app!\"";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
    Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET console app!").ToString());

    // Demonstrate "SET Message" executed as expected...
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    // Get the client list, useful to see if connection list is growing...
    // Note that this requires allowAdmin=true in the connection string
    cacheCommand = "CLIENT LIST";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    var endpoint = (System.Net.DnsEndPoint)GetEndPoints()[0];
    IServer server = GetServer(endpoint.Host, endpoint.Port);
    ClientInfo[] clients = server.ClientList();

    Console.WriteLine("Cache response :");
    foreach (ClientInfo client in clients)
    {
        Console.WriteLine(client.Raw);
    }

    CloseConnection(lazyConnection);
}
```

Azure Cache for Redis には、Azure Cache for Redis 内のデータを論理的に分離する目的に使用できるデータベースがあります (既定ではデータベースの数が 16 個ですが、設定により変更できます)。 コードは、既定のデータベース DB 0 に接続されます。 詳細については、「[What are Redis databases? (Redis データベースとは)](cache-development-faq.yml#what-are-redis-databases-)」と「[既定の Redis サーバー構成](cache-configure.md#default-redis-server-configuration)」を参照してください。

キャッシュ項目の格納と取得には、`StringSet` および `StringGet`メソッドを使用します。

Redis では、ほとんどのデータが Redis 文字列として保存されますが、これらの文字列には、さまざまなデータ型を格納することができます。シリアル化したバイナリ データもその 1 つで、.NET のオブジェクトをキャッシュに保存する際に使用することができます。

**Ctrl + F5** キーを押してアプリをビルドし、実行します。

次の例では、`Message` キーは、前に Azure portal の Redis コンソールを使って設定されたキャッシュ値を持っていたことがわかります。 アプリは、そのキャッシュ値を更新しました。 また、アプリは `PING` および `CLIENT LIST` コマンドも実行しました。

![コンソール アプリの一部](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>キャッシュ内で .NET オブジェクトを使用する

Azure Cache for Redis は .NET オブジェクトとプリミティブ データ型の両方をキャッシュできますが、.NET オブジェクトをキャッシュするためには、あらかじめシリアル化しておく必要があります。 この .NET オブジェクトのシリアル化はアプリケーション開発者が行わなければなりません。逆にそのことでシリアライザーの選択に幅が生まれ、開発者にとってのメリットとなっています。

オブジェクトをシリアル化する簡単な方法の 1 つは、[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) の `JsonConvert` シリアル化メソッドを使用して、JSON へおよび JSON からシリアル化する方法です。 このセクションでは、.NET オブジェクトをキャッシュに追加します。

Visual Studio で、 **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択し、[パッケージ マネージャー コンソール] ウィンドウから次のコマンドを実行します。

```powershell
Install-Package Newtonsoft.Json
```

*Program.cs* の先頭に次の `using` ステートメントを追加します。

```csharp
using Newtonsoft.Json;
```

次の `Employee` クラス定義を *Program.cs* に追加します。

```csharp
class Employee
{
    public string Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }

    public Employee(string employeeId, string name, int age)
    {
        Id = employeeId;
        Name = name;
        Age = age;
    }
}
```

*Program.cs* の `Main()` プロシージャの一番下と、`CloseConnection()` の呼び出しの前に、シリアル化された .NET オブジェクトをキャッシュして取得する以下のコード行を追加します。

```csharp
    // Store .NET object to cache
    Employee e007 = new Employee("007", "Davide Columbo", 100);
    Console.WriteLine("Cache response from storing Employee .NET object : " + 
    cache.StringSet("e007", JsonConvert.SerializeObject(e007)));

    // Retrieve .NET object from cache
    Employee e007FromCache = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e007"));
    Console.WriteLine("Deserialized Employee .NET object :\n");
    Console.WriteLine("\tEmployee.Name : " + e007FromCache.Name);
    Console.WriteLine("\tEmployee.Id   : " + e007FromCache.Id);
    Console.WriteLine("\tEmployee.Age  : " + e007FromCache.Age + "\n");
```

**Ctrl + F5** キーを押してコンソール アプリをビルドして実行し、.NET オブジェクトのシリアル化をテストします。 

![完了したコンソール アプリ](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のチュートリアルに進む場合は、このクイック スタートで作成したリソースを維持して、再利用することができます。

クイック スタートのサンプル アプリケーションの使用を終える場合は、課金を避けるために、このクイック スタートで作成した Azure リソースを削除することができます。 

> [!IMPORTANT]
> いったん削除したリソース グループを元に戻すことはできません。リソース グループとそこに存在するすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 このサンプルをホストするためのリソースを、保持するリソースが含まれる既存のリソース グループ内に作成した場合、リソース グループを削除する代わりに、左側で各リソースを個別に削除できます。
>

[Azure portal](https://portal.azure.com) にサインインし、 **[リソース グループ]** を選択します。

**[名前でフィルター]** ボックスにリソース グループの名前を入力します。 この記事の手順では、*TestResources* という名前のリソース グループを使用しました。 結果一覧でリソース グループの **[...]** を選択し、 **[リソース グループの削除]** を選択します。

![削除](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-delete-resource-group.png)

リソース グループの削除の確認を求めるメッセージが表示されます。 確認のためにリソース グループの名前を入力し、 **[削除]** を選択します。

しばらくすると、リソース グループとそこに含まれているすべてのリソースが削除されます。



<a name="next-steps"></a>

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、.NET アプリケーションから Azure Cache for Redis を使用する方法を説明しました。 ASP.NET Web アプリと Azure Cache for Redis を使用するには、次のクイック スタートに進みます。

> [!div class="nextstepaction"]
> [Azure Cache for Redis を使用する ASP.NET Web アプリを作成する](./cache-web-app-howto.md)

クラウドの支出を最適化して節約しますか?

> [!div class="nextstepaction"]
> <bpt id="p1">[</bpt>Cost Management を使用してコスト分析を開始する<ept id="p1">](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)</ept>
