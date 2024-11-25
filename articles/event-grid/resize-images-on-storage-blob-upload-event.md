---
title: チュートリアル:Azure Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する
description: チュートリアル:Azure Event Grid は、Azure Storage での BLOB アップロードをトリガーできます。 これを使って、Azure Storage にアップロードされたイメージ ファイルを、サイズ変更や他の改善のために Azure Functions などの他のサービスに送信することができます。
ms.topic: tutorial
ms.date: 09/28/2021
ms.openlocfilehash: 74e7905bbf548b0864a9b871eba964a5759a91a6
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234131"
---
# <a name="tutorial-automate-resizing-uploaded-images-using-event-grid"></a>チュートリアル:Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する

[Azure Event Grid](overview.md) は、クラウドのイベント処理サービスです。 Event Grid を使うと、Azure サービスまたはサード パーティのリソースによって生成されるイベントのサブスクリプションを作成できます。  

このチュートリアルは、ストレージ チュートリアル シリーズの第 2 部です。 [前のストレージ チュートリアル][previous-tutorial]に、Azure Event Grid と Azure Functions を使うサーバーレスの自動サムネイル生成機能を追加します。 Event Grid により、[Azure Functions](../azure-functions/functions-overview.md) は [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) のイベントに応答して、アップロードされたイメージのサムネイルを生成できます。 Blob Storage の作成イベントに対して、イベント サブスクリプションが作成されます。 特定の Blob Storage コンテナーに BLOB が追加されると、関数エンドポイントが呼び出されます。 Event Grid から関数バインドに渡されたデータが、BLOB へのアクセスとサムネイル イメージの生成に使われます。

既存のイメージ アップロード アプリにサイズ変更機能を追加するには、Azure CLI と Azure Portal を使います。

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

![ブラウザーでの発行済みの Web アプリ (\.NET v12 SDK 用) を示すスクリーンショット。](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)

![ブラウザーでの発行済みの Web アプリ (\.NET v10 SDK 用) を示すスクリーンショット。](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Storage アカウントの作成
> * Azure Functions を使ってサーバーレス コードをデプロイします
> * Event Grid で Blob Storage のイベント サブスクリプションを作成します

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このチュートリアルを完了するには、以下が必要です。

- [Azure サブスクリプション](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)が必要です。 このチュートリアルでは、**無料** のサブスクリプションは使用できません。 
- 前の Blob ストレージのチュートリアル「[Azure Storage を使用してクラウドに画像データをアップロードする][previous-tutorial]」を完了している必要があります。  

## <a name="create-an-azure-storage-account"></a>Azure Storage アカウントの作成
Azure Functions には、一般的なストレージ アカウントが必要です。 前のチュートリアルで作成した BLOB ストレージ アカウントに加え、リソース グループ内に一般的なストレージ アカウントを別に作成します。 ストレージ アカウント名の長さは 3 から 24 文字である必要があり、数字と小文字のみを使用できます。

前のチュートリアルで作成したリソース グループの名前、作成するリソースの場所、Azure Functions に必要な新しいストレージ アカウントの名前を保持する変数を設定します。 その後に、Azure 関数用のストレージ アカウントを作成します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) コマンドを使用します。

1. リソース グループの名前を指定します。 

    ```azurepowershell-interactive
    $resourceGroupName="myResourceGroup"
    ```
2. ストレージ アカウントの場所を指定します。

    ```azurepowershell-interactive
    $location="eastus"    
    ```
3. 関数によって使用されるストレージ アカウントの名前を指定します。

    ```azurepowershell-interactive
    $functionstorage="<name of the storage account to be used by the function>"    
    ```
4. ストレージ アカウントを作成します。 

    ```azurepowershell-interactive
    New-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $functionstorage -Location $location -SkuName Standard_LRS -Kind StorageV2        
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az storage account create](/cli/azure/storage/account) コマンドを使用します。

> [!NOTE]
> Cloud Shell の Bash シェルでは、次のコマンドを使用します。 必要に応じて、Cloud Shell の左上隅にあるドロップダウン リストを使用して、Bash シェルに切り替えます。 

1. リソース グループの名前を指定します。 

    ```azurecli-interactive
    resourceGroupName="myResourceGroup"    
    ```
2. ストレージ アカウントの場所を指定します。

    ```azurecli-interactive
    location="eastus"
    ```
3. 関数によって使用されるストレージ アカウントの名前を指定します。

    ```azurecli-interactive
    functionstorage="<name of the storage account to be used by the function>"
    ```
4. ストレージ アカウントを作成します。 

    ```azurecli-interactive
    az storage account create --name $functionstorage --location $location --resource-group $resourceGroupName --sku Standard_LRS --kind StorageV2
    ```

---

## <a name="create-a-function-app"></a>Function App を作成する  

関数の実行環境をホストするには Function App が必要です。 Function App は、関数コードのサーバーレスの実行環境を提供します。

以下のコマンドには、実際に使用する一意の関数アプリ名を使用してください。 この関数アプリ名は、関数アプリの既定の DNS ドメインとして使用されます。そのため、名前は Azure のすべてのアプリ間で一意である必要があります。

作成する関数アプリの名前を指定し、Azure 関数を作成します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

関数アプリの作成には、[New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) コマンドを使用します。

1. 関数アプリの名前を指定します。 

    ```azurepowershell-interactive
    $functionapp="<name of the function app>"    
    ```
2. 関数アプリを作成します。 

    ```azurepowershell-interactive
    New-AzFunctionApp -Location $location -Name $functionapp -ResourceGroupName $resourceGroupName -Runtime PowerShell -StorageAccountName $functionstorage    
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Function App の作成には、[az functionapp create](/cli/azure/functionapp) コマンドを使用します。

