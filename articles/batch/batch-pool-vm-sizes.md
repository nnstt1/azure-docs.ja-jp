---
title: プールの VM サイズとイメージを選択する
description: Azure Batch プールのコンピューティング ノード用に使用可能な VM サイズと OS バージョンを選択する方法
ms.topic: conceptual
ms.date: 09/02/2021
ms.custom: seodec18
ms.openlocfilehash: 64dc4f11d5b80f0b493ca393f9a04521090c02cb
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437060"
---
# <a name="choose-a-vm-size-and-image-for-compute-nodes-in-an-azure-batch-pool"></a>Azure Batch プールのコンピューティング ノード用の VM サイズとイメージを選択する

Azure Batch プールのノード サイズを選択するときは、Azure で使用可能なほぼすべての VM サイズを選択することができます。 Azure では、さまざまなワークロードに対応する Linux および Windows VM の幅広いサイズが提供されています。

## <a name="supported-vm-series-and-sizes"></a>サポートされている VM シリーズおよびサイズ

### <a name="pools-in-virtual-machine-configuration"></a>仮想マシン構成のプール

仮想マシン構成の Batch プールでは、ほぼすべての [VM サイズ](../virtual-machines/sizes.md)がサポートされています。 リージョンでサポートされる VM サイズは、[Batch Management API](batch-apis-tools.md#batch-management-apis) や[コマンド ライン ツール](batch-apis-tools.md#batch-command-line-tools) (PowerShell コマンドレットおよび Azure CLI) を使用して取得できます。  たとえば、リージョンでサポートされる VM サイズを一覧表示する [Azure Batch CLI コマンド](/cli/azure/batch/location#az_batch_location_list_skus)は、次のようになります。

```azurecli-interactive
az batch location list-skus --location
                            [--filter]
                            [--maxresults]
                            [--subscription] 
```

次の表では、それぞれの VM シリーズについて、VM シリーズと VM サイズが Batch でサポートされているかどうかを示します。

| VM シリーズ  | サポートされるサイズ |
|------------|---------|
| Basic A | Basic_A0 (A0) を *除く* すべてのサイズ |
| A | Standard_A0、Standard_A8、Standard_A9、Standard_A10、Standard_A11 を *除く* すべてのサイズ |
| Av2 | すべてのサイズ |
| B | サポートされていません |
| DCsv2 | すべてのサイズ |
| Dv2、DSv2 | すべてのサイズ |
| Dv3、Dsv3 | すべてのサイズ |
| Dav4、Dasv4 | すべてのサイズ |
| Ddv4、Ddsv4 |  すべてのサイズ |
| Dv4、Dsv4 | サポートされていません |
| Ev3、Esv3 | E64is_v3 を除くすべてのサイズ |
| Eav4、Easv4 | すべてのサイズ |
| Edv4、Edsv4 | すべてのサイズ |
| Ev4、Esv4 | サポートされていません |
| F、Fs | すべてのサイズ |
| Fsv2 | すべてのサイズ |
| FX<sup>1</sup> | すべてのサイズ |
| G、Gs | すべてのサイズ |
| H | すべてのサイズ |
| HB | すべてのサイズ |
| HBv2 | すべてのサイズ |
| HBv3 | すべてのサイズ |
| HC | すべてのサイズ |
| Ls | すべてのサイズ |
| Lsv2 | すべてのサイズ |
| M | すべてのサイズ |
| Mv2<sup>1</sup> | すべてのサイズ |
| NC | すべてのサイズ |
| NCv2 | すべてのサイズ |
| NCv3 | すべてのサイズ |
| NCasT4_v3 | すべてのサイズ |
| ND | すべてのサイズ |
| NDv4 | すべてのサイズ |
| NDv2 | なし - まだ使用できません |
| NP | すべてのサイズ |
| NV | すべてのサイズ |
| NVv3 | すべてのサイズ |
| NVv4 | すべてのサイズ |
| SAP HANA | サポートされていません |

<sup>1</sup> これらの VM シリーズは第 2 世代の VM イメージでのみ使用できます。

### <a name="using-generation-2-vm-images"></a>第 2 世代 VM イメージの使用

[Mv2](../virtual-machines/mv2-series.md) など、一部の VM シリーズは[第 2 世代の VM イメージ](../virtual-machines/generation-2.md)でのみ使用できます。 第 2 世代 VM イメージは、あらゆる VM イメージと同様に、["imageReference"](/rest/api/batchservice/pool/add#imagereference) 構成の "sku" プロパティを利用して指定されます。"sku" 文字列には、"-g2" や "-gen2" のようなサフィックスが与えられます。 第 2 世代 VM イメージを含め、Batch でサポートされている VM イメージの一覧を取得するには、["list supported images"](/rest/api/batchservice/account/listsupportedimages) API、[PowerShell](/powershell/module/az.batch/get-azbatchsupportedimage)、[Azure CLI](/cli/azure/batch/pool/supported-images) を使用します。

### <a name="pools-in-cloud-services-configuration"></a>クラウド サービスの構成におけるプール

> [!WARNING]
> Cloud Services 構成プールは[非推奨](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/)とされます。 代わりに、仮想マシン構成プールを使用してください。

クラウド サービス構成のバッチ プールでは、次を **除く** すべての [Cloud Services 向け VM サイズ](../cloud-services/cloud-services-sizes-specs.md)がサポートされます。

| VM シリーズ  | サポートされないサイズ |
|------------|-------------------|
| A シリーズ   | XS       |
| Av2 シリーズ | Standard_A1_v2、Standard_A2_v2、Standard_A2m_v2 |

## <a name="size-considerations"></a>サイズの考慮事項

- **アプリケーションの要件** - ノード上で実行するアプリケーションの特性と要件を考慮してください。 アプリケーションがマルチスレッドであるかどうかや、どのくらいのメモリが消費されるかという点が、最も適切でコスト効率の高いノード サイズを選ぶうえでのヒントになります。 マルチインスタンスの [MPI ワークロード](batch-mpi.md)または CUDA アプリケーションについては、それぞれ特別な [HPC](../virtual-machines/sizes-hpc.md) または [GPU 対応](../virtual-machines/sizes-gpu.md)の VM サイズを検討してください。 詳細については、「[Batch プールでの RDMA 対応または GPU 対応インスタンスの使用](batch-pool-compute-intensive-sizes.md)」を参照してください。

- **ノードあたりのタスク数** - ノードのサイズは、そこで一度に実行されるタスクが 1 つであるという想定で選択するのが一般的です。 しかしジョブの実行中に、コンピューティング ノードで複数のタスク (ひいては複数のアプリケーション インスタンス) が[並列実行](batch-parallel-node-tasks.md)できるとメリットがあります。 そのような場合は、タスクを並列に実行するという需要の増大に対応するために、マルチコア ノード サイズを選ぶことが基本です。

- **さまざまなタスクの負荷水準** - プール内のすべてのノードは同じサイズです。 システム要件や負荷水準の異なる複数のアプリケーションを実行する場合は、プールを分けることをお勧めします。

- **利用可能なリージョン** - Batch アカウントを作成したリージョンで、VM シリーズまたはサイズが利用できない場合があります。 特定のサイズが使用可能かどうかを確認するには、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」をご覧ください。

- **クォータ** - Batch アカウントの [コア クォータ](batch-quota-limit.md#resource-quotas)により、Batch プールに追加できる指定されたサイズのノードの数を制限できます。 必要に応じて、[クォータの増量を要求](batch-quota-limit.md#increase-a-quota)できます。

- **プールの構成** - 一般的に、仮想マシン構成のプールを作成する際は、クラウド サービス構成と比較して VM サイズ オプションが増えます。

## <a name="supported-vm-images"></a>サポートされている VM イメージ

次のいずれかの API を使用すると、Batch で現在サポートされている Windows および Linux の VM イメージの一覧 (各イメージのノード エージェント SKU ID を含む) が返されます。

- Batch Service REST API:[サポートされているイメージの一覧表示](/rest/api/batchservice/account/listsupportedimages)
- PowerShell:[Get-AzBatchSupportedImage](/powershell/module/az.batch/get-azbatchsupportedimage)
- Azure CLI: [az batch pool supported-images](/cli/azure/batch/pool/supported-images)

Batch のサポート終了日 (EOL) が近いイメージは、使用しないようにすることを強くお勧めします。 このような日付は、[ `ListSupportedImages` API](/rest/api/batchservice/account/listsupportedimages)、[PowerShell](/powershell/module/az.batch/get-azbatchsupportedimage)、または [Azure CLI](/cli/azure/batch/pool/supported-images) で確認することができます。 Batch プールの VM イメージの選択に関する詳細については、[Batch のベストプラクティス ガイド](best-practices.md)を参照してください。

## <a name="next-steps"></a>次のステップ

- [Batch サービスのワークフローと主要なリソース](batch-service-workflow-features.md) (プール、ノード、ジョブ、タスクなど) について学習します。
- コンピューティング集中型 VM のサイズを使用する方法について詳しくは、「[Batch プールでの RDMA 対応または GPU 対応インスタンスの使用](batch-pool-compute-intensive-sizes.md)」をご覧ください。
