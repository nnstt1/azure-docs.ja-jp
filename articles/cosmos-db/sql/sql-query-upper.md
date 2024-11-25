---
title: Azure Cosmos DB クエリ言語の UPPER
description: Azure Cosmos DB での SQL システム関数 UPPER について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1a1d3c19bd76805fb26d365d4093a796cc134a41
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206515"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 文字列式の小文字データを大文字に変換して返します。  

UPPER システム関数では、インデックスは使用されません。 大文字と小文字を区別しない比較を頻繁に行う場合、UPPER システム関数は、大量の RU を消費する可能性があります。 この場合は、UPPER システム関数を使用して比較のたびにデータを正規化するのではなく、挿入時に大文字と小文字を正規化することができます。 その後、SELECT * FROM c WHERE UPPER(c.name) = 'BOB' などのクエリは、単に SELECT * FROM c WHERE c.name = 'BOB' になります。

## <a name="syntax"></a>構文
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   文字列式です。  
  
## <a name="return-types"></a>戻り値の型
  
  文字列式を返します。  
  
## <a name="examples"></a>例
  
  次の例は、クエリ内での `UPPER` の使用方法を示しています  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 結果セットは次のようになります。  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>解説

このシステム関数では、[インデックスは使用](../index-overview.md#index-usage)されません。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
