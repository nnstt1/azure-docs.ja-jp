---
title: 空間分析操作
titleSuffix: Azure Cognitive Services
description: 空間分析操作。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/08/2021
ms.author: pafarley
ms.custom: ignite-fall-2021
ms.openlocfilehash: 845c2e7080ccc56d49e0d930d041c55f88710aab
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131033496"
---
# <a name="spatial-analysis-operations"></a>空間分析操作

空間分析により、カメラ デバイスから取得したリアルタイム ストリーミング ビデオの分析が可能になります。 空間分析の操作によって、構成したカメラ デバイスごとに Azure IoT Hub のインスタンスに送信される JSON メッセージの出力ストリームが生成されます。 

空間分析コンテナーは、次の操作を実装します。

| 操作識別子| 説明|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount | カメラの視野内の指定されたゾーン内の人数をカウントします。 PersonCount によって正確な合計を記録するには、ゾーンを 1 台のカメラで完全にカバーする必要があります。 <br> 初期 _personCountEvent_ イベントを生成し、人数が変わると、_personCountEvent_ イベントを生成します。  |
| cognitiveservices.vision.spatialanalysis-personcrossingline | 人がカメラの視野内の指定されたラインを越えた時点を追跡します。 <br>人がラインを越えると、_personLineEvent_ イベントを生成し、方向情報を提供します。 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon | 人がゾーンに出入りするときに _personZoneEnterExitEvent_ イベントを発行し、交差したゾーンの番号付き側に方向情報を提供します。 人がゾーンを出るときに _personZoneDwellTimeEvent_ を発行し、方向情報と人がゾーン内で過ごしたミリ秒数を提供します。 |
| cognitiveservices.vision.spatialanalysis-persondistance | 人が距離ルールに違反した時点を追跡します。 <br> 各距離違反が発生した場所で _personDistanceEvent_ を定期的に生成します。 |
| cognitiveservices.vision.spatialanalysis | 上記にあるすべてのシナリオを実行する場合に使用できる汎用的な操作。 このオプションは、同じカメラで複数のシナリオを実行したり、システム リソース (GPU など) をより効率的に使用したりする場合に便利です。 |

上記のすべての操作は、処理中のビデオ フレームを視覚化する機能を備えた `.debug` バージョンでも使用できます。 ビデオ フレームとイベントの視覚化を有効にするには、ホスト コンピューターで `xhost +` を実行する必要があります。

操作識別子| 説明
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.debug | カメラの視野内の指定されたゾーン内の人数をカウントします。 <br> 初期 _personCountEvent_ イベントを生成し、人数が変わると、_personCountEvent_ イベントを生成します。  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.debug | 人がカメラの視野内の指定されたラインを越えた時点を追跡します。 <br>人がラインを越えると、_personLineEvent_ イベントを生成し、方向情報を提供します。 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.debug | 人がゾーンに出入りするときに _personZoneEnterExitEvent_ イベントを発行し、交差したゾーンの番号付き側に方向情報を提供します。 人がゾーンを出るときに _personZoneDwellTimeEvent_ を発行し、方向情報と人がゾーン内で過ごしたミリ秒数を提供します。 |
| cognitiveservices.vision.spatialanalysis-persondistance.debug | 人が距離ルールに違反した時点を追跡します。 <br> 各距離違反が発生した場所で _personDistanceEvent_ を定期的に生成します。 |
| cognitiveservices.vision.spatialanalysis.debug | 上記にあるすべてのシナリオを実行する場合に使用できる汎用的な操作。 このオプションは、同じカメラで複数のシナリオを実行したり、システム リソース (GPU など) をより効率的に使用したりする場合に便利です。 |

[Live Video Analytics](../../azure-video-analyzer/video-analyzer-docs/overview.md) を使用すると、空間分析をビデオ AI モジュールとして実行することもできます。 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| 操作識別子| 説明|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics | カメラの視野内の指定されたゾーン内の人数をカウントします。 <br> 初期 _personCountEvent_ イベントを生成し、人数が変わると、_personCountEvent_ イベントを生成します。  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.livevideoanalytics | 人がカメラの視野内の指定されたラインを越えた時点を追跡します。 <br>人がラインを越えると、_personLineEvent_ イベントを生成し、方向情報を提供します。 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics | 人がゾーンに出入りするときに _personZoneEnterExitEvent_ イベントを発行し、交差したゾーンの番号付き側に方向情報を提供します。 人がゾーンを出るときに _personZoneDwellTimeEvent_ を発行し、方向情報と人がゾーン内で過ごしたミリ秒数を提供します。  |
| cognitiveservices.vision.spatialanalysis-persondistance.livevideoanalytics | 人が距離ルールに違反した時点を追跡します。 <br> 各距離違反が発生した場所で _personDistanceEvent_ を定期的に生成します。 |
| cognitiveservices.vision.spatialanalysis.livevideoanalytics | 上記にあるすべてのシナリオを実行する場合に使用できる汎用的な操作。 このオプションは、同じカメラで複数のシナリオを実行したり、システム リソース (GPU など) をより効率的に使用したりする場合に便利です。 |

Live Video Analytics の操作は、処理中のビデオ フレームを視覚化する機能を備えた `.debug` バージョン (例: cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics.debug) でも使用できます。 ビデオ フレームとイベントの視覚化を有効にするには、ホスト コンピューターで `xhost +` を実行する必要があります。

> [!IMPORTANT]
> コンピューター ビジョン AI モデルは、ビデオ映像で人の存在を検出してその位置を特定し、人体を囲む境界ボックスを使用して出力します。 これらの AI モデルは、個人の ID や人口統計情報の検出を試みることはありません。

これらの各空間分析操作に必要なパラメーターを次に示します。

