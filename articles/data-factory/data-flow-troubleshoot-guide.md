---
title: マッピング データ フローのトラブルシューティング
description: Azure Data Factory でのデータ フローに関する問題のトラブルシューティングを行う方法について説明します。
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.openlocfilehash: f41d53207eb00e4044e523e481de444a615a5d88
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389401"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Azure Data Factory でマッピング データ フローをトラブルシューティングする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

<<<<<<< HEAD
この記事では、Azure Data Factory でマッピング データ フローのための一般的なトラブルシューティング方法について説明します。

## <a name="common-error-codes-and-messages"></a>一般的なエラー コードとメッセージ 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>エラー コード:DF-Executor-SourceInvalidPayload
- **メッセージ**:コンテナーが存在しないため、データ プレビュー、デバッグ、パイプライン データ フローの実行が失敗しました
- **原因**: ストレージに存在しないコンテナーがデータセットに含まれています。
- **推奨事項**: データセットで参照されているコンテナーが存在し、アクセス可能であることをご確認ください。

### <a name="error-code-df-executor-systeminvalidjson"></a>エラー コード:DF-Executor-SystemInvalidJson

- **メッセージ**:JSON 解析エラー、サポートされていないエンコードまたは複数行が存在します
- **原因**: JSON ファイルで、次のような問題が発生しているおそれがあります。サポートされていないエンコード、バイトの破損、または入れ子になった多数の行での単一ドキュメントとしての JSON ソースの使用。
- **推奨事項**: JSON ファイルのエンコードがサポートされていることをご確認ください。 JSON データセットを使用しているソース変換で **[JSON Settings]\(JSON 設定\)** を展開し、 **[Single Document]\(単一ドキュメント\)** をオンにします。
 
### <a name="error-code-df-executor-broadcasttimeout"></a>エラー コード:DF-Executor-BroadcastTimeout

- **メッセージ**:ブロードキャスト結合のタイムアウトエラーが発生しました。ブロードキャスト ストリームが、デバッグ実行では 60 秒以内に、ジョブ実行では 300 秒以内にデータを生成することを確認してください
- **原因**: ブロードキャストの既定のタイムアウトは、デバッグ実行では 60 秒、ジョブ実行では 300 秒です。 ブロードキャスト用に選択されたストリームは、この制限内にデータを生成するには大きすぎます。
- **推奨事項**: データ フロー変換の **[最適化]** タブで、結合、存在、参照をご確認ください。 ブロードキャストの既定のオプションは **[Auto]\(自動\)** です。 **[Auto]\(自動\)** が設定されている場合、または **[Fixed]\(固定\)** で左側または右側でブロードキャストするように手動で設定している場合は、より大きな Azure 統合ランタイム (IR) 構成を設定するか、ブロードキャストをオフにすることができます。 データ フローで最適なパフォーマンスを得るためには、 **[Auto]\(自動\)** を使用して Spark がブロードキャストできるよう許可し、メモリ最適化 Azure IR を使用できるようにすることをお勧めします。 
 
  デバッグ パイプライン実行からのデバッグ テスト実行でデータ フローを実行している場合、この条件がより頻繁に実行されることがあります。 これは、より高速のデバッグ エクスペリエンスを維持するために、Azure Data Factory によってブロードキャスト タイムアウトが 60 秒に調整されるためです。 タイムアウトは、トリガーされた実行の 300 秒のタイムアウトまで延長できます。 これを行うには、 **[Debug]\(デバッグ\)**  >  **[Use Activity Runtime]\(アクティビティ ランタイムを使用\)** オプションを使用して、データ フローの実行パイプライン アクティビティで定義されている Azure IR を使用できます。

- **メッセージ**: ブロードキャスト結合のタイムアウト エラー。この問題を回避するには、結合/存在/参照変換でブロードキャスト オプションの [オフ] を選択します。 パフォーマンスを向上させるために結合オプションをブロードキャストする場合は、ブロードキャスト ストリームで、デバッグ実行では 60 秒以内に、ジョブ実行では 300 秒以内にデータを生成できることを確認します。
- **原因**: ブロードキャストの既定のタイムアウトは、デバッグ実行では 60 秒、ジョブ実行では 300 秒です。 ブロードキャスト結合の場合、ブロードキャスト用に選択されたストリームは、この制限内にデータを生成するには大きすぎます。 ブロードキャスト結合が使用されていない場合、データフローによる既定のブロードキャストが同じ制限に達することがあります。
- **推奨事項**: ブロードキャスト オプションをオフにするか、処理に 60 秒を超える時間を要する可能性がある大規模なデータ ストリームのブロードキャストは避けてください。 小さいストリームを選択してブロードキャストします。 大きな Azure SQL データ ウェアハウス テーブルとソース ファイルは、通常は適切な選択ではありません。 ブロードキャスト結合が存在しない場合、エラーが発生したときはより大きなクラスターを使用します。

