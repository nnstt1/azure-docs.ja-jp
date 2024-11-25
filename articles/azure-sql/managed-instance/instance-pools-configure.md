---
title: SQL Managed Instance をインスタンス プールにデプロイする
titleSuffix: Azure SQL Managed Instance
description: この記事では、Azure SQL Managed Instance プール (プレビュー) を作成および管理する方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: devx-track-azurepowershell
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma
ms.date: 09/05/2019
ms.openlocfilehash: a003370180471e02f4801bffd2477f0c50faa99d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676563"
---
# <a name="deploy-azure-sql-managed-instance-to-an-instance-pool"></a>Azure SQL Managed Instance をインスタンス プールにデプロイする
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

この記事では、[インスタンス プール](instance-pools-overview.md)を作成し、それに Azure SQL Managed Instance をデプロイする方法の詳細を説明します。 

## <a name="instance-pool-operations"></a>インスタンス プールの操作

次の表は、インスタンス プールに関連する使用可能な操作と、Azure portal、PowerShell、Azure CLI でそれらを使用できるかどうかを示しています。

|コマンド|Azure portal|PowerShell|Azure CLI|
|:---|:---|:---|:---|
|インスタンス プールを作成する|いいえ|はい|はい|
|インスタンス プールを更新する (プロパティ数に制限あり)|いいえ |はい | はい|
|インスタンス プールの使用状況とプロパティを確認する|いいえ|はい | はい |
|インスタンス プールを削除する|いいえ|はい|はい|
|インスタンス プール内にマネージド インスタンスを作成する|いいえ|はい|いいえ|
|マネージド インスタンスのリソースの使用状況を更新する|はい |はい|いいえ|
|マネージド インスタンスの使用状況とプロパティを確認する|はい|はい|いいえ|
|プールからマネージド インスタンスを削除する|はい|はい|いいえ|
|プール内のインスタンスにデータベースを作成する|はい|はい|いいえ|
|SQL Managed Instance からデータベースを削除する|はい|はい|いいえ|

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Powershell を使用するには、[最新バージョンの PowerShell Core をインストール](/powershell/scripting/install/installing-powershell#powershell)し、指示に従って [Azure PowerShell モジュールをインストール](/powershell/azure/install-az-ps)します。

使用可能な [PowerShell コマンド](/powershell/module/az.sql/)

|コマンドレット |説明 |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | SQL Managed Instance プールを作成します。 |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | インスタンス プールに関する情報を返します。 |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | SQL Managed Instance 内のインスタンス プールのプロパティを設定します。 |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | SQL Managed Instance 内のインスタンス プールを削除します。 |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | SQL Managed Instance プールの使用状況に関する情報を返します |

プール内のインスタンスと単一インスタンスの両方に関連する操作では、標準の [マネージド インスタンス コマンド](api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances)を使用しますが、プール内のインスタンスでこれらのコマンドを使用する場合は、"*インスタンス プール名*" プロパティを設定する必要があります。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI の環境を準備します。

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

使用可能な [Azure CLI](/cli/azure/sql) コマンド:

|コマンドレット |説明 |
|:---|:---|
|[az sql instance-pool create](/cli/azure/sql/instance-pool#az_sql_instance_pool_create) | SQL Managed Instance プールを作成します。 |
|[az sql instance-pool show](/cli/azure/sql/instance-pool#az_sql_instance_pool_show) | インスタンス プールに関する情報を返します。 |
|[az sql instance-pool update](/cli/azure/sql/instance-pool#az_sql_instance_pool_update) | SQL Managed Instance 内のインスタンス プールのプロパティを設定または更新します。 |
|[az sql instance-pool delete](/cli/azure/sql/instance-pool#az_sql_instance_pool_delete) | SQL Managed Instance 内のインスタンス プールを削除します。 |

---

## <a name="deployment-process"></a>デプロイ プロセス

マネージド インスタンスをインスタンス プールにデプロイするには、最初にインスタンス プールをデプロイする必要があります。これは実行時間の長い 1 回限り操作です。なお、その期間は、[空のサブネットに作成された単一インスタンス](sql-managed-instance-paas-overview.md#management-operations)をデプロイする場合と同じです。 その後、マネージド インスタンスをプールにデプロイできます。これは、通常、最大で 5 分かかる比較的高速な操作です。 インスタンス プール パラメーターは、この操作の一部として明示的に指定する必要があります。

パブリック プレビューでは、どちらのアクションも、PowerShell および Azure Resource Manager のテンプレートを使用した場合のみサポートされます。 Azure portal エクスペリエンスは現在使用できません。

プールにマネージド インスタンスがデプロイされた後は、Azure portal を使用して、価格レベル ページでそのプロパティを変更 "*できます*"。

## <a name="create-a-virtual-network-with-a-subnet"></a>サブネットを含む仮想ネットワークを作成する 

同じ仮想ネットワーク内に複数のインスタンス プールを配置するには、次の記事を参照してください。

- [Azure SQL Managed Instance 用の VNet のサブネット サイズを決める](vnet-subnet-determine-size.md)
- [Azure portal テンプレート](virtual-network-subnet-create-arm-template.md)を使用して新しい仮想ネットワークとサブネットを作成するか、[既存の仮想ネットワークを準備する](vnet-existing-add-subnet.md)手順に従う。
 

## <a name="create-an-instance-pool"></a>インスタンス プールを作成する 

前の手順を完了すると、インスタンス プールを作成する準備が整います。

インスタンス プールには、次の制限が適用されます。

- パブリック プレビューでは、General Purpose と Gen5 のみを使用できます。
- プール名に使用できるのは、小文字、数字、ハイフンのみで、先頭をハイフンにすることはできません。
- Azure ハイブリッド特典を使用する場合は、インスタンス プール レベルで適用されます。 プールの作成時にライセンスの種類を設定できます。また、作成後にいつでも更新することができます。

> [!IMPORTANT]
> インスタンス プールのデプロイは実行時間の長い操作であり、約 4.5 時間かかります。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

ネットワーク パラメーターを取得するには:

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

インスタンス プールを作成するには:

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 8 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

仮想ネットワーク パラメーターを取得するには:

```azurecli
az network vnet show --resource-group MyResourceGroup --name miPoolVirtualNetwork
```

仮想サブネット パラメーターを取得するには:

```azurecli
az network vnet subnet show --resource group MyResourceGroup --name miPoolSubnet --vnet-name miPoolVirtualNetwork
```

インスタンス プールを作成するには:

```azurecli
az sql instance-pool create
    --license-type LicenseIncluded 
    --location westeurope
    --name mi-pool-name
    --capacity 8
    --tier GeneralPurpose
    --family Gen5 
    --resrouce-group myResourceGroup
    --subnet miPoolSubnet
    --vnet-name miPoolVirtualNetwork
```

---

> [!IMPORTANT]
> インスタンス プールのデプロイは実行時間の長い操作であるため、この記事の次の手順はすべて、完了するまで待ってから行ってください。

## <a name="create-a-managed-instance"></a>マネージド インスタンスを作成する

インスタンス プールが正常にデプロイされたら、その内部にマネージド インスタンスを作成します。

マネージド インスタンスを作成するには、次のコマンドを実行します。

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-one-name" -VCore 2 -StorageSizeInGB 256
```

プール内にインスタンスをデプロイするには数分かかります。 最初のインスタンスが作成された後、追加のインスタンスを作成できます。

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-two-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database"></a>データベースを作成する 

プール内のマネージド インスタンスでデータベースを作成および管理するには、単一インスタンス コマンドを使用します。

マネージド インスタンス内にデータベースを作成するには、次のようにします。

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-pool-usage"></a>プールの使用状況を取得する 
 
プール内のインスタンスのリストを取得するには:

```powershell
$instancePool | Get-AzSqlInstance
```


プール リソースの使用状況を取得するには:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


プールとその内部のインスタンスの詳細な使用状況の概要を取得するには:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

インスタンス内のデータベースをリストするには:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> インスタンス プールおよびプール内にデプロイされたマネージド インスタンスごとのデータベース数の制限を確認するには、[インスタンス プールのリソース制限](instance-pools-overview.md#resource-limitations)に関するセクションを参照してください。


## <a name="scale"></a>スケール 


マネージド インスタンスにデータベースを設定した後、ストレージまたはパフォーマンスに関するインスタンスの制限に達することがあります。 その場合、プールの使用量を超えていなければ、インスタンスをスケーリングできます。
プール内のマネージド インスタンスのスケーリングは、数分かかる操作です。 スケーリングの前提条件として、インスタンス プール レベルで仮想コアとストレージを使用できます。

仮想コアの数とストレージ サイズを更新するには:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


ストレージ サイズのみを更新するには:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```

## <a name="connect"></a>接続する 

プール内のマネージド インスタンスに接続するには、次の 2 つの手順を行う必要があります。

1. [インスタンスのパブリック エンドポイントを有効にします](#enable-the-public-endpoint)。
2. [ネットワーク セキュリティ グループ (NSG) に受信規則を追加します](#add-an-inbound-rule-to-the-network-security-group)。

両方の手順が完了したら、インスタンス作成時に提供されたパブリック エンドポイント アドレス、ポート、および資格情報を使用して、インスタンスに接続できます。 

### <a name="enable-the-public-endpoint"></a>パブリック エンドポイントを有効にする

インスタンスのパブリック エンドポイントの有効化は、Azure portal から、または次の PowerShell コマンドを使用して行うことができます。


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

このパラメーターは、インスタンスの作成時にも設定できます。

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>ネットワーク セキュリティ グループに受信規則を追加する 

この手順は、Azure portal から、または PowerShell コマンドを使用して行うことができ、マネージド インスタンスのサブネットが準備された後はいつでも行うことができます。

詳細については、「[ネットワーク セキュリティ グループでパブリック エンドポイント トラフィックを許可する](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)」を参照してください。


## <a name="move-an-existing-single-instance-to-a-pool"></a>既存の単一インスタンスをプールに移動する
 
プール内外へのインスタンスの移動は、パブリック プレビューの制限事項の 1 つです。 回避策は、プールの外部のインスタンスから、プールに既に存在するインスタンスへの、データベースのポイントインタイム リストアに依存します。 

両方のインスタンスが同じサブスクリプションとリージョン内にある必要があります。 リージョン間およびサブスクリプション間での復元は、現在、サポートされていません。

このプロセスにはダウンタイムがあります。

既存のデータベースを移動するには:

1. 移行元のマネージド インスタンスのワークロードを一時停止します。
2. システム データベースを作成するスクリプトを生成して、インスタンス プール内のインスタンスで実行します。
3. 単一インスタンスから、プール内のインスタンスへの各データベースのポイントインタイム リストアを行います。

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. アプリケーションを新しいインスタンスにポイントし、そのワークロードを再開します。

複数のデータベースがある場合は、データベースごとにプロセスを繰り返します。


## <a name="next-steps"></a>次のステップ

- 機能比較一覧については、[SQL 共通機能](../database/features-comparison.md)に関する記事をご覧ください。
- VNet の構成の詳細については、[SQL Managed Instance VNet の構成](connectivity-architecture-overview.md)に関するページを参照してください。
- マネージド インスタンスを作成し、バックアップ ファイルからデータベースを復元するためのクイック スタートについては、[マネージド インスタンスの作成](instance-create-quickstart.md)に関するページを参照してください。
- Azure Database Migration Service を使用して移行する方法のチュートリアルについては、[Database Migration Service を使用した SQL Managed Instance の移行](../../dms/tutorial-sql-server-to-managed-instance.md)に関するページを参照してください。
- 組み込みのトラブルシューティング インテリジェンスを使用した SQL Managed Instance データベースの高度なパフォーマンス監視については、[Azure SQL Analytics を使用した Azure SQL Managed Instance の監視](../../azure-monitor/insights/azure-sql.md)に関するページを参照してください。
- 料金情報については、[SQL Managed Instance の価格](https://azure.microsoft.com/pricing/details/sql-database/managed/)に関するページを参照してください。
