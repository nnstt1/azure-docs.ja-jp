---
title: BLOB の論理的な削除
titleSuffix: Azure Storage
description: BLOB の論理的な削除では、お客様のデータを保護して、アプリケーションまたは別のストレージ アカウント ユーザーによってデータが誤って変更または削除されたときに簡単に復旧できるようにします。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/23/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 1e40e8cb31019fa8a9dd35f41dc677225d613180
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720589"
---
# <a name="soft-delete-for-blobs"></a>BLOB の論理的な削除

BLOB の論理的な削除を使用すると、削除されたデータがシステムに一定の期間保持されることにより、個々の BLOB、スナップショット、またはバージョンが誤った削除または上書きから保護されます。 保持期間中は、論理的に削除されたオブジェクトを削除された時点の状態に復元することができます。 保持期間が経過すると、オブジェクトは完全に削除されます。

> [!IMPORTANT]
> 階層型名前空間機能が有効になっているアカウントでの論理的な削除は、現在プレビュー段階であり、すべての Azure リージョンでグローバルに利用できます。
> ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。
>
>
> プレビューに登録するには、[こちらのフォーム](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pUOVRVOUpDRUtHVUtDUUtMVTZUR0tUMjZWNy4u)を参照してください。

## <a name="recommended-data-protection-configuration"></a>推奨されるデータ保護の構成

BLOB の論理的な削除は、BLOB データの包括的なデータ保護戦略の一部です。 BLOB データの最適な保護のためには、次のデータ保護機能をすべて有効にすることをお勧めします。

- コンテナーの論理的な削除。削除されたコンテナーを復元します。 コンテナーの論理的な削除を有効にする方法については、「[コンテナーの論理的な削除を有効にして管理する](soft-delete-container-enable.md)」をご覧ください。
- BLOB のバージョン管理。以前のバージョンの BLOB が自動的に維持されます。 BLOB のバージョン管理が有効になっている場合は、以前のバージョンの BLOB を復元し、データが誤って変更または削除された場合に復旧することができます。 BLOB のバージョン管理を有効にする方法については、「[BLOB のバージョン管理を有効にして管理する](versioning-enable.md)」をご覧ください。
- BLOB の論理的な削除。削除された BLOB、スナップショット、またはバージョンを復元します。 BLOB の論理的な削除も有効にする方法については、[BLOB の論理的な削除の有効化と管理](soft-delete-blob-enable.md)に関する記事をご覧ください。

データ保護に関する Microsoft の推奨事項の詳細については、「[データ保護の概要](data-protection-overview.md)」を参照してください。

## <a name="how-blob-soft-delete-works"></a>BLOB の論理的な削除のしくみ

ストレージ アカウントで BLOB の論理的な削除を有効にするときは、削除されたオブジェクトの保持期間を 1 日から 365 日の範囲で指定します。 保持期間は、データが削除または上書きされた後で、そのデータを使用できる期間を示します。 オブジェクトが削除または上書きされるとすぐに、保持期間が開始されます。

保持期間がアクティブな間は、[Undelete Blob](/rest/api/storageservices/undelete-blob) 操作を呼び出すことによって、削除された BLOB とそのスナップショット、または削除されたバージョンを復元できます。 次の図は、BLOB の論理的な削除が有効になっている場合に、削除されたオブジェクトを復元する方法を示したものです。

:::image type="content" source="media/soft-delete-blob-overview/blob-soft-delete-diagram.png" alt-text="論理的に削除された BLOB を復元する方法を示す図":::

論理的な削除のリテンション期間はいつでも変更できます。 更新された保持期間は、保持期間が変更された後で削除されたデータにのみ適用されます。 保持期間が変更される前に削除されたデータには、削除された時点で有効であった保持期間が適用されます。

論理的に削除されたオブジェクトの削除を試みても、有効期限に影響はありません。

BLOB の論理的な削除を無効にすると、論理的な削除の保持期間が経過するまでは、ストレージ アカウント内の論理的に削除されたオブジェクトに引き続きアクセスして復旧できます。

BLOB のバージョン管理は、汎用 v2、ブロック BLOB、Blob Storage の各アカウントで使用できます。 階層型名前空間を持つストレージ アカウントは、現在サポートされていません。

バージョン 2017-07-29 以降の Azure Storage REST API では、BLOB の論理的な削除がサポートされています。

