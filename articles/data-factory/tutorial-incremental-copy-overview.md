---
title: ソース データ ストアからターゲット データ ストアにデータを増分コピーする
description: これらのチュートリアルでは、ソース データ ストアからターゲット データ ストアにデータを増分コピーする方法について説明します。 1 つ目は、単一のテーブルからデータをコピーするものです。
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 543acb129d23a0b74434535306aca801d2f5fdd2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763635"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>ソース データ ストアからターゲット データ ストアにデータを増分読み込みする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

データ統合ソリューションでは、初回のフル データ読み込みの後、増分 (または差分) データを読み込む手法が広く利用されています。 このセクションの各チュートリアルでは、Azure Data Factory を使用して、データを増分読み込みするさまざまな方法を紹介しています。

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>基準値を使用してデータベースから差分データを読み込む
このケースでは、ソース データベースにおける基準値を定義します。 基準値とは、最終更新タイムスタンプやインクリメントされるキーを格納する列のことです。 差分読み込みソリューションでは、古い基準値から新しい基準値までの間に生じた変更済みのデータが読み込まれます。 このアプローチのワークフローを表したのが次の図です。 

:::image type="content" source="media/tutorial-incremental-copy-overview/workflow-using-watermark.png" alt-text="基準値を使用するためのワークフロー":::

具体的な手順については、次のチュートリアルを参照してください。 
- [Azure SQL Database 内の 1 つのテーブルから Azure Blob Storage にデータを増分コピーする](tutorial-incremental-copy-powershell.md)
- [SQL Server インスタンスにある複数のテーブルから Azure SQL Database にデータを増分コピーする](tutorial-incremental-copy-multiple-tables-powershell.md)

テンプレートについては、以下を参照してください。
- [制御テーブルを使用した差分コピー](solution-template-delta-copy-with-control-table.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Change Tracking テクノロジを使用して SQL DB から差分データを読み込む
Change Tracking テクノロジは、SQL Server と Azure SQL Database において、アプリケーションのための効率的な変更追跡メカニズムとなる軽量ソリューションです。 挿入、更新、削除されたデータをアプリケーションから簡単に特定することができます。 

このアプローチのワークフローを表したのが次の図です。

:::image type="content" source="media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png" alt-text="Change Tracking を使用するためのワークフロー":::

具体的な手順については、次のチュートリアルを参照してください。 <br/>
- [Change Tracking テクノロジを使用して Azure SQL Database から Azure Blob Storage にデータを増分コピーする](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>LastModifiedDate を使用して新しいファイルと変更済みのファイルを読み込む
LastModifiedDate を使用して、新しいファイルと変更されたファイルのみをターゲット ストアにコピーすることができます。 ADF はソース ストアのすべてのファイルをスキャンし、LastModifiedDate に基づいてファイル フィルターを適用して、前回以降の新しいファイルと更新されたファイルのみをターゲット ストアにコピーします。  ADF で大量のファイルをスキャンするが、数個のファイルしかコピー先にコピーしない場合、ファイルのスキャン プロセスがあるため、やはり長い時間がかかることに注意してください。   

具体的な手順については、次のチュートリアルを参照してください。 <br/>
- [LastModifiedDate に基づいて新しいファイルと変更済みのファイルを Azure Blob Storage から Azure Blob Storage に増分コピーする](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

テンプレートについては、以下を参照してください。
- [LastModifiedDate を基準にした新しいファイルのコピー](solution-template-copy-new-files-lastmodifieddate.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>時間でパーティション分割されたフォルダーまたはファイルの名前を使用して新しいファイルを読み込む
ファイルまたはフォルダーが時間 (ファイル名またはフォルダー名に含まれるタイムスライス情報) でパーティション分割されているときに (例: /yyyy/mm/dd/file.csv)、新しいファイルのみをコピーすることができます。 これは、新しいファイルを増分読み込みする場合に最も効率のよいアプローチです。 

具体的な手順については、次のチュートリアルを参照してください。 <br/>
- [時間でパーティション分割されたフォルダー名またはファイル名に基づく新しいファイルを Azure Blob Storage から Azure Blob Storage に増分コピーする](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>次のステップ
次のチュートリアルに進みます。 

> [!div class="nextstepaction"]
>[Azure SQL Database 内の 1 つのテーブルから Azure Blob Storage にデータを増分コピーする](tutorial-incremental-copy-powershell.md)
