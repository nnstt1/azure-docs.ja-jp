---
title: データベース ワークロードの宛先として Azure SQL Managed Instance を使用した SSIS の移行
description: データベース ワークロードの宛先として Azure SQL Managed Instance を使用した SSIS の移行。
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: 4696a5405be18ca5ef0f95df9eea20756c02cacf
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852201"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>データベース ワークロードの宛先として Azure SQL Managed Instance を使用した SSIS の移行

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

SQL Server インスタンスから Azure SQL Database マネージド インスタンスにデータベース ワークロードを移行する場合は、[Azure Data Migration Service](../dms/dms-overview.md) (DMS) と、[DMS を使用した SQL Managed Instance の移行のネットワーク トポロジ](../dms/resource-network-topologies.md)を理解している必要があります。

この記事では、SSIS カタログ (SSISDB) に格納されている SQL Server Integration Service (SSIS) パッケージと SSIS パッケージの実行をスケジュールする SQL Server エージェント ジョブの移行に焦点を当てています。

## <a name="migrate-ssis-catalog-ssisdb"></a>SSIS カタログ (SSISDB) を移行する

SSISDB の移行は、次の記事で説明されているように、DMS を使用して行うことができます。「[SSIS パッケージを SQL Managed Instance に移行する](../dms/how-to-migrate-ssis-packages-managed-instance.md)」。

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>SSIS ジョブを SQL Database Managed Instance エージェントに移行する

SQL Managed Instance には、オンプレミスの SQL Server エージェントと同様に、ネイティブの第一級のスケジューラがあります。  [Azure SQL Managed Instance エージェント経由で SSIS パッケージを実行](how-to-invoke-ssis-package-managed-instance-agent.md)できます。

SSIS ジョブの移行ツールはまだ使用できないため、スクリプト/手動コピーを使用して、オンプレミスの SQL Server エージェントから SQL Managed Instance  エージェントに移行する必要があります。

## <a name="additional-resources"></a>その他のリソース

- [Azure Data Factory](./introduction.md)
- [Azure-SSIS Integration Runtime](./create-azure-ssis-integration-runtime.md)
- [Azure Database Migration Service](../dms/dms-overview.md)
- [DMS を使用した SQL Managed Instance の移行のためのネットワーク トポロジ](../dms/resource-network-topologies.md)
- [SSIS パッケージを SQL Managed Instance に移行する](../dms/how-to-migrate-ssis-packages-managed-instance.md)

## <a name="next-steps"></a>次のステップ

- [Azure での SSISDB への接続](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Azure にデプロイされた SSIS パッケージを実行する](/sql/integration-services/lift-shift/ssis-azure-run-packages)