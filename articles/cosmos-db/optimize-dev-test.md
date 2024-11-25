---
title: Azure Cosmos DB での開発とテストのための最適化
description: この記事では、Azure Cosmos DB でサービスの開発およびテスト用に無料で提供されている複数のオプションについて説明します。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 60f586e3f2ead0bde743f75d7810bf2d43c737af
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123028613"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Azure Cosmos DB での開発とテストのコストを最適化する
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

この記事では、開発およびテスト用に Azure Cosmos DB を無料で使用するさまざまなオプションと、開発またはテスト アカウントでのコストを最適化する手法について説明します。

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB エミュレーター (ローカルにダウンロード可能なバージョン)

[Azure Cosmos DB エミュレーター](local-emulator.md)は Azure Cosmos DB クラウド サービスによく似ており、ローカルにダウンロードできます。 ネットワークに接続していなくても、コストをかけずに、Azure Cosmos DB API を使用してコードの記述とテストを行うことができます。 Azure Cosmos DB エミュレーターでは、クラウド サービスとほとんど同じローカル環境が開発用に提供されます。 Azure サブスクリプションを作成することなく、アプリケーションの開発とテストをローカルに行うことができます。 アプリケーションをクラウドにデプロイする準備ができたら、クラウドの Azure Cosmos DB エンドポイントに接続するように接続文字列を更新します。それ以外の変更は必要ありません。 Azure DevOps で [Azure Cosmos DB エミュレーター ビルド タスクを使用して CI/CD パイプラインを設定](tutorial-setup-ci-cd.md)し、テストを実行することもできます。 始めるには、[Azure Cosmos DB エミュレーター](local-emulator.md)に関する記事を参照してください。

## <a name="azure-cosmos-db-free-tier"></a>Azure Cosmos DB の Free レベル

Azure Cosmos DB の Free レベルを使用すると、アプリケーションの利用開始、開発、およびテストを簡単に行えるようになります。また、小規模な実稼働ワークロードを無料で実行することもできます。 アカウント上で Free レベルを有効にすると、そのアカウントでの最初の 1000 RU/s と 25 GB のストレージが無料で利用できるようになります。

