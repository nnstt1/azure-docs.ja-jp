---
title: 'チュートリアル: Blob Storage で静的な Web サイトをホストする - Azure Storage'
description: 静的な Web サイト ホスティングのためにストレージ アカウントを構成し、静的な Web サイトを Azure Storage にデプロイする方法について説明します。
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 11/04/2021
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 2bc3579c1e1c3748ded0cf68d7f75ec269e9d1e0
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853379"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>チュートリアル: Blob Storage で静的な Web サイトをホストする

このチュートリアルでは、静的な Web サイトを構築し、Azure Storage にデプロイする方法を学習します。 終了すると、ユーザーがパブリックにアクセスできる静的な Web サイトが用意できます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> - 静的な Web サイト ホスティングを構成する
> - Hello World Web アプリをデプロイする

[!INCLUDE [static websites vs static web apps](../../../includes/storage-blob-static-website-vs-static-web-app.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

> [!NOTE]
> 汎用 v2 Standard ストレージ アカウントだけでなく、階層型名前空間が有効になっているストレージ アカウントでも、静的 Web サイトを使用できます。

このチュートリアルでは [Visual Studio Code](https://code.visualstudio.com/download) (プログラマ向け無料ツール) を使用して、静的な Web サイトを構築し、それを Azure Storage アカウントにデプロイします。

Visual Studio Code をインストールした後、Azure Storage プレビューの拡張機能をインストールします。 この拡張機能では、Azure Storage の管理機能を Visual Studio Code と統合します。 拡張機能を使用して、Azure Storage に静的な Web サイトをデプロイします。 拡張機能をインストールするには、次のようにします。

1. Visual Studio Code を起動します。
2. ツール バーの **[拡張機能]** をクリックします。 *Azure Storage* を検索し、リストで **[Azure Storage]** の拡張機能を選択します。 次に、**[インストール]** ボタンをクリックして拡張機能をインストールします。

    ![VS Code で Azure Storage 拡張機能をインストールする](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure ポータル](https://portal.azure.com/)にサインインして、作業を開始します。

## <a name="configure-static-website-hosting"></a>静的な Web サイト ホスティングを構成する

最初の手順では、Azure ポータルで静的な Web サイトをホストするためにストレージ アカウントを構成します。 静的な Web サイト ホスティングを構成する際に、Azure Storage で *$web* という名前のコンテナーが自動的に作成されます。 *$web* コンテナーには、静的な Web サイトのファイルが含まれます。

1. Web ブラウザーで [Azure ポータル](https://portal.azure.com/)を開きます。
1. ストレージ アカウントを見つけて、アカウントの概要を表示します。
1. **[静的な Web サイト]** を選択して、静的な Web サイトの構成ページを表示します。
1. **[有効]** を選択して、ストレージ アカウントに対して静的な Web サイト ホスティングを有効にします。
1. **[インデックス ドキュメント名]** フィールドで、*index.html* の既定のインデックス ページを指定します。 既定のインデックス ページは、ユーザーが静的な Web サイトのルートに移動したときに表示されます。
1. **[エラー ドキュメントのパス]** フィールドで、*404.html* の既定のエラー ページを指定します。 既定のエラー ページは、ユーザーが静的な Web サイトに存在しないページに移動しようとしたときに表示されます。
1. **[保存]** をクリックします。 これで、Azure ポータルで静的な Web サイトのエンドポイントが表示されるようになります。

    ![ストレージ アカウントに対して静的な Web サイト ホスティングを有効にする](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>Hello World Web アプリをデプロイする

次に、Visual Studio Code を使用して Hello World Web ページを作成し、それを Azure Storage アカウントでホストされている静的な Web サイトにデプロイします。

1. ローカル ファイル システムで *mywebsite* という名前の空のフォルダーを作成します。
1. Visual Studio Code を起動して、**[エクスプローラー]** パネルから先ほど作成したフォルダーを開きます。

    ![Visual Studio Code でフォルダーを開く](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. *mywebsite* フォルダーに既定のインデックス ファイルを作成し、*index.html* という名前を付けます。

    ![Visual Studio Code で既定のインデックス ファイルを作成する](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. エディターで *index.html* を開き、以下のテキストをファイルに貼り付けて保存します。

    ```
    <!DOCTYPE html>
    <html>
      <body>
        <h1>Hello World!</h1>
      </body>
    </html>
    ```

1. 既定のエラー ファイルを作成し、*404.html* という名前を付けます。
1. エディターで *404.html* を開き、以下のテキストをファイルに貼り付けて保存します。

    ```
    <!DOCTYPE html>
    <html>
      <body>
        <h1>404</h1>
      </body>
    </html>
    ```

1. **[エクスプローラー]** パネルの *mywebsite* フォルダーの下を右クリックし、**[Deploy to Static Website...]\(静的な Web サイトにデプロイ...\)** を選択して Web サイトをデプロイします。 Azure にログインしてサブスクリプションのリストを取得することを求めるプロンプトが表示されます。

1. 静的な Web サイト ホスティングを有効にしたストレージ アカウントを含むサブスクリプションを選択します。 次に、プロンプトが表示されたらストレージ アカウントを選択します。

これで、Visual Studio Code でファイルが Web エンドポイントにアップロードされ、成功を示すステータス バーが表示されます。 Web サイトを起動し、Azure で表示します。

チュートリアルは正常に完了し、静的な Web サイトが Azure にデプロイされました。

## <a name="feature-support"></a>機能サポート

この表は、アカウントでのこの機能のサポート状況と、特定の機能を有効にした場合のサポートへの影響を示しています。

| ストレージ アカウントの種類                | Blob Storage (既定のサポート)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| Standard 汎用 v2 | ![はい](../media/icons/yes-icon.png) |![はい](../media/icons/yes-icon.png)              | ![はい](../media/icons/yes-icon.png) |
| Premium ブロック BLOB          | ![はい](../media/icons/yes-icon.png) |![はい](../media/icons/yes-icon.png)              | ![はい](../media/icons/yes-icon.png) |

<sup>1</sup>    Data Lake Storage Gen2 とネットワーク ファイル システム (NFS) 3.0 プロトコルの両方で、階層型名前空間が有効になっているストレージ アカウントが必要です。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、静的な Web サイト ホスティングのために Azure Storage アカウントを構成する方法と、静的な Web サイトを作成して Azure エンドポイントにデプロイする方法を学習しました。

次は、静的な Web サイトでカスタム ドメインを構成する方法を学習しましょう。

> [!div class="nextstepaction"]
> [カスタム ドメインを Azure Blob Storage エンドポイントにマップする](storage-custom-domain-name.md)
