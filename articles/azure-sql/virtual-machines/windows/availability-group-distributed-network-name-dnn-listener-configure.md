---
title: 可用性グループの DNN リスナーの構成
description: 分散ネットワーク名 (DNN) リスナーを構成して、仮想ネットワーク名 (VNN) リスナーを置き換え、Azure VM 上の SQL Server で Always On 可用性グループにトラフィックをルーティングする方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: rajeshsetlem
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: f4a79ae5b1083bf9091486e9f4d87c6fdad14747
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156904"
---
# <a name="configure-a-dnn-listener-for-an-availability-group"></a>可用性グループの DNN リスナーの構成
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> 同じ Azure 仮想ネットワーク内の[複数のサブネット](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md)に SQL Server VM を作成することで、Always On 可用性グループ (AG) に対して分散ネットワーク名が不要になります。

1 つのサブネット内の Azure VM 上の SQL Server を使用すると、分散ネットワーク名 (DNN) によって、クラスター化された適切なリソースにトラフィックがルーティングされます。 これにより、仮想ネットワーク名 (VNN) リスナーよりも簡単に Always On 可用性グループ (AG) に接続する方法が提供されます。Azure Load Balancer は必要ありません。

この記事では、高可用性とディザスター リカバリー (HADR) のために、DNN リスナーを構成して、VNN リスナーを置き換え、Azure VM 上の SQL Server で可用性グループにトラフィックをルーティングする方法について説明します。


代替の接続オプションとしては、代わりに [VNN リスナーと Azure Load Balancer](availability-group-vnn-azure-load-balancer-configure.md) を検討してください。

## <a name="overview"></a>概要

分散ネットワーク名 (DNN) リスナーは、[SQL Server VM 上の Always On 可用性グループ](availability-group-overview.md)と共に使用した場合、従来の仮想ネットワーク名 (VNN) 可用性グループ リスナーに取って代わります。 これにより、トラフィックのルーティングに Azure Load Balancer が不要になり、デプロイと保守が簡素化され、フェールオーバーが向上します。

DNN リスナーを既存の VNN リスナーの代わりに使用するか、または既存の VNN リスナーと組み合わせて使用します。後者の場合は、可用性グループに 2 つの異なる接続ポイント (VNN リスナー名 (既定以外の場合はポートも) を使用するものと、DNN リスナー名およびポートを使用するもの) が存在します。

