---
title: Azure Purview 用 Amazon S3 Multi-Cloud Scanning Connector
description: この攻略ガイドでは、Azure Purview 内で Amazon S3 バケットをスキャンする方法の詳細について説明します。
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.custom: references_regions
ms.openlocfilehash: 86f0296ced4846dce7ec4be0d5b503d343d060bc
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848118"
---
# <a name="amazon-s3-multi-cloud-scanning-connector-for-azure-purview"></a>Azure Purview 用 Amazon S3 Multi-Cloud Scanning Connector

Azure Purview 用 Multi-Cloud Scanning Connector を使用すると、Azure ストレージ サービスに加えてアマゾン ウェブ サービスなど、クラウド プロバイダー全体の組織データを探索できます。

この記事では、Azure Purview を使用して、Amazon S3 標準バケットに現在格納されている非構造化データをスキャンし、データ内に存在する機密情報の種類を検出する方法について説明します。 また、この攻略ガイドでは、情報保護とデータ コンプライアンスを容易にするためにデータが現在格納されている Amazon S3 バケットを識別する方法についても説明します。

このサービスでは、Purview を使用して AWS に安全にアクセスできる Microsoft アカウントを提供し、そこで Azure Purview 用 Multi-Cloud Scanning Connector が実行されるようにします。 Amazon S3 バケットへのこのアクセスは、Azure Purview 用 Multi-Cloud Scanning Connector によりデータを読み取るために使用され、メタデータと分類のみを含むスキャン結果が Azure に報告されます。 Purview の分類およびラベル付けレポートを使用して、データ スキャンの結果を分析して確認します。

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| はい | はい | はい | はい | はい | いいえ | 制限あり** |

\** データセットが [Data Factory Copy アクティビティ](how-to-link-azure-data-factory.md)でソース/シンクとして使用される場合、系列はサポートされています 

> [!IMPORTANT]
> Azure Purview 用 Multi-Cloud Scanning Connector は、Azure Purview への個別のアドオンです。 Azure Purview 用 Multi-Cloud Scanning Connector のご契約条件は、お客様が締結した Microsoft Azure サービスの契約に含まれています。 詳細については、 https://azure.microsoft.com/support/legal/ の「Microsoft Azure の法的情報」を参照してください。
>

## <a name="purview-scope-for-amazon-s3"></a>Purview の Amazon S3 のスコープ

AWS ソースに対応するインジェスト プライベート エンドポイントは、現時点ではサポートされていません。

Purview の制限の詳細については、以下を参照してください。

- [Azure Purview を使用する、リソースのクォータの管理と引き上げ](how-to-manage-quotas.md)
- [Azure Purview でサポートされているデータ ソースとファイルの種類](sources-and-scans.md)

### <a name="storage-and-scanning-regions"></a>ストレージとスキャンのリージョン

Amazon S3 サービス用 Purview コネクタは現在、特定のリージョンにのみデプロイされています。 次の表は、データが格納されているリージョンと Azure Purview によってスキャンされるリージョンのマップを示しています。

> [!IMPORTANT]
> お使いのバケットのリージョンに応じて、関連するすべてのデータ転送料金が請求されます。
>

| ストレージ リージョン | スキャン リージョン |
| ------------------------------- | ------------------------------------- |
| 米国東部 (オハイオ)                  | 米国東部 (オハイオ)                        |
| 米国東部 ( バージニア北部)           | 米国東部 ( バージニア北部)                       |
| 米国西部 (北 カリフォルニア)         | 米国西部 (北 カリフォルニア)                        |
| 米国西部 (オレゴン)                | 米国西部 (オレゴン)                      |
| アフリカ (ケープタウン)              | ヨーロッパ (フランクフルト)                    |
| アジア太平洋 (香港特別行政区)        | アジア太平洋 (東京)                |
| アジア太平洋 (ムンバイ)           | アジア太平洋 (シンガポール)                |
| アジア太平洋 (大阪ローカル)      | アジア太平洋 (東京)                 |
| アジア太平洋 (ソウル)            | アジア太平洋 (東京)                 |
| アジア太平洋 (シンガポール)        | アジア太平洋 (シンガポール)                 |
| アジア太平洋 (シドニー)           | アジア太平洋 (シドニー)                  |
| アジア太平洋 (東京)            | アジア太平洋 (東京)                |
| カナダ (中部)                | 米国東部 (オハイオ)                        |
| 中国 (北京)                 | サポートされていません                    |
| 中国 (Ningxia)                 | サポートされていません                   |
| ヨーロッパ (フランクフルト)              | ヨーロッパ (フランクフルト)                    |
| ヨーロッパ (アイルランド)                | ヨーロッパ (アイルランド)                   |
| ヨーロッパ (ロンドン)                 | ヨーロッパ (ロンドン)                 |
| ヨーロッパ (ミラノ)                  | ヨーロッパ (パリ)                    |
| ヨーロッパ (パリ)                  | ヨーロッパ (パリ)                   |
| ヨーロッパ (ストックホルム)              | ヨーロッパ (フランクフルト)                    |
| 中東 (バーレーン)           | ヨーロッパ (フランクフルト)                    |
| 南米 (サンパウロ)       | 米国東部 (オハイオ)                        |
| | |

