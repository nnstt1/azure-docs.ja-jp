---
title: 動的データ マスク
description: 動的データ マスキングでは、Azure SQL Database、Azure SQL Managed Instance、Azure Synapse Analytics の特権のないユーザーに対して機密データをマスクすることにより、機密データの公開を制限します
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 09/12/2021
tags: azure-synpase
ms.openlocfilehash: 8a3740a228aa03a23f3584c3412b8451ebacc35e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812402"
---
# <a name="dynamic-data-masking"></a>動的データ マスク 
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database、Azure SQL Managed Instance、および Azure Synapse Analytics は、動的データ マスキングをサポートしています。 動的データ マスクは、特権のないユーザーに対してデリケートなデータをマスクし、データの公開を制限します。 

動的データ マスクでは、公開するデリケートなデータの量を指定することで、デリケートなデータに対する未承認のアクセスを防ぎ、アプリケーション レイヤーへの影響は最小限に抑えられます。 これはポリシー ベースのセキュリティ機能です。これにより、データベース内のデータはそのままで、指定されたデータベース フィールドに対するクエリの結果セットで機微なデータを非表示にすることができます。

たとえば、コール センターのサービス担当者は、メール アドレスのいくつかの文字を確認することによって発信者を識別できます。ただし、完全なメール アドレスをサービス担当者に開示すべきではありません。 クエリの結果セット内のすべての電子メール アドレスをマスクするマスク ルールを定義できます。 別の例として、開発者は、適切なデータ マスクを定義し、個人データを保護し、法令遵守規定に違反することなくトラブルシューティングの目的で運用環境に対して照会を行うことができます。

## <a name="dynamic-data-masking-basics"></a>動的データ マスキングの基礎

Azure portal で動的データ マスキング ポリシーを設定するには、SQL Database 構成ペインの **[セキュリティ]** の下にある **[動的データ マスキング]** ブレードを選択します。 SQL Managed Instance の場合、この機能はポータルを使用して設定できません。 詳細については、「 [Dynamic Data Masking](/sql/relational-databases/security/dynamic-data-masking)」を参照してください。

### <a name="dynamic-data-masking-policy"></a>動的データ マスク ポリシー

* **マスクから除外する SQL ユーザー** - SQL クエリの結果でデータがマスクされない SQL ユーザーまたは Azure AD の ID のセット。 管理者特権を持つユーザーは常にマスクから除外され、マスクのない元のデータを見ることができます。
* **マスク ルール** - マスクされる指定のフィールドと使用されるマスク関数を定義するルールのセット。 データベースのスキーマ名、テーブル名、列名を使用し、指定のフィールドを定義できます。
* **マスク関数** - さまざまなシナリオに対応してデータの公開を制御する方法のセット。

