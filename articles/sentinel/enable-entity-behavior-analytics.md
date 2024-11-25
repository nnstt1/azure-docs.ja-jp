---
title: エンティティ動作分析を利用し、高度な脅威を検出する | Microsoft Docs
description: Microsoft Azure Sentinel でユーザーとエンティティの動作分析を有効にし、データ ソースを構成する
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: e8541c6205f8cfd7503b16fa918ca531d30d4339
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521360"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-microsoft-sentinel"></a>Microsoft Azure Sentinel でのユーザーとエンティティの動作分析 (UEBA) の有効化 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> UEBA およびエンティティ ページ機能は、Microsoft Azure Sentinel の "**_すべて_**" の地域とリージョンで **一般提供** になりました。 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>前提条件

この機能を有効または無効にするには (この機能を使用するためにこれらの前提条件は必要ありません)

- ユーザーは、ゲスト ユーザーではなく、組織の Azure Active Directory のメンバーである必要があります。

- ユーザーには、Azure AD の **全体管理者** または **セキュリティ管理者** のロールを割り当てる必要があります。

- ユーザーには、次の **Azure ロール** の少なくとも 1 つが割り当てられている必要があります ([Azure RBAC に関する詳細](roles.md))。
    - ワークスペースまたはリソース グループ レベルの **Microsoft Azure Sentinel 共同作成者**
    - リソース グループまたはサブスクリプション レベルの **Log Analytics 共同作成者**

- ワークスペースに Azure リソース ロックを適用することはできません。 [Azure リソースのロックに関する詳細](../azure-resource-manager/management/lock-resources.md)。

> [!NOTE]
> Microsoft Azure Sentinel に UEBA 機能を追加するために特別なライセンスは必要ありませんが、**追加料金** が適用されることがあります。

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>ユーザーとエンティティの動作分析を有効にする方法

1. Microsoft Azure Sentinel のナビゲーション メニューから、 **[Entity behavior]\(エンティティの動作\)** を選択します。

1. **[有効にする]** という見出しの下でトグルを **[オン]** に切り替えます。

1. **[Select data sources]\(データ ソースの選択\)** ボタンをクリックします。

1. **[データ ソースの選択]** ウィンドウで、UEBA を有効にするデータ ソースの横にあるチェック ボックスを選択し、 **[適用]** を選択します。

    > [!NOTE]
    >
    > **[データ ソースの選択]** ウィンドウの下半分に、まだ有効にしていない UEBA 対応データ ソースの一覧が表示されます。 
    >
    > UEBA を有効にすると、新しいデータ ソースに接続するとき、UEBA 対応であれば、データ コネクタ ウィンドウから直接、データ ソースの UEBA を有効にできるようになります。

1. **[Go to entity search]\(エンティティ検索に進む\)** を選択します。 これでエンティティ検索ウィンドウが表示されます。今後、Microsoft Azure Sentinel のメイン メニューから **[Entity behavior]\(エンティティの動作\)** を選択したとき、これが表示されます。

## <a name="next-steps"></a>次の手順
このドキュメントでは、Microsoft Azure Sentinel でユーザーとエンティティの動作分析 (UEBA) を有効にし、構成する方法について学習しました。 Microsoft Azure Sentinel の詳細については、次の記事を参照してください。
- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Microsoft Azure Sentinel を使用した脅威の検出](detect-threats-built-in.md)の概要。
