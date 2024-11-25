---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 03/02/2018
ms.openlocfilehash: 96d6668a25422824f0e5ddb5f3fea65f7f9daabb
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131571580"
---
1. [Azure Portal](https://portal.azure.com) で、空のロジック アプリを作成します。

2. Logic Apps デザイナーで、フィルターとして「*github*」を入力します。

3. 使用する GitHub コネクタとトリガーを選択します。

   ![GitHub コネクタとトリガーの選択](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > すべてのロジック アプリ ワークフローが、トリガーによって開始する必要があります。 アクションを選択できるのは、使用しているロジック ワークフローが既にトリガーによって開始されているときだけです。 

4. 以前に接続を作成していない場合は、**[サインイン]** を選択し、メッセージが表示されたら GitHub の資格情報を入力します。

   ![GitHub の資格情報でサインイン](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   ロジック アプリはこれらの資格情報を使用して、GitHub アカウントのデータへの接続とアクセスを承認します。 

5. GitHub のユーザー名とパスワードを入力して、承認を確認します。

   ![資格情報を入力して承認を確認](./media/connectors-create-api-github/github-connector-authorize.png)   

   Azure Portal で接続が作成され、いつでもご利用いただけます。

6. ロジック アプリ ワークフローを引き続き定義する。

   ![その他のアクションをロジック アプリ ワークフローに追加する](./media/connectors-create-api-github/github-connector-logic-app.png)
