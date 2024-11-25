---
title: Front Door 用の WAF のレート制限ルールを構成する - Azure PowerShell
description: 既存の Front Door エンドポイント用のレート制限ルールの構成方法について説明します。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 02/26/2020
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6db1c2a80700da1710294b2b8d08ec87c9398a2b
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110678478"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Azure PowerShell を使用して Web アプリケーション ファイアウォールのレート制限ルールを構成する
Azure Front Door 用の Azure Web アプリケーション ファイアウォール (WAF) のレート制限ルールでは、1 分間にクライアントから送信できる要求数を制御します。
この記事では、Azure PowerShell を使用して WAF レート制限ルールを構成し、クライアントから Web アプリケーション (URL に */promo* が含まれる) に送信できる要求数を制御する方法を示します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

> [!NOTE]
> レート制限はクライアント IP アドレスごとに適用されます。 複数のクライアントが別の IP アドレスから Front Door にアクセスしている場合は、それら独自のレート制限が適用されます。

## <a name="prerequisites"></a>前提条件
レート制限ポリシーの設定を開始する前に、PowerShell 環境を設定して Front Door プロファイルを作成します。
### <a name="set-up-your-powershell-environment"></a>PowerShell 環境をセットアップする
Azure PowerShell には、Azure リソースの管理に [Azure Resource Manager](../../azure-resource-manager/management/overview.md) モデルを使う一連のコマンドレットが用意されています。 

[Azure PowerShell](/powershell/azure/) をローカル コンピューターにインストールして、すべての PowerShell セッションで使用することができます。 リンク先のページの手順に従って Azure の資格情報でサインインし、Az PowerShell モジュールをインストールします。

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>サインインのための対話型ダイアログを使用して Azure に接続する
```
Connect-AzAccount

```
Front Door モジュールをインストールする前に、現在のバージョンの PowerShellGet がインストールされていることを確認してください。 次のコマンドを実行したら、PowerShell を再度開きます。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Az.FrontDoor モジュールをインストールする 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Front Door プロファイルを作成する
Front Door プロファイルを作成するには、[Front Door プロファイルの作成に関するクイック スタート](../../frontdoor/quickstart-create-front-door.md)で説明されている手順に従います

## <a name="define-url-match-conditions"></a>URL 一致条件を定義する
URL 一致条件 (URL に /promo が含まれる) を定義するには、[New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) を使用します。
次の例では、*/promo* が *RequestUri* 変数の値として照合されます。

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>カスタム レート制限ルールを作成する
レート制限を設定するには、[New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) を使用します。 次の例では、制限が 1000 に設定されています。 クライアントから promo ページへの要求数が 1 分間中に 1000 件を超えると、次の 1 分間が開始されるまでの間、要求がブロックされます。

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>セキュリティ ポリシーを構成する

`Get-AzureRmResourceGroup` を使用して、Front Door プロファイルが含まれているリソース グループの名前を見つけます。 次に、Front Door プロファイルを含む指定されたリソース グループ内で、[New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) を使用して、カスタム レート制限ルールによるセキュリティ ポリシーを構成します。

次の例では、*myResourceGroupFD1* という名前のリソース グループを使用します。また、Front Door プロファイルを作成したときに、[Front Door の作成に関するクイック スタート](../../frontdoor/quickstart-create-front-door.md)で説明されている手順 ([New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) を使用) に従ったと想定しています。

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Front Door フロントエンド ホストにポリシーをリンクする
セキュリティ ポリシー オブジェクトを既存の Front Door フロントエンド ホストにリンクして、Front Door のプロパティを更新します。 最初に、[Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) コマンドを使用して Front Door オブジェクトを取得します。
次に、[Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) コマンドを使用して、フロントエンド *WebApplicationFirewallPolicyLink* プロパティを、前の手順で作成した "$ratePolicy" の *resourceId* に設定します。 

次の例では、*myResourceGroupFD1* という名前のリソース グループを使用します。また、Front Door プロファイルを作成したときに、[Front Door の作成に関するクイック スタート](../../frontdoor/quickstart-create-front-door.md)で説明されている手順に従ったと想定しています。 また、次の例では、$frontDoorName を Front Door プロファイルの名前に置き換えます。 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Front Door フロントエンドにセキュリティ ポリシーをリンクさせるために必要な *WebApplicationFirewallPolicyLink* プロパティの設定は 1 回だけです。 それ以降のポリシーの更新は、自動的にフロントエンドに適用されます。

## <a name="next-steps"></a>次のステップ

- [Front Door](../../frontdoor/front-door-overview.md) の詳細を確認する。
