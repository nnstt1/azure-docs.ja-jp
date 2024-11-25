---
title: Azure Data Box、Azure Data Box Heavy へのデータ コピー中の問題のトラブルシューティング
description: Azure Data Box と Azure Data Box Heavy デバイスにデータをコピーするときの問題のトラブルシューティングを行う方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 08/11/2021
ms.author: alkohli
ms.openlocfilehash: e3921e8b326be2163981eacc089e16e6fcf6f1bc
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772413"
---
# <a name="troubleshoot-data-copy-issues-on-azure-data-box-and-azure-data-box-heavy"></a>Azure Data Box と Azure Data Box Heavy でのデータ コピーの問題のトラブルシューティング

この記事では、Azure Data Box または Azure Data Box Heavy のインポート注文でデータ コピーやデータ アップロードを実行するときの問題のトラブルシューティングを行う方法について説明します。 記事には、データが Data Box にコピーされるとき、または Data Box からアップロードされるときに、発生する可能性があるエラーの一覧が含まれます。

デバイス上の共有へのアクセスに関する問題のトラブルシューティングについては、[データ コピー中の共有接続エラーのトラブルシューティング](data-box-troubleshoot-share-access.md)に関する記事を参照してください。


> [!NOTE]
> この記事の情報はインポート注文にのみ適用されます。

## <a name="error-classes"></a>エラーのクラス

Data Box および Data Box Heavy でのエラーをまとめると次のようになります。

