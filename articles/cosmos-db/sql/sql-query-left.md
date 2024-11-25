---
title: Azure Cosmos DB クエリ言語の LEFT
description: Azure Cosmos DB での SQL システム関数 LEFT について学習します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5a73b0052d92a7cf78a3014d944ba7ca1bfc97ee
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206385"
---
# <a name="left-azure-cosmos-db"></a>LEFT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 指定された文字数分、文字列の左側の部分を返します。  
  
## <a name="syntax"></a>構文
  
```sql
LEFT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   文字を抽出する文字列式です。  
  
*num_expr*  
   文字数を指定する数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  文字列式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、さまざまな長さの値の "abc" の左側の部分を返します。  
  
```sql
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 結果セットは次のようになります。  
  
```json
[{"l1": "a", "l2": "ab"}]  
```  

## <a name="remarks"></a>解説

このシステム関数は、[範囲インデックス](../index-policy.md#includeexclude-strategy)の恩恵を受けます。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
