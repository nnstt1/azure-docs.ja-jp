---
title: グローバル パラメーター
description: Azure Data Factory 環境ごとにグローバル パラメーターを設定する
ms.service: data-factory
ms.subservice: authoring
ms.topic: conceptual
author: joshuha-msft
ms.author: joowen
ms.date: 05/12/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3205d5c3be7fe13cbaa4b881a3de6bffc15e9fd8
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550914"
---
# <a name="global-parameters-in-azure-data-factory"></a>Data Factory のグローバル パラメーター

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

グローバル パラメーターは、データ ファクトリ全体での定数で、任意の式のパイプラインで使用できます。 これらは、パラメーター名と値が同一のパイプラインが複数ある場合に便利です。 継続的インテグレーションとデプロイ プロセス (CI/CD) を使用してデータ ファクトリを昇格させる場合は、各環境のこれらのパラメーターを上書きできます。 

## <a name="creating-global-parameters"></a>グローバル パラメーターの作成

グローバル パラメーターを作成するには、 **[管理]** セクションの *[グローバル パラメーター]* タブに移動します。 **[新規]** を選択して作成サイド ナビゲーションを開きます。

:::image type="content" source="media/author-global-parameters/create-global-parameter-1.png" alt-text="グローバルパラメーターを作成するために選択した [新規] ボタンを強調表示しているスクリーンショット。":::

サイド ナビゲーションで、名前を入力し、データ型を選択して、パラメーターの値を指定します。

:::image type="content" source="media/author-global-parameters/create-global-parameter-2.png" alt-text="新しいグローバル パラメーターの名前、データ型、値を追加する場所を示すスクリーンショット。":::

グローバル パラメーターが作成されたら、パラメーターの名前をクリックすると編集できます。 複数のパラメーターを一度に変更するには、 **[すべて編集]** を選択します。

:::image type="content" source="media/author-global-parameters/create-global-parameter-3.png" alt-text="グローバル パラメーターを作成する":::

## <a name="using-global-parameters-in-a-pipeline"></a>パイプラインでのグローバル パラメーターの使用

グローバル パラメーターは、任意の[パイプライン式](control-flow-expression-language-functions.md)で使用できます。 パイプラインが、データセットやデータ フローなどの別のリソースを参照している場合は、そのリソースのパラメーターを介してグローバル パラメーター値を渡すことができます。 グローバル パラメーターは `pipeline().globalParameters.<parameterName>` として参照されます。

:::image type="content" source="media/author-global-parameters/expression-global-parameters.png" alt-text="グローバル パラメーターの使用":::

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a> CI/CD でのグローバル パラメーター

継続的インテグレーションとデプロイ ソリューションにグローバル パラメーターを統合するには、次の 2 つの方法があります。

* ARM テンプレートにグローバル パラメーターを含める
* PowerShell スクリプトを使用してグローバル パラメーターをデプロイする

一般的なユース ケースでは、ARM テンプレートにグローバル パラメーターを含めることをお勧めします。 これは、[CI/CD のドキュメント](continuous-integration-delivery.md)で説明されているソリューションとネイティブに統合されます。自動発行および Purview 接続の場合は、**PowerShell スクリプト** による方法を使用する必要があります。 PowerShell スクリプトによる方法の詳細については、後で説明します。 グローバル パラメーターは、環境によって変わることが多いため、既定で ARM テンプレート パラメーターとして追加されます。 **管理** ハブから有効にして、ARM テンプレートにグローバル パラメーターを含めることができます。

:::image type="content" source="media/author-global-parameters/include-arm-template.png" alt-text="ARM テンプレートに含める":::

> [!NOTE]
> **ARM テンプレートに含める** 構成は、"Git モード" でのみ使用できます。 現在は "ライブ モード" または "Data Factory" モードのため、これは無効です。 自動発行または Purview 接続の場合は、グローバル パラメーターを含める方法は使用せず、PowerShell スクリプトによる方法を使用してください。 

> [!WARNING]
>パラメーター名に '-' を使用することはできません。 エラーコード "{"code":"BadRequest","message":"ErrorCode=InvalidTemplate,ErrorMessage=The expression >'pipeline().globalParameters.myparam-dbtest-url' is not valid: .....}" が表示されます。 ただし、パラメーター名に ‘_’ を使用することはできます。 

