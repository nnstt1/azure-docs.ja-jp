---
title: カスタマー マネージド キーを使用したバックアップ データの暗号化
description: Azure Backup でカスタマー マネージド キー (CMK) を使用してご自分のバックアップ データを暗号化できるようにする方法を説明します。
ms.topic: conceptual
ms.date: 08/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f16974d00f4801f288180814daf9ff5ed4558748
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778721"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>カスタマー マネージド キーを使用したバックアップ データの暗号化

Azure Backup を使用すると、既定で有効になっているプラットフォーム マネージド キーを使用する代わりに、カスタマー マネージド キー (CMK) を使用してバックアップ データを暗号化できます。 バックアップ データの暗号化に使用するキーは、[Azure Key Vault](../key-vault/index.yml) に格納する必要があります。

バックアップの暗号化に使用される暗号化キーは、ソースに使用されているものと異なることがあります。 データは、AES 256 ベースのデータ暗号化キー (DEK) を使用して保護され、このキーはご自身のキー (KEK) を使用して保護されます。 これにより、データとキーを完全に制御できます。 暗号化を許可するには、Recovery Services コンテナーに、Azure Key Vault の暗号化キーへのアクセスが許可されている必要があります。 必要に応じて、キーを変更できます。

この記事では、以下について説明します。

- Recovery Services コンテナーの作成
- カスタマー マネージド キーを使用してバックアップ データを暗号化するための Recovery Services コンテナーの構成
- カスタマー マネージド キーを使用して暗号化されたコンテナーへのバックアップの実行
- バックアップからのデータの復元

## <a name="before-you-start"></a>開始する前に

- この機能で暗号化できるのは、**新しい Recovery Services コンテナーのみ** です。 登録済みの既存項目を含むコンテナーや、そのようなコンテナーへの登録試行はサポートされていません。

- いったん Recovery Services コンテナーに対してカスタマー マネージド キーによる暗号化を有効にすると、プラットフォーム マネージド キー (既定) を使用するように戻すことができなくなります。 暗号化キーは、実際の要件に応じて変更することができます。

- 現在、この機能では、**MARS エージェントを使用したバックアップがサポートされていません**。また、CMK で暗号化されたコンテナーを同じ目的に使用することはできません。 MARS エージェントでは、ユーザーのパスフレーズベースの暗号化を使用します。 この機能では、クラシック VM のバックアップもサポートされていません。

- この機能は [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) とは関係がありません。こちらでは、BitLocker (Windows の場合) と DM-Crypt (Linux の場合) を使った、VM のディスクに対するゲストベースの暗号化を使用します

- Recovery Services コンテナーは、**同じリージョン** にある Azure キー コンテナーに格納されているキーを使用した場合にのみ暗号化できます。 また、キーは **RSA キー** に限定され、**有効** 状態である必要があります。

- 現在、CMK で暗号化された Recovery Services コンテナーをリソース グループとサブスクリプションの間で移動することは、サポートされていません。
- カスタマーマネージド キーを使用して暗号化された Recovery Services コンテナーでは、バックアップ インスタンスのリージョンをまたがる復元はサポートされません。
- 既にカスタマー マネージド キーを使用して暗号化された Recovery Services コンテナーを新しいテナントに移動する場合、Recovery Services コンテナーを更新して、コンテナーのマネージド ID および CMK (新しいテナント内にあるはずです) を再作成および再構成する必要があります。 これを実行しないと、バックアップおよび復元操作が失敗するようになります。 また、サブスクリプション内に設定されているすべてのロールベースのアクセス制御 (RBAC) アクセス許可も再構成する必要があります。

- この機能は、Azure portal と PowerShell を使用して構成できます。

    >[!NOTE]
    >Az モジュール 5.3.0 以上を使用して、Recovery Services コンテナーでのバックアップ用のカスタマー マネージド キーを使用します。
    
    >[!Warning]
    >Backup 用の暗号化キーを管理するために PowerShell を使用している場合は、ポータルからキーを更新しないことをお勧めします。<br>ポータルからキーを更新すると、新しいモデルをサポートする PowerShell 更新プログラムが利用可能になるまで、PowerShell を使用して暗号化キーを更新できなくなります。 ただし、Azure portal からキーを更新し続けることはできます。

