---
title: 概要 - Azure 仮想ネットワークにアクセスする
description: 統合サービス環境 (ISE) を使用して、Azure Logic Apps から Azure 仮想ネットワーク (VNET) にアクセスする方法について説明します
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 05/16/2021
ms.openlocfilehash: 6abcc030d77f9b7d06f9d5f43d32611a0670053b
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130162372"
---
# <a name="access-to-azure-virtual-networks-from-azure-logic-apps-using-an-integration-service-environment-ise"></a>統合サービス環境 (ISE) を使用して Azure Logic Apps から Azure 仮想ネットワークにアクセスする

ロジック アプリ ワークフローで、Azure 仮想ネットワーク内にあるか、それに接続されている保護されたリソース (仮想マシン (VM) や他のシステムまたはサービスなど) へのアクセスが必要になる場合があります。 通常はマルチテナント Azure Logic Apps で実行されるワークフローからこれらのリソースに直接アクセスするために、代わりに "*統合サービス環境*" (ISE) でロジック アプリを作成して実行できます。 ise は、実際には、グローバルマルチテナント Azure 環境とは別に専用リソースで実行される Azure Logic Apps のインスタンスであり、[ISE をデプロイするリージョンの外部にデータを格納、処理、またはレプリケート](https://azure.microsoft.com/global-infrastructure/data-residency#select-geography)することはありません。

たとえば、一部の Azure 仮想ネットワークでは、プライベート エンドポイント ([Azure Private Link](../private-link/private-link-overview.md)) を使用して、Azure でホストされている Azure PaaS サービス (Azure Storage、Azure Cosmos DB、Azure SQL Database など)、パートナー サービス、カスタマー サービスへのアクセスを提供します。 ロジック アプリ ワークフローでプライベート エンドポイントを使用する仮想ネットワークにアクセスする必要がある場合は、次のオプションがあります。

* リソースの種類として **ロジック アプリ (従量課金)** を使用してワークフローを開発し、ワークフローでプライベート エンドポイントを使用する必要がある場合は、ISE でロジック アプリを作成、デプロイ、実行する "*必要があります*"。 詳細については、「[統合サービス環境 (ISE) を使用して Azure Logic Apps から Azure Virtual Network に接続する](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)」を参照してください。

* リソースの種類として **ロジック アプリ (Standard)** を使用してワークフローを開発し、ワークフローでプライベート エンドポイントを使用する必要がある場合、ISE は不要です。 代わりに、ワークフローでは、受信トラフィックにプライベート エンドポイントを使用し、送信トラフィックに仮想ネットワーク統合を使用して、仮想ネットワークとプライベートかつ安全に通信できます。 詳細については、[プライベート エンドポイントを使用した仮想ネットワークとシングルテナント Azure Logic Apps 間のトラフィックのセキュリティ保護](secure-single-tenant-workflow-virtual-network-private-endpoint.md)に関する記事を参照してください。

詳細については、[マルチテナント Azure Logic Apps と統合サービス環境の違い](logic-apps-overview.md#resource-environment-differences)を確認してください。

## <a name="how-an-ise-works-with-a-virtual-network"></a>ISE と仮想ネットワークの連携

ISE を作成するときに、Azure で ISE を "*挿入*" またはデプロイする Azure 仮想ネットワークを選択します。 この仮想ネットワークへのアクセスを必要とするロジック アプリと統合アカウントを作成するときに、そのロジック アプリと統合アカウントのホストの場所として ISE を選択できます。 ISE 内では、ロジック アプリは、マルチテナント Azure Logic Apps 環境内の他のリソースとは別に、専用リソースで実行されます。 ISE 内のデータは、[その ISE を作成して展開するリージョンと同じ場所](https://azure.microsoft.com/global-infrastructure/data-residency/)にあります。

![統合サービス環境を選択する](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Azure Storage によって使用される暗号化キーをより詳細に制御するには、[Azure Key Vault](../key-vault/general/overview.md) を使用して、独自のキーを設定、使用、管理できます。 この機能は "Bring Your Own Key" (BYOK) とも呼ばれ、キーは "カスタマー マネージド キー" と呼ばれます。 詳細については、「[Azure Logic Apps の統合サービス環境 (ISE) の保存データを暗号化するためにカスタマー マネージド キーを設定する](../logic-apps/customer-managed-keys-integration-service-environment.md)」を参照してください。

この概要では、[ISE を使用する理由](#benefits)、[専用の場合とマルチテナントの場合の Logic Apps サービスの違い](#difference)、Azure 仮想ネットワーク内にあるリソース、または仮想ネットワークに接続されているリソースに直接アクセスできる方法について詳しく説明します。

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>ISE を使用する理由

分離された自分専用のインスタンスでロジック アプリを実行することで、他の Azure テナントが自分のアプリのパフォーマンスに与える可能性がある影響 (["うるさい隣人" エフェクト](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)とも呼ばれる) を減らすことができます。 ISE には、次の利点があります。

* 仮想ネットワーク内にあるリソース、または仮想ネットワークに接続されているリソースへの直接アクセス

  ISE で作成して実行するロジック アプリでは、[ISE で実行される特別に設計されたコネクタ](../connectors/managed.md#ise-connectors)を使用できます。 オンプレミスのシステムまたはデータ ソース用の ISE コネクタが存在する場合は、[オンプレミスのデータ ゲートウェイ](../logic-apps/logic-apps-gateway-connection.md)を使用しなくても、直接接続できます。 詳細については、このトピックの後の方の「[専用とマルチテナント](#difference)」および「[オンプレミス システムへのアクセス](#on-premises)」のセクションを参照してください。

* 仮想ネットワーク外にあるリソース、または仮想ネットワークに接続されていないリソースへの継続的なアクセス

  ISE で作成して実行するロジック アプリでは、ISE 固有のコネクタが使用できない場合、マルチテナント Logic Apps サービスで実行されているコネクタを引き続き使用できます。 詳細については、「[専用とマルチテナント](#difference)」のセクションを参照してください。

* 自分専用の静的 IP アドレス。これらの IP アドレスは、マルチテナント サービスのロジック アプリによって共有される静的 IP アドレスとは区別されています。 また、送信先システムとの通信のために、単一の予測可能な静的パブリック アウトバウンド IP アドレスを自分で設定することもできます。 このようにすると、それらの送信先システムで ISE ごとに追加のファイアウォールを設定する必要はありません。

* 実行継続時間、ストレージのリテンション期間、スループット、HTTP の要求と応答のタイムアウト、メッセージのサイズ、カスタム コネクタの要求の上限が引き上げられました。 詳細については、[Azure Logic Apps の制限と構成](logic-apps-limits-and-config.md)に関するページを参照してください。

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>専用とマルチテナント

ISE にロジック アプリを作成して実行すると、マルチテナント Logic Apps サービスと同じユーザー エクスペリエンスおよび同様の機能が得られます。 マルチテナント Logic Apps サービスで使用できるのと同じすべての組み込みトリガー、アクション、マネージド コネクタを使用できます。 一部のマネージド コネクタでは、追加の ISE バージョンが提供されます。 ISE コネクタと非 ISE コネクタの違いは、ISE 内で作業するときにロジック アプリ デザイナーに付けられるラベルと実行される場所です。

![ISE 内のラベルが付いているコネクタと付いていないコネクタ](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* HTTP のような組み込みのトリガーとアクションには、**CORE** というラベルが表示され、これらはロジック アプリと同じ ISE で実行されます。

* **ISE** というラベルが表示されるマネージド コネクタは、ISE 用に特別に設計されており、"*常にロジック アプリと同じ ISE で実行されます*"。 たとえば、以下に [ISE のバージョンを提供するコネクタ](../connectors/managed.md#ise-connectors)をいくつか示します。<p>

  * Azure Blob Storage、Azure File Storage、Azure Table Storage
  * Azure Service Bus、Azure Queues、Azure Event Hubs
  * Azure Automation、Azure Key Vault、Azure Event Grid、および Azure Monitor ログ
  * FTP、SFTP-SSH、File System、および SMTP
  * SAP、IBM MQ、IBM DB2、および IBM 3270
  * SQL Server、Azure Synapse Analytics、Azure Cosmos DB
  * AS2、X12、EDIFACT

  オンプレミスのシステムまたはデータ ソース用の ISE コネクタを使用できる場合は、ほぼ例外なく、[オンプレミスのデータ ゲートウェイ](../logic-apps/logic-apps-gateway-connection.md)を使用せずに、直接接続できます。 詳細については、このトピックの後の方の「[オンプレミス システムへのアクセス](#on-premises)」のセクションを参照してください。

* **ISE** というラベルが表示されないマネージド コネクタは、ISE 内のロジック アプリのために、引き続き機能します。 これらのコネクタは、ISE 内ではなく、"*常にマルチテナント Logic Apps サービスで実行されます*"。

* "*ISE の外部*" で作成するカスタム コネクタは、[オンプレミス データ ゲートウェイ](../logic-apps/logic-apps-gateway-connection.md)が必要かどうかにかかわらず、ISE 内のロジック アプリのために、引き続き機能します。 ただし、"*ISE 内*" で作成するカスタム コネクタは、オンプレミス データ ゲートウェイでは動作しません。 詳細については、「[オンプレミス システムへのアクセス](#on-premises)」のセクションを参照してください。

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>オンプレミス システムへのアクセス

ISE 内で実行されるロジック アプリは、これらの項目を使用して、Azure 仮想ネットワーク内にある、または Azure 仮想ネットワークに接続されている、オンプレミス システムおよびデータ ソースに直接アクセスできます。<p>

* **CORE** というラベルが表示される HTTP トリガーまたはアクション

* オンプレミスのシステムまたはデータ ソース用の **ISE** コネクタ (使用可能な場合)

  ISE コネクタを使用できる場合は、[オンプレミス データ ゲートウェイ](../logic-apps/logic-apps-gateway-connection.md)なしで、システムまたはデータ ソースに直接アクセスできます。 ただし、ISE から SQL Server にアクセスし、Windows 認証を使用する必要がある場合は、コネクタの ISE 以外のバージョンとオンプレミス データ ゲートウェイを使用する必要があります。 コネクタの ISE バージョンでは、Windows 認証はサポートされていません。 詳細については、「[ISE コネクタ](../connectors/managed.md#ise-connectors)」および「[統合サービス環境から接続する](../connectors/managed.md#integration-account-connectors)」のセクションを参照してください。

* カスタム コネクタ

  * "*ISE の外部*" で作成するカスタム コネクタは、[オンプレミス データ ゲートウェイ](../logic-apps/logic-apps-gateway-connection.md)が必要かどうかにかかわらず、ISE 内のロジック アプリのために、引き続き機能します。

  * "*ISE 内*" で作成するカスタム コネクタは、オンプレミス データ ゲートウェイでは動作しません。 ただし、これらのコネクタでは、ISE をホストしている仮想ネットワーク内にある、またはそのような仮想ネットワークに接続されている、オンプレミスのシステムおよびデータ ソースに直接アクセスできます。 そのため、ISE 内のロジック アプリでは、ほとんどの場合、それらのリソースにアクセスするときにデータ ゲートウェイは不要です。

ISE コネクタがないか、仮想ネットワークの外部にあるか、または仮想ネットワークに接続されていないオンプレミスのシステムおよびデータ ソースにアクセスするには、引き続きオンプレミス データ ゲートウェイを使用する必要があります。 ISE 内のロジック アプリは、引き続き、**CORE** または **ISE** というラベルを持たないコネクタを使用できます。 それらのコネクタは、ISE 内ではなくマルチテナント Logic Apps サービスで実行されます。 

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKU

ISE を作成するときは、Developer SKU または Premium SKU を選択できます。 この SKU オプションは、ISE 作成時にのみ使用できます。後で変更することはできません。 これらの SKU の違いを以下に示します。

* **開発者**

  探索、実験、開発、テストには使用できますが、運用環境とパフォーマンス テストには使用できない、低コストの ISE が提供されます。 Developer SKU には、組み込みのトリガーとアクション、標準コネクタ、エンタープライズ コネクタ、および[固定月額料金](https://azure.microsoft.com/pricing/details/logic-apps)用の 1 つの [Free レベル](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)統合アカウントが含まれています。 

  > [!IMPORTANT]
  > この SKU には、サービス レベル アグリーメント (SLA)、スケールアップ機能、リサイクル中の冗長性がありません。これは、遅延やダウンタイムが発生する可能性があることを意味します。 バックエンドの更新により、サービスが断続的に中断する場合があります。

  容量と制限については、[Azure Logic Apps での ISE の制限](logic-apps-limits-and-config.md#integration-service-environment-ise)に関する記事を参照してください。 ISE の課金のしくみについては、[Logic Apps の料金モデル](../logic-apps/logic-apps-pricing.md#ise-pricing)に関する記事を参照してください。

* **Premium**

  運用環境とパフォーマンス テストに使用できる ISE が提供されます。 Premium SKU には、SLA のサポート、組み込みのトリガーとアクション、標準コネクタ、エンタープライズ コネクタ、単一の [Standard レベル](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)の統合アカウント、スケールアップ機能、[固定月額料金](https://azure.microsoft.com/pricing/details/logic-apps)のリサイクル中の冗長性などが含まれています。

  容量と制限については、[Azure Logic Apps での ISE の制限](logic-apps-limits-and-config.md#integration-service-environment-ise)に関する記事を参照してください。 ISE の課金のしくみについては、[Logic Apps の料金モデル](../logic-apps/logic-apps-pricing.md#ise-pricing)に関する記事を参照してください。

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE エンドポイントへのアクセス

ISE を作成するときに、内部アクセス エンドポイントと外部アクセス エンドポイントのどちらを使用するかを選択できます。 この選択により、ISE 内のロジック アプリ上で要求または Webhook トリガーが仮想ネットワークの外からの呼び出しを受信できるかどうかが決まります。 これらのエンドポイントは、ロジック アプリの実行履歴から入力と出力にアクセスする方法にも影響します。

> [!IMPORTANT]
> アクセス エンドポイントは、ISE 作成中にのみ選択できます。後でこのオプションを変更することはできません。

* **内部**:ロジック アプリの実行履歴から、"*仮想ネットワーク内からのみ*"、入力と出力を表示およびアクセスできる ISE のロジック アプリの呼び出しを許可するプライベート エンドポイント。

  > [!IMPORTANT]
  > これらの Webhook ベースのトリガーを使用する必要があり、サービスが仮想ネットワークおよびピアリングされた仮想ネットワークの外部にある場合は、ISE を作成するときに、内部エンドポイントでは "*なく*" 外部エンドポイントを使用します。
  > 
  > * Azure DevOps
  > * Azure Event Grid
  > * Common Data Service
  > * Office 365
  > * SAP (マルチテナント バージョン)
  > 
  > また、プライベート エンドポイントと、実行履歴へのアクセス元として使用するコンピューターの間にネットワーク接続があることを確認します。 そうでないと、ロジック アプリの実行履歴を表示しようとしたときに、"予期しないエラーが発生しました。 フェッチできませんでした。" というエラーが発生します。
  >
  > ![ファイアウォール経由でトラフィックを送信できないために Azure Storage アクション エラーが発生する](./media/connect-virtual-network-vnet-isolated-environment-overview/integration-service-environment-error.png)
  >
  > たとえば、クライアント コンピューターは、ISE の仮想ネットワーク内にも、ピアリングや仮想プライベート ネットワークを介して ISE の仮想ネットワークに接続されている仮想ネットワーク内にも配置できます。 

* **外部**:ロジック アプリの実行履歴から、"*仮想ネットワーク外からの*" 入力と出力を表示およびアクセスできる ISE のロジック アプリの呼び出しを許可するパブリック エンドポイント。 ネットワーク セキュリティ グループ (NSG) を使用する場合は、実行履歴の入力と出力へのアクセスを許可する受信規則が設定されていることを確認します。 詳細については、「[ISE のアクセスを有効にする](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)」を参照してください。

ISE が内部アクセス エンドポイントを使用しているか、外部アクセス エンドポイントを使用しているかを確認するには、ISE のメニューで、 **[設定]** の下にある **[プロパティ]** を選択し、 **[アクセス エンドポイント]** プロパティを見つけます。

![ISE アクセス エンドポイントを見つける](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>価格モデル

ISE 内で実行されるロジック アプリ、組み込みトリガー、組み込みアクション、およびコネクタでは、使用量ベースの価格プランとは異なる固定価格プランが使用されます。 詳細については、[Logic Apps の価格モデル](../logic-apps/logic-apps-pricing.md#ise-pricing)を参照してください。 価格については、[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/)に関する記事を参照してください。

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>ISE での統合アカウント

統合サービス環境 (ISE) の内部のロジック アプリで統合アカウントを使用できます。 ただし、これらの統合アカウントでは、リンクされているロジック アプリと "*同じ ISE*" を使用する必要があります。 ISE のロジック アプリは、同じ ISE にある統合アカウントのみを参照できます。 統合アカウントを作成すると、ご利用の統合アカウントの場所として、自分の ISE を選択できます。 統合アカウントと ISE に対する価格と課金のしくみについては、「[固定価格モデル](../logic-apps/logic-apps-pricing.md#ise-pricing)」を参照してください。 価格については、[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/)に関する記事を参照してください。 制限の詳細については、「[統合アカウントの制限](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)」をご覧ください。

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps から Azure Virtual Network に接続する](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) の詳細を理解する
* [Azure サービスの仮想ネットワーク統合](../virtual-network/virtual-network-for-azure-services.md)について理解する
