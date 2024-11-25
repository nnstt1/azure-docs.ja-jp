---
title: チュートリアル - Azure Spring Cloud でサーキット ブレーカー ダッシュボードを使用する
description: Azure Spring Cloud でサーキット ブレーカー ダッシュボードを使用する方法について説明します。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 6fce05bf1f853edd527df920f579689e23f080c8
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015665"
---
# <a name="tutorial-use-circuit-breaker-dashboard-with-azure-spring-cloud"></a>チュートリアル:Azure Spring Cloud でサーキット ブレーカー ダッシュボードを使用する

**この記事の適用対象:** ✔️ Java

Spring [Cloud Netflix Turbine](https://github.com/Netflix/Turbine) とは、Hystrix ダッシュボードを使用して単一のビューでストリームを監視できるように複数の [Hystrix](https://github.com/Netflix/Hystrix) メトリック ストリームを集約するためのものであり、広く使用されています。 このチュートリアルでは、Azure Spring Cloud 上でそれらを使用する方法について説明します。

> [!NOTE]
> Netflix Hystrix は、既存の多くの Spring Cloud アプリで広く使用されていますが、現在活発な開発は行われていません。 プロジェクトを新規に開発する場合は、[resilience4j](https://github.com/resilience4j/resilience4j) のような Spring Cloud Circuit Breaker の実装を代わりに使用してください。 このチュートリアルで示した Turbine とは異なり、新しい Spring Cloud Circuit Breaker フレームワークでは、そのメトリック データ パイプラインのすべての実装が Micrometer に統合されます。これも、Azure Spring Cloud によってサポートされています。 [詳細については、こちらを参照してください](./how-to-circuit-breaker-metrics.md)。

## <a name="prepare-your-sample-applications"></a>サンプル アプリケーションを準備する

サンプルは、この[リポジトリ](https://github.com/StackAbuse/spring-cloud/tree/master/spring-turbine)からフォークされます。

ご利用の開発環境にサンプル リポジトリを複製します。

```bash
git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
cd Azure-Spring-Cloud-Samples/hystrix-turbine-sample
```

このチュートリアルで使用する 3 つのアプリケーションをビルドします。

* user-service: /personalized/{id} という単一のエンドポイントを備えたシンプルな REST サービス
* recommendation-service: /recommendations という単一のエンドポイントを備えたシンプルな REST サービスです。これは、user-service によって呼び出されます。
* hystrix-turbine: Hystrix ストリームを表示する Hystrix ダッシュボード サービスと、他のサービスから Hystrix メトリック ストリームを集約する Turbine サービス。

```bash
mvn clean package -D skipTests -f user-service/pom.xml
mvn clean package -D skipTests -f recommendation-service/pom.xml
mvn clean package -D skipTests -f hystrix-turbine/pom.xml
```

## <a name="provision-your-azure-spring-cloud-instance"></a>Azure Spring Cloud インスタンスをプロビジョニングする

手順「[Azure CLI でサービス インスタンスをプロビジョニングする](./quickstart.md#provision-an-instance-of-azure-spring-cloud)」に従ってください。

## <a name="deploy-your-applications-to-azure-spring-cloud"></a>Azure Spring Cloud にアプリケーションをデプロイする

これらのアプリでは **Config Server** は使用されません。そのため、Azure Spring Cloud 用に **Config Server** を設定する必要はありません。  次のように作成してデプロイします。

```azurecli
az spring-cloud app create -n user-service --assign-endpoint
az spring-cloud app create -n recommendation-service
az spring-cloud app create -n hystrix-turbine --assign-endpoint

az spring-cloud app deploy -n user-service --jar-path user-service/target/user-service.jar
az spring-cloud app deploy -n recommendation-service --jar-path recommendation-service/target/recommendation-service.jar
az spring-cloud app deploy -n hystrix-turbine --jar-path hystrix-turbine/target/hystrix-turbine.jar
```

## <a name="verify-your-apps"></a>アプリを確認する

すべてのアプリが実行中で検出可能になったら、ブラウザーから `https://<username>-user-service.azuremicroservices.io/personalized/1` パスを使用して `user-service` にアクセスします。 user-service から `recommendation-service` にアクセスできる場合は、次の出力が表示されます。 Web ページが動作しない場合は、何度か更新してください。

```json
[{"name":"Product1","description":"Description1","detailsLink":"link1"},{"name":"Product2","description":"Description2","detailsLink":"link3"},{"name":"Product3","description":"Description3","detailsLink":"link3"}]
```

## <a name="access-your-hystrix-dashboard-and-metrics-stream"></a>Hystrix ダッシュボードとメトリックストリームにアクセスする

パブリック エンドポイントまたはプライベート テスト エンドポイントを使用して確認します。

### <a name="using-public-endpoints"></a>パブリック エンドポイントの使用

ご利用のブラウザーからパス `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/hystrix` を使用して hystrix-turbine にアクセスします。  次の図に、このアプリで実行されている Hystrix ダッシュボードを示します。

![Hystrix ダッシュボード](media/spring-cloud-circuit-breaker/hystrix-dashboard.png)

Turbine ストリームの URL `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/turbine.stream?cluster=default` をテキスト ボックスにコピーし、 **[Monitor Stream]\(ストリームの監視\)** を選択します。  これでダッシュボードが表示されます。 ビューアーに何も表示されない場合は、`user-service` エンドポイントにアクセスしてストリームを生成してください。

![Hystrix ストリーム](media/spring-cloud-circuit-breaker/hystrix-stream.png) これで、サーキット ブレーカー ダッシュボードを試すことができるようになりました。

> [!NOTE]
> 運用環境では、Hystrix ダッシュボードとメトリック ストリームをインターネットに公開しないでください。

### <a name="using-private-test-endpoints"></a>プライベート テスト エンドポイントの使用

Hystrix メトリック ストリームには、`test-endpoint` からアクセスすることもできます。 バックエンド サービスとして、`recommendation-service` に対してパブリック エンドポイントを割り当てませんでしたが、そのメトリックは、`https://primary:<KEY>@<SERVICE-NAME>.test.azuremicroservices.io/recommendation-service/default/actuator/hystrix.stream` で test-endpoint を使用して表示できます。

![Hystrix test-endpoint ストリーム](media/spring-cloud-circuit-breaker/hystrix-test-endpoint-stream.png)

Web アプリとして、Hystrix ダッシュボードが `test-endpoint` 上で機能している必要があります。 正常に機能していない場合は、次の 2 つの理由が考えられます。1 つは、`test-endpoint` を使用したことで、ベース URL が `/` から `/<APP-NAME>/<DEPLOYMENT-NAME>` に変更されたこと、もう 1 つは Web アプリで静的リソースに絶対パスが使用されていることです。 `test-endpoint` で動作させるには、フロントエンド ファイル内の `<base>` を手動で編集することが必要になる場合があります。

## <a name="next-steps"></a>次のステップ

* [Azure CLI でサービス インスタンスをプロビジョニングする](./quickstart.md#provision-an-instance-of-azure-spring-cloud)
* [Azure Spring Cloud で Java Spring アプリケーションをデプロイ用に準備する](how-to-prepare-app-deployment.md)
