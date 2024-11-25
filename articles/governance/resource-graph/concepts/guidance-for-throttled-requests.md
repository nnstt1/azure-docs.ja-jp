---
title: スロットルされた要求に関するガイダンス
description: Azure Resource Graph によって要求がスロットルされないように、グループ化、時間差処理、改ページ調整、およびクエリの並列処理を行う方法について説明します。
ms.date: 09/13/2021
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: eece1d35cdabcca957e4ce1e72e32f243a1747e1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589741"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Azure Resource Graph のスロットルされた要求に関するガイダンス

Azure Resource Graph データのプログラムによる頻繁な使用を作成するときは、スロットリングがクエリの結果にどのように影響するかを検討する必要があります。 データの要求方法を変更することで、ユーザーと組織でのスロットルが回避され、Azure リソースに関するタイムリーなデータのフローを維持することができます。

この記事では、Azure Resource Graph でのクエリの作成に関連する 4 つの領域とパターンについて説明します。

- スロットリング ヘッダーを理解する
- クエリのグループ化
- クエリの時間差処理
- 改ページ位置の自動修正の影響

## <a name="understand-throttling-headers"></a>スロットリング ヘッダーを理解する

Azure Resource Graph では、タイム ウィンドウに基づいて各ユーザーにクォータ数が割り当てられます。 たとえば、ユーザーは、5 秒間のウィンドウごとに最大 15 のクエリをスロットルなしで送信できます。 クォータ値は多数の要因によって決定され、変更される可能性があります。

すべてのクエリ応答で、Azure Resource Graph によって、2 つのスロットリング ヘッダーが追加されます。

- `x-ms-user-quota-remaining` (int):ユーザーの残りリソース クォータ。 この値はクエリ カウントにマップされます。
- `x-ms-user-quota-resets-after` (hh:mm:ss):ユーザーのクォータ消費量がリセットされるまでの期間。

セキュリティ プリンシパルがテナントまたは管理グループ [クエリ スコープ](./query-language.md#query-scope)内の 5,000 を超えるサブスクリプションにアクセスできる場合、応答は最初の 5,000 サブスクリプションに限定され、`x-ms-tenant-subscription-limit-hit` ヘッダーは `true` として返されます。

ヘッダーの働きを示すために、ヘッダーと、値 `x-ms-user-quota-remaining: 10` と `x-ms-user-quota-resets-after: 00:00:03` が含まれているクエリ応答を検討してみましょう。

- 次の 3 秒以内に、最大 10 個のクエリをスロットルなしで送信できます。
- 3 秒後に、`x-ms-user-quota-remaining` と `x-ms-user-quota-resets-after` の値が、それぞれ `15` と `00:00:05` にリセットされます。

これらのヘッダーを使用したクエリ要求の "_バックオフ_" の例については、「[並行で処理されるクエリ](#query-in-parallel)」を参照してください。

## <a name="grouping-queries"></a>クエリのグループ化

サブスクリプション、リソース グループ、または個々のリソースによるクエリのグループ化は、クエリの並列化よりも効率的です。 大規模なクエリのクォータ コストは、多くの場合、多数の小規模なターゲット クエリのクォータ コストよりも小さくなります。 グループ サイズを _300_ 未満にすることが推奨されています。

- 最適化が不足しているアプローチの例

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "Resoures | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- 最適化されたグループ化アプローチの例 1

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int groupSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / groupSize; ++i)
  {
      var currSubscriptionGroup = subscriptionIds.Skip(i * groupSize).Take(groupSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionGroup,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- 1 つのクエリで複数のリソースを取得するための最適化されたグループ化アプローチの例 2

  ```kusto
  Resources | where id in~ ({resourceIdGroup}) | project name, type
  ```

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int groupSize = 100;
  for (var i = 0; i <= resourceIds.Count / groupSize; ++i)
  {
      var resourceIdGroup = string.Join(",",
          resourceIds.Skip(i * groupSize).Take(groupSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIdGroup}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>クエリの時間差処理

スロットリングの実行方法のため、クエリを時間差で実行することをお勧めします。 つまり、60 のクエリを同時に送信する代わりに、クエリを 4 つの 5 秒間のウィンドウで時間差で送信します。

- 時間差を使用しないクエリのスケジュール

  | クエリ数         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | 期間 (秒) | 0-5 | 5-10 | 10-15 | 15-20 |

- 時間差を使用するクエリのスケジュール

  | クエリ数         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | 期間 (秒) | 0-5 | 5-10 | 10-15 | 15-20 |

次に示すのは、Azure Resource Graph に対してクエリを実行するときのスロットリング ヘッダーに関する例です。

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>並行で処理されるクエリ

並列処理よりもグループ化が推奨されますが、クエリを簡単にグループ化できない場合があります。 このような場合は、複数のクエリを並列で送信することによって、Azure Resource Graph をクエリします。 このようなシナリオで、スロットリング ヘッダーに基づいて "_バックオフ_" する方法の例を次に示します。

```csharp
IEnumerable<IEnumerable<string>> queryGroup = /* Groups of queries  */
// Run groups in parallel.
await Task.WhenAll(queryGroup.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);

        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>改ページ位置の自動修正

Azure Resource Graph では、単一のクエリ応答で最大 1,000 のエントリが返されるため、探している完全なデータセットを取得するには、クエリの[改ページ調整](./work-with-data.md#paging-results)が必要な場合があります。 ただし、一部の Azure Resource Graph クライアントでは、他とは異なる改ページ位置の自動修正処理が行われます。

- C# SDK

  ResourceGraph SDK を使用する場合は、前のクエリ応答から返されるスキップ トークンを次の改ページ調整されるクエリに渡すことで、改ページ位置の自動修正を処理する必要があります。 この設計が意味しているのは、すべての改ページ調整された呼び出しの結果を収集し、それらを最後に結合する必要があることです。 この場合、改ページ調整された各クエリの送信によって、1 つのクエリ クォータが消費されます。

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "Resources | project id, name, type");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

## <a name="still-get-throttled"></a>スロットルが解消されない場合

上記の推奨事項を実行してもスロットルされる場合は、[Azure Resource Graph チーム](mailto:resourcegraphsupport@microsoft.com)にお問い合わせください。

次の詳細情報を提供してください。

- スロットリング制限が高い具体的なユース ケースとビジネス推進ニーズ。
- アクセスするリソースの数。 単一のクエリで返されるエントリの数。
- 関心のあるリソースの種類。
- お使いのクエリ パターン。 Y 秒あたり X 回のクエリなど。

## <a name="next-steps"></a>次のステップ

- [初歩的なクエリ](../samples/starter.md)で使用されている言語を確認します。
- [高度なクエリ](../samples/advanced.md)で高度な使用方法を確認します。
- [リソースを探索する](explore-resources.md)方法について詳しく確認します。