1. 関数アプリの名前を指定します。 

    ```azurecli-interactive
    functionapp="<name of the function app>"
    ```
2. 関数アプリを作成します。 

    ```azurecli-interactive
    az functionapp create --name $functionapp --storage-account $functionstorage --resource-group $resourceGroupName --consumption-plan-location $location --functions-version 2    
    ```

---

ここで、[前のチュートリアル][previous-tutorial]で作成した BLOB ストレージ アカウントに接続するように関数アプリを構成します。

## <a name="configure-the-function-app"></a>Function App を構成する

関数には、BLOB ストレージ アカウントの資格情報が必要となります。これを関数アプリのアプリケーション設定に追加するには、[az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) または [Update-AzFunctionAppSetting](/powershell/module/az.functions/update-azfunctionappsetting) の各コマンドを使用します。

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName --settings "AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2 FUNCTIONS_WORKER_RUNTIME=dotnet"
```

```azurepowershell-interactive
$storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName --name $blobStorageAccount --query connectionString --output tsv)

Update-AzFunctionAppSetting -Name $functionapp -ResourceGroupName $resourceGroupName -AppSetting @{AzureWebJobsStorage=$storageConnectionString; THUMBNAIL_CONTAINER_NAME=thumbnails; THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2; 'FUNCTIONS_WORKER_RUNTIME'='dotnet'}
```

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)

```azurecli-interactive
blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName -n $blobStorageAccount --query [0].value --output tsv)

storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName --settings "FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString FUNCTIONS_WORKER_RUNTIME=node"
```

---

`FUNCTIONS_EXTENSION_VERSION=~2` 設定によって、関数アプリは Azure Functions ランタイムのバージョン 2.x で動作するようになります。

この Function App に関数コード プロジェクトをデプロイできるようになります。

## <a name="deploy-the-function-code"></a>関数コードをデプロイする 

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

C# のサイズ変更関数のサンプルは、[GitHub](https://github.com/Azure-Samples/function-image-upload-resize) で入手できます。 [az functionapp deployment source config](/cli/azure/functionapp/deployment/source) コマンドを使って、このコード プロジェクトを関数アプリにデプロイします。

```azurecli-interactive
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName --branch master --manual-integration --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)

Node.js のサイズ変更関数のサンプルは、[GitHub](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10) で入手できます。 [az functionapp deployment source config](/cli/azure/functionapp/deployment/source) コマンドを使って、この Functions コード プロジェクトを関数アプリにデプロイします。

```azurecli-interactive
az functionapp deployment source config --name $functionapp \
  --resource-group $resourceGroupName --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node-v10
```

---

イメージのサイズ変更関数は、Event Grid サービスからその関数に送信される HTTP 要求によってトリガーされます。 Event Grid には、イベントのサブスクリプションを作成して関数の URL でこれらの通知を取得することを指示します。 このチュートリアルでは、BLOB によって作成されたイベントをサブスクライブします。

Event Grid の通知から関数に渡されるデータには、BLOB の URL が含まれます。 その URL は、Blob Storage からアップロードされたイメージを取得するために入力バインドに渡されます。 関数はサムネイル イメージを生成し、結果のストリームを Blob Storage 内の別のコンテナーに書き込みます。

このプロジェクトでは、トリガーの種類として `EventGridTrigger` が使用されています。 汎用の HTTP トリガーよりも Event Grid トリガーを使用することをお勧めします。 Event Grid では、Event Grid 関数トリガーが自動的に検証されます。 汎用 HTTP トリガーの場合は、[検証応答](security-authentication.md)を実装する必要があります。

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

この関数の詳細については、[function.json ファイルと run.csx ファイル](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/ImageFunctions)を参照してください。

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)

この関数の詳細については、[function.json ファイルと index.js ファイル](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10/tree/master/Thumbnail)を参照してください。

---

関数プロジェクトのコードは、パブリック サンプル リポジトリから直接デプロイされます。 Azure Functions のデプロイ オプションについて詳しくは、「[Azure Functions の継続的なデプロイ](../azure-functions/functions-continuous-deployment.md)」をご覧ください。

## <a name="create-an-event-subscription"></a>イベント サブスクリプションの作成

イベント サブスクリプションは、どのプロバイダー生成イベントを特定のエンドポイントに送信するかを示します。 この場合、エンドポイントは関数によって公開されます。 Azure Portal で関数に通知を送信するイベント サブスクリプションを作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) のページの上部で `Function App` を検索して選択し、先ほど作成した関数アプリを選択します。 **[関数]** を選択し、**Thumbnail** 関数を選択します。

    :::image type="content" source="media/resize-images-on-storage-blob-upload-event/choose-thumbnail-function.png" alt-text="ポータルで Thumbnail 関数を選択する":::

