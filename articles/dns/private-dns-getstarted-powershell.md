---
title: クイックスタート - Azure PowerShell を使用して Azure プライベート DNS ゾーンを作成する
description: このクイックスタートでは、Azure PowerShell を使用して最初のプライベート DNS ゾーンとレコードを作成して管理する方法について説明します。
services: dns
author: rohinkoul
ms.author: rohink
ms.date: 10/20/2020
ms.topic: quickstart
ms.service: dns
ms.custom: devx-track-azurepowershell, mode-api
ms.openlocfilehash: 4f6ac82358a9cdf247dee4b0934e98f97f962d9b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131077669"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-azure-powershell"></a>クイックスタート: Azure PowerShell を使用して Azure プライベート DNS ゾーンを作成する

この記事では、Azure PowerShell を使用して最初のプライベート DNS ゾーンとレコードを作成する手順について説明します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。 Azure DNS でドメインのホストを開始するには、そのドメイン名用に DNS ゾーンを作成する必要があります。 ドメインの DNS レコードはすべて、この DNS ゾーン内に作成されます。 仮想ネットワークにプライベート DNS ゾーンを発行するには、そのゾーン内のレコードを解決することが認められた仮想ネットワークの一覧を指定します。  これらを "*リンクされている*" 仮想ネットワーク と呼びます。 また、自動登録を有効にすると、仮想マシンの作成または削除、あるいはその IP アドレスの変更を行うたびに、Azure DNS でそのゾーン レコードも更新されます。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

好みに応じて、[Azure CLI](private-dns-getstarted-cli.md) を使用してこのクイックスタートを実行することもできます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>リソース グループを作成する

最初に、DNS ゾーンが含まれるリソース グループを作成します。 

```azurepowershell
New-AzResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-private-dns-zone"></a>プライベート DNS ゾーンの作成

DNS ゾーンは、 `New-AzPrivateDnsZone` コマンドレットを使用して作成します。

次の例では、**myAzureVNet** という名前の仮想ネットワークを作成します。 次に、**private.contoso.com** という名前の DNS ゾーンを **MyAzureResourceGroup** リソース グループに作成し、その DNS ゾーンを **MyAzureVnet** 仮想ネットワークにリンクし、自動登録を有効にします。

```azurepowershell
Install-Module -Name Az.PrivateDns -force

$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

$zone = New-AzPrivateDnsZone -Name private.contoso.com -ResourceGroupName MyAzureResourceGroup

$link = New-AzPrivateDnsVirtualNetworkLink -ZoneName private.contoso.com `
  -ResourceGroupName MyAzureResourceGroup -Name "mylink" `
  -VirtualNetworkId $vnet.id -EnableRegistration
```

名前の解決のためだけにゾーンを作成する (ホスト名の自動登録を行わない) 場合は、`-EnableRegistration` パラメーターを省略できます。

### <a name="list-dns-private-zones"></a>DNS プライベート ゾーンの一覧表示

`Get-AzPrivateDnsZone`からゾーン名を省略することで、リソース グループ内のすべてのゾーンを列挙できます。 この操作により、ゾーン オブジェクトの配列が返されます。

```azurepowershell
$zones = Get-AzPrivateDnsZone -ResourceGroupName MyAzureResourceGroup
$zones
```

`Get-AzPrivateDnsZone` でゾーン名とリソース グループ名の両方を省略すると、Azure サブスクリプションにすべてのゾーンを列挙できます。

```azurepowershell
$zones = Get-AzPrivateDnsZone
$zones
```

## <a name="create-the-test-virtual-machines"></a>テスト用仮想マシンの作成

次に、プライベート DNS ゾーンをテストできるように 2 つの仮想マシンを作成します。

```azurepowershell
New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM01" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389

New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM02" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389
```

これが完了するまでに数分かかります。

## <a name="create-an-additional-dns-record"></a>追加の DNS レコードの作成

レコード セットは、`New-AzPrivateDnsRecordSet` コマンドレットを使用して作成します。 次の例では、リソース グループ **MyAzureResourceGroup** の DNS ゾーン **private.contoso.com** に、相対名が **db** のレコードを作成します。 レコード セットの完全修飾名は、**db.private.contoso.com** になります。 レコードの種類は "A"、IP アドレスは "10.2.0.4"、TTL は 3,600 秒です。

```azurepowershell
New-AzPrivateDnsRecordSet -Name db -RecordType A -ZoneName private.contoso.com `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>DNS レコードの表示

ゾーン内の DNS レコードを一覧表示するには、次のコマンドを実行します。

```azurepowershell
Get-AzPrivateDnsRecordSet -ZoneName private.contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="test-the-private-zone"></a>プライベート ゾーンのテスト

これで、**private.contoso.com** プライベート ゾーンでの名前解決をテストできます。

### <a name="configure-vms-to-allow-inbound-icmp"></a>受信 ICMP を許可するように VM を構成する

名前解決は ping コマンドを使用してテストできます。 そこで、両方の仮想マシン上のファイアウォールを、受信 ICMP パケットを許可するように構成します。

1. myVM01 に接続し、管理者特権で Windows PowerShell ウィンドウを開きます。
2. 次のコマンドを実行します。

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

MyVM02 についても同じ手順を繰り返します。

### <a name="ping-the-vms-by-name"></a>VM を名前で ping する

1. myVM02 の Windows PowerShell コマンド プロンプトから、自動的に登録されたホスト名を使用して myVM01 を ping します。

   ```
   ping myVM01.private.contoso.com
   ```

   次のような出力が表示されるはずです。

   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```

2. 次に、前に作成した **db** 名を ping します。

   ```
   ping db.private.contoso.com
   ```

   次のような出力が表示されるはずです。

   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milliseconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事で作成したリソースが不要になったときに削除するには、**MyAzureResourceGroup** リソース グループを削除します。

```azurepowershell
Remove-AzResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure DNS Private Zones のシナリオ](private-dns-scenarios.md)
