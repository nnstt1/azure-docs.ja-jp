---
title: Azure Monitor と Azure Data Explorer 間のクロス サービス クエリ
description: Azure Log Analytics ツールを介して Azure Data Explorer のデータにクエリを実行したり、その逆を行うことで、すべてのデータを 1 か所で結合して分析することができます。
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 7556469049e072c6915baef60083c306753f8717
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727986"
---
# <a name="cross-service-query---azure-monitor-and-azure-data-explorer"></a>クロス サービス クエリ - Azure Monitor と Azure Data Explorer
[Azure Data Explorer](/azure/data-explorer/)、[Application Insights](../app/app-insights-overview.md)、[Log Analytics](../logs/data-platform-logs.md) の間でクロス サービス クエリを作成します。
## <a name="azure-monitor-and-azure-data-explorer-cross-service-querying"></a>Azure Monitor と Azure Data Explorer のクロス サービス クエリ
[Azure Data Explorer と Azure Monitor の間でクロス サービス クエリを作成したり](/azure/data-explorer/query-monitor-data)、[Azure Monitor と Azure Data Explorer の間でクロス サービス クエリを作成したり](./azure-monitor-data-explorer-proxy.md)できます。

例 (Azure Data Explorer のクエリを Log Analytics から実行する):
```kusto
CustomEvents | where aField == 1
| join (adx("Help/Samples").TableName | where secondField == 3) on $left.Key == $right.key
```
ここでは、外側のクエリがワークスペース内のテーブルに対してクエリを実行し、次に新しい "adx ()" 関数を使用して、Azure Data Explorer クラスター内の別のテーブルと結合します (この場合、clustername = help、databasename = samples)。これは、クエリ テキスト内から別のワークスペースに対してクエリを実行できるのと同様です。

## <a name="query-exported-log-analytics-data-from-azure-blob-storage-account"></a>エクスポートされた Log Analytics データに対して Azure Blob ストレージ アカウントからクエリを実行する

Azure Monitor から Azure ストレージ アカウントにデータをエクスポートすると、低コストのリテンション期間が有効になり、ログを別のリージョンに再割り当てすることができます。

Azure Data Explorer を使用して、Log Analytics ワークスペースからエクスポートされたデータのクエリを実行します。 構成されると、ワークスペースから Azure ストレージ アカウントに送信されるサポートされているテーブルが、Azure Data Explorer のデータ ソースとして使用できるようになります。 [Azure Data Explorer を使用して Azure Monitor からエクスポートされたデータのクエリを実行する](../logs/azure-data-explorer-query-storage.md)。

[ストレージからの Azure Data Explorer のクエリのフロー](media\azure-data-explorer-query-storage\exported-data-query.png)

>[!tip] 
> * Log Analytics ワークスペースのすべてのデータを Azure ストレージ アカウントまたはイベント ハブにエクスポートするには、Azure Monitor ログの Log Analytics ワークスペース データ エクスポート機能を使用します。 「[Azure Monitor の Log Analytics ワークスペース データ エクスポート](/azure/data-explorer/query-monitor-data)」を参照してください。

## <a name="next-steps"></a>次のステップ
各項目の詳細情報
* [Azure Data Explorer と Azure Monitor の間のクロス サービス クエリの作成](/azure/data-explorer/query-monitor-data)。 Azure Data Explorer から Azure Monitor のデータのクエリを実行する
* [Azure Monitor と Azure Data Explorer の間のクロス サービス クエリの作成](./azure-monitor-data-explorer-proxy.md)。 Azure Monitor から Azure Data Explorer のデータのクエリを実行する
* [Azure Monitor の Log Analytics ワークスペース データ エクスポート](/azure/data-explorer/query-monitor-data)。 Log Analytics のエクスポートされたデータと Azure Blob ストレージ アカウントをリンクし、クエリを実行します。