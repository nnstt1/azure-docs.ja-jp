---
title: Azure Migrate アプライアンスのアーキテクチャ
description: サーバーの検出、評価、移行に使用される Azure Migrate アプライアンスの概要について説明します。
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 78b62c91148fc7a2c202cbf6792c1de442c142cc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742025"
---
# <a name="azure-migrate-appliance-architecture"></a>Azure Migrate アプライアンスのアーキテクチャ

この記事では、Azure Migrate アプライアンスのアーキテクチャとプロセスについて説明します。 Azure Migrate アプライアンスは、オンプレミスに展開された軽量のアプライアンスで、Azure に移行するためのサーバーを検出します。

## <a name="deployment-scenarios"></a>デプロイメント シナリオ

Azure Migrate アプライアンスは、次のシナリオで使用します。

**シナリオ** | **ツール** | **用途**
--- | --- | ---
**VMware 環境で実行されているサーバーの検出と評価** | Azure Migrate: Discovery and Assessment | VMware 環境で実行されているサーバーを検出します<br/><br/> インストールされているソフトウェア インベントリ、ASP.NET Web アプリ、SQL Server インスタンスとデータベースの検出、およびエージェントレスの依存関係分析を実行します。<br/><br/> 評価のためにサーバー構成とパフォーマンス メタデータを収集します。
**VMware 環境で実行されているサーバーのエージェントレス移行** | Azure Migrate:Server Migration | VMware 環境で実行されているサーバーを検出します。<br/><br/> エージェントをインストールせずにサーバーをレプリケートします。
**Hyper-V 環境で実行されているサーバーの検出と評価** | Azure Migrate: Discovery and Assessment | Hyper-V 環境で実行されているサーバーを検出します。<br/><br/> 評価のためにサーバー構成とパフォーマンス メタデータを収集します。
**オンプレミスの物理または仮想化されたサーバーの検出と評価** |  Azure Migrate: Discovery and Assessment |  オンプレミスの物理または仮想化されたサーバーを検出します。<br/><br/> 評価のためにサーバー構成とパフォーマンス メタデータを収集します。

## <a name="deployment-methods"></a>デプロイ方法

このアプライアンスは、次のいくつかの方法を使用してデプロイできます。

- アプライアンスは、VMware または Hyper-V 環境で実行されているサーバー用のテンプレート ([VMware の場合は OVA テンプレート](how-to-set-up-appliance-vmware.md)、[Hyper-V の場合は VHD](how-to-set-up-appliance-hyper-v.md)) を使用してデプロイできます。
- テンプレートを使用しない場合は、[PowerShell インストーラー スクリプト](deploy-appliance-script.md)を使って VMware または Hyper-V 環境用のアプライアンスをデプロイできます。
- Azure Government では、PowerShell インストーラー スクリプトを使用してアプライアンスをデプロイする必要があります。 デプロイの手順については、[こちら](deploy-appliance-script-government.md)を参照してください。
- オンプレミスまたはその他のクラウドの物理あるいは仮想化されたサーバーの場合は、常に PowerShell インストーラー スクリプトを使用してアプライアンスをデプロイします。デプロイの手順については、[こちら](how-to-set-up-appliance-physical.md)を参照してください。
- ダウンロード リンクは、以下の表にあります。

## <a name="appliance-services"></a>アプライアンス サービス

アプライアンスには次のサービスがあります。

- **アプライアンス構成マネージャー**: これは Web アプリケーションであり、サーバーの検出と評価を開始するためにソースの詳細を構成できます。
- **検出エージェント**: このエージェントにより、オンプレミスの評価として作成するために使用できるサーバー構成メタデータが収集されます。
- **評価エージェント**: このエージェントにより、パフォーマンスベースの評価を作成するために使用できるサーバー パフォーマンス メタデータが収集されます。
- **自動更新サービス**: このサービスにより、アプライアンス上で実行されているすべてのエージェントが最新の状態に保たれます。 24 時間ごとに自動的に実行されます。
- **DRA エージェント**: サーバーのレプリケーションを調整し、レプリケートされたサーバーと Azure との間の通信をコーディネートします。 エージェントレス移行を使用してサーバーを Azure にレプリケートする場合にのみ使用されます。
- **ゲートウェイ**: レプリケートされたデータを Azure に送信します。 エージェントレス移行を使用してサーバーを Azure にレプリケートする場合にのみ使用されます。
- **SQL 検出および評価エージェント**: SQL Server インスタンスおよびデータベースの構成とパフォーマンスのメタデータを Azure に送信します。
- **Web アプリの検出と評価エージェント**: Web アプリの構成データを Azure に送信します。

