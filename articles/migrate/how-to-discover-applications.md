---
title: Azure Migrate を使用したオンプレミス サーバーでのソフトウェア インベントリの検出
description: Azure Migrate Discovery and Assessment を使用して、オンプレミスのサーバーを検出する方法について説明します。
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: c337229a8e9a4fa98e80c955c317813950c51bfb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732653"
---
# <a name="discover-installed-software-inventory-web-apps-and-sql-server-instances-and-databases"></a>インストールされているソフトウェア インベントリ、Web アプリ、および SQL Server インスタンスとデータベースの検出

この記事では、Azure Migrate: Discovery and Assessment ツールを使用して、VMware 環境で動作するサーバーにインストールされているソフトウェア インベントリ、Web アプリ、および SQL Server インスタンスとデータベースを検出する方法について説明します。

ソフトウェア インベントリを実行すると、ワークロードに合わせて Azure への移行パスを特定し、調整するために役立ちます。 サーバーの資格情報を使用して検出を実行するために、ソフトウェア インベントリにより、Azure Migrate アプライアンスが使用されます。 これは完全にエージェントレスです。このデータを収集するエージェントはサーバーにインストールされていません。

## <a name="before-you-start"></a>開始する前に

- [プロジェクトを作成](./create-manage-projects.md)し、Azure Migrate: Discovery and Assessment ツールを追加したことを確認します。
- [VMware の要件](migrate-support-matrix-vmware.md#vmware-requirements)を確認し、ソフトウェア インベントリを実行します。
- アプライアンスを設定する前に、[アプライアンスの要件](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements)を確認します。
- サーバー上でソフトウェア インベントリを開始する前に、[アプリケーションの検出要件](migrate-support-matrix-vmware.md#software-inventory-requirements)を確認します。

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Azure Migrate アプライアンスをデプロイおよび構成する

1. Azure Migrate アプライアンスを展開するための要件を[確認](migrate-appliance.md#appliance---vmware)します。
2. アプライアンスが[パブリック](migrate-appliance.md#public-cloud-urls)および [Government クラウド](migrate-appliance.md#government-cloud-urls)でアクセスする必要がある Azure の URL について確認します。
3. 検出および評価中にアプライアンスによって収集される[データを確認](migrate-appliance.md#collected-data---vmware)します。
4. アプライアンスのポート アクセス要件に[注意](migrate-support-matrix-vmware.md#port-access-requirements)します。
5. [Azure Migrate アプライアンスを展開](how-to-set-up-appliance-vmware.md)して検出を開始します。 アプライアンスをデプロイするには、OVA テンプレートをダウンロードして VMware にインポートし、vCenter Server で実行されるサーバーを作成します。 アプライアンスをデプロイした後、プロジェクトに登録し、検出を開始するように構成する必要があります。
6. アプライアンスを構成するときに、アプライアンス構成マネージャーで以下を指定する必要があります。
    - 接続先の vCenter Server の詳細。
    - VMware 環境内のサーバーを検出する場合に対象となる vCenter Server 資格情報。
    - サーバーの資格情報 (ドメイン、Windows (ドメイン以外)、または Linux (ドメイン以外) の資格情報)。 資格情報の指定方法とそれらの処理方法の詳細については、[こちら](add-server-credentials.md)を参照してください。

## <a name="verify-permissions"></a>アクセス許可の確認

- 検出と評価のために [vCenter Server の読み取り専用アカウントを作成する](./tutorial-discover-vmware.md#prepare-vmware)必要があります。 読み取り専用アカウントには、ソフトウェア インベントリを実行するときにサーバーとやりとりするため、 **[Virtual Machines]**  >  **[ゲスト操作]** に対して有効な特権が必要です。
- アプリケーション検出のために、アプライアンス構成マネージャーにドメインおよびドメイン以外 (Windows または Linux) の複数の資格情報を追加することができます。 Windows サーバー用にゲスト ユーザー アカウントと、すべての Linux サーバー用に通常または標準ユーザー アカウント (非 sudo アクセス) が必要です。資格情報の指定方法とそれらの処理方法の詳細について[参照](add-server-credentials.md)してください。

### <a name="add-credentials-and-initiate-discovery"></a>資格情報を追加して検出を開始する

1. アプライアンス構成マネージャーを開き、アプライアンスの前提条件の確認と登録を完了します。
2. **[資格情報と検出ソースの管理]** パネルに移動します。
1.  **[Step 1: Provide vCenter Server credentials]\(ステップ 1: vCenter Server の資格情報を指定する\)** で、 **[資格情報の追加]** をクリックし、vCenter Server で実行されているサーバーの検出にアプライアンスによって使用される vCenter Server アカウントの資格情報を指定します。
1. **[Step 2: Provide vCenter Server details]\(ステップ 2: VCenter Server の詳細を指定する\)** で、 **[Add discovery source]\(検出ソースの追加\)** をクリックして、ドロップダウンから資格情報のフレンドリ名を選択し、vCenter Server インスタンスの **IP アドレスまたは FQDN** を指定します :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="vCenter Server の詳細に関するアプライアンス構成マネージャーのパネル 3":::
1. 「**手順 3: ソフトウェア インベントリ、エージェントレスの依存関係の分析、SQL Server インスタンスとデータベースの検出、VMware 環境での ASP.NET Web アプリの検出を実行するためのサーバー資格情報を指定する**」で、 **[資格情報の追加]** をクリックし、複数のサーバー資格情報を指定してソフトウェア インベントリを開始します。
1. vCenter Server の検出を開始するには、 **[検出の開始]** をクリックします。

 vCenter Server の検出が完了すると、アプライアンスにより、インストールされているアプリケーション、ロール、機能 (ソフトウェア インベントリ) の検出が開始されます。 期間は検出されたサーバーの数によって異なります。 500 台のサーバーがある場合、検出されたインベントリが Azure Migrate ポータルに表示されるまでに約 1 時間かかります。

## <a name="review-and-export-the-inventory"></a>インベントリの確認とエクスポート

ソフトウェア インベントリが完了したら、Azure portal でインベントリを確認し、エクスポートすることができます。

1. **[Azure Migrate - サーバー、データベース、Web アプリ]**  >  **[Azure Migrate: 検出と評価]** で、表示されたカウントをクリックして **[検出済みサーバー]** ページを開きます。

    > [!NOTE]
    > この段階で、必要に応じて、検出されたサーバーの依存関係分析を有効にして、評価するサーバー間の依存関係を視覚化することもできます。 依存関係の分析の[詳細を見る](concepts-dependency-visualization.md)。

2. **[ソフトウェア インベントリ]** 列で、表示されたカウントをクリックして検出されたアプリケーション、ロール、機能を確認します。
4. インベントリをエクスポートするには、 **[検出済みサーバー]** の **[ソフトウェア インベントリのエクスポート]** をクリックします。

ソフトウェア インベントリは、Excel 形式でエクスポートおよびダウンロードされます。 **[Software Inventory]\(ソフトウェア インベントリ\)** シートには、すべてのサーバーで検出されたすべてのアプリが表示されます。

## <a name="discover-sql-server-instances-and-databases"></a>SQL Server インスタンスとデータベースを検出する

- ソフトウェア インベントリにより、VMware 環境で実行されている SQL Server インスタンスも識別されます。
- アプライアンス構成マネージャーで Windows 認証または SQL Server 認証の資格情報を指定していない場合、アプライアンスで資格情報を利用し、それぞれの SQL Server インスタンスに接続できるよう、資格情報を追加してください。

    > [!NOTE]
    > アプライアンスは、ネットワークの見通しがある SQL Server インスタンスのみに接続できますが、ソフトウェア インベントリ単体ではネットワークの見通しが必要ない場合があります。

接続後、アプライアンスにより SQL Server インスタンスと SQL Server データベースの構成とパフォーマンスのデータが収集されます。 SQL Server 構成データは 24 時間ごとに更新され、パフォーマンス データは 30 秒ごとにキャプチャされます。 そのため、データベースの状態や互換性レベルなど、SQL Server インスタンスおよびデータベースのプロパティ変更は、ポータルで更新されるまで、最大 24 時間かかることがあります

## <a name="discover-aspnet-web-apps"></a>ASP.NET Web アプリを検出する

ソフトウェア インベントリは、検出されたサーバーに既存の Web サーバーの役割を識別します。 サーバーで Web サーバーの役割が有効になっている場合、Azure Migrate により、サーバーで Web アプリの検出が実行されます。
ユーザーは、アプライアンスにドメイン資格情報とドメイン以外の資格情報の両方を追加できます。 使用するアカウントは、ソース サーバーに対するローカル管理者特権を持っている必要があります。 Azure Migrate により、各サーバーに資格情報が自動的にマップされます。そのため、手動でマップする必要はありません。 最も重要なのは、これらの資格情報が Microsoft に送信されず、ソース環境で実行されているアプライアンスに残るという点です。
アプライアンスが接続されると、IIS Web サーバーと ASP.NET Web アプリの構成データが収集されます。 Web アプリ構成データは 24 時間に 1 回更新されます。

## <a name="next-steps"></a>次のステップ

- 検出されたサーバーの[評価を作成](how-to-create-assessment.md)します。
- Azure App Service へ移行するために [Web アプリを評価](how-to-create-azure-app-service-assessment.md)します。
