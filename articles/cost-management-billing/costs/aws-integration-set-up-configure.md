---
title: Azure Cost Management と AWS の統合セットアップをする
description: この記事では、Cost Management で AWS のコストと使用状況レポートの統合を設定して構成する方法を説明します。
author: bandersmsft
ms.author: banders
ms.date: 10/07/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: matrive
ms.openlocfilehash: 6c8c03f93e811e622daa93515740e1cc25e65434
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706154"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>AWS のコストと使用状況レポートの統合を設定して構成する

Amazon Web サービス (AWS) のコストと使用状況レポート (CUR) の統合では、Cost Management で AWS 支出を監視して制御します。 統合により、Azure portal の単一の場所で、Azure と AWS の両方での支出を監視して制御します。 この記事では、統合を設定し、Cost Management 機能を使用して、コストを分析し、予算を確認できるように構成する方法を説明します。

Cost Management では、レポート定義を取得し、レポートの GZIP CSV ファイルをダウンロードするために AWS アクセス資格情報を使用して、S3 バケットに格納されている AWS のコストと使用状況レポートを処理します。

AWS レポートの統合を設定する方法の詳細については、[Cost Management での AWS のコネクターの設定方法](https://www.youtube.com/watch?v=Jg5KC1cx5cA)に関する動画をご覧ください。 他の動画を視聴するには、[Cost Management の YouTube チャンネル](https://www.youtube.com/c/AzureCostManagement)にアクセスしてください。

>[!VIDEO https://www.youtube.com/embed/Jg5KC1cx5cA]

## <a name="create-a-cost-and-usage-report-in-aws"></a>AWS でコストと使用状況レポートを作成する

コストと使用状況レポートの使用は、AWS のコストを収集して処理するための AWS で推奨される方法です。 Cost Management クロス クラウド コネクタでは、管理 (統合) アカウント レベルで構成されたコストと使用状況レポートがサポートされています。 詳細については、「[AWS Cost and Usage Report](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html)」 (AWS のコストと使用状況レポート) というドキュメントを参照してください。

AWS の Billing and Cost Management コンソールの **[Cost & Usage Reports]\(コストと使用状況レポート\)** ページを使用して、以下の手順でコストと使用状況レポートを作成します。

1. AWS Management コンソールにサインインし、[[Billing and Cost Management]\(課金とコスト管理\) コンソール](https://console.aws.amazon.com/billing)を開きます。
2. ナビゲーション ウィンドウで、で、 **[Cost & Usage Reports]\(コストと使用状況レポート\)** を選択します。
3. **[レポートの作成]** を選択します。
4. **[レポート名]** には、レポートの名前を入力します。
5. **[Additional report details]** の下で **[Include resource IDs]** を選択します。
6. **[Data refresh settings]\(データ更新設定\)** では、お客様の請求確定後に AWS が返金、クレジット、またはサポート料金をお客様のアカウントに適用した場合に、AWS のコストと使用状況レポートを更新するかどうかを選択します。 レポートが更新されると、新しいレポートが Amazon S3 にアップロードされます。 この設定は選択されたままにしておくことをお勧めします。
7. **[次へ]** を選択します。
8. **[S3 bucket]\(S3 バケット\)** には、 **[構成]** を選択します。
9. [Configure S3 Bucket]\(S3 バケットの構成\) ダイアログ ボックスで、バケット名と新しいバケットを作成するリージョンとを入力して **[次へ]** を選択します。
10. **[I have confirmed that this policy is correct]** を選択してから、 **[Save]** をクリックします。
11. (省略可能) [Report path prefix]\(レポート パス プレフィックス\) では、レポートの名前の先頭に追加するレポート パス プレフィックスを入力します。
プレフィックスを指定しない場合、既定のプレフィックスはレポートに指定した名前になります。 日付範囲は `/report-name/date-range/` の形式です。
12. **[時間単位]** では、 **[毎時間]** を選択します。
13. **[Report versioning]** では、レポートの各バージョンで前のバージョンを上書きするか、新しいレポートを追加するかを選択します。
14. **[Enable data integration for]\(データ統合の有効化対象\)** は、選択の必要はありません。
15. **[圧縮]** では、 **[GZIP]** を選択します。
16. **[次へ]** を選択します。
17. レポートの設定を確認した後、 **[Review and Complete]\(確認して完了\)** を選択します。

    レポート名をメモしておきます。 これは後の手順で使用します。

AWS で Amazon S3 バケットへのレポートの配信が開始されるまで、最大で 24 時間かかる場合があります。 配信が開始された後、AWS では、少なくとも 1 日 1 回は AWS のコストと使用状況レポート ファイルが更新されます。 配信の開始を待たずに、AWS 環境の構成を続行することができます。

> [!NOTE]
> メンバー (リンク済み) アカウント レベルで構成されたコストと使用状況レポートは、現在サポートされていません。

## <a name="create-a-role-and-policy-in-aws"></a>AWS でロールとポリシーを作成する

Cost Management では、1 日数回、コストと使用状況レポートが配置されている S3 バケットにアクセスします。 このサービスでは、新しいデータを確認するために資格情報へのアクセスが必要です。 Cost Management によるアクセスを許可するには、AWS でロールとポリシーを作成します。

Cost Management で AWS アカウントへのロールベースのアクセスを有効にするために、AWS コンソールでロールが作成されます。 AWS コンソールからの _ロール ARN_ と _外部 ID_ が必要です。 後で、Cost Management の **[AWS コネクタの作成]** ページでこれらを使用します。

[新しいロールの作成] ウィザードを使用します。

1. AWS コンソールにサインインし、 **[サービス]** を選択します。
2. サービスの一覧で **[IAM]** を選択します。
3. **[ロール]** を選択してから **[ロールの作成]** を選択します。
4. 次のページで、 **[Another AWS account]\(別の AWS アカウント\)** を選択します。
5. **[アカウント ID]** に、「**432263259397**」と入力します。
6. **[オプション]** で、 **[Require external ID (Best practice when a third party will assume this role)]\(外部 ID が必要 (サード パーティでこのロールを想定する場合のベスト プラクティス)\)** を選択します。
7. **[外部 ID]** に、外部 ID を入力します。これは、AWS ロールと Cost Management 間の共有パスコードです。 Cost Management の **[新しいコネクタ]** ページでも同じ外部 ID が使用されます。 外部 ID を入力する場合には、強力なパスコードポリシーを使用するようお勧めします。
    > [!NOTE]
    > **[MFA の要求]** の選択は変更しないでください。 クリアされたままにしておく必要があります。
8. **Permissions\(次へ: アクセス許可\)** をクリックします。
9. **[ポリシーの作成]** を選択します。 新しいブラウザーのタブが開きます。 そこでポリシーを作成します。
10. **[サービスの選択]** を選択します。

コストと使用状況レポートのアクセス許可を構成します。

1. 「**Cost and Usage Report**」と入力します。
2. **[アクセス レベル]**  >  **[読み取り]**  > **DescribeReportDefinitions** の順に選択します。 この手順により、Cost Management では、定義されている CUR レポートを読み取り、レポート定義の前提条件が一致するかどうかを判断できます。
3. **[Add additional permissions]\(さらにアクセス許可を追加\)** を選択します。

S3 バケットとオブジェクトのアクセス許可を構成します。

1. **[サービスの選択]** を選択します。
2. 「**S3**」と入力します。
3. **[アクセス レベル]**  >  **[List]\(一覧表示\)**  > **ListBucket** の順に選択します。 このアクションでは、S3 バケット内のオブジェクトの一覧を取得します。
4. **[アクセス レベル]**  >  **[読み取り]**  > **GetObject** の順に選択します。 このアクションでは、課金ファイルのダウンロードを許可します。
5. **[リソース]** を選択します。
6. **[bucket – Add ARN]\(バケット – ARN の追加\)** を選択します。
7. **[バケット名]** に、CUR ファイルを格納するために使用されるバケットを入力します。
8. **[object – Add ARN]\(オブジェクト - ARN の追加\)** を選択します。
9. **[バケット名]** に、CUR ファイルを格納するために使用されるバケットを入力します。
10. **[オブジェクト名]** で、 **[任意]** を選択します。
11. **[Add additional permissions]\(さらにアクセス許可を追加\)** を選択します。

Cost エクスプローラーのアクセス許可を構成します。

1. **[サービスの選択]** を選択します。
2. 「**Cost エクスプローラー サービス**」と入力します。
3. **[All Cost Explorer Service actions (ce:\*)]\(すべての Cost エクスプローラー サービス アクション (ce:*)\)** を選択します。 このアクションでは、コレクションが正しいことを検証します。
4. **[Add additional permissions]\(さらにアクセス許可を追加\)** を選択します。

AWS 組織へのアクセス許可を追加します。

1. **[組織]** を入力します。
2. **[アクセス レベル]**  >  **[List]\(一覧表示\)**  > **ListAccounts** の順に選択します。 このアクションでは、アカウントの名前を取得します。
3. **[ポリシーの確認]** に、新しいポリシーの名前を入力します。 正しい情報を入力したことを確認してから、 **[ポリシーの作成]** を選択します。
4. 前のタブに戻り、ブラウザーの Web ページを更新します。 検索バーで、新しいポリシーを検索します。
5. **確認\)** をクリックします。
6. 新しいロールの名前を入力します。 正しい情報を入力したことを確認してから、 **[ロールの作成]** を選択します。

    ロールの作成時に前の手順で使用されたロール ARN と外部 ID をメモしておいてください。 これらは後で Cost Management コネクタを設定するときに使用します。

ポリシー JSON は次の例のようになるはずです。 _bucketname_ を S3 バケットの名前に置き換えます。

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
             "ce:*",
             "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-connector-for-aws-in-azure"></a>Azure で新しい AWS コネクタを設定する

AWS コネクタを作成し、AWS コストの監視を開始するには、次の情報を使用します。

### <a name="prerequisites"></a>前提条件

- 少なくとも 1 つの管理グループが有効になっていることを確認します。 サブスクリプションを AWS サービスにリンクするには、管理グループが必要です。 管理グループの作成方法の詳細については、[Azure での管理グループの作成](../../governance/management-groups/create-management-group-portal.md)に関する記事をご覧ください。 
- 自分がサブスクリプションの管理者であることを確認します。
- 「[AWS でコストと使用状況レポートを作成する](#create-a-cost-and-usage-report-in-aws)」セクションの説明に従って、新しい AWS コネクタに必要な設定を完了します。


### <a name="create-a-new-connector"></a>新しいコネクタを作成する

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[コストの管理と請求]** に移動し、必要に応じて課金スコープを選択します。
1. **[コスト分析]** を選択し、 **[設定]** を選択します。 
1. **[AWS のコネクタ]** を選択します。
1. **[コネクタの追加]** を選択します。
1. **[コネクタの作成]** ページで、 **[表示名]** にコネクタの名前を入力します。  
    :::image type="content" source="./media/aws-integration-setup-configure/create-aws-connector01.png" alt-text="AWS コネクタの作成ページの例" :::
1. 必要に応じて、既定の管理グループを選択します。 検出されたすべてのリンクされたアカウントが格納されます。 これは後で設定できます。
1. 継続処理を希望する場合は、 **[課金]** セクションで **[自動更新]** を **[オン]** にします。 自動オプションを選択した場合は、課金サブスクリプションを選択する必要があります。
1. **[ロール ARN]** に、AWS でのロールの設定時に使用した値を入力します。
1. **[外部 ID]** に、AWS でのロールの設定時に使用した値を入力します。
1. **[レポート名]** に、AWS で作成した名前を入力します。
1. **[次へ]** 、 **[作成]** の順に選択します。

新しい AWS スコープ、AWS 統合アカウントと AWS のリンクされたアカウント、およびそれらのコスト データが表示されるまで、数時間かかる場合があります。

コネクタを作成した後、アクセス制御をそれに割り当てることをお勧めします。 ユーザーには、新しく検出されたスコープへのアクセス許可が割り当てられます。AWS 統合アカウントと AWS のリンクされたアカウントです。 コネクタを作成するユーザーは、コネクタ、統合アカウント、およびすべてのリンクされたアカウントの所有者です。

検出後にユーザーにコネクタのアクセス許可を割り当てても、既存の AWS スコープにアクセス許可は割り当てられません。 代わりに、新しいリンクされたアカウントにのみ、アクセス許可が割り当てられます。

## <a name="take-additional-steps"></a>追加の手順を実行する

- まだ設定していない場合は、[管理グループを設定](../../governance/management-groups/overview.md#initial-setup-of-management-groups)します。
- スコープ ピッカーに新しいスコープが追加されたことを確認します。 **[更新]** を選択して最新のデータを表示します。
- **[クラウド コネクタ]** ページで、コネクタを選択し、 **[請求先アカウントに移動する]** を選択して、管理グループにリンクされたアカウントを割り当てます。

> [!NOTE]
> 管理グループは、Microsoft 顧客契約 (MCA) のお客様に対して現在サポートされていません。 MCA のお客様は、コネクタを作成し、ご自身の AWS データを表示できます。 ただし、MCA のお客様は、管理グループの下に Azure のコストと AWS コストをまとめて表示することはできません。

## <a name="manage-aws-connectors"></a>AWS コネクタを管理する

**[AWS のコネクタ]** ページでコネクタを選択した場合は、次の操作を行うことができます。

- **[請求先アカウントに移動する]** を選択して、AWS 統合アカウントの情報を表示します。
- **[アクセスの制御]** を選択して、コネクタのロール割り当てを管理します。
- **[編集]** を選択して、コネクタを更新します。 AWS アカウント番号はロール ARN に表示されるため、変更できません。 ただし、新しいコネクタを作成することはできます。
- **[確認]** を選択して、確認テストを再実行し、Cost Management でコネクタ設定を使用して、データを収集できることを確認します。

:::image type="content" source="./media/aws-integration-setup-configure/aws-connector-details.png" alt-text="AWS コネクタ情報の例" :::

## <a name="set-up-azure-management-groups"></a>Azure 管理グループを設定する

クラウド間のプロバイダーの情報を表示する単一の場所を作成するには、Azure サブスクリプションと AWS のリンクされたアカウントを同じ管理グループに配置します。 Azure 環境を管理グループでまだ構成していない場合は、「[管理グループの初期セットアップ](../../governance/management-groups/overview.md#initial-setup-of-management-groups)」を参照してください。

コストを分ける場合は、AWS のリンクされたアカウントのみを保持する管理グループを作成できます。

## <a name="set-up-an-aws-consolidated-account"></a>AWS 統合アカウントを設定する

AWS 統合アカウントは、複数の AWS アカウントの請求と支払いを結合します。 AWS のリンクされたアカウントとしても機能します。 AWS 統合アカウントの詳細は、AWS コネクタ ページ上のリンクを使用して表示できます。 

:::image type="content" source="./media/aws-integration-setup-configure/aws-consolidated-account01.png" alt-text="AWS 統合アカウントの詳細の例" :::

ページからは、次のことを行うことができます。

- **[更新]** を選択して、AWS のリンクされたアカウントと管理グループの関連付けを一括更新します。
- **[アクセスの制御]** を選択して、スコープのロール割り当てを設定します。

### <a name="permissions-for-an-aws-consolidated-account"></a>AWS 統合アカウントのアクセス許可

既定では、AWS 統合アカウントのアクセス許可は、AWS コネクタのアクセス許可に基づいて、アカウントの作成時に設定されます。 コネクタの作成者は所有者です。

AWS 統合アカウントの **[アクセス レベル]** ページを使用して、アクセス レベルを管理します。 ただし、AWS のリンクされたアカウントは、AWS 統合アカウントへのアクセス許可を継承しません。

## <a name="set-up-an-aws-linked-account"></a>AWS のリンクされたアカウントを設定する

AWS のリンクされたアカウントは、AWS リソースが作成され、管理される場所です。 リンクされたアカウントは、セキュリティ境界としても機能します。

このページからは、次のことを行うことができます。

- **[更新]** を選択して、AWS のリンクされたアカウントと管理グループの関連付けを更新します。
- **[アクセスの制御]** を選択して、スコープのロール割り当てを設定します。

:::image type="content" source="./media/aws-integration-setup-configure/aws-linked-account01.png" alt-text="AWS のリンクされたアカウント ページの例" :::

### <a name="permissions-for-an-aws-linked-account"></a>AWS のリンクされたアカウントのアクセス許可

既定では、AWS のリンクされたアカウントのアクセス許可は、AWS コネクタのアクセス許可に基づいて、作成時に設定されます。 コネクタの作成者は所有者です。 AWS のリンクされたアカウントの **[アクセス レベル]** ページを使用して、アクセス レベルを管理します。 AWS のリンクされたアカウントは、AWS 統合アカウントからアクセス許可を継承しません。

AWS のリンクされたアカウントは常に、それらが所属する管理グループからアクセス許可を継承します。

## <a name="next-steps"></a>次のステップ

- これで、AWS のコストと使用状況レポートの統合を設定して構成したので、[AWS のコストと使用状況の管理](aws-integration-manage.md)に進みます。
- コスト分析に慣れていない場合は、[コスト分析を使用するコストの調査と分析](quick-acm-cost-analysis.md)に関するクイックスタートを参照してください。
- Azure の予算に慣れていない場合は、[Azure の予算の作成と管理](tutorial-acm-create-budgets.md)に関する記事を参照してください。
