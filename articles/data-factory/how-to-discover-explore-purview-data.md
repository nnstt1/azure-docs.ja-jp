---
title: Purview を使用した ADF でのデータの検出と探索
description: Purview を使用した Azure Data Factory でのデータの検出と探索の方法について説明します
ms.service: data-factory
ms.topic: conceptual
author: linda33wj
ms.author: jingwang
ms.custom: seo-lt-2019
ms.date: 08/10/2021
ms.openlocfilehash: 037e7fadd84d3b3b7405507a6116ed1e8c0ef59d
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427050"
---
# <a name="discover-and-explore-data-in-adf-using-purview"></a>Purview を使用した ADF でのデータの検出と探索

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、Azure Purview アカウントを Data Factory に登録します。 その接続により、Azure Purview の資産を検出し、ADF 機能を使用してそれらを操作できるようになります。 

ADF では、次のタスクを実行できます。 
- 上部にある検索ボックスを使用して、キーワードに基づいて Purview の資産を検索する 
- メタデータ、系列、注釈に基づいてデータを理解する 
- リンクされたサービスまたはデータセットを使用して、これらのデータをデータ ファクトリに接続する 

## <a name="prerequisites"></a>前提条件 

- [Azure Purview アカウント](../purview/create-catalog-portal.md) 
- [Data Factory](./quickstart-create-data-factory-portal.md) 
- [Azure Purview アカウントを Data Factory に接続する](./connect-data-factory-to-azure-purview.md) 

## <a name="using-azure-purview-in-data-factory"></a>Data Factory での Azure Purview の使用 

Data Factory で Azure Purview を使用するには、その Purview アカウントにアクセスできる必要があります。 Data Factory では、Purview のアクセス許可をパススルーします。 たとえば、キュレーター アクセス許可ロールがある場合は、Azure Purview によってスキャンされたメタデータを編集できます。 

### <a name="data-discovery-search-datasets"></a>データ検出: データセットの検索 

Azure Purview によって登録およびスキャンされたデータを検出するには、Data Factory ポータルの上部中央にある検索バーを使用します。 すべての組織データを検索するために、必ず Azure Purview を選択してください。 

:::image type="content" source="./media/data-factory-purview/search-dataset.png" alt-text="データセットに対する実行のスクリーンショット。":::

### <a name="actions-that-you-can-perform-over-datasets-with-data-factory-resources"></a>Data Factory リソースを使用してデータセットに対して実行できるアクション 
Azure Purview によって検索したデータに対して、リンクされたサービス、データセット、またはデータフローを直接作成できます。

:::image type="content" source="./media/data-factory-purview/actions-over-purview-data.png" alt-text="Azure Purview によって検索したデータに対して、リンクされたサービス、データセット、またはデータフローを直接作成できることを示すスクリーンショット。":::

##  <a name="nextsteps"></a>次のステップ 

[チュートリアル: Data Factory 系列データを Azure Purview にプッシュする](turorial-push-lineage-to-purview.md)

[Azure Purview アカウントを Data Factory に接続する](connect-data-factory-to-azure-purview.md) 

[Azure Purview Data Catalog でデータを検索する方法](../purview/how-to-search-catalog.md)