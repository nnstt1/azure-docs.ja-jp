---
title: AzCopy v10 を使用して Azure Storage にデータをコピーまたは移動する | Microsoft Docs
description: AzCopy は、ストレージ アカウント間のデータ コピーに利用できるコマンドライン ユーティリティです。 この記事は、AzCopy をダウンロードし、ストレージ アカウントに接続し、ファイルを転送する際に役立ちます。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 11/15/2021
ms.author: normesta
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: e2b00c5a0eda3f4679bcbc12a979064cde680e3c
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132516838"
---
# <a name="get-started-with-azcopy"></a>AzCopy を使ってみる

AzCopy は、ストレージ アカウント間の BLOB またはファイル コピーに利用できるコマンドライン ユーティリティです。 この記事は、AzCopy をダウンロードし、ストレージ アカウントに接続し、ファイルを転送する際に役立ちます。

> [!NOTE]
> AzCopy **V10** が現在サポートされているバージョンの AzCopy です。
>
> 以前のバージョンの AzCopy を使用する必要がある場合は、この記事の「[以前のバージョンの AzCopy の使用](#previous-version)」セクションを参照してください。

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>AzCopy をダウンロードする

まず、お使いのコンピューター上の任意のディレクトリに AzCopy V10 実行可能ファイルをダウンロードします。 AzCopy V10 は単に実行可能ファイルなので、インストールするものはありません。

- [Windows 64 ビット](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32 ビット](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [macOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

これらのファイルは、zip ファイル (Windows および Mac) または tar ファイル (Linux) として圧縮されます。 Linux 上で tar ファイルをダウンロードして圧縮を解除するには、お使いの Linux ディストリビューションのドキュメントを参照してください。

> [!NOTE]
> [Azure Table Storage](../tables/table-storage-overview.md) サービスとの間でデータをコピーする場合、[AzCopy バージョン 7.3](https://aka.ms/downloadazcopynet) をインストールしてください。

## <a name="run-azcopy"></a>AzCopy を実行する

利便性のため、AzCopy 実行可能ファイルのディレクトリの場所をご自分のシステム パスに追加して使いやすくすることを検討してください。 そうすると、ご使用のシステム上にある任意のディレクトリから「`azcopy`」を入力できます。

AzCopy ディレクトリをご自分のパスに追加しないことを選択した場合、実際の AzCopy 実行可能ファイルの場所にディレクトリを変更し、Windows PowerShell コマンド プロンプトで「`azcopy`」または「`.\azcopy`」と入力する必要があります。

ご自分の Azure Storage アカウントの所有者であっても、データへのアクセス許可が自動的に割り当てられるわけではありません。 AzCopy を使用して意味のある動作を行う前に、ストレージ サービスに認証資格情報を提供する方法を決定する必要があります。

<a id="choose-how-youll-provide-authorization-credentials"></a>

## <a name="authorize-azcopy"></a>AzCopy を承認する

認証資格情報は、Azure Active Directory (AD) または Shared Access Signature (SAS) トークンを使用して提供できます。

次の表をガイドとして使用してください。

| ストレージの種類 | 現在サポートされている認証方法 |
|--|--|
|**Blob Storage** | Azure AD および SAS |
|**Blob Storage (階層型名前空間)** | Azure AD および SAS |
|**File Storage** | SAS のみ |

#### <a name="option-1-use-azure-active-directory"></a>オプション 1: Azure Active Directory を使用する

<<<<<<< HEAD
このオプションは、Blob Storage でのみ使用できます。 Azure Active Directory を使用すると、各コマンドに SAS トークンを追加する代わりに、資格情報を 1 回入力するだけで済みます。  
=======
このオプションは、BLOB ストレージでのみ使用できます。 Azure Active Directory を使用すると、各コマンドに SAS トークンを追加する代わりに、資格情報を 1 回入力するだけで済みます。
>>>>>>> repo_sync_working_branch

> [!NOTE]
> 現在のリリースでは、ストレージ アカウント間で BLOB をコピーする場合は、各ソース URL に SAS トークンを追加する必要があります。 コピー先 URL からのみ、SAS トークンを省略できます。 例については、「[ストレージ アカウント間で BLOB をコピーする](#transfer-data)」をご覧ください。

Azure AD を使用してアクセスを承認するには、「[AzCopy と Azure Active Directory (Azure AD) を使用して BLOB へのアクセスを承認する](storage-use-azcopy-authorize-azure-active-directory.md)」を参照してください。

#### <a name="option-2-use-a-sas-token"></a>オプション 2:SAS トークンを使用する

AzCopy コマンドで使用する各コピー元または各コピー先の URL に SAS トークンを追加できます。

この例のコマンドでは、ローカル ディレクトリから BLOB コンテナーにデータが繰り返しコピーされます。 架空の SAS トークンが、コンテナー URL の末尾に追加されます。

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

SAS トークンの詳細とその取得方法については、「[Shared Access Signatures (SAS) の使用](./storage-sas-overview.md)」を参照してください。

> [!NOTE]
> ストレージ アカウントの [[安全な転送が必須]](storage-require-secure-transfer.md) 設定によって、ストレージ アカウントへの接続がトランスポート層セキュリティ (TLS) で保護されるかどうかが決まります。 既定では、この設定は有効になっています。

<a id="transfer-data"></a>

## <a name="transfer-data"></a>データの転送

ID を承認するか、SAS トークンを取得したら、データの転送を開始できます。

サンプル コマンドは次の記事のいずれかをご覧ください。

| サービス | [アーティクル] |
|--------|-----------|
|Azure Blob Storage|[Azure Blob Storage にファイルをアップロードする](storage-use-azcopy-blobs-upload.md) |
|Azure Blob Storage|[Azure Blob Storage から BLOB をダウンロードする](storage-use-azcopy-blobs-download.md)|
|Azure Blob Storage|[Azure ストレージ アカウント間で BLOB をコピーする](storage-use-azcopy-blobs-copy.md)|
|Azure Blob Storage|[Azure Blob Storage と同期する](storage-use-azcopy-blobs-synchronize.md)|
|Azure Files |[AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)|
|Amazon S3|[Amazon S3 から Azure Storage にデータをコピーする](storage-use-azcopy-s3.md)|
|Google Cloud Storage|[Google Cloud Storage から Azure Storage にデータをコピーする (プレビュー)](storage-use-azcopy-google-cloud.md)|
|Azure Stack ストレージ|[AzCopy と Azure Stack ストレージを使用してデータを転送する](/azure-stack/user/azure-stack-storage-transfer#azcopy)|

## <a name="get-command-help"></a>コマンドのヘルプを表示する

コマンドの一覧を表示するには、「`azcopy -h`」と入力し、ENTER キーを押します。

特定のコマンドの情報を知るには、単にコマンドの名前を含めてください (例: `azcopy list -h`)。

> [!div class="mx-imgBorder"]
> ![インライン ヘルプ](media/storage-use-azcopy-v10/azcopy-inline-help.png)

### <a name="list-of-commands"></a>コマンドの一覧

次の表に、AzCopy v10 のすべてのコマンドを示します。 各コマンドは、リファレンス記事にリンクされています。

|command|説明|
|---|---|
|[azcopy bench](storage-ref-azcopy-bench.md?toc=/azure/storage/blobs/toc.json)|指定した場所との間でテスト データをアップロードまたはダウンロードすることで、パフォーマンス ベンチマークを実行します。|
|[azcopy copy](storage-ref-azcopy-copy.md?toc=/azure/storage/blobs/toc.json)|ソース データをコピー先の場所にコピーします。|
|[azcopy doc](storage-ref-azcopy-doc.md?toc=/azure/storage/blobs/toc.json)|ツールのドキュメントをマークダウン形式で生成します。|
|[azcopy env](storage-ref-azcopy-env.md?toc=/azure/storage/blobs/toc.json)|AzCopy の動作を構成できる環境変数を示します。|
|[azcopy jobs](storage-ref-azcopy-jobs.md?toc=/azure/storage/blobs/toc.json)|ジョブの管理に関連するサブコマンド。|
|[azcopy jobs clean](storage-ref-azcopy-jobs-clean.md?toc=/azure/storage/blobs/toc.json)|すべてのジョブのログおよびプランのファイルをすべて削除します。|
|[azcopy jobs list](storage-ref-azcopy-jobs-list.md?toc=/azure/storage/blobs/toc.json)|すべてのジョブに関する情報を表示します。|
|[azcopy jobs remove](storage-ref-azcopy-jobs-remove.md?toc=/azure/storage/blobs/toc.json)|指定されたジョブ ID に関連付けられているすべてのファイルを削除します。|
|[azcopy jobs resume](storage-ref-azcopy-jobs-resume.md?toc=/azure/storage/blobs/toc.json)|指定されたジョブ ID を持つ既存のジョブを再開します。|
|[azcopy jobs show](storage-ref-azcopy-jobs-show.md?toc=/azure/storage/blobs/toc.json)|指定されたジョブ ID の詳細情報を表示します。|
|[azcopy load](storage-ref-azcopy-load.md)|特定の形式でのデータ転送に関連するサブコマンド。|
|[azcopy load clfs](storage-ref-azcopy-load-avere-cloud-file-system.md?toc=/azure/storage/blobs/toc.json)|ローカル データをコンテナーに転送し、Microsoft の Avere Cloud FileSystem (CLFS) 形式で格納します。|
|[azcopy list](storage-ref-azcopy-list.md?toc=/azure/storage/blobs/toc.json)|指定されたリソース内のエンティティを一覧表示します。|
|[azcopy login](storage-ref-azcopy-login.md?toc=/azure/storage/blobs/toc.json)|Azure Storage リソースにアクセスするために Azure Active Directory にログインします。|
|[azcopy logout](storage-ref-azcopy-logout.md?toc=/azure/storage/blobs/toc.json)|ユーザーをログアウトし、Azure Storage リソースへのアクセスを終了します。|
|[azcopy make](storage-ref-azcopy-make.md?toc=/azure/storage/blobs/toc.json)|コンテナーまたはファイル共有を作成します。|
|[azcopy remove](storage-ref-azcopy-remove.md?toc=/azure/storage/blobs/toc.json)|Azure ストレージ アカウントから BLOB またはファイルを削除します。|
|[azcopy sync](storage-ref-azcopy-sync.md?toc=/azure/storage/blobs/toc.json)|元の場所を同期先の場所にレプリケートします。|

> [!NOTE]
> AzCopy には、ファイルの名前を変更するコマンドはありません。

## <a name="use-in-a-script"></a>スクリプト内で使用する

#### <a name="obtain-a-static-download-link"></a>静的なダウンロード リンクを取得する

時間と共に、AzCopy の[ダウンロード リンク](#download-and-install-azcopy)は AzCopy の新しいバージョンを指します。 実際のスクリプトで AzCopy をダウンロードする場合、実際のスクリプトで使用する機能が新しいバージョンの AzCopy で変更されていると、スクリプトの動作が停止する可能性があります。

これらの問題を回避するには、AzCopy の現在のバージョンの静的 (変更されない) リンクを取得します。 そうすることで、実際のスクリプトを実行するたびに、まったく同じバージョンの AzCopy がダウンロードされます。

そのリンクを取得するには、このコマンドを実行します。

| オペレーティング システム  | command |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> Linux の場合、`tar` コマンドの `--strip-components=1` では、バージョン名を含む最上位フォルダーが削除され、代わりに現在のフォルダーに直接バイナリが抽出されます。 これにより、`wget` URL を更新するだけで、新しいバージョンの `azcopy` でスクリプトを更新できます。

この URL はこのコマンドの出力に表示されます。 その後、実際のスクリプトでその URL を使用して AzCopy をダウンロードできます。

| オペレーティング システム  | command |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

#### <a name="escape-special-characters-in-sas-tokens"></a>SAS トークンの特殊文字をエスケープする

拡張子が `.cmd` のバッチ ファイルでは、SAS トークンに出現する `%` 文字をエスケープする必要があります。 これを行うには、SAS トークン文字列の既存の `%` 文字の横に、`%` の文字を追加します。

#### <a name="run-scripts-by-using-jenkins"></a>Jenkins を使用してスクリプトを実行する

[Jenkins](https://jenkins.io/) を使用してスクリプトを実行する場合は、必ずスクリプトの先頭に次のコマンドを配置してください。

```
/usr/bin/keyctl new_session
```

## <a name="use-in-azure-storage-explorer"></a>Azure Storage Explorer で使用する

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) は、AzCopy を使用してすべてのデータ転送操作を実行します。 AzCopy のパフォーマンス上の利点を活用する場合は、[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用できますが、ファイルの操作にはコマンド ラインではなくグラフィカル ユーザー インターフェイスを使用することをお勧めします。

Storage Explorer では、ご自分のアカウント キーを使用して、操作を実行します。そのため、Storage Explorer にサインインした後は、追加の承認資格情報を提供する必要はありません。

<a id="previous-version"></a>

## <a name="configure-optimize-and-fix"></a>構成、最適化、修正を行う

次のいずれかのリソースを参照してください。

- [AzCopy の構成設定](storage-ref-azcopy-configuration-settings.md)

- [AzCopy のパフォーマンスを最適化する](storage-use-azcopy-optimize.md)

- [ログ ファイルを使用した Azure Storage での AzCopy V10 の問題のトラブルシューティング](storage-use-azcopy-configure.md)

## <a name="use-a-previous-version"></a>以前のバージョンを使用する

以前のバージョンの AzCopy を使用する必要がある場合は、次のいずれかのリンクを参照してください。

- [Windows での AzCopy (v8)](/previous-versions/azure/storage/storage-use-azcopy)

- [Linux での AzCopy (v7)](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>次のステップ

ご質問、問題、一般的なフィードバックは、[GitHub](https://github.com/Azure/azure-storage-azcopy) ページからお送りください。
