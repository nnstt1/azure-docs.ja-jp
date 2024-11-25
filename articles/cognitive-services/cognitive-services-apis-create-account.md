---
title: Azure portal で Cognitive Services リソースを作成する
titleSuffix: Azure Cognitive Services
description: Azure portal でリソースを作成し、サブスクライブすることによって、Azure Cognitive Services の使用を開始します。
services: cognitive-services
author: aahill
manager: nitinme
keywords: Cognitive Services, コグニティブ インテリジェンス, コグニティブ ソリューション, AI サービス
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: aahi
ms.openlocfilehash: 5ebccc07b4816237f12363e7729175dde5537e50
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458344"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-portal"></a>クイックスタート: Azure portal を使用して Cognitive Services リソースを作成する

このクイックスタートを使用して、Azure Cognitive Services の使用を開始します。 Azure portal で Cognitive Services リソースを作成すると、アプリケーションの認証を行うためのエンドポイントとキーが得られます。

Azure Cognitive Services は、開発者が直接的な人工知能 (AI) またはデータ サイエンスのスキルや知識がなくてもコグニティブかつインテリジェントなアプリケーションを構築できる、REST API シリーズとクライアント ライブラリ SDK を含むクラウドベースのサービスです。 開発者は Azure Cognitive Services を使用して、見たり、聞いたり、話したり、理解したり、推論し始めたりできるコグニティブ ソリューションを使用したコグニティブ機能をそのアプリケーションに容易に追加することができます。

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>前提条件

* 有効な Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/cognitive-services/)。
* [!INCLUDE [contributor-requirement](./includes/quickstarts/contributor-requirement.md)]


## <a name="create-a-new-azure-cognitive-services-resource"></a>新しい Azure Cognitive Services リソースを作成する

1. リソースを作成します。

### <a name="multi-service-resource"></a>[マルチサービス リソース](#tab/multiservice)

マルチサービス リソースには、ポータルで **Cognitive Services** という名前が付けられます。 [Cognitive Services リソースを作成します](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)。

現在、マルチサービス リソースでは次の Cognitive Services にアクセスできます。

* **視覚** - Computer Vision、Custom Vision、Face
* **音声** - Speech
* **言語** - Language、Translator
* **決定** - Content Moderator

### <a name="single-service-resource"></a>[単一サービス リソース](#tab/singleservice)

以下のリンクを使用して、利用可能な Cognitive Services のリソースを作成します。

| 視覚                      | 音声                  | Language                          | 決定             |
|-----------------------------|-------------------------|-----------------------------------|----------------------|
| [Computer Vision](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Speech Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [イマーシブ リーダー](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Anomaly Detector](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | 
| [Custom Vision Service](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) |  | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | 
| [Face](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     |
|        |                         | [言語サービス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |  [Metrics Advisor](https://go.microsoft.com/fwlink/?linkid=2142156)                    |
| | | [Translator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) | |

---

2. **[作成]** ページで、次の情報を指定します。
<!-- markdownlint-disable MD024 -->

### <a name="multi-service-resource"></a>[マルチサービス リソース](#tab/multiservice)

|プロジェクトの詳細| 説明   |
|--|--|
| **サブスクリプション** | 使用できる Azure サブスクリプションのいずれかを選択します。 |
| **リソース グループ** | Cognitive Services リソースを含むことになる Azure リソース グループ。 新しいグループを作成することも、既存のグループに追加することもできます。 |
| **リージョン** | Cognitive Services インスタンスの場所。 別の場所を選択すると待機時間が生じる可能性がありますが、リソースのランタイムの可用性には影響しません。 |
| **名前** | Cognitive Services リソースのわかりやすい名前。 例: *MyCognitiveServicesResource*。 |
| **価格レベル** | Cognitive Services アカウントのコストは、選択しているオプションと使用量によって異なります。 詳細については、「[API の価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/)」をご覧ください。

<!--![Multi-service resource creation screen](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)-->
:::image type="content" source="media/cognitive-services-apis-create-account/resource_create_screen-multi.png" alt-text="マルチサービス リソースの作成画面":::

状況に合わせて条件を読んで同意し、 **[Review + create]\(レビュー + 作成\)** を選択します。

### <a name="single-service-resource"></a>[単一サービス リソース](#tab/singleservice)

|プロジェクトの詳細| 説明   |
|--|--|
| **サブスクリプション** | 使用できる Azure サブスクリプションのいずれかを選択します。 |
| **リソース グループ** | Cognitive Services リソースを含むことになる Azure リソース グループ。 新しいグループを作成することも、既存のグループに追加することもできます。 |
| **リージョン** | Cognitive Services インスタンスの場所。 別の場所を選択すると待機時間が生じる可能性がありますが、リソースのランタイムの可用性には影響しません。 |
| **名前** | Cognitive Sservices リソースのわかりやすい名前。 例: *MyCognitiveServicesResource*。 |
| **価格レベル** | Cognitive Services アカウントのコストは、選択しているオプションと使用量によって異なります。 詳細については、「[API の価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/)」をご覧ください。

<!--![Single-service resource creation screen](media/cognitive-services-apis-create-account/resource_create_screen.png)-->
:::image type="content" source="media/cognitive-services-apis-create-account/resource_create_screen.png" alt-text="単一サービス リソースのリソース作成画面":::

**[Next: Virtual Network]\(次へ: 仮想ネットワーク\)** を選択し、リソースに対して許可するネットワーク アクセスの種類を選択してから、 **[Review + create]\(レビュー + 作成\)** を選択します。

---

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>リソースのキーを取得する

1. リソースが正常にデプロイされたら、 **[次の手順]** の下にある **[リソースに移動]** をクリックします。

    ![Cognitive Services を検索する](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. 開かれたクイックスタート ウィンドウで、キーとエンドポイントにアクセスできます。

    ![キーとエンドポイントを取得する](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、そのグループに含まれている他のリソースも削除されます。

1. Azure Portal で左側のメニューを展開してサービスのメニューを開き、 **[リソース グループ]** を選択して、リソース グループの一覧を表示します。
2. 削除するリソースが含まれているリソース グループを見つけます
3. リソース グループの一覧を右クリックします。 **[リソース グループの削除]** を選択し、確認します。

削除されたリソースを復旧する必要がある場合は、[削除された Cognitive Services リソースの復旧](manage-resources.md)に関するページを参照してください。

## <a name="see-also"></a>関連項目

* Cognitive Services を安全に使用する方法については、「 **[Azure Cognitive Services に対する要求の認証](authentication.md)** 」をご覧ください。
* Cognitive Services 内のさまざまなカテゴリの一覧を入手するには、「 **[Azure Cognitive Services とは](./what-are-cognitive-services.md)** 」をご覧ください。
* Cognitive Services がサポートする自然言語の一覧を確認するには、 **[自然言語のサポート](language-support.md)** に関する記事をご覧ください。
* Cognitive Services をオンプレミスで使用する方法については、 **[コンテナーとしての Cognitive Services の使用](cognitive-services-container-support.md)** に関する記事をご覧ください。
* Cognitive Services の使用コストを見積もるには、 **[Cognitive Services のコストの計画および管理](plan-manage-costs.md)** に関する記事をご覧ください。
