---
title: チュートリアル:Gatsby サイトを Azure Static Web Apps に発行する
description: このチュートリアルでは、Gatsby アプリケーションを Azure Static Web Apps にデプロイする方法について説明します。
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/10/2021
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: 4c6a68b8db40aa07c251cabab28217143105aab1
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2021
ms.locfileid: "109814516"
---
# <a name="tutorial-publish-a-gatsby-site-to-azure-static-web-apps"></a>チュートリアル:Gatsby サイトを Azure Static Web Apps に発行する

この記事では、[Gatsby](https://gatsbyjs.org) Web アプリケーションを作成して [Azure Static Web Apps](overview.md) にデプロイする方法を説明します。 最終結果として、アプリの構築と発行の方法を制御できる新しい Static Web Apps サイト (および関連する GitHub Actions) が得られます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> - Gatsby アプリの作成
> - Azure Static Web Apps サイトのセットアップ
> - Gatsby アプリの Azure へのデプロイ

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 アカウントがない場合は、[無料でアカウントを作成する](https://azure.microsoft.com/free/)ことができます。
- GitHub アカウント。 アカウントがない場合は、[無料でアカウントを作成する](https://github.com/join)ことができます。
- [Node.js](https://nodejs.org) がインストールされていること。

## <a name="create-a-gatsby-app"></a>Gatsby アプリの作成

Gatsby コマンド ライン インターフェイス (CLI) を使用して Gatsby アプリを作成します。

1. ターミナルを開きます。
1. Gatsby CLI を使用して新しいアプリを作成するには、[npx](https://www.npmjs.com/package/npx) ツールを使用します。 これには数分かかることがあります。

   ```bash
   npx gatsby new static-web-app
   ```

1. 新しく作成されたアプリに移動します。

   ```bash
   cd static-web-app
   ```

1. Git リポジトリを初期化します

   ```bash
   git init
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>アプリケーションの GitHub へのプッシュ

新しい Azure Static Web Apps リソースを作成するには、GitHub のリポジトリが必要です。

1. [https://github.com/new](https://github.com/new) で **gatsby-static-web-app** という名前で空の GitHub リポジトリを作成します (README は作成しないでください)。

1. 次に、先ほど作成した GitHub リポジトリをリモートとしてローカル リポジトリに追加します。 次のコマンドの `<YOUR_USER_NAME>` プレースホルダーの代わりに、GitHub のユーザー名を追加してください。

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/gatsby-static-web-app
   ```

1. ローカル リポジトリを GitHub にプッシュします。

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>Web アプリのデプロイ

次の手順では、新しい静的サイト アプリを作成し、運用環境にデプロイする方法について説明します。

### <a name="create-the-application"></a>アプリケーションを作成する

1. [Azure Portal](https://portal.azure.com) に移動します
1. **[リソースの作成]** を選択します
1. **[Static Web Apps]** を探します
1. **[Static Web Apps]** を選択します。
1. **[作成]**
1. _[基本]_ タブで、次の値を入力します。

    | プロパティ | 値 |
    | --- | --- |
    | _サブスクリプション_ | Azure サブスクリプション名。 |
    | _リソース グループ_ | **my-gatsby-group**  |
    | _名前_ | **my-gatsby-app** |
    | _[プランの種類]_ | **Free** |
    | _Azure Functions API のリージョンとステージング環境_ | 最も近いリージョンを選択します。 |
    | _ソース_ | **GitHub** |

1. **[GitHub でサインイン]** を選択し、GitHub で認証します。

1. 次の GitHub 値を入力します。

    | プロパティ | 値 |
    | --- | --- |
    | _組織_ | ご自分の希望する GitHub 組織を選択します。 |
    | _リポジトリ_ | **gatsby-static-web-app** を選択します。 |
    | _ブランチ_ | **[main]\(メイン\)** を選択します。 |

1. _[Build Details]\(ビルドの詳細\)_ セクションで、 _[Build Presets]\(ビルドのプリセット\)_ ドロップダウンから **[Gatsby]** を選択し、既定値をそのままにします。

### <a name="review-and-create"></a>[Review and create] (確認および作成)

1. **[確認および作成]** ボタンを選択して、詳細がすべて正しいことを確認します。

1. **[作成]** を選択して、App Service Static Web App の作成を開始し、デプロイのための GitHub アクションをプロビジョニングします。

1. デプロイが完了したら、 **[リソースに移動]** をクリックします。

1. リソース画面で、 _[URL]_ リンクをクリックして、デプロイしたアプリケーションを開きます。 GitHub アクションが完了するまで 1 - 2 分かかることがあります。

   :::image type="content" source="./media/publish-gatsby/deployed-app.png" alt-text="デプロイされたアプリケーション":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [カスタム ドメインの追加](custom-domain.md)