> [!CAUTION]
> DNN を使用する場合のルーティング動作は、VNN を使用する場合と異なります。 ポート 1433 を使用しないでください。 詳細については、この記事で後述する「[ポートに関する考慮事項](#port-considerations)」セクションをご覧ください。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

- SQL Server ([SQL Server 2019 CU8](https://support.microsoft.com/topic/cumulative-update-8-for-sql-server-2019-ed7f79d9-a3f0-a5c2-0bef-d0b7961d2d72) 以降、[SQL Server 2017 CU25](https://support.microsoft.com/topic/kb5003830-cumulative-update-25-for-sql-server-2017-357b80dc-43b5-447c-b544-7503eee189e9) 以降、または Windows Server 2016 以降の [SQL Server 2016 SP3](https://support.microsoft.com/topic/kb5003279-sql-server-2016-service-pack-3-release-information-46ab9543-5cf9-464d-bd63-796279591c31) 以降)。
- 分散ネットワーク名が[お客様の HADR ソリューションに適切な接続オプション](hadr-cluster-best-practices.md#connectivity)であると判断済みであること。
- [Always On 可用性グループ](availability-group-overview.md)を構成済みであること。 
- 最新バージョンの [PowerShell](/powershell/azure/install-az-ps) をインストール済みであること。 
- DNN リスナーに使用する一意のポートを特定してあること。 DNN リスナーに使用されるポートは、可用性グループまたはフェールオーバー クラスター インスタンスのすべてのレプリカ間で一意である必要があります。  他の接続で同じポートを共有することはできません。



## <a name="create-script"></a>スクリプトの作成

PowerShell を使用して分散ネットワーク名 (DNN) リソースを作成し、可用性グループに関連付けます。

これを行うには、次の手順に従います。

1. メモ帳などのテキスト エディターを開きます。
1. 次のスクリプトをコピーして貼り付けます。

   ```powershell
   param (
      [Parameter(Mandatory=$true)][string]$Ag,
      [Parameter(Mandatory=$true)][string]$Dns,
      [Parameter(Mandatory=$true)][string]$Port
   )
   
   Write-Host "Add a DNN listener for availability group $Ag with DNS name $Dns and port $Port"
   
   $ErrorActionPreference = "Stop"
   
   # create the DNN resource with the port as the resource name
   Add-ClusterResource -Name $Port -ResourceType "Distributed Network Name" -Group $Ag 
   
   # set the DNS name of the DNN resource
   Get-ClusterResource -Name $Port | Set-ClusterParameter -Name DnsName -Value $Dns 
   
   # start the DNN resource
   Start-ClusterResource -Name $Port
   
   
   $Dep = Get-ClusterResourceDependency -Resource $Ag
   if ( $Dep.DependencyExpression -match '\s*\((.*)\)\s*' )
   {
   $DepStr = "$($Matches.1) or [$Port]"
   }
   else
   {
   $DepStr = "[$Port]"
   }
   
   Write-Host "$DepStr"
   
   # add the Dependency from availability group resource to the DNN resource
   Set-ClusterResourceDependency -Resource $Ag -Dependency "$DepStr"
   
   
   #bounce the AG resource
   Stop-ClusterResource -Name $Ag
   Start-ClusterResource -Name $Ag
   ```

1. スクリプトを `add_dnn_listener.ps1` などの `.ps1` ファイルとして保存します。

## <a name="execute-script"></a>スクリプトの実行

DNN リスナーを作成するには、可用性グループ名、リスナー名、およびポートのパラメーターを渡すスクリプトを実行します。

たとえば、可用性グループ名 `ag1`、リスナー名 `dnnlsnr`、リスナー ポート `6789` を想定した場合、次の手順を実行します。

1. コマンド プロンプトや PowerShell などのコマンドライン インターフェイス ツールを開きます。
1. `.ps1` スクリプトを保存した場所 (C:\Documents など) に移動します。
1. スクリプト ```add_dnn_listener.ps1 <ag name> <listener-name> <listener port>``` を実行します。 次に例を示します。

   ```console
   c:\Documents> add_dnn_listener.ps1 ag1 dnnlsnr 6789
   ```

## <a name="verify-listener"></a>リスナーの確認

DNN リスナーが正常に作成されたことを確認するには、SQL Server Management Studio または Transact-SQL を使用します。

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) で **[可用性グループ リスナー]** を展開して、DNN リスナーを表示します。

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-in-ssms.png" alt-text="SQL Server Management Studio (SSMS) の [可用性グループ リスナー] で DNN リスナーを表示する":::

### <a name="transact-sql"></a>Transact-SQL

Transact-SQL を使用して、DNN リスナーの状態を表示します。

```sql
SELECT * FROM SYS.AVAILABILITY_GROUP_LISTENERS
```

`is_distributed_network_name` の値 `1` は、リスナーが分散ネットワーク名 (DNN) リスナーであることを示します。

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-tsql.png" alt-text="sys.availability_group_listeners を使用して、is_distributed_network_name の値が 1 である DNN リスナーを識別する":::

## <a name="update-connection-string"></a>接続文字列を更新する

DNN リスナーに接続する必要があるアプリケーションの接続文字列を更新します。 DNN リスナーへの接続文字列では、DNN ポート番号を指定し、接続文字列で `MultiSubnetFailover=True` を指定する必要があります。 SQL クライアントで `MultiSubnetFailover=True` パラメーターがサポートされていない場合は、DNN リスナーと互換性がありません。  

リスナー名 **DNN_Listener** とポート 6789 の接続文字列の例を次に示します。 

`DataSource=DNN_Listener,6789,MultiSubnetFailover=True`

## <a name="test-failover"></a>[テスト フェールオーバー]

可用性グループのフェールオーバーをテストして、機能を確認します。

フェールオーバーをテストするには、次の手順に従います。

1. [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) を使用して、DNN リスナーまたはいずれかのレプリカに接続します。
1. **オブジェクト エクスプローラー** で **[Always On 可用性グループ]** を展開します。
1. 可用性グループを右クリックし、 **[フェールオーバー]** を選択して、**フェールオーバー ウィザード** を開きます。
1. プロンプトに従ってフェールオーバー ターゲットを選択し、可用性グループをセカンダリ レプリカにフェールオーバーします。
1. 新しいプライマリ レプリカで、データベースが同期状態であることを確認します。
1. (省略可能) 元のプライマリ レプリカまたは別のセカンダ リレプリカにフェールバックします。

## <a name="test-connectivity"></a>接続をテストする

次の手順に従って、DNN リスナーへの接続をテストします。

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を開きます。
1. DNN リスナーに接続します。
1. 新しいクエリ ウィンドウを開き、`SELECT @@SERVERNAME` を実行して、接続しているレプリカを確認します。
1. 可用性グループを別のレプリカにフェールオーバーします。
1. 十分な時間が経過したら、`SELECT @@SERVERNAME` を実行して、可用性グループが別のレプリカでホストされていることを確認します。

## <a name="limitations"></a>制限事項

- DNN リスナーは、一意のポートを使用して構成する **必要があります**。  このポートは、どのレプリカでも他の接続と共有できません。
- DNN リスナーに接続するクライアントは、接続文字列の `MultiSubnetFailover=True` パラメーターをサポートする必要があります。 
- DNN を使用してその他の SQL Server 機能と可用性グループを操作する場合は、さらなる考慮事項が生じることがあります。 詳細については、[AG と DNN の相互運用性](availability-group-dnn-interoperability.md)に関する記事をご覧ください。 

## <a name="port-considerations"></a>ポートに関する考慮事項

DNN リスナーは、特定の一意のポートで、すべての IP アドレスをリッスンするように設計されています。 リスナー名の DNS エントリは、可用性グループ内のすべてのレプリカのアドレスに解決されます。 これは、「[スクリプトの作成](#create-script)」セクションで示している PowerShell スクリプトで自動的に実行されます。 DNN リスナーはすべての IP アドレスで接続を受け入れるため、リスナー ポートが一意であること、可用性グループ内の他のレプリカで使用されていないことが重要です。 既定では、SQL Server は、直接、または SQL Browser サービスを介して、ポート 1433 でリッスンするため、DNN リスナーにはポート 1433 を使用しないことを強くお勧めします。 

## <a name="next-steps"></a>次のステップ

可用性グループがデプロイされたら、[Azure VM 上の SQL Server に対する HADR 設定](hadr-cluster-best-practices.md)を最適化することを検討します。 


詳細については、以下をご覧ください。

- [Windows Server フェールオーバー クラスターと Azure VM 上の SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [AlwaysOn 可用性グループと Azure VM 上の SQL Server](availability-group-overview.md)
- [AlwaysOn 可用性グループの概要](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
