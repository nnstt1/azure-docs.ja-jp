---
title: BLOB のバージョン管理
titleSuffix: Azure Storage
description: Blob Storage のバージョン管理では、以前のバージョンのオブジェクトが自動的に維持され、それらはタイムスタンプで識別されます。 以前のバージョンの BLOB を復元し、データが誤って変更または削除された場合に復旧することができます。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/10/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d56276ab4af0be07a040235bd32e25df382f393d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720494"
---
# <a name="blob-versioning"></a>BLOB のバージョン管理

Blob Storage のバージョン管理を有効にし、以前のバージョンのオブジェクトを自動的に維持することができます。 BLOB のバージョン管理が有効になっている場合は、以前のバージョンの BLOB を復元し、データが誤って変更または削除された場合に復旧することができます。

## <a name="recommended-data-protection-configuration"></a>推奨されるデータ保護の構成

BLOB のバージョン管理は、BLOB データの包括的なデータ保護戦略の一部です。 BLOB データの最適な保護のために、Microsoft では次のデータ保護機能をすべて有効にすることをお勧めしています。

- BLOB のバージョン管理。以前のバージョンの BLOB が自動的に維持されます。 BLOB のバージョン管理が有効になっている場合は、以前のバージョンの BLOB を復元し、データが誤って変更または削除された場合に復旧することができます。 BLOB のバージョン管理を有効にする方法については、「[BLOB のバージョン管理を有効にして管理する](versioning-enable.md)」をご覧ください。
- コンテナーの論理的な削除。削除されたコンテナーを復元します。 コンテナーの論理的な削除を有効にする方法については、「[コンテナーの論理的な削除を有効にして管理する](soft-delete-container-enable.md)」をご覧ください。
- BLOB の論理的な削除。削除された BLOB、スナップショット、またはバージョンを復元します。 BLOB の論理的な削除も有効にする方法については、[BLOB の論理的な削除の有効化と管理](soft-delete-blob-enable.md)に関する記事をご覧ください。

データ保護に関する Microsoft の推奨事項の詳細については、「[データ保護の概要](data-protection-overview.md)」を参照してください。

## <a name="how-blob-versioning-works"></a>BLOB のバージョン管理のしくみ

バージョンによって、指定された時点での BLOB の状態がキャプチャされます。 各バージョンはバージョン ID で識別されます。 ストレージ アカウントに対して BLOB のバージョン管理が有効になっている場合、BLOB が最初に作成されたときと、その BLOB が後で変更されるたびに、一意の ID を持つ新しいバージョンが Azure Storage によって自動的に作成されます。

バージョン ID によって、現在のバージョンまたは以前のバージョンを識別することができます。 BLOB には、一度に 1 つの現在のバージョンのみを含めることができます。

新しい BLOB を作成すると、1 つのバージョンが存在することになり、そのバージョンが現在のバージョンになります。 既存の BLOB を変更すると、現在のバージョンが以前のバージョンになります。 更新された状態を取り込むために新しいバージョンが作成され、その新しいバージョンが現在のバージョンになります。 ある BLOB を削除すると、その BLOB の現在のバージョンが前のバージョンになり、現在のバージョンは存在しなくなります。 その BLOB の以前のすべてのバージョンが保持されます。

次の図は、書き込み操作でバージョンがどのように作成され、以前のバージョンがどのように現在のバージョンに昇格されるかを示しています。

:::image type="content" source="media/versioning-overview/blob-versioning-diagram.png" alt-text="BLOB のバージョン管理のしくみを示す図":::

BLOB のバージョンは変更できません。 既存の BLOB のバージョンの内容やメタデータを変更することはできません。

BLOB ごとに多数のバージョンがあると、BLOB の一覧表示操作の待機時間が長くなる可能性があります。 Microsoft では、BLOB ごとのバージョン数を 1000 未満に保つことをお勧めします。 ライフサイクル管理を使用して、古いバージョンを自動的に削除できます。 ライフサイクル管理の詳細については、「[Azure Blob Storage アクセス層の自動化によるコストの最適化](./lifecycle-management-overview.md)」を参照してください。