### <a name="error-code-df-executor-conversion"></a>エラー コード:DF-Executor-Conversion

- **メッセージ**:無効な文字が原因で、日付または時刻への変換に失敗しました
- **原因**: データの形式が正しくありません。
- **推奨事項**: 正しいデータ型を使用してください。

### <a name="error-code-df-executor-invalidcolumn"></a>エラー コード:DF-Executor-InvalidColumn
- **メッセージ**:クエリに列名を指定する必要があります。SQL 関数を使用している場合は別名を設定してください
- **原因**: 列名が指定されていません。
- **推奨事項**: min() や max() などの SQL 関数を使用している場合は、別名を設定します。

### <a name="error-code-df-executor-drivererror"></a>エラー コード:DF-Executor-DriverError
- **メッセージ**:INT96 は、ADF データフローではサポートされていない従来のタイムスタンプ型です。 列の型を最新の型にアップグレードすることを検討してください。
- **原因**: ドライバーエラーです。
- **推奨事項**: INT96 は、Azure Data Factory データ フローでサポートされていない従来のタイムスタンプ型です。 列の型を最新の型にアップグレードすることを検討してください。

### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>エラー コード:DF-Executor-BlockCountExceedsLimitError
- **メッセージ**:コミット前のブロック数は、ブロックの上限である 100,000 個を超えることはできません。 BLOB 構成を確認してください。
- **原因**: BLOB でのコミットされていないブロックの最大数は 100,000 です。
- **推奨事項**: この問題の詳細については、Microsoft の製品チームにお問い合わせください。

### <a name="error-code-df-executor-partitiondirectoryerror"></a>エラー コード:DF-Executor-PartitionDirectoryError
- **メッセージ**:指定されたソース パスに、複数のパーティション ディレクトリ (たとえば、<Source Path>/<パーティション ルート ディレクトリ 1>/a=10/b=20、<Source Path>/<パーティション ルート ディレクトリ 2>/c=10/d=30)、他のファイルを含むパーティション ディレクトリ、またはパーティション分割されていないディレクトリ (たとえば、<Source Path>/<パーティション ルート ディレクトリ 1>/a=10/b=20、<Source Path>/Directory 2/file1) のいずれかが含まれています。ソース パスからパーティション ルート ディレクトリを削除して、別個のソース変換を利用して読み取ってください。
- **原因**: ソース パスに複数のパーティション ディレクトリ、別のファイルを含むパーティション ディレクトリ、またはパーティション分割されていないディレクトリのいずれかが含まれています。
- **推奨事項**: パーティション ルート ディレクトリをソース パスから削除し、別個のソース変換を使用して読み取ってください。

### <a name="error-code-df-executor-invalidtype"></a>エラー コード:DF-Executor-InvalidType
- **メッセージ**:パラメーターの型が、渡された値の型と一致していることを確認してください。 パイプラインからの float パラメーターの引き渡しは現在、サポートされていません。
- **原因**: 宣言された型のデータ型に、実際のパラメーター値との互換性がありません。
- **推奨事項**: データ フローに渡されたパラメーター値が、宣言された型と一致することをご確認ください。

### <a name="error-code-df-executor-parseerror"></a>エラー コード:DF-Executor-ParseError
- **メッセージ**:式を解析できません
- **原因**: 書式が正しくないため、式によって解析エラーが生成されました。
- **推奨事項**: 式の書式をご確認ください。

### <a name="error-code-df-executor-systemimplicitcartesian"></a>エラー コード:DF-Executor-SystemImplicitCartesian
- **メッセージ**:INNER join では暗黙的なデカルト積はサポートされていません。代わりに CROSS JOIN を使用してください。 結合で使用される列は、行に対して一意のキーを作成する必要があります。
- **原因**: 論理プラン間の INNER 結合では暗黙的なデカルト積はサポートされていません。 結合で列を使用している場合は、一意のキーを作成します。
- **推奨事項**:非等値ベースの結合では、CROSS JOIN を使用します。

### <a name="error-code-getcommand-outputasync-failed"></a>エラー コード:GetCommand OutputAsync に失敗しました
- **メッセージ**:データ フロー デバッグおよびデータ プレビューの実行中:GetCommand OutputAsync が次の理由で失敗しました。
- **原因**: このエラーはバックエンド サービス エラーです。 
- **推奨事項**: 操作を再試行し、デバッグ セッションを再起動してみてください。 再試行して再起動しても問題が解決しない場合は、カスタマー サポートにお問い合わせください。 

### <a name="error-code-df-executor-outofmemoryerror"></a>エラー コード:DF-Executor-OutOfMemoryError
 
