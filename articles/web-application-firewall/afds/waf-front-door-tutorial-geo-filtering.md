---
title: Azure Front Door サービスの geo フィルタリング Web アプリケーション ファイアウォール ポリシーを構成する
description: このチュートリアルでは、geo フィルタリング ポリシーを作成して、既存の Front Door フロントエンド ホストに関連付ける方法について学習します。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5c1bd9b4f9420a67283c3b0bbed77e5fbf1a87a7
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110668765"
---
# <a name="set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Front Door に使用する geo フィルタリング WAF ポリシーを設定する

このチュートリアルでは、Azure PowerShell を使用して、サンプル geo フィルタリング ポリシーを作成し、それを既存の Front Door フロントエンド ホストに関連付ける方法を説明します。 このサンプル geo フィルタリング ポリシーでは、他のすべての国/地域 (米国を除く) からの要求がブロックされます。

Azure サブスクリプションをお持ちでない場合は、ここで[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

geo フィルター ポリシーの設定を開始する前に、PowerShell 環境を設定して Front Door プロファイルを作成します。
### <a name="set-up-your-powershell-environment"></a>PowerShell 環境をセットアップする
Azure PowerShell には、Azure リソースの管理に [Azure Resource Manager](../../azure-resource-manager/management/overview.md) モデルを使う一連のコマンドレットが用意されています。 

[Azure PowerShell](/powershell/azure/) をローカル コンピューターにインストールして、すべての PowerShell セッションで使用することができます。 リンク先のページの手順に従って Azure の資格情報でサインインし、Az PowerShell モジュールをインストールします。

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>サインインのための対話型ダイアログを使用して Azure に接続する

```
Install-Module -Name Az
Connect-AzAccount
```
最新バージョンの PowerShellGet がインストールされていることを確認します。 次のコマンドを実行して、PowerShell を再度開きます。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Az.FrontDoor モジュールをインストールする 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Front Door プロファイルを作成する

Front Door プロファイルを作成するには、[Front Door プロファイルの作成に関するクイック スタート](../../frontdoor/quickstart-create-front-door.md)で説明されている手順に従います。

## <a name="define-geo-filtering-match-condition"></a>geo フィルタリングの一致条件を定義する

一致条件を作成するときに、[New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) のパラメーターを使用して、"US" から送信されていない要求を選択するサンプル一致条件を作成します。 国/地域マッピングに対する 2 文字の国/地域番号は、「[Azure Front Door のドメインに対する geo フィルタリングとは](waf-front-door-geo-filtering.md)」で提供されています。

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>アクションおよび優先度と共に geo フィルタリングの一致条件をルールに追加する

[New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) を使用して、一致条件、アクション、および優先度に基づいて CustomRule オブジェクト `nonUSBlockRule` を作成します。  CustomRule には、複数の MatchCondition を含めることができます。  この例では、Action を Block に、Priority を 1 (最高優先度) に設定しています。

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>ポリシーにルールを追加する

`Get-AzResourceGroup` を使用して、Front Door プロファイルが含まれているリソース グループの名前を見つけます。 次に、[New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) を使用して、Front Door プロファイルが含まれている指定したリソース グループに、`nonUSBlockRule` を含む `geoPolicy` ポリシー オブジェクトを作成します。 geo ポリシーには、一意の名前を指定する必要があります。 

次の例では、*myResourceGroupFD1* という名前のリソース グループを使用します。また、Front Door プロファイルを作成したときに、[Front Door の作成に関するクイック スタート](../../frontdoor/quickstart-create-front-door.md)で説明されている手順に従ったと想定しています。 次の例のポリシー名 *geoPolicyAllowUSOnly* を一意のポリシー名に置き換えてください。

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Front Door フロントエンド ホストに WAF ポリシーをリンクさせる

WAF ポリシー オブジェクトを既存の Front Door フロントエンド ホストにリンクさせて、Front Door のプロパティを更新します。 

そうするには、まず、[Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor) を使用して Front Door オブジェクトを取得します。 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

次に、[Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor) を使用して、フロントエンド WebApplicationFirewallPolicyLink プロパティを `geoPolicy` の resourceId に設定します。

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Front Door フロントエンド ホストに WAF ポリシーをリンクさせるために必要な WebApplicationFirewallPolicyLink プロパティの設定は 1 回だけです。 それ以降のポリシーの更新は、自動的にフロントエンド ホストに適用されます。

## <a name="next-steps"></a>次のステップ

- [Azure Web アプリケーション ファイアウォール](../overview.md)について学習します。
- [フロント ドアの作成](../../frontdoor/quickstart-create-front-door.md)方法について学習します。
