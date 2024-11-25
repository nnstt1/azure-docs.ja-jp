---
title: 従来の Azure DNS プライベート ゾーンを新しいリソース モデルに移行する
titleSuffix: Azure DNS
description: このガイドでは、従来のプライベート DNS ゾーンを最新のリソース モデルに移行する方法の詳細な手順について説明します
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: 566608da26cafab0d491663300136ebc815eb3ff
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357096"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>従来の Azure DNS プライベート ゾーンを新しいリソース モデルに移行する

パブリック プレビュー中、プライベート DNS ゾーンは "zoneType" プロパティが "Private" に設定された "dnszones" リソースを使用して作成されました。 このようなゾーンは、2019 年 12 月 31 日以降はサポートされないため、"dnszones" ではなく "privateDnsZones" というリソースの種類を使用する GA リソース モデルに移行する必要があります。 移行プロセスはシンプルです。Microsoft は、このプロセスを自動化するための PowerShell スクリプトを用意しました。 このガイドでは、Azure DNS プライベート ゾーンを新しいリソース モデルに移行するための詳細な手順について説明します。

移行が必要な dnszones リソースを確認するには、Azure CLI で次のコマンドを実行します。

```azurecli
az account set --subscription <SubscriptionId>
az network dns zone list --query "[?zoneType=='Private']"
```

## <a name="prerequisites"></a>前提条件

最新バージョンの Azure PowerShell がインストールされていることを確認します。 Azure PowerShell (Az) とそのインストール方法の詳細については、「[Azure Az PowerShell モジュールの概要](/powershell/azure/new-azureps-module-az)」を参照してください。

Azure PowerShell の Az.PrivateDns モジュールがインストールされていることを確認します。 このモジュールをインストールするには、管理者特権の PowerShell ウィンドウ (管理者モード) を開いて、次のコマンドを入力します

```powershell
Install-Module -Name Az.PrivateDns
```

>[!IMPORTANT]
>移行プロセスは完全に自動化されており、ダウンタイムを発生させることはないと予想されます。 ただし、重要な運用環境で Azure DNS プライベート ゾーン (プレビュー) を使用している場合は、計画メンテナンスの期間内に以下の移行プロセスを実行する必要があります。 移行スクリプトを実行している間は、プライベート DNS ゾーンの構成またはレコードセットを変更しないようにしてください。

## <a name="installing-the-script"></a>スクリプトのインストール

管理者特権の PowerShell ウィンドウ (管理者モード) を開いて、次のコマンドを実行します

```powershell
install-script PrivateDnsMigrationScript
```

スクリプトをインストールするよう求めるメッセージが表示されたら、「A」と入力します

![スクリプトのインストール](./media/private-dns-migration-guide/install-migration-script.png)

