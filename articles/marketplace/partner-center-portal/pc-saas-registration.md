---
title: SaaS アプリケーションを登録する - Azure Marketplace
description: Azure portal を使用して SaaS アプリケーションを登録し、Azure Active Directory セキュリティ トークンを受け取る方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/10/2020
author: saasguide
ms.author: souchak
ms.openlocfilehash: 78a66070c6bcf03f4a279163106048d87fe27f23
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061384"
---
# <a name="register-a-saas-application"></a>SaaS アプリケーションを登録する

この記事では、Microsoft [Azure portal](https://portal.azure.com/) を使用して SaaS アプリケーションを登録する方法と、公開元のアクセス トークン (Azure Active Directory アクセス トークン) を取得する方法について説明します。 公開元はこのトークンを使用して、SaaS Fulfillment API を呼び出すことによって、SaaS アプリケーションを認証します。  Fulfillment API は、OAuth 2.0 クライアント資格情報を使用して、サービス間のアクセス トークン要求を行うための Azure Active Directory (v1.0) エンドポイントのフローを許可します。

Azure Marketplace では、SaaS サービスがエンド ユーザーに対して使用する認証方法に対して、制約がありません。 次のフローは、SaaS Service を Azure Marketplace で認証する場合にのみ必要です。

Azure AD (Active Directory) の詳細については、[認証の概要](../../active-directory/develop/authentication-vs-authorization.md)に関するページを参照してください。

## <a name="register-an-azure-ad-secured-app"></a>Azure AD で保護されるアプリを登録する

アプリケーションで Azure AD の機能を使用するには、まず Azure AD テナントにそのアプリケーションを登録する必要があります。 この登録プロセスでは、アプリケーションに関する詳細の一部を Azure AD に渡します。 Azure portal を使用して新しいアプリケーションを登録するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. お使いのアカウントで複数のアプリケーションにアクセスできる場合は、右上隅で自分のアカウントを選択します。 その後、ポータル セッションを目的の Azure AD テナントに変更します。
3. 左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** サービスを選択してから、 **[アプリの登録]** 、 **[新規アプリケーションの登録]** の順に選択します。

    ![SaaS AD のアプリ登録](./media/saas-offer-app-registration-v1.png)

4. 作成\' ページで、アプリケーションの登録情報を入力します。
    -   **Name**:わかりやすいアプリケーション名を入力します
    -   **アプリケーションの種類**:  
        
        セキュリティで保護されたサーバーにインストールされている [クライアント アプリケーション](../../active-directory/develop/developer-glossary.md#client-application)と [リソースおよび API アプリケーション](../../active-directory/develop/developer-glossary.md#resource-server)については、 **[Web アプリ/API]** を選択します。 OAuth のコンフィデンシャル [Web クライアント](../../active-directory/develop/developer-glossary.md#web-client)と、パブリック [ユーザーエージェントベース クライアント](../../active-directory/develop/developer-glossary.md#user-agent-based-client)の場合には、この設定を使用します。
        同じアプリケーションでクライアントとリソース/API を両方とも公開することもできます。

        Web アプリケーションの具体的な例については、[Azure AD 開発者向けガイド](../../active-directory/develop/index.yml)の[開始](../../active-directory/develop/quickstart-create-new-tenant.md)セクションで利用できるクイックスタート ガイド付きセットアップを確認してください。

5. 終了したら、 **[登録]** を選択します。  Azure AD によって、新しいアプリケーションに一意の "*アプリケーション ID*" が割り当てられます。 API にアクセスする 1 つのアプリだけをシングル テナントとして登録することをお勧めします。

6. クライアント シークレットを作成するには、 **[証明書とシークレット]** ページに移動し、 **[+ 新しいクライアント シークレット]** を選択します。  シークレット値は、コードで使用するため、必ずコピーしてください。

**Azure AD アプリ ID** は自分の公開元 ID に関連付けられているため、自分のすべてのプランで同じ "*アプリ ID*" が使用されるようにしてください。

>[!Note]
>公開元がパートナー センターに 2 つ以上の異なるアカウントを持っている場合、Azure AD アプリの登録の詳細は 1 つのアカウントでのみ使用できます。 別の公開元アカウントのプランに同じテナント ID、アプリ ID のペアを使用することはサポートされません。

## <a name="how-to-get-the-publishers-authorization-token"></a>公開元の承認トークンを取得する方法

アプリケーションを登録したら、公開元の承認トークン (Azure AD アクセス トークン、Azure AD V1 エンドポイントを使用) をプログラムで要求できます。 公開元は、さまざまな SaaS Fulfillment API を呼び出すときに、このトークンを使用する必要があります。 このトークンは 1 時間だけ有効です。

これらのトークンの詳細については、「[Azure Active Directory アクセス トークン](../../active-directory/develop/access-tokens.md)」を参照してください。  下のフローでは、V1 エンドポイント トークンが使用されます。

### <a name="get-the-token-with-an-http-post"></a>HTTP POST を使用してトークンを取得する

#### <a name="http-method"></a>HTTP メソッド

Post<br>

##### <a name="request-url"></a>*要求 URL* 

`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`

##### <a name="uri-parameter"></a>*URI パラメーター*

|  パラメーター名    |  必須         |  説明 |
|  ---------------   |  ---------------  | ------------ |
|  `tenantId`        |  True      |  登録された AAD アプリケーションのテナント ID。 |

##### <a name="request-header"></a>*要求ヘッダー*

|  ヘッダー名       |  必須         |  説明 |
|  ---------------   |  ---------------  | ------------ |
|  `content-type`    |  True      |  要求に関連付けられたコンテンツの種類。 既定値は `application/x-www-form-urlencoded` です。 |

##### <a name="request-body"></a>*要求本文*

|  プロパティ名     |  必須         |  説明 |
|  ---------------   |  ---------------  | ------------ |
|  `grant_type`      |  True      |  付与タイプ。 `"client_credentials"`を使用します。 |
|  `client_id`       |  True      |  Azure AD アプリに関連付けられているクライアントまたはアプリの識別子。 |
|  `client_secret`   |  True      |  Azure AD アプリに関連付けられているシークレット。 |
|  `resource`        |  True      |  トークンを要求されたターゲット リソース。 この場合、Marketplace SaaS API は常にターゲット リソースであるため、`20e940b3-4c77-4b0b-9a53-9e16a1b010a7` を使用します。 |

##### <a name="response"></a>*Response*

|  名前     |  Type         |  説明 |
|  ------   |  ---------------  | ------------ |
|  200 OK   |  TokenResponse    |  要求成功。 |

##### <a name="tokenresponse"></a>*TokenResponse*

応答のサンプル:

```json
{
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

| 要素 | 説明 |
| ------- | ----------- |
| `access_token` | この要素は、すべての SaaS Fulfillment API と Marketplace 計測 API を呼び出すときに承認パラメーターとして渡す `<access_token>` です。 セキュリティで保護された REST API を呼び出すとき、トークンは `Authorization` 要求ヘッダー フィールドに "ベアラー" トークンとして埋め込まれ、API が呼び出し元を認証できるようにします。 |
| `expires_in` | アクセス トークンが発行されてから期限切れになるまでの有効継続時間 (秒単位)。 発行時刻はトークンの `iat` 要求で確認できます。 |
| `expires_on` | アクセス トークンが期限切れになるまでの期間。 日付は "1970-01-01T0:0:0Z UTC" からの秒数として表されます (トークンの `exp` 要求に対応)。 |
| `not_before` | アクセス トークンが有効になり、承認されるまでの期間。 日付は "1970-01-01T0:0:0Z UTC" からの秒数として表されます (トークンの `nbf` 要求に対応)。 |
| `resource` | アクセス トークンの要求対象リソース。要求の `resource` クエリ文字列パラメーターと一致します。 |
| `token_type` | トークンの種類。つまり "ベアラー" アクセス トークン。リソースが、このトークンのベアラーへのアクセスを提供できることを意味します。 |

## <a name="next-steps"></a>次のステップ

Azure AD で保護されたアプリで、[SaaS Fulfillment サブスクリプション API バージョン 2](pc-saas-fulfillment-subscription-api.md) および [SaaS Fulfillment 操作 API バージョン 2](pc-saas-fulfillment-operations-api.md) を使用できるようになりました。
