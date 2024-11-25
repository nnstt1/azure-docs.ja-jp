---
title: 'クイックスタート: ASP.NET Web アプリのサインインを設定する'
titleSuffix: Azure AD B2C
description: このクイックスタートでは、Azure Active Directory B2C を使用してアカウント サインインを提供するサンプル ASP.NET Web アプリを実行します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.topic: quickstart
ms.custom: devx-track-csharp, mvc
ms.date: 10/01/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: e48ce8e709a81270bc7a0fdac6d57817f582c9f2
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130035469"
---
# <a name="quickstart-set-up-sign-in-for-an-aspnet-application-using-azure-active-directory-b2c"></a>クイックスタート: Azure Active Directory B2C を使用した ASP.NET アプリケーションのサインインの設定

Azure Active Directory B2C (Azure AD B2C) は、アプリケーション、ビジネス、顧客を保護するためのクラウド ID 管理を提供します。 Azure AD B2C に対応したアプリケーションは、オープンな標準プロトコルを使用し、ソーシャル アカウントやエンタープライズ アカウントで認証を行うことができます。 

このクイック スタートでは、ASP.NET アプリケーションにソーシャル ID プロバイダーを使ってサインインし、Azure AD B2C で保護された Web API を呼び出します。



## <a name="prerequisites"></a>前提条件

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) と **ASP.NET と Web 開発** ワークロード。
- Facebook、Google、または Microsoft のソーシャル アカウント。
- [ZIP ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip)するか、GitHub からサンプル Web アプリケーションを複製します。

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
    ```

    サンプル ソリューションには次の 2 つのプロジェクトがあります。

    - **TaskWebApp** - タスク リストの作成と編集を行う Web アプリケーション。 この Web アプリケーションは、**サインアップまたはサインイン** のユーザー フローを使用してユーザーをサインアップまたはサインインします。
    - **TaskService** - タスク リストの作成、読み取り、更新、削除機能をサポートする Web API。 この Web API は Azure AD B2C によって保護されており、Web アプリケーションによって呼び出されます。

## <a name="run-the-application-in-visual-studio"></a>Visual Studio でアプリケーションを実行する

1. サンプル アプリケーションのプロジェクト フォルダーにある **B2C-WebAPI-DotNet.sln** ソリューションを Visual Studio で開きます。
2. このクイック スタートでは、**TaskWebApp** プロジェクトと **TaskService** プロジェクトの両方を同時に実行します。 ソリューション エクスプローラーで **B2C-WebAPI-DotNet** ソリューションを右クリックし、**[スタートアップ プロジェクトの設定]** を選択します。
3. **[マルチ スタートアップ プロジェクト]** を選択し、両方のプロジェクトの **[アクション]** を **[開始]** に変更します。
4. **[OK]** を選択します。
5. **F5** キーを押して両方のアプリケーションをデバッグします。 各アプリケーションは、それぞれ別のブラウザー タブで開かれます。

    - `https://localhost:44316/` - ASP.NET Web アプリケーション。 このクイック スタートでは、このアプリケーションを直接操作します。
    - `https://localhost:44332/` - ASP.NET Web アプリケーションによって呼び出される Web API。

## <a name="sign-in-using-your-account"></a>自分のアカウントを使用してサインインする

1. ASP.NET Web アプリケーションで **[サインアップ/サインイン]** を選択してワークフローを開始します。

    ![[Sign up / Sign in]\(サインアップ/サインイン\) リンクが強調表示されている、ブラウザー内のサンプル ASP.NET Web アプリ](./media/quickstart-web-app-dotnet/web-app-sign-in.png)

    このサンプルは、ソーシャル ID プロバイダーを使用する方法や、メール アドレスを使用してローカル アカウントを作成する方法など、複数のサインアップ方法に対応しています。 このクイック スタートでは、Facebook、Google、Microsoft のいずれかのソーシャル ID プロバイダー アカウントを使用します。

2. Azure AD B2C では、サンプルの Web アプリケーションに対する Fabrikam と呼ばれる架空の会社のサインイン ページが提供されます。 ソーシャル ID プロバイダーを使用してサインアップするには、使用する ID プロバイダーのボタンを選択します。

    ![ID プロバイダー ボタンが表示されたサインインまたはサインアップ ページ](./media/quickstart-web-app-dotnet/sign-in-or-sign-up-web.png)

    ユーザーは、ソーシャル アカウントの資格情報を使用して認証 (サインイン) し、アプリケーションがそのソーシャル アカウントから情報を読み取ることを承認します。 アクセスを許可することにより、アプリケーションはソーシャル アカウントからプロファイル情報 (名前やお住まいの都市など) を取得できるようになります。

3. ID プロバイダーのサインイン プロセスを完了します。

## <a name="edit-your-profile"></a>プロファイルの編集

Azure Active Directory B2C には、ユーザーが自分のプロファイルを更新することができる機能があります。 このサンプル Web アプリのワークフローには、Azure AD B2C の編集プロファイル ユーザー フローが使用されます。

1. アプリケーションのメニュー バーで、プロファイル名を選択してから **[プロファイルの編集]** を選択して、作成したプロファイルを編集します。

    ![[Edit Profile]\(プロファイルの編集\) リンクが強調表示されている、ブラウザー内のサンプル Web アプリ](./media/quickstart-web-app-dotnet/edit-profile-web.png)

2. **[表示名]** または **[市]** を変更し、 **[続行]** を選択してプロファイルを更新します。

    変更内容が Web アプリケーションのホーム ページの右上の部分に表示されます。

## <a name="access-a-protected-api-resource"></a>保護された API リソースにアクセスする

1. **[To Do リスト]** を選択して、To-Do リスト項目を入力および変更します。

2. **[新しい項目]** テキスト ボックスにテキストを入力します。 To-Do リスト項目を追加する Azure AD B2C で保護された Web API を呼び出すには、 **[追加]** を選択します。

    ![ブラウザー内のサンプル Web アプリの To-do リスト項目の追加機能](./media/quickstart-web-app-dotnet/add-todo-item-web.png)

    ASP.NET Web アプリケーションは、保護された Web API リソースへの要求に、ユーザーの To Do リスト項目に対する操作を実行するための Azure AD アクセス トークンを追加します。

Azure AD B2C ユーザー アカウントを使用して、Azure AD B2C で保護された Web API の承認済みの呼び出しを正しく行いました。


## <a name="next-steps"></a>次のステップ

[Azure Portal で Azure Active Directory B2C テナントを作成する](tutorial-create-tenant.md)
