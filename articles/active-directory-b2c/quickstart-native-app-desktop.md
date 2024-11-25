---
title: 'クイック スタート: デスクトップ アプリのサインインを設定する'
titleSuffix: Azure AD B2C
description: このクイックスタートでは、Azure Active Directory B2C を使用してアカウント サインインを提供するサンプル WPF デスクトップ アプリケーションを実行します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/16/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 964de300a9a37a4a2d5248778fe0e81d3d05fd3c
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130035583"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>クイック スタート: Azure Active Directory B2C を使用したデスクトップ アプリのサインインの設定

Azure Active Directory B2C (Azure AD B2C) は、アプリケーション、ビジネス、顧客を保護するためのクラウド ID 管理を提供します。 Azure AD B2C に対応したアプリケーションは、オープンな標準プロトコルを使用し、ソーシャル アカウントやエンタープライズ アカウントで認証を行うことができます。 このクイック スタートでは、WPF (Windows Presentation Foundation) デスクトップ アプリケーションにソーシャル ID プロバイダーを使ってサインインし、Azure AD B2C で保護された Web API を呼び出します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) と **ASP.NET と Web 開発** ワークロード。
- Facebook、Google、Microsoft のいずれかのソーシャル アカウント。
- GitHub から [ZIP ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip)するか、[Azure-Samples/active-directory-b2c-dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) リポジトリをクローンします。

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>Visual Studio でアプリケーションを実行する

1. サンプル アプリケーションのプロジェクト フォルダーにある **active-directory-b2c-wpf.sln** ソリューションを Visual Studio で開きます。
2. [NuGet パッケージを復元します](/nuget/consume-packages/package-restore)。
3. **F5** キーを押してアプリケーションをデバッグします。

## <a name="sign-in-using-your-account"></a>自分のアカウントを使用してサインインする

1. **[Sign in]\(サインイン\)** をクリックして、**サインアップまたはサインイン** ワークフローを開始します。

    ![サンプル WPF アプリケーションのスクリーンショット](./media/quickstart-native-app-desktop/wpf-sample-application.png)

    このサンプルは、いくつかのサインアップ オプションをサポートしています。 これらのオプションには、ソーシャル ID プロバイダーの使用や、メール アドレスを使用したローカル アカウントの作成が含まれます。 このクイック スタートでは、Facebook、Google、Microsoft のいずれかのソーシャル ID プロバイダー アカウントを使用します。


2. Azure AD B2C では、サンプルの Web アプリケーションに対する Fabrikam と呼ばれる架空の会社のサインイン ページが提供されます。 ソーシャル ID プロバイダーを使用してサインアップするには、使用する ID プロバイダーのボタンをクリックします。

    ![ID プロバイダーが表示されたサインインまたはサインアップ ページ](./media/quickstart-native-app-desktop/sign-in-or-sign-up-wpf.png)

    ユーザーは、ソーシャル アカウントの資格情報を使用して認証 (サインイン) し、アプリケーションがそのソーシャル アカウントから情報を読み取ることを承認します。 アクセスを許可することにより、アプリケーションはソーシャル アカウントからプロファイル情報 (名前やお住まいの都市など) を取得できるようになります。

2. ID プロバイダーのサインイン プロセスを完了します。

    新しいアカウントのプロファイルの詳細には、ソーシャル アカウントからの情報があらかじめ設定されています。

## <a name="edit-your-profile"></a>プロファイルの編集

Azure AD B2C には、ユーザーが自分のプロファイルを更新することができる機能があります。 このサンプル Web アプリのワークフローには、Azure AD B2C の編集プロファイル ユーザー フローが使用されます。

1. 作成したプロファイルを編集するには、アプリケーションのメニュー バーで **[プロファイルの編集]** をクリックします。

    ![[プロファイルの編集] ボタンが強調表示されている WPF サンプル アプリ](./media/quickstart-native-app-desktop/edit-profile-wpf.png)

2. 作成したアカウントに関連付けられている ID プロバイダーを選択します。 たとえば、アカウントの作成時に ID プロバイダーとして Facebook を使用した場合は、Facebook を選択して、関連付けられているプロファイルの詳細を変更します。

3. **表示名** や **都市** を変更し、**[Continue]\(続行\)** をクリックします。

    新しいアクセス トークンが *[Token info]\(トークン情報\)* テキスト ボックスに表示されます。 プロファイルに対する変更を確認する場合は、アクセス トークンをコピーしてトークン デコーダー https://jwt.ms に貼り付けます。

## <a name="access-a-protected-api-resource"></a>保護された API リソースにアクセスする

**[Call API]\(API の呼び出し\)** をクリックして、保護されたリソースに対して要求を送信します。

![API の呼び出し](./media/quickstart-native-app-desktop/call-api-wpf.png)

このアプリケーションは、保護された Web API リソースへの要求に Azure AD アクセス トークンを追加します。 Web API からは、アクセス トークンに含まれている表示名が返されます。

Azure AD B2C ユーザー アカウントを使用して、Azure AD B2C で保護された Web API の承認済みの呼び出しを正しく行いました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

他の Azure AD B2C クイックスタートやチュートリアルを試す場合は、Azure AD B2C テナントを使用できます。 不要になったら、[Azure AD B2C テナントを削除する](faq.yml#how-do-i-delete-my-azure-ad-b2c-tenant-)ことができます。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、サンプルのデスクトップ アプリケーションを使用して次のことを行いました。

* カスタム ログイン ページを使用してサインインする
* ソーシャル ID プロバイダーを使用してサインインする
* Azure AD B2C アカウントを作成する
* Azure AD B2C によって保護された Web API を呼び出す

独自の Azure AD B2C テナントを作成してみましょう。

> [!div class="nextstepaction"]
> [Azure Portal で Azure Active Directory B2C テナントを作成する](tutorial-create-tenant.md)
