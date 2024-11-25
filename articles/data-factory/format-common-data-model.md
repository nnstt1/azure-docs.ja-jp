---
title: Common Data Model 形式
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory および Synapse Analytics パイプラインの Common Data Model メタデータ システムを使用してデータを変換します。
author: kromerm
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: makromer
ms.openlocfilehash: 5e97bb5a51bc636cf8f2227473f090e51bb49c1c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787700"
---
# <a name="common-data-model-format-in-azure-data-factory-and-synapse-analytics"></a>Azure Data Factory および Synapse Analytics での Common Data Model 形式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Common Data Model (CDM) メタデータ システムを使用すると、データとその意味をアプリケーションやビジネス プロセス間で簡単に共有できます。 詳しくは、「[Common Data Model](/common-data-model/)」の概要を参照してください。

Azure Data Factory および Synapse パイプラインでは、マッピング データ フローを使用して、[Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2) に格納されている model.json およびマニフェスト形式の両方の CDM エンティティのデータの変換を行うことができます。 また、データをパーティション分割されたフォルダーに CSV または Parquet 形式で格納する CDM エンティティ参照を使用して、データを CDM 形式でシンクすることもできます。 

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

Common Data Model は、マッピング データ フローの[インライン データセット](data-flow-source.md#inline-datasets)として、ソースとシンクの両方として利用できます。

> [!NOTE]
> CDM エンティティを記述する際は、参照として使用する既存の CDM エンティティ定義 (メタデータ スキーマ) が既に定義されている必要があります。 データ フロー シンクでは、その CDM エンティティ ファイルが読み取られ、フィールド マッピング用のシンクにスキーマがインポートされます。

### <a name="source-properties"></a>ソースのプロパティ

次の表に、CDM ソースでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[ソース オプション]** タブで編集できます。

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | 形式は `cdm` である必要があります | はい | `cdm` | format |
| メタデータ形式 | データへのエンティティ参照が置かれている場所。 CDM バージョン 1.0 を使用する場合は、manifest を選択します。 1\.0 より前のバージョンの CDM を使用している場合は、model.json を選択します。 | はい | `'manifest'` または `'model'` | manifestType |
| ルートの場所: コンテナー | CDM フォルダーのコンテナー名 | はい | String | fileSystem |
| ルートの場所: フォルダー パス | CDM フォルダーのルート フォルダーの場所 | はい | String | folderPath |
| マニフェスト ファイル: エンティティのパス | ルート フォルダー内のエンティティのフォルダー パス | no | String | entityPath |
| マニフェスト ファイル: マニフェスト名 | マニフェスト ファイルの名前。 既定値は 'default'  | いいえ | String | manifestName |
| 最終更新日時でフィルター処理 | 最後に変更された日時に基づいてファイルをフィルター処理する場合に選択 | no | Timestamp | modifiedAfter <br> modifiedBefore | 
| スキーマのリンクされたサービス | コーパスが配置されているリンクされたサービス | はい (マニフェストを使用する場合) | `'adlsgen2'` または `'github'` | corpusStore | 
| エンティティ参照コンテナー | その中にコーパスがあるコンテナー | はい (ADLS Gen2 でマニフェストとコーパスを使用する場合) | String | adlsgen2_fileSystem |
| エンティティ参照リポジトリ | GitHub リポジトリ名 | はい (GitHub でマニフェストとコーパスを使用する場合) | String | github_repository |
| エンティティ参照ブランチ | GitHub リポジトリ ブランチ | はい (GitHub でマニフェストとコーパスを使用する場合) | String |  github_branch |
| コーパス フォルダー | コーパスのルートの場所 | はい (マニフェストを使用する場合) | String | corpusPath |
| コーパス エンティティ | エンティティ参照のパス | はい | String | エンティティ |
| [Allow no files found]\(ファイルの未検出を許可\) | true の場合、ファイルが見つからない場合でもエラーはスローされない | no | `true` または `false` | ignoreNoFilesFound |

ソース変換とシンク変換の両方で "エンティティ参照" を選択する際に、以下の 3 つのオプションからエンティティ参照の場所を選択できます。

* ローカル - サービスによって既に使用されているマニフェスト ファイルで定義されているエンティティを使用します
* カスタム - サービスによって使用されているマニフェスト ファイルとは異なるエンティティ マニフェスト ファイルを指定するように求めるメッセージが表示されます
* 標準 - ```Github``` に保持されている CDM エンティティの標準ライブラリにあるエンティティ参照を使用します。

### <a name="sink-settings"></a>シンクの設定

* 記述するエンティティの定義を含む CDM エンティティ参照ファイルをポイントします。

:::image type="content" source="media/data-flow/common-data-model-111.png" alt-text="エンティティの設定":::

* サービスでエンティティの記述に使用する出力ファイルのパーティション パスと形式を定義します。

:::image type="content" source="media/data-flow/common-data-model-222.png" alt-text="エンティティ形式":::

* 出力ファイルの場所とマニフェスト ファイルの場所と名前を設定します。

:::image type="content" source="media/data-flow/common-data-model-333.png" alt-text="CDM の場所":::


#### <a name="import-schema"></a>Import schema

CDM はインライン データセットとしてのみ利用でき、既定では、関連付けられたスキーマはありません。 列メタデータを取得するには、 **[プロジェクション]** タブの **[スキーマのインポート]** ボタンをクリックします。これにより、コーパスによって指定されている列名とデータ型を参照できます。 スキーマをインポートするには、[データ フロー デバッグ セッション](concepts-data-flow-debug-mode.md)をアクティブにする必要があり、既存の CDM エンティティ定義ファイルをポイントする必要があります。

シンク変換でデータ フロー列をエンティティ プロパティにマップする場合は、"マッピング" タブをクリックし、"スキーマのインポート" を選択します。 サービスによって、シンク オプションでポイントしたエンティティ参照が読み取られ、これでターゲットの CDM スキーマにマップできるようになります。

:::image type="content" source="media/data-flow/common-data-model-444.png" alt-text="CDM シンクの設定":::

> [!NOTE]
>  Power BI または Power Platform データフローに由来する model.json ソース タイプを使用すると、ソース変換から "コーパスのパスが null または空である" というエラーが発生することがあります。 原因は、model.json ファイル内のパーティションの場所のパスで書式に問題がある可能性があります。 これを修正するには、次の手順に従います。 

1. model.json ファイルをテキスト エディターで開く
2. partitions.Location プロパティを検索する 
3. "blob.core.windows.net" を "dfs.core.windows.net" に変更する
4. URL の "% 2F" エンコードを "/" に修正する
5. ADF データ フローを使用する場合は、パーティション ファイル パス内の特殊文字を英数字に置き換えるか、Azure Synapse データ フローに切り替える必要があります

### <a name="cdm-source-data-flow-script-example"></a>CDM ソース データ フロー スクリプトの例

```
source(output(
        ProductSizeId as integer,
        ProductColor as integer,
        CustomerId as string,
        Note as string,
        LastModifiedDate as timestamp
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    manifestType: 'manifest',
    manifestName: 'ProductManifest',
    entityPath: 'Product',
    corpusPath: 'Products',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    folderPath: 'ProductData',
    fileSystem: 'data') ~> CDMSource
```

### <a name="sink-properties"></a>シンクのプロパティ

次の表に、CDM シンクでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[設定]** タブで編集できます。

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | 形式は `cdm` である必要があります | はい | `cdm` | format |
| ルートの場所: コンテナー | CDM フォルダーのコンテナー名 | はい | String | fileSystem |
| ルートの場所: フォルダー パス | CDM フォルダーのルート フォルダーの場所 | はい | String | folderPath |
| マニフェスト ファイル: エンティティのパス | ルート フォルダー内のエンティティのフォルダー パス | no | String | entityPath |
| マニフェスト ファイル: マニフェスト名 | マニフェスト ファイルの名前。 既定値は 'default' | いいえ | String | manifestName |
| スキーマのリンクされたサービス | コーパスが配置されているリンクされたサービス | はい | `'adlsgen2'` または `'github'` | corpusStore | 
| エンティティ参照コンテナー | その中にコーパスがあるコンテナー | はい (コーパスが ADLS Gen2 にある場合) | String | adlsgen2_fileSystem |
| エンティティ参照リポジトリ | GitHub リポジトリ名 | はい (コーパスが GitHub にある場合) | String | github_repository |
| エンティティ参照ブランチ | GitHub リポジトリ ブランチ | はい (コーパスが GitHub にある場合) | String |  github_branch |
| コーパス フォルダー | コーパスのルートの場所 | はい | String | corpusPath |
| コーパス エンティティ | エンティティ参照のパス | はい | String | エンティティ |
| パーティションのパス | パーティションが書き込まれる場所 | no | String | partitionPath |
| フォルダーのクリア | 書き込みの前に宛先フォルダーがクリアされるかどうか | no | `true` または `false` | truncate |
| 形式の種類 | Parquet 形式を指定する場合に選択 | no | `parquet` (指定される場合) | subformat |
| 列区切り記号 | DelimitedText に書き込む場合に、列を区切る方法 | はい (DelimitedText に書き込む場合) | String | columnDelimiter |
| 先頭の行を見出しとして使用 | DelimitedText を使用する場合に、列名を見出しとして追加するかどうか | no | `true` または `false` | columnNamesAsHeader |

### <a name="cdm-sink-data-flow-script-example"></a>CDM シンク データ フロー スクリプトの例

関連付けられているデータ フロー スクリプトは次のとおりです。

```
CDMSource sink(allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    entityPath: 'ProductSize',
    manifestName: 'ProductSizeManifest',
    corpusPath: 'Products',
    partitionPath: 'adf',
    folderPath: 'ProductSizeData',
    fileSystem: 'cdm',
    subformat: 'parquet',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CDMSink

```

## <a name="next-steps"></a>次のステップ

マッピング データ フローの[ソース変換](data-flow-source.md)を作成します。
