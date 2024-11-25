---
title: デジタル ツインを管理する
titleSuffix: Azure Digital Twins
description: 個々のツインとリレーションシップを取得、更新、削除する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 9/13/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4be8ef1085d6a940e7f2d95f43a75d1b4e7c11f8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128611693"
---
# <a name="manage-digital-twins"></a>デジタル ツインを管理する

環境内のエンティティは、[デジタル ツイン](concepts-twins-graph.md)で表されます。 デジタル ツインの管理には、作成、変更、削除などが伴います。

この記事では、デジタル ツインの管理に重点を置いて説明します。リレーションシップと[ツイン グラフ](concepts-twins-graph.md)の全体的な操作については、「[ツイン グラフとリレーションシップを管理する](how-to-manage-graph.md)」をご覧ください。

> [!TIP]
> すべての SDK 関数に同期バージョンと非同期バージョンがあります。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [digital-twins-developer-interfaces.md](../../includes/digital-twins-developer-interfaces.md)]

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png" alt-text="サンプルのモデルとツインが表示されている Azure Digital Twins Explorer のスクリーンショット。" lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png":::

## <a name="create-a-digital-twin"></a>デジタル ツインを作成する

ツインを作成するには、次のようにサービス クライアントで `CreateOrReplaceDigitalTwinAsync()` メソッドを使用します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwinCall":::

