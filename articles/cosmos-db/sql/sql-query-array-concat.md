---
title: Azure Cosmos DB クエリ言語の ARRAY_CONCAT
description: Azure Cosmos DB の Array Concat SQL システム関数から、複数の配列値を連結した結果の配列がどのように返されるかについて説明します
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: da2ae4b30be7e8c089344a47b5fad52b07accebc
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206502"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 2 つ以上の配列値を連結した結果である配列を返します。  
  
## <a name="syntax"></a>構文
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>引数
  
*arr_expr*  
   他の値に連結する配列式です。 `ARRAY_CONCAT` 関数には、少なくとも 2 つの *arr_expr* 引数が必要です。  
  
## <a name="return-types"></a>戻り値の型
  
  配列式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、2 つの配列を結合する方法を示します。  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 結果セットは次のようになります。  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
## <a name="remarks"></a>解説

このシステム関数では、インデックスは使用されません。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の配列関数](sql-query-array-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