> [!IMPORTANT]
> BLOB の論理的な削除は、個々の BLOB、スナップショット、(階層型名前空間の) ディレクトリ、またはバージョンの復元のみに使用できます。 コンテナーとその内容を復元するには、ストレージ アカウントでコンテナーの論理的な削除を有効にする必要があります。 BLOB データを完全に保護するために、コンテナーの論理的な削除と BLOB のバージョン管理を BLOB の論理的な削除と共に有効にすることをお勧めします。 詳細については、「[データ保護の概要](data-protection-overview.md)」を参照してください。
>
> BLOB 論理的な削除を使用して、ストレージ アカウントの削除から保護することはできません。 ストレージ アカウントを削除から保護するには、ストレージ アカウント リソースに対してロックを構成します。 ストレージ アカウントのロックの詳細については、「[Azure Resource Manager のロックをストレージ アカウントに適用する](../common/lock-account-resource.md)」を参照してください。

### <a name="how-deletions-are-handled-when-soft-delete-is-enabled"></a>論理的な削除が有効になっているときに削除が処理される方法

BLOB の論理的な削除が有効になっている場合、BLOB を削除すると、その BLOB は論理的に削除済みとしてマークされます。 スナップショットは作成されません。 保持期間が終了すると、論理的に削除された BLOB は完全に削除されます。

BLOB にスナップショットがある場合、スナップショットも削除しない限り、BLOB を削除することはできません。 BLOB とそのスナップショットを削除すると、BLOB とスナップショットの両方が論理的に削除済みとしてマークされます。 新しいスナップショットは作成されません。

ベース BLOB を削除せずに、アクティブなスナップショットを 1 つ以上削除することもできます。 この場合、スナップショットは論理的に削除されます。

階層型名前空間機能が有効になっているアカウントでディレクトリが削除された場合、ディレクトリとそのすべての内容は、論理的な削除としてマークされます。

論理的に削除されたオブジェクトは、明示的に表示または一覧表示されない限り、非表示になります。 論理的に削除されたオブジェクトを一覧表示する方法の詳細については、「[論理的に削除された BLOB を管理および復元する](soft-delete-blob-manage.md)」を参照してください。

### <a name="how-overwrites-are-handled-when-soft-delete-is-enabled"></a>論理的な削除が有効になっているときに上書きが処理される方法

> [!IMPORTANT]
> このセクションは、階層型名前空間を持つアカウントには適用されません。

[Put Blob](/rest/api/storageservices/put-blob)、[Put Block List](/rest/api/storageservices/put-block-list)、[Copy Blob](/rest/api/storageservices/copy-blob) などの操作を呼び出すと、BLOB 内のデータが上書きされます。 BLOB の論理的な削除が有効になっている場合、BLOB を上書きすると、書き込み操作の前に、BLOB の状態の論理的に削除されたスナップショットが自動的に作成されます。 保持期間が終了すると、論理的に削除されたスナップショットは完全に削除されます。

論理的に削除されたスナップショットは、論理的に削除されたオブジェクトが明示的に表示または一覧表示されない限り、非表示になります。 論理的に削除されたオブジェクトを一覧表示する方法の詳細については、「[論理的に削除された BLOB を管理および復元する](soft-delete-blob-manage.md)」を参照してください。

コピー操作を保護するには、コピー先のストレージ アカウントに対して BLOB の論理的な削除を有効にする必要があります。

BLOB の論理的な削除では、BLOB のメタデータまたはプロパティを書き込む操作からは保護されません。 BLOB のメタデータまたはプロパティが更新されても、論理的に削除されたスナップショットは作成されません。

アーカイブ層の BLOB については、BLOB の論理的な削除により上書きの保護は提供されません。 アーカイブ層内の BLOB が任意の層の新しい BLOB で上書きされた場合、上書きされた BLOB は完全に削除されます。

Premium Storage アカウントの場合、論理的に削除されたスナップショットは、BLOB ごとの制限である 100 スナップショットにはカウントされません。

### <a name="restoring-soft-deleted-objects"></a>論理的に削除されたオブジェクトの復元

保持期間内に [Undelete Blob](/rest/api/storageservices/undelete-blob) 操作を呼び出すことで、論理的に削除された BLOB または (階層型名前空間の) ディレクトリを復元できます。 **Undelete Blob** 操作を実行すると、BLOB とそれに関連付けられている論理的に削除されたスナップショットが復元されます。 保持期間中に削除されたすべてのスナップショットが復元されます。

