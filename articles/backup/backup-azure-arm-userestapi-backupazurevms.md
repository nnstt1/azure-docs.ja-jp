---
title: REST API を使用して Azure VM をバックアップする
description: この記事では、REST API を使用して Azure VM Backup のバックアップ操作を構成、開始、および管理する方法について説明します。
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 57187a9f7ecf3e1d00fa395d25d98fd03f5d2698
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114461025"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>REST API を通して Azure Backup を使用して Azure VM をバックアップする

この記事では、REST API を通して Azure Backup を使用し、Azure VM のバックアップを管理する方法について説明します。 ここで説明するように REST API を通して、以前に保護されていない Azure VM の保護を初めて構成し、保護された Azure VM に対してオンデマンド バックアップをトリガーして、バックアップされた VM のバックアップ プロパティを変更します。

新しいコンテナーやポリシーを作成する場合は、[コンテナーの作成](backup-azure-arm-userestapi-createorupdatevault.md)と[ポリシーの作成する](backup-azure-arm-userestapi-createorupdatepolicy.md)に関する REST API のチュートリアルを参照してください。

既定のポリシー (名前は "DefaultPolicy") を使用し、リソース グループ "testVaultRG" 内に存在する Recovery Services コンテナー "testVault" に対して、リソース グループ "testRG" の "testVM" という VM を保護すると仮定します。

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>REST API を使用して保護されていない Azure VM のバックアップを構成する

### <a name="discover-unprotected-azure-vms"></a>保護されていない Azure VM を検出する

最初に、コンテナーが Azure VM を識別できるようにする必要があります。 これは、[refresh 操作](/rest/api/backup/protection-containers/refresh)を使用してトリガーされます。 これは非同期の *POST* 操作であり、これにより、コンテナーが現在のサブスクリプション内で保護されていない全ての VM の最新の一覧を取得して、それらを "キャッシュ" することが確実に実行されます。 VM が "キャッシュ" されると、Recovery Services は VM にアクセスし、それを保護できるようになります。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

POST URI には、パラメーターとして `{subscriptionId}`、`{vaultName}`、`{vaultresourceGroupName}`、`{fabricName}` があります。 `{fabricName}` は "Azure" です。 この例に従えば、`{vaultName}` は "testVault" で、`{vaultresourceGroupName}` は "testVaultRG"です。 すべての必須パラメーターは URI で指定されるため、別の要求本文は不要です。

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses-to-refresh-operation"></a>更新操作に対する応答

"refresh" 操作は[非同期操作](../azure-resource-manager/management/async-operations.md)です。 つまり、この操作では、個別に追跡する必要がある別の操作が作成されます。

これにより、2 つの応答が返されます。別の操作が作成されたときは 202 (Accepted)、その操作が完了したときは 200 (OK) です。

|名前  |型  |説明  |
|---------|---------|---------|
|204 No Content     |         |  OK で、返されたコンテンツはありません      |
|202 Accepted     |         |     承認済み    |

##### <a name="example-responses-to-refresh-operation"></a>更新操作に対する応答の例

*POST* 要求が送信されると、202 (Accepted) 応答が返されます。

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
X-Content-Type-Options: nosniff
x-ms-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-client-request-id: 4910609f-bb9b-4c23-8527-eb6fa2d3253f; 4910609f-bb9b-4c23-8527-eb6fa2d3253f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-routing-request-id: SOUTHINDIA:20180521T105701Z:43cf550d-e463-421c-8922-37e4766db27d
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:57:00 GMT
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
X-Powered-By: ASP.NET
```

簡単な *GET* コマンドで "Location" ヘッダーを使用して結果の操作を追跡します

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
```

すべての Azure VM が検出されると、GET コマンドは 204 (No Content) 応答を返します。 コンテナーはこれで、サブスクリプション内のすべての VM を検出できるようになりました。

```http
HTTP/1.1 204 NoContent
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-client-request-id: 25bb6345-f9fc-4406-be1a-dc6db0eefafe; 25bb6345-f9fc-4406-be1a-dc6db0eefafe
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14997
x-ms-correlation-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-routing-request-id: SOUTHINDIA:20180521T105825Z:cf6cd73b-9189-4942-a61d-878fcf76b1c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:58:25 GMT
X-Powered-By: ASP.NET
```

