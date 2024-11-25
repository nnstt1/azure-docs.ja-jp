---
title: 最新の API を使用してプログラムで Microsoft 顧客契約の Azure サブスクリプションを作成する
description: 最新バージョンの REST API、Azure CLI、Azure PowerShell、および Azure Resource Manager テンプレートを使用して、プログラムによって Microsoft 顧客契約の Azure サブスクリプションを作成する方法について説明します。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 09/01/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: ea619824223e5f424b7a2edd88c9adb826ea7927
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2021
ms.locfileid: "123478754"
---
# <a name="programmatically-create-azure-subscriptions-for-a-microsoft-customer-agreement-with-the-latest-apis"></a>最新の API を使用してプログラムで Microsoft 顧客契約の Azure サブスクリプションを作成する

この記事は、最新の API バージョンを使用してプログラムで Microsoft 顧客契約の Azure サブスクリプションを作成する場合に役に立ちます。 古いプレビュー バージョンをまだ使用している場合は、「[レガシ API を使用してプログラムで Azure サブスクリプションを作成する](programmatically-create-subscription-preview.md)」を参照してください。 

この記事では、Azure Resource Manager を使用してプログラムからサブスクリプションを作成する方法について説明します。

プログラムで Azure サブスクリプションを作成した場合、そのサブスクリプションには、お客様が Microsoft または正規の販売代理店と締結した Azure サービスの契約が適用されます。 詳細については、「[Microsoft Azure の法的情報](https://azure.microsoft.com/support/legal/)」を参照してください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

サブスクリプションを作成するには、請求書セクションで所有者、共同作成者、または Azure サブスクリプション作成者のロールを持っているか、課金プロファイルまたは課金アカウントで所有者または共同作成者のロールを持っている必要があります。 また、同じロールをサービス プリンシパル名 (SPN) に割り当てることもできます。 ロールとロールへのアクセス許可の割り当ての詳細については、「[サブスクリプションの課金ロールとタスク](understand-mca-roles.md#subscription-billing-roles-and-tasks)」を参照してください。

SPN を使用してサブスクリプションを作成している場合は [Azure Active Directory PowerShell](/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0&preserve-view=true) または [Azure CLI](/cli/azure/ad/sp?view=azure-cli-latest&preserve-view=true#az_ad_sp_list) を使用して、Azure AD アプリケーション登録の ObjectId をサービス プリンシパルの ObjectId として使用します。 

Microsoft 顧客契約アカウントへのアクセス権があるかどうかわからない場合は、「[Microsoft 顧客契約へのアクセスの確認](../understand/mca-overview.md#check-access-to-a-microsoft-customer-agreement)」のセクションを参照してください。

## <a name="find-billing-accounts-that-you-have-access-to"></a>アクセスできる課金アカウントを検索する

すべての課金アカウントの一覧を表示するには、次の要求を行います。

### <a name="rest"></a>[REST](#tab/rest)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01

```
API 応答で、自分がアクセスできる課金アカウントが一覧表示されます。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

`displayName` プロパティを使用して、サブスクリプションを作成する課金アカウントを指定します。 アカウントの agreementType が *MicrosoftCustomerAgreement* であることを確認します。 アカウントの `name` をコピーします。  たとえば、`Contoso` 課金アカウントのサブスクリプションを作成するには、`5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` をコピーします。 次の手順で使用できるように、値をどこかに貼り付けておきます。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzBillingAccount
```
アクセスできる課金アカウントの一覧が返されます 

```json
Name          : 5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx
DisplayName   : Contoso
AccountStatus : Active
AccountType   : Enterprise
AgreementType : MicrosoftCustomerAgreement
HasReadAccess : True
```
`displayName` プロパティを使用して、サブスクリプションを作成する課金アカウントを指定します。 アカウントの agreementType が *MicrosoftCustomerAgreement* であることを確認します。 アカウントの `name` をコピーします。  たとえば、`Contoso` 課金アカウントのサブスクリプションを作成するには、`5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` をコピーします。 次の手順で使用できるように、値をどこかに貼り付けておきます。


### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az billing account list
```
アクセスできる課金アカウントの一覧が返されます 

```json
[
  {
    "accountStatus": "Active",
    "accountType": "Enterprise",
    "agreementType": "MicrosoftCustomerAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": null,
    "enrollmentDetails": null,
    "hasReadAccess": true,
    "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "soldTo": null,
    "type": "Microsoft.Billing/billingAccounts"
  }
]
```

`displayName` プロパティを使用して、サブスクリプションを作成する課金アカウントを指定します。 アカウントの agreementType が *MicrosoftCustomerAgreement* であることを確認します。 アカウントの `name` をコピーします。  たとえば、`Contoso` 課金アカウントのサブスクリプションを作成するには、`5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` をコピーします。 次の手順で使用できるように、値をどこかに貼り付けておきます。

---

## <a name="find-billing-profiles--invoice-sections-to-create-subscriptions"></a>サブスクリプションを作成するための課金プロファイルと請求書のセクションを検索する

お使いのサブスクリプションの料金は、課金プロファイルの請求書のセクションに表示されます。 次の API を使用して、Azure サブスクリプションを作成するためのアクセス許可を自分が持っている、課金プロファイルと請求書のセクションの一覧を取得します。

まず、アクセスできる課金アカウントの課金プロファイルの一覧を取得します (1 つ前の手順で取得した `name` を使用してください)。

### <a name="rest"></a>[REST](#tab/rest)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingprofiles/?api-version=2020-05-01
```
API の応答には、サブスクリプションを作成するためのアクセス権が自分にあるすべての課金プロファイルの一覧が含まれます。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx",
      "name": "AW4F-xxxx-xxx-xxx",
      "properties": {
        "billingRelationshipType": "Direct",
        "billTo": {
          "addressLine1": "One Microsoft Way",
          "city": "Redmond",
          "companyName": "Contoso",
          "country": "US",
          "email": "kenny@contoso.com",
          "phoneNumber": "425xxxxxxx",
          "postalCode": "98052",
          "region": "WA"
        },
        "currency": "USD",
        "displayName": "Contoso Billing Profile",
        "enabledAzurePlans": [
          {
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
          },
          {
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
          }
        ],
        "hasReadAccess": true,
        "invoiceDay": 5,
        "invoiceEmailOptIn": false,
        "invoiceSections": {
          "hasMoreResults": false
        },
        "poNumber": "001",
        "spendingLimit": "Off",
        "status": "Active",
        "systemId": "AW4F-xxxx-xxx-xxx",
        "targetClouds": []
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles"
    }
  ]
}
```

 次に請求プロファイルで請求書セクションを示すために、`id` をコピーします。 たとえば、`/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx` をコピーして、次の API を呼び出します。

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoicesections?api-version=2020-05-01
```
### <a name="response"></a>Response

```json
{
  "totalCount": 1,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
      "name": "SH3V-xxxx-xxx-xxx",
      "properties": {
        "displayName": "Development",
        "state": "Active",
        "systemId": "SH3V-xxxx-xxx-xxx"
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections"
    }
  ]
}
```

`id` プロパティを使用して、サブスクリプションを作成する請求書セクションを指定します。 文字列全体をコピーします。 たとえば、`/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx` のようにします。 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzBillingProfile -BillingAccountName 5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx
```

応答の一環として、このアカウントの課金プロファイルの一覧が返されます。

```json
Name              : AW4F-xxxx-xxx-xxx
DisplayName       : Contoso Billing Profile
Currency          : USD
InvoiceDay        : 5
InvoiceEmailOptIn : True
SpendingLimit     : Off
Status            : Active
EnabledAzurePlans : {0002, 0001}
HasReadAccess     : True
BillTo            :
CompanyName       : Contoso
AddressLine1      : One Microsoft Way
AddressLine2      : 
City              : Redmond
Region            : WA
Country           : US
PostalCode        : 98052
```

上の応答から、課金プロファイルの `name` をメモしておいてください。 次の手順では、この課金プロファイルの下でアクセスできる請求書セクションを取得します。 課金プロファイルと課金アカウントの `name` が必要になります。

```azurepowershell
Get-AzInvoiceSection -BillingAccountName 5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx -BillingProfileName AW4F-xxxx-xxx-xxx
```

請求書セクションが返されます。

```json
Name        : SH3V-xxxx-xxx-xxx
DisplayName : Development
```

上の `name` は請求書セクションの名前で、これを基にサブスクリプションを作成する必要があります。 形式 `/providers/Microsoft.Billing/billingAccounts/<BillingAccountName>/billingProfiles/<BillingProfileName>/invoiceSections/<InvoiceSectionName>` を使用して、課金範囲を構築します。 この例では、この値は `"/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"` に等しくなります。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az billing profile list --account-name "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx" --expand "InvoiceSections"
```

この API では、指定された課金アカウントの課金プロファイルと請求書セクションの一覧が返されます。

```json
[
  {
    "billTo": {
      "addressLine1": "One Microsoft Way",
      "addressLine2": "",
      "addressLine3": null,
      "city": "Redmond",
      "companyName": "Contoso",
      "country": "US",
      "district": null,
      "email": null,
      "firstName": null,
      "lastName": null,
      "phoneNumber": null,
      "postalCode": "98052",
      "region": "WA"
    },
    "billingRelationshipType": "Direct",
    "currency": "USD",
    "displayName": "Contoso Billing Profile",
    "enabledAzurePlans": [
      {
        "skuDescription": "Microsoft Azure Plan for DevTest",
        "skuId": "0002"
      },
      {
        "skuDescription": "Microsoft Azure Plan",
        "skuId": "0001"
      }
    ],
    "hasReadAccess": true,
    "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx",
    "indirectRelationshipInfo": null,
    "invoiceDay": 5,
    "invoiceEmailOptIn": true,
    "invoiceSections": {
      "hasMoreResults": false,
      "value": [
        {
          "displayName": "Field_Led_Test_Ace",
          "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
          "labels": null,
          "name": "SH3V-xxxx-xxx-xxx",
          "state": "Active",
          "systemId": "SH3V-xxxx-xxx-xxx",
          "targetCloud": null,
          "type": "Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections"
        }
      ]
    },
    "name": "AW4F-xxxx-xxx-xxx",
    "poNumber": null,
    "spendingLimit": "Off",
    "status": "Warned",
    "statusReasonCode": "PastDue",
    "systemId": "AW4F-xxxx-xxx-xxx",
    "targetClouds": [],
    "type": "Microsoft.Billing/billingAccounts/billingProfiles"
  }
]
```
請求書セクション オブジェクトの `id` プロパティを使用して、サブスクリプションを作成する請求書セクションを特定します。 文字列全体をコピーします。 たとえば、/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx です。

---

## <a name="create-a-subscription-for-an-invoice-section"></a>請求書セクションのサブスクリプションを作成する

次の例では、*Development* 請求書セクションに対して *Dev Team subscription* という名前のサブスクリプションが作成されます。 サブスクリプションは *Contoso Billing Profile* 課金プロファイルに対して請求され、請求書の *Development* セクションに表示されます。 前の手順でコピーした課金スコープ `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx` を使用します。 

### <a name="rest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="request-body"></a>要求本文

```json
{
  "properties":
    {
        "billingScope": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
        "DisplayName": "Dev Team subscription",
        "Workload": "Production"
    }
}
```

### <a name="response"></a>Response

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Accepted"
  }
}
```

同じ URL で GET を実行して、要求の状態を取得できます。

### <a name="request"></a>Request

```json
GET https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="response"></a>Response

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Succeeded"
  }
}
```

進行中の状態は、`provisioningState` で `Accepted` 状態として返されます。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

`New-AzSubscriptionAlias` コマンドレットを含む最新バージョンのモジュールをインストールするには、`Install-Module Az.Subscription` を実行します。 PowerShellGet の最新バージョンをインストールするには、[PowerShellGet モジュールの取得](/powershell/scripting/gallery/installing-psget)に関するページを参照してください。

次の [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscriptionalias) コマンドと課金スコープ `"/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"` を実行します。 

```azurepowershell
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx" -Workload "Production"
```

コマンドからの応答の一部として、subscriptionId を取得します。

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

まず、`az extension add --name account` と `az extension add --name alias` を実行して、拡張機能をインストールします。

次の [az account alias create](/cli/azure/account/alias#az_account_alias_create) コマンドを実行します。

```azurecli
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx" --display-name "Dev Team Subscription" --workload "Production"
```

コマンドからの応答の一部として、subscriptionId を取得します。

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

---

## <a name="use-arm-template-or-bicep"></a>ARM テンプレートまたは Bicep を使用する

前のセクションでは、PowerShell、CLI、または REST API を使用してサブスクリプションを作成する方法を説明しました。 サブスクリプションの作成を自動化する必要がある場合は、Azure Resource Manager テンプレート (ARM テンプレート) か [Bicep ファイル](../../azure-resource-manager/bicep/overview.md)の使用をご検討ください。

次のテンプレートを使用すると、サブスクリプションを作成できます。 `billingScope` には、請求書セクション ID を指定します。 サブスクリプションはルート管理グループに作成されます。 サブスクリプションを作成した後、それを別の管理グループに移動できます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionAliasName": {
            "type": "string",
            "metadata": {
                "description": "Provide a name for the alias. This name will also be the display name of the subscription."
            }
        },
        "billingScope": {
            "type": "string",
            "metadata": {
                "description": "Provide the full resource ID of billing scope to use for subscription creation."
            }
        }
    },
    "resources": [
        {
            "scope": "/", 
            "name": "[parameters('subscriptionAliasName')]",
            "type": "Microsoft.Subscription/aliases",
            "apiVersion": "2020-09-01",
            "properties": {
                "workLoad": "Production",
                "displayName": "[parameters('subscriptionAliasName')]",
                "billingScope": "[parameters('billingScope')]"
            }
        }
    ],
    "outputs": {}
}
```