階層型名前空間を持つアカウントでは、**BLOB の削除の取り消し** 操作を使用して、論理的に削除されたディレクトリとそのすべての内容を復元することもできます。 論理的に削除された BLOB を含むディレクトリの名前を変更すると、それらの論理的に削除された BLOB はディレクトリから切断されます。 それらの BLOB を復元するには、ディレクトリの名前を元の名前に戻すか、元のディレクトリ名を使用する別のディレクトリを作成する必要があります。 そうしないと、これらの論理的に削除された BLOB を復元しようとしたときにエラーが発生します。

論理的に削除されていない BLOB に対して **Undelete Blob** を呼び出すと、その BLOB に関連付けられている論理的に削除されたすべてのスナップショットが復元されます。 BLOB にスナップショットがなく、論理的に削除されていない場合、**Undelete Blob** を呼び出しても何も行われません。

論理的に削除されたスナップショットをベース BLOB にレベル上げするには、最初にベース BLOB で **Undelete Blob** を呼び出して、BLOB とそのスナップショットを復元します。 次に、目的のスナップショットをベース BLOB に上書きコピーします。 新しい BLOB にスナップショットをコピーすることもできます。

論理的に削除された BLOB またはスナップショット内のデータは、オブジェクトが復元されるまで読み取ることができません。

論理的に削除されたオブジェクトを復元する方法の詳細については、「[論理的に削除された BLOB を管理および復元する](soft-delete-blob-manage.md)」を参照してください。

## <a name="blob-soft-delete-and-versioning"></a>BLOB の論理的な削除とバージョン管理

> [!IMPORTANT]
> バージョン管理は、階層型名前空間があるアカウントではサポートされていません。

ストレージ アカウントで BLOB のバージョン管理と BLOB の論理的な削除の両方が有効になっている場合、BLOB を上書きすると、新しいバージョンが自動的に作成されます。 新しいバージョンは論理的に削除されず、論理的な削除の保有期間が過ぎても削除されません。 論理的に削除されたスナップショットは作成されません。 ある BLOB を削除すると、その BLOB の現在のバージョンが前のバージョンになり、現在のバージョンは存在しなくなります。 新しいバージョンは作成されず、論理的に削除されたスナップショットは作成されません。

論理的な削除とバージョン管理を一緒に有効にすると、BLOB のバージョンが削除から保護されます。 論理的な削除が有効になっている場合、バージョンを削除すると、論理的に削除されたバージョンが作成されます。 **Undelete Blob** 操作を使用すると、論理的に削除されたバージョンを論理的な削除の保有期間中に復元できます。 **Undelete Blob** 操作では常に、BLOB の論理的に削除されたすべてのバージョンが復元されます。 論理的に削除された 1 つのバージョンのみを復元することはできません。

論理的な削除の保有期間が経過した後、論理的に削除された BLOB のバージョンは完全に削除されます。

> [!NOTE]
> バージョン管理が有効になっている場合に、削除された BLOB に対して **Undelete Blob** 操作を呼び出すと、論理削除されたバージョンまたはスナップショットは復元されますが、現在のバージョンは復元されません。 現在のバージョンを復元するには、前のバージョンを現在のバージョンにコピーして昇格させます。