> [!Note]
> 最後の 4 つのサービスは、VMware 環境で実行されているサーバーの検出と評価に使用されるアプライアンスでのみ使用できます。

## <a name="discovery-and-collection-process"></a>検出と収集のプロセス

:::image type="content" source="./media/migrate-appliance-architecture/architecture1.png" alt-text="アプライアンス アーキテクチャ":::

アプライアンスと検出ソースの間で、次のプロセスを使用して通信が行われます。

**処理** | **VMware アプライアンス** | **Hyper-V アプライアンス** | **物理アプライアンス**
---|---|---|---
**検出の開始** | 既定では、アプライアンスと vCenter サーバーの通信は TCP ポート 443 で行われます。 vCenter サーバーが別のポートでリッスンしている場合は、それをアプライアンス構成マネージャーで構成できます。 | アプライアンスと Hyper-V ホストとの通信は、WinRM ポート 5985 (HTTP) で行われます。 | アプライアンスと Windows サーバーの通信は WinRM ポート 5985 (HTTP) を介して、Linux サーバーとはポート 22 (TCP) を介して行われます。
**構成とパフォーマンスのメタデータの収集** | アプライアンスからポート 443 (既定のポート) または vCenter Server がリッスンしているその他のポートで接続することによって、vCenter Server で実行されているサーバーのメタデータが vSphere API を使用して収集されます。 | アプライアンスとポート 5985 のホストとの Common Information Model (CIM) セッションを使用して、Hyper-V ホスト上で実行されているサーバーのメタデータが収集されます。| アプライアンスとポート 5985 のサーバーとの Common Information Model (CIM) セッションを使用して Windows サーバーから、およびポート 22 での SSH 接続を使用して Linux サーバーから、メタデータが収集されます。
**検出データの送信** | アプライアンスから SSL ポート 443 を介して、収集されたデータが Azure Migrate: Discovery and assessment と Azure Migrate: Server Migration に送信されます。<br/><br/>  アプライアンスは、インターネット経由または ExpressRoute プライベート ピアリングまたは Microsoft ピアリング回線経由で Azure に接続できます。 | アプライアンスから SSL ポート 443 を介して、収集されたデータが Azure Migrate: Discovery and assessment に送信されます。<br/><br/> アプライアンスは、インターネット経由または ExpressRoute プライベート ピアリングまたは Microsoft ピアリング回線経由で Azure に接続できます。 | アプライアンスから SSL ポート 443 を介して、収集されたデータが Azure Migrate: Discovery and assessment に送信されます。<br/><br/> アプライアンスは、インターネット経由または ExpressRoute プライベート ピアリングまたは Microsoft ピアリング回線経由で Azure に接続できます。 
**データ収集の頻度** | 構成メタデータは 30 分ごとに収集され、送信されます。 <br/><br/> パフォーマンス メタデータは 20 秒ごとに収集され、10 分ごとに集計されてデータ ポイントが Azure に送信されます。 <br/><br/> ソフトウェア インベントリ データは、12 時間ごとに Azure に送信されます。 <br/><br/> エージェントレスの依存関係データは 5 分ごとに収集され、アプライアンス上で集計されて、6 時間ごとに Azure に送信されます。 <br/><br/> SQL Server 構成データは 24 時間ごとに更新され、パフォーマンス データは 30 秒ごとにキャプチャされます。 <br/><br/> Web アプリの構成データは 24 時間に 1 回更新されます。 Web アプリのパフォーマンス データはキャプチャされません。| 構成メタデータは 30 分ごとに収集され、送信されます。 <br/><br/> パフォーマンス メタデータは 30 秒ごとに収集され、10 分ごとに集計されてデータ ポイントが Azure に送信されます。|  構成メタデータは 30 分ごとに収集され、送信されます。 <br/><br/> パフォーマンス メタデータは 5 分ごとに収集され、10 分ごとに集計されてデータ ポイントが Azure に送信されます。
**評価と移行** | Azure Migrate: Discovery and assessment ツールを使用して、アプライアンスによって収集されたメタデータから評価を作成できます。<br/><br/>また、Azure Migrate: Server Migration ツールを使用して、VMware 環境で実行しているサーバーの移行を開始し、エージェントレスのサーバー レプリケーションを調整することもできます。| Azure Migrate: Discovery and assessment ツールを使用して、アプライアンスによって収集されたメタデータから評価を作成できます。 | Azure Migrate: Discovery and assessment ツールを使用して、アプライアンスによって収集されたメタデータから評価を作成できます。

## <a name="next-steps"></a>次のステップ

アプライアンスのサポート マトリックスを[確認](migrate-appliance.md)します。