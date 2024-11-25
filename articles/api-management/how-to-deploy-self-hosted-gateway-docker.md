---
title: Docker にセルフホステッド ゲートウェイをデプロイする
description: Azure API Management のセルフホステッド ゲートウェイ コンポーネントを Docker にデプロイする方法について説明します
author: dlepow
manager: gwallace
ms.service: api-management
ms.topic: article
ms.date: 04/19/2021
ms.author: danlep
ms.openlocfilehash: 3ef8e0316b6df0b95f2163b6df8ae139ebb8fe6b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580948"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Azure API Management のセルフホステッド ゲートウェイを Docker にデプロイする

この記事では、Azure API Management のセルフホステッド ゲートウェイ コンポーネントを Docker 環境にデプロイする手順について説明します。

> [!NOTE]
> セルフホステッド ゲートウェイを Docker でホストすることは、評価と開発のユースケースに最適です。 運用環境での使用には Kubernetes が推奨されます。 Kubernetes にセルフホステッド ゲートウェイをデプロイする方法については、[こちらの](how-to-deploy-self-hosted-gateway-kubernetes.md)ドキュメントを参照してください。

## <a name="prerequisites"></a>前提条件

- [Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関するクイックスタートを完了します
- Docker 環境を作成します。 [デスクトップ向けの Docker](https://www.docker.com/products/docker-desktop) は、開発および評価の目的に適したオプションです。 Docker のすべてのエディション、その機能、および Docker 自体に関する包括的なドキュメントについては、「[Docker Documentation (Docker ドキュメント)](https://docs.docker.com)」を参照してください。
- [API Management インスタンスにゲートウェイ リソースをプロビジョニングします](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> セルフホステッド ゲートウェイは、x86-64 の Linux ベースの Docker コンテナーとしてパッケージ化されます。

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Docker にセルフホステッド ゲートウェイをデプロイする

1. **[Deployment and infrastructure]\(デプロイとインフラストラクチャ\)** から **[ゲートウェイ]** を選択します。
2. デプロイするゲートウェイ リソースを選択します。
3. **[Deployment]/(デプロイ/)** を選択します。
4. **[トークン]** テキスト ボックスのアクセス トークンが、既定の **[有効期限]** および **[秘密鍵]** の値を使用して、自動生成されたことに注意してください。 必要に応じて、いずれかまたは両方のコントロールで必要な値を選択して、新しいトークンを生成します。
5. **[デプロイ スクリプト]** 下で **[Docker]** が選択されていることを確認します。
6. **[環境]** の横にある **env.conf** ファイル リンクを選択して、ファイルをダウンロードします。
7. **[実行]** テキスト ボックスの右端にある **[コピー]** アイコンを選択して、Docker コマンドをクリップボードにコピーします。
8. コマンドをターミナル (またはコマンド) ウィンドウに貼り付けます。 必要に応じて、ポート マッピングとコンテナー名を調整します。 コマンドでは、ダウンロードした環境ファイルが現在のディレクトリに存在することが前提であることに注意してください。

   ```
   docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
   ```

9. コマンドを実行します。 コマンドでは、Microsoft Container Registry からダウンロードした[コンテナー イメージ](https://aka.ms/apim/sputnik/dhub)を使用してコンテナーを実行し、コンテナーの HTTP (8080) ポートと HTTPS (8081) ポートをホスト上のポート 80 と 443 にマップするように、Docker 環境に対して指示します。
10. 次のコマンドを実行して、ゲートウェイ コンテナーが実行中であるかどうかを確認します。
    ```console
    docker ps
    CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
    895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:latest   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
    ```
10. Azure portal に戻り、 **[概要]** をクリックして、デプロイしたばかりのセルフホステッド ゲートウェイ コンテナーが正常な状態を報告していることを確認します。

    ![ゲートウェイの状態](media/how-to-deploy-self-hosted-gateway-docker/status.png)

> [!TIP]
> `console docker container logs <gateway-name>` コマンドを使用して、セルフホステッド ゲートウェイ ログのスナップショットを表示します。
>
> `docker container logs --help` コマンドを使用して、すべてのログ表示オプションを表示します。

## <a name="next-steps"></a>次のステップ

* セルフホステッド ゲートウェイの詳細については、[Azure API Management のセルフホステッド ゲートウェイの概要](self-hosted-gateway-overview.md)に関するページを参照してください。
* [セルフホステッド ゲートウェイのカスタム ドメイン名を構成する](api-management-howto-configure-custom-domain-gateway.md)。
