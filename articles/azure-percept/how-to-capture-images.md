---
title: Azure Percept Studio でイメージをキャプチャする
description: Azure Percept Studio で Azure Percept DK を使用して画像をキャプチャする方法。
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 0712c10df8f987237df74d2078519afbd70e72f1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006584"
---
# <a name="capture-images-in-azure-percept-studio"></a>Azure Percept Studio でイメージをキャプチャする

既存のビジョン プロジェクトで、Azure Percept DK を使用して画像をキャプチャするには、このガイドに従ってください。 まだビジョン プロジェクトを作成していない場合は、[コーディングなしのビジョンに関するチュートリアル](./tutorial-nocode-vision.md)を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure Percept DK (開発キット)
- [Azure サブスクリプション](https://azure.microsoft.com/free/)
- [Azure Percept DK セットアップ エクスペリエンス](./quickstart-percept-dk-set-up.md): 開発キットを Wi-Fi ネットワークに接続し、IoT ハブを作成して、開発キットを IoT ハブに接続済みであること
- [コーディングなしのビジョン プロジェクト](./tutorial-nocode-vision.md)

## <a name="capture-images"></a>キャプチャ画像

1. 開発キットの電源をオンにします。

1. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) に移動します。

1. 概要ページの左側にある **[デバイス]** を選択します。

    :::image type="content" source="./media/how-to-capture-images/overview-devices-inline.png" alt-text="Azure Percept Studio の概要画面。" lightbox="./media/how-to-capture-images/overview-devices.png":::

1. 一覧から開発キットを選択します。

    :::image type="content" source="./media/how-to-capture-images/select-device.png" alt-text="Percept デバイスの一覧。":::

1. デバイス ページで、 **[Capture images for a project]\(プロジェクトの画像をキャプチャ\)** を選択します。

    :::image type="content" source="./media/how-to-capture-images/capture-images.png" alt-text="利用可能なアクションが一覧表示された Percept デバイス ページ。":::

1. **[Image capture]\(画像のキャプチャ\)** ウィンドウで、次のステップを実行します。

    1. **[プロジェクト]** ボックスの一覧から、画像を収集するビジョン プロジェクトを選択します。

    1. **[View device stream]\(デバイス ストリームの表示\)** を選択して、Vision SoM のカメラが正しく配置されていることを確認します。

    1. **[Take photo]\(写真を撮る\)** を選択して画像をキャプチャします。

    1. または、画像キャプチャのタイマーを設定するには、 **[Automatic image capture]\(自動画像キャプチャ\)** の横にあるチェック ボックスをオンにします。

        1. **[Capture rate]\(キャプチャ レート\)** で、望ましい画像化レートを選択します。
        1. 収集する画像の合計枚数を **[Target]\(ターゲット\)** で選択します。

    :::image type="content" source="./media/how-to-capture-images/take-photo.png" alt-text="画像のキャプチャ画面。":::

すべての画像には、[Custom Vision](https://www.customvision.ai/) からアクセスできます。

## <a name="next-steps"></a>次のステップ

[コーディングなしのビジョン モデルのテストと再トレーニング](../cognitive-services/custom-vision-service/test-your-model.md)。
