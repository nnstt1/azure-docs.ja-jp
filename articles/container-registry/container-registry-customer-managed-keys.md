---
title: カスタマー マネージド キーを使用してレジストリを暗号化する
<<<<<<< HEAD
description: Azure Container Registry の保存時の暗号化、および Azure Key Vault に格納されているカスタマー マネージド キーを使用して Premium レジストリを暗号化する方法について説明します。
ms.topic: article
ms.date: 03/03/2021
ms.custom: ''
ms.openlocfilehash: 09eea79eb6fb9ad9e4526b1a0390664e5dd9d61e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
=======
description: Azure コンテナー レジストリの保存時の暗号化、および Azure Key Vault に格納されているカスタマー マネージド キーを使用して Premium レジストリを暗号化する方法について説明します。
ms.topic: how-to
ms.date: 09/13/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: cd15a42585f5722432d32d9cffdfa89e2c17f8a2
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
>>>>>>> repo_sync_working_branch
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130046432"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>カスタマー マネージド キーを使用してレジストリを暗号化する

イメージや他の成果物を Azure Container Registry に格納すると、保存時のレジストリの内容は Azure によって[サービス マネージド キー](../security/fundamentals/encryption-models.md)を使用して自動的に暗号化されます。 Azure Key Vault 内で作成して管理するキー (カスタマー マネージド キー) を使用すると、既定の暗号化を追加の暗号化レイヤーで補完することができます。 この記事では、Azure CLI、Azure portal、または Resource Manager テンプレートを使用する手順について説明します。

カスタマー マネージド キーを使用するサーバー側暗号化は、[Azure Key Vault](../key-vault/general/overview.md) との統合によってサポートされています。 

* 独自の暗号化キーを作成してキー コンテナーに格納したり、Azure Key Vault の API を使ってキーを生成したりできます。 
* Azure Key Vault を使用してキー使用法を監査することもできます。
* Azure Key Vault で新しいキーのバージョンが利用可能になったときのレジストリ暗号化キーの自動ローテーションが Azure Container Registry でサポートされています。 レジストリ暗号化キーは手動でローテーションすることもできます。

この機能は、**Premium** コンテナー レジストリ サービス レベルで使用できます。 レジストリ サービスのレベルと制限については、[Azure Container Registry のサービス レベル](container-registry-skus.md)に関する記事を参照してください。


## <a name="things-to-know"></a>注意事項

