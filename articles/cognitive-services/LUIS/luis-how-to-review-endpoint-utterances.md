---
title: ユーザーの発話を確認する - LUIS
titleSuffix: Azure Cognitive Services
description: アクティブ ラーニングによってキャプチャされた発話を確認し、意図を選択して、文字にされた発話に対応するエンティティをマークします。その後、変更を承認し、トレーニングして公開することになります。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/05/2021
ms.openlocfilehash: 41c45b2887736847c2413c2193e57d35f49594e7
ms.sourcegitcommit: 38d81c4afd3fec0c56cc9c032ae5169e500f345d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2021
ms.locfileid: "109518087"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>エンドポイントの発話を見直し、LUIS アプリを改善する方法

正しく予測するためにエンドポイントの発話を見直すプロセスは[アクティブ ラーニング](luis-concept-review-endpoint-utterances.md)と呼ばれています。 アクティブ ラーニングでは、エンドポイント クエリがキャプチャされ、ユーザーの不確かなエンドポイントの発話が選択されます。 これらの発話をレビューして、意図を選択し、文字にされた発話に対応するエンティティをマークすることになります。 それらの変更を承認して発話の例に追加したら、トレーニングして公開します。 そうすることで、LUIS による発話の識別の精度が向上していきます。

## <a name="log-user-queries-to-enable-active-learning"></a>アクティブ ラーニングを有効にするためにユーザーのクエリをログに記録する

アクティブ ラーニングを有効にするには、ユーザー クエリをログに記録する必要があります。 そのためには、`log=true` という querystring パラメーターと値を指定し、[エンドポイント クエリ](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint)を呼び出します。

LUIS ポータルを使用して、正しいエンドポイント クエリを作成します。

1. [LUIS ポータル](https://www.luis.ai)にサインインし、自分の **サブスクリプション** と **作成リソース** を選択して、その作成リソースに割り当てられているアプリを表示します。
1. **[マイ アプリ]** ページで自分のアプリの名前を選択して、そのアプリを開きます。
1. **[Manage]\(管理\)** セクションに移動し、 **[Azure resources]\(Azure リソース\)** を選択します。
1. 割り当てられた予測リソースについて、 **[Change query parameters]\(クエリ パラメーターの変更\)** を選択します。

    > [!div class="mx-imgBorder"]
    > ![[Change query parameters]\(クエリ パラメーターの変更\) リンクを示すスクリーンショット。](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. **[Save logs]\(ログの保存\)** を切り替え、 **[Done]\(完了\)** を選択して保存します。

    > [!div class="mx-imgBorder"]
    > ![LUIS ポータルを使用してログを保存します。これはアクティブ ラーニングに必要です。](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     この操作によって `log=true` querystring パラメーターが追加され、サンプル URL が変更されます。 ランタイム エンドポイントに対する予測クエリを実行するときに、変更したサンプル クエリ URL をコピーして使用します。

## <a name="correct-predictions-to-align-utterances"></a>正しい予測で発話を調整する

各発話では、 **[予測される意図]** 列に意図の候補が表示され、点線の境界ボックスにエンティティの候補が表示されます。

> [!div class="mx-imgBorder"]
> [![LUIS で理解されないエンドポイントの発話を見直す](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

予測される意図およびエンティティに同意する場合は、発話の横にあるチェック マークを選択します。 チェック マークが無効になっている場合、これは確認すべきものがないということを意味します。  
意図の候補に同意しない場合は、[予測される意図] ドロップダウン リストから適切な意図を選択します。 エンティティの候補に同意しない場合は、それらのラベル付けを開始します。
完了したら、発話の横にあるチェック マークを選択して、ラベル付けした内容を確認します。 **[Save utterance]/(発話の保存)** を選択して、それをレビュー リストから移動し、それぞれの意図を追加します。

> [!TIP]
> [Intent detail]\(意図の詳細\) ページに進み、 **[Review Endpoint Utterances]\(エンドポイントの発話を見直す\)** リストの全サンプル発話のエンティティ予測を見直し、修正することが重要です。

## <a name="delete-utterance"></a>発話を削除する

各発話は、レビュー リストから削除できます。 一度削除すると、リストに再び表示されることはなくなります。 ユーザーがエンドポイントから同じ発話を入力したとしても同様です。

発話を削除すべきかわからない場合、None 意図に移動するか、`miscellaneous` のような新しい意図を作成し、その意図に発話を移動してください。

## <a name="disable-active-learning"></a>アクティブ ラーニングを無効にする

アクティブ ラーニングを無効にするには、ユーザーのクエリがログされないようにします。 これを行うには、上記のようにクエリ パラメーターを変更するか、エンドポイント クエリで `log=false` パラメーターを設定します。あるいは、既定値が `false` なので `log` パラメーターを省略してもかまいません。


## <a name="next-steps"></a>次のステップ

推奨される発話にラベル付けした後にパフォーマンスがどのように向上するかをテストするには、上部のパネルの **[Test]\(テスト\)** を選択してテスト コンソールにアクセスします。 テスト コンソールを使用してアプリをテストする手順については、[アプリのトレーニングとテスト](luis-interactive-test.md)に関する記事をご覧ください。
