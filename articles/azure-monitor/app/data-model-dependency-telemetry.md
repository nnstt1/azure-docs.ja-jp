---
title: Azure Monitor Application Insights 依存関係データ モデル
description: 依存関係テレメトリ用の Application Insights データ モデル
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 06e22c69e8d2d5187b2f78022302d10d12fe6a6c
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110098958"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>依存関係テレメトリ:Application Insights データ モデル

依存関係テレメトリ ([Application Insights](./app-insights-overview.md)) は、監視対象のコンポーネントと、SQL や HTTP エンドポイントのようなリモート コンポーネントとのやりとりを表します。

## <a name="name"></a>名前

この依存関係呼び出しで開始されたコマンドの名前。 小さなカーディナリティの値です。 例として、ストアド プロシージャ名と URL パス テンプレートがあります。

## <a name="id"></a>id

依存関係呼び出しインスタンスの識別子。 この依存関係の呼び出しに対応する要求テレメトリ項目の相関付けに使用されます。 詳細については、[相関付け](./correlation.md)に関するページを参照してください。

## <a name="data"></a>Data

この依存関係呼び出しによって開始されるコマンド。 例: すべてのクエリ パラメーターを使用する SQL ステートメントと HTTP URL。

## <a name="type"></a>Type

依存関係の種類の名前。 依存関係を論理的にグループ化するための小さなカーディナリティの値と、commandName や resultCode のようなフィールドの変換です。 例: SQL、Azure テーブル、HTTP。

## <a name="target"></a>移行先

依存関係呼び出しのターゲット サイト。 例: サーバー名、ホスト アドレス。 詳細については、[相関付け](./correlation.md)に関するページを参照してください。

## <a name="duration"></a>Duration

`DD.HH:MM:SS.MMMMMM` 形式の要求時間。 `1000` 日未満である必要があります。

## <a name="result-code"></a>結果コード

依存関係呼び出しの結果コード。 例: SQL エラー コードと HTTP 状態コード。

## <a name="success"></a>Success

呼び出しの成功または失敗を示す値。

## <a name="custom-properties"></a>カスタム プロパティ

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>カスタム測定値

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>次のステップ

- [.NET](./asp-net-dependencies.md) の依存関係追跡を設定します。
- [Java](java-2x-agent.md) の依存関係追跡を設定します。
- [カスタム依存関係テレメトリを記述します](./api-custom-events-metrics.md#trackdependency)。
- Application Insights の型とデータ モデルについては、[データ モデル](data-model.md)に関するページを参照してください。
- Application Insights でサポートされている[プラットフォーム](./platforms.md)を確認します。

