---
title: サブドメインを委任する - Azure PowerShell - Azure DNS
description: このラーニング パスでは、Azure PowerShell を使用した Azure DNS サブドメインの委任について説明します。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 05/03/2021
ms.author: rohink
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0030eccff0885c2cf2e1e0eef386b8907aa0df29
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110689356"
---
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>Azure PowerShell を使用して Azure DNS サブドメインを委任する

Azure PowerShell を使用して DNS サブドメインを委任することができます。 たとえば、contoso.com ドメインを所有している場合は、*engineering* というサブドメインを contoso.com ゾーンとは別に管理できる別のゾーンに委任できます。

必要に応じて、[Azure portal](delegate-subdomain.md) を使用して、サブドメインを委任することもできます。

> [!NOTE]
> この記事では、contoso.com を例として使用します。 contoso.com を独自のドメイン名に置き換えてください。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>前提条件

Azure DNS サブドメインを委任するには、まずパブリック ドメインを Azure DNS に委任する必要があります。 ネーム サーバーを委任のために構成する方法については、[Azure DNS へのドメインの委任](./dns-delegate-domain-azure-dns.md)に関するページを参照してください。 ドメインが Azure DNS ゾーンに委任された後は、サブドメインを委任できるようになります。

## <a name="create-a-zone-for-your-subdomain"></a>サブドメインのゾーンを作成する

まず **engineering** サブドメインのゾーンを作成します。

```azurepowershell-interactive
New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com
```

## <a name="note-the-name-servers"></a>ネーム サーバーをメモする

次に、engineering サブドメインの 4 つのネーム サーバーをメモします。

```azurepowershell-interactive
Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS
```

## <a name="create-a-test-record"></a>テスト レコードを作成する

engineering ゾーンに、テストに使用する **A** レコードを作成します。

```azurepowershell-interactive
New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)
```

## <a name="create-an-ns-record"></a>NS レコードの作成

次に、contoso.com ゾーンに、**engineering** ゾーンのネーム サーバー (NS) レコードを作成します。

```azurepowershell-interactive
$Records = @()
$Records += New-AzDnsRecordConfig -Nsdname <name server 1 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 2 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 3 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 4 noted previously>
$RecordSet = New-AzDnsRecordSet -Name engineering -RecordType NS -ResourceGroupName <resource group name> -TTL 3600 -ZoneName contoso.com -DnsRecords $Records
```

## <a name="test-the-delegation"></a>委任をテストする

nslookup を使用して委任をテストします。

1. PowerShell ウィンドウを開きます。

1. コマンド プロンプトに「`nslookup www.engineering.contoso.com.`」と入力します。

1. アドレス **10.10.10.10** を示す権限のない回答を受け取ります。

## <a name="next-steps"></a>次のステップ

[Azure でホストされているサービスの逆引き DNS を構成する](dns-reverse-dns-for-azure-services.md)方法について説明します。
