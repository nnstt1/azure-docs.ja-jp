---
title: テンプレートを使用して Azure Monitor で Windows VM のメトリックを収集する
description: Resource Manager テンプレートを使用して Windows 仮想マシンのゲスト OS メトリックを Azure Monitor メトリック データベース ストアに送信する
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: bwren
ms.openlocfilehash: c0e8ae9e642caad0486b862b48d94ba392256a45
ms.sourcegitcommit: 1ee13b62c094a550961498b7a52d0d9f0ae6d9c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2021
ms.locfileid: "109839493"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine"></a>Azure Resource Manager テンプレートを使用して Windows 仮想マシンのゲスト OS メトリックを Azure Monitor メトリック ストアに送信する
Azure 仮想マシンのゲスト OS から収集したパフォーマンス データは、他の[プラットフォーム メトリック](./monitor-azure-resource.md#monitoring-data)のように自動的には収集されません。 Azure Monitor [診断拡張機能](../agents/diagnostics-extension-overview.md)をインストールして、メトリック データベースにゲスト OS メトリックを収集し、凖リアルタイムのアラート、グラフ作成、ルーティング、REST API からのアクセスなど、Azure Monitor メトリックのすべての機能で使用できるようにします。 この記事では、Resource Manager テンプレートを使用して Windows 仮想マシンのゲスト OS のパフォーマンス メトリックをメトリック データベースに送信するプロセスについて説明します。

> [!NOTE]
> Azure portal を使用してゲスト OS メトリックを収集するように診断拡張機能を構成する方法の詳細については、「[Windows Azure Diagnostics 拡張機能 (WAD) のインストールと構成](../agents/diagnostics-extension-windows-install.md)」を参照してください。


Resource Manager テンプレートを初めて利用する場合は、[テンプレートのデプロイ](../../azure-resource-manager/management/overview.md)とその構造および構文についてご確認ください。

## <a name="prerequisites"></a>前提条件

- サブスクリプションを [Microsoft.Insights](../../azure-resource-manager/management/resource-providers-and-types.md) に登録する必要があります

- [Azure PowerShell](/powershell/azure) または [Azure Cloud Shell](../../cloud-shell/overview.md) がインストールされている必要があります。

- お使いの VM リソースが、[カスタム メトリックをサポートするリージョン](./metrics-custom-overview.md#supported-regions)に存在する必要があります。


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Azure Monitor をデータ シンクとして設定する
Azure Diagnostics 拡張機能では、"データ シンク" と呼ばれる機能を使って、メトリックとログがさまざまな場所にルーティングされます。 次の手順では、Resource Manager テンプレートと PowerShell を使用して、新しい "Azure Monitor" データ シンクを使って VM をデプロイする方法を説明します。

## <a name="author-resource-manager-template"></a>Resource Manager テンプレートを作成する
この例では、公開されているサンプル テンプレートを使用できます。 開始用テンプレートは https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-simple-windows にあります。

- **Azuredeploy.json** は、仮想マシンのデプロイ用に事前構成された Resource Manager テンプレートです。

- **Azuredeploy.parameters.json** は、VM 用に設定するユーザー名やパスワードなどの情報が格納されているパラメーター ファイルです。 デプロイ中、このファイルで設定されているパラメーターが、Resource Manager テンプレートによって使用されます。

両方のファイルをダウンロードし、ローカルに保存します。

### <a name="modify-azuredeployparametersjson"></a>azuredeploy.parameters.json ファイルを更新する
*azuredeploy.parameters.json* ファイルを開きます

1. VM の **adminUsername** と **adminPassword** について値を入力します。 これらのパラメーターは VM へのリモート アクセスに使用されます。 お使いの VM がハイジャックされないように、このテンプレートの値は使用しないでください。 ボットは、インターネット上にあるパブリック GitHub リポジトリのユーザー名やパスワードをスキャンします。 多くの場合、このようなボットは、これらの既定値を使用して VM をテストします。

1. VM について一意の dnsname を作成します。

### <a name="modify-azuredeployjson"></a>azuredeploy.json を変更する

*azuredeploy.json* ファイルを開きます

ストレージ アカウント ID を、テンプレートの **variables** セクションの、**storageAccountName** エントリの後ろに追加します。

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

このマネージド サービス ID (MSI) 拡張機能を、テンプレートの **resources** セクションの先頭に追加します。 この拡張機能により、Azure Monitor が、出力されているメトリックを確実に受け取るようになります。

```json
//Find this code.
"resources": [
// Add this code directly below.
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'), '/', 'WADExtensionSetup')]",
        "apiVersion": "2017-12-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    },
```

**identity** 構成を VM リソースに追加して、Azure によってシステム ID が MSI 拡張機能に確実に割り当てられるようにします。 この手順により、VM が自身に関するゲスト メトリックを Azure Monitor に確実に出力できます。

```json
// Find this section
                "subnet": {
            "id": "[variables('subnetRef')]"
            }
        }
        }
    ]
    }
},
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    // add these 3 lines below
    "identity": {
    "type": "SystemAssigned"
    },
    //end of added lines
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
    "hardwareProfile": {
    ...
```

次の構成を追加して、Windows 仮想マシンの診断拡張機能を有効にします。 シンプルな Resource Manager ベースの仮想マシンについては、拡張機能の構成を、仮想マシンのリソース配列に追加できます。 "sinks" 行 ("AzMonSink" と、このセクションで後ほど出てくる対応する "SinksConfig") によって、拡張機能がメトリックを Azure Monitor に直接出力できます。 必要に応じて、パフォーマンス カウンターを自由に追加または削除してください。


```json
        "networkProfile": {
            "networkInterfaces": [
            {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
            ]
        },
"diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob]"
    }
}
},
//Start of section to add
"resources": [
{
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'), '/', 'Microsoft.Insights.VMDiagnosticsSettings')]",
            "apiVersion": "2017-12-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.12",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "WadCfg": {
                "DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
        },
                    "Directories": {
                    "scheduledTransferPeriod": "PT1M",
    "IISLogs": {
                        "containerName": "wad-iis-logfiles"
                    },
                    "FailedRequestLogs": {
                        "containerName": "wad-failedrequestlogs"
                    }
                    },
                    "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "sinks": "AzMonSink",
                    "PerformanceCounterConfiguration": [
                        {
                        "counterSpecifier": "\\Memory\\Available Bytes",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\Committed Bytes",
                        "sampleRate": "PT15S"
                        }
                    ]
                    },
                    "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                        {
                        "name": "Application!*"
                        }
                    ]
                    },
                    "Logs": {
                    "scheduledTransferPeriod": "PT1M",
                    "scheduledTransferLogLevelFilter": "Error"
                    }
                },
                "SinksConfig": {
                    "Sink": [
                    {
                        "name" : "AzMonSink",
                        "AzureMonitor" : {}
                    }
                    ]
                }
                },
                "StorageAccount": "[variables('storageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[variables('storageAccountName')]",
                "storageAccountKey": "[listKeys(variables('accountid'),'2015-06-15').key1]",
                "storageAccountEndPoint": "https://core.windows.net/"
            }
            }
        }
        ]
//End of section to add
```


両方のファイルを保存して閉じます。


## <a name="deploy-the-resource-manager-template"></a>Resource Manager テンプレートをデプロイする

> [!NOTE]
> Azure Diagnostics 拡張機能バージョン 1.5 以上を実行する必要があります。また、Resource Manager テンプレートで **autoUpgradeMinorVersion**: プロパティが "true" に設定されていなければなりません。 その後、Azure によって VM の開始時に適切な拡張機能が読み込まれます。 ご自身のテンプレートにこれらの設定がない場合は、テンプレートを変更して再デプロイします。


Resource Manager テンプレートをデプロイするために、Azure PowerShell を利用します。

1. PowerShell を起動します。
1. `Login-AzAccount` を使用して、Azure にログインします。
1. `Get-AzSubscription` を使用して、サブスクリプションの一覧を取得します。
1. 仮想マシンを作成または更新するために使用しているサブスクリプションを設定します。

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. デプロイする VM の新しいリソース グループを作成するために、次のコマンドを実行します。

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > 必ず、[カスタム メトリックに対して有効になっている Azure リージョンを使用してください](./metrics-custom-overview.md)。

1. 次のコマンドを実行して、Resource Manager テンプレートを使用して VM をデプロイします。
   > [!NOTE]
   > 既存の VM を更新するには、 *-Mode Incremental* を以下のコマンドの末尾に追加するだけです。

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. デプロイが成功した後、Azure portal に VM が表示されるようになります。この VM によって、メトリックが Azure Monitor に出力されます。

   > [!NOTE]
   > 選択した vmSkuSize に関連するエラーが発生することがあります。 この場合は、azuredeploy.json ファイルに戻り、vmSkuSize パラメーターの既定値を更新します。 ここでは "Standard_DS1_v2" を試してみることをお勧めします。

## <a name="chart-your-metrics"></a>メトリックをグラフ化する

1. Azure ポータルにログインします。

2. 左側のメニューで **[モニター]** を選択します。

3. [モニター] ページで、 **[メトリック]** を選択します。

   ![メトリック ページ](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. 集計の期間を **[過去 30 分間]** に変更します。

5. リソースのドロップダウン メニューで、作成した VM を選択します。 テンプレートの名前を変更しなかった場合は、*SimpleWinVM2* のはずです。

6. 名前空間のドロップダウン メニューで、 **[azure.vm.windows.guest]** を選択します

7. メトリックのドロップダウン メニューで、 **[Memory\%Committed Bytes in Use]** を選択します。


## <a name="next-steps"></a>次のステップ
- [カスタム メトリック](./metrics-custom-overview.md)の詳細を確認します。