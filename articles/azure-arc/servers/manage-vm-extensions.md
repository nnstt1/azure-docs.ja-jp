---
title: Azure Arc 対応サーバーを使用した VM 拡張機能の管理
description: Azure Arc 対応サーバーを使用すると、Azure 以外の VM でのデプロイ後構成と自動化タスクを提供する仮想マシン拡張機能のデプロイを管理できます。
ms.date: 10/28/2021
ms.topic: conceptual
ms.openlocfilehash: d853317fad2da9d8d7f27cece4fd7d219cdc42dc
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132294183"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Azure Arc 対応サーバーを使用した仮想マシン拡張機能の管理

仮想マシン (VM) 拡張機能は、Azure VM でのデプロイ後の構成と自動タスクを提供する複数の小さなアプリケーションです。 たとえば、仮想マシンでソフトウェアのインストールやウイルス対策保護が必要な場合や、そこでスクリプトを実行するために、VM 拡張機能を使用できます。

Azure Arc 対応サーバーを使用すると、Azure VM 拡張機能を Azure 以外の Windows や Linux の VM にデプロイ、削除、更新して、ハイブリッド マシンのライフサイクルを通じた管理を簡素化できます。 VM 拡張機能は、ハイブリッド コンピューターまたは Arc 対応サーバーで管理されているサーバーで、次の方法を使用して管理できます。

