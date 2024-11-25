---
title: IPv6 デュアル スタック アプリケーションと Basic Load Balancer を Azure 仮想ネットワークでデプロイする - Resource Manager テンプレート
titlesuffix: Azure Virtual Network
description: この記事では、Azure Resource Manager VM テンプレートを使用して Azure 仮想ネットワークで IPv6 デュアル スタック アプリケーションをデプロイする方法を示します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 548b416ed0f21df83dafdb1c2d7015c625c36e4c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367793"
---
# <a name="deploy-an-ipv6-dual-stack-application-with-basic-load-balancer-in-azure---template"></a>IPv6 デュアル スタック アプリケーションと Basic Load Balancer を Azure でデプロイする - テンプレート

この記事では、適用する Azure Resource Manager VM テンプレートの一部と共にIPv6 構成タスクのリストを示します。 この記事で説明されているテンプレートを使用して、Basic Load Balancer でデュアル スタック (IPv4 と IPv6) アプリケーションをデプロイします。これには、IPv4 と IPv6 のサブネットを持つデュアル スタック仮想ネットワーク、デュアル (IPv4 と IPv6) のフロントエンド構成を持つ Basic Load Balancer、デュアル IP 構成、ネットワーク セキュリティ グループ、およびパブリック IP を持つ NIC の仮想マシンが含まれます。

Standard Load Balancer を使用してデュアル スタック (IPV4 + IPv6) アプリケーションをデプロイするには、「[IPv6 デュアル スタック アプリケーションと Standard Load Balancer を Azure でデプロイする - テンプレート (プレビュー)](ipv6-configure-standard-load-balancer-template-json.md)」を参照してください。

## <a name="required-configurations"></a>必要構成

テンプレート内に実行する場所を表示するテンプレート セクションがないか検索します。

### <a name="ipv6-addressspace-for-the-virtual-network"></a>仮想ネットワークの IPv6 addressSpace

追加するテンプレートのセクション:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>IPv6 仮想ネットワーク addressSpace 内の IPv6 サブネット

追加するテンプレートのセクション:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>NIC の IPv6 構成

追加するテンプレートのセクション:
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>IPv6 ネットワーク セキュリティ グループ (NSG) のルール

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "fd00:db8:deca:deed::/64",
              "destinationAddressPrefix": "fd00:db8:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>条件付き構成

ネットワーク仮想アプライアンスを使用している場合は、ルート テーブルの IPv6 ルートを追加します。 それ以外の場合、この構成は省略可能です。

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "fd00:db8:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "fd00:db8:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>オプションの構成

### <a name="ipv6-internet-access-for-the-virtual-network"></a>仮想ネットワークの IPv6 インターネットへのアクセス

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>IPv6 パブリック IP アドレス

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>ロード バランサーの IPv6 フロント エンド

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>ロード バランサーの IPv6 のバック エンド アドレス プール

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>を受信および送信ポートを関連付けるための IPv6 ロード バランサーのルール

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>VM テンプレートのサンプル JSON
Azure 仮想ネットワークで Azure Resource Manager テンプレートを使用し、Basic Load Balancer を使用して IPv6 デュアル スタック アプリケーションをデプロイするには、[こちら](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/)のサンプル テンプレートを表示してください。

## <a name="next-steps"></a>次のステップ

[パブリック IP アドレス](https://azure.microsoft.com/pricing/details/ip-addresses/)、[ネットワーク帯域幅](https://azure.microsoft.com/pricing/details/bandwidth/)、または [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/) の価格に関する詳細を確認してください。
