---
title: Azure での Update Management のデプロイで事前スクリプトと事後スクリプトを管理する
description: この記事では、更新プログラムのデプロイのための事前スクリプトおよび事後スクリプトを構成および管理する方法について説明します。
services: automation
ms.subservice: update-management
ms.date: 09/16/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 19101cea8f435f09cb37aa2340f0bc02788442f3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459066"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>事前スクリプトと事後スクリプトを管理する

事前スクリプトと事後スクリプトは、更新プログラムのデプロイの前 (事前タスク) と後 (事後タスク) に Azure Automation アカウントで実行する Runbook です。 事前スクリプトと事後スクリプトは、ローカルではなく、Azure コンテキストで実行されます。 事前スクリプトは、更新プログラムのデプロイの開始時に実行されます。 Windows では、事後スクリプトはデプロイの最後で、かつ構成されているすべての再起動の後に実行されます。 Linux の場合、事後スクリプトはマシンの再起動の後ではなく、デプロイの終了後に実行されます。 

## <a name="pre-script-and-post-script-requirements"></a>事前スクリプトと事後スクリプトの要件

Runbook を事前スクリプトまたは事後スクリプトとして使用するには、Automation アカウントにインポートし、[Runbook を発行](../manage-runbooks.md#publish-a-runbook)する必要があります。

現時点では、PowerShell Runbook と Python 2 Runbook のみが事前および事後スクリプトとしてサポートされています。 Python 3、グラフィカル、PowerShell ワークフロー、グラフィカル PowerShell ワークフローなどの他の種類の Runbook は、現在、事前スクリプトまたは事後スクリプトとしてサポートされていません。

## <a name="pre-script-and-post-script-parameters"></a>事前スクリプトと事後スクリプトのパラメーター

事前スクリプトと事後スクリプトを構成する場合、Runbook のスケジュール設定と同様にパラメーターを渡すことができます。 パラメーターは、更新プログラムの展開の作成の時点で定義されます。 事前スクリプトと事後スクリプトでは、次の型がサポートされます。

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

事前スクリプトと事後スクリプトの Runbook パラメーターでは、boolean、object、または array 型はサポートされません。 これらの値を指定すると、Runbook は失敗します。 

別のオブジェクト型が必要な場合は、Runbook 内の独自のロジックで別の方にキャストできます。

標準の Runbook パラメーターに加えて、`SoftwareUpdateConfigurationRunContext` パラメーター (JSON 文字列の型) が提供されます。 事前スクリプトまたは事後スクリプトにパラメーターを定義すると、更新プログラムのデプロイによって自動的に渡されます。 パラメーターには、[SoftwareUpdateconfigurations API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) から返された情報のサブセットである、更新プログラムの展開に関する情報が含まれます。 以下のセクションでは、関連するプロパティを定義します。

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext プロパティ

|プロパティ  |Type |説明  |
|---------|---------|---------|
|SoftwareUpdateConfigurationName     |String | ソフトウェア更新構成の名前。        |
|SoftwareUpdateConfigurationRunId     |GUID | 実行の一意の ID。        |
|SoftwareUpdateConfigurationSettings     || ソフトウェア更新構成に関連したプロパティのコレクション。         |
|SoftwareUpdateConfigurationSettings.OperatingSystem     |int | 更新プログラムの展開の対象となるオペレーティング システム。 `1` = Windows および `2` = Linux        |
|SoftwareUpdateConfigurationSettings.Duration     |Timespan (HH:MM:SS) | ISO8601 に従って `PT[n]H[n]M[n]S` として実行される更新プログラムのデプロイの最大期間。メンテナンス期間とも呼ばれる。<br> 例: 02:00:00         |
|SoftwareUpdateConfigurationSettings.WindowsConfiguration     || Windows コンピューターに関連したプロパティのコレクション。         |
|SoftwareUpdateConfigurationSettings.WindowsConfiguration.excludedKbNumbers     |String | 更新プログラムの展開から除外される KB のスペース区切りの一覧。        |
|SoftwareUpdateConfigurationSettings.WindowsConfiguration.includedKbNumbers     |String | 更新プログラムの展開に含まれる KB のスペース区切りの一覧。        |
|SoftwareUpdateConfigurationSettings.WindowsConfiguration.UpdateCategories     |整数型 | 1 = "Critical";<br> 2 = "Security"<br> 4 = "UpdateRollUp"<br> 8 = "FeaturePack"<br> 16 = "ServicePack"<br> 32 = "Definition"<br> 64 = "Tools"<br> 128 = "Updates"        |
|SoftwareUpdateConfigurationSettings.WindowsConfiguration.rebootSetting     |String | 更新プログラムの展開のための再起動設定。 値は `IfRequired`、`Never`、`Always` です      |
|SoftwareUpdateConfigurationSettings.LinuxConfiguration     || Linux コンピューターに関連したプロパティのコレクション。         |
|SoftwareUpdateConfigurationSettings.LinuxConfiguration.IncludedPackageClassifications |整数型 |0 = "Unclassified"<br> 1 = "Critical"<br> 2 = "Security"<br> 4 = "Other"|
|SoftwareUpdateConfigurationSettings.LinuxConfiguration.IncludedPackageNameMasks |String | 更新プログラムの展開に含まれるパッケージ名のスペース区切りの一覧。 |
|SoftwareUpdateConfigurationSettings.LinuxConfiguration.ExcludedPackageNameMasks |String |更新プログラムの展開から除外されるパッケージ名のスペース区切りの一覧。 |
|SoftwareUpdateConfigurationSettings.LinuxConfiguration.RebootSetting |String |更新プログラムの展開のための再起動設定。 値は `IfRequired`、`Never`、`Always` です      |
|SoftwareUpdateConfiguationSettings.AzureVirtualMachines     |文字列配列 | 更新プログラムの展開における Azure VM 用の resourceId の一覧。        |
|SoftwareUpdateConfigurationSettings.NonAzureComputerNames|文字列配列 |更新プログラムの展開における Azure 以外のコンピューターの FQDN の一覧。|

次の例は、Linux コンピューターの **SoftwareUpdateConfigurationSettings** プロパティに渡される JSON 文字列です。

```json
"SoftwareUpdateConfigurationSettings": {
     "OperatingSystem": 2,
     "WindowsConfiguration": null,
     "LinuxConfiguration": {
         "IncludedPackageClassifications": 7,
         "ExcludedPackageNameMasks": "fgh xyz",
         "IncludedPackageNameMasks": "abc bin*",
         "RebootSetting": "IfRequired"
     },
     "Targets": {
         "azureQueries": null,
         "nonAzureQueries": ""
     },
     "NonAzureComputerNames": [
        "box1.contoso.com",
        "box2.contoso.com"
     ],
     "AzureVirtualMachines": [
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/vm-01"
     ],
     "Duration": "02:00:00",
     "PSComputerName": "localhost",
     "PSShowComputerName": true,
     "PSSourceJobInstanceId": "2477a37b-5262-4f4f-b636-3a70152901e9"
 }
```

次の例は、Windows コンピューターの **SoftwareUpdateConfigurationSettings** プロパティに渡される JSON 文字列です。

```json
"SoftwareUpdateConfigurationRunContext": {
    "SoftwareUpdateConfigurationName": "sampleConfiguration",
    "SoftwareUpdateConfigurationRunId": "00000000-0000-0000-0000-000000000000",
    "SoftwareUpdateConfigurationSettings": {
      "operatingSystem": "Windows",
      "duration": "02:00:00",
      "windows": {
        "excludedKbNumbers": [
          "168934",
          "168973"
        ],
        "includedUpdateClassifications": "Critical",
        "rebootSetting": "IfRequired"
      },
      "azureVirtualMachines": [
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/vm-01",
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/vm-02",
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/vm-03"
      ],
      "nonAzureComputerNames": [
        "box1.contoso.com",
        "box2.contoso.com"
      ]
    }
  }
```

すべてのプロパティの完全な例は、「[Get software update configuration by name (ソフトウェア更新構成を名前別に取得する)](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)」にあります。

> [!NOTE]
> `SoftwareUpdateConfigurationRunContext` オブジェクトには、マシン用の重複するエントリを含めることができます。 これにより、同じマシンで事前スクリプトと事後スクリプトが複数回実行される可能性があります。 この動作を回避するには、`Sort-Object -Unique` を使用して一意の VM 名だけを選択します。

## <a name="use-a-pre-script-or-post-script-in-a-deployment"></a>デプロイで事前スクリプトまたは事後スクリプトを使用する

更新プログラムの展開において事前スクリプトまたは事後スクリプトを使用するには、更新プログラムの展開の作成から始めます。 **[事前スクリプトと事後スクリプト]** を選択します。 この操作で、 **[Select Pre-scripts + Post-scripts] (事前スクリプト + 事後スクリプトの選択)** ページが開きます。

![スクリプトを選択する](./media/pre-post-scripts/select-scripts.png)

使用するスクリプトを選択します。 この例では、**UpdateManagement-TurnOnVms** Runbook を使用します。 Runbook を選択すると、 **[スクリプトの構成]** ページが開きます。 **[事前スクリプト]** を選択してから、 **[OK]** を選択します。

**UpdateManagement-TurnOffVms** スクリプトについて、このプロセスを繰り返します。 ただし、 **[スクリプトの種類]** を選択した場合は、 **[事後スクリプト]** を選択します。

これで、 **[選択された項目]** セクションに、ご自身が選択した両方のスクリプトが表示されます。 1 つは事前スクリプトで、もう 1 つは事後スクリプトです。

![[Selected items] (選択された項目)](./media/pre-post-scripts/selected-items.png)

更新プログラムの展開の構成を終了します。

更新プログラムの展開が完了したら、 **[更新プログラムの展開]** に移動して結果を表示できます。 見てわかるように、事前スクリプトと事後スクリプトの状態が表示されます。

![更新プログラムの結果](./media/pre-post-scripts/update-results.png)

更新プログラムのデプロイの実行を選択すると、事前スクリプトと事後スクリプトの追加の詳細情報が表示されます。 実行の時点でのスクリプト ソースへのリンクが表示されます。

![展開の実行の結果](./media/pre-post-scripts/deployment-run.png)

## <a name="stop-a-deployment"></a>デプロイを停止する

事前スクリプトに基づくデプロイを停止する場合は、例外を[スロー](../automation-runbook-execution.md#throw)する必要があります。 そうしないと、デプロイと事後スクリプトが引き続き実行されます。 次のコード スニペットは、PowerShell を利用して例外をスローする方法を示しています。

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.
        throw $summary.Summary
    }
}
```

Python 2 では、例外処理は [try](https://www.python-course.eu/exception_handling.php) ブロックで管理されます。

## <a name="interact-with-machines"></a>マシンを操作する

事前スクリプトと事後スクリプトは、デプロイ内のマシンで直接実行されるのではなく、Automation アカウント内で Runbook として実行されます。 事前タスクと事後タスクは Azure コンテキストでも実行され、Azure 以外のマシンにはアクセスできません。 以降のセクションでは、Azure VM であるか Azure 以外のマシンであるかにかかわらず、それらのマシンと直接対話できる方法について説明します。

### <a name="interact-with-azure-machines"></a>Azure マシンの操作

事前タスクと事後タスクは、Runbook として実行され、デプロイ内の Azure VM でネイティブに実行されることはありません。 Azure VM と対話するには、次のものが必要です。

* [マネージド ID](../automation-security-overview.md#managed-identities) または実行アカウント
* 実行する Runbook

Azure マシンを操作するには、[Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) コマンドレットを使用して、Azure VM を操作する必要があります。 この方法を示した例については、「[Update Management - スクリプトを実行コマンドで実行する](https://github.com/azureautomation/update-management-run-script-with-run-command)」にある Runbook の例を参照してください。

### <a name="interact-with-non-azure-machines"></a>Azure 以外のマシンの操作

事前タスクと事後タスクは Azure コンテキストで実行され、Azure 以外のマシンにはアクセスできません。 Azure 以外のマシンと対話するには、次のものが必要です。

* [マネージド ID](../automation-security-overview.md#managed-identities) または実行アカウント
* コンピューターにインストールされた Hybrid Runbook Worker
* ローカルで実行する Runbook
* 親 Runbook

Azure 以外のマシンと対話するためには、親 Runbook が Azure コンテキストで実行されます。 この Runbook は、[Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) コマンドレットを使用して子 Runbook を呼び出します。 `RunOn` パラメーターを指定し、スクリプトを実行する Hybrid Runbook Worker の名前を指定する必要があります。 Runbook の例「[Update Management - スクリプトをローカルで実行する](https://github.com/azureautomation/update-management-run-script-locally)」を参照してください。

## <a name="abort-patch-deployment"></a>修正プログラムのデプロイを中止する

事前スクリプトからエラーが返されたときに、展開を中止したい場合があります。 それを行うには、スクリプト内で障害を起こしているロジックのエラーを[スロー](/powershell/module/microsoft.powershell.core/about/about_throw)する必要があります。

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

Python 2 では、特定の条件が発生したときにエラーをスローする場合、[raise](https://docs.python.org/2.7/reference/simple_stmts.html#the-raise-statement) ステートメントを使用します。

```python
If (<My custom error logic>)
   raise Exception('Something happened.')
