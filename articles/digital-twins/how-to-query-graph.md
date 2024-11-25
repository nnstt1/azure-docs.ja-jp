---
title: ツイン グラフにクエリを実行する
titleSuffix: Azure Digital Twins
description: Azure Digital Twins ツイン グラフに対してクエリを実行する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 9d8a72f4457c6759dc93fcdeae03abd1d4c8b168
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2021
ms.locfileid: "114729833"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Azure Digital Twins ツイン グラフに対してクエリを実行する

この記事では、クエリの例と、**Azure Digital Twins クエリ言語** を使用して [ツイン グラフ](concepts-twins-graph.md)で情報をクエリする手順について説明します。 (クエリ言語の概要については、[クエリ言語](concepts-query-language.md)に関する記事を参照してください。)

この記事では、デジタル ツインのクエリ言語の構造および共通のクエリ操作を示すサンプル クエリについて説明します。 また、Azure Digital Twins の [Query API](/rest/api/digital-twins/dataplane/query) または [SDK](concepts-apis-sdks.md#overview-data-plane-apis) を使用して、クエリを作成した後にそれらを実行する方法について説明します。

> [!NOTE]
> 次のサンプル クエリを、API または SDK の呼び出しで実行している場合は、クエリ テキストを 1 行にまとめる必要があります。

[!INCLUDE [digital-twins-query-reference.md](../../includes/digital-twins-query-reference.md)]

## <a name="show-all-digital-twins"></a>すべてのデジタル ツインを表示する

次に示すのは、インスタンス内のすべてのデジタル ツインの一覧を返す基本的なクエリです。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="GetAllTwins":::

## <a name="query-by-property"></a>プロパティで照会

**プロパティ** (ID とメタデータを含む) を指定してデジタル ツインを取得します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByProperty1":::

上記のクエリに示されているように、デジタル ツインの ID は、メタデータ フィールド `$dtId` を使用してクエリが実行されます。

>[!TIP]
> Cloud Shell を使用して、`$` で始まるメタデータ フィールドでクエリを実行する場合は、バッククォートで `$` をエスケープして、それが変数ではなく、クエリ テキスト内のリテラルとして使用する必要があることを Cloud Shell に通知する必要があります。

**特定のプロパティが定義されているかどうか** に基づいて Twins を取得することもできます。 次は、*Location* プロパティが定義されているツインを取得するクエリです。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByProperty2":::

「[デジタル ツインにタグを追加する](how-to-use-tags.md)」で説明されているように、このクエリは *tag* プロパティを使用してツインを取得する場合に役立ちます。 次は、*red* のタグが付いているツインをすべて取得するクエリです。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryMarkerTags1":::

**プロパティの型** に基づいてツインを取得することもできます。 次は、*Temperature* プロパティが数字であるツインを取得するクエリです。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByProperty3":::

>[!TIP]
> プロパティの型が `Map` の場合は、次のように、クエリでマップのキーと値を直接使用できます。
> :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByProperty4":::

## <a name="query-by-model"></a>モデルでクエリを実行する

`IS_OF_MODEL` 演算子を使用すると、ツインの[モデル](concepts-models.md)に基づいてフィルター処理できます。

[継承](concepts-models.md#model-inheritance) とモデルの [バージョン管理](how-to-manage-model.md#update-models)を調べた上で、指定されたツインが次のいずれかの条件を満たしている場合に、そのツインに対して **true** として評価されます。

* `IS_OF_MODEL()` に指定されたモデルがツインに直接実装され、ツイン上のモデルのバージョン番号が、指定されたモデルのバージョン番号 "*以上*" である。
* `IS_OF_MODEL()` に指定されたモデルを "*拡張する*" モデルがツインに実装され、ツインの拡張モデルのバージョン番号が、指定されたモデルのバージョン番号 "*以上*" である。

したがって、たとえば、モデル `dtmi:example:widget;4` のツインに対してクエリを実行した場合、このクエリから返されるのは、**バージョン 4 以上** の **ウィジェット** モデルに基づくすべてのツインと、さらに **バージョン 4 以上** の、**ウィジェットから継承した任意のモデル** に基づくツインとなります。

`IS_OF_MODEL` にはいくつかの異なるパラメーターを指定することができます。このセクションの残りの部分では、その各種のオーバーロード オプションのみについて説明します。

`IS_OF_MODEL` の最も簡単な使用法では、`IS_OF_MODEL(twinTypeName)` のように `twinTypeName` パラメーターのみを受け取ります。
このパラメーターで値を渡すクエリの例を次に示します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByModel1":::

(`JOIN` が使用されている場合のように) 複数のツイン コレクションがある場合に検索対象を指定するには、`IS_OF_MODEL(twinCollection, twinTypeName)` のように `twinCollection` パラメーターを追加します。
このパラメーターに値を追加するクエリの例を次に示します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByModel2":::

完全一致を行うには、`IS_OF_MODEL(twinTypeName, exact)` のように `exact` パラメーターを追加します。
このパラメーターに値を追加するクエリの例を次に示します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByModel3":::

また、`IS_OF_MODEL(twinCollection, twinTypeName, exact)` のように 3 つの引数すべてを渡すこともできます。
3 つのパラメーターすべての値を指定するクエリの例を次に示します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByModel4":::

## <a name="query-by-relationship"></a>リレーションシップによるクエリ

デジタル ツインの **リレーションシップ** に基づいてクエリを実行する場合、Azure Digital Twins クエリ言語には特殊な構文があります。

リレーションシップは `FROM` 句のクエリ スコープにプルされます。 "従来の" SQL 型言語とは異なり、`FROM` 句の各式はテーブルではありません。そうではなく、`FROM` 句は、エンティティ間のリレーションシップのトラバーサルを表します。 リレーションシップをトラバーサルするために、Azure Digital Twins ではカスタム バージョンの `JOIN` を使用します。

Azure Digital Twins [モデル](concepts-models.md)の機能を使用すると、リレーションシップがツインとは無関係に存在しないことを思い出してください。これは、リレーションシップを個別に照会することができず、ツインに関連付けられている必要があることを意味します。
この事実を反映するために、キーワード `RELATED` を `JOIN` 句に使用して、ツイン コレクションからの特定の種類のリレーションシップのセットを取得します。 次に、クエリでは、`WHERE` 句でフィルター処理して、リレーションシップ クエリで使用する特定のツインを指定する必要があります (ツインの `$dtId` 値を使用)。

次のセクションでは、このような例をいくつか示します。

### <a name="basic-relationship-query"></a>基本的なリレーションシップ クエリ

リレーションシップベースのクエリの例を次に示します。 このコード スニペットを実行すると、*ID* プロパティが 'ABC' であるすべてのデジタル ツインと、*contains* リレーションシップを介してこれらのデジタル ツインに関連するすべてのデジタル ツインが選択されます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByRelationship1":::

リレーションシップの種類 (上の例の `contains`) は、[DTDL 定義](concepts-models.md#basic-relationship-example)内のリレーションシップの **name** フィールドを使用して示されます。

> [!NOTE]
> 開発者は、この `JOIN` を `WHERE` 句のキー値と関連付ける (または `JOIN` 定義を使用してインラインでキー値を指定する) 必要はありません。 リレーションシップのプロパティ自体でターゲット エンティティが識別されるため、この相関関係はシステムによって自動的に計算されます。

### <a name="query-by-the-source-or-target-of-a-relationship"></a>リレーションシップのソースまたはターゲットによるクエリ

リレーションシップのクエリ構造を使用すると、リレーションシップのソースまたはターゲットであるデジタル ツインを特定できます。

たとえば、ソース ツインから開始し、そのリレーションシップに従ってリレーションシップのターゲット ツインを見つけることができます。 次に、ツイン source-twin からの *feeds* リレーションシップのターゲット ツインを見つけるクエリの例を示します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByRelationshipSource":::

また、リレーションシップのターゲットから開始し、リレーションシップをトレースしてソース ツインを見つけることもできます。 次に、ツイン target-twin への *feeds* リレーションシップのソース ツインを見つけるクエリの例を示します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByRelationshipTarget":::

### <a name="query-the-properties-of-a-relationship"></a>リレーションシップのプロパティのクエリを実行する

デジタル ツインに DTDL を介して記述されるプロパティが存在するのと同様に、リレーションシップにプロパティを含めることもできます。 **リレーションシップのプロパティに基づいて** ツインにクエリを実行できます。
Azure Digital Twins ストア言語を使用すると、`JOIN` 句内のリレーションシップにエイリアスを割り当てることで、リレーションシップのフィルター処理とプロジェクションを行うことができます。

例として、*reportedCondition* プロパティがある *servicedBy* リレーションシップを考えてみます。 次のクエリでは、プロパティを参照するために、このリレーションシップには 'R' という別名が与えられています。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByRelationship2":::

上記の例では、*reportedCondition* が *servicedBy* リレーションシップ自体のプロパティであることに注意してください (*servicedBy* リレーションシップを持つ何らかのデジタル ツインではありません)。

### <a name="query-with-multiple-joins"></a>複数の JOIN を使用したクエリ

1 つのクエリで最大 5 つの `JOIN` がサポートされているため、複数レベルのリレーションシップを一度に走査できます。 

複数レベルのリレーションシップをクエリするには、1 つの `FROM` ステートメントに続けて N 個の `JOIN` ステートメントを使用します。ここで、`JOIN` ステートメントは前の `FROM` または `JOIN` ステートメントの結果に関するリレーションシップを表します。

次に、複数結合のクエリの例を示します。このクエリでは、ルーム 1 と 2 のライト パネルに含まれるすべての電球が取得されます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByRelationship3":::

## <a name="count-items"></a>項目をカウントする

`Select COUNT` 句を使用して、結果セット内の項目の数をカウントできます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="SelectCount1":::

`WHERE` 句を追加することで、特定の条件に一致する項目の数をカウントします。 ツイン モデルの種類に基づいて適用されたフィルターを使用してカウントする例をいくつか次に示します (この構文の詳細については、後述する [「モデルでクエリを実行する」](#query-by-model) を参照してください)。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="SelectCount2":::

また、`COUNT` を `JOIN` 句と共に使用することもできます。 次に、ルーム 1 と 2 のライト パネルに含まれるすべての電球をカウントするクエリを示します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="SelectCount3":::

## <a name="filter-results-select-top-items"></a>結果のフィルター処理: 上位項目を選択する

`Select TOP` 句を使用して、1 つのクエリで複数の "上位" 項目を選択できます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="SelectTop":::

## <a name="filter-results-specify-return-set-with-projections"></a>結果のフィルター処理: プロジェクションを使用して戻り値のセットを指定する

`SELECT` ステートメントでプロジェクションを使用すると、クエリによって返される列を選択できます。 プロジェクションは、プリミティブおよび複合プロパティの両方でサポートされるようになりました。 Azure Digital Twins によるプロジェクションの詳細については、[SELECT 句のリファレンス ドキュメント](reference-query-clause-select.md#select-columns-with-projections)を参照してください。

プロジェクションを使用してツインとリレーションシップを返すクエリの例を次に示します。 次のクエリでは、ID が "*ABC*" の "Factory" が "*Factory.customer*" のリレーションシップを介して "Consumer" と関連付けられており、そのリレーションシップが "*Edge*" として表されるシナリオから、"Consumer"、"Factory"、"Edge" がプロジェクションされています。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="Projections1":::

また、プロジェクションを使用してツインのプロパティを返すこともできます。 以下のクエリでは、"Factory.customer" のリレーションシップを介して、ID が "ABC" の "*Factory*" に関連付けられている "*Consumers*" の "*Name*" プロパティがプロジェクションされています。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="Projections2":::

また、プロジェクションを使用してリレーションシップのプロパティを返すこともできます。 前述の例と同様に、以下のクエリでは、"Factory.customer" のリレーションシップを介して、ID が "ABC" の "*Factory*" に関連付けられている "*Consumers*" の "*Name*" プロパティがプロジェクションされていますが、今回は、そのリレーションシップの 2 つのプロパティである *prop1* および *prop2* も返されます。 これは、リレーションシップに "*Edge*" という名前を付け、そのプロパティを収集することで行います。  

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="Projections3":::

また、別名を使用して、プロジェクションによるクエリを簡略化することもできます。

次のクエリを実行すると、前述の例と同じ操作が行われますが、プロパティ名には別名の `consumerName`、`first`、`second`、および `factoryArea` が使用されています。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="Projections4":::

これは、上記と同じセットに対してクエリを実行する類似のクエリですが、*Consumer.name* プロパティのみが `consumerName` としてプロジェクションされ、完全な Factory はツインとしてプロジェクションされます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="Projections5":::

## <a name="build-efficient-queries-with-the-in-operator"></a>IN 演算子を使用して効率的なクエリを作成する

必要なクエリ数を大幅に減らすには、ツインの配列を構築し、`IN` 演算子を使用してクエリを実行します。 

たとえば、"Buildings"(建物) に "Floors" (フロア) が含まれ、"Floors" に "Rooms" (部屋) が含まれるシナリオを考えてみます。 建物内の暑い部屋を見つけるには、1 つの方法として、次の手順に従います。

1. `contains` のリレーションシップに基づいて、建物内のフロアを見つけます。

    :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="INOperatorWithout":::

2. 部屋を見つけるには、フロアを 1 つずつ検討し、`JOIN` クエリを実行してそれぞれの部屋を見つけるのではなく、建物内のフロアのコレクション (以下のクエリでは Floor という名前) を使用してクエリを実行することができます。

    クライアント アプリ:
    
    ```csharp
    var floors = "['floor1','floor2', ..'floorn']"; 
    ```
    
    クエリ:
    
    :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="INOperatorWith":::

## <a name="other-compound-query-examples"></a>その他の複合クエリの例

結合演算子を使用して上記のクエリ タイプを **組み合わせ**、1 つのクエリに含める詳細を増やすことができます。 次に、複数のツイン記述子の種類に対して一度にクエリを実行する複合クエリのその他の例をいくつか挙げます。

* Room 123 に与えられているデバイスの中から、Operator の役割を担う MxChip デバイスが返されます
    :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="OtherExamples1":::
* ID が *id1* の別のツインとの間に *Contains* という名前のリレーションシップがあるツインを取得します
    :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="OtherExamples2":::
* floor11 によって包含されるこの部屋モデルのすべての部屋を取得します
    :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="OtherExamples3":::

## <a name="run-queries-with-the-api"></a>API を使用してクエリを実行する

クエリ文字列を決定したら、[Query API](/rest/api/digital-twins/dataplane/query) を呼び出して実行します。

API を直接呼び出すか、Azure Digital Twins で使用可能な [SDK](concepts-apis-sdks.md#overview-data-plane-apis) のいずれかを使用することができます。

次のコード スニペットは、クライアント アプリからの [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 呼び出しを示しています。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="RunQuery":::

この呼び出しで使用されるクエリでは、デジタル ツインの一覧が返されます。これは、上記の例で [BasicDigitalTwin](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin?view=azure-dotnet&preserve-view=true) オブジェクトによって表されています。 各クエリのデータの戻り値の型は、`SELECT` ステートメントで指定する項目によって異なります。
* `SELECT * FROM ...` で始まるクエリでは、デジタル ツイン (`BasicDigitalTwin` オブジェクトとして、または独自に作成した他のカスタム デジタル ツインの種類としてシリアル化可能) の一覧が返されます。
* `SELECT <A>, <B>, <C> FROM ...` の形式で始まるクエリでは、キー `<A>`、`<B>`、`<C>` を含むディクショナリが返されます。
* カスタム データを返すように、他の形式の `SELECT` ステートメントを作成できます。 独自のクラスを作成して、カスタマイズされた結果セットが処理されるようにすることを検討してください。 

### <a name="query-with-paging"></a>ページングを使用したクエリ

クエリ呼び出しはページングをサポートしています。 エラー処理とページングを含むクエリ結果の種類として `BasicDigitalTwin` を使用した完全な例を以下に示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

## <a name="next-steps"></a>次のステップ

この記事のクエリの実行に使用される Query API を含め、詳細については、[Azure Digital Twins API と SDK](concepts-apis-sdks.md) に関する記事を参照してください。
