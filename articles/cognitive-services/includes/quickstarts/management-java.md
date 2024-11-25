---
title: クイック スタート:Java 用 Azure 管理クライアント ライブラリ
description: このクイックスタートでは、Java 用の Azure 管理クライアント ライブラリの使用を開始します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/04/2021
ms.author: pafarley
ms.openlocfilehash: 1c397c1fd3078de7144d2e4cd292ce23a8510b7d
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123122762"
---
[リファレンス ドキュメント](/java/api/com.microsoft.azure.management.cognitiveservices) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/cognitiveservices/azure-resourcemanager-cognitiveservices/src/main/java/com/azure/resourcemanager/cognitiveservices) | [パッケージ (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-mgmt-cognitiveservices)

## <a name="java-prerequisites"></a>Java の前提条件

* 有効な Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)。
* 最新バージョンの [Java Development Kit(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle ビルド ツール](https://gradle.org/install/)、または別の依存関係マネージャー。
* [!INCLUDE [contributor-requirement](./contributor-requirement.md)]
* [!INCLUDE [terms-azure-portal](./terms-azure-portal.md)]


[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-java-application"></a>新しい Java アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。 

```console
mkdir myapp && cd myapp
```

作業ディレクトリから `gradle init` コマンドを実行します。 このコマンドによって、Gradle 用の重要なビルド ファイルが作成されます。たとえば、実行時にアプリケーションを作成して構成するために使用される *build.gradle.kts* などです。

```console
gradle init --type basic
```

**DSL** を選択するよう求められたら、**Kotlin** を選択します。

作業ディレクトリから、次のコマンドを実行します。

```console
mkdir -p src/main/java
```

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

このクイックスタートでは、Gradle 依存関係マネージャーを使用します。 クライアント ライブラリとその他の依存関係マネージャーの情報については、[Maven Central Repository](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) を参照してください。

プロジェクトの *build.gradle.kts* ファイルに、必要なプラグインと設定と共に、クライアント ライブラリを `implementation` ステートメントとして含めます。

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.microsoft.azure", name = "azure-mgmt-cognitiveservices", version = "1.10.0-beta")
}
```

### <a name="import-libraries"></a>ライブラリをインポートする

新しい **src/main/java** フォルダーに移動し、*Management.java* という名前のファイルを作成します。 それを任意のエディターまたは IDE で開き、以下の `import` ステートメントを追加します。

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_imports)]

## <a name="authenticate-the-client"></a>クライアントを認証する

*Management.java* にクラスを追加した後、次のフィールドと値をその中に追加します。 作成したサービス プリンシパルと他の Azure アカウント情報を使用して、値を指定します。

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_constants)]

次に、**main** メソッドでこれらの値を使用して、**CognitiveServicesManager** オブジェクトを構築します。 このオブジェクトは、すべての Azure 管理操作に必要です。

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_auth)]

## <a name="call-management-methods"></a>管理メソッドを呼び出す

**Main** メソッドに次のコードを追加して、使用可能なリソースの一覧表示、サンプル リソースの作成、所有しているリソースの一覧表示、およびサンプル リソースの削除を行います。 これらのメソッドは、以降の手順で定義します。

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource-java"></a>Cognitive Services リソースを作成する (Java)

新しい Cognitive Services リソースを作成してサブスクライブするには、**create** メソッドを使用します。 このメソッドで、渡したリソース グループに新しい課金対象リソースが追加されます。 新しいリソースを作成するときには、使用するサービスの "種類"、その価格レベル (つまり SKU)、および Azure の場所を把握している必要があります。 次のメソッドは、これらのすべてを引数として受け取り、リソースを作成します。

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>サービスと価格レベルを選択する

新しいリソースを作成するときには、使用するサービスの "種類" と、必要な[価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/) (つまり SKU) を把握する必要があります。 リソースを作成するときに、この情報と他の情報をパラメーターとして使用します。 使用できる Cognitive Service の "種類" の一覧を確認するには、次のメソッドを呼び出します。

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>リソースを表示する

Azure アカウントのすべてのリソース (すべてのリソース グループにわたる) を表示するには、次のメソッドを使用します。

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list)]

## <a name="delete-a-resource"></a>リソースの削除

次のメソッドは、特定のリソース グループから指定されたリソースを削除します。

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_delete)]

削除されたリソースを復旧する必要がある場合は、[削除された Cognitive Services リソースの復旧](../../manage-resources.md)に関するページを参照してください。

## <a name="see-also"></a>関連項目

* Cognitive Services を安全に使用する方法については、「 **[Azure Cognitive Services に対する要求の認証](../../authentication.md)** 」をご覧ください。
* Cognitive Services 内のさまざまなカテゴリの一覧を入手するには、「 **[Azure Cognitive Services とは](../../what-are-cognitive-services.md)** 」をご覧ください。
* Cognitive Services がサポートする自然言語の一覧を確認するには、 **[自然言語のサポート](../../language-support.md)** に関する記事をご覧ください。
* Cognitive Services をオンプレミスで使用する方法については、 **[コンテナーとしての Cognitive Services の使用](../../cognitive-services-container-support.md)** に関する記事をご覧ください。
* Cognitive Services の使用コストを見積もるには、 **[Cognitive Services のコストの計画および管理](../../plan-manage-costs.md)** に関する記事をご覧ください。
* Management SDK の詳細については、 **[Azure Management SDK のリファレンス ドキュメント](/java/api/com.microsoft.azure.management.cognitiveservices)** を参照してください。
