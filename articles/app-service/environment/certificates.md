---
title: 証明書のバインド
description: App Service Environment v2 の証明書に関連するさまざまなトピックについて説明します。 ASE の単一テナントアプリで証明書バインドがどのように機能するかについて説明します。
author: madsd
ms.topic: overview
ms.date: 11/15/2021
ms.author: madsd
ms.openlocfilehash: 0ecc9f29ae469cea01c29a23c5491320c37e7cc5
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520999"
---
# <a name="certificates-and-the-app-service-environment-v2"></a>証明書と App Service Environment v2
> [!NOTE]
> これは、Isolated App Service プランで使用される App Service Environment v2 に関する記事です
> 
App Service Environment (ASE) は、ご使用の Azure Virtual Network (VNet) 内で実行する Azure App Service のデプロイです。 これは、インターネットでアクセス可能なアプリケーション エンドポイントまたはご使用の VNet 内にあるアプリケーション エンドポイントを使用してデプロイできます。 インターネットでアクセス可能なエンドポイントを使用して ASE をデプロイすると、そのデプロイは外部 ASE と呼ばれます。 VNet 内にあるエンドポイントを使用して ASE をデプロイすると、そのデプロイは ILB ASE と呼ばれます。 ILB ASE の詳細については、[ILB ASE の作成と使用](./create-ilb-ase.md)に関するページを参照してください。

ASE は、シングル テナント システムです。 シングル テナントのため、マルチテナントの App Service では使用できない ASE でのみ使用できるいくつかの機能があります。 

## <a name="ilb-ase-certificates"></a>ILB ASE 証明書 

外部 ASE を使用している場合、アプリには &lt;appname&gt;.&lt;asename&gt;.p.azurewebsites.net でアクセスします。 既定では、ILB ASE を含め、すべての ASE が、その形式に従った証明書で作成されます。 ILB ASE がある場合、アプリには ILB ASE の作成時に指定したドメイン名に基づいて到達します。 アプリが TLS をサポートするためには、証明書をアップロードする必要があります。 内部証明機関を利用する、外部の発行者から証明書を購入する、自己署名証明書を使用する、のいずれかの手段で有効な TLS/SSL 証明書を取得します。 

ILB ASE で証明書を構成するには、次の 2 つのオプションがあります。  ILB ASE に既定のワイルドカード証明書を設定するか、ASE 内の個別の Web アプリに証明書を設定します。  どちらを選択した場合でも、次の証明書属性を適切に構成する必要があります。

- **Subject:** この属性は、ワイルドカード ILB ASE 証明書用に *.[your-root-domain-here] に設定する必要があります。 アプリ用に証明書を作成する場合は、これは [appname].[your-root-domain-here] にする必要があります。
- **Subject Alternative Name:** この属性には、ワイルドカード ILB ASE 証明書用に *.[your-root-domain-here] と *.scm.[your-root-domain-here] の両方を含める必要があります。 アプリ用に証明書を作成する場合は、これを [appname].[your-root-domain-here] と [appname].scm.[your-root-domain-here] にする必要があります。

3 番目のバリアントとして、ワイルドカード参照を使用する代わりに、証明書の SAN 内の個別のアプリ名をすべて含む ILB ASE 証明書を作成することができます。 この方法の問題は、ASE に配置するアプリの名前を事前にすべて把握しておく必要があることです。そうしないと、ILB ASE 証明書を常に更新し続ける必要があります。

### <a name="upload-certificate-to-ilb-ase"></a>ILB ASE に証明書をアップロードする 

ポータルで ILB ASE が作成されたら、その ILB ASE 用に証明書を設定する必要があります。 証明書が設定されるまで、ASE には、証明書が設定されていないことを示すバナーが表示されます。  

アップロードする証明書は .pfx ファイルである必要があります。 証明書がアップロードされた後、証明書が使用されるまで約20分間の遅延が発生します。 

ASE の作成と証明書のアップロードは、ポータルで 1 つのアクションとして実行することはできません。また、1 つのテンプレートとしても実行できません。 別々のアクションとして、[テンプレートからの ASE の作成](./create-from-template.md)に関するドキュメントに記載されているように、テンプレートを使用して証明書をアップロードすることはできます。  

テストのために自己署名証明書をすばやく作成するには、次の少量の PowerShell を使用できます。

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password
```

自己署名証明書を作成するときは、サブジェクト名を CN={ASE_NAME_HERE}_InternalLoadBalancingASE の形式にする必要があります。

## <a name="application-certificates"></a>アプリケーション証明書 

ASE でホストされているアプリは、マルチテナント App Service で利用できるアプリ中心の証明書機能を使用できます。 これには次のような機能が含まれます。  

- SNI 証明書 
- IP ベースの SSL。外部 ASE でのみサポートされます。  ILB ASE は、IP ベースの SSL をサポートしていません。
- KeyVault がホストされている証明書 

これらの証明書をアップロードおよび管理する手順については、「[Azure App Service で TLS/SSL 証明書を追加する](../configure-ssl-certificate.md)」を参照してください。  Web アプリに割り当てたカスタム ドメイン名と一致するように証明書を構成するだけの場合は、これらの手順で十分です。 既定のドメイン名を使用して ILB ASE Web アプリ用に証明書をアップロードする場合は、前述したように、証明書の SAN 内の scm サイトを指定します。 

## <a name="tls-settings"></a>TLS の設定 

TLS の設定は、アプリ レベルで構成できます。  

## <a name="private-client-certificate"></a>クライアントのプライベート証明書 

一般的なユース ケースでは、クライアント/サーバー モデルでのクライアントとしてアプリを構成します。 プライベート CA 証明書を使用してサーバーをセキュリティで保護する場合は、ご使用のアプリにクライアント証明書をアップロードする必要があります。  次の手順では、アプリが実行されているワーカーの truststore に証明書を読み込みます。 1 つのアプリに証明書を読み込むと、証明書をもう一度アップロードしなくても、同じ App Service プラン内にある他のアプリにもその証明書を使用できます。

ASE 内のアプリに証明書をアップロードするには、次の手順を実行します。

1. 証明書の *.cer* ファイルを生成します。 
2. Azure portal で証明書を必要とするアプリに移動します。
3. アプリで SSL 設定に移動します。 [証明書のアップロード] をクリックします。 [パブリック] を選択します。 [ローカル コンピューター] を選択します。 名前を指定します。 *.cer* ファイルを参照して選択します。 [アップロード] を選択します。 
4. サムプリントをコピーします。
5. [アプリケーション設定] に移動します。 サムプリントを値として使用して、アプリ設定 WEBSITE_LOAD_ROOT_CERTIFICATES を作成します。 複数の証明書がある場合は、次のように空白なしのコンマで区切ることで、同じ設定に配置することができます。 

    84EC242A4EC7957817B8E48913E50953552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

証明書は、その設定を構成したアプリと同じ App Service プラン内のすべてのアプリで使用できます。 別の App Service プラン内のアプリに対してもその証明書を使用できるようにする必要がある場合は、その App Service プランでアプリ設定操作を繰り返す必要があります。 証明書が設定されていることを確認するには、Kudu コンソールに移動し、PowerShell デバッグ コンソールで次のコマンドを発行します。

```azurepowershell-interactive
dir cert:\localmachine\root
```

テストを実行するには、自己署名証明書を作成し、次の PowerShell を使用して *.cer* ファイルを生成します。 

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.cer"
export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT
```
