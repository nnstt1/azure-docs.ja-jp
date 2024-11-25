---
title: 'トラブルシューティング: 診断ログ'
titleSuffix: Azure Digital Twins
description: この記事では、診断設定を使用したログ記録を有効にし、ログに対してクエリを実行してすぐに表示する方法を確認します。 また、ログ カテゴリとそのスキーマについて学習します。
author: baanders
ms.author: baanders
ms.date: 9/24/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy22q1
ms.openlocfilehash: 89b7c741ce75a629de99e3337428027429bce5b7
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131773"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logs"></a>Azure Digital Twins のトラブルシューティング: 診断ログ

この記事では、[Azure portal](https://portal.azure.com) で診断設定を構成する方法について説明します。これには、収集するログの種類と、それらを保存する場所 (Log Analytics や選択したストレージ アカウントなど) が含まれます。 その後、ログに対してクエリを実行し、カスタム分析情報をすばやく収集することができます。

Azure Digital Twins では、サービス インスタンスの **ログ** を収集し、そのパフォーマンス、アクセス、その他のデータを監視できます。 これらのログを使用して、Azure Digital Twins インスタンスで何が起こっているかを把握し、問題の根本原因を分析できます。Azure サポートに連絡する必要はありません。

この記事には、Azure Digital Twins で収集できるすべての **ログ カテゴリ** と、それらの **スキーマ** に関する情報も含まれています。

## <a name="turn-on-diagnostic-settings"></a>診断設定を有効にする 

診断設定を有効にして、Azure Digital Twins インスタンスでのログ収集を開始します。 エクスポートされたログの保存先を選択することもできます。 Azure Digital Twins インスタンスの診断設定を有効にする方法は次のとおりです。

1. [Azure portal](https://portal.azure.com) にサインインし、Azure Digital Twins インスタンスに移動します。 その名前をポータルの検索バーに入力して、検索することができます。 

2. メニューから **[診断設定]** 、 **[診断設定を追加する]** の順に選択します。

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Azure portal の [診断設定] ページと追加するボタンが表示されたスクリーンショット。" lightbox="media/troubleshoot-diagnostics/diagnostic-settings.png":::

3. 次のページで、以下の値を入力します。
     * **診断設定の名前**:診断設定の名前を付けます。
     * **カテゴリの詳細**:監視する操作を選択し、チェック ボックスをオンにしてそれらの操作の診断を有効にします。 診断設定では、次の操作をレポートできます。
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        これらのカテゴリとそれらに含まれる情報の詳細については、後述の「[ログのカテゴリ](#log-categories)」セクションを参照してください。
     * **宛先の詳細**:ログの送信先を選択します。 3 つのオプションを自由に組み合わせて選択できます。
        - Log Analytics への送信
        - ストレージ アカウントへのアーカイブ
        - イベント ハブへのストリーミング

        宛先の選択のために追加の詳細情報が必要な場合に、入力を求められることがあります。  
    
4. 新しい設定を保存します。 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Azure portal の [診断設定] ページが表示されたスクリーンショット。ここで、ユーザーは診断設定の情報を入力します。" lightbox="media/troubleshoot-diagnostics/diagnostic-settings-details.png":::

新しい設定は、10 分ほどで有効になります。 その後、インスタンスの **[診断設定]** ページ上の構成されたターゲットにログが表示されます。 

診断設定とそれらの設定オプションの詳細については、「[プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。

## <a name="view-and-query-logs"></a>ログを表示してクエリを実行する

Azure Digital Twins ログのストレージの詳細を構成したら、**カスタム クエリ** を記述して分析情報を生成し、問題のトラブルシューティングを行うことができます。 また、このサービスには、顧客からのインスタンスに関する一般的な質問に対処することで、開始に役立つクエリの例もいくつか用意されています。

ここでは、インスタンスのログに対してクエリを実行する方法を示します。

1. [Azure portal](https://portal.azure.com) にサインインし、Azure Digital Twins インスタンスに移動します。 その名前をポータルの検索バーに入力して、検索することができます。 

2. メニューから **[ログ]** を選択して、ログ クエリ ページを開きます。 ページが開き、 *[クエリ]* というウィンドウが表示されます。

    :::image type="content" source="media/troubleshoot-diagnostics/logs.png" alt-text="Azure portal で Azure Digital Twins インスタンスの [ログ] ページが表示されたスクリーンショット。[クエリ] ウィンドウが重ねて表示され、事前構築済みクエリが表示されています。" lightbox="media/troubleshoot-diagnostics/logs.png":::

    これらのクエリは、さまざまなログ用に記述された構築済みのサンプルです。 クエリの 1 つを選択してクエリ エディターに読み込み、それを実行してインスタンスのこれらのログを確認できます。

    また、カスタム クエリ コードを記述したり編集したりできる、クエリ エディター ページに直接移動するために何も実行せずに *[クエリ]* ウィンドウを閉じることもできます。

3. *[クエリ]* ウィンドウを閉じた後、クエリ エディターのメイン ページが表示されます。 ここでは、サンプル クエリのテキストを表示および編集したり、独自のクエリを最初から記述したりすることができます。
    :::image type="content" source="media/troubleshoot-diagnostics/logs-query.png" alt-text="Azure portal で Azure Digital Twins インスタンスの [ログ] ページが表示されたスクリーンショット。これには、ログ、クエリ コード、クエリ履歴の一覧が含まれます。" lightbox="media/troubleshoot-diagnostics/logs-query.png":::

    左側のペインでは、 
    - *[テーブル]* タブに、クエリで使用できるさまざまな Azure Digital Twins の [ログ カテゴリ](#log-categories)が表示されています。 
    - *[クエリ]* タブには、エディターに読み込むことができるクエリの例が含まれています。
    - *[フィルター]* タブでは、クエリによって返されるデータのフィルター処理されたビューをカスタマイズできます。

ログ クエリとその記述方法の詳細については、[Azure Monitor のログ クエリの概要](../azure-monitor/logs/log-query-overview.md)に関するページを参照してください。

## <a name="log-categories"></a>ログのカテゴリ

ここでは、Azure Digital Twins で収集されるログのカテゴリについて詳しく説明します。

| ログのカテゴリ | 説明 |
| --- | --- |
| ADTModelsOperation | モデルに関連するすべての API コールをログに記録する |
| ADTQueryOperation | クエリに関連するすべての API コールをログに記録する |
| ADTEventRoutesOperation | イベント ルートに関連するすべての API 呼び出しに加え、Azure Digital Twins から Event Grid、Event Hubs、Service Bus などのエンドポイント サービスへのイベントの送信をログに記録する |
| ADTDigitalTwinsOperation | 個々のツインに関連する API 呼び出しをすべてログに記録する |

各ログ カテゴリは、書き込み、読み取り、削除、およびアクションの操作で構成されます。 これらのカテゴリは、次のように REST API 呼び出しにマップされます。

| イベントの種類 | REST API の操作 |
| --- | --- |
| Write | PUT と PATCH |
| Read | GET |
| 削除 | DELETE |
| アクション | POST |

各カテゴリに記録される操作と対応する [Azure Digital Twins REST API 呼び出し](/rest/api/azure-digitaltwins/)の一覧を次に示します。 

>[!NOTE]
> 各ログ カテゴリには、複数の操作/REST API 呼び出しが含まれています。 次の表では、各ログ カテゴリがすべての操作/REST API 呼び出しにマップされています (次のログ カテゴリが表示されるまで)。 

| ログのカテゴリ | 操作 | REST API 呼び出しとその他のイベント |
| --- | --- | --- |
| ADTModelsOperation | Microsoft.DigitalTwins/models/write | デジタル ツイン モデル更新 API |
|  | Microsoft.DigitalTwins/models/read | デジタル ツイン モデルの ID による取得とリスト API |
|  | Microsoft.DigitalTwins/models/delete | デジタル ツイン モデルの削除 API |
|  | Microsoft.DigitalTwins/models/action | デジタル ツイン モデルの追加 API |
| ADTQueryOperation | Microsoft.DigitalTwins/query/action | ツインのクエリ API |
| ADTEventRoutesOperation | Microsoft.DigitalTwins/eventroutes/write | イベント ルート追加 API |
|  | Microsoft.DigitalTwins/eventroutes/read | イベント ルートの ID による取得とリスト API |
|  | Microsoft.DigitalTwins/eventroutes/delete | イベント ルートの削除 API |
|  | Microsoft.DigitalTwins/eventroutes/action | エンドポイント サービスにイベントを発行しようとしてエラーが発生した (API 呼び出しではない) |
| ADTDigitalTwinsOperation | Microsoft.DigitalTwins/digitaltwins/write | Digital Twins の追加、リレーションシップの追加、更新、コンポーネントの更新 |
|  | Microsoft.DigitalTwins/digitaltwins/read | Digital Twins の ID による取得、コンポーネントの取得、ID によるリレーションシップの取得、受信リレーションシップのリスト、リレーションシップのリスト |
|  | Microsoft.DigitalTwins/digitaltwins/delete | Digital Twins の削除、リレーションシップの削除 |
|  | Microsoft.DigitalTwins/digitaltwins/action | Digital Twins のコンポーネント テレメトリの送信、テレメトリの送信 |

## <a name="log-schemas"></a>ログ スキーマ 

各ログ カテゴリには、そのカテゴリ内のイベントの報告方法を定義するスキーマがあります。 個々のログ エントリはテキストとして保存され、形式は JSON BLOB となります。 ログおよび JSON 本文例内のフィールドは、以下の各種類のログに対して提供されます。 

`ADTDigitalTwinsOperation`、`ADTModelsOperation`、および `ADTQueryOperation` は、一貫性のある API ログ スキーマを使用します。 `ADTEventRoutesOperation` はスキーマを拡張して、プロパティに `endpointName` フィールドを含めます。

### <a name="api-log-schemas"></a>API ログ スキーマ

このログ スキーマは、`ADTDigitalTwinsOperation`、`ADTModelsOperation`、`ADTQueryOperation` で一貫しています。 同じスキーマが `ADTEventRoutesOperation` にも使用されますが、操作名 `Microsoft.DigitalTwins/eventroutes/action` の **例外** があります (このスキーマの詳細については、次のセクション、「[エグレス ログ スキーマ](#egress-log-schemas)」を参照してください)。

このスキーマには、Azure Digital Twins インスタンスへの API 呼び出しに関連する情報が含まれています。

API ログのフィールドおよびプロパティの説明を次に示します。

| フィールド名 | データ型 | 説明 |
|-----|------|-------------|
| `Time` | DateTime | このイベントが発生した日時 (UTC) |
| `ResourceId` | String | イベントが発生したリソースの Azure Resource Manager リソース ID |
| `OperationName` | String  | イベント中に実行されるアクションの種類 |
| `OperationVersion` | String | イベント中に使用される API バージョン |
| `Category` | String | 出力されるリソースの種類 |
| `ResultType` | String | イベントの結果 |
| `ResultSignature` | String | イベントの HTTP ステータス コード |
| `ResultDescription` | String | イベントに関する追加情報 |
| `DurationMs` | String | イベントの実行にかかった時間 (ミリ秒) |
| `CallerIpAddress` | String | イベントのマスクされた発信元 IP アドレス |
| `CorrelationId` | Guid | 顧客が指定したイベントの一意識別子 |
| `ApplicationId` | Guid | ベアラー認証で使用されるアプリケーション ID |
| `Level` | int | イベントのログ重大度 |
| `Location` | String | イベントが発生したリージョン |
| `RequestUri` | Uri | イベント中に使用されるエンドポイント |
| `TraceId` | String | `TraceId`、[W3C トレース コンテキスト](https://www.w3.org/TR/trace-context/)の一部として。 システム全体に分散されたトレースを一意に識別するために使用されるトレース全体の ID です。 |
| `SpanId` | String | `SpanId`、[W3C トレース コンテキスト](https://www.w3.org/TR/trace-context/)の一部として。 トレース内にあるこの要求の ID です。 |
| `ParentId` | String | `ParentId`、[W3C トレース コンテキスト](https://www.w3.org/TR/trace-context/)の一部として。 親 ID がない要求は、トレースのルートになります。 |
| `TraceFlags` | String | `TraceFlags`、[W3C トレース コンテキスト](https://www.w3.org/TR/trace-context/)の一部として。 サンプリング、トレース レベルなどのトレース フラグを制御します。 |
| `TraceState` | String | `TraceState`、[W3C トレース コンテキスト](https://www.w3.org/TR/trace-context/)の一部として。 さまざまな分散トレース システムにまたがる、その他のベンダー固有のトレース識別情報。 |

これらの種類のログの JSON 本文の例を次に示します。

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-10-31",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": 8,
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-10-31",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "80",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-10-31",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

これは `Microsoft.DigitalTwins/eventroutes/action` 型 **ではない** `ADTEventRoutesOperation` の JSON 本文になります (このスキーマの詳細については、次のセクション、「[エグレス ログ スキーマ](#egress-log-schemas)」を参照してください)。

```json
  {
    "time": "2020-10-30T22:18:38.0708705Z",
    "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
    "operationName": "Microsoft.DigitalTwins/eventroutes/write",
    "operationVersion": "2020-10-31",
    "category": "EventRoutesOperation",
    "resultType": "Success",
    "resultSignature": "204",
    "resultDescription": "",
    "durationMs": 42,
    "callerIpAddress": "212.100.32.*",
    "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
    "identity": {
      "claims": {
        "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
      }
    },
    "level": "4",
    "location": "southcentralus",
    "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/EventRoutes/egressRouteForEventHub?api-version=2020-10-31",
    "properties": {},
    "traceContext": {
      "traceId": "95ff77cfb300b04f80d83e64d13831e7",
      "spanId": "b630da57026dd046",
      "parentId": "9f0de6dadae85945",
      "traceFlags": "01",
      "tracestate": "k1=v1,k2=v2"
    }
  },
```

### <a name="egress-log-schemas"></a>エグレス ログ スキーマ

次の例は、`Microsoft.DigitalTwins/eventroutes/action` 操作名に固有の `ADTEventRoutesOperation` ログに対するスキーマです。 これらには、例外に関する詳細と、Azure Digital Twins インスタンスに接続されたエグレス エンドポイントに関する API 操作の詳細が含まれます。

|フィールド名 | データ型 | 説明 |
|-----|------|-------------|
| `Time` | DateTime | このイベントが発生した日時 (UTC) |
| `ResourceId` | String | イベントが発生したリソースの Azure Resource Manager リソース ID |
| `OperationName` | String  | イベント中に実行されるアクションの種類 |
| `Category` | String | 出力されるリソースの種類 |
| `ResultDescription` | String | イベントに関する追加情報 |
| `CorrelationId` | Guid | 顧客が指定したイベントの一意識別子 |
| `ApplicationId` | Guid | ベアラー認証で使用されるアプリケーション ID |
| `Level` | int | イベントのログ重大度 |
| `Location` | String | イベントが発生したリージョン |
| `TraceId` | String | `TraceId`、[W3C トレース コンテキスト](https://www.w3.org/TR/trace-context/)の一部として。 システム全体に分散されたトレースを一意に識別するために使用されるトレース全体の ID です。 |
| `SpanId` | String | `SpanId`、[W3C トレース コンテキスト](https://www.w3.org/TR/trace-context/)の一部として。 トレース内にあるこの要求の ID です。 |
| `ParentId` | String | `ParentId`、[W3C トレース コンテキスト](https://www.w3.org/TR/trace-context/)の一部として。 親 ID がない要求は、トレースのルートになります。 |
| `TraceFlags` | String | `TraceFlags`、[W3C トレース コンテキスト](https://www.w3.org/TR/trace-context/)の一部として。 サンプリング、トレース レベルなどのトレース フラグを制御します。 |
| `TraceState` | String | `TraceState`、[W3C トレース コンテキスト](https://www.w3.org/TR/trace-context/)の一部として。 さまざまな分散トレース システムにまたがる、その他のベンダー固有のトレース識別情報。 |
| `EndpointName` | String | Azure Digital Twins で作成されたエグレス エンドポイントの名前 |

これらの種類のログの JSON 本文の例を次に示します。

#### <a name="adteventroutesoperation-for-microsoftdigitaltwinseventroutesaction"></a>ADTEventRoutesOperation for Microsoft.DigitalTwins/eventroutes/action

これは、`Microsoft.DigitalTwins/eventroutes/action` 型の `ADTEventRoutesOperation` に対する JSON 本文です。

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "operationVersion": "",
  "category": "EventRoutesOperation",
  "resultType": "",
  "resultSignature": "",
  "resultDescription": "Unable to send EventHub message to [myPath] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "durationMs": -1,
  "callerIpAddress": "",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "",
  "properties": {
    "endpointName": "myEventHub"
  },
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
},
```

## <a name="next-steps"></a>次のステップ

* 診断を構成することの詳細については、[Azure リソースからログ データを収集して使用する](../azure-monitor/essentials/platform-logs-overview.md)ことに関するページを参照してください。
* Azure Digital Twins のメトリックについては、[トラブルシューティング: メトリック](troubleshoot-metrics.md)に関するページを参照してください。
* メトリックのアラートを有効にする方法については、[トラブルシューティング: アラート](troubleshoot-alerts.md)に関するページを参照してください。