自分の Recovery Services コンテナーを作成して構成していない場合は、[こちらで方法を確認](backup-create-rs-vault.md)してください。

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>カスタマー マネージド キーを使用して暗号化するためのコンテナーの構成

このセクションでは、次の手順を実行します。

1. Recovery Services コンテナーのマネージド ID を有効にする

1. Azure キー コンテナー内の暗号化キーにアクセスするためのアクセス許可をコンテナーに割り当てる

1. Azure キー コンテナーで論理的な削除と消去保護を有効にする

1. Recovery Services コンテナーに暗号化キーを割り当てる

意図した結果を得るには、これらすべての手順を、上記の順序で実行する必要があります。 各手順については、以下で詳しく説明します。

## <a name="enable-managed-identity-for-your-recovery-services-vault"></a>Recovery Services コンテナーのマネージド ID を有効にする

Azure Backup では、システム割り当てマネージド ID とユーザー割り当てマネージド ID を使用して、Azure Key Vault に格納されている暗号化キーにアクセスする Recovery Services コンテナーを認証します。 Recovery Services コンテナーのマネージド ID を有効にするには、次の手順に従います。

>[!NOTE]
>マネージド ID は、いったん有効にしたら (一時的にであっても) 無効に **しないでください**。 マネージド ID を無効にすると、動作に一貫性がなくなる可能性があります。

### <a name="enable-system-assigned-managed-identity-for-the-vault"></a>コンテナーのシステム割り当てマネージド ID を有効にする

**ポータルの場合:**

1. ご自身の Recovery Services コンテナー、 **[ID]** の順に移動します

    ![Identity settings](media/encryption-at-rest-with-cmk/enable-system-assigned-managed-identity-for-vault.png)

1. **[システム割り当て済み]** タブに移動します。

1. **[状態]** を **[オン]** に変更します。

1. **[保存]** をクリックして、コンテナーの ID を有効にします。

オブジェクト ID が生成されます。これがコンテナーのシステム割り当てマネージド ID です。

>[!NOTE]
>マネージド ID は、いったん有効にしたら (一時的にであっても) 無効にしないでください。 マネージド ID を無効にすると、動作に一貫性がなくなる可能性があります。


**PowerShell の場合:**

[Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault) コマンドを使用して、Recovery Services コンテナーのシステム割り当てマネージド ID を有効にします。

例:

```AzurePowerShell
$vault=Get-AzRecoveryServicesVault -ResourceGroupName "testrg" -Name "testvault"

Update-AzRecoveryServicesVault -IdentityType SystemAssigned -VaultId $vault.ID

$vault.Identity | fl
```

Output:

```output
PrincipalId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
TenantId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Type        : SystemAssigned
```

### <a name="assign-user-assigned-managed-identity-to-the-vault-in-preview"></a>ユーザー割り当てマネージド ID をコンテナーに割り当てる (プレビュー段階)

>[!Note]
>- CMK 暗号化のためにユーザーが割り当てたマネージド ID を使用するコンテナーでは、バックアップ用のプライベート エンドポイントの使用はサポートされません。
>- 特定のネットワークへのアクセスを制限する Azure Key Vault は、CMK 暗号化用のユーザー割り当てマネージド ID と共に、まだサポートされていません。

Recovery Services コンテナーのユーザー割り当てマネージド ID を割り当てるには、次の手順を実行します。

1.  ご自身の Recovery Services コンテナー、 **[ID]** の順に移動します

    ![ユーザー割り当てマネージド ID をコンテナーに割り当てる](media/encryption-at-rest-with-cmk/assign-user-assigned-managed-identity-to-vault.png)

