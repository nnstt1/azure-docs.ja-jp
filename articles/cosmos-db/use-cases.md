---
title: Azure Cosmos DB の一般的なユース ケースとシナリオ
description: Azure Cosmos DB の上位 5 つのユース ケースとして、ユーザーが生成したコンテンツ、イベントのログ記録、カタログ データ、ユーザー設定のデータ、およびモノのインターネット (IoT) について説明します。
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 95b11c82d8470eaf1d4b3217567a3d67509e61fb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722406"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Azure Cosmos DB の一般的なユース ケース
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

この記事では、Azure Cosmos DB のいくつかの一般的なユースケースの概要について説明します。  この記事に記載されている推奨事項は、Cosmos DB を使用してアプリケーションを開発する際の出発点として利用できます。

この記事を読むと、次の質問に回答できるようになります。 

* Azure Cosmos DB の一般的なユースケースとはどのようなものですか?
* 小売アプリケーションで Azure Cosmos DB を使用する利点は何ですか?
* モノのインターネット (IoT) システムのデータ ストアとして Azure Cosmos DB を使用する利点は何ですか?
* Web およびモバイル アプリケーションで Azure Cosmos DB を使用する利点は何ですか?

## <a name="introduction"></a>はじめに

[Azure Cosmos DB](../cosmos-db/introduction.md) は、あらゆる規模に対応する、オープン API を備えた Microsoft の高速 NoSQL データベースです。 このサービスは、顧客が任意の数の地理的リージョン間でスループットとストレージを弾力的に (かつ独立して) スケーリングできるように設計されています。 Azure Cosmos DB は、スループット、待機時間、可用性、整合性を含む包括的な[サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/cosmos-db/)を提供するための、今日の市場で最初のグローバルに分散されたデータベース サービスです。

