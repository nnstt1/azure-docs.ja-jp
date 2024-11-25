---
title: SQL Server 2016/2017 Azure VM の自動バックアップ v2 | Microsoft Docs
description: この記事では、Azure 上で実行されている SQL Server 2016/2017 VM の自動バックアップ機能について説明します。 この記事は、Resource Manager を使用する VM のみにあてはまります。
services: virtual-machines-windows
documentationcenter: na
author: bluefooted
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: pamela
ms.reviewer: mathoma
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5910a432f2dce0afe43506fe8d66cc2c0f09a599
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550705"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>Azure Virtual Machines の自動バックアップ v2 (Resource Manager)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [SQL Server 2014](automated-backup-sql-2014.md)
> * [SQL Server 2016 以降](automated-backup.md)

自動バックアップ v2 では、SQL Server 2016 以降の Standard、Enterprise、または Developer エディションを実行している Azure VM 上のすべての既存および新規データベースのための [Microsoft Azure へのマネージド バックアップ](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)が自動的に構成されます。 これにより、永続的な Azure BLOB ストレージを利用した日常的なデータベース バックアップを構成できます。 自動バックアップ v2 は、[SQL Server IaaS (サービスとしてのインフラストラクチャ) Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に依存します。

## <a name="prerequisites"></a>前提条件
自動バックアップ v2 を使用するには、次の前提条件を参照してください。

**[オペレーティング システム]** :

- Windows Server 2012 R2 以降

**SQL Server バージョン/エディション**:

- SQL Server 2016 以降:Developer、Standard、または Enterprise

> [!NOTE]
> SQL Server 2014 については、[SQL Server 2014 の自動バックアップ](automated-backup-sql-2014.md)に関するページを参照してください。

**データベースの構成**:

- ターゲット "_ユーザー_" データベースでは、完全復旧モデルを使用する必要があります。 システム データベースでは、完全復旧モデルを使用する必要はありません。 しかし、モデルまたは MSDB のログのバックアップの作成を必要とする場合は、完全復旧モデルを使用する必要があります。 バックアップに対する完全復旧モデルの影響の詳細については、「[完全復旧モデルでのバックアップ](/previous-versions/sql/sql-server-2008-r2/ms190217(v=sql.105))」を参照してください。 
- SQL Server VM が SQL IaaS Agent 拡張機能に[フル管理モード](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full)で登録されています。 
-  自動バックアップは、フル [SQL Server IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に依存します。 そのため、自動バックアップは、既定のインスタンスのターゲット データベース、または単一の名前付きインスタンスでのみサポートされます。 既定のインスタンスがなく、複数の名前付きインスタンスがある場合、SQL IaaS 拡張機能は失敗し、自動バックアップは機能しません。 

## <a name="settings"></a>設定
自動バックアップ v2 で構成できるオプションを次の表に示します。 実際の構成手順は、Azure ポータルと Azure Windows PowerShell コマンドのどちらを使用するかによって異なります。

### <a name="basic-settings"></a>基本設定

| 設定 | 範囲 (既定値) | 説明 |
| --- | --- | --- |
| **自動化されたバックアップ** | 有効/無効 (無効) | SQL Server 2016/2017 Developer、Standard、または Enterprise を実行している Azure VM の自動バックアップを有効または無効にします。 |
| **保有期間** | 1 ～ 30 日 (30 日) | バックアップを保持する日数。 |
| **ストレージ アカウント** | Azure ストレージ アカウント | 自動バックアップのファイルを BLOB ストレージに保存するために使用する Azure ストレージ アカウント。 この場所にコンテナーが作成され、すべてのバックアップ ファイルが保存されます。 バックアップ ファイルの名前付け規則には、日付、時刻、およびデータベース GUID が含まれます。 |
| **暗号化** |有効/無効 (無効) | 暗号化を有効または無効にします。 暗号化を有効にすると、バックアップの復元に使用する証明書は、指定されたストレージ アカウントに配置されます。 それには、同じ **自動バックアップ** コンテナーと、同じ名前付け規則が使われます。 パスワードが変更された場合、そのパスワードを使用して新しい証明書が生成されますが、以前のバックアップの復元には古い証明書が引き続き使用されます。 |
| **パスワード** |パスワード テキスト | 暗号化キーのパスワード。 このパスワードは、暗号化を有効にした場合にのみ必須となります。 暗号化されたバックアップを復元するには、バックアップの作成時に使用した正しいパスワードおよび関連する証明書が必要です。 |

### <a name="advanced-settings"></a>詳細設定

| 設定 | 範囲 (既定値) | 説明 |
| --- | --- | --- |
| **システム データベースのバックアップ** | 有効/無効 (無効) | 有効にすると、この機能によりシステム データベース (マスター、MSDB、およびモデル) もバックアップされます。 ログのバックアップを作成する場合は、MSDB とモデル データベースが完全復旧モードであることを確認します。 マスターのログのバックアップは作成されません。 また TempDB のバックアップは一切作成されません。 |
| **バックアップ スケジュール** | 手動/自動 (自動) | 既定では、バックアップ スケジュールはログの増加に基づいて自動的に決定されます。 手動のバックアップ スケジュールでは、ユーザーはバックアップの時間枠を指定することができます。 この場合、バックアップは指定された頻度で、特定の日の指定された時間枠内でのみ行われます。 |
| **完全バックアップの頻度** | 毎日/毎週 | 完全バックアップの頻度。 どちらの場合も、完全バックアップは次のスケジュールされた時間枠内に開始されます。 毎週を選択すると、すべてのデータベースが正常にバックアップされるまで、バックアップは数日にわたることがあります。 |
| **完全バックアップの開始時刻** | 00:00 – 23:00 (01:00) | 完全バックアップが行われる日の開始時刻。 |
| **完全バックアップの時間枠** | 1 – 23 時間 (1 時間) | 完全バックアップが行われる日の時間枠。 |
| **ログのバックアップの頻度** | 5 – 60 分 (60 分) | ログのバックアップの頻度。 |

## <a name="understanding-full-backup-frequency"></a>完全バックアップの頻度を理解する
毎日および毎週の完全バックアップの違いについて理解することは重要です。 ここでは、次の 2 つのシナリオを例に説明します。

### <a name="scenario-1-weekly-backups"></a>シナリオ 1:毎週のバックアップ
いくつかの大規模なデータベースを含む SQL Server VM が存在します。

月曜日に、次の設定で自動バックアップ v2 を有効にします。

- バックアップ スケジュール: **手動**
- 完全バックアップの頻度: **毎週**
- 完全バックアップの開始時刻: **01:00**
- 完全バックアップの時間枠: **1 時間**

これは、次の利用可能なバックアップの枠が火曜日の午前 1 時からの 1 時間であることを意味します。 その時点で、自動バックアップはデータベースを 1 つずつバックアップすることを開始します。 このシナリオでは、データベースが大規模なため、完全バックアップでは最初のいくつかのデータベースのバックアップが完了します。 ただし、1 時間後にすべてのデータベースがバックアップされたわけではありません。

この場合、自動バックアップによって、次の日、つまり水曜日の午前 1 時からの 1 時間で、残りのデータベースがバックアップされます。 この時にバックアップされていないデータベースがある場合は、次の日の同じ時刻にもう一度バックアップが試みられます。 これは、すべてのデータベースが正常にバックアップされるまで続きます。

翌週の火曜日になると、自動バックアップは再びすべてのデータベースのバックアップを開始します。

このシナリオは、自動バックアップが指定された時間枠内でのみ動作し、各データベースは 週 1 回バックアップされることを示しています。 また、すべてのバックアップを 1 日で完了できない場合は、バックアップが数日にまたがる可能性があることを示しています。

### <a name="scenario-2-daily-backups"></a>シナリオ 2: 毎日のバックアップ
いくつかの大規模なデータベースを含む SQL Server VM が存在します。

月曜日に、次の設定で自動バックアップ v2 を有効にします。

- バックアップ スケジュール: マニュアル
- 完全バックアップの頻度: 毎日
- 完全バックアップの開始時刻: 22:00
- 完全バックアップの時間枠: 6 時間

これは、次の利用可能なバックアップの枠が月曜日の午後 10 時からの 6 時間であることを意味します。 その時点で、自動バックアップはデータベースを 1 つずつバックアップすることを開始します。

その後、火曜日の午後 10 時からの 6 時間、すべてのデータベースの完全バックアップが再び開始されます。


> [!IMPORTANT]
> バックアップは各間隔で順番に行われます。 多数のデータベースがあるインスタンスの場合は、すべてのバックアップに対応する十分な時間でバックアップ間隔をスケジュールします。 指定された間隔内にバックアップを完了できない場合、一部のバックアップがスキップされ、1 つのデータベースのバックアップ間の時間が構成されたバックアップ間隔時間より長くなり、回復ポイントの目標 (RPO) に悪影響を与える可能性があります。 

## <a name="configure-new-vms"></a>新しい VM を構成する

Resource Manager デプロイ モデルで新しい SQL Server 2016 または 2017 仮想マシンを作成する場合は、Azure portal を使用して自動バックアップ v2 を構成します。

**[SQL Server の設定]** タブで、 **[自動バックアップ]** の **[有効にする]** を選択します。 次の Azure Portal のスクリーンショットは、**SQL Automated Backup** の設定を示しています。

![Azure portal での SQL 自動バックアップ構成](./media/automated-backup/automated-backup-blade.png)

> [!NOTE]
> 既定では、自動バックアップ v2 は無効です。

## <a name="configure-existing-vms"></a>既存の VM を構成する

既存の SQL Server 仮想マシンの場合、[SQL 仮想マシン リソース](manage-sql-vm-portal.md#access-the-resource)に移動してから **[バックアップ]** を選択し、自動バックアップを構成します。

![既存の VM の SQL 自動バックアップ](./media/automated-backup/sql-server-configuration.png)


完了したら、 **[バックアップ]** 設定ページの下にある **[適用]** ボタンをクリックして、変更内容を保存します。

自動バックアップを初めて有効にすると、バックグラウンドで SQL Server IaaS エージェントが構成されます。 この間、自動バックアップが構成されていることは、Azure ポータルに示されない可能性があります。 エージェントがインストールされ、構成されるまで数分待ちます。 その後、Azure ポータルに新しい設定が反映されます。

## <a name="configure-with-powershell"></a>PowerShell での構成

PowerShell を使用して自動バックアップ v2 を構成できます。 開始する前に、次の操作を行う必要があります。

- [最新の Azure PowerShell をダウンロードしてインストールします](https://aka.ms/webpi-azps)。
- Windows PowerShell を開き、**Connect-AzAccount** コマンドを使用してそれをアカウントに関連付けます。

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-server-iaas-extension"></a>SQL Server IaaS 拡張機能のインストール
SQL Server 仮想マシンを Azure Portal からプロビジョニングした場合は、SQL Server IaaS 拡張機能は既にインストールされています。 これがお使いの仮想マシンにインストールされているかどうかは、**Get-AzVM** コマンドを呼び出し、**Extensions** プロパティを調べることで判断できます。

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions 
```

SQL Server IaaS Agent 拡張機能がインストールされている場合、それは "SqlIaaSAgent" または "SQLIaaSExtension" と表示されます。 また、拡張機能の **ProvisioningState** も "Succeeded" と表示されるはずです。 

インストールされていない場合、またはプロビジョニングに失敗した場合は、次のコマンドを使ってインストールできます。 VM 名とリソース グループのほかに、VM が配置されているリージョン ( **$region**) を指定する必要があります。

```powershell
$region = "EASTUS2"
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region 
```

### <a name="verify-current-settings"></a><a id="verifysettings"></a> 現在の設定の確認
プロビジョニング中に自動バックアップを有効にした場合は、PowerShell を使って現在の構成を確認することができます。 **Get-AzVMSqlServerExtension** コマンドを実行して **AutoBackupSettings** プロパティを調べます。

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

次のような出力が表示されます。

```
Enable                      : True
EnableEncryption            : False
RetentionPeriod             : 30
StorageUrl                  : https://test.blob.core.windows.net/
StorageAccessKey            :  
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : Manual
FullBackupFrequency         : WEEKLY
FullBackupStartTime         : 2
FullBackupWindowHours       : 2
LogBackupFrequency          : 60
```

出力で **Enable** が **False** に設定されていることが示された場合は、自動バックアップを有効にする必要があります。 自動バックアップは同じ方法で有効にし、構成できます。 詳細については、次のセクションを参照してください。

> [!NOTE] 
> 変更直後に設定を確認すると、以前の構成値が表示されることがあります。 変更が適用されていることを確認するには、数分経ってから設定を再び確認します。

### <a name="configure-automated-backup-v2"></a>自動バックアップ v2 の構成
PowerShell を使用すると、自動バックアップを有効にできるほか、自動バックアップの構成や動作をいつでも変更することができます。 

最初に、バックアップ ファイル用のストレージ アカウントを選択または作成します。 次のスクリプトでは、ストレージ アカウントを選択し、ストレージ アカウントが存在しない場合は作成します。

```powershell
$storage_accountname = "yourstorageaccount"
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region } 
```

> [!NOTE]
> 自動バックアップでは Premium Storage でのバックアップの保存をサポートしていませんが、Premium Storage を使用する VM ディスクからバックアップを取ることができます。

次に **New-AzVMSqlServerAutoBackupConfig** コマンドを使って、Azure ストレージ アカウントにバックアップを保存するように、自動バックアップ v2 を有効にして設定を構成します。 この例では、バックアップは 10 日間保持されるよう設定されています。 システム データベースのバックアップが有効になっています。 完全バックアップは、毎週 20:00 からの 2 時間でスケジュールされています。 ログのバックアップは 30 分ごとにスケジュールされています。 2 番目のコマンド **Set-AzVMSqlServerExtension** は、指定された Azure VM をこれらの設定で更新します。

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname 
```

SQL Server IaaS エージェントのインストールと構成には数分かかる場合があります。 

暗号化を有効にするには、**EnableEncryption** パラメーターと、**CertificatePassword** パラメーターのパスワード (セキュリティで保護された文字列) を渡すように、前のスクリプトを変更します。 次のスクリプトでは、前の例の自動バックアップ設定を有効にし、暗号化を追加します。

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

設定が適用されたことを確認するには、[自動バックアップの構成を確認します](#verifysettings)。

### <a name="disable-automated-backup"></a>自動バックアップを無効にする
自動バックアップを無効にするには、**New-AzVMSqlServerAutoBackupConfig** コマンドの **-Enable** パラメーターを指定せずに、同じスクリプトを実行します。 **-Enable** パラメーターがない場合は、機能を無効にするコマンドが伝えられます。 インストールと同様に、自動バックアップの無効化には数分かかる場合があります。

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>サンプル スクリプト
次のスクリプトでは、お使いの VM で自動バックアップを有効にして構成するようカスタマイズできる変数のセットを提供しています。 場合によっては、要件に基づきスクリプトをカスタマイズする必要があるかもしれません。 たとえば、システム データベースのバックアップを無効にしたり、暗号化を有効にする場合は変更が必要です。

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = "Azure region name such as EASTUS2"
$storage_accountname = "storageaccountname"
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL Server IaaS Extension 

Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>監視

SQL Server 2016/2017 上で自動バックアップを監視するには、主なオプションが 2 つあります。 自動バックアップでは SQL Server マネージド バックアップ機能を使用するため、この両方に同じ監視手法が適用されます。

まず、[msdb.managed_backup.sp_get_backup_diagnostics](/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql) を呼び出すことによって状態をポーリングできます。 または、[msdb.managed_backup.fn_get_health_status](/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) テーブル値関数のクエリを実行します。

もう 1 つのオプションは、通知に組み込みのデータベース メール機能を利用する方法です。

1. [msdb.managed_backup.sp_set_parameter](/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) ストアド プロシージャを呼び出して、**SSMBackup2WANotificationEmailIds** パラメーターにメール アドレスを割り当てます。 
1. [SendGrid](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#create-a-twilio-sendgrid-accountcreate-a-twilio-sendgrid-account) が Azure VM から電子メールを送信できるようにします。
1. SMTP サーバーとユーザー名を使用してデータベース メールを構成します。 データベース メールは、SQL Server Management Studio または Transact-SQL コマンドで構成できます。 詳細については、「[データベース メール](/sql/relational-databases/database-mail/database-mail)」を参照してください。
1. [データベース メールを使用するように SQL Server エージェントを構成します](/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail)。
1. SMTP ポートがローカルの VM ファイアウォールと、その VM のネットワーク セキュリティ グループの両方で許可されていることを確認します。

## <a name="next-steps"></a>次のステップ
自動バックアップ v2 では、Azure VM でマネージド バックアップが構成されます。 そのため、 [マネージド バックアップに関するドキュメント](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure) を見直して、動作と影響を理解することが重要です。

Azure VM の SQL Server のバックアップと復元に関するその他のガイダンスについては、[Azure Virtual Machines での SQL Server のバックアップと復元](backup-restore.md)に関する記事を参照してください。

その他の利用可能なオートメーション タスクについては、 [SQL Server IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に関するページをご覧ください。

Azure VM で SQL Server を実行する方法の詳細については、[Azure 仮想マシンにおける SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)に関するページをご覧ください。
