---
title: サービス プリンシパルでの認証
description: Azure Active Directory サービス プリンシパルを使用して、プライベート コンテナー レジストリ内のイメージへのアクセスを提供します。
ms.topic: article
ms.date: 03/15/2021
ms.openlocfilehash: 117929df4c8b0fa7a54a23bcc039294122cd5afa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651885"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>サービス プリンシパルによる Azure Container Registry 認証

Azure Active Directory (Azure AD) サービス プリンシパルを使用して、コンテナー レジストリへのプッシュ、プル、またはその他のアクセス許可を提供できます。 サービス プリンシパルを使うことで、「ヘッドレス」のサービスとアプリケーションへのアクセスを提供できます。

## <a name="what-is-a-service-principal"></a>サービス プリンシパルとは

Azure AD の "[*サービス プリンシパル*](../active-directory/develop/app-objects-and-service-principals.md)" は、サブスクリプション内の Azure リソースへのアクセスを提供します。 サービス プリンシパルはサービスのユーザー ID と考えることができます。ここで "サービス" は、リソースへのアクセスが必要なアプリケーション、サービス、またはプラットフォームです。 指定したそれらのリソースのみをスコープとするアクセス権を持つサービス プリンシパルを構成できます。 その後、サービス プリンシパルの資格情報を使用してそれらのリソースにアクセスするようにアプリケーションまたはサービスを構成します。

Azure Container Registry のコンテキストでは、Azure 内のプライベート レジストリに対するプル、プッシュとプル、またはその他のアクセス許可を持つ Azure AD サービス プリンシパルを作成できます。 完全な一覧については、[Azure Container Registry のロールとアクセス許可](container-registry-roles.md)に関するページを参照してください。

## <a name="why-use-a-service-principal"></a>サービス プリンシパルを使う理由

Azure AD サービス プリンシパルを使うことで、スコープを設定したプライベート コンテナー レジストリへのアクセスを提供できます。 各アプリケーションや各サービスに異なるサービス プリンシパルを作成し、それぞれにレジストリへの調整済みのアクセス権を持たせます。 また、サービスやアプリケーションの間での資格情報の共有を避けることができるため、資格情報を交換したり、選択したサービス プリンシパル (および対象とするアプリケーション) のみのアクセスを取り消したりできます。

たとえば、イメージの `pull` アクセスのみを提供するサービス プリンシパルを使用するように Web アプリケーションを構成し、一方でビルド システムでは `push` と `pull` 両方のアクセスを提供するサービス プリンシパルを使用します。 アプリケーション開発の所有者が変わった場合は、ビルド システムに影響を与えずにそのサービス プリンシパルの資格情報を交換することができます。

## <a name="when-to-use-a-service-principal"></a>サービス プリンシパルを使う場合

サービス プリンシパルは、**ヘッドレス シナリオ** でレジストリへのアクセスを提供する際に使う必要があります。 つまり、自動的にまたはそれ以外の無人の方法でコンテナー イメージをプッシュまたはプルする必要があるすべてのアプリケーション、サービス、またはスクリプトが対象です。 次に例を示します。

  * *Pull*: レジストリからオーケストレーション システム (Kubernetes、DC/OS、Docker Swarm など) にコンテナーをデプロイします。 また、コンテナー レジストリから、[Azure Container Instances](container-registry-auth-aci.md)、[App Service](../app-service/index.yml)、[Batch](../batch/index.yml)、[Service Fabric](../service-fabric/index.yml) などの関連する Azure サービスにプルすることもできます。

    > [!TIP]
    > Azure コンテナー レジストリからイメージをプルするには、いくつかの [Kubernetes シナリオ](authenticate-kubernetes-options.md)でサービス プリンシパルをお勧めします。 またAzure Kubernetes Service (AKS) を使用して、クラスターの[マネージド ID ](../aks/cluster-container-registry-integration.md)を有効にすることで、自動メカニズムを使用してターゲット レジストリ で認証することもできます。 
  * *Push*: コンテナー イメージを作成し、継続的インテグレーションと Azure Pipelines や Jenkins などのデプロイ ソリューションを使用してレジストリにプッシュします。