### <a name="selecting-the-relevant-azure-vm"></a>関連する Azure VM の選択

 サブスクリプションの下で[保護可能なすべての項目を一覧表示](/rest/api/backup/backup-protectable-items/list)し、応答内で目的の VM を見つけることで、"キャッシュ処理" が行われることを確認できます。 [この操作の応答](#example-responses-to-get-operation)には、Recovery Services が VM を識別する方法に関する情報も示されます。  このパターンに精通したら、この手順をスキップし、[保護の有効化](#enabling-protection-for-the-azure-vm)に直接進むことができます。

この操作は *GET* 操作です。

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

*GET* URI には、すべての必須パラメーターが含まれます。 追加の要求本文は必要ありません。

#### <a name="responses-to-get-operation"></a>取得操作に対する応答

|名前  |型  |説明  |
|---------|---------|---------|
|200 OK     | [WorkloadProtectableItemResourceList](/rest/api/backup/backup-protectable-items/list#workloadprotectableitemresourcelist)        |       [OK] |

#### <a name="example-responses-to-get-operation"></a>取得操作に対する応答の例

*GET* 要求を送信すると、200 (OK) 応答が返されます。

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-client-request-id: 40c8601a-c217-4c68-87da-01db8dac93dd; 40c8601a-c217-4c68-87da-01db8dac93dd
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14979
x-ms-correlation-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-routing-request-id: SOUTHINDIA:20180521T071408Z:7c2cf56a-e6be-4345-96df-c27ed849fe36
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:14:08 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG;testVM/protectableItems/vm;iaasvmcontainerv2;testRG;testVM",
      "name": "iaasvmcontainerv2;testRG;testVM",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
      "properties": {
        "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "virtualMachineVersion": "Compute",
        "resourceGroup": "testRG",
        "backupManagementType": "AzureIaasVM",
        "protectableItemType": "Microsoft.Compute/virtualMachines",
        "friendlyName": "testVM",
        "protectionState": "NotProtected"
      }
    },……………..

```

> [!TIP]
> *GET* 応答に含まれる値の数の上限は、1 "ページ" あたり 200 です。 "nextLink" フィールドを使用して、次の応答セットの URL を取得します。

応答には保護されていないすべての Azure VM の一覧が含まれていて、各 `{value}` には、Azure Recovery サービスがバックアップを構成するために必要なすべての情報が含まれています。 バックアップを構成するには、`{properties}` セクションの `{name}` フィールドと `{virtualMachineId}` フィールドをメモしてください。 下記のように、これらのフィールドの値から 2 つの変数を作成します。

- containerName = "iaasvmcontainer;"+`{name}`
- protectedItemName = "vm;"+ `{name}`
- `{virtualMachineId}` は後で、[要求本文](#example-request-body)で使用されます

この例では、上の値を以下のように変えます。

- containerName = "iaasvmcontainer;iaasvmcontainerv2;testRG;testVM"
- protectedItemName = "vm;iaasvmcontainerv2;testRG;testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Azure VM の保護の有効化

適切な VM の "キャッシュ" と "識別" が行われたら、保護するポリシーを選択します。 コンテナー内にある既存のポリシーの詳細について知るには、[Policy API の list](/rest/api/backup/backup-policies/list) を参照してください。 次に、ポリシー名を参照して、[適切なポリシー](/rest/api/backup/protection-policies/get)を選択します。 ポリシーを作成するには、[ポリシーの作成に関するチュートリアル](backup-azure-arm-userestapi-createorupdatepolicy.md)を参照してください。 以下の例では "DefaultPolicy" が選択されています。

保護を有効にすることは、"保護された項目" を作成する非同期の *PUT* 操作です。

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

`{containerName}` と `{protectedItemName}` は、上のように作成されます。 `{fabricName}` は "Azure" です。 この例では、これを次のように変えます。

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>要求本文を作成する

保護された項目を作成する場合、要求本文のコンポーネントは次のようになります。

|名前  |型  |説明  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |ProtectedItem リソースのプロパティ         |

要求本文の定義の完全な一覧およびその他の詳細については、[保護された項目の作成に関する REST API ドキュメント](/rest/api/backup/protected-items/create-or-update#request-body)を参照してください。

##### <a name="example-request-body"></a>要求本文の例

次の要求本文では、保護された項目の作成に必要なプロパティを定義しています。

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

`{sourceResourceId}` は、上述の、[保護可能な項目の一覧表示の応答](#example-responses-to-get-operation)からの `{virtualMachineId}` です。

#### <a name="responses-to-create-protected-item-operation"></a>保護された項目の作成操作に対する応答

保護された項目の作成は、[非同期操作](../azure-resource-manager/management/async-operations.md)です。 つまり、この操作では、個別に追跡する必要がある別の操作が作成されます。

これにより、2 つの応答が返されます。別の操作が作成されたときは 202 (Accepted)、その操作が完了したときは 200 (OK) です。

|名前  |型  |説明  |
|---------|---------|---------|
|200 OK     |    [ProtectedItemResource](/rest/api/backup/protected-item-operation-results/get#protecteditemresource)     |  [OK]       |
|202 Accepted     |         |     承認済み    |

##### <a name="example-responses-to-create-protected-item-operation"></a>保護された項目の作成操作に対する応答の例

保護された項目の作成または更新のための *PUT* 要求を送信したときの最初の応答は、場所のヘッダーまたは Azure-async-header を含む 202 (Accepted) です。

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

その後は、場所ヘッダーまたは Azure-AsyncOperation ヘッダーを使用して簡単な *GET* コマンドで結果の操作を追跡します。

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

操作が完了すると、応答本文に保護された項目のコンテンツが含まれる 200 (OK) が返されます。

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM",
  "name": "VM;testRG;testVM",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems",
  "properties": {
    "friendlyName": "testVM",
    "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "protectionStatus": "Healthy",
    "protectionState": "IRPending",
    "healthStatus": "Passed",
    "lastBackupStatus": "",
    "lastBackupTime": "2001-01-01T00:00:00Z",
    "protectedItemDataId": "17592691116891",
    "extendedInfo": {
      "recoveryPointCount": 0,
      "policyInconsistent": false
    },
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "backupManagementType": "AzureIaasVM",
    "workloadType": "VM",
    "containerName": "iaasvmcontainerv2;testRG;testVM",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "policyName": "DefaultPolicy"
  }
}
```

これにより、VM の保護が有効になっており、ポリシー スケジュールに従って最初のバックアップがトリガーされることが確認されます。

### <a name="excluding-disks-in-azure-vm-backup"></a>Azure VM のバックアップでのディスクの除外

Azure Backup には、Azure VM 内のディスクのサブセットを選択的にバックアップする方法も用意されています。 詳細については、[こちら](selective-disk-backup-restore.md)をご覧ください。 保護の有効化時に、いくつかのディスクを選択的にバックアップする場合は、次のコード スニペットを、[保護の有効化時の要求本文](#example-request-body)としてください。

```json
{
"properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "extendedProperties":  {
      "diskExclusionProperties":{
          "diskLunList":[0,1],
          "isInclusionList":true
        }
    }
}
}
```

上記の要求本文では、バックアップするディスクの一覧は、extended properties セクションに指定されています。

|プロパティ  |値  |
|---------|---------|
|diskLunList     | ディスク LUN 一覧は、"*データ ディスクの LUN*" の一覧です。 **OS ディスクは常にバックアップされているため、監視する必要はありません**。        |
|IsInclusionList     | バックアップ中に LUN を含めるには、**true** にする必要があります。 **false** の場合、前述の LUN は除外されます。         |

したがって、OS ディスクのみをバックアップする必要がある場合は、"_すべての_" データ ディスクを除外する必要があります。 もっと簡単に言うと、どのデータ ディスクも含める必要がないということです。 この結果、ディスク LUN 一覧は空になり、**IsInclusionList** は **true** になります。 同様に、サブセットを選択するための簡単な方法を考えてみましょう。一部のディスクを常に除外する必要がある、またな常に含める必要があるとします。 それに応じて、LUN 一覧とブール型変数値を選択してください。

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>保護された Azure VM のオンデマンド バックアップをトリガーする

Azure VM のバックアップの構成が済むと、ポリシーのスケジュールに従ってバックアップが行われます。 スケジュールされた最初のバックアップを待機できます。または、いつでもオンデマンド バックアップをトリガーできます。 オンデマンド バックアップの保持期間は、バックアップ ポリシーの保持期間から独立していて、特定の日付と時刻を指定できます。 指定されていない場合、オンデマンド バックアップをトリガーした日から 30 日と見なされます。

オンデマンド バックアップをトリガーすることは、*POST* 操作です。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

`{containerName}` と `{protectedItemName}` は、[上](#responses-to-get-operation)のように作成されます。 `{fabricName}` は "Azure" です。 この例では、これを次のように変えます。

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body-for-on-demand-backup"></a>オンデマンド バックアップの要求本文を作成する

オンデマンド バックアップをトリガーする場合、要求本文のコンポーネントは次のようになります。

|名前  |型  |説明  |
|---------|---------|---------|
|properties     | [IaaSVMBackupRequest](/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |BackupRequestResource プロパティ         |

要求本文の定義の完全な一覧およびその他の詳細については、[保護された項目のバックアップをトリガーすることに関する REST API ドキュメント](/rest/api/backup/backups/trigger#request-body)を参照してください。

#### <a name="example-request-body-for-on-demand-backup"></a>オンデマンド バックアップの要求本文の例

次の要求本文では、保護された項目のバックアップをトリガーするのに必要なプロパティを定義しています。 保持期間が指定されていない場合は、バックアップ ジョブをトリガーしたときから 30 日間保持されます。

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses-for-on-demand-backup"></a>オンデマンドバックアップの応答

オンデマンド バックアップのトリガーは[非同期操作](../azure-resource-manager/management/async-operations.md)として扱われます。 つまり、この操作では、個別に追跡する必要がある別の操作が作成されます。

これにより、2 つの応答が返されます。別の操作が作成されたときは 202 (Accepted)、その操作が完了したときは 200 (OK) です。

|名前  |型  |説明  |
|---------|---------|---------|
|202 Accepted     |         |     承認済み    |

#### <a name="example-responses-for-on-demand-backup"></a>オンデマンドバックアップの応答の例

オンデマンド バックアップの *POST* 要求を送信したときの最初の応答は、場所のヘッダーまたは Azure-async-header を含む 202 (Accepted) です。

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-client-request-id: 7df8e874-1d66-4f81-8e91-da2fe054811d; 7df8e874-1d66-4f81-8e91-da2fe054811d
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-routing-request-id: SOUTHINDIA:20180521T083541Z:7885ca75-c7c6-43fb-a38c-c0cc437d8810
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:35:41 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

その後は、場所ヘッダーまたは Azure-AsyncOperation ヘッダーを使用して簡単な *GET* コマンドで結果の操作を追跡します。

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

操作が完了すると、応答本文に結果のバックアップ ジョブの ID が含まれる 200 (OK) が返されます。

```json
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-client-request-id: 5a63209d-3708-4e69-a75f-9499f4c8977c; 5a63209d-3708-4e69-a75f-9499f4c8977c
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14995
x-ms-correlation-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-routing-request-id: SOUTHINDIA:20180521T083723Z:a8b13524-2c95-445f-b107-920806f385c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:37:22 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "00000000-0000-0000-0000-000000000000",
  "status": "Succeeded",
  "startTime": "2018-05-21T08:35:40.9488967Z",
  "endTime": "2018-05-21T08:35:40.9488967Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "7ddead57-bcb9-4269-ac31-6a1b57588700"
  }
}
```

バックアップ ジョブは実行時間が長い操作のため、[REST API を使用したジョブの監視に関するドキュメント](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)で説明されているように、ジョブを追跡する必要があります。

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>保護されている Azure VM のバックアップ構成を変更する

### <a name="changing-the-policy-of-protection"></a>保護のポリシーの変更

VM の保護に使用されているポリシーを変更するために、[保護を有効にする](#enabling-protection-for-the-azure-vm)のと同じ形式を使用できます。 [要求本文](#example-request-body)で新しいポリシー ID を指定し、要求を送信するだけです。 次に例を示します。testVM のポリシーを 'DefaultPolicy' から 'ProdPolicy' に変更するには、要求本文で ID 'ProdPolicy' を指定します。

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

応答は、[保護を有効にする場合](#responses-to-create-protected-item-operation)に説明したのと同じ書式に従ったものになります

#### <a name="excluding-disks-during-azure-vm-protection"></a>Azure VM 保護中のディスクの除外

Azure VM が既にバックアップされている場合は、保護のポリシーを変更することによって、バックアップまたは除外するディスクの一覧を指定できます。 要求は、[保護の有効時のディスクの除外](#excluding-disks-in-azure-vm-backup)と同じ形式で作成してください。

> [!IMPORTANT]
> 上記の要求本文は常に、除外するまたは含めるデータ ディスクの最終的なコピーです。 前の構成に "*追加*" されるわけではありません。 次に例を示します。最初に "データ ディスク 1 を除外する" として保護を更新し、その後に、"データ ディスク 2 を除外する" で保護を更新した場合、その後のバックアップでは "*データ ディスク 2 のみが除外*" され、データ ディスク 1 は含まれます。 これは、常に、その後のバックアップで含まれる、または除外される最終的な一覧です。

除外される、または含まれるディスクの最新の一覧を取得するには、[こちら](/rest/api/backup/protected-items/get)に記載されている保護された項目の情報を取得してください。 応答には、データ ディスク LUN の一覧が示され、それらが含まれるかのか除外されるのかが示されます。

### <a name="stop-protection-but-retain-existing-data"></a>保護を停止するが既存のデータを保持する

保護された VM の保護を削除し、バックアップ済みのデータは保持するには、要求本文内のポリシーを削除し、要求を送信します。 ポリシーとの関連付けが削除されると、バックアップはトリガーされなくなり、新しい復旧ポイントは作成されません。

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

応答は、[オンデマンド バックアップをトリガーする場合](#example-responses-for-on-demand-backup)に説明したのと同じ書式に従ったものになります。 結果のジョブは、[REST API を使用してジョブを監視することに関するドキュメント](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)で説明したように追跡する必要があります。

### <a name="stop-protection-and-delete-data"></a>保護を停止してデータを削除する

保護された VM の保護を停止し、バックアップ データも削除するには、[ここ](/rest/api/backup/protected-items/delete)で詳しく説明されている削除操作を実行します。

保護の停止とデータの削除は、*DELETE* 操作です。

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

`{containerName}` と `{protectedItemName}` は、[上](#responses-to-get-operation)のように作成されます。 `{fabricName}` は "Azure" です。 この例では、これを次のように変えます。

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="responses-for-delete-protection"></a>保護の削除の応答

保護の *DELETE* は [非同期操作](../azure-resource-manager/management/async-operations.md)です。 つまり、この操作では、個別に追跡する必要がある別の操作が作成されます。

これにより、2 つの応答が返されます。別の操作が作成されたときは 202 (Accepted)、次にその操作が完了したときは 204 (NoContent)。

|名前  |型  |説明  |
|---------|---------|---------|
|204 NoContent     |         |  NoContent       |
|202 Accepted     |         |     承認済み    |

> [!IMPORTANT]
> 誤った削除のシナリオから保護するために、Recovery Services コンテナーに対して[使用できる論理的な削除機能](use-restapi-update-vault-properties.md#soft-delete-state)があります。 コンテナーの論理的な削除の状態が [有効] に設定されている場合、削除操作では、データは直ちに削除されません。 14 日間保持された後に、完全に消去されます。 この 14 日間は、ストレージに対して課金されません。 削除操作を元に戻すには、[削除の取り消しに関するセクション](#undo-the-deletion)を参照してください。

### <a name="undo-the-deletion"></a>削除を元に戻す

誤削除の取り消しは、バックアップ項目の作成と似ています。 削除を取り消すと、その項目は保持されますが、以降のバックアップはトリガーされません。

削除の取り消しは *PUT* 操作であり、[ポリシーの変更](#changing-the-policy-of-protection)や [保護の有効化](#enabling-protection-for-the-azure-vm)と非常に似ています。 [要求本文](#example-request-body)の中で変数 *isRehydrate* を使用して、単に削除を取り消すという目的を提示して、要求を送信します。 次に例を示します。testVM に対する削除を取り消すには、次の要求本文を使用する必要があります。

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "protectionState": "ProtectionStopped",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "isRehydrate": true
  }
}
```

応答は、[オンデマンド バックアップをトリガーする場合](#example-responses-for-on-demand-backup)に説明したのと同じ書式に従ったものになります。 結果のジョブは、[REST API を使用してジョブを監視することに関するドキュメント](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)で説明したように追跡する必要があります。

## <a name="next-steps"></a>次のステップ

[Azure 仮想マシンのバックアップからデータを復元します](backup-azure-arm-userestapi-restoreazurevms.md)。

Azure Backup REST API について詳しくは、次のドキュメントをご覧ください。

- [Azure Recovery Services プロバイダー REST API](/rest/api/recoveryservices/)
- [Azure Rest API の開始](/rest/api/azure/)
