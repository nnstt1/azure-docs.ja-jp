---
title: Azure Arc 対応サーバーをリージョン間で移行する方法
description: Azure Arc 対応サーバーをリージョン間で移行する方法について説明します。
ms.date: 07/16/2021
ms.topic: conceptual
ms.openlocfilehash: 5039ff2d00b83a8f93cf32caa27eee2032ee35dd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462047"
---
# <a name="how-to-migrate-azure-arc-enabled-servers-across-regions"></a>Azure Arc 対応サーバーをリージョン間で移行する方法

既存の Azure Arc 対応サーバーをリージョン間で移動する必要があるシナリオがあります。 たとえば、マシンが間違ったリージョンに登録されていることに気づいた場合や、管理のしやすさを向上させるため、またはガバナンス上の理由で移動するためなどです。

Azure Arc 対応サーバーをある Azure リージョンから別のものに移行するには、VM 拡張機能をアンインストールし、Azure 内のリソースを削除してから、もう一方のリージョン内で再作成する必要があります。 これらの手順を実行する前に、マシンを監査して、インストールされている VM 拡張機能を確認する必要があります。

> [!NOTE]
> インストール済みの拡張機能は引き続き実行され、この手順の完了後に通常の操作が実行されますが、管理することはできません。 マシンに拡張機能を再デプロイしようとすると、予期しない動作が発生することがあります。

## <a name="move-machine-to-other-region"></a>マシンを他のリージョンに移動する

> [!NOTE]
> この操作では、移行中にダウンタイムが発生します。

1. [Azure portal](manage-vm-extensions-portal.md#remove-extensions) から、または [Azure CLI](manage-vm-extensions-cli.md#remove-extensions) を使用するか [Azure PowerShell](manage-vm-extensions-powershell.md#remove-extensions) を使用して、インストールされた VM 拡張機能を削除します。

2. **azcmagent** ツールと [Disconnect](manage-agent.md#disconnect) パラメーターを使用して、Azure Arc からマシンを切断し、Azure からマシン リソースを削除します。 マシンを Azure Arc 対応サーバーから切断しても、Connected Machine エージェントは削除されず、このプロセスの一環としてエージェントを削除する必要はありません。 これは、対話形式でログオンしているときに手動で実行できます。または、複数のエージェントのオンボードに使用したのと同じサービス プリンシパルを使用するか、Microsoft ID プラットフォームの[アクセス トークン](../../active-directory/develop/access-tokens.md)を使用して自動化できます。 サービス プリンシパルを使用してマシンを Azure Arc 対応サーバーに登録していない場合は、次の[記事](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)を参照して、サービス プリンシパルを作成してください。

3. もう一方のリージョンで Azure Arc 対応サーバーに Connected Machine エージェントを再登録します。 [Connect](manage-agent.md#connect) パラメーターを指定して `azcmagent` ツールを実行し、この手順を完了します。

4. Azure Arc 対応サーバーからマシンに最初にデプロイされた VM 拡張機能を再デプロイします。 Azure Policy 定義を使用して Azure Monitor for VMs (分析情報) エージェントまたは Log Analytics エージェントをデプロイした場合、それらのエージェントは、次の[評価サイクル](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)の後に再デプロイされます。

## <a name="next-steps"></a>次のステップ

* トラブルシューティング情報は、[Connected Machine エージェントの問題解決ガイド](troubleshoot-agent-onboard.md)を参照してください。

* [Azure Policy](../../governance/policy/overview.md) を使用してマシンを管理する方法 (たとえば、VM の[ゲスト構成](../../governance/policy/concepts/guest-configuration.md)、予期された Log Analytics ワークスペースにマシンが報告していることの確認、[VM 分析情報](../../azure-monitor/vm/vminsights-enable-policy.md)ポリシーでの監視の有効化など) について説明します。