1.  **[ユーザー割り当て済み]** タブに移動します。

1.  **[+追加]** をクリックして、ユーザー割り当てマネージド ID を追加します。

1.  開いた **[ユーザー割り当てマネージド ID の追加]** ブレードで、ID のサブスクリプションを選択します。

1.  一覧から ID を選択します。 ID またはリソース グループの名前でフィルター処理することもできます。

1.  完了したら、 **[追加]** をクリックして ID の割り当てを完了します。

## <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>Azure キー コンテナー内の暗号化キーにアクセスするためのアクセス許可を Recovery Services コンテナーに割り当てる

>[!Note]
>ユーザー割り当て ID を使用する場合は、同じアクセス許可をユーザー割り当て ID に割り当てる必要があります。

次に、Recovery Services コンテナーに対して、暗号化キーが格納されている Azure キー コンテナーにアクセスする許可を与える必要があります。 これを行うには、Recovery Services コンテナーのマネージド ID がキー コンテナーにアクセスできるようにします。

**ポータルの場合**:

1. ご自身の Azure キー コンテナー、 **[アクセス ポリシー]** の順に移動します。 続いて **[+ アクセス ポリシーの追加]** を選択します。

    ![アクセス ポリシーを追加する](./media/encryption-at-rest-with-cmk/access-policies.png)

