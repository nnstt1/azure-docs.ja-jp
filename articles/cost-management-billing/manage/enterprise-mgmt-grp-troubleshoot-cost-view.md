---
title: Azure エンタープライズ コスト表示のトラブルシューティング
description: Azure Portal で組織のコストを表示するときに発生しうる問題を解決する方法を説明します。
author: bandersmsft
ms.reviewer: amberb
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: troubleshooting
ms.date: 10/22/2021
ms.author: banders
ms.custom: sapnakeshari
ms.openlocfilehash: 02507604c1194d726014453bde6cd03c2c21aa31
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130255428"
---
# <a name="troubleshoot-enterprise-cost-views"></a>エンタープライズ コスト表示のトラブルシューティング

エンタープライズ加入契約では、一部の設定において登録ユーザーにコストが表示されません。  これらの設定は、登録管理者によって管理されます。 または、登録を Microsoft から直接購入していない場合は、設定はパートナーによって管理されます。  この記事では、設定の概要と、設定が登録に与える影響について説明します。 これらの設定は、Azure ロールには依存しません。

## <a name="enable-access-to-costs"></a>コストへのアクセスを有効にする

コストの情報を探しているときに、未承認のメッセージまたは *''登録ではコストのビューが無効になっています''* というメッセージが表示されますか。 コストの情報を探しているときに、
![サブスクリプションの [現在のコスト] フィールドに "未承認" が表示されているスクリーンショット。](./media/enterprise-mgmt-grp-troubleshoot-cost-view/unauthorized.png)

それには、次のいずれかの理由が考えられます。

1. エンタープライズ パートナーを通して Azure を購入し、そのパートナーがまだ料金を公開していない。 パートナーに問い合わせて[エンタープライズ ポータル](https://ea.azure.com)で料金設定を更新してもらってください。
2. EA の直接のお客様である場合は、次の可能性が考えられます。
    * お客様はアカウント所有者であり、登録管理者が **AO ビュー料金** 設定を無効にした。  
    * お客様は部門管理者であり、登録管理者が **DA ビュー料金** 設定を無効にした。
    * 登録管理者に問い合わせて、アクセスできるように設定してもらってください。 登録管理者は [Azure portal](https://portal.azure.com/) で設定を更新できます。 **[ポリシー]** メニューに移動して、設定を変更します。 
    * 登録管理者は[エンタープライズ ポータル](https://ea.azure.com/manage/enrollment)で設定を更新できます。

      ![ビュー変更のためのエンタープライズ ポータル設定を示しているスクリーンショット。](./media/enterprise-mgmt-grp-troubleshoot-cost-view/ea-portal-settings.png)
    
 

## <a name="asset-is-unavailable"></a>アセットを使用できない場合

サブスクリプションまたは管理グループにアクセスしようとしたときに、**このアセットは使用できません** というエラー メッセージが表示された場合、この項目を表示するための正しいロールを持っていないということになります。  

!["アセットは使用できません" というメッセージを示しているスクリーンショット。](./media/enterprise-mgmt-grp-troubleshoot-cost-view/asset-not-found.png)

Azure サブスクリプションを確認するか、管理グループ管理者にアクセスを依頼してください。 詳細については、[Azure portal を使用して Azure ロールを割り当てる方法](../../role-based-access-control/role-assignments-portal.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
- ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
