---
title: クイック スタート - Azure Communication Services でリソースを作成して管理する
titleSuffix: An Azure Communication Services quickstart
description: このクイック スタートでは、最初の Azure Communication Services リソースを作成して管理する方法について説明します。
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: arm
zone_pivot_groups: acs-plat-azp-azcli-net-ps
ms.openlocfilehash: e3be24cda914edbf4f3ae3474ff43eef438fce20
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635643"
---
# <a name="quickstart-create-and-manage-communication-services-resources"></a>クイック スタート:Communication Services のリソースを作成して管理する

最初の Communication Services リソースをプロビジョニングすることで、Azure Communication Services を開始します。 Communication Services のリソースは、[Azure portal](https://portal.azure.com) または .NET Management SDK を使用してプロビジョニングできます。 Management SDK と Azure portal を使用すると、リソースの作成、構成、更新、削除を行うことができます。また、これは [Azure Resource Manager](../../azure-resource-manager/management/overview.md) (Azure のデプロイおよび管理サービス) と連携します。 SDK で使用できるすべての機能は、Azure portal で利用できます。 


> [!WARNING]
> Communication Services は複数の地域で利用できますが、電話番号を取得するには、リソースのデータ一が 'US' に設定されている必要があります。 Azure Communication Services のリソースと同時にリソース グループを作成できないことにも注意してください。 リソースを作成するときは、既に作成されているリソース グループを使用する必要があります。

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/create-resource-azp.md)]
::: zone-end

::: zone pivot="platform-azcli"
[!INCLUDE [Azure CLI](./includes/create-resource-azcli.md)]
::: zone-end

::: zone pivot="platform-net"
[!INCLUDE [.NET](./includes/create-resource-net.md)]
::: zone-end

::: zone pivot="platform-powershell"
[!INCLUDE [PowerShell](./includes/create-resource-powershell.md)]
::: zone-end


## <a name="access-your-connection-strings-and-service-endpoints"></a>接続文字列とサービス エンドポイントにアクセスする

接続文字列を使用すると、Communication Services の SDK から Azure への接続と認証を行うことができます。 Communication Services の接続文字列とサービス エンドポイントには、Azure portal から、または Azure Resource Manager API を使用してプログラムでアクセスできます。

Communication Services リソースに移動した後、ナビゲーション メニューから **[キー]** を選択し、Communication Services の SDK で使用される **[接続文字列]** または **[エンドポイント]** の値をコピーします。 主キーとセカンダリ キーにアクセスできることに注意してください。 これは、Communication Services リソースへの一時的なアクセスをサード パーティまたはステージング環境に提供するシナリオで役に立ちます。

:::image type="content" source="./media/key.png" alt-text="Communication Services のキーのページのスクリーンショット。":::

特定のリソースのキーやリソース グループなど、重要な情報には、Azure CLI を使用してアクセスすることもできます。 

[Azure CLI](/cli/azure/install-azure-cli-windows?tabs=azure-cli) をインストールし、次のコマンドを使用してログインします。 Azure アカウントを使用して接続するには、資格情報を入力する必要があります。
```azurecli
az login
```

これで、リソースに関する重要な情報にアクセスできるようになりました。
```azurecli
az communication list --resource-group "<resourceGroup>"

az communication list-key --name "<communicationName>" --resource-group "<resourceGroup>"
```

特定のサブスクリプションを選択したい場合は、```--subscription``` フラグを指定してサブスクリプション ID を指定することもできます。
```
az communication list --resource-group  "resourceGroup>"  --subscription "<subscriptionID>"

az communication list-key --name "<communicationName>" --resource-group "resourceGroup>" --subscription "<subscriptionID>"
```

## <a name="store-your-connection-string"></a>接続文字列を格納する

Communication Services の SDK は、接続文字列を使用して、Communication Services に対して行われた要求を承認します。 接続文字列は次の方法で保存できます。

* デスクトップまたはデバイスで実行するアプリケーションの場合は、**app.config** ファイルまたは **web.config** ファイルに接続文字列を保存できます。 この接続文字列は、これらのファイルの **AppSettings** セクションに追加します。
* Azure App Service で実行されているアプリケーションでは、接続文字列は [App Service のアプリケーションの設定](../../app-service/configure-common.md)内に格納できます。 ポータル内の [アプリケーションの設定] タブの **[接続文字列]** セクションに接続文字列を追加します。
* 接続文字列は、[Azure Key Vault](../../data-factory/store-credentials-in-key-vault.md) に格納できます。
* アプリケーションをローカルで実行している場合は、環境変数に接続文字列を格納することもできます。

### <a name="store-your-connection-string-in-an-environment-variable"></a>環境変数に接続文字列を格納する

環境変数を構成するには、コンソール ウィンドウを開き、以下のタブからお使いのオペレーティン グシステムを選択します。 `<yourconnectionstring>` は、実際の接続文字列に置き換えてください。

#### <a name="windows"></a>[Windows](#tab/windows)

コンソール ウィンドウを開き、次のコマンドを入力します。

```console
setx COMMUNICATION_SERVICES_CONNECTION_STRING "<yourconnectionstring>"
```

実行中のプログラムのうち、環境変数の読み取りを必要とするプログラム (コンソール ウィンドウを含む) については、環境変数を追加した後で再起動が必要となる場合があります。 たとえば、Visual Studio をエディターとして使用している場合、サンプルを実行する前に Visual Studio を再起動します。

#### <a name="macos"></a>[macOS](#tab/unix)

**.zshrc** を編集し、環境変数を追加します。

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source ~/.zshrc` を実行します。 IDE が開いている状態で環境変数を作成した場合、その変数にアクセスするには、エディター、IDE、またはシェルを閉じて、再度開くことが必要な場合があります。

#### <a name="linux"></a>[Linux](#tab/linux)

**.bash_profile** を編集し、環境変数を追加します。

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source ~/.bash_profile` を実行します。 IDE が開いている状態で環境変数を作成した場合、その変数にアクセスするには、エディター、IDE、またはシェルを閉じて、再度開くことが必要な場合があります。

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 

リソースの削除時にリソースに電話番号が割り当てられている場合、その電話番号は同時にリソースから自動的に解放されます。 

> [!Note]
> リソースの削除は **永続的** であり、リソースを削除すると、イベント ガード フィルター、電話番号など、リソースに関連付けられたデータを含むデータを復元できなくなります。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、次の方法について説明しました。

> [!div class="checklist"]
> * Communication Services リソースを作成する
> * リソースの地域およびタグを構成する
> * そのリソースのキーにアクセスする
> * リソースを削除する

> [!div class="nextstepaction"]
> [最初のユーザー アクセス トークンを作成する](access-tokens.md)
