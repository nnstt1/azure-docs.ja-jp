---
title: イベントベースのトリガーの作成
titleSuffix: Azure Data Factory & Azure Synapse
description: イベントに応答してパイプラインを実行するトリガーを Azure Data Factory または Azure Synapse Analytics で作成する方法について説明します。
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: da1348f0e31fbe0c3e2528692d40675d799e43da
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469694"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-storage-event"></a>ストレージ イベントに応答してパイプラインを実行するトリガーを作成する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Data Factory または Synapse のパイプラインで作成できるストレージ イベント トリガーについて説明します。

イベントドリブン アーキテクチャ (EDA) は、イベントの運用、検出、使用、および応答を含む一般的なデータ統合パターンです。 データ統合シナリオでは多くの場合、Azure Blob Storage アカウントでのファイルの到着や削除など、ストレージ アカウントで発生するイベントに基づいて、パイプラインをトリガーする必要があります。 Data Factory および Synapse のパイプラインは [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) とネイティブに統合されているので、そのようなイベントでパイプラインをトリガーできます。

この機能の概要とデモンストレーションについては、以下の 10 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]

> [!NOTE]
> この記事で説明されている統合は、[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) に依存しています。 サブスクリプションが Event Grid リソース プロバイダーに登録されていることを確認してください。 詳細については、「[リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)」を参照してください。 *Microsoft.EventGrid/eventSubscriptions/* * アクションを実行できる必要があります。 このアクションは、EventGrid EventSubscription 共同作成者の組み込みロールの一部です。

> [!NOTE]
> BLOB ストレージ アカウントが[プライベート エンドポイント](../storage/common/storage-private-endpoints.md)の背後に存在し、パブリック ネットワーク アクセスをブロックする場合は、BLOB ストレージから Azure Event Grid への通信を許可するようにネットワーク規則を構成する必要があります。 [Storage のドキュメント](../storage/common/storage-network-security.md#grant-access-to-trusted-azure-services)に従って、Event Grid などの信頼された Azure サービスにストレージへのアクセス権を付与するか、[Event Grid のドキュメント](../event-grid/configure-private-endpoints.md)に従って、VNet アドレス空間にマップする Event Grid のプライベート エンドポイントを構成できます

## <a name="create-a-trigger-with-ui"></a>UI を使用してトリガーを作成する

このセクションでは、Azure Data Factory および Synapse のパイプラインのユーザー インターフェイス内でストレージ イベント トリガーを作成する方法について説明します。

1. Azure Data Factory の **[編集]** タブまたは Azure Synapse の **[統合]** タブに切り替えます。

1. メニューの **[トリガー]** を選択して、 **[New/Edit]\(新規作成/編集\)** を選択します。

1. **[Add Triggers]\(トリガーの追加\)** ページで、 **[Choose trigger]\(トリガーの選択\)** を選択してから、 **[+新規]** を選択します。

1. トリガーの種類で **[Storage Event]\(ストレージ イベント\)** を選択します

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image-1.png" alt-text="Data Factory UI で新しいストレージ イベント トリガーを作成するための作成者ページのスクリーンショット。":::
    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image-1-synapse.png" alt-text="Azure Synapse UI で新しいストレージ イベント トリガーを作成するための作成者ページのスクリーンショット。":::

5. [Azure サブスクリプション] ドロップダウンからストレージ アカウントを選択するか、ストレージ アカウントのリソース ID を使用して手動で選択します。 イベントを発生させるコンテナーを選択します。 コンテナーの選択は必須ですが、すべてのコンテナーを選択すると、多数のイベントが発生する可能性があることに注意してください。

   > [!NOTE]
   > 現在、ストレージ イベント トリガーでサポートされているのは、Azure Data Lake Storage Gen2 と汎用バージョン 2 のストレージ アカウントのみです。 Azure Event Grid の制限により、Azure Data Factory でサポートされるストレージ イベント トリガーは、ストレージ アカウントあたり最大 500 個のみです。 上限に達した場合は、推奨事項、および Event Grid チームによる評価時の制限の引き上げについてサポートにお問い合わせください。 

   > [!NOTE]
   > 新しいストレージ イベント トリガーの作成や既存のストレージ イベント トリガーの変更を行うには、サービスへのログインとストレージ イベント トリガーの発行に使用される Azure アカウントに、ストレージ アカウントに対する適切なロールベースのアクセス制御 (Azure RBAC) アクセス許可が付与されている必要があります。 追加のアクセス許可は不要です。Azure Data Factory および Azure Synapse のサービス プリンシパルには、ストレージ アカウントまたは Event Grid に対する特別なアクセス許可は必要 "_ありません_"。 アクセスの制御の詳細については、「[ロールベースのアクセス制御](#role-based-access-control)」セクションを参照してください。

1. **[Blob path begins with]** (次で始まる BLOB パス) と **[Blob path ends with]** (次で終わる BLOB パス) のプロパティでは、イベントを受け取るコンテナー、フォルダー、および BLOB の名前を指定できます。 ストレージ イベント トリガーでは、これらのプロパティの少なくとも 1 つを定義する必要があります。 この記事で後述する例に示すように、 **[Blob path begins with]\(Blob パスの先頭\)** と **[Blob path ends with]\(Blob パスの末尾\)** のプロパティにはさまざまなパターンを使用できます。

    * **次で始まる BLOB パス:** BLOB パスはフォルダーパスで始める必要があります。 有効な値として、`2018/` および `2018/april/shoes.csv` があります。 コンテナーが選択されていない場合、このフィールドを選択することはできません。
    * **次で終わる BLOB パス:** BLOB パスは、ファイル名または拡張子で終わる必要があります。 有効な値として、`shoes.csv` および `.csv` があります。 コンテナー名とフォルダー名を指定する場合は、`/blobs/` セグメントで区切る必要があります。 たとえば、'orders' という名前のコンテナーには、`/orders/blobs/2018/april/shoes.csv` の値を指定できます。 任意のコンテナー内のフォルダーを指定するには、先頭の '/' 文字を省略します。 たとえば、`april/shoes.csv` は、任意のコンテナーの 'april' というフォルダー内の `shoes.csv` という名前の任意のファイルでイベントをトリガーします。
    * 注: ストレージ イベント トリガーで許可されるパターン マッチングは、**次で始まる** と **次で終わる** の BLOB パスだけです。 その他の種類のワイルドカードによるマッチングは、トリガーの種類ではサポートされていません。

1. トリガーが **[Blob created]** (作成された BLOB) イベント、 **[Blob deleted]** (削除された BLOB) イベント、またはその両方に応答するかどうかを選択します。 指定されたストレージの場所で、各イベントにより、トリガーに関連付けられている Data Factory および Synapse のパイプラインがトリガーされます。

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image-2.png" alt-text="ストレージ イベント トリガー作成ページのスクリーンショット。":::

1. ゼロ バイトの BLOB をトリガーで無視するかどうかを選択します。

1. トリガーを構成したら、 **[次へ: データのプレビュー]** をクリックします。 この画面には、ストレージ イベント トリガーの構成に一致する既存の BLOB が表示されます。 特定のフィルターがあることを確認します。 対象範囲が広くなりすぎるフィルターを構成すると、多数のファイルに一致する場合があり、コストに相当の影響が及ぶ可能性があります。 フィルター構成が確認されたら、 **[完了]** をクリックします。

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image-3.png" alt-text="ストレージ イベント トリガー プレビュー ページのスクリーンショット。":::

1. このトリガーにパイプラインをアタッチするには、パイプライン キャンバスにアクセスし、 **[トリガー]** をクリックして、 **[新規作成/編集]** を選択します。 サイド ナビゲーションが表示されたら、 **[Choose trigger...]** (トリガーの選択...) ドロップダウンをクリックし、作成したトリガーを選択します。 **[次へ: データのプレビュー]** をクリックして構成が正しいことを確認し、 **[次へ]** をクリックして [データのプレビュー] が正しいことを確認します。

1. パイプラインにパラメーターがある場合は、トリガー実行パラメーター サイド ナビゲーションで指定できます。 ストレージ イベント トリガーにより、BLOB のフォルダー パスとファイル名が、プロパティ `@triggerBody().folderPath` および `@triggerBody().fileName` にキャプチャされます。 パイプラインでこれらのプロパティの値を使用するには、プロパティをパイプライン パラメーターにマップする必要があります。 プロパティをパラメーターにマップしたら、パイプライン全体で `@pipeline().parameters.parameterName` 式を使用して、トリガーでキャプチャされた値にアクセスできます。 詳しい説明については、「[パイプラインでのトリガー メタデータの参照](how-to-use-trigger-parameterization.md)」を参照してください。

   :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image-4.png" alt-text="プロパティをパイプライン パラメーターにマッピングしているストレージ イベント トリガーのスクリーンショット。":::

   前の例では、.csv で終わる BLOB パスがコンテナー _sample-data_ の _event-testing_ フォルダーに作成されたときにトリガーが起動するように構成されています。 **folderPath** と **fileName** のプロパティは、新しい BLOB の場所をキャプチャします。 たとえば、MoviesDB.csv が sample-data/event-testing のパスに追加された場合、`@triggerBody().folderPath` には `sample-data/event-testing` の値が、`@triggerBody().fileName` には `moviesDB.csv` の値が含まれます。 これらの値は、例では、パイプライン パラメーター `sourceFolder` および `sourceFile` にマップされ、それぞれ `@pipeline().parameters.sourceFolder` および `@pipeline().parameters.sourceFile` としてパイプライン全体で使用できます。

   > [!NOTE]
   > [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) でパイプラインとトリガーを作成する場合は、パラメーターとして `@trigger().outputs.body.fileName` と `@trigger().outputs.body.folderPath` を使用する必要があります。 これら 2 つのプロパティによって、BLOB 情報がキャプチャされます。 `@triggerBody().fileName` と `@triggerBody().folderPath` の代わりに、これらのプロパティを使用してください。

1. 終了したら **[完了]** をクリックします。

## <a name="json-schema"></a>JSON スキーマ

次の表に、ストレージ イベント トリガーに関連するスキーマ要素の概要を示します。

| **JSON 要素** | **説明** | **Type** | **使用できる値** | **必須** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **スコープ (scope)** | ストレージ アカウントの Azure Resource Manager リソース ID。 | String | Azure Resource Manager ID | はい |
| **events** | このトリガーを起動するイベントの種類。 | Array    | Microsoft.Storage.BlobCreated、Microsoft.Storage.BlobDeleted | はい、これらの値の任意の組み合わせが必須です。 |
| **blobPathBeginsWith** | BLOB パスは、トリガーを起動するために指定されているパターンで始まる必要があります。 たとえば、`/records/blobs/december/` のみが、`records` コンテナー下の `december` フォルダーにあるブロブのトリガーを起動します。 | String   | | `blobPathBeginsWith` または `blobPathEndsWith` の少なくとも 1 つのプロパティに値を指定します。 |
| **blobPathEndsWith** | BLOB パスは、トリガーを起動するために指定されているパターンで終わる必要があります。 たとえば、`december/boxes.csv` のみが、`december` フォルダー内の `boxes` というブロブのトリガーを起動します。 | String   | | `blobPathBeginsWith` または `blobPathEndsWith` の少なくとも 1 つのプロパティに値を指定します。 |
| **ignoreEmptyBlobs** | ゼロバイトの BLOB でパイプライン実行をトリガーするかどうか。 既定では、これは true に設定されています。 | Boolean | true または false | いいえ |

## <a name="examples-of-storage-event-triggers"></a>ストレージ イベント トリガーの例

このセクションでは、ストレージ イベント トリガーの設定の例を示します。

> [!IMPORTANT]
> 以下の例に示すように、コンテナーとフォルダー、コンテナーとファイル、またはコンテナー、フォルダー、およびファイルを指定するたびに、パスの `/blobs/` セグメントを含める必要があります。 **blobPathBeginsWith** の場合、UI では、トリガー JSON のフォルダーとコンテナー名の間に `/blobs/` が自動的に追加されます。

| プロパティ | 例 | 説明 |
|---|---|---|
| **次で始まる BLOB パス** | `/containername/` | コンテナー内の任意の BLOB のイベントを受信します。 |
| **次で始まる BLOB パス** | `/containername/blobs/foldername/` | `containername`コンテナーおよび `foldername` フォルダー内の任意の BLOB のイベントを受信します。 |
| **次で始まる BLOB パス** | `/containername/blobs/foldername/subfoldername/` | サブフォルダーを参照することもできます。 |
| **次で始まる BLOB パス** | `/containername/blobs/foldername/file.txt` | `containername` コンテナー下の `foldername` フォルダー内にある `file.txt` という名前の BLOB のイベントを受信します。 |
| **次で終わる BLOB パス** | `file.txt` | 任意のパスで `file.txt` という名前の BLOB のイベントを受信します。 |
| **次で終わる BLOB パス** | `/containername/blobs/file.txt` | コンテナー `containername` 下の `file.txt` という名前の BLOB のイベントを受信します。 |
| **次で終わる BLOB パス** | `foldername/file.txt` | 任意のコンテナー下の `foldername` フォルダーにある `file.txt` という名前の BLOB のイベントを受信します。 |

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

Azure Data Factory および Synapse のパイプラインでは、Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、BLOB イベントにリンクされたパイプラインをリッスン、更新元をサブスクライブ、トリガーするための不正アクセスが禁止されます。

* ストレージ イベント トリガーの新規作成または既存の更新を正常に行うには、サービスにサインインしている Azure アカウントが、関連するストレージ アカウントに適切にアクセスできる必要があります。 それ以外の場合は、_アクセス拒否_ によって操作が失敗します。
* Azure Data Factory と Azure Synapse には Event Grid に対する特別なアクセス許可は必要ありません。この操作のために Data Factory または Azure Synapse のサービス プリンシパルに特別な RBAC アクセス許可を割り当てる必要は "_ありません_"。

ストレージ イベント トリガーでは、次のいずれかの RBAC 設定が機能します。

* ストレージ アカウントに対する所有者ロール
* ストレージ アカウントに対する共同作成者ロール
* ストレージ アカウント _/subscriptions/####/resourceGroups/####/providers/Microsoft.Storage/storageAccounts/storageAccountName_ に対する _Microsoft.EventGrid/EventSubscriptions/Write_ アクセス許可

サービスによって 2 つの約束がどのように実現されるかを理解するために、一歩下がって、舞台裏を覗いてみましょう。 Azure Data Factory または Azure Synapse、Storage、および Event Grid 間における統合のワークフローの概要を次に示します。

### <a name="create-a-new-storage-event-trigger"></a>新しいストレージ イベント トリガーを作成する

このワークフローの概要は、Azure Data Factory によって Event Grid とやり取りしてストレージ イベント トリガーを作成する方法を示しています。  Azure Synapse の場合もデータ フローは同じで、Synapse のパイプラインが次の図の Data Factory の役割を果たします。

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-5-create-subscription.png" alt-text="ストレージ イベント トリガー作成のワークフロー。":::

ワークフローからの 2 つの目立つ引き出し線:

* Azure Data Factory および Azure Synapse は、ストレージ アカウントと直接コンタクト "_しません_"。 サブスクリプションの作成要求は、代わりに Event Grid によってリレーされて処理されます。 このため、このステップでは、サービスにストレージ アカウントへのアクセス許可は必要ありません。

* アクセス制御とアクセス許可のチェックは、サービス内で行われます。 サービスでは、ユーザーのアクセス許可がチェックされてから、ストレージ イベントをサブスクライブする要求が送信されます。 具体的には、サインインしてストレージ イベント トリガーを作成しようとしている Azure アカウントが、関連するストレージ アカウントに適切にアクセスできるかどうかをチェックします。 アクセス許可のチェックに失敗すると、トリガーの作成も失敗します。

### <a name="storage-event-trigger-pipeline-run"></a>ストレージ イベント トリガーでのパイプラインの実行

このワークフローの概要は、ストレージ イベントによって Event Grid を通じてパイプラインの実行をトリガーする方法を示しています。 Azure Synapse の場合もデータ フローは同じで、Synapse のパイプラインが次の図の Data Factory の役割を果たします。

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-6-trigger-pipeline.png" alt-text="パイプラインの実行をトリガーするストレージ イベントのワークフロー。":::

ワークフローには、サービス内のイベント トリガー パイプラインに関連する 3 つの顕著な呼び出しがあります。

* Event Grid では、ストレージによってメッセージがシステムにドロップされたらできるだけ早くメッセージを中継するプッシュ モデルが使用されます。 これは、プル システムが使用されている Kafka などのメッセージング システムとは異なります。
* イベント トリガーは、受信メッセージのアクティブなリスナーとして機能し、これにより、関連付けられているパイプラインが適切にトリガーされます。
* ストレージ イベント トリガー自体が、ストレージ アカウントと直接コンタクトすることはありません

  * ただし、ストレージ アカウント内のデータを処理するためのコピーやその他のアクティビティがパイプライン内にある場合、サービスは、リンク サービスに格納されている資格情報を使用して、ストレージと直接コンタクトします。 リンク サービスが適切に設定されていることを確認します
  * ただし、パイプラインでストレージ アカウントへの参照を作成しない場合、ストレージ アカウントにアクセスするためのアクセス許可をサービスに付与する必要はありません

## <a name="next-steps"></a>次のステップ

* トリガーについて詳しくは、「[Azure Data Factory でのパイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md#trigger-execution)」をご覧ください。
* パイプラインでトリガー メタデータを参照する方法については、[パイプライン実行でのトリガー メタデータの参照](how-to-use-trigger-parameterization.md)に関する記事を参照してください
