---
title: Azure Portal で Log Analytics ワークスペースを作成する | Microsoft Docs
description: Azure Portal で Log Analytics ワークスペースを作成して、クラウド環境とオンプレミス環境から管理ソリューションを有効にし、データを収集できるようにします。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/18/2021
ms.openlocfilehash: 3d9e075e50b4be0a21f4138a49faf8309474ad9c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440053"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Azure Portal で Log Analytics ワークスペースを作成する
Azure portal で、 **[Log Analytics ワークスペース]** メニューを使用して、Log Analytics ワークスペースを作成します。 Log Analytics ワークスペースは、Azure Monitor ログ データ用の一意の環境です。 各ワークスペースには、独自のデータ リポジトリと構成があり、データ ソースとソリューションは、特定のワークスペースにデータを格納するように構成されます。 次のソースからデータを収集しようとする場合、Log Analytics ワークスペースが必要です。

* サブスクリプション内の Azure リソース
* System Center Operations Manager によって監視されているオンプレミスのコンピューター
* Configuration Manager のデバイス コレクション 
* Azure ストレージからの診断またはログ データ

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-azure-portal"></a>Azure Portal にサインインする
Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。 

## <a name="create-a-workspace"></a>ワークスペースの作成
Azure Portal で、 **[すべてのサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics ワークスペース]** を選択します。

![Azure portal](media/quick-create-workspace/azure-portal-01.png)
  
**[追加]** をクリックし、次のオプションの値を指定します。

   * 関連付ける **サブスクリプション** をドロップダウン リストから選択します (既定値が適切でない場合)。
   * **[リソース グループ]** で、設定済みの既存のリソース グループを使用することを選択するか、新しいリソース グループを作成します。  
   * 新しい **Log Analytics ワークスペース** の名前 (*DefaultLAWorkspace* など) を指定します。 この名前は、リソース グループごとに一意である必要があります。
   * 使用可能な **リージョン** を選択します。  詳細については、[Log Analytics を使用できるリージョン](https://azure.microsoft.com/regions/services/)に関するページを参照し、 **[製品を検索する]** フィールドから Azure Monitor を検索してください。  


        ![Log Analytics リソース ブレードの作成](media/quick-create-workspace/create-workspace.png)  


**[確認および作成]** をクリックして設定を見直し、 **[作成]** をクリックしてワークスペースを作成します。 これによって従量課金制という既定の価格レベルが選択され、課金対象となる量のデータが収集され始めるまで料金は一切発生しません。 その他の価格レベルの詳細については、[Log Analytics の価格の詳細](https://azure.microsoft.com/pricing/details/log-analytics/)に関するページを参照してください。



## <a name="troubleshooting"></a>トラブルシューティング
過去 14 日間に削除され、[論理的な削除状態](../logs/delete-workspace.md#soft-delete-behavior)になっているワークスペースを作成した場合は、ワークスペースの構成に応じて、操作の結果が異なる可能性があります。
1. 削除されたワークスペースと同じワークスペース名、リソース グループ、サブスクリプション、リージョンを指定した場合は、データ、構成、および接続されたエージェントを含むワークスペースが復旧されます。
2. ワークスペース名は、各リソース グループ内で一意である必要があります。 既に存在しているワークスペース名を使用する場合は、リソース グループ内の論理的な削除でも、ワークスペース名 'workspace-name' が一意ではない、または競合しているというエラーが表示されます。 ご自分のワークスペースの論理的な削除をオーバーライドし、完全に削除して同じ名前の新しいワークスペースを作成するには、次の手順に従って、最初にワークスペースを回復してから、完全な削除を実行します。
   - ワークスペースを[回復します](../logs/delete-workspace.md#recover-workspace)
   - ワークスペースを[完全に削除](../logs/delete-workspace.md#permanent-workspace-delete)します
   - 同じワークスペース名を使用して新しいワークスペースを作成します

## <a name="next-steps"></a>次のステップ
使用できるワークスペースが用意されたので、管理テレメトリの収集の構成、ログ検索の実行によるデータの分析、管理ソリューションの追加による追加データと分析的な考察の提供を行うことができます。 

* ワークスペースの正常性を監視するためのアラート ルールを作成するには、「[Azure Monitor で Log Analytics ワークスペースの正常性を監視する](../logs/monitor-workspace.md)」を参照してください。 
* Microsoft Azure Diagnostics または Azure ストレージを使用して Azure リソースからデータを収集できるようにするには、「[Log Analytics で Azure サービスのログとメトリックを使用できるように収集する](../essentials/resource-logs.md#send-to-log-analytics-workspace)」を参照してください。
