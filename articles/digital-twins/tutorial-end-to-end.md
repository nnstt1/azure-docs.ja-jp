---
title: チュートリアル:エンド ツー エンドのソリューションを接続する"
titleSuffix: Azure Digital Twins
description: このチュートリアルに従って、デバイス データによって駆動されるエンド ツー エンドの Azure Digital Twins ソリューションを構築する方法を学習します。
author: baanders
ms.author: baanders
ms.date: 10/19/2021
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 9233f33818e9961cc5daf8cf35b14457172ecae3
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504837"
---
# <a name="tutorial-build-out-an-end-to-end-solution"></a>チュートリアル:エンド ツー エンドのソリューションを構築する

この Azure Digital Twins のチュートリアルでは、サービスの機能を示すエンド ツー エンドのソリューションを構築する方法について説明します。 Azure Digital Twins インスタンスは、環境のライブ データによって駆動される完全なエンド ツー エンドのソリューションを設定するために、デバイスとデータの管理を目的とした他の Azure サービスに接続することができます。

このチュートリアルでは次のことを行います。
> [!div class="checklist"]
> * Azure Digital Twins インスタンスを設定する
> * サンプル ビルディング シナリオを把握し、事前に作成されたコンポーネントをインスタンス化する
> * [Azure Functions](../azure-functions/functions-overview.md) アプリを使用して、[IoT Hub](../iot-hub/about-iot-hub.md) デバイスから、シミュレートしたテレメトリをデジタル ツインのプロパティにルーティングする
> * Azure Functions、エンドポイント、ルートを使用してデジタル ツインの通知を処理することにより、**ツイン グラフ** 全体に変更を反映させる

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Cloud Shell セッションの設定
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="get-started-with-the-building-scenario"></a>ビルディングのシナリオの概要

このチュートリアルで使用するサンプル プロジェクトは、フロア、部屋、サーモスタット デバイスを含んだ実世界の **ビルディングのシナリオ** を表しています。 Azure Digital Twins インスタンスでは、これらのコンポーネントがデジタルで表現されます。ここでは、データの移動を可能にするために、Azure Digital Twins インスタンスを、[IoT Hub](../iot-hub/about-iot-hub.md) と [Event Grid](../event-grid/overview.md)、そして 2 つの [Azure Functions](../azure-functions/functions-overview.md) に接続しています。

次の図は、このシナリオ全体を表したものです。 

ここではまず、Azure Digital Twins インスタンス (図の **セクション A**) を作成します。その後、デジタル ツインへのテレメトリ データ フロー (**矢印 B**) を設定し、さらに、ツイン グラフ全体へのデータの反映 (**矢印 C**) を設定します。

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="さまざまな Azure サービスを介してデバイスと Azure Digital Twins の間を流れるデータが示された完全なビルディング シナリオの図。":::

このシナリオに取り組むためには、先ほどダウンロードしておいた、あらかじめ作成されたサンプル アプリのコンポーネントとやり取りする必要があります。

ビルディング シナリオの *AdtSampleApp* サンプル アプリに実装されているコンポーネントは次のとおりです。
* デバイスの認証 
* [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) の使用例 (*CommandLoop.cs* 内で確認可能)
* Azure Digital Twins API を呼び出すためのコンソール インターフェイス
* *SampleClientApp* - サンプル Azure Digital Twins ソリューション
* *SampleFunctionsApp* - IoT Hub および Azure Digital Twins のイベントから受け取ったテレメトリに基づいて Azure Digital Twins グラフを更新する Azure Functions アプリ

### <a name="instantiate-the-pre-created-twin-graph"></a>事前に作成されたツイン グラフをインスタンス化する

まず、サンプル プロジェクトの *AdtSampleApp* ソリューションを使用して、エンド ツー エンド シナリオの Azure Digital Twins 部分 (**セクション A**) を構築します。

:::image type="content" source="media/tutorial-end-to-end/building-scenario-a.png" alt-text="Azure Digital Twins インスタンス セクションが強調表示された、完全なビルディング シナリオからの抜粋図。":::

_**AdtE2ESample**_ プロジェクトが開いている Visual Studio ウィンドウで、ツール バーにあるこのボタンでプロジェクトを実行します。

