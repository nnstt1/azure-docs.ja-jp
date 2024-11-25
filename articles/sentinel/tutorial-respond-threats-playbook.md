---
title: Microsoft Sentinel でオートメーション ルールとプレイブックを使用する
description: このチュートリアルは、Microsoft Sentinel でオートメーション ルールとプレイブックを組み合わせてインシデント対応を自動化し、セキュリティ上の脅威を修復する際の参考としてご使用ください。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 8a1c290cebe566ec7d3a812de9746df8e0297120
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517750"
---
# <a name="tutorial-use-playbooks-with-automation-rules-in-microsoft-sentinel"></a>チュートリアル: Microsoft Sentinel でオートメーション ルールとプレイブックを使用する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

このチュートリアルでは、オートメーション ルールとプレイブックを組み合わせてインシデント対応を自動化し、Microsoft Sentinel によって検出されたセキュリティ上の脅威を修復する方法について説明します。 このチュートリアルを終えると、次のことができるようになります。

> [!div class="checklist"]
>
> * オートメーション ルールを作成する
> * プレイブックを作成する
> * プレイブックにアクションを追加する
> * オートメーション ルールまたは分析ルールにプレイブックをアタッチして脅威への対応を自動化する

> [!NOTE]
> このチュートリアルでは、お客様の主なタスク (インシデントをトリアージするための自動化の作成) に関する基本的なガイダンスを提供します。 詳細については、「[Microsoft Sentinel のプレイブックを使用して脅威への対応を自動化する](automate-responses-with-playbooks.md)」や「[Microsoft Sentinel のプレイブックでトリガーとアクションを使用する](playbook-triggers-actions.md)」などの **操作方法** のセクションを参照してください。
>

## <a name="what-are-automation-rules-and-playbooks"></a>オートメーション ルールとプレイブックとは

オートメーション ルールは、Microsoft Sentinel でインシデントをトリアージするのに役立ちます。 それらを使用して、インシデントを自動的に適切な担当者に割り当てたり、ノイズの多いインシデントや既知の[擬陽性](false-positives.md)を終了したり、それらの重大度を変更したり、タグを追加したりすることができます。 これらは、インシデントへの対応としてプレイブックの実行を可能にするメカニズムでもあります。

プレイブックは、アラートやインシデントに対する応答として Microsoft Sentinel から実行できる手順のコレクションです。 プレイブックは対応の自動化や調整に役立ちます。分析ルールまたはオートメーション ルールにそれぞれアタッチすることで、特定のアラートまたはインシデントが発生した際に自動的に実行されるように設定できます。 必要なときに手動で実行することもできます。

Microsoft Sentinel のプレイブックは [Azure Logic Apps](../logic-apps/logic-apps-overview.md) で構築されたワークフローをベースにしています。これは、Logic Apps のすべての機能、カスタマイズ性、組み込みテンプレートを利用できることを意味します。 それぞれのプレイブックは、それが属する特定のサブスクリプションを対象に作成されますが、 **[プレイブック]** 画面には、選択されているサブスクリプションの範囲を越えて、利用できるすべてのプレイブックが表示されます。

> [!NOTE]
> プレイブックには Azure Logic Apps が利用されているため、追加料金が適用される場合があります。 詳細については、[Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) の価格ページを参照してください。

たとえば、セキュリティ侵害を受けた可能性のあるユーザーに、ネットワークを巡回して情報を盗むことを止めさせたい場合、セキュリティ侵害を受けたユーザーを検出するルールによって生成されるインシデントに対して自動化された多面的な対応を作成できます。 まず、次のアクションを実行するプレイブックを作成します。

1. プレイブックは、オートメーション ルールによって呼び出されてインシデントを受け取ると、[ServiceNow](/connectors/service-now/) などの IT チケット システムのチケットを開きます。

1. そこから、セキュリティ アナリストがインシデントを認識できるように、[Microsoft Teams](/connectors/teams/) または [Slack](/connectors/slack/) のセキュリティ オペレーション チャンネルにメッセージが送信されます。

