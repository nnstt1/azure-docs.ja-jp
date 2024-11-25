---
title: Azure Functions 用 JavaScript 開発者向けリファレンス
description: JavaScript を使用して関数を開発する方法について説明します。
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: conceptual
ms.date: 10/07/2021
ms.custom: devx-track-js
ms.openlocfilehash: 8a4026334e4b0313513e57ac8ed78cd9f24f6e34
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028042"
---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions の JavaScript 開発者向けガイド

このガイドでは、JavaScript を使用した Azure Functions の開発を成功させるために役立つ詳細情報について説明します。

Express.js、Node.js、または JavaScript の開発者が、Azure Functions を初めて使用する場合は、まず次のいずれかの記事を読むことをお勧めします。

| 作業の開始 | 概念| ガイド付き学習 |
| -- | -- | -- | 
| <ul><li>[Visual Studio Code を使用した Node.js 関数](./create-first-function-vs-code-node.md)</li><li>[ターミナル/コマンド プロンプトを使用した Node.js 関数](./create-first-function-cli-node.md)</li><li>[Azure portal を使用した Node.js 関数](functions-create-function-app-portal.md)</li></ul> | <ul><li>[開発者ガイド](functions-reference.md)</li><li>[ホスティング オプション](functions-scale.md)</li><li>[TypeScript 関数](#typescript)</li><li>[パフォーマンス&nbsp;に関する考慮事項](functions-best-practices.md)</li></ul> | <ul><li>[サーバーレス アプリケーションの作成](/learn/paths/create-serverless-applications/)</li><li>[Node.js と Express API をサーバーレス API にリファクタリングする](/learn/modules/shift-nodejs-express-apis-serverless/)</li></ul> |

## <a name="javascript-function-basics"></a>JavaScript 関数の基本

JavaScript (Node.js) 関数はエクスポートされた `function` であり、トリガーされると実行します ([トリガーは function.json で構成します](functions-triggers-bindings.md))。 各関数に渡される最初の引数は `context` オブジェクトで、バインディング データの送受信、ログ記録、ランタイムとの通信に使用されます。

## <a name="folder-structure"></a>フォルダー構造

JavaScript プロジェクトでは、次のようなフォルダー構造が必要です。 この既定値は変更可能です。 詳しくは、後の [scriptFile](#using-scriptfile) に関するセクションをご覧ください。

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

プロジェクトのルートには、関数アプリの構成に使用できる共有 [host.json](functions-host-json.md) ファイルがあります。 各関数には、独自のコード ファイル (.js) とバインド構成ファイル (function.json) が含まれるフォルダーがあります。 `function.json` の親ディレクトリの名前は常に関数の名前です。

Functions ランタイムの[バージョン 2.x](functions-versions.md) に必要なバインディング拡張機能は `extensions.csproj` ファイル内に定義されており、実際のライブラリ ファイルは `bin` フォルダーにあります。 ローカルで開発する場合は、[バインド拡張機能を登録する](./functions-bindings-register.md#extension-bundles)必要があります。 Azure portal 上で関数を開発するときに、この登録が実行されます。

## <a name="exporting-a-function"></a>関数のエクスポート

[`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (または [`exports`](https://nodejs.org/api/modules.html#modules_exports)) を使用して、JavaScript 関数をエクスポートする必要があります。 エクスポートする関数は、トリガーされたときに実行される JavaScript 関数である必要があります。

既定では、Functions ランタイムは `index.js` で関数を検索します。`index.js` は、対応する `function.json` と同じ親ディレクトリを共有します。 既定では、エクスポートされる関数は、そのファイルからの唯一のエクスポートである必要があります (`run` または `index` という名前のエクスポート)。 関数のファイルの場所とエクスポートの名前を構成する方法については、後の「[関数のエントリ ポイントを構成する](functions-reference-node.md#configure-function-entry-point)」をご覧ください。

エクスポートされる関数には、実行時に多数の引数が渡されます。 受け取る最初の引数は常に、`context` オブジェクトです。 関数が同期の場合 (Promise を返しません)、正しく使用するためには `context.done` の呼び出しが必要なので、`context` オブジェクトを渡す必要があります。

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>async function をエクスポートする
[`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) 宣言またはバージョン 2.x の Functions ランタイムでプレーンな JavaScript の [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) を使用するときは、[`context.done`](#contextdone-method) コールバックを呼び出して関数が完了したことを明示的に通知する必要はありません。 エクスポートされた async function/Promise が完了すると、関数は完了します。 バージョン 1.x ランタイムを対象とする関数では、引き続きコードの実行が完了した際に [`context.done`](#contextdone-method) を呼び出す必要があります。

次の例で示すのは、トリガーされてすぐに実行が完了したことを記録する簡単な関数です。

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

async function をエクスポートするときは、`return` の値を取得するための出力バインドを構成することもできます。 これは、出力バインドが 1 つしかない場合に推奨されます。

`return` を使用して出力を割り当てるには、`function.json` で `name` プロパティを `$return` に変更します。

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

この場合、関数は次の例のようになります。

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>バインド 
JavaScript では、[バインド](functions-triggers-bindings.md)が構成され、関数の function.json で定義されます。 関数は、さまざまな方法でバインドを操作します。

### <a name="inputs"></a>入力
Azure Functions では、入力は、トリガー入力と追加入力という 2 つのカテゴリに分けられます。 関数は、トリガーと他の入力バインド (`direction === "in"` のバインド) を 3 つの方法で読み取ることができます。
 - **_[推奨]_ 関数に渡されるパラメーターを使用します。** それらは、*function.json* に定義されている順序で関数に渡されます。 *function.json* で定義されている `name` プロパティは、パラメーターの名前と一致する方が望ましいですが、必ずしもそうする必要はありません。
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **[`context.bindings`](#contextbindings-property) オブジェクトのメンバーを使用します。** 各メンバーの名前は、*function.json* で定義されている `name` プロパティによって決まります。
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **JavaScript の [`arguments`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments) オブジェクトの入力を使用します。** これは、基本的にパラメーターとして入力を渡すのと同じですが、動的に入力を処理することができます。
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>出力
関数は、さまざまな方法で出力 (`direction === "out"` のバインド) に書き込むことができます。 どの場合も、*function.json* で定義されているバインドの `name` プロパティは、関数に書き込むオブジェクトのメンバーの名前に対応しています。 

次の方法のいずれかで (これらの方法を組み合わせることはできません)、出力バインドにデータを割り当てることができます。

- **_[出力が複数の場合に推奨]_ オブジェクトを返します。** 非同期関数または Promise を返す関数を使用している場合は、割り当てられた出力データを含むオブジェクトを返すことができます。 次の例の出力バインドは、*function.json* で "httpResponse" および "queueOutput" という名前が付けられています。

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  同期関数を使用している場合、[`context.done`](#contextdone-method) を使用してこのオブジェクトを返すことができます (例を参照)。
- **_[出力が 1 つの場合に推奨]_ 直接値を返し $return バインド名を使用します。** これは、関数を返す非同期/Promise でのみ機能します。 「[async function をエクスポートする](#exporting-an-async-function)」の例を参照してください。 
- **`context.bindings` に値を割り当てます。** context.bindings に直接値を割り当てることができます。

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>バインドのデータ型

入力バインドのデータ型を定義するには、バインド定義の `dataType` プロパティを使用します。 たとえば、バイナリ形式で HTTP 要求のコンテンツを読み取るには、`binary` 型を使用します。

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

`dataType` のオプションは、`binary`、`stream`、`string` です。

## <a name="context-object"></a>context オブジェクト

ランタイムは `context` オブジェクトを使用して、関数とランタイムとの間でデータを受け渡します。 バインドからのデータの読み取りと設定や、ログへの書き込みのために使用される `context` オブジェクトは常に、関数に渡される最初のパラメーターです。

同期コードを備えた関数の場合は、context オブジェクトに、その関数が処理を完了したときに呼び出す `done` コールバックが含まれています。 非同期コードを記述する場合は、`done` を明示的に呼び出す必要はありません。`done` コールバックは暗黙的に呼び出されます。

```javascript
module.exports = (context) => {

    // function logic goes here

    context.log("The function has executed.");

    context.done();
};
```

関数に渡されるコンテキストでは、次のプロパティを持つオブジェクトである `executionContext` プロパティが公開されます。

| プロパティ名  | Type  | 説明 |
|---------|---------|---------|
| `invocationId` | String | 特定の関数呼び出しのための一意識別子を提供します。 |
| `functionName` | String | 実行中の関数の名前を提供します。 |
| `functionDirectory` | String | 関数のアプリ ディレクトリを提供します。 |

次の例は、`invocationId` を返す方法を示しています。

```javascript
module.exports = (context, req) => {
    context.res = {
        body: context.executionContext.invocationId
    };
    context.done();
};
```

### <a name="contextbindings-property"></a>context.bindings プロパティ

```js
context.bindings
```

バインド データの読み取りまたは割り当てに使用される名前付きオブジェクトを返します。 入力およびトリガー バインド データには、`context.bindings` のプロパティを読み取ることでアクセスできます。 出力バインド データは、`context.bindings` にデータを追加することで割り当てることができます。

たとえば、function.json で次のようなバインド定義を使用すると、`context.bindings.myInput` からキューの内容にアクセスし、`context.bindings.myOutput` を使用して出力をキューに割り当てることができます。

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

`context.binding` オブジェクトの代わりに `context.done` メソッドを使用して、出力バインド データを定義できます (下記参照)。

### <a name="contextbindingdata-property"></a>context.bindingData プロパティ

```js
context.bindingData
```

トリガーのメタデータと関数呼び出しデータを含む名前付きオブジェクトを返します (`invocationId`、`sys.methodName`、`sys.utcNow`、`sys.randGuid`)。 トリガーのメタデータの例については、こちらの[イベント ハブの例](functions-bindings-event-hubs-trigger.md)をご覧ください。

### <a name="contextdone-method"></a>context.done メソッド

**context.done** メソッドは、同期メソッドによって使用されます。

|同期実行|[非同期](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function)実行<br>(Node 8+、Functions ランタイム 2+)|
|--|--|
|必須: 関数が完了したとランタイムに通知する `context.done([err],[propertyBag])`。 これがない場合、実行はタイムアウトします。<br>`context.done` メソッドを使用すると、ランタイムに対するユーザー定義のエラーと、出力バインド データを含む JSON オブジェクトの両方を、戻すことができます。 `context.done` に渡されるプロパティは、`context.bindings` オブジェクトで設定されているすべてのものを上書きします。|不要: `context.done` - 暗黙的に呼び出されます。| 


```javascript
// Synchronous code only
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>context.log メソッド  

```js
context.log(message)
```

既定のトレース レベルでストリーミング関数ログに書き込むことができます。他のログ レベルも使用できます。 トレース ログの詳細については、次のセクションを参照してください。 

## <a name="write-trace-output-to-logs"></a>トレース出力をログに書き込む

Functions で、`context.log` メソッドを使用してトレース出力をログとコンソールに書き込みます。 `context.log()` を呼び出すと、既定のトレース レベルである、"_情報_" トレース レベルでログにメッセージが書き込まれます。 Functions は Azure Application Insights と統合されているため、関数アプリのログをより適切にキャプチャすることができます。 Azure Monitor の一部である Application Insights では、アプリケーション テレメトリとトレース出力の両方を収集、視覚的に表示、分析するための機能を使用できます。 詳細については、「[Azure Functions の監視](functions-monitoring.md)」を参照してください。

次の例では、情報トレース レベルで呼び出し ID を含む、ログを書き込んでいます。

```javascript
context.log("Something has happened. " + context.invocationId); 
```

すべての `context.log` メソッドは、Node.js の [util.format メソッド](https://nodejs.org/api/util.html#util_util_format_format)でサポートされているのと同じパラメーター形式をサポートしています。 既定のトレース レベルを使用して関数ログに書き込む次のようなコードについて考えます。

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

同じコードを次の形式で記述することもできます。

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

> [!NOTE]  
> トレース出力の書き込みには、`console.log` を使用しないでください。 `console.log` からの出力は関数アプリ レベルでキャプチャされるため、特定の関数呼び出しには関連付けられておらず、特定の関数のログには表示されません。 また、Functions ランタイムのバージョン 1.x では、`console.log` を使用したコンソールへの書き込みはサポートされていません。

### <a name="trace-levels"></a>トレース レベル

既定のレベルに加えて、特定のトレース レベルで関数のログを書き込むことができる次の追加のログ記録メソッドがあります。

| メソッド                 | 説明                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | ログにエラーレベルのイベントを書き込みます。   |
| **warn(_message_)**    | 警告レベルのイベントをログに書き込みます。 |
| **info(_message_)**    | 情報レベルのログ、またはそれ以下に書き込みます。    |
| **verbose(_message_)** | 詳細なレベルのログに書き込みます。           |

次の例では、情報レベルではなく、警告トレース レベルで同じログを書き込みます。

```javascript
context.log.warn("Something has happened. " + context.invocationId); 
```

_エラー_ は最高のトレース レベルであるため、ログ記録が有効になっている限り、このトレースはすべてのトレース レベルで出力に書き込まれます。

### <a name="configure-the-trace-level-for-logging"></a>ログ記録のトレース レベルを構成する

Fuctions を使用して、ログまたはコンソールに書き込むためのしきい値のトレース レベルを定義できます。 具体的なしきい値の設定は、使用している Functions ランタイムのバージョンによって異なります。

# <a name="v2x"></a>[v2.x+](#tab/v2)

ログに書き込まれるトレースのしきい値を設定するには、host.json ファイルの `logging.logLevel` プロパティを使用します。 この JSON オブジェクトを使用すると、関数アプリのすべての関数に既定のしきい値を定義できます。また、個々の関数に対して特定のしきい値を定義することもできます。 詳しくは、[Azure Functions で監視を構成する](configure-monitoring.md)方法に関するページを参照してください。

# <a name="v1x"></a>[v1.x](#tab/v1)

ログおよびコンソールに書き込まれるすべてのトレースのしきい値を設定するには、host.json ファイルの `tracing.consoleLevel` プロパティを使用します。 この設定は、関数アプリのすべての関数に適用されます。 次の例では、詳細ログ記録が有効になるようにトレースのしきい値を設定します。

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

**consoleLevel** の値は、`context.log` メソッドの名前に対応します。 コンソールへのすべてのトレース ログ記録を無効にするには、**consoleLevel** を _off_ に設定します。 詳細については、[host.json v1.x のリファレンス](functions-host-json-v1.md)を参照してください。

---

### <a name="log-custom-telemetry"></a>カスタム テレメトリをログに記録する

既定では、出力は Functions によってトレースとして Application Insights に書き込まれます。 より細かく制御するには、代わりに [Application Insights Node.js SDK](https://github.com/microsoft/applicationinsights-node.js) を使用して、Application Insights インスタンスにカスタム テレメトリ データを送信することもできます。 

# <a name="v2x"></a>[v2.x+](#tab/v2)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.traceContext.traceparent};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

# <a name="v1x"></a>[v1.x](#tab/v1)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.operationId};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

---

`tagOverrides` パラメーターにより、関数の呼び出し ID に `operation_Id` を設定します。 この設定により、特定の関数呼び出しについての自動生成されたテレメトリとカスタム テレメトリをすべて関連付けることができます。

## <a name="http-triggers-and-bindings"></a>HTTP トリガーとバインディング

HTTP、webhook トリガー、および HTTP 出力バインディングでは、要求オブジェクトと応答オブジェクトを使用して HTTP メッセージングを表します。

### <a name="request-object"></a>要求オブジェクト

`context.req` (要求) オブジェクトには、次のプロパティがあります。

| プロパティ      | 説明                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | 要求の本文を格納するオブジェクト。               |
| _headers_     | 要求ヘッダーを格納するオブジェクト。                   |
| _method_      | 要求の HTTP メソッド。                                |
| _originalUrl_ | 要求の URL。                                        |
| _params_      | 要求のルーティング パラメーターを格納するオブジェクト。 |
| _query_       | クエリ パラメーターを格納するオブジェクト。                  |
| _rawBody_     | 文字列としてのメッセージの本文。                           |


### <a name="response-object"></a>応答オブジェクト

`context.res` (応答) オブジェクトには、次のプロパティがあります。

| プロパティ  | 説明                                               |
| --------- | --------------------------------------------------------- |
| _body_    | 応答の本文を格納するオブジェクト。         |
| _headers_ | 応答ヘッダーを格納するオブジェクト。             |
| _isRaw_   | 応答の書式設定をスキップすることを示します。    |
| _status_  | 応答の HTTP 状態コード。                     |
| _cookies_ | 応答に設定される HTTP Cookie オブジェクトの配列。 HTTP Cookie オブジェクトには、`name`、`value`、およびその他の Cookie プロパティ (`maxAge` や `sameSite` など) があります。 |

### <a name="accessing-the-request-and-response"></a>要求と応答へのアクセス 

HTTP トリガーを使用する場合、HTTP 要求オブジェクトと応答オブジェクトにアクセスする方法がいくつかあります。

+ **`context` オブジェクトの `req` プロパティと `res` プロパティから。** この方法で、完全な `context.bindings.name` パターンを使用する代わりに、従来のパターンを使用して context オブジェクトから HTTP データにアクセスできます。 次の例では、`context` の `req` オブジェクトと `res` オブジェクトにアクセスする方法を示します。

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **名前付きの入力バインディングと出力バインディングから。** この方法で、HTTP トリガーとバインディングは他のバインディングと同じように動作します。 次の例では、名前付き `response` バインディングを使用して、応答オブジェクトを設定します。 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[応答のみ]_ `context.res.send(body?: any)` の呼び出し。** HTTP の応答は、入力 `body` を応答本文として使用して作成されます。 `context.done()` は暗黙的に呼び出されます。

+ **_[応答のみ]_ `context.done()` の呼び出し。** これは、`context.done()` メソッドに渡される応答を返す特殊な種類の HTTP バインディングです。 次の HTTP 出力バインディングで、`$return` 出力パラメーターを定義します。

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

要求と応答のキーは小文字であることに注意してください。

## <a name="scaling-and-concurrency"></a>スケーリングと同時性

既定では、Azure Functions は、アプリケーションの負荷を自動的に監視し、必要に応じて node.js 用の追加のホストインスタンスを作成します。 関数は、さまざまなトリガー型の組み込み（ユーザー設定不可）しきい値を使用して、メッセージの経過時間や QueueTrigger のキューサイズなど、インスタンスを追加するタイミングを決定します。 詳細については、「[従量課金プランと Premium プランのしくみ](event-driven-scaling.md)」をご覧ください。

ほとんどの Node.js アプリケーションでは、このスケーリング動作で十分です。 CPUにバインドされたアプリケーションの場合、複数の言語ワーカープロセスを使用して、パフォーマンスをさらに向上させることができます。

既定では、すべての Functions ホスト インスタンスに 1 つの言語ワーカー プロセスがあります。 [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) アプリケーション設定を使用して、ホストごとのワーカー プロセスの数を増やすことができます (最大 10)。 次に、Azure Functions は、これらのワーカー間で同時関数呼び出しを均等に分散しようとします。 

FUNCTIONS_WORKER_PROCESS_COUNT は、要求に応じてアプリケーションをスケールアウトするときに、関数作成する各ホストに適用されます。 

## <a name="node-version"></a>Node バージョン

次の表は、Functions ランタイムの各メジャー バージョンに対して現在サポートされている Node.js バージョンをオペレーティング システムごとに示しています。

| Functions バージョン | Node バージョン (Windows) | Node バージョン (Linux) |
|---|---| --- |
| 4.x | `~14` | `node|14` |
| 3.x (推奨) | `~14` (推奨)<br/>`~12`<br/>`~10` | `node|14` (推奨)<br/>`node|12`<br/>`node|10` |
| 2.x  | `~12`<br/>`~10`<br/>`~8` | `node|10`<br/>`node|8`  |
| 1.x | 6.11.2 (ランタイムによりロック) | 該当なし |

ランタイムが使用している現在のバージョンを確認するには、任意の関数から `process.version` をログに記録します。

### <a name="setting-the-node-version"></a>Node のバージョンを設定する

Windows 関数アプリの場合は、`WEBSITE_NODE_DEFAULT_VERSION` [アプリ設定](functions-how-to-use-azure-function-app-settings.md#settings)をサポートされている LTS バージョン (`~14` など) に設定して、Azure のバージョンをターゲットにします。

Linux 関数アプリの場合は、次の Azure CLI コマンドを実行して、Node のバージョンを更新します。

```bash
az functionapp config set --linux-fx-version "node|14" --name "<MY_APP_NAME>" --resource-group "<MY_RESOURCE_GROUP_NAME>"
```

Azure Functions のランタイム サポート ポリシーの詳細については、こちらの[記事](./language-support-policy.md)を参照してください。

## <a name="dependency-management"></a>依存関係の管理
JavaScript コードでコミュニティ ライブラリを使用するには、次の例で示すように、Azure 内の関数アプリにすべての依存関係がインストールされている必要があります。

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> 関数アプリのルートに `package.json` ファイルを定義する必要があります。 このファイルを定義することによって、アプリのすべての関数を同じキャッシュされたパッケージで共有し、最高クラスのパフォーマンスを得ることができます。 バージョンの競合がある場合は、個別の関数のフォルダーに `package.json` ファイルを追加することで競合を解決できます。  

ソース管理から関数アプリをデプロイする場合、リポジトリ内に存在する `package.json` ファイルはすべて、デプロイ中にそのフォルダー内の `npm install` をトリガーします。 ただし、ポータルまたは CLI 経由でデプロイする場合は、パッケージを手動でインストールする必要があります。

関数アプリにパッケージをインストールするには 2 つの方法があります。 

### <a name="deploying-with-dependencies"></a>依存関係と共に展開する
1. `npm install` を実行して、すべての必要なパッケージをローカルにインストールします。

2. コードを展開し、`node_modules` フォルダーが展開に含まれることを確認します。 


### <a name="using-kudu"></a>Kudu を使用する
1. `https://<function_app_name>.scm.azurewebsites.net` にアクセスします。

2. **[デバッグ コンソール]**  >  **[CMD]** をクリックします。

3. `D:\home\site\wwwroot` に移動し、ページの上半分にある **wwwroot** フォルダーに package.json ファイルをドラッグします。  
    関数アプリにファイルをアップロードする方法は、他にもあります。 詳細については、「[関数アプリ ファイルを更新する方法](functions-reference.md#fileupdate)」を参照してください。 

4. package.json ファイルがアップロードされたら、**Kudu リモート実行コンソール** で `npm install` コマンドを実行します。  
    この操作によって、package.json ファイルに示されているパッケージがダウンロードされ、関数アプリが再起動されます。

## <a name="environment-variables"></a>環境変数

ローカル環境とクラウド環境の両方で、運用シークレット (接続文字列、キー、エンドポイント) や環境設定 (プロファイル変数など) など、独自の環境変数を関数アプリに追加します。 これらの設定には、関数コードで `process.env` を使用してアクセスします。

### <a name="in-local-development-environment"></a>ローカル開発環境

ローカルで実行されている場合は、関数プロジェクトに [`local.settings.json` ファイル](./functions-run-local.md)が含まれています。このファイルに、`Values` オブジェクトの環境変数が格納されます。 

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "translatorTextEndPoint": "https://api.cognitive.microsofttranslator.com/",
    "translatorTextKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "languageWorkers__node__arguments": "--prof"
  }
}
```

### <a name="in-azure-cloud-environment"></a>Azure クラウド環境

Azure で実行されている場合は、関数アプリで[アプリケーション設定](functions-app-settings.md) (サービス接続文字列など) を設定して使用し、これらの設定を実行時に環境変数として公開できます。 

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

### <a name="access-environment-variables-in-code"></a>コードの環境変数にアクセスする

2 つ目と 3 つ目の `context.log()` の呼び出しで示されているように、`process.env` を使用して環境変数としてアプリケーション設定にアクセスします。ここで、`AzureWebJobsStorage` と `WEBSITE_SITE_NAME` の環境変数がログに記録されます。

```javascript
module.exports = async function (context, myTimer) {

    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

## <a name="ecmascript-modules-preview"></a><a name="ecmascript-modules"></a>ECMAScript モジュール (プレビュー)

> [!NOTE]
> 現在、ECMAScript モジュールは Node.js 14 で "*試験段階*" としてラベル付けされているため、Node.js 14 Azure Functions のプレビュー機能として利用できます。 ECMAScript モジュールへの Node.js 14 のサポートが "*安定*" するまで、API または動作が変更される可能性があります。

[ECMAScript モジュール](https://nodejs.org/docs/latest-v14.x/api/esm.html#esm_modules_ecmascript_modules) (ES モジュール) は、Node.js 用の新しい公式標準モジュール システムです。 これまで、この記事のコード サンプルは、CommonJS 構文を使用しています。 Node.js 14 で Azure Functions を実行するときに、ES モジュール構文を使用して関数を記述することを選択できます。

関数で ES モジュールを使用するには、`.mjs` 拡張子を使用するようにファイル名を変更します。 次の *index.mjs* ファイルの例は、ES モジュール構文を使用して `uuid` ライブラリをインポートし、値を返す、HTTP によってトリガーされる関数です。

```js
import { v4 as uuidv4 } from 'uuid';

export default async function (context, req) {
    context.res.body = uuidv4();
};
```

## <a name="configure-function-entry-point"></a>関数のエントリ ポイントを構成する

`function.json` のプロパティ `scriptFile` と `entryPoint` を使用して、エクスポートされた関数の名前と場所を構成できます。 これらのプロパティは、JavaScript がトランスパイルされる場合に重要になることがあります。

### <a name="using-scriptfile"></a>`scriptFile` の使用

既定では、JavaScript 関数は `index.js` から実行されます。これは、対応する `function.json` と同じ親ディレクトリを共有するファイルです。

`scriptFile` を使用すると、次の例のようなフォルダー構造を取得できます。

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

`myNodeFunction` に対する `function.json` には、エクスポートされた関数を実行するファイルを指し示す `scriptFile` プロパティが含まれている必要があります。

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>`entryPoint` の使用

`scriptFile` (または `index.js`) では、関数が発見されて実行されるためには、`module.exports` を使用して関数をエクスポートする必要があります。 既定では、トリガーされたときに実行される関数は、そのファイルからの唯一のエクスポートです (`run` という名前のエクスポート、または `index` という名前のエクスポート)。

これは、次の例で示すように、`entryPoint` の `function.json` を使用して構成することができます。

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

Functions v2.x では、ユーザー関数内の `this` パラメーターがサポートされており、関数のコードは次の例のようになります。

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

この例では、オブジェクトはエクスポートされていますが、実行間で状態が保持される保証はないことに注意することが重要です。

## <a name="local-debugging"></a>ローカル デバッグ

Node.js プロセスは、`--inspect` パラメーターを指定して起動されると、指定されたポートでデバッグ クライアントをリッスンします。 Azure Functions 2.x では、環境変数またはアプリ設定 `languageWorkers:node:arguments = <args>` を追加することで、コードを実行する Node.js プロセスに渡す引数を指定できます。 

ローカルでデバッグするには、[local.settings.json](./functions-develop-local.md#local-settings-file) ファイルの `Values` の下に `"languageWorkers:node:arguments": "--inspect=5858"` を追加し、デバッガーをポート 5858 に接続します。

VS Code を使用してデバッグするときは、プロジェクトの launch.json ファイルの `port` 値を使用して、`--inspect` パラメーターが自動的に追加されます。

バージョン 1.x では、設定 `languageWorkers:node:arguments` は機能しません。 デバッグ ポートは、Azure Functions Core Tools の [`--nodeDebugPort`](./functions-run-local.md#start) パラメーターを使用して選択できます。

## <a name="typescript"></a>TypeScript

Functions ランタイムのバージョン 2.x を対象とする場合、[Visual Studio Code 用の Azure Functions](./create-first-function-cli-typescript.md) と [Azure Functions Core Tools](functions-run-local.md) の両方によって、TypeScript 関数アプリ プロジェクトをサポートするテンプレートを使用して関数アプリを作成することができます。 テンプレートは、`package.json` および `tsconfig.json` プロジェクト ファイルを生成します。これらのプロジェクト ファイルは、これらのツールによって TypeScript コードから JavaScript 関数のトランスパイル、実行、発行を容易にします。

生成された `.funcignore` ファイルは、プロジェクトが Azure に発行されるときに除外するファイルを指定するために使用します。  

TypeScript ファイル (.ts) は、`dist` 出力ディレクトリ内の JavaScript ファイル (.js) にトランスパイルされます。 TypeScript テンプレートは、`function.json` の [`scriptFile` パラメーター](#using-scriptfile)を使用して `dist` フォルダー内の対応する .js ファイルの場所を示します。 出力場所は、`tsconfig.json` ファイルの `outDir` パラメーターを使用することで、テンプレートによって設定されます。 この設定またはフォルダーの名前を変更した場合、ランタイムは実行するコードを見つけることができません。

TypeScript プロジェクトからローカルで開発およびデプロイする方法は、開発ツールによって異なります。

### <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code 用の [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) の拡張機能を使用すると、TypeScript を使用して関数を開発することができます。 Core Tools は Azure Functions の拡張機能の要件です。

Visual Studio Code で TypeScript 関数アプリを作成するには、関数アプリを作成するときに言語として `TypeScript` を選択します。

**F5** を押してアプリをローカルで実行すると、ホスト (func.exe) が初期化される前にトランスパイルが実行されます。 

**[Deploy to function app... (関数アプリにデプロイする...)]** ボタンを使用して関数アプリを Azure にデプロイすると、Azure Functions の拡張機能はまず、TypeScript ソース ファイルから実稼働可能な JavaScript ファイルのビルドを生成します。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Core Tools の使用に関して、TypeScript プロジェクトと JavaScript プロジェクトでは異なる点がいくつかあります。

#### <a name="create-project"></a>Create project

Core Tools を使用して TypeScript 関数アプリ プロジェクトを作成するには、関数アプリを作成するときに TypeScript 言語オプションを指定する必要があります。 これは、次の方法のいずれかで実行できます。

- `func init` コマンドを実行し、言語スタックとして `node` を選択してから `typescript` を選択してください。

- `func init --worker-runtime typescript` コマンドを実行します。

#### <a name="run-local"></a>ローカルで実行する

Core Tools を使用して関数アプリのコードをローカルで実行するには、`func host start` ではなく次のコマンドを使用します。 

```command
npm install
npm start
```

`npm start` コマンドは次のコマンドと同等です。

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Azure に発行する

[`func azure functionapp publish`] コマンドを使用して Azure にデプロイする前に、TypeScript ソース ファイルから JavaScript ファイルの運用対応のビルドを作成します。 

Core Tools を使用し、次のコマンドで TypeScript プロジェクトを準備して発行します。 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

このコマンドでは、`<APP_NAME>` を実際の関数アプリの名前に置き換えます。

## <a name="considerations-for-javascript-functions"></a>JavaScript 関数に関する考慮事項

JavaScript 関数を使用するときは、以下のセクションに記載されている事柄に注意する必要があります。

### <a name="choose-single-vcpu-app-service-plans"></a>シングル vCPU App Service プランを選択する

App Service プランを使用する関数アプリを作成するときは、複数の vCPU を持つプランではなく、シングル vCPU プランを選択することをお勧めします。 今日では、関数を使用して、シングル vCPU VM で JavaScript 関数をより効率的に実行できるようになりました。そのため、大規模な VM を使用しても、期待以上にパフォーマンスが向上することはありません。 必要な場合は、シングル vCPU VM インスタンスを追加することで手動でスケールアウトするか、自動スケーリングを有効にすることができます。 詳細については、「[手動または自動によるインスタンス数のスケール変更](../azure-monitor/autoscale/autoscale-get-started.md?toc=/azure/app-service/toc.json)」を参照してください。

### <a name="cold-start"></a>コールド スタート

サーバーレス ホスティング モデルで Azure 関数を開発するときは、コールド スタートが現実のものになります。 *コールド スタート* とは、非アクティブな期間の後で初めて関数アプリが起動するとき、起動に時間がかかることを意味します。 特に、大きな依存関係ツリーを持つ JavaScript 関数の場合は、コールド スタートが重要になる可能性があります。 コールド スタート プロセスをスピードアップするには、可能な場合、[パッケージ ファイルとして関数を実行](run-functions-from-deployment-package.md)します。 多くの展開方法ではパッケージからの実行モデルが既定で使用されますが、大規模なコールド スタートが発生していて、この方法で実行していない場合は、変更が大きな向上につながる可能性があります。

### <a name="connection-limits"></a>接続の制限

Azure Functions アプリケーションでサービス固有のクライアントを使用する場合は、関数呼び出しごとに新しいクライアントを作成しないでください。 代わりに、グローバル スコープに 1 つの静的クライアントを作成してください。 詳細については、[Azure Functions での接続の管理](manage-connections.md)に関するページを参照してください。

### <a name="use-async-and-await"></a>`async` と `await` を使用する

Azure Functions を JavaScript で記述する場合、`async` と `await` のキーワードを使用してコードを記述することをお勧めします。 コールバックや Promise の `.then` および `.catch` の代わりに `async` と `await` を使用してコードを記述することにより、2 つの一般的な問題を回避できます。
 - [Node.js プロセスをクラッシュさせる](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly)、キャッチされない例外のスロー。他の関数の実行に影響する可能性があります。
 - 適切に待機しない非同期呼び出しによって発生する、context.log からのログの欠落などの予期しない動作。

以下の例では、その 2 番目のパラメーターとしてエラーファースト コールバック関数を使用して非同期メソッド `fs.readFile` が呼び出されます。 このコードは上記の両方の問題の原因となります。 正しいスコープでは明示的にキャッチされない例外によって、プロセス全体がクラッシュします (問題 1)。 コールバック関数のスコープ外で `context.done()` を呼び出すことは、ファイルが読み取られる前に関数呼び出しが終了する可能性があることを意味します (問題 2)。 この例では、`context.done()` の呼び出しが早すぎるため、結果として `Data from file:` で始まるログ エントリが欠落します。

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

`async` および `await` のキーワードを使用すると、これらの両方のエラーを回避しやすくなります。 Node.js のユーティリティ関数 [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) を使用して、エラーファースト コールバック スタイルの関数を、待機可能な関数に変更してください。

次の例では、関数の実行中にスローされたハンドルされない例外により、例外を発生させた個々の呼び出しのみが失敗します。 `await` キーワードは、`readFileAsync` に続くステップが、`readFile` の完了後にのみ実行されることを意味しています。 `async` と `await` を使用することで、`context.done()` コールバックを呼び出す必要もありません。

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    let data;
    try {
        data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>次のステップ

詳細については、次のリソースを参照してください。

+ [Azure Functions のベスト プラクティス](functions-best-practices.md)
+ [Azure Functions 開発者向けリファレンス](functions-reference.md)
+ [Azure Functions triggers and bindings (Azure Functions のトリガーとバインド)](functions-triggers-bindings.md)

[`func azure functionapp publish`]: functions-run-local.md#project-file-deployment
