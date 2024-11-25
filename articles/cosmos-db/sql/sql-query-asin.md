---
title: Azure Cosmos DB クエリ言語の ASIN
description: Azure Cosmos DB の Arcsine (ASIN) SQL システム関数が、サインが指定された数値式である角度をラジアンで返す方法について説明します
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: aa2628d83cbd91abe6163a8758349bca47257221
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206500"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 サインが指定された数値式となる角度をラジアン単位で返します。 アークサインとも呼ばれます。  
  
## <a name="syntax"></a>構文
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、-1 の `ASIN` を返します。  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 結果セットは次のようになります。  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>解説

このシステム関数では、インデックスは使用されません。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