- **メッセージ**:実行中に、クラスターにメモリ不足の問題が発生しました。コア数がより大きく、メモリが最適化されたコンピューティングの種類で統合ランタイムを使用して、もう一度お試しください
- **原因**:クラスターのメモリがメモリ不足しています。
- **推奨事項**: デバッグ クラスターは、開発のためのものではありません。 データ サンプリングおよび適切なコンピューティングの種類とサイズを使用して、ペイロードを実行します。 パフォーマンスに関するヒントについては、[マッピング データ フローのパフォーマンス ガイド](concepts-data-flow-performance.md)に関する記事を参照してください。

### <a name="error-code-df-executor-illegalargument"></a>エラー コード:DF-Executor-illegalArgument

- **メッセージ**:リンクされたサービスのアクセス キーが正しいことを確認してください
- **原因**: アカウント名またはアクセス キーが正しくありません。
- **推奨事項**:リンクされたサービスで指定されたアカウント名またはアクセス キーが正しいことを確認してください。 

### <a name="error-code-df-executor-columnunavailable"></a>エラー コード:DF-Executor-ColumnUnavailable
- **メッセージ**: 式に使用されている列名が利用できないか、または無効です。
- **原因**: 式に使用されている列名が無効か、または利用できません。
- **推奨事項**: 式に使用されている列名をご確認ください。

 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>エラー コード:DF-Executor-OutOfDiskSpaceError
- **メッセージ**: 内部サーバー エラー
- **原因**: クラスターのディスク領域が不足しています。
- **推奨事項**: パイプラインをもう一度お試しください。 それを行っても問題が解決しない場合は、カスタマー サポートにお問い合わせください。


 ### <a name="error-code-df-executor-storeisnotdefined"></a>エラー コード:DF-Executor-StoreIsNotDefined
- **メッセージ**: ストアの構成が定義されていません。 このエラーは、パイプラインでの無効なパラメーター割り当てが原因である可能性があります。
- **原因**: 不明です。
- **推奨事項**: パイプラインでのパラメーター値の割り当てをご確認ください。 パラメーター式に無効な文字が含まれているおそれがあります。


### <a name="error-code-4502"></a>エラー コード:4502
- **メッセージ**: Integration Runtime での調整のために障害を発生させている、多くの同時 MappingDataflow 実行があります。
- **原因**: 統合ランタイムで、大量のデータ フロー アクティビティの実行が同時に発生しています。 詳細については、[Azure Data Factory の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)に関する記事を参照してください。
- **推奨事項**: 多くのデータ フロー アクティビティを並列に実行する場合は、複数の統合ランタイムに分散させてください。


### <a name="error-code-invalidtemplate"></a>エラー コード:InvalidTemplate
- **メッセージ**: パイプライン式を評価できません。
- **原因**: データ フロー アクティビティで渡されたパイプライン式が、構文エラーのため正しく処理されていません。
- **推奨事項**: アクティビティ監視でアクティビティを確認して、式を検証します。

### <a name="error-code-2011"></a>エラー コード:2011
- **メッセージ**: アクティビティは Azure Integration Runtime で実行されていましたが、セルフホステッド統合ランタイムを介して接続されたデータ ストアまたはコンピューティングの資格情報の暗号化を解除できませんでした。 このアクティビティに関連付けられているリンク サービスの構成を確認し、適切な統合ランタイムの種類を使用していることを確認してください。
- **原因**: セルフホステッド統合ランタイムでのリンク サービスは、データ フローではサポートされていません。
- **推奨事項**: マネージド仮想ネットワーク統合ランタイムで実行するように、データ フローを構成します。

### <a name="error-code-df-xml-invalidvalidationmode"></a>エラー コード: DF-Xml-InvalidValidationMode
- **メッセージ**: 無効な xml 検証モードが指定されています。
- **推奨事項**: パラメーター値を確認し、適切な検証モードを指定します。

### <a name="error-code-df-xml-invaliddatafield"></a>エラー コード: DF-Xml-InvalidDataField
- **メッセージ**: 破損レコードのフィールドは、文字列型および null 許容型である必要があります。
- **推奨事項**: ソース プロジェクトの列 `\"_corrupt_record\"` に文字列データ型が含まれていることを確認してください。

### <a name="error-code-df-xml-malformedfile"></a>エラー コード: DF-Xml-MalformedFile
- **メッセージ**: 'FailFastMode' の xml の形式が正しくありません。
- **推奨事項**: XML ファイルの内容を適切な形式に更新します。

### <a name="error-code-df-xml-invaliddatatype"></a>エラー コード: DF-Xml-InvalidDataType
- **メッセージ**: XML 要素にはサブ要素または属性があるため、変換できません。

