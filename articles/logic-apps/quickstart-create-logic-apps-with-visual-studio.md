---
title: 'クイックスタート: Visual Studio においてマルチテナント Azure Logic Apps で統合ワークフローを作成する'
description: Visual Studio Code においてマルチテナント Azure Logic Apps で自動統合ワークフローを作成する
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 05/25/2021
ms.openlocfilehash: 8aa96455eba49dd624f1ced469d34f0cf0f48757
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110372500"
---
# <a name="quickstart-create-automated-integration-workflows-with-multi-tenant-azure-logic-apps-and-visual-studio"></a>クイックスタート: マルチテナント Azure Logic Apps と Visual Studio で自動統合ワークフローを作成する

このクイックスタートでは、マルチテナント [Azure Logic Apps](../logic-apps/logic-apps-overview.md) と Visual Studio を使用して、企業と組織の間でアプリ、データ、システム、サービスを統合する自動ワークフローを設計、開発、デプロイする方法を示します。 これらのタスクは Azure portal でも実行できます。しかし、Visual Studio を使用すれば、ロジック アプリをソース管理に追加したり、さまざまなバージョンを発行したり、異なるデプロイ環境用の Azure Resource Manager テンプレートを作成したりできます。 マルチテナントとシングルテナントのモデルの比較の詳細については、「[シングルテナントとマルチテナント、および統合サービス環境](single-tenant-overview-compare.md)」を参照してください。

Azure Logic Apps が初めてであり、その基本的な概念だけを必要としている場合は、[Azure Portal でのロジック アプリの作成に関するクイック スタート](../logic-apps/quickstart-create-first-logic-app-workflow.md)をお試しください。 ロジック アプリ デザイナーは、Azure Portal と Visual Studio の両方で同じように動作します。

このクイック スタートでは、Visual Studio で Azure Portal クイック スタートと同じロジック アプリを作成します。 また、[Visual Studio Code でサンプル アプリを作成する](quickstart-create-logic-apps-visual-studio-code.md)方法と、[Azure コマンド ライン インターフェイス (Azure CLI) を使用してロジック アプリを作成および管理する](quickstart-logic-apps-azure-cli.md)方法についても学習できます。このロジック アプリは、Web サイトの RSS フィードを監視し、そのフィード内で新しい項目が見つかるたびにメールを送信します。 完成したロジック アプリは、次の高レベルのワークフローのようになります。