または、Bicep ファイルを使用してサブスクリプションを作成します。

```bicep
targetScope = 'managementGroup'

@description('Provide a name for the alias. This name will also be the display name of the subscription.')
param subscriptionAliasName string

@description('Provide the full resource ID of billing scope to use for subscription creation.')
param billingScope string

resource subscriptionAlias 'Microsoft.Subscription/aliases@2020-09-01' = {
  scope: tenant()
  name: subscriptionAliasName
  properties: {
    workload: 'Production'
    displayName: subscriptionAliasName
    billingScope: billingScope
  }
}
```

テンプレートを[管理グループ レベル](../../azure-resource-manager/templates/deploy-to-management-group.md)でデプロイします。 次の例では、JSON ARM テンプレートのデプロイを示しますが、代わりに Bicep ファイルをデプロイすることもできます。

### <a name="rest"></a>[REST](#tab/rest)

```json
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/mg1/providers/Microsoft.Resources/deployments/exampledeployment?api-version=2020-06-01
```

要求本文:

```json
{
  "location": "eastus",
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json"
    },
    "parameters": {
      "subscriptionAliasName": {
        "value": "sampleAlias"
      },
      "billingScope": {
        "value": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"
      }
    },
    "mode": "Incremental"
  }
}
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzManagementGroupDeployment `
  -Name exampledeployment `
  -Location eastus `
  -ManagementGroupId mg1 `
  -TemplateFile azuredeploy.json `
  -subscriptionAliasName sampleAlias `
  -billingScope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment mg create \
  --name exampledeployment \
  --location eastus \
  --management-group-id mg1 \
  --template-file azuredeploy.json \
  --parameters subscriptionAliasName='sampleAlias' billingScope='/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx'
```

