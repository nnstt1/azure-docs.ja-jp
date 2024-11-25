---
title: クイック スタート:テンプレートを使用して共有クエリを作成する
description: このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、OS 別に仮想マシンをカウントする Resource Graph 共有クエリを作成します。
ms.date: 08/17/2021
ms.topic: quickstart
ms.custom: subject-armqs, mode-arm
ms.openlocfilehash: 19c0a4b0177d8c5f8f856ea1eb7640cf93fb68da
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131016152"
---
# <a name="quickstart-create-a-shared-query-by-using-an-arm-template"></a>クイック スタート:ARM テンプレートを使用して共有クエリを作成する

Resource Graph クエリは、"_プライベート クエリ_" または "_共有クエリ_" として保存できます。 プライベート クエリは、個人のポータル プロファイルに保存され、他のユーザーには表示されません。 共有クエリは、アクセス許可とロールベースのアクセス権を使用して他のユーザーと共有できる Resource Manager オブジェクトです。 共有クエリにより、共通かつ一貫したリソース検出の実行が提供されます。 このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して共有クエリを作成します。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure への共有クエリを作成するための ARM テンプレートをデプロイする" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fdemos%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートでは、_Count VMs by OS_ という共有クエリを作成します。 SDK 内または Resource Graph エクスプローラーを使用してポータル内でこのクエリを実行するには、[OS の種類別に仮想マシンをカウントするサンプル](./samples/starter.md#count-os)に関する説明を参照してください。

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/)からのものです。

:::code language="json" source="~/quickstart-templates/demos/resourcegraph-sharedquery-countos/azuredeploy.json":::

テンプレート内に定義されているリソース:

- [Microsoft.ResourceGraph/queries](/azure/templates/microsoft.resourcegraph/queries)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

> [!NOTE]
> Azure Resource Graph サービスは無料です。 詳細については、[Azure Resource Graph の概要](./overview.md)に関するページを参照してください。

1. 次のイメージを選択して、Azure portal にサインインし、テンプレートを開きます。

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure への共有クエリを作成するための ARM テンプレートをデプロイする" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fdemos%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

1. 次の値を選択または入力します。

   | 名前 | [値] |
   |------|-------|
   | サブスクリプション | Azure サブスクリプションを選択します。 |
   | Resource group | **[新規作成]** を選択し、名前を指定して、 **[OK]** を選択します。 |
   | 場所 | リージョンを選択します。 たとえば **[米国中部]** です。 |
   | クエリ名 | 既定値をそのまま使用します: **Count VMs by OS**。 |
   | クエリ コード | 既定値をそのまま使用します: `Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)` |
   | クエリの説明 | 既定値をそのまま使用します: **This shared query counts all virtual machine resources and summarizes by the OS type.** |
   | 上記の使用条件に同意する | (選択) |

1. **[購入]** を選択します。

その他のリソース:

- その他のサンプル テンプレートについては、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)」を参照してください。
- テンプレート リファレンスを確認するには、[Azure テンプレート リファレンス](/azure/templates/microsoft.resourcegraph/allversions)に関するページを参照してください。
- ARM テンプレートを開発する方法については、[Azure Resource Manager のドキュメント](../../azure-resource-manager/management/overview.md)を参照してください。
- サブスクリプション レベルのデプロイについては、「[サブスクリプション レベルでリソース グループとリソースを作成する](../../azure-resource-manager/templates/deploy-to-subscription.md)」を参照してください。

## <a name="validate-the-deployment"></a>デプロイの検証

新しい共有クエリを実行するには、次の手順に従います。

1. ポータルの検索バーから "**Resource Graph のクエリ**" を検索して選択します。

1. **Count VMs by OS** という名前の共有クエリを選択し、 **[概要]** ページの **[結果]** タブを選択します。

共有クエリは、Resource Graph エクスプローラーから開くこともできます。

1. ポータルの検索バーから "**Resource Graph エクスプローラー**" を検索して選択します。

1. **[クエリを開く]** を選択します。

1. **[Type]\(種類\)** を _[共有クエリ]_ に変更します。 **Count VMs by OS** が一覧に表示されない場合は、フィルター ボックスを使用して結果を制限します。 **Count VMs by OS** 共有クエリが表示されたら、その名前を選択します。

1. クエリが読み込まれたら、 **[クエリの実行]** を選択します。 結果は、 **[結果]** タブに表示されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成した共有クエリを削除するには、次の手順に従います。

1. ポータルの検索バーから "**Resource Graph のクエリ**" を検索して選択します。

1. **Count VMs by OS** という名前の共有クエリの横にあるチェック ボックスをオンにします。

1. ページの上部にある **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Resource Graph 共有クエリを作成しました。

共有クエリの詳細については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure portal でのクエリの管理](./tutorials/create-share-query.md)