1.  **[統合]** を選択し、 **[イベント グリッド トリガー]** を選択して、 **[Event Grid サブスクリプションの作成]** を選択します。

    :::image type="content" source="./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png" alt-text="Azure portal で [Event Grid サブスクリプションの追加] に移動する" :::

1. 次の表で指定されているようにイベント サブスクリプションを設定します。
    
    ![Azure Portal で関数からイベント サブスクリプションを作成する](./media/resize-images-on-storage-blob-upload-event/event-subscription-create.png)

    | 設定      | 推奨値  | 説明                                        |
    | ------------ | ---------------- | -------------------------------------------------- |
    | **名前** | imageresizersub | 新しいイベント サブスクリプションを示す名前。 |
    | **トピックの種類** | ストレージ アカウント | ストレージ アカウント イベント プロバイダーを選びます。 |
    | **サブスクリプション** | お使いの Azure サブスクリプション | 既定では、現在の Azure サブスクリプションが選択されています。 |
    | **リソース グループ** | myResourceGroup | **[既存のものを使用]** を選び、このチュートリアルで使っているリソース グループを選びます。 |
    | **リソース** | お使いの BLOB ストレージ アカウント | 作成した Blob Storage アカウントを選びます。 |
    | **[システム トピック名]** | imagestoragesystopic | システム トピックの名前を指定します。 システム トピックについては、[システム トピックの概要](system-topics.md)に関するページを参照してください。 |    
    | **イベントの種類** | Blob created (作成された BLOB) | **[Blob created]\(作成された BLOB\)** 以外のすべての種類をオフにします。 `Microsoft.Storage.BlobCreated` のイベントの種類のみが関数に渡されます。 |
    | **[エンドポイントの種類]** | 自動生成 | **Azure Function** としてあらかじめ定義されています。 |
    | **エンドポイント** | 自動生成 | 関数の名前です。 この場合は、**Thumbnail** です。 |

1. **[フィルター]** タブに切り替えて、次のアクションを実行します。
    1. **[サブジェクト フィルタリングを有効にする]** オプションを選択します。
    1. **[次で始まるサブジェクト]** には、「 **/blobServices/default/containers/images/** 」と入力します。

        ![イベント サブスクリプションのフィルターを指定する](./media/resize-images-on-storage-blob-upload-event/event-subscription-filter.png)

1. **[作成]** を選択して、イベント サブスクリプションを追加します。 これにより、BLOB が `images` コンテナーに追加されたときに `Thumbnail` 関数をトリガーするイベント サブスクリプションが作成されます。 この関数により、イメージはサイズが変更されて、`thumbnails` コンテナーに追加されます。

バックエンド サービスの構成が済んだので、サンプル Web アプリでイメージ サイズ変更の機能をテストします。

## <a name="test-the-sample-app"></a>サンプル アプリをテストする

Web アプリでイメージのサイズ変更をテストするには、公開したアプリの URL を参照します。 Web アプリの既定の URL は、`https://<web_app>.azurewebsites.net` です。

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

**[Upload photos]** 領域をクリックし、ファイルを選んでアップロードします。 この領域に写真をドラッグしてもかまいません。

アップロードされたイメージが消えた後、アップロードされたイメージのコピーが **[Generated Thumbnails]** 領域に表示されることを確認します。 この画像は、関数によってサイズが変更され、*thumbnails* コンテナーに追加された後、Web クライアントによってダウンロードされたものです。

![ブラウザーでの発行済みの "ImageResizer" というタイトルの Web アプリ (\.NET v12 SDK 用) を示すスクリーンショット。](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)

**[Choose File]** をクリックしてファイルを選び、 **[Upload Image]** をクリックします。 アップロードが成功すると、ブラウザーが成功ページに移動します。 リンクをクリックすると、ホーム ページに戻ります。 **[Generated Thumbnails]** 領域に、アップロードされた画像のコピーが表示されます。 画像が表示されない場合は、ページを再度読み込んでみてください。この画像は、関数によってサイズが変更され、*thumbnails* コンテナーに追加された後、Web クライアントによってダウンロードされたものです。

![ブラウザーでの発行された Web アプリ](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 通常の Azure ストレージ アカウントの作成
> * Azure Functions を使ってサーバーレス コードをデプロイします
> * Event Grid で Blob Storage のイベント サブスクリプションを作成します

ストレージ チュートリアル シリーズの第 3 部に進み、ストレージ アカウントへのアクセスをセキュリティで保護する方法について学習します。

> [!div class="nextstepaction"]
> [クラウド内のアプリケーションのデータへのアクセスをセキュリティで保護する](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

+ Event Grid について詳しくは、「[Azure Event Grid の概要](overview.md)」をご覧ください。
+ Azure Functions を利用するもう 1 つのチュートリアルを試すには、「[Azure Logic Apps と統合される関数を作成する](../azure-functions/functions-twitter-email.md)」をご覧ください。

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md