最適なデータ保護のため、ストレージ アカウントのバージョン管理と BLOB の論理的な削除の両方を有効にすることをお勧めします。 BLOB のバージョン管理と論理的な削除の併用の詳細については、「[BLOB のバージョン管理と論理的な削除](versioning-overview.md#blob-versioning-and-soft-delete)」を参照してください。

## <a name="blob-soft-delete-protection-by-operation"></a>操作による BLOB の論理的な削除の保護

次の表は、BLOB の論理的な削除が有効にされていて、BLOB のバージョン管理が有効または無効にされている場合の、削除操作と書き込み操作の想定される動作を示しています。

### <a name="storage-account-no-hierarchical-namespace"></a>ストレージ アカウント (階層型名前空間なし)

| REST API の操作 | 論理的な削除が有効 | 論理的な削除とバージョン管理が有効 |
|--|--|--|
| [Delete Storage Account](/rest/api/storagerp/storageaccounts/delete) | 変更はありません。 削除されたアカウントのコンテナーと BLOB は復旧できません。 | 変更はありません。 削除されたアカウントのコンテナーと BLOB は復旧できません。 |
| [Delete Container](/rest/api/storageservices/delete-container) | 変更はありません。 削除されたコンテナーの BLOB は復旧できません。 | 変更はありません。 削除されたコンテナーの BLOB は復旧できません。 |
| [Delete Blob](/rest/api/storageservices/delete-blob) | BLOB の削除に使った場合、その BLOB は論理的に削除済みとしてマークされます。 <br /><br /> BLOB のスナップショットを削除するために使用した場合、そのスナップショットは論理的に削除済みとしてマークされます。 | BLOB を削除するために使用した場合、現在のバージョンが前のバージョンになり、現在のバージョンは削除されます。 新しいバージョンは作成されず、論理的に削除されたスナップショットは作成されません。<br /><br /> BLOB のバージョンを削除するために使用した場合、そのバージョンは論理的に削除済みとしてマークされます。 |
| [BLOB の削除の取り消し](/rest/api/storageservices/undelete-blob) | 保持期間内に削除された BLOB とすべてのスナップショットが復元されます。 | 保持期間内に削除された BLOB とすべてのバージョンが復元されます。 |
| [Put Blob](/rest/api/storageservices/put-blob)<br />[Put Block List](/rest/api/storageservices/put-block-list)<br />[Copy Blob](/rest/api/storageservices/copy-blob)<br />[Copy Blob from URL](/rest/api/storageservices/copy-blob) | アクティブな BLOB に対して呼び出した場合、操作の前の BLOB の状態のスナップショットが自動的に生成されます。 <br /><br /> 論理的に削除された BLOB に対して呼び出した場合、同じ種類の BLOB で置き換えられている場合にのみ、BLOB の以前の状態のスナップショットが生成されます。 BLOB の種類が異なる場合は、既存のすべての論理的に削除されたデータが完全に削除されます。 | 操作の前の BLOB の状態をキャプチャした新しいバージョンが、自動的に生成されます。 |
| [Put Block](/rest/api/storageservices/put-block) | アクティブな BLOB にブロックをコミットするために使用した場合、変更はありません。<br /><br />論理的に削除された BLOB にブロックをコミットするために使用した場合、新しい BLOB が作成され、論理的に削除された BLOB の状態をキャプチャするためにスナップショットが自動的に生成されます。 | 変更はありません。 |
| [Put Page](/rest/api/storageservices/put-page)<br />[Put Page from URL](/rest/api/storageservices/put-page-from-url) | 変更はありません。 この操作を使って上書きまたは消去されるページ BLOB のデータは保存されず、復旧できません。 | 変更はありません。 この操作を使って上書きまたは消去されるページ BLOB のデータは保存されず、復旧できません。 |
| [Append Block](/rest/api/storageservices/append-block)<br />[Append Block from URL](/rest/api/storageservices/append-block-from-url) | 変更はありません。 | 変更はありません。 |
| [Set Blob Properties](/rest/api/storageservices/set-blob-properties) | 変更はありません。 上書きされた BLOB のプロパティは復旧できません。 | 変更はありません。 上書きされた BLOB のプロパティは復旧できません。 |
| [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata) | 変更はありません。 上書きされた BLOB のメタデータは復旧できません。 | 操作の前の BLOB の状態をキャプチャした新しいバージョンが、自動的に生成されます。 |
| [Set Blob Tier](/rest/api/storageservices/set-blob-tier) | ベース BLOB は新しい層に移動されます。 アクティブなスナップショットまたは論理的に削除されたスナップショットは、元の層に残ります。 論理的に削除されたスナップショットは作成されません。 | ベース BLOB は新しい層に移動されます。 アクティブなバージョンまたは論理的に削除されたバージョンは、元の層に残ります。 新しいバージョンは作成されません。 |

### <a name="storage-account-hierarchical-namespace"></a>ストレージ アカウント (階層型名前空間)

|**REST API の操作**|**論理的な削除が有効**|
|---|---|
|[パス - 削除](/rest/api/storageservices/datalakestoragegen2/path/delete) |論理的に削除された BLOB またはディレクトリが作成されます。 論理的に削除されたオブジェクトは、保有期間が経過すると削除されます。|
|[Delete Blob](/rest/api/storageservices/delete-blob)|論理的に削除されたオブジェクトが作成されます。 論理的に削除されたオブジェクトは、保有期間が経過すると削除されます。 論理的な削除は、スナップショットを含む BLOB とスナップショットに対してはサポートされません。|
|[パス - 作成](/rest/api/storageservices/datalakestoragegen2/path/create) (BLOB またはディレクトリの名前が変更される) | 既存の操作先の BLOB または空のディレクトリは、論理的に削除され、ソースによって置き換えられます。 論理的に削除されたオブジェクトは、保有期間が経過すると削除されます。|

## <a name="feature-support"></a>機能サポート

次の表は、アカウントでのこの機能のサポートと、特定の機能を有効にした場合のサポートへの影響を示しています。

| ストレージ アカウントの種類 | Blob Storage (既定のサポート) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> | SFTP <sup>1</sup> |
|--|--|--|--|--|
| Standard 汎用 v2 | ![はい](../media/icons/yes-icon.png) |![はい](../media/icons/yes-icon.png)  <sup>2</sup>  <sup>3</sup>            | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Premium ブロック BLOB          | ![はい](../media/icons/yes-icon.png) |![はい](../media/icons/yes-icon.png)  <sup>2</sup>  <sup>3</sup>            | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2、ネットワーク ファイル システム (NFS) 3.0 プロトコル、セキュア ファイル転送プロトコル (SFTP) のサポートでは、すべて階層型名前空間が有効になっているストレージ アカウントが必要です。

<sup>2</sup>    機能はプレビュー レベルでサポートされています。

<sup>3</sup>    詳細については、「[Azure Data Lake Storage Gen2 に関する既知の問題](data-lake-storage-known-issues.md)」を参照してください。 これらの問題は、階層型名前空間機能が有効になっているすべてのアカウントに適用されます。

## <a name="pricing-and-billing"></a>価格と課金

論理的に削除されたデータはすべて、アクティブなデータと同じレートで課金されます。 保持期間が経過した後、完全に削除されたデータについては請求されません。

論理的な削除を有効にするときは、保持期間を短くしてこの機能が請求に及ぼす影響をよく理解することをお勧めします。 推奨される最小保持期間は 7 日です。

頻繁に上書きされるデータに対して論理的な削除を有効にすると、ストレージ容量の料金が増えたり、BLOB を一覧表示するときの待ち時間が長くなったりすることがあります。 論理的な削除が無効な別のストレージ アカウントに頻繁に上書きされるデータを格納することで、この追加のコストと待機時間を軽減できます。

BLOB が上書きまたは削除されるとき、スナップショットまたはバージョンの自動生成に関連するトランザクションに対しては課金されません。 **Undelete Blob** 操作の呼び出しについては、書き込み操作のトランザクションのレートで課金されます。

Blob Storage の料金の詳細については、[Blob Storage の価格](https://azure.microsoft.com/pricing/details/storage/blobs/)に関するページを参照してください。

## <a name="blob-soft-delete-and-virtual-machine-disks"></a>BLOB の論理的な削除と仮想マシンのディスク

BLOB の論理的な削除は、内部のページ BLOB である Premium と Standard の両方のアンマネージド ディスクで使用できます。 論理的な削除は、[Delete Blob](/rest/api/storageservices/delete-blob)、[Put Blob](/rest/api/storageservices/put-blob)、[Put Block List](/rest/api/storageservices/put-block-list)、[Copy Blob](/rest/api/storageservices/copy-blob) の各操作についてのみ、削除または上書きされたデータの復旧に役立ちます。

[Put Page](/rest/api/storageservices/put-page) を呼び出すことで上書きされたデータは復旧できません。 Azure 仮想マシンからアンマネージド ディスクへの書き込みには、[Put Page](/rest/api/storageservices/put-page) の呼び出しが使用されます。そのため、Azure VM からアンマネージド ディスクへの書き込みを、論理的な削除を使用して元に戻すことはできません。

## <a name="next-steps"></a>次のステップ

- [BLOB の論理的な削除の有効化](./soft-delete-blob-enable.md)
- [論理的に削除された BLOB を管理および復元する](soft-delete-blob-manage.md)
- [BLOB のバージョン管理](versioning-overview.md)
