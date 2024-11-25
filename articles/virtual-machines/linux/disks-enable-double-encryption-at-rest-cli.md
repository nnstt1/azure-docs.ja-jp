---
title: 保存時の二重暗号化を有効にする - Azure CLI - マネージド ディスク
description: Azure CLI を使用して、マネージド ディスク データの保存時の二重暗号化を有効にします。
author: roygara
ms.date: 06/29/2021
ms.topic: how-to
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 9fc3e8570deb2cb9dd588f34e30f3c6662c7f91a
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698456"
---
# <a name="use-the-azure-cli-to-enable-double-encryption-at-rest-for-managed-disks"></a>Azure CLI を使用して、マネージド ディスクの保存時の二重暗号化を有効にします

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: フレキシブル スケール セット 

Azure Disk Storage は、マネージド ディスクに対する保存時の二重暗号化をサポートしています。 保存時の二重暗号化とその他のマネージド ディスクの暗号化の概念については、ディスクの暗号化の記事の[保存時の暗号化](../disk-encryption.md#double-encryption-at-rest)に関するセクションを参照してください。

## <a name="prerequisites"></a>前提条件

最新の [Azure CLI](/cli/azure/install-az-cli2) をインストールし、[az login](/cli/azure/reference-index) を使用して Azure アカウントにログインします。

## <a name="getting-started"></a>作業の開始

1. Azure Key Vault と暗号化キーのインスタンスを作成します。

    Key Vault インスタンスを作成する場合、論理的な削除と消去保護を有効にする必要があります。 論理的な削除では、Key Vault は削除されたキーを特定の保持期間 (既定では90日) にわたって保持します。 消去保護では、保持期間が経過するまで、削除されたキーを完全に削除できないようになります。 これらの設定は、誤って削除したためにデータが失われるのを防ぎます。 これらの設定は、Key Vault を使用してマネージド ディスクを暗号化する場合は必須です。

    
    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    EncryptionAtRestWithPlatformAndCustomerKeys として encryptionType を設定して、DiskEncryptionSet を作成します。 Azure Resource Manager (ARM) テンプレートで API バージョン **2020-05-01** を使用します。 
    
        ```azurecli
        az deployment group create -g $rgName \
       --template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" \
        --parameters "diskEncryptionSetName=$diskEncryptionSetName" "encryptionType=EncryptionAtRestWithPlatformAndCustomerKeys" "keyVaultId=$keyVaultId" "keyVaultKeyUrl=$keyVaultKeyUrl" "region=$location"
        ```

1.    DiskEncryptionSet リソースに Key Vault へのアクセス権を付与します。 

        > [!NOTE]
        > Azure がお使いの Azure Active Directory にご自分の DiskEncryptionSet の ID を作成するのには数分かかる場合があります。 次のコマンドを実行しているときに "Active Directory オブジェクトが見つかりません" のようなエラーが表示された場合は、数分待ってから再試行してください。

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)

        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
        ```

## <a name="next-steps"></a>次のステップ

これらのリソースを作成し、構成したので、これらを使用してマネージド ディスクをセキュリティで保護することができます。 次のリンクには、個々のシナリオごとにスクリプトの例が含まれています。これは、マネージド ディスクをセキュリティで保護するために使用できます。

- [Azure Resource Manager テンプレートのサンプル](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [サーバー側の暗号化でカスタマー マネージド キーを有効にする - 例](disks-enable-customer-managed-keys-cli.md#examples)
