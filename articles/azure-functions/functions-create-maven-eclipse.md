---
title: Java と Eclipse を使用して Azure Functions アプリを作成する
description: Java と Eclipse を使用して、単純な HTTP によってトリガーしたサーバーレス アプリを Azure Functions に公開するためのハウツー ガイド。
author: ggailey777
ms.topic: how-to
ms.date: 07/01/2018
ms.author: glenga
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: 3720ac243fd90be23c18f760977dc2a0d13e0412
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2021
ms.locfileid: "112989113"
---
# <a name="create-your-first-function-with-java-and-eclipse"></a>Java と Eclipse を使用して初めての関数を作成する 

この記事では、Eclipse IDE と Apache Maven を使用して、[サーバーレス](https://azure.microsoft.com/solutions/serverless/)関数プロジェクトを作成し、テストおよびデバッグして、Azure Functions にデプロイする方法を説明します。 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>開発環境を設定する

Java と Eclipse を使用して関数アプリを開発するには、以下のものがインストールされている必要があります。

-  [Java Developer Kit](https://www.azul.com/downloads/zulu/) バージョン 8。
-  [Apache Maven](https://maven.apache.org) バージョン 3.0 以降。
-  Java と Maven をサポートする [Eclipse](https://www.eclipse.org/downloads/packages/)。
-  [Azure CLI](/cli/azure)

> [!IMPORTANT] 
> このクイックスタートを行うには、JAVA_HOME 環境変数を JDK のインストール場所に設定する必要があります。

Azure Functions を実行およびデバッグするためのローカル環境を提供する、[Azure Functions Core Tools、バージョン 2](functions-run-local.md#v2) もインストールすることを強くお勧めします。 

## <a name="create-a-functions-project"></a>Functions プロジェクトを作成する

1. Eclipse で、 **[File]\(ファイル\)** メニューを選択し、 **[New]\(新規作成\) &gt; [Maven Project]\(Maven プロジェクト\)** を選択します。 
1. **[New Maven Project]\(新しい Maven プロジェクト\)** ダイアログを既定値のままにして **[Next]\(次へ\)** を選択します。
1. [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype) を探して選択し、 **[次へ]** をクリックします。
1. `resourceGroup`、`appName`、`appRegion` を含むすべてのフィールドに値を入力し (**fabrikam-function-20170920120101928** とは異なる appName を使用してください)、最後に **[完了]** をクリックします。
    ![Eclipse Maven の作成 2](media/functions-create-first-java-eclipse/functions-create-eclipse2.png)  

Maven は、_artifactId_ という名前の新しいフォルダーに、プロジェクト ファイルを作成します。 プロジェクトで生成されるコードは、トリガーする HTTP 要求の本文をエコーする、[HTTP によってトリガーされる](./functions-bindings-http-webhook.md)単純な関数です。

## <a name="run-functions-locally-in-the-ide"></a>IDE で関数をローカルで実行する

> [!NOTE]
> 関数をローカルで実行およびデバッグするには、[Azure Functions Core Tools、バージョン 2](functions-run-local.md#v2) をインストールする必要があります。

1. 生成されたプロジェクトを右クリックし、 **[別のユーザーとして実行]** と **[Maven ビルド]** を選択します。
1. **[構成の編集]** ダイアログで、 **[目標]** フィールドに「`package`」と入力し、 **[実行]** を選択します。 関数コードがビルドされ、パッケージ化されます。
1. ビルドが完了したら、目標と名前に `azure-functions:run` を指定して前述のように別の実行構成を作成します。 **[実行]** を選択して IDE で関数を実行します。

関数のテストが完了したら、コンソール ウィンドウのランタイムを終了します。 アクティブにして同時にローカルで実行できる関数ホストは 1 つだけです。

### <a name="debug-the-function-in-eclipse"></a>Eclipse で関数をデバッグする

前の手順で設定した **[別のユーザーとして実行]** で、`azure-functions:run` を `azure-functions:run -DenableDebug` に変更し、更新された構成を実行してデバッグ モードで関数アプリを起動します。

**[実行]** メニューを選択し、 **[デバッグ構成]** を開きます。 **[Remote Java Application]\(リモート Java アプリケーション\)** を選択し、新しいアプリケーションを作成します。 構成に名前を付けて、設定を入力します。 ポートは、関数ホストによって開かれたデバッグ ポート (既定では `5005`) と同じにする必要があります。 設定が完了したら、[`Debug`] をクリックしてデバッグを開始します。

![Eclipse での関数のデバッグ](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

IDE を使用して、ブレークポイントを設定し、関数内のオブジェクトを検査します。 完了したら、デバッガーと実行中の関数ホストを停止します。 アクティブにして同時にローカルで実行できる関数ホストは 1 つだけです。

## <a name="deploy-the-function-to-azure"></a>関数を Azure にデプロイする

Azure Functions へのデプロイ プロセスでは、Azure CLI からアカウントの資格情報を使います。 コンピューターのコマンド プロンプトを使用し続ける前に、[Azure CLI でログイン](/cli/azure/authenticate-azure-cli)します。

```azurecli
az login
```

新しい **別のユーザーとして実行** 構成で `azure-functions:deploy`Maven 目標を使用して、新しい関数アプリにコードを展開します。

デプロイが完了すると、Azure Functions アプリへのアクセスに使うことができる URL が表示されます。

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

## <a name="next-steps"></a>次のステップ

- 「[Azure Functions Java developer guide](functions-reference-java.md)」(Azure Functions Java 開発者ガイド) で、Java 関数の開発の詳細について確認します。
- `azure-functions:add` Maven ターゲットを使って、異なるトリガーの新しい関数をプロジェクトに追加します。
