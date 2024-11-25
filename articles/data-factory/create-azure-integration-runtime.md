---
title: Azure Integration Runtime の作成
titleSuffix: Azure Data Factory & Azure Synapse
description: データのコピーや変換操作のディスパッチに使用される Azure 統合ランタイムを Azure Data Factory と Azure Synapse Analytics で作成する方法を説明します。
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.date: 09/09/2021
author: lrtoyou1223
ms.author: lle
ms.custom: devx-track-azurepowershell, synapse
ms.openlocfilehash: 063d4138a86fb5235562ebb0d2592640f370806e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124760329"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Azure 統合ランタイムを作成して構成する方法
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Integration Runtime (IR) は、異なるネットワーク環境間でデータ統合機能を提供するために Azure Data Factory と Synapse のパイプラインによって使用されるコンピューティング インフラストラクチャです。 IR の詳細については、「[Integration runtime (統合ランタイム)](concepts-integration-runtime.md)」を参照してください。

Azure IR は、ネイティブにデータ移動を実行したり、HDInsight などのコンピューティング サービスにデータ変換操作をディスパッチしたりするための、フル マネージドのコンピューティングを提供します。 Azure IR は Azure 環境でホストされます。また、パブリック ネットワーク環境内にあってパブリック アクセス可能なエンドポイントを持つリソースへの接続をサポートします。

このドキュメントでは、Azure 統合ランタイムを作成して構成する方法について説明します。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>既定の Azure IR
既定では、個々のデータ ファクトリまたは Synapse ワークスペースは、パブリック ネットワーク内のクラウド データ ストアおよびコンピューティング サービスに対する操作をサポートする Azure IR をバックエンドに備えます。 その Azure IR の場所は自動解決されます。 リンクされたサービス定義で **connectVia** プロパティが指定されていない場合、既定の Azure IR が使用されます。 IR の場所を明示的に定義したい場合、または、管理上の目的から、異なる IR でのアクティビティ実行を仮想的にグループ化したい場合に限り、Azure IR を明示的に作成する必要があります。 

## <a name="create-azure-ir"></a>Azure IR を作成する

Azure IR を作成して設定するには、次の手順を使用できます。

### <a name="create-an-azure-ir-via-azure-powershell"></a>Azure PowerShell を使用して Azure IR を作成する
統合ランタイムは、**Set-AzDataFactoryV2IntegrationRuntime** PowerShell コマンドレットを使用して作成できます。 Azure IR を作成するには、このコマンドに対して名前、場所、種類を指定します。 次に示すのは、場所を "West Europe" (西ヨーロッパ) に設定して Azure IR を作成するコマンドの例です。

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Azure IR の場合、タイプを **Managed** に設定する必要があります。 コンピューティングの詳細はクラウドで柔軟に完全管理されるため、指定する必要はありません。 Azure-SSIS IR を作成したい場合、ノード サイズやノード数など、コンピューティングの詳細を指定します。 詳細については、「[Create and Configure Azure-SSIS IR (Azure-SSIS IR の作成と構成)](create-azure-ssis-integration-runtime.md)」を参照してください。

Set-AzDataFactoryV2IntegrationRuntime PowerShell コマンドレットを使用して、既存の Azure IR を構成し、その場所を変更することができます。 Azure IR の場所の詳細については、「[Introduction to integration runtime (統合ランタイムの概要)](concepts-integration-runtime.md)」を参照してください。

### <a name="create-an-azure-ir-via-ui"></a>UI を使用して Azure IR を作成する
UI を使用して Azure IR を作成するには、次の手順を使用します。

1. サービスのホーム ページの一番左にあるウィンドウで [[管理] タブ](./author-management-hub.md)を選択します。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
    
    :::image type="content" source="media/doc-common-process/get-started-page-manage-button.png" alt-text="ホーム ページの [管理] ボタン":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/get-started-page-manage-button-synapse.png" alt-text="ホーム ページの [管理] ボタン":::

2. 左ペインの **[統合ランタイム]** を選択し、 **[+ 新規]** を選択します。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/manage-new-integration-runtime.png" alt-text="左側のウィンドウの統合ランタイムと [+ 新規] ボタンが強調表示されているスクリーンショット。":::
   
    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/manage-new-integration-runtime-synapse.png" alt-text="左側のウィンドウの統合ランタイムと [+ 新規] ボタンが強調表示されているスクリーンショット。":::

3. **[統合ランタイムのセットアップ]** ページで、 **[Azure, Self-Hosted] (Azure、セルフホステッド)** を選択してから、 **[続行]** を選択します。 

1. 次のページで、Azure IR を作成する **[Azure]** を選択してから、 **[続行]** を選択します。
   :::image type="content" source="media/create-azure-integration-runtime/new-azure-integration-runtime.png" alt-text="統合ランタイムの作成":::

1. Azure IR の名前を入力し、 **[作成]** を選択します。
   :::image type="content" source="media/create-azure-integration-runtime/create-azure-integration-runtime.png" alt-text="Azure IR を作成する":::

1. 作成が完了すると、ポップアップ通知が表示されます。 **[統合ランタイム]** ページで、新しく作成された IR が一覧に表示されていることを確認します。

## <a name="use-azure-ir"></a>Azure IR を使用する

Azure IR が作成されたら、リンクされたサービスの定義でその Azure IR を参照することができます。 次に示すのは、上記の手順で作成した Azure 統合ランタイムを、リンクされたサービスの Azure Storage から参照する方法の例です。

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=..."
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>次のステップ
その他のタイプの統合ランタイムの作成方法については、以下の記事を参照してください。

- [Create self-hosted integration runtime (セルフホステッド統合ランタイムの作成)](create-self-hosted-integration-runtime.md)
- [Azure-SSIS 統合ランタイムを作成する](create-azure-ssis-integration-runtime.md)
