---
title: TDE 保護機能を削除する (PowerShell と Azure CLI)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Bring Your Own Key (BYOK) をサポートする TDE を使用している Azure SQL Database または Azure Synapse Analytics で侵害された可能性のある TDE プロテクターに対応する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 06/23/2021
ms.openlocfilehash: 6d3027afae6b1d4121582014bb2b525ba2fd2c44
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090261"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>PowerShell を使用した Transparent Data Encryption (TDE) 保護機能の削除
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


このトピックでは、Azure Key Vault のカスタマー マネージド キー、つまり Bring Your Own Key (BYOK) をサポートする TDE を使用している Azure SQL Database または Azure Synapse Analytics の侵害された可能性のある TDE 保護機能に対応する方法について説明します。 TDE の BYOK サポートの詳細については、[概要ページ](transparent-data-encryption-byok-overview.md)をご覧ください。

> [!CAUTION]
> この記事で説明する手順は、極端な状況またはテスト環境でのみ実行する必要があります。 アクティブに使用されている TDE 保護機能を Azure Key Vault から削除すると、**データベースは使用不能** になるため、手順をよく確認してください。

サービスまたはユーザーがキーに不正アクセスしているなど、キーが侵害された疑いがある場合は、キーを削除するのが最善です。

Key Vault で TDE 保護機能を削除したら、最大 10 分ですべての暗号化されたデータベースで、対応するエラー メッセージを使用してすべての接続の拒否が開始され、状態が [[アクセス不可]](./transparent-data-encryption-byok-overview.md#inaccessible-tde-protector) に変更されることに注意してください。

このハウツー ガイドでは、侵害インシデント対応の後でデータベースが **アクセスされない** ようにする方法を説明します。

> [!NOTE]
> この記事は、Azure SQL Database、Azure SQL Managed Instance、Azure Synapse Analytics (専用 SQL プール (以前の SQL DW)) に適用されます。 Synapse ワークスペース内の専用 SQL プールの Transparent Data Encryption に関するドキュメントについては、[Azure Synapse Analytics の暗号化](../../synapse-analytics/security/workspaces-encryption.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションがあり、そのサブスクリプションの管理者である必要があります。
- Azure PowerShell がインストールされ、実行されている必要があります。
- このハウツー ガイドは、Azure SQL Database または Azure Synapse の TDE 保護機能として、Azure Key Vault のキーを既に使用していることを前提としています。 詳細については、[BYOK をサポートする Transparent Data Encryption](transparent-data-encryption-byok-overview.md) に関する記事をご覧ください。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

 Az モジュールのインストール手順については、[Azure PowerShell のインストール](/powershell/azure/install-az-ps)を参照してください。 具体的なコマンドレットについては、「[AzureRM.Sql](/powershell/module/AzureRM.Sql/)」を参照してください。

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) モジュールは引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 AzureRM モジュールのバグ修正は、少なくとも 2020 年 12 月までは引き続き受け取ることができます。  Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。 その互換性の詳細については、「[新しい Azure PowerShell Az モジュールの概要](/powershell/azure/new-azureps-module-az)」を参照してください。

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

インストールについては、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

* * *

## <a name="check-tde-protector-thumbprints"></a>TDE 保護機能の拇印を確認する

次の手順では、特定のデータベースの仮想ログ ファイル (VLF) でまだ使用されている TDE 保護機能の拇印を確認する方法の概要を示します。
データベースの現在の TDE 保護機能の拇印、およびデータベース ID は、

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint]
 FROM [sys].[dm_database_encryption_keys]
```

次のクエリでは、VLF と、使用中の TDE 保護機能のそれぞれの拇印が返されます。 異なる拇印でそれぞれ、Azure Key Vault (AKV) の異なるキーが参照されます。

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

PowerShell または Azure CLI を使用することもできます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell コマンドの **Get-AzureRmSqlServerKeyVaultKey**  では、クエリで使用される TDE 保護機能の拇印が提供されるため、AKV で保持するキーと削除するキーを確認できます。 データベースで使用されなくなったキーのみを、Azure Key Vault から安全に削除することができます。

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

PowerShell コマンドの **az sql server key show**  では、クエリで使用される TDE 保護機能の拇印が提供されるため、AKV で保持するキーと削除するキーを確認できます。 データベースで使用されなくなったキーのみを、Azure Key Vault から安全に削除することができます。

* * *

## <a name="keep-encrypted-resources-accessible"></a>暗号化されたリソースを引き続きアクセス可能にしておく

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Key Vault に新しいキー](/powershell/module/az.keyvault/add-azkeyvaultkey)を作成します。 アクセス制御はコンテナー レベルでプロビジョニングされるため、この新しいキーは、侵害された可能性のある TDE 保護機能とは別のキー コンテナーに作成する必要があります。

2. [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) および [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) コマンドレットを使用してサーバーに新しいキーを追加し、サーバーの新しい TDE 保護機能として更新します。

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) コマンドレットを使用して、サーバーとすべてのレプリカが新しい TDE 保護機能に更新されたことを確認します。

   > [!NOTE]
   > サーバーにあるすべてのデータベースとセカンダリ データベースに新しい TDE 保護機能が伝達されるまでに数分かかることがあります。

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Key Vault 内の[新しいキーのバックアップ](/powershell/module/az.keyvault/backup-azkeyvaultkey)を作成します。

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) コマンドレットを使用して、侵害されたキーを Key Vault から削除します。

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. 今後、Key Vault にキーを復元するときは、[Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) コマンドレットを使用します。

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

コマンド リファレンスについては、[Azure CLI keyvault](/cli/azure/keyvault/key) に関するページを参照してください。

1. [Key Vault に新しいキー](/cli/azure/keyvault/key#az_keyvault_key_create)を作成します。 アクセス制御はコンテナー レベルでプロビジョニングされるため、この新しいキーは、侵害された可能性のある TDE 保護機能とは別のキー コンテナーに作成する必要があります。

2. 新しいキーをサーバーに追加し、サーバーの新しい TDE 保護機能として更新します。

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. サーバーとすべてのレプリカが新しい TDE 保護機能に更新されていることを確認します。

   > [!NOTE]
   > サーバーにあるすべてのデータベースとセカンダリ データベースに新しい TDE 保護機能が伝達されるまでに数分かかることがあります。

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Key Vault 内の新しいキーのバックアップを作成します。

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Key Vault から、侵害されたキーを削除します。

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. 今後は、次のようにして Key Vault にキーを復元します。

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="make-encrypted-resources-inaccessible"></a>暗号化されたリソースをアクセス不可にする

1. 侵害された可能性のあるキーによって暗号化されているデータベースを削除します。

   データベースとログ ファイルは自動的にバックアップされるので、(キーを提供すれば) データベースのポイントインタイム リストアをいつでも実行できます。 最新のトランザクションの最大 10 分間のデータが失われる可能性を防ぐために、アクティブな TDE 保護機能を削除する前にデータベースを削除する必要があります。

2. Key Vault 内の TDE 保護機能のキー マテリアルをバックアップします。
3. 侵害された可能性のあるキーを Key Vault から削除します。

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>次のステップ

- セキュリティ要件に準拠するためにサーバーの TDE 保護機能をローテーションする方法を確認する:[PowerShell を使用して Transparent Data Encryption (TDE) 保護機能をローテーションする](transparent-data-encryption-byok-key-rotation.md)
- TDE の Bring Your Own Key サポートを使用する:[PowerShell で Key Vault の独自のキーを使用して TDE を有効にする](transparent-data-encryption-byok-configure.md)