- [Azure Portal](manage-vm-extensions-portal.md)
- [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Azure [Resource Manager テンプレート](manage-vm-extensions-template.md)

> [!NOTE]
> Azure Arc 対応サーバーでは、Azure 仮想マシンへの VM 拡張機能のデプロイと管理はサポートされていません。 Azure VM については、次の [VM 拡張機能の概要](../../virtual-machines/extensions/overview.md)に関する記事をご覧ください。

> [!NOTE]
> 現時点では、拡張機能は Azure portal または Azure CLI からのみ更新できます。 現時点では、Azure PowerShell、または Azure Resource Manager テンプレートを使用してこの操作を実行することはサポートされていません。

## <a name="key-benefits"></a>主な利点

Azure Arc 対応サーバーによる VM 拡張機能のサポートには、次のような主な利点があります。

- Log Analytics エージェント VM 拡張機能を有効にすることで、[Azure Monitor のログ](../../azure-monitor/logs/data-platform-logs.md)を使用して分析用のログ データを収集します。 Log Analytics によって、さまざまな種類のソースのデータに対する複雑な分析の実行のための仕様が便利になります。

- [VM インサイト](../../azure-monitor/vm/vminsights-overview.md)を使用して、Windows および Linux VM のパフォーマンスを分析し、それらの VM による処理や、他のリソースおよび外部の処理の利用状況を監視します。 これは、Log Analytics エージェントと Dependency Agent の両方の VM 拡張機能を有効にすることで実現されます。

- スクリプトをダウンロードし、カスタム スクリプト拡張機能を使用してハイブリッド接続マシンで実行します。 この拡張機能は、展開後の構成、ソフトウェアのインストール、その他の構成タスクや管理タスクに役立ちます。

- [Azure Key Vault](../../key-vault/general/overview.md) に保存されている証明書を自動的に更新します。

## <a name="availability"></a>可用性

VM 拡張機能は、[サポートされているリージョン](overview.md#supported-regions)の一覧でのみ使用できます。 これらのリージョンのいずれかにマシンをオンボードしてください。

## <a name="extensions"></a>拡張機能

このリリースでは、Windows および Linux マシンで次の VM 拡張機能がサポートされています。

Azure Connected Machine エージェント パッケージと拡張機能エージェント コンポーネントの詳細については、[エージェントの概要](agent-overview.md#agent-component-details)に関する記事を参照してください。

> [!NOTE]
> Azure Arc 対応サーバーでは、最近 DSC VM 拡張機能のサポートがなくなりました。 代わりに、カスタム スクリプト拡張機能を使用してサーバーやマシンの展開後の構成を管理することをお勧めします。

Arc 対応サーバーでは、リソース グループまたは別の Azure サブスクリプションの間に 1 つ以上の VM 拡張機能がインストールされたマシンの移動がサポートされ、このときに構成への影響は発生しません。 移動元と移動先のサブスクリプションが同じ [Azure Active Directory テナント](../../active-directory/develop/quickstart-create-new-tenant.md)内に存在している必要があります。 このサポートは、Connected Machine エージェントのバージョン **1.8.21197.005** から有効になります。 リソースの移動に進む前の詳細情報と考慮事項については、「[リソースを新しいリソース グループまたはサブスクリプションに移動する](../../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。

### <a name="windows-extensions"></a>Windows の拡張機能

|拡張子 |Publisher |Type |追加情報 |
|----------|----------|-----|-----------------------|
|Microsoft Defender for Cloud に組み込まれている脆弱性スキャナー |Qualys |WindowsAgent.AzureSecurityCenter |[Defender for Cloud に組み込まれている、Azure およびハイブリッド マシン向けの脆弱性評価ソリューション](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Microsoft Antimalware 拡張機能 |Microsoft.Azure.Security |IaaSAntimalware |[Windows 用の Microsoft Antimalware 拡張機能](../../virtual-machines/extensions/iaas-antimalware-windows.md) |
|カスタム スクリプト拡張機能 |Microsoft.Compute | CustomScriptExtension |[Windows カスタム スクリプト拡張機能](../../virtual-machines/extensions/custom-script-windows.md)|
|Log Analytics エージェント |Microsoft.EnterpriseCloud.Monitoring |MicrosoftMonitoringAgent |[Windows 用 Log Analytics VM 拡張機能](../../virtual-machines/extensions/oms-windows.md)|
|Azure Monitor for VMs (分析情報) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentWindows | [Windows 用 Dependency Agent 仮想マシン拡張機能](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Azure Key Vault 証明書の同期 | Microsoft.Azure.Key.Vault |KeyVaultForWindows | [Windows 用の Key Vault 仮想マシン拡張機能](../../virtual-machines/extensions/key-vault-windows.md) |
|Azure Monitor エージェント |Microsoft.Azure.Monitor |AzureMonitorWindowsAgent |[Azure Monitor エージェントをインストールする (プレビュー)](../../azure-monitor/agents/azure-monitor-agent-install.md) |
|Azure Automation Hybrid Runbook Worker 拡張機能 (プレビュー) |Microsoft.Compute |HybridWorkerForWindows |ローカルで Runbook を実行するために、[拡張機能ベースのユーザー Hybrid Runbook Worker をデプロイ](../../automation/extension-based-hybrid-runbook-worker-install.md)します。 |

### <a name="linux-extensions"></a>Linux の拡張機能

|拡張子 |Publisher |Type |追加情報 |
|----------|----------|-----|-----------------------|
|Microsoft Defender for Cloud に組み込まれている脆弱性スキャナー |Qualys |LinuxAgent.AzureSecurityCenter |[Defender for Cloud に組み込まれている、Azure およびハイブリッド マシン向けの脆弱性評価ソリューション](../../security-center/deploy-vulnerability-assessment-vm.md)|
|カスタム スクリプト拡張機能 |Microsoft.Azure.Extensions |CustomScript |[Linux カスタム スクリプト拡張機能バージョン 2](../../virtual-machines/extensions/custom-script-linux.md) |
|Log Analytics エージェント |Microsoft.EnterpriseCloud.Monitoring |OmsAgentForLinux |[Linux 用 Log Analytics VM 拡張機能](../../virtual-machines/extensions/oms-linux.md) |
|Azure Monitor for VMs (分析情報) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentLinux |[Linux 用 Dependency Agent 仮想マシン拡張機能](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Azure Key Vault 証明書の同期 | Microsoft.Azure.Key.Vault |KeyVaultForLinux | [Linux 用の Key Vault 仮想マシン拡張機能](../../virtual-machines/extensions/key-vault-linux.md) |
|Azure Monitor エージェント |Microsoft.Azure.Monitor |AzureMonitorLinuxAgent |[Azure Monitor エージェントをインストールする (プレビュー)](../../azure-monitor/agents/azure-monitor-agent-install.md) |
|Azure Automation Hybrid Runbook Worker 拡張機能 (プレビュー) |Microsoft.Compute |HybridWorkerForLinux |ローカルで Runbook を実行するために、[拡張機能ベースのユーザー Hybrid Runbook Worker をデプロイ](../../automation/extension-based-hybrid-runbook-worker-install.md)します。|

## <a name="prerequisites"></a>前提条件

この機能は、サブスクリプション内の次の Azure リソース プロバイダーに依存します。

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

まだ登録されていない場合は、「[Azure リソース プロバイダーを登録する](agent-overview.md#register-azure-resource-providers)」の手順に従ってください。

ネットワークまたはシステム要件があるかどうかを把握するために、前の表で参照されている各 VM 拡張機能のドキュメントを確認してください。 これにより、その VM 拡張機能に依存する Azure サービスまたは機能で接続の問題が発生しないようにすることができます。

### <a name="log-analytics-vm-extension"></a>Log Analytics VM 拡張機能

Linux 用の Log Analytics エージェント VM 拡張機能を使用するには、ターゲット マシンに Python 2.x がインストールされている必要があります。 

拡張機能をインストールする前に、[Log Analytics エージェントのデプロイ オプション](concept-log-analytics-extension-deployment.md)を確認して、使用可能なさまざまな方法と要件を満たす方法を理解してください。

### <a name="azure-key-vault-vm-extension"></a>Azure Key Vault VM 拡張機能

Key Vault VM 拡張機能では、次の Linux オペレーティング システムはサポートされていません。

- CentOS Linux 7 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

Azure Key Vault VM 拡張機能のデプロイは、以下を使用した場合にのみサポートされます。

- Azure CLI
- Azure PowerShell
- Azure Resource Manager テンプレート

機能拡張をデプロイする前に、次の作業を完了する必要があります。

1. [コンテナーと証明書 (自己署名証明書またはインポート証明書) を作成](../../key-vault/certificates/quick-create-portal.md)します。

2. Azure Arc 対応サーバーに証明書シークレットへのアクセスを許可します。 [RBAC プレビュー](../../key-vault/general/rbac-guide.md)を使用する場合は、Azure Arc リソースの名前を検索し、それを **Key Vault Secrets User (プレビュー)** ロールに割り当てます。 [Key Vault アクセス ポリシー](../../key-vault/general/assign-access-policy-portal.md)を使用する場合は、Azure Arc リソースのシステム割り当て ID にシークレットの **Get** アクセス許可を割り当てます。

### <a name="connected-machine-agent"></a>Connected Machine エージェント

お使いのマシンが、Azure Connected Machine エージェントに対する Windows および Linux オペレーティング システムの[サポートされているバージョン](agent-overview.md#supported-operating-systems)と一致することを確認します。

Windows および Linux のこの機能でサポートされる Connected Machine エージェントの最小バージョンは 1.0 リリースです。

マシンを必要なエージェントのバージョンにアップグレードするには、「[エージェントのアップグレード](manage-agent.md#upgrading-agent)」を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure CLI](manage-vm-extensions-cli.md)、[Azure PowerShell](manage-vm-extensions-powershell.md)、[Azure portal](manage-vm-extensions-portal.md)、または [Azure Resource Manager テンプレート](manage-vm-extensions-template.md)を使用して、VM 拡張機能をデプロイ、管理、および削除できます。
