---
title: 方法 - ソース コードから Spring Cloud アプリケーションを起動する
description: このクイックスタートでは、ソース コードから Azure Spring Cloud でアプリケーションを直接起動する方法について説明します
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/12/2021
ms.author: karler
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 0815f48fd9b194a84566138b25b4bedc97de1a83
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490328"
---
# <a name="how-to-deploy-spring-boot-applications-from-azure-cli"></a>Azure CLI から Spring Boot アプリケーションをデプロイする方法

**この記事の適用対象:** ✔️ Java

Azure Spring Cloud を使用して Azure で Spring Boot アプリケーションを有効にすることができます。

Java ソース コードまたはビルド済み JAR からアプリケーションを直接起動できます。 この記事では、デプロイ手順について説明します。

このクイックスタートでは、以下の方法について説明します。

> [!div class="checklist"]
> * サービス インスタンスをプロビジョニングする
> * インスタンスに構成サーバーを設定する
> * ローカルでアプリケーションをビルドする
> * 各アプリケーションをデプロイする
> * アプリケーションのパブリック エンドポイントを割り当てる

## <a name="prerequisites"></a>前提条件

開始する前に、ご利用の Azure サブスクリプションで、以下の必要な依存関係があることを確認します。

1. [Git をインストールする](https://git-scm.com/)
2. [JDK 8 をインストールする](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Maven 3.0 以上をインストールする](https://maven.apache.org/download.cgi)
4. [Azure CLI のインストール](/cli/azure/install-azure-cli)
5. [Azure サブスクリプションにサインアップする](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。  最新バージョンの Git、JDK、Maven、Azure CLI など、一般的な Azure ツールがプレインストールされています。 Azure サブスクリプションにログインしている場合は、shell.azure.com から [Azure Cloud Shell](https://shell.azure.com) を起動します。  Azure Cloud Shell の詳細については、[ドキュメントを参照](../cloud-shell/overview.md)してください

## <a name="install-the-azure-cli-extension"></a>Azure CLI 拡張機能をインストールする

次のコマンドを使用して、Azure CLI 用の Azure Spring Cloud 拡張機能をインストールします

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Azure CLI を使用してサービス インスタンスをプロビジョニングする

Azure CLI にサインインし、アクティブなサブスクリプションを選択します。

```azurecli
az login
az account list -o table
az account set --subscription
```

Azure Spring Cloud でサービスを格納するリソース グループを作成します。 Azure リソース グループの詳細については[こちら](../azure-resource-manager/management/overview.md)をご覧ください。

```azurecli
az group create --location eastus --name <resource-group-name>
```

次のコマンドを実行して、Azure Spring Cloud のインスタンスをプロビジョニングします。 Azure Spring Cloud のサービスの名前を準備します。 名前は 4 から 32 文字で、小文字、数字、およびハイフンのみを使用できます。 サービス名の最初の文字は英字でなければならず、最後の文字は英字または数字でなければなりません。

```azurecli
az spring-cloud create --resource-group <resource-group-name> --name <resource-name>
```

サービス インスタンスのデプロイには約 5 分かかります。

次のコマンドを使用して、既定のリソース グループ名と Azure Spring Cloud インスタンス名を設定します。

```azurecli
az config set defaults.group=<service-group-name>
az config set defaults.spring-cloud=<service-instance-name>
```

## <a name="create-the-application-in-azure-spring-cloud"></a>Azure Spring Cloud でアプリケーションを作成する

次のコマンドで、自分のサブスクリプションに Azure Spring Cloud でアプリケーションを作成します。  それにより、アプリケーションをアップロードできる空のサービスが作成されます。

```azurecli
az spring-cloud app create --name <app-name>
```

## <a name="deploy-your-spring-boot-application"></a>Spring Boot アプリケーションをデプロイする

ビルド済みの JAR から、あるいは Gradle または Maven リポジトリからアプリケーションをデプロイできます。  ケースごとに次の手順を行います。

### <a name="deploy-a-pre-built-jar"></a>ビルド済みの JAR をデプロイする

ローカル コンピューター上にビルドされた JAR からデプロイするには、ビルドによって [fat-JAR](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven) が生成されることを確認します。

アクティブなデプロイに fat-JAR をデプロイするには

```azurecli
az spring-cloud app deploy --name <app-name> --jar-path <path-to-fat-JAR>
```

特定のデプロイに fat-JAR をデプロイするには

```azurecli
az spring-cloud app deployment create --app <app-name> \
    --name <deployment-name> \
    --jar-path <path-to-fat-JAR>
```

### <a name="deploy-from-source-code"></a>ソース コードからのデプロイ

Azure Spring Cloud では、[kpack](https://github.com/pivotal/kpack) を使用してプロジェクトをビルドします。  Azure CLI を使用してソース コードをアップロードし、kpack を使用してプロジェクトをビルドして、ターゲット アプリケーションにデプロイすることができます。

> [!WARNING]
> このプロジェクトでは、`target` (Maven デプロイの場合) または `build/libs` (Gradle デプロイの場合) の `MANIFEST.MF` に `main-class` エントリがある JAR ファイルを生成するのは 1 つだけにする必要があります。  `main-class` のエントリを持つ複数の JAR ファイルを使用すると、デプロイが失敗します。

単一モジュールの Maven/Gradle プロジェクトの場合:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy --name <app-name>
```

複数のモジュールを含む Maven/Gradle プロジェクトの場合は、モジュールごとに以下を繰り返します。

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy --name <app-name> \
    --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>デプロイ ログの表示

次のコマンドを使用して、kpack ビルド ログを確認します。

```azurecli
az spring-cloud app show-deploy-log --name <app-name>
```

> [!NOTE]
> kpack ログには、そのデプロイが kpack を使用してソースからビルドされた場合にのみ、最新のデプロイが表示されます。

## <a name="assign-a-public-endpoint-to-gateway"></a>ゲートウェイにパブリック エンドポイントを割り当てる

1. **[アプリケーション ダッシュボード]** ページを開きます。
2. `gateway` アプリケーションを選択して、**[アプリケーションの詳細]** ページを表示します。
3. **[Assign Endpoint]\(エンドポイントの割り当て\)** を選択して、ゲートウェイにパブリック エンドポイントを割り当てます。 これには数分かかることがあります。
4. 実行中のアプリケーションを表示するには、割り当てられたパブリック IP をブラウザーに入力します。

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、次の方法について説明しました。

> [!div class="checklist"]
> * サービス インスタンスをプロビジョニングする
> * インスタンスに構成サーバーを設定する
> * ローカルでアプリケーションをビルドする
> * 各アプリケーションをデプロイする
> * アプリケーションの環境変数を編集する
> * アプリケーション ゲートウェイにパブリック IP を割り当てる

> [!div class="nextstepaction"]
> [Spring Cloud のログ、メトリック、トレース](./quickstart-logs-metrics-tracing.md)

その他のサンプルを GitHub で入手できます ([Azure Spring Cloud のサンプル](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql))。
