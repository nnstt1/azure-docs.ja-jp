---
title: Azure の予約のディレクトリを変更する
description: この記事は、予約所有者が Azure Active Directory テナント (ディレクトリ) 間で予約注文を譲渡する際に役立ちます。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 04/27/2021
ms.openlocfilehash: 183071454cbe6c9185ecb1868abfe1ac217e9519
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108143667"
---
# <a name="change-an-azure-reservation-directory-between-tenants"></a>Azure の予約のディレクトリをテナント間で変更する

この記事は、予約所有者が Azure Active Directory テナント (ディレクトリ) 間で予約注文のディレクトリを変更する際に役立ちます。 予約注文のディレクトリを変更すると、予約注文と従属する予約への Azure RBAC アクセスが削除されます。 変更後にアクセスできるのは予約所有者だけになります。 ディレクトリを変更しても、予約注文の課金所有権は変更されません。 親予約注文と従属する予約のディレクトリが変更されます。

予約注文のディレクトリを変更するうえで予約の交換やキャンセルは必要ありません。

予約のディレクトリを別のテナントに変更した後で、その予約に所有者を追加することもできます。 詳細については、「[既定で予約を管理できるユーザー](view-reservations.md#who-can-manage-a-reservation-by-default)」をご覧ください。

予約注文のディレクトリを変更すると、その注文の下にあるすべての予約が一緒に譲渡されます。

## <a name="change-a-reservation-orders-directory"></a>予約注文のディレクトリを変更する

予約注文のディレクトリとそれに従属する予約を別のテナントに変更するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 課金管理者ではなく、予約所有者の場合は、 **[予約]** に移動し、手順 5. に進みます。
1. **[コストの管理と請求]** に移動します。
    - EA 管理者の場合は、左側のメニューで **[課金スコープ]** を選択し、課金スコープの一覧でスコープを選択します。
    - Microsoft 顧客契約の課金プロファイル所有者の場合は、左側のメニューで **[課金プロファイル]** を選択します。 課金プロファイルの一覧でプロファイルを選択します。
1. EA 加入契約または課金プロファイルの予約の完全な一覧が表示されます。
1. 譲渡する予約を選択します。
1. 予約の詳細で予約注文 ID を選択します。
1. 予約注文で、 **[ディレクトリの変更]** を選択します。
1. [ディレクトリの変更] ペインで、予約の譲渡先となる Azure AD ディレクトリを選択し、 **[確認]** を選択します。

## <a name="next-steps"></a>次のステップ

- 予約の詳細については、「[Azure の予約とは](save-compute-costs-reservations.md)」を参照してください。