---
title: Azure HPC Cache にストレージを追加する
description: Azure HPC Cache で長期的なファイルの保管にオンプレミス NFS システムまたは Azure BLOB コンテナーを使用できるようにストレージ ターゲットを定義する方法
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/22/2021
ms.custom: subject-rbac-steps
ms.author: femila
ms.openlocfilehash: 29ecaecc579d1947d3e86ef6d9e080030a419331
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070605"
---
# <a name="add-storage-targets"></a>ストレージ ターゲットを追加する

"*ストレージ ターゲット*" は、Azure HPC Cache を通じてアクセスされるファイルのバックエンド ストレージです。 NFS ストレージ (オンプレミスのハードウェア システムなど) を追加できるほか、Azure BLOB にデータを格納することができます。

キャッシュに対して 10 個の異なるストレージ ターゲットを定義できます。より大きなキャッシュでは、[最大で 20 個のストレージ ターゲットをサポート](#size-your-cache-correctly-to-support-your-storage-targets)できます。

このキャッシュでは、すべてのストレージ ターゲットが 1 つの[集約された名前空間](hpc-cache-namespace.md)で提供されます。 名前空間パスは、ストレージ ターゲットを追加した後に個別に構成されます。

キャッシュの仮想ネットワークからストレージのエクスポートにアクセスできなければならないことに注意してください。 オンプレミスのハードウェア ストレージの場合、NFS ストレージへのアクセスに使用されるホスト名を解決できる DNS サーバーの設定が必要になることがあります。 詳細については、「[DNS アクセス](hpc-cache-prerequisites.md#dns-access)」を参照してください。

キャッシュを作成した後で、ストレージ ターゲットを追加します。 次のプロセスに従います。

1. [キャッシュを作成する](hpc-cache-create.md)
1. ストレージ ターゲットを定義する (この記事の情報)
1. [ クライアント側のパスを作成する](add-namespace-paths.md) ([集約された名前空間](hpc-cache-namespace.md)用)

ストレージ ターゲットを追加する手順は、使用するストレージの種類によって若干異なります。 それぞれの詳細を以下に示します。

Azure portal からのキャッシュの作成とストレージ ターゲットの追加に関する[ビデオ デモ](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)を視聴するには、次の画像をクリックしてください。

[![ビデオのサムネイル:Azure HPC Cache: セットアップ (クリックしてビデオ ページにアクセス)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="size-your-cache-correctly-to-support-your-storage-targets"></a>ストレージ ターゲットをサポートするようキャッシュを正しいサイズに設定する

サポートされるストレージ ターゲットの数はキャッシュ サイズによって異なり、キャッシュの作成時に設定されます。 キャッシュ容量は、スループット容量 (GB/秒) とストレージ容量 (TB) の組み合わせです。

* 最大 10 個のストレージ ターゲット - 選択したスループットに対して最小または中程度のキャッシュ ストレージ値を持つ標準的なキャッシュには、最大 10 個のストレージ ターゲットを指定できます。

  たとえば、2 GB/秒のスループットを選択し、最大のキャッシュ ストレージ サイズを選択しない場合、そのキャッシュでは最大 10 個のストレージ ターゲットがサポートされます。

* 最大 20 個のストレージ ターゲット -

  * (キャッシュ ストレージ サイズが事前に構成されている) すべての高スループット キャッシュでは、最大 20 個のストレージ ターゲットをサポートできます。
  * 選択したスループット値に対して、使用可能な最大のキャッシュ サイズを選択した場合、標準的なキャッシュでは最大 20 個のストレージ ターゲットをサポートできます。 (Azure CLI を使用する場合は、お使いのキャッシュ SKU で有効な最大キャッシュ サイズを選択してください。)

スループットとキャッシュ サイズの設定について詳しくは、「[キャッシュ容量を設定する](hpc-cache-create.md#set-cache-capacity)」をご覧ください。

## <a name="choose-the-correct-storage-target-type"></a>適切なストレージ ターゲットの種類を選択する

**NFS**、**BLOB**、および **ADLS-NFS** の 3 種類のストレージ ターゲットから選択できます。 この HPC Cache プロジェクト中にファイルを格納するために使用するストレージ システムの種類に一致する種類を選択してください。

* **NFS** - ネットワーク接続ストレージ (NAS) システム上のデータにアクセスする NFS ストレージ ターゲットを作成します。 これは、オンプレミスのストレージ システムか、NFS を使用してアクセスできる別のストレージの種類になります。

  * 要件: [NFS ストレージの要件](hpc-cache-prerequisites.md#nfs-storage-requirements)
  * 指示: [新しい NFS ストレージ ターゲットを追加する](#add-a-new-nfs-storage-target)

* **BLOB** - BLOB ストレージ ターゲットを使用して、作業ファイルを新しい Azure BLOB コンテナーに格納します。 このコンテナーの読み取りまたは書き込みは、Azure HPC Cache からのみ行われます。

  * 前提条件: [Blob Storage の要件](hpc-cache-prerequisites.md#blob-storage-requirements)
  * 指示: [新しい Azure Blob Storage ターゲットを追加する](#add-a-new-azure-blob-storage-target)

* **ADLS-NFS** - ADLS-NFS ストレージ ターゲットは、[NFS 対応 BLOB](../storage/blobs/network-file-system-protocol-support.md) コンテナーからデータにアクセスします。 標準の NFS コマンドを使用してコンテナーを事前に読み込み、後で NFS を使用してファイルを読み取ることができます。

  * 前提条件: [ADLS-NFS ストレージの要件](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements)
  * 指示: [新しい ADLS-NFS ストレージ ターゲットを追加する](#add-a-new-adls-nfs-storage-target)

## <a name="add-a-new-azure-blob-storage-target"></a>新しい Azure Blob Storage ターゲットを追加する

新しい Blob Storage ターゲットには、空の BLOB コンテナーか、Azure HPC Cache クラウド ファイル システム フォーマットでデータが事前設定されているコンテナーが必要です。 BLOB コンテナーの事前読み込みの詳細については、[Azure Blob Storage へのデータの移動](hpc-cache-ingest.md)に関するページを参照してください。

Azure portal の **[ストレージ ターゲットの追加]** ページには、追加する直前に新しい BLOB コンテナーを作成するオプションが含まれています。

> [!NOTE]
>
> * NFS でマウントされた BLOB ストレージの場合は、[ADLS-NFS ストレージ ターゲット](#add-a-new-adls-nfs-storage-target) タイプを使用します。
> * [高スループットのキャッシュ構成](hpc-cache-create.md#choose-the-cache-type-for-your-needs)では、標準の Azure Blob Storage ターゲットはサポートされていません。 代わりに、NFS 対応 BLOB ストレージ (ADLS-NFS) を使用してください。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal からキャッシュ インスタンスを開き、左側のサイド バーにある **[ストレージ ターゲット]** をクリックします。

![テーブルに 2 つの既存のストレージ ターゲットがあり、テーブルの上の [+ ストレージターゲットの追加] ボタンの周囲が強調表示された [設定] > [ストレージ ターゲット] ページのスクリーンショット](media/add-storage-target-button.png)

**[ストレージ ターゲット]** ページには、既存のターゲットがすべて表示されるほか、新たに追加するためのリンクが表示されます。

**[ストレージ ターゲットの追加]** ボタンをクリックします。

![新しい Azure Blob Storage ターゲットの情報が事前設定されている [ストレージ ターゲットの追加] ページのスクリーンショット](media/hpc-cache-add-blob.png)

Azure BLOB コンテナーを定義するには、次の情報を入力します。

* **[ストレージ ターゲット名]** - Azure HPC Cache 内でこのストレージ ターゲットを識別する名前を設定します。
* **[ターゲットの種類]** - **[BLOB]** を選択します。
* **[ストレージ アカウント]** - 使用するアカウントを選択します。

  [アクセス ロールの追加](#add-the-access-control-roles-to-your-account)に関するセクションの説明に従って、ストレージ アカウントへのアクセスをキャッシュ インスタンスに承認する必要があります。

  使用できるストレージ アカウントの種類の詳細については、「[Blob Storage の要件](hpc-cache-prerequisites.md#blob-storage-requirements)」を参照してください。

* **[ストレージ コンテナー]** - このターゲットの BLOB コンテナーを選択するか、 **[新規作成]** をクリックします。

  ![新しいコンテナーの名前とアクセス レベル (プライベート) を指定するダイアログのスクリーンショット](media/add-blob-new-container.png)

完了したら、 **[OK]** をクリックしてストレージ ターゲットを追加します。

> [!NOTE]
> お使いのストレージ アカウント ファイアウォールが "選択されているネットワーク" のみにアクセスできるように制限されている場合、「[BLOB ストレージ アカウントのファイアウォール設定を回避する](hpc-cache-blob-firewall-fix.md)」に記されている一時的な回避策をご利用ください。

### <a name="add-the-access-control-roles-to-your-account"></a>アクセス制御ロールをアカウントに追加する

Azure HPC Cache では、Azure Blob Storage ターゲットのストレージ アカウントにキャッシュ サービスがアクセスすることを承認する場合、[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/index.yml) が使用されます。

ストレージ アカウント所有者は、"HPC Cache リソース プロバイダー" ユーザーの[ストレージ アカウント共同作成者](../role-based-access-control/built-in-roles.md#storage-account-contributor)ロールと[ストレージ BLOB データ共同作成者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)ロールを明示的に追加する必要があります。

この作業は、事前に、または Blob Storage ターゲットを追加する portal ページ上のリンクをクリックして実行できます。 ロールの設定を Azure 環境経由で反映させるとき、最大 5 分かかる場合があることにご留意ください。ロールの追加後、少し待ってからストレージ ターゲットを作成してください。

1. ストレージ アカウントの **[アクセス制御 (IAM)]** を開きます。

1. **[追加]**  >  **[ロールの割り当ての追加]** を選択して、[ロールの割り当ての追加] ページを開きます。

1. 一度に 1 つずつ、次のロールを割り当てます。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」を参照してください。
    
    | 設定 | 値 |
    | --- | --- |
    | ロール | [Storage Account Contributor](../role-based-access-control/built-in-roles.md#storage-account-contributor) <br/>  [ストレージ BLOB データ共同作成者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) |
    | アクセスの割り当て先 | HPC Cache リソース プロバイダー |

    ![[ロールの割り当ての追加] ページ](../../includes/role-based-access-control/media/add-role-assignment-page.png)

   > [!NOTE]
   > HPC Cache リソース プロバイダーが見つからない場合は、代わりに文字列 "storagecache" を検索してみてください。 これは、サービス プリンシパルの GA 前の名前でした。

<!-- 
Steps to add the Azure roles:

1. Open the **Access control (IAM)** page for the storage account. (The link in the **Add storage target** page automatically opens this page for the selected account.)

1. Click the **+** at the top of the page and choose **Add a role assignment**.

1. Select the role "Storage Account Contributor" from the list.

1. In the **Assign access to** field, leave the default value selected ("Azure AD user, group, or service principal").  

1. In the **Select** field, search for "hpc".  This string should match one service principal, named "HPC Cache Resource Provider". Click that principal to select it.

   > [!NOTE]
   > If a search for "hpc" doesn't work, try using the string "storagecache" instead. Users who participated in previews (before GA) might need to use the older name for the service principal.

1. Click the **Save** button at the bottom.

1. Repeat this process to assign the role "Storage Blob Data Contributor".  

![screenshot of add role assignment GUI](media/hpc-cache-add-role.png) -->

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisite-storage-account-access"></a>前提条件:ストレージ アカウント アクセス

[Azure HPC Cache 向けに Azure CLI を設定します](./az-cli-prerequisites.md)。

BLOB ストレージ ターゲットを追加する前に、ストレージ アカウントにアクセスするための適切なロールをキャッシュが備えていること、ファイアウォール設定によってストレージターゲットの作成が許可されることを確認してください。

Azure HPC Cache では、Azure Blob Storage ターゲットのストレージ アカウントにキャッシュ サービスがアクセスすることを承認する場合、[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/index.yml) が使用されます。

ストレージ アカウント所有者は、"HPC Cache リソース プロバイダー" ユーザーの[ストレージ アカウント共同作成者](../role-based-access-control/built-in-roles.md#storage-account-contributor)ロールと[ストレージ BLOB データ共同作成者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)ロールを明示的に追加する必要があります。

キャッシュにこれらのロールがない場合、ストレージ ターゲットの作成は失敗します。

ロールの設定を Azure 環境経由で反映させるとき、最大 5 分かかる場合があります。そのため、ロールの追加後、少し待ってからストレージ ターゲットを作成してください。

詳細な手順については、[Azure CLI を使用して Azure でのロールの割り当てを追加または削除する](../role-based-access-control/role-assignments-cli.md)方法に関するページを参照してください。

また、ストレージ アカウントのファイアウォール設定も確認してください。 アクセスを "選択されたネットワーク" のみに制限するようにファイアウォールが設定されている場合、ストレージ ターゲットの作成は失敗する可能性があります。 「[BLOB ストレージ アカウントのファイアウォール設定を回避する](hpc-cache-blob-firewall-fix.md)」に記載されている回避策を使用してください。

### <a name="add-a-blob-storage-target-with-azure-cli"></a>Azure CLI を使用して BLOB ストレージ ターゲットを追加する

Azure Blob ストレージ ターゲットを定義するには、[az hpc-cache blob-storage-target add](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_add) インターフェイスを使用します。

> [!NOTE]
> 現在、Azure CLI コマンドでは、ストレージ ターゲットを追加するときに、名前空間パスを作成する必要があります。 これは、Azure portal インターフェイスで使用されるプロセスとは異なります。

標準のリソース グループおよびキャッシュ名のパラメーターに加えて、ストレージ ターゲットに対して次のオプションを指定する必要があります。

* ``--name`` - Azure HPC Cache 内でこのストレージ ターゲットを識別する名前を設定します。

* ``--storage-account`` - アカウント識別子。次の形式になります: /subscriptions/ *<subscription_id>* /resourceGroups/ *<storage_resource_group>* /providers/Microsoft.Storage/storageAccounts/ *<account_name>*

  使用できるストレージ アカウントの種類の詳細については、「[Blob Storage の要件](hpc-cache-prerequisites.md#blob-storage-requirements)」を参照してください。

* ``--container-name`` - このストレージ ターゲットに使用するコンテナーの名前を指定します。

* ``--virtual-namespace-path`` - このストレージ ターゲットに使用するクライアント側のファイル パスを設定します。 パスを引用符で囲みます。 仮想名前空間の機能の詳細については、「[集約された名前空間を計画する](hpc-cache-namespace.md)」を参照してください。

コマンドの例:

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>新しい NFS ストレージ ターゲットを追加する

NFS ストレージ ターゲットには、このストレージ システムからデータを格納する方法をキャッシュに指示する使用モデルの設定など、BLOB ストレージ ターゲットとは異なる設定があります。

![NFS ターゲットが定義されている [ストレージ ターゲットの追加] ページのスクリーンショット](media/add-nfs-target.png)

> [!NOTE]
> NFS ストレージ ターゲットを作成する前に、ストレージ システムが Azure HPC Cache からアクセス可能であり、アクセス許可要件を満たしていることを確認します。 キャッシュからストレージ システムにアクセスできない場合、ストレージ ターゲットの作成は失敗します。 詳細については、「[NFS ストレージの要件](hpc-cache-prerequisites.md#nfs-storage-requirements)」と「[NAS 構成および NFS ストレージ ターゲットに関する問題のトラブルシューティング](troubleshoot-nas.md)」を参照してください。

### <a name="choose-a-usage-model"></a>使用モデルを選択する

NFS を使用してストレージ システムにアクセスするストレージ ターゲットを作成するときは、そのターゲットの使用モデルを選択する必要があります。 どのようにデータがキャッシュされるかは、このモデルによって決まります。

これらすべての設定の詳細については、[使用モデル](cache-usage-models.md)に関するページを参照してください。

HPC Cache の組み込み使用モデルでは、迅速な応答と、古いデータを取得するリスクとのバランスを取る方法を選択できます。 ファイルの読み取り速度を最適化する場合、キャッシュ内のファイルがバックエンド ファイルと照合されるかどうかは重要でないかもしれません。 一方、リモート ストレージでファイルが常に最新の状態になっていることを確認する場合は、頻繁にチェックを行うモデルを選択します。

> [!NOTE]
> [高スループット スタイルのキャッシュ](hpc-cache-create.md#choose-the-cache-type-for-your-needs)では、読み取りキャッシュのみがサポートされます。

ほとんどの状況は、次の 3 つのいずれかのオプションに当てはまります。

* **読み取りの負荷が高く、書き込みの頻度が低い** - 静的、またはほとんど変更されないファイルへの読み取りアクセスを速くします。

  このオプションでは、クライアントにより読み取られるファイルがキャッシュされますが、書き込みは直後にバックエンド ストレージを通してクライアントに渡されます。 キャッシュに格納されているファイルが NFS ストレージ ボリューム上のファイルと自動的に比較されることはありません

  最初にキャッシュに書き込まず、ストレージ システムで直接、ファイルが変更されるリスクがある場合、このオプションは使用しないでください。 これが発生すると、キャッシュされたバージョンのファイルは、バックエンド ファイルと非同期になります。

* **書き込みが 15% を超えています** - このオプションを使用すると、書き込みと読み取りの両方が速くなります。

  クライアントの読み取りとクライアントの書き込みの両方がキャッシュされます。 キャッシュ内のファイルは、バックエンド ストレージ システム上のファイルよりも新しいものと見なされます。 キャッシュされたファイルは、8 時間ごとにバックエンド ストレージのファイルと自動的に照合されます。 キャッシュにある変更後のファイルは、追加の変更なしでキャッシュに 20 分間留まった後にバックエンド ストレージ システムに書き込まれます。

  クライアントがバックエンド ストレージ ボリュームを直接マウントする場合は、このオプションを使用しないでください。これは、古くなったファイルが存在する可能性があるためです。

* **クライアントは、キャッシュをバイパスして NFS ターゲットに書き込みます** - ワークフローのクライアントで、先にキャッシュに書き込まずにストレージ システムにデータが直接書き込まれる場合、またはデータの整合性を最適化する場合、このオプションを選択します。

  クライアントによって要求されたファイルがキャッシュされますが、クライアントからのそれらのファイルの変更は、直後にバックエンド ストレージ システムに戻されます。 キャッシュ内のファイルは、更新があるかどうかを確認するため、バックエンド バージョンと頻繁に照合されます。 この検証では、キャッシュではなくストレージ システムでファイルが直接変更された場合に、データの一貫性が維持されます。

その他のオプションの詳細については、[使用モデル](cache-usage-models.md)に関するページを参照してください。

次の表は、すべての使用モデルの違いをまとめたものです。

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

> [!NOTE]
> **バックエンド検証** の値は、キャッシュ内のファイルとリモート ストレージ内のソース ファイルとの自動比較がどの時点で行われるかを示します。 ただし、バックエンド ストレージ システムで readdirplus 操作を含むクライアント要求を送信することによって、比較をトリガーすることができます。 readdirplus は、ディレクトリ メタデータを返す標準的な NFS API です (拡張読み取りとも呼ばれます)。これにより、キャッシュでファイルが比較され更新されます。

### <a name="create-an-nfs-storage-target"></a>NFS ストレージ ターゲットを作成する

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal からキャッシュ インスタンスを開き、左側のサイド バーにある **[ストレージ ターゲット]** をクリックします。

![テーブルに 2 つの既存のストレージ ターゲットがあり、テーブルの上の [+ ストレージターゲットの追加] ボタンの周囲が強調表示された [設定] > [ストレージ ターゲット] ページのスクリーンショット](media/add-storage-target-button.png)

**[ストレージ ターゲット]** ページには、既存のターゲットがすべて表示されるほか、新たに追加するためのリンクが表示されます。

**[ストレージ ターゲットの追加]** ボタンをクリックします。

![NFS ターゲットが定義されている [ストレージ ターゲットの追加] ページのスクリーンショット](media/add-nfs-target.png)

NFS を使用したストレージ ターゲットについて次の情報を入力します。

* **[ストレージ ターゲット名]** - Azure HPC Cache 内でこのストレージ ターゲットを識別する名前を設定します。

* **[ターゲットの種類]** - **[NFS]** を選択します。

* **[ホスト名]** - NFS ストレージ システムの IP アドレスまたは完全修飾ドメイン名を入力します (ドメイン名は、名前を解決できる DNS サーバーへのアクセス権がキャッシュにある場合にのみ使用します)。ストレージ システムが複数の IP によって参照されている場合は、複数の IP アドレスを入力できます。

* **[使用モデル]** - ワークフローに基づいていずれかのデータ キャッシュ プロファイルを選択します ([前述の「使用モデルを選択する」](#choose-a-usage-model)を参照)。

完了したら、 **[OK]** をクリックしてストレージ ターゲットを追加します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache 向けに Azure CLI を設定します](./az-cli-prerequisites.md)。

ストレージ ターゲットを作成するには、Azure CLI コマンド [az hpc-cache nfs-storage-target add](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_add) を使用します。

> [!NOTE]
> 現在、Azure CLI コマンドでは、ストレージ ターゲットを追加するときに、名前空間パスを作成する必要があります。 これは、Azure portal インターフェイスで使用されるプロセスとは異なります。

キャッシュ名とキャッシュ リソース グループに加えて、次の値を指定します。

* ``--name`` - Azure HPC Cache 内でこのストレージ ターゲットを識別する名前を設定します。
* ``--nfs3-target`` - NFS ストレージ システムの IP アドレス (名前を解決できる DNS サーバーへのアクセス権がキャッシュにある場合、ここでは完全修飾ドメイン名を使用できます)。
* ``--nfs3-usage-model`` - データ キャッシュ プロファイルの 1 つ (前述の「[使用モデルを選択する](#choose-a-usage-model)」を参照)。

  使用モデルの名前を確認するには、コマンド [az hpc-cache usage-model list](/cli/azure/hpc-cache/usage-model#az_hpc_cache_usage_model_list) を使用します。

* ``--junction`` - junction パラメーターによって、クライアント側の仮想ファイル パスがストレージ システム上のエクスポート パスにリンクされます。

  各パスが同じストレージ システム上の別のエクスポートまたはサブディレクトリを表している限り、1 つの NFS ストレージ ターゲットに複数の仮想パスを使用できます。 1 つのストレージ ターゲット上の 1 つのストレージ システムに対するすべてのパスを作成します。

  ストレージ ターゲットではいつでも、[名前空間パスを追加したり、編集したり](add-namespace-paths.md)できます。

  ``--junction`` パラメーターでは次の値が使用されます。

  * ``namespace-path`` - クライアント側の仮想ファイル パス
  * ``nfs-export`` - クライアント側パスに関連付けるストレージ システム エクスポート
  * ``target-path`` (省略可能) - 必要な場合、エクスポートのサブディレクトリ

  例: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  仮想名前空間の機能の詳細については、「[集約された名前空間を構成する](hpc-cache-namespace.md)」を参照してください。

コマンドの例:

```azurecli

az hpc-cache nfs-storage-target add --resource-group "hpc-cache-group" --cache-name "doc-cache0629" \
    --name nfsd1 --nfs3-target 10.0.0.4 --nfs3-usage-model WRITE_WORKLOAD_15 \
    --junction namespace-path="/nfs1/data1" nfs-export="/datadisk1" target-path=""
```

出力:
```azurecli

{- Finished ..
  "clfs": null,
  "id": "/subscriptions/<subscriptionID>/resourceGroups/hpc-cache-group/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "hpc-cache-group",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}

```

---

## <a name="add-a-new-adls-nfs-storage-target"></a>新しい ADLS-NFS ストレージ ターゲットを追加する

ADLS-NFS ストレージ ターゲットは、Network File System (NFS) 3.0 プロトコルをサポートする Azure Blob コンテナーを使用します。

この機能の詳細については、[NFS 3.0 プロトコルのサポート](../storage/blobs/network-file-system-protocol-support.md)に関する記事を参照してください。

ADLS-NFS ストレージ ターゲットには、BLOB ストレージ ターゲットおよび NFS ストレージ ターゲットと類似する点がいくつかあります。 次に例を示します。

* BLOB ストレージ ターゲットと同様に、[ストレージ アカウントにアクセス](#add-the-access-control-roles-to-your-account)するためのアクセス許可を Azure HPC Cache に付与する必要があります。
* NFS ストレージ ターゲットと同様に、キャッシュ[使用モデル](#choose-a-usage-model)を設定する必要があります。
* NFS 対応の BLOB コンテナーには NFS と互換性のある階層構造があるため、データの取り込みにキャッシュを使用する必要はありません。また、コンテナーは他の NFS システムから読み取ることができます。

  ADLS-NFS コンテナーにデータを事前に読み込み、それをストレージ ターゲットとして HPC Cache に追加すると、HPC Cache の外部からデータにアクセスできます。 標準の BLOB コンテナーを HPC Cache 上のストレージ ターゲットとして使用する場合、データは独自仕様の形式で書き込まれ、他の Azure HPC Cache 互換製品からのみアクセスできます。

ADLS-NFS ストレージ ターゲットを作成する前に、NFS 対応のストレージ アカウントを作成する必要があります。 [Azure HPC Cache の前提条件](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements)に関するページの手順と、[NFS を使用した BLOB ストレージのマウント](../storage/blobs/network-file-system-protocol-support-how-to.md)に関するページの説明に従ってください。 キャッシュとストレージ アカウントに同じ仮想ネットワークを使用しない場合は、キャッシュの VNet でストレージアカウントの VNet にアクセスできることを確認します。

ストレージ アカウントを設定した後は、ストレージ ターゲットの作成時に新しいコンテナーを作成できます。

この構成の詳細については、[Azure HPC Cache で NFS マウント済み BLOB ストレージを使用する](nfs-blob-considerations.md)方法に関するページを参照してください。

ADLS-NFS ストレージ ターゲットを作成するには、Azure portal の **[ストレージ ターゲットの追加]** ページを開きます。 (それ以外の方法は開発中です。)

![ADLS-NFS ターゲットが定義されている [ストレージ ターゲットの追加] ページのスクリーンショット](media/add-adls-target.png)

次の情報を入力します。

* **[ストレージ ターゲット名]** - Azure HPC Cache 内でこのストレージ ターゲットを識別する名前を設定します。
* **[ターゲットの種類]** - **ADLS-NFS** を選択します。
* **[ストレージ アカウント]** - 使用するアカウントを選択します。 NFS 対応のストレージ アカウントが一覧に表示されない場合は、前提条件に準拠していること、およびキャッシュからアクセスできることを確認します。

  [アクセス ロールの追加](#add-the-access-control-roles-to-your-account)に関するセクションの説明に従って、ストレージ アカウントへのアクセスをキャッシュ インスタンスに承認する必要があります。

* **[ストレージ コンテナー]** - このターゲットの NFS 対応 BLOB コンテナーを選択するか、 **[新規作成]** をクリックします。

* **[使用モデル]** - ワークフローに基づいていずれかのデータ キャッシュ プロファイルを選択します ([前述の「使用モデルを選択する」](#choose-a-usage-model)を参照)。

完了したら、 **[OK]** をクリックしてストレージ ターゲットを追加します。

## <a name="view-storage-targets"></a>ストレージ ターゲットを表示する

Azure portal または Azure CLI を使用して、キャッシュにすでに定義されているストレージ ターゲットを表示できます。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal からキャッシュ インスタンスを開き、左側のサイド バーの [設定] 見出しの下にある **[ストレージ ターゲット]** をクリックします。 ストレージ ターゲットのページには、既存のすべてのターゲットと、それらを追加または削除するためのコントロールが表示されます。

ストレージ ターゲットの名前をクリックして、その詳細ページを開きます。

詳細については、「[ストレージ ターゲットを管理する](manage-storage-targets.md)」および「[ストレージ ターゲットを編集する](hpc-cache-edit-storage.md)」を参照してください。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache 向けに Azure CLI を設定します](./az-cli-prerequisites.md)。

[az hpc-cache storage-target list](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage-target-list) オプションを使用すると、キャッシュ用の既存のストレージ ターゲットを表示することができます。 キャッシュ名とリソース グループを指定します (それがグローバルに設定している場合は除きます)。

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

特定のストレージ ターゲットに関する詳細を表示するには、[az hpc-cache storage-target show](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage-target-list) を使用します (ストレージ ターゲットを名前で指定します)。

例:

```azurecli
$ az hpc-cache storage-target show --cache-name doc-cache0629 --name nfsd1

{
  "clfs": null,
  "id": "/subscriptions/<subscription_ID>/resourceGroups/scgroup/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "scgroup",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}
$
```

---

## <a name="next-steps"></a>次のステップ

ストレージ ターゲットを作成したら、次のタスクを続行して、キャッシュを使用できるように準備します。

* [集約された名前空間を設定する](add-namespace-paths.md)
* [Azure HPC キャッシュをマウントする](hpc-cache-mount.md)
* [Azure Blob Storage にデータを移動する](hpc-cache-ingest.md)

設定を更新する必要がある場合、[ストレージ ターゲットを編集](hpc-cache-edit-storage.md)できます。
