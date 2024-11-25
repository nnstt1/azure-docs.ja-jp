---
title: 単一データベースに対する DTU リソースの制限
description: このページでは、Azure SQL Database の単一データベースに対するいくつかの一般的な DTU リソースの制限について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: service-overview
ms.custom: references_regions, seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 04/16/2021
ms.openlocfilehash: aecf872bcac77c94090d374cc18415eba6323b61
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110689817"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model---azure-sql-database"></a>DTU 購入モデルを使用した単一データベースのリソース制限 - Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

この記事では、DTU 購入モデルを使用した Azure SQL Database の単一データベースに対する詳細なリソース制限について説明します。

* サーバー上の単一データベースに対する DTU 購入モデルの制限については、[サーバー上のリソース制限の概要](resource-limits-logical-server.md)に関するページを参照してください。
* Azure SQL Database に対する DTU 購入モデルのリソース制限については、[単一データベースに対する DTU リソースの制限](resource-limits-dtu-single-databases.md)と [DTU リソースによるエラスティック プールの制限](resource-limits-dtu-elastic-pools.md)に関するページを参照してください。
* 仮想コア リソースの制限については、[Azure SQL Database に対する仮想コア リソースの制限](resource-limits-vcore-single-databases.md)および[エラスティック プールに対する仮想コア リソースの制限](resource-limits-vcore-elastic-pools.md)に関するページを参照してください。
* さまざまな購入モデルについて詳しくは、[購入モデルとサービス レベル](purchasing-models.md)に関する記事をご覧ください。

各読み取り専用レプリカには、DTU、ワーカー、セッションなどの独自のリソースがあります。 読み取り専用の各レプリカには、この記事で後述するリソース制限が適用されます。 


## <a name="single-database-storage-sizes-and-compute-sizes"></a>単一データベース:ストレージ サイズとコンピューティング サイズ

次の表では、各サービス レベルおよびコンピューティング サイズにおいて単一データベースで使用可能なリソースを示します。 次を使用して、単一データベースのサービス レベル、コンピューティング サイズ、およびストレージ量を設定できます。