1. さらに、インシデントのすべての情報が、電子メール メッセージでシニア ネットワーク管理者とセキュリティ管理者に送信されます。電子メール メッセージには、 **[ブロック]** および **[無視]** のユーザー オプション ボタンが含まれます。

1. プレイブックは管理者からの応答を待機し、それを受け取ってから次の手順に進みます。

1. 管理者が **[ブロック]** を選択した場合、ユーザーを無効にするコマンドが Azure AD に、IP アドレスをブロックするコマンドがファイアウォールに送信されます。

1. 管理者が **[無視]** を選択した場合、Microsoft Sentinel のインシデントと ServiceNow のチケットがプレイブックによって終了されます。

プレイブックをトリガーするためには、これらのインシデントが生成されたときに実行されるオートメーション ルールを作成します。 そのルールでは、これらの手順が実行されます。

1. インシデントの状態を **[アクティブ]** に変更します。

1. この種のインシデントの管理を担当するアナリストにインシデントを割り当てます。

1. "compromised user (セキュリティ侵害を受けたユーザー)" タグを追加します。

1. 最後に、先ほど作成したプレイブックを呼び出します。 ([この手順には特殊なアクセス許可が必要です](automate-responses-with-playbooks.md#incident-creation-automated-response)。)

前述の例のように、アクションとしてプレイブックを呼び出すオートメーション ルールを作成することにより、インシデントへの対応としてプレイブックを自動的に実行することができます。 アラートが生成されたときに 1 つまたは複数のプレイブックを自動的に実行するよう分析ルールに指定することで、それらをアラートへの対応として自動的に実行することもできます。 

さらに、選択したアラートへの対応として、プレイブックを手動でオンデマンド実行することもできます。

Microsoft Sentinel での[オートメーション ルール](automate-incident-handling-with-automation-rules.md)と[プレイブック](automate-responses-with-playbooks.md)を使用した脅威への対応の自動化について、より完全で詳細な説明を確認してください。

> [!IMPORTANT]
>
> - プレイブックの **インシデント トリガー** の使用と **オートメーション ルール** は、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="create-a-playbook"></a>プレイブックを作成する

Microsoft Sentinel に新しいプレイブックを作成するには、これらの手順に従います。

### <a name="prepare-the-playbook-and-logic-app"></a>プレイブックとロジックアプリを準備する

1. **Microsoft Sentinel** のナビゲーション メニューから **[オートメーション]** を選択します。

1. 上部のメニューで **[作成]** と **[Add new playbook]\(新しいプレイブックの追加\)** を選択します。

    :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-playbook.png" alt-text="新しいプレイブックの追加":::

    新しいブラウザー タブが開いて、**ロジック アプリの作成** ウィザードが起動します。

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-playbook.png" alt-text="ロジック アプリの作成":::

1. **サブスクリプション** と **リソース グループ** を入力し、 **[ロジック アプリ名]** でプレイブックに名前を付けます。

1. **[リージョン]** には、ロジック アプリの情報の格納先となる Azure リージョンを選択します。

1. このプレイブックのアクティビティを診断目的で監視したい場合は、 **[Enable log analytics]\(Log Analytics の有効化\)** チェック ボックスをオンにして、自分の **Log Analytics ワークスペース** の名前を入力します。

1. プレイブックにタグを適用したい場合は、 **[次へ: タグ >]** をクリックします (オートメーション ルールによって適用されたタグには接続されません。 [タグについての詳しい情報を参照してください](../azure-resource-manager/management/tag-resources.md))。 それ以外の場合は、 **[確認と作成]** をクリックします。 入力した詳細を確認し、 **[作成]** をクリックします。

1. プレイブックが作成されてデプロイされている間 (これには数分かかります)、 **[Microsoft.EmptyWorkflow]** という画面が表示されます。 "デプロイが完了しました" というメッセージが表示されたら、 **[リソースに移動]** をクリックします。

1. 新しいプレイブックの [[Logic Apps デザイナー]](../logic-apps/logic-apps-overview.md) が表示されます。ここからワークフローの設計を開始できます。 短い紹介動画とよく使用されるいくつかの Logic Apps トリガーおよびテンプレートが画面に表示されます。 Logic Apps でプレイブックを作成する方法について[詳しい情報をご確認ください](../logic-apps/logic-apps-create-logic-apps-from-templates.md)。

1. **[空のロジック アプリ]** テンプレートを選択します。

   :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-playbook-template.png" alt-text="Logic Apps デザイナーのテンプレート ギャラリー":::

### <a name="choose-the-trigger"></a>トリガーを選択する

すべてのプレイブックは、トリガーによって開始する必要があります。 プレイブックを開始するアクションと、プレイブックが受け取ることになるスキーマは、トリガーによって定義されます。

1. 検索バーで、Microsoft Sentinel を検索します。 **[Microsoft Sentinel]** が結果に表示されたらそれを選択します。

1. 結果の **[トリガー]** タブには、Microsoft Sentinel に用意されている 2 つのトリガーが表示されます。
    - [When a response to an Microsoft Sentinel alert is triggered]\(Microsoft Azure Sentinel アラートへの対応がトリガーされたとき)\
    - [When Microsoft Sentinel incident creation rule was triggered]\(Microsoft Sentinel インシデント作成ルールがトリガーされたとき\)

   作成するプレイブックの種類に一致するトリガーを選択します。

    > [!NOTE]
    > オートメーション ルールで呼び出すことができるのは、**インシデント トリガー** に基づくプレイブックだけであることに注意してください。 **アラート トリガー** に基づくプレイブックは、[分析ルール](detect-threats-custom.md#set-automated-responses-and-create-the-rule)で直接実行されるように定義する必要があります。また、手動で実行することもできます。
    > 
    > 使用するトリガーの詳細については、[**Microsoft Sentinel プレイブックでのトリガーとアクションの使用**](playbook-triggers-actions.md)に関するページを参照してください

    :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-trigger.png" alt-text="プレイブックのトリガーを選択する":::

> [!NOTE]
> トリガーまたはそれ以降のアクションを選択すると、操作しているリソース プロバイダーに対する認証を求めるメッセージが表示されます。 今回の場合、プロバイダーは Microsoft Sentinel です。 認証は、いくつかの異なる方法を使用して行うことができます。 詳細と手順については、[**Microsoft Sentinel に対してプレイブックを認証する**](authenticate-playbooks-to-sentinel.md)方法に関する記事を参照してください。

### <a name="add-actions"></a>アクションの追加

これで、プレイブックを呼び出したときの動作を定義できます。 アクション、論理条件、ループ、switch case 条件はいずれも、 **[新しいステップ]** を選択することで追加できます。 この選択によってデザイナーに新しいフレームが開くので、そこで、操作対象となるシステムやアプリケーション、設定する条件を選択できます。 フレーム上部の検索バーにシステムまたはアプリケーションの名前を入力し、表示される結果から選択します。

これらの各ステップでフィールドをクリックすると、 **[動的なコンテンツ]** と **[式]** の 2 つのメニューを含んだパネルが表示されます。 **[動的なコンテンツ]** メニューから、プレイブックに渡されたアラートまたはインシデントの属性への参照、たとえば関係するすべてのエンティティの値や属性を追加できます。 **[式]** メニューからは、ステップに追加するロジックを豊富な関数のライブラリから選択できます。

   :::image type="content" source="./media/tutorial-respond-threats-playbook/logic-app.png" alt-text="ロジック アプリ デザイナー":::

このスクリーンショットは、このドキュメントの冒頭の例で説明したプレイブックの作成時に追加することになるアクションと条件を示しています。 唯一の違いは、ここに示したプレイブックでは、**インシデント トリガー** ではなく **アラート トリガー** を使用していることです。 つまりこのプレイブックは、オートメーション ルールからではなく、分析ルールから直接呼び出すことになります。 以下では、プレイブックを呼び出すための両方の方法について説明します。

## <a name="automate-threat-responses"></a>脅威への対応を自動化する

プレイブックを作成し、トリガーを定義して、条件を設定し、実行するアクションとそれによって生成される出力を指定しました。 今度は、実行する条件を決め、それらの条件が満たされたときに実行される自動化メカニズムを設定する必要があります。

### <a name="respond-to-incidents"></a>インシデントへの対応

プレイブックを使用して **インシデント** に対応するには、[オートメーション ルール](automate-incident-handling-with-automation-rules.md)を作成します。これはインシデントが生成されたときに実行されます。そしてそれにより、プレイブックが呼び出されます。

オートメーション ルールを作成するには:

1. Microsoft Sentinel のナビゲーション メニューにある **[オートメーション]** ブレードで、トップ メニューの **[作成]** 、 **[新しいルールの追加]** の順に選択します。

   :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-rule.png" alt-text="新しいルールを追加する":::

1. **[Create new automation rule]\(新しいオートメーション ルールの作成\)** パネルが開きます。 ルールの名前を入力します。

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-automation-rule.png" alt-text="オートメーション ルールを作成する":::

1. 特定の分析ルールでのみオートメーション ルールが有効になるようにしたい場合は、 **[If Analytics rule name]\(分析ルール名が次に該当する場合\)** の条件を変更することで、それを指定します。

1. このオートメーション ルールのアクティブ化を決定する条件が他にあれば追加します。 **[条件の追加]** をクリックし、ボックスの一覧から条件を選択します。 条件の一覧は、アラートの詳細とエンティティ識別子のフィールドによって設定されます。

1. このオートメーション ルールで実行するアクションを選択します。 **[Assign owner]\(所有者の割り当て\)** 、 **[状態の変更]** 、 **[重大度の変更]** 、 **[タグの追加]** 、 **[プレイブックの実行]** などのアクションを選択できます。 アクセスは、必要な数だけ追加できます。

1. **[プレイブックの実行]** アクションを追加した場合、ボックスの一覧から使用可能なプレイブックを選択するよう求められます。 オートメーション ルールから実行できるのは、**インシデント トリガー** で開始されるプレイブックのみであるため、一覧には、それらのみが表示されます。<a name="permissions-to-run-playbooks"></a>

    > [!IMPORTANT]
    > オートメーション ルールからプレイブックを実行するには、明示的なアクセス許可が Microsoft Sentinel に付与されている必要があります。 ドロップダウン リストでプレイブックが "淡色表示" される場合、そのプレイブックのリソース グループに対するアクセス許可が Sentinel にはありません。 **[Manage playbook permissions]\(プレイブックのアクセス許可の管理\)** リンクをクリックしてアクセス許可を割り当ててください。
    > 表示された **[アクセス許可の管理]** パネルで、実行したいプレイブックがあるリソース グループのチェック ボックスをオンにし、 **[適用]** をクリックします。
    > :::image type="content" source="./media/tutorial-respond-threats-playbook/manage-permissions.png" alt-text="アクセス許可の管理":::
    > - 自分自身には、Microsoft Sentinel にアクセス許可を付与するリソース グループの **所有者** アクセス許可と、実行したいプレイブックがあるリソース グループの **ロジック アプリの共同作成者** ロールが割り当てられている必要があります。
    > - マルチテナント デプロイで、実行したいプレイブックが別のテナントに存在する場合、そのプレイブックのテナントでプレイブックを実行するアクセス許可を Microsoft Sentinel に付与する必要があります。
    >    1. プレイブックのテナントにある Microsoft Sentinel のナビゲーション メニューから **[設定]** を選択します。
    >    1. **[設定]** ブレードで、 **[設定]** タブ、 **[Playbook permissions]\(プレイブックのアクセス許可\)** 展開コントロールの順に選択します。
    >    1. **[アクセス許可の構成]** ボタンをクリックして前述の **[アクセス許可の管理]** パネルを開き、そこでの説明に沿って続行します。
    > - **MSSP** シナリオで、サービス プロバイダー テナントへのサインイン中に作成された自動化ルールから [顧客テナントでプレイブックを実行する](automate-incident-handling-with-automation-rules.md#permissions-in-a-multi-tenant-architecture)必要がある場合、「**_両方のテナント_ *_」でプレイブックを実行するためのアクセス許可を Microsoft Sentinel に付与する必要があります。「_* 顧客**」テナントで、前の箇条書きにあるマルチテナント デプロイの手順に従います。 **サービス プロバイダー** テナントで、Azure Security Insights アプリを Azure Lighthouse オンボード テンプレートに追加する必要があります。
    >    1. Azure portal で、 **[Azure Active Directory]** に移動します。
    >    1. **[エンタープライズ アプリケーション]** をクリックします。
    >    1. **[アプリケーションの種類]** を選択し、 **[Microsoft アプリケーション]** でフィルター処理します。
    >    1. 検索ボックスに、「**Azure Security Insights**」と入力します。
    >    1. **[オブジェクト ID]** フィールドをコピー します。 この追加の承認を、既存の Azure Lighthouse の委任に追加する必要があります。
    >
    >    **Microsoft Sentinel Automation 共同作成者** ロールには、固定 GUID である `f4c81013-99ee-4d62-a7ee-b3f1f648599a` があります。 Azure Lighthouse 承認のサンプルは、パラメーター テンプレートでは次のようになります。
    >    
    >    ```json
    >    {
    >        "principalId": "<Enter the Azure Security Insights app Object ID>", 
    >        "roleDefinitionId": "f4c81013-99ee-4d62-a7ee-b3f1f648599a",
    >        "principalIdDisplayName": "Microsoft Sentinel Automation Contributors" 
    >    }
    >    ```

1. 必要に応じてオートメーション ルールの有効期限を設定します。

1. **[Order]\(順序\)** に数値を入力して、このルールが実行されるオートメーション ルールのシーケンス内の位置を指定します。

1. **[Apply]** をクリックします。 以上で終わりです。

オートメーション ルールを作成する[その他の方法をご確認ください](automate-incident-handling-with-automation-rules.md#creating-and-managing-automation-rules)。

### <a name="respond-to-alerts"></a>アラートに応答する

プレイブックを使用して **アラート** に対応するには、アラートが生成されたときに実行される **分析ルール** を作成 (または既存のものを編集) し、[分析ルール ウィザード](detect-threats-custom.md)で、自動化された対応として自分のプレイブックを選択します。

1. Microsoft Sentinel のナビゲーション メニューの **[Analytics]** ブレードから、対応を自動化したい分析ルールを選択し、詳細ペインの **[編集]** をクリックします。

1. **[Analytics rule wizard - Edit existing rule]\(分析ルール ウィザード - 既存のルールを編集する\)** ページで、 **[Automated response]\(自動化された対応\)** タブを選択します。

   :::image type="content" source="./media/tutorial-respond-threats-playbook/automated-response-tab.png" alt-text="[Automated response]\(自動化された対応\)"::: タブ

1. ドロップダウン リストからプレイブックを選択します。 複数のプレイブックを選択することもできますが、選択できるのは、**アラート トリガー** を使用するプレイブックのみです。

1. **[確認と作成]** タブで **[保存]** を選択します。

## <a name="run-a-playbook-on-demand"></a>オンデマンドでプレイブックを実行する

プレイブックをオンデマンドで実行することもできます。

 > [!NOTE]
 > オンデマンドで実行できるのは、**アラート トリガー** を使用するプレイブックだけです。

プレイブックをオンデマンドで実行するには:

1. **[インシデント]** ページで、インシデントを選択して **[View full details]\(完全な詳細を表示\)** をクリックします。

2. **[アラート]** タブで、プレイブックを実行するアラートをクリックし、右側にスクロールして **[プレイブックの表示]** をクリックし、サブスクリプションで使用可能なプレイブックの一覧から、**実行** するプレイブックを選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Microsoft Sentinel でプレイブックとオートメーション ルールを使用して脅威に対応する方法について説明しました。 
- Microsoft Sentinel を使用して[脅威を予防的に検出する方法](hunting.md)をご覧ください。
