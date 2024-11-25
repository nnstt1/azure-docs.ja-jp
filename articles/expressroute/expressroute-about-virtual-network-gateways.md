---
title: ExpressRoute 用の仮想ネットワーク ゲートウェイについて - Azure | Microsoft Docs
description: ExpressRoute 用の仮想ネットワーク ゲートウェイについて説明します。 この記事には、ゲートウェイの SKU と種類に関する情報が含まれます。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/23/2021
ms.author: duau
ms.openlocfilehash: 719ad0681e9d66828fc0e030394a2c910017a3ad
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130259563"
---
# <a name="about-expressroute-virtual-network-gateways"></a>ExpressRoute の仮想ネットワーク ゲートウェイについて

お使いの Azure 仮想ネットワークとオンプレミス ネットワークを ExpressRoute 経由で接続するには、最初に仮想ネットワーク ゲートウェイを作成する必要があります。 仮想ネットワーク ゲートウェイには 2 つの目的があります。1 つはネットワーク間で IP ルートを交換すること、もう 1 つはネットワーク トラフィックをルーティングすることです。 この記事では、ゲートウェイの種類、ゲートウェイ SKU、および SKU ごとの予測パフォーマンスについて説明します。 また、パフォーマンスを向上させるために、お使いのオンプレミス ネットワークからのネットワーク トラフィックが仮想ネットワーク ゲートウェイをバイパスできるようにする機能、ExpressRoute [FastPath](#fastpath) についても説明します。

## <a name="gateway-types"></a>ゲートウェイの種類

仮想ネットワーク ゲートウェイを作成する場合、設定をいくつか指定する必要があります。 必須の設定の 1 つである '-GatewayType' は、ゲートウェイを ExpressRoute と VPN トラフィックのどちらに使用するかを指定します。 ゲートウェイには、次の 2 種類があります。

* **Vpn** - パブリック インターネットでトラフィックを暗号化して送信するには、ゲートウェイの種類 "Vpn" を使用します。 これは、VPN ゲートウェイとも呼ばれます。 サイト間接続、ポイント対サイト接続、VNet 間接続のすべてで、VPN ゲートウェイが使用されます。

* **ExpressRoute** - プライベート接続でネットワーク トラフィックを送信するには、ゲートウェイの種類 'ExpressRoute' を使用します。 これは ExpressRoute ゲートウェイとも呼ばれ、ExpressRoute の構成時に使用するゲートウェイの種類です。

各仮想ネットワークに配置できる仮想ネットワーク ゲートウェイは、ゲートウェイの種類ごとに 1 つに限られています。 たとえば、-GatewayType が Vpn の仮想ネットワーク ゲートウェイと -GatewayType が ExpressRoute の仮想ネットワーク ゲートウェイをそれぞれ 1 つ配置できます。

## <a name="gateway-skus"></a><a name="gwsku"></a>ゲートウェイの SKU
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

ゲートウェイをより強力なゲートウェイ SKU にアップグレードする場合、ほとんどの場合 "Resize-AzVirtualNetworkGateway" PowerShell コマンドレットを使用できます。 これは、Standard および HighPerformance SKU へのアップグレードの場合でも機能します。 ただし、可用性ゾーン (AZ) 以外のゲートウェイを UltraPerformance SKU にアップグレードするには、ゲートウェイを再作成する必要があります。 ゲートウェイの再作成によりダウンタイムが発生します。 AZ 対応 SKU をアップグレードするためにゲートウェイを削除して再作成する必要はありません。
### <a name="feature-support-by-gateway-sku"></a><a name="gatewayfeaturesupport"></a>ゲートウェイ SKU による機能のサポート
次の表では、各ゲートウェイの種類でサポートされる機能を示しています。

|**ゲートウェイ SKU**|**VPN Gateway と ExpressRoute の共存**|**FastPath**|**回線接続の最大数**|
| --- | --- | --- | --- |
|**Standard SKU/ERGw1Az**|はい|いいえ|4|
|**High Perf SKU/ERGw2Az**|はい|いいえ|8
|**Ultra Performance SKU/ErGw3Az**|はい|はい|16

### <a name="estimated-performances-by-gateway-sku"></a><a name="aggthroughput"></a>ゲートウェイ SKU の推定パフォーマンス
次の表は、ゲートウェイの種類と、予測されるパフォーマンスとスケールの数値を示したものです。 これらの数値は、以下のテスト条件から得られたもので、サポートの上限を表しています。 実際のパフォーマンスは、トラフィックによってテスト条件がどれだけ厳密に再現されるかによって異なる場合があります。

### <a name="testing-conditions"></a>テスト条件
##### <a name="standardergw1az"></a>**Standard/ERGw1Az** #####

- 回線帯域幅: 1 Gbps
- ゲートウェイによってアドバタイズされたルートの数: 500
- 学習されたルートの数: 4,000
##### <a name="high-performanceergw2az"></a>**High Performance/ERGw2Az** #####

- 回線帯域幅: 1 Gbps
- ゲートウェイによってアドバタイズされたルートの数: 500
- 学習されたルートの数: 9,500
##### <a name="ultra-performanceergw3az"></a>**Ultra Performance/ErGw3Az** #####

- 回線帯域幅: 1 Gbps
- ゲートウェイによってアドバタイズされたルートの数: 500
- 学習されたルートの数: 9,500

 この表は、リソース マネージャーとクラシック デプロイ モデルの両方に適用されます。
 
|**ゲートウェイ SKU**|**1 秒あたりの接続数**|**1 秒あたりのメガビット数**|**1 秒あたりのパケット数**|**Virtual Network でサポートされている VM の数**|
| --- | --- | --- | --- | --- |
|**Standard/ERGw1Az**|7,000|1,000|100,000|2,000|
|**High Performance/ERGw2Az**|14,000|2,000|250,000|4,500|
|**Ultra Performance/ErGw3Az**|16,000|10,000|1,000,000|11,000|

> [!IMPORTANT]
> アプリケーションのパフォーマンスは複数の要因によって異なります。これらの要因には、エンド ツー エンドの待機時間、アプリケーションが起動するトラフィック フローの数などがあります。 テーブルの数値は、アプリケーションが理想的な環境で理論上達成できる上限を表しています。

>[!NOTE]
> 同じ仮想ネットワークに接続できる同じピアリングの場所からの ExpressRoute 回線の最大数は、すべてのゲートウェイに対して 4 です。
>

## <a name="gateway-subnet"></a><a name="gwsub"></a>ゲートウェイ サブネット

ExpressRoute ゲートウェイを作成する前に、ゲートウェイ サブネットを作成する必要があります。 ゲートウェイ サブネットには、仮想ネットワーク ゲートウェイの VM とサービスが使用する IP アドレスが含まれます。 仮想ネットワーク ゲートウェイを作成すると、ゲートウェイ VM はゲートウェイ サブネットにデプロイされ、必要な ExpressRoute ゲートウェイ設定で構成されます。 ゲートウェイ サブネットには、追加の VM などをデプロイしないでください。 ゲートウェイ サブネットを正常に動作させるには、"GatewaySubnet" という名前を付ける必要があります。 ゲートウェイ サブネットに "GatewaySubnet" という名前を付けることで、これが仮想ネットワーク ゲートウェイの VM とサービスをデプロイするサブネットであることを Azure が認識できます。

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

ゲートウェイ サブネットを作成するときに、サブネットに含まれる IP アドレスの数を指定します。 ゲートウェイ サブネット内の IP アドレスは、ゲートウェイ VM とゲートウェイ サービスに割り当てられます。 一部の構成では、他の構成よりも多くの IP アドレスを割り当てる必要があります。 

ゲートウェイ サブネットのサイズを計画する際は、作成する構成に関するドキュメントを参照してください。 たとえば、ExpressRoute/VPN Gateway が共存する構成には、他のほとんどの構成より大規模なゲートウェイ サブネットが必要です。 また、ゲートウェイ サブネットには、将来の構成の追加に対応できる十分な数の IP アドレスが含まれるようにしてください。 /29 のような小さいゲートウェイ サブネットを作成できますが、使用できるアドレス空間がある場合は、/27 以上 (/27、/26 など) のゲートウェイ サブネットを作成することをお勧めします。 ゲートウェイに 16 本の ExpressRoute 回線を接続するつもりであれば、/26 以上のゲートウェイ サブネットを作成する **必要があります**。 デュアル スタックのゲートウェイ サブネットを作成する場合は、/64 以上の IPv6 範囲も使用することをお勧めします。 このゲートウェイ サブネットは、ほとんどの構成に対応します。

次の Resource Manager PowerShell の例では、GatewaySubnet という名前のゲートウェイ サブネットを示しています。 CIDR 表記で /27 を指定しています。これで既存のほとんどの構成で IP アドレスに十分対応できます。

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zone-redundant-gateway-skus"></a><a name="zrgw"></a>ゾーン冗長ゲートウェイ SKU

Azure Availability Zones に、ExpressRoute ゲートウェイをデプロイすることもできます。 これにより、ゲートウェイは異なる Availability Zones に物理的かつ論理的に分離され、オンプレミス ネットワークの Azure への接続がゾーン レベルの障害から保護されます。

![ゾーン冗長 ExpressRoute ゲートウェイ](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

ゾーン冗長ゲートウェイでは、ExpressRoute ゲートウェイに特定の新しいゲートウェイ SKU が使用されます。

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

新しいゲートウェイ SKU では、ニーズに最も適したその他のデプロイ オプションもサポートされます。 新しいゲートウェイ SKU を使用して仮想ネットワーク ゲートウェイを作成する場合、特定のゾーン内にゲートウェイをデプロイするオプションもあります。 これは、ゾーン ゲートウェイと呼ばれます。 ゾーン ゲートウェイをデプロイすると、すべてのゲートウェイ インスタンスが同じ可用性ゾーンにデプロイされます。

## <a name="fastpath"></a><a name="fastpath"></a>FastPath

ExpressRoute 仮想ネットワーク ゲートウェイの目的は、ネットワーク ルートを交換し、ネットワーク トラフィックをルーティングすることです。 FastPath の目的は、お使いのオンプレミス ネットワークと仮想ネットワークの間のデータ パスのパフォーマンスを向上させることです。 FastPath が有効になっていると、ゲートウェイはバイパスされ、ネットワーク トラフィックが仮想ネットワーク内の仮想マシンに直接送信されます。

制限や要件を含む FastPath の詳細については、[FastPath の概要](about-fastpath.md)に関するページを参照してください。

## <a name="rest-apis-and-powershell-cmdlets"></a><a name="resources"></a>REST API および PowerShell コマンドレット
仮想ネットワーク ゲートウェイの構成に対して REST API および PowerShell コマンドレットを使用する場合のテクニカル リソースおよび特定構文の要件については、次のページを参照してください。

| **クラシック** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/servicemanagement/azure.service/#azure) |[PowerShell](/powershell/module/az.network#networking) |
| [REST API](/previous-versions/azure/reference/jj154113(v=azure.100)) |[REST API](/rest/api/virtual-network/) |

## <a name="next-steps"></a>次のステップ

使用可能な接続構成の詳細については、「[ExpressRoute の概要](expressroute-introduction.md)」を参照してください。

ExpressRoute ゲートウェイの作成の詳細については、[ExpressRoute 用の仮想ネットワーク ゲートウェイの作成](expressroute-howto-add-gateway-resource-manager.md)に関するページを参照してください。

ゾーン冗長ゲートウェイの構成の詳細については、[ゾーン冗長仮想ネットワーク ゲートウェイの作成](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md)に関するページを参照してください。

FastPath の詳細については、[FastPath の概要](about-fastpath.md)に関するページを参照してください。