| 操作のパラメーター| 説明|
|---------|---------|
| 操作 ID | 上記の表の操作識別子。|
| enabled | ブール値: true または false。|
| VIDEO_URL| カメラ デバイスの RTSP URL (例: `rtsp://username:password@url`)。 空間分析では、RTSP、http、または mp4 のいずれかを使用して H.264 でエンコードされたストリームがサポートされています。 Video_URL は、AES 暗号化を使用して難読化された base64 文字列値として指定できます。ビデオの URL が難読化されている場合は、環境変数として `KEY_ENV` と `IV_ENV` を指定する必要があります。 キーと暗号化を生成するためのサンプル ユーティリティについては、[こちら](/dotnet/api/system.security.cryptography.aesmanaged)を参照してください。 |
| VIDEO_SOURCE_ID | カメラ デバイスまたはビデオ ストリームのフレンドリ名。 これは、イベントの JSON 出力と共に返されます。|
| VIDEO_IS_LIVE| カメラ デバイスの場合は true、録画されたビデオの場合は false。|
| VIDEO_DECODE_GPU_INDEX| ビデオ フレームをデコードする GPU。 既定では 0 です。 `DETECTOR_NODE_CONFIG`、`CAMERACALIBRATOR_NODE_CONFIG` などの他のノード構成の `gpu_index` と同じである必要があります。|
| INPUT_VIDEO_WIDTH | ビデオまたはストリームのフレーム幅を入力します (例: 1920)。 これは省略可能なフィールドですが、指定されている場合、フレームは縦横比を維持しながらこのディメンションに合わせてスケーリングされます。|
| DETECTOR_NODE_CONFIG | 検出ノードを実行する GPU を示す JSON。 これは、`"{ \"gpu_index\": 0 }",` という形式になっている必要があります。|
| TRACKER_NODE_CONFIG | トラッカー ノードで速度を計算するかどうかを示す JSON です。 これは、`"{ \"enable_speed\": true }",` という形式になっている必要があります。|
| CAMERA_CONFIG | 複数のカメラ用に調整されたカメラ パラメーターを示す JSON です。 使用したスキルに調整が必要であり、カメラ パラメーターが既にある場合は、この構成を使用して直接指定することができます。 `"{ \"cameras\": [{\"source_id\": \"endcomputer.0.persondistancegraph.detector+end_computer1\", \"camera_height\": 13.105561256408691, \"camera_focal_length\": 297.60003662109375, \"camera_tiltup_angle\": 0.9738943576812744}] }"` は次の形式にする必要があります。`source_id` は、各カメラを識別するために使用されます。 発行されたイベントの `source_info` から取得できます。 このメソッドは、`DETECTOR_NODE_CONFIG` で `do_calibration=false` の場合にのみ有効になります。|
| CAMERACALIBRATOR_NODE_CONFIG | カメラ キャリブレーター ノードを実行する GPU と、調整を使用するかどうかを示す JSON です。 これは、`"{ \"gpu_index\": 0, \"do_calibration\": true, \"enable_orientation\": true}",` という形式になっている必要があります。|
| CALIBRATION_CONFIG | カメラの調整方法を制御するパラメーターを示す JSON です。 これは、`"{\"enable_recalibration\": true, \"quality_check_frequency_seconds\": 86400}",` という形式になっている必要があります。|
| SPACEANALYTICS_CONFIG | 後述するゾーンとラインの JSON 構成。|
| ENABLE_FACE_MASK_CLASSIFIER | `True` を使用すると、ビデオ ストリームでフェイス マスクを着用している人を検出できるようになり、`False` を使用すると無効になります。 既定では、この構成は無効です。 フェイス マスクの検出には、入力ビデオ幅パラメーターを 1920 `"INPUT_VIDEO_WIDTH": 1920` にする必要があります。 検出された人物がカメラに向いていないか、カメラから離れすぎている場合、フェイス マスク属性は返されません。 詳細については、[カメラの配置](spatial-analysis-camera-placement.md)に関するガイドを参照してください。 |

### <a name="detector-node-parameter-settings"></a>検出ノードのパラメーター設定
すべての空間分析操作の DETECTOR_NODE_CONFIG パラメーターの例を次に示します。

```json
{
"gpu_index": 0,
"enable_breakpad": false
}
```

| 名前 | 型| 説明|
|---------|---------|---------|
| `gpu_index` | string| この操作が実行される GPU インデックス。|
| `enable_breakpad`| [bool] | デバッグ用のクラッシュ ダンプを生成するために使用される breakpad を有効にするかどうかを示します。 既定値は `false` です。 `true` に設定した場合は、コンテナー `createOptions` の `HostConfig` 部分に `"CapAdd": ["SYS_PTRACE"]` も追加する必要があります。 既定では、クラッシュ ダンプは [RealTimePersonTracking](https://appcenter.ms/orgs/Microsoft-Organization/apps/RealTimePersonTracking/crashes/errors?version=&appBuild=&period=last90Days&status=&errorType=all&sortCol=lastError&sortDir=desc) AppCenter アプリにアップロードされます。クラッシュ ダンプを独自の AppCenter アプリにアップロードする場合は、環境変数 `RTPT_APPCENTER_APP_SECRET` をアプリのアプリ シークレットでオーバーライドできます。|

### <a name="camera-calibration-node-parameter-settings"></a>カメラ調整ノードのパラメーター設定
すべての空間分析操作の `CAMERACALIBRATOR_NODE_CONFIG` パラメーターの例を次に示します。

```json
{
  "gpu_index": 0,
  "do_calibration": true,
  "enable_breakpad": false,
  "enable_orientation": true
}
```

| 名前 | 型 | 説明 |
|---------|---------|---------|
| `do_calibration` | string | 調整がオンになっていることを示します。 **cognitiveservices.vision.spatialanalysis-persondistance** が正しく機能するには、`do_calibration` が true である必要があります。 `do_calibration` は既定で `True` に設定されます。 |
| `enable_breakpad`| [bool] | デバッグ用のクラッシュ ダンプを生成するために使用される breakpad を有効にするかどうかを示します。 既定値は `false` です。 `true` に設定した場合は、コンテナー `createOptions` の `HostConfig` 部分に `"CapAdd": ["SYS_PTRACE"]` も追加する必要があります。 既定では、クラッシュ ダンプは [RealTimePersonTracking](https://appcenter.ms/orgs/Microsoft-Organization/apps/RealTimePersonTracking/crashes/errors?version=&appBuild=&period=last90Days&status=&errorType=all&sortCol=lastError&sortDir=desc) AppCenter アプリにアップロードされます。クラッシュ ダンプを独自の AppCenter アプリにアップロードする場合は、環境変数 `RTPT_APPCENTER_APP_SECRET` をアプリのアプリ シークレットでオーバーライドできます。
| `enable_orientation` | bool | 検出された人の向きを計算するかどうかを示します。 `enable_orientation` は既定で `True` に設定されます。 |

### <a name="calibration-config"></a>調整の構成

すべての空間分析操作の `CALIBRATION_CONFIG` パラメーターの例を次に示します。

```json
{
  "enable_recalibration": true,
  "calibration_quality_check_frequency_seconds": 86400,
  "calibration_quality_check_sample_collect_frequency_seconds": 300,
  "calibration_quality_check_one_round_sample_collect_num": 10,
  "calibration_quality_check_queue_max_size": 1000,
  "calibration_event_frequency_seconds": -1
}
```

| 名前 | 型| 説明|
|---------|---------|---------|
| `enable_recalibration` | [bool] | 自動再調整がオンになっているかどうかを示します。 既定値は `true` です。|
| `calibration_quality_check_frequency_seconds` | INT | 再調整が必要かどうかを判断するための、各品質チェック間の最小秒数。 既定値は `86400` (24 時間) です。 `enable_recalibration=True` の場合のみ使用されます。|
| `calibration_quality_check_sample_collect_frequency_seconds` | INT | 再調整と品質チェックのために新しいデータ サンプルを収集する間の最小秒数。 既定値は `300` (5 分間) です。 `enable_recalibration=True` の場合のみ使用されます。|
| `calibration_quality_check_one_round_sample_collect_num` | INT | サンプル コレクションのラウンドごとに収集する新しいデータ サンプルの最小数。 既定値は `10` です。 `enable_recalibration=True` の場合のみ使用されます。|
| `calibration_quality_check_queue_max_size` | INT | カメラ モデルの調整時に格納するデータ サンプルの最大数。 既定値は `1000` です。 `enable_recalibration=True` の場合のみ使用されます。|
| `calibration_event_frequency_seconds` | INT | カメラ調整イベントの出力頻度 (秒)。 値が `-1` の場合、カメラの調整情報が変更されない限り、カメラの調整は送信されません。 既定値は `-1` です。|

### <a name="camera-calibration-output"></a>カメラ調整の出力

これは、カメラ調整 (有効な場合) からの出力例です。 省略記号は、リスト内に同じタイプのオブジェクトがさらにあることを示しています。

```json
{
  "type": "cameraCalibrationEvent",
  "sourceInfo": {
    "id": "camera1",
    "timestamp": "2021-04-20T21:15:59.100Z",
    "width": 640,
    "height": 360,
    "frameId": 531,
    "cameraCalibrationInfo": {
      "status": "Calibrated",
      "cameraHeight": 13.294151306152344,
      "focalLength": 372.0000305175781,
      "tiltupAngle": 0.9581864476203918,
      "lastCalibratedTime": "2021-04-20T21:15:59.058"
    }
  },
  "zonePlacementInfo": {
    "optimalZoneRegion": {
      "type": "POLYGON",
       "points": [
        {
          "x": 0.8403755868544601,
          "y": 0.5515320334261838
        },
        {
          "x": 0.15805946791862285,
          "y": 0.5487465181058496
        },
        ...
      ],
      "name": "optimal_zone_region"
    },
    "fairZoneRegion": {
      "type": "POLYGON",
      "points": [
        {
          "x": 0.7871674491392802,
          "y": 0.7437325905292479
        },
        {
          "x": 0.22065727699530516,
          "y": 0.7325905292479109
        },
        ...
      ],
      "name": "fair_zone_region"
    },
    "uniformlySpacedPersonBoundingBoxes": [
      {
        "type": "RECTANGLE",
        "points": [
          {
            "x": 0.0297339593114241,
            "y": 0.0807799442896936
          },
          {
            "x": 0.10015649452269171,
            "y": 0.2757660167130919
          }
        ]
      },
      ...
    ],
    "personBoundingBoxGroundPoints": [
      {
        "x": -22.944068908691406,
        "y": 31.487680435180664
      },
      ...
    ]
  }
}
```

`source_info` の詳細については、[空間分析操作の出力](#spatial-analysis-operation-output)をご覧ください。

| ZonePlacementInfo のフィールド名 | 型| 説明|
|---------|---------|---------|
| `optimalZonePolygon` | object| 操作の実線またはゾーンを配置して最適な結果を得られるカメラ画像内の多角形。 <br/> 各値のペアは、多角形の頂点の x、y を表します。 多角形は、人の追跡または人数のカウントを行う領域を表します。多角形ポイントは、正規化された座標 (0-1) に基づいています。左上隅が (0.0, 0.0)、右下隅が (1.0, 1.0) になります。|
| `fairZonePolygon` | object| 操作の実線またはゾーンを配置して良い結果を得られるが、最適な結果は得られない可能性のあるカメラ画像内の多角形。 <br/> 内容の詳細な説明については、上記の `optimalZonePolygon` を参照してください。 |
| `uniformlySpacedPersonBoundingBoxes` | list | 実際の空間で均一に分散されたカメラ画像内の人の境界ボックスのリスト。 値は、正規化された座標 (0-1) に基づいています。|
| `personBoundingBoxGroundPoints` | list | カメラに相対するフロア平面上の座標の一覧。 各座標は、同じインデックスを持つ `uniformlySpacedPersonBoundingBoxes` の境界ボックスの右下に対応します。 <br/> フロア平面上の座標を計算する方法について詳しくは、[cognitiveservices.vision.spatialanalysis-persondistance の AI 分析情報の JSON 形式](#json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights)セクションの `centerGroundPoint` フィールドを参照してください。 |

ビデオ フレームで視覚化されたゾーン配置情報の出力例: ![ゾーン配置情報の視覚化](./media/spatial-analysis/zone-placement-info-visualization.png)

ゾーンの配置情報には構成に関する提案が示されていますが、最適な結果を得るには、[カメラ構成](#camera-configuration)のガイドラインに従う必要があります。

### <a name="speed-parameter-settings"></a>速度パラメーターの設定
トラッカー ノードのパラメーター設定を使用して、速度計算を構成できます。
```
{
"enable_speed": true,
}
```
| Name | 型| 説明|
|---------|---------|---------|
| `enable_speed` | bool | 検出された人の速度を計算するかどうかを示します。 `enable_speed` は既定で `True` に設定されます。 速度と向きの両方に最適な推定値を設定することを強くお勧めします。 |

## <a name="spatial-analysis-operations-configuration-and-output"></a>空間分析操作の構成と出力

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>cognitiveservices.vision.spatialanalysis-personcount のゾーン構成

これは、ゾーンを構成する SPACEANALYTICS_CONFIG パラメーターの JSON 入力の例です。 この操作には複数のゾーンを構成できます。

```json
{
  "zones": [
    {
      "name": "lobbycamera",
      "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
      "events": [
        {
          "type": "count",
          "config": {
            "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ]
}
```

| 名前 | 型| 説明|
|---------|---------|---------|
| `zones` | list| ゾーンのリスト。 |
| `name` | string| このゾーンのフレンドリ名。|
| `polygon` | list| 各値のペアは、多角形の頂点の x、y を表します。 多角形は、人の追跡または人数のカウントを行う領域を表します。多角形ポイントは、正規化された座標 (0-1) に基づいています。左上隅が (0.0, 0.0)、右下隅が (1.0, 1.0) になります。   
| `threshold` | float| その人がゾーン内のこのピクセル数よりも大きい場合にイベントが送信されます。 |
| `type` | string| **cognitiveservices.vision.spatialanalysis-personcount** の場合、これは `count` である必要があります。|
| `trigger` | string| イベントを送信するためのトリガーの種類。 サポートされている値は、`event` (カウントが変わったときにイベントを送信する場合)、または `interval` (カウントが変わったかどうかに関係なく、イベントを定期的に送信する場合) です。
| `output_frequency` | INT | イベントが送信される頻度。 `output_frequency` = X の場合、X おきにイベントが送信されます。たとえば、 `output_frequency` = 2 は、1 つおきにイベントが送信されることを意味します。 `output_frequency` は、`event` と `interval` の両方に適用されます。 |
| `focus` | string| イベントの計算に使用される人の境界ボックス内のポイントの場所。 フォーカスの値は、`footprint` (人の占有領域)、`bottom_center` (人の境界ボックスの下部中央)、`center` (人の境界ボックスの中央) にすることができます。|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>cognitiveservices.vision.spatialanalysis-personcrossingline のライン構成

これは、ラインを構成する SPACEANALYTICS_CONFIG パラメーターの JSON 入力の例です。 この操作には、交差する複数のラインを構成できます。

```json
{
   "lines": [
       {
           "name": "doorcamera",
           "line": {
               "start": {
                   "x": 0,
                   "y": 0.5
               },
               "end": {
                   "x": 1,
                   "y": 0.5
               }
           },
           "events": [
               {
                   "type": "linecrossing",
                   "config": {
                       "trigger": "event",
                       "threshold": 16.00,
                       "focus": "footprint"
                   }
               }
           ]
       }
   ]
}
```

| 名前 | 型| 説明|
|---------|---------|---------|
| `lines` | list| ラインのリスト。|
| `name` | string| このラインのフレンドリ名。|
| `line` | list| ラインの定義。 これは、"入る" と "出る" を理解できるようにするための方向線です。|
| `start` | 値のペア| ラインの始点の x、y 座標。 浮動小数点値は、左上隅を基準とした頂点の位置を表します。 x、y の絶対値を計算するには、これらの値とフレーム サイズを乗算します。 |
| `end` | 値のペア| ラインの終点の x、y 座標。 浮動小数点値は、左上隅を基準とした頂点の位置を表します。 x、y の絶対値を計算するには、これらの値とフレーム サイズを乗算します。 |
| `threshold` | float| その人がゾーン内のこのピクセル数よりも大きい場合にイベントが送信されます。 既定値は 16 です。 これは、最大の精度を実現するために推奨される値です。 |
| `type` | string| **cognitiveservices.vision.spatialanalysis-personcrossingline** の場合、これは `linecrossing` である必要があります。|
|`trigger`|string|イベントを送信するためのトリガーの種類。<br>サポートされている値: "event": だれかがラインを越えたときに発生します。|
| `focus` | string| イベントの計算に使用される人の境界ボックス内のポイントの場所。 フォーカスの値は、`footprint` (人の占有領域)、`bottom_center` (人の境界ボックスの下部中央)、`center` (人の境界ボックスの中央) にすることができます。 既定値は footprint です。|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>cognitiveservices.vision.spatialanalysis-personcrossingpolygon のゾーン構成

これは、ゾーンを構成する SPACEANALYTICS_CONFIG パラメーターの JSON 入力の例です。 この操作には複数のゾーンを構成できます。

```json
{
"zones":[
   {
       "name": "queuecamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonecrossing",
           "config":{
               "trigger": "event",
               "threshold": 48.00,
               "focus": "footprint"
               }
           }]
   },
   {
       "name": "queuecamera1",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonedwelltime",
           "config":{
               "trigger": "event",
               "threshold": 16.00,
               "focus": "footprint"
               }
           }]
   }]
}
```

| 名前 | 型| 説明|
|---------|---------|---------|
| `zones` | list| ゾーンのリスト。 |
| `name` | string| このゾーンのフレンドリ名。|
| `polygon` | list| 各値のペアは、多角形の頂点の x、y を表します。 多角形は、人の追跡または人数のカウントを行う領域を表します。 浮動小数点値は、左上隅を基準とした頂点の位置を表します。 x、y の絶対値を計算するには、これらの値とフレーム サイズを乗算します。 
| `target_side` | INT| `polygon` によって定義されたゾーンの辺を指定します。これにより、ゾーン内で人が辺に向いている時間を測定します。 'dwellTimeForTargetSide' では、その推定時間が出力されます。 各辺は、ゾーンを表す多角形の 2 つの頂点の間の番号付きのエッジです。 たとえば、多角形の最初の 2 つの頂点間のエッジは、最初の辺である 'side'=1 を表します。 `target_side` の値の範囲は `[0,N-1]` です。`N` は `polygon` の辺の数です。 これフィールドは必須ではありません。  |
| `threshold` | float| その人がゾーン内のこのピクセル数よりも大きい場合にイベントが送信されます。 既定値は、type が zonecrossing の場合は 48、time が DwellTime の場合は 16 です。 これらは、最大の精度を実現するために推奨される値です。  |
| `type` | string| **cognitiveservices.vision.spatialanalysis-personcrossingpolygon** の場合、これは `zonecrossing` または `zonedwelltime` である必要があります。|
| `trigger`|string|イベントを送信するためのトリガーの種類。<br>サポートされている値: "event": だれかがゾーンに入ったときまたはゾーンから出たときに発生します。|
| `focus` | string| イベントの計算に使用される人の境界ボックス内のポイントの場所。 フォーカスの値は、`footprint` (人の占有領域)、`bottom_center` (人の境界ボックスの下部中央)、`center` (人の境界ボックスの中央) にすることができます。 既定値は footprint です。|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>cognitiveservices.vision.spatialanalysis-persondistance のゾーン構成

これは、**cognitiveservices.vision.spatialanalysis-persondistance** のゾーンを構成する SPACEANALYTICS_CONFIG パラメーターの JSON 入力の例です。 この操作には複数のゾーンを構成できます。

```json
{
"zones":[{
   "name": "lobbycamera",
   "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
   "events":[{
       "type": "persondistance",
       "config":{
           "trigger": "event",
           "output_frequency":1,
           "minimum_distance_threshold":6.0,
           "maximum_distance_threshold":35.0,
        "aggregation_method": "average"
           "threshold": 16.00,
           "focus": "footprint"
                   }
          }]
   }]
}
```

| 名前 | 型| 説明|
|---------|---------|---------|
| `zones` | list| ゾーンのリスト。 |
| `name` | string| このゾーンのフレンドリ名。|
| `polygon` | list| 各値のペアは、多角形の頂点の x、y を表します。 多角形は人数をカウントする領域を表し、人と人との距離が測定されます。 浮動小数点値は、左上隅を基準とした頂点の位置を表します。 x、y の絶対値を計算するには、これらの値とフレーム サイズを乗算します。 
| `threshold` | float| その人がゾーン内のこのピクセル数よりも大きい場合にイベントが送信されます。 |
| `type` | string| **cognitiveservices.vision.spatialanalysis-persondistance** の場合、これは `persondistance` である必要があります。|
| `trigger` | string| イベントを送信するためのトリガーの種類。 サポートされている値は、`event` (カウントが変わったときにイベントを送信する場合)、または `interval` (カウントが変わったかどうかに関係なく、イベントを定期的に送信する場合) です。
| `output_frequency` | INT | イベントが送信される頻度。 `output_frequency` = X の場合、X おきにイベントが送信されます。たとえば、 `output_frequency` = 2 は、1 つおきにイベントが送信されることを意味します。 `output_frequency` は、`event` と `interval` の両方に適用されます。|
| `minimum_distance_threshold` | float| 人と人との距離がその距離よりも近いときに "TooClose" イベントをトリガーする距離 (フィート単位)。|
| `maximum_distance_threshold` | float| 人と人との距離がその距離よりも離れているときに "TooFar" イベントをトリガーする距離 (フィート単位)。|
| `aggregation_method` | string| 個人距離の結果を集計するためのメソッド。 aggregation_method は `mode` と `average` の両方に適用できます。|
| `focus` | string| イベントの計算に使用される人の境界ボックス内のポイントの場所。 フォーカスの値は、`footprint` (人の占有領域)、`bottom_center` (人の境界ボックスの下部中央)、`center` (人の境界ボックスの中央) にすることができます。|

### <a name="configuration-for-cognitiveservicesvisionspatialanalysis"></a>cognitiveservices.vision.spatialanalysis の構成
これは、**cognitiveservices.vision.spatialanalysis** のラインとゾーンを構成する SPACEANALYTICS_CONFIG パラメーターの JSON 入力の例です。 この操作では複数のラインやゾーンを構成でき、各ラインやゾーンには異なるイベントを持たせることができます。

```json
{
  "lines": [
    {
      "name": "doorcamera",
      "line": {
        "start": {
          "x": 0,
          "y": 0.5
        },
        "end": {
          "x": 1,
          "y": 0.5
        }
      },
      "events": [
        {
          "type": "linecrossing",
          "config": {
            "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ],
  "zones": [
    {
      "name": "lobbycamera",
      "polygon": [[0.3, 0.3],[0.3, 0.9],[0.6, 0.9],[0.6, 0.3],[0.3, 0.3]],
      "events": [
        {
          "type": "persondistance",
          "config": {
            "trigger": "event",
            "output_frequency": 1,
            "minimum_distance_threshold": 6.0,
            "maximum_distance_threshold": 35.0,
            "threshold": 16.00,
            "focus": "footprint"
          }
        },
        {
          "type": "count",
          "config": {
            "trigger": "event",
            "output_frequency": 1,
            "threshold": 16.00,
            "focus": "footprint"
          }
        },
        {
          "type": "zonecrossing",
          "config": {
            "threshold": 48.00,
            "focus": "footprint"
          }
        },
        {
          "type": "zonedwelltime",
          "config": {
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ]
}
```

## <a name="camera-configuration"></a>カメラの構成

ゾーンとラインを構成する方法の詳細については、[カメラの配置](spatial-analysis-camera-placement.md)に関するガイドラインを参照してください。

## <a name="spatial-analysis-operation-output"></a>空間分析操作の出力

各操作のイベントは、JSON 形式で Azure IoT Hub に送信されます。

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>cognitiveservices.vision.spatialanalysis-personcount の AI 分析情報の JSON 形式

この操作によって出力されるイベントのサンプル JSON。

```json
{
    "events": [
        {
            "id": "b013c2059577418caa826844223bb50b",
            "type": "personCountEvent",
            "detectionIds": [
                "bc796b0fc2534bc59f13138af3dd7027",
                "60add228e5274158897c135905b5a019"
            ],
            "properties": {
                "personCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:57.224Z",
        "width": 608,
        "height": 342,
        "frameId": "1400",
        "cameraCalibrationInfo": {
            "status": "Calibrated",
            "cameraHeight": 10.306597709655762,
            "focalLength": 385.3199462890625,
            "tiltupAngle": 1.0969393253326416
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "bc796b0fc2534bc59f13138af3dd7027",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.612683747944079,
                        "y": 0.25340268765276636
                    },
                    {
                        "x": 0.7185954043739721,
                        "y": 0.6425260577285499
                    }
                ]
            },
            "confidence": 0.9559211134910583,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadata": {
            "attributes": {
                "face_mask": 0.99
            }
        }
        },
        {
            "type": "person",
            "id": "60add228e5274158897c135905b5a019",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.22326200886776573,
                        "y": 0.17830915618361087
                    },
                    {
                        "x": 0.34922296122500773,
                        "y": 0.6297955429344847
                    }
                ]
            },
            "confidence": 0.9389744400978088,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadata":{
            "attributes": {
            "face_nomask": 0.99
            }
            }
       }
    ],
    "schemaVersion": "1.0"
}
```

| イベント フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| イベント ID|
| `type` | string| イベントの種類|
| `detectionsId` | array| このイベントをトリガーした人検出の一意識別子のサイズ 1 の配列|
| `properties` | collection| 値のコレクション|
| `trackinId` | string| 検出された人の一意識別子|
| `zone` | string | 越えられたゾーンを表す多角形の "name" フィールド|
| `trigger` | string| SPACEANALYTICS_CONFIG の `trigger` の値に応じて、トリガーの種類は "event" または "interval" になります。|

| Detections フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| 検出 ID|
| `type` | string| 検出の種類|
| `region` | collection| 値のコレクション|
| `type` | string| 領域の種類|
| `points` | collection| 領域の種類が RECTANGLE の場合の、左上と右下のポイント |
| `confidence` | float| アルゴリズムの信頼度|
| `face_mask` | float | 範囲 (0-1) の属性信頼度値は、検出された人がフェイス マスクを着用していることを示します |
| `face_nomask` | float | 範囲 (0-1) の属性信頼度値は、検出された人がフェイス マスクを着用して **いない** ことを示します |

| SourceInfo フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| Camera ID (カメラ ID)|
| `timestamp` | 日付| JSON ペイロードが生成された UTC 日付|
| `width` | INT | ビデオ フレームの幅|
| `height` | INT | ビデオ フレームの高さ|
| `frameId` | INT | フレーム識別子|
| `cameraCallibrationInfo` | collection | 値のコレクション|
| `status` | string | `state[;progress description]` の形式での調整の状態。 状態は、`Calibrating`、`Recalibrating` (再調整が有効な場合)、または `Calibrated` になります。 進行状況の説明部分は、現在の調整プロセスの進行状況を示すために使用される `Calibrating` および `Recalibrating` 状態の場合にのみ有効です。|
| `cameraHeight` | float | 地面からのカメラの高さ (フィート単位)。 これは自動調整から推論されます。 |
| `focalLength` | float | カメラの焦点距離 (ピクセル単位)。 これは自動調整から推論されます。 |
| `tiltUpAngle` | float | 垂直線からのカメラの傾斜角度。 これは自動調整から推論されます。|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>cognitiveservices.vision.spatialanalysis-personcrossingline の AI 分析情報の JSON 形式

この操作によって出力される検出のサンプル JSON。

```json
{
    "events": [
        {
            "id": "3733eb36935e4d73800a9cf36185d5a2",
            "type": "personLineEvent",
            "detectionIds": [
                "90d55bfc64c54bfd98226697ad8445ca"
            ],
            "properties": {
                "trackingId": "90d55bfc64c54bfd98226697ad8445ca",
                "status": "CrossLeft"
            },
            "zone": "doorcamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:53.261Z",
        "width": 608,
        "height": 342,
        "frameId": "1340",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "90d55bfc64c54bfd98226697ad8445ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.491627341822574,
                        "y": 0.2385801348769874
                    },
                    {
                        "x": 0.588894994635331,
                        "y": 0.6395559924387793
                    }
                ]
            },
            "confidence": 0.9005028605461121,
            "metadata": {
            "attributes": {
                "face_mask": 0.99
            }
        }
        }
    ],
    "schemaVersion": "1.0"
}
```
| イベント フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| イベント ID|
| `type` | string| イベントの種類|
| `detectionsId` | array| このイベントをトリガーした人検出の一意識別子のサイズ 1 の配列|
| `properties` | collection| 値のコレクション|
| `trackinId` | string| 検出された人の一意識別子|
| `status` | string| ラインを越える方向 ("CrossLeft" または "CrossRight") 方向は、そのラインの "終わり" に向かって "開始" 位置に立つことを想像することに基づいています。 CrossRight は左から右に交差しています。 CrossLeft は右から左に交差しています。|
| `orientationDirection` | string| 検出された人がラインを越える向きの方向。 "Left"、"Right"、または "Straigh" を指定できます。 この値は、`CAMERACALIBRATOR_NODE_CONFIG`で `True` に`enable_orientation` が設定されている場合に出力されます。 |
| `zone` | string | 越えられたラインの "name" フィールド|

| Detections フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| 検出 ID|
| `type` | string| 検出の種類|
| `region` | collection| 値のコレクション|
| `type` | string| 領域の種類|
| `points` | collection| 領域の種類が RECTANGLE の場合の、左上と右下のポイント |
| `groundOrientationAngle` | float| 推論された地上平面での人の向きの時計回りの角度 |
| `mappedImageOrientation` | float| 2D イメージ空間における人の向きの時計回りの予測角度 |
| `speed` | float| 検出された人の推定速度。 単位は `foot per second (ft/s)` です。|
| `confidence` | float| アルゴリズムの信頼度|
| `face_mask` | float | 範囲 (0-1) の属性信頼度値は、検出された人がフェイス マスクを着用していることを示します |
| `face_nomask` | float | 範囲 (0-1) の属性信頼度値は、検出された人がフェイス マスクを着用して **いない** ことを示します |

| SourceInfo フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| Camera ID (カメラ ID)|
| `timestamp` | 日付| JSON ペイロードが生成された UTC 日付|
| `width` | INT | ビデオ フレームの幅|
| `height` | INT | ビデオ フレームの高さ|
| `frameId` | INT | フレーム識別子|


> [!IMPORTANT]
> AI モデルは、人がカメラに顔を向けているか、カメラから顔をそらしているかに関係なく、その人を検出します。 AI モデルでは、顔認識は実行されず、生体認証情報も生成されません。 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>cognitiveservices.vision.spatialanalysis-personcrossingpolygon の AI 分析情報の JSON 形式

`zonecrossing` 型の SPACEANALYTICS_CONFIG を使用してこの操作によって出力された検出用のサンプル JSON。

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEnterExitEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": "1"
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
        "metadata": {
        "attributes": {
        "face_mask": 0.99
        }
        }
           
        }
    ],
    "schemaVersion": "1.0"
}
```

`zonedwelltime` 型の SPACEANALYTICS_CONFIG を使用してこの操作によって出力された検出用のサンプル JSON。

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneDwellTimeEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Exit",
                "side": "1",
                      "dwellTime": 7132.0,
                      "dwellFrames": 20            
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
            "metadataType": "",
             "metadata": { 
                     "groundOrientationAngle": 1.2,
                     "mappedImageOrientation": 0.3,
                     "speed": 1.2
               },
        }
    ],
    "schemaVersion": "1.0"
}
```

| イベント フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| イベント ID|
| `type` | string| イベントの種類。 この値には、_personZoneDwellTimeEvent_ または _personZoneEnterExitEvent_ のいずれかを指定できます。|
| `detectionsId` | array| このイベントをトリガーした人検出の一意識別子のサイズ 1 の配列|
| `properties` | collection| 値のコレクション|
| `trackinId` | string| 検出された人の一意識別子|
| `status` | string| 多角形を越える方向 ("Enter" または "Exit")|
| `side` | INT| 人が越えた多角形の辺の数。 各辺は、ゾーンを表す多角形の 2 つの頂点の間の番号付きのエッジです。 多角形にある最初の 2 つの頂点間のエッジは、最初の辺を表します。 オクルージョンが原因でイベントが特定の側に関連付けられていない場合、'Side' は空です。 たとえば、人の姿が消えたにも関わらずゾーンの辺を横切る姿が見られなかった際に出たことが検出されたり、ゾーン内に人の姿が見られるにも関わらず、辺を横切っているのが見られなかった際に入りが発生したりなどです。|
| `dwellTime` | float | 人がゾーンで過ごした時間を表すミリ秒数。 このフィールドは、イベントの種類が personZoneDwellTimeEvent の場合に指定されます。|
| `dwellFrames` | INT | 人がゾーン内にいたフレーム数。 このフィールドは、イベントの種類が personZoneDwellTimeEvent の場合に指定されます。|
| `dwellTimeForTargetSide` | float | 人がゾーン内にいて `target_side` に向いていた時間を表すミリ秒数。 このフィールドは、`CAMERACALIBRATOR_NODE_CONFIG ` で `enable_orientation` が `True` にあり、`target_side` の値が `SPACEANALYTICS_CONFIG` に設定されている場合に指定されます。|
| `avgSpeed` | float| ゾーン内の人の平均速度。 単位は `foot per second (ft/s)` です。|
| `minSpeed` | float| ゾーン内の人の最小速度。 単位は `foot per second (ft/s)` です。|
| `zone` | string | 越えられたゾーンを表す多角形の "name" フィールド|

| Detections フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| 検出 ID|
| `type` | string| 検出の種類|
| `region` | collection| 値のコレクション|
| `type` | string| 領域の種類|
| `points` | collection| 領域の種類が RECTANGLE の場合の、左上と右下のポイント |
| `groundOrientationAngle` | float| 推論された地上平面での人の向きの時計回りの角度 |
| `mappedImageOrientation` | float| 2D イメージ空間における人の向きの時計回りの予測角度 |
| `speed` | float| 検出された人の推定速度。 単位は `foot per second (ft/s)` です。|
| `confidence` | float| アルゴリズムの信頼度|
| `face_mask` | float | 範囲 (0-1) の属性信頼度値は、検出された人がフェイス マスクを着用していることを示します |
| `face_nomask` | float | 範囲 (0-1) の属性信頼度値は、検出された人がフェイス マスクを着用して **いない** ことを示します |

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>cognitiveservices.vision.spatialanalysis-persondistance の AI 分析情報の JSON 形式

この操作によって出力される検出のサンプル JSON。

```json
{
    "events": [
        {
            "id": "9c15619926ef417aa93c1faf00717d36",
            "type": "personDistanceEvent",
            "detectionIds": [
                "9037c65fa3b74070869ee5110fcd23ca",
                "7ad7f43fd1a64971ae1a30dbeeffc38a"
            ],
            "properties": {
                "personCount": 5,
                "averageDistance": 20.807043981552123,
                "minimumDistanceThreshold": 6.0,
                "maximumDistanceThreshold": "Infinity",
                "eventName": "TooClose",
                "distanceViolationPersonCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:17:25.309Z",
        "width": 608,
        "height": 342,
        "frameId": "1199",
        "cameraCalibrationInfo": {
            "status": "Calibrated",
            "cameraHeight": 12.9940824508667,
            "focalLength": 401.2800598144531,
            "tiltupAngle": 1.057669997215271
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "9037c65fa3b74070869ee5110fcd23ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.39988183975219727,
                        "y": 0.2719132942065858
                    },
                    {
                        "x": 0.5051516984638414,
                        "y": 0.6488402517218339
                    }
                ]
            },
            "confidence": 0.948630690574646,
            "centerGroundPoint": {
                "x": -1.4638760089874268,
                "y": 18.29732322692871
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "7ad7f43fd1a64971ae1a30dbeeffc38a",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5200299714740954,
                        "y": 0.2875368218672903
                    },
                    {
                        "x": 0.6457497446160567,
                        "y": 0.6183311060855263
                    }
                ]
            },
            "confidence": 0.8235412240028381,
            "centerGroundPoint": {
                "x": 2.6310102939605713,
                "y": 18.635927200317383
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| イベント フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| イベント ID|
| `type` | string| イベントの種類|
| `detectionsId` | array| このイベントをトリガーした人検出の一意識別子のサイズ 1 の配列|
| `properties` | collection| 値のコレクション|
| `personCount` | INT| イベントの生成時に検出された人数|
| `averageDistance` | float| 検出されたすべての人の間の平均距離 (フィート単位)|
| `minimumDistanceThreshold` | float| 人と人との距離がその距離よりも近いときに "TooClose" イベントをトリガーする距離 (フィート単位)。|
| `maximumDistanceThreshold` | float| 人と人との距離がその距離よりも離れているときに "TooFar" イベントをトリガーする距離 (フィート単位)。|
| `eventName` | string| イベント名は、`TooClose` (`minimumDistanceThreshold` に違反している場合)、`TooFar` (`maximumDistanceThreshold` に違反している場合)、または `unknown` (自動調整が完了していない場合) です。|
| `distanceViolationPersonCount` | INT| `minimumDistanceThreshold` または `maximumDistanceThreshold` の違反で検出された人数|
| `zone` | string | 人と人との間の距離について監視されていたゾーンを表す多角形の "name" フィールド|
| `trigger` | string| SPACEANALYTICS_CONFIG の `trigger` の値に応じて、トリガーの種類は "event" または "interval" になります。|

| Detections フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| 検出 ID|
| `type` | string| 検出の種類|
| `region` | collection| 値のコレクション|
| `type` | string| 領域の種類|
| `points` | collection| 領域の種類が RECTANGLE の場合の、左上と右下のポイント |
| `confidence` | float| アルゴリズムの信頼度|
| `centerGroundPoint` | 2 つの浮動小数点値| 地面上での人の推論された位置の座標の `x` および `y` 値 (フィート単位)。 `x` と `y` は、フロアが階層であると仮定した場合の床面上の座標です。 カメラの位置は原点です。 |

`centerGroundPoint` を計算する場合、`x` は、カメラの画像平面に垂直になる線に沿った、カメラから人までの距離です。 `y` は、カメラの画像平面に平行な線に沿った、カメラから人までの距離です。 

![中心の地表点の例](./media/spatial-analysis/x-y-chart.png) 

この例では `centerGroundPoint` は `{x: 4, y: 5}` です。 これは、カメラから 4 フィート、右に 5 フィート離れた場所に、部屋を見下ろしている人がいることを意味します。


| SourceInfo フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| Camera ID (カメラ ID)|
| `timestamp` | 日付| JSON ペイロードが生成された UTC 日付|
| `width` | INT | ビデオ フレームの幅|
| `height` | INT | ビデオ フレームの高さ|
| `frameId` | INT | フレーム識別子|
| `cameraCallibrationInfo` | collection | 値のコレクション|
| `status` | string | `state[;progress description]` の形式での調整の状態。 状態は、`Calibrating`、`Recalibrating` (再調整が有効な場合)、または `Calibrated` になります。 進行状況の説明部分は、現在の調整プロセスの進行状況を示すために使用される `Calibrating` および `Recalibrating` 状態の場合にのみ有効です。|
| `cameraHeight` | float | 地面からのカメラの高さ (フィート単位)。 これは自動調整から推論されます。 |
| `focalLength` | float | カメラの焦点距離 (ピクセル単位)。 これは自動調整から推論されます。 |
| `tiltUpAngle` | float | 垂直線からのカメラの傾斜角度。 これは自動調整から推論されます。|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-ai-insights"></a>cognitiveservices.vision.spatialanalysis AI 分析情報の JSON 形式

この操作の出力は構成された `events` によって異なります。たとえば、この操作用に構成された `zonecrossing` のイベントがある場合、出力は `cognitiveservices.vision.spatialanalysis-personcrossingpolygon` と同じになります。

## <a name="use-the-output-generated-by-the-container"></a>コンテナーによって生成された出力を使用する

空間分析の検出またはイベントをアプリケーションに統合することをお勧めします。 検討すべきいくつかの方法を次に示します。 

* 選択したプログラミング言語の Azure Event Hub SDK を使用して、Azure IoT Hub エンドポイントに接続し、イベントを受信します。 詳細については、「[デバイスからクラウドへのメッセージを組み込みのエンドポイントから読み取る](../../iot-hub/iot-hub-devguide-messages-read-builtin.md)」をご覧ください。 
* イベントを他のエンドポイントに送信したり、イベントをデータ ストレージに保存したりするには、Azure IoT Hub の **メッセージ ルーティング** を設定します。 詳細については、[IoT Hub メッセージ ルーティング](../../iot-hub/iot-hub-devguide-messages-d2c.md)に関する記事をご覧ください。 
* 到着したイベントをリアルタイムで処理し、視覚化を作成する Azure Stream Analytics ジョブを設定します。 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>空間分析操作を大規模に展開する (複数のカメラ)

GPU のパフォーマンスと使用率を最大限に引き出すために、グラフ インスタンスを使用して複数のカメラに空間分析操作を展開できます。 次に示すのは、15 台のカメラで `cognitiveservices.vision.spatialanalysis-personcrossingline` 操作を実行するためのサンプルです。

```json
  "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
        "personzonelinecrossing": {
        "operationId": "cognitiveservices.vision.spatialanalysis-personcrossingline",
        "version": 1,
        "enabled": true,
        "sharedNodes": {
            "shared_detector0": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 7, \"do_calibration\": true}",
                }
            },
            "shared_calibrator0": {
                "node": "PersonCrossingLineGraph/cameracalibrator",
                "parameters": {
                    "CAMERACALIBRATOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"do_calibration\": true, \"enable_zone_placement\": true}",
                    "CALIBRATION_CONFIG": "{\"enable_recalibration\": true, \"quality_check_frequency_seconds\": 86400}",
                }
        },
        "parameters": {
            "VIDEO_DECODE_GPU_INDEX": 0,
            "VIDEO_IS_LIVE": true
        },
        "instances": {
            "1": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                    "VIDEO_SOURCE_ID": "camera 1",
                    "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]]}]}"
                }
            },
            "2": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                    "VIDEO_SOURCE_ID": "camera 2",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "3": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                    "VIDEO_SOURCE_ID": "camera 3",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "4": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                    "VIDEO_SOURCE_ID": "camera 4",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "5": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                    "VIDEO_SOURCE_ID": "camera 5",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "6": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 6>",
                    "VIDEO_SOURCE_ID": "camera 6",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "7": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 7>",
                    "VIDEO_SOURCE_ID": "camera 7",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "8": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 8>",
                    "VIDEO_SOURCE_ID": "camera 8",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "9": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 9>",
                    "VIDEO_SOURCE_ID": "camera 9",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "10": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 10>",
                    "VIDEO_SOURCE_ID": "camera 10",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "11": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 11>",
                    "VIDEO_SOURCE_ID": "camera 11",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "12": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 12>",
                    "VIDEO_SOURCE_ID": "camera 12",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "13": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 13>",
                    "VIDEO_SOURCE_ID": "camera 13",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "14": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 14>",
                    "VIDEO_SOURCE_ID": "camera 14",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "15": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 15>",
                    "VIDEO_SOURCE_ID": "camera 15",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            }
          }
        },
      }
  }
  ```
| 名前 | 型| 説明|
|---------|---------|---------|
| `batch_size` | INT | すべてのカメラの解像度が同じ場合は、`batch_size` をその操作で使用されるカメラの数に設定します。それ以外の場合は、`batch_size` を 1 に設定するか、既定値 (1) のままにします。これは、バッチがサポートされないことを示します。 |

## <a name="next-steps"></a>次のステップ

* [人数カウント Web アプリをデプロイする](spatial-analysis-web-app.md)
* [ロギングおよびトラブルシューティング](spatial-analysis-logging.md)
* [カメラの配置ガイド](spatial-analysis-camera-placement.md)
* [ゾーンとラインの配置ガイド](spatial-analysis-zone-line-placement.md)
