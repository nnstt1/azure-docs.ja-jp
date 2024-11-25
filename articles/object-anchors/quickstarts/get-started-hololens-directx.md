---
title: クイック スタート:DirectX を使用して HoloLens アプリを作成する
description: このクイック スタートでは、Object Anchors を使用する HoloLens アプリを作成する方法について説明します。
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 09/08/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 2cb1f51d47c95f47f6a1eb3a242b54cf60929b44
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124792122"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-cwinrt-and-directx"></a>クイック スタート: Azure Object Anchors を使用する HoloLens アプリを C++/WinRT と DirectX で作成する

このクイック スタートでは、[Azure Object Anchors](../overview.md) を使用する HoloLens アプリを C++/WinRT と DirectX で作成する方法について説明します。 Azure Object Anchors は、3D アセットを HoloLens の物体認識 Mixed Reality エクスペリエンスを実現する AI モデルに変換するマネージド クラウド サービスです。 作業を終えると、Holographic DirectX 11 (ユニバーサル Windows) アプリケーション内で物体とその姿勢を検出できる HoloLens アプリが完成します。

学習内容は次のとおりです。

> [!div class="checklist"]
> * HoloLens アプリケーションを作成してサイドロードする
> * 物体を検出し、そのモデルを視覚化する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このクイック スタートを実行するには、以下が必要です。

* ご利用環境にある対象物と、その 3D モデル (CAD またはスキャン済みのいずれか)。
* 以下がインストールされている Windows マシン。
  * <a href="https://git-scm.com" target="_blank">Git for Windows</a>
  * **ユニバーサル Windows プラットフォーム開発** ワークロードを含む <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> と **Windows 10 SDK (10.0.18362.0 以降)** コンポーネント
* [開発者モード](/windows/mixed-reality/using-visual-studio#enabling-developer-mode)が有効になっている最新の HoloLens 2 デバイス。
  * HoloLens を最新のリリースに更新するには、 **[設定]** アプリを開き、 **[更新とセキュリティ]** を選択し、 **[更新プログラムの確認]** を選択します。

[!INCLUDE [Create Account](../../../includes/object-anchors-get-started-create-account.md)]

[!INCLUDE [Upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

## <a name="open-the-sample-project"></a>サンプル プロジェクトを開く

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

Visual Studio で `quickstarts/apps/directx/DirectXAoaSampleApp.sln` を開きます。

**ソリューション構成** を **[リリース]** に変更し、**ソリューション プラットフォーム** を **[ARM64]** に変更し、配置先オプションから **[デバイス]** を選択します。

## <a name="configure-the-account-information"></a>アカウント情報を構成する

次のステップは、自分のアカウントの情報を使用するようにアプリを構成することです。 [[Object Anchors アカウントの作成]](#create-an-object-anchors-account) セクションの **アカウント キー**、**アカウント ID** および **アカウント ドメイン** 値をメモします。

`Assets\ObjectAnchorsConfig.json`を開きます。

`AccountId` フィールドを見つけ、`Set me` をアカウント ID に置き換えます。

`AccountKey` フィールドを見つけ、`Set me` をアカウント キーに置き換えます。

`AccountDomain` フィールドを見つけ、`Set me` をアカウント ドメインに置き換えます。

次に、**AoaSampleApp** プロジェクトを右クリックし、 **[ビルド]** を選択して、プロジェクトをビルドします。

:::image type="content" source="./media/vs-deploy-to-device.png" alt-text="配置する Visual Studio プロジェクトを構成する":::

## <a name="deploy-the-app-to-hololens"></a>アプリを HoloLens に配置する

サンプル プロジェクトを正常にコンパイルしたら、アプリを HoloLens に配置できます。

HoloLens デバイスの電源が入っていて、USB ケーブルを介して PC に接続されていることを確認します。 **[デバイス]** が、選択された配置先であることを確認します (上記を参照)。

**AoaSampleApp** プロジェクトを右クリックし、ポップアップ メニューの **[配置]** をクリックして、アプリをインストールします。 Visual Studio の **出力ウィンドウ** にエラーが表示されなければ、アプリは HoloLens にインストールされます。

:::image type="content" source="./media/vs-deploy-app.png" alt-text="アプリを HoloLens に配置する":::

アプリを起動する前に、HoloLens の **3D Objects** フォルダーに、オブジェクト モデル (たとえば **chair.ou**) をアップロードする必要があります。 まだ行っていない場合は、「[モデルをアップロードする](#upload-your-model)」セクションの手順に従ってください。

アプリを起動してデバッグするには、 **[デバッグ] > [デバッグの開始]** の順に選択します。

## <a name="ingest-object-model-and-detect-its-instance"></a>オブジェクト モデルを取り込み、そのインスタンスを検出する

**AoaSampleApp** アプリが HoloLens デバイスで現在実行されています。 対象の物体 (椅子) の近く (2 メートル以内の距離) を歩き、複数の視点からそれを見てスキャンします。 その物体の周囲にピンク色の境界ボックスが表示され、いくつかの黄色の点が物体の表面の近くにレンダリングされています。これは、それが検出されたことを示しています。

:::image type="content" source="./media/chair-detection.png" alt-text="椅子の検出":::

図: 検出された椅子。その境界ボックス (ピンク色)、点群 (黄色)、および検索領域 (大きな黄色のボックス) でレンダリングされています。

アプリ内で物体の検索スペースを定義するには、左右のどちらかの手を使用して空中で指をパチンと鳴らします。 検索スペースは、半径 2 メートルの球、4 m^3 の境界ボックス、ビューの視錐台の間で切り替わります。 自動車などの大きい物体の場合、通常、約 2 メートルの距離で物体の角の方を向いて立ちながら、ビューの視錐台の選択範囲を使用することが最適な方法です。
検索領域が変更されるたびに、アプリは現在追跡されているインスタンスを削除してから、新しい検索領域で再度それらの検索を試行します。

このアプリは、一度に複数の物体を追跡できます。 これを行うには、複数のモデルをデバイスの **3D Objects** フォルダーにアップロードし、対象とするすべての物体を範囲に含む検索領域を設定します。 複数の物体の検出と追跡には時間がかかることがあります。

このアプリでは、3D モデルをそれに物理的に対応するものと密接に整合させます。 ユーザーは、左手を使用してエアタップを行って、高精度の追跡モードを有効にすることができます。これにより、さらに正確な姿勢が計算されます。 これはまだ試験段階の機能であり、より多くのシステム リソースを消費します。また、推定される姿勢に高いジッターが発生する恐れがあります。 通常の追跡モードに戻すには、左手で再度エアタップを行います。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [クイック スタート: 3D モデルの取り込み](./get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [概念: SDK の概要](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FAQ](../faq.md)

> [!div class="nextstepaction"]
> [変換 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)

> [!div class="nextstepaction"]
> [オブジェクト検出のトラブルシューティング](../troubleshoot/object-detection.md)