### <a name="error-code-df-xml-invalidreferenceresource"></a>エラー コード: DF-Xml-InvalidReferenceResource
- **メッセージ**: xml データ ファイル内の参照リソースを解決できません。
- **推奨事項**: XML データ ファイル内の参照リソースを確認する必要があります。

### <a name="error-code-df-xml-invalidschema"></a>エラー コード: DF-Xml-InvalidSchema
- **メッセージ**: スキーマの検証に失敗しました。

### <a name="error-code-df-xml-unsupportedexternalreferenceresource"></a>エラー コード: DF-Xml-UnsupportedExternalReferenceResource
- **メッセージ**: xml データ ファイル内の外部参照リソースはサポートされていません。
- **推奨事項**: 外部参照リソースが現在サポートされていない場合は、XML ファイルの内容を更新します。

### <a name="error-code-df-gen2-invalidaccountconfiguration"></a>エラー コード: DF-GEN2-InvalidAccountConfiguration
- **メッセージ**: アカウント キーまたは tenant/spnId/spnCredential/spnCredentialType または miServiceUri/miServiceToken のいずれかを指定する必要があります。
- **推奨事項**: 関連する GEN2 リンク サービスで、適切なアカウントを構成します。

### <a name="error-code-df-gen2-invalidauthconfiguration"></a>エラー コード: DF-GEN2-InvalidAuthConfiguration
- **メッセージ**: 3 つの認証方法 (Key、ServicePrincipal、MI) のうち 1 つだけを指定できます。 
- **推奨事項**: 関連する GEN2 リンク サービスで、適切な認証方法を選択します。

### <a name="error-code-df-gen2-invalidserviceprincipalcredentialtype"></a>エラー コード: DF-GEN2-InvalidServicePrincipalCredentialType
- **メッセージ**: ServicePrincipalCredentialType が無効です。

### <a name="error-code-df-gen2-invaliddatatype"></a>エラー コード: DF-GEN2-InvalidDataType
- **メッセージ**: クラウドの種類が無効です。

### <a name="error-code-df-blob-invalidaccountconfiguration"></a>エラー コード: DF-Blob-InvalidAccountConfiguration
- **メッセージ**: アカウント キーまたは sas_token のいずれかを指定する必要があります。

### <a name="error-code-df-blob-invalidauthconfiguration"></a>エラー コード: DF-InvalidAuthConfiguration
- **メッセージ**: 2 つの認証方法 (Key、SAS) のうち 1 つだけを指定できます。

### <a name="error-code-df-blob-invaliddatatype"></a>エラー コード: DF-Blob-InvalidDataType
- **メッセージ**: クラウドの種類が無効です。

### <a name="error-code-df-cosmos-partitionkeymissed"></a>エラー コード: DF-Cosmos-PartitionKeyMissed
- **メッセージ**: 更新操作と削除操作には、パーティション キーのパスを指定する必要があります。
- **推奨事項**: Cosmos シンクの設定で提供パーティション キーを使用します。

### <a name="error-code-df-cosmos-invalidpartitionkey"></a>エラー コード: DF-Cosmos-InvalidPartitionKey
- **メッセージ**: 更新操作と削除操作では、パーティション キーのパスを空にすることはできません。
- **推奨事項**: Cosmos シンクの設定で提供パーティション キーを使用します。

### <a name="error-code-df-cosmos-idpropertymissed"></a>エラー コード: DF-Cosmos-IdPropertyMissed
- **メッセージ**: 削除操作と更新操作には、'id' プロパティをマップする必要があります。
- **推奨事項**: Cosmos シンクの設定で、入力データに `id` 列が含まれていることを確認します。 含まれていない場合は、**変換の選択または派生** を使用して、シンクの前にこの列を生成します。

### <a name="error-code-df-cosmos-invalidpartitionkeycontent"></a>エラー コード: DF-Cosmos-InvalidPartitionKeyContent
- **メッセージ**: パーティション キーは/で始まっている必要があります。
- **推奨事項**: Cosmos シンクの設定で、パーティション キーが `/` で始まっていることを確認します (例: `/movieId`)。

### <a name="error-code-df-cosmos-invalidpartitionkey"></a>エラー コード: DF-Cosmos-InvalidPartitionKey
- **メッセージ**: 削除操作および更新操作のためのパーティション キーがシンクでマップされていません。
- **推奨事項**: Cosmos シンクの設定で、コンテナーのパーティション キーと同じパーティション キーを使用します。

### <a name="error-code-df-cosmos-invalidconnectionmode"></a>エラー コード: DF-Cosmos-InvalidConnectionMode
- **メッセージ**: connectionMode が無効です。
- **推奨事項**: Cosmos の設定で、サポートされているモードが **Gateway** と **DirectHttps** であることを確認します。

### <a name="error-code-df-cosmos-invalidaccountconfiguration"></a>エラー コード: DF-Cosmos-InvalidAccountConfiguration
- **メッセージ**: accountName または accountEndpoint のいずれかを指定する必要があります。

