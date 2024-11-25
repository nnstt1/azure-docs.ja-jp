---
title: 条件付きアクセス - 場所ごとにアクセスをブロックする - Azure Active Directory
description: カスタムの条件付きアクセス ポリシーを作成して、IP の場所によってリソースへのアクセスをブロックします
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 11/05/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09510a64fdee1525ca18ee4985206faa18263b85
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132706895"
---
# <a name="conditional-access-block-access-by-location"></a>条件付きアクセス:場所ごとにアクセスをブロックする

条件付きアクセスで場所の条件を使用すると、ユーザーのネットワークの場所に基づいて、クラウド アプリへのアクセスを制御できます。 場所の条件は、一般に、トラフィックの発信元として不適切であると組織が認識している国またはリージョンからのアクセスをブロックするために使用されます。

> [!NOTE]
> 条件付きアクセス ポリシーは、第 1 段階認証が完了した後で適用されます。 条件付きアクセスはサービス拒否 (DoS) 攻撃などのシナリオに対する組織の防御の最前線を意図したものではありませんが、これらのイベントからのシグナルを使用してアクセス権を判定できます。

## <a name="define-locations"></a>場所を定義する

1. **Azure portal** にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]**  >  **[ネームド ロケーション]** を参照します。
1. **[新しい場所]** を選択します。
1. 場所に名前を付けます。
1. その場所または **国/地域** を構成する、外部からアクセス可能な特定の IPv4 アドレス範囲がわかっている場合は、 **[IP 範囲]** を選択します。
   1. 指定しようとしている場所の **[IP 範囲]** を指定するか、 **[国/地域]** を選択します。
      * 国/地域を選択する場合、必要に応じて不明な領域を含めることもできます。
1. **[保存]** を選択します。

条件付きアクセスにおける場所の条件の詳細については、「[Azure Active Directory 条件付きアクセスの場所の条件の概要](location-condition.md)」の記事を参照してください。

## <a name="create-a-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する

1. **Azure portal** にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** の順に移動します。
1. **[新しいポリシー]** を選択します。
1. ポリシーに名前を付けます。 ポリシーの名前に対する意味のある標準を組織で作成することをお勧めします。
1. **[割り当て]** で、 **[ユーザーとグループ]** を選択します。
   1. **[Include]\(含める\)** で、 **[すべてのユーザー]** を選択します。
   1. **[除外]** で、 **[ユーザーとグループ]** を選択し、組織の緊急アクセス用または非常用アカウントを選択します。 
   1. **[Done]** を選択します。
1. **[Cloud apps or actions]\(クラウド アプリまたはアクション\)**  >  **[Include]\(含める\)** の順に移動し、 **[すべてのクラウド アプリ]** を選択します。
1. **[条件]**  >  **[Location]\(場所\)** で
   1. **[Configure]\(構成する\)** を **[はい]** に設定します
   1. **[Include]\(含める\)** で **[選択された場所]** を選択します
   1. 組織に対して作成したブロック対象の場所を選択します。
   1. **[選択]** をクリックします。
1. **[アクセス制御]** で、 **[アクセスのブロック]** を選択し、さらに **[選択]** を選択します。
1. 設定を確認し、 **[ポリシーの有効化]** を **[レポート専用]** に設定します。
1. **[作成]** を選択して、ポリシーを作成および有効化します。

管理者は、[レポート専用モード](howto-conditional-access-insights-reporting.md)を使用して設定を確認したら、 **[ポリシーの有効化]** トグルを **[レポートのみ]** から **[オン]** に移動できます。

## <a name="next-steps"></a>次のステップ

[Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)

[条件付きアクセスのレポート専用モードを使用した影響を判断する](howto-conditional-access-insights-reporting.md)

[Simulate sign in behavior using the Conditional Access What If tool](troubleshoot-conditional-access-what-if.md) (条件付きアクセスの What If ツールを使用したサインイン動作のシミュレート)
