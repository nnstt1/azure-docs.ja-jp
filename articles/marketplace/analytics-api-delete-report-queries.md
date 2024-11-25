---
title: レポート クエリの削除 API
description: この API を使用して、コマーシャル マーケットプレース分析のユーザー定義クエリを削除します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 927534b11bc00b3ec21db2ab5efef2eb09104976
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748366"
---
# <a name="delete-report-queries-api"></a>レポート クエリの削除 API

この API ではユーザー定義クエリを削除します。

**要求の構文**

| **方法** | **要求 URI** |
| --- | --- |
| DELETE | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/{queryId}` |

**要求ヘッダー**

| **Header** | **Type** | **説明** |
| --- | --- | --- |
| 承認 | string | 必須。 `Bearer <token>` という形式の Azure Active Directory (Azure AD) アクセス トークン |
| Content-Type | string | `Application/JSON` |

**パス パラメーター**

| **パラメーター名** | **Type** | **説明** |
| --- | --- | --- |
| `queryId` | string | この引数で指定された ID を持つクエリのみの詳細を取得するフィルター |

**クエリ パラメーター**

なし

**要求ペイロード**

なし

**用語集**

None

**応答**

応答のペイロードは、JSON 形式で次のように構成されます。

応答コード: 200、400、401、403、404、500

応答ペイロード:

```json
{
  "Value": [
    {
      "QueryId": "string",
      "Name": "string",
      "Description": "string",
      "Query": "string",
      "Type": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string"
    }
  ],
  "TotalCount": 0,
  "Message": "string",
  "StatusCode": 0
}
```

**用語集**

次の表では、応答内の要素の主な定義を示しています。

| **パラメーター** | **説明** |
| --- | --- |
| `QueryId` | 削除されたクエリの一意の UUID。 |
| `Name` | 削除されたクエリの名前 |
| `Description` | 削除されたクエリの説明 |
| `Query` | 削除されたクエリのレポート クエリ文字列 |
| `Type` | userDefined |
| `User` | クエリを作成したユーザーの ID |
| `CreatedTime` | クエリが作成された時刻 |
| `ModifiedTime` | [Null] |
| `TotalCount` | Value 配列内のデータセットの数 |
| `StatusCode` | 結果コード。 200、400、401、403、500 の値になる可能性があります |