コンテナー イメージを開発用ワークステーションに手動でプルするときなど、レジストリへの個別アクセスでは、代わりに各自の [Azure AD ID](container-registry-authentication.md#individual-login-with-azure-ad) を使ってレジストリにアクセスすることをお勧めします ([az acr login][az-acr-login] を使うなど)。

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>サンプルのスクリプト

Azure CLI の以前のサンプル スクリプトを GitHub 上で検索できます。各バージョンの Azure PowerShell についても同様です。

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>サービス プリンシパルでの認証

コンテナー レジストリへのアクセスが許可されているサービス プリンシパルがある場合は、"ヘッドレス" サービスおよびアプリケーションにアクセスするための資格情報を構成するか、`docker login` コマンドを使用してそれらを入力することができます。 次の値を使用します。

* **ユーザー名** - サービス プリンシパルの **アプリケーション (クライアント) ID**
* **パスワード** - サービス プリンシパルの **パスワード (クライアント シークレット)**

各値の形式は `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` です。 

> [!TIP]
> [az ad sp credential reset](/cli/azure/ad/sp/credential#az_ad_sp_credential_reset) コマンドを実行することで、サービス プリンシパルのパスワード (クライアント シークレット) を再生成できます。
>

### <a name="use-credentials-with-azure-services"></a>Azure サービスで資格情報を使用する

サービス プリンシパルの資格情報は、Azure Container Registry に対して認証を行うあらゆる Azure サービスから使用できます。  さまざまなシナリオで、レジストリの管理者の資格情報の代わりにサービス プリンシパルの資格情報を使用します。

たとえば、その資格情報を使用して Azure Container Registry から [Azure Container Instances](container-registry-auth-aci.md) にイメージをプルします。

### <a name="use-with-docker-login"></a>Docker ログインで使用する

サービス プリンシパルを使用して `docker login` を実行できます。 次の例では、サービス プリンシパルのアプリケーション ID が環境変数 `$SP_APP_ID` に、パスワードが変数 `$SP_PASSWD` に渡されます。 Docker 資格情報の管理の推奨プラクティスについては、[docker login](https://docs.docker.com/engine/reference/commandline/login/) コマンドのリファレンスを参照してください。

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

ログインすると、Docker によって資格情報がキャッシュされます。

### <a name="use-with-certificate"></a>証明書と共に使用する

サービス プリンシパルに証明書を追加した場合は、証明書ベースの認証を使用して Azure CLI にサインインし、[az acr login][az-acr-login] コマンドを使用してレジストリにアクセスできます。 パスワードの代わりに証明書をシークレットとして使用すると、CLI を使用するときのセキュリティが強化されます。 

[サービス プリンシパルを作成](/cli/azure/create-an-azure-service-principal-azure-cli)するときに、自己署名証明書を作成できます。 または、既存のサービス プリンシパルに 1 つ以上の証明書を追加します。 たとえば、この記事のスクリプトの 1 つを使用して、レジストリからイメージをプルまたはプッシュする権限を持つサービス プリンシパルを作成または更新する場合は、[az ad sp credential reset][az-ad-sp-credential-reset] コマンドを使用して証明書を追加します。

証明書と共にサービス プリンシパルを使用して [Azure CLI](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal) にサインインするには、証明書を PEM 形式にして、秘密キーを含める必要があります。 証明書が必要な形式ではない場合は、`openssl` などのツールを使用して変換します。 [az login][az-login] を実行し、サービス プリンシパルを使用して CLI にサインインする場合は、サービス プリンシパルのアプリケーション ID と Active Directory テナント ID も指定します。 これらの値を環境変数として指定する例を次に示します。

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

次に、[az acr login][az-acr-login] を実行し、レジストリによる認証を受けます。

```azurecli
az acr login --name myregistry
```

CLI では、`az login` を実行したときに作成されたトークンが使用され、セッションがレジストリによる認証を受けます。

## <a name="create-service-principal-for-cross-tenant-scenarios"></a>テナント間シナリオ用のサービス プリンシパルを作成する

サービス プリンシパルは、ある Azure Active Directory (テナント) 内のコンテナー レジストリから別の サービスまたはアプリにイメージをプルする必要がある Azure のシナリオでも使用できます。 たとえば、組織は、テナント B の共有コンテナー レジストリからイメージをプルする必要があるアプリをテナント A で実行する場合があります。

テナント間のシナリオでコンテナー レジストリで認証できるサービス プリンシパルを作成するには、以下を行います。

*  テナント A で[マルチテナント アプリ](../active-directory/develop/single-and-multi-tenant-apps.md) (サービス プリンシパル) を作成する 
* テナント B でアプリをプロビジョニングする
* テナント B のレジストリからプルするサービス プリンシパルのアクセス許可を付与する
* 新しいサービス プリンシパルを使用して認証を行うテナント A のサービスまたはアプリを更新する

手順の例については、[「コンテナー レジストリから別の AD テナント内の AKS クラスターにイメージ をプルする」](authenticate-aks-cross-tenant.md)を参照してください。

## <a name="next-steps"></a>次のステップ

* Azure Container Registry による認証を受けるその他のシナリオについては、[認証の概要](container-registry-authentication.md)に関する記事を参照してください。

* Azure キー コンテナーを使用してコンテナー レジストリのサービス プリンシパルの資格情報を格納および取得する例については、[ACR タスクを使用したコンテナー イメージの構築とデプロイ](container-registry-tutorial-quick-task.md)に関するチュートリアルを参照してください。

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-login]: /cli/azure/reference-index#az_login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az_ad_sp_credential_reset
