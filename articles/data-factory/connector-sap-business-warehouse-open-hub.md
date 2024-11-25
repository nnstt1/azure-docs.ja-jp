---
title: オープン ハブを介して SAP Business Warehouse からデータをコピーする
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory または Synapse Analytics パイプラインでコピー アクティビティを使用して、オープン ハブを介して SAP Business Warehouse (BW) から、サポートされているシンク データ ストアへデータをコピーする方法について説明します。
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 9e4bc92687c098709de5298d7ea22ed81b1e3783
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124836122"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory または Synapse Analytics を使用するオープン ハブを介して SAP Business Warehouse からデータをコピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory および Synapse Analytics パイプラインでコピー アクティビティを使用して、オープン ハブを介して SAP Business Warehouse (BW) からデータをコピーする方法の概要を説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

>[!TIP]
>SAP データ統合シナリオの全体的なサポートについては、各 SAP コネクタの詳細な説明、比較、ガイダンスが含まれた、[SAP データ統合に関するホワイトペーパー](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

オープン ハブ コネクタ経由のこの SAP Business Warehouse は、次のアクティビティでサポートされています。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

SAP Business Warehouse のデータは、オープン ハブを介して、サポートされている任意のシンク データ ストアにコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この SAP Business Warehouse オープン ハブ コネクタは以下をサポートします。

- SAP Business Warehouse **バージョン 7.01 以上 (2015 年以後にリリースされた最近の SAP サポート パッケージ スタックの場合)** 。 このコネクタでは、SAP BW/4HANA はサポートされていません。
- オープン ハブ宛先の、DSO、InfoCube、MultiProvider、DataSource などがその下にある可能性があるローカル テーブルを介したデータのコピー。
- 基本認証を使用したデータのコピー。
- SAP アプリケーション サーバーまたは SAP メッセージ サーバーへの接続。
- RFC を介したデータの取得。

## <a name="sap-bw-open-hub-integration"></a>SAP BW オープン ハブの統合 

[SAP BW オープン ハブ サービス](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service)は、SAP BW からデータを抽出する効率的な方法です。 次の図は、お客様が利用する SAP システム内の典型的なフローの 1 つを示しています。この場合、データは SAP ECC、PSA、DSO、キューブと流れています。

SAP BW オープン ハブ宛先 (OHD) は、SAP データの中継先のターゲットを定義します。 SAP データ転送プロセス (DTP) によってサポートされているすべてのオブジェクトは、たとえば DSO、InfoCube、DataSource などのオープン ハブ データ ソースとして使用できます。中継されたデータが保存されるオープン ハブ宛先の種類は、データベース テーブル (ローカルまたはリモート) またはフラット ファイルである場合があります。 この SAP BW オープン ハブ コネクタは、BW の OHD ローカル テーブルからのデータ コピーをサポートしています。 その他の種類を使用している場合は、その他のコネクタを使用してデータベースまたはファイル システムに直接接続できます。

:::image type="content" source="./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png" alt-text="SAP BW オープン ハブ":::

## <a name="delta-extraction-flow"></a>差分抽出フロー

SAP BW オープン ハブ コネクタでは、次の 2 つの省略可能なプロパティが提供されます。オープン ハブからの差分読み込みを処理するために使用できる `excludeLastRequest` と `baseRequestId` です。 

- **excludeLastRequestId**: 最後の要求のレコードを除外するかどうか。 既定値は true です。 
- **baseRequestId**: 差分読み込み要求の ID。 設定されると、requestId がこのプロパティの値より大きいデータのみが取得されます。 

全体として、SAP InfoProviders からの抽出は、次の 2 つの手順で構成されます。 

1. **SAP BW データ転送プロセス (DTP)** この手順では、SAP BW InfoProvider から SAP BW オープン ハブ テーブルにデータがコピーされます 

1. **データのコピー** この手順では、オープン ハブ テーブルがコネクタによって読み取られます 

:::image type="content" source="media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png" alt-text="差分抽出フロー":::

最初の手順では、DTP が実行されます。 実行ごとに新しい SAP 要求 ID が作成されます。 要求 ID はオープン ハブ テーブルに保存され、差分を識別するためにコネクタによって使用されます。 2 つ目の手順は非同期で実行されます。DTP は SAP によってトリガーされ、データのコピーはサービスを介してトリガーされます。 

既定では、サービスでオープン ハブ テーブルから最新の差分を読み取ることはありません ("最後の要求を除外する" オプションは true です)。 このため、サービスのデータはオープン ハブ テーブルのデータに関して 100% 最新というわけではありません (最後の差分はありません)。 代わりに、このプロシージャによって、非同期の抽出で行を失わないようにすることができます。 サービスでオープン ハブ テーブルを読み取るときでも問題なく動作し、DTP は引き続き同じテーブルに書き込みます。 

通常、サービスによる最後の実行でコピーされた最大の要求 ID をステージング データ ストア (上図の Azure BLOB など) に保存します。 そのため、後続の実行でサービスによって同じ要求が 2 回読み取られることはありません。 その間、データはオープン ハブ テーブルから自動的に削除されないことに注意してください。

適切は差分処理では、同じオープン ハブ テーブルの異なる DTP からの要求 ID を持つことは許可されません。 そのため、オープン ハブ宛先 (OHD) ごとに複数の DTP を作成する必要はありません。 同じ InfoProvider から完全抽出と差分抽出が必要な場合、同じ InfoProvider に OHD を 2 つ作成する必要があります。 

## <a name="prerequisites"></a>前提条件

この SAP Business Warehouse オープン ハブ コネクタを使用するには、次の作業を行う必要があります。

- バージョン 3.13 以上のセルフホステッド統合ランタイムをセットアップする。 詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関する記事をご覧ください。

- SAP の Web サイトから **64 ビットの [SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)** をダウンロードし、それをセルフホステッド IR マシンにインストールします。 インストール時には、省略可能なセットアップ手順のウィンドウで、次の図のように **[Install Assemblies to GAC] (アセンブリを GAC にインストールする)** オプションを選択したことを確認します。 

    :::image type="content" source="./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png" alt-text="SAP .NET Connector をインストールする":::

- BW コネクタで使用する予定の SAP ユーザーは、以下のアクセス許可を持っている必要があります。 

    - RFC と SAP BW の認可。 
    - 認可オブジェクト "S_SDSAUTH" の "実行" アクティビティに対するアクセス許可。

- SAP オープン ハブ宛先の種類は、[Technical Key] (テクニカル キー) オプションをオンにして、**データベース テーブル** として作成します。  また、必須ではありませんが、[Deleting Data from Table] (テーブルからデータを削除する) はオンにしないままにすることをお勧めします。 DTP を利用して (直接実行するか、既存のプロセス チェーンに統合する)、選択したソース オブジェクト (キューブなど) のデータをオープン ハブ宛先テーブルに取り込みます。

## <a name="getting-started"></a>作業の開始

> [!TIP]
>
> SAP BW オープン ハブ コネクタの使用に関するチュートリアルについては、「[SAP Business Warehouse (BW) からデータを読み込む](load-sap-bw-data.md)」を参照してください。

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

以下のセクションでは、SAP Business Warehouse オープン ハブ コネクタに固有のエンティティを定義するために使用されるプロパティの詳細を説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

SAP Business Warehouse オープン ハブのリンクされたサービスでは、以下のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**SapOpenHub** | はい |
| server | SAP BW インスタンスが存在するサーバーの名前。 | はい |
| systemNumber | SAP BW システムのシステムの数。<br/>使用できる値: 文字列として表される 2 桁の 10 進数。 | はい |
| messageServer | SAP メッセージ サーバーのホスト名。<br/>SAP メッセージ サーバーへの接続に使用されます。 | いいえ |
| messageServerService | メッセージ サーバーのサービス名またはポート番号。<br/>SAP メッセージ サーバーへの接続に使用されます。 | いいえ |
| systemId | テーブルが配置されている SAP システムの ID。<br/>SAP メッセージ サーバーへの接続に使用されます。 | いいえ |
| logonGroup | SAP システムのログオン グループ。<br/>SAP メッセージ サーバーへの接続に使用されます。 | いいえ |
| clientId | SAP BW システム内のクライアントのクライアント ID。<br/>使用できる値: 文字列として表される 3 桁の 10 進数。 | はい |
| language | SAP システムで使用する言語。 | いいえ (既定値は **EN**)|
| userName | SAP サーバーにアクセスできるユーザーの名前。 | はい |
| password | ユーザーのパスワード。 このフィールドを SecureString とマークして安全に保存するか、[Azure Key Vault に保存されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 「[前提条件](#prerequisites)」に記されているように、セルフホステッド統合ランタイムが必要です。 |はい |

**例:**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、SAP BW オープン ハブ データセットでサポートされるプロパティの一覧を示します。

SAP BW オープン ハブとの間でデータをコピーするには、データセットの type プロパティを **SapOpenHubTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **SapOpenHubTable** に設定する必要があります。  | はい |
| openHubDestinationName | データのコピー元になるオープン ハブ宛先の名前。 | はい |

データセットに `excludeLastRequest` と `baseRequestId` を設定していた場合は現状のまま引き続きサポートされますが、今後のアクティビティ ソースでは新しいモデルを使用することをお勧めします。

**例:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、SAP BW オープン ハブ ソースでサポートされるプロパティの一覧を示します。

### <a name="sap-bw-open-hub-as-source"></a>ソースとしての SAP BW オープン ハブ

SAP BW Open Hub からデータをコピーするために、コピー アクティビティの **source** セクションでは次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティは **SapOpenHubSource** に設定する必要があります | はい |
| excludeLastRequest | 最後の要求のレコードを除外するかどうか。 | いいえ (既定値は **true**)。 |
| baseRequestId | 差分読み込み要求の ID。 設定されると、requestId がこのプロパティの値 **より大きい** データのみが取得されます。  | いいえ |
| customRfcReadTableFunctionModule | SAP テーブルからデータを読み取るために使用できるカスタム RFC 関数モジュール。 <br/> カスタム RFC 関数モジュールを使用して、SAP システムからデータを取得し、サービスに返す方法を定義できます。 カスタム関数モジュールには、`/SAPDS/RFC_READ_TABLE2` と同様のインターフェイスが実装されている必要があります (インポート、エクスポート、テーブル)。これは、サービスで使用される既定のインターフェイスです。 | いいえ |
| sapDataColumnDelimiter | 出力データを分けるために SAP RFC に渡される、区切り記号として使用される 1 文字。 | いいえ |

>[!TIP]
>オープン ハブ テーブルには、1 つの要求 ID によって生成されたデータのみが含まれています。たとえば、常に完全な読み込みを行い、テーブル内の既存のデータを上書きする場合や、DTP はテストのために 1 回のみ実行する場合は、データを外へコピーするため、忘れずに "excludeLastRequest" オプションをオフにします。

データ読み込みを高速化するために、コピー アクティビティに [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) を設定して、SAP BW オープン ハブから並行してデータを読み込むことができます。 たとえば、`parallelCopies` を 4 に設定すると、サービスでは同時に 4 つの RFC 呼び出しが実行され、各 RFC 呼び出しは、DTP 要求 ID とパッケージ ID でパーティション分割された SAP BW オープン ハブ テーブルからデータの一部を取得します。 これは、一意の DTP 要求 ID + パッケージ ID の数値が `parallelCopies` の値よりも大きい場合に適用されます。 ファイルベースのデータ ストアにデータをコピーする場合は、複数のファイルとしてフォルダーに書き込む (フォルダー名のみを指定する) こともお勧めします。この場合、1 つのファイルに書き込むよりもパフォーマンスが優れています。

**例:**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapOpenHubSource",
                "excludeLastRequest": true
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>SAP BW オープン ハブのデータ型マッピング

SAP BW オープン ハブからデータをコピーするとき、SAP BW のデータ型からサービス内で内部的に使用される中間データ型への、以下のマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| SAP ABAP の型 | 中間サービス データ型 |
|:--- |:--- |
| C (String) | String |
| I (integer) | Int32 |
| F (Float) | Double |
| D (Date) | String |
| T (Time) | String |
| P (BCD Packed、Currency、Decimal、Qty) | Decimal |
| N (Numc) | String |
| X (Binary および Raw) | String |

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="troubleshooting-tips"></a>トラブルシューティングのヒント

**現象:** SAP BW on HANA を実行しているとき、コピー アクティビティ (100万行) を使用してもデータのサブセットしかコピーされない場合、考えられる原因は、DTP で [SAP HANA Execution]\(SAP HANA 実行\) オプションを有効にしていることです。この場合、サービスではデータの最初のバッチだけを取得できます。

**解決策:** DTP で [SAP HANA Execution]\(SAP HANA 実行\) オプションを無効にして、データを再処理してから、コピー アクティビティをもう一度実行してみてください。

## <a name="next-steps"></a>次のステップ
Copy アクティビティでソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。
