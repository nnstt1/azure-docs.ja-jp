---
title: 多くのワークスペースの Microsoft Sentinel インシデントを一度に操作する |Microsoft Docs
description: Microsoft Sentinel で複数のワークスペース内のインシデントを同時に表示する方法。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: c556fa7da28aab7e28affddaf82cd3e100dfc191
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712986"
---
# <a name="work-with-incidents-in-many-workspaces-at-once"></a>多くのワークスペースのインシデントを一度に操作する 

 [!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Sentinel の機能を最大限に活用するために、Microsoft では 1 つのワークスペース環境を使用することをお勧めしています。 ただし、場合によっては、複数のテナントにまたがって複数のワークスペースが必要になるユース ケースがあります。たとえば、[マネージド セキュリティ サービス プロバイダー (MSSP)](./multiple-tenants-service-providers.md) とその顧客の場合が挙げられます。 **[複数ワークスペース ビュー]** を使用すると、複数のワークスペース (場合によってはテナント) 全体のセキュリティ インシデントを同時に表示して操作でき、組織のセキュリティの応答性を完全に可視化して制御できます。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="entering-multiple-workspace-view"></a>[複数ワークスペース ビュー] に入る

Microsoft Sentinel を開くと、選択したすべてのテナントとサブスクリプション全体の、ご自身がアクセス権を持つすべてのワークスペースの一覧が表示されます。 各ワークスペース名の左側にはチェックボックスがあります。 1 つのワークスペースの名前をクリックすると、そのワークスペースが表示されます。 複数のワークスペースを選択するには、対応するすべてのチェックボックスをクリックし、ページの上部にある **[複数ワークスペース ビュー]** ボタンをクリックします。

> [!IMPORTANT]
> 現在、[複数ワークスペース ビュー] では、最大 10 個ワークスペースを同時に表示できます。 
> 
> 10 個を超えるワークスペースにチェックを入れると、警告メッセージが表示されます。

ワークスペースの一覧には、各ワークスペースに関連付けられているディレクトリ、サブスクリプション、場所、リソース グループが表示されます。 ディレクトリはテナントに対応します。

   ![複数のワークスペースを選択する](./media/multiple-workspace-view/workspaces.png)

## <a name="working-with-incidents"></a>インシデントを操作する

**[複数ワークスペース ビュー]** では、現在 **[Incidents]\(インシデント\)** 画面のみを利用できます。 これは、通常の **[Incidents]\(インシデント\)** 画面とほとんど同様に表示され、機能します。 ただし、いくつか重要な相違点があります。

   ![複数のワークスペースのインシデントを表示する](./media/multiple-workspace-view/incidents.png)

- ページ上部のカウンター ( *[Open incidents]\(オープン インシデント\)* 、 *[新しいインシデント]* 、 *[進行中]* など) には、選択したすべてのワークスペースについての数が集合的に表示されます。

- 選択したすべてのワークスペースとディレクトリ (テナント) のインシデントが、統合された 1 つの一覧に表示されます。 通常の **[Incidents]\(インシデント\)** 画面のフィルターに加えて、ワークスペースとディレクトリによって一覧をフィルターできます。

- インシデントを選択したすべてのワークスペースに対して、読み取りと書き込みの権限が必要です。 一部のワークスペースに対して読み取り権限しかない場合、それらのワークスペースでインシデントを選択すると、警告メッセージが表示されます。 それらのインシデントも、それらと一緒に選択した他のインシデントも (他のものに対する権限がある場合でも) 変更できません。

- 1 つのインシデントを選択し、 **[すべての詳細を表示]** または **[Actions]\(アクション\)**  >  **[調査]** をクリックすると、それ以降、そのインシデントのワークスペースのデータ コンテキストに入ることになり、他には入りません。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、複数の Microsoft Sentinel ワークスペースのインシデントを同時に表示して操作する方法を学習しました。 Microsoft Sentinel の詳細については、次の記事を参照してください。
- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Microsoft Sentinel を使用した脅威の検出](detect-threats-built-in.md)の概要。