ARM テンプレートにグローバル パラメーターを追加すると、他の環境でのカスタマー マネージド キーや git 構成など、その他のファクトリレベル設定をオーバーライドできるファクトリレベル設定が追加されます。 UAT や PROD などの管理者特権環境でこれらの設定を有効にしている場合は、以下で説明する手順で、PowerShell スクリプトを使用してグローバル パラメーターをデプロイすることをお勧めします。 


### <a name="deploying-using-powershell"></a>PowerShell を使用したデプロイ

以下の手順では、PowerShell を使用してグローバル パラメーターをデプロイする方法を説明します。 これは、ターゲット ファクトリに、カスタマー マネージド キーなどのファクトリレベル設定がある場合に便利です。

グローバル パラメーターを使用してファクトリを発行したり、Resource Manager テンプレートをエクスポートしたりすると、*your-factory-name_GlobalParameters.json* という名前のファイルが含まれる *globalParameters* という名前のフォルダーが作成されます。 このファイルは、発行されたファクトリ内の各グローバル パラメーターの型と値を含む JSON オブジェクトです。

:::image type="content" source="media/author-global-parameters/global-parameters-adf-publish.png" alt-text="グローバル パラメーターの発行":::

TEST や PROD などの新しい環境にデプロイする場合は、このグローバル パラメーター ファイルのコピーを作成し、適切な環境固有の値を上書きすることをお勧めします。 再発行時に元のグローバル パラメーター ファイルは上書きされますが、他の環境用のコピーはそのままになります。

たとえば、"ADF-DEV" という名前のファクトリと、値 "DEV" を持つ "environment" という名前の文字列型のグローバル パラメーターがある場合、発行時には *ADF-DEV_GlobalParameters.json* という名前のファイルが生成されます。 "ADF_TEST" という名前のテスト ファクトリにデプロイする場合は、JSON ファイルのコピーを (ADF-TEST_GlobalParameters.json などの名前で) 作成し、パラメーター値を環境固有の値に置き換えます。 これでパラメーター "environment" の値は、"test" になる可能性があります。 

:::image type="content" source="media/author-global-parameters/powershell-task.png" alt-text="グローバル パラメーターのデプロイ":::

次の PowerShell スクリプトを使用して、グローバル パラメーターを他の環境に昇格させます。 Resource Manager テンプレートのデプロイ前に、Azure PowerShell DevOps タスクを追加します。 DevOps タスクでは、新しいパラメーター ファイルの場所、ターゲット リソース グループ、ターゲット データ ファクトリを指定する必要があります。

> [!NOTE]
> PowerShell を使用してグローバル パラメーターをデプロイするには、バージョン 4.4.0 以降の Az モジュールを使用する必要があります。

```powershell
param
(
    [parameter(Mandatory = $true)] [String] $globalParametersFilePath,
    [parameter(Mandatory = $true)] [String] $resourceGroupName,
    [parameter(Mandatory = $true)] [String] $dataFactoryName
)

Import-Module Az.DataFactory

$newGlobalParameters = New-Object 'system.collections.generic.dictionary[string,Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification]'

Write-Host "Getting global parameters JSON from: " $globalParametersFilePath
$globalParametersJson = Get-Content $globalParametersFilePath

Write-Host "Parsing JSON..."
$globalParametersObject = [Newtonsoft.Json.Linq.JObject]::Parse($globalParametersJson)

foreach ($gp in $globalParametersObject.GetEnumerator()) {
    Write-Host "Adding global parameter:" $gp.Key
    $globalParameterValue = $gp.Value.ToObject([Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification])
    $newGlobalParameters.Add($gp.Key, $globalParameterValue)
}

$dataFactory = Get-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Name $dataFactoryName
$dataFactory.GlobalParameters = $newGlobalParameters

Write-Host "Updating" $newGlobalParameters.Count "global parameters."

Set-AzDataFactoryV2 -InputObject $dataFactory -Force
```

## <a name="next-steps"></a>次の手順

* Azure Data Factory における[継続的インテグレーションとデプロイ プロセス](continuous-integration-delivery-improvements.md)について確認する
* [制御フローの式言語](control-flow-expression-language-functions.md)の使用方法を確認します