### <a name="error-code-df-github-writenotsupported"></a>エラー コード: DF-Github-WriteNotSupported
- **メッセージ**: GitHub ストアで書き込みが許可されていません。

### <a name="error-code-df-pgsql-invalidcredential"></a>エラー コード: DF-PGSQL-InvalidCredential
- **メッセージ**: ユーザー/パスワードを指定する必要があります。
- **推奨事項**: 関連する postgresql リンク サービスに適切な資格情報が設定されていることを確認します。

### <a name="error-code-df-snowflake-invalidstageconfiguration"></a>エラー コード: DF-Snowflake-InvalidStageConfiguration
- **メッセージ**: Snowflake 読み取り/書き込み操作では、BLOB ストレージ タイプのみをステージとして使用できます。

### <a name="error-code-df-snowflake-invalidstageconfiguration"></a>エラー コード: DF-Snowflake-InvalidStageConfiguration
- **メッセージ**: Snowflake のステージ プロパティは、Azure BLOB + SAS 認証で指定する必要があります。

### <a name="error-code-df-snowflake-invaliddatatype"></a>エラー コード: DF-Snowflake-InvalidDataType
- **メッセージ**: spark 型は Snowflake ではサポートされていません。
- **推奨事項**: **変換の派生** を使用して、Snowflake シンクの前に入力データの関連列を文字列型に変更します。 

### <a name="error-code-df-hive-invalidblobstagingconfiguration"></a>エラー コード: DF-Hive-InvalidBlobStagingConfiguration
- **メッセージ**: BLOB ストレージのステージング プロパティを指定する必要があります。

### <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>エラー コード: DF-Hive-InvalidGen2StagingConfiguration
- **メッセージ**: ADLS Gen2 ストレージのステージングは、サービス プリンシパルのキー資格情報のみをサポートしています。
- **推奨事項**: ステージングとして使用される ADLS Gen2 リンク サービスに、サービス プリンシパルのキー資格情報が適用されていることを確認します。

### <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>エラー コード: DF-Hive-InvalidGen2StagingConfiguration
- **メッセージ**: ADLS Gen2 ストレージのステージング プロパティを指定する必要があります。 キーまたは tenant/spnId/spnKey または miServiceUri/miServiceToken のいずれかが必要です。
- **推奨事項**: 関連する ADLS Gen2 リンク サービスに、ハイブでステージングとして使用される適切な資格情報を適用します。 

### <a name="error-code-df-hive-invaliddatatype"></a>エラー コード: DF-Hive-InvalidDataType
- **メッセージ**: サポートされていない列です。
- **推奨事項**: 入力データの列を、ハイブでサポートされているデータ型と一致するように更新します。

### <a name="error-code-df-hive-invalidstoragetype"></a>エラー コード: DF-Hive-InvalidStorageType
- **メッセージ**: ストレージの種類には、blob または gen2 を指定できます。

### <a name="error-code-df-delimited-invalidconfiguration"></a>エラー コード: DF-Delimited-InvalidConfiguration
- **メッセージ**: 空の行またはカスタム ヘッダーのいずれかを指定する必要があります。
- **推奨事項**: CSV の設定で、空の行またはカスタム ヘッダーを指定します。

### <a name="error-code-df-delimited-columndelimitermissed"></a>エラー コード: DF-Delimited-ColumnDelimiterMissed
- **メッセージ**: 解析には列区切り記号が必要です。
- **推奨事項**: CSV の設定に列区切り記号があることを確認します。

### <a name="error-code-df-mssql-invalidcredential"></a>エラー コード: DF-MSSQL-InvalidCredential
- **メッセージ**: user/pwd または tenant/spnId/spnKey または miServiceUri/miServiceToken のいずれかを指定する必要があります。
- **推奨事項**: 関連する MSSQL リンク サービスに、適切な資格情報を適用します。

### <a name="error-code-df-mssql-invaliddatatype"></a>エラー コード: DF-MSSQL-InvalidDataType
- **メッセージ**: サポートされていないフィールドです。
- **推奨事項**: 入力データの列を、MSSQL でサポートされているデータ型と一致するように変更します。

### <a name="error-code-df-mssql-invalidauthconfiguration"></a>エラー コード: DF-MSSQL-InvalidAuthConfiguration
- **メッセージ**: 3 つの認証方法 (Key、ServicePrincipal、MI) のうち 1 つだけを指定できます。
- **推奨事項**: 関連する MSSQL リンク サービスでは、3 つの認証方法 (Key、ServicePrincipal、MI) のうち 1 つだけを指定できます。

