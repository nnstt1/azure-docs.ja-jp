---
title: 自動 TLS を使用したイングレスの作成
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) クラスターで、自動的に TLS 証明書を生成する Let's Encrypt を使用する NGINX イングレス コントローラーをインストールおよび構成する方法について説明します。
services: container-service
ms.topic: article
ms.date: 04/23/2021
ms.openlocfilehash: 9de0d9e0c85afe7608f59a17ce28f2ba1f50c1bd
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553117"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で HTTPS イングレス コントローラーを作成する

イングレス コントローラーは、リバース プロキシ、構成可能なトラフィック ルーティング、および Kubernetes サービスの TLS 終端を提供するソフトウェアです。 個別の Kubernetes サービスのイングレス ルールとルートを構成するには、Kubernetes イングレス リソースが使われます。 イングレス コントローラーとイングレス ルールを使用すれば、1 つの IP アドレスで Kubernetes クラスター内の複数のサービスにトラフィックをルーティングできます。

この記事では、Azure Kubernetes Service (AKS) クラスターに [NGINX イングレス コントローラー][nginx-ingress]を展開する方法について説明します。 [Let's Encrypt][lets-encrypt] 証明書を自動的に生成して構成するには、[cert-manager][cert-manager] プロジェクトを使用します。 最後に、それぞれが 1 つの IP アドレスでアクセスできる、2 つのアプリケーションを AKS クラスターで実行します。

次のこともできます。

- [外部のネットワーク接続を使用して基本的なイングレス コントローラーを作成する][aks-ingress-basic]
- [HTTP アプリケーションのルーティング アドオンを有効にする][aks-http-app-routing]
- [内部のプライベート ネットワークと IP アドレスを使用するイングレス コントローラーを作成する][aks-ingress-internal]
- [ご自身の TLS 証明書を使用するイングレス コントローラーを作成する][aks-ingress-own-tls]
- [Let's Encrypt を使用して静的パブリック IP アドレス付きの TLS 証明書を自動的に生成するイングレス コントローラーを作成する][aks-ingress-static-tls]

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

また、この記事では、[カスタム ドメイン][custom-domain]の [DNS ゾーン][dns-zone]が、お使いの AKS クラスターと同じリソース グループにあることも前提としています。

この記事では [Helm 3][helm] を使用して、[サポートされているバージョンの Kubernetes][aks-supported versions] に NGINX イングレス コントローラーをインストールします。 最新リリースの Helm を使用しており、`ingress-nginx` および `jetstack` Helm リポジトリにアクセスできることを確認します。 この記事に記載されている手順は、以前のバージョンの Helm グラフ、NGINX イングレス コントローラー、または Kubernetes と互換性がない可能性があります。

Helm の構成および使用方法の詳細については、「[Azure Kubernetes Service (AKS) での Helm を使用したアプリケーションのインストール][use-helm]」を参照してください。 アップグレード手順については、「[Helm のインストール ドキュメント」を参照してください。Helm の構成と使用について詳しくは、「Azure Kubernetes Service (AKS) での Helm を使用したアプリケーションのインストール」を参照してください。][helm-install]

この記事ではまた、Azure CLI バージョン 2.0.64 以降を実行していることも必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

また、この記事では、ACR が統合された既存の AKS クラスターがあることを前提としています。 ACR が統合された AKS クラスターを作成する方法の詳細については、[Azure Kubernetes Service からの Azure Container Registry による認証][aks-integrated-acr]に関する記事を参照してください。

## <a name="import-the-images-used-by-the-helm-chart-into-your-acr"></a>Helm Chart で使用されるイメージを ACR にインポートする

この記事では、3 つのコンテナー イメージに依存する [NGINX イングレス コントローラー Helm Chart][ingress-nginx-helm-chart] を使用します。 これらのイメージを ACR にインポートするには、`az acr import` を使用します。

```azurecli
REGISTRY_NAME=<REGISTRY_NAME>
SOURCE_REGISTRY=k8s.gcr.io
CONTROLLER_IMAGE=ingress-nginx/controller
CONTROLLER_TAG=v1.0.4
PATCH_IMAGE=ingress-nginx/kube-webhook-certgen
PATCH_TAG=v1.1.1
DEFAULTBACKEND_IMAGE=defaultbackend-amd64
DEFAULTBACKEND_TAG=1.5
CERT_MANAGER_REGISTRY=quay.io
CERT_MANAGER_TAG=v1.5.4
CERT_MANAGER_IMAGE_CONTROLLER=jetstack/cert-manager-controller
CERT_MANAGER_IMAGE_WEBHOOK=jetstack/cert-manager-webhook
CERT_MANAGER_IMAGE_CAINJECTOR=jetstack/cert-manager-cainjector

az acr import --name $REGISTRY_NAME --source $SOURCE_REGISTRY/$CONTROLLER_IMAGE:$CONTROLLER_TAG --image $CONTROLLER_IMAGE:$CONTROLLER_TAG
az acr import --name $REGISTRY_NAME --source $SOURCE_REGISTRY/$PATCH_IMAGE:$PATCH_TAG --image $PATCH_IMAGE:$PATCH_TAG
az acr import --name $REGISTRY_NAME --source $SOURCE_REGISTRY/$DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG --image $DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG
az acr import --name $REGISTRY_NAME --source $CERT_MANAGER_REGISTRY/$CERT_MANAGER_IMAGE_CONTROLLER:$CERT_MANAGER_TAG --image $CERT_MANAGER_IMAGE_CONTROLLER:$CERT_MANAGER_TAG
az acr import --name $REGISTRY_NAME --source $CERT_MANAGER_REGISTRY/$CERT_MANAGER_IMAGE_WEBHOOK:$CERT_MANAGER_TAG --image $CERT_MANAGER_IMAGE_WEBHOOK:$CERT_MANAGER_TAG
az acr import --name $REGISTRY_NAME --source $CERT_MANAGER_REGISTRY/$CERT_MANAGER_IMAGE_CAINJECTOR:$CERT_MANAGER_TAG --image $CERT_MANAGER_IMAGE_CAINJECTOR:$CERT_MANAGER_TAG
```

> [!NOTE]
> コンテナー イメージを ACR にインポートするだけでなく、Helm Chart を ACR にインポートすることもできます。 詳細については、「[Azure コンテナー レジストリに対する Helm グラフのプッシュおよびプル][acr-helm]」を参照してください。

## <a name="create-an-ingress-controller"></a>イングレス コントローラーを作成する

イングレス コントローラーを作成するには、`helm` コマンドを使用して *nginx-ingress* をインストールします。 追加された冗長性については、NGINX イングレス コントローラーの 2 つのレプリカが `--set controller.replicaCount` パラメーターでデプロイされています。 イングレス コントローラーのレプリカの実行から十分にメリットを享受するには、AKS クラスターに複数のノードが存在していることを確認します。

イングレス コントローラーも Linux ノード上でスケジュールする必要があります。 Windows Server ノードでは、イングレス コントローラーを実行しないでください。 ノード セレクターは、`--set nodeSelector` パラメーターを使用して指定され、Linux ベース ノード上で NGINX イングレス コントローラーを実行するように Kubernetes スケジューラに指示されます。

> [!TIP]
> 次の例では、*ingress-basic* という名前のイングレス リソースの Kubernetes 名前空間が作成され、その名前空間内で動作することを想定しています。 必要に応じて、ご自身の環境の名前空間を指定できます。

> [!TIP]
> クラスター内のコンテナーへの要求で[クライアント ソース IP の保持][client-source-ip]を有効にする場合は、Helm インストール コマンドに `--set controller.service.externalTrafficPolicy=Local` を追加します。 クライアント ソース IP が要求ヘッダーの *X-Forwarded-For* の下に格納されます。 クライアント ソース IP の保持が有効になっているイングレス コントローラーを使用する場合、TLS パススルーは機能しません。

```console
# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Set variable for ACR location to use for pulling images
ACR_URL=<REGISTRY_URL>

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic --create-namespace \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.image.registry=$ACR_URL \
    --set controller.image.image=$CONTROLLER_IMAGE \
    --set controller.image.tag=$CONTROLLER_TAG \
    --set controller.image.digest="" \
    --set controller.admissionWebhooks.patch.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.image.registry=$ACR_URL \
    --set controller.admissionWebhooks.patch.image.image=$PATCH_IMAGE \
    --set controller.admissionWebhooks.patch.image.tag=$PATCH_TAG \
    --set controller.admissionWebhooks.patch.image.digest="" \
    --set defaultBackend.nodeSelector."kubernetes\.io/os"=linux \
    --set defaultBackend.image.registry=$ACR_URL \
    --set defaultBackend.image.image=$DEFAULTBACKEND_IMAGE \
    --set defaultBackend.image.tag=$DEFAULTBACKEND_TAG \
    --set defaultBackend.image.digest=""
```

インストールの間に、Azure パブリック IP アドレスがイングレス コントローラーに対して作成されます。 このパブリック IP アドレスは、イングレス コントローラーが存続している間は静的です。 イングレス コントローラーを削除すると、パブリック IP アドレスの割り当てが失われます。 続いてさらに別のイングレス コントローラーを作成すると、新しいパブリック IP アドレスが割り当てられます。 パブリック IP アドレスを使用し続けることを望む場合は、代わりに[静的パブリック IP アドレス][aks-ingress-static-tls]を使用してイングレス コントローラーを作成できます。

パブリック IP アドレスを取得するには、`kubectl get service` コマンドを使います。 IP アドレスがサービスに割り当てられるまでに、少し時間がかかる場合があります。

```console
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

まだイングレス ルールは作成されていません。 パブリック IP アドレスを参照すると、NGINX イングレス コントローラーの既定の 404 ページが表示されます。

## <a name="add-an-a-record-to-your-dns-zone"></a>DNS ゾーンに A レコードを追加する

[az network dns record-set a add-record][az-network-dns-record-set-a-add-record] を使用して、*A* レコードを NGINX サービスの外部 IP アドレスが使用された DNS ゾーンに追加します。

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name "*" \
    --ipv4-address MY_EXTERNAL_IP
```

### <a name="configure-an-fqdn-for-the-ingress-controller"></a>イングレス コントローラーの FQDN を構成する
必要に応じて、イングレス コントローラーの IP アドレスに、カスタム ドメインではなく FQDN を構成することもできます。  FQDN の形式は、`<CUSTOM LABEL>.<AZURE REGION NAME>.cloudapp.azure.com` のようになります。

この構成には、次に示す 2 つの方法があります。

#### <a name="method-1-set-the-dns-label-using-the-azure-cli"></a>方法 1: Azure CLI を使用して DNS ラベルを設定する
このサンプルは、Bash シェル用である点に注意してください。

```bash
# Public IP address of your ingress controller
IP="MY_EXTERNAL_IP"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME

# Display the FQDN
az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
 ```

#### <a name="method-2-set-the-dns-label-using-helm-chart-settings"></a>方法 2: Helm グラフの設定を使用して DNS ラベルを設定する
`--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"` パラメーターを使用することで、注釈の設定を、Helm グラフの構成に渡すことができます。  これは、イングレス コントローラーを最初にデプロイするときに設定することも、後で構成することもできます。
次の例は、コントローラーをデプロイした後にこの設定を更新する方法を示しています。

```
DNS_LABEL="demo-aks-ingress"
NAMESPACE="nginx-basic"

helm upgrade ingress-nginx ingress-nginx/ingress-nginx \
  --namespace $NAMESPACE \
  --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"=$DNS_LABEL

```

## <a name="install-cert-manager"></a>cert-manager をインストールする

NGINX イングレス コントローラーは、TLS の終端をサポートしています。 HTTPS の証明書を取得および構成するには、いくつかの方法があります。 この記事では、[Lets Encrypt][lets-encrypt] 証明書を自動的に作成および管理する機能を提供する [cert-manager][cert-manager] の使用方法を説明します。

cert-manager コントローラーをインストールするには、次のようにします。

```console
# Label the ingress-basic namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install cert-manager jetstack/cert-manager \
  --namespace ingress-basic \
  --version $CERT_MANAGER_TAG \
  --set installCRDs=true \
  --set nodeSelector."kubernetes\.io/os"=linux \
  --set image.repository=$ACR_URL/$CERT_MANAGER_IMAGE_CONTROLLER \
  --set image.tag=$CERT_MANAGER_TAG \
  --set webhook.image.repository=$ACR_URL/$CERT_MANAGER_IMAGE_WEBHOOK \
  --set webhook.image.tag=$CERT_MANAGER_TAG \
  --set cainjector.image.repository=$ACR_URL/$CERT_MANAGER_IMAGE_CAINJECTOR \
  --set cainjector.image.tag=$CERT_MANAGER_TAG
```

cert-manager の構成の詳細については、[cert-manager プロジェクト][cert-manager]についてのページを参照してください。

## <a name="create-a-ca-cluster-issuer"></a>CA クラスター発行者を作成する

証明書を発行する前に、cert-manager には [Issuer][cert-manager-issuer] リソースまたは [ClusterIssuer][cert-manager-cluster-issuer] リソースが必要です。 これらの Kubernetes リソースは機能面では同一ですが、`Issuer` は単一の名前空間で機能し、`ClusterIssuer` はすべての名前空間にわたって機能します。 詳細については、[cert-manager issuer][cert-manager-issuer] についてのページを参照してください。

次のマニフェスト例を使用して、`cluster-issuer.yaml` などのクラスター発行者を作成します。 メール アドレスを実際の組織の有効なアドレスで置き換えてください。

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

発行者を作成するには、`kubectl apply` コマンドを使用します。

```console
kubectl apply -f cluster-issuer.yaml
```

## <a name="run-demo-applications"></a>デモ アプリケーションを実行する

イングレス コントローラーと証明書管理ソリューションを構成しました。 ここでは、AKS クラスターで 2 つのデモ アプリケーションを実行します。 この例では、Helm を使って、単純な *Hello world* アプリケーションのインスタンスを 2 つデプロイします。

動作中のイングレス コントローラーを確認するには、AKS クラスターで 2 つのデモ アプリケーションを実行します。 この例では、`kubectl apply` を使って、シンプルな *Hello world* アプリケーションのインスタンスを 2 つデプロイします。

*aks-helloworld-one.yaml* ファイルを作成し、次の例のように YAML にコピーします。

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-one
  template:
    metadata:
      labels:
        app: aks-helloworld-one
    spec:
      containers:
      - name: aks-helloworld-one
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-one
```

*aks-helloworld-two.yaml* ファイルを作成し、次の例のように YAML にコピーします。

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-two
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-two
  template:
    metadata:
      labels:
        app: aks-helloworld-two
    spec:
      containers:
      - name: aks-helloworld-two
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld-two
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-two
```

`kubectl apply` を使用して、次の 2 つのデモ アプリケーションを実行します。

```console
kubectl apply -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl apply -f aks-helloworld-two.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>イングレス ルートを作成する

両方のアプリケーションは、Kubernetes クラスターで実行するようになります。 ただし、構成に使用されているサービスの種類は `ClusterIP` であるため、それらのアプリケーションにインターネットからアクセスすることはできません。 公開するには、Kubernetes イングレス リソースを作成します。 イングレス リソースでは、2 つのアプリケーションのいずれかにトラフィックをルーティングするルールを構成します。

次の例では、アドレス *hello-world-ingress.MY_CUSTOM_DOMAIN* へのトラフィックが *aks-helloworld-one* サービスにルーティングされます。 アドレス *hello-world-ingress.MY_CUSTOM_DOMAIN/hello-world-two* へのトラフィックは、*aks-helloworld-two* サービスにルーティングされます。 *hello-world-ingress.MY_CUSTOM_DOMAIN/static* へのトラフィックは、静的アセット用の *aks-helloworld-one* というサービスにルーティングされます。

> [!NOTE]
> カスタム ドメインではなく、イングレス コントローラーの IP アドレスの FQDN を構成した場合は、*hello-world-ingress.MY_CUSTOM_DOMAIN* ではなく、FQDN を使用します。 たとえば、FQDN が *demo-aks-ingress.eastus.cloudapp.azure.com* の場合は、`hello-world-ingress.yaml` の *hello-world-ingress.MY_CUSTOM_DOMAIN* を *demo-aks-ingress.eastus.cloudapp.azure.com* に置き換えます。

次の YAML の例を使用して、`hello-world-ingress.yaml` という名前のファイルを作成します。 *hosts* と *host* を前の手順で作成した DNS 名に更新します。

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - path: /hello-world-one(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld-one
            port:
              number: 80
      - path: /hello-world-two(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld-two
            port:
              number: 80
      - path: /(.*)
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld-one
            port:
              number: 80
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - path:
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld-one
            port: 
              number: 80
        path: /static(/|$)(.*)
```

`kubectl apply` コマンドを使用してイングレス リソースを作成します。

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>証明書オブジェクトが作成されたことを確認する

次に、証明書リソースを作成する必要があります。 証明書リソースでは、必要な X.509 証明書を定義します。 詳細については、[cert-manager の証明書][cert-manager-certificates]についてのページを参照してください。 証明書マネージャーによって証明書オブジェクトが ingress-shim を使用して自動的に作成されており、その証明書オブジェクトは v0.2.2 以降の証明書マネージャーで自動的にデプロイされます。 詳しくは、[ingress-shim のドキュメント][ingress-shim]をご覧ください。

証明書が正常に作成されたことを確認するには、`kubectl get certificate --namespace ingress-basic` コマンドを使用して、*READY* が *True* になっていることを確認します。これには数分かかることがあります。

```console
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>イングレスの構成をテストする

Web ブラウザーを開いて、お使いの Kubernetes イングレス コントローラーの *hello-world-ingress.MY_CUSTOM_DOMAIN* にアクセスします。 HTTPS を使用するようにリダイレクトされ、証明書が信頼されると、Web ブラウザーにデモ アプリケーションが 表示されます。 */hello-world-two* パスを追加すると、カスタム タイトルの付いた 2 番目のデモ アプリケーションが表示されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事では、Helm を使用して、イングレス コンポーネント、証明書、およびサンプル アプリをインストールしました。 Helm グラフをデプロイすると、多数の Kubernetes リソースが作成されます。 これらのリソースには、ポッド、デプロイ、およびサービスが含まれます。 これらのリソースをクリーンアップするには、サンプルの名前空間全体を削除するか、またはリソースを個々に削除します。

### <a name="delete-the-sample-namespace-and-all-resources"></a>サンプルの名前空間とすべてのリソースを削除する

サンプルの名前空間全体を削除するには、`kubectl delete` コマンドを使用して、名前空間の名前を指定します。 名前空間内のすべてのリソースが削除されます。

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>リソースを個々に削除する

作成したリソースを個々に削除するという、きめ細かな方法もあります。 最初に、クラスター発行者リソースを削除します。

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

`helm list` コマンドを使用して、Helm リリースを一覧表示します。 次の出力例に示すように、*nginx* と *cert-manager* という名前のグラフを探します。

```console
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

`helm uninstall` コマンドでリリースをアンインストールします。 次の例では、NGINX イングレスと cert-manager のデプロイをアンインストールします。

```console
$ helm uninstall cert-manager nginx --namespace ingress-basic

release "cert-manager" uninstalled
release "nginx" uninstalled
```

次に、2 つのサンプル アプリケーションを削除します。

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
```

サンプル アプリにトラフィックを送信していたイングレス ルートを削除します。

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

最後に、名前空間自体を削除します。 `kubectl delete` コマンドを使用して、名前空間の名前を指定します。

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>次のステップ

この記事には、AKS への外部コンポーネントが含まれています。 これらのコンポーネントの詳細については、次のプロジェクト ページを参照してください。

- [Helm CLI][helm-cli]
- [NGINX イングレス コントローラー][nginx-ingress]
- [cert-manager][cert-manager]

次のこともできます。

- [外部のネットワーク接続を使用して基本的なイングレス コントローラーを作成する][aks-ingress-basic]
- [HTTP アプリケーションのルーティング アドオンを有効にする][aks-http-app-routing]
- [内部のプライベート ネットワークと IP アドレスを使用するイングレス コントローラーを作成する][aks-ingress-internal]
- [ご自身の TLS 証明書を使用するイングレス コントローラーを作成する][aks-ingress-own-tls]
- [Let's Encrypt を使用して静的パブリック IP アドレス付きの TLS 証明書を自動的に生成するイングレス コントローラーを作成する][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/#az_network_dns_record_set_a_add_record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.io/docs/concepts/certificate/
[ingress-shim]: https://cert-manager.io/docs/usage/ingress/
[cert-manager-cluster-issuer]: https://cert-manager.io/docs/concepts/issuer/
[cert-manager-issuer]: https://cert-manager.io/docs/concepts/issuer/
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-nginx-helm-chart]: https://github.com/kubernetes/ingress-nginx/tree/main/charts/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-supported versions]: supported-kubernetes-versions.md
[aks-integrated-acr]: cluster-container-registry-integration.md?tabs=azure-cli#create-a-new-aks-cluster-with-acr-integration
[acr-helm]: ../container-registry/container-registry-helm-repos.md
