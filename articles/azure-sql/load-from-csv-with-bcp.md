---
title: CSV ファイルからデータベースへのデータの読み込み (bcp)
description: データ サイズが小さい場合は、bcp を使用して Azure SQL Database にデータをインポートできます。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: dzsquared
ms.author: drskwier
ms.reviewer: mathoma
ms.date: 01/25/2019
ms.openlocfilehash: 6fe0fdf53235ce1ec42a2b46e95291c111ed2dff
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743690"
---
# <a name="load-data-from-csv-into-azure-sql-database-or-sql-managed-instance-flat-files"></a>CSV から Azure SQL Database または SQL Managed Instance へのデータの読み込み (フラット ファイル)
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

bcp コマンドライン ユーティリティを使用して、CSV ファイルから Azure SQL Database または Azure SQL Managed Instance にデータをインポートできます。

## <a name="before-you-begin"></a>開始する前に

### <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* Azure SQL Database 内のデータベース
* インストールされた bcp コマンド ライン ユーティリティ
* インストールされた sqlcmd コマンド ライン ユーティリティ

bcp および sqlcmd ユーティリティは [[Microsoft sqlcmd ドキュメント]](/sql/tools/sqlcmd-utility?view=sql-server-ver15&preserve-view=true) からダウンロードできます。

### <a name="data-in-ascii-or-utf-16-format"></a>ASCII または UTF-16 形式のデータ

自身のデータを使ってこのチュートリアルを試す場合、bcp では UTF-8 がサポートされないため、データには ASCII または UTF-16 エンコードを使用する必要があります。

## <a name="1-create-a-destination-table"></a>1.ターゲット テーブルを作成する

SQL Database 内でターゲット テーブルとなるテーブルを定義します。 テーブル内の各列は、データ ファイルの各行のデータに対応する必要があります。

テーブルを作成するには、コマンド プロンプトを開き、sqlcmd.exe を使用して次のコマンドを実行します。

```cmd
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
"
```

## <a name="2-create-a-source-data-file"></a>2.ソース データ ファイルを作成する

メモ帳を開き、データの以下の行を新しいテキスト ファイルにコピーして、このファイルをローカルの一時ディレクトリに保存します (C:\Temp\DimDate2.txt)。 このデータは ASCII 形式です。

```txt
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

(オプション) 自身のデータを SQL Server データベースからエクスポートするには、コマンド プロンプトを開き、次のコマンドを実行します。 TableName、ServerName、DatabaseName、Username、および Password を自身の情報に置き換えてください。

```cmd
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ,
```

## <a name="3-load-the-data"></a>3.データを読み込む

データを読み込むには、コマンド プロンプトを開き、次のコマンドを実行します。ここでは、ServerName、DatabaseName、Username、および Password を自身の情報に置き換えます。

```cmd
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ,
```

次のコマンドを使用して、データが正しく読み込まれたことを確認します。

```cmd
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

結果は次のようになります。

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

## <a name="next-steps"></a>次のステップ

SQL Server データベースを移行するには、 [SQL Server データベースの移行](database/migrate-to-database-from-sql-server.md)に関するページを参照してください。

<!--MSDN references-->
[bcp]: /sql/tools/bcp-utility
[CREATE TABLE syntax]: /sql/t-sql/statements/create-table-azure-sql-data-warehouse

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