---

サブスクリプションを新しい管理グループに移動するには、次の ARM テンプレートを使用します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMgId": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the management group that you want to move the subscription to."
            }
        },
        "subscriptionId": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the existing subscription to move."
            }
        }
    },
    "resources": [
        {
            "scope": "/",
            "type": "Microsoft.Management/managementGroups/subscriptions",
            "apiVersion": "2020-05-01",
            "name": "[concat(parameters('targetMgId'), '/', parameters('subscriptionId'))]",
            "properties": {
            }
        }
    ],
    "outputs": {}
}
```

または、次の Bicep ファイルを使用します。

```bicep
targetScope = 'managementGroup'

@description('Provide the ID of the management group that you want to move the subscription to.')
param targetMgId string

@description('Provide the ID of the existing subscription to move.')
param subscriptionId string

resource subToMG 'Microsoft.Management/managementGroups/subscriptions@2020-05-01' = {
  scope: tenant()
  name: '${targetMgId}/${subscriptionId}'
}
```

## <a name="next-steps"></a>次のステップ

* サブスクリプションを作成し終えたら、他のユーザーおよびサービス プリンシパルでその機能を利用できるようになります。 詳細については、「[Azure Enterprise サブスクリプションを作成する権限を付与する (プレビュー)](grant-access-to-create-subscription.md)」を参照してください。
* 管理グループを使用して大量のサブスクリプションを管理する方法の詳細については、[Azure 管理グループによるリソースの整理](../../governance/management-groups/overview.md)に関する記事を参照してください。
* サブスクリプションの管理グループを変更するには、「[サブスクリプションの移動](../../governance/management-groups/manage.md#move-subscriptions)」を参照してください。
