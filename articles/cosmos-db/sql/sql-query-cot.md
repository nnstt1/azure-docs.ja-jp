---
title: Azure Cosmos DB クエリ言語の COT
description: Azure Cosmos DB の Cotangent (COT) SQL システム関数で、数値式で指定されたラジアン単位の角度の三角関数コタンジェントを返す方法について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7ebcdea51e4a39c93883db80bf5a297e5c513acb
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206389"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 数値式で指定されたラジアン単位の角度の三角関数コタンジェントを返します。  
  
## <a name="syntax"></a>構文
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、指定された角度の `COT` を計算します。  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 結果セットは次のようになります。  
  
```json
[{"cot": -0.040311998371148884}]  
```  

## <a name="remarks"></a>解説

このシステム関数では、インデックスは使用されません。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
