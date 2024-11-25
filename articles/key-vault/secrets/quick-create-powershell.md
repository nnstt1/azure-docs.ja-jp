---
title: クイックスタート - PowerShell を使用して Key Vault との間でシークレットの設定と取得を行う
description: このクイックスタートでは、Azure PowerShell を使用して Azure キー コンテナーからシークレットを作成、取得、および削除する方法について説明します
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, devx-track-azurepowershell
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: f8ca630054e2583380aa02984d094c24bd025f00
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755712"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>クイック スタート:PowerShell を使用して Azure Key Vault との間でシークレットの設定と取得を行う

Azure Key Vault は、セキュリティで保護されたシークレット ストアとして機能するクラウド サービスです。 キー、パスワード、証明書、およびその他のシークレットを安全に保管することができます。 Key Vault の詳細については、[概要](../general/overview.md)に関するページを参照してください。 このクイック スタートでは、PowerShell を使用してキー コンテナーを作成します。 次に、新しく作成したキー コンテナーにシークレットを格納します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell をローカルにインストールして使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 5.0.0 以降が必要になります。 バージョンを確認するには、「`$PSVersionTable.PSVersion`」と入力します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Key Vault を作成します

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="give-your-user-account-permissions-to-manage-secrets-in-key-vault"></a>Key Vault でシークレットを管理するアクセス許可をユーザー アカウントに付与する

Azure PowerShell の [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) コマンドレットを使用して、Key Vault アクセス ポリシーを更新し、シークレットのアクセス許可をユーザー アカウントに付与します。

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -UserPrincipalName "user@domain.com" -PermissionsToSecrets get,set,delete
```

## <a name="adding-a-secret-to-key-vault"></a>Key Vault へのシークレットの追加

シークレットをコンテナーに追加するには、いくつかの手順を実行する必要があります。 このケースでは、アプリケーションによって使用可能なパスワードを追加します。 パスワードは **ExamplePassword** と呼ばれ、値 **hVFkk965BuUv** がその中に格納されます。

最初に、次のように入力して、**hVFkk965BuUv** の値をセキュリティで保護された文字列に変換します。

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString "hVFkk965BuUv" -AsPlainText -Force
```

その後、Azure PowerShell の [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) コマンドレットを使用して **ExamplePassword** という名前の Key Vault に値 **hVFkk965BuUv** でシークレットを作成します。


```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "ExamplePassword" -SecretValue $secretvalue
```

## <a name="retrieve-a-secret-from-key-vault"></a>Key Vault からシークレットを取得する

シークレットに含まれる値をプレーンテキストとして表示するには、Azure PowerShell の [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) コマンドレットを使用します。

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "ExamplePassword" -AsPlainText
```

これで、キー コンテナーを作成し、シークレットを格納した後、取得しました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

 このコレクションの他のクイックスタートとチュートリアルは、このクイックスタートに基づいています。 他のクイック スタートおよびチュートリアルを引き続き実行する場合は、これらのリソースをそのまま残しておくことをお勧めします。

必要がなくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループ、Key Vault、およびすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Key Vault を作成してシークレットを格納しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](../general/overview.md)を確認する
- [複数行のシークレットを Key Vault に格納](multiline-secrets.md)する方法について確認する
- [Azure PowerShell の Key Vault コマンドレット](/powershell/module/az.keyvault/#key_vault)のリファレンスを参照する
- [Key Vault のセキュリティの概要](../general/security-features.md)を確認する