```

## <a name="samples"></a>サンプル

事前スクリプトと事後スクリプトのサンプルは [Azure Automation の GitHub 組織](https://github.com/azureautomation)と [PowerShell ギャラリー](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)にあります。または、Azure portal を使用してインポートできます。 Automation アカウントで、 **[プロセス オートメーション]** の下にある **[Runbook ギャラリー]** を選択することで、行うことができます。 フィルターに **[更新管理]** を使用します。

![ギャラリーの一覧](./media/pre-post-scripts/runbook-gallery.png)

または、次の一覧に示すように、スクリプト名で検索できます。

* 更新管理 - VM 有効
* 更新管理 - VM 無効
* 更新管理 - スクリプトをローカルで実行する
* 更新管理 - 事前および事後スクリプトのテンプレート
* 更新管理 - スクリプトを実行コマンドで実行する

> [!IMPORTANT]
> Runbook をインポートした後、使用できるようにするには、発行する必要があります。 それを行うには、Automation アカウントで Runbook を見つけて、 **[編集]** を選択してから **[発行]** を選択します。

サンプルはすべて、次の例で定義されている基本のテンプレートに基づいています。 このテンプレートを使用すると、事前スクリプトと事後スクリプトで使用する独自の Runbook を作成できます。 Azure に対して認証したり、`SoftwareUpdateConfigurationRunContext` パラメーターを処理したりするために必要なロジックが含まれています。

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
  It requires the Automation account's system-assigned managed identity.

.PARAMETER SoftwareUpdateConfigurationRunContext
  This is a system variable which is automatically passed in by Update Management during a deployment.
#>

param(
    [string]$SoftwareUpdateConfigurationRunContext
)

#region BoilerplateAuthentication
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext
#endregion BoilerplateAuthentication

#If you wish to use the run context, it must be converted from JSON
$context = ConvertFrom-Json $SoftwareUpdateConfigurationRunContext
#Access the properties of the SoftwareUpdateConfigurationRunContext
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId

Write-Output $context

#Example: How to create and write to a variable using the pre-script:
<#
#Create variable named after this run so it can be retrieved
New-AzAutomationVariable -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccount -Name $runId -Value "" -Encrypted $false
#Set value of variable
Set-AutomationVariable -Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

Runbook をシステム割り当てマネージド ID で実行する場合は、コードをそのままにしておきます。 ユーザー割り当てマネージド ID を使用する場合は、次のようにします。
1. 22 行目から `$AzureContext = (Connect-AzAccount -Identity).context` を削除し、
1. それを `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` に置き換えた後、
1. クライアント ID を入力します。

> [!NOTE]
> 非グラフィカル PowerShell Runbook の場合、`Add-AzAccount` と `Add-AzureRMAccount` は [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) のエイリアスです。 これらのコマンドレットを使用するか、Automation アカウントの[モジュール最新バージョンに更新](../automation-update-azure-modules.md)することができます。 Automation アカウントを作成したばかりのときでも、モジュールを更新する必要がある場合があります。

## <a name="next-steps"></a>次のステップ

Update Management の詳細については、「[VM の更新プログラムとパッチの管理](manage-updates-for-vm.md)」を参照してください。