BLOB のバージョン管理は、汎用 v2、Premium ブロック BLOB、および従来の BLOB ストレージ アカウントで使用できます。 階層型名前空間が Azure Data Lake Storage Gen2 で使用できるストレージ アカウントは、現在、サポートされていません。

バージョン 2019-10-10 以降の Azure Storage REST API では、BLOB のバージョン管理がサポートされています。

> [!IMPORTANT]
> ストレージ アカウントやコンテナーが誤って削除された場合、BLOB のバージョン管理はその復旧には役立ちません。 ストレージ アカウントが誤って削除されないようにするには、ストレージ アカウント リソースに対してロックを構成します。 ストレージ アカウントのロックの詳細については、「[Azure Resource Manager のロックをストレージ アカウントに適用する](../common/lock-account-resource.md)」を参照してください。

### <a name="version-id"></a>バージョン ID

各 BLOB のバージョンは、一意のバージョン ID によって識別されます。 バージョン ID の値は、BLOB が更新された時点のタイムスタンプです。 バージョン ID は、バージョンの作成時に割り当てられます。

バージョン ID を指定することにより、特定のバージョンの BLOB に対する読み取りや削除の操作を実行できます。 バージョン ID を省略した場合、現在のバージョンに対して操作が行われます。

書き込み操作を呼び出して BLOB を作成または変更すると、Azure Storage によって、応答で *x-ms-version-id* ヘッダーが返されます。 このヘッダーには、書き込み操作で作成された現在のバージョンの BLOB のバージョン ID が含まれています。

バージョン ID はバージョンの有効期間中は変わりません。

### <a name="versioning-on-write-operations"></a>書き込み操作でのバージョン管理

BLOB のバージョン管理が有効になっている場合、BLOB に対する各書き込み操作で新しいバージョンが作成されます。 書き込み操作には [Put Blob](/rest/api/storageservices/put-blob)、[Put Block List](/rest/api/storageservices/put-block-list)、[Copy Blob](/rest/api/storageservices/copy-blob)、および [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata) が含まれます。

書き込み操作で新しい BLOB が作成された場合、結果として得られる BLOB が現在のバージョンの BLOB となります。 書き込み操作で既存の BLOB が変更された場合、現在のバージョンが以前のバージョンになり、更新された BLOB を取り込むために新しい現在のバージョンが作成されます。

次の図は、書き込み操作が BLOB のバージョンにどのように影響するかを示しています。 わかりやすくするために、この記事に示されている図では、バージョン ID がシンプルな整数値として示されています。 実際には、バージョン ID はタイムスタンプです。 現在のバージョンは青色で示され、以前のバージョンは灰色で示されます。

:::image type="content" source="media/versioning-overview/write-operations-blob-versions.png" alt-text="書き込み操作がバージョン管理された BLOB にどのように影響するかを示す図。":::

> [!NOTE]
> ストレージ アカウントに対してバージョン管理を有効にする前に作成された BLOB には、バージョン ID はありません。 BLOB が変更されると、その変更された BLOB が現在のバージョンになり、更新前の BLOB の状態を保存するためにバージョンが作成されます。 バージョンには、その作成時刻であるバージョン ID が割り当てられます。

ストレージ アカウントで BLOB のバージョン管理が有効になっている場合、ブロック BLOB に対するすべての書き込み操作で新しいバージョンの作成がトリガーされます。ただし、[Put Block](/rest/api/storageservices/put-block) 操作は除きます。

ページ BLOB と追加 BLOB の場合、書き込み操作のサブセットによってのみ、バージョンの作成がトリガーされます。 記録される操作には次のようなものがあります。

