---
title: Azure Marketplace でのコンテナー オファーの発行ガイド
description: この記事では、Azure Marketplace でコンテナー オファーを発行するための要件を説明します。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/30/2021
ms.openlocfilehash: 43c569204f879e865eb5bd4f040c96e907e63abb
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108144711"
---
# <a name="plan-an-azure-container-offer"></a>Azure コンテナー オファーを計画する

Azure Container オファーは、Azure Marketplace にコンテナー イメージを発行するのに役立ちます。 このガイドでは、このオファーの種類の要件について説明します。

Azure Container オファーは、Azure Marketplace を通じてデプロイおよび課金されるトランザクション オファーです。 ユーザーに表示される登録情報オプションは、 **[今すぐ入手する]** です。

対象となるソリューションが、Kubernetes ベースの Azure Container インスタンスとして設定される Docker コンテナー イメージであるときは、Azure Container オファーの種類を使用します。

> [!NOTE]
> Azure コンテナー インスタンスは、Azure でコンテナーを実行する最速で最も簡単な方法を提供する実行時 docker インスタンスです。仮想マシンを管理したり、より高度なサービスの採用したりする必要はありません。 コンテナー インスタンスは、Azure に直接デプロイすることも、Azure Kubernetes Services または Azure Kubernetes Service Engine によって調整することもできます。  

## <a name="licensing-options"></a>ライセンス オプション

Azure コンテナー オファーで使用可能なライセンス オプションは次のとおりです。

| ライセンス オプション | トランザクション プロセス |
| --- | --- |
| Free | 無料のオファーの一覧を顧客に提示します。 |
| BYOL | ライセンス持ち込みオプションを使用すると、顧客は既存のソフトウェア ライセンスを Azure に持ち込むことができます。\* |
|

\* 公開元は、ソフトウェア ライセンス トランザクションのすべての側面 (注文、フルフィルメント、使用状況測定、課金、請求、支払い、収集を含みますが、これらに限定されません) をサポートします。

## <a name="customer-leads"></a>潜在顧客

パートナー センターを使用してオファーをコマーシャル マーケットプレースに公開している場合、それをカスタマー リレーションシップ マネジメント (CRM) システムに接続することができます。 これにより、自社の製品に顧客が関心を示したり、製品を使用したりした場合はすぐにその顧客の連絡先情報を受信できるようになります。 体験版を有効にする場合は、CRM に接続する必要があります。それ以外の場合、CRM への接続は任意です。 パートナー センターでは、Azure テーブル、Dynamics 365 Customer Engagement、HTTPS エンドポイント、Marketo、および Salesforce がサポートされています。

## <a name="legal-contracts"></a>法的契約

顧客の調達プロセスを簡素化し、ソフトウェア ベンダーの法務の複雑さを軽減するため、Microsoft では、コマーシャル マーケットプレースでオファーに使用できる標準契約を用意しています。 標準契約の下でソフトウェアを提供すると、顧客はそれを読んで一度承諾するだけで済み、提供元は独自の使用条件を作成する必要はありません。

標準契約ではなく、独自の使用条件を指定することもできます。 顧客は、オファーを試す前にこれらの条件に同意する必要があります。

## <a name="offer-listing-details"></a>オファー登録情報の詳細

> [!NOTE]
> オファーの説明が「このアプリケーションは、[英語以外の言語] でのみ利用可能です」という文言で始まっている場合、オファー登録情報は英語である必要はありません。

オファーの作成をより簡単にするには、これらの項目をあらかじめ準備します。 特に明記されている場合を除き、すべて必須です。

- **名前** - 名前は、コマーシャル マーケットプレースでオファー登録情報のタイトルとして表示されます。 この名前は商標登録されている場合があります。 これは、絵文字 (商標および著作権マークの場合を除く) を使用できず、50 文字に制限されています。
- **Search results summary (検索結果の概要)** - オファーの目的または機能を、100 文字以内で改行のない単一文として記載します。 これは、コマーシャル マーケットプレースの登録情報の検索結果で使用されます。
- **簡単な説明** – オファーの目的または機能の詳細。改行なしでプレーンテキストで記述されます。 これは、オファーの詳細ページに表示されます。
- **説明** - この説明は、コマーシャル マーケットプレースの登録情報の概要に表示されます。 価値提案、主なメリット、対象となるユーザー ベース、カテゴリまたは業界との関連性、アプリ内の購入機会、必要な情報開示、詳細情報へのリンクを含めることを検討してください。 このテキスト ボックスには、説明をより魅力的にするリッチ テキスト エディター コントロールが用意されています。 必要に応じて、書式設定に HTML タグを使用します。
- **プライバシー ポリシー リンク** - 会社のプライバシー ポリシーの URL。 ご自身でアプリがプライバシーに関する法律および規則に準拠するようにする必要があります。
- **役に立つリンク** (省略可能): オファーのユーザー向けのさまざまなリソースへのリンク。 たとえば、フォーラム、FAQ、リリース ノートなどがあります。
- **連絡先情報**
  - **サポートの連絡先** – 顧客がチケットを開いたときに Microsoft パートナーが使用する名前、電話番号、およびメール アドレス。 サポート Web サイトの URL を含めます。
  - **エンジニアリングの連絡先** – オファーに問題がある場合に直接使用する、Microsoft の名前、電話番号、メール アドレス。 この連絡先情報は、コマーシャル マーケットプレースには表示されません。
  - **CSP プログラムの連絡先** (省略可能): 名前、電話番号、およびメール アドレス (CSP プログラムにオプトインする場合)。これにより、これらのパートナーが質問がある場合に問い合わせることができます。 マーケティング資料の URL も含めることができます。
