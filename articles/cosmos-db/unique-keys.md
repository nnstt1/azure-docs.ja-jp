---
title: Azure Cosmos DB で一意なキーを使用する
description: Azure Cosmos データベースの一意のキーを定義して使用する方法について説明します。 この記事では、一意キーによってデータ整合性の層がどのように追加されるかについても説明します。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/26/2021
ms.reviewer: sngun
ms.openlocfilehash: 951f641c9bded27929a1308d7dfe826a797ae08f
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123037143"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Azure Cosmos DB の一意キー制約
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

一意キーを使用すると、Azure Cosmos コンテナーにデータ整合性のレイヤーが追加されます。 一意キー ポリシーは、Azure Cosmos コンテナーを作成するときに作成します。 一意キーを使用して、論理パーティション内にある 1 つ以上の値が一意であることを保証します。 また、[パーティション キー](partitioning-overview.md)ごとの一意性を保証することもできます。

一意キー ポリシーを使用してコンテナーを作成した後は、論理パーティション内に新しく項目を作成したり既存の項目を更新したりして重複項目を作成することは、一意キー制約で指定されているためできなくなります。 パーティション キーと一意キーを組み合わせることで、コンテナーのスコープ内にある項目の一意性が確保されます。

たとえば、`Email address` を一意キー制約にし、`CompanyID` をパーティション キーにして、Azure Cosmos コンテナーを作成するものとします。 ユーザーのメール アドレスを一意キーとして構成すると、各項目は特定の `CompanyID` 内で一意のメール アドレスを持つようになります。 重複する電子メール アドレスを同じパーティション キー値と組み合わせて、2 つの項目を作成することはできません。 Azure Cosmos DB の SQL (Core) API では、項目は JSON 値として保存されます。 これらの JSON 値では、大文字と小文字が区別されます。 プロパティを一意のキーとして選択すると、そのプロパティに大文字と小文字を区別する値を挿入できます。 たとえば、name プロパティに一意のキーが定義されている場合、"Gaby" は "gaby" とは異なるため、これらの両方をコンテナーに挿入できます。

メール アドレスは同じでも、姓と名とメール アドレスは異なる項目を作成するには、一意キー ポリシーにさらにパスを追加します。 つまり、メール アドレスだけに基づいて一意なキーを作成するのではなく、姓、名、メール アドレスの組み合わせを使用して、一意キーを作成することもできます。 このキーは、複合一意キーと呼ばれます。 その場合、指定した `CompanyID` 内では、3 つの値の一意な組み合わせだけが使用できるようになります。 

たとえば、コンテナーには次の値を使った項目を含めることができます。これらの各項目は、一意キー制約に従っています。

|CompanyID|名|姓|電子メール アドレス|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

上の表で示されている値の組み合わせで別の項目を挿入しようとすると、エラーが発生します。 そのエラーでは、一意キー制約を満たしていないことが示されます。 `Resource with specified ID or name already exists` と `Resource with specified ID, name, or unique index already exists` のどちらかのメッセージが返されます。 

## <a name="define-a-unique-key"></a>一意キーを定義する

一意キーは、Azure Cosmos コンテナーの作成時にのみ定義できます。 一意キーは論理パーティションに対してスコープ指定されます。 上の例では、郵便番号に基づいてコンテナーをパーティション分割すると、各論理パーティションに重複する項目が存在するようになります。 一意キーを作成するときは、次の特性を考慮してください。

* 異なる一意キーを使用するように、既存のコンテナーを更新することはできません。 つまり、一意キー ポリシーを指定してコンテナーを作成した後で、ポリシーを変更することはできません。

* 既存のコンテナーに対して一意キーを設定するには、一意キー制約を持つ新しいコンテナーを作成します。 適切なデータ移行ツールを使用して、既存のコンテナーから新しいコンテナーにデータを移動します。 SQL コンテナーの場合は、[データ移行ツール](import-data.md)を使用してデータを移動します。 MongoDB コンテナーの場合は、[mongoimport.exe または mongorestore.exe](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) を使用してデータを移動します。

* 一意キー ポリシーでは、最大で 16 個のパス値を使用できます。 たとえば、`/firstName`、`/lastName`、`/address/zipCode` を値として使用できます。 一意キー ポリシーには、それぞれ最大 10 個の一意キー制約または一意なキーの組み合わせを含めることができます。 一意インデックス制約ごとの結合されたパスの長さは、60 バイトを超えないようにする必要があります。 上の例では、姓、名、メール アドレスの組み合わせで 1 つの制約になっています。 この制約では、可能な 16 個のパスのうち 3 個が使用されています。

* コンテナーに一意キー ポリシーがある場合、項目を作成、更新、削除するための[要求ユニット (RU)](request-units.md) 料金が若干高くなります。

* スパースな一意キーはサポートされていません。 一部の一意パスの値がない場合、それらは null 値として扱われて、一意性制約に組み込まれます。 そのため、この制約を満たすには、null 値を持つことができる項目は 1 つだけです。

* 一意キー名では大文字と小文字が区別されます。 たとえば、あるコンテナーの一意キー制約が `/address/zipcode` に設定されているとします。 データに `ZipCode` という名前のフィールドがあると、`zipcode` と `ZipCode` は同じではないため、一意キーとして "null" が挿入されます。 このように大文字と小文字が区別されることにより、ZipCode を含む他のすべてのレコードは、重複する "null" が一意キー制約に違反するため、挿入できません。

## <a name="next-steps"></a>次のステップ

* [論理パーティション](partitioning-overview.md)の詳細を確認する
* コンテナーの作成時に[一意キーを定義する方法](how-to-define-unique-keys.md)を確認する
* Azure Cosmos DB への移行のための容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
    * 知っていることが既存のデータベース クラスター内の仮想コアとサーバーの数のみである場合は、[仮想コアまたは仮想 CPU を使用した要求ユニットの見積り](convert-vcore-to-request-unit.md)に関するページを参照してください。 
    * 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB 容量計画ツールを使用した要求ユニットに見積り](estimate-ru-with-capacity-planner.md)に関するページを参照してください
