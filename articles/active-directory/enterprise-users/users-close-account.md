---
title: アンマネージド Azure AD の組織で職場または学校アカウントを閉鎖する
description: アンマネージド Azure Active Directory のご自身の職場または学校アカウントを削除する方法。
services: active-directory
author: rolyon
manager: KarenH444
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 05/04/2021
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5e4466f86b4ffa0242bd86cfbb71809393e5999
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129985259"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-azure-ad-organization"></a>アンマネージド Azure AD 組織の職場または学校アカウントを削除する

ご自身がアンマネージド Azure Active Directory (Azure AD) 組織のユーザーで、その組織のアプリを利用する必要がなくなった場合、または関連付けを維持する必要がなくなった場合、お使いのアカウントはいつでも削除できます。 アンマネージド組織には、グローバル管理者がいません。 アンマネージド組織のユーザーは自分のアカウントをご自身で削除できます。その際、管理者に連絡する必要はありません。

アンマネージド組織のユーザーは、多くの場合、セルフサービス サインアップ中に作成されます。 たとえば、無料サービスにサインアップした組織のインフォメーション ワーカーが、このユーザーに該当する可能性があります。 セルフサービス サインアップの詳細については、 ("バイラル") サインアップの詳細については、「[Azure Active Directory のセルフサービス サインアップについて](directory-self-service-signup.md)」を参照してください。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>開始する前に

ご自身のアカウントを削除するには、事前に次の項目を確認しておく必要があります。

* ご自身がアンマネージド Azure AD 組織のユーザーであることを確認してください。 マネージド組織のユーザーの場合、自分のアカウントを削除することはできません。 マネージド組織のユーザーがアカウントを削除する必要がある場合は、管理者に連絡する必要があります。 ご自身がアンマネージド組織のユーザーかどうかを確認する方法については、「[アンマネージド テナントからユーザーを削除する](/power-automate/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)」を参照してください。

* 保持する必要があるすべてのデータを保存します。 エクスポート要求を送信する方法については、「[Accessing and exporting system-generated logs for Unmanaged Tenants (アンマネージド テナントのシステム生成ログへのアクセスとエクスポート)](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)」を参照してください。

> [!WARNING]
> 一度アカウントを削除すると、元に戻すことはできません。 ご自身のアカウントを削除すると、すべての個人データが削除され、 ご自身のアカウントやそのアカウントに関連付けられているデータにアクセスできなくなります。

## <a name="close-your-account"></a>アカウントを削除する

職場または学校のアンマネージド アカウントを削除するには、次の手順を実行します。

1. 削除するアカウントを使用してサインインし、[ご自身のアカウントを削除](https://portal.azure.com/#blade/Microsoft_AAD_IAM/PrivacyDataRequests)します。

1. **[データ要求]** で **[アカウントの削除]** を選択します。

    ![データ要求 - アカウントの削除](./media/users-close-account/close-account.png)

1. 確認メッセージを確認して、 **[はい]** を選択します。

    ![データ要求 - 削除の確認](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory のセルフサービス サインアップについて](directory-self-service-signup.md)
- [アンマネージド テナントからユーザーを削除する](/power-automate/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [アンマネージド テナントのシステム生成ログへのアクセスとエクスポート](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)