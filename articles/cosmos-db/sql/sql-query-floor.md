---
title: Azure Cosmos DB クエリ言語の FLOOR
description: 指定された数値式以下の最大の整数を返す Azure Cosmos DB の FLOOR SQL システム関数について説明します
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: dddaa1d4b89c41318b6496ec1702030773f2676a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206481"
---
# <a name="floor-azure-cosmos-db"></a>FLOOR (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 指定された数値式以下の最大の整数を返します。  
  
## <a name="syntax"></a>構文
  
```sql
FLOOR (<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、`FLOOR` 関数を使用して、正の数値、負の数値、およびゼロ値を示します。  
  
```sql
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 結果セットは次のようになります。  
  
```json
[{fl1: 123, fl2: -124, fl3: 0}]  
```

## <a name="remarks"></a>解説

このシステム関数は、[範囲インデックス](../index-policy.md#includeexclude-strategy)の恩恵を受けます。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
