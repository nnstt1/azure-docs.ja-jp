---
title: Azure File Sync のデプロイの計画 | Microsoft Docs
description: Azure File Sync を使用したデプロイを計画します。このサービスを使用すると、オンプレミスの Windows Server またはクラウド VM に複数の Azure ファイル共有をキャッシュすることができます。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: c4429e0410fb9511d511ce5841876d5fbca173f5
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129388098"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Azure File Sync のデプロイの計画

:::row:::
    :::column:::
        [![Azure File Sync を紹介するインタビューとデモ- クリックして再生](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        Azure File Sync は、オンプレミスの Windows Server またはクラウド VM に多数の Azure ファイル共有をキャッシュできるサービスです。 
        
        この記事では、Azure File Sync の概念と機能を紹介します。 Azure File Sync について理解したら、[Azure File Sync デプロイ ガイド](file-sync-deployment-guide.md)に従って、このサービスを試してみることを検討してください。        
    :::column-end:::
:::row-end:::

ファイルは [Azure ファイル共有](../files/storage-files-introduction.md)のクラウドに格納されます。 Azure ファイル共有は 2 つの方法で使用できます。これらのサーバーレスの Azure ファイル共有 (SMB) を直接マウントするか、Azure File Sync を使用してオンプレミスで Azure ファイル共有をキャッシュします。選択するデプロイ オプションによって、デプロイを計画するときに考慮する必要がある側面が変わります。 

- **Azure ファイル共有を直接マウントする**:Azure Files では SMB アクセスが提供されるため、Windows、macOS、および Linux で使用可能な標準的な SMB クライアントを使用して、オンプレミスまたはクラウドで Azure ファイル共有をマウントすることができます。 Azure ファイル共有はサーバーレスであるため、運用環境でデプロイするシナリオでは、ファイル サーバーや NAS デバイスを管理する必要ありません。 つまり、ソフトウェアの修正プログラムを適用したり、物理ディスクを交換したりする必要はありません。 

- **Azure File Sync を使用したオンプレミスでの Azure ファイル共有のキャッシュ**:Azure File Sync を使用すると、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を維持しながら、Azure Files で組織のファイル共有を一元化できます。 Azure File Sync によって、オンプレミス (またはクラウド) の Windows Server が Azure ファイル共有の高速キャッシュに変換されます。 

## <a name="management-concepts"></a>管理の概念
Azure File Sync のデプロイには、次の 3 つの基本的な管理オブジェクトがあります。

- **Azure ファイル共有**: Azure ファイル共有はサーバーレス クラウド ファイル共有であり、Azure File Sync の同期関係の *クラウド エンドポイント* を提供します。 Azure ファイル共有内のファイルには、SMB または FileREST プロトコルを使用して直接アクセスできます。ただし、Azure ファイル共有が Azure File Sync で使用されている場合は、主に Windows Server キャッシュを介してファイルにアクセスすることをお勧めします。これは、現在 Azure Files には Windows Server のような効率的な変更検出メカニズムがないため、Azure ファイル共有に直接加えた変更がサーバー エンドポイントに反映されるまでに時間がかかるためです。
- **サーバー エンドポイント**:Azure ファイル共有に同期されている Windows Server 上のパス。 これには、ボリューム上の特定のフォルダー、またはボリュームのルートを指定できます。 名前空間が重複していなければ、同じボリューム上に複数のサーバー エンドポイントが存在できます。
- **同期グループ**:**クラウド エンドポイント**、Azure ファイル共有、およびサーバー エンドポイント間の同期関係を定義するオブジェクト。 同期グループ内のエンドポイントは、相互に同期を維持されます。 たとえば、Azure File Sync で管理するファイル セットが 2 組ある場合、2 つの同期グループを作成し、各同期グループに別のエンドポイントを追加します。

### <a name="azure-file-share-management-concepts"></a>Azure ファイル共有の管理の概念
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Azure File Sync の管理の概念
同期グループは、**ストレージ同期サービス** にデプロイされます。これは、Azure File Sync で使用するサーバーを登録する最上位のオブジェクトで、同期グループのリレーションシップが含まれています。 ストレージ同期サービス リソースは、ストレージ アカウント リソースのピアであり、同じように Azure リソース グループにデプロイできます。 ストレージ同期サービスでは、複数のストレージ アカウントと複数の登録済み Windows サーバー間で Azure ファイル共有を含む同期グループを作成できます。

ストレージ同期サービスで同期グループを作成する前に、まずストレージ同期サービスで Windows Server を登録する必要があります。 これにより、**登録済みサーバー** オブジェクトが作成され、これはサーバー (またはクラスター) とストレージ同期サービス間の信頼関係を表します。 ストレージ同期サービスを登録するには、まずサーバーに Azure File Sync エージェントをインストールする必要があります。 個々のサーバーまたはクラスターは、同時に 1 つのストレージ同期サービスのみに登録できます。

同期グループには、1 つのクラウド エンドポイント (Azure ファイル共有) と 1 つ以上のサーバー エンドポイントが含まれます。 サーバー エンドポイント オブジェクトには、**クラウドを使った階層化** 機能を構成する設定が含まれています。これにより Azure File Sync のキャッシュ機能が提供されます。Azure ファイル共有と同期するには、Azure ファイル共有を含むストレージ アカウントが、ストレージ同期サービスと同じ Azure リージョンに存在している必要があります。

> [!Important]  
> 同期グループ内の任意のクラウド エンドポイントまたはサーバー エンドポイントの名前空間に変更を行うことにより、ファイルを同期グループ内の他のエンドポイントに同期できます。 クラウド エンドポイント (Azure ファイル共有) を直接変更した場合、その変更は、Azure File Sync の変更検出ジョブによって最初に認識される必要があります。 クラウド エンドポイントに対する変更検出ジョブは、24 時間に 1 回のみ起動されます。 詳細については、「[Azure Files についてよく寄せられる質問 (FAQ)](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#afs-change-detection)」を参照してください。

### <a name="consider-the-count-of-storage-sync-services-needed"></a>必要なストレージ同期サービスの数を検討する
前のセクションでは、Azure File Sync に対して構成するコア リソース ("*ストレージ同期サービス*") について説明されています。 Windows Server は、1 つのストレージ同期サービスにのみ登録できます。 そのため、多くの場合、単一のストレージ同期サービスをデプロイし、それにすべてのサーバーを登録することをお勧めします。 

以下の場合に限り、複数のストレージ同期サービスを作成します。
* データを交換する必要のない個別のサーバー セットがある場合。 この場合は、異なるストレージ同期サービスの同期グループ内でクラウド エンドポイントとして既に使用されている Azure ファイル共有と同期するために、特定のサーバー セットを除外するようにシステムを設計する必要があります。 これは見方を変えると、別々のストレージ同期サービスに登録されている Windows Server は、同一の Azure ファイル共有と同期できないということになります。
* 1 つのストレージ同期サービスでサポートできるよりも多くの登録済みサーバーまたは同期グループを用意する必要がある場合。 詳細については、「[Azure File Sync のスケール ターゲット](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#azure-file-sync-scale-targets)」を確認してください。

## <a name="plan-for-balanced-sync-topologies"></a>バランスの取れた同期トポロジの計画
リソースをデプロイする前に、ローカル サーバー上でどの Azure ファイル共有と同期するかを計画することが重要です。 計画を作成すると、必要なストレージ アカウント、Azure ファイル共有、および同期リソースの数を決定するのに役立ちます。 これらの考慮事項は、データが現在 Windows Server または長期的に使用するサーバー上に存在しない場合でも、やはり関係があります。 状況に応じて適切な移行パスを決定する際は、[移行セクション](#migration)が役立ちます。

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="windows-file-server-considerations"></a>Windows ファイル サーバーに関する考慮事項
Windows Server で同期機能を有効にするには、Azure File Sync のダウンロード可能なエージェントをインストールする必要があります。 Azure File Sync エージェントには、2 つの主な構成要素があります。`FileSyncSvc.exe` は、サーバー エンドポイントの変更の監視と同期セッションの開始を担当するバックグラウンド Windows サービスであり、`StorageSync.sys` はクラウドの階層化と迅速なディザスター リカバリーを可能にするファイル システム フィルターです。  

### <a name="operating-system-requirements"></a>オペレーティング システムの要件
Azure File Sync は、次のバージョンの Windows Server でサポートされています。

| Version | サポートされている SKU | サポートされているデプロイ オプション |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter、Standard、および IoT | 完全およびコア |
| Windows Server 2016 | Datacenter、Standard、および Storage Server | 完全およびコア |
| Windows Server 2012 R2 | Datacenter、Standard、および Storage Server | 完全およびコア |

Windows Server の今後のバージョンは、それらがリリースされたときに追加されます。

> [!Important]  
> Azure File Sync で使用するすべてのサーバーは、Windows Update の最新の更新プログラムを使用して常に最新の状態を保つことをお勧めします。 

### <a name="minimum-system-resources"></a>最小システム リソース
Azure File Sync には、少なくとも 1 つの CPU と最低 2 GiB のメモリを備えたサーバー (物理または仮想) が必要です。

> [!Important]  
> 動的メモリを有効にした仮想マシンでサーバーが実行されている場合は、2048 MiB 以上のメモリで VM を構成する必要があります。

ほとんどの運用環境のワークロードでは、最小要件のみを使用して Azure File Sync 同期サーバーを構成することはお勧めしません。 詳細については、「[推奨されるシステム リソース](#recommended-system-resources)」を参照してください。

### <a name="recommended-system-resources"></a>推奨されるシステム リソース
サーバーの機能やアプリケーションと同様に、Azure File Sync のシステム リソース要件は、デプロイの規模によって決まります。サーバー上の大規模なデプロイでは、より多くのシステム リソースが必要になります。 Azure File Sync の場合、スケールは、サーバー エンドポイント全体のオブジェクト数とデータセットのチャーンによって決まります。 1 台のサーバーは複数の同期グループにサーバー エンドポイントを持つことができ、次の表に示すオブジェクトの数では、サーバーが接続されている完全な名前空間が考慮されています。 

たとえば、サーバー エンドポイント A (1000 万オブジェクト) + サーバー エンドポイント B (1000 万オブジェクト) = 2000 万オブジェクトです。 その例のデプロイでは、8 個の CPU と、安定状態の場合は 16 GiB メモリ、初期移行の場合は (可能であれば) 48 GiB のメモリが推奨されます。
 
名前空間データは、パフォーマンス上の理由からメモリに格納されます。 そのため、よいパフォーマンスを維持するには、名前空間が大きいほど多くのメモリが必要であり、チャーンが多いほど処理に必要な CPU が増えます。 
 
次の表では、名前空間のサイズと、一般的な汎用ファイル共有の容量への変換の両方を示してあります。ファイルの平均サイズは 512 KiB です。 ファイル サイズが小さい場合は、同じ容量になるようにメモリを追加することを検討してください。 名前空間のサイズに基づいてメモリを構成します。

| 名前空間のサイズ - ファイルとディレクトリ (百万)  | 一般的な容量 (TiB)  | CPU コア  | 推奨されるメモリ (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (初期同期)/2 (通常のチャーン)      |
| 5        | 2.3     | 2        | 16 (初期同期)/4 (通常のチャーン)    |
| 10       | 4.7     | 4        | 32 (初期同期)/8 (通常のチャーン)   |
| 30       | 14.0    | 8        | 48 (初期同期)/16 (通常のチャーン)   |
| 50       | 23.3    | 16       | 64 (初期同期)/32 (通常のチャーン)  |
| 100*     | 46.6    | 32       | 128 (初期同期)/32 (通常のチャーン)  |

\*現時点では、1 億を超えるファイルとディレクトリを同期することは推奨されていません。 これは、テストされたしきい値に基づくソフト制限です。 詳細については、「[Azure File Sync のスケール ターゲット](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#azure-file-sync-scale-targets)」をご覧ください。

> [!TIP]
> 名前空間の初期同期は集中的操作であるため、初期同期が完了するまでは、より多くのメモリを割り当てることをお勧めします。 これは必須ではありませんが、初期同期の時間が短くなる場合があります。 
> 
> 通常のチャーンは、1 日に変更される名前空間の 0.5% です。 チャーンのレベルがそれより高い場合は、CPU を追加することを検討してください。 

- NTFS ファイル システムでフォーマットされたローカルに接続されたボリューム。

### <a name="evaluation-cmdlet"></a>評価コマンドレット
Azure File Sync をデプロイする前に、Azure File Sync 評価コマンドレットを使用して、お使いのシステムと互換性があるかどうかを評価する必要があります。 このコマンドレットでは、サポートされていない文字やサポートされていないオペレーティング システム バージョンなど、ファイル システムとデータセットに関する潜在的な問題をチェックします。 そのチェックでは、以下で説明する機能のほとんどがカバーされていますが、すべてではありません。デプロイが円滑に進むように、このセクションの残りの部分をよく読むことをお勧めします。 

評価コマンドレットをインストールするには、Az PowerShell モジュールをインストールします。これは、次の手順に従ってインストールできます。[Azure PowerShell をインストールして構成](/powershell/azure/install-Az-ps)します。

#### <a name="usage"></a>使用法  
複数の方法で評価ツールを呼び出すことができます。システム チェックとデータセット チェックのどちらか一方または両方を行うことができます。 システム チェックとデータセット チェックの両方を実行するには: 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

データセットのみをテストするには:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
システム要件のみをテストするには:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name> -SkipNamespaceChecks
```
 
CSV で結果を表示するには:
```powershell
$validation = Invoke-AzStorageSyncCompatibilityCheck C:\DATA
$validation.Results | Select-Object -Property Type, Path, Level, Description, Result | Export-Csv -Path C:\results.csv -Encoding utf8
```

### <a name="file-system-compatibility"></a>ファイル システムの互換性
Azure File Sync は、直接接続された NTFS ボリュームでのみサポートされています。 Windows Server のダイレクト アタッチド ストレージ (DAS) は、Windows Server オペレーティング システムがファイル システムを所有していることを意味します。 DAS は、ディスクをファイル サーバーに物理的に接続すること、仮想ディスクをファイル サーバー VM (Hyper-V によってホストされる VM など) に接続すること、または ISCSI を使用して提供されます。

NTFS ボリュームのみがサポートされます。ReFS、FAT、FAT32 およびその他のファイル システムはサポートされていません。

次の表に、NTFS ファイル システムの機能の相互運用の状態を示します。 

| 特徴量 | サポートの状態 | Notes |
|---------|----------------|-------|
| アクセス制御リスト (ACL) | 完全にサポートされています | Windows スタイルの随意アクセス制御リストは Azure File Sync に保持され、サーバー エンドポイント上の Windows Server によって適用されます。 Azure ファイル共有を直接マウントするときに ACL を適用することもできますが、これには追加の構成が必要です。 詳細については、[ID に関するセクション](#identity)を参照してください。 |
| ハード リンク | スキップ | |
| シンボリック リンク | スキップ | |
| マウント ポイント | 部分的にサポートされています。 | マウント ポイントがサーバー エンドポイントのルートである場合がありますが、マウント ポイントがサーバー エンドポイントの名前空間に含まれている場合、マウント ポイントはスキップされます。 |
| 接合 | スキップ | たとえば、分散ファイル システムの DfrsrPrivate フォルダーや DFSRoots フォルダーなどです。 |
| 再解析ポイント | スキップ | |
| NTFS 圧縮 | 完全にサポートされています | |
| スパース ファイル | 完全にサポートされています | スパース ファイルは同期されます (ブロックされません) が、完全なファイルとしてクラウドと同期されます。 クラウド内 (または他のサーバー上) のファイルの内容が変更され、変更がダウンロードされると、ファイルはスパースではなくなります。 |
| 代替データ ストリーム (ADS) | 保持されますが、同期されません | たとえば、ファイル分類インフラストラクチャによって作成された分類タグは同期されません。 各サーバー エンドポイント上のファイルに既存の分類タグは、変更されません。 |

<a id="files-skipped"></a>Azure File Sync は、次のような特定の一時ファイルとシステム フォルダーもスキップします。

| ファイル/フォルダー | Note |
|-|-|
| pagefile.sys | システムに固有のファイル |
| Desktop.ini | システムに固有のファイル |
| thumbs.db | サムネイル用の一時ファイル |
| ehthumbs.db | メディア サムネイル用の一時ファイル |
| ~$\*.\* | Office の一時ファイル |
| \*.tmp | 一時ファイル |
| \*.laccdb | Access データベースのロック ファイル|
| 635D02A9D91C401B97884B82B3BCDAEA.* | 内部の同期ファイル|
| \\System Volume Information | ボリュームに固有のフォルダー |
| $RECYCLE.BIN| Folder |
| \\SyncShareState | 同期用のフォルダー |

### <a name="consider-how-much-free-space-you-need-on-your-local-disk"></a>ローカルディスクで必要な空き領域を検討する
Azure File Sync の使用を計画するときは、サーバー エンドポイントを作成する予定のローカル ディスクで必要な空き領域を考慮してください。

Azure File Sync では、以下のものがローカル ディスク上のスペースを使用することを考慮する必要があります。
- クラウドを使った階層化が有効な場合:
    - 階層化されたファイルの再解析ポイント
    - Azure File Sync メタデータ データベース
    - Azure File Sync heatstore
    - ホットキャッシュに完全にダウンロードされたファイル (存在する場合)
    - ボリュームの空き領域ポリシーの要件

- クラウドを使った階層化が無効な場合:  
    - 完全にダウンロードされたファイル
    - Azure File Sync heatstore
    - Azure File Sync メタデータ データベース

ここでは、ローカル ディスクで必要な空き容量を見積もる方法を、例を使って説明します。 たとえば、Azure Windows VM に Azure File Sync エージェントをインストールし、ディスク F にサーバー エンドポイントを作成する予定だとします。ファイル数は 100 万で、そのすべてを階層化します。ディレクトリ数が 10 万、ディスク クラスター サイズは 4 KiB になります。 ディスク サイズは 1000 GiB です クラウドを使った階層化を有効にし、ボリュームの空き領域ポリシーを 20% に設定する必要があります。 

1. NTFS は、階層化されたファイルごとにクラスター サイズを割り当てます。 100 万ファイル * 4 KiB クラスター サイズ = 4,000,000 KiB (4 GiB)
> [!Note]  
> 階層化されたファイルによって占有される領域は、NTFS によって割り当てられます。 したがって、UI には表示されません。
3. 同期メタデータは、項目ごとにクラスター サイズを占有します。 (100 万ファイル + 100,000 ディレクトリ) * 4 KiB クラスター サイズ = 4,400,000 KiB (4.4 GiB)
4. Azure File Sync heatstore は、ファイルあたり 1.1 KiB を占有します。 100 万ファイル * 1.1 KiB = 1,100,000 KiB (1.1 GiB)
5. ボリュームの空き領域ポリシーは 20% です。 1000 GiB * 0.2 = 200 GiB

この場合、Azure File Sync には約 209,500,000 KiB (209.5 GiB) の領域が必要になります。 このディスクに必要な空き領域を計算するために望ましい追加の空き領域に、この量を追加します。

### <a name="failover-clustering"></a>フェールオーバー クラスタリング
1. Windows Server フェールオーバー クラスタリングは、Azure ファイル同期の "汎用ファイル サーバー" デプロイ オプションでサポートされています。 
2. Azure File Sync によってサポートされる唯一のシナリオは、クラスター化されたディスクを使用する Windows Server フェールオーバー クラスターです
3. フェールオーバー クラスタリングは、"アプリケーション データ用のスケールアウト ファイル サーバー" (SOFS)、クラスター共有ボリューム (CSV)、またはローカル ディスクではサポートされていません。

> [!Note]  
> 同期が適切に機能するには、フェールオーバー クラスターのすべてのノードに Azure File Sync エージェントをインストールする必要があります。

### <a name="data-deduplication"></a>データ重複除去
**Windows Server 2016 および Windows Server 2019**   
データ重複除去は、Windows Server 2016 と Windows Server 2019 のボリューム上の 1 つまたは複数のサーバー エンドポイントでクラウドを使った階層化が有効になっているか無効になっているかに関係なく、サポートされています。 クラウドを使った階層化が有効なボリュームでデータ重複除去を有効にすると、より多くのストレージをプロビジョニングしなくても、より多くのファイルをオンプレミスでキャッシュできます。 

クラウドを使った階層化が有効なボリュームでデータ重複除去が有効になっている場合、サーバー エンドポイントの場所にある重複除去最適化ファイルは、クラウドを使った階層化のポリシー設定に基づいて、通常のファイルと同様に階層化されます。 重複除去最適化ファイルが階層化された後、データ重複除去ガベージ コレクション ジョブが自動的に実行され、ボリューム上の他のファイルから参照されなくなった不要なチャンクを削除することによって、ディスク領域が解放されます。

ボリュームの節約はサーバーにのみ適用されることに注意してください。Azure ファイル共有内のデータは重複除去されません。

> [!Note]  
> Windows Server 2019 でクラウドを使った階層化が有効になっているボリュームでデータ重複除去をサポートするには、Windows 更新プログラム [KB4520062 - 2019 年 10 月](https://support.microsoft.com/help/4520062)またはそれ以降の月次ロールアップ更新プログラムをインストールする必要があり、Azure File Sync エージェント バージョン 12.0.0.0 以降が必要です。

**Windows Server 2012 R2**  
Azure File Sync については、データ重複除去とクラウドを使った階層化は、Windows Server 2012 R2 の同じボリューム上ではサポートされていません。 ボリュームでデータ重複除去が有効になっている場合は、クラウドを使った階層化を無効にする必要があります。 

**メモ**
- Azure File Sync エージェントをインストールする前にデータ重複除去がインストールされている場合、同じボリューム上でのデータ重複除去およびクラウドを使った階層化をサポートするには再起動が必要です。
- クラウドを使った階層化を有効にした後にボリューム上のデータ重複除去を有効にする場合、最初の重複除去最適化ジョブで、そのボリューム上のまだ階層化されていないファイルが最適化され、クラウドを使った階層化に次のような影響を与えます。
    - 空き領域ポリシーに従い、ボリューム上の空き領域に応じて、ヒートマップを使用してファイルが継続的に階層化されます。
    - 重複除去最適化ジョブがファイルにアクセスしているために、本来ならば階層化の対象となっていたかもしれないファイルの階層化が日付ポリシーによってスキップされます。
- 進行中の重複除去最適化ジョブについては、ファイルがまだ階層化されていない場合は、データ ポリシーでのクラウドを使った階層化が、データ重複除去の [MinimumFileAgeDays](/powershell/module/deduplication/set-dedupvolume) 設定により遅延します。 
    - 例:MinimumFileAgeDays 設定が 7 日、クラウドを使った階層化の日付ポリシーが 30 日の場合、日付ポリシーによって 37 日後にファイルが階層化されます。
    - 注:Azure File Sync によってファイルが階層化されると、重複除去最適化ジョブによってそのファイルはスキップされます。
- インストールされている Azure File Sync エージェントを使用して Windows Server 2012 R2 を実行しているサーバーが、Windows Server 2016 または Windows Server 2019 にアップグレードされる場合、同じボリューム上でのデータ重複除去およびクラウドを使った階層化がサポートされるには次の手順を行う必要があります。  
    - Windows Server 2012 R2 用の Azure File Sync エージェントをアンインストールして、サーバーを再起動します。
    - 新しいサーバーのオペレーティング システム バージョン用 (Windows Server 2016 または Windows Server 2019) の Azure File Sync エージェントをダウンロードします。
    - Azure File Sync エージェントをインストールして、サーバーを再起動します。  
    
    注:サーバー上の Azure File Sync 構成設定は、エージェントがアンインストールされ再インストールされるときに保持されます。

### <a name="distributed-file-system-dfs"></a>分散ファイル システム (DFS)
Azure File Sync は、DFS 名前空間 (DFS-N) および DFS レプリケーション (DFS-R) との相互運用をサポートします。

**DFS 名前空間 (DFS-N)** :Azure File Sync は DFS-N サーバーで完全にサポートされます。 1 つまたは複数の DFS-N メンバーに Azure File Sync をインストールして、サーバー エンドポイントとクラウド エンドポイントの間でデータを同期できます。 詳しくは、「[DFS 名前空間の概要](/windows-server/storage/dfs-namespaces/dfs-overview)」をご覧ください。
 
**DFS レプリケーション (DFS-R)** :DFS-R と Azure File Sync はどちらもレプリケーション ソリューションなので、ほとんどの場合は、DFS-R を Azure File Sync に置き換えることをお勧めします。ただし、DFS-R と Azure File Sync を併用するのが望ましいシナリオがいくつかあります。

- DFS-R のデプロイから Azure File Sync のデプロイに移行しているとき。 詳しくは、「[DFS レプリケーション (DFS-R) のデプロイを Azure File Sync に移行する](file-sync-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)」をご覧ください。
- ファイル データのコピーが必要なオンプレミスのサーバーの中に、インターネットに直接接続することができないものが含まれる場合。
- ブランチ サーバーが単一のハブ サーバーにデータを統合しており、それに対して Azure File Sync を使いたい場合。

Azure File Sync と DFS-R を併用するには、次のことが必要です。

1. DFS-R でレプリケートされるフォルダーを含むボリュームで、Azure File Sync のクラウドの階層化を無効にする必要があります。
2. DFS-R の読み取り専用レプリケーション フォルダーには、サーバー エンドポイントを構成しないようにする必要があります。

詳しくは、[DFS レプリケーションの概要](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj127250(v=ws.11))に関するページをご覧ください。

### <a name="sysprep"></a>Sysprep
Azure File Sync エージェントがインストールされているサーバー上で sysprep を使用することはサポートされていません。予期しない結果になる可能性があります。 エージェントのインストールとサーバーの登録は、サーバー イメージの展開と sysprep ミニ セットアップの完了後に行われます。

### <a name="windows-search"></a>Windows Search
クラウドの階層化がサーバー エンドポイントで有効になっている場合、階層化されたファイルはスキップされ、Windows Search によってインデックスが付けられます。 階層化されていないファイルは適切にインデックスが付けられます。

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>その他の階層型ストレージ管理 (HSM) ソリューション
その他の HSM ソリューションを Azure File Sync で使用することはできません。

## <a name="performance-and-scalability"></a>パフォーマンスとスケーラビリティ

Azure File Sync エージェントは Azure ファイル共有に接続している Windows Server マシンで実行されているため、実質的な同期パフォーマンスは、Windows Server と基になるディスクの構成、サーバーと Azure Storage の間のネットワーク帯域幅、ファイルのサイズ、データセット全体のサイズ、データセットでのアクティビティなど、お使いのインフラストラクチャの要因によって異なります。 Azure File Sync はファイル レベルで動作するため、Azure File Sync ベースのソリューションのパフォーマンス特性の測定には、1 秒間に処理されたオブジェクト (ファイルとディレクトリ) の数が適しています。

Azure Portal または SMB を使用して Azure ファイル共有に加えられた変更は、サーバー エンドポイントに対する変更とは異なり、検出とレプリケーションが即座に行われることはありません。 Azure Files にはまだ変更通知/ジャーナルがないため、ファイルが変更されたときに自動的に同期セッションを開始する方法はありません。 Windows Server では、Azure File Sync は [Windows USN ジャーナル](/windows/win32/fileio/change-journals)を使用して、ファイルが変更されたときに同期セッションを自動的に開始します。

Azure ファイル共有に対する変更を検出するために、Azure File Sync には、変更検出ジョブと呼ばれるスケジュールされたジョブがあります。 変更検出ジョブは、ファイル共有内のすべてのファイルを列挙した後、ファイルの同期バージョンと比較します。 変更検出ジョブによってファイルが変更されていると判断された場合に、Azure File Sync は同期セッションを開始します。 変更検出ジョブは 24 時間ごとに実行されます。 変更検出ジョブは Azure ファイル共有内のすべてのファイルを列挙することで機能するため、変更の検出は、大きな名前空間のほうが小さな名前空間よりも時間がかかります。 大規模な名前空間の場合、変更されたファイルを判断するのに 24 時間ごとに 1 回より長くなる場合があります。

詳細については、「[Azure File Sync のパフォーマンス メトリック](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#azure-file-sync-performance-metrics)」と「[Azure File Sync のスケール ターゲット](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#azure-file-sync-scale-targets)」を参照してください。

## <a name="identity"></a>ID
Azure File Sync は、同期のセットアップ以外に特別な設定を行わなくても、標準の AD ベースの ID で動作します。Azure File Sync を使用する場合に一般的に想定されるのは、ほとんどのアクセスが Azure ファイル共有ではなく、Azure File Sync キャッシュ サーバーを経由することです。 サーバー エンドポイントは Windows Server 上にあり、Windows Server では AD と Windows スタイルの ACL が長い間サポートされてきたため、ストレージ同期サービスに登録されている Windows ファイル サーバーがドメインに参加していることを確認する以外は何も必要ありません。 Azure File Sync では、Azure ファイル共有内のファイルに ACL が格納され、それらはすべてのサーバー エンドポイントにレプリケートされます。

Azure ファイル共有に直接加えられた変更は、同期グループ内のサーバー エンドポイントに同期するのに時間がかかる場合がありますが、ファイル共有に対する AD アクセス許可をクラウドで直接適用できることも確認する必要があります。 これを行うには、Windows ファイル サーバーがドメイン参加しているのと同じように、ストレージ アカウントをオンプレミスの AD にドメイン参加させる必要があります。 お客様が所有する Active Directory へのストレージ アカウントのドメイン参加について詳しくは、[Azure Files Active Directory の概要](../files/storage-files-active-directory-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)に関するページを参照してください。

> [!Important]  
> Azure File Sync を正常にデプロイするために、ストレージ アカウントを Active Directory にドメイン参加させる必要はありません。これは、ユーザーが Azure ファイル共有を直接マウントするときにオンプレミスの ACL を適用することを Azure ファイル共有に許可する、厳密には省略可能な手順です。

## <a name="networking"></a>ネットワーク
Azure File Sync エージェントは、Azure File Sync REST プロトコルと FileREST プロトコルを使用して、ストレージ同期サービスと Azure ファイル共有と通信します。どちらも、常にポート443 経由で HTTPS を使用します。 SMB は、Windows Server と Azure ファイル共有の間でデータをアップロードまたはダウンロードするために使用されることはありません。 ほとんどの組織では、ほぼすべての Web サイトにアクセスするための要件として、ポート 443 経由の HTTPS トラフィックを許可しているため、通常、Azure File Sync をデプロイするために特別なネットワーク構成は必要ありません。

組織のポリシーまたは独自の規制要件に基づいて、Azure とのより制限的な通信が必要になる可能性があります。そのため、Azure File Sync は、ネットワークを構成するためのいくつかのメカニズムを提供します。 要件に応じて、次のことができます。

- ExpressRoute または Azure VPN 経由のトンネル同期とファイルのアップロードまたはダウンロード トラフィック。 
- Azure Files と Azure のネットワーク機能 (サービス エンドポイント、プライベート エンドポイントなど) の使用。
- お使いの環境でプロキシをサポートするよう Azure File Sync を構成。
- Azure File Sync からネットワーク アクティビティを調整。

> [!Important]  
> Azure File Sync は、インターネット ルーティングをサポートしていません。 既定のネットワーク ルーティング オプションである Microsoft ルーティングは、Azure File Sync でサポートされています。

Azure File Sync とネットワークの詳細については、「[Azure File Sync のネットワークに関する考慮事項](file-sync-networking-overview.md)」を参照してください。

## <a name="encryption"></a>暗号化
Azure File Sync を使用する場合、Windows Server のストレージの保存時の暗号化、Azure File Sync エージェントと Azure 間での暗号化、Azure ファイル共有のデータの暗号化という、3 つの異なるレベルの暗号化を考慮する必要があります。 

### <a name="windows-server-encryption-at-rest"></a>Windows Server の保存時の 暗号化 
一般的に Azure File Sync で機能する Windows Server 上のデータを暗号化する方法には、ファイル システムとそれに書き込まれたすべてのデータが暗号化されるファイル システムにおける暗号化と、ファイル形式自体での暗号化という、2 つの方法があります。 これらのメソッドは相互に排他的ではありません。暗号化の目的が異なるため、必要に応じて組み合わせて使用できます。

そのファイル システムにおける暗号化機能を提供するために、Windows Server は BitLocker 受信トレイを提供します。 BitLocker は Azure File Sync に対して完全に透過的です。BitLocker などの暗号化メカニズムを使用する主な理由は、ディスクの盗難によるオンプレミスのデータセンターからの物理的なデータの流出を防ぎ、権限のない OS によってデータへの不正読み取り、または不正書き込みが行われるのを防ぐためです。 BitLocker の詳細については、「[BitLocker の概要](/windows/security/information-protection/bitlocker/bitlocker-overview)」を参照してください。

NTFS ボリューム下に配置されるという点で BitLocker と同様に機能するサード パーティ製品は、Azure File Sync で同様に完全に透過的に機能するはずです。 

データを暗号化するもう 1 つの主な方法は、アプリケーションがファイルを保存するときにファイルのデータ ストリームを暗号化することです。 アプリケーションによっては、これがネイティブに実行されることもありますが、一般的にはそうではありません。 ファイルのデータ ストリームを暗号化する方法の例としては、Azure Information Protection (AIP)/Azure Rights Management Services (Azure RMS)/Active Directory RMS があります。 AIP/RMS などの暗号化メカニズムを使用する主な理由は、ファイル共有のデータを、誰かがフラッシュ ドライブなどの別の場所にコピーしたり、承認されていないユーザーに電子メールで送信したりすることによって、データがファイル共有から流出することを防ぐためです。 ファイルのデータ ストリームがファイル形式の一部として暗号化されている場合、このファイルは引き続き Azure ファイル共有で暗号化されます。 

Azure File Sync は、ファイル システムより上に位置し、ファイルのデータ ストリームの下にある NTFS 暗号化ファイル システム (NTFS EFS) やサード パーティの暗号化ソリューションと相互運用できません。 

### <a name="encryption-in-transit"></a>転送中の暗号化

> [!NOTE]
> 2020 年 8 月 1 日より、Azure File Sync サービスでは TLS 1.0 と 1.1 のサポートが削除されます。 サポートされているすべての Azure File Sync エージェントのバージョンは、既に TLS 1.2 を既定で使用しています。 TLS 1.2 がサーバーで無効になっているか、プロキシが使用されている場合は、以前のバージョンの TLS が使用される可能性があります。 プロキシを使用している場合は、プロキシの構成を確認することをお勧めします。 2020 年 5 月 1 日より後に追加された Azure File Sync サービスのリージョンでは TLS 1.2 のみがサポートされます。TLS 1.0 と 1.1 のサポートは、2020 年 8 月 1 日に既存のリージョンから削除されます。  詳細については、[トラブルシューティング ガイド](file-sync-troubleshoot.md#tls-12-required-for-azure-file-sync)を参照してください。

Azure File Sync エージェントは、Azure File Sync REST プロトコルと FileREST プロトコルを使用して、ストレージ同期サービスと Azure ファイル共有と通信します。どちらも、常にポート443 経由で HTTPS を使用します。 Azure File Sync では、暗号化されていない要求が HTTP 経由で送信されることはありません。 

Azure ストレージ アカウントには、転送中の暗号化を要求するスイッチが含まれています。これは既定で有効になっています。 ストレージ アカウント レベルでスイッチが無効になっており、Azure ファイル共有への暗号化されていない接続が可能な場合でも、Azure File Sync は暗号化されたチャネルのみを使用してファイル共有にアクセスします。

ストレージ アカウントの転送中の暗号化を無効にする主な理由は、古いオペレーティング システム (Windows Server 2008 R2 や古い Linux ディストリビューションなど) 上で実行する必要のある、Azure ファイル共有と直接通信するレガシ アプリケーションをサポートするためです。 レガシ アプリケーションとファイル共有の Windows Server キャッシュが通信する場合、この設定を切り替えても効果はありません。 

転送中のデータの暗号化が有効になっていることを確認することを強くお勧めします。

転送中の暗号化の詳細については、「[Azure Storage で安全な転送が必要](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」を参照してください。

### <a name="azure-file-share-encryption-at-rest"></a>Azure ファイル共有の保存時の暗号化
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>ストレージ層
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

#### <a name="regional-availability"></a>リージョン別の提供状況
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Azure File Sync の利用可能なリージョン

リージョン別の提供状況については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=storage)」を参照してください。

次のリージョンでは、Azure File Sync を使用する前に、Azure Storage へのアクセス権を要求する必要があります。

- フランス南部
- 南アフリカ西部
- アラブ首長国連邦中部

これらのリージョンへのアクセス権を要求するには、[このドキュメント](https://azure.microsoft.com/global-infrastructure/geographies/)の手順に従ってください。

## <a name="redundancy"></a>冗長性
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> Geo 冗長ストレージおよび Geo ゾーン冗長ストレージには、セカンダリ リージョンに手動でストレージをフェールオーバーする機能があります。 データ損失の可能性が高くなるため、Azure File Sync を使用している場合は、障害が発生していない場合にこの操作を行わないことをお勧めします。 ストレージの手動フェールオーバーを開始する必要がある障害が発生した場合は、Azure File Sync でセカンダリ エンドポイントとの同期が再開されるよう、Microsoft のサポートケースを開く必要があります。

## <a name="migration"></a>移行
既存の Windows ファイル サーバー 2012R2 以降がある場合は、新しいサーバーにデータを移動しなくても、Azure File Sync を直接インストールすることができます。 Azure File Sync の導入の一環として新しい Windows ファイル サーバーに移行することを計画している場合、または現在データがネットワーク接続ストレージ (NAS) に配置されている場合は、このデータに関して Azure File Sync を使用したいくつかの移行方法が可能です。 どの移行方法を選択するかは、現在データが存在する場所によって異なります。 

シナリオの詳細なガイダンスについては、[Azure File Sync と Azure ファイル共有の移行の概要](../files/storage-files-migration-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)に関する記事をご覧ください。

## <a name="antivirus"></a>ウイルス対策
ウイルス対策は、ファイルをスキャンして既知の悪意のあるコードを検索することで機能するため、ウイルス対策製品によって階層化されたファイルの再呼び出しが発生することがあります。この結果、エグレス料金が高額になる可能性があります。 Azure File Sync エージェントのバージョン 4.0 以降では、階層化されたファイルにはセキュリティで保護された Windows 属性 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS が設定されています。 この属性を設定してオフライン ファイルの読み取りをスキップするようにソリューションを構成する方法について、ソフトウェア ベンダーと相談することをお勧めします (多くの場合は自動実行されます)。 

Microsoft の社内ウイルス対策ソリューションである Windows Defender と System Center Endpoint Protection (SCEP) では、この属性が設定されたファイルの読み取りが自動的にスキップされます。 そのテスト結果として小さな問題点がわかりました。既存の同期グループにサーバーを追加すると、新しいサーバー上で 800 バイト未満のファイルの呼び戻し (ダウンロード) が実行されるという問題です。 このようなファイルは新しいサーバー上に残り、階層化のサイズ要件 (64 KB を超えるサイズ) を満たしていないため、階層化されません。

> [!Note]  
> ウイルス対策ソフトウェア ベンダーは、その製品と Azure File Sync との間の互換性を、[Azure File Sync Antivirus Compatibility Test Suite](https://www.microsoft.com/download/details.aspx?id=58322) を使用して確認することができます。これは、Microsoft ダウンロード センターでダウンロードできます。

## <a name="backup"></a>バックアップ 
クラウドを使った階層化が有効になっている場合、サーバー エンドポイント、またはサーバー エンドポイントが配置されている VM を直接バックアップするソリューションは使用しないでください。 クラウドを使った階層化では、データのサブセットのみがサーバー エンドポイントに格納され、完全なデータセットは Azure ファイル共有に保存されます。 使用されるバックアップ ソリューションによっては、階層化されたファイルはスキップされ、バックアップされないか (FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS 属性が設定されているため)、ディスクに再呼び出しされるため、エグレス料金が高額になります。 Azure ファイル共有を直接バックアップするには、クラウド バックアップ ソリューションを使用することをお勧めします。 詳細については、「[Azure ファイル共有のバックアップについて](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」を参照するか、バックアップ プロバイダーに問い合わせて、Azure ファイル共有のバックアップがサポートされているかどうかを確認してください。

オンプレミス バックアップ ソリューションを使用する場合、クラウドを使った階層化が無効な同期グループ内のサーバーに対してバックアップを実行する必要があります。 復元を実行するときは、ボリューム レベルまたはファイル レベルの復元オプションを使用します。 ファイル レベルの復元オプションを使用して復元されたファイルは、同期グループ内のすべてのエンドポイントに同期され、既存のファイルはバックアップから復元されたバージョンに置き換えられます。  ボリューム レベルの復元では、Azure ファイル共有やその他のサーバー エンドポイントの新しいファイル バージョンに置き換えられません。

> [!WARNING]
> 同期元またはターゲット サーバーのいずれかで実行されている Azure File Sync エージェントで Robocopy /B を使用する必要がある場合は、Azure File Sync エージェント バージョン v12.0 以上にアップグレードしてください。 v12.0 よりも前のバージョンのエージェントで Robocopy /B を使用すると、コピー中に階層化されたファイルが破損する可能性があります。

> [!Note]  
> ベアメタル (BMR) 復元は予期しない結果が生じることがあるため、現在サポートされていません。

> [!Note]  
> Azure File Sync エージェントのバージョン 9 では、VSS スナップショット ([以前のバージョン] タブを含む) が、クラウドの階層化が有効なボリュームでサポートされるようになりました。 ただし、PowerShell を使用して以前のバージョンの互換性を有効にする必要があります。 方法については、[こちら](file-sync-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service)をご覧ください。

## <a name="data-classification"></a>データ分類
データ分類ソフトウェアがインストールされている場合、クラウドを使った階層化を有効にすると、次の 2 つの理由によりコストが増加する可能性があります。

1. クラウドを使った階層化を有効にすると、アクセス頻度の高いファイルがローカルにキャッシュされ、アクセス頻度の低いファイルがクラウド内の Azure ファイル共有に階層化されます。 データ分類によってファイル共有内のすべてのファイルが定期的にスキャンされる場合、クラウドに階層化されたファイルは、スキャンされるたびにリコールされる必要があります。 

2. データ分類ソフトウェアでファイルのデータ ストリームのメタデータを使用する場合は、ソフトウェアが分類を確認するために、ファイルを完全にリコールする必要があります。 

リコールの回数と、リコールされるデータ量の両方の増加のために、コストが増加する可能性があります。

## <a name="azure-file-sync-agent-update-policy"></a>Azure ファイル同期エージェントの更新ポリシー
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>次のステップ
* [ファイアウォールとプロキシの設定の考慮事項](file-sync-firewall-and-proxy.md)
* [Azure Files をデプロイする](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)
* [Azure File Sync をデプロイする](file-sync-deployment-guide.md)
* [Azure File Sync の監視](file-sync-monitoring.md)
