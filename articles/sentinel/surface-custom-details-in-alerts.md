---
title: Microsoft Sentinel アラートでカスタムの詳細を表示する | Microsoft Docs
description: Microsoft Sentinel 分析ルールでアラートに含まれるカスタム イベントの詳細を抽出して表示し、より適切で詳細なインシデント情報を取得します
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0b8cf32cce618ec5298ac9494062a67b53d98d76
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132711611"
---
# <a name="surface-custom-event-details-in-alerts-in-microsoft-sentinel"></a>Microsoft Sentinel でアラートに含まれるカスタム イベントの詳細を表示する 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - カスタム詳細機能は **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="introduction"></a>はじめに

[スケジュールされたクエリ分析ルール](detect-threats-custom.md)を使用すると、Microsoft Sentinel に接続されたデータ ソースの **イベント** を分析し、それらのイベントの内容がセキュリティの観点から重要な場合に **アラート** を生成できます。 これらのアラートは、Microsoft Sentinel のさまざまなエンジンを使用してさらに分析、グループ化、フィルター処理が行われ、SOC アナリストにとって注意が必要な **インシデント** へと抽出されます。 ただし、アナリストがインシデントを表示した場合、すぐに表示されるのはコンポーネント アラート自体のプロパティのみです。 実際の内容 (イベントに含まれる情報) を確認するには、さらに調べる必要があります。

**分析ルール ウィザード** の **カスタムの詳細** 機能を使用すると、それらのイベントから構築されたイベント データをアラートに表示し、イベント データをアラート プロパティの一部にすることができます。 事実上、これにより、インシデントのイベントの内容をすぐに可視化できるため、はるかに高速で効率的なトリアージ、調査、結論の導出、対応が可能になります。

以下で詳しく説明する手順は、分析ルールの作成ウィザードの一部です。 ここでは、既存の分析ルールでカスタムの詳細を追加または変更するシナリオに対処するために、個別に扱います。

## <a name="how-to-surface-custom-event-details"></a>カスタム イベントの詳細を表示する方法

1. Microsoft Sentinel のナビゲーション メニューから **[分析]** を選択します。

1. スケジュールされたクエリ ルールを選択し、 **[編集]** をクリックします。 または、画面の上部にある **[作成] > [スケジュール済みクエリ ルール]** をクリックして新しいルールを作成します。

1. **[ルールのロジックを設定]** タブをクリックします。

1. **[Alert enhancement (Preview)]\(アラート集約 (プレビュー\))** セクションで、 **[Custom details]\(カスタムの詳細\)** を展開します。

    :::image type="content" source="media/surface-custom-details-in-alerts/alert-enrichment.png" alt-text="カスタムの詳細の検索と選択":::

1. 展開された **[Custom details]\(カスタムの詳細\)** セクションで、表示する詳細に対応するキーと値のペアを追加します。

    1. **[キー]** フィールドに、アラートのフィールド名として表示される任意の名前を入力します。

    1. **[値]** フィールドで、アラートに表示するイベント パラメーターをドロップダウン リストから選択します。 この一覧には、ルール クエリの対象となるテーブルのフィールドに対応する値が設定されます。
    
        :::image type="content" source="media/surface-custom-details-in-alerts/custom-details.png" alt-text="カスタムの詳細を追加する":::

1. **[新規追加]** をクリックして詳細を表示し、最後の手順を繰り返してキーと値のペアを定義します。 

    気が変わった場合や、間違えた場合は、その詳細の **[値]** ドロップダウン リストの横にあるゴミ箱アイコンをクリックすると、カスタムの詳細を削除できます。

1. カスタム詳細の定義が完了したら、 **[確認と作成]** タブをクリックします。ルールの検証に成功したら、 **[保存]** をクリックします。

    > [!NOTE]
    > 
    > **サービスの制限**
    > - 1 つの分析ルールで **最大 20 個のカスタムの詳細** を定義できます。
    >
    > - すべてのカスタム詳細のサイズ制限は、まとめて **2 KB** です。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Microsoft Sentinel 分析ルールを使用してアラートにカスタムの詳細を表示する方法について説明します。 Microsoft Azure Sentinel の詳細については、次の記事を参照してください。
- [スケジュールされたクエリ分析ルール](detect-threats-custom.md)の完全な画像を取得します。
- [Microsoft Azure Sentinel のエンティティ](entities.md)について詳しく確認します。
