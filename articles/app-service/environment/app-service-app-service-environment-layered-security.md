---
title: 多層セキュリティ v1
description: App Service 環境で階層化された、セキュリティ アーキテクチャを実装する方法を学びます。 このドキュメントは、レガシ v1 ASE を使用するお客様にのみ提供されます。
author: madsd
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.topic: article
ms.date: 08/30/2016
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: d2245cc7557b7f5d6c6c392957fe0ef817975c53
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130001677"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>App Service 環境によるレイヤード セキュリティ アーキテクチャの実装
App Service Environment は、Virtual Network にデプロイされる分離されたランタイム環境です。開発者は、セキュリティ アーキテクチャを階層化し、物理的なアプリケーションの層ごとにネットワーク アクセスのレベルに違いを設けることができます。

一般に、API バックエンドは通常のインターネット アクセスから隠し、アップストリームの Web アプリにのみ API の呼び出しを許可することが望ましいと考えられています。  App Service 環境を含んだサブネットに対して[ネットワーク セキュリティ グループ (NSG)][NetworkSecurityGroups] を使用することで、API アプリケーションへのパブリック アクセスを制限することができます。

以下の図は、WebAPI ベースのアプリを App Service Environment にデプロイしたアーキテクチャの例です。  3 つの Web アプリのインスタンスが、独立した 3 つの App Service Environment に別々にデプロイされ、バックエンドにある同じ WebAPI アプリを呼び出します。

![Conceptual Architecture][ConceptualArchitecture] 

"apiase" を含んだサブネットに対するネットワーク セキュリティ グループは、アップストリームの Web アプリから入ってくる呼び出しと、自分自身からの呼び出しを許可します。これを示したのが緑色の正符号です。  一方、インターネットから入ってくる一般的なトラフィックについてはアクセスを明示的に拒否しています。 

以下では、"apiase" を含んだサブネットに対してネットワーク セキュリティ グループを構成する手順について説明します。

## <a name="determining-the-network-behavior"></a>ネットワークの動作の決定
必要なネットワーク セキュリティ ルールを把握するためには、API アプリを含んだ App Service Environment へのアクセスをどのネットワーク クライアントに許可し、どのクライアントをブロックするかを調べる必要があります。

[ネットワーク セキュリティ グループ (NSG)][NetworkSecurityGroups] はサブネットに適用され、そのサブネットの中に App Service 環境がデプロイされます。そのため NSG 内のルールは、App Service 環境上で動作する **すべての** アプリに適用されます。  この記事のサンプル アーキテクチャを使用して、"apiase" を含んだサブネットに対してネットワーク セキュリティ グループを適用すると、"apiase" App Service Environment 上で動作するすべてのアプリが同じセキュリティ ルール一式で保護されます。 

* **アップストリームの呼び出し元の送信 IP アドレスを判断する:** アップストリームの呼び出し元の IP アドレスとは何ですか。  これらのアドレスは NSG で明示的にアクセスを許可する必要があります。  App Service Environment 間の呼び出しは "インターネット" を介した呼び出しと見なされるので、3 つのアップストリーム App Service Environment にそれぞれ割り当てられた送信 IP アドレスを、"apiase" サブネットの NSG でアクセスを許可する必要があります。   App Service 環境で実行されるアプリの送信 IP アドレスを調べる方法については、[ネットワーク アーキテクチャ][NetworkArchitecture]の概要の記事をご覧ください。
* **バックエンド API アプリは自己呼び出しを行うか。**  バックエンド アプリケーションが自己呼び出しを行うかどうかは、見逃しやすいポイントです。  App Service Environment のバックエンド API アプリケーションに自己呼び出しが伴う場合、それも "インターネット" を介した呼び出しと見なされます。  サンプル アーキテクチャのケースでは、"apiase" App Service Environment の送信 IP アドレスについてもアクセスを許可する必要があります。

## <a name="setting-up-the-network-security-group"></a>ネットワーク セキュリティ グループの設定
一連の送信 IP アドレスが確認できたら、今度はネットワーク セキュリティ グループを構築します。  ネットワーク セキュリティ グループは、Resource Manager ベースの仮想ネットワークとクラシック仮想ネットワークのどちらにも作成できます。  次の例では、PowerShell を使用してクラシック仮想ネットワーク上に NSG を作成して構成する方法を示します。

このサンプル アーキテクチャの環境は、米国中南部に置かれているため、そのリージョンに空の NSG を作成します。

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" 
-Label "Only allow web frontend and loopback traffic"
```

最初に、Azure の管理インフラストラクチャに使用する明示的な許可ルールを追加します (App Service 環境の[受信トラフィック][InboundTraffic]の記事を参照)。

```azurepowershell-interactive
#Open ports for access by Azure management infrastructure
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" 
-Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
```

次に、1 つ目のアップストリーム App Service Environment ("fe1ase") からの HTTP 呼び出しと HTTPS 呼び出しを許可する 2 つのルールを追加します。

```azurepowershell-interactive
#Grant access to requests from the first upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" 
-Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" 
-Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

2 つ目と 3 つ目のアップストリーム App Service Environment ("fe2ase" と "fe3ase") についても同じ作業を行います。

```azurepowershell-interactive
#Grant access to requests from the second upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" 
-Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" 
-Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

#Grant access to requests from the third upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" 
-Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" 
-Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

最後に、バックエンド API の App Service Environment が自己呼び出しを行えるよう、その送信 IP アドレスにアクセス権を付与します。

```azurepowershell-interactive
#Allow apps on the apiase environment to call back into itself
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" 
-Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" 
-Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

すべての NSG には既定のルールのセットがあり、インターネットから入ってくるアクセスを既定でブロックするため、他のネットワーク セキュリティ ルールは必要ありません。

以下に示したのは、このネットワーク セキュリティ グループに含まれる全ルールの一覧です。  最後のルール (ハイライト部分) に注目してください。明示的にアクセスが許可されている呼び出し元以外はすべて、このルールによって受信アクセスがブロックされます。

![NSG Configuration][NSGConfiguration] 

最後に、"apiase" App Service Environment を含んだサブネットにこの NSG を適用する必要があります。

```azurepowershell-interactive
#Apply the NSG to the backend API subnet
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet 
-VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'
```

このサブネットに NSG を適用すると、アップストリームにある 3 つの App Service Environment と、API バックエンドを含んだ 1 つの App Service Environment とにのみ、"apiase" 環境に対する呼び出しが許可されるようになります。

## <a name="additional-links-and-information"></a>その他のリンクおよび情報
[ネットワーク セキュリティ グループ](../../virtual-network/network-security-groups-overview.md)に関する情報。

[送信 IP アドレス][NetworkArchitecture]と App Service 環境について。

App Service 環境で使用される[ネットワーク ポート][InboundTraffic]。

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png