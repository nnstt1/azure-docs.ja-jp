---
title: Advanced Threat Protection の構成
description: Advanced Threat Protection では、Azure SQL Database のデータベースに対する潜在的なセキュリティの脅威を示す異常なデータベース アクティビティが検出されます
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 12/01/2020
ms.openlocfilehash: f2402fe795141a00ac488cb163930cbde58141f6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325659"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>Advanced Threat Protection for Azure SQL Database を構成する
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Advanced Threat Protection](threat-detection-overview.md) for Azure SQL Database では、データベースへのアクセスや悪用を試みる、通常とは異なる、害を及ぼす可能性がある異常なアクティビティが検出されます。 Advanced Threat Protection によって、**SQL インジェクションの可能性**、**通常とは異なる場所またはデータ センターからのアクセス**、**通常とは異なるプリンシパルまたは有害な可能性があるアプリケーションからのアクセス**、**SQL 資格情報に対するブルート フォース攻撃** を特定できます。詳細については、[Advanced Threat Protectionアラート](threat-detection-overview.md#alerts)に関する記事を参照してください。

検出された脅威に関する通知は、[電子メールの通知](threat-detection-overview.md#explore-detection-of-a-suspicious-event)または [Azure portal](threat-detection-overview.md#explore-alerts-in-the-azure-portal) で受け取ることができます。

[Advanced Threat Protection](threat-detection-overview.md) は、高度な SQL セキュリティ機能の統合パッケージである [Microsoft Defender for SQL](azure-defender-for-sql.md) オファリングの一部です。 Advanced Threat Protection は、中央の Microsoft Defender for SQL ポータル経由でアクセスし、管理できます。

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Azure portal で Advanced Threat Protection を設定する

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 保護するサーバーの構成ページに移動します。 セキュリティの設定で、 **[Defender for Cloud]** を選択します。
3. **Microsoft Defender for SQL** の構成ページで、以下の操作を行います。

   - サーバーで **Microsoft Defender for SQL** を有効にします。
   - **[Advanced Threat Protection 設定]** の **[アラートの送信先]** テキスト ボックスで、異常なデータベース アクティビティが検出されたときにセキュリティ アラートを受け取るメール アドレスの一覧を指定します。
   
   :::image type="content" source="media/azure-defender-for-sql/set-up-advanced-threat-protection.png" alt-text="Advanced Threat Protection の設定":::

## <a name="set-up-advanced-threat-protection-using-powershell"></a>PowerShell を使用して Advanced Threat Protection を設定する

スクリプトの例については、[PowerShell を使用した監査と Advanced Threat Protection の構成](scripts/auditing-threat-detection-powershell-configure.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

- [Advanced Threat Protection](threat-detection-overview.md) の詳細について学習します。
- [SQL Managed Instance での Advanced Threat Protection](../managed-instance/threat-detection-configure.md) の詳細について学習します。  
- [Microsoft Defender for SQL](azure-defender-for-sql.md) の詳細について学習します。
- [監査](../../azure-sql/database/auditing-overview.md)の詳細について学習します。
- [Microsoft Defender for Cloud](../../security-center/security-center-introduction.md) の詳細について学習します。
- 価格の詳細については、[SQL Database の価格のページ](https://azure.microsoft.com/pricing/details/sql-database/)を参照してください。