### <a name="error-code-df-mssql-invalidcloudtype"></a>エラー コード: DF-MSSQL-InvalidCloudType
- **メッセージ**: クラウドの種類が無効です。
- **推奨事項**: 関連する MSSQL リンク サービスで、クラウドの種類を確認します。

### <a name="error-code-df-sqldw-invalidblobstagingconfiguration"></a>エラー コード: DF-SQLDW-InvalidBlobStagingConfiguration
- **メッセージ**: BLOB ストレージのステージング プロパティを指定する必要があります。

### <a name="error-code-df-sqldw-invalidstoragetype"></a>エラー コード: DF-SQLDW-InvalidStorageType
- **メッセージ**: ストレージの種類には、blob または gen2 を指定できます。

### <a name="error-code-df-sqldw-invalidgen2stagingconfiguration"></a>エラー コード: DF-SQLDW-InvalidGen2StagingConfiguration
- **メッセージ**: ADLS Gen2 ストレージのステージングは、サービス プリンシパルのキー資格情報のみをサポートしています。

### <a name="error-code-df-sqldw-invalidconfiguration"></a>エラー コード: DF-SQLDW-InvalidConfiguration
- **メッセージ**: ADLS Gen2 ストレージのステージング プロパティを指定する必要があります。 キーまたは tenant/spnId/spnCredential/spnCredentialType または miServiceUri/miServiceToken のいずれかが必要です。

### <a name="error-code-df-delta-invalidconfiguration"></a>エラー コード: DF-DELTA-InvalidConfiguration
- **メッセージ**: タイムスタンプとバージョンを同時に設定することはできません。

### <a name="error-code-df-delta-keycolumnmissed"></a>エラー コード: DF-DELTA-KeyColumnMissed
- **メッセージ**: 挿入不可能な操作には、キー列を指定する必要があります。

### <a name="error-code-df-delta-invalidtableoperationsettings"></a>エラー コード: DF-DELTA-InvalidTableOperationSettings
- **メッセージ**: 再作成オプションと切り詰めオプションの両方を指定することはできません。

### <a name="error-code-df-excel-worksheetconfigmissed"></a>エラー コード: DF-Excel-WorksheetConfigMissed
- **メッセージ**: Excel シートの名前またはインデックスが必要です。
- **推奨事項**: パラメーター値を確認し、Excel データを読み取るシート名またはインデックスを指定してください。

### <a name="error-code-df-excel-invalidworksheetconfiguration"></a>エラー コード: DF-Excel-InvalidWorksheetConfiguration
- **メッセージ**: Excel シートの名前とインデックスが同時に存在することはできません。
- **推奨事項**: パラメーター値を確認し、Excel データを読み取るシート名またはインデックスを指定してください。