* 現在カスタマー マネージド キーを有効にできるのは、レジストリを作成するときだけです。 キーを有効にするときは、"*ユーザー割り当て*" マネージド ID を構成して、キー コンテナーにアクセスします。 後で、必要に応じて、キー コンテナーへのアクセスに対してレジストリのシステム マネージド ID を有効にできます。
* レジストリでカスタマー マネージド キーを使用して暗号化を有効にすると、この暗号化は無効にできなくなります。  
* Azure Container Registry でサポートされているのは、RSA または RSA HSM キーのみです。 楕円曲線キーは現在サポートされていません。
* 現在、カスタマー マネージド キーで暗号化されたレジストリでは、[コンテンツの信頼](container-registry-content-trust.md)はサポートされていません。
* カスタマー マネージド キーで暗号化されたレジストリでは、現在、[ACR タスク](container-registry-tasks-overview.md)に対する実行ログは 24 時間だけ保持されます。 それより長くログを保持する必要がある場合は、[タスク実行ログのエクスポートと保存](container-registry-tasks-logs.md#alternative-log-storage)に関するガイダンスを参照してください。

## <a name="automatic-or-manual-update-of-key-versions"></a>キーのバージョンの自動または手動更新

カスタマー マネージド キーで暗号化されたレジストリのセキュリティに関する重要な考慮事項は、暗号化キーを更新 (ローテーション) する頻度です。 Azure Key Vault に格納されているキーの[バージョン](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)は、カスタマー マネージド キーとして使用される場合、組織のコンプライアンス ポリシーによって定期的な更新が必要になることがあります。 

カスタマー マネージド キーを使用してレジストリの暗号化を構成する場合、暗号化に使用するキーのバージョンを更新するには、次の 2 つの方法があります。

* **キーのバージョンを自動的に更新する** - Azure Key Vault で新しいバージョンが利用可能になったときに自動的にカスタマー マネージド キーを更新するには、カスタマー マネージド キーを使用した暗号化を有効にするときに、キーのバージョンを省略します。 レジストリがバージョンなしのキーで暗号化されている場合、Azure Container Registry により、キー コンテナーの新しいキーのバージョンが定期的に確認され、カスタマー マネージド キーが 1 時間以内に更新されます。 最新バージョンのキーが Azure Container Registry によって自動的に使用されます。

* **キーのバージョンを手動で更新する** - レジストリの暗号化に特定のバージョンのキーを使用するには、カスタマー マネージド キーを使用した暗号化を有効にするときに、そのキー バージョンを指定します。 レジストリが特定のキーのバージョンで暗号化されている場合、カスタマー マネージド キーを手動でローテーションするまで、そのバージョンが Azure Container Registry で暗号化に使用されます。

詳細については、この記事の後半にある「[キーのバージョンあり、またはなしのキー ID を選択する](#choose-key-id-with-or-without-key-version)」と「[キーのバージョンを更新する](#update-key-version)」を参照してください。

## <a name="prerequisites"></a>前提条件

この記事の Azure CLI の手順を使用するには、Azure CLI バージョン 2.2.0 以降、または Azure Cloud Shell が必要です。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="enable-customer-managed-key---cli"></a>カスタマー マネージド キーを有効にする - CLI

### <a name="create-a-resource-group"></a>リソース グループを作成する

必要な場合は、[az group create][az-group-create] コマンドを実行して、キー コンテナー、コンテナー レジストリ、その他の必要なリソースを作成するためのリソース グループを作成します。

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する

[az identity create](../active-directory/managed-identities-azure-resources/overview.md) コマンドを使用して、ユーザー割り当てによる [Azure リソース用マネージド ID][az-identity-create] を作成します。 この ID は、Key Vault サービスにアクセスするためにレジストリによって使用されます。

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name>
```

コマンドの出力で、`id` と `principalId` の値を記録しておきます。 これらの値は、後の手順でキー コンテナーへのレジストリ アクセスを構成するために必要です。

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

利便性を考えて、これらの値を環境変数に格納します。

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Key Vault を作成します

[az keyvault create][az-keyvault-create] を使用して、レジストリ暗号化用のカスタマー マネージド キーを格納するためのキー コンテナーを作成します。 

既定では、新しいキー コンテナーの **[論理的な削除]** 設定が自動的に有効になっています。 キーまたはキー コンテナーを誤って削除することによるデータ損失を防ぐには、 **[消去保護]** 設定も有効にします。

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-purge-protection
```

後続の手順で使用するため、キー コンテナーのリソース ID を取得します。

```azurecli
keyvaultID=$(az keyvault show --resource-group <resource-group-name> --name <key-vault-name> --query 'id' --output tsv)
```

### <a name="enable-key-vault-access-by-trusted-services"></a>信頼されたサービスによるキー コンテナーへのアクセスを有効にする

キー コンテナーがファイアウォールまたは仮想ネットワーク (プライベート エンドポイント) で保護されている場合は、[信頼された Azure サービス](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)によるアクセスを許可するように、ネットワーク設定を有効にします。 

詳細については、「[Azure Key Vault のネットワーク設定を構成する](../key-vault/general/how-to-azure-key-vault-network-security.md?tabs=azure-cli)」を参照してください。


### <a name="enable-key-vault-access-by-managed-identity"></a>マネージド ID によるキー コンテナーへのアクセスを有効にする

#### <a name="enable-key-vault-access-policy"></a>キー コンテナーのアクセス ポリシーを有効にする

1 つのオプションは、ID でキー コンテナーにアクセスできるようにキー コンテナーのポリシーを構成することです。 次の [az keyvault set-policy][az-keyvault-set-policy] コマンドでは、前に作成して環境変数に格納したマネージ ID のプリンシパル ID を渡します。 キーのアクセス許可を **get**、**unwrapKey**、**wrapKey** に設定します。  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey

```
#### <a name="assign-rbac-role"></a>RBAC ロールを割り当てる

または、[Key Vault 用の Azure RBAC](../key-vault/general/rbac-guide.md) を使用して、キー コンテナーにアクセスするためのアクセス許可を ID に割り当てます。 たとえば、[az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) コマンドを使用して、Key Vault Crypto Service Encryption ロールを ID に割り当てます。

```azurecli 
az role assignment create --assignee $identityPrincipalID \
  --role "Key Vault Crypto Service Encryption User" \
  --scope $keyvaultID
```

### <a name="create-key-and-get-key-id"></a>キーを作成してキー ID を取得する

[az keyvault key create][az-keyvault-key-create] コマンドを実行して、キー コンテナーにキーを作成します。

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

コマンドの出力で、キーの ID `kid` を記録しておきます。 この ID は、次のステップで使用します。

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/<version>",
    "kty": "RSA",
[...]
```

### <a name="choose-key-id-with-or-without-key-version"></a>キーのバージョンあり、またはなしのキー ID を選択する

後で使用できるように、キー ID に選択した形式を $keyID 環境変数に格納します。 バージョンありのキー ID またはバージョンなしのキーを使用できます。

#### <a name="manual-key-rotation---key-id-with-version"></a>手動のキー ローテーション - バージョンありのキー ID

カスタマー マネージド キーを使用してレジストリを暗号化する場合、このキーにより、Azure Container Registry ではキーの手動ローテーションのみが許可されます。

次の例では、キーの `kid` プロパティを格納します。

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

#### <a name="automatic-key-rotation---key-id-omitting-version"></a>キーの自動ローテーション - バージョンを省略したキー ID 

カスタマー マネージド キーを使用してレジストリを暗号化する場合、このキーにより、Azure Key Vault で新しいキーのバージョンが検出されたときのキーの自動ローテーションが可能になります。

次の例では、キーの `kid` プロパティからバージョンを削除します。

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)

keyID=$(echo $keyID | sed -e "s/\/[^/]*$//")
```

### <a name="create-a-registry-with-customer-managed-key"></a>カスタマー マネージド キーを使用してレジストリを作成する

[az acr create][az-acr-create] コマンドを実行して Premium サービス レベルでレジストリを作成し、カスタマー マネージド キーを有効にします。 前に環境変数に格納したマネージド ID とキー ID を渡します。

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>暗号化の状態を表示する

カスタマー マネージド キーによるレジストリ暗号化が有効になっているかどうかを確認するには、[az acr encryption show][az-acr-encryption-show] コマンドを実行します。

```azurecli
az acr encryption show --name <registry-name>
```

レジストリの暗号化に使用されるキーに応じて、出力は次のようになります。

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "keyRotationEnabled": true,
    "lastKeyRotationTimestamp": xxxxxxxx
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
  },
  "status": "enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>カスタマー マネージド キーを有効にする - ポータル

### <a name="create-a-managed-identity"></a>マネージド ID の作成

Azure portal で、ユーザー割り当てによる [Azure リソース用マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を作成します。 手順については、「[ユーザー割り当て ID を作成する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)」を参照してください。

後の手順で ID の名前を使用します。

:::image type="content" source="media/container-registry-customer-managed-keys/create-managed-identity.png" alt-text="Azure portal でユーザー割り当て ID を作成する":::

### <a name="create-a-key-vault"></a>Key Vault を作成します

キー コンテナーを作成する手順については、「[クイックスタート: Azure portal を使用してキー コンテナーを作成する](../key-vault/general/quick-create-portal.md)」を参照してください。

カスタマー マネージド キー用のキー コンテナーを作成する場合は、 **[基本]** タブで、 **[消去保護]** 設定を有効にします。 この設定は、キーまたはキー コンテナーを誤って削除することによるデータ損失を防ぐのに役立ちます。

:::image type="content" source="media/container-registry-customer-managed-keys/create-key-vault.png" alt-text="Azure portal でキー コンテナーを作成する":::

### <a name="enable-key-vault-access-by-trusted-services"></a>信頼されたサービスによるキー コンテナーへのアクセスを有効にする

キー コンテナーがファイアウォールまたは仮想ネットワーク (プライベート エンドポイント) で保護されている場合は、[信頼された Azure サービス](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)によるアクセスを許可するように、ネットワーク設定を有効にします。 

詳細については、「[Azure Key Vault のネットワーク設定を構成する](../key-vault/general/how-to-azure-key-vault-network-security.md?tabs=azure-portal)」を参照してください。

### <a name="enable-key-vault-access-by-managed-identity"></a>マネージド ID によるキー コンテナーへのアクセスを有効にする

#### <a name="enable-key-vault-access-policy"></a>キー コンテナーのアクセス ポリシーを有効にする

1 つのオプションは、ID でキー コンテナーにアクセスできるようにキー コンテナーのポリシーを構成することです。

1. お使いのキー コンテナーに移動します。
1. **[設定]**  >  **[アクセス ポリシー] > [+ アクセス ポリシーの追加]** を選択します。
1. **[キーのアクセス許可]** を選択し、 **[取得]** 、 **[キーの折り返しを解除]** 、および **[キーを折り返す]** を選択します。
1. **[プリンシパルの選択]** で、ユーザー割り当てマネージド ID のリソース名を選択します。  
1. **[追加]** を選択し、 **[保存]** を選択します。

:::image type="content" source="media/container-registry-customer-managed-keys/add-key-vault-access-policy.png" alt-text="キー コンテナーのアクセス ポリシーを作成する":::

#### <a name="assign-rbac-role"></a>RBAC ロールを割り当てる    

または、Key Vault Crypto Service Encryption User ロールを、キー コンテナー スコープでユーザー割り当てマネージド ID に割り当てます。

詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」を参照してください。

### <a name="create-key-optional"></a>キーを作成する (省略可能)

必要に応じて、レジストリの暗号化に使用するキーをキー コンテナーに作成します。 特定のキー バージョンをカスタマー マネージド キーとして選択する場合は、これらの手順に従います。 キー コンテナーへのアクセスがプライベート エンドポイントまたは選択されたネットワークに制限されている場合は、レジストリを作成する前に、キーの作成も必要になる場合があります。 

1. お使いのキー コンテナーに移動します。
1. **[設定]**  >  **[キー]** の順に選択します。
1. **[+ 生成/インポート]** を選択し、キーの一意の名前を入力します。
1. それ以外については既定値をそのまま使用し、 **[作成]** を選択します。
1. 作成後、そのキーを選択し、次に現在のバージョンを選択します。 そのキー バージョンの **キー識別子** をコピーします。

### <a name="create-azure-container-registry"></a>Azure Container Registry の作成

1. **[リソースの作成]**  >  **[コンテナー]**  >  **[コンテナー レジストリ]** の順に選択します。
1. **[基本]** タブで、リソース グループを選択または作成し、レジストリ名を入力します。 **[SKU]** で **[Premium]** を選択します。
1. **[暗号化]** タブの **[顧客が管理するキー]** で、 **[有効]** を選択します。
1. **[ID]** で、作成したマネージド ID を選択します。
1. **[暗号化]** で、次のいずれかを選択します。
    * **[キー コンテナーから選ぶ]** を選択し、既存のキー コンテナーとキーを選択するか、 **[新規作成]** を選択します。 選択するキーはバージョン管理されておらず、キーの自動ローテーションが有効になります。
    * **[キー URI を入力]** を選択し、既存のキーの識別子を指定します。 バージョン管理されたキー URI (手動でローテーションする必要があるキーの場合) またはバージョン管理されていないキー URI (キーの自動ローテーションが有効になる) のいずれかを指定できます。 キーを作成する手順については、前のセクションを参照してください。
1. **[暗号化]** タブで、 **[確認と作成]** を選択します。
1. **[作成]** を選択して、レジストリ インスタンスをデプロイします。

:::image type="content" source="media/container-registry-customer-managed-keys/create-encrypted-registry.png" alt-text="Azure portal で、暗号化されたレジストリを作成する":::

ポータルでレジストリの暗号化状態を確認するには、お使いのレジストリに移動します。 **[設定]** で、 **[暗号化]** を選択します。

## <a name="enable-customer-managed-key---template"></a>カスタマー マネージド キーを有効にする - テンプレート

Resource Manager テンプレートを使用して、レジストリを作成し、カスタマー マネージド キーで暗号化を有効にすることもできます。

次のテンプレートでは、新しいコンテナー レジストリとユーザー割り当てのマネージド ID が作成されます。 次の内容を新しいファイルにコピーし、`CMKtemplate.json` などのファイル名を使用して保存します。

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}
```

前のセクションの手順に従って、次のリソースを作成します。

* キー コンテナー、名前で識別されます
* キー コンテナー キー、キー ID で識別されます

次の [az deployment group create][az-deployment-group-create] コマンドを実行し、前述のテンプレート ファイルを使用してレジストリを作成します。 要求されたら、新しいレジストリの名前とマネージド ID の名前、および作成したキー コンテナーの名前とキー ID を指定します。

```bash
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>暗号化の状態を表示する

レジストリ暗号化の状態を表示するには、[az acr encryption show][az-acr-encryption-show] コマンドを実行します。

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>レジストリを使用する

レジストリでカスタマー マネージド キーを有効にした後は、カスタマー マネージド キーで暗号化されていないレジストリで実行するのと同じレジストリ操作を実行できます。 たとえば、レジストリを使用して認証を行ったり、Docker イメージをプッシュしたりできます。 「[イメージのプッシュとプル](container-registry-get-started-docker-cli.md)」のコマンドの例をご覧ください。

## <a name="rotate-key"></a>キーをローテーションする

Azure Key Vault でキーのバージョンを更新するか、新しいキーを作成してから、そのキーを使用してデータを暗号化するようにレジストリを更新します。 これらの手順は、Azure CLI または Azure portal を使用して行うこともできます。

キーをローテーションする場合、通常はレジストリの作成時に使用したものと同じ ID を指定します。 必要に応じて、キー アクセス用に新しいユーザー割り当て ID を構成するか、レジストリのシステム割り当て ID を有効にして指定します。

> [!NOTE]
> * ポータルでレジストリのシステム割り当て ID を有効にするには、 **[設定]**  >  **[ID]** を選択し、システム割り当て ID の状態を **[オン]** に設定します。
> * キー アクセス用に構成する ID に対して、必要な[キー コンテナーのアクセス](#enable-key-vault-access-by-managed-identity)が設定されていることを確認してください。

### <a name="update-key-version"></a>キーのバージョンを更新する

一般的なシナリオでは、カスタマー マネージド キーとして使用されるキーのバージョンを更新します。 レジストリの暗号化の構成方法に応じて、Azure Container Registry のカスタマー マネージド キーは、自動的に更新される、または手動で更新する必要があります。

### <a name="azure-cli"></a>Azure CLI

[az keyvault key][az-keyvault-key] コマンドを使用して、キー コンテナー キーを作成または管理します。 新しいキーのバージョンを作成するには、[az keyvault key create][az-keyvault-key-create] コマンドを実行します。

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>
```

次の手順は、レジストリの暗号化の構成方法によって異なります。

* キーのバージョンの更新を検出するようにレジストリが構成されている場合は、カスタマー マネージド キーは 1 時間以内に自動的に更新されます。

* 新しいキーのバージョンの手動更新が必要になるようにレジストリが構成されている場合は、[az acr encryption rotate-key][az-acr-encryption-rotate-key] コマンドを新しいキー ID と構成する ID を渡して実行します。

カスタマー マネージド キーのバージョンを手動で更新するには、次のようにします。

```azurecli
# Rotate key and use user-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity <principal-id-user-assigned-identity>

# Rotate key and use system-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity [system]
```

> [!TIP]
> `az acr encryption rotate-key` を実行するとき、バージョンありのキー ID またはバージョンなしのキー ID のいずれかを渡すことができます。 バージョンなしのキー ID を使用すると、その後のキーのバージョンの更新を自動的に検出するようにレジストリが構成されます。

### <a name="portal"></a>ポータル

レジストリの **[暗号化]** 設定を使用して、カスタマー マネージド キーに使用されるキー コンテナー、キー、または ID 設定を更新します。

たとえば、新しいキーを構成するには、次のようにします。

1. ポータルで、レジストリに移動します。
1. **[設定]** で、 **[暗号化]**  >  **[キーの変更]** を選択します。

    :::image type="content" source="media/container-registry-customer-managed-keys/rotate-key.png" alt-text="Azure portal でのキーのローテーション":::
1. **[暗号化]** で、次のいずれかを選択します。
    * **[キー コンテナーから選ぶ]** を選択し、既存のキー コンテナーとキーを選択するか、 **[新規作成]** を選択します。 選択するキーはバージョン管理されておらず、キーの自動ローテーションが有効になります。
    * **[キー URI を入力する]** を選択し、キー識別子を直接指定します。 バージョン管理されたキー URI (手動でローテーションする必要があるキーの場合) またはバージョン管理されていないキー URI (キーの自動ローテーションが有効になる) のいずれかを指定できます。
1. キーの選択を完了し、 **[保存]** を選択します。

## <a name="revoke-key"></a>キーを取り消す

カスタマー マネージド暗号化キーを取り消すには、キー コンテナーのアクセス ポリシーまたはアクセス許可を変更するか、キーを削除します。 たとえば、レジストリによって使用されるマネージド ID のアクセス ポリシーを変更するには、[az keyvault delete-policy][az-keyvault-delete-policy] コマンドを使用します。

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

キーを取り消すと、レジストリは暗号化キーにアクセスできないため、すべてのレジストリ データへのアクセスが実質的にブロックされます。 キーへのアクセスを有効にするか、削除したキーを復元すると、レジストリによってキーが取得されるので、暗号化されたレジストリ データに再びアクセスできるようになります。 

## <a name="troubleshoot"></a>トラブルシューティング

### <a name="removing-managed-identity"></a>マネージド ID の削除


暗号化の構成に使用されるユーザー割り当てまたはシステム割り当てのマネージド ID をレジストリから削除しようとすると、次のようなエラー メッセージが表示されることがあります。
 
```
Azure resource '/subscriptions/xxxx/resourcegroups/myGroup/providers/Microsoft.ContainerRegistry/registries/myRegistry' does not have access to identity 'xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx' Try forcibly adding the identity to the registry <registry name>. For more information on bring your own key, please visit 'https://aka.ms/acr/cmk'.
```
 
暗号化キーを変更 (ローテーション) することもできません。 解決手順は、暗号化に使用される ID の種類によって異なります。

**ユーザー割り当て ID**

ユーザー割り当て ID でこの問題が発生した場合は、最初に [ac acr identity assign](/cli/azure/acr/identity/#az_acr_identity_assign) コマンドを使用して、ID の再割り当てを行います。 ID のリソース ID を渡すか、ID がレジストリと同じリソース グループにある場合は ID の名前を使用します。 次に例を示します。

```azurecli
az acr identity assign -n myRegistry \
    --identities "/subscriptions/mysubscription/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentity"
```
        
その後、キーを変更して異なる ID を割り当てれば、元のユーザー割り当て ID を削除することができます。

**システム割り当て ID**

システム割り当て ID でこの問題が発生した場合は、[Azure サポート チケットを作成](https://azure.microsoft.com/support/create-ticket/)して ID の復元の支援を求めてください。

### <a name="enabling-key-vault-firewall"></a>キー コンテナー ファイアウォールを有効にする

暗号化されたレジストリを作成した後でキー コンテナー ファイアウォールまたは仮想ネットワークを有効にすると、イメージのインポートまたはキーの自動ローテーションで HTTP 403 などのエラーが発生する場合があります。 この問題を解決するには、最初に暗号化に使用したマネージ ID とキーを再構成します。 「[キーをローテーションする](#rotate-key)」の手順を参照してください。 

問題が解決しない場合は、Azure サポートにお問い合わせください。

### <a name="accidental-deletion-of-key-vault-or-key"></a>キー コンテナーまたはキーの誤削除

カスタマー マネージド キーによるレジストリの暗号化に使用されるキー コンテナーまたはキーが削除されると、レジストリの内容にアクセスできなくなります。 キー コンテナーで[論理的な削除](../key-vault/general/soft-delete-overview.md)が有効になっている場合 (既定のオプション)、削除されたコンテナーまたはキー コンテナー オブジェクトを回復して、レジストリ操作を再開することができます。

キー コンテナーの削除と回復のシナリオについては、「[論理的な削除と消去保護を使用した Azure Key Vault の回復の管理](../key-vault/general/key-vault-recovery.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure での保存時の暗号化](../security/fundamentals/encryption-atrest.md)についてさらに学習します。
* アクセス ポリシーと、[キー コンテナーへのアクセスをセキュリティで保護する](../key-vault/general/security-features.md)方法についてさらに学習します。


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-show]: /cli/azure/feature#az_feature_show
[az-group-create]: /cli/azure/group#az_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-keyvault-create]: /cli/azure/keyvault#az_keyvault_create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az_keyvault_key_create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az_keyvault_set_policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az_keyvault_delete_policy
[az-resource-show]: /cli/azure/resource#az_resource_show
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az_acr_encryption_rotate_key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az_acr_encryption_show