## <a name="prerequisites"></a>前提条件

Amazon S3 バケットを Purview データ ソースとして追加し、S3 データをスキャンする前に、次の前提条件を確実に実行します。

> [!div class="checklist"]
> * Azure Purview データ ソース管理者である必要があります。
> * まだ持っていない場合は、[Purview アカウントを作成](#create-a-purview-account)します
> * [Purview で使用するための新しい AWS ロールを作成する](#create-a-new-aws-role-for-purview)
> * [AWS バケット スキャン用の Purview 資格情報を作成する](#create-a-purview-credential-for-your-aws-s3-scan)
> * 該当する場合は、[暗号化されている Amazon S3 バケットのスキャンを構成する](#configure-scanning-for-encrypted-amazon-s3-buckets)
> * バケットを Purview リソースとして追加するとき、[AWS ARN](#retrieve-your-new-role-arn)、[バケット名](#retrieve-your-amazon-s3-bucket-name)、および場合によっては [AWS アカウント ID](#locate-your-aws-account-id) の値が必要になります。

### <a name="create-a-purview-account"></a>Purview アカウントを作成する

- **Purview アカウントを既にお持ちの場合は、** AWS S3 サポートに必要な構成に進むことができます。 まず、[AWS バケット スキャン用の Purview 資格情報を作成](#create-a-purview-credential-for-your-aws-s3-scan)します。

- **Purview アカウントを作成する必要がある場合は、** 「[Azure Purview アカウント インスタンスの作成](create-catalog-portal.md)」の手順に従います。 アカウントを作成したら、ここに戻り、構成を完了して Amazon S3 用 Purview コネクタの使用を開始します。

### <a name="create-a-new-aws-role-for-purview"></a>Purview 用の新しい AWS ロールを作成する

この手順では、Azure アカウント ID と外部 ID の値を特定し、AWS ロールを作成してから、Purview 内でロール ARN の値を入力する方法について説明します。

**Microsoft アカウント ID と外部 ID を検索するには**:

1. Purview で、 **[Management Center]\(管理センター\)**  >  **[セキュリティとアクセス]**  >  **[資格情報]** に移動します。

1. **[新規]** を選択して新しい資格情報を作成します。

    右側に表示される **[新しい資格情報]** ペインの **[認証方法]** ドロップダウンで **[ロール ARN]** を選択します。 
    
    AWS の関連フィールドに貼り付けるための **Microsoft アカウント ID** と **外部 ID** の値をコピーして、別のファイルに表示するか、手元に用意しておきます。 次に例を示します。

    [ ![Microsoft アカウント ID と外部 ID の値を検索します。](./media/register-scan-amazon-s3/locate-account-id-external-id.png) ](./media/register-scan-amazon-s3/locate-account-id-external-id.png#lightbox)


**Purview 用の AWS ロールを作成するには**:

1.  **アマゾン ウェブ サービス** コンソールを開き、 **[セキュリティ、アイデンティティ、コンプライアンス]** で **[IAM]** を選択します。

1. **[ロール]** を選択してから **[ロールの作成]** を選択します。

1. **[Another AWS accoun]\(別の AWS アカウント\)** を選択し、次の値を入力します。

    |フィールド  |説明  |
    |---------|---------|
    |**アカウント ID**     |    Microsoft アカウント ID を入力します。 例: `181328463391`     |
    |**外部 ID**     |   [オプション] の **[Require external ID...]\(外部 ID が必要...\)** を選択し、指定フィールドに外部 ID を入力します。 <br>例: `e7e2b8a3-0a9f-414f-a065-afaf4ac6d994`     |
    | | |

    次に例を示します。

    ![Microsoft アカウント ID を AWS アカウントに追加します。](./media/register-scan-amazon-s3/aws-create-role-amazon-s3.png)

1. **[ロールの作成] > [Attach permissions policies]\(アクセス許可ポリシーのアタッチ\)** 領域で、 **[S3]** に表示されるアクセス権限をフィルター処理します。 **AmazonS3ReadOnlyAccess** を選択してから、 **[次へ: タグ]** を選択します。

    ![新しい Amazon S3 スキャン ロールに ReadOnlyAccess ポリシーを選択します。](./media/register-scan-amazon-s3/aws-permission-role-amazon-s3.png)

    > [!IMPORTANT]
    > **AmazonS3ReadOnlyAccess** ポリシーは、S3 バケットのスキャンに必要な最小限のアクセス許可を提供します。また、他のアクセス許可を含めることもできます。
    >
    >バケットのスキャンに必要な最小限のアクセス許可のみを適用するには、新しいポリシーを作成し、1 つのバケットをスキャンするか、アカウント内のすべてのバケットをスキャンするかに応じて、「[AWS ポリシー用の最小限のアクセス許可](#minimum-permissions-for-your-aws-policy)」の一覧にあるアクセス許可を指定します。 
    >
    >**AmazonS3ReadOnlyAccess** の代わりに、新しいポリシーをロールに適用します。

1. **[タグの追加 (オプション)]** 領域で、必要に応じて、この新しいロールのわかりやすいタグを作成できます。 有用なタグを使用して、作成する各ロールのアクセスを整理、追跡、制御できます。

    必要に応じて、タグの新しいキーと値を入力します。 完了した場合またはこの手順をスキップする場合は、 **[次へ: 確認]** を選択してロールの詳細を確認し、ロールの作成を完了します。

    ![新しいロールのアクセスを整理、追跡、または制御できるようにわかりやすいタグを追加します。](./media/register-scan-amazon-s3/add-tag-new-role.png)

1. **[確認]** 領域で、以下を実行します。

    - **[Role name]\(ロール名\)** フィールドで、ロールのわかりやすい名前を入力します
    - **[Role description]\(ロールの説明\)** ボックスに、ロールの目的を示す説明を入力します (省略可能)
    - **[ポリシー]** セクションで、ポリシー (**AmazonS3ReadOnlyAccess**) が正しくロールにアタッチされていることを確認します。

    **[ロールの作成]** を選択してプロセスを完了します。

    次に例を示します。

    ![ロールを作成する前に詳細を確認します。](./media/register-scan-amazon-s3/review-role.png)


### <a name="create-a-purview-credential-for-your-aws-s3-scan"></a>AWS S3 スキャン用の Purview 資格情報を作成する

この手順では、AWS バケットをスキャンするときに使用する新しい Purview 資格情報を作成する方法について説明します。

> [!TIP]
> 「[Purview 用の新しい AWS ロールを作成する](#create-a-new-aws-role-for-purview)」から直接続行した場合は、Purview 内で **[新しい資格情報]** ペインが既に開いている可能性があります。
>
> 新しい資格情報は、[スキャン構成](#create-a-scan-for-one-or-more-amazon-s3-buckets)時のプロセスの途中で作成することもできます。 その場合は、 **[資格情報]** フィールドで **[新規]** を選択します。
>

1. Purview で **[Management Center]\(管理センター\)** に移動し、 **[セキュリティとアクセス]** で **[資格情報]** を選択します。

1. **[新規]** を選択し、右側に表示される **[新しい資格情報]** ペインで、次のフィールドを使用して Purview 資格情報を作成します。

    |フィールド |説明  |
    |---------|---------|
    |**名前**     |この資格情報のわかりやすい名前を入力します。        |
    |**説明**     |この資格情報の説明を「`Used to scan the tutorial S3 buckets`」のように入力します (省略可能)         |
    |**認証方法**     |**[ロール ARN]** を選択します。これは、ロール ARN を使用してバケットにアクセスするためです。         |
    |**ロール ARN**     | [Amazon IAM ロールを作成](#create-a-new-aws-role-for-purview)したら、AWS IAM 領域のロールに移動し、 **[ロール ARN]** の値をコピーして、ここに入力します。 (例: `arn:aws:iam::181328463391:role/S3Role`)。 <br><br>詳細については、「[新しいロール ARN を取得する](#retrieve-your-new-role-arn)」を参照してください。 |
    | | |
    
    **[Microsoft アカウント ID]** と **[外部 ID]** の値は、[AWS で [ロール ARN] を作成する](#create-a-new-aws-role-for-purview)ときに使用されます。

1. 完了したら **[作成]** を選択し、資格情報の作成を終了します。

Purview の資格情報の詳細については、「[Azure Purview のソース認証用の資格情報](manage-credentials.md)」を参照してください。


### <a name="configure-scanning-for-encrypted-amazon-s3-buckets"></a>暗号化されている Amazon S3 バケットのスキャンを構成する

AWS バケットは、複数の暗号化の種類をサポートしています。 **AWS-KMS** 暗号化を使用するバケットでは、スキャンを有効にするために特別な構成が必要です。

> [!NOTE]
> 暗号化を使用しないか、AES-256 または AWS-KMS S3 暗号化を使用するバケットの場合は、このセクションをスキップし、「[Amazon S3 バケット名を取得する](#retrieve-your-amazon-s3-bucket-name)」に進みます。
>

**Amazon S3 バケットで使用されている暗号化の種類を確認するには、次のようにします。**

1. AWS で、 **[ストレージ]**  >  **[S3]** > に移動し、左側のメニューから **[バケット]** を選択します。

    ![[Amazon S3 バケット] タブを選択します。](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. チェックするバケットを選択します。 バケットの詳細ページで、 **[プロパティ]** タブを選択し、 **[Default encryption]\(デフォルトの暗号化\)** 領域まで下にスクロールします。

    - 選択したバケットが、**AWS-KMS** 以外の暗号化を使用するように構成されている場合 (バケットのデフォルトの暗号化が **無効** になっている場合など) は、この手順の残りの部分をスキップし、「[Amazon S3 バケット名を取得する](#retrieve-your-amazon-s3-bucket-name)」に進みます。

    - 選択したバケットが **AWS-KMS** 暗号化を使用するように構成されている場合は、次の手順に従って、カスタムの **AWS-KMS** 暗号化を使用するバケットをスキャンできるように新しいポリシーを追加します。

    次に例を示します。

    ![AWS-KMS 暗号化を使用するように構成されている Amazon S3 バケットを表示する](./media/register-scan-amazon-s3/default-encryption-buckets.png)

**新しいポリシーを追加してカスタムの AWS-KMS 暗号化を使用するバケットをスキャンできるようにするには、次のようにします。**

1. AWS で、 **[サービス]**  >   **[IAM]**  >   **[ポリシー]** に移動して **[ポリシーの作成]** を選択します。

1. **[ポリシーの作成]**  >  **[Visual editor]\(ビジュアル エディタ\)** タブで、次の値を使用してポリシーを定義します。

    |フィールド  |説明  |
    |---------|---------|
    |**サービス**     |  このフィールドに入り、 **[KMS]** を選択します。       |
    |**アクション**     | **[Access level]\(アクセス レベル\)** で、 **[Write]\(書き込み\)** を選択して **[Write]\(書き込み\)** セクションを展開します。<br>展開したら、 **[Decrypt]\(暗号化の解除\)** オプションのみを選択します。        |
    |**リソース**     |特定のリソースまたは **[すべてのリソース]** を選択します。         |
    | | |

    完了したら、 **[Review policy]\(ポリシーの確認\)** を選択して続行します。

    ![AWS-KMS 暗号化を使用するバケットをスキャンするためのポリシーを作成します。](./media/register-scan-amazon-s3/create-policy-kms.png)

1. **[Review policy]\(ポリシーの確認\)** ページで、ポリシーのわかりやすい名前を入力し、必要に応じて説明を入力して、 **[ポリシーの作成]** を選択します。

    新しく作成されたポリシーが、ポリシーの一覧に追加されます。

1. スキャンできるように、追加したロールに新しいポリシーをアタッチします。

    1. **[IAM]**  >  **[ロール]** ページに戻り、[前](#create-a-new-aws-role-for-purview)の手順で追加したロールを選択します。

    1. **[Permissions]\(アクセス許可\)** タブで、 **[Attach policies]\(ポリシーのアタッチ\)** を選択します。

        ![ご自分のロールの [Permissions]\(アクセス許可\) タブで、[Attach policies]\(ポリシーのアタッチ\) を選択します。](./media/register-scan-amazon-s3/iam-attach-policies.png)

    1. **[Attach Permissions]\(アクセス許可のアタッチ\)** ページで、先ほど作成した新しいポリシーを検索して選択します。 ポリシーをロールにアタッチするには、 **[Attach policy]\(ポリシーのアタッチ\)** を選択します。

        **[Summary]\(概要\)** ページが更新され、新しいポリシーがロールにアタッチされます。

        ![新しいポリシーがロールにアタッチされた、更新された概要ページを表示します。](./media/register-scan-amazon-s3/attach-policy-role.png)

### <a name="retrieve-your-new-role-arn"></a>新しいロール ARN を取得する

[Amazon S3 バケットのスキャンを作成する](#create-a-scan-for-one-or-more-amazon-s3-buckets)場合は、AWS ロール ARN を記録して Purview にコピーする必要があります。

**ロール ARN を取得するには、次のようにします。**

1. AWS の **Identity and Access Management (IAM)**  >  **[ロール]** 領域で、[Purview 用に作成](#create-a-purview-credential-for-your-aws-s3-scan)した新しいロールを検索して選択します。

1. ロールの **[Summary]\(概要\)** ページで、 **[ロール ARN]** の値の右側にある **[クリップボードにコピー]** ボタンを選択します。

    ![ロール ARN の値をクリップボードにコピーします。](./media/register-scan-amazon-s3/aws-copy-role-purview.png)

Purview では、AWS S3 の資格情報を編集し、取得したロールを **[ロール ARN]** フィールドに貼り付けることができます。 詳細については、「[1 つ以上の Amazon S3 バケットのスキャンを作成する](#create-a-scan-for-one-or-more-amazon-s3-buckets)」を参照してください。

### <a name="retrieve-your-amazon-s3-bucket-name"></a>Amazon S3 バケット名を取得する

[Amazon S3 バケットのスキャンを作成する](#create-a-scan-for-one-or-more-amazon-s3-buckets)ときに、Amazon S3 バケットの名前を Purview にコピーする必要があります

**バケット名を取得するには、次のようにします。**

1. AWS で、 **[ストレージ]**  >  **[S3]** > に移動し、左側のメニューから **[バケット]** を選択します。

    ![[Amazon S3 バケット] タブを表示します。](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. バケットを検索して選択し、バケットの詳細ページを表示してから、バケット名をクリップボードにコピーします。

    次に例を示します。

    ![S3 バケット URL を取得してコピーします。](./media/register-scan-amazon-s3/retrieve-bucket-url-amazon.png)

    バケット名をセキュリティで保護されたファイルに貼り付け、その名前に `s3://` プレフィックスを追加して、バケットを Purview リソースとして構成するときに入力する必要がある値を作成します。

    例: `s3://purview-tutorial-bucket`

> [!TIP]
> Purview データ ソースとして、バケットのルート レベルのみがサポートされています。 たとえば、URL `s3://purview-tutorial-bucket/view-data` はサブフォルダーが含まれるため、サポートされて "*いません*"
>
> ただし、特定の S3 バケットのスキャンを構成する場合は、スキャン用に 1 つ以上の特定のフォルダーを選択できます。 詳細については、[スキャンの範囲を指定する](#create-a-scan-for-one-or-more-amazon-s3-buckets)手順を参照してください。
>

### <a name="locate-your-aws-account-id"></a>AWS アカウント ID を検索する

AWS アカウントを Purview データ ソースとして登録するには、そのすべてのバケットと共に、AWS アカウント ID が必要です。

AWS アカウント ID とは、AWS コンソールにログインするために使用する ID です。 IAM ダッシュボードにログインすると、左側のナビゲーション オプションの下、上部のサインイン URL 内の数値として表示されるので、確認することができます。

次に例を示します。

![AWS アカウント ID を取得します。](./media/register-scan-amazon-s3/aws-locate-account-id.png)


## <a name="add-a-single-amazon-s3-bucket-as-a-purview-resource"></a>単一の Amazon S3 バケットを Purview リソースとして追加する

この手順は、データ ソースとして Purview に登録する S3 バケットが 1 つのみの場合、または AWS アカウントに複数のバケットがあるが、Purview にそのすべてを登録するのではない場合に使用します。

**バケットを追加するには**:

1. Azure Purview の **[Data Map]** ページに移動し、 **[登録]** ![[登録] アイコン](./media/register-scan-amazon-s3/register-button.png) を選択します。 >  **[Amazon S3]**  >  **[続行]** を選択します。

    ![Amazon AWS バケットを Purview データ ソースとして追加します。](./media/register-scan-amazon-s3/add-s3-datasource-to-purview.png)

    > [!TIP]
    > 複数の [コレクション](manage-data-sources.md#manage-collections)があり、Amazon S3 を特定のコレクションに追加する場合は、右上にある **[マップ ビュー]** を選択し、 **[登録]** ![[登録] アイコン](./media/register-scan-amazon-s3/register-button.png) ボタンをコレクション内で選択します。
    >

1. **[Register sources (Amazon S3)]\(ソースの登録 (Amazon S3)\)** パネルが開くので、次の情報を入力します。

    |フィールド  |説明  |
    |---------|---------|
    |**名前**     |わかりやすい名前を入力するか、提供されているデフォルトを使用します。         |
    |**[Bucket URL]\(バケット URL\)**     | 構文 `s3://<bucketName>` を使用して、AWS バケット URL を入力します     <br><br>**注**: バケットのルート レベルのみを使用してください。 詳細については、「[Amazon S3 バケット名を取得する](#retrieve-your-amazon-s3-bucket-name)」を参照してください。 |
    |**コレクションを選択する** |コレクション内からデータ ソースを登録することを選択した場合、そのコレクションは既にリストに含まれています。 <br><br>必要に応じて別のコレクションを選択します。コレクションを割り当てない場合は **[なし]** 、新しいコレクションを作成する場合は **[新規]** を選択します。 <br><br>詳細については、「[Azure Purview でデータ ソースを管理する](manage-data-sources.md#manage-collections)」を参照してください。|
    | | |

    完了したら、 **[完了]** を選択して登録を完了します。

「[1 つ以上の Amazon S3 バケットのスキャンを作成する](#create-a-scan-for-one-or-more-amazon-s3-buckets)」に進みます。

## <a name="add-an-aws-account-as-a-purview-resource"></a>AWS アカウントを Purview リソースとして追加する

Amazon アカウントに複数の S3 バケットがあり、それらすべてを Purview データ ソースとして登録する場合は、この手順を使用します。

それらすべてを一度にスキャンしないようにする場合は、[スキャンを構成する](#create-a-scan-for-one-or-more-amazon-s3-buckets)ときに、スキャン対象の特定のバケットを選択できます。

**Amazon アカウントを追加するには**:

1. Azure Purview の **[Data Map]** ページに移動し、 **[登録]** ![[登録] アイコン](./media/register-scan-amazon-s3/register-button.png) を選択します。 >  **[Amazon accounts]\(Amazon アカウント\)**  >  **[続行]** を選択します。

    ![Amazon アカウントを Purview データ ソースとして追加します。](./media/register-scan-amazon-s3/add-s3-account-to-purview.png)

    > [!TIP]
    > 複数の [コレクション](manage-data-sources.md#manage-collections)があり、Amazon S3 を特定のコレクションに追加する場合は、右上にある **[マップ ビュー]** を選択し、 **[登録]** ![[登録] アイコン](./media/register-scan-amazon-s3/register-button.png) ボタンをコレクション内で選択します。
    >

1. **[Register sources (Amazon S3)]\(ソースの登録 (Amazon S3)\)** パネルが開くので、次の情報を入力します。

    |フィールド  |説明  |
    |---------|---------|
    |**名前**     |わかりやすい名前を入力するか、提供されているデフォルトを使用します。         |
    |**[AWS account ID]\(AWS アカウント ID\)**     | AWS アカウント ID を入力します。 詳細については、「[AWS アカウント ID を検索する](#locate-your-aws-account-id)」を参照してください|
    |**コレクションを選択する** |コレクション内からデータ ソースを登録することを選択した場合、そのコレクションは既にリストに含まれています。 <br><br>必要に応じて別のコレクションを選択します。コレクションを割り当てない場合は **[なし]** 、新しいコレクションを作成する場合は **[新規]** を選択します。 <br><br>詳細については、「[Azure Purview でデータ ソースを管理する](manage-data-sources.md#manage-collections)」を参照してください。|
    | | |

    完了したら、 **[完了]** を選択して登録を完了します。

「[1 つ以上の Amazon S3 バケットのスキャンを作成する](#create-a-scan-for-one-or-more-amazon-s3-buckets)」に進みます。

## <a name="create-a-scan-for-one-or-more-amazon-s3-buckets"></a>1 つ以上の Amazon S3 バケットのスキャンを作成する

バケットを Purview データ ソースとして追加したら、スケジュールされた間隔で、または直ちに実行するようにスキャンを構成できます。

1. [Purview Studio](https://web.purview.azure.com/resource/) の左側のペインで **[Data Map]** タブを選択してから、次のいずれかの操作を行います。

    - **[マップ ビュー]** で、 **[新しいスキャン]** ![[新しいスキャン] アイコン](./media/register-scan-amazon-s3/new-scan-button.png) をデータ ソース ボックス内で選択します。
    - **[リスト ビュー]** で、データ ソースの行をポイントし、 **[新しいスキャン]** ![[新しいスキャン] アイコン](./media/register-scan-amazon-s3/new-scan-button.png) を選択します。

1. **[スキャン...]** ペインが右側に表示されるので、次のフィールドを定義し、 **[続行]** を選択します。

    |フィールド  |説明  |
    |---------|---------|
    |**名前**     |  スキャンのわかりやすい名前を入力するか、デフォルトを使用します。       |
    |**Type** |AWS アカウントを追加した場合にのみ表示され、すべてのバケットが含まれます。 <br><br>現在のオプションには、 **[すべて]**  >  **[Amazon S3]** のみが含まれます。 Purview のサポート マトリックスの拡大に応じて、より多くのオプションを選択できるようになります。 |
    |**資格情報**     |  ご自分のロール ARN の Purview 資格情報を選択します。 <br><br>**ヒント**: このとき、新しい資格情報を作成する場合は、 **[新規]** を選択します。 詳細については、「[AWS バケット スキャン用の Purview 資格情報を作成する](#create-a-purview-credential-for-your-aws-s3-scan)」を参照してください。     |
    | **Amazon S3**    |   AWS アカウントを追加した場合にのみ表示され、すべてのバケットが含まれます。 <br><br>スキャンするバケットを 1 つ以上選択するか、 **[すべて選択]** してアカウント内のすべてのバケットをスキャンします。      |
    | | |

    Purview により、ロール ARN が有効であること、バケットとバケット内のオブジェクトがアクセス可能であることが自動的にチェックされ、接続が成功した場合は続行します。

    > [!TIP]
    > 続行する前に自分で別の値を入力して接続をテストするには、右下にある **[テスト接続]** を選択してから **[続行]** を選択します。
    >

1. <a name="scope-your-scan"></a> **[Scope your scan]\(スキャンの範囲を指定する\)** ペインで、スキャンに含める特定のバケットまたはフォルダーを選択します。

    AWS アカウント全体のスキャンを作成する場合は、スキャンする特定のバケットを選択できます。 特定の AWS S3 バケットのスキャンを作成する場合は、スキャンする特定のフォルダーを選択できます。

1. **[Select a scan rule set]\(スキャン ルール セットの選択\)** ペインで、デフォルトの **AmazonS3** ルール セットを選択するか、 **[New scan rule set]\(新しいスキャン ルール セット\)** を選択してカスタムの新しいルール セットを作成します。 ルール セットを選択したら、 **[続行]** を選択します。

    カスタムの新しいスキャン ルール セットの作成を選択した場合は、ウィザードを使用して次の設定を定義します。

    |ペイン  |説明  |
    |---------|---------|
    |**[New scan rule set]\(新しいスキャン ルール セット\)**  /<br>**[Scan rule description]\(スキャン ルールの説明\)**    |   ルール セットのわかりやすい名前と説明 (省略可能) を入力します      |
    |**[ファイルの種類の選択]**     | スキャンに含めるすべてのファイルの種類を選択し、 **[続行]** を選択します。<br><br>新しいファイルの種類を追加するには、 **[新しいファイルの種類]** を選択し、以下を定義します。 <br>- 追加するファイル拡張子 <br>- 任意の説明  <br>- ファイル コンテンツにカスタムの区切り記号を含めるかどうか、またはシステム ファイルの種類。 次に、カスタムの区切り記号を入力するか、システム ファイルの種類を選択します。 <br><br>**[作成]** を選択して、カスタムのファイルの種類を作成します。     |
    |**[Select classification rules]\(分類ルールの選択\)**     |   データセットで実行する分類ルールに移動して選択します。      |
    |     |         |

    ルール セットの作成が完了したら、 **[作成]** を選択します。

1. **[Set a scan trigger]\(スキャン トリガーの設定\)** ペインで、次のいずれかを選択し、 **[続行]** を選択します。

    - **[Recurring]\(定期的\)** を選択して、定期的なスキャンのスケジュールを構成する
    - **[Once]\(1 回のみ\)** を選択して、直ちに開始するようにスキャンを構成する

1. **[Review your scan]\(スキャンの確認\)** ペインで、スキャンの詳細をチェックして正しいことを確認し、前のペインで **[Once]\(1 回のみ\)** を選択した場合は **[保存]** または **[保存および実行]** を選択します。

    > [!NOTE]
    > スキャンが開始されると、完了するまでに最大 24 時間かかることがあります。 各スキャンを開始してから 24 時間後に **分析情報レポート** を確認して、カタログを検索することができるようになりきます。
    >

詳細については、「[Purview スキャン結果を探索する](#explore-purview-scanning-results)」を参照してください。

## <a name="explore-purview-scanning-results"></a>Purview スキャン結果を探索する

Amazon S3 バケットで Purview スキャンが完了したら、Purview の **[Data Map]** 領域をドリルダウンしてスキャンの履歴を表示します。

データ ソースを選択して詳細を表示し、 **[スキャン]** タブを選択して、現在実行中または完了したスキャンを表示します。
追加した AWS アカウントにバケットが複数ある場合は、アカウントの下に各バケットのスキャン履歴が表示されます。

次に例を示します。

![AWS アカウント ソースの下に AWS S3 バケット スキャンを表示します。](./media/register-scan-amazon-s3/account-scan-history.png)

Purview の他の領域を使用して、Amazon S3 バケットなど、データ資産のコンテンツに関する詳細を確認します。

- **Purview データ カタログを検索** し、特定のバケットをフィルター処理します。 次に例を示します。

    ![カタログで AWS S3 資産を検索します。](./media/register-scan-amazon-s3/search-catalog-screen-aws.png)

- **分析情報レポートを表示** して、コンテンツについての分類、秘密度ラベル、ファイルの種類、および詳細情報を表示します。

    すべての Purview 分析情報レポートには、Amazon S3 のスキャン結果と、Azure データ ソースから得られたその他の結果が含まれます。 該当する場合、追加の **Amazon S3** 資産の種類がレポート フィルター オプションに追加されています。

    詳細については、「[Azure Purview の分析情報についての理解](concept-insights.md)」を参照してください。

## <a name="minimum-permissions-for-your-aws-policy"></a>AWS ポリシー用の最小限のアクセス許可

S3 バケットをスキャンするときに使用する、[Purview 用の AWS ロールを作成する](#create-a-new-aws-role-for-purview)ための既定の手順では、**AmazonS3ReadOnlyAccess** ポリシーを使用します。

**AmazonS3ReadOnlyAccess** ポリシーは、S3 バケットのスキャンに必要な最小限のアクセス許可を提供します。また、他のアクセス許可を含めることもできます。

バケットのスキャンに必要な最小限のアクセス許可のみを適用するには、新しいポリシーを作成し、1 つのバケットをスキャンするか、アカウント内のすべてのバケットをスキャンするかに応じて、移行のセクションの一覧にあるアクセス許可を指定します。

**AmazonS3ReadOnlyAccess** の代わりに、新しいポリシーをロールに適用します。

### <a name="individual-buckets"></a>個々のバケット

個々の S3 バケットをスキャンする場合、最小限の AWS アクセス許可は次のとおりです。

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListBucket`

必ず特定のバケット名を使用してリソースを定義してください。 次に例を示します。

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": "arn:aws:s3:::<bucketname>"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3::: <bucketname>/*"
        }
    ]
}
```

### <a name="all-buckets-in-your-account"></a>アカウント内のすべてのバケット

AWS アカウント内のすべてのバケットをスキャンする場合、最小限の AWS アクセス許可は次のとおりです。

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListAllMyBuckets`
- `ListBucket`.

必ずワイルドカードを使用してリソースを定義してください。 次に例を示します。

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListAllMyBuckets",
                "s3:ListBucket"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "*"
        }
    ]
}
```

## <a name="next-steps"></a>次のステップ

Azure Purview の分析情報レポートの詳細について学習します。

> [!div class="nextstepaction"]
> [Azure Purview の分析情報についての理解](concept-insights.md)
