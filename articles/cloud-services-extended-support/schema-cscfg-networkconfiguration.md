---
title: Azure Cloud Services (延長サポート) NetworkConfiguration スキーマ | Microsoft Docs
description: Cloud Services (延長サポート) のネットワークの構成スキーマに関連する情報
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: e6b55782c7bdb8404bb19e4b7d58faf62178f73d
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114220801"
---
# <a name="azure-cloud-services-extended-support-config-networkconfiguration-schema"></a>Azure Cloud Services (延長サポート) 構成 networkConfiguration スキーマ

サービス構成ファイルの `NetworkConfiguration` 要素は、仮想ネットワークと DNS の値を指定します。 これらの設定は、Cloud Services では省略可能です。

仮想ネットワークと関連付けられたスキーマの詳細については、次のリソースをご覧ください。

- [クラウド サービス (延長サポート) 構成スキーマ](schema-cscfg-file.md)。
- [クラウド サービス (延長サポート) 定義スキーマ](schema-csdef-file.md)。
- [仮想ネットワークを作成する](../virtual-network/manage-virtual-network.md)。

## <a name="networkconfiguration-element"></a>NetworkConfiguration 要素
次の例は、`NetworkConfiguration` 要素とその子要素を示します。

```xml
<ServiceConfiguration>
  <NetworkConfiguration>
    <AccessControls>
      <AccessControl name="aclName1">
        <Rule order="<rule-order>" action="<rule-action>" remoteSubnet="<subnet-address>" description="rule-description"/>
      </AccessControl>
    </AccessControls>
    <EndpointAcls>
      <EndpointAcl role="<role-name>" endpoint="<endpoint-name>" accessControl="<acl-name>"/>
    </EndpointAcls>
    <Dns>
      <DnsServers>
        <DnsServer name="<server-name>" IPAddress="<server-address>" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSite name="Group <RG-VNet> <VNet-name>"/>
    <AddressAssignments>
      <InstanceAddress roleName="<role-name>">
        <Subnets>
          <Subnet name="<subnet-name>"/>
        </Subnets>
      </InstanceAddress>
      <ReservedIPs>
        <ReservedIP name="<reserved-ip-name>"/>
      </ReservedIPs>
    </AddressAssignments>
  </NetworkConfiguration>
</ServiceConfiguration>
```

次の表は、`NetworkConfiguration` 要素の子要素の説明です。

| 要素       | 説明 |
| ------------- | ----------- |
| AccessControl | 省略可能。 クラウド サービス内のエンドポイントにアクセスするためのルールを指定します。 アクセス制御の名前は、`name` 属性の文字列で定義されます。 `AccessControl` 要素には、1 つ以上の `Rule` 要素が含まれています。 複数の `AccessControl` 要素を定義できます。|
| ルール | 省略可能。 IP アドレスの指定されたサブネット範囲に対して実行されるアクションを指定します。 ルールの順序は `order` 属性の文字列値で定義されます。 ルール番号が小さいほど、優先度は高くなります。 たとえば、ルールは、100、200、および 300 の順序番号で指定できます。 100 の順序番号のルールは、200 の順序であるルールよりも優先されます。<br /><br /> ルールのアクションは `action` 属性の文字列で定義されます。 指定できる値は次のとおりです。<br /><br /> -   `permit`– 指定されたサブネット範囲からのパケットのみがエンドポイントと通信できることを指定します。<br />-   `deny`– 指定されたサブネット範囲内のエンドポイントへのアクセスが拒否されることを指定します。<br /><br /> ルールによって影響を受ける IP アドレスのサブネット範囲は、`remoteSubnet` 属性の文字列で定義されます。 ルールの説明は `description` 属性の文字列で定義されます。|
| EndpointAcl | 省略可能。 アクセス制御ルールのエンドポイントへの割り当てを指定します。 エンドポイントを含むロールの名前は、`role` 属性の文字列で定義されます。 エンドポイントの名前は、`endpoint` 属性の文字列で定義されます。 エンドポイントに適用される `AccessControl` ルールのセットの名前は、`accessControl` 属性の文字列で定義されます。 複数の `EndpointAcl` 要素を定義することができます。|
| DnsServer | 省略可能。 DNS サーバーの設定を指定します。 仮想ネットワークを使用しない DNS サーバーの設定を指定できます。 DNS サーバーの名前は、`name` 属性の文字列で定義されます。 DNS サーバーの IP アドレスは、`IPAddress` 属性の文字列で定義されます。 IP アドレスは、有効な IPv4 アドレスである必要があります。|
| VirtualNetworkSite | 必須。 クラウド サービスをデプロイする仮想ネットワーク サイトの名前を指定します。 この設定では、仮想ネットワーク サイトは作成されません。 お使いの仮想ネットワークのネットワーク ファイルに既に定義されているサイトを参照します。 クラウド サービス (拡張サポート) がメンバーになることができる Virtual Network は 1 つだけです。 仮想ネットワークの名前は、`name` 属性の文字列で定義されます。|
| InstanceAddress | 必須。 仮想ネットワーク内の 1 つのサブネットまたはサブネットのセットへのロールの関連付けを指定します。 ロール名をインスタンス アドレスに関連付ける場合、このロールに関連付けたいサブネットを指定できます。 `InstanceAddress` はサブネット要素を含んでいます。 サブネットに関連付けられているロールの名前は `roleName` 属性の文字列で定義されます。クラウド サービスに定義されているロールごとにインスタンス アドレスを 1 つ指定する必要があります。|
| Subnet | 必須。 ネットワーク構成ファイル内のサブネット名に対応するサブネットを指定します。 サブネットの名前は、`name` 属性の文字列で定義されます。|
| ReservedIP | 省略可能。 デプロイに関連付けられる予約済み IP アドレスを指定します。 予約済み IP の割り当て方法は、テンプレートと powershell のデプロイ用に `Static` として指定する必要があります。 クラウド サービス内の各デプロイは、1 つの予約済み IP アドレスにのみ関連付けることができます。 予約済み IP アドレスの名前は、`name` 属性の文字列で定義されます。|

## <a name="see-also"></a>関連項目
[クラウド サービス (延長サポート) 構成スキーマ](schema-cscfg-file.md)。
