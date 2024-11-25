---
title: Azure Stream Analytics からの Power BI 出力
description: この記事では、Azure Stream Analytics から Power BI にデータを出力する方法について説明します。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 7/7/2021
ms.openlocfilehash: d3f8bab950af095a457347b825966a0845ef36f6
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2021
ms.locfileid: "113506783"
---
# <a name="power-bi-output-from-azure-stream-analytics"></a>Azure Stream Analytics からの Power BI 出力

[Power BI](https://powerbi.microsoft.com/) を Stream Analytics ジョブの出力として使用して、分析結果の豊富な視覚化エクスペリエンスを提供できます。 この機能は、操作ダッシュボード、レポート生成、およびメトリックドリブン レポート作成に使用できます。

Stream Analytics からの Power BI 出力は、現在、Azure China 21Vianet および Azure Germany (T-Systems International) リージョンでは利用できません。

## <a name="output-configuration"></a>出力の構成

次の表に、ご自分の Power BI 出力を構成するためのプロパティの名前とその説明を示します。

| プロパティ名 | 説明 |
| --- | --- |
| 出力エイリアス |クエリの出力をこの Power BI 出力に出力するためにクエリ内で使用されるフレンドリ名を指定します。 |
| グループ ワークスペース |他の Power BI ユーザーとのデータの共有を有効にするには、ご自分の Power BI アカウント内のグループを選択できます。グループに書き込む必要がない場合は、 **[マイ ワークスペース]** を選択します。 既存のグループを更新するには、Power BI の認証を更新する必要があります。 |
| データセットの名前 |Power BI 出力で使用するデータセット名を指定します。 |
| テーブル名 |Power BI 出力のデータセットの下にテーブル名を入力します。 現在、Stream Analytics ジョブからの Power BI 出力では、1 つのデータセット内に 1 つのテーブルのみを保持できます。 |
| 接続の承認 | Power BI に出力設定を構成することを承認する必要があります。 この出力アクセスを Power BI ダッシュボードに付与した後は、ユーザー アカウントのパスワードを変更するか、ジョブ出力を削除するか、または Stream Analytics ジョブを削除することによってアクセスを取り消すことができます。 | 

Power BI の出力とダッシュボードを構成するチュートリアルについては、[Azure Stream Analytics と Power BI](stream-analytics-power-bi-dashboard.md) に関するチュートリアルを参照してください。

> [!NOTE]
> Power BI ダッシュボードにデータセットとテーブルを明示的に作成しないでください。 ジョブが開始され、このジョブによって出力が Power BI に流し込まれるときに、データセットとテーブルが自動的に設定されます。 ジョブ クエリで結果が生成されない場合、データセットとテーブルは作成されません。 また、この Stream Analytics ジョブで指定したものと同じ名前のデータセットとテーブルが Power BI に既に存在する場合は、既存のデータが上書きされます。
>

### <a name="create-a-schema"></a>スキーマの作成

Power BI データセットとテーブル スキーマがまだ存在しない場合は、ユーザーのために Azure Stream Analytics がデータセットとテーブル スキーマを作成します。 それ以外の場合、テーブルは新しい値で更新されます。 現時点では、1 つのデータセット内に存在できるテーブルは 1 つのみです。 

Power BI では、先入れ先出し (FIFO) のアイテム保持ポリシーが使用されます。 200,000 行に到達するまでデータがテーブルに収集されます。

> [!NOTE]
> 複数の出力を使用して同じデータセットに書き込むことは、問題がいくつか発生する可能性があるため、お勧めしません。 各出力によって、Power BI データセットを個別に作成する試みが行われますが、これにより、同じ名前を持つ複数のデータセットが生成される可能性があります。 また、出力に一貫性のあるスキーマが含まれていない場合は、データセットによって書き込みごとにスキーマが変更されるため、スキーマ変更要求の過多につながります。 これらの問題が回避された場合でも、複数の出力は、マージされた 1 つの出力よりもパフォーマンスが低下します。

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Stream Analytics から Power BI にデータ型を変換する

Azure Stream Analytics では、出力スキーマが変更されると、データ モデルが実行時に動的に更新されます。 列名の変更、列の型の変更、列の追加または削除は、すべて追跡されます。

次の表は、Power BI データセットとテーブルが存在しない場合の、[Stream Analytics データ型](/stream-analytics-query/data-types-azure-stream-analytics)から Power BI の [Entity Data Model (EDM) 型](/dotnet/framework/data/adonet/entity-data-model)へのデータ型の変換を示します。

Stream Analytics から | Power BI へ
-----|-----
bigint | Int64
nvarchar(max) | String
DATETIME | Datetime
float | Double
レコードの配列 | 文字列型、定数値 "IRecord" または "IArray"

### <a name="update-the-schema"></a>スキーマを更新する

Stream Analytics では、出力内の最初のイベント セットに基づいてデータ モデルのスキーマを推論します。 その後、データ モデルのスキーマは、必要に応じて、元のスキーマに適合しない可能性のある受信イベントに対応できるように更新されます。

複数行にわたってスキーマが動的に更新されるのを防ぐために、`SELECT *` クエリの使用を避ける必要があります。 パフォーマンスが低下する可能性があるだけでなく、結果を得るまでの所要時間が不確実になる恐れもあります。 Power BI ダッシュボードに表示する必要があるフィールドを厳密に選択してください。 また、データ値が、選択したデータ型に準拠している必要があります。

以前/現在 | Int64 | String | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | String | String | Double
String | String | String | String | String 
Datetime | String | String |  Datetime | String

## <a name="limitations-and-best-practices"></a>制限事項とベスト プラクティス
現在、Power BI は、およそ 1 秒に 1 回呼び出すことができます。 ストリーミング ビジュアルでは、15 KB のパケットがサポートされます。 これを超えると、ストリーミング ビジュアルは失敗します (ただし、プッシュは動作し続けます)。 このような制限から必然的に、Power BI は、Azure Stream Analytics で大幅なデータ負荷の低減が見られるケースへと落ち着きます。 1 秒あたりのデータ プッシュを 1 回以内に抑え、かつスループットの要件の範囲内にクエリを抑えるために、タンブリング ウィンドウやホッピング ウィンドウの使用をお勧めします。

出力バッチ サイズの詳細については、「[Power BI REST API の制限事項](/power-bi/developer/automation/api-rest-api-limitations)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [マネージド ID を使用して、Power BI に対して Azure Stream Analytics ジョブを認証する](powerbi-output-managed-identity.md)
* [クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)
