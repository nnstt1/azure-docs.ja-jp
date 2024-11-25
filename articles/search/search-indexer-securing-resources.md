---
title: 保護されたリソースへのインデクサー アクセス
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search のインデクサーが Azure データにアクセスする場合のネットワーク レベルのセキュリティ オプションに関する概念的な概要を示します。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: a541eb900648fe33beb76207da956c1489f89cb5
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485106"
---
# <a name="indexer-access-to-content-protected-by-azure-network-security-features"></a>Azure ネットワーク セキュリティ機能を使用したデータ ソースへのインデクサーのアクセス

Azure Cognitive Search インデクサーは、実行中にさまざまな Azure リソースへの発信呼び出しを行うことができます。 この記事では、IP ファイアウォール、プライベート エンドポイント、またはその他の Azure ネットワークレベルのセキュリティ メカニズムによって保護されているコンテンツへのインデクサー アクセスの背後にある概念について説明します。 

インデクサーは、次の 2 つの状況で送信呼び出しを行います。

- インデックス作成中に外部のデータ ソースに接続する場合
- スキルセットを使って外部のカプセル化されたコードに接続する場合

通常の実行でインデクサーがアクセスする可能性があるリソースの種類を次の表にリストとして示します。

| リソース | インデクサー実行内の目的 |
| --- | --- |
| Azure Storage (BLOB、テーブル、ADLS Gen 2) | データ ソース |
| Azure Storage (BLOB、テーブル) | スキルセット (エンリッチメントされたドキュメントのキャッシュとナレッジ ストアのプロジェクションの保存) |
| Azure Cosmos DB (さまざまな API) | データ ソース |
| Azure SQL データベース | データ ソース |
| Azure 仮想マシン上の SQL Server | データ ソース |
| SQL Managed Instance | データ ソース |
| Azure Functions | カスタム Web API スキルのホスト |
| Cognitive Services | 20 個の無料ドキュメント制限を超えるエンリッチメントの請求に使用されるスキルセットに関連付けられます |

> [!NOTE]
> スキルセットに関連付けられたコグニティブ サービス リソースは、実行され、検索インデックスに書き込まれたエンリッチメントに基づいて請求に使用されます。 これは、Cognitive Services API にアクセスするためには使用されません。 インデクサーのエンリッチメント パイプラインから Cognitive Services APIs へのアクセスは、セキュリティで保護された内部通信チャネルを介して行われます。ここでは、データは転送時に強力に暗号化され、保存されることはありません。

お客様は、Azure が提供する複数のネットワーク分離メカニズムを使用して、これらのリソースをセキュリティで保護できます。 コグニティブ サービス リソースを除いて、インデクサーは、以下の表に概略を示すように、ネットワークが分離されている場合でも、他のすべてのリソースにアクセスする能力に制限があります。

| リソース | IP 制限 | プライベート エンドポイント |
| --- | --- | ---- |
| Azure Storage (BLOB、テーブル、ADLS Gen 2) | ストレージ アカウントと検索サービスが異なるリージョンにある場合にのみサポートされます | サポートされています |
| Azure Cosmos DB - SQL API | サポートされています | サポートされています |
| Azure Cosmos DB - MongoDB および Gremlin API | サポートされています | サポートされていない |
| Azure SQL データベース | サポートされています | サポートされています |
| Azure 仮想マシン上の SQL Server | サポートされています | N/A |
| SQL Managed Instance | サポートされています | N/A |
| Azure Functions | サポートされています | Azure Functions の特定の層に対してのみサポートされます |

