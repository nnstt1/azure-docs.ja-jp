---
title: クイック スタート:Azure DNS ゾーンとレコードを作成する - Azure PowerShell
titleSuffix: Azure DNS
description: Azure DNS で、DNS ゾーンとレコードを作成する方法について説明します。 これは、Azure PowerShell を使用して最初の DNS ゾーンとレコードを作成して管理するための詳細なクイック スタートです。
services: dns
author: rohinkoul
ms.author: rohink
ms.date: 04/23/2021
ms.topic: quickstart
ms.service: dns
ms.custom: devx-track-azurepowershell, mode-api
ms.openlocfilehash: f30fd154f3998166381f4cf3a0341c023480bd5e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131080044"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>クイック スタート:Azure PowerShell を使用して Azure DNS ゾーンとレコードを作成する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このクイック スタートでは、Azure PowerShell を使用して最初の DNS ゾーンとレコードを作成します。 これらの手順は、[Azure portal](dns-getstarted-portal.md) または [Azure CLI](dns-getstarted-cli.md) を使用して実行することもできます。 

DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。 Azure DNS でドメインのホストを開始するには、そのドメイン名用に DNS ゾーンを作成する必要があります。 ドメインの DNS レコードはすべて、この DNS ゾーン内に作成されます。 最後に、DNS ゾーンをインターネットに公開するには、ドメインのネーム サーバーを構成する必要があります。 ここでは、その手順について説明します。

:::image type="content" source="media/dns-getstarted-portal/environment-diagram.png" alt-text="Azure PowerShell を使用した DNS デプロイ環境の図。" border="false":::

Azure DNS は、プライベート ドメインの作成もサポートします。 プライベート DNS ゾーンとレコードを初めて作成する方法に関する順を追った説明については、「[PowerShell で Azure DNS プライベート ゾーンの使用を開始する](private-dns-getstarted-powershell.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- ローカルにインストールされた Azure PowerShell または Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>リソース グループの作成

DNS ゾーンを作成する前に、DNS ゾーンが含まれるリソース グループを作成します。

```powershell
New-AzResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

DNS ゾーンは、 `New-AzDnsZone` コマンドレットを使用して作成します。 次の例では、*MyResourceGroup* というリソース グループに *contoso.xyz* という DNS ゾーンを作成します。 この例の値を実際の値に置き換えて、DNS ゾーンを作成できます。

```powershell
New-AzDnsZone -Name contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>DNS レコードの作成

レコード セットは、`New-AzDnsRecordSet` コマンドレットを使用して作成します。 下の例では、リソース グループ "MyResourceGroup" で DNS ゾーン "contoso.xyz" に相対名 "www" を持つレコードを作成します。 レコード セットの完全修飾名は、"www.contoso.xyz" になります。 レコードの種類は "A"、IP アドレスは "10.10.10.10"、TTL は 3,600 秒です。

```powershell
New-AzDnsRecordSet -Name www -RecordType A -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.10.10.10")
```

## <a name="view-records"></a>レコードの表示

ゾーンで DNS レコードを表示するには、次を使用します。

```powershell
Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="test-the-name-resolution"></a>名前解決をテストする

テスト DNS ゾーンを作成し、その中にテスト "A" レコードを含めたので、*nslookup* という名前のツールを使用して、名前解決をテストできます。 

**DNS 名前解決をテストするには:**

1. 次のコマンドレットを実行して、ゾーン用のネーム サーバーの一覧を取得します。

   ```azurepowershell
   Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -RecordType ns
   ```

1. 前の手順の出力から、いずれかのネーム サーバーの名前をコピーします。

1. コマンド プロンプトを開いて、次のコマンドを実行します。

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   次に例を示します。

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   次のような画面が表示されます。

   ![n s lookup コマンドと、Server、Address、Name、Address の値を表示するコマンド プロンプト ウィンドウを示すスクリーンショット。](media/dns-getstarted-portal/nslookup.PNG)

ホスト名 **www\.contoso.xyz** は、構成したとおり、**10.10.10.10** に名前解決されています。 この結果で、名前解決が正常に機能していることを確認できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になった場合、リソース グループを削除することで、このクイック スタートで作成したすべてのリソースを削除できます。

```powershell
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>次のステップ

これで、Azure PowerShell を使用して最初の DNS ゾーンとレコードを作成できました。カスタム ドメインで Web アプリのレコードを作成できます。

> [!div class="nextstepaction"]
> [カスタム ドメインにおける Web アプリの DNS レコードの作成](./dns-web-sites-custom-domain.md)
