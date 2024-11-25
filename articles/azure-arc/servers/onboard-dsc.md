---
title: Windows PowerShell DSC を使用して Connected Machine エージェントをインストールする
description: この記事では、Windows PowerShell DSC を使用して、Azure Arc 対応サーバーを使用してマシンを Azure に接続する方法について説明します。
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: 357d89a989efbc3b8deded193808d47753b722d9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124789087"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>Windows PowerShell DSC を使用して Connected Machine エージェントをインストールする方法

[Windows PowerShell Desired State Configuration](/powershell/scripting/dsc/getting-started/winGettingStarted) (DSC) を使用して、Windows コンピューターのソフトウェアのインストールと構成を自動化できます。 この記事では、DSC を使用して Azure Arc 対応サーバー Connected Machine エージェントをハイブリッド Windows マシンにインストールする方法について説明します。

## <a name="requirements"></a>必要条件

- Windows PowerShell バージョン 4.0 以降

- [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc) DSC モジュール

- マシンを Azure Arc 対応サーバーに非対話形式で接続するためのサービス プリンシパル。 Azure Arc 対応サーバー用のサービス プリンシパルをまだ作成していない場合は、「[大規模なオンボーディング用にサービス プリンシパルを作成する](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)」のセクションにある手順に従ってください。

## <a name="install-the-connectedmachine-dsc-module"></a>ConnectedMachine DSC モジュールをインストールする

1. モジュールを手動でインストールするには、ソース コードをダウンロードし、プロジェクト ディレクトリの内容を `$env:ProgramFiles\WindowsPowerShell\Modules folder` に解凍します。 または、次のコマンドを実行して、(PowerShell 5.0 の) PowerShellGet を使用して PowerShell ギャラリーからインストールします。

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. インストールを確認するには、次のコマンドを実行して、Azure Connected Machine DSC リソースが使用可能であることを確認します。

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   次のような出力結果が表示されます。

   ![Connected Machine DSC モジュールのインストールの確認の例](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>エージェントをインストールして Azure に接続する

このモジュールのリソースは、Azure Connected Machine Agent の構成を管理するように設計されています。 また、`AzureConnectedMachineDsc\examples` フォルダーにある PowerShell スクリプト `AzureConnectedMachineAgent.ps1` も含まれています。 コミュニティ リソースを使用して、ダウンロードとインストールが自動化され、Azure Arc との接続が確立されます。このスクリプトでは、「[Azure portal からハイブリッド マシンを Azure に接続する](onboard-portal.md)」に説明されているものに似た手順が実行されます。

マシンでプロキシ サーバーを介してサービスと通信する必要がある場合は、エージェントをインストールした後、[こちら](manage-agent.md#update-or-remove-proxy-settings)に記載されているコマンドを実行する必要があります。 これにより、プロキシ サーバーのシステム環境変数 `https_proxy` が設定されます。 コマンドを手動で実行する代わりに、[ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc) モジュールを使用して DSC でこの手順を実行できます。 この構成を使用した場合、エージェントは HTTP プロトコルを使用してプロキシ サーバー経由で通信します。

>[!NOTE]
>DSC で実行できるようにするには、localhost の構成を実行している場合でも PowerShell のリモート コマンドを受信するように Windows を構成する必要があります。 環境を簡単に正しく構成するには、管理者特権の PowerShell ターミナルで `Set-WsManQuickConfig -Force` を実行するだけです。
>

`Start-DscConfiguration` コマンドレットを使用して、構成ドキュメント (MOF ファイル) をマシンに適用できます。

PowerShell スクリプトに渡して使用するパラメーターを次に示します。

- `TenantId`:Azure AD の専用インスタンスを表す一意識別子 (GUID)。

- `SubscriptionId`:その中でマシンが必要な Azure サブスクリプションのサブスクリプション ID (GUID)。

- `ResourceGroup`:接続されたマシンの所属先にしたいリソース グループの名前。

- `Location`:[サポート対象の Azure リージョン](overview.md#supported-regions)に関するページを参照してください。 この場所は、リソース グループの場所と同じ場合も異なる場合もあります。

- `Tags`:Cnnected Machine リソースに適用する必要があるタグの文字列配列。

- `Credential`:[サービス プリンシパル](onboard-service-principal.md)を使用して大規模にマシンを登録するために使用される、**ApplicationId** と **password** を持つ PowerShell 資格情報オブジェクト。

1. PowerShell コンソールで、`.ps1` ファイルを保存したフォルダーに移動します。

2. 次の PowerShell コマンドを実行して、MOF ドキュメントをコンパイルします (DSC 構成のコンパイルについては、「[DSC 構成](/powershell/scripting/dsc/configurations/configurations)」をご覧ください):

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. これにより、`C:\dsc` という名前の新しいフォルダーに、`localhost.mof file` が作成されます。

エージェントをインストールし、Azure Arc 対応サーバーに接続するように構成したら、Azure portal に移動して、サーバーが正常に接続されたことを確認します。 自分のマシンは [Azure portal](https://aka.ms/hybridmachineportal) に表示されます。

## <a name="adding-to-existing-configurations"></a>既存の構成への追加

このリソースを既存の DSC 構成に追加して、コンピューターのエンドツーエンドの構成を表すことができます。 たとえば、セキュリティで保護されたオペレーティング ステムの設定を行う構成に、このリソースを追加できます。

PowerShell ギャラリーの [CompositeResource](https://www.powershellgallery.com/packages/compositeresource) モジュールを使用して、構成例の[複合リソース](/powershell/scripting/dsc/resources/authoringResourceComposite)を作成して、構成の組み合わせをさらにシンプルにすることができます。

## <a name="next-steps"></a>次のステップ

* トラブルシューティング情報は、[Connected Machine エージェントの問題解決ガイド](troubleshoot-agent-onboard.md)を参照してください。

* [計画と展開ガイド](plan-at-scale-deployment.md)を参照して、任意の規模で Azure Arc 対応サーバーをデプロイし、一元的な管理と監視を実装する計画を立ててください。

* [Azure Policy](../../governance/policy/overview.md) を使用してマシンを管理する方法を確認します。VM の[ゲスト構成](../../governance/policy/concepts/guest-configuration.md)、マシンの報告先が、予期された Log Analytics ワークスペースであることの確認、[VM 分析情報](../../azure-monitor/vm/vminsights-enable-policy.md)を使用した監視の有効化などの方法です。
