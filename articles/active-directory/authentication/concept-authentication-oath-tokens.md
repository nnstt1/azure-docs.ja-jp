---
title: OATH トークンの認証方法 - Azure Active Directory
description: Azure Active Directory で OATH トークンを使用して、サインイン イベントの改善と安全性の確保に役立てる方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 449a0ecd02e12816a9a9952fad0446f392ff4af7
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352885"
---
# <a name="authentication-methods-in-azure-active-directory---oath-tokens"></a>Azure Active Directory の認証方法 - OATH トークン 

OATH TOTP (Time-based One Time Password) は、1 回限りのパスワード (OTP) のコードの生成方法を指定するオープン標準です。 OATH TOTP は、コードを生成するために、ソフトウェアまたはハードウェアを使用して実装できます。 Azure AD は、別のコード生成標準である OATH HOTP をサポートしていません。

## <a name="oath-software-tokens"></a>OATH ソフトウェア トークン

ソフトウェア OATH トークンは、通常、Microsoft Authenticator アプリやその他の認証アプリなどのアプリケーションです。 Azure AD は、各 OTP を生成するためにアプリに入力して使用される秘密鍵 (シード) を生成します。

Authenticator アプリは、プッシュ通知を行うように設定されたときに自動的にコードを生成します。これにより、デバイスが接続されていない場合でも、ユーザーにはバックアップがあります。 OATH TOTP を使用してコードを生成するサード パーティ アプリケーションを使用することもできます。

一部の OATH TOTP ハードウェア トークンはプログラミング可能であり、秘密鍵やシードは事前にプログラミングされていません。 これらのプログラミング可能なハードウェア トークンは、ソフトウェア トークンのセットアップ フローから取得した秘密鍵またはシードを使用して設定できます。 顧客は、選択したベンダーからこれらのトークンを購入し、ベンダーのセットアップ プロセスで秘密鍵またはシードを使用することができます。

## <a name="oath-hardware-tokens-preview"></a>OATH ハードウェア トークン (プレビュー)

Azure AD では、30 秒または 60 秒ごとにコードを更新する OATH-TOTP SHA-1 トークンの使用をサポートしています。 顧客は、選択したベンダーからこれらのトークンを購入できます。 

OATH TOTP ハードウェア トークンには、通常、トークンで事前にプログラミングされた秘密鍵 (シード) が付属しています。 これらのキーは、次の手順に従って Azure AD に入力する必要があります。 秘密鍵は 128 文字に制限されていて、すべてのトークンと互換性があるとは限りません。 秘密キーに含めることができるのは、文字 *a-z* または *A-Z* と数字 *2-7* のみです。また、*Base32* でエンコードする必要があります。

再シードできるプログラミング可能な OATH TOTP ハードウェア トークンは、ソフトウェア トークンのセットアップ フローで Azure AD に設定することもできます。

OATH ハードウェア トークンはパブリック プレビュー段階でサポートされています。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

![OATH トークンの MFA OATH トークン ブレードへのアップロード](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

いったんトークンを取得したら、次に示す例のように、UPN、シリアル番号、秘密鍵、間隔、製造元、モデルを含む、コンマ区切り値 (CSV) ファイル形式でアップロードする必要があります。

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,2234567abcdef2234567abcdef,60,Contoso,HardwareKey
```  

> [!NOTE]
> CSV ファイルにヘッダー行が含まれていることを確認します。 UPN に単一引用符が含まれている場合は、もう 1 つの単一引用符でエスケープします。 たとえば、UPN が my'user@domain.com である場合、ファイルをアップロードするときに my''user@domain.com に変更します。

CSV ファイルとして適切な形式が整ったら、グローバル管理者は Azure portal にサインインして、 **[Azure Active Directory] > [セキュリティ] > [MFA] > [OATH トークン]** の順に移動し、作成した CSV ファイルをアップロードできます。

CSV ファイルのサイズによって異なりますが、この処理には数分間かかることがあります。 **[最新の情報に更新]** ボタンを選択して、現在の状態を取得します。 ファイルにエラーがある場合、修正するために、エラーが含まれる CSV ファイルをダウンロードできます。 ダウンロードした CSV ファイル内のフィールド名は、アップロードされたバージョンとは異なります。  

すべてのエラーが修正されたら、管理者は各キーをアクティブにすることができます。トークンの **[アクティブ化]** を選択し、トークンに表示されている OTP を入力します。 5 分ごとに最大 200 の OATH トークンをアクティブにできます。 

ユーザーは、最大 5 つの OATH ハードウェア トークンまたはいつでも使用されるように構成された認証アプリケーション (Microsoft Authenticator アプリなど) を組み合わせることもできます。 ハードウェア OATH トークンは、リソース テナントのゲスト ユーザーに割り当てることはできません。

>[!IMPORTANT]
>このプレビューは、Azure Government やソブリン クラウドではサポートされていません。

## <a name="next-steps"></a>次の手順

[Microsoft Graph REST API](/graph/api/resources/authenticationmethods-overview) を使用した認証方法の構成の詳細を確認してください。
パスワードなし認証対応の [FIDO2 セキュリティ キー プロバイダー](concept-authentication-passwordless.md#fido2-security-key-providers)についてご確認ください。