### <a name="error-code-df-excel-invalidrange"></a>エラー コード: DF-Excel-InvalidRange
- **メッセージ**: 無効な範囲が指定されています。
- **推奨事項**: パラメーター値を確認し、 [「Azure Data Factory での Excel 形式」の「データセットのプロパティ」](./format-excel.md#dataset-properties)に従って有効な範囲を指定してください。

### <a name="error-code-df-excel-worksheetnotexist"></a>エラー コード: DF-Excel-WorksheetNotExist
- **メッセージ**: Excel ワークシートが存在しません。
- **推奨事項**: パラメーター値を確認し、Excel データを読み取る有効なシート名またはインデックスを指定してください。

### <a name="error-code-df-excel-differentschemanotsupport"></a>エラー コード: DF-Excel-DifferentSchemaNotSupport
- **メッセージ**: スキーマが異なる Excel ファイルの読み取りは、現時点ではサポートされていません。

### <a name="error-code-df-excel-invaliddatatype"></a>エラー コード: DF-Excel-InvalidDataType
- **メッセージ**: データ型はサポートされていません。

### <a name="error-code-df-excel-invalidfile"></a>エラー コード: DF-Excel-InvalidFile
- **メッセージ**: 無効な Excel ファイルが指定されていますが、.xlsx と .xls のみがサポートされています。

=======
この記事では、Azure Data Factory でマッピング データ フローのための一般的なトラブルシューティング方法について説明します。  特定のエラー メッセージ、および関連する原因と推奨事項については、[一般的なデータ フローのエラー コードとメッセージ](data-flow-troubleshoot-errors.md)に関するページを参照してください。
>>>>>>> repo_sync_working_branch

## <a name="miscellaneous-troubleshooting-tips"></a>その他のトラブルシューティングのヒント
- **問題**: 予期しない例外が発生し、実行が失敗しました。
    - **メッセージ**:データ フロー アクティビティの実行中:予期しない例外が発生し、実行が失敗しました。
    - **原因**: このエラーはバックエンド サービス エラーです。 操作を再試行し、デバッグ セッションを再起動してみてください。
    - **推奨事項**: 再試行して再起動しても問題が解決しない場合は、カスタマー サポートにお問い合わせください。

-  **問題**: デバッグ データ プレビューの間の結合で出力データがありません。
    - **メッセージ**:サンプリングされた行数が少なすぎることが原因で発生した可能性がある、多数の null 値または不足値があります。 デバッグ行の制限を更新し、データを最新の情報に更新してみてください。
    - **原因**: データ プレビューの間に、結合条件が、どの行とも一致しなかったか、多数の null 値になりました。
    - **推奨事項**: **[Debug Settings]\(デバッグ設定\)** で、ソース行の制限の行数を増やします。 より多くのデータを処理するのに十分な大きさのデータ フロー クラスターを持つ Azure IR を選択してください。
    
- **問題**: 複数行の CSV ファイルが含まれるソースで検証エラーが発生しました。 
    - **メッセージ**: 次のエラー メッセージのいずれかが表示される場合があります。
        - 最後の列が null であるか、見つかりません。
        - ソースでスキーマの検証に失敗しました。
        - UX でスキーマのインポートが正しく表示されず、最後の列の名前に改行文字が含まれています。
    - **原因**: 現在、マッピング データ フローでは、行区切り記号として \r\n が使用されている場合、複数行の CSV ソース ファイルは機能しません。 復帰に余分な行があると、エラーが発生することがあります。 
    - **推奨事項**:行区切り記号として \r\n ではなく \n が使用されているソースで、ファイルを生成します。 または、コピー アクティビティを使用して、行区切り記号として \n を使用するように CSV ファイルを変換します。

## <a name="general-troubleshooting-guidance"></a>一般的なトラブルシューティング ガイダンス
1. データセット接続の状態を確認します。 ソースとシンクの変換ごとに、使用している各データベースのリンク サービスに移動して、接続をテストします。
2. データ フロー デザイナーでファイルとテーブルの接続の状態をご確認ください。 デバッグ モードで、ソース変換の **[Data Preview]\(データ プレビュー\)** を選択し、確実にデータにアクセスできるようにします。
3. データ プレビューですべてが正しいようであれば、パイプライン デザイナーに進み、パイプライン アクティビティにデータ フローを配置します。 エンドツーエンド テストとしてパイプラインをデバッグします。

### <a name="improvement-on-csvcdm-format-in-data-flow"></a>データ フローでの CSV/CDM 形式の改善 

**Azure Data Factory V2 での マッピング データ フローに区切りテキストまたは CDM の書式設定** を使用した場合、**2021 年 5 月 1 日** 以降、データ フローの区切りテキスト/CDM が改善されているため、既存のパイプラインに動作変更が発生する可能性があります。 

改善前は次の問題が発生する可能性がありますが、改善後は問題が修正されました。 この改善によって影響を受けるかどうかを判断するには、次のコンテンツをお読みください。 

#### <a name="scenario-1-encounter-the-unexpected-row-delimiter-issue"></a>シナリオ 1: 行区切り記号の予期しない問題が発生する

 次の条件に該当する場合は、影響を受けます。
 - 複数行の設定をソースとして True または CDM に設定して、区切りテキストを使用する。
 - 最初の行の文字数が 128 文字を超えている。 
 - データ ファイル内の行区切り記号が `\n` ではない。

 改善前は、区切りテキスト ファイルの解析に既定の行区切り記号 `\n` が予期せず使用される場合があります。これは、複数行の設定が True に設定されている場合に、行区切り記号の設定が無効になり、最初の 128 文字に基づいて行区切り記号が自動的に検出されるためです。 実際の行区切り記号が検出されなかった場合は、`\n` にフォールバックします。  

 改善後は、3 つの行区切り記号 (`\r`、`\n`、`\r\n`) のいずれも機能します。
 
 次の例は、改善後の 1 つのパイプラインの動作変更を示しています。

 **例**:<br/>
   次の列の場合:<br/>
    `C1, C2, {long first row}, C128\r\n `<br/>
    `V1, V2, {values………………….}, V128\r\n `<br/>
 
   改善前、`\r` は列の値に保持されます。 解析された列の結果は次のようになります。<br/>
   `C1 C2 {long first row} C128`**`\r`**<br/>
   `V1 V2 {values………………….} V128`**`\r`**<br/> 

   改善後、解析された列の結果は次のようになります。<br/>
   `C1 C2 {long first row} C128`<br/>
   `V1 V2 {values………………….} V128`<br/>
  
#### <a name="scenario-2-encounter-an-issue-of-incorrectly-reading-column-values-containing-rn"></a>シナリオ 2: '\r\n' を含む列の値を誤って読み取る問題が発生する

 次の条件に該当する場合は、影響を受けます。
 - 複数行の設定をソースとして True または CDM に設定して、区切りテキストを使用する。 
 - 行区切り記号が `\r\n` である。

 改善前、列の値を読み取るときに、その値の `\r\n` が誤って `\n` に置き換えられる場合があります。 

 改善後、列の値の `\r\n` は `\n` に置き換えられません。

 次の例は、改善後の 1 つのパイプラインの動作変更を示しています。
 
 **例**:<br/>
  
 次の列の場合:<br/>
  **`"A\r\n"`**`, B, C\r\n`<br/>

 改善前、解析された列の結果は次のようになります。<br/>
  **`A\n`**` B C`<br/>

 改善後、解析された列の結果は次のようになります。<br/>
  **`A\r\n`**` B C`<br/>  

#### <a name="scenario-3-encounter-an-issue-of-incorrectly-writing-column-values-containing-n"></a>シナリオ 3: '\n' を含む列の値を誤って書き込む問題が発生する

 次の条件に該当する場合は、影響を受けます。
 - 区切りテキストをシンクとして使用する。
 - 列の値に `\n` が含まれている。
 - [行区切り記号] を `\r\n` に設定します。
 
 改善前、列の値を書き込むときに、その値の `\n` が誤って `\r\n` に置き換えられる場合があります。 

 改善後、列の値の `\n` は `\r\n` に置き換えられません。
 
 次の例は、改善後の 1 つのパイプラインの動作変更を示しています。

 **例**:<br/>

 次の列の場合:<br/>
 **`A\n`**` B C`<br/>

 改善前、CSV シンクは次のようになります。<br/>
  **`"A\r\n"`**`, B, C\r\n` <br/>

 改善後、CSV シンクは次のようになります。<br/>
  **`"A\n"`**`, B, C\r\n`<br/>

#### <a name="scenario-4-encounter-an-issue-of-incorrectly-reading-empty-string-as-null"></a>シナリオ 4: 空の文字列を NULL として誤って読み取る問題が発生する
 
 次の条件に該当する場合は、影響を受けます。
 - 区切りテキストをソースとして使用する。 
 - NULL 値が空でない値に設定される。 
 - 列の値が空の文字列で、引用符で囲まれていない。 
 
 改善前、引用符で囲まれていない空の文字列の列の値は NULL として読み取られます。 

 改善後、空の文字列は NULL 値として解析されません。 
 
 次の例は、改善後の 1 つのパイプラインの動作変更を示しています。

 **例**:<br/>

 次の列の場合:<br/>
  `A, ,B, `<br/>

 改善前、解析された列の結果は次のようになります。<br/>
  `A null B null`<br/>

 改善後、解析された列の結果は次のようになります。<br/>
  `A "" (empty string) B "" (empty string)`<br/>

###  <a name="internal-server-errors"></a>内部サーバー エラー

内部サーバー エラーの原因となる可能性がある特定のシナリオを次に示します。

#### <a name="scenario-1-not-choosing-the-appropriate-compute-sizetype-and-other-factors"></a>シナリオ 1: コンピューティングの適切なサイズや種類、その他の要因が選択されていない

  データ フローの正常な実行は、コンピューティングのサイズと種類、処理するソースとシンクの数、パーティションの指定、関連する変換、データセットのサイズ、データの偏りなど、さまざまな要因に依存します。<br/>
  
  詳細なガイドについては、[Integration Runtime のパフォーマンス](concepts-integration-runtime-performance.md)に関するページを参照してください。

#### <a name="scenario-2-using-debug-sessions-with-parallel-activities"></a>シナリオ 2: 並列アクティビティでのデバッグ セッションの使用

  パイプラインに ForEach のようなコンストラクトが含まれるデータ フロー デバッグ セッションを使用して実行をトリガーすると、同じクラスターに複数の並列実行が送信される可能性があります。 この状況では、メモリ不足などのリソースの問題のため、クラスターの障害に関する問題が実行中に発生する可能性があります。<br/>
  
  変更を発行した後に、パイプライン アクティビティで定義されている適切な統合ランタイム構成を使用して実行を送信するには、 **[今すぐトリガー]** または **[デバッグ]**  >  **[アクティビティ ランタイムを使用]** を選択します。

#### <a name="scenario-3-transient-issues"></a>シナリオ 3: 一時的な問題

  実行に関係するマイクロサービスの一時的な問題により、実行が失敗する可能性があります。<br/>
  
  パイプライン アクティビティに再試行を構成すると、一時的な問題による問題が解決されることがあります。 詳細なガイドについては、「[アクティビティ ポリシー](concepts-pipelines-activities.md#activity-json)」を参照してください。

## <a name="next-steps"></a>次のステップ

トラブルシューティングの詳細について、次のリソースを参照してください。

- [一般的なマッピング データ フローのエラーとメッセージ](data-flow-troubleshoot-errors.md)
- [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
- [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