1. **[キーのアクセス許可]** で、 **[取得]** 、 **[一覧]** 、 **[キーの折り返しを解除]** 、 **[キーを折り返す]** の各操作を選択します。 これにより、キーに対して許可するアクションが指定されます。

    ![キーのアクセス許可を割り当てる](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. **[プリンシパルの選択]** に移動し、検索ボックスで名前またはマネージド ID を使用して、ご自身のコンテナーを検索します。 表示されたら、そのコンテナーを選択し、ペインの下部にある **[選択]** を選択します。

    ![プリンシパルの選択](./media/encryption-at-rest-with-cmk/select-principal.png)

1. 完了したら、 **[追加]** を選択して新しいアクセス ポリシーを追加します。

1. **[保存]** を選択して、Azure キー コンテナーのアクセス ポリシーに加えた変更を保存します。

>[!NOTE] 
>前述のアクセス許可を含む Recovery Services コンテナーに、 _[Key Vault Crypto Officer](../key-vault/general/rbac-guide.md#azure-built-in-roles-for-key-vault-data-plane-operations)_ 役割など、RBAC の役割を割り当てることもできます。<br><br>これらの役割には、上記で説明したもの以外の追加のアクセス許可を含めることができます。

## <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>Azure キー コンテナーで論理的な削除と消去保護を有効にする

暗号化キーを格納している Azure キー コンテナーで、**論理的な削除と消去保護を有効にする** 必要があります。 これは、次に示すように、Azure キー コンテナーの UI から行うことができます (または、これらのプロパティはキー コンテナーの作成時に設定することができます)。 これらのキー コンテナーのプロパティの詳細については、[こちら](../key-vault/general/soft-delete-overview.md)を参照してください。

![論理的な削除と消去保護を有効にする](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

また、次の手順に従って、PowerShell を使って論理的な削除と消去保護を有効にすることもできます。

1. Azure アカウントにサインインします。

    ```azurepowershell
    Login-AzAccount
    ```

1. コンテナーが含まれているサブスクリプションを選択します。

    ```azurepowershell
    Set-AzContext -SubscriptionId SubscriptionId
    ```

1. 論理的な削除を有効にする

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

1. 消去保護を有効にします

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="assign-encryption-key-to-the-rs-vault"></a>RS コンテナーに暗号化キーを割り当てる

>[!NOTE]
> 先に進む前に、次の点を確認してください。
>
> - ここまでの手順がすべて正常に完了していること:
>   - Recovery Services コンテナーのマネージド ID が有効になり、必要なアクセス許可が割り当てられている
>   - Azure キー コンテナーで、論理的な削除と消去保護が有効になっている
> - CMK 暗号化を有効にする Recovery Services コンテナーでは、項目の保護も、その登録も **されていない**

上記の内容を確認したら、引き続きコンテナーの暗号化キーを選択します。

### <a name="to-assign-the-key-in-the-portal"></a>ポータルでキーを割り当てるには

1. ご自身の Recovery Services コンテナー、 **[プロパティ]** の順に移動します

    ![暗号化の設定](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. **[暗号化の設定]** にある **[更新]** を選択します。

1. [暗号化の設定] ペインで、 **[独自のキーを使用する]** を選択し、次のいずれかの方法を使用してキーの指定を続行します。 **自分が使用するキーが、有効な状態の RSA 2048 キーであることを確認してください。**

    1. この Recovery Services コンテナー内のデータを暗号化するために使用する **キー URI** を入力します。 また、(このキーを格納する) Azure キー コンテナーが存在するサブスクリプションを指定する必要もあります。 このキー URI は、お使いの Azure キー コンテナー内の対応するキーから取得できます。 キー URI は間違いのないようにコピーしてください。 キー識別子と共に表示される **[クリップボードにコピー]** ボタンを使用することをお勧めします。

        >[!NOTE]
        >キー URI を使用して暗号化キーを指定すると、キーは自動ローテーションされません。 そのため、必要に応じて新しいキーを指定して、キーの更新を手動で行う必要があります。

        ![キー URI を入力する](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. キーの選択ペインで、キー コンテナーのキーを参照して選択します。

        >[!NOTE]
        >キーの選択ペインを使用して暗号化キーを指定すると、キーの新しいバージョンが有効になるたびにキーが自動ローテーションされます。 暗号化キーの自動ローテーションの有効化については、[こちら](#enabling-auto-rotation-of-encryption-keys)を参照してください。

        ![Key Vault からのキーの選択](./media/encryption-at-rest-with-cmk/key-vault.png)

1. **[保存]** を選択します。

1. **暗号化キーの更新の進行状況と状態の追跡**:左のナビゲーション バーにある **[バックアップ ジョブ]** ビューを使用して、暗号化キーの割り当ての進行状況と状態を追跡できます。 少し時間が経過すると状態が **[完了]** に変わります。 コンテナーでは、指定したキーを KEK として使用して、すべてのデータを暗号化するようになります。

    ![状態 - 完了](./media/encryption-at-rest-with-cmk/status-succeeded.png)

    暗号化キーの更新は、コンテナーのアクティビティ ログにも記録されます。

    ![アクティビティ ログ](./media/encryption-at-rest-with-cmk/activity-log.png)

### <a name="to-assign-the-key-with-powershell"></a>PowerShell を使用してキーを割り当てるには

[Set-AzRecoveryServicesVaultProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultproperty) コマンドを使用して、カスタマー マネージド キーを使用した暗号化を有効にし、使用する暗号化キーの割り当てまたは更新を行います。

例:

```azurepowershell
$keyVault = Get-AzKeyVault -VaultName "testkeyvault" -ResourceGroupName "testrg" 
$key = Get-AzKeyVaultKey -VaultName $keyVault -Name "testkey" 
Set-AzRecoveryServicesVaultProperty -EncryptionKeyId $key.ID -KeyVaultSubscriptionId "xxxx-yyyy-zzzz"  -VaultId $vault.ID


$enc=Get-AzRecoveryServicesVaultProperty -VaultId $vault.ID
$enc.encryptionProperties | fl
```

Output:

```output
EncryptionAtRestType          : CustomerManaged
KeyUri                        : testkey
SubscriptionId                : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 
LastUpdateStatus              : Succeeded
InfrastructureEncryptionState : Disabled
```

>[!NOTE]
> 暗号化キーを更新または変更する場合も、このプロセスは同じです。 (現在使用されているものとは異なる) 別のキー コンテナーのキーを更新して使用する場合は、次のことを確認してください。
>
> - そのキー コンテナーが、Recovery Services コンテナーと同じリージョンにある
>
> - そのキー コンテナーで論理的な削除と消去保護が有効になっている
>
> - Recovery Services コンテナーに、キー コンテナーにアクセスするために必要なアクセス許可がある。

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>カスタマー マネージド キーで暗号化されたコンテナーへのバックアップ

保護の構成に進む前に、次のチェックリストに準拠していることを確認することを強くお勧めします。 これが重要なのは、カスタマー マネージド キー以外で暗号化さているコンテナーに項目をバックアップするように構成 (または構成しようと) すると、CMK を使用した暗号化をそのコンテナーでは有効にすることができず、引き続きプラットフォーム マネージド キーを使用することになるためです。

>[!IMPORTANT]
> 保護の構成に進むには、次の手順を **正しく** 完了している必要があります。
>
>1. Recovery Services コンテナーを作成した
>1. Recovery Services コンテナーのシステム割り当てマネージド ID を有効にした。または、ユーザー割り当てマネージド ID をコンテナーに割り当てた
>1. Key Vault から暗号化キーにアクセスするために、Recovery Services コンテナー (またはユーザー当てマネージド ID) にアクセス許可を割り当てた
>1. キー コンテナーで論理的な削除と消去保護を有効にした
>1. Recovery Services コンテナーに有効な暗号化キーを割り当てた
>
>上記の手順をすべて確認できた場合のみ、バックアップの構成に進んでください。

カスタマー マネージド キーで暗号化された Recovery Services コンテナーへのバックアップを構成して実行するプロセスは、プラットフォーム マネージド キーを使用するコンテナーの場合と同じで、**エクスペリエンスに変わりはありません**。 このことは、[Azure VM のバックアップ](./quick-backup-vm-portal.md)および VM 内で実行されるワークロード ([SAP HANA](./tutorial-backup-sap-hana-db.md)、[SQL Server](./tutorial-sql-backup.md) データベースなど) のバックアップにも当てはまります。

## <a name="restoring-data-from-backup"></a>バックアップからのデータの復元

### <a name="vm-backup"></a>VM のバックアップ

Recovery Services コンテナーの格納データは、[こちら](./backup-azure-arm-restore-vms.md)で説明されている手順に従って復元できます。 カスタマー マネージド キーを使用して暗号化された Recovery Services コンテナーから復元する場合は、復元されたデータを、ディスク暗号化セット (DES) を使用して暗号化することを選択できます。

#### <a name="restoring-vm--disk"></a>VM およびディスクの復元

1. "スナップショット" 復旧ポイントからディスクおよび VM を復元すると、復元データは、ソース VM のディスクの暗号化に使用される DES で暗号化されます。

1. 回復の種類に "コンテナー" を指定して復旧ポイントからディスクまたは VM を復元すると、復元時に指定した DES を使用して、復元データを暗号化することを選択できます。 または、DES を指定せずにデータの復元を続行するように選択することもできます。この場合は、Microsoft のマネージド キーを使用して暗号化されます。

復元が完了したら、復元を開始した際に行った選択に関係なく、復元されたディスクまたは VM を暗号化できます。

![復元ポイント](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>コンテナーの復旧ポイントからの復元中にディスク暗号化セットを選択する

**ポータルの場合**:

ディスク暗号化セットは、次に示すように、復元ペインの [暗号化の設定] で指定します。

1. **[Encrypt disk(s) using your key]\(キーを使用してディスクを暗号化する\)** で、 **[はい]** を選択します。

1. ドロップダウンから、復元されたディスクに使用する DES を選択します。 **DES にアクセスできることを確認してください。**

>[!NOTE]
>Azure Disk Encryption を使用する VM を復元する場合は、復元中に DES を選択する機能を使用できません。

![キーを使用してディスクを暗号化する](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

**PowerShell の場合**:

[Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) コマンドをパラメーター [`-DiskEncryptionSetId <string>`] と共に使用して、復元されたディスクの暗号化に使用される [DES の指定](/powershell/module/az.compute/get-azdiskencryptionset)を行います。 VM バックアップからのディスクの復元の詳細については、[こちらの記事](./backup-azure-vms-automation.md#restore-an-azure-vm)を参照してください。

例:

```azurepowershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $vault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $vault.ID
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $vault.ID
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -DiskEncryptionSetId “testdes1” -VaultId $vault.ID
```

#### <a name="restoring-files"></a>ファイルの復元

ファイル復元を実行すると、復元されるデータは、ターゲットの場所を暗号化するために使用されたキーで暗号化されます。

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>Azure VM での SAP HANA または SQL データベースの復元

Azure VM で実行されているバックアップ SAP HANA または SQL データベースから復元を行うと、復元されたデータは、ターゲットの保存場所で使用される暗号化キーを使用して暗号化されます。 これは、VM のディスクの暗号化に使用される、カスタマー マネージド キーである可能性も、プラットフォーム マネージド キーである可能性もあります。

## <a name="additional-topics"></a>関連トピック

### <a name="enable-encryption-using-customer-managed-keys-at-vault-creation-in-preview"></a>コンテナーの作成時にカスタマー マネージド キーを使用して暗号化を有効にする (プレビュー)

>[!NOTE]
>カスタマー マネージド キーを使用してコンテナー作成時に暗号化を有効にすることは、限定パブリック プレビュー段階であり、サブスクリプションを許可リストに登録する必要があります。 プレビューにサインアップするには、[フォーム](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURDNTVVhGOUxXSVBZMEwxUU5FNDkyQkU4Ny4u)に記入し、[AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) までご連絡ください。

サブスクリプションが許可リストに登録されている場合は、 **[バックアップの暗号化]** タブが表示されます。 これにより、新しい Recovery Services コンテナーの作成時にカスタマー マネージド キーを使用してバックアップで暗号化を有効にできます。 暗号化を有効にするには、次の手順に従います。

1. **[基本]** タブの隣にある **[バックアップの暗号化]** タブで、暗号化に使用する暗号化キーと ID を指定します。

   ![コンテナー レベルでの暗号化を有効にする](media/encryption-at-rest-with-cmk/enable-encryption-using-cmk-at-vault.png)


   >[!NOTE]
   >設定は Backup にのみ適用され、省略可能です。

1. 暗号化の種類として **[カスタマー マネージド キーの使用]** を選択します。

1. 暗号化に使用するキーを指定するには、適切なオプションを選択します。

   暗号化キーの URI を指定することも、キーを参照して選択することもできます。 **[キー コンテナーを選択する]** オプションを使用してキーを指定すると、暗号化キーの自動ローテーションが自動的に有効になります。 自動ローテーションについては、[こちら](#enabling-auto-rotation-of-encryption-keys)を参照してください。 

1. カスタマー マネージド キーを使用して暗号化を管理するには、ユーザー割り当てマネージド ID を指定します。 **[選択]** をクリックして、必要な ID を参照して選択します。

1. 完了したら、タグの追加 (オプション) に進み、コンテナーの作成を続行します。

### <a name="enabling-auto-rotation-of-encryption-keys"></a>暗号化キーの自動ローテーションの有効化

バックアップの暗号化に使用する必要があるカスタマー マネージド キーを指定する場合は、次の方法を使用して指定します。

- キーの URI を入力する
- キー コンテナーから選ぶ

**[キー コンテナーから選ぶ]** オプションを使用すると、選択したキーの自動ローテーションを有効にできます。 これにより、次のバージョンに手動で更新する手間を省くことができます。 ただし、このオプションを使用すると、次のようになります。
- キー バージョンの更新が有効になるまでに最大で 1 時間かかる場合があります。
- キーの新しいバージョンが有効になると、キーの更新が有効になった後、少なくとも 1 回の後続バックアップ ジョブまで、古いバージョンも使用可能 (有効な状態) になります。

### <a name="using-azure-policies-for-auditing-and-enforcing-encryption-utilizing-customer-managed-keys-in-preview"></a>カスタマー マネージド キーによる監査と暗号化適用のための Azure ポリシーの使用 (プレビュー)

Azure Backup により、Azure ポリシーを使用して、Recovery Services コンテナー内のデータに、カスタマー マネージド キーを使用して監査を行い、暗号化を適用することができます。 Azure ポリシーを使用した場合:

- 監査ポリシーを使用して、2021 年 4 月 1 日以降に有効になったカスタマー マネージド キーにより、暗号化された資格情報コンテナーを監査できます。 この日付より前に CMK 暗号化が有効になっている資格情報コンテナーの場合は、ポリシーの適用に失敗したり、不正な結果が表示されることがあります (つまり、**CMK 暗号化** が有効になっているにもかかわらず、これらのコンテナーが非準拠として報告される場合があります)。
- 2021 年 4 月 1 日より前に **CMK 暗号化** が有効になった資格情報コンテナーの監査ポリシーを使用するには、Azure portal を使用して暗号化キーを更新します。 これにより、新しいモデルにアップグレードできます。 暗号化キーを変更しない場合は、キー URI またはキー選択オプションを使用して、同じキーを再度指定します。 

   >[!Warning]
    >Backup 用の暗号化キーを管理するために PowerShell を使用している場合は、ポータルからキーを更新しないことをお勧めします。<br>ポータルからキーを更新すると、新しいモデルをサポートする PowerShell 更新プログラムが利用可能になるまで、PowerShell を使用して暗号化キーを更新できなくなります。 ただし、Azure portal からキーを更新し続けることはできます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>カスタマー マネージド キーを使用して既存のバックアップ コンテナーを暗号化することはできますか。

いいえ、CMK による暗号化は、新しいコンテナーに対してのみ有効です。 そのため、コンテナー内には、保護されている項目があってはなりません。 実際、カスタマー マネージド キーを使用して暗号化を有効にする前に、コンテナーに項目を保護しようとする試みが行われてはいけません。

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>コンテナーに項目を保護しようとしましたが失敗したため、コンテナーに保護された項目はまだ含まれていません。 このコンテナーに対して CMK 暗号化を有効にすることはできますか。

いいえ。コンテナーに項目を保護しようとしたことが過去にあってはなりません。

### <a name="i-have-a-vault-thats-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>CMK 暗号化を使用しているコンテナーがあります。 バックアップ項目がコンテナー内に保護されている場合でも、後でプラットフォーム マネージド キーを使用した暗号化に戻すことはできますか。

いいえ。いったん CMK 暗号化を有効にすると、プラットフォーム マネージド キーを使用するように戻すことはできません。 使用するキーは、実際の要件に応じて変更することができます。

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>Azure Backup の CMK 暗号化は、Azure Site Recovery にも適用されますか。

いいえ。この記事では、バックアップ データの暗号化についてのみ説明しています。 Azure Site Recovery では、このサービスから使用できるように別途プロパティを設定する必要があります。

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>この記事にある手順の 1 つを実行しないまま続行し、データ ソースを保護しました。 引き続き CMK 暗号化を使用できますか。

記事のステップに従わずに項目の保護まで続行した場合、カスタマー マネージド キーを使用した暗号化をコンテナーで使用できなくなる可能性があります。 このため、項目の保護に進む前に、[このチェックリスト](#backing-up-to-a-vault-encrypted-with-customer-managed-keys)を参照することをお勧めします。

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>CMK 暗号化を使用すると、バックアップのコストは増えますか。

バックアップに CMK 暗号化を使用しても、追加のコストは発生しません。 ただし、キーが格納されている Azure キー コンテナーの使用には、引き続きコストが発生する可能性があります。

## <a name="next-steps"></a>次のステップ

- [Azure Backup のセキュリティ機能の概要](security-overview.md)
