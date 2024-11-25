---
title: sys.sp_cleanup_data_retention (Transact-SQL) - Azure SQL Edge
description: Azure SQL Edge での sys.sp_cleanup_data_retention (Transact-SQL) の使用について説明します
keywords: sys.sp_cleanup_data_retention (Transact-SQL), SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 64d24c089ed109c8bd90d76b832c7a38d2185184
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751346"
---
# <a name="syssp_cleanup_data_retention-transact-sql"></a>sys.sp_cleanup_data_retention (Transact-SQL)

**適用対象:** Azure SQL Edge

データ保持ポリシーが有効になっているテーブルで古いレコードのクリーンアップを実行します。 詳細については、[データ保持ポリシー](data-retention-overview.md)に関するページを参照してください。

## <a name="syntax"></a>構文

```syntaxsql
sys.sp_cleanup_data_retention
    { [@schema_name = ] 'schema_name' },
    { [@table_name = ] 'table_name' },
    [ [@rowcount =] rowcount OUTPUT ]

```

## <a name="arguments"></a>引数
`[ @schema_name = ] schema_name` クリーンアップを実行する必要があるテーブルを所有するスキーマの名前を指定します。 *schema_name* は **sysname** 型の必須パラメーターです。

`[ @table_name = ] 'table_name'` クリーンアップ操作を実行する必要があるテーブルの名前を指定します。 *table_name* は **sysname** 型の必須パラメーターです。

## <a name="output-parameter"></a>出力パラメーター

`[ @rowcount = ] rowcount OUTPUT` rowcount は、テーブルからクリーンアップされたレコードの数を表す省略可能な出力パラメーターです。 *rowcount* は int です。

## <a name="permissions"></a>アクセス許可
 db_owner のアクセス許可が必要です。

## <a name="next-steps"></a>次のステップ
- [データ保有期間と自動データ削除](data-retention-overview.md)
- [アイテム保持ポリシーを使用して履歴データを管理する](data-retention-cleanup.md)
- [データ保有期間を有効または無効にする](data-retention-enable-disable.md)