| エラー カテゴリ*        | 説明        | 推奨される操作    |
|----------------------------------------------|---------|--------------------------------------|
| コンテナーまたは共有の名前 | コンテナーまたは共有名が、Azure の名前付け規則に従っていません。  |エラー一覧をダウンロードします。 <br> コンテナーまたは共有の名前を変更します。 [詳細については、こちらを参照してください](#container-or-share-name-errors)。  |
| コンテナーまたは共有のサイズ制限 | コンテナーまたは共有内のデータの合計が、Azure の制限を超えています。   |エラー一覧をダウンロードします。 <br> コンテナーまたは共有内のデータの総量を削減します。 [詳細については、こちらを参照してください](#container-or-share-size-limit-errors)。|
| オブジェクトまたはファイルのサイズ制限 | コンテナーまたは共有内のオブジェクトまたはファイルが、Azure の制限を超えています。|エラー一覧をダウンロードします。 <br> コンテナーまたは共有内のファイルのサイズを削減します。 [詳細については、こちらを参照してください](#object-or-file-size-limit-errors)。 |    
| データまたはファイルの種類 | データの形式またはファイルの種類がサポートされていません。 |エラー一覧をダウンロードします。 <br> ページ BLOB またはマネージド ディスクの場合は、データが 512 バイト アラインであり、事前に作成されたフォルダーにコピーされていることを確認します。 [詳細については、こちらを参照してください](#data-or-file-type-errors)。 |
| フォルダーまたはファイルの内部エラー | ファイルまたはフォルダーに内部エラーがあります。 |エラー一覧をダウンロードします。 <br> ファイルを削除し、もう一度コピーします。 フォルダーの場合は、ファイルの名前を変更するか、ファイルを追加または削除することで変更します。 エラーは 30 分以内に解消されます。  [詳細については、こちらを参照してください](#folder-or-file-internal-errors)。 |
| BLOB またはファイルの重大ではないエラー  | BLOB またはファイルの名前が Azure の名前付け規則に従っていないか、ファイルの種類がサポートされていません。 | これらの BLOB またはファイルはコピーされない場合、または名前を変更される場合があります。 [これらのエラーを修正する方法](#non-critical-blob-or-file-errors)。 |

\* 最初の 5 つのエラー カテゴリは重大なエラーであり、発送準備に進む前に修正する必要があります。


## <a name="container-or-share-name-errors"></a>コンテナーまたは共有の名前のエラー

これらは、コンテナーと共有の名前に関連するエラーです。

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**エラーの説明:** コンテナーまたは共有の名前は、3 文字から 63 文字で指定する必要があります。 

**推奨される解決方法:** Data Box または Data Box Heavy 共有 (SMB または NFS) の下にあるフォルダー (データのコピー先) は、ストレージ アカウント内の Azure コンテナーになります。 

- デバイスのローカル Web UI の **[接続とコピー]** ページで、エラー ファイルをダウンロードして確認し、問題があるフォルダーの名前を特定します。
- Data Box または Data Box Heavy 共有の下にあるフォルダーの名前を変更します。次の点に留意してください。

    - 名前は 3 から 63 文字で指定します。
    - 名前には、文字、数字、ハイフンのみを使用できます。
    - 名前の先頭または末尾をハイフンにすることはできません。
    - 名前に連続するハイフンを含めることはできません。
    - 有効な名前の例: `my-folder-1`、`my-really-extra-long-folder-111`
    - 無効な名前の例: `my-folder_1`、`my`、`--myfolder`、`myfolder--`、`myfolder!`

    詳細については、[コンテナー名](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)と[共有名](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)の Azure 名前付け規則を参照してください。


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**エラーの説明:** コンテナーまたは共有の名前に使用できるのは、英字、数字、またはハイフンだけです。

**推奨される解決方法:** Data Box または Data Box Heavy 共有 (SMB または NFS) の下にあるフォルダー (データのコピー先) は、ストレージ アカウント内の Azure コンテナーになります。 

- デバイスのローカル Web UI の **[接続とコピー]** ページで、エラー ファイルをダウンロードして確認し、問題があるフォルダーの名前を特定します。
- Data Box または Data Box Heavy 共有の下にあるフォルダーの名前を変更します。次の点に留意してください。

    - 名前は 3 から 63 文字で指定します。
    - 名前には、文字、数字、ハイフンのみを使用できます。
    - 名前の先頭または末尾をハイフンにすることはできません。
    - 名前に連続するハイフンを含めることはできません。
    - 有効な名前の例: `my-folder-1`、`my-really-extra-long-folder-111`
    - 無効な名前の例: `my-folder_1`、`my`、`--myfolder`、`myfolder--`、`myfolder!`

    詳細については、[コンテナー名](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)と[共有名](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)の Azure 名前付け規則を参照してください。

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**エラーの説明:** コンテナー名と共有名では、先頭または末尾をハイフンにすることはできず、連続するハイフンを含めることもできません。

**推奨される解決方法:** Data Box または Data Box Heavy 共有 (SMB または NFS) の下にあるフォルダー (データのコピー先) は、ストレージ アカウント内の Azure コンテナーになります。 

- デバイスのローカル Web UI の **[接続とコピー]** ページで、エラー ファイルをダウンロードして確認し、問題があるフォルダーの名前を特定します。
- Data Box または Data Box Heavy 共有の下にあるフォルダーの名前を変更します。次の点に留意してください。

    - 名前は 3 から 63 文字で指定します。
    - 名前には、文字、数字、ハイフンのみを使用できます。
    - 名前の先頭または末尾をハイフンにすることはできません。
    - 名前に連続するハイフンを含めることはできません。
    - 有効な名前の例: `my-folder-1`、`my-really-extra-long-folder-111`
    - 無効な名前の例: `my-folder_1`、`my`、`--myfolder`、`myfolder--`、`myfolder!`

    詳細については、[コンテナー名](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)と[共有名](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)の Azure 名前付け規則を参照してください。
    
### <a name="error_file_or_directory_name_illegal"></a>ERROR_FILE_OR_DIRECTORY_NAME_ILLEGAL

**エラーの説明**: ディレクトリまたはコンテナーの名前に無効な文字が含まれています。

**推奨される解決方法**: コピーしたディレクトリまたはコンテナーの名前に、サポートされていない文字が含まれています。

- ローカル Web UI の [接続とコピー] ページで、エラー ファイルをダウンロードして確認し、問題があるフォルダーの名前を特定します。 
- ディレクトリまたはコンテナーの名前を変更して、Azure の名前付け規則に準拠していることを確認します。

詳細については、[ディレクトリ](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)と[コンテナー](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)の Azure の名前付け規則に関する記事を参照してください。

## <a name="container-or-share-size-limit-errors"></a>コンテナーまたは共有のサイズ制限のエラー

これらは、コンテナーまたは共有で許容されるデータのサイズを超過したデータに関連するエラーです。

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**エラーの説明:** Azure ファイル共有でデータの共有が 5 TiB に制限されており、ストレージ アカウントで大きなファイル共有が有効になっていません。 一部の共有で、この制限を超えました。

**推奨される解決方法:** ローカル Web UI の **[接続とコピー]** ページで、エラー ファイルをダウンロードして確認します。

- エラー ログから、この問題があるフォルダーを特定し、そのフォルダー内のファイルが 5 TiB 未満であることを確認します。
- 5 TiB の制限は、大きなファイル共有を許可しているストレージ アカウントには適用されません。 ただし、注文時に大きなファイル共有が構成されている必要があります。 
  - [Microsoft サポート](data-box-disk-contact-microsoft-support.md)に連絡して、新しい配送先住所ラベルを要求してください。
  - [ストレージ アカウントで大きなファイル共有を有効にします](../storage/files/storage-how-to-create-file-share.md#enable-large-files-shares-on-an-existing-account)
  - [ストレージ アカウントのファイル共有を拡張](../storage/files/storage-how-to-create-file-share.md#expand-existing-file-shares)し、クォータを 100 TiB に設定します。
  
  
## <a name="object-or-file-size-limit-errors"></a>オブジェクトまたはファイルのサイズ制限のエラー

これらは、Azure で許可されているオブジェクトまたはファイルの最大サイズを超過したデータに関連するエラーです。 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**エラーの説明:** ファイル サイズがアップロードできる最大ファイル サイズを超えています。

**推奨される解決方法:** BLOB またはファイルのサイズが、アップロード可能な最大サイズを超えています。

- ローカル Web UI の **[接続とコピー]** ページで、エラー ファイルをダウンロードして確認します。
- BLOB およびファイルのサイズが、Azure オブジェクト サイズの制限を超えていないことを確認します。

## <a name="data-or-file-type-errors"></a>データまたはファイルの種類のエラー

これらは、コンテナーまたは共有で検出されたサポートされていないファイルの種類またはデータの種類に関連するエラーです。 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**エラーの説明:** BLOB またはファイルが正しくアラインされていません。

**推奨される解決方法:** Data Box または Data Box Heavy のページ BLOB 共有では、512 バイトにアラインされたファイルのみがサポートされます (VHD や VHDX など)。 ページ BLOB 共有にコピーされたデータはすべて、ページ BLOB として Azure にアップロードされます。

ページ BLOB 共有から、VHD または VHDX 以外のデータを削除します。 汎用データには、ブロック BLOB または Azure ファイル用の共有を使用できます。

詳細については、[ページ BLOB の概要](../storage/blobs/storage-blob-pageblob-overview.md)に関するページを参照してください。

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**エラーの説明:** マネージド ディスク共有に、サポートされていないファイルの種類が存在します。 容量固定の VHD のみが許可されます。

**推奨される解決方法:**

- 容量固定の VHD のみをアップロードしてマネージド ディスクを作成していることを確認します。
- VHDX ファイル、**動的** および **差分** VHD はサポートされていません。

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**エラーの説明:** ディレクトリは、マネージド ディスク用の既存のフォルダーでは許可されません。 これらのフォルダーでは、容量固定の VHD のみが許可されます。

**推奨される解決方法:** マネージド ディスクでは、各共有内に、お使いのストレージ アカウントのコンテナーに対応する次の 3 つのフォルダーが作成されます。Premium SSD、Standard HDD、および Standard SSD。 これらのフォルダーは、マネージド ディスクのパフォーマンス レベルに対応します。

- これらの既存のフォルダーのいずれかにページ BLOB のデータ (VHD) をコピーしていることを確認します。
- フォルダーまたはディレクトリは、これらの既存のフォルダーでは許可されません。 既存のフォルダー内に作成したすべてのフォルダーを削除します。

詳細については、[マネージド ディスクへのコピー](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)に関するページを参照してください。

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**エラーの説明:** シンボリック リンクは、Linux では許可されません。 

**推奨される解決方法:** シンボリック リンクは、通常、リンク、パイプ、および他のそのようなファイルです。 リンクを削除するか、またはリンクを解決してデータをコピーします。

## <a name="folder-or-file-internal-errors"></a>フォルダーまたはファイルの内部エラー

**エラーの説明:** ファイルまたはフォルダーが内部エラー状態にあります。

**推奨される解決策:** ファイルの場合は、ファイルを削除し、もう一度コピーします。 フォルダーの場合は、フォルダーを変更します。 フォルダーの名前を変更します。または、フォルダーにファイルを追加するか、フォルダーからファイルを削除します。 このエラーは、30 分以内に自然に解消されます。 エラーが引き続き発生する場合は、Microsoft サポートに問い合わせてください。

## <a name="non-critical-blob-or-file-errors"></a>BLOB またはファイルの重大ではないエラー

次のセクションでは、データのコピー中に表示される、BLOB、ファイル、またはコンテナーの名前に関連する重大ではないエラーがすべてまとめられています。 これらのエラーが存在する場合は、Azure の名前付け規則に従って名前が変更されます。 データ アップロードの対応する注文の状態は、**完了 (警告あり)** になります。  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**エラーの説明:** BLOB またはファイルの名前に、サポートされていない制御文字が含まれています。

**推奨される解決方法:** コピーした BLOB またはファイルに、サポートされていない文字を使った名前が含まれています。

ローカル Web UI の **[接続とコピー]** ページで、エラー ファイルをダウンロードして確認します。
ファイルを削除するか名前を変更し、サポートされていない文字を削除します。

詳細については、[BLOB 名](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)と[ファイル名](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)の Azure 名前付け規則を参照してください。

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**エラーの説明:** BLOB またはファイルの名前に、無効な文字が含まれています。

**推奨される解決方法:** コピーした BLOB またはファイルに、サポートされていない文字を使った名前が含まれています。

ローカル Web UI の **[接続とコピー]** ページで、エラー ファイルをダウンロードして確認します。
ファイルを削除するか名前を変更し、サポートされていない文字を削除します。

詳細については、[BLOB 名](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)と[ファイル名](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)の Azure 名前付け規則を参照してください。


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**エラーの説明:** BLOB またはファイルの名前が、無効な文字で終わっています。

**推奨される解決方法:** コピーした BLOB またはファイルに、サポートされていない文字を使った名前が含まれています。

ローカル Web UI の **[接続とコピー]** ページで、エラー ファイルをダウンロードして確認します。
ファイルを削除するか名前を変更し、サポートされていない文字を削除します。

詳細については、[BLOB 名](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)と[ファイル名](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)の Azure 名前付け規則を参照してください。


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**エラーの説明:** BLOB またはファイルの名前に含まれるパスのセグメントが多すぎます。

**推奨される解決方法:** コピーした BLOB またはファイルが、パスのセグメントの最大数を超えています。 パスのセグメントは、連続する区切り記号文字 (スラッシュ "/" など) の間の文字列です。

- ローカル Web UI の **[接続とコピー]** ページで、エラー ファイルをダウンロードして確認します。
- [BLOB 名](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)と[ファイル名](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)が Azure 名前付け規則に準拠していることを確認します。

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**エラーの説明:** BLOB またはファイルの名前が長すぎます。

**推奨される解決方法:** BLOB またはファイル名が最大長を超えています。

- ローカル Web UI の **[接続とコピー]** ページで、エラー ファイルをダウンロードして確認します。
- BLOB 名は 1,024 文字を超えてはいけません。
- 名前が 1,024 文字を超えないように、BLOB またはファイルを削除するか名前を変更します。

詳細については、BLOB 名とファイル名の Azure 名前付け規則を参照してください。

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**エラーの説明:** BLOB またはファイルの名前のセグメントの 1 つが長すぎます。

**推奨される解決方法:** BLOB またはファイルの名前に含まれるパスのセグメントの 1 つが、最大文字数を超えています。 パスのセグメントは、連続する区切り記号文字 (スラッシュ "/" など) の間の文字列です。

- ローカル Web UI の **[接続とコピー]** ページで、エラー ファイルをダウンロードして確認します。
- [BLOB 名](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)と[ファイル名](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)が Azure 名前付け規則に準拠していることを確認します。


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**エラーの説明:** マネージド ディスク共有に不適切なコンテナー名が指定されています。

**推奨される解決方法:** マネージド ディスクでは、各共有内に、お使いのストレージ アカウントのコンテナーに対応する次の 4 つのフォルダーが作成されます。Premium SSD、Standard HDD、および Standard SSD。 これらのフォルダーは、マネージド ディスクのパフォーマンス レベルに対応します。

- これらの既存のフォルダーのいずれかにページ BLOB のデータ (VHD) をコピーしていることを確認します。 Azure にアップロードされるのは、これらの既存のコンテナーのデータのみです。
- Premium SSD、Standard HDD、および Standard SSD と同じレベルで作成されるその他のフォルダーは、いずれも有効なパフォーマンス レベルに対応しておらず、使用することはできません。
- これらのパフォーマンス レベル以外で作成されたファイルまたはフォルダーを削除します。

詳細については、[マネージド ディスクへのコピー](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

- [Data Box Blob ストレージのシステム要件](data-box-system-requirements-rest.md)を確認します。