* [Transact-SQL](single-database-manage.md#transact-sql-t-sql) ([ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database) を使用)
* [Azure Portal](single-database-manage.md#the-azure-portal)
* [PowerShell](single-database-manage.md#powershell)
* [Azure CLI](single-database-manage.md#the-azure-cli)
* [REST API](single-database-manage.md#rest-api)

> [!IMPORTANT]
> スケーリングのガイダンスと考慮事項については、[単一データベースのスケーリング](single-database-scale.md)に関するページを参照してください

### <a name="basic-service-tier"></a>Basic サービス レベル

| **コンピューティング サイズ** | **Basic** |
| :--- | --: |
| 最大 DTU 数 | 5 |
| 付属ストレージ (GB) | 2 |
| 最大ストレージ (GB) | 2 |
| 最大インメモリ OLTP ストレージ容量 (GB) |該当なし |
| 最大同時実行ワーカー (要求) 数 | 30 |
| 最大同時セッション数 | 300 |
|||

> [!IMPORTANT]
> Basic サービス レベルでは、1 未満の仮想コア (CPU) が提供されます。  CPU 負荷の高いワークロードの場合は、S3 以上のサービス レベルをお勧めします。
>
>データ ストレージに関しては、Basic サービス レベルは Standard ページ BLOB に配置されます。 Standard ページ BLOB は、ハード ディスク ドライブ (HDD) ベースのストレージ メディアで、パフォーマンス変動の影響を受けにくい、開発、テスト、その他のアクセス頻度の少ないワークロードに最適です。
>

### <a name="standard-service-tier"></a>Standard サービス レベル

| **コンピューティング サイズ** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| 最大 DTU 数 | 10 | 20 | 50 | 100 |
| 付属ストレージ (GB) <sup>1</sup> | 250 | 250 | 250 | 250 |
| 最大ストレージ (GB) | 250 | 250 | 250 | 1024 |
| 最大インメモリ OLTP ストレージ容量 (GB) | 該当なし | N/A | N/A | 該当なし |
| 最大同時実行ワーカー (要求) 数| 60 | 90 | 120 | 200 |
| 最大同時セッション数 |600 | 900 | 1200 | 2400 |
||||||

<sup>1</sup> 追加のストレージがプロビジョニングされたために発生する追加コストの詳細については、[SQL Database の価格オプション](https://azure.microsoft.com/pricing/details/sql-database/single/)に関するページを参照してください。

> [!IMPORTANT]
> Standard S0、S1、S2 レベルでは、1 未満の仮想コア (CPU) が提供されます。  CPU 負荷の高いワークロードの場合は、S3 以上のサービス レベルをお勧めします。
>
>データ ストレージに関しては、Standard S0 および S1 サービス レベルは Standard ページ BLOB に配置されます。 Standard ページ BLOB は、ハード ディスク ドライブ (HDD) ベースのストレージ メディアで、パフォーマンス変動の影響を受けにくい、開発、テスト、その他のアクセス頻度の少ないワークロードに最適です。
>

### <a name="standard-service-tier-continued"></a>Standard サービス レベル (続き)

| **コンピューティング サイズ** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| 最大 DTU 数 | 200 | 400 | 800 | 1600 | 3000 |
| 付属ストレージ (GB) <sup>1</sup> | 250 | 250 | 250 | 250 | 250 |
| 最大ストレージ (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
| 最大インメモリ OLTP ストレージ容量 (GB) | 該当なし | N/A | N/A | N/A |該当なし |
| 最大同時実行ワーカー (要求) 数| 400 | 800 | 1600 | 3200 |6000 |
| 最大同時セッション数 |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

<sup>1</sup> 追加のストレージがプロビジョニングされたために発生する追加コストの詳細については、[SQL Database の価格オプション](https://azure.microsoft.com/pricing/details/sql-database/single/)に関するページを参照してください。

### <a name="premium-service-tier"></a>Premium サービス レベル

| **コンピューティング サイズ** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| 最大 DTU 数 | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| 付属ストレージ (GB) <sup>1</sup> | 500 | 500 | 500 | 500 | 4096 <sup>2</sup> | 4096 <sup>2</sup> |
| 最大ストレージ (GB) | 1024 | 1024 | 1024 | 1024 | 4096 <sup>2</sup> | 4096 <sup>2</sup> |
| 最大インメモリ OLTP ストレージ容量 (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| 最大同時実行ワーカー (要求) 数| 200 | 400 | 800 | 1600 | 2800 | 6400 |
| 最大同時セッション数 | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

<sup>1</sup> 追加のストレージがプロビジョニングされたために発生する追加コストの詳細については、[SQL Database の価格オプション](https://azure.microsoft.com/pricing/details/sql-database/single/)に関するページを参照してください。

<sup>2</sup> 1024 GB から最大 4096 GB (256 GB ずつ増分)。

> [!IMPORTANT]
> 現在、1 TB を超える Premium レベルのストレージは、中国東部、中国北部、ドイツ中部、ドイツ北東部、を除くすべてのリージョンで利用できます。 これらのリージョンでは、Premium レベルのストレージの最大容量は 1 TB です。  詳しくは、[P11-P15 の現在の制限事項](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb)に関するページをご覧ください。

> [!NOTE]
> `tempdb` の制限については、[tempdb の制限](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)に関する記事を参照してください。
> 
> Premium サービス レベルのストレージの制限事項に関するその他の詳細については、「[ストレージ スペースのガバナンス](resource-limits-logical-server.md#storage-space-governance)」を参照してください。

## <a name="next-steps"></a>次のステップ

- 単一データベースに対する仮想コア リソースの制限については、[仮想コア購入モデルを使用した単一データベースに対するリソース制限](resource-limits-vcore-single-databases.md)に関するページを参照してください
- エラスティック プールに対する仮想コア リソースの制限については、[仮想コア購入モデルを使用したエラスティック プールに対するリソース制限](resource-limits-vcore-elastic-pools.md)に関するページを参照してください
- エラスティック プールに対する DTU リソースの制限については、「[DTU ベースの購入モデルを使用したエラスティック プールのリソース制限](resource-limits-dtu-elastic-pools.md)」を参照してください
- Azure SQL Managed Instance に対するリソース制限については、[SQL Managed Instance のリソース制限](../managed-instance/resource-limits.md)に関するページを参照してください。
- Azure の一般的な制限については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../../azure-resource-manager/management/azure-subscription-service-limits.md)」をご覧ください。
- 論理 SQL サーバーでのリソース制限については、サーバーおよびサブスクリプション レベルの制限に関する情報が記載された、[論理 SQL サーバー上のリソース制限の概要](resource-limits-logical-server.md)に関するページを参照してください。