---
title: Azure Cosmos DB クエリ言語の StartsWith
description: Azure Cosmos DB の SQL システム関数 STARTSWITH について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d850ae0a01a93e2214d4dc26df98234f54e28a8b
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206569"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 1 つ目の文字列式が 2 つ目の文字列で始まっているかどうかを示すブール値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
STARTSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>引数
  
*str_expr1*  
   文字列式です。
  
*str_expr2*  
   *str_expr1* の先頭と比較される文字列式です。

*bool_expr* 大文字と小文字の区別を無視する場合のオプションの値です。 true に設定すると、STARTSWITH は大文字と小文字を区別せずに検索を実行します。 指定しない場合、この値は false になります。

## <a name="return-types"></a>戻り値の型
  
  ブール式を返します。  
  
## <a name="examples"></a>例
  
次の例は、文字列 "abc" が "b" および "A" で始まるかどうかを確認します。  
  
```sql
SELECT STARTSWITH("abc", "b", false) AS s1, STARTSWITH("abc", "A", false) AS s2, STARTSWITH("abc", "A", true) AS s3
```  
  
 結果セットは次のようになります。  
  
```json
[
    {
        "s1": false,
        "s2": false,
        "s3": true
    }
]
```  

## <a name="remarks"></a>解説

この文字列システム関数でインデックスがどのように使用されるかについては[こちら](sql-query-string-functions.md)をご覧ください。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
