---
title: Azure Application Gateway インフラストラクチャの構成
description: この記事では、Azure Application Gateway インフラストラクチャを構成する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 06/14/2021
ms.author: surmb
ms.openlocfilehash: 841583de276e4657384854f8430bbb82d75517d3
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130045921"
---
# <a name="application-gateway-infrastructure-configuration"></a>Application Gateway インフラストラクチャの構成

アプリケーション ゲートウェイのインフラストラクチャには、仮想ネットワーク、サブネット、ネットワーク セキュリティ グループ、ユーザー定義ルートが含まれます。

## <a name="virtual-network-and-dedicated-subnet"></a>仮想ネットワークと専用サブネット

アプリケーション ゲートウェイは、お客様の仮想ネットワーク内の専用デプロイメントです。 仮想ネットワーク内では、ご使用のアプリケーション ゲートウェイのために専用サブネットが必要です。 1 つのサブネットに特定のアプリケーション ゲートウェイ デプロイの複数インスタンスを配置できます。 また、サブネット内に他のアプリケーション ゲートウェイをデプロイすることもできます。 ただし、アプリケーション ゲートウェイ サブネットに他のリソースをデプロイすることはできません。 同じサブネット上に Standard_v2 と Standard Azure Application Gateway を混在することはできません。

> [!NOTE]
> [仮想ネットワーク サービス エンドポイント ポリシー](../virtual-network/virtual-network-service-endpoint-policies-overview.md)は現在、Application Gateway のサブネットではサポートされません。

### <a name="size-of-the-subnet"></a>サブネットのサイズ

Application Gateway は、インスタンスごとに 1 つのプライベート IP アドレスを使用します。さらに、プライベート フロントエンド IP を構成している場合は、もう 1 つのプライベート IP アドレスを使用します。

また、Azure は内部使用のために各サブネットに 5 個の IP アドレス (最初の 4 個の IP アドレスと最後の IP アドレス) を予約しています。 たとえば、プライベート フロントエンド IP がない 15 個のアプリケーション ゲートウェイ インスタンスがあるとします。 このサブネットには少なくとも 20 個の IP アドレスが必要です。内部使用のために 5 個、アプリケーション ゲートウェイ インスタンスのために 15 個です。

27 個のアプリケーション ゲートウェイ インスタンスと、プライベート フロントエンド IP 用の IP アドレスが 1 個あるサブネットがあるとします。 この場合、33 個の IP アドレスが必要です。アプリケーション ゲートウェイ インスタンスのために 27 個、プライベート フロント エンドのために 1 個、内部使用のために 5 個です。

Application Gateway (Standard または WAF) SKU では、最大 32 のインスタンス (32 のインスタンス IP アドレス + 1 つのプライベート フロントエンド IP + 5 つの予約済み Azure) をサポートできます。そのため、最小サブネット サイズは /26 をお勧めします。

Application Gateway (Standard_v2 または WAF_v2) SKU では、最大 125 のインスタンス (125 のインスタンス IP アドレス + 1 つのプライベート フロントエンド IP + 5 つの予約済み Azure) をサポートできます。 最小サブネット サイズは /24 をお勧めします。

