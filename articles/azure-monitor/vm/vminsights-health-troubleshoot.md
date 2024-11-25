---
title: VM 分析情報のゲストの正常性のトラブルシューティング (プレビュー)
description: VM 分析情報の正常性に関する問題があるときに実行できるトラブルシューティング手順について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/25/2021
ms.openlocfilehash: 13de7b055820ac680e8b513198224bcfd6421e11
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129708859"
---
# <a name="troubleshoot-vm-insights-guest-health-preview"></a>VM 分析情報のゲストの正常性のトラブルシューティング (プレビュー)
この記事では、VM 分析情報の正常性に関する問題があるときに実行できるトラブルシューティング手順について説明します。

## <a name="installation-errors"></a>インストール エラー
以下に示したどの解決策でもインストールの問題が解決しない場合は、`/var/log/azure/Microsoft.Azure.Monitor.VirtualMachines.GuestHealthLinuxAgent/*.log` にある VM Health エージェント ログを収集して、Microsoft に詳しい調査を依頼してください。

### <a name="error-message-showing-db5-error"></a>db5 エラーを示すエラー メッセージ
インストールが成功せず、次のようなインストール エラー メッセージが表示されます。

```
script execution exit with error: error: db5 error(5) from dbenv->open: Input/output error
error: cannot open Packages index using db5 - Input/output error (5)
error: cannot open Packages database in /var/lib/rpm
error: db5 error(5) from dbenv->open: Input/output error
error: cannot open Packages database in /var/lib/rpm
```
原因は、パッケージ マネージャーの rpm データベースの破損です。「[RPM データベース復旧](https://rpm.org/user_doc/db_recovery.html)」のガイダンスに従って復旧を試みてください。 rpm データベースが復旧してから、もう一度インストールを試みます。

### <a name="init-file-already-exist-error"></a>Init ファイルが既に存在するというエラー
インストールが成功せず、次のようなインストール エラー メッセージが表示されます。

```
Exiting with the following error: "Failed to install VM Guest Health Agent: Init already exists: /etc/systemd/system/vmGuestHealthAgent.service"install vmGuestHealthAgent service execution failed with exit code 37
```

VM Health エージェントは、既存のサービスをアンインストールしたうえで最新のバージョンをインストールします。 おそらく、以前のサービス ファイルが何かの理由でクリーンアップされなかったことがエラーの原因と考えられます。 VM にログインし、次のコマンドで既存のサービス ファイルをバックアップしてから再インストールしてください。

```
sudo mv /etc/systemd/system/vmGuestHealthAgent.service  /etc/systemd/system/vmGuestHealthAgent.service.bak
```

インストールが成功した場合は、次のコマンドを実行してバックアップ ファイルを削除します。

```
sudo rm /etc/systemd/system/vmGuestHealthAgent.service.bak
```

### <a name="installation-failed-to-exit-code-37"></a>終了コード 37 でインストールが失敗する
インストールが成功せず、次のようなインストール エラー メッセージが表示されます。 

```
Exiting with the following error: "Failed to install VM Guest Health Agent: exit status 1"install vmGuestHealthAgent service execution failed with exit code 37
```
おそらく、VM ゲスト エージェントがサービス ファイルのロックを取得できなかったことが原因と考えられます。 VM を再起動して、ロックの解放を試みてください。


## <a name="upgrade-errors"></a>アップグレード エラー

### <a name="upgrade-available-message-is-still-displayed-after-upgrading-guest-health"></a>ゲストの正常性をアップグレードした後も、アップグレードを利用可能のメッセージが表示される 

- VM がグローバル Azure で実行されていることを確認します。 Azure Arc 対応サーバーは、まだサポートされていません。
- [Azure Monitor for VMs ゲストの正常性で監視を有効にする (プレビュー)](vminsights-health-enable.md) に関するページの説明に従って、仮想マシンのリージョンとオペレーティング システムのバージョンがサポートされていることを確認します。
- ゲストの正常性の拡張機能が 0 終了コードで正常にインストールされていることを確認します。
- Azure Monitor エージェント拡張機能が正常にインストールされていることを確認します。
- システムによって割り当てられたマネージド ID が仮想マシンで有効になっていることを確認します。
- ユーザーによって割り当てられたマネージド ID が仮想マシンに指定されていないことを確認します。
- Windows 仮想マシンのロケールが *英語 (米国)* になっていることを確認します。 Azure Monitor エージェントでは、ローカリゼーションは現在サポートされていません。
- 仮想マシンがネットワーク プロキシを使用していないことを確認します。 Azure Monitor エージェントでは、現在プロキシをサポートしていません。
- 正常性の拡張機能エージェントがエラーなしで起動されたことを確認します。 エージェントを起動できない場合は、エージェントの状態が破損している可能性があります。 エージェントの状態フォルダーの内容を削除し、エージェントを再起動します。
  - Linux の場合: デーモンは *vmGuestHealthAgent* です。 状態フォルダーは */var/opt/vmGuestHealthAgent/* * です。
  - Windows の場合: サービスは *VM ゲスト正常性エージェント* です。 状態フォルダーは _%ProgramData%\Microsoft\VMGuestHealthAgent\\*_ です。
- Azure Monitor エージェントがネットワークに接続されていることを確認します。 
  - 仮想マシンから、 _\<region\>.handler.control.monitor.azure.com_ に ping を試行します。 たとえば、westeurope の仮想マシンの場合は、_westeurope.handler.control.monitor.azure.com:443_ に ping を試行します。
- Log Analytics ワークスペースと同じリージョン内のデータ収集ルールとの関連付けが仮想マシンにあることを確認します。
  -  DCR の構造が正しいことを確認するには、[VM insights のゲストの正常性で監視を有効にする (プレビュー)](vminsights-health-enable.md) に関するページの **データ収集ルール (DCR) の作成** に関する項目を参照してください。 サンプルの 3 つのカウンターに設定された *performanceCounters* データ ソース セクションが存在し、カウンターを拡張機能に送信するための正常性の拡張機能の構成に *inputDataSources* セクションが存在していることに特に注意してください。
-  仮想マシンをチェックし、ゲストの正常性の拡張機能のエラーがないかを確認してください。
   -  Linux の場合: _/var/log/azure/Microsoft.Azure.Monitor.VirtualMachines.GuestHealthLinuxAgent/*.log_ でログを確認します。
   -  Windows の場合: _C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitor.VirtualMachines.GuestHealthWindowsAgent\{extension version}\*.log_ でログを確認します。
-  仮想マシンをチェックし、Azure Monitor エージェントのエラーがないかを確認します。
   -  Linux の場合: _/var/log/mdsd.*_ でログを確認します。
   -  Windows の場合: _C:\WindowsAzure\Resources\*{vmName}.AMADataStore_ でログを確認します。
 

## <a name="usage-errors"></a>使用上のエラー

### <a name="error-message-that-no-data-is-available"></a>使用できるデータがないというエラー メッセージ 

![データなし](media/vminsights-health-troubleshoot/no-data.png)


#### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>仮想マシンが構成要件を満たしていることを確認する

1. 仮想マシンが Azure 仮想マシンであることを確認します。 Azure Arc for servers は現在サポートされていません。
2. 仮想マシンで、[サポートされているオペレーティング システム](vminsights-health-enable.md?current-limitations.md)が実行中であることを確認します。
3. 仮想マシンが、[サポートされているリージョン](vminsights-health-enable.md?current-limitations.md)にインストールされていることを確認します。
4. Log Analytics ワークスペースが、[サポートされているリージョン](vminsights-health-enable.md?current-limitations.md)にインストールされていることを確認します。

#### <a name="verify-that-the-vm-is-properly-onboarded"></a>VM が適切にオンボードされていることを確認する
Azure Monitor エージェント拡張機能とゲスト VM 正常性エージェントが、仮想マシンに正常にプロビジョニングされていることを確認します。 Azure portal の仮想マシンのメニューから **[拡張機能]** を選択し、2 つのエージェントが一覧表示されていることを確認します。

![VM 拡張機能](media/vminsights-health-troubleshoot/extensions.png)

#### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>仮想マシンでシステム割り当て ID が有効になっていることを確認する
仮想マシンでシステム割り当て ID が有効になっていることを確認します。 Azure portal で、仮想マシンのメニューから **[ID]** を選択します。 ユーザー マネージド IDが有効な場合、システム マネージド ID の状態に関係なく、Azure Monitor エージェントは構成サービスと通信できないため、ゲストの正常性拡張機能は機能しません。

![システム割り当て ID](media/vminsights-health-troubleshoot/system-identity.png)

#### <a name="verify-data-collection-rule"></a>データ収集ルールを確認する
データ ソースとして正常性拡張機能を指定するデータ収集ルールが、仮想マシンに関連付けられていることを確認します。

### <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>アクセス許可が十分でないため無効な要求であることを示すエラー メッセージ
このエラーは、**Microsoft.WorkloadMonitor** リソース プロバイダーがサブスクリプションに登録されていないことを示しています。 このリソース プロバイダーの登録の詳細については、「[Azure リソース プロバイダーと種類](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)」を参照してください。 

![正しくない要求](media/vminsights-health-troubleshoot/bad-request.png)

### <a name="health-shows-as-unknown-after-guest-health-is-enabled"></a>正常性は、ゲストの正常性が有効になった後、"不明" と表示されます。

#### <a name="verify-that-performance-counters-on-windows-nodes-are-working-correctly"></a>Windows ノードのパフォーマンス カウンターが正しく機能していることを確認する 
ゲストの正常性は、エージェントがノードからパフォーマンス カウンターを収集できることに依存します。 パフォーマンス カウンター ライブラリの基本セットが破損し、再構築が必要になる場合があります。 「[パフォーマンス カウンター ライブラリの値を手動で再構築する](/troubleshoot/windows-server/performance/rebuild-performance-counter-library-values)」の手順に従って、パフォーマンス カウンターを再構築します。





## <a name="next-steps"></a>次のステップ

- [VM 分析情報のゲストの正常性の概要を確認する](vminsights-health-overview.md)
