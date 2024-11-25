---
title: PowerShell を使用して DPM ワークロードをバックアップする
description: PowerShell を使用して、Data Protection Manager (DPM) 用に Microsoft Azure Backup をデプロイおよび管理する手順の説明
ms.topic: conceptual
ms.date: 01/23/2017
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 04906bcad3bb3f3362e92429d44f9775bd21d40f
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110672204"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>PowerShell を使用して Data Protection Manager (DPM) サーバーに Microsoft Azure Backup をデプロイおよび管理する手順

この記事では、PowerShell を使用して、DPM サーバー上に Microsoft Azure Backup をセットアップし、バックアップと回復を管理する方法を示します。

## <a name="setting-up-the-powershell-environment"></a>PowerShell 環境のセットアップ

PowerShell を使用して Data Protection Manager から Azure へのバックアップを管理する前に、PowerShell の環境が整っている必要があります。 PowerShell セッションの開始時に、必ず次のコマンドレットを実行して適切なモジュールをインポートし、DPM コマンドレットを適切に参照できるようにしてください。

```powershell
& "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"
```

```Output
Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>セットアップと登録

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

開始するには、[最新の Azure PowerShell をダウンロード](/powershell/azure/install-az-ps)します。

PowerShell を使用して次のセットアップおよび登録タスクを自動化できます。

* Recovery Services コンテナーを作成する
* Microsoft Azure Backup エージェントのインストール
* Microsoft Azure Backup サービスへの登録
* ネットワークの設定
* 暗号化の設定

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

次の手順では、Recovery Services コンテナーの作成について説明します。 Recovery Services コンテナーは Backup コンテナーとは異なります。

1. Azure Backup を初めて使用する場合、**Register-AzResourceProvider** コマンドレットを使って Azure Recovery Services プロバイダーをサブスクリプションに登録する必要があります。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Recovery Services コンテナーは ARM リソースであるため、リソース グループ内に配置する必要があります。 既存のリソース グループを使用することも、新しいリソース グループを作成することもできます。 新しいリソース グループを作成する場合、リソース グループの名前と場所を指定します。

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "West US"
    ```

