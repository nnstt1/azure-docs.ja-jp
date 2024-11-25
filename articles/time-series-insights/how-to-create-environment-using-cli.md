---
title: Azure CLI を使用して Azure Time Series Insights Gen2 環境を作成する - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure CLI を使用して Azure Time Series Insights Gen2 で環境を設定する方法について説明します。
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.reviewer: orspodek
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 36a6da683912ac004b948b9a334da1758ab903fd
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "110791880"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-cli"></a>Azure CLI を使用して Azure Time Series Insights Gen2 環境を作成する

このドキュメントでは、新しい Time Series Insights Gen2 環境を作成する手順について説明します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>前提条件

* 自分の環境の[コールド ストア](./concepts-storage.md#cold-store)用 Azure ストレージ アカウントを作成する。 このアカウントは、履歴データの長期保有と分析を目的として設計されています。

> [!NOTE]
> コード内の `mytsicoldstore` をコールド ストレージ アカウントの一意の名前に置き換えます。

まず、ストレージ アカウントを作成します。

```azurecli-interactive
storage=mytsicoldstore
rg=-my-resource-group-name
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv
```

## <a name="creating-the-environment"></a>環境を作成する

ストレージ アカウントが作成され、その名前と管理キーが変数に割り当てられたので、次のコマンドを実行して Azure Time Series Insights 環境を作成します。

> [!NOTE]
> コード内で、次の項目を実際のシナリオの一意の名前に置き換えます。
>
> * `my-tsi-env` を環境名に置き換えます。
> * `my-ts-id-prop` を時系列 ID プロパティの名前に置き換えます。

> [!IMPORTANT]
> 環境内の時系列 ID は、データベースのパーティション キーのようなものです。 時系列 ID は、時系列モデルの主キーとして機能します。
>
> 詳細については、「[タイム シリーズ ID の選択に関するベスト プラクティス](./how-to-select-tsid.md)」を参照してください。

```azurecli-interactive
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=my-ts-id-prop type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

## <a name="remove-an-azure-time-series-insights-environment"></a>Azure Time Series Insights 環境を削除する

Azure CLI を使用すると、Time Series Insights 環境などの個々のリソースを削除したり、リソース グループとそのすべてのリソース (Time Series Insights 環境など) を削除したりできます。

[Time Series Insights 環境を削除](/cli/azure/tsi/environment#az_tsi_environment_delete)するには、次のコマンドを実行します。

```azurecli-interactive
az tsi environment delete --name "my-tsi-env" --resource-group $rg
```

[ストレージ アカウントを削除](/cli/azure/storage/account#az_storage_account_delete)するには、次のコマンドを実行します。

```azurecli-interactive
az storage account delete --name $storage --resource-group $rg
```

[リソース グループとそのすべてのリソースを削除](/cli/azure/group#az_group_delete)するには、次のコマンドを実行します。

```azurecli-interactive
az group delete --name $rg
```

## <a name="next-steps"></a>次のステップ

* Azure Time Series Insights Gen2 環境の[ストリーミング インジェストのイベント ソース](./concepts-streaming-ingestion-event-sources.md)をご覧ください。
* [IoT Hub](./how-to-ingest-data-iot-hub.md) に接続する方法をご覧ください。
