---
title: Azure Data Box Disk からストレージ アカウントへのデータのアップロードの確認に関するチュートリアル
description: このチュートリアルを使用して、Azure Data Box Disk から Azure Storage アカウントにアップロードされたデータを確認する方法を学習します。
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 09/17/2019
ms.author: alkohli
ms.openlocfilehash: 53e2db3728d92a862fce64ba1fc379a2ae2205ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591636"
---
::: zone target="docs"

# <a name="tutorial-verify-data-upload-from-azure-data-box-disk"></a>チュートリアル:Azure Data Box Disk からのデータのアップロードを確認する

これは、Azure Data Box Disk のデプロイについて取り上げたシリーズの最後のチュートリアルです。 このチュートリアルでは、次の事項について説明します。

> [!div class="checklist"]
> * Azure へのデータのアップロードを確認する
> * Data Box Disk からデータを消去する

## <a name="prerequisites"></a>前提条件

開始する前に、「[チュートリアル:Azure Data Box Disk を返送する](data-box-disk-deploy-picked-up.md)」を参照してください。


## <a name="verify-data-upload-to-azure"></a>Azure へのデータのアップロードを確認する

運送業者によってディスクが集荷されると、ポータルの注文状態が "**集荷されました**" に更新されます。 追跡 ID も表示されます。

![ディスクが集荷済み](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Microsoft がディスクを受け取ってスキャンすると、ジョブの状態が "**受取済み**" に更新されます。 

![ディスク受取済み](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Azure データセンター内のサーバーにディスクが接続されると、データが自動的にコピーされます。 データのサイズによっては、コピー処理が完了するまでに数時間から数日かかる場合があります。 コピー ジョブの進行状況は、ポータルで監視することができます。

コピーが完了すると、注文の状態が "**完了**" に更新されます。

![データのコピーが完了](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

コピーがエラーで完了した場合は、[アップロード エラーのトラブルシューティング](data-box-disk-troubleshoot-upload.md)に関するページを参照してください。

コピー元からデータを削除する前に、データがストレージ アカウントに存在することを確認します。 データは次の場所にあります。

- お使いの Azure Storage アカウント。 データを Data Box にコピーする場合は、そのデータがタイプに応じて Azure Storage アカウントの次のいずれかのパスにアップロードされます。

  - ブロック BLOB およびページ BLOB の場合: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files の場合: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    あるいは、Azure portal でお使いの Azure ストレージ アカウントにアクセスし、そこから移動することもできます。

- マネージド ディスク リソース グループ。 マネージド ディスクを作成するとき、VHD はページ BLOB としてアップロードされた後、マネージド ディスクに変換されます。 マネージド ディスクは、注文の作成時に指定されたリソース グループに接続されています。

  - Azure のマネージド ディスクへのコピーが正常に完了した場合は、Azure portal の **[注文の詳細]** に移動して、マネージド ディスクに指定されているリソース グループをメモすることができます。

      ![[注文の詳細] を表示する](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    メモしたリソース グループに移動し、目的のマネージド ディスクを見つけます。

      ![マネージド ディスク用のリソース グループ](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

    > [!NOTE]
    > ページ BLOB は、データ コピー中に正常にマネージド ディスクに変換されなかった場合、ストレージ アカウントに残るため、ストレージの使用料を課金されます。

  -  VHDX または動的/差分 VHD をコピーした場合、VHDX/VHD はブロック BLOB としてステージング ストレージ アカウントにアップロードされます。 ステージング環境の **[ストレージ アカウント] > [BLOB]** に移動し、適切なコンテナー (StandardSSD、StandardHDD、または PremiumSSD) を選択します。 VHDX/VHD は、ステージング ストレージ アカウントにブロック BLOB として表示されます。
  

  
::: zone-end

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure へのデータのアップロードを確認する

データが Azure にアップロードされた後、コピー元からデータを削除する前に、データがストレージ アカウントに存在することを確認します。 データは次の場所にあります。

- お使いの Azure Storage アカウント。 データを Data Box にコピーする場合は、そのデータがタイプに応じて Azure Storage アカウントの次のいずれかのパスにアップロードされます。

    - **ブロック BLOB およびページ BLOB の場合**: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`

    - **Azure Files の場合**: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

- マネージド ディスク リソース グループ。 マネージド ディスクを作成するとき、VHD はページ BLOB としてアップロードされた後、マネージド ディスクに変換されます。 マネージド ディスクは、注文の作成時に指定されたリソース グループに接続されています。

::: zone-end

Azure にデータがアップロードされたことを確認するには、次の手順を実行します。

1. ディスクの注文に関連付けられているストレージ アカウントに移動します。
2. **[Blob service] > [BLOB の参照]** に移動します。 コンテナーの一覧が表示されます。 *BlockBlob* フォルダーと *PageBlob* フォルダーに作成したサブフォルダーに対応して、同じ名前のコンテナーがご利用のストレージ アカウントに作成されます。
    Azure の名前付け規則にフォルダー名が準拠していない場合、Azure へのデータのアップロードに失敗します。

3. データセット全体が読み込み済みであることを確認するには、Microsoft Azure Storage Explorer を使用します。 Data Box Disk 注文に対応するストレージ アカウントをアタッチし、BLOB コンテナーの一覧に注目します。 いずれかのコンテナーを選択し、**[その他]** をクリックして **[Folder statistics]\(フォルダーの統計情報\)** をクリックします。 **[アクティビティ]** ウィンドウに、そのフォルダーの統計情報 (BLOB 数、合計 BLOB サイズなど) が表示されます。 合計 BLOB サイズ (バイト単位) がデータセットのサイズと一致している必要があります。

    ![フォルダーの統計情報を Storage Explorer で表示](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Data Box Disk からデータを消去する

Azure へのアップロードが完了すると、Data Box Disk は、[NIST SP 800-88](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) 標準に従ってディスク上のデータを消去します。

::: zone target="docs"

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Data Box Disk に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * Azure へのデータのアップロードを確認する
> * Data Box Disk からデータを消去する


次の記事に進み、Azure portal で Data Box Disk を管理する方法について学習してください。

> [!div class="nextstepaction"]
> [Azure portal を使用して Azure Data Box Disk を管理する](./data-box-portal-ui-admin.md)

::: zone-end




