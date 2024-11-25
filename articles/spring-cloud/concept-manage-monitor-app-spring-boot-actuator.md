---
title: Azure Spring Boot アクチュエータを使用したアプリの管理と監視
description: Spring Boot Actuator を使用してアプリの管理と監視を行う方法について説明します。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 001b88e5abe0fc8710d7c87d792de23db4135e16
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486282"
---
# <a name="manage-and-monitor-app-with-azure-spring-boot-actuator"></a>Azure Spring Boot アクチュエータを使用したアプリの管理と監視

**この記事の適用対象:** ✔️ Java

アプリに新しいバイナリをデプロイした後、機能をチェックし、実行中のアプリケーションに関する情報を確認できます。 この記事では、Azure Spring Cloud によって提供されるテスト エンドポイントから API にアクセスし、アプリの実稼働可能な機能を公開する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事では、Azure Spring Cloud サービスに問題なくデプロイして起動できる Spring Boot 2.x アプリケーションがあることを前提としています。  [クイックスタート: Azure portal を使用して Azure Spring Cloud 内の既存のアプリケーションを起動する](./quickstart.md)方法に関する記事を参照してください

## <a name="verify-app-through-test-endpoint"></a>テスト エンドポイントを介してアプリを検証する

1. **アプリケーション ダッシュボード** にアクセスし、お使いのアプリを選択してアプリの概要ページに移動します。

1. **[概要]** ペインに、**テスト エンドポイント** が表示されます。  コマンド ラインまたはブラウザーからこのエンド ポイントにアクセスして、API 応答を確認します。

1. 後のセクションで、この **テスト エンドポイント** URI が使用されることに注意してください。

>[!TIP]
> * アプリからフロントエンド ページが返され、相対パスを使用して他のファイルが参照されている場合は、テスト エンドポイントがスラッシュ (/) で終わっていることを確認します。 これにより、CSS ファイルが正しく読み込まれるようになります。
> * ブラウザーから API を表示するときに、ページを表示するためにブラウザーでログイン資格情報の入力を求められる場合は、[URL デコード](https://www.urldecoder.org/)を使用してお使いのテスト エンドポイントをデコードします。 URL デコードでは、"https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/\<app-name>/\<deployment-name>" の形式で URL が返されます。  このフォームを使用してエンドポイントにアクセスします。

## <a name="add-actuator-dependency"></a>アクチュエータの依存関係を追加する

アクチュエータを Maven ベースのプロジェクトに追加するには、'Starter' 依存関係を追加します。

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

新しいバイナリをコンパイルして、アプリにデプロイします。

## <a name="enable-production-ready-features"></a>実稼働可能な機能を有効にする

アクチュエータのエンドポイントを使用して、アプリケーションの監視と操作を行うことができます。 既定では、Spring Boot アプリケーションでは、`health` エンドポイントと `info` エンドポイントを公開して、任意のアプリケーション情報と正常性情報が表示されます。

構成と構成可能環境を観察するには、`env` エンドポイントと `configgrops` エンドポイントも有効にする必要があります。

1. アプリの **[概要]** ペインにアクセスし、[設定] メニューの **[構成]** を選択して **環境変数** の構成ページにアクセスします。
1. 次のプロパティを、"キー:値" の形式で追加します。 この環境では、Spring Actuator エンドポイント "env"、"health"、"info" が開きます。

   ```properties
   management.endpoints.web.exposure.include: env,health,info
   ```

1. **[保存]** ボタンを選択すると、アプリケーションが自動的に再起動され、新しい環境変数が読み込まれます。

アプリの [概要] ペインに戻り、[プロビジョニングの状態] が [成功] に変わるまで待ちます。  複数のインスタンスが実行されます。

> [!Note]
> アプリをパブリックに公開すると、これらのアクチュエータのエンドポイントもパブリックに公開されます。 環境変数 `management.endpoints.web.exposure.include` を削除することですべてのエンドポイントを非表示にし、`management.endpoints.web.exposure.exclude=*` を設定することができます。

## <a name="view-the-actuator-endpoint-to-view-application-information"></a>アクチュエータ エンドポイントを表示してアプリケーション情報を確認する

1. これで、URL `"<test-endpoint>/actuator/"` にアクセスして、Spring Boot Actuator によって公開されているすべてのエンドポイントを確認できます。
1. URL `"<test-endpoint>/actuator/env"` にアクセスすると、アプリによって使用されているアクティブなプロファイルと、読み込まれるすべての環境変数を確認できます。
1. 特定の環境を検索する場合は、URL `"<test-endpoint>/actuator/env/{toMatch}"` にアクセスして表示できます。

組み込みのエンドポイントをすべて表示するには、「[エンドポイントの公開](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-endpoints-exposing-endpoints)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Spring Cloud のメトリックについて](./concept-metrics.md)
* [Azure Spring Cloud でのアプリの状態の概要](./concept-app-status.md)
