---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 7f4a2e5a7186032995ee277dfb9f2c226853163b
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2021
ms.locfileid: "112573987"
---
## <a name="create-an-image-gallery"></a>イメージ ギャラリーを作成する

イメージ ギャラリーは、イメージの共有を有効にするために使用されるプライマリ リソースです。 ギャラリー名で許可されている文字は、英字 (大文字または小文字)、数字、ドット、およびピリオドです。 ギャラリー名にダッシュを含めることはできません。  ギャラリー名は、お使いのサブスクリプション内で一意にする必要があります。 

次の例では、*myGalleryRG* リソース グループに *myGallery* という名前のギャラリーを作成します。

1. Azure Portal ( https://portal.azure.com ) にサインインします。
1. 検索ボックスで **共有イメージ ギャラリー** という種類を使用して、結果で **[共有イメージ ギャラリー]** を選択します。
1. **[共有イメージ ギャラリー]** ページで、 **[追加]** をクリックします。
1. **[Shared Image Gallery の作成]** ページで、適切なサブスクリプションを選択します。
1. **[リソース グループ]** で、 **[新規作成]** を選択し、名前として「*myGalleryRG*」を入力します。
1. **[名前]** に、ギャラリー名として「*myGallery*」と入力します。
1. **[リージョン]** は既定値のままにしておきます。
1. 「*テスト用の自分のイメージ ギャラリー*」など、ギャラリーの簡単な説明を入力し、 **[確認および作成]** をクリックすることができます。
1. 検証に合格した後、 **[作成]** を選択します。
1. デプロイが完了したら、 **[リソースに移動]** を選択します。


## <a name="create-an-image-definition"></a>イメージ定義を作成する 

イメージ定義では、イメージの論理グループを作成します。 これは、その中に作成されるイメージ バージョンに関する情報を管理するために使用されます。 

イメージ定義名は、大文字または小文字、数字、ドット、ダッシュおよびピリオドで構成できます。 イメージ定義に指定できる値の詳細については、[イメージ定義](../articles/virtual-machines/shared-image-galleries.md#image-definitions)に関するページを参照してください。

ギャラリー内でギャラリー イメージ定義を作成します。 

1. 新しいイメージ ギャラリーのページで、ページの上部から **[Add a new image definition]\(新しいイメージ定義の追加\)** を選択します。 
1. **[共有イメージ ギャラリーに新しいイメージ定義を追加]** の **[リージョン]** で *[米国東部]* を選択します。
1. **[イメージの定義名]** で、「*myImageDefinition*」などの名前を入力します。
1. **[オペレーティング システム]** では、ソース VM に基づいて適切なオプションを選択します。  
1. **[VM の生成]** では、ソース VM に基づいてオプションを選択します。 ほとんどの場合、これは *[Gen 1]* になります。 詳細については、[第 2 世代 VM に対するサポート](../articles/virtual-machines/generation-2.md)に関するページを参照してください。
1. **[オペレーティング システムの状態]** では、ソース VM に基づいて適切なオプションを選択します。 詳細については、[一般化と特殊化](../articles/virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)に関するページを参照してください。
1. **[発行元]** では、「*myPublisher*」と入力します。 
1. **[プラン]** では、「*myOffer*」と入力します。
1. **[SKU]** では、「*mySKU*」と入力します。
1. 終わったら、 **[確認と作成]** を選択します。
1. イメージ定義の検証に合格した後、 **[作成]** を選択します。
1. デプロイが完了したら、 **[リソースに移動]** を選択します。


## <a name="create-an-image-version"></a>イメージ バージョンを作成する

 レプリケーションのターゲット リージョンを選択するときに、レプリケーションのターゲットとして、"*ソース*" リージョンも含める必要があることに注意してください。

イメージのバージョンを作成する手順は、ソースが一般化されたイメージであるか、特殊化された VM のスナップショットであるかによって若干異なります。 


1. イメージ定義のページで、ページの上部から **[バージョンの追加]** を選択します。
1. **[リージョン]** でイメージを作成するリージョンを選択します。
1. **[バージョン番号]** には、「*1.0.0*」のような数値を入力します。 イメージ バージョン名では、整数を使用する *major*.*minor*.*patch* という形式に従う必要があります。 
1. **[ソース イメージ]** では、ドロップダウンからソースのマネージド イメージを選択します。 各種類のソースの具体的な詳細については、次の表を参照してください。

    | ソース | その他のフィールド |
    |---|---|
    | ディスクまたはスナップショット | - **OS ディスク** の場合、ドロップダウンからディスクまたはスナップショットを選択します。 <br> - データ ディスクを追加するには、LUN 番号を入力し、ドロップダウンからデータ ディスクを選択します。 |
    | イメージ バージョン | - ドロップダウンから **ソース ギャラリー** を選択します。 <br> - ドロップダウンから正しいイメージ定義を選択します。 <br>- ドロップダウンから、使用する既存のイメージ バージョンを選択します。 |
    | マネージド イメージ | \- ドロップダウンから **ソース イメージ** を選択します。 <br>マネージド イメージは、 **[インスタンスの詳細]** で選択したのと同じリージョンにある必要があります。
    | ストレージ アカウント内の VHD | **[参照]** を選択して、VHD のストレージ アカウントを選択します。 |

1. **[最新から除外]** は、既定値の *[いいえ]* のままにしておきます。
1. **[End of life date]\(有効期限の終了日\)** では、今後数か月の予定表から日付を選択します。
1. **[レプリケーション]** タブで、ドロップダウンからストレージの種類を選択します。
1. **[既定のレプリカ数]** を設定すると、追加するリージョンごとにこれをオーバーライドできます。 
1. ソース リージョンにレプリケートする必要があります。そのため、一覧の最初のレプリカは、イメージを作成したリージョンになります。 レプリカをさらに追加するには、ドロップダウンからリージョンを選択し、必要に応じてレプリカ数を調整します。
1. 完了したら、 **[確認および作成]** を選択します。 Azure で構成が検証されます。
1. イメージ バージョンの検証に合格したら、 **[作成]** を選択します。
1. デプロイが完了したら、 **[リソースに移動]** を選択します。

イメージをすべてのターゲット リージョンにレプリケートするにはしばらく時間がかかる場合があります。

ポータルから既存の VM をイメージとしてキャプチャすることもできます。 詳細については、[ポータルでの VM のイメージの作成](../articles/virtual-machines/capture-image-portal.md)に関するページを参照してください。

## <a name="share-the-gallery"></a>ギャラリーを共有する

イメージ ギャラリー レベルでアクセスを共有することをお勧めします。 先ほど作成したギャラリーを共有する手順を以下に示します。

1. 新しいイメージ ギャラリーのページで、左側のメニューにある **[アクセス制御 (IAM)]** を選択します。 
1. **[ロールの割り当てを追加する]** で **[追加]** を選択します。 **[ロールの割り当てを追加する]** ウィンドウが開きます。 
1. **[ロール]** で **[閲覧者]** を選択します。
1. **[アクセスの割り当て先]** で既定値の **[Azure AD のユーザー、グループ、サービス プリンシパル]** のままにします。
1. **[選択]** の下で、招待する人のメール アドレスを入力します。
1. ユーザーが組織外の場合、"**このユーザーには、Microsoft で共同作業できるようにするメールが送信されます**" というメッセージが表示されます。 メール アドレスでユーザーを選択して、 **[保存]** をクリックします。

ユーザーが組織外の場合は、組織に参加するための招待メールを受け取ります。 そのユーザーは招待に同意する必要があり、その後ギャラリーとリソースの一覧にイメージの定義とバージョンをすべて表示できるようになります。
