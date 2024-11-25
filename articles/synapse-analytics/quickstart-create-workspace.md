---
title: 'クイックスタート: Synapse ワークスペースを作成する'
description: このガイドの手順に従って、Synapse ワークスペースを作成します。
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 09/03/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.custom: subject-rbac-steps
ms.openlocfilehash: 0f593d801bdcc477d6084a395630211393ffa9c7
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2021
ms.locfileid: "113217289"
---
# <a name="quickstart-create-a-synapse-workspace"></a>クイック スタート:Synapse ワークスペースを作成する
このクイックスタートでは、Azure portal を使用して Azure Synapse ワークスペースを作成する手順について説明します。

## <a name="create-a-synapse-workspace"></a>Synapse ワークスペースを作成する

1. [Azure portal](https://portal.azure.com) を開き、上部で **Synapse** を検索します。
1. 検索結果の **[サービス]** で、 **[Azure Synapse Analytics]** を選択します。
1. **[追加]** を選択してワークスペースを作成します。
1. **[基本]** タブで、ワークスペースに一意の名前を付けます。 このドキュメントでは、**mysworkspace** を使用します。
1. ワークスペースを作成するには、ADLSGEN2 アカウントが必要です。 最も簡単な方法は、新しいものを作成することです。 既存のものを再利用する場合は、追加の構成を行う必要があります。 
1. オプション 1: 新しい ADLSGEN2 アカウントの作成 
    1. **[Data Lake Storage Gen 2 の選択]** で、 **[新規作成]** をクリックして、「**contosolake**」という名前を指定します。
    1. **[Data Lake Storage Gen 2 の選択]** で、 **[ファイル システム]** をクリックして、「**users**」という名前を指定します。
1. オプション 2: このドキュメントの下部にある「**ストレージ アカウントを準備する**」の手順を参照してください。
1. Azure Synapse ワークスペースは、このストレージ アカウントを "プライマリ" ストレージ アカウントとして使用し、ワークスペース データを格納するコンテナーを使用します。 このワークスペースでは、データが Apache Spark テーブルに格納されます。 **/synapse/workspacename** というフォルダーに Spark アプリケーション ログが格納されます。
1. **[確認と作成]**  >  **[作成]** の順に選択します。 ワークスペースの準備は数分で完了します。

> [!NOTE]
> Azure Synapse ワークスペースを作成した後、ワークスペースを別の Azure Active Directory テナントに移動することはできません。 サブスクリプションの移行またはその他のアクションを使用してこれを行うと、ワークスペース内の成果物にアクセスできなくなる可能性があります。
> また、現在、[クラウド ソリューション プロバイダー (CSP)](/partner-center/csp-overview) サブスクリプションで Synapse Analytics ワークスペースを作成することはできません。

## <a name="open-synapse-studio"></a>Synapse Studio を開く

Azure Synapse ワークスペースが作成された後、Synapse Studio を開く方法は 2 つあります。

* [Azure portal](https://portal.azure.com) で Synapse ワークスペースを開きます。 **[概要]** セクションの上部にある **[Synapse Studio の起動]** を選択します。
* `https://web.azuresynapse.net` にアクセスし、ワークスペースにサインインします。

## <a name="prepare-an-existing-storage-account-for-use-with-azure-synapse-analytics"></a>Azure Synapse Analytics を使用するために既存のストレージ アカウントを準備する

1. [Azure Portal](https://portal.azure.com)を開きます。
1. 既存の ADLSGEN2 ストレージ アカウントに移動する
1. **[アクセス制御 (IAM)]** を選択します。
1. **[追加]**  >  **[ロールの割り当ての追加]** を選択して、[ロールの割り当ての追加] ページを開きます。
1. 次のロールを割り当てます。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」を参照してください。
    
    | 設定 | 値 |
    | --- | --- |
    | Role | 所有者とストレージ BLOB のデータ所有者 |
    | アクセスの割り当て先 | ユーザー |
    | メンバー | ユーザー名 |

    ![Azure portal でロール割り当てページを追加します。](../../includes/role-based-access-control/media/add-role-assignment-page.png)
1. 左ペインで **[コンテナー]** を選択し、コンテナーを作成します。
1. コンテナーには任意の名前を付けることができます。 このドキュメントでは、このコンテナーに **users** という名前を付けます。
1. 既定の設定である **[パブリック アクセス レベル]** をそのまま使用し、 **[作成]** を選択します。

### <a name="configure-access-to-the-storage-account-from-your-workspace"></a>ワークスペースからストレージ アカウントへのアクセスを構成する

Azure Synapse ワークスペースのマネージド ID には、既にストレージ アカウントへのアクセス権がある可能性があります。 確認するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) を開き、ワークスペース用に選択したプライマリ ストレージ アカウントを開きます。
1. **[アクセス制御 (IAM)]** を選択します。
1. **[追加]**  >  **[ロールの割り当ての追加]** を選択して、[ロールの割り当ての追加] ページを開きます。
1. 次のロールを割り当てます。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」を参照してください。
    
    | 設定 | 値 |
    | --- | --- |
    | Role | ストレージ BLOB データ共同作成者 |
    | アクセスの割り当て先 | マネージド ID |
    | メンバー | マイ ワークスペース  |

    > [!NOTE]
    > マネージド ID の名前は、ワークスペース名でもあります。

    ![Azure portal でロール割り当てページを追加します。](../../includes/role-based-access-control/media/add-role-assignment-page.png)
1. **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

* [専用 SQL プールを作成する](quickstart-create-sql-pool-studio.md) 
* [サーバーレス Apache Spark プールを作成する](quickstart-create-apache-spark-pool-portal.md)
* [サーバーレス SQL プールを使用する](quickstart-sql-on-demand.md)