- **メディア**
    - **ロゴ** – **大** ロゴ用の PNG ファイル。 パートナー センターではこれを使用して、その他の必要なロゴ サイズを作成します。 必要に応じて、別の画像に置き換えることもできます。
    - **スクリーンショット** – オファーがどのように機能するかを示す最小 1 つ、最大 5 つのスクリーンショット。 画像は、1280 x 720 ピクセル (PNG 形式) で、キャプションが含まれている必要があります。
    - **ビデオ** (省略可能) - オファーをデモンストレーションする最大 4 つのビデオ。 名前、YouTube または Vimeo の URL、1280 x 720 ピクセルの PNG サムネイルを含めます。

> [!Note]
> コマーシャル マーケットプレースに公開するには、オファーが一般的な[コマーシャル マーケットプレースの認定ポリシー](/legal/marketplace/certification-policies#100-general)を満たしている必要があります。

## <a name="preview-audience"></a>プレビュー対象ユーザー

プレビュー対象ユーザーは、オンライン ストアで公開される前にオファーにアクセスして、公開前にエンドツーエンドの機能をテストできます。 **[プレビュー対象ユーザー]** ページで、限定されたプレビュー対象ユーザーを定義できます。

Azure サブスクリプション ID に招待状を送信できます。 最大 10 個の ID を手動で追加するか、.csv ファイルを使用して最大 100 個をインポートします。 オファーが既に公開されている場合も、オファーの変更や更新をテストするためにプレビュー対象ユーザーを定義することができます。

## <a name="plans-and-pricing"></a>プランと価格

コンテナー オファーには、1 つ以上のプランが必要です。 プランでは、ソリューションのスコープと制限事項を定義します。 オファーに対して複数のプランを作成して、技術およびライセンスのさまざまなオプションを顧客に提供することができます。 

コンテナーでは、Free またはライセンス持ち込み (BYOL) の 2 つのライセンス モデルがサポートされています。 BYOL は、お客様が顧客に直接請求し、Microsoft はお客様に料金を一切請求しないことを意味します。 Microsoft は、Azure インフラストラクチャの使用料金のみを管理します。 詳細については、「[コマーシャル マーケットプレースの販売機能](marketplace-commercial-transaction-capabilities-and-considerations.md)」を参照してください。

## <a name="additional-sales-opportunities"></a>その他の営業案件

Microsoft がサポートするマーケティングおよびセールス チャネルのオプトインを選択できます。 パートナー センターでオファーを作成すると、プロセスの終盤で、次の 2 つのタブが表示されます。

- **CSP を通じた再販 (Resell through CSPs)** – Microsoft クラウド ソリューション プロバイダー (CSP) パートナーが、バンドルされたオファーの一部としてソリューションを再販できるようにします。 このプログラムの詳細については、「[クラウド ソリューション プロバイダー プログラム](cloud-solution-providers.md)」を参照してください。
- **Microsoft との共同販売** - Microsoft セールス チームが顧客のニーズを評価するときに、IP の共同販売対象ソリューションを検討できます。 共同販売の資格の詳細については、[共同販売の状態の要件](/legal/marketplace/certification-policies)に関する記事を参照してください。 評価のためにオファーを準備する方法の詳細については、[パートナー センターの [共同販売] オプション](./co-sell-configure.md)に関する記事を参照してください。

## <a name="container-offer-requirements"></a>コンテナー オファーの要件

| 要件 | 詳細 |  
|:--- |:--- |  
| 請求/メータリング | 無料または BYOL のどちらかの課金モデルをサポートします。 |
| Dockerfile からビルドされたイメージ | コンテナー イメージは、Docker イメージの仕様に基づき、Dockerfile からビルドされる必要があります。 Docker イメージのビルドに関する詳細については、「[Dockerfile reference](https://docs.docker.com/engine/reference/builder/#usage)」(Dockerfile リファレンス) の「Usage」(使用方法) セクションを参照してください。 |
| Azure Container Registry リポジトリでのホスティング | コンテナー イメージは、Azure Container Registry リポジトリでホストされる必要があります。 Azure Container Registry の操作の詳細については、[クイック スタート: Azure portal を使用したプライベート コンテナー レジストリの作成](../container-registry/container-registry-get-started-portal.md)に関するページを参照してください。<br><br> |
| イメージのタグ付け | コンテナー イメージには、少なくとも 1 つのタグを含める必要があります (タグの最大数: 16)。 イメージへのタグ付けに関する詳細については、[Docker ドキュメント](https://docs.docker.com/engine/reference/commandline/tag) サイトの `docker tag` に関するページを参照してください。 |

## <a name="next-steps"></a>次のステップ

- [技術資産を準備する](azure-container-technical-assets.md)