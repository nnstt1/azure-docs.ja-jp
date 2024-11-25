---
title: 'クイックスタート: Java Web アプリに "Microsoft でサインイン" を追加する | Azure'
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、OpenID Connect を使用して、Java Web アプリケーションに "Microsoft でサインイン" を追加する方法について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/22/2021
ROBOTS: NOINDEX
ms.author: marsma
ms.custom: aaddev, "scenarios:getting-started", "languages:Java", devx-track-java, mode-api
ms.openlocfilehash: fa85851bba852448550aa3a4c392a329af012eaf
ms.sourcegitcommit: 3f20f370425cb7d51a35d0bba4733876170a7795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2022
ms.locfileid: "137804890"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>クイック スタート:Java Web アプリに "Microsoft でサインイン" を追加する

このクイックスタートでは、Java Web アプリケーションでユーザーをサインインし、Microsoft Graph API を呼び出す方法を示すコード サンプルをダウンロードして実行します。 Azure Active Directory (Azure AD) 組織のユーザーはアプリケーションにサインインできます。

 概要については、[このサンプルのしくみの図](#how-the-sample-works)を参照してください。

## <a name="prerequisites"></a>前提条件

このサンプルを実行するには、以下が必要です。

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 以降。
- [Maven](https://maven.apache.org/)。


#### <a name="step-1-configure-your-application-in-the-azure-portal"></a>手順 1:Azure portal でのアプリケーションの構成

このクイックスタートのコード サンプルを使用するには、次のことを行います。

1. 応答 URL として `https://localhost:8443/msal4jsample/secure/aad` および `https://localhost:8443/msal4jsample/graph/me` を追加します。
1. クライアント シークレットを作成します。
> [!div class="nextstepaction"]
> [これらの変更を行います]()

> [!div class="alert alert-info"]
> ![構成済み](media/quickstart-v2-aspnet-webapp/green-check.png) アプリケーションはこれらの属性で構成されています。

#### <a name="step-2-download-the-code-sample"></a>手順 2:コード サンプルのダウンロード

プロジェクトをダウンロードし、.ZIP ファイルを、自分のドライブのルート付近にあるフォルダーに抽出します (例: *C:\Azure-Samples*)。

localhost で HTTPS を使用するには、`server.ssl.key` プロパティを指定します。 自己署名証明書を生成するには、keytool ユーティリティ (JRE に含まれています) を使用します。

次に例を示します。
```
  keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password

  server.ssl.key-store-type=PKCS12
  server.ssl.key-store=classpath:keystore.p12
  server.ssl.key-store-password=password
  server.ssl.key-alias=testCert
  ```
  生成されたキーストア ファイルを *resources* フォルダーに配置します。

> [!div class="sxs-lookup nextstepaction"]
> [コード サンプルをダウンロードします](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div class="sxs-lookup"]

#### <a name="step-3-run-the-code-sample"></a>手順 3:コード サンプルの実行

プロジェクトを実行するには、次のいずれかの手順を実行します。

- 埋め込みの Spring Boot サーバーを使用して IDE から直接実行する。
- [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) を使用して WAR ファイルにパッケージ化したうえで、[Apache Tomcat](http://tomcat.apache.org/) などの J2EE コンテナー ソリューションにデプロイする。

##### <a name="running-the-project-from-an-ide"></a>IDE からのプロジェクトの実行

IDE から Web アプリケーションを実行するには、[実行] を選択し、プロジェクトのホーム ページに移動します。 このサンプルでは、標準のホームページ URL は https://localhost:8443 です。

1. 前面のページで、 **[ログイン]** ボタンを選択してユーザーを Azure Active Directory にリダイレクトし、資格情報の入力を求めます。

1. ユーザーは認証後、`https://localhost:8443/msal4jsample/secure/aad` にリダイレクトされます。 ユーザーがサインインしたので、ページにユーザー アカウントに関する情報が表示されます。 サンプル UI には、次のボタンがあります。
    - "**サインアウト**": 現在のユーザーをアプリケーションからサインアウトし、ホーム ページにリダイレクトします。
    - **Show User Info (ユーザー情報の表示)** :Microsoft Graph のトークンを取得し、そのトークンを含む要求で Microsoft Graph を呼び出します。これにより、サインインしたユーザーに関する基本情報が返されます。

##### <a name="running-the-project-from-tomcat"></a>Tomcat からのプロジェクトの実行

Web サンプルを Tomcat にデプロイする場合は、ソース コードにいくつかの変更を加えます。

1. *ms-identity-java-webapp/src/main/java/com.microsoft.azure.msalwebsample/MsalWebSampleApplication* を開きます。

    - すべてのソース コードを削除し、こちらのコードで置き換えます。

      ```Java
       package com.microsoft.azure.msalwebsample;

       import org.springframework.boot.SpringApplication;
       import org.springframework.boot.autoconfigure.SpringBootApplication;
       import org.springframework.boot.builder.SpringApplicationBuilder;
       import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

       @SpringBootApplication
       public class MsalWebSampleApplication extends SpringBootServletInitializer {

        public static void main(String[] args) {
         SpringApplication.run(MsalWebSampleApplication.class, args);
        }

        @Override
        protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
         return builder.sources(MsalWebSampleApplication.class);
        }
       }
      ```

2.   Tomcat の既定の HTTP ポートは 8080 ですが、ポート 8443 経由の HTTPS 接続が必要です。 この設定を構成するには:
        - *tomcat/conf/system.xml* にアクセスします。
        - `<connector>` タグを検索し、既存のコネクタをこのコネクタで置き換えます。

          ```xml
          <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
          ```

3. コマンド プロンプト ウィンドウを開きます。 このサンプルのルート フォルダー (pom.xml ファイルが配置されている場所) に移動し、`mvn package` を実行してプロジェクトをビルドします。
    - このコマンドを実行すると、 */targets* ディレクトリに *msal-web-sample-0.1.0.war* ファイルが生成されます。
    - このファイルの名前を *msal4jsample.war* に変更します。
    - Tomcat またはその他の J2EE コンテナー ソリューションを使用して、WAR ファイルをデプロイします。
        - msal4jsample.war ファイルをデプロイするには、これを Tomcat インストールの下にある */webapps/* ディレクトリにコピーしてから、Tomcat サーバーを起動します。

4. ファイルがデプロイされたら、ブラウザーを使用して https://localhost:8443/msal4jsample に移動します。

> [!IMPORTANT]
> このクイックスタート アプリケーションでは、クライアント シークレットを使用して、それ自体を機密クライアントとして識別します。 クライアント シークレットはプレーンテキストとしてプロジェクト ファイルに追加されるため、セキュリティ上の理由から、アプリケーションを運用環境で使用する前に、クライアント シークレットの代わりに証明書を使用することをお勧めします。 証明書の使用方法の詳細については、「[アプリケーションを認証するための証明書資格情報](./active-directory-certificate-credentials.md)」を参照してください。

## <a name="more-information"></a>詳細情報

### <a name="how-the-sample-works"></a>このサンプルのしくみ
![このクイックスタートで生成されたサンプル アプリの動作を示す図。](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="get-msal"></a>MSAL の取得

MSAL for Java (MSAL4J) は Java ライブラリであり、ユーザーをサインインさせるために使用されたり、Microsoft ID プラットフォームによって保護されている API へのアクセスに使用されるトークンの要求に使用されたりします。

Maven または Gradle を使用して、アプリケーションに MSAL4J を追加し、アプリケーションの pom.xml (Maven) または build.gradle (Gradle) ファイルに対して以下の変更を行うことで、依存関係を管理します。

pom.xml 内:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

build.gradle 内:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="initialize-msal"></a>MSAL の初期化

MSAL4J を使用するファイルの先頭に次のコードを追加して、MSAL for Java への参照を追加します。

```Java
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次のステップ

Microsoft ID プラットフォームでユーザーをサインインさせる Web アプリの構築方法の詳細については、複数のパートから構成されるシナリオ シリーズを参照してください。

> [!div class="nextstepaction"]
> [シナリオ: ユーザーをサインインさせる Web アプリ](scenario-web-app-sign-user-overview.md?tabs=java)