3. **New-AzRecoveryServicesVault** コマンドレットを使用して、新しいコンテナーを作成します。 リソース グループに使用したのと同じコンテナーの場所を指定してください。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. 使用するストレージ冗長性の種類を指定します。 [ローカル冗長ストレージ (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage)、[geo 冗長ストレージ (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)、または [ゾーン冗長ストレージ (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) を使用できます。 次に示す例では、*testVault* の **-BackupStorageRedundancy** オプションが **GeoRedundant** に設定されています。

   > [!TIP]
   > Azure Backup コマンドレットの多くは、入力として Recovery Services コンテナー オブジェクトを必要としています。 このため、Backup Recovery Services コンテナー オブジェクトを変数に格納すると便利です。
   >
   >

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>サブスクリプション内のコンテナーの表示

**Get-AzRecoveryServicesVault** を使用して、現在のサブスクリプション内にあるすべてのコンテナーを一覧表示します。 このコマンドは、新しく作成したコンテナーを確認したり、サブスクリプション内の利用可能なコンテナーを確認したりするのに使用できます。

Get-AzRecoveryServicesVault コマンドを実行すると、サブスクリプション内にあるすべてのコンテナーが一覧表示されます。

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>DPM サーバーへの Microsoft Azure Backup エージェントのインストール

Microsoft Azure Backup エージェントをインストールする前に、Windows Server に、インストーラーをダウンロードする必要があります。 最新バージョンのインストーラーは、 [Microsoft ダウンロード センター](https://aka.ms/azurebackup_agent) または Recovery Services コンテナーの [ダッシュボード] ページから入手することができます。 インストーラーを、`C:\Downloads\*` などの、簡単にアクセスできる場所に保存します。

エージェントをインストールするには、 **DPM サーバー** の管理者特権の PowerShell コンソールで、次のコマンドを実行します。

```powershell
MARSAgentInstaller.exe /q
```

これにより、エージェントはすべて既定のオプションが指定されてインストールされます。 インストールは、バックグラウンドで数分かかります。 */nu* オプションを指定しない場合、インストールの最後に **[Windows Update]** ウィンドウが開き、更新プログラムが確認されます。

エージェントが、インストールされているプログラムの一覧に表示されます。 インストールされているプログラムの一覧を表示するには、 **[コントロール パネル]**  >  **[プログラム]**  >  **[プログラムと機能]** に移動します。

![インストールされているエージェント](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>インストール オプション

コマンド ラインで使用できるすべてのオプションを表示するには、次のコマンドを使用します。

```powershell
MARSAgentInstaller.exe /?
```

利用可能なオプションは、次のとおりです。

| オプション | 詳細 | Default |
| --- | --- | --- |
| /q |サイレント インストール |- |
| /p:"location" |Azure Backup エージェントのインストール フォルダーへのパス |C:\Program Files\Microsoft Azure Recovery Services Agent |
| /s:"location" |Azure Backup エージェントのキャッシュ フォルダーへのパス |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Microsoft Update へのオプトイン |- |
| /nu |インストールが完了するまで更新プログラムを確認しない |- |
| /d |Microsoft Azure Recovery Services エージェントをアンインストールする |- |
| /ph |プロキシ ホストのアドレス |- |
| /po |プロキシ ホストのポート番号 |- |
| /pu |プロキシ ホストのユーザー名 |- |
| /pw |プロキシ パスワード |- |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Recovery Services コンテナーへの DPM の登録

Recovery Services コンテナーを作成したら、最新のエージェントとコンテナーの資格情報をダウンロードし、C:\Downloads のような便利な場所に保管します。

```powershell
$credspath = "C:\downloads"
$credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
$credsfilename
```

```Output
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

DPM サーバーで [Start-OBRegistration](/powershell/module/msonlinebackup/start-obregistration) コマンドレットを実行し、コンピューターをコンテナーに登録します。

```powershell
$cred = $credspath + $credsfilename
Start-OBRegistration-VaultCredentials $cred -Confirm:$false
```

```Output
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>初期構成の設定

DPM サーバーが Recovery Services コンテナーに登録されると、既定のサブスクリプション設定で起動されます。 これらのサブスクリプションの設定には、ネットワーク、暗号化、ステージング領域が含まれます。 サブスクリプション設定を変更するには、まず既存 (既定) の設定のハンドルを [Get-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/get-dpmcloudsubscriptionsetting) コマンドレットを使用して取得する必要があります。

```powershell
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

このローカル PowerShell オブジェクト ```$setting``` にすべての変更が行われ、[Set-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting) コマンドレットを使用して保存するために、完全なオブジェクトが DPM および Azure Backup にコミットされます。 変更が保持されていることを確認するには、 ```–Commit``` フラグを使用します。 コミットしない限り、Azure Backup により設定が適用または使用されません。

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>ネットワーク

インターネット上の Azure Backup サービスへの DPM コンピューターの接続にプロキシ サーバーを使用している場合、バックアップを正しく行うには、プロキシ サーバーの設定を指定する必要があります。 これを行うには、```-ProxyServer```、```-ProxyPort```、```-ProxyUsername```、および ```ProxyPassword``` パラメーターと [Set-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting) コマンドレットを使用します。 この例では、プロキシ サーバーがないため、プロキシ関連のすべての情報を明示的にクリアしています。

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

特定の曜日セットに対して、帯域幅の使用を、```-WorkHourBandwidth```オプションと```-NonWorkHourBandwidth```オプションで制御することもできます。 この例ではスロットルは設定しません。

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>ステージング領域の構成

DPM サーバーで実行されている Microsoft Azure Backup エージェントには、クラウドから復元されるデータ用の一時的なストレージ (ローカル ステージング領域) が必要です。 [Set-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting) コマンドレットと ```-StagingAreaPath``` パラメーターを使用してステージング領域を構成します。

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

上の例では、ステージング領域は PowerShell オブジェクト ```$setting``` の *C:\StagingArea* に設定されます。 指定したフォルダーが既に存在することを確認します。存在しない場合は、サブスクリプション設定の最終コミットが失敗します。

### <a name="encryption-settings"></a>暗号化の設定

データの機密性を保護するために、Microsoft Azure Backup に送信されるバックアップ データは暗号化されます。 暗号化パスフレーズは、復元時にデータの暗号化を解除するための "パスワード" になります。 設定したら、この情報をセキュリティで保護された安全な場所に保管することが重要です。

次の例では、最初のコマンドは、文字列 ```passphrase123456789``` をセキュリティ保護された文字列に変換し、そのセキュリティ保護された文字列を ```$Passphrase``` という名前の変数に割り当てます。 次のコマンドは、セキュリティ保護された文字列を ```$Passphrase``` にバックアップ暗号化のパスワードとして設定します。

```powershell
$Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> 設定したら、パスフレーズ情報をセキュリティで保護された安全な場所に保管してください。 このパスフレーズがないと、Azure からデータを復元できません。
>
>

この時点で、必要な変更はすべて ```$setting``` オブジェクトに行われています。 変更を忘れずにコミットします。

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Microsoft Azure Backup へのデータの保護

このセクションでは、DPM に運用サーバーを追加した後、ローカル DPM ストレージへのデータ、次に Azure Backup へのデータを保護します。 この例では、ファイルとフォルダーをバックアップする方法を示します。 このロジックは、DPM がサポートされたすべてのデータ ソースのバックアップに簡単に拡張して適用できます。 すべての DPM バックアップは、4 つの部分で保護グループ (PG) によって制御されます。

1. **グループ メンバー** は、同じ保護グループ内で保護するすべての保護可能オブジェクト (DPM では *データソース* とも呼ばれます) の一覧です。 たとえば、運用 VM とテスト SQL Server データベースがそれぞれ異なるバックアップ要件を持つため、別々の PG で保護したいという場合があります。 運用サーバー上のデータソースをバックアップする前に、DPM エージェントがサーバーにインストールされており、DPM により管理されていることを確認する必要があります。 [DPM エージェントをインストールする](/system-center/dpm/deploy-dpm-protection-agent) 手順と、適切な DPM サーバーにリンクする手順に従います。
2. **データ保護方法** では、目的のバックアップ場所 (テープ、ディスク、クラウドなど) を指定します。 この例では、ローカル ディスクへのデータとクラウドへのデータを保護します。
3. **バックアップ スケジュール** は、バックアップを実行する必要があるタイミングと、DPM サーバーと運用サーバーの間でデータを同期すべき頻度を指定します。
4. **保持スケジュール** は、Azure で回復ポイントを保持する期間を指定します。

### <a name="creating-a-protection-group"></a>保護グループの作成

まず新しい PG を、 [New-DPMProtectionGroup](/powershell/module/dataprotectionmanager/new-dpmprotectiongroup) コマンドレットを使用して作成します。

```powershell
$PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

前記のコマンドレットは、 *ProtectGroup01* という名前の保護グループを作成します。 既存の保護グループを後で変更して、バックアップを Azure クラウドに追加することもできます。 ただし、新しいか既存かを問わず保護グループに変更を加えるには、 *Get-DPMModifiableProtectionGroup* コマンドレットを使って [変更可能な](/powershell/module/dataprotectionmanager/get-dpmmodifiableprotectiongroup) オブジェクトのハンドルを取得する必要があります。

```powershell
$MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>保護グループへのグループ メンバーの追加

各 DPM エージェントは、インストール先のサーバー上でデータソースの一覧を認識しています。 保護グループにデータソースを追加するには、DPM エージェントは最初にデータソースの一覧を DPM サーバーに送信する必要があります。 1 つ以上のデータソースが選択され、保護グループに追加されます。 このために必要な PowerShell の手順は、次のとおりです。

1. DPM エージェントから DPM が管理するすべてのサーバーの一覧を取得します。
2. 特定のサーバーの選択
3. サーバー上のすべてのデータソースの一覧を取得します。
4. 1 つ以上のデータソースの選択と保護グループへの追加

DPM エージェントがインストールされており、DPM サーバーによって管理されるサーバーの一覧を、 [Get-DPMProductionServer](/powershell/module/dataprotectionmanager/get-dpmproductionserver) コマンドレットを使用して取得します。 この例では、フィルター処理を行い、バックアップ用に *productionserver01* という名前を持つ PowerShell のみを構成します。

```powershell
$server = Get-ProductionServer -DPMServerName "TestingServer" | Where-Object {($_.servername) –contains "productionserver01"}
```

[Get-DPMDatasource](/powershell/module/dataprotectionmanager/get-dpmdatasource) コマンドレットを使用して ```$server``` のデータソースの一覧を取得します。 この例では、バックアップ用に構成するボリューム `D:\` をフィルター処理します。 次に、このデータソースを [Add-DPMChildDatasource](/powershell/module/dataprotectionmanager/add-dpmchilddatasource) コマンドレットを使用して保護グループに追加します。 "*変更可能な*" 保護グループ オブジェクト ```$MPG``` を使用して、忘れずに追加します。

```powershell
$DS = Get-Datasource -ProductionServer $server -Inquire | Where-Object { $_.Name -contains "D:\" }

Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

選択されたすべてのデータソースが保護グループに追加されるまで、この手順を必要な回数繰り返します。 1 つのデータソースで開始して、保護グループを作成するためのワークフローを完了し、後で保護グループにさらにデータソースを追加することもできます。

### <a name="selecting-the-data-protection-method"></a>データ保護方法の選択

データソースが保護グループに追加されたら、次の手順は、 [Set-DPMProtectionType](/powershell/module/dataprotectionmanager/set-dpmprotectiontype) コマンドレットを使用した保護方法の指定です。 この例では、保護グループをローカル ディスクとクラウド バックアップ用にセットアップします。 また、 [Add-DPMChildDatasource](/powershell/module/dataprotectionmanager/add-dpmchilddatasource) コマンドレットに -Online フラグを付けて、保護対象のデータソースをクラウドに指定する必要があります。

```powershell
Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>保有期間の範囲設定

[Set-DPMPolicyObjective](/powershell/module/dataprotectionmanager/set-dpmpolicyobjective) コマンドレットを使用してバックアップ ポイントの保有期間を設定します。 バックアップ スケジュールが定義される前に保有期間を設定するのは不自然な操作に思えるかもしれませんが、 ```Set-DPMPolicyObjective``` コマンドレットでは、後で変更できる既定のバックアップ スケジュールが自動的に設定されます。 バックアップ スケジュールを設定した後で、保有ポリシーを設定することも常に可能です。

次の例では、コマンドレットはディスク バックアップ用の保有期間パラメーターを設定します。 ここではバックアップの保有期間は 10 日間で、運用サーバーと DPM サーバーの間でデータが 6 時間ごとに同期されます。 ```SynchronizationFrequencyMinutes``` ではバックアップ ポイントの作成頻度は定義されませんが、DPM サーバーへのデータのコピー頻度は定義されます。  この設定により、バックアップの肥大化を回避できます。

```powershell
Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Azure へのバックアップ (DPM ではオンライン バックアップの意味) では、リテンション期間の範囲は [Grandfather-Father-Son スキーム (GFS) を使用して長期間保有](backup-azure-backup-cloud-as-tape.md)用に構成できます。 つまり、日次、週次、月次、年次の保有期間ポリシーを組み合わせた保有ポリシーを定義できます。 この例では、必要な複雑な保有期間スキームを表す配列を作成し、 [Set-DPMPolicyObjective](/powershell/module/dataprotectionmanager/set-dpmpolicyobjective) コマンドレットを使用して保有期間の範囲を構成します。

```powershell
$RRlist = @()
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>バックアップ スケジュールの設定

```Set-DPMPolicyObjective``` コマンドレットを使用して保護目標を指定する場合、DPM は既定のバックアップ スケジュールを自動的に設定します。 既定のスケジュールを変更するには、[Get-DPMPolicySchedule](/powershell/module/dataprotectionmanager/get-dpmpolicyschedule) コマンドレットと、それに続いて [Set-DPMPolicySchedule](/powershell/module/dataprotectionmanager/set-dpmpolicyschedule) コマンドレットを使用します。

```powershell
$onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

上記の例で ```$onlineSch``` は、GFS スキームの保護グループ用の既存のオンライン保護スケジュールを含む 4 つの要素を持つ配列です。

1. ```$onlineSch[0]``` は日次スケジュールを含みます
2. ```$onlineSch[1]``` は週次スケジュールを含みます
3. ```$onlineSch[2]``` は月次スケジュールを含みます
4. ```$onlineSch[3]``` は年次スケジュールを含みます

したがって、週次のスケジュールを変更する必要がある場合は、 ```$onlineSch[1]```を参照します。

### <a name="initial-backup"></a>初回バックアップ

データソースを初めてバックアップする場合、DPM レプリカ ボリューム上に保護対象データソースの完全なコピーを作成する初期レプリカを、DPM により作成する必要があります。 このアクティビティは特定の期間スケジュール設定することも、[Set-DPMReplicaCreationMethod](/powershell/module/dataprotectionmanager/set-dpmreplicacreationmethod) コマンドレットと ```-NOW``` パラメーターを使用して手動でトリガーすることもできます。

```powershell
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>DPM レプリカと回復ポイントのボリューム サイズの変更

DPM レプリカ ボリュームとシャドウ コピー ボリュームのサイズは、次の例に示すように、[Set-DPMDatasourceDiskAllocation](/powershell/module/dataprotectionmanager/set-dpmdatasourcediskallocation) コマンドレットを使用して変更することもできます。Get-DatasourceDiskAllocation -Datasource $DS Set-DatasourceDiskAllocation -Datasource $DS -ProtectionGroup $MPG -manual -ReplicaArea (2gb) -ShadowCopyArea (2gb)

### <a name="committing-the-changes-to-the-protection-group"></a>保護グループに対する変更のコミット

最後に、DPM が新しい保護グループの構成ごとにバックアップを実行する前に、変更をコミットする必要があります。 これを実行するには、 [Set-DPMProtectionGroup](/powershell/module/dataprotectionmanager/set-dpmprotectiongroup) コマンドレットを使用します。

```powershell
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## <a name="view-the-backup-points"></a>バックアップ ポイントの表示

データソースのすべての回復ポイントの一覧を取得するには、 [Get DPMRecoveryPoint](/powershell/module/dataprotectionmanager/get-dpmrecoverypoint) コマンドレットを使用します。 この例の内容:

* 配列 ```$PG```
* ```$PG[0]```
* データソースのすべての回復ポイントを取得します。

```powershell
$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Azure で保護されているデータの復元

データの復元は、```RecoverableItem``` オブジェクトと ```RecoveryOption``` オブジェクトの組み合わせです。 前のセクションで、データソースのバックアップ ポイントの一覧を取得しました。

次の例では、バックアップ ポイントと回復対象を組み合わせて、Hyper-V 仮想マシンを Microsoft Azure Backup から復元する方法を示します。 この例には次が含まれています。

* [New-DPMRecoveryOption](/powershell/module/dataprotectionmanager/new-dpmrecoveryoption) コマンドレットを使用した回復オプションの作成。
* ```Get-DPMRecoveryPoint``` コマンドレットを使用したバックアップ ポイントの配列の取得。
* 復元元のバックアップ ポイントの選択。

```powershell
$RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation "C:\VMRecovery"

$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

使用されているコマンドは、任意のデータソースの種類に合わせて簡単に拡張できます。

## <a name="next-steps"></a>次のステップ

* Azure Backup と DPM の詳細については、 [DPM バックアップの概要](backup-azure-dpm-introduction.md)
