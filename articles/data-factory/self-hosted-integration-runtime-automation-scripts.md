---
title: ローカル PowerShell スクリプトを使用したセルフホステッド統合ランタイムのインストールの自動化
description: ローカル コンピューターでのセルフホステッド統合ランタイムのインストールを自動化するには、次の操作を実行します。
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 05/09/2020
ms.openlocfilehash: a1a05322a43f791ad9058659ea6e0cd53839196c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124814771"
---
# <a name="automating-self-hosted-integration-runtime-installation-using-local-powershell-scripts"></a>ローカル PowerShell スクリプトを使用したセルフホステッド統合ランタイムのインストールの自動化
ローカル コンピューター (Resource Manager テンプレートを利用できる Azure VM を除く) でのセルフホステッド統合ランタイムのインストールを自動化するために、ローカル PowerShell スクリプトを使用できます。 この記事では、使用できる 2 つのスクリプトについて説明します。

## <a name="prerequisites"></a>前提条件

* ローカル コンピューターで PowerShell を起動します。 スクリプトを実行するには、 **[管理者として実行]** を選択する必要があります。
* セルフホステッド統合ランタイム ソフトウェアを[ダウンロード](https://www.microsoft.com/download/details.aspx?id=39717)します。 ダウンロードしたファイルのパスをコピーします。 
* セルフホステッド統合ランタイムを登録するには **認証キー** も必要です。
* 手動更新を自動化するには、事前に構成されたセルフホステッド統合ランタイムが必要です。

## <a name="scripts-introduction"></a>スクリプトの概要 

> [!NOTE]
> これらのスクリプトは、セルフホステッド統合ランタイムの[ドキュメント化されたコマンド ライン ユーティリティ](./create-self-hosted-integration-runtime.md#set-up-an-existing-self-hosted-ir-via-local-powershell)を使用して作成されます。 必要に応じて、自動化のニーズに対応するために、これらのスクリプトをカスタマイズできます。
> スクリプトはノードごとに適用する必要があるため、高可用性のセットアップ (2 つ以上のノード) の場合は、必ずすべてのノードで実行してください。

* セットアップを自動化する場合: **[InstallGatewayOnLocalMachine.ps1](https://github.com/Azure/Azure-DataFactory/blob/main/SamplesV2/SelfHostedIntegrationRuntime/AutomationScripts/InstallGatewayOnLocalMachine.ps1)** を使用して、新しいセルフホステッド統合ランタイム ノードをインストールし統合します。このスクリプトを使用して、セルフホステッド統合ランタイム ノードをインストールし、認証キーに登録できます。 このスクリプトでは、2 つの引数を受け取ります。**最初** の引数は、ローカルディスク上の [セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)の場所を指定し、**2 番目** の引数は、(セルフホステッド IR ノードの登録用の) **認証キー** を指定します。

* 手動更新を自動化する場合:セルフホステッド IR ノードを特定のバージョンまたは最新バージョンの **[script-update-gateway.ps1](https://github.com/Azure/Azure-DataFactory/blob/main/SamplesV2/SelfHostedIntegrationRuntime/AutomationScripts/script-update-gateway.ps1)** に更新します。これは、自動更新を無効にした場合、または更新をより細かく制御する場合にもサポートされます。 スクリプトを使用して、セルフホステッド統合ランタイム ノードを最新バージョンまたは指定された上位バージョンに更新できます (ダウングレードは機能しません)。 バージョン番号を指定するための引数を受け取ります (例: -version 3.13.6942.1)。 バージョンが指定されていない場合、セルフホステッド IR は常に、[ダウンロード](https://www.microsoft.com/download/details.aspx?id=39717)で見つかった最新バージョンに更新されます。
    > [!NOTE]
    > 最後の 3 つのバージョンのみを指定できます。 これは、既存のノードを最新バージョンに更新するために使用するのが理想的です。 **これは、登録済みのセルフホステッド IR があることを前提としています**。 

## <a name="usage-examples"></a>使用例

### <a name="for-automating-setup"></a>セットアップを自動化する場合
1. セルフホステッド IR を[こちら](https://www.microsoft.com/download/details.aspx?id=39717)からダウンロードします。 
1. 上記のダウンロードされた SHIR MSI (インストール ファイル) のパスを指定します。 たとえば、パスが *C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi* の場合、下の PowerShell コマンドライン例をこのタスクに使用できます。

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\InstallGatewayOnLocalMachine.ps1 -path "C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi" -authKey "[key]"
   ```

    > [!NOTE]
    > [Key] を認証キーに置き換えて、IR を登録します。
    > "username" をユーザー名に置き換えます。
    > スクリプトを実行するときに、"InstallGatewayOnLocalMachine.ps1" ファイルの場所を指定します。 この例では、デスクトップに保存しています。

1. コンピューターに事前にインストールされたセルフホステッド IR が 1 つある場合は、スクリプトによって自動的にアンインストールされ、新しい IR が構成されます。 次のウィンドウがポップアウトされます: :::image type="content" source="media/self-hosted-integration-runtime-automation-scripts/integration-runtime-configure.png" alt-text="統合ランタイムの構成":::

1. インストールとキーの登録が完了すると、"*Succeed to install gateway\(ゲートウェイのインストールに成功\)* " と "*Succeed to register gateway\(ゲートウェイの登録に成功\)* " の結果が、ローカルの PowerShell に表示されます。
        [:::image type="content" source="media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png#lightbox" alt-text="スクリプト 1 の実行結果](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png)":::

### <a name="for-automating-manual-updates"></a>手動更新を自動化する場合
このスクリプトは、最新のセルフホステッド統合ランタイムを更新したり、インストールして登録したりするために使用されます。 スクリプトの実行では、次の手順が実行されます。
1. 現在のセルフホステッド IR バージョンを確認します
2. 引数から最新バージョンまたは指定されたバージョンを取得します
3. 現在のバージョンより新しいバージョンがある場合:
    * セルフホステッド IR msi をダウンロードします
    * これをアップグレードします

次のコマンドライン例に従って、このスクリプトを使用できます。
* 最新のゲートウェイをダウンロードしてインストールする。

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1
   ```    
* 指定されたバージョンのゲートウェイをダウンロードしてインストールする。
   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1 -version 3.13.6942.1
   ``` 
   現在のバージョンが既に最新のバージョンである場合は、更新が必要でないことを示した次の結果が表示されます。   
    [:::image type="content" source="media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png#lightbox" alt-text="スクリプト 2 の実行結果](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png)":::