- [Put Blob](/rest/api/storageservices/put-blob)
- [Put Block List](/rest/api/storageservices/put-block-list)
- [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [Copy Blob](/rest/api/storageservices/copy-blob)

次の操作では、新しいバージョンの作成はトリガーされません。 これらの操作から変更をキャプチャするには、手動スナップショットを作成します。

- [Put Page](/rest/api/storageservices/put-page) (ページ BLOB)
- [Append Block](/rest/api/storageservices/append-block) (追加 BLOB)

すべてのバージョンの BLOB が、同じ BLOB の種類である必要があります。 BLOB に以前のバージョンがある場合、BLOB とそのすべてのバージョンを最初に削除しない限り、ある種類の BLOB を別の種類で上書きすることはできません。

### <a name="versioning-on-delete-operations"></a>削除操作でのバージョン管理

バージョン ID を指定せずに [Delete Blob](/rest/api/storageservices/delete-blob) 操作を呼び出すと、現在のバージョンが以前のバージョンになり、現在のバージョンは存在しなくなります。 既存の以前のバージョンの BLOB はすべて保持されます。

次の図は、バージョン管理された BLOB に対する削除操作の影響を示しています。

:::image type="content" source="media/versioning-overview/delete-versioned-base-blob.png" alt-text="バージョン管理された BLOB の削除を示す図。":::

特定のバージョンの BLOB を削除するには、削除操作に対してそのバージョンの ID を指定します。 ストレージ アカウントに対しても BLOB の論理的な削除が有効になっている場合は、論理的な削除の保有期間が経過するまで、そのバージョンはシステムで保持されます。

新しいデータを BLOB に書き込むと、新しい現在のバージョンの BLOB が作成されます。 次の図に示すように、既存のバージョンは影響を受けません。

:::image type="content" source="media/versioning-overview/recreate-deleted-base-blob.png" alt-text="削除後のバージョン管理された BLOB の再作成を示す図。":::

### <a name="access-tiers"></a>アクセス層

[Set Blob Tier](/rest/api/storageservices/set-blob-tier) 操作を呼び出すことにより、現在のバージョンを含む、任意のバージョンのブロック BLOB を別の BLOB のアクセス層に移動することができます。 古いバージョンの BLOB をクールまたはアーカイブ層に移動することにより、より低い容量価格を利用できます。 詳細については、[BLOB データのホット、クール、アーカイブ アクセス層](access-tiers-overview.md)に関するページを参照してください。

ブロック BLOB を適切な層に移動するプロセスを自動化するには、BLOB のライフ サイクル管理を使用します。 ライフ サイクル管理の詳細については、「[Azure Blob Storage のライフ サイクルを管理する](./lifecycle-management-overview.md)」を参照してください。

## <a name="enable-or-disable-blob-versioning"></a>BLOB のバージョン管理を有効または無効にする

BLOB のバージョン管理を有効または無効にする方法については、「[BLOB のバージョン管理を有効にして管理する](versioning-enable.md)」をご覧ください。

BLOB のバージョン管理を無効にしても、既存の BLOB、バージョン、スナップショットは削除されません。 BLOB のバージョン管理を無効にした場合、既存のすべてのバージョンはストレージ アカウントで引き続きアクセス可能です。 その後、新しいバージョンは作成されません。

ストレージ アカウントに対してバージョン管理が無効になった後に BLOB が作成または変更された場合、BLOB を上書きすると新しいバージョンが作成されます。 更新された BLOB は現在のバージョンではなくなり、バージョン ID はありません。 BLOB に対する以降のすべての更新では、前の状態を保存せずにデータが上書きされます。

バージョン管理が無効になった後に、バージョン ID を使用してバージョンを読み取ったり削除したりすることができます。 また、バージョン管理が無効になった後に、BLOB のバージョンを一覧表示することもできます。

次の図は、バージョン管理が無効になった後に BLOB を変更すると、バージョン管理されない BLOB がどのように作成されるかを示しています。 BLOB に関連付けられている既存のすべてのバージョンが保持されます。

:::image type="content" source="media/versioning-overview/modify-base-blob-versioning-disabled.png" alt-text="バージョン管理が無効になった後に変更されたベース BLOB を示す図。":::

## <a name="blob-versioning-and-soft-delete"></a>BLOB のバージョン管理と論理的な削除

最適なデータ保護のため、Microsoft ではストレージ アカウントのバージョン管理と BLOB の論理的な削除の両方を有効にすることをお勧めしています。 BLOB の論理的な削除の詳細については、[Azure Storage BLOB の論理的な削除](./soft-delete-blob-overview.md)に関するページを参照してください。

### <a name="overwriting-a-blob"></a>BLOB の上書き

ストレージ アカウントに対して BLOB のバージョン管理と BLOB の論理的な削除の両方が有効になっている場合、BLOB を上書きすると、新しいバージョンが自動的に作成されます。 新しいバージョンは論理的に削除されず、論理的な削除の保有期間が過ぎても削除されません。 論理的に削除されたスナップショットは作成されません。

### <a name="deleting-a-blob-or-version"></a>BLOB またはバージョンの削除

ストレージ アカウントに対してバージョン管理と論理的な削除の両方が有効になっている場合、ある BLOB を削除すると、その BLOB の現在のバージョンが以前のバージョンになります。 新しいバージョンは作成されず、論理的に削除されたスナップショットは作成されません。 削除された BLOB に対しては、論理的な削除の保有期間は適用されません。

論理的な削除では、BLOB のバージョンを削除するための追加の保護が提供されます。 以前のバージョンの BLOB を削除すると、そのバージョンは論理的に削除されます。 論理的に削除されたバージョンは、論理的な削除の保有期間が経過するまで保持され、経過すると完全に削除されます。

以前のバージョンの BLOB を削除するには、**Delete Blob** 操作を呼び出し、バージョン ID を指定します。

次の図は、BLOB または BLOB のバージョンを削除した場合の動作を示しています。

:::image type="content" source="media/versioning-overview/soft-delete-historical-version.png" alt-text="論理的な削除が有効になっているバージョンの削除を示す図。":::

### <a name="restoring-a-soft-deleted-version"></a>論理的に削除されたバージョンの復元

[Undelete Blob](/rest/api/storageservices/undelete-blob) 操作を使用すると、論理的に削除されたバージョンを論理的な削除の保有期間中に復元できます。 **Undelete Blob** 操作では常に、BLOB の論理的に削除されたすべてのバージョンが復元されます。 論理的に削除された 1 つのバージョンのみを復元することはできません。

**Undelete Blob** 操作で論理的に削除されたバージョンを復元しても、どのバージョンも現在のバージョンに昇格されません。 現在のバージョンを復元するには、まず論理的に削除されたすべてのバージョンを復元してから、[Copy Blob](/rest/api/storageservices/copy-blob) 操作を使用して以前のバージョンを新しい現在のバージョンにコピーします。

次の図は、**Undelete Blob** 操作で論理的に削除された BLOB のバージョンを復元する方法と、**Copy Blob** 操作で現在のバージョンの BLOB を復元する方法を示しています。

:::image type="content" source="media/versioning-overview/undelete-version.png" alt-text="論理的に削除されたバージョンを復元する方法を示す図。":::

論理的な削除の保有期間が経過した後、論理的に削除された BLOB のバージョンは完全に削除されます。

## <a name="blob-versioning-and-blob-snapshots"></a>BLOB のバージョン管理と BLOB のスナップショット

BLOB のスナップショットは、ある時点で作成された BLOB の読み取り専用コピーです。 BLOB のスナップショットと BLOB のバージョンは似ていますが、スナップショットがユーザーまたはアプリケーションによって手動で作成されるのに対し、BLOB のバージョンは、ストレージ アカウントで BLOB のバージョン管理が有効になっている場合に書き込みまたは削除操作で自動的に作成されます。

> [!IMPORTANT]
> Microsoft では、BLOB のバージョン管理を有効にした後で、ブロック BLOB のスナップショットの作成を停止するようにアプリケーションを更新することもお勧めします。 ストレージ アカウントでバージョン管理が有効になっている場合、すべてのブロック BLOB の更新と削除がキャプチャされ、バージョンによって保持されます。 BLOB のバージョン管理が有効になっている場合、スナップショットを作成してもブロック BLOB データに対する追加の保護は提供されず、コストとアプリケーションの複雑さが増す可能性があります。

### <a name="snapshot-a-blob-when-versioning-is-enabled"></a>バージョン管理が有効になっている場合に BLOB のスナップショットを作成する

お勧めしませんが、バージョン管理もされる BLOB のスナップショットを作成することができます。 バージョン管理を有効にしたときに、BLOB のスナップショットの作成を停止するようにアプリケーションを更新できない場合、アプリケーションではスナップショットとバージョンの両方をサポートできます。

バージョン管理された BLOB のスナップショットを作成すると、スナップショットの作成と同時に新しいバージョンが作成されます。 スナップショットの作成時に、新しい現在のバージョンも作成されます。

次の図は、バージョン管理された BLOB のスナップショットを作成した場合の動作を示しています。 この図では、バージョン ID が 2 および 3 の BLOB バージョンとスナップショットに同一のデータが含まれています。

:::image type="content" source="media/versioning-overview/snapshot-versioned-blob.png" alt-text="バージョン管理された BLOB のスナップショットを示す図。":::

## <a name="authorize-operations-on-blob-versions"></a>BLOB のバージョンに対する操作を承認する

次のいずれかの方法を使用して、BLOB のバージョンへのアクセスを承認することができます。

- Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、Azure Active Directory (Azure AD) セキュリティ プリンシパルにアクセス許可を付与する。 セキュリティと使いやすさのために、Azure AD を使用することをお勧めします。 BLOB 操作での Azure AD の使用に関する詳細については、「[Azure Storage 内のデータへのアクセスを認可する](../common/authorize-data-access.md)」を参照してください。
- Shared Access Signature (SAS) を使用して、BLOB バージョンへのアクセスを委任する。 BLOB バージョンを表す、署名済みリソースの種類である `bv` のバージョン ID を指定して、特定のバージョンに対する操作の SAS トークンを作成します。 Shared Access Signature の詳細については、「[Shared Access Signatures (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](../common/storage-sas-overview.md)」を参照してください。
- アカウント アクセス キーを使用して、共有キーでの BLOB バージョンに対する操作を承認する。 詳細については、[共有キーによる承認](/rest/api/storageservices/authorize-with-shared-key)に関するページを参照してください。

BLOB のバージョン管理は、誤削除や悪意のある削除からデータを保護するように設計されています。 保護を強化するために、BLOB バージョンを削除するには特殊なアクセス許可が必要です。 以下のセクションでは、BLOB バージョンを削除するために必要なアクセス許可について説明します。

### <a name="azure-rbac-action-to-delete-a-blob-version"></a>BLOB バージョンを削除する Azure RBAC アクション

次の表は、BLOB または BLOB バージョンを削除する Azure RBAC アクションをまとめたものです。

| 説明 | Blob service の操作 | 必要な Azure RBAC データ アクション | Azure の組み込みロールのサポート |
|----------------------------------------------|------------------------|---------------------------------------------------------------------------------------|-------------------------------|
| 現在のバージョンの削除 | Delete Blob | **Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete** | ストレージ BLOB データ共同作成者 |
| 以前のバージョンの削除 | Delete Blob | **Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteBlobVersion/action** | ストレージ BLOB データ所有者 |

### <a name="shared-access-signature-sas-parameters"></a>Shared Access Signature (SAS) パラメーター

BLOB バージョンの署名済みリソースは、`bv` です。 詳細については、「[サービス SAS を作成する](/rest/api/storageservices/create-service-sas)」または「[ユーザー委任 SAS の作成](/rest/api/storageservices/create-user-delegation-sas)」を参照してください。

次の表には、BLOB バージョンを削除するために SAS に必要な権限が示されています。

| **権限** | **URI の略記** | **許可される操作** |
|----------------|----------------|------------------------|
| 削除         | x              | BLOB バージョンを削除します。 |

## <a name="pricing-and-billing"></a>価格と課金

BLOB のバージョン管理を有効にすると、アカウントに追加のデータ ストレージ料金が発生する可能性があります。 不要なコストを抑えるためにも、アプリケーションを設計する際は、この料金が発生するしくみを理解しておくことが重要です。

BLOB のスナップショットと同様に、BLOB のバージョンは、アクティブなデータと同じレートで課金されます。 バージョンの課金方法は、ベース BLOB またはそのいずれかのバージョン (またはスナップショット) のどちらに対して層を明示的に設定したかによって異なります。 BLOB 層の詳細については、[BLOB データのホット、クール、アーカイブ アクセス層](access-tiers-overview.md)に関するページを参照してください。

BLOB またはバージョンの層を変更していない場合は、その BLOB、そのバージョン、およびそれが持つ可能性があるすべてのスナップショットにわたるデータの一意のブロックに対して課金されます。 詳細については、「[BLOB 層が明示的に設定されていない場合の課金](#billing-when-the-blob-tier-has-not-been-explicitly-set)」を参照してください。

BLOB またはバージョンの層を変更した場合は、BLOB とバージョンが最終的に同じ層になるかどうかに関係なく、オブジェクト全体に対して課金されます。 詳細については、「[BLOB 層が明示的に設定されている場合の課金](#billing-when-the-blob-tier-has-been-explicitly-set)」を参照してください。

> [!NOTE]
> 頻繁に上書きされるデータに対してバージョン管理を有効にすると、ストレージ容量の料金が増えたり、操作を一覧表示している間の待ち時間が長くなったりすることがあります。 これらの問題を軽減するには、頻繁に上書きされるデータを、バージョン管理が無効になっている別のストレージ アカウントに格納します。

BLOB のスナップショットの課金の詳細については、「[BLOB のスナップショット](snapshots-overview.md)」を参照してください。

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>BLOB 層が明示的に設定されていない場合の課金

ベース BLOB またはそのいずれかのバージョンに対して BLOB 層を明示的に設定していない場合は、BLOB、そのバージョン、およびそれが持つ可能性があるすべてのスナップショットにわたる一意のブロックまたはページについて課金されます。 BLOB とそのバージョン間で共有されるデータは、1 回だけ課金されます。 BLOB が更新されると、ベース BLOB 内のデータはそのバージョンに格納されているデータから分岐し、一意のデータはブロックまたはページごとに課金されます。

ブロック BLOB 内のブロックを置き換えた場合、以後そのブロックは一意のブロックとして課金されます。 これは、ブロックのブロック ID とデータが前のバージョンと同じである場合にも当てはまります。 再度コミットされた後のブロックは、前のバージョンの対応するブロックから分岐し、そのデータに対する料金が発生します。 これは、まったく同じデータでページ BLOB 内のページを更新した場合にも当てはまります。

BLOB ストレージには、2 つのブロックに同一のデータが含まれているかどうかを判断する手段はありません。 アップロードされてコミットされたブロックは、同じデータや同じブロック ID がある場合でも、それぞれが一意のものとして扱われます。 料金は一意のブロックに対して発生するため、バージョン管理が有効になっている場合に BLOB を更新すると、一意のブロックが増え、追加料金が発生することに留意することが重要です。

BLOB のバージョン管理が有効になっているときは、ブロック BLOB に対して更新操作を呼び出し、可能な限り少ない数のブロックが更新されるようにします。 ブロックの詳細な制御を許可する書き込み操作は、[Put Block](/rest/api/storageservices/put-block) と [Put Block List](/rest/api/storageservices/put-block-list) です。 一方、[Put Blob](/rest/api/storageservices/put-blob) 操作では、BLOB の内容全体が置き換えられるため、追加料金が発生する可能性があります。

次のシナリオでは、BLOB 層が明示的に設定されていないときに、ブロック BLOB とそのバージョンについて料金が発生するしくみを示します。

#### <a name="scenario-1"></a>シナリオ 1

シナリオ 1 では、BLOB に前のバージョンがあります。 バージョンが作成されてから BLOB は更新されていません。したがって、一意のブロックである 1、2、3 に対してのみ、料金が発生します。

![ベース BLOB と前のバージョンでの一意のブロックに対する課金を示す図 1。](./media/versioning-overview/versions-billing-scenario-1.png)

#### <a name="scenario-2"></a>シナリオ 2

シナリオ 2 では、BLOB の 1 つのブロック (図のブロック 3) が更新されています。 更新されたブロックに同じデータと ID が含まれている場合でも、以前のバージョンのブロック 3 と同じではありません。 このため、4 ブロック分の料金がアカウントに課金されます。

![ベース BLOB と前のバージョンでの一意のブロックに対する課金を示す図 2。](./media/versioning-overview/versions-billing-scenario-2.png)

#### <a name="scenario-3"></a>シナリオ 3

シナリオ 3 では、BLOB は更新されていますが、バージョンは更新されていません。 ベース BLOB のブロック 3 がブロック 4 に置き換わっていますが、以前のバージョンでは引き続きブロック 3 が反映されています。 このため、4 ブロック分の料金がアカウントに課金されます。

![ベース BLOB と前のバージョンでの一意のブロックに対する課金を示す図 3。](./media/versioning-overview/versions-billing-scenario-3.png)

#### <a name="scenario-4"></a>シナリオ 4

シナリオ 4 では、ベース BLOB が完全に更新されており、元のブロックは 1 つも含まれていません。 その結果、アカウントには、8 つ (ベース BLOB の 4 つと、以前のバージョンの 4 つ) のすべての一意のブロックの料金が課金されます。 このシナリオは、[Put Blob](/rest/api/storageservices/put-blob) 操作で BLOB に書き込む場合に発生する可能性があります。これは、ベース BLOB の内容全体が置き換えられるためです。

![ベース BLOB と前のバージョンでの一意のブロックに対する課金を示す図 4。](./media/versioning-overview/versions-billing-scenario-4.png)

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>BLOB 層が明示的に設定されている場合の課金

BLOB またはバージョン (またはスナップショット) の BLOB 層が明示的に設定されている場合、元の層のオブジェクトとブロックが共有されているかどうかにかかわらず、新しい層におけるオブジェクトのコンテンツの完全な長さに対して課金されます。 また、元の層の最も古いバージョンのコンテンツの完全な長さに対しても課金されます。 元の層に残っているそれ以外の以前のバージョンまたはスナップショットについては、「[BLOB 層が明示的に設定されていない場合の課金](#billing-when-the-blob-tier-has-not-been-explicitly-set)」で説明されているように、共有できる一意のブロックに対して課金されます。

#### <a name="moving-a-blob-to-a-new-tier"></a>新しい層への BLOB の移動

次の表では、BLOB またはバージョンを新しい層に移動する場合の課金の動作について説明します。

| BLOB 層の明示的な設定対象 | 課金の対象 |
|-|-|
| 前のバージョンがあるベース BLOB | 新しい層のベース BLOB と、元の層の最も古いバージョン、および他のバージョンの一意のブロック。<sup>1</sup> |
| 前のバージョンとスナップショットがあるベース BLOB | 新しい層のベース BLOB、元の層の最も古いバージョン、元の層の最も古いスナップショット、および他のバージョンまたはスナップショットの一意のブロック。<sup>1</sup> |
| 前のバージョン | 新しい層のバージョンと、元の層のベース BLOB、および他のバージョンの一意のブロック。<sup>1</sup> |

<sup>1</sup> 元の層から移動されていない他の以前のバージョンまたはスナップショットがある場合、それらのバージョンまたはスナップショットは、「[BLOB 層が明示的に設定されていない場合の課金](#billing-when-the-blob-tier-has-not-been-explicitly-set)」で説明されているように、それに含まれる一意のブロックの数に基づいて課金されます。

次の図は、バージョン管理された BLOB を別の層に移動した場合のオブジェクトの課金方法を示したものです。

:::image type="content" source="media/versioning-overview/versioning-billing-tiers.png" alt-text="バージョン管理された BLOB が明示的に階層化されたときのオブジェクトの課金方法を示す図。":::

BLOB、バージョン、またはスナップショットに対する層の明示的な設定を、元に戻すことはできません。 BLOB を新しい層に移動した後、元の層に戻すと、元の層の他のオブジェクトとブロックが共有されている場合でも、オブジェクトのコンテンツの完全な長さに対して課金されます。

BLOB、バージョン、またはスナップショットの層を明示的に設定する操作には、次のものがあります。

- [Set Blob Tier](/rest/api/storageservices/set-blob-tier)
- 層を指定した [Put Blob](/rest/api/storageservices/put-blob)
- 層を指定した [Put Block List](/rest/api/storageservices/put-block-list)
- 層を指定した [Copy Blob](/rest/api/storageservices/copy-blob)

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>論理的な削除が有効になっている場合の BLOB の削除

BLOB の論理的な削除が有効になっている場合に、層が明示的に設定されているベース BLOB を削除または上書きすると、論理的に削除された BLOB の以前のすべてのバージョンが、コンテンツの完全な長さで課金されます。 BLOB のバージョン管理と論理的な削除の連携のしくみの詳細については、「[BLOB のバージョン管理と論理的な削除](#blob-versioning-and-soft-delete)」を参照してください。

次の表では、バージョン管理が有効か無効かによる、論理的に削除される BLOB の課金動作を説明します。 バージョン管理が有効になっている場合、BLOB を論理的に削除すると、バージョンが作成されます。 バージョン管理が無効になっている場合、BLOB を論理的に削除すると、論理的な削除のスナップショットが作成されます。

| ベース BLOB を明示的に層を設定して上書きする場合 | 課金の対象 |
|-|-|
| BLOB の論理的な削除とバージョン管理の両方が有効になっている場合 | 層に関係なく、すべての既存バージョンがコンテンツ全体の長さで。 |
| BLOB の論理的な削除は有効になっているが、バージョン管理は無効になっている場合 | 層に関係なく、すべての既存の論理的な削除のスナップショットがコンテンツ全体の長さで。 |

## <a name="feature-support"></a>機能サポート

次の表は、アカウントでのこの機能のサポートと、特定の機能を有効にした場合のサポートへの影響を示しています。

| ストレージ アカウントの種類 | Blob Storage (既定のサポート) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> | SFTP <sup>1</sup> |
|--|--|--|--|--|
| Standard 汎用 v2 | ![はい](../media/icons/yes-icon.png) |![いいえ](../media/icons/no-icon.png)              | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Premium ブロック BLOB          | ![はい](../media/icons/yes-icon.png) |![いいえ](../media/icons/no-icon.png)              | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2、ネットワーク ファイル システム (NFS) 3.0 プロトコル、セキュア ファイル転送プロトコル (SFTP) のサポートでは、すべて階層型名前空間が有効になっているストレージ アカウントが必要です。

## <a name="see-also"></a>関連項目

- [BLOB のバージョン管理を有効にして管理する](versioning-enable.md)
- [BLOB のスナップショットの作成](/rest/api/storageservices/creating-a-snapshot-of-a-blob)
- [Azure Storage BLOB の論理的な削除](./soft-delete-blob-overview.md)
