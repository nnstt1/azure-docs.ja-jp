---
title: 'クイックスタート: アプリケーションの高可用性のためのプロファイルを作成する - Azure PowerShell - Azure Traffic Manager'
description: このクイック スタート記事では、高可用性 Web アプリケーションを構築するための Traffic Manager プロファイルの作成方法について説明します。
services: traffic-manager
author: duongau
ms.author: duau
manager: kumud
ms.date: 04/19/2021
ms.topic: quickstart
ms.service: traffic-manager
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: devx-track-azurepowershell, mode-api
ms.openlocfilehash: a970013ebffc9ed4249baf34de39d146357742c4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074690"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>クイック スタート:Azure PowerShell を使用して Web アプリケーションの高可用性を実現する Traffic Manager プロファイルを作成する

このクイック スタートでは、Web アプリケーションの高可用性を実現する Traffic Manager プロファイルの作成方法について説明します。

このクイック スタートでは、Web アプリケーションの 2 つのインスタンスを作成します。 これらは、それぞれ別の Azure リージョンで実行されています。 皆さんは、[エンドポイントの優先度](traffic-manager-routing-methods.md#priority-traffic-routing-method)に基づいて Traffic Manager プロファイルを作成します。 このプロファイルにより、Web アプリケーションを実行しているプライマリ サイトにユーザー トラフィックを誘導します。 Traffic Manager では、Web アプリケーションが継続的に監視されます。 プライマリ サイトが利用できなくなった場合には、バックアップ サイトへの自動フェールオーバーが実行されます。

:::image type="content" source="./media/quickstart-create-traffic-manager-profile/environment-diagram.png" alt-text="Azure PowerShell を使用した Traffic Manager デプロイ環境の図。" border="false":::

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、ここで[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-resource-group"></a>リソース グループを作成する
[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用してリソース グループを作成します。

```azurepowershell-interactive

# Variables
$Location1="EastUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager プロファイルの作成

[New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) を使用して、エンドポイントの優先度に基づいてユーザー トラフィックを誘導する Traffic Manager プロファイルを作成します。

```azurepowershell-interactive

# Generates a random value
$Random=(New-Guid).ToString().Substring(0,8)
$mytrafficmanagerprofile="mytrafficmanagerprofile$Random"

New-AzTrafficManagerProfile `
-Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup `
-TrafficRoutingMethod Priority `
-MonitorPath '/' `
-MonitorProtocol "HTTP" `
-RelativeDnsName $mytrafficmanagerprofile `
-Ttl 30 `
-MonitorPort 80
```

## <a name="create-web-apps"></a>Web アプリを作成する

このクイック スタートでは、2 つの異なる Azure リージョン ("*米国西部*" と "*米国東部*") にデプロイされた、2 つの Web アプリケーション インスタンスが必要になります。 これらインスタンスは、それぞれ Traffic Manager のプライマリとフェールオーバーのエンドポイントとして機能します。

### <a name="create-web-app-service-plans"></a>Web App Service プランを作成する
2 つの異なる Azure リージョンにデプロイする Web アプリケーションの 2 つのインスタンスに対して [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) を使用して Web App Service プランを作成します。

```azurepowershell-interactive

# Variables
$Location1="EastUS"
$Location2="WestEurope"

# Create an App service plan
New-AzAppservicePlan -Name "myAppServicePlanEastUS" -ResourceGroupName MyResourceGroup -Location $Location1 -Tier Standard
New-AzAppservicePlan -Name "myAppServicePlanEastUS" -ResourceGroupName MyResourceGroup -Location $Location2 -Tier Standard

```
### <a name="create-a-web-app-in-the-app-service-plan"></a>App Service プランで Web アプリを作成する
"*米国東部*" と "*西ヨーロッパ*" の Azure リージョンの App Service プランで、[New-AzWebApp](/powershell/module/az.websites/new-azwebapp) を使用して Web アプリケーションの 2 つのインスタンスを作成します。

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name myWebAppEastUS -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "myAppServicePlanEastUS").Id
$App2ResourceId=(New-AzWebApp -Name myWebAppWestEurope -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "myAppServicePlanWestEurope").Id

```

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager エンドポイントの追加
次のように、[New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) を使用して、Traffic Manager プロファイルに 2 つの Web アプリを Traffic Manager エンドポイントとして追加します。
- すべてのユーザー トラフィックをルーティングするプライマリ エンドポイントとして、"*米国東部*" Azure リージョンにある Web アプリを追加します。 
- フェールオーバー エンドポイントとして "*西ヨーロッパ*" Azure リージョンにある Web アプリを追加します。 プライマリ エンドポイントが使用できない場合、トラフィックは自動的にフェールオーバー エンドポイントにルーティングされます。

```azurepowershell-interactive
New-AzTrafficManagerEndpoint -Name "myPrimaryEndpoint" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App1ResourceId `
-EndpointStatus "Enabled"

New-AzTrafficManagerEndpoint -Name "myFailoverEndpoint" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App2ResourceId `
-EndpointStatus "Enabled"
```

## <a name="test-traffic-manager-profile"></a>Traffic Manager プロファイルのテスト

このセクションでは、Traffic Manager プロファイルのドメイン名を確認します。 また、プライマリ エンドポイントを使用できないように構成します。 最後に、Web アプリがまだ使用できることを確認します。 これは、Traffic Manager によってトラフィックがフェールオーバー エンドポイントへと送信されるためです。

### <a name="determine-the-dns-name"></a>DNS 名の決定

[Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile) を使用して Traffic Manager プロファイルの DNS 名を決定します。

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

**RelativeDnsName** 値をコピーします。 Traffic Manager プロファイルの DNS 名は *http://<* relativednsname *>.trafficmanager.net* です。 

### <a name="view-traffic-manager-in-action"></a>Traffic Manager の動作確認
1. Web ブラウザーで、Traffic Manager プロファイルの DNS 名 (*http://<* relativednsname *>.trafficmanager.net*) を入力して、Web アプリの既定の Web サイトを確認します。

    > [!NOTE]
    > このクイック スタート シナリオでは、すべての要求がプライマリ エンドポイントにルーティングされます。 これは **優先度 1** に設定されています。
2. 実際の Traffic Manager フェールオーバーを確認するには、[Disable-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint) を使用してプライマリ サイトを無効にします。

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name "myPrimaryEndpoint" `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Traffic Manager プロファイルの DNS 名 (*http://<* relativednsname *>.trafficmanager.net*) をコピーして、新しい Web ブラウザー セッションで Web サイトを表示します。
4. Web アプリがまだ使用できることを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

完了したら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用してリソース グループ、Web アプリケーション、およびすべての関連リソースを削除します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Web アプリケーションの高可用性を実現する Traffic Manager プロファイルを作成しました。 トラフィックのルーティングについて理解を深めるために、引き続き Traffic Manager のチュートリアルをご覧ください。

> [!div class="nextstepaction"]
> [Traffic Manager のチュートリアル](tutorial-traffic-manager-improve-website-response.md)
