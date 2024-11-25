---
title: チュートリアル:環境の作成 - Azure Time Series Insights | Microsoft Docs
description: シミュレートされたデバイスからのデータが入力された Azure Time Series Insights 環境を作成する方法について説明します。
services: time-series-insights
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.reviewer: orspodek
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: 165d703e8515ccec5c92b7297769ccc02fbdf009
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2021
ms.locfileid: "112369148"
---
# <a name="tutorial-create-an-azure-time-series-insights-gen1-environment"></a>チュートリアル:Azure Time Series Insights Gen1 環境を作成する

> [!CAUTION]
> これは Gen1 の記事です。

このチュートリアルでは、シミュレートされたデバイスのデータが入力される Azure Time Series Insights 環境を作成するプロセスについて説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * Azure Time Series Insights 環境を作成する。
> * IoT ハブを含むデバイス シミュレーション ソリューションを作成する。
> * Azure Time Series Insights 環境を IoT ハブに接続する。
> * デバイス シミュレーションを実行して Azure Time Series Insights 環境にデータをストリーム配信する。
> * シミュレートされたテレメトリ データを確認する。

> [!IMPORTANT]
> お持ちでない場合は、[無料の Azure サブスクリプション](https://azure.microsoft.com/free/)にサインアップしてください。

## <a name="prerequisites"></a>前提条件

* また、Azure のサインイン アカウントは、サブスクリプションの **所有者** ロールのメンバーである必要があります。 詳細については、「[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」を参照してください。

## <a name="review-video"></a>ビデオの視聴

Azure IoT ソリューション アクセラレータを使用してデータを生成し、Azure Time Series Insights の使用を開始する方法について説明します。

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>概要

Azure Time Series Insights 環境とは、デバイス データが収集、格納される場所のことです。 格納された後は、[Azure Time Series Insights Explorer](time-series-quickstart.md) と [Azure Time Series Insights クエリ API](/rest/api/time-series-insights/gen1-query-api) を使用して、データのクエリおよび分析を行うことができます。

Azure IoT Hub は、お使いの Azure クラウドに安全に接続してデータを転送するために、チュートリアルのすべての (シミュレートされた、または物理的な) デバイスによって使用される、イベント ソースです。

また、このチュートリアルでは [IoT ソリューション アクセラレータ](https://www.azureiotsolutions.com)を使用し、サンプルのテレメトリ データを生成して IoT Hub にストリーム配信します。

>[!TIP]
> [IoT ソリューション アクセラレータ](https://www.azureiotsolutions.com)によって、カスタム IoT ソリューションの開発を高速化するために使用できる、エンタープライズ レベルのあらかじめ構成されたソリューションが提供されます。

## <a name="create-a-device-simulation"></a>デバイス シミュレーションを作成する

まず、Azure Time Series Insights 環境に入力されるテスト データを生成する、デバイス シミュレーション ソリューションを作成します。

1. 別のウィンドウまたはタブで [azureiotsolutions.com](https://www.azureiotsolutions.com) に移動します。 同じ Azure サブスクリプション アカウントを使用してサインインし、 **[デバイス シミュレーション]** アクセラレータを選択します。

   [![デバイス シミュレーション アクセラレータを実行する](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png#lightbox)

1. **[今すぐ試してみる]** をクリックします。 その後、 **[デバイス シミュレーション ソリューションの作成]** ページで、次の必須パラメーターを入力します。

   パラメーター|説明
   ---|---
   **デプロイ名** | この一意の値は、新しいリソース グループを作成するために使用されます。 一覧の Azure リソースが作成され、リソース グループに割り当てられます。
   **Azure サブスクリプション** | 前のセクションで Azure Time Series Insights 環境の作成に使用したものと同じサブスクリプションを指定します。
   **デプロイ オプション** | **[Provision new IoT Hub]\(新しい IoT ハブをプロビジョニングする\)** を選択して、このチュートリアル専用の新しい IoT ハブを作成します。
   **Azure の場所** | 前のセクションで Azure Time Series Insights 環境の作成に使用したものと同じリージョンを指定します。

   完了したら、 **[作成]** を選択してソリューションの Azure リソースをプロビジョニングします。 このプロセスが完了するまでに最大で 20 分かかる場合があります。

   [![デバイス シミュレーション ソリューションをプロビジョニングする](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png#lightbox)

1. プロビジョニングが完了すると、2 つの更新が表示され、デプロイ状態が **[プロビジョニング中]** から **[準備完了]** に移行したことがわかります。

   >[!IMPORTANT]
   > まだソリューション アクセラレータは入力しないでください。 後で戻ってくるので、この Web ページは開いたままにしておきます。

   [![デバイス シミュレーション ソリューションのプロビジョニング完了](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. ここで、Azure portal で新しく作成されたリソースを検査します。 **[リソース グループ]** ページで、最後の手順で指定した **ソリューション名** を使用して新しいリソース グループが作成されたことを確認します。 デバイス シミュレーション用に作成されたリソースをメモします。

   [![デバイス シミュレーションのリソース](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>環境の作成

次に、お使いの Azure サブスクリプションに Azure Time Series Insights 環境を作成します。

1. Azure サブスクリプション アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左上隅の **[+ リソースの作成]** を選択します。
1. **[モノのインターネット (IoT)]** カテゴリを選択し、 **[Time Series Insights]** を選択します。

   [![Azure Time Series Insights 環境リソースを選択する](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png)](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png#lightbox)

1. **[Time Series Insights 環境]** ページで、必須パラメーターを入力します。

   パラメーター|説明
   ---|---
   **環境名** | Azure Time Series Insights 環境の一意の名前を選択します。 その名前は Azure Time Series Insights Explorer と[クエリ API シリーズ](/rest/api/time-series-insights/gen1-query)で使用されます。
   **サブスクリプション** | サブスクリプションとは、Azure リソース用のコンテナーです。 Azure Time Series Insights 環境を作成するサブスクリプションを選択します。
   **リソース グループ** | リソース グループとは、Azure リソース用のコンテナーです。 Azure Time Series Insights 環境リソース用に既存のリソース グループを選択するか、新しいリソース グループを作成します。
   **場所** | Azure Time Series Insights 環境のデータ センター リージョンを選択します。 待ち時間の増加を防ぐために、Azure Time Series Insights 環境を他の IoT リソースと同じリージョンに作成します。
   **レベル** | 必要なスループットを選択します。 **[S1]** を選択します。
   **[容量]** | 容量は、選択した SKU に関連するイングレス レートとストレージ容量に適用される乗数です。 この容量は、作成後に変更できます。 容量には **1** を選択します。

   終了したら、 **[次へ:イベント ソース]** を選択して次の手順に進みます。

   [![Azure Time Series Insights 環境リソースを作成する](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png#lightbox)

1. 次に、Azure Time Series Insights 環境を、ソリューション アクセラレータによって作成された IoT ハブに接続します。 **[ハブを選択]** を `Select existing` に設定します。 次に、**IoT ハブ名** を設定するときにソリューション アクセラレータによって作成された IoT ハブを選択します。

   [![作成した IoT ハブに Azure Time Series Insights 環境を接続する](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png#lightbox)

   最後に、 **[確認および作成]** を選択します。

1. **[通知]** パネルを確認して、デプロイの完了を監視します。

   [![Azure Time Series Insights 環境のデプロイ成功](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>デバイスのシミュレーションの実行

これでデプロイおよび初期構成が完了しました。次は、[アクセラレータによって作成されたシミュレートされたデバイス](#create-a-device-simulation)からのサンプル データを Azure Time Series Insights 環境に入力します。

IoT ハブに加えて、シミュレートされたデバイス テレメトリを作成して転送する Azure App Service Web アプリケーションが生成されました。

1. [ソリューション アクセラレータのダッシュ ボード](https://www.azureiotsolutions.com/Accelerators#dashboard)に戻ります。 必要に応じて、このチュートリアルで使用している同じ Azure アカウントを使用して再度サインインします。 [Device Solution]\(デバイス ソリューション\)、 **[ソリューション アクセラレータに移動]** の順に選択して、デプロイされたソリューションを起動します。

   [![ソリューション アクセラレータのダッシュボード](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. デバイス シミュレーション Web アプリは、最初に Web アプリケーションに "**サインインとプロファイルの読み取り**" アクセス許可を付与することをユーザーに促します。 このアクセス許可により、アプリケーションがアプリケーションの機能をサポートするのに必要なユーザー プロファイル情報を取得することを許可します。

   [![デバイス シミュレーション Web アプリケーションの同意](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. **[+ New simulation]\(+ 新しいシミュレーション\)** を選択します。 **[Simulation setup]\(シミュレーションのセットアップ\)** ページの読み込み後、次の必須パラメーターを入力します。

   パラメーター|説明
   ---|---
   **[Target IoT Hub]\(IoT Hub をターゲットにする\)** | **[Use pre-provisioned IoT Hub]\(事前プロビジョニングされている IoT Hub を使用する\)** を選択します。
   **[デバイス モデル]** | **[Chiller]** を選択します。
   **[Number of devices]\(デバイスの数\)**  | **[数量]** に `10` と入力します。
   **[Telemetry frequency]\(テレメトリ頻度\)** | `10` 秒を入力します。
   **[Simulation duration]\(シミュレーション期間\)** | **[End in:]\(実行時間:\)** を選択し、`5` 分と入力します。

   完了したら、 **[シミュレーションの開始]** を選択します。 シミュレーションは、合計で 5 分間実行されます。 1,000 台のシミュレートされたデバイスから 10 秒ごとにデータを生成します。

   [![デバイス シミュレーションのセットアップ](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. シミュレーションの実行中、約 10 秒ごとに **[Total messages]\(メッセージの総数\)** フィールドと **[Messages per second]\(1 秒ごとのメッセージ数\)** フィールドが更新されることに注目してください。 シミュレーションは約 5 分後に終了し、 **[Simulation setup]\(シミュレーションのセットアップ\)** に戻ります。

   [![実行中のデバイス シミュレーション](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>テレメトリ データを確認する

この最後のセクションでは、テレメトリ データが作成され、Azure Time Series Insights 環境に格納されたことを確認します。 データを確認するには、Azure Time Series Insights Explorer を使用します。これは、テレメトリ データのクエリと分析に使用します。

1. Azure Time Series Insights 環境のリソース グループの **[概要]** ページに戻ります。 Azure Time Series Insights 環境を選択します。

   [![Azure Time Series Insights 環境のリソース グループと環境](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. Azure Time Series Insights 環境の **[概要]** ページで、 **[Time Series Insights エクスプローラーの URL]** を選択して Azure Time Series Insights Explorer を開きます。

   [![Azure Time Series Insights Explorer](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. Azure Time Series Insights Explorer では、Azure portal アカウントを使用して読み込みと認証を行います。 最初は、Azure Time Series Insights 環境に入力されたグラフ エリアが、シミュレートされたテレメトリ データと共に表示されます。 時間の範囲を限定するには、左上隅のドロップダウンを選択します。 デバイス シミュレーションの期間が含まれる時間の範囲を入力します。 次に、検索の虫眼鏡をクリックします。

   [![Azure Time Series Insights Explorer の時間の範囲フィルター](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. 時間の範囲を絞り込むと、IoT ハブや Azure Time Series Insights 環境に対する明らかなデータ転送バーストの部分にグラフをズームインできます。 また、右上隅の "**Streaming complete**" (ストリーミングが完了しました) のテキストには、見つかったイベントの合計数が表示されます。 **[Interval size]\(間隔のサイズ\)** スライダーをドラッグして、グラフのプロット細分性を制御することもできます。

   [![Azure Time Series Insights Explorer の時間の範囲フィルターが適用されたビュー](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. 最後に、リージョンを左クリックして、範囲をフィルター処理することもできます。 その後右クリックして **[イベントの探索]** を使用してタブ形式の **[イベント]** ビューでイベントの詳細を表示します。

   [![Azure Time Series Insights Explorer の時間の範囲フィルターが適用されたビューとイベント](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルでは、Azure Time Series Insights 環境とデバイス シミュレーション ソリューションをサポートする、稼働する Azure サービスをいくつか作成します。 これらを削除するには、Azure portal に戻ります。

Azure portal の左側のメニューで、次のようにします。

1. **[リソース グループ]** アイコンを選択します。 次に、Azure Time Series Insights 環境用に作成したリソース グループを選択します。 ページの上部で **[リソース グループの削除]** を選択し、リソース グループの名前を入力して、 **[削除]** を選択します。

1. **[リソース グループ]** アイコンを選択します。 次に、デバイス シミュレーション ソリューション アクセラレータによって作成されたリソース グループを選択します。 ページの上部で **[リソース グループの削除]** を選択し、リソース グループの名前を入力して、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
>
> * Azure Time Series Insights 環境を作成する。
> * IoT ハブを含むデバイス シミュレーション ソリューションを作成する。
> * Azure Time Series Insights 環境を IoT ハブに接続する。
> * デバイス シミュレーションを実行して Azure Time Series Insights 環境にデータをストリーム配信する。
> * シミュレートされたテレメトリ データを確認する。

独自の Azure Time Series Insights 環境を作成する方法を確認したので、Azure Time Series Insights 環境のデータを使用する Web アプリケーションを構築する方法について確認します。

> [!div class="nextstepaction"]
> [ホストされているクライアント SDK のビジュアル化の例](https://tsiclientsample.azurewebsites.net/)