[https://www.powershellgallery.com/packages/PrivateDnsMigrationScript](https://www.powershellgallery.com/packages/PrivateDnsMigrationScript) では、最新バージョンの PowerShell スクリプトを手動で取得することもできます

>[!IMPORTANT]
>移行スクリプトは Azure Cloud Shell で実行できず、インターネットに接続されている VM またはローカル コンピューターで実行する必要があります。

## <a name="running-the-script"></a>スクリプトの実行

次のコマンドを実行して、スクリプトを実行します

```powershell
PrivateDnsMigrationScript.ps1
```

![スクリプトの実行](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>サブスクリプション ID を入力して Azure にサインインする

移行する予定のプライベート DNS ゾーンが含まれるサブスクリプションの ID を入力するよう求められます。 自分の Azure アカウントにサインインするよう求められます。 スクリプトがサブスクリプション内のプライベート DNS ゾーン リソースにアクセスできるように、サインインを完了します。

![Azure にログインする](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>移行したい DNS ゾーンを選択する

スクリプトによって、サブスクリプション内にあるすべてのプライベート DNS ゾーンの一覧が取得され、移行したいものを確認するよう求められます。 「A」と入力して、すべてのプライベート DNS ゾーンを移行します。 この手順を実行すると、スクリプトによって、新しいリソース モデルを使用して新しいプライベート DNS ゾーンが作成され、データが新しい DNS ゾーンにコピーされます。 この手順によって既存のプライベート DNS ゾーンに変更が加えられることはありません。

![DNS ゾーンを選択する](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>DNS 解決を新しい DNS ゾーンに切り替える

ゾーンとレコードが新しいリソース モデルにコピーされたら、DNS 解決を新しい DNS ゾーンに切り替えるよう、スクリプトによって求められます。 この手順では、従来のプライベート DNS ゾーンと自分の仮想ネットワークの間における関連付けが削除されます。 従来のゾーンが仮想ネットワークからリンク解除されると、上記の手順で作成された新しい DNS ゾーンが、これらの仮想ネットワークの DNS 解決を自動的に引き継ぎます。

"A" を選択して、すべての仮想ネットワークの DNS 解決を切り替えます。

![名前解決の切り替え](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>DNS 解決を検証する

先に進む前に、自分の DNS ゾーンでの DNS 解決が想定どおりに機能していることを検証します。 自分の Azure VM にサインインし、移行済みのゾーンに対して nslookup クエリを発行して、DNS 解決が機能していることを検証できます。

![名前解決を検証する](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

DNS クエリが解決されていないとわかった場合、数分待ってクエリを再試行します。 DNS クエリが想定どおりに機能する場合、スクリプトによってプライベート DNS ゾーンから仮想ネットワークを削除するよう要求されたら、「Y」と入力します。

![名前解決を確認する](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>何らかの理由で、移行済みのゾーンに対する DNS 解決が想定どおりに機能しない場合は、上記の手順で「N」と入力します。スクリプトによって、DNS 解決が従来のゾーンに戻されます。 サポート チケットを作成してください。Microsoft がお客様の DNS ゾーンの移行をお手伝いいたします。

## <a name="cleanup"></a>クリーンアップ

この手順では、従来の DNS ゾーンを削除します。これは、DNS 解決が想定どおりに機能していることを検証した後にのみ実行するようにしてください。 各プライベート DNS ゾーンを削除するよう求められます。 そのゾーンの DNS 解決が正常に機能することを確認した後、すべてのプロンプトで「Y」と入力します。

![クリーンアップ](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>自動化の更新

テンプレート、PowerShell スクリプト、SDK を使用して開発されたカスタム コードなど、自動化を使用している場合、プライベート DNS ゾーンで新しいリソース モデルを使用するために、自動化を更新する必要があります。 新しいプライベート DNS の CLI、PS、SDK のドキュメントを以下に示します。
* [Azure DNS プライベート ゾーンの REST API](/rest/api/dns/privatedns/privatezones)
* [Azure DNS プライベート ゾーンの CLI](/cli/azure/network/private-dns/link/vnet)
* [Azure DNS プライベート ゾーンの PowerShell](/powershell/module/az.privatedns/)
* [Azure DNS プライベート ゾーンの SDK](/dotnet/api/overview/azure/privatedns/management)

## <a name="need-further-help"></a>さらにサポートが必要な場合

移行プロセスに関してさらにサポートが必要な場合、または何らかの理由で上記の手順がうまくいかない場合は、サポート チケットを作成してください。 サポート チケットには、PowerShell スクリプトによって生成されたトランスクリプト ファイルを添えてください。

## <a name="next-steps"></a>次の手順

* [Azure PowerShell](./private-dns-getstarted-powershell.md) または [Azure CLI](./private-dns-getstarted-cli.md) を使用して Azure DNS のプライベート ゾーンを作成する方法を確認します。

* [プライベート ゾーンのシナリオ](./private-dns-scenarios.md)に関しては、Azure DNS のプライベート ゾーンで実現できるいくつかの一般的なシナリオを参照してください。

* Azure DNS のプライベート ゾーンに関する一般的な質問と回答については、[プライベート DNS の FAQ](./dns-faq-private.yml) に関する記事をご覧ください。特定の種類の操作で期待できる固有の動作についても説明があります。

* 「[DNS ゾーンとレコードの概要](dns-zones-records.md)」で DNS ゾーンとレコードについて学びます。

* Azure のその他の重要な[ネットワーク機能](../networking/fundamentals/networking-overview.md)について参照してください。