![完成したロジック アプリの大まかなワークフローを示すスクリーンショット。](./media/quickstart-create-logic-apps-with-visual-studio/high-level-workflow-overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 Azure Government サブスクリプションをご利用の場合、追加の手順に従って [Azure Government Cloud 用に Visual Studio を設定](#azure-government)してください。

* まだお持ちでない場合は、以下のツールをダウンロードしてインストールしてください。

  * [Visual Studio 2019、2017、または 2015 - Community Edition 以降](https://aka.ms/download-visual-studio)。 このクイック スタートでは、Visual Studio Community 2017 を使用します。

    > [!IMPORTANT]
    > Visual Studio 2019 または 2017 をインストールする場合は、 **[Azure の開発]** ワークロードを選択してください。

  * [Microsoft Azure SDK for .NET (2.9.1 以降)](https://azure.microsoft.com/downloads/)。 [Azure SDK for .NET](/dotnet/azure/intro) の詳細を参照してください。

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * 必要なバージョンの Visual Studio 拡張機能用の最新の Azure Logic Apps ツール。

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Azure Logic Apps Tools は、Visual Studio Marketplace から直接ダウンロードしてインストールできます。または、[この拡張機能を Visual Studio 内からインストールする方法](/visualstudio/ide/finding-and-using-visual-studio-extensions)を確認できます。 インストールが完了したら、必ず Visual Studio を再起動してください。

* 組み込みのロジック アプリ デザイナーを使用する際の Web へのアクセス

  デザイナーが Azure でリソースを作成し、ロジック アプリでコネクタからプロパティやデータを読み取るには、インターネット接続が必要です。

* Logic Apps でサポートされるメール アカウント (Outlook for Microsoft 365、Outlook.com、Gmail など)。 その他のプロバイダーについては、[こちらのコネクタ一覧](/connectors/)を参照してください。 この例では Office 365 Outlook を使います。 別のプロバイダーを使用する場合も、全体的な手順は同じです。ただし、UI がやや異なる場合があります。

  > [!IMPORTANT]
  > Gmail コネクタを使用する場合、ロジック アプリで制限なしにこのコネクタを使用できるのは、G-Suite ビジネス アカウントだけです。 Gmail コンシューマー アカウントを持っている場合は、Google によって承認された特定のサービスのみでこのコネクタを使用できるほか、[認証に使用する Google クライアント アプリを Gmail コネクタで作成する](/connectors/gmail/#authentication-and-bring-your-own-application)ことができます。 詳細については、「[Azure Logic Apps での Google コネクタのデータ セキュリティとプライバシー ポリシー](../connectors/connectors-google-data-security-privacy-policy.md)」を参照してください。

* ロジック アプリが特定の IP アドレスへのトラフィックを制限するファイアウォールを経由して通信する必要がある場合、そのファイアウォールは、Logic Apps サービスまたはロジック アプリが存在する Azure リージョンのランタイムが使用する [インバウンド](logic-apps-limits-and-config.md#inbound)と [アウトバウンド](logic-apps-limits-and-config.md#outbound)の IP アドレスの "*両方*" のアクセスを許可する必要があります。 また、ロジック アプリが Office 365 Outlook コネクタや SQL コネクタなどの [マネージド コネクタ](../connectors/managed.md)を使用している場合、または [カスタム コネクタ](/connectors/custom-connectors/)を使用している場合、そのファイアウォールでは、ロジック アプリの Azure リージョン内の "*すべて*" の [マネージド コネクタ アウトバウンド IP アドレス](logic-apps-limits-and-config.md#outbound)へのアクセスを許可する必要もあります。

<a name="azure-government"></a>

## <a name="set-up-visual-studio-for-azure-government"></a>Azure Government 用に Visual Studio を設定する

### <a name="visual-studio-2017"></a>Visual Studio 2017

[Azure Environment Selector Visual Studio 拡張機能](https://devblogs.microsoft.com/azuregov/introducing-the-azure-environment-selector-visual-studio-extension/)を使用できます。これは、[Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=SteveMichelotti.AzureEnvironmentSelector) からダウンロードしてインストールできます。

### <a name="visual-studio-2019"></a>Visual Studio 2019

Azure Logic Apps で Azure Government サブスクリプションを使用するには、[Azure Government クラウドの検出エンドポイントを Visual Studio に追加](../azure-government/documentation-government-connect-vs.md)する必要があります。 ただし、"*Azure Government アカウントで Visual Studio にサインインする前に*"、検出エンドポイントの追加後に生成される JSON ファイルの名前を次の手順で変更する必要があります。

1. Visual Studio を閉じます。

1. 次の場所に `Azure U.S. Government-A3EC617673C6C70CC6B9472656832A26.Configuration` という名前で生成される JSON ファイルを見つけます。

   `%localappdata%\.IdentityService\AadConfigurations`
 
1. JSON ファイルの名前を `AadProvider.Configuration.json` に変更します。

1. Visual Studio を再起動します。

1. 引き続き Azure Government アカウントでサインインする手順に進みます。

この設定を元に戻すには、次の場所にある JSON ファイルを削除して、Visual Studio を再起動してください。

`%localappdata%\.IdentityService\AadConfigurations\AadProvider.Configuration.json`

<a name="create-resource-group-project"></a>

## <a name="create-azure-resource-group-project"></a>Azure リソース グループ プロジェクトを作成する

最初に、[Azure リソース グループ プロジェクト](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)を作成します。 Azure リソース グループとリソースについて詳しくは、[こちら](../azure-resource-manager/management/overview.md)を参照してください。

1. Visual Studio を起動します。 Azure のアカウントを使用してサインインします。

1. **[ファイル]** メニューで、 **[新規作成]**  >  **[プロジェクト]** の順に選択します (Ctrl + Shift + N)

   ![[ファイル] メニューで [新規作成]、[プロジェクト] の順に選択する](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. **[インストール済み]** で、 **[Visual C#]** または **[Visual Basic]** を選択します。 **[クラウド]**  >  **[Azure リソース グループ]** の順に選択します。 プロジェクトに名前を付けます。例:

   ![Azure リソース グループ プロジェクトを作成する](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > リソース グループ名には、文字、数字、ピリオド (`.`)、アンダースコア (`_`)、ハイフン (`-`)、およびかっこ (`(`、`)`) のみを含めることができます。ただし、リソース グループ名をピリオド (`.`) で "*終了する*" ことはできません。
   >
   > **[クラウド]** または **[Azure リソース グループ]** が表示されない場合は、Azure SDK for Visual Studio がインストールされていることを確認してください。

   Visual Studio 2019 を使用している場合は、以下の手順に従ってください。

   1. **[新しいプロジェクトの作成]** ボックスで、Visual C# または Visual Basic の **[Azure リソース グループ]** プロジェクトを選択します。 **[次へ]** を選択します。

   1. 使用する Azure リソース グループの名前やその他のプロジェクト情報を指定します。 **［作成］** を選択します

1. テンプレートの一覧で、 **[ロジック アプリ]** テンプレートを選択します。 **[OK]** を選択します。

   ![ロジック アプリ テンプレートを選ぶ](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   プロジェクトが作成された後、ソリューション エクスプローラーが開かれ、ソリューションが表示されます。 ソリューションで、**LogicApp.json** ファイルはロジック アプリの定義を格納するだけでなく、デプロイのために使用できる Azure Resource Manager テンプレートにもなっています。

   ![ソリューション エクスプローラーに新しいロジック アプリ ソリューションとデプロイ ファイルが表示される](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>空のロジック アプリを作成する

Azure リソース グループ プロジェクトが作成されたら、 **[空のロジック アプリ]** テンプレートを使用してロジック アプリを作成します。

1. ソリューション エクスプローラーで、**LogicApp.json** ファイルのショートカット メニューを開きます。 **[Open With Logic App Designer]\(ロジック アプリ デザイナーで開く\)** を選択します (Ctrl + L)

   ![ロジック アプリ デザイナーでロジック アプリの .json ファイルを開く](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > このコマンドが Visual Studio 2019 にない場合は、Visual Studio の最新の更新プログラムが適用されていることを確認してください。

   Visual Studio では、ロジック アプリや接続のリソースを作成してデプロイするために Azure サブスクリプションと Azure リソース グループを指定するよう求められます。

1. **[サブスクリプション]** で、Azure サブスクリプションを選択します。 **[リソース グループ]** で、 **[新規作成]** を選択して別の Azure リソース グループを作成します。

   ![Azure サブスクリプション、リソース グループ、リソースの場所を選択する](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | 設定 | 値の例 | 説明 |
   | ------- | ------------- | ----------- |
   | ユーザー アカウント | Fabrikam <br> sophia-owen@fabrikam.com | Visual Studio にサインインしたときに使用したアカウント |
   | **サブスクリプション** | 従量課金制 <br> (sophia-owen@fabrikam.com) | Azure サブスクリプションの名前および関連付けられたアカウント |
   | **リソース グループ** | MyLogicApp-RG <br> (米国西部) | ロジック アプリのリソースを格納およびデプロイするための Azure リソース グループと場所 |
   | **場所** | **[Same as Resource Group]\(リソース グループと同じ\)** | ロジック アプリをデプロイする場所の種類と特定の場所。 場所の種類は、Azure リージョンまたは既存の[統合サービス環境 (ISE)](connect-virtual-network-vnet-isolated-environment.md) です。 <p>このクイックスタートでは、場所の種類を **[リージョン]** に設定し、場所を **[Same as Resource Group]\(リソース グループと同じ\)** に設定します。 <p>**注**:リソース グループ プロジェクトを作成した後で、[場所の種類と場所を変更する](manage-logic-apps-with-visual-studio.md#change-location)ことができますが、異なる場所の種類はロジック アプリにさまざまな影響を与えます。 |
   ||||

1. Logic Apps デザイナーが開き、紹介ビデオやよく使用されるトリガーが含まれたページが表示されます。 ビデオやトリガーの後の **[テンプレート]** まで下へスクロールし、 **[空のロジック アプリ]** を選択します。

   ![[空のロジック アプリ] を選択する](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>ロジック アプリ ワークフローを構築する

次に、新しいフィード項目が現れると起動される RSS [トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)を追加します。 ロジック アプリはすべて、特定の条件が満たされると起動されるトリガーで開始されます。 トリガーが起動されるたびに、ワークフローを実行するロジック アプリ インスタンスが Logic Apps エンジンによって作成されます。

1. ロジック アプリ デザイナーの検索ボックスの下の **[すべて]** を選択します。 検索ボックスに「rss」と入力します。 トリガーの一覧から、**フィード項目が発行される場合**

   ![トリガーとアクションを追加してロジック アプリを構築する](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. デザイナーにトリガーが表示されたら、[Azure Portal クイック スタート](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger)にあるワークフロー手順に従ってロジック アプリの構築を完了してから、この記事に戻ります。 完了すると、ロジック アプリは次の例のようになります。

   ![完成したロジック アプリ](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app-workflow.png)

1. Visual Studio ソリューションを保存します。 (Ctrl + S キー)。

<a name="deploy-to-Azure"></a>

## <a name="deploy-logic-app-to-azure"></a>ロジック アプリを Azure にデプロイする

ロジック アプリを実行してテストする前に、Visual Studio からそのアプリを Azure にデプロイします。

1. ソリューション エクスプローラーのプロジェクトのショートカット メニューで、 **[デプロイ]**  >  **[新規作成]** の順に選択します。 メッセージに従って Azure アカウントでサインインします。

   ![ロジック アプリ デプロイを作成する](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. このデプロイでは、既定の Azure サブスクリプション、リソース グループ、およびその他の設定を保持します。 **[デプロイ]** を選択します。

   ![ロジック アプリを Azure リソース グループにデプロイする](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. **[パラメーターの編集]** ボックスが表示された場合は、ロジック アプリのリソース名を指定します。 設定を保存します。

   ![ロジック アプリのデプロイ名を入力する](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   デプロイが開始されると、Visual Studio の **[出力]** ウィンドウにアプリのデプロイ状態が表示されます。 状態が表示されない場合、 **[Show output from]\(出力元の表示\)** の一覧を開いて、Azure リソース グループを選択します。

   ![デプロイの状態の出力](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   選択したコネクタにユーザーからの入力が必要な場合は、バックグラウンドで PowerShell ウィンドウが開き、必要なパスワードまたはシークレット キーの入力を求められます。 その情報を入力すると、デプロイが続行されます。

   ![PowerShell ウィンドウ](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   デプロイが完了すると、ロジック アプリは Azure Portal で有効になり、指定されたスケジュールで (1 分ごとに) 実行されます。 新しいフィード項目が検出されるとトリガーが起動され、それにより、ロジック アプリのアクションを実行するワークフロー インスタンスが作成されます。 ロジック アプリは、新しい項目ごとに電子メールを送信します。 新しい項目が検出されない場合、トリガーは起動されず、ワークフローのインスタンス化を "スキップ" します。 ロジック アプリは、次の間隔まで待ってからチェックします。

   このロジック アプリが送信するサンプルの電子メールを次に示します。 電子メールが届かない場合は、迷惑メール フォルダーを確認してください。

   ![Outlook が新しい RSS 項目ごとにメールを送信する](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

これで、Visual Studio でロジック アプリが正常に構築およびデプロイされました。 ロジック アプリを管理して実行履歴を確認するには、「[Manage logic apps with Visual Studio (Visual Studio でロジック アプリを管理する)](../logic-apps/manage-logic-apps-with-visual-studio.md)」を参照してください。

## <a name="add-new-logic-app"></a>新しいロジック アプリを追加する

既存の Azure リソース グループ プロジェクトがある場合は、[JSON アウトライン] ウィンドウを使用してそのプロジェクトに新しい空のロジック アプリを追加できます。

1. ソリューション エクスプローラーで、`<logic-app-name>.json` ファイルを開きます。

1. **[表示]** メニューの **[その他のウィンドウ]**  >  **[JSON アウトライン]** を選択します。

1. テンプレート ファイルにリソースを追加するには、[JSON アウトライン] ウィンドウの上部にある **[リソースの追加]** を選択します。 または、[JSON アウトライン] ウィンドウで、 **[リソース]** ショートカット メニューを開き、 **[新しいリソースの追加]** を選びます。

   ![[JSON アウトライン] ウィンドウ](./media/quickstart-create-logic-apps-with-visual-studio/json-outline-window-add-resource.png)

1. **[リソースの追加]** ダイアログ ボックスの検索ボックスで、`logic app` を検索し、 **[ロジック アプリ]** を選びます。 ロジック アプリの名前を指定し、 **[追加]** を選びます。

   ![リソースの追加](./media/quickstart-create-logic-apps-with-visual-studio/add-logic-app-resource.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ロジック アプリの使用を完了したら、ロジック アプリと関連リソースが含まれているリソース グループを削除します。

1. ロジック アプリの作成に使用したのと同じアカウントで、[Azure Portal](https://portal.azure.com) にサインインします。

1. Azure portal メニューで **[リソース グループ]** を選択するか、または任意のページから **[リソース グループ]** を検索して選択します。 ロジック アプリのリソース グループを選択します。

1. **[概要]** ページで、 **[リソース グループの削除]** を選択します。 確認のためにリソース グループ名を入力し、 **[削除]** を選択します。

   ![[リソース グループ] > [概要] > [リソース グループの削除]](./media/quickstart-create-logic-apps-with-visual-studio/clean-up-resources.png)

1. ローカル コンピューターで Visual Studio ソリューションを削除します。

## <a name="next-steps"></a>次のステップ

この記事では、Visual Studio を使用してロジック アプリの構築、デプロイ、実行を行いました。 Visual Studio でロジック アプリの高度なデプロイを管理および実行する方法の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Visual Studio でロジック アプリを管理する](../logic-apps/manage-logic-apps-with-visual-studio.md)