:::image type="content" source="media/tutorial-end-to-end/start-button-sample.png" alt-text="SampleClientApp プロジェクトが開いている、Visual Studio のスタート ボタンのスクリーンショット。":::

コンソール ウィンドウが表示されて認証が行われ、コマンドを待つ状態となります。 このコンソールで、次のコマンドを実行して、サンプル Azure Digital Twins ソリューションをインスタンス化します。

> [!IMPORTANT]
> Azure Digital Twins インスタンスに既にデジタル ツインとリレーションシップが存在する場合、このコマンドを実行すると、それらは削除されて、サンプル シナリオのツインおよびリレーションシップに置き換えられます。

```cmd/sh
SetupBuildingScenario
```

このコマンドでは、Azure Digital Twins インスタンスで 3 つの[デジタル ツイン](concepts-twins-graph.md) (floor1 という名前のフロア、room21 という名前の部屋、thermostat67 という名前の温度センサー) が作成され、接続された際に一連の確認メッセージが出力されます。 これらのデジタル ツインは、実世界の環境に存在するであろうエンティティを表します。

それらがリレーションシップを介して接続され、以下の[ツイン グラフ](concepts-twins-graph.md)が形成されます。 ツイン グラフは、エンティティの相互作用や相互関係を含め、環境を全体として表します。

:::image type="content" source="media/tutorial-end-to-end/building-scenario-graph.png" alt-text="floor1 に room21 が含まれ、room21 に thermostat67 が含まれていることが示された図。" border="false":::

作成されたツインは、次のコマンドを実行し、接続されている Azure Digital Twins インスタンスに、そこに含まれるすべてのデジタル ツインを照会することで確認できます。

```cmd/sh
Query
```

[!INCLUDE [digital-twins-query-latency-note.md](../../includes/digital-twins-query-latency-note.md)]

プロジェクトの実行はもう停止してかまいません。 ただし、ソリューションは Visual Studio で開いたままにしてください。この後、チュートリアルが終わるまで使用することになります。

## <a name="set-up-the-sample-function-app"></a>サンプル関数アプリを設定する

次に、このチュートリアル全体を通してデータを処理する目的で使用される [Azure Functions アプリ](../azure-functions/functions-overview.md)を設定します。 この関数アプリ (*SampleFunctionsApp*) には、次の 2 つの関数が含まれています。
* *ProcessHubToDTEvents*: 受信した IoT Hub データを処理し、それに応じて Azure Digital Twins を更新します。
* *ProcessDTRoutedData*: デジタル ツインからのデータを処理し、それに応じて Azure Digital Twins 内の親ツインを更新します。

このセクションでは、事前に作成された関数アプリを発行し、Azure Active Directory (Azure AD) ID を割り当てることで、関数アプリから Azure Digital Twins に確実にアクセスできるようにします。 これらの手順が完了すると、このチュートリアルで以降、関数アプリに含まれている関数を使用できるようになります。 

_**AdtE2ESample**_ プロジェクトが開かれた状態の Visual Studio ウィンドウに戻ります。関数アプリは、_**SampleFunctionsApp**_ プロジェクト ファイルにあります。 *[ソリューション エクスプローラー]* ペインに表示されています。

### <a name="update-dependencies"></a>依存関係を更新する

アプリを発行する前に、依存関係が最新であることを確認するようにお勧めします。含まれているすべてのパッケージが最新バージョンであることを確認してください。

*[ソリューション エクスプローラー]* ペインで、_**[SampleFunctionsApp]** > [依存関係]_ の順に展開します。 *[パッケージ]* を右クリックし、 *[NuGet パッケージの管理]* を選択します。

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-1.png" alt-text="SampleFunctionsApp プロジェクトの [NuGet パッケージの管理] メニュー ボタンが表示されている Visual Studio のスクリーンショット。" border="false":::

これで、NuGet パッケージ マネージャーが開かれます。 *[更新]* タブを選択し、更新するパッケージがある場合は、 *[すべてのパッケージを選択]* ボックスをオンにします。 次に、 *[更新]* を選択します。

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-2.png" alt-text="NuGet パッケージ マネージャーですべてのパッケージを更新するための選択方法が示されている Visual Studio のスクリーンショット。":::

### <a name="publish-the-app"></a>アプリの発行

関数アプリを Azure に発行するには、まずストレージ アカウントを作成し、次に Azure に関数アプリを作成し、最後に関数を Azure 関数アプリに発行する必要があります。 このセクションでは、Azure CLI を使用してこれらのアクションを完了します。

1. 次のコマンドを使用して、**Azure ストレージ アカウント** を作成します。

    ```azurecli-interactive
    az storage account create --name <name-for-new-storage-account> --location <location> --resource-group <resource-group> --sku Standard_LRS
    ```

1. 次のコマンドを実行して **Azure 関数アプリ** を作成します。

    ```azurecli-interactive
    az functionapp create --name <name-for-new-function-app> --storage-account <name-of-storage-account-from-previous-step> --consumption-plan-location <location> --runtime dotnet --resource-group <resource-group>
    ```

1. 次に、関数を **zip** で圧縮し、新しい Azure 関数アプリに **発行** します。

    1. ローカル コンピューター上で PowerShell などのターミナルを開き、チュートリアルで前にダウンロードした [Digital Twins サンプル リポジトリ](https://github.com/azure-samples/digital-twins-samples/tree/master/)に移動します。 ダウンロードしたリポジトリ フォルダー内の *digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp* に移動します。
    
    1. ターミナルで次のコマンドを実行し、プロジェクトを発行します。

        ```powershell
        dotnet publish -c Release
        ```

        このコマンドにより、プロジェクトは *digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp\bin\Release\netcoreapp3.1\publish* ディレクトリに発行されます。

    1. *digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp\bin\Release\netcoreapp3.1\publish* ディレクトリにある、発行されたファイルの zip を作成します。 
        
        PowerShell を使用している場合、zip を作成するには、その *\publish* ディレクトリの完全なパスをコピーし、次のコマンドに貼り付けます。
    
        ```powershell
        Compress-Archive -Path <full-path-to-publish-directory>\* -DestinationPath .\publish.zip
        ```

        このコマンドレットにより、ターミナルのディレクトリの位置に **publish.zip** ファイルが作成されます。この中には *host.json* ファイルと、*bin*、*ProcessDTRoutedData*、および *ProcessHubToDTEvents* の各ディレクトリが含まれています。

        PowerShell を使用しておらず、`Compress-Archive` コマンドレットにアクセスできない場合は、エクスプローラーまたは他の方法を使用してファイルを zip で圧縮する必要があります。

1. Azure CLI で次のコマンドを実行し、発行され zip で圧縮された関数を Azure 関数アプリにデプロイします。

    ```azurecli-interactive
    az functionapp deployment source config-zip --resource-group <resource-group> --name <name-of-your-function-app> --src "<full-path-to-publish.zip>"
    ```

    > [!NOTE]
    > Azure CLI をローカルで使用している場合、お使いのマシン上でパスを使用して、コンピューター上の ZIP ファイルに直接アクセスできます。
    > 
    >Azure Cloud Shell を使用している場合は、コマンドを実行する前に、このボタンを使用して ZIP ファイルを Cloud Shell にアップロードします。
    >
    > :::image type="content" source="media/tutorial-end-to-end/azure-cloud-shell-upload.png" alt-text="ファイルをアップロードする方法が強調表示された Azure Cloud Shell のスクリーンショット。":::
    >
    > この場合、ファイルは Cloud Shell ストレージのルート ディレクトリにアップロードされるので、コマンドの `--src` パラメーターにファイル名を指定して (`--src publish.zip` のように) 直接参照することができます。

    デプロイが成功すると、状態コード 202 で応答され、新しい関数の詳細を含む JSON オブジェクトが出力されます。 結果にこのフィールドがあることで、デプロイの成功を確認できます。

    ```json
    {
      ...
      "provisioningState": "Succeeded",
      ...
    }
    ```

これで、Azure 関数アプリに関数が発行されました。

次に、関数アプリに Azure Digital Twins インスタンスへの適切なアクセス許可を与える必要があります。 このアクセスを次のセクションで構成します。

### <a name="configure-permissions-for-the-function-app"></a>関数アプリのアクセス許可を構成する

関数アプリが Azure Digital Twins インスタンスにアクセスするには、2 つの設定を行う必要があります。どちらも、Azure CLI を使用して実行できます。 

#### <a name="assign-access-role"></a>アクセス ロールの割り当て

最初の設定によって、関数アプリに Azure Digital Twins インスタンスの **Azure Digital Twins データ所有者** ロールが付与されます。 このロールは、インスタンスに対して多くのデータ プレーン アクティビティを実行するすべてのユーザーまたは関数に必要です。 セキュリティとロールの割り当てについては、「[Azure Digital Twins ソリューションのセキュリティ](concepts-security.md)」を参照してください。 

1. 次のコマンドを使用して、関数のシステム マネージド ID の詳細を確認します。 出力の **principalId** フィールドを書き留めてください。

    ```azurecli-interactive 
    az functionapp identity show --resource-group <your-resource-group> --name <your-function-app-name> 
    ```

    >[!NOTE]
    > 結果が空の場合は、ID の詳細を表示する代わりに、次のコマンドを使用して関数の新しいシステム マネージド ID を作成します。
    > 
    >```azurecli-interactive    
    >az functionapp identity assign --resource-group <your-resource-group> --name <your-function-app-name>  
    >```
    >
    > これで、出力には、次の手順で必要な **principalId** 値を含む、ID の詳細が表示されます。 

1. **principalId** 値を次のコマンドで使用して、関数アプリの ID を Azure Digital Twins インスタンスの **Azure Digital Twins Data Owner (Azure Digital Twins データ所有者)** ロールに割り当てます。

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

このコマンドを実行すると、作成したロールの割り当てに関する情報が出力されます。 これで関数アプリに、Azure Digital Twins インスタンスのデータへのアクセス許可が付与されました。

#### <a name="configure-application-settings"></a>アプリケーション設定の構成

2 番目の設定では、Azure Digital Twins インスタンスの URL を使用して、関数の **環境変数** を作成します。 関数コードでは、この変数の値を使用してインスタンスを参照します。 環境変数の詳細については、「[お使いの関数アプリの管理](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)」を参照してください。 

次のコマンドを実行して、プレースホルダーにリソースの詳細を設定します。

```azurecli-interactive
az functionapp config appsettings set --resource-group <your-resource-group> --name <your-function-app-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

出力は、Azure 関数の設定の一覧です。ここには、**ADT_SERVICE_URL** というエントリが含まれているはずです。


## <a name="process-simulated-telemetry-from-an-iot-hub-device"></a>IoT Hub デバイスからのシミュレートされたテレメトリを処理する

Azure Digital Twins グラフは、実デバイスからのテレメトリによって駆動するように作られています。 

このステップでは、[IoT Hub](../iot-hub/about-iot-hub.md) に登録されたサーモスタットのシミュレーション デバイスを、Azure Digital Twins におけるサーモスタット デバイスを表すデジタル ツインに接続します。 シミュレートされたデバイスがテレメトリを出力すると、そのデータが Azure 関数 (*ProcessHubToDTEvents*) に誘導され、そこでデジタル ツインへの対応する更新がトリガーされます。 こうして、デジタル ツインが、実デバイスのデータとの間で常に最新の状態に保たれます。 Azure Digital Twins では、イベント データを別の場所に誘導するプロセスが[イベントのルーティング](concepts-route-events.md)と呼ばれます。

シミュレートされたテレメトリの処理が行われるのは、エンド ツー エンド シナリオのこちらの部分 (**矢印 B**) です。

:::image type="content" source="media/tutorial-end-to-end/building-scenario-b.png" alt-text="Azure Digital Twins の前の要素を示すセクションが強調表示された、完全なビルディング シナリオ図からの抜粋の図。":::

このデバイス接続を設定するためには、こちらの操作を行う必要があります。
1. シミュレートされたデバイスを管理する IoT ハブを作成します。
2. イベント サブスクリプションを設定して IoT ハブを適切な Azure 関数に接続します。
3. シミュレートされたデバイスを IoT ハブに登録します。
4. シミュレートされたデバイスを実行してテレメトリを生成します。
5. Azure Digital Twins にクエリを実行して結果をライブで確認します。

### <a name="create-an-iot-hub-instance"></a>IoT Hub インスタンスを作成する

Azure Digital Twins は、デバイスとそのデータを管理するための Azure サービス、[IoT Hub](../iot-hub/about-iot-hub.md) と連携するように設計されています。 このステップでは、このチュートリアルのサンプル デバイスを管理する IoT ハブを設定します。

Azure Cloud Shell で、次のコマンドを使用して新しい IoT ハブを作成します。

```azurecli-interactive
az iot hub create --name <name-for-your-IoT-hub> --resource-group <your-resource-group> --sku S1
```

このコマンドからは、作成された IoT ハブについての情報が出力されます。

IoT ハブに付けた **名前** は保存しておいてください。 これは後で使用します。

### <a name="connect-the-iot-hub-to-the-azure-function"></a>IoT ハブを Azure 関数に接続する

次に、先ほど発行した関数アプリの Azure 関数 *ProcessHubToDTEvents* に IoT ハブを接続します。これで、IoT Hub 内のデバイスから関数へとデータが流れることができ、関数によって Azure Digital Twins が更新されます。

そのためには、Azure 関数をエンドポイントとする **イベント サブスクリプション** を IoT Hub に作成します。 これで、IoT Hub 内で発生したイベントを関数が "サブスクライブ" するようになります。

[Azure portal](https://portal.azure.com/) の上部の検索バーで、新しく作成した IoT ハブの名前を検索して、その IoT ハブに移動します。 ハブ メニューから *[イベント]* を選択し、 *[+ イベント サブスクリプション]* を選択します。

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1.png" alt-text="IoT Hub イベント サブスクリプションが示された Azure portal のスクリーンショット。":::

このオプションを選択すると、 *[イベント サブスクリプションの作成]* ページが表示されます。

:::image type="content" source="media/tutorial-end-to-end/event-subscription-2.png" alt-text="イベント サブスクリプションの作成方法が示された Azure portal のスクリーンショット。":::

各フィールドに次のように入力します (既定値が入力されるフィールドは省略しています)。
* *[イベント サブスクリプションの詳細]*  >  **[名前]** : イベント サブスクリプションに名前を付けます。
* *[トピックの詳細]*  >  **[システム トピック名]** : システム トピックに使用する名前を指定します。 
* *[イベントの種類]*  >  **[イベントの種類のフィルター]** : メニュー オプションから *[Device Telemetry]\(デバイス テレメトリ\)* を選択します。
* *[エンドポイントの詳細]*  >  **[エンドポイントのタイプ]** : メニュー オプションから *[Azure 関数]* を選択します。
* *[エンドポイントの詳細]* > **[エンドポイント]** : *[エンドポイントの選択]* リンクを選択します。これによって、 *[Azure 関数の選択]* ウィンドウが開きます。:::image type="content" source="media/tutorial-end-to-end/event-subscription-3.png" alt-text="Azure 関数を選択するためのウィンドウが示された Azure portal イベント サブスクリプションのスクリーンショット" border="false":::。
    - **サブスクリプション**、**リソース グループ**、**関数アプリ**、**関数** (*ProcessHubToDTEvents*) を入力します。 それらの値のいくつかは、サブスクリプションの選択後に自動的に入力されます。
    - **[選択の確認]** を選択します。

再び *[イベント サブスクリプションの作成]* ページで、 **[作成]** を選択します。

### <a name="register-the-simulated-device-with-iot-hub"></a>シミュレートされたデバイスを IoT Hub に登録する 

このセクションでは、IoT Hub におけるデバイスの表現を thermostat67 という ID で作成します。 シミュレートされたデバイスは、この表現に接続されます。このようにして、テレメトリ イベントはデバイスから IoT Hub に送信されます。 IoT ハブは、前の手順でサブスクライブされた Azure 関数がリッスンする場所です。イベントを選択して、処理を続ける準備ができています。

Azure Cloud Shell で、IoT Hub にデバイスを作成するには、次のコマンドを使用します。

```azurecli-interactive
az iot hub device-identity create --device-id thermostat67 --hub-name <your-IoT-hub-name> --resource-group <your-resource-group>
```

作成されたデバイスについての情報が出力されます。

### <a name="configure-and-run-the-simulation"></a>シミュレーションを構成して実行する

次に、作成した IoT Hub インスタンスにデータを送信するようにデバイス シミュレーターを構成します。

まず、次のコマンドで "*IoT ハブの接続文字列*" を取得します。

```azurecli-interactive
az iot hub connection-string show --hub-name <your-IoT-hub-name>
```

次に、"*デバイスの接続文字列*" を次のコマンドで取得します。

```azurecli-interactive
az iot hub device-identity connection-string show --device-id thermostat67 --hub-name <your-IoT-hub-name>
```

ローカル プロジェクトで、これらの値をデバイス シミュレーターのコードに挿入し、この IoT ハブと IoT ハブ デバイスにシミュレーターを接続します。

新しい Visual Studio ウィンドウで、(ダウンロードしたソリューションのフォルダーから) _[Device Simulator] > **[DeviceSimulator.sln]**_ を開きます。

>[!NOTE]
> これで、Visual Studio ウィンドウは 2 つになりました。_**DeviceSimulator.sln**_ のウィンドウと、先ほど開いた _**AdtE2ESample.sln**_ のウィンドウです。

この新しい Visual Studio ウィンドウの *[ソリューション エクスプローラー]* ペインから、_DeviceSimulator/**AzureIoTHub.cs**_ を選択して編集ウィンドウで開きます。 次の接続文字列の値は、先ほど収集した値に置き換えてください。

```csharp
iotHubConnectionString = <your-hub-connection-string>
deviceConnectionString = <your-device-connection-string>
```

ファイルを保存します。

今度は、設定したデータ シミュレーションの結果を確認するために、ツール バーにある次のボタンで **DeviceSimulator** プロジェクトを実行します。

:::image type="content" source="media/tutorial-end-to-end/start-button-simulator.png" alt-text="DeviceSimulator プロジェクトが開いた、Visual Studio のスタート ボタンのスクリーンショット。":::

コンソール ウィンドウが開いて、シミュレートされた温度のテレメトリ メッセージが表示されます。 これらのメッセージは IoT Hub に送信され、そこで Azure 関数によって収集されて処理されます。

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="送信中の温度テレメトリが示されたデバイス シミュレーターのコンソール出力のスクリーンショット。":::

このコンソールで行うべき作業は他にありませんが、次のステップに取り組む間、コンソールは実行したままにしておいてください。

### <a name="see-the-results-in-azure-digital-twins"></a>Azure Digital Twins で結果を確認する

先ほど発行した *ProcessHubToDTEvents* 関数は、IoT Hub データをリッスンし、Azure Digital Twins API を呼び出して、thermostat67 ツインの *Temperature* プロパティを更新します。

Azure Digital Twins 側からデータを確認するには、_**AdtE2ESample**_ プロジェクトが開いている Visual Studio ウィンドウに移動し、プロジェクトを実行します。

プロジェクトのコンソール ウィンドウが開いたら、次のコマンドを実行して、デジタル ツイン thermostat67 からレポートされる温度を取得します。

```cmd
ObserveProperties thermostat67 Temperature
```

"*Azure Digital Twins インスタンスから*" のライブ更新された温度が 2 秒ごとにコンソールにログされていることを確認できます。

>[!NOTE]
> デバイスからのデータがツインに伝達されるまでに数秒かかることがあります。 最初のいくつかの温度の測定値は、データが到着し始めるまで 0 と表示される場合があります。

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry.png" alt-text="デジタル ツイン thermostat67 からの温度メッセージのログが表示されたコンソール出力のスクリーンショット。":::

ライブの温度ログが正しく動作していることを確認したら、どちらのプロジェクトも実行を停止してかまいません。 Visual Studio のウィンドウは開いたままにしてください。以降、引き続きこのチュートリアルで使用します。

## <a name="propagate-azure-digital-twins-events-through-the-graph"></a>Azure Digital Twins のイベントをグラフ全体に反映させる

このチュートリアルではこれまで、外部のデバイス データから Azure Digital Twins を更新する方法を見てきました。 今度は、ある 1 つのデジタル ツインから Azure Digital Twins グラフ全体に変更を反映させる方法、つまり、サービス内部のデータからツインを更新する方法について見ていきます。

そのために、ここでは *ProcessDTRoutedData* という Azure 関数を使用して、Thermostat ツインが更新されたときに、接続された Room ツインを更新します。 更新機能が行われるのは、エンド ツー エンド シナリオのこちらの部分 (**矢印 C**) です。

:::image type="content" source="media/tutorial-end-to-end/building-scenario-c.png" alt-text="Azure Digital Twins の後の要素を示すセクションが強調表示された、完全なビルディング シナリオ図からの抜粋図。":::

このデータ フローを設定するためには、こちらの操作を行う必要があります。
1. [Event Grid トピックを作成する](#create-the-event-grid-topic)。これにより、Azure サービス間でデータを移動できるようにします
1. Azure Digital Twins に[エンドポイントを 作成する](#create-the-endpoint)。これは、インスタンスを Event Grid トピックに接続するためのものです
1. Azure Digital Twins 内に[ルートを設定する](#create-the-route)。これで、エンドポイントにツインのプロパティ変更イベントを送信します
1. [Azure 関数を設定する](#connect-the-azure-function)。これで、エンドポイントで Event Grid トピックをリッスンし、そこに送信されるツイン プロパティ変更イベントを受信し、グラフ内の他のツインを適宜更新します

[!INCLUDE [digital-twins-twin-to-twin-resources.md](../../includes/digital-twins-twin-to-twin-resources.md)]

### <a name="connect-the-azure-function"></a>Azure 関数を接続する

次に、先ほど作成した Event Grid トピックを Azure 関数 (*ProcessDTRoutedData*) からサブスクライブします。そうすることで、テレメトリ データは thermostat67 ツインから Event Grid トピックを介して関数へと流れることができ、関数が再び Azure Digital Twins に作用して room21 ツインを適宜更新します。

これを行うには、先ほど作成した **Event Grid トピック** から *ProcessDTRoutedData* Azure 関数にデータを送信する **Event Grid サブスクリプション** を作成します。

[Azure portal](https://portal.azure.com/) の上部の検索バーで、Event Grid トピックの名前を検索してそのトピックに移動します。 *[+ イベント サブスクリプション]* を選択します。

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1b.png" alt-text="Event Grid イベント サブスクリプションの作成方法が示された Azure portal のスクリーンショット。":::

このイベント サブスクリプションを作成する手順は、このチュートリアルの中で先ほど 1 つ目の Azure 関数から IoT Hub をサブスクライブしたときと同様です。 ただし今回は、リッスンするイベントの種類として "*デバイス テレメトリ*" を指定する必要はありません。また、接続先となる Azure 関数も異なります。

*[イベント サブスクリプションの作成]* ページで、各フィールドに次のように入力します (既定値が入力されるフィールドは省略しています)。
* *[イベント サブスクリプションの詳細]*  >  **[名前]** : イベント サブスクリプションに名前を付けます。
* *[エンドポイントの詳細]*  >  **[エンドポイントのタイプ]** : メニュー オプションから *[Azure 関数]* を選択します。
* *[エンドポイントの詳細]*  >  **[エンドポイント]** : [*エンドポイントの選択*] リンクを選択して、 *[Azure 関数の選択]* ウィンドウを開きます。
    - **サブスクリプション**、**リソース グループ**、**関数アプリ**、**関数** (*ProcessDTRoutedData*) を入力します。 それらの値のいくつかは、サブスクリプションの選択後に自動的に入力されます。
    - **[選択の確認]** を選択します。

再び *[イベント サブスクリプションの作成]* ページで、 **[作成]** を選択します。

## <a name="run-the-simulation-and-see-the-results"></a>シミュレーションを実行して結果を確認する

これで、イベントには、シミュレートされたデバイスから Azure Digital Twins にフローし、必要に応じて、Azure Digital Twins グラフを使用してツインを更新する機能が必要になります。 このセクションでは、デバイス シミュレーターをもう一度実行して、設定したイベント フロー全体を開始し、Azure Digital Twins にクエリを実行してライブの結果を確認します。

_**DeviceSimulator**_ プロジェクトが開いている Visual Studio ウィンドウに移動し、プロジェクトを実行します。

先ほどデバイス シミュレーターを実行したときと同様に、コンソール ウィンドウが開いて、シミュレートされた温度のテレメトリ メッセージが表示されます。 これらのイベントは、前半に設定したフローをたどって thermostat67 ツインを更新した後、後半に設定したフローをたどって room21 ツインを thermostat67 と同じ状態に更新します。

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="送信中の温度テレメトリが示されたデバイス シミュレーターのコンソール出力のスクリーンショット。":::

このコンソールで行うべき作業は他にありませんが、次のステップに取り組む間、コンソールは実行したままにしておいてください。

Azure Digital Twins 側からデータを確認するには、_**AdtE2ESample**_ プロジェクトが開いている Visual Studio ウィンドウに移動し、プロジェクトを実行します。

プロジェクトのコンソール ウィンドウが開いたら、次のコマンドを実行して、thermostat67 と room21 の **両方** のデジタル ツインによってレポートされる温度を取得します。

```cmd
ObserveProperties thermostat67 Temperature room21 Temperature
```

"*Azure Digital Twins インスタンスから*" のライブ更新された温度が 2 秒ごとにコンソールにログされていることを確認できます。 thermostat67 への変更に合わせて room21 の温度が更新されていることがわかります。

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry-b.png" alt-text="thermostat と room からの温度メッセージのログが示されたコンソール出力のスクリーンショット。":::

インスタンスからのライブの温度ログが正しく動作していることを確認したら、どちらのプロジェクトも実行を停止してかまいません。 また、チュートリアルはこれで完了なので、Visual Studio ウィンドウも閉じてかまいません。

## <a name="review"></a>確認

こちらは、このチュートリアルで構築したシナリオの確認です。

1. Azure Digital Twins インスタンスは、フロア、部屋、サーモスタットをデジタルで表現します (以下の図の **セクション A**)。
2. シミュレートされたデバイスのテレメトリが IoT Hub に送信されます。IoT Hub では、Azure 関数 *ProcessHubToDTEvents* がテレメトリ イベントをリッスンします。 Azure 関数 *ProcessHubToDTEvents* は、これらのイベント内の情報を使用して、thermostat67 の *Temperature* プロパティを設定します (図の **矢印 B**)。
3. Azure Digital Twins のプロパティ変更イベントが Event Grid トピックにルーティングされます。Event Grid トピックでは、Azure 関数 *ProcessDTRoutedData* がイベントをリッスンしています。 Azure 関数 *ProcessDTRoutedData* は、これらのイベント内の情報を使用して、room21 の *Temperature* プロパティを設定します (図の **矢印 C**)。

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="さまざまな Azure サービスを介してデバイスと Azure Digital Twins の間を流れるデータが示された完全なビルディング シナリオの図。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルを終えたら、次に行う作業に応じて、削除するリソースを選択できます。

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

* **この記事でセットアップした Azure Digital Twins インスタンスは引き続き使用するものの、そのモデル、ツイン、関係の一部または全部をクリアする場合** は、[Azure Cloud Shell](https://shell.azure.com) ウィンドウから [az dt](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) という CLI コマンドを使用して、目的の要素を削除することができます。

    この方法では、このチュートリアルで作成した他の Azure リソース (IoT ハブ、Azure Functions アプリなど) は一切削除されません。 これらのリソースは、それぞれの種類に合った適切な [dt コマンド](/cli/azure/reference-index?view=azure-cli-latest&preserve-view=true)を使用して個別に削除できます。

さらに、プロジェクト フォルダーもローカル コンピューターから削除してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、デバイスのライブ データによって駆動される Azure Digital Twins を紹介するエンド ツー エンドのシナリオを作成しました。

今度は、次のチュートリアルの概念ドキュメントを参照し、ここで扱った要素についてさらに理解を深めましょう。

> [!div class="nextstepaction"]
> [カスタム モデル](concepts-models.md)