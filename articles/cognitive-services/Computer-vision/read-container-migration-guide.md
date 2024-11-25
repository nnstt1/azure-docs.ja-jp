---
title: Read v3.x コンテナーへの移行
titleSuffix: Azure Cognitive Services
description: Read v3 OCR コンテナーに移行する方法について説明します
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 09/28/2021
ms.author: aahi
ms.openlocfilehash: 5998ccdfa48a700ac8789954246f6b7bcec60543
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360509"
---
# <a name="migrate-to-the-read-v3x-ocr-containers"></a>Read v3. x OCR コンテナーへの移行

Computer Vision Read OCR コンテナーのバージョン 2 を使用している場合は、この記事でコンテナーのバージョン 3.x を使用するようにアプリケーションをアップグレードする方法について学習してください。 


## <a name="configuration-changes"></a>構成の変更

* `ReadEngineConfig:ResultExpirationPeriod` はサポート対象から除外されました。 Read OCR コンテナーには、要求に関連付けられた結果とメタデータを 48 時間後に削除するビルド済みの Cron ジョブが含まれています。
* `Cache:Redis:Configuration` はサポート対象から除外されました。 Cache は v3.x コンテナーでは使用されないため、設定する必要はありません。

## <a name="api-changes"></a>API の変更

Read v3.2 コンテナーは、Computer Vision API のバージョン 3 を使用し、次のエンドポイントを備えています。

* `/vision/v3.2-preview.1/read/analyzeResults/{operationId}`
* `/vision/v3.2-preview.1/read/analyze`
* `/vision/v3.2-preview.1/read/syncAnalyze`

クラウドベースの Read API のバージョン 3 を使用するようにアプリケーションを更新する方法の詳細については、[Computer Vision v3 REST API 移行ガイド](./upgrade-api-versions.md)を参照してください。 この情報は、コンテナーにも適用されます。 同期操作はコンテナーでのみサポートされています。

## <a name="memory-requirements"></a>メモリ要件

要件と推奨事項は、29 行におよぶ合計 803 文字のビジネス レターのスキャン画像 (8 MB) を使用した、1 秒あたり 1 つの要求というベンチマークに基づいています。 次の表に、各 Read OCR コンテナーに割り当てるリソースの最小値と推奨値を示します。

|コンテナー  |最小値 | 推奨  |
|---------|---------|------|
|Read 3.2-プレビュー | 8 コア、16 GB のメモリ         | 8 コア、24 GB のメモリ |

各コアは少なくとも 2.6 ギガヘルツ (GHz) 以上にする必要があります。

コアとメモリは、docker run コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。

## <a name="storage-implementations"></a>ストレージの実装

>[!NOTE]
> MongoDB は、3.x バージョンのコンテナーではサポートされなくなりました。 代わりに、コンテナーは Azure Storage およびオフラインのファイル システムをサポートします。

| 実装 |    必須のランタイム引数 |
|---------|---------|
|ファイル レベル (既定)    | 必須のランタイム引数はありません。 `/share` ディレクトリが使用されます。 |
|Azure BLOB    | `Storage:ObjectStore:AzureBlob:ConnectionString={AzureStorageConnectionString}` |

## <a name="queue-implementations"></a>キューの実装

コンテナーの v3.x では、現在 RabbitMQ はサポートされていません。 サポートされているバッキング実装は次のとおりです。

| 実装 | ランタイム引数 | 使用目的 |
|---------|---------|-------|
| メモリ内 (既定) | 必須のランタイム引数はありません。 | 開発とテスト |
| Azure キュー | `Queue:Azure:ConnectionString={AzureStorageConnectionString}` | 実稼働 |
| RabbitMQ    | 利用不可 | 実稼働 |

冗長性を高めるため、Read v3.x コンテナーでは、可視性タイマーを使用して、複数コンテナーのセットアップの実行時にクラッシュが発生した場合に要求を正常に処理できるようになっています。 

タイマーを `Queue:Azure:QueueVisibilityTimeoutInMilliseconds` に設定します。これにより、別のワーカーが処理しているときにメッセージが非表示になる時間を設定できます。 ページが冗長的に処理されないようにするには、タイムアウト期間を 120 秒に設定することをお勧めします。 既定値は 30 秒です。

| 既定値 | 推奨値 |
|---------|---------|
| 30000 |    120000 |


## <a name="next-steps"></a>次のステップ

* 構成設定について、[コンテナーの構成](computer-vision-resource-container-config.md)を確認する
* [OCR の概要](overview-ocr.md)に関するページを読み、印刷されたテキストと手書きのテキストの認識の詳細について確認する
* コンテナーでサポートされるメソッドの詳細について、[Read API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) を参照する。
* [よく寄せられる質問 (FAQ)](FAQ.yml) を参照して、Computer Vision 機能に関連する問題を解決する。
* さらに [Cognitive Services コンテナー](../cognitive-services-container-support.md)を使用する