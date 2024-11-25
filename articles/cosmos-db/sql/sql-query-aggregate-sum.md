---
title: Azure Cosmos DB クエリ言語の SUM
description: Azure Cosmos DB の Sum (SUM) SQL システム関数について説明します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 77f00474a53b3fa068989a111517dc05806a1ed4
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206539"
---
# <a name="sum-azure-cosmos-db"></a>SUM (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

この集計関数では、式内の値の合計が返されます。
  
## <a name="syntax"></a>構文
  
```sql
SUM(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
数値式を返します。  
  
## <a name="examples"></a>例
  
次の例では、`propertyA` の合計が返されます。
  
```sql
SELECT SUM(c.propertyA)
FROM c
```  

## <a name="remarks"></a>解説

このシステム関数は、[範囲インデックス](../index-policy.md#includeexclude-strategy)の恩恵を受けます。 `SUM` のいずれかの引数が文字列、ブール値、または null 値の場合、集計システム関数全体では `undefined` が返されます。 いずれかの引数に `undefined` 値が含まれている場合、`SUM` の計算には影響しません。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の集計関数](sql-query-aggregate-functions.md)