> [!NOTE]
> ネットワークの保護された Azure Storage アカウントについては、上記のオプションに加えて、お客様は Azure Cognitive Search が[信頼された Microsoft サービス](../storage/common/storage-network-security.md#trusted-microsoft-services)であるという事実を活用できます。 これは、ストレージ アカウントで適切なロールベースのアクセスの制御が有効になっている場合、特定の検索サービスがストレージ アカウントの仮想ネットワークまたは IP の制限をバイパスし、ストレージ アカウントのデータにアクセスできることを意味します。 詳細については、[信頼されたサービスの例外を使用したインデクサー接続](search-indexer-howto-access-trusted-service-exception.md)に関する記事を参照してください。 このオプションは、ストレージ アカウントまたは検索サービスを別のリージョンに移動できない場合に、IP 制限ルートの代わりに使用できます。

セキュリティ保護されたアクセス機構を選択する際は、次の制約を考慮してください。

- インデクサーは[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)に接続できません。 インデクサー接続でサポートされている方法は、資格情報、プライベート エンドポイント、信頼できるサービス、および IP アドレス指定を使ったパブリック エンドポイントだけです。
- 検索サービスは常にクラウド上で実行され、仮想マシン上でネイティブに実行されている特定の仮想ネットワークにプロビジョニングすることはできません。 この機能は、Azure Cognitive Search では提供されません。
- インデクサーが (発信) プライベート エンドポイントを使用してリソースにアクセスする場合、追加の[プライベート リンク料金](https://azure.microsoft.com/pricing/details/search/)が適用される場合があります。

## <a name="indexer-execution-environment"></a>インデクサー実行環境

Azure Cognitive Search インデクサーは、データ ソースからコンテンツを効率的に抽出し、抽出されたコンテンツにエンリッチメントを追加でき、さらにオプションで、検索インデックスに結果を書き込む前にプロジェクションを生成できます。

最適な処理を行うために、操作をセットアップするための内部的な実行環境が検索サービスによって判断されます。 ユーザーが環境を制御したり構成したりすることはできませんが、IP ファイアウォール規則を設定する際にそれらを考慮できるよう、その存在を知っておくことは大切です。

インデクサーは、割り当てられているタスクの数と種類に応じて、次の 2 つの環境のどちらかで実行されます。

- 特定の検索サービス専用の環境。 このような環境で実行されるインデクサーは、他のワークロード (お客様が開始した他のインデックス作成やクエリ実行のワークロードなど) とリソースを共有します。 通常、この環境では、テキストベースのインデックス作成 (たとえば、スキルセットを使わない) インデクサーのみが実行されます。

- スキルセットを使う場合など、リソースを集中的に使うインデクサーをホストするマルチテナント環境。 この環境は、大量のコンピューティング処理を要する処理の負荷を軽減して、サービス固有のリソースをルーチン処理に残しておくために使います。 このマルチテナント環境は、Microsoft によって管理および保護されており、お客様に追加コストはかかりません。

指定されたインデクサーの実行に対して、Azure Cognitive Search により、そのインデクサーを実行するための最適な環境が決定されます。 IP ファイアウォールを使用して Azure リソースへのアクセスを制御している場合は、実行環境について理解しておくと、両方を含む IP 範囲を設定するのに役立ちます。次のセクションでは、この点について説明します。

## <a name="granting-access-to-indexer-ip-ranges"></a>インデクサーの IP 範囲へのアクセスを許可する

インデクサーがデータをプルするリソースがファイアウォールの内側に存在する場合、インバウンド ルールの IP 範囲に、インデクサーの要求の送信元となるすべての IP を含める必要があります。 前述のように、インデクサーが実行され、アクセス要求が発生する可能性がある環境が 2 つあります。 インデクサー アクセスを機能させるために、**両方** の環境の IP アドレスを追加する必要があります。

- 検索サービス固有のプライベート環境の IP アドレスを取得するには、検索サービスの完全修飾ドメイン名 (FQDN) に対して `nslookup` (または `ping`) を使用します。 たとえば、パブリック クラウドの検索サービスの FQDN は、`<service-name>.search.windows.net` です。 この情報は、Azure portal で入手できます。

- インデクサーが実行される可能性のあるマルチテナント環境の IP アドレスを取得するには、`AzureCognitiveSearch` サービス タグを使用します。 [Azure サービス タグ](../virtual-network/service-tags-overview.md)には、サービスごとの公開された IP アドレス範囲が含まれています。 これらの IP は、[Discovery API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api) または[ダウンロード可能な JSON ファイル](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)を使用して調べることができます。 どちらのケースも、IP 範囲はリージョンごとに分類されています。 検索サービスのプロビジョニング先リージョンに割り当てられた IP 範囲だけを指定する必要があります。

特定のデータ ソースについては、IP 範囲の一覧を列挙する代わりに、サービス タグ自体を直接使用できます (検索サービスの IP アドレスは、引き続き明示的に使用する必要があります)。 これらのデータ ソースでは、[ネットワーク セキュリティ グループの規則](../virtual-network/network-security-groups-overview.md)を設定することでアクセスを制限します。これは、Azure Storage、Cosmos DB、Azure SQL などが提供する IP 規則とは異なり、サービス タグの追加をネイティブでサポートします。 検索サービスの IP アドレスに加えて、`AzureCognitiveSearch` サービス タグを直接使用する機能をサポートするデータ ソースを次に示します。

- [Azure 仮想マシン上の SQL Server](./search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md#restrict-access-to-the-azure-cognitive-search)

- [SQL マネージド インスタンス](./search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md#verify-nsg-rules)

この接続オプションの詳細については、[IP ファイアウォールを使用したインデクサー接続](search-indexer-howto-access-ip-restricted.md)に関する記事を参照してください。

## <a name="granting-access-via-private-endpoints"></a>プライベート エンドポイントを介してアクセスを許可する

ロック ダウンされているリソース (保護された仮想ネットワークで実行されている、または単にパブリック接続では利用できないなど) には、インデクサーは、接続の[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用してアクセスします。

この機能は、課金対象の検索サービス (Basic 以降) でのみ利用でき、テキストベースのインデックス作成とスキルベースのインデックス作成に関して、作成できるプライベート エンドポイントの数にはレベルごとの制限が適用されます。 詳細については、サービスの制限に関するドキュメントの[「共有プライベート リンク リソースの制限」セクション](search-limits-quotas-capacity.md#shared-private-link-resource-limits)を参照してください。

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>手順 1:セキュリティで保護されたリソースへのプライベート エンドポイントを作成する

お客様は、セキュリティで保護されたリソース (たとえば、ストレージ アカウントなど) へのプライベート エンドポイント接続を作成するために、検索管理操作 [CreateOrUpdate API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/create-or-update) を **共有プライベート リンク リソース** 上に呼び出す必要があります。 この (発信) プライベート エンドポイント接続を介して移動するトラフィックは、検索サービス固有の "プライベート" インデクサー実行環境にある仮想ネットワークからのみ発信されます。

この API の呼び出し元に、セキュリティで保護されたリソースへのプライベート エンドポイント接続要求を承認する Azure RBAC アクセス許可があるかどうかが、Azure Cognitive Search によって検証されます。 たとえば、読み取り専用のアクセス許可があるストレージ アカウントへのプライベート エンドポイント接続を要求した場合、この呼び出しは拒否されます。

### <a name="step-2-approve-the-private-endpoint-connection"></a>手順 2:プライベート エンドポイント接続を承認する

共有プライベート リンク リソースを作成する (非同期) 操作が完了すると、プライベート エンドポイント接続が "保留中" 状態で作成されます。 この接続を介してフローするトラフィックはまだありません。

お客様は、セキュリティで保護されたリソースでこの要求を見つけて、"承認" することが求められます。 通常、これを行うには、Azure portal を使用するか、[REST API](/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection) を使用します。

### <a name="step-3-force-indexers-to-run-in-the-private-environment"></a>手順 3:"プライベート" 環境でインデクサーを強制的に実行する

承認されたプライベート エンドポイントにより、検索サービスから、何らかの形式のネットワーク レベルのアクセス制限があるリソース (たとえば、特定の仮想ネットワークからのみアクセスされるように構成されたストレージ アカウントのデータ ソース) への発信呼び出しを正常に機能させることができます。

これは、プライベート エンドポイントを介してこのようなデータソースに接続できるすべてのインデクサーが正常に機能することを意味します。
プライベート エンドポイントが承認されていない場合、またはインデクサーがプライベート エンドポイント接続を使用していない場合、インデクサーの実行は `transientFailure` で終了します。

インデクサーがプライベート エンドポイント接続を介してリソースにアクセスできるようにするには、インデクサーの `executionEnvironment` を `"Private"` に設定して、すべてのインデクサーの実行でプライベート エンドポイントを使用できるようにする必要があります。 これは、プライベート エンドポイントがプライベート検索サービス固有の環境内でプロビジョニングされるためです。

```json
    {
      "name" : "myindexer",
      ... other indexer properties
      "parameters" : {
          ... other parameters
          "configuration" : {
            ... other configuration properties
            "executionEnvironment": "Private"
          }
        }
    }
```

これらの手順の詳細については、[プライベート エンドポイントを使用したインデクサー接続](search-indexer-howto-access-private.md)に関する記事を参照してください。
リソースに対して承認されたプライベート エンドポイントを使用すると、*private* に設定されているインデクサーは、プライベート エンドポイント接続を介してアクセスを取得しようとします。

## <a name="next-steps"></a>次の手順

- [IP ファイアウォールを使用したインデクサー接続](search-indexer-howto-access-ip-restricted.md)
- [信頼されたサービスの例外を使用したインデクサー接続](search-indexer-howto-access-trusted-service-exception.md)
- [プライベート エンドポイントへのインデクサー接続](search-indexer-howto-access-private.md)
