---
title: Azure Spring Cloud での問題を自己診断して解決する方法
description: Azure Spring Cloud での問題を自己診断して解決する方法について説明します。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/29/2020
ms.custom: devx-track-java
ms.openlocfilehash: b6997329b8e0be698d83aa5dddc32a09fb23eafb
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015355"
---
# <a name="self-diagnose-and-solve-problems-in-azure-spring-cloud"></a>Azure Spring Cloud での問題の自己診断と解決

**この記事の適用対象:** ✔️ Java ✔️ C#

Azure Spring Cloud 診断は、構成無しでアプリのトラブルシューティングが可能な対話型エクスペリエンスです。 Azure Spring Cloud 診断は、問題点を特定し、問題のトラブルシューティングと解決に役立つ情報を示します。

## <a name="prerequisites"></a>前提条件

この演習を完了するには、以下が必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
* デプロイされた Azure Spring Cloud サービス インスタンス。 [Azure CLI を使用したアプリのデプロイに関するクイックスタート](./quickstart.md)に従って作業を開始してください。
* サービス インスタンスで既に作成してある少なくとも 1 つのアプリケーション。

## <a name="navigate-to-the-diagnostics-page"></a>診断ページに移動する

1. Azure portal にサインインします。
2. Azure Spring Cloud の **[概要]** ページに移動します。
3. 左側のナビゲーションから **[問題の診断と解決]** を選択します。

![診断、解決ダイアログ](media/spring-cloud-diagnose/diagnose-solve-dialog.png)

## <a name="search-logged-issues"></a>ログに記録された問題を検索する

問題を見つけるには、キーワードを入力して検索するか、ソリューション グループを選択して、そのカテゴリ内のすべてを調べることができます。

![問題を検索する](media/spring-cloud-diagnose/search-detectors.png)

**[Config Server Health Check]\(Config Server の正常性の検査\)** 、 **[Config Server Health Status]\(Config Server の正常性の状態\)** 、または **[Config Server Update History]\(Config Server の更新履歴\)** を選択すると、さまざまな結果が表示されます。

![問題のオプション](media/spring-cloud-diagnose/detectors-options.png)

ターゲットの検出機能を見つけ、選択して実行します。 検出機能を実行すると、診断の概要が表示されます。 **[レポート全体の表示]** を選択して診断の詳細を確認するか、 **[Show Tile Menu]\(タイル メニューを表示\)** ボタンを選択して検出機能の一覧に戻ることができます。

![診断の概要](media/spring-cloud-diagnose/summary-diagnostics.png)

[診断の詳細] ページで、右上隅にあるコントローラーを使って診断の時間範囲を変更できます。 さらに多くのメトリックまたはログを表示するには、各診断を切り替えます。 メトリックとログには 15 分の遅延がある可能性があります。

![診断の詳細](media/spring-cloud-diagnose/diagnostics-details.png)

一部の結果には、関連ドキュメントが含まれています。

![関連する詳細](media/spring-cloud-diagnose/related-details.png)

## <a name="next-steps"></a>次のステップ

* [アラートとアクション グループを使用して Spring Cloud のリソースを監視する](./tutorial-alerts-action-groups.md)
* [Azure Spring Cloud Service のセキュリティ コントロール](./concept-security-controls.md)