Free レベルは、アカウントの有効期間中は無期限に存続し、無制限のストレージとスループット (RU/秒)、SLA、高可用性、すべての Azure リージョンでのターンキー グローバル分散など、通常の Azure Cosmos DB アカウントの[利点と機能](introduction.md#key-benefits)をすべて備えます。 Free レベル アカウントは、Azure portal、CLI、PowerShell、および Resource Manager テンプレートを使用して作成できます。 詳細については、[Free レベル アカウントの作成](free-tier.md)に関する記事および[価格に関するページ](https://azure.microsoft.com/pricing/details/cosmos-db/)を参照してください。

## <a name="try-azure-cosmos-db-for-free"></a>Azure Cosmos DB を無料で試す

「[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)」では、Azure アカウントにサインアップしたりクレジット カードを使用したりせずに、クラウドでの Azure Cosmos DB を無料のエクスペリエンスとして試すことができます。 Azure Cosmos DB 試用アカウントでは、利用期間が限られています (現在は 30 日)。 いつでも更新できます。 Azure Cosmos DB 試用アカウントを利用すると、Azure Cosmos DB の評価、アプリケーションのビルドおよびテスト、クイック スタートまたはチュートリアルの使用を簡単に行えるようになります。 また、デモの作成、単体テストの実行に加えて、複数リージョンのアカウントを作成してコストを発生させずにそのアカウント上でアプリを実行することもできます。 Azure Cosmos DB 試用アカウントでは、最大 25 個のコンテナーと 20,000 RU/秒のスループットを備えた 1 つの共有スループット データベースと、最大 5000 RU/秒の 1 つのコンテナーを利用できます。 始めるには、「[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)」ページをご覧ください。

## <a name="azure-free-account"></a>Azure 無料アカウント

Azure Cosmos DB が含まれる [Azure 無料アカウント](https://azure.microsoft.com/free)では、Azure クレジットとリソースが一定の期間無料で提供されています。 具体的には、Azure Cosmos DB の場合、この無料アカウントでは、25 GB のストレージと 400 RU のプロビジョニング済みスループットが 1 年間提供されます。 このエクスペリエンスでは、すべての開発者が簡単に無料で、Azure Cosmos DB の機能をテストしたり、他の Azure サービスと統合したりできます。 Azure の無料アカウントでは、最初の 30 日間使用できる 200 ドルのクレジットが手に入ります。 サービスの利用を開始した後でも、アップグレードするまでは無料で利用できます。 始めるには、[Azure 無料アカウント](https://azure.microsoft.com/free)のページをご覧ください。

## <a name="azure-cosmos-db-serverless"></a>Azure Cosmos DB サーバーレス

[Azure Cosmos DB サーバーレス](serverless.md)では、使用量ベースの方法で Azure Cosmos アカウントを使用できます。この方法では、データベース操作によって消費される要求ユニットと、データによって消費されるストレージに対してのみ課金されます。 Azure Cosmos DB をサーバーレス モードで使用する場合に必要となる最小料金はありません。 プロビジョニングされた容量の概念がなくなるため、とりわけ、データベースがほとんど常にアイドル状態にある開発アクティビティやテスト アクティビティに最適です。

## <a name="use-shared-throughput-databases"></a>共有スループット データベースを使用する

[共有スループット データベース](set-throughput.md#set-throughput-on-a-database)では、データベース内のすべてのコンテナーによって、そのデータベースのプロビジョニング済みスループット (RU/秒) が共有されます。 たとえば、400 RU/秒のデータベースがプロビジョニングされ、コンテナーが 4 つある場合、4 つのコンテナーすべてによって 400 RU/秒 が共有されます。 開発環境またはテスト環境では、各コンテナーへのアクセス頻度が低くなり、最小の 400 RU/秒未満しか必要にならない場合があります。その場合は、1 つの共有スループット データベース内にコンテナーを配置することで、コストを最適化できます。

たとえば、開発またはテスト アカウントに 4 つのコンテナーがあるとします。 専用のスループット (最小 400 RU/秒) で 4 つのコンテナーを作成した場合、RU/秒の合計は 1600 RU/秒になります。 一方、1 つの共有スループット データベース (最小 400 RU/秒) を作成して、そこにコンテナーを配置した場合、RU/秒の合計は 400 RU/秒になります。 一般に、共有スループット データベースは、保証されているスループットを個々のコンテナー上では必要としないシナリオに最適です。  [共有スループット データベース](set-throughput.md#set-throughput-on-a-database)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

以下の記事を参考にして、エミュレーターまたは無料の Azure Cosmos DB アカウントを使い始めることができます。

* [Azure Cosmos DB の課金内容の確認](understand-your-bill.md)の詳細について学習します
* [Azure Cosmos DB サーバーレス](serverless.md)の詳細について学習します
* [スループット コストの最適化](optimize-cost-throughput.md)の詳細について学習します
* [ストレージ コストの最適化](optimize-cost-storage.md)の詳細について学習します
* [読み取りと書き込みのコストの最適化](optimize-cost-reads-writes.md)の詳細について学習します
* [クエリ コストの最適化](./optimize-cost-reads-writes.md)の詳細について学習します
* [複数リージョンの Azure Cosmos アカウント コストの最適化](optimize-cost-regions.md)の詳細について学習します
* Azure Cosmos DB への移行のための容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
    * 既存のデータベース クラスター内の仮想コアとサーバーの数のみがわかっている場合は、[仮想コア数または仮想 CPU 数を使用した要求ユニットの見積もり](convert-vcore-to-request-unit.md)に関するページを参照してください 
    * 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](estimate-ru-with-capacity-planner.md)に関するページを参照してください