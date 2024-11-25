---
title: Azure Virtual Desktop (クラシック) テナントのホスト プールの作成 - Azure
description: Azure Virtual Desktop (クラシック) テナント環境設定時のテナントおよびホスト プールの問題をトラブルシューティングし、解決する方法。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 61aea8002faa92c88081c78bc9ff05ec7e053094
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124818507"
---
# <a name="tenant-and-host-pool-creation-in-azure-virtual-desktop-classic"></a>Azure Virtual Desktop (クラシック)でのテナントとホスト プールの作成

>[!IMPORTANT]
>この内容は、Azure Resource Manager Azure Virtual Desktop オブジェクトをサポートしていない Azure Virtual Desktop (クラシック) に適用されます。 Azure Resource Manager Azure Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../troubleshoot-set-up-issues.md)を参照してください。

この記事では、Azure Virtual Desktop テナントと、関連するセッション ホスト プール インフラストラクチャの初期セットアップ中の問題について説明します。

## <a name="provide-feedback"></a>フィードバックの提供

Azure Virtual Desktop サービスに関して製品チームや活発なコミュニティのメンバーと話をしたいときは、[Azure Virtual Desktop の技術コミュニティ](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)にアクセスしてください。

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Windows 10 Enterprise マルチセッションのイメージの入手

Windows 10 Enterprise マルチセッションのイメージを使用するには、Azure Marketplace にアクセスし、 **[Get Started]\(開始する\)**  >  **[Microsoft Windows 10]** 、[[Windows 10 Enterprise for Virtual Desktops, Version 1809]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice) を選択します。

> [!div class="mx-imgBorder"]
> ![Windows 10 Enterprise for Virtual Desktops, Version 1809 を選択するスクリーンショット。](../media/AzureMarketPlace.png)

## <a name="creating-azure-virtual-desktop-tenant&quot;></a>Azure Virtual Desktop テナントを作成する

このセクションでは、Azure Virtual Desktop テナントを作成するときの潜在的な問題について説明します。

### <a name=&quot;error-aadsts650052-the-app-needs-access-to-a-service&quot;></a>エラー:AADSTS650052 アプリにはサービスへのアクセスが必要です。

未処理エラーの例:

```Error
AADSTS650052 Message The app needs access to a service(\"{name}\") that your organization
\"{organization}\" has not subscribed to or enabled. Contact your IT Admin to review the
configuration of your service subscriptions.650052 Message The app needs access to a service
(\"{name}\") that your organization \"{organization}\" has not subscribed to or enabled.
Contact your IT Admin to review the configuration of your service subscriptions.
```

**原因:** Azure Active ディレクトリ インスタンスで、Azure Virtual Desktop への同意が付与されていません。

