---
title: サーバーレス SQL プールからクエリの結果を格納する
description: この記事では、サーバーレス SQL プールを使用してクエリの結果をストレージに格納する方法を学習します。
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: ba93588dc686a57c05371b86d8f9e058f2bd2cf2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131460035"
---
# <a name="store-query-results-to-storage-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics のサーバーレス SQL プールを使用してクエリの結果をストレージに格納する

この記事では、サーバーレス SQL プールを使用してクエリの結果をストレージに格納する方法を学習します。

## <a name="prerequisites"></a>前提条件

最初の手順として、クエリを実行する **データベースを作成** します。 次に、そのデータベースで[セットアップ スクリプト](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)を実行して、オブジェクトを初期化します。 このセットアップ スクリプトにより、データ ソース、データベース スコープの資格情報、これらのサンプルでデータの読み取りに使用される外部ファイル形式が作成されます。

この記事の手順に従って、データ ソース、データベース スコープの資格情報、出力ストレージへのデータの書き込みに使用される外部ファイル形式を作成してください。

## <a name="create-external-table-as-select"></a>CREATE EXTERNAL TABLE AS SELECT

CREATE EXTERNAL TABLE AS SELECT (CETAS) ステートメントを使用して、クエリ結果をストレージに格納することができます。

> [!NOTE]
> クエリの最初の行 ([mydbname]) は、自分で作成したデータベースを使用するように変更してください。

```sql
USE [mydbname];
GO

CREATE DATABASE SCOPED CREDENTIAL [SasTokenWrite]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://<storage account name>.blob.core.windows.net/csv', CREDENTIAL = [SasTokenWrite]
);
GO

CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH (
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
);
GO

CREATE EXTERNAL TABLE [dbo].[PopulationCETAS] WITH (
        LOCATION = 'populationParquet/',
        DATA_SOURCE = [MyDataSource],
        FILE_FORMAT = [ParquetFF]
) AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

> [!NOTE]
> このスクリプトを変更し、ターゲットの場所を変更して再度実行する必要があります。 データが既に存在する場所に外部テーブルを作成することはできません。

## <a name="use-the-external-table"></a>外部テーブルを使用する

CETAS で作成した外部テーブルは、通常の外部テーブルと同じように使用できます。

> [!NOTE]
> クエリの最初の行 ([mydbname]) は、自分で作成したデータベースを使用するように変更してください。

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM PopulationCETAS
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="remarks"></a>Remarks

結果を格納したら、外部テーブルのデータを変更することはできません。 CETAS は前回の実行で作成された基になるデータを上書きしないため、このスクリプトを繰り返すことはできません。 実際のシナリオでこれらが必要な場合は、次のフィードバック項目に投票するか、Azure フィードバック サイトで新しいものを提案してください。
- [外部テーブルへの新しいデータの挿入を有効にする](https://feedback.azure.com/d365community/forum/9b9ba8e4-0825-ec11-b6e6-000d3a4f07b8)
- [外部テーブルからのデータの削除を有効にする](https://feedback.azure.com/d365community/idea/fb5a00c9-0a25-ec11-b6e6-000d3a4f07b8)
- [CETAS でパーティションを指定する](https://feedback.azure.com/d365community/idea/e28278db-0a25-ec11-b6e6-000d3a4f07b8)
- [ファイルのサイズと数を指定する](https://feedback.azure.com/d365community/idea/262048b9-0925-ec11-b6e6-000d3a4f07b8)

サポートされている出力の種類は、Parquet と CSV だけです。 その他の種類については、[Azure フィードバック サイト](https://feedback.azure.com/d365community/forum/9b9ba8e4-0825-ec11-b6e6-000d3a4f07b8)で投票することができます。

## <a name="next-steps"></a>次のステップ

さまざまな種類のファイルに対するクエリの実行については、[単一の CSV ファイルに対するクエリの実行](query-single-csv-file.md)、[Parquet ファイルに対するクエリの実行](query-parquet-files.md)、および [JSON ファイルに対するクエリの実行](query-json-files.md)に関するページを参照してください。
