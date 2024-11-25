---
title: Azure Cosmos DB クエリ言語の LENGTH
description: Azure Cosmos DB での SQL システム関数 LENGTH について学習します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0603f9c7ff79f9aba1766819575e31dfb848bbb8
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206384"
---
# <a name="length-azure-cosmos-db"></a>LENGTH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 指定された文字列式の文字数を返します。  
  
## <a name="syntax"></a>構文
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   評価される文字列式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、文字列の長さを返します。  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 結果セットは次のようになります。  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>解説

このシステム関数では、インデックスは使用されません。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