> [!IMPORTANT]
> Application Gateway v2 SKU のデプロイでは /24 サブネットは必須ではありませんが、強くお勧めします。 これは、Application Gateway v2 で拡張とメンテナンスのアップグレードの自動スケール用に十分な領域を確保するためです。 予想される最大トラフィックの処理に必要なインスタンス数に対応するための、十分なアドレス空間が Application Gateway v2 サブネットにあることを確認する必要があります。 最大インスタンス数を指定する場合、そのサブネットには少なくともその数のアドレスに対応する容量が必要です。 インスタンス数に関するキャパシティ プランニングについては、[インスタンス数の詳細](understanding-pricing.md#instance-count)に関する記事を参照してください。

> [!TIP]
> 同じ仮想ネットワーク内にある既存の Application Gateway のサブネットを変更することができます。 これを行うには、Azure PowerShell または Azure CLI を使用します。 詳細については、「[Application Gateway に関してよく寄せられる質問](application-gateway-faq.yml#can-i-change-the-virtual-network-or-subnet-for-an-existing-application-gateway)」を参照してください

## <a name="network-security-groups"></a>ネットワーク セキュリティ グループ

ネットワーク セキュリティ グループ (NSG) は、Application Gateway でサポートされています。 ただし、いくつかの制限が適用されます。

- Application Gateway v1 SKU の TCP ポート 65503 ～ 65534 と、v2 SKU の TCP ポート 65200 ～ 65535 で、宛先サブネットが **[すべて]** 、ソースが **GatewayManager** サービス タグである着信インターネット トラフィックを許可する必要があります。 このポート範囲は、Azure インフラストラクチャの通信に必要です。 これらのポートは、Azure の証明書によって保護 (ロックダウン) されます。 それらのゲートウェイの顧客を含む外部エンティティは、これらのエンドポイントで通信できません。

- 送信インターネット接続はブロックできません。 NSG の既定のアウトバウンド規則ではインターネット接続が許可されています。 推奨事項は次のとおりです。

  - 既定のアウトバウンド規則は削除しないでください。
  - アウトバウンド接続を拒否する他のアウトバウンド規則は作成しないでください。

- **AzureLoadBalancer** タグからで宛先サブネットが **[すべて]** のトラフィックを許可する必要があります。

### <a name="allow-access-to-a-few-source-ips"></a>いくつかのソース IP へのアクセスを許可する

このシナリオでは、Application Gateway サブネット上の NSG を使用します。 次の制約は、この優先順位でサブネットに適用します。

1. ソース IP または IP 範囲からの着信トラフィックで、宛先が Application Gateway のサブネット アドレス範囲全体であり、宛先ポートがご使用の着信アクセス ポート (たとえば、HTTP アクセス用のポート 80) であるものを許可します。
2. [バックエンド正常性状態通信](./application-gateway-diagnostics.md)のために、ソースが **GatewayManager** サービス タグ、宛先が **[すべて]** 、宛先ポートが Application Gateway v1 SKU の 65503 ～ 65534、および v2 SKU のポート 65200 ～ 65535 である着信要求を許可します。 このポート範囲は、Azure インフラストラクチャの通信に必要です。 これらのポートは、Azure の証明書によって保護 (ロックダウン) されます。 適切な証明書が配置されていない外部エンティティは、そのようなエンドポイントに対する変更を開始できません。
3. [ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)で受信 Azure Load Balancer プローブ (*AzureLoadBalancer* タグ) を許可します。
4. [ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)で受信仮想ネットワーク トラフィック (*VirtualNetwork* タグ) を許可します。
5. 「すべて拒否」の規則を使用して、その他すべての着信トラフィックをブロックします。
6. すべての宛先に対してインターネットへの送信トラフィックを許可します。

## <a name="supported-user-defined-routes"></a>サポートされているユーザー定義ルート 

> [!IMPORTANT]
> Application Gateway サブネット上で UDR を使用すると、[バックエンドの正常性ビュー](./application-gateway-diagnostics.md#back-end-health)に正常性状態が **不明** と表示される場合があります。 また、Application Gateway ログとメトリックの生成が失敗する場合があります。 バックエンドの正常性、ログ、およびメトリックを表示できるように、Application Gateway サブネット上で UDR を使用しないことをお勧めします。

- **v1**

   v1 SKU の場合、ユーザー定義ルート (UDR) は、エンド ツー エンドの要求/応答の通信を変えない限り、Application Gateway サブネットでサポートされます。 たとえば、パケットの検査のためにファイアウォール アプライアンスを指すように Application Gateway サブネットの UDR を設定できます。 ただし、検査後にパケットが目的の宛先に到達できることを確認する必要があります。 これに失敗すると、不適切な正常性プローブやトラフィック ルーティング動作が発生する場合があります。 これには仮想ネットワークの Azure ExpressRoute や VPN ゲートウェイによってプロパゲートされる学習済みのルートまたは既定の 0.0.0.0/0 ルートが含まれます。

- **v2**

   v2 SKU の場合、サポートされるシナリオとサポートされないシナリオがあります。

   **v2 でサポートされるシナリオ**
   > [!WARNING]
   > ルート テーブルの構成が正しくないと Application Gateway v2 で非対称ルーティングが発生する可能性があります。 すべての管理/コントロール プレーン トラフィックが、仮想アプライアンス経由ではなく、インターネットに直接送信されることを確認します。 ログ記録とメトリックも影響を受ける可能性があります。


  **シナリオ 1**: Application Gateway サブネットへの Border Gateway Protocol (BGP) ルート伝達を無効にする UDR

   既定のゲートウェイ ルート (0.0.0.0/0) は、Application Gateway 仮想ネットワークに関連付けられている ExpressRoute または VPN ゲートウェイ経由でアドバタイズされる場合があります。 これにより、管理プレーン トラフィックが中断され、インターネットへの直接パスが必要になります。 このようなシナリオでは、UDR を使用して、BGP ルート伝達を無効にすることができます。 

   BGP ルート伝達を無効にするには、次の手順に従います。

   1. Azure でルート テーブル リソースを作成します。
   2. **仮想ネットワーク ゲートウェイのルート伝達** パラメーターを無効にします。 
   3. ルート テーブルを適切なサブネットに関連付けます。 

   このシナリオで UDR を有効にすると、既存のセットアップが中断されないはずです。

  **シナリオ 2**: 0.0.0.0/0 をインターネットに送信する UDR

   0\.0.0.0/0 トラフィックをインターネットに直接送信する UDR を作成できます。 

  **シナリオ 3**:Azure Kubernetes Service と kubenet の UDR

  Azure Kubernetes Service (AKS) と Application Gateway Ingress Controller (AGIC) で kubenet を使用している場合は、アプリケーション ゲートウェイからポッドに送信されたトラフィックが正しいノードにルーティングされるように、ルート テーブルが必要になります。 これは、Azure CNI を使用する場合は必要ありません。 

  kubenet が機能するようにルート テーブルを使用するには、次の手順に従います。

  1. AKS によって作成されたリソース グループに移動します (リソース グループの名前は "MC_" で始まるはずです)
  2. そのリソース グループで AKS によって作成されたルート テーブルを探します。 ルート テーブルには次の情報が入ります。
     - アドレス プレフィックスは、AKS でアクセスするポッドの IP 範囲にする必要があります。 
     - 次ホップの種類は [仮想アプライアンス] にする必要があります。 
     - 次ホップ アドレスは、ポッドをホストしているノードの IP アドレスになります。
  3. このルートテーブルを Application Gateway サブネットに関連付けます。 
    
  **v2 でサポートされないシナリオ**

  **シナリオ 1**: 仮想アプライアンスの UDR

  v2 では、仮想アプライアンス、ハブ/スポーク仮想ネットワーク、またはオンプレミス (強制トンネリング) を介して 0.0.0.0/0 をリダイレクトする必要があるシナリオはサポートされません。

## <a name="next-steps"></a>次の手順

- [フロントエンド IP アドレスの構成について学習します](configuration-front-end-ip.md)。