**解決策:** [こちらのガイドに従って](./tenant-setup-azure-active-directory.md#grant-permissions-to-azure-virtual-desktop)、同意を付与してください。

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>エラー:管理サービスに問い合わせる許可がユーザーに与えられていません

> [!div class="mx-imgBorder"]
> ![ユーザーが管理サービスのクエリを承認されていない PowerShell ウィンドウのスクリーンショット。](../media/UserNotAuthorizedNewTenant.png)

未処理エラーの例:

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**原因:** サインインしているユーザーに、Azure Active Directory の TenantCreator ロールが割り当てられていません。

**解決策:** [Azure Active Directory テナント内のユーザーに TenantCreator アプリケーション ロールを割り当てる](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role)の指示に従います。 指示に従った後、ユーザーを TenantCreator ロールに割り当てます。

> [!div class="mx-imgBorder"]
> ![TenantCreator ロールを割り当てたスクリーンショット。](../media/TenantCreatorRoleAssigned.png)

## <a name="creating-azure-virtual-desktop-session-host-vms"></a>Azure Virtual Desktop セッション ホスト VM を作成する

セッションホスト VM はいくつかの方法で作成できますが、Azure Virtual Desktop チームでサポートするのは、[Azure Marketplace](https://azuremarketplace.microsoft.com/) オファリングに関連する VM プロビジョニングの問題のみです。 詳細については、「[Azure Virtual Desktop を使用する際の問題– ホスト プール Azure Marketplace のオファリングをプロビジョニングする](#issues-using-azure-virtual-desktop--provision-a-host-pool-azure-marketplace-offering)」を参照してください。

## <a name="issues-using-azure-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Azure Virtual Desktop を使用する際の問題– ホスト プール Azure Marketplace のオファリングをプロビジョニングする

Azure Virtual Desktop – ホスト プールのプロビジョニング テンプレートは、Azure Marketplace から入手できます。

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>エラー:GitHub からのリンクを使用すると "無料アカウントの作成" メッセージが表示される

> [!div class="mx-imgBorder"]
> ![無料アカウントを作成するスクリーンショット。](../media/be615904ace9832754f0669de28abd94.png)

**原因 1:** Azure へのサインインに使用されたアカウントにアクティブなサブスクリプションがないか、使用されたアカウントにサブスクリプションを表示するアクセス許可がありません。

**解決策 1:** セッション ホスト VM がデプロイされる予定のサブスクリプションに対して、共同作成者 (以上) のアクセス権を持つアカウントでサインインします。

**原因 2:** 使用されているサブスクリプションが、Microsoft クラウド サービス プロバイダー (CSP) テナントの一部です。

**解決策 2:** GitHub の場所に移動して、**新しい Windows Virtual Desktop ホスト プールを作成およびプロビジョニング** し、次の手順に従います。

1. **[Azure に配置する]** を右クリックして **[リンク アドレスをコピー]** を選択します。
2. **メモ帳** を開き、リンクを貼り付けます。
3. # 文字の前に、CSP エンド カスタマー テナント名を挿入します。
4. ブラウザーで新しいリンクを開くと、Azure portal でテンプレートが読み込まれます。

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>エラー:"テンプレートのデプロイが有効ではありません"というエラーが表示される

> [!div class="mx-imgBorder"]
> !["テンプレートのデプロイ... が有効ではありません"というエラーのスクリーンショット](../media/troubleshooting-marketplace-validation-error-generic.png)

特定のアクションを実行する前に、アクティビティ ログを確認して、失敗したデプロイの検証に関する詳細なエラーを確認する必要があります。

アクティビティ ログのエラーを表示するには、次のようにします:

1. 現在の Azure Marketplace デプロイ オファリングを終了します。
2. 上部の検索バーで、**アクティビティ ログ** を検索して選択します。
3. **[デプロイの検証]** という名前で状態が **[失敗]** のアクティビティを見つけて、そのアクティビティを選択します。

   > [!div class="mx-imgBorder"]
   > ![ **失敗** 状態での各々の "デプロイの検証" 操作スクリーンショット](../media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. [JSON] を選択し、"statusMessage" フィールドが表示されるまで、画面の一番下までスクロールします。

   > [!div class="mx-imgBorder"]
   > ![失敗したアクティビティのスクリーンショット。JSON テキストの statusMessage メッセージプロパティを囲む赤いボックスがあります。](../media/troubleshooting-marketplace-validation-error-json-boxed.png)

操作テンプレートがクォータ制限を超えた場合は、次のいずれかの方法で修正できます:

 - 最初に使用したパラメーターで Azure Marketplace を実行しますが、今回は、使用する VM と VM コアの数を減らすことができます。
 - ブラウザーで **[statusMessage]** フィールドに表示されているリンクを開き、指定した VM SKU の Azure サブスクリプションのクォータを増やす要求を送信します。

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Azure Resource Manager テンプレートと PowerShell Desired State Configuration (DSC) エラー

Azure Resource Manager テンプレートと PowerShell DSC のデプロイ失敗をトラブルシューティングするには、次の手順に従います。

1. 「[Azure Resource Manager でのデプロイ操作の表示](../../azure-resource-manager/templates/deployment-history.md)」を使用して、デプロイのエラーを確認します。
2. デプロイにエラーがない場合、「[リソースのアクションを監査するアクティビティ ログの表示](../../azure-monitor/essentials/activity-log.md)」を使用して、アクティビティ ログのエラーを確認します。
3. エラーが特定されたら、エラー メッセージと、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](../../azure-resource-manager/templates/common-deployment-errors.md)」のリソースを使用して問題に対処します。
4. 以前のデプロイ中に作成されたすべてのリソースを削除し、テンプレートのデプロイを再試行します。

### <a name="error-your-deployment-failedhostnamejoindomain"></a>エラー:デプロイに失敗しました….\<hostname>/joindomain

> [!div class="mx-imgBorder"]
> ![デプロイ失敗のスクリーンショット。](../media/e72df4d5c05d390620e07f0d7328d50f.png)

未処理エラーの例:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**原因 1:** VM をドメインに参加させるために提供された資格情報が正しくありません。

**解決策 1:** [セッション ホストの VM の構成](troubleshoot-vm-configuration-2019.md)に関するページで、「VMs are not joined to the domain」\(VM がドメインに参加していません\) の「Incorrect credentials」\(資格情報が正しくありません\) エラーを参照してください。

**原因 2:** ドメイン名が解決されません。

**解決策 2:** 「[エラー: ドメイン名が解決されません](troubleshoot-vm-configuration-2019.md#error-domain-name-doesnt-resolve)」 (「[セッション ホスト仮想マシンの構成](troubleshoot-vm-configuration-2019.md)」) を参照してください。

**原因 3:** 仮想ネットワーク (VNET) の DNS 構成が **既定** に設定されています。

これを解決するには、次の操作を実行します。

1. Azure Portal を開き、 **[仮想ネットワーク]** タブに移動します。
2. お使いの VNET を見つけて、 **[DNS サーバー]** を選択します。
3. 画面の右側に DNS サーバーのメニューが表示されます。 そのメニューで、 **[カスタム]** を選択します。
4. [カスタム] の下に表示されている DNS サーバーが、ドメイン コントローラーまたは Active Directory ドメインと一致していることを確認します。 お使いの DNS サーバーが表示されない場合は、 **[DNS サーバーの追加]** フィールドに値を入力して追加できます。

### <a name="error-your-deployment-failedunauthorized"></a>エラー:デプロイに失敗しました...\権限がありません

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**原因:** 使用しているサブスクリプションは、お客様の展開先のリージョンで必要な機能にアクセスすることができない種類です。 このエラーは、MSDN、無料、または教育向けのサブスクリプションなどで表示されることがあります。

**解決策:** サブスクリプションの種類またはリージョンを必要な機能にアクセスできるものに変更します。

### <a name="error-vmextensionprovisioningerror"></a>エラー:VMExtensionProvisioningError

> [!div class="mx-imgBorder"]
> ![ターミナルのプロビジョニングの状態 (失敗) を示しているデプロイ失敗のスクリーンショット。](../media/7aaf15615309c18a984673be73ac969a.png)

**原因 1:** Azure Virtual Desktop 環境の一時的なエラーです。

**原因 2:** 一時的な接続エラーです。

**解決:** PowerShell を使用してサインインすることによって、Azure Virtual Desktop 環境が正常であることを確認します。 「[PowerShell を使用してホスト プールを作成する](create-host-pools-powershell-2019.md)」で、VM の登録を手動で終了します。

### <a name="error-the-admin-username-specified-isnt-allowed"></a>エラー:指定した管理者のユーザー名は許可されません

> [!div class="mx-imgBorder"]
> ![指定した管理者が許可されないことを示しているデプロイ失敗のスクリーンショット。](../media/f2b3d3700e9517463ef88fa41875bac9.png)

未処理エラーの例:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**原因:** 指定されたパスワードに、禁止された部分文字列 (admin、administrator、root) が含まれています。

**解決策:** ユーザー名を更新するか、別のユーザーを使用します。

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>エラー:拡張機能を処理しているときに VM がエラーを報告しました

> [!div class="mx-imgBorder"]
> ![リソース操作の完了とターミナルのプロビジョニングの状態を示している、デプロイ失敗のスクリーンショット。](../media/49c4a1836a55d91cd65125cf227f411f.png)

未処理エラーの例:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-provision-host-pool-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource":
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**原因:** PowerShell DSC 拡張機能が、VM 上で管理者アクセス権を取得できませんでした。

**解決策:** ユーザー名とパスワードが仮想マシン上で管理アクセス権を持っていることを確認して、Azure Resource Manager テンプレートをもう一度実行します。

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>エラー:DeploymentFailed – PowerShell DSC 構成 'FirstSessionHost' がエラーで完了しました

> [!div class="mx-imgBorder"]
> ![PowerShell DSC 構成 'FirstSessionHost' がエラーで完了したデプロイ失敗のスクリーンショット。](../media/64870370bcbe1286906f34cf0a8646ab.png)

未処理エラーの例:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**原因:** PowerShell DSC 拡張機能が、VM 上で管理者アクセス権を取得できませんでした。

**解決策:** 指定されたユーザー名とパスワードが仮想マシン上で管理アクセス権を持っていることを確認して、Azure Resource Manager テンプレートをもう一度実行します。

### <a name="error-deploymentfailed--invalidresourcereference"></a>エラー:DeploymentFailed – InvalidResourceReference

未処理エラーの例:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**原因:** リソース グループ名の一部が、テンプレートによって作成されている特定のリソースに使用されます。 既存のリソースと名前が一致するため、テンプレートは別のグループから既存のリソースを選択する場合があります。

**解決策:** Azure Resource Manager テンプレートを実行してセッション ホスト VM をデプロイするときは、サブスクリプションのリソース グループ名の最初の 2 文字を一意にします。

### <a name="error-deploymentfailed--invalidresourcereference"></a>エラー:DeploymentFailed – InvalidResourceReference

未処理エラーの例:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**原因:** このエラーの原因は、Azure Resource Manager テンプレートによって作成された NIC が、既に VNET にある別の NIC と同じ名前であることです。

**解決策:** 異なるホスト プレフィックスを使用します。

### <a name="error-deploymentfailed--error-downloading"></a>エラー:DeploymentFailed – ダウンロード中のエラー

未処理エラーの例:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**原因:** このエラーの原因は、Azure Resource Manager テンプレートに関連付けられている zip ファイルのダウンロードが、静的ルート、ファイアウォール規則、または NSG によってブロックされていることです。

**解決策:** ブロックしている静的ルート、ファイアウォール規則、または NSG を削除します。 必要に応じて、Azure Resource Manager テンプレートの json ファイルをテキスト エディターで開き、zip ファイルへのリンクを実行し、許可されている場所にリソースをダウンロードします。

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>エラー:管理サービスに問い合わせる許可がユーザーに与えられていません

未処理エラーの例:

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00",
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s).
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**原因:** 指定された Azure Virtual Desktop テナント管理者に、有効なロールの割り当てがありません。

**解決策:** Azure Virtual Desktop テナントを作成したユーザーが、Azure Virtual Desktop PowerShell にサインインして、試行されたユーザーにロールの割り当てを割り当てる必要があります。 GitHub の Azure Resource Manager テンプレートのパラメーターを実行している場合、PowerShell コマンドを使用して次の手順に従います。

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Azure Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-ad-multi-factor-authentication-mfa"></a>エラー:ユーザーが Azure AD Multi-Factor Authentication (MFA) を要求する

> [!div class="mx-imgBorder"]
> ![多要素認証 (MFA) の欠如によるデプロイ失敗のスクリーンショット](../media/MFARequiredError.png)

未処理エラーの例:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**原因:** 指定された Azure Virtual Desktop テナント管理者が、サインインするために Azure AD 多要素認証 (MFA) を必要としています。

**修正:** [「チュートリアル: PowerShell を使用してサービス プリンシパルとロールの割り当てを作成する](create-service-principal-role-powershell.md)」の手順に従って、サービス プリンシパルを作成し、Azure 仮想デスクトップ テナントのロールを割り当てます。 サービス プリンシパルを使用して Azure Virtual Desktop にサインインできることを確認した後、使用している方法に応じて、Azure Marketplace オファリングまたは GitHub の Azure Resource Manager テンプレートを再実行します。 次の指示に従って、方法に応じた正しいパラメーターを入力します。

Azure Marketplace オファリングを実行している場合、Azure Virtual Desktop に正しく認証されるよう、次のパラメーターの値を指定します。

- Azure Virtual Desktop テナント RDS 所有者:サービス プリンシパル
- アプリケーション ID:作成した新しいサービス プリンシパルのアプリケーション ID
- パスワード/パスワードの確認:サービス プリンシパル用に生成したパスワードのシークレット
- Azure AD テナント ID:作成したサービス プリンシパルの Azure AD テナント ID

GitHub の Azure Resource Manager テンプレートを実行している場合、Azure Virtual Desktop に正しく認証されるように、次のパラメーターの値を指定します。

- テナント管理者のユーザー プリンシパル名 (UPN) またはアプリケーション ID:作成した新しいサービス プリンシパルのアプリケーション ID
- テナント管理パスワード:サービス プリンシパル用に生成したパスワードのシークレット
- IsServicePrincipal: **true**
- AadTenantId:作成したサービス プリンシパルの Azure AD テナント ID

### <a name="error-vmsubnet-not-available-when-configuring-virtual-networks"></a>エラー: 仮想ネットワークを構成するときに vmSubnet を使用できません

**原因:** Azure Virtual Desktop Marketplace テンプレートでは、テンプレートに指定されている VM の合計数と同じ数以上の IP アドレスが含まれるサブネットのみが UI に表示されます。 サブネットで使用できる IP アドレスの実際の数は、デプロイされている新しい VM の数と同じであれば十分ですが、現在の UI ではこの数を計算できません。

**解決策:** Marketplace UI を使用しないで、追加されている VM の数と同じ数以上の IP アドレスを持つサブネットを指定できます。これは、[既存のデプロイを再デプロイする](expand-existing-host-pool-2019.md#redeploy-from-azure)か、[GitHub から基になる ARM テンプレートを使用してデプロイする](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool)ときに、"**existingSubnetName**" パラメーターでサブネット名を指定して実行できます。

## <a name="next-steps"></a>次のステップ

- Azure Virtual Desktop トラブルシューティングの概要とエスカレーション トラックについては、「[トラブルシューティングの概要、フィードバック、サポート](troubleshoot-set-up-overview-2019.md)」を参照してください。
- Azure Virtual Desktop で仮想マシン (VM) の構成中に発生した問題を解決するには、[セッション ホスト仮想マシンの構成](troubleshoot-vm-configuration-2019.md) に関する記事を参照してください。
- Azure Virtual Desktop クライアント接続の問題のトラブルシューティングを行う場合は、「[Windows Virtual Desktop サービスの接続](troubleshoot-service-connection-2019.md)」を参照してください。
- リモート デスクトップ クライアントの問題をトラブルシューティングするには、[リモート デスクトップ クライアントのトラブルシューティング](../troubleshoot-client.md) に関するページを参照してください
- Azure Virtual Desktop で PowerShell を使用しているときに発生した問題を解決するには、「[Azure Virtual Desktop PowerShell](troubleshoot-powershell-2019.md)」を参照してください。
- サービスの詳細については、[Azure Virtual Desktop 環境](environment-setup-2019.md)に関するページを参照してください。
- トラブルシューティング チュートリアルについては、「[Tutorial:Resource Manager テンプレート デプロイのトラブルシューティング](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md)」を参照してください。
- 監査アクションについては、「 [リソース マネージャーの監査操作](../../azure-monitor/essentials/activity-log.md)」をご覧ください。
- デプロイ時にエラーが発生した場合の対応については、 [デプロイ操作の確認](../../azure-resource-manager/templates/deployment-history.md)に関するページを参照してください。