| マスク関数 | マスク ロジック |
| --- | --- |
| **[Default]** |**指定のフィールドのデータ型に応じたフル マスク**<br/><br/>• 文字列データ型 (nchar、ntext、nvarchar) のフィールドのサイズが 4 文字未満の場合は、XXXX またはそれ未満の数の X を使用します。<br/>• 数値データ型 (bigint、bit、decimal、int、money、numeric、smallint、smallmoney、tinyint、float、real) の場合は、値 0 を使用します。<br/>• 日付/時刻データ型 (date、datetime2、datetime、datetimeoffset、smalldatetime、time) の場合は、01-01-1900 を使用します。<br/>• SQL バリアントの場合は、現在の型の既定値が使用されます。<br/>• XML の場合は、ドキュメント \<masked/> が使用されます。<br/>• 特殊なデータ型 (タイムスタンプ テーブル、hierarchyid、GUID、binary、image、varbinary 空間型) の場合は、空の値を使用します。 |
| **クレジット カード** |クレジット カードの形式でプレフィックスとして定数文字列を追加し、**指定のフィールドの末尾 4 桁を公開するマスク方法**。<br/><br/>XXXX-XXXX-XXXX-1234 |
| **Email** |電子メール アドレスの形式でプレフィックスとして定数文字列を使用して、**最初の文字を公開し、ドメインを XXX.com に置き換えるマスク方法**。<br/><br/>aXX@XXXX.com |
| **ランダムな数値** |選択した境界と実際のデータ型に応じて **乱数を生成するマスク方法**。 指定された境界が等しい場合、マスク関数は定数になります。<br/><br/>![乱数を生成するためのマスク方法を示すスクリーンショット。](./media/dynamic-data-masking-overview/1_DDM_Random_number.png) |
| **カスタム テキスト** |間にカスタム埋め込み文字列を追加し、**最初と最後の文字を公開するマスク方法**。 元の文字列が公開されたプレフィックスやサフィックスより短い場合、埋め込み文字列のみが使用されます。 <br/>prefix[padding]suffix<br/><br/>![ナビゲーション ウィンドウ](./media/dynamic-data-masking-overview/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>マスクが推奨されるフィールド

DDM の推奨エンジンでは、データベースの特定のフィールドに「機密データの可能性あり」の注意が付けられます。この注意を参考にマスク候補を選択できます。 ポータルの動的データ マスク ブレードには、データベースの推奨列が表示されます。 1 つまたは複数の列の **[マスクの追加]** をクリックし、 **[保存]** をクリックするだけでそれらのフィールドにマスクを適用できます。

## <a name="manage-dynamic-data-masking-using-t-sql"></a>T-SQL を使用して動的データ マスキングを管理する

- 動的データ マスクを作成するには、[「動的データ マスクを作成する」](/sql/relational-databases/security/dynamic-data-masking#creating-a-dynamic-data-mask)を参照してください。
- 既存の列にマスクを追加するか、マスクを編集するには、「[既存の列のマスクを追加または編集する](/sql/relational-databases/security/dynamic-data-masking#adding-or-editing-a-mask-on-an-existing-column)」を参照してください。
- マスクされていないデータを表示するアクセス許可を付与するには、「[アクセス許可を付与して、マスクが解除されたデータを表示する](/sql/relational-databases/security/dynamic-data-masking#granting-permissions-to-view-unmasked-data)」を参照してください。
- 動的データ マスクをドロップするには、「[動的データ マスクをドロップする](/sql/relational-databases/security/dynamic-data-masking#dropping-a-dynamic-data-mask)」を参照してください。

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>PowerShell コマンドレットを使用して、ご使用のデータベースの動的データ マスクを設定する

### <a name="data-masking-policies"></a>データ マスキングのポリシー

- [Get-AzSqlDatabaseDataMaskingPolicy](/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingPolicy)
- [Set-AzSqlDatabaseDataMaskingPolicy](/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingPolicy)

### <a name="data-masking-rules"></a>データ マスキングのルール

- [Get-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingRule)
- [New-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/New-AzSqlDatabaseDataMaskingRule)
- [Remove-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Remove-AzSqlDatabaseDataMaskingRule)
- [Set-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingRule)

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-rest-api"></a>REST API を使用してデータベース用の動的データ マスクを設定する

REST API を使用して、データ マスク ポリシーおよびルールをプログラムで管理できます。 公開された REST API では、次の操作がサポートされます。

### <a name="data-masking-policies"></a>データ マスキングのポリシー

- [作成または更新](/rest/api/sql/2014-04-01/datamaskingpolicies/createorupdate):データベース データ マスク ポリシーを作成または更新します。
- [取得](/rest/api/sql/2014-04-01/datamaskingpolicies/get):データベース データ マスク ポリシーを取得します。 

### <a name="data-masking-rules"></a>データ マスキングのルール

- [作成または更新](/rest/api/sql/2014-04-01/datamaskingrules/createorupdate):データベース データ マスク ルールを作成または更新します。
- [データベース別の一覧表示](/rest/api/sql/2014-04-01/datamaskingrules/listbydatabase):データベース データ マスク ルールの一覧を取得します。

## <a name="permissions"></a>アクセス許可

動的データ マスキングを構成する組み込みロールは以下のとおりです。
- [SQL Security Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager)
- [SQL DB Contributor](../../role-based-access-control/built-in-roles.md#sql-db-contributor)
- [SQL Server Contributor](../../role-based-access-control/built-in-roles.md#sql-server-contributor)

動的データ マスキングを使用するために必要なアクションは以下のとおりです。

読み取り/書き込み:
- Microsoft.Sql/servers/databases/dataMaskingPolicies/* Read:
- Microsoft.Sql/servers/databases/dataMaskingPolicies/read Write:
-   Microsoft.Sql/servers/databases/dataMaskingPolicies/write

T-SQL コマンドで動的データ マスキングを使用する場合のアクセス許可の詳細については、「[アクセス許可](/sql/relational-databases/security/dynamic-data-masking#permissions)」を参照してください

## <a name="see-also"></a>関連項目

- SQL Server の[動的データ マスク](/sql/relational-databases/security/dynamic-data-masking)。
- Channel 9 の Data Exposed エピソード ([Azure SQL 動的データ マスクの粒度の細かいアクセス許可](https://channel9.msdn.com/Shows/Data-Exposed/Granular-Permissions-for-Azure-SQL-Dynamic-Data-Masking))。
