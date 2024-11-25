---
title: CLI を使用した Azure Event Grid システム トピックの作成、表示、管理
description: この記事では、Azure CLI を使用して、システムのトピックを作成、表示、削除する方法について説明します。
ms.topic: conceptual
ms.date: 07/22/2021
ms.openlocfilehash: e8ea2e1351b5faf93e58d5a96561555050ce05b6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469419"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>Azure CLI を使用した Event Grid システム トピックの作成、表示、管理
この記事では、Azure CLI を使用してシステム トピックを作成および管理する方法について説明します。 システム トピックの概要については、[システム トピック](system-topics.md)に関するページを参照してください。

## <a name="install-extension-for-azure-cli"></a>Azure CLI 用の拡張機能のインストール
Azure CLI の場合は、[Event Grid 拡張機能](/cli/azure/azure-cli-extensions-list)が必要です。

Cloud Shell で:

- 拡張機能を以前にインストールしている場合は、それを更新します。`az extension update -n eventgrid`
- 拡張機能を以前にインストールしていない場合は、それをインストールします。`az extension add -n eventgrid`

ローカル インストールの場合:

1. [Azure CLI のインストール](/cli/azure/install-azure-cli)を実行します。 `az --version` を使って確認し、最新バージョンがあることを確認します。
2. 以前のバージョンの拡張機能をアンインストールします。`az extension remove -n eventgrid`
3. `az extension add -n eventgrid` によって、eventgrid 拡張機能をインストールします

## <a name="create-a-system-topic"></a>システム トピックを作成する

- 最初に Azure ソースにシステム トピックを作成してから、そのトピックのイベント サブスクリプションを作成するには、次の参照トピックを参照してください。
    - [az eventgrid system-topic create](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_create)

        ```azurecli-interactive
        # Get the ID of the Azure source (for example: Azure Storage account)
        storageid=$(az storage account show \
                --name <AZURE STORAGE ACCOUNT NAME> \
                --resource-group <AZURE RESOURCE GROUP NAME> \
                    --query id --output tsv)
    
        # Create the system topic on the Azure source (example: Azure Storage account)
        az eventgrid system-topic create \
            -g <AZURE RESOURCE GROUP NAME> \
            --name <SPECIFY SYSTEM TOPIC NAME> \
            --location <LOCATION> \
            --topic-type microsoft.storage.storageaccounts \
            --source $storageid
        ```           

        システム トピックの作成に使用できる `topic-type` 値の一覧については、次のコマンドを実行してください。 これらのトピックの種類の値は、システム トピックの作成をサポートするイベント ソースを表します。 一覧から `Microsoft.EventGrid.Topics` と `Microsoft.EventGrid.Domains` を無視してください。 

        ```azurecli-interactive
        az eventgrid topic-type  list --output json | grep -w id
        ```
    - [az eventgrid system-topic event-subscription create](/cli/azure/eventgrid/system-topic/event-subscription#az_eventgrid_system_topic_event-subscription-create)

        ```azurecli-interactive
        az eventgrid system-topic event-subscription create --name <SPECIFY EVENT SUBSCRIPTION NAME> \
            -g rg1 --system-topic-name <SYSTEM TOPIC NAME> \
            --endpoint <ENDPOINT URL>         
        ```
- Azure ソースのイベント サブスクリプションを作成するときに、(暗黙的に) システム トピックを作成するには、[az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) メソッドを使用します。 次に例を示します。
    
    ```azurecli-interactive
    storageid=$(az storage account show --name <AZURE STORAGE ACCOUNT NAME> --resource-group <AZURE RESOURCE GROUP NAME> --query id --output tsv)
    endpoint=<ENDPOINT URL>

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <EVENT SUBSCRIPTION NAME> \
      --endpoint $endpoint
    ```
    詳細な手順のチュートリアルについては、「[ストレージ アカウントをサブスクライブする](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account)」を参照してください。

## <a name="view-all-system-topics"></a>すべてのシステム トピックを表示する
すべてのシステム トピックと選択したシステム トピックの詳細を表示するには、次のコマンドを使用します。

- [az eventgrid system-topic list](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_list)

    ```azurecli-interactive
    az eventgrid system-topic list   
     ```
- [az eventgrid system-topic show](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_show)

    ```azurecli-interactive
    az eventgrid system-topic show -g <AZURE RESOURCE GROUP NAME> -n <SYSTEM TOPIC NAME>     
     ```

## <a name="delete-a-system-topic"></a>システム トピックを削除します
システム トピックを削除するには、次のコマンドを使用します。 

- [az eventgrid system-topic delete](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_delete)

    ```azurecli-interactive
    az eventgrid system-topic delete -g <AZURE RESOURCE GROUP NAME> --name <SYSTEM TOPIC NAME>   
     ```

## <a name="next-steps"></a>次のステップ
Azure Event Grid でサポートされるシステム トピックとトピックの種類の詳細については、「[Azure Event Grid でのシステム トピック](system-topics.md)」セクションを参照してください。 