Azure Cosmos DB は、さまざまなアプリケーションやユース ケースで使用されている、グローバルに分散されたマルチモデル データベースです。 ミリ秒レベルの高速な応答時間を必要とし、迅速かつグローバルにスケーリングする必要がある [サーバーレス](https://azure.com/serverless) アプリケーションに最適です。 複数のデータ モデル (キー値、ドキュメント、グラフ、多桁式) および [Azure Cosmos DB の MongoDB 用 API](mongodb/mongodb-introduction.md)、[SQL API](./introduction.md)、[Gremlin API](graph-introduction.md)、[Table API](table/introduction.md) を含むデータ アクセス用の多くの API を、ネイティブおよび拡張可能な方法でサポートします。 

Azure Cosmos DB を、グローバルな展開を視野に入れた高性能アプリケーションに最適なものにしている属性のいくつかを次に示します。

* Azure Cosmos DB は、高可用性とスケーラビリティのためにデータをネイティブにパーティション分割します。 Azure Cosmos DB は、すべての単一リージョン アカウントと緩やかな一貫性のすべてのマルチリージョン アカウントにおける可用性、スループット、低待機時間、一貫性に対して 99.99% の保証を提供し、すべての複数リージョン データベース アカウントに対して 99.999% の 読み取り可用性を提供します。
* Azure Cosmos DB には、待機時間の短いミリ秒レベルの応答時間を備えた、SSD でバックアップされたストレージが含まれています。
* Azure Cosmos DB の結果、一貫性のあるプレフィックス、セッション、有界整合性制約などの整合性レベルのサポートにより、完全な柔軟性と優れた費用性能比が可能になります。 整合性のレベルで Azure Cosmos DB ほど高い柔軟性を提供するデータベース サービスは存在しません。 
* Azure Cosmos DB には、ストレージとスループットを個別に測定する、データを考慮した柔軟な価格モデルが用意されています。
* Azure Cosmos DB の予約済みスループット モデルでは、基になるハードウェアの CPU/メモリ/IOP ではなく、読み取り/書き込みの数の点から考慮することができます。
* Azure Cosmos DB の設計により、1 日あたり数兆個レベルの要求という大量の要求まで拡張できます。

このように、DocumentDB は大量の読み取り/書き込みを迅速に処理する必要がある Web アプリケーション、モバイル アプリケーション、ゲーム アプリケーション、IoT アプリケーションに有用です。

## <a name="iot-and-telematics"></a>IoT とテレマティックス

IoT のユース ケースでは、データの取り込み、処理、および格納の方法にいくつかの共通パターンがあります。  まず、これらのシステムでは、さまざまなロケールのデバイス センサーから大量のデータを取り込む必要があります。 次に、これらのシステムではストリーミング データを処理および分析して、インサイトをリアルタイムで導き出しています。 その後、データはバッチ分析用にコールド ストレージにアーカイブされます。 Microsoft Azure では、Azure Cosmos DB、Azure Event Hubs、Azure Stream Analytics、Azure Notification Hub、Azure Machine Learning、Azure HDInsight、Power BI など、IoT のユース ケースに適用できる豊富なサービスを提供しています。 

:::image type="content" source="./media/use-cases/iot.png" alt-text="Azure Cosmos DB IoT リファレンス アーキテクチャ" border="false":::

大量データの取り込みは、待機時間が短く高スループットのデータ取り込みが可能な Azure Event Hubs で実行できます。 取り込んだデータを処理してリアルタイムのインサイトを得る必要がある場合は、Azure Stream Analytics にデータを投入してリアルタイムで分析できます。 アドホック クエリのためにデータを Azure Cosmos DB に読み込むことができます。 データが Azure Cosmos DB に読み込まれたら、そのデータはクエリの準備ができています。 さらに、新しいデータと既存のデータへの変更は、Change Feed で読み取ることができます。 変更フィードは、Cosmos コンテナーへの変更を順番に格納する、永続的な追加専用のログです。 Azure Cosmos DB 内のすべてのデータ、またはデータへの変更のみを、リアルタイム分析に含まれる参照データとして使用できます。 さらに、Azure Cosmos DB データを Pig、Hive、または Map/Reduce ジョブ用に HDInsight に接続することによって、データをさらに絞り込んで処理できます。  絞り込まれたデータはその後、レポート作成のために元の Azure Cosmos DB に読み込まれます。   

Azure Cosmos DB、EventHubs、および Storm を使用したサンプルの IoT ソリューションについては、「[hdinsight-storm-examples repository on GitHub (GitHub 上の hdinsight-storm-examples リポジトリ)](https://github.com/hdinsight/hdinsight-storm-examples/)」を参照してください。

IoT に対する Azure サービスの詳細については、 [モノのインターネットの作成](https://www.microsoft.com/en-us/internet-of-things)に関するページを参照してください。 

## <a name="retail-and-marketing"></a>小売とマーケティング
Azure Cosmos DB は、Windows ストアおよび XBox Live を実行する Microsoft の独自の電子商取引プラットフォームで広く使用されています。 小売り業界ではカタログ データの格納や、順次処理パイプラインのイベント ソーシングにも使用できます。

カタログ データの使用シナリオでは、人、場所、製品などのエンティティの一連の属性を保存し、照会する必要があります。 カタログ データの例には、ユーザー アカウント、製品カタログ、IoT のデバイス レジストリ、部品表システムなどがあります。 これらのデータの属性は、アプリケーションの要件によって異なり、さらに時間の経過と共に変化する場合があります。

自動車部品メーカーの製品カタログの例を考えてみましょう。 各部品には、すべて部品に共通の属性に加えて、それぞれに固有の属性があります。 また、特定の部品の属性は、翌年に新しいモデルがリリースされたときに変更されることがあります。 Azure Cosmos DB は柔軟なスキーマと階層データをサポートしているため、製品カタログ データの格納に最適です。

:::image type="content" source="./media/use-cases/product-catalog.png" alt-text="Azure Cosmos DB 小売カタログ リファレンス アーキテクチャ" border="false":::

Azure Cosmos DB は、多くの場合、その [Change Feed](change-feed.md) 機能を使用するイベント駆動アーキテクチャのイベント ソーシングに使用されます。 Change Feed 機能は、Azure Cosmos DB 用に作られた、段階的かつ確実にインサートやアップデートを読み取る (注文イベントなど) 能力をダウンストリームのマイクロサービスに提供します。 この機能を利用すれば、状態の変化するイベントのメッセージ ブローカーとして永続的なイベント ストアを提供し、多数のマイクロサービス間で注文処理ワークフローを稼働することができます ([サーバーレス Azure Functions](https://azure.com/serverless) として実装可能)。

:::image type="content" source="./media/use-cases/event-sourcing.png" alt-text="Azure Cosmos DB 注文パイプライン リファレンス アーキテクチャ" border="false":::

さらに、Azure Cosmos DB に格納されたデータは、Apache Spark ジョブによるビッグ データ分析のために HDInsight と統合できます。 Azure Cosmos DB 用の Spark コネクタの詳細については、「[Run a Spark job with Cosmos DB and HDInsight (Cosmos DB と HDInsight を使用した Spark ジョブの実行)](./create-sql-api-spark.md)」を参照してください。

## <a name="gaming"></a>Gaming
データベース層は、ゲーム アプリケーションの重要なコンポーネントです。 最近のゲームはモバイル/コンソール クライアントでグラフィック処理を行いますが、ゲーム内統計、ソーシャル メディア統合、スコアボードなどの個人向けにカスタマイズされたコンテンツの配信は、クラウドに依存しています。 多くの場合、ゲームでは魅力的なゲーム内エクスペリエンスを提供するために、読み取り/書き込みの待機時間を 1 ミリ秒にする必要があります。 ゲーム データベースは高速であることが必要であり、新しいゲームのリリース時や機能の更新時に、要求レートの急増に対処できる必要があります。

Azure Cosmos DB は、[The Walking Dead: No Man's Land](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) ([Next Games](https://www.nextgames.com/)) や [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/) などのゲームで使用されています。 Azure Cosmos DB は、ゲーム開発者に次の利点を提供します。

* Azure Cosmos DB では、パフォーマンスを柔軟にスケールアップまたはスケールダウンできます。 これにより、同時にプレイしている数十から数百万ものプレイヤーのプロファイルや統計の更新を、1 回の API 呼び出しだけで処理することができます。
* Azure Cosmos DB は、ゲーム プレイ中の遅延の回避に役立つように、ミリ秒の読み取り/書き込みをサポートしています。
* Azure Cosmos DB の自動インデックス作成により、複数のさまざまなプロパティに対してリアルタイムにフィルター処理できます。たとえば、内部のプレーヤー ID や GameCenter、Facebook、Google ID によってプレーヤーを見つけたり、ギルド内のプレーヤー メンバーシップに基づいてクエリを実行したりできます。 複雑なインデックスやシャーディング インフラストラクチャを構築する必要もありません。
* ゲーム内チャット メッセージ、ギルド メンバーシップ、ミッション完了、スコアボード、ソーシャル グラフなどのソーシャル機能は、柔軟なスキーマで簡単に実装できます。
* Azure Cosmos DB は、管理されたサービスとしてのプラットフォーム (PaaS) として、迅速な繰り返しを可能にして製品化までの時間を短縮するために必要なセットアップや管理作業が最小限に抑えられます。

:::image type="content" source="./media/use-cases/gaming.png" alt-text="Azure Cosmos DB ゲーム リファレンス アーキテクチャ" border="false":::

## <a name="web-and-mobile-applications"></a>Web アプリケーションとモバイル アプリケーション
Azure Cosmos DB は一般に Web およびモバイル アプリケーション内で使用され、ソーシャル インタラクションのモデル化、サード パーティ サービスとの統合、および豊富な個人別のエクスペリエンスの構築に適しています。 Cosmos DB SDK を使用すると、一般的な [Xamarin フレームワーク](mobile-apps-with-xamarin.md)を使って豊富な iOS および Android アプリケーションを構築できます。  

### <a name="social-applications"></a>ソーシャル アプリケーション
Azure Cosmos DB の一般的なユース ケースは、Web、モバイル、ソーシャル メディア アプリケーションにユーザーが生成したコンテンツ (UGC) の格納およびクエリの実行です。 UGC の例には、チャット セッション、ツイート、ブログの投稿、評価、コメントなどがあります。 ソーシャル メディア アプリケーション内の UGC は、多くの場合、厳格な構造による制約のない、自由形式のテキスト、プロパティ、タグ、およびリレーションシップが組み合わさったものです。 チャット、コメント、投稿などのコンテンツは Cosmos DB に格納でき、変換や複雑なオブジェクト リレーショナル マッピング層は必要ありません。  開発者がアプリケーション コードを反復処理する際、要件に合うようにデータのプロパティを簡単に追加または変更できるため、迅速な開発が促進されます。  

サード パーティ ソーシャル ネットワークを統合するアプリケーションは、これらのネットワークから届くスキーマの変更に応答する必要があります。 Cosmos DB では既定で自動的にデータにインデックスが設定されるため、データに対していつでもクエリを実行できます。 これにより、これらのアプリケーションでは、それぞれのニーズに応じて柔軟にプロジェクションを取得できます。

ソーシャル アプリケーションの多くが世界規模で実行されており、予測できない使用パターンが発生することがあります。 アプリケーション層は使用ニーズを満たすようにスケールするため、データ ストアを柔軟にスケールできることがきわめて重要です。  Cosmos DB では、Cosmos DB アカウントにデータのパーティションを追加することでスケールアウトできます。  また、複数のリージョンにわたって追加の Cosmos DB アカウントを作成することもできます。 Cosmos DB サービス リージョンの可用性については、「[Azure のリージョン](https://azure.microsoft.com/regions/#services)」を参照してください。

:::image type="content" source="./media/use-cases/apps-with-global-reach.png" alt-text="Azure Cosmos DB Web アプリ リファレンス アーキテクチャを示す図。" border="false":::

### <a name="personalization"></a>パーソナル化
今日では、最新のアプリケーションは複雑なビューとエクスペリエンスを備えています。 それらはたいてい動的で、ユーザーの設定や気分、ブランドのニーズに対応します。 そのため、アプリケーションは、UI 要素とエクスペリエンスをすばやく表示するために、個人用設定を効率的に取得できる必要があります。 

Cosmos DB でサポートされる形式である JSON は、軽量なうえに JavaScript で容易に解釈できるため、UI レイアウト データを表すのに効果的な形式です。 Cosmos DB は、チューニング可能な一貫性レベルを提供し、高速な読み取りと待機時間の短い書き込みを実現します。 これにより、個人用設定を含む UI レイアウト データを Cosmos DB に JSON ドキュメントとして格納すれば、これらのデータをネットワーク経由で効率よく取得することができます。

:::image type="content" source="./media/use-cases/personalization.png" alt-text="Azure Cosmos DB Web アプリ リファレンス アーキテクチャ" border="false":::

## <a name="next-steps"></a>次のステップ

* Azure Cosmos DB の使用を開始するには、[クイック スタート](create-sql-api-dotnet.md)をご覧ください。このクイック スタートでは、アカウントを作成して Cosmos DB を実際に使用する方法を説明しています。

* Azure Cosmos DB を使用する顧客の詳細については、「[顧客のケース スタディ](https://azure.microsoft.com/case-studies/?service=cosmos-db)」ページを参照してください。