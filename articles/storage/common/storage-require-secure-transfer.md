---
title: セキュリティで保護された接続を確保するために安全な転送を要求する
titleSuffix: Azure Storage
description: Azure Storage への要求に対して安全な転送を求める方法について説明します。 ストレージ アカウント用に安全な転送を要求すると、セキュリティで保護されていない接続から送信される要求はすべて拒否されます。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/01/2021
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 65d877bce0bdcab35248d4b9a41b92f46c132903
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128630738"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>セキュリティで保護された接続を確保するために安全な転送を要求する

ストレージ アカウントに **[安全な転送が必須]** プロパティを設定すると、セキュリティで保護された接続からの要求のみを受け入れるようにストレージ アカウントを構成できます。 安全な転送を要求すると、セキュリティで保護されていない接続から送信される要求はすべて拒否されます。 Microsoft では、すべてのストレージ アカウントに対して常に、セキュリティで保護された転送を要求することをお勧めしています。

安全な転送が必要な場合、Azure Storage REST API 操作の呼び出しは HTTPS を介して行う必要があります。 HTTP を介して行われた要求はすべて拒否されます。 既定では、ストレージ アカウントを作成すると、 **[安全な転送が必須]** プロパティが有効になります。

Azure Policy には、ストレージ アカウントに対して安全な転送が要求されることを保証する組み込みのポリシーが用意されています。 詳細については、「[Azure Policy の組み込みのポリシー定義](../../governance/policy/samples/built-in-policies.md#storage)」の「**ストレージ**」セクションを参照してください。

ストレージ アカウントに対して安全な転送が求められている場合、暗号化なしの SMB 経由で Azure ファイル共有に接続しても失敗します。 セキュリティで保護されていない接続の例としては、暗号化なしの SMB 2.1 または SMB 3.x 経由の接続を挙げることができます。

> [!NOTE]
> Azure Storage ではカスタム ドメイン名の HTTPS はサポートされないため、カスタム ドメイン名を使用している場合、このオプションは適用されません。
>
> このセキュリティで保護された転送設定は、TCP には適用されません。 セキュリティで保護されていない、TCP を使用した Azure Blob Storage での NFS 3.0 プロトコルのサポートによる接続は成功します。

## <a name="require-secure-transfer-in-the-azure-portal"></a>Azure portal で安全な転送を要求する

[Azure portal](https://portal.azure.com) でストレージ アカウントを作成するときに、 **[安全な転送が必須]** プロパティを有効にすることができます。 既存のストレージ アカウントの場合も有効にすることができます。

### <a name="require-secure-transfer-for-a-new-storage-account"></a>新しいストレージ アカウントの安全な転送が必須

1. Azure Portal で **[ストレージ アカウントの作成]** ウィンドウを開きます。
1. **[詳細設定]** ページで、 **[安全な転送を有効にする]** チェック ボックスをオンにします。

   ![[ストレージ アカウントの作成] ブレード](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>既存のストレージ アカウントの安全な転送が必須

1. Azure Portal で既存のストレージ アカウントを選択します。
1. ストレージ アカウント メニュー ウィンドウの **[設定]** で、 **[構成]** を選択します。
1. **[安全な転送が必須]** で、 **[有効]** を選択します。

   ![ストレージ アカウント メニュー ウィンドウ](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>コードから安全な転送を要求する

安全な転送をプログラムで要求するには、ストレージ アカウントで *enableHttpsTrafficOnly* プロパティを *True* に設定します。 このプロパティを設定するには、ストレージ リソース プロバイダーの REST API、クライアント ライブラリ、またはツールを使用します。

- [REST API](/rest/api/storagerp/storageaccounts)
- [PowerShell](/powershell/module/az.storage/set-azstorageaccount)
- [CLI](/cli/azure/storage/account)
- [NodeJS](https://www.npmjs.com/package/@azure/arm-storage/)
- [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
- [Python SDK](https://pypi.org/project/azure-mgmt-storage)
- [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>PowerShell を使用して安全な転送を要求する

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

このサンプルには、Azure PowerShell モジュール Az バージョン 0.7 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。

`Connect-AzAccount` を実行して、Azure との接続を作成します。

 以下のコマンド ラインを使って、設定を確認します。

```powershell
Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

以下のコマンド ラインを使って、設定を有効にします。

```powershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

## <a name="require-secure-transfer-with-azure-cli"></a>Azure CLI を使用して安全な転送を要求する

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 次のコマンドを使用して設定を確認します。

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

次のコマンドを使用して設定を有効にします。

```azurecli-interactive
az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>次のステップ

[BLOB ストレージのセキュリティに関する推奨事項](../blobs/security-recommendations.md)
