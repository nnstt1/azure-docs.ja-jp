---
title: Azure Data Factory を使用した SSIS パッケージの呼び出し - ストアド プロシージャ アクティビティ
description: この記事では、ストアド プロシージャ アクティビティを使用して SQL Server Integration Services (SSIS) パッケージを Azure Data Factory パイプラインから呼び出す方法を説明します。
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.devlang: powershell
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: jingwang
ms.openlocfilehash: 7f6b574bd24d7bd284d985aeb9a129d2143819e1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131016513"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Azure Data Factory のストアド プロシージャ アクティビティを使用して SSIS パッケージを呼び出す
この記事では、ストアド プロシージャ アクティビティを使用して SSIS パッケージを Azure Data Factory パイプラインから呼び出す方法を説明します。 

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[ストアド プロシージャ アクティビティを使用した SSIS パッケージの呼び出し](../how-to-invoke-ssis-package-stored-procedure-activity.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

### <a name="azure-sql-database"></a>Azure SQL データベース 
この記事のチュートリアルでは、Azure SQL Database を使用します。 Azure SQL Managed Instance を使うこともできます。

### <a name="create-an-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムを作成します
Azure-SSIS 統合ランタイムがない場合は、[SSIS パッケージのデプロイに関するチュートリアル](../tutorial-deploy-ssis-packages-azure.md)の手順に従って作成します。 Data Factory バージョン 1 を使用して Azure-SSIS 統合ランタイムを作成することはできません。 

## <a name="azure-powershell"></a>Azure PowerShell
このセクションでは、Azure PowerShell を使用して、SSIS パッケージを呼び出すストアド プロシージャ アクティビティを含む Data Factory パイプラインを作成します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Azure PowerShell のインストールと構成の方法](/powershell/azure/install-az-ps)に関するページの手順に従って、最新の Azure PowerShell モジュールをインストールしてください。

### <a name="create-a-data-factory"></a>Data Factory の作成
次の手順では、データ ファクトリを作成する方法を説明します。 このデータ ファクトリにストアド プロシージャ アクティビティを含むパイプラインを作成します。 ストアド プロシージャ アクティビティが SSISDB データベース内でストアド プロシージャを実行して、SSIS パッケージを実行します。

1. 後で PowerShell コマンドで使用できるように、リソース グループ名の変数を定義します。 次のコマンド テキストを PowerShell にコピーし、[Azure リソース グループ](../../azure-resource-manager/management/overview.md)の名前を二重引用符で囲んで指定し、コマンドを実行します。 (例: `"adfrg"`)。 

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    リソース グループが既に存在する場合、上書きしないようお勧めします。 `$ResourceGroupName` 変数に別の値を割り当てて、コマンドをもう一度実行します。

2. Azure リソース グループを作成するには、次のコマンドを実行します。 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 

    リソース グループが既に存在する場合、上書きしないようお勧めします。 `$ResourceGroupName` 変数に別の値を割り当てて、コマンドをもう一度実行します。

3. データ ファクトリ名の変数を定義します。 

    > [!IMPORTANT]
    >  データ ファクトリ名は、グローバルに一意となるように更新してください。 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. データ ファクトリを作成するには、$ResGrp 変数の Location および ResourceGroupName プロパティを使用して、次の **New-AzDataFactory** コマンドレットを実行します。 
    
    ```powershell
    $df = New-AzDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

以下の点に注意してください。

* Azure Data Factory の名前はグローバルに一意にする必要があります。 次のエラーが発生した場合は、名前を変更してからもう一度実行してください。

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory インスタンスを作成するには、Azure へのログインに使用するユーザー アカウントが、**共同作成者** ロールまたは **所有者** ロールのメンバーであるか、Azure サブスクリプションの **管理者** である必要があります。

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database のリンクされたサービスを作成する
リンクされたサービスを作成し、SSIS カタログをホストしている、Azure SQL Database 内のデータベースをデータ ファクトリにリンクします。 このリンクされたサービスの情報をデータ ファクトリが使用して、SSISDB データベースに接続し、ストアド プロシージャを実行して SSIS パッケージを実行します。 

1. 次の内容を記述した **AzureSqlDatabaseLinkedService.json** という名前の JSON ファイルを **C:\ADF\RunSSISPackage** フォルダーに作成します。 

    > [!IMPORTANT]
    > &lt;servername&gt;、&lt;username&gt;@&lt;servername&gt;、&lt;password&gt; を実際の Azure SQL データベースの値に置き換えてからファイルを保存してください。

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
        }
    ```
2. **Azure PowerShell** で **C:\ADF\RunSSISPackage** フォルダーに切り替えます。
3. **New-AzDataFactoryLinkedService** コマンドレットを実行して、リンクされたサービスを作成します。**AzureSqlDatabaseLinkedService** を作成します。 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>出力データセットを作成する
この出力データセットは、パイプラインのスケジュールを開始するダミーのデータセットです。 頻度は [時間] に設定され、間隔は 1 に設定されています。 したがって、パイプラインは、パイプラインの開始時刻から終了時刻までの間に 1 時間に 1 回実行されます。 

1. 次のような内容の OutputDataset.json ファイルを作成します。 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. **New-AzDataFactoryDataset** コマンドレットを実行して、データセットを作成します。 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>ストアド プロシージャ アクティビティを含むパイプラインを作成する 
この手順では、ストアド プロシージャ アクティビティを含むパイプラインを作成します。 このアクティビティは、SSIS パッケージを実行する sp_executesql ストアド プロシージャを呼び出します。 

1. 次の内容を記述した **MyPipeline.json** という名前の JSON ファイルを **C:\ADF\RunSSISPackage** フォルダーに作成します。

    > [!IMPORTANT]
    > &lt;フォルダー名&gt;、&lt;プロジェクト名&gt;、&lt;パッケージ名&gt; を SSIS カタログのフォルダー、プロジェクト、パッケージの名前で置き換えてから、ファイルを保存してください。

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. パイプライン **RunSSISPackagePipeline** を作成するには、**New-AzDataFactoryPipeline** コマンドレットを実行します。

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>パイプラインの実行を監視します

1. **Get-AzDataFactorySlice** を実行し、output dataset** のすべてのスライスの詳細を表示します。これは、パイプラインの出力データセットです。

    ```powershell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    ここで指定した StartDateTime がパイプライン JSON で指定されている開始時刻と同じであることに注意してください。 
1. **Get-AzDataFactoryRun** を実行して、特定のスライスに関するアクティビティの実行の詳細を取得します。

    ```powershell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    スライスが **準備完了** 状態または **失敗** 状態になるまで、このコマンドレットを実行し続けることができます。 

    次のクエリをサーバーの SSISDB データベースに対して実行することで、パッケージが実行されたことを確認できます。 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>次のステップ
ストアド プロシージャ アクティビティの詳細については、[ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md)に関する記事をご覧ンください。