デジタル ツインを作成するには、以下を指定する必要があります。
* デジタル ツインに割り当てる ID 値 (ツインの作成時にその ID を定義する)
* 使用する[モデル](concepts-models.md)
* 任意のツイン データの必要な初期化。次を含みます
    - プロパティ (初期化オプション): 必要に応じて、デジタル ツインのプロパティの初期値を設定できます。 プロパティはオプションとして扱われ、後で設定できますが、**設定されるまではツインの一部として表示されません**。
    - テレメトリ (初期化のために推奨): ツインでテレメトリ フィールドの初期値を設定することもできます。 テレメトリの初期化は必須ではありませんが、テレメトリ フィールドは、設定されるまでツインの一部としては表示されません。 つまり、**最初に初期化されていない限り、ツインのテレメトリ値を編集することはできません**。
    - コンポーネント (ツインに存在する場合に初期化する必要があります): ツインに[コンポーネント](concepts-models.md#elements-of-a-model)が含まれている場合は、ツインの作成時に初期化する必要があります。 空のオブジェクトにすることができますが、コンポーネント自体は存在する必要があります。
    
モデルと初期プロパティ値は、`initData` パラメーターによって提供されます。これは、関連データを含む JSON 文字列です。 このオブジェクトを構造化する方法の詳細については、次のセクションに進んでください。

> [!TIP]
> ツインを作成または更新した後、変更が[クエリ](how-to-query-graph.md)に反映されるまでに最大 10 秒の待機時間が発生する可能性があります。 `GetDigitalTwin` API ([この記事で後ほど](#get-data-for-a-digital-twin)説明します) ではこの遅延が発生しないため、迅速な応答が必要な場合は、クエリの代わりに API 呼び出しを使用して、新しく作成されたツインを確認してください。 

### <a name="initialize-model-and-properties"></a>モデルとプロパティを初期化する

ツインの作成時、ツインのプロパティを初期化できます。 

ツイン作成 API は、ツイン プロパティの有効な JSON 記述にシリアル化されるオブジェクトを受け入れます。 ツインの JSON 形式の説明については、[デジタル ツインとツイン グラフ](concepts-twins-graph.md)に関する記事をご覧ください。 

まず、ツインとそのプロパティ データを表すデータ オブジェクトを作成することができます。 パラメーター オブジェクトは手動で作成することも、用意されているヘルパー クラスを使用して作成することもできます。 それぞれの例を以下に示します。

#### <a name="create-twins-using-manually-created-data"></a>手動で作成したデータを使用してツインを作成する

カスタム ヘルパー クラスを使用しない場合は、`Dictionary<string, object>` でツインのプロパティを表すことができます。ここで、`string` はプロパティの名前であり、`object` はプロパティとその値を表すオブジェクトです。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

#### <a name="create-twins-with-the-helper-class"></a>ヘルパー クラスを使用してツインを作成する

`BasicDigitalTwin` のヘルパー クラスを使用すると、"ツイン" オブジェクトにプロパティ フィールドを直接的に格納できます。 `Dictionary<string, object>` を使用してプロパティのリストを作成することもできます。それは、`CustomProperties` としてツイン オブジェクトに直接追加できます。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

>[!NOTE]
> `BasicDigitalTwin` オブジェクトには、`Id` フィールドが付属しています。 このフィールドは空のままとすることができますが、ID 値を追加する場合は、それが、`CreateOrReplaceDigitalTwinAsync()` 呼び出しに渡される ID パラメーターと一致する必要があります。 次に例を示します。
>
>```csharp
>twin.Id = "myRoomId";
>```

## <a name="get-data-for-a-digital-twin"></a>デジタル ツインのデータを取得する

次のように `GetDigitalTwin()` メソッドを呼び出すことで、デジタル ツインの詳細にアクセスすることができます。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwinCall":::

この呼び出しからは、ツイン データが `BasicDigitalTwin` のような厳密に型指定されたオブジェクト型として返されます。 `BasicDigitalTwin` は、SDK に含まれているシリアル化ヘルパー クラスであり、ツインのコア メタデータとプロパティが解析済みの形で返されます。 `System.Text.Json` や `Newtonsoft.Json` といった任意の JSON ライブラリを使用して、ツイン データをいつでも逆シリアル化できます。 ただし、ツインへの基本的なアクセスについては、ヘルパー クラスを使用すると便利です。

> [!NOTE]
> `BasicDigitalTwin` では `System.Text.Json` 属性が使用されます。 `BasicDigitalTwin` を [DigitalTwinsClient](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient?view=azure-dotnet&preserve-view=true) で使用するためには、既定のコンストラクターを使用してクライアントを初期化する必要があります。または、シリアライザー オプションをカスタマイズする場合は、[JsonObjectSerializer](/dotnet/api/azure.core.serialization.jsonobjectserializer?view=azure-dotnet&preserve-view=true) を使用します。

また、`BasicDigitalTwin` ヘルパー クラスを使用すると、ツインで定義されたプロパティに `Dictionary<string, object>` を介してアクセスできます。 ツインのプロパティを一覧表示するには、次のコードを使用します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin" highlight="2":::

`GetDigitalTwin()` メソッドを使用してツインを取得すると、少なくとも 1 回は設定されたプロパティだけが返されます。

>[!TIP]
>ツインの `displayName` はモデル メタデータの一部であるため、ツイン インスタンスのデータを取得するときには表示されません。 この値を表示するには、[モデルから取得する](how-to-manage-model.md#retrieve-models)ことができます。

1 つの API 呼び出しを使用して複数のツインを取得するには、「[ツイン グラフにクエリを実行する](how-to-query-graph.md)」のクエリ API の例を参照してください。

Moon を定義する次のモデル ([Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL) で記述) について考えてみましょう。

:::code language="json" source="~/digital-twins-docs-samples/models/Moon.json":::

Moon 型ツインで `object result = await client.GetDigitalTwinAsync("my-moon");` を呼び出すと、結果は次のようになります。

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:example:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

デジタル ツインの定義済みプロパティは、デジタル ツインの最上位プロパティとして返されます。 DTDL 定義に含まれていないメタデータやシステム情報は、`$` プレフィックス付きで返されます。 メタデータ プロパティには以下の値が含まれます。
* `$dtId`: この Azure Digital Twins インスタンスのデジタル ツインの ID
* `$etag`: Web サーバーによって割り当てられた標準 HTTP フィールド。 これはツインが更新されるたびに新しい値に更新されます。これは、前回のチェックからサーバー上でツインのデータが更新されているかどうかを判断するのに役立ちます。 `If-Match` を使用すると、 エンティティの etag が指定された etag と一致する場合にのみ完了する更新と削除を実行できます。 これらの操作の詳細については、 [DigitalTwins Update](/rest/api/digital-twins/dataplane/twins/digitaltwins_update) と [DigitalTwins Delete](/rest/api/digital-twins/dataplane/twins/digitaltwins_delete)のドキュメントを参照してください。
* `$metadata`: 次を含むその他のプロパティのセット:
  - デジタル ツインのモデルの DTMI。
  - 書き込み可能な各プロパティの同期の状態。 これは、サービスとデバイスの状態が異なる可能性がある場合 (デバイスがオフラインの場合など) に、デバイスで最も役立ちます。 現在、このプロパティは IoT Hub に接続されている物理デバイスにのみ適用されます。 メタデータ セクションのデータにより、プロパティの完全な状態と、最終変更のタイムスタンプを把握できます。 同期の状態の詳細については、デバイスの状態の同期に関する[こちらの IoT Hub チュートリアル](../iot-hub/tutorial-device-twins.md)をご覧ください。
  - IoT Hub や Azure Digital Twins などのサービス固有のメタデータ。 

`BasicDigitalTwin` などのシリアル化ヘルパー クラスの詳細については、「[Azure Digital Twins API と SDK](concepts-apis-sdks.md#serialization-helpers)」で参照してください。

## <a name="view-all-digital-twins"></a>すべてのデジタル ツインを表示する

インスタンス内のすべてのデジタル ツインを表示するには、[クエリ](how-to-query-graph.md)を使用します。 クエリは、[Query API](/rest/api/digital-twins/dataplane/query) または [CLI コマンド](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_query)を使用して実行できます。

次に示すのは、インスタンス内のすべてのデジタル ツインの一覧を返す基本的なクエリの本文です。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="GetAllTwins":::

## <a name="update-a-digital-twin"></a>デジタル ツインを更新する

デジタル ツインのプロパティを更新するには、置き換える情報を [JSON Patch](http://jsonpatch.com/) 形式で記述します。 これにより、一度に複数のプロパティを置き換えることができます。 次に、その JSON Patch ドキュメントを `UpdateDigitalTwin()` メソッドに渡します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="UpdateTwinCall":::

パッチの呼び出しでは、1 つのツインのプロパティを必要な数だけ (それらをすべてであっても) 更新することができます。 複数のツイン間でプロパティを更新する必要がある場合は、ツインごとに個別の更新呼び出しが必要になります。

> [!TIP]
> ツインを作成または更新した後、変更が[クエリ](how-to-query-graph.md)に反映されるまでに最大 10 秒の待機時間が発生する可能性があります。 `GetDigitalTwin` API ([この記事の前の方で](#get-data-for-a-digital-twin)説明しました) ではこの遅延が発生しないため、迅速な応答が必要な場合は、クエリの代わりに API 呼び出しを使用して、新しく更新されたツインを確認してください。 

JSON Patch コードの例を次に示します。 このドキュメントでは、適用先のデジタル ツインの *mass* および *radius* プロパティ値を置き換えます。

:::code language="json" source="~/digital-twins-docs-samples/models/patch.json":::

>[!NOTE]
> この例では、既存のプロパティの値を置き換える JSON Patch `replace` 操作を示しています。 `add` と `remove` を含めて、使用可能な JSON Patch 操作の詳細なリストは、「[JSON Patch の操作](http://jsonpatch.com/#operations)」を参照してください。 

.NET SDK を使用してコードプロジェクトからツインを更新する場合は、Azure .NET SDK の [Jsonpatchdocument](/dotnet/api/azure.jsonpatchdocument?view=azure-dotnet&preserve-view=true) を使用して JSON 修正プログラムを作成できます。 次に例を示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

### <a name="update-sub-properties-in-digital-twin-components"></a>デジタル ツイン コンポーネントのサブプロパティを更新する

モデルには、他のモデルで構成できるコンポーネントが含まれている場合があることに注意してください。 

デジタル ツインのコンポーネントのプロパティにパッチを適用するには、JSON Patch で path 構文を使用することができます。

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component.json":::

### <a name="update-sub-properties-in-object-type-properties"></a>オブジェクト型プロパティのサブプロパティを更新する

モデルには、オブジェクト型のプロパティを含めることができます。 これらのオブジェクトには独自のプロパティが含まれる場合があります。また、オブジェクト型プロパティに属するサブプロパティのいずれかの更新が必要になる場合もあります。 このプロセスは、[コンポーネント のサブプロパティを更新する](#update-sub-properties-in-digital-twin-components)プロセスに似ていますが、追加の手順が必要な場合があります。 

オブジェクト型プロパティ `ObjectProperty` を持つモデルについて検討してみましょう。 `ObjectProperty` には `StringSubProperty` という名前の文字列プロパティがあります。

このモデルを使用してツインを作成する場合、その時点で `ObjectProperty` をインスタンス化する必要はありません。 ツインの作成中にオブジェクト プロパティがインスタンス化されない場合、パッチ操作用の `ObjectProperty` とその `StringSubProperty` にアクセスするための既定のパスは作成されません。 プロパティを更新する前に、`ObjectProperty` へのパスを追加する必要があります。

これは、次のように JSON Patch `add` 操作 を使用して実行できます。

:::code language="json" source="~/digital-twins-docs-samples/models/patch-object-sub-property-1.json":::

>[!NOTE]
> `ObjectProperty` に複数のプロパティがある場合は、更新するものが 1 つだけのときでも、この操作の `value` フィールドにそれらすべてを含める必要があります。
> ```json
>... "value": {"StringSubProperty":"<string-value>", "Property2":"<property2-value>", ...}
>```


これが 1 回実行された後は、`StringSubProperty` へのパスが存在するため、以降は通常の `replace` 操作で直接更新できます。

:::code language="json" source="~/digital-twins-docs-samples/models/patch-object-sub-property-2.json":::

最初の手順は、ツインの作成時に `ObjectProperty` がインスタンス化されている場合には必要ありませんが、オブジェクト プロパティが最初にインスタンス化されたかどうかを常に確認できるとは限らないので、サブプロパティを初めて更新するごとに最初の手順を使用することをお勧めします。

### <a name="update-a-digital-twins-model"></a>デジタル ツインのモデルを更新する

`UpdateDigitalTwin()` 関数を使用して、デジタル ツインを別のモデルに移行することもできます。 

たとえば、デジタル ツインのメタデータの `$model` フィールドを置き換える、次の JSON Patch ドキュメントについて考えてみましょう。

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-1.json":::

この操作は、パッチによって変更されるデジタル ツインが新しいモデルに適合する場合にのみ成功します。 

次の例を確認してください。
1. foo_old というモデルを使用するデジタル ツインがあるとします。 foo_old では、必須プロパティの *mass* を定義しています。
2. 新しいモデル foo_new では、mass プロパティを定義し、新しい必須プロパティの *temperature* を追加します。
3. パッチの適用後、このデジタル ツインには mass と temperature の両方のプロパティが含まれている必要があります。 

この場合のパッチでは、次のように、モデルとツインの temperature プロパティを両方とも更新する必要があります。

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-2.json":::

### <a name="handle-conflicting-update-calls"></a>競合する更新呼び出しを処理する

Azure Digital Twins では、すべての受信要求が確実に 1 つずつ処理されます。 つまり、複数の関数が同時にツイン上の同じプロパティを更新しようとする場合でも、競合を処理するための明示的なロック コードを記述する **必要はありません**。

この動作はツインごとに行われます。 

例として、これら 3 つの呼び出しが同時に到着するシナリオを考えてみましょう。 
*   Twin1 でのプロパティ A の書き込み
*   Twin1 でのプロパティ B の書き込み
*   Twin2 でのプロパティ A の書き込み

Twin1 を変更する 2 つの呼び出しが 1 つずつ実行され、変更のたびに変更メッセージが生成されます。 Twin2 を変更する呼び出しは、到着したらすぐに、競合なしで同時に実行できます。

## <a name="delete-a-digital-twin"></a>デジタル ツインを削除する

`DeleteDigitalTwin()` メソッドを使用してツインを削除することができます。 ただし、ツインを削除できるのは、ツインにリレーションシップがない場合だけです。 そのため、最初にツインの受信と送信のリレーションシップを削除します。

ここに、ツインとそのリレーションシップを削除するコードの例を示します。 `DeleteDigitalTwin` SDK の呼び出しが強調表示されており、この例の幅広いコンテキストでの位置が明確になっています。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="DeleteTwin" highlight="7":::

### <a name="delete-all-digital-twins"></a>すべてのデジタル ツインを削除する

一度にすべてのツインを削除する方法の例については、[サンプル クライアント アプリを使用した基本事項の確認](tutorial-command-line-app.md)に関するページで使用されているサンプル アプリをダウンロードしてください。 *CommandLoop.cs* ファイルでは、`CommandDeleteAllTwins()` 関数でこれを実行します。

## <a name="runnable-digital-twin-code-sample"></a>実行可能なデジタル ツインのコード サンプル

次の実行可能なコード サンプルを使用してツインを作成し、その詳細を更新して、ツインを削除することができます。 

### <a name="set-up-sample-project-files"></a>サンプル プロジェクト ファイルの設定

このスニペットでは、サンプル モデル定義 [Room.json](https://raw.githubusercontent.com/Azure-Samples/digital-twins-samples/master/AdtSampleApp/SampleClientApp/Models/Room.json) を使用します。 **モデル ファイルをダウンロード** してコードで使用できるようにするには、このリンクを使用して GitHub のファイルに直接アクセスします。 次に、画面上の任意の場所を右クリックし、ブラウザーの右クリック メニューから **[名前を付けて保存]** を選択して、[名前を付けて保存] のウィンドウでファイルを **Room.json** で保存します。

次に、Visual Studio または任意のエディターで、**新しいコンソール アプリ プロジェクト** を作成します。

次に、実行可能なサンプルの **次のコードをプロジェクトにコピー** します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs":::

### <a name="configure-project"></a>プロジェクトを構成する

次に、以下の手順を実行してプロジェクト コードを構成します。
1. 以前ダウンロードした **Room.json** ファイルをプロジェクトに追加し、コード内部の `<path-to>` プレースホルダーを置き換えて、プログラムに検索する場所を指示します。
2. プレースホルダー `<your-instance-hostname>` を Azure Digital Twins インスタンスのホスト名に置き換えます。
3. Azure Digital Twins を操作するために必要な 2 つの依存関係をプロジェクトに追加します。 1 つ目は [.NET 用 Azure Digital Twins SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 用のパッケージであり、2 つ目では Azure に対する認証に役立つツールが提供されます。

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

サンプルを直接実行する場合は、ローカルの資格情報も設定する必要があります。 次のセクションでは、これについて説明します。
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>サンプルを実行する

セットアップが完了したので、これでサンプル コード プロジェクトを実行できます。

上記のプログラムのコンソール出力は次のようになります。 

:::image type="content" source="./media/how-to-manage-twin/console-output-manage-twins.png" alt-text="ツインが作成、更新、削除されたことを示すコンソール出力のスクリーンショット。" lightbox="./media/how-to-manage-twin/console-output-manage-twins.png":::

## <a name="next-steps"></a>次のステップ

デジタル ツイン間のリレーションシップを作成および管理する方法を確認します。
* [ツイン グラフとリレーションシップを管理する](how-to-manage-graph.md)