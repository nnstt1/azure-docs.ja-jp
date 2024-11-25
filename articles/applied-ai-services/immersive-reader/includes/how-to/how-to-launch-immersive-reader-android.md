---
author: nitinme
manager: nitinme
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: nitinme
ms.openlocfilehash: e080a0b7cc05b776e3085715e20d21bfb2545dd7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253869"
---
## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* Azure Active Directory 認証用に構成されたイマーシブ リーダー リソース。 設定するには、[これらの手順](../../how-to-create-immersive-reader.md)に従ってください。  環境のプロパティを構成するときに、ここで作成した値の一部が必要になります。 後で参照するために、実際のセッションの出力をテキスト ファイルに保存します。
* [Git](https://git-scm.com/).
* [イマーシブ リーダー SDK](https://github.com/microsoft/immersive-reader-sdk)。
* [Android Studio](https://developer.android.com/studio)。

## <a name="configure-authentication-credentials"></a>認証資格情報の構成

1. Android Studio を起動して、**immersive-reader-sdk/js/samples/quickstart-java-android** ディレクトリ (Java) または **immersive-reader-sdk/js/samples/quickstart-kotlin** ディレクトリ (Kotlin) からプロジェクトを開きます。

1. **/assets** フォルダー内に **env** という名前のファイルを作成します。 次の名前と値を追加し、適切な値を指定します。 このファイルには公開してはならないシークレットが含まれているため、ソース管理にコミットしないでください。
    
    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET=<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

## <a name="start-the-immersive-reader-with-sample-content"></a>イマーシブ リーダーを起動してサンプル コンテンツを表示する

AVD マネージャーからデバイス エミュレーターを選択して、プロジェクトを実行します。

## <a name="next-steps"></a>次のステップ

* [イマーシブ リーダー SDK](https://github.com/microsoft/immersive-reader-sdk) と[イマーシブ リーダー SDK リファレンス](../../reference.md)を確認します。
* [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/) でコード サンプルを見る。