---
title: Microsoft Azure Data Box Heavy の概要 | Microsoft Docs in data
description: 大量のデータを Azure に転送できるハイブリッド ソリューションである Azure Data Box について説明します
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: overview
ms.date: 10/20/2021
ms.author: alkohli
ms.openlocfilehash: 57a5ccb94f94fdc12083f49394235123b0e26d96
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130251872"
---
# <a name="what-is-azure-data-box-heavy"></a>Azure Data Box Heavy とは

Azure Data Box Heavy では、信頼性が高く、迅速かつ安価な方法で、数百テラバイトのデータを Azure に送信できます。 1 PB のストレージ容量を持つ Data Box Heavy デバイスがお客様に出荷され、お客様がこのデバイスにデータを入力して Microsoft に返送することによって、データが Azure に転送されます。 デバイスは堅牢な筐体で保護され、転送中のデータはセキュリティで保護されます。

データセンターでデバイスを受け取ったら、ローカル Web UI を使用して設定します。 データをサーバーからデバイスにコピーし、デバイスを Azure に返送します。 Azure データセンターで、お客様のデータがお客様の Azure Storage アカウントにアップロードされます。 お客様は Azure portal でエンドツーエンドのプロセス全体を追跡できます。


> [!IMPORTANT]
> - デバイスを要求するには、[Azure portal](https://portal.azure.com) でサインアップします。


## <a name="use-cases"></a>ユース ケース

Data Box Heavy は、ネットワーク接続では Azure にデータをアップロードするのに十分でない、数百テラバイト単位のデータ サイズに最適です。 データは 1 回だけ移動することも、定期的に移動することもできます。また、初期一括データ転送の後に定期的な転送を行うこともできます。 Data Box Heavy は、以下のようなさまざまなシナリオで、データ転送に使用できます。

 - **1 回限りの移行** - 大量のオンプレミス データを Azure に移動する場合。
     - オフライン テープのメディア ライブラリを Azure に移動し、オンライン メディア ライブラリを作成します。
     - VM ファーム、SQL Server、アプリケーションを Azure に移行します。
     - HDInsight を使用した詳細な分析やレポートのために、履歴データを Azure に移動します。

 - **初期一括転送** - Data Box Heavy (シード) を使用して初期一括転送が実行された後に、ネットワーク経由で増分転送を行う場合。
     - たとえば、Data Box Heavy とバックアップ ソリューション パートナーは、最初に大量の履歴バックアップを Azure に移動するために使用されます。 完了後のデータの増分は、ネットワーク経由で Azure ストレージに転送する。

 - **定期的なアップロード** - 定期的に生成される大量のデータを Azure に移動する必要がある場合。 たとえば、石油掘削装置や風力発電地帯でビデオ コンテンツが生成されるエネルギー探査でこれを実行します。

## <a name="benefits"></a>メリット

Data Box Heavy は、ネットワークにほとんどまたはまったく影響を与えずに大量のデータを Azure に移動することを目的としています。 このソリューションには次の利点があります。

- **速度** - Data Box Heavy は、高パフォーマンスの 40 Gbps ネットワーク インターフェイスを使用します。

- **セキュリティ** - Data Box Heavy には、デバイス、データ、サービスのセキュリティ保護が組み込まれています。
    - デバイスの筐体は堅牢で、不正開封防止ネジと不正開封防止ステッカーによってセキュリティ保護されています。
    - デバイスのデータは、AES 256 ビット暗号化によって常にセキュリティ保護されています。
    - デバイスは、Azure portal で提供されるパスワードでのみロックを解除できます。
    - このサービスは、Azure のセキュリティ機能によって保護されています。
    - データが Azure にアップロードされたら、NIST (アメリカ国立標準技術研究所) 800-88r1 規格に従って、デバイスのディスクが完全にワイプされます。


## <a name="features-and-specifications"></a>機能と仕様

このリリースの Data Box Heavy デバイスには、次の機能があります。

| 仕様                                          | 説明              |
|---------------------------------------------------------|--------------------------|
| Weight                                                  | 最大 500 ポンド <br>輸送用ロッキング ホイール上のデバイス|
| Dimensions                                              | 幅:26 インチ 高さ:28 インチ 長さ:48 インチ |
| ラック スペース                                              | ラックマウント不可|
| 必要なケーブル                                         | 接地 120 V、10 A 電源コード (NEMA 5-15) 付属 x 4 <br> デバイスは最大 240 V 電源をサポートし、C-13 電源レセプタクルを備える <br> [Mellanox MCX314 A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) と互換性のあるネットワーク ケーブルを使用  |
| Power                                                    | 両方のデバイス ノードで共有される 4 基の内蔵電源装置 (PSU) <br> 1,200 ワット定格消費電力|
| ストレージの容量                                        | 最大 1 PB (ロー)、各 14 TB のディスク 70 台 <br> 使用可能な容量は 770 TB|
| ノードの数                                          | デバイスごとに 2 つの独立したノード (各 500 TB) |
| ノードあたりのネットワーク インターフェイス数                             | ノードあたり 4 つのネットワーク インターフェイス <br><br> MGMT、DATA3 <ul><li> 2 X 1 GbE インターフェイス </li><li> MGMT は管理および初期セットアップ用、ユーザー構成不可 </li><li> DATA3 はユーザー構成可能であり、既定では動的ホスト構成プロトコル (DHCP)</li></ul>DATA1、DATA2 データ インターフェイス <ul><li>2 X 40 GbE インターフェイス </li><li> ユーザー構成可能 (既定値の DHCP の場合)、または静的</li></ul>|


## <a name="components"></a>Components

Data Box Heavy に含まれるコンポーネントを次に示します。

* **Data Box Heavy デバイス** - データを安全に保存する堅牢な外装を備えた物理的なデバイス。 このデバイスでは、770 TB のストレージ容量を使用できます。
    
* **Data Box サービス** - さまざまな地理的場所からアクセスできる Web インターフェイスから、Data Box Heavy デバイスを管理できる、Azure portal の拡張機能。 Data Box サービスを使用して Data Box Heavy デバイスを管理します。 サービス タスクには、注文の作成と管理、警告の表示と管理、共有の管理が含まれます。  

* **ローカル Web ユーザー インターフェイス** - デバイスの構成に使用する Web ベースの UI。この UI を使用して、ローカル ネットワークに接続し、デバイスを Data Box サービスに登録できます。 ローカル Web UI を使用すると、デバイスのシャット ダウンと再起動、コピー ログの表示、Microsoft サポートへの連絡とサービス要求の送信も行うことができます。


## <a name="the-workflow"></a>ワークフロー

一般的なフローには次の手順が含まれます。

1. **注文** - Azure portal で注文を作成し、配送情報とデータのコピー先 Azure ストレージ アカウントを指定します。 デバイスが利用可能な場合は、Azure がデバイスを準備し、出荷追跡 ID が割り当てられたデバイスを出荷します。

2. **受領** - デバイスが到着したら、デバイスをネットワークに接続し、指定のケーブルを電源につなぎます。 電源を入れてデバイスに接続します。 デバイスのネットワークを構成し、データをコピーするホスト コンピューターに共有をマウントします。

3. **データのコピー** - Data Box Heavy の共有にデータをコピーします。

4. **返却** - デバイスを準備し、電源を切って Azure データセンターに返送します。

5. **アップロード** - デバイスから Azure にデータが自動的にコピーされます。 デバイスのディスクは、米国国立標準技術研究所 (NIST) のガイドラインに従って安全に消去されます。

このプロセス全体を通じて、状態のすべての変更について電子メールで通知されます。

## <a name="region-availability"></a>利用可能なリージョン

Data Box Heavy は、サービスが展開されているリージョン、デバイスが出荷される国/地域、データの転送対象となる Azure ストレージ アカウントに基づいてデータを転送できます。

- **サービスの可用性** - Data Box Heavy は、米国およびヨーロッパでご利用いただけます。


- **転送先ストレージ アカウント** - データを格納するストレージ アカウントは、サービスが使用可能なすべての Azure リージョンで利用できます。

Data Box Heavy の提供状況に関するリージョン別の最新情報については、[リージョン別の Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)を参照してください。

<!--## Sign up

Take the following steps to sign up for Data Box Heavy:

1. [Sign into the Azure portal](https://portal.azure.com).
2. Click **+ Create a resource** to create a new resource. Search for **Azure Data Box**. Select **Azure Data Box** service.
3. Click **Create**.
4. Pick the subscription that you want to use for Data Box Heavy. Select the region where you want to deploy the Data Box Heavy resource. In the **Data Box Heavy** option, click **Sign up**.
5. Answer the questions regarding data residence country/region, time-frame, target Azure service for data transfer, network bandwidth, and data transfer frequency. Review Privacy and terms and select the checkbox against Microsoft can use your email address to contact you.

Once you're signed up, you can order a Data Box Heavy.-->

    
