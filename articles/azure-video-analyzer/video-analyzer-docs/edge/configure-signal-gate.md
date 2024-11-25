---
title: イベントベースのビデオ記録用にシグナル ゲートを構成する - Azure
description: この記事では、パイプラインでシグナル ゲートを構成する方法に関するガイダンスを提供します。
ms.topic: how-to
ms.date: 06/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4fbb530cc4cf0c4b5ea4871c922b7fff6c75ec93
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091984"
---
# <a name="configuring-a-signal-gate-for-event-based-video-recording"></a>イベントベースのビデオ記録用にシグナル ゲートを構成する

[!INCLUDE [header](includes/edge-env.md)]

パイプライン内の[シグナル ゲート プロセッサ ノード](../pipeline.md#signal-gate-processor)を使用すると、イベントによってゲートがトリガーされたときに、あるノードから別のノードにメディアを転送できます。 トリガーされると、ゲートが開き、指定された期間だけメディアがフローできるようになります。 ゲートをトリガーするイベントがない場合、ゲートは閉じ、メディアのフローは停止します。 イベントベースのビデオ記録には、シグナル ゲート プロセッサを使用できます。

> [!NOTE]
> シグナル ゲート プロセッサ ノードの直後に、ビデオ シンクまたはファイル シンクを配置する必要があります。

この記事では、シグナル ゲート プロセッサを構成する方法について説明します。

## <a name="suggested-prereading"></a>先に読んでおくことが推奨される記事

- [パイプライン トポロジ](../pipeline.md)
- [イベントベースのビデオ記録](../event-based-video-recording-concept.md)

## <a name="problem"></a>問題

ユーザーは、イベントによってゲートがトリガーされる前または後の特定の時刻に記録を開始することを望む場合があります。 ユーザーは、システム内で許容される待機時間を把握しています。 そのため、ユーザーはシグナル ゲート プロセッサの待機時間を指定する必要があります。 また、受信した新しいイベントの数に関係なく、記録の最小期間と最大期間を指定する必要があります。
 
### <a name="use-case-scenario"></a>ユース ケース シナリオ

建物のフロント ドアが開かれるたびにビデオを録画するものとします。 次のように記録を行います。 

- ドアが開く前の *X* 秒を含めます。 
- ドアが再び開かれない場合は、少なくとも *Y* 秒継続します。 
- ドアが繰り返し開かれた場合は、最大で *Z* 秒継続します。 
 
ドア センサーには *K* 秒の待機時間があることがわかっています。 イベントが遅延到着として無視される可能性を減らすため、イベントが到着するまでに少なくとも *K* 秒を猶予する必要があります。

## <a name="solution"></a>解決策

この問題に対処するため、シグナル ゲート プロセッサのパラメーターを変更します。

シグナル ゲート プロセッサを構成するには、次の 4 つのパラメーターを使用します。

- アクティブ化評価期間
- アクティブ化シグナル オフセット
- 最小アクティブ化期間
- 最大アクティブ化期間

トリガーされたシグナル ゲート プロセッサは、最小アクティブ化時間だけ開いたままになります。 アクティブ化イベントは、最早イベントのタイムスタンプにアクティブ化シグナル オフセットを加えた時間に開始されます。 

開いている間にシグナル ゲート プロセッサが再度トリガーされた場合は、タイマーはリセットされ、少なくとも最小アクティブ化時間だけゲートは開いたままになります。 シグナル ゲート プロセッサは、最大アクティブ化時間より長く開いたままになることはありません。 

メディア タイムスタンプを基にすると別のイベント (イベント 2) より前に発生したイベント (イベント 1) が、システムの遅延により、イベント 2 より後にシグナル ゲート プロセッサに到着した場合、イベント 1 は無視される可能性があります。 イベント 1 がイベント 2 の到着からアクティブ化評価期間の間に到着しない場合、イベント 1 は無視されます。 シグナル ゲート プロセッサを通過することはありません。 

相関 ID は、すべてのイベントに対して設定されます。 これらの ID は、初期イベントから設定されます。 これらは、以降の各イベントで連続しています。

> [!IMPORTANT]
> メディアの時刻は、メディア内でイベントが発生したときのメディア タイムスタンプに基づきます。 シグナル ゲートに到着するイベントのシーケンスは、メディア時刻でのイベントの到着シーケンスを反映していない場合があります。

### <a name="parameters-based-on-the-physical-time-that-events-arrive-at-the-signal-gate"></a>イベントがシグナル ゲートに到着した物理時刻に基づくパラメーター

* **minimumActivationTime (記録の最短可能期間)** : maximumActivationTime によって中断されない限り、シグナル ゲート プロセッサが新しいイベントの受信をトリガーされた後で開いたままになる最小秒数。
* **maximumActivationTime (記録の最長可能期間)** : 受信したイベントに関係なく、新しいイベントを受信するためにトリガーされた後で、シグナル ゲート プロセッサが開いたままになる、最初のイベントからの最大秒数。
* **activationSignalOffset**: シグナル ゲート プロセッサのアクティブ化から、ビデオ記録が開始されるまでの秒数。 通常、トリガー イベントの前に記録が開始されるため、この値は負になります。
* **activationEvaluationWindow**: メディア時刻で初期トリガー イベントより前に発生したイベントが、遅延到着と見なされて無視されることなくシグナル ゲート プロセッサに到着するために必要な、初期イベントからの秒数。

> [!NOTE]
> "*遅延到着*" とは、アクティブ化評価期間が経過した後で到着したが、メディア時刻で初期イベントより前に到着したイベントです。

### <a name="limits-of-parameters"></a>パラメーターの制限

* **activationEvaluationWindow**: 0 秒から 10 秒
* **activationSignalOffset**: -1 分から 1 分
* **minimumActivationTime**: 10 秒から 1 時間
* **maximumActivationTime**: 10 秒から 1 時間

このユース ケースでは、パラメーターを次のように設定します。

* **activationEvaluationWindow**: *K* 秒
* **activationSignalOffset**: *-X* 秒
* **minimumActivationWindow**: *Y* 秒
* **maximumActivationWindow**: *Z* 秒

ここの例では、パラメーターが次の値の場合の **シグナル ゲート プロセッサ** ノード セクションによるパイプライン トポロジの処理方法を示します。

* **activationEvaluationWindow**: 1 秒
* **activationSignalOffset**: -5 秒
* **minimumActivationTime**: 20 秒
* **maximumActivationTime**: 40 秒

> [!IMPORTANT]
> 各パラメーター値には、[ISO 8601 の期間形式](https://en.wikipedia.org/wiki/ISO_8601#Durations)が想定されます。 たとえば、PT1S = 1 秒のようになります。

```
"processors":              
[
          {
            "@type": "#Microsoft.VideoAnalyzer.SignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "iotMessageSource"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "-PT5S",
            "minimumActivationTime": "PT20S",
            "maximumActivationTime": "PT40S"
          }
]
```

ここで、このシグナル ゲート プロセッサの構成がさまざまな記録シナリオでどのように動作するかを検討します。

### <a name="recording-scenarios"></a>記録シナリオ

**1 つのソースから 1 つのイベント ("*通常のアクティブ化*")**

1 つのイベントを受信したシグナル ゲート プロセッサは、イベントがゲートに到着する 5 秒前から (アクティブ化シグナル = 5 秒) 記録を開始します。 最小アクティブ化時間が終了する前にゲートを再トリガーする他のイベントが到着しないため、残りの記録は 20 秒 (最小アクティブ化時間 = 20 秒) です。

例の図:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate/normal-activation.svg" alt-text="1 つのソースから 1 つのイベントの通常のアクティブ化を示す図。":::

* 記録期間 = -offset + minimumActivationTime = [E1+offset, E1+minimumActivationTime]

**1 つのソースから 2 つのイベント ("*再トリガーされたアクティブ化*")**

2 つのイベントを受信したシグナル ゲート プロセッサは、イベントがゲートに到着する 5 秒前から (アクティブ化シグナルオフセット = 5 秒) 記録を開始します。 また、イベント 2 はイベント 1 から 5 秒後に到着します。 イベント 2 は、イベント 1 の最小アクティブ化時間 (20 秒) が終了する前に到着するため、ゲートは再トリガーされます。 イベント 2 からの最小アクティブ化時間が終了する前にゲートを再トリガーする他のイベントが到着しないため、残りの記録は 20 秒 (最小アクティブ化時間 = 20 秒) です。

例の図:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate/retriggering-activation.svg" alt-text="1 つのソースからの 2 つのイベントの再トリガーされたアクティブ化を示す図。":::

* 記録の期間 = -offset + (イベント 2 の到着 - イベント 1 の到着) + minimumActivationTime

**1 つのソースからの *N* イベント ("*最大アクティブ化*")**

*N* 個のイベントを受信したシグナル ゲート プロセッサは、最初のイベントがゲートに到着する 5 秒前から (アクティブ化シグナルオフセット = 5 秒) 記録を開始します。 各イベントは、前のイベントからの最小アクティブ化時間 20 秒が終了する前に到着するので、ゲートは継続的に再トリガーされます。 最初のイベントから最大アクティブ化時間の 40 秒が経過するまで開いたままになります。 その後ゲートは閉じて、新しいイベントを受け付けなくなります。

例の図:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate/maximum-activation.svg" alt-text="1 つのソースからの N 個のイベントの最大アクティブ化を示す図。":::
 
* 記録の期間 = -offset + maximumActivationTime

> [!IMPORTANT]
> 上の図では、すべてのイベントは物理時刻とメディア時刻で同時に到着するものと想定しています。 つまり、遅延到着は発生していないことを前提としています。

### <a name="naming-video-or-files"></a>ビデオまたはファイルの名前付け

パイプラインを使用すると、ビデオをクラウドに記録したり、エッジ デバイス上の MP4 ファイルとして記録したりすることができます。 これらは、[継続的なビデオ記録](use-continuous-video-recording.md)、または[イベントベースのビデオ記録](record-event-based-live-video.md)を行うことで生成できます。

クラウドに記録する場合、ビデオ リソースに付ける名前の構造は、`<anytext>-${System.TopologyName}-${System.PipelineName}` のようにすることをお勧めします。 特定のライブ パイプラインは 1 つの RTSP 対応 IP カメラにのみ接続できます。また、そのカメラからの入力は 1 つのビデオ リソースに記録する必要があります。 たとえば、次のように `VideoName` を Video Sink 上に設定できます。

```
"VideoName": "sampleVideo-${System.TopologyName}-${System.PipelineName}"
```
置換パターンは、 **${variableName}** のように、`$` 記号の後に中かっこを続けて指定します。

イベント ベースの記録を使用してエッジ デバイス上の MP4 ファイルに記録する場合は、次のコマンドを使用できます。

```
"fileNamePattern": "sampleFilesFromEVR-${System.TopologyName}-${System.PipelineName}-${fileSinkOutputName}-${System.Runtime.DateTime}"
```

> [!Note]
> 上記の例では、変数 **fileSinkOutputName** は、ライブ パイプラインを作成するときに定義するサンプル変数名です。 これは、システム変数では **ありません**。 **DateTime** を使用することで、イベントごとに一意の MP4 ファイル名を付けることができます。

#### <a name="system-variables"></a>システム変数

使用できるシステム定義変数には、次のものがあります。

| システム変数        | 説明                                                  | 例              |
| :--------------------- | :----------------------------------------------------------- | :------------------- |
| System.Runtime.DateTime        | ISO8601 ファイルに準拠した形式の UTC 日付時刻 (基本表現 YYYYYMMDDThhmmss)。 | 20200222T173200Z     |
| System.Runtime.PreciseDateTime | ミリ秒付きの ISO8601 ファイルに準拠した形式の UTC 日付時刻 (基本表現 YYYYMMDDThhmmss.sss)。 | 20200222T173200.123Z |
| System.TopologyName    | ユーザーが指定した実行中のパイプライン トポロジの名前。          | IngestAndRecord      |
| System.PipelineName    | ユーザーが指定した実行中のライブ パイプラインの名前。          | camera001            |

> [!Tip]
> System.Runtime.PreciseDateTime と System.Runtime.DateTime は、クラウド内のビデオに名前を付けるときには使用できません。

## <a name="next-steps"></a>次のステップ

[イベントベースのビデオ記録チュートリアル](record-event-based-live-video.md)を試してください。 まず、[topology.json](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json) を編集します。 signalgateProcessor ノードのパラメーターを変更した後、チュートリアルの残りの部分に従います。 ビデオ記録を確認して、パラメーターの効果を分析します。
