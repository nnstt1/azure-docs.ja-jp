---
title: Azure Blob Storage でのネットワーク ファイル システム 3.0 のサポート
description: Blob Storage では、ネットワーク ファイル システム (NFS) 3.0 プロトコルがサポートされるようになりました。 このサポートにより、Linux クライアントは、Azure 仮想マシン (VM) またはオンプレミスで実行されているコンピューターから Blob Storage にコンテナーをマウントできます。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.reviewer: yzheng
ms.openlocfilehash: 81cf2e4f7c703dbea4592c754c8a63de68f29f7d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638332"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage"></a>Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポート

Blob Storage では、ネットワーク ファイル システム (NFS) 3.0 プロトコルがサポートされるようになりました。 このサポートにより、オブジェクト ストレージのスケールと価格で Linux ファイル システムの互換性が得られます。また、Linux クライアントは、Azure 仮想マシン (VM) またはオンプレミスのコンピューターから Blob Storage にコンテナーをマウントできます。

ハイ パフォーマンス コンピューティング (HPC) などの大規模なレガシ ワークロードをクラウドで実行することは、常に難しい課題でした。 その理由の 1 つは、アプリケーションがデータへのアクセスに NFS やサーバー メッセージ ブロック (SMB) などの従来のファイル プロトコルを使用することが多いためです。 また、ネイティブ クラウド ストレージ サービスでは、階層型名前空間と効率的なメタデータ操作を提供するファイル システムではなく、フラット型名前空間と広範なメタデータを持つオブジェクト ストレージに重点が置かれていました。

Blob Storage では、階層型名前空間がサポートされるようになりました。また、NFS 3.0 プロトコル サポートと組み合わせることで、Azure によって、大規模なクラウド オブジェクト ストレージ上でレガシ アプリケーションを実行することがはるかに容易になります。

## <a name="applications-and-workloads-suited-for-this-feature"></a>この機能に適したアプリケーションとワークロード

NFS 3.0 プロトコル機能は、高スループット、高スケール、読み取り負荷の高いワークロード (メディア処理、リスク シミュレーション、ゲノミクス シーケンスなど) の処理に最適です。 この機能は、高帯域幅を必要とする、複数のリーダーと多くのスレッドを使用するその他の種類のワークロードに使用することを検討してください。

## <a name="nfs-30-and-the-hierarchical-namespace"></a>NFS 3.0 と階層型名前空間

NFS 3.0 プロトコルのサポートでは、BLOB を階層型名前空間に編成する必要があります。 ストレージ アカウントを作成するときに、階層型名前空間を有効にできます。 階層型名前空間を使用する機能は、Azure Data Lake Storage Gen2 によって導入されました。 これにより、コンピューター上のファイル システムを編成するのと同じ方法で、オブジェクト (ファイル) がディレクトリとサブディレクトリの階層に編成されます。 階層型名前空間は、スケールが線形で、データ容量もパフォーマンスも低下しません。 階層型名前空間からさまざまなプロトコルが拡張されます。 NFS 3.0 プロトコルは、これらの使用可能なプロトコルの 1 つです。

> [!div class="mx-imgBorder"]
> ![階層型名前空間](./media/network-protocol-support/hierarchical-namespace-and-nfs-support.png)

## <a name="data-stored-as-block-blobs"></a>ブロック BLOB として格納されるデータ

NFS 3.0 プロトコル サポートを有効にすると、ストレージ アカウント内のすべてのデータがブロック BLOB として格納されます。 ブロック BLOB は、読み取り負荷の高い大量のデータを効率的に処理するように最適化されています。 ブロック BLOB は複数のブロックで構成されています。 各ブロックは、ブロック ID によって識別されます。 ブロック BLOB には、最大 50,000 個のブロックを含めることができます。 ブロック BLOB 内の各ブロックのサイズは、お客様のアカウントで使用されているサービスのバージョンで許可される最大サイズまで変更できます。

アプリケーションが NFS 3.0 プロトコルを使用して要求を行うと、その要求はブロック BLOB 操作の組み合わせに変換されます。 たとえば、NFS 3.0 読み取りリモート プロシージャ コール (RPC) 要求は、[Get Blob](/rest/api/storageservices/get-blob) 操作に変換されます。 NFS 3.0 書き込み RPC 要求は、[Get Block List](/rest/api/storageservices/get-block-list)、[Put Block](/rest/api/storageservices/put-block)、[Put Block List](/rest/api/storageservices/put-block-list) の組み合わせに変換されます。

## <a name="general-workflow-mounting-a-storage-account-container"></a>一般的なワークフロー:ストレージ アカウント コンテナーのマウント

Linux クライアントは、Azure 仮想マシン (VM) またはオンプレミスのコンピューターから Blob Storage にコンテナーをマウントできます。 ストレージ アカウント コンテナーをマウントするには、次の操作を行う必要があります。

1. Azure 仮想ネットワーク (VNet) を作成します。

2. ネットワーク セキュリティを構成します。

3. VNet からのトラフィックのみを受け入れるストレージ アカウントを作成および構成します。

4. ストレージ アカウントにコンテナーを作成します。

5. コンテナーをマウントします。

詳細な手順については、「[ネットワーク ファイル システム (NFS) 3.0 プロトコルを使用して Blob Storage をマウントする](network-file-system-protocol-support-how-to.md)」を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

トラフィックは VNet から発信される必要があります。 VNet を使用すると、クライアントはストレージ アカウントに安全に接続できます。 アカウント内のデータをセキュリティで保護する唯一の方法は、VNet とその他のネットワーク セキュリティ設定を使用することです。 アカウント キーの承認、Azure Active Directory (AD) セキュリティ、アクセス制御リスト (ACL) などのデータをセキュリティで保護するために使用されるその他のツールは、NFS 3.0 プロトコルのサポートが有効にされているアカウントではまだサポートされません。

詳細については、[BLOB ストレージのネットワーク セキュリティに関する推奨事項](security-recommendations.md#networking)に関するページを参照してください。

### <a name="supported-network-connections"></a>サポートされているネットワーク接続

クライアントは、パブリックまたは[プライベート エンドポイント](../common/storage-private-endpoints.md)経由で接続でき、次のいずれかのネットワークの場所から接続できます。

- ストレージ アカウント用に構成した VNet。

  この記事では、その VNet を "*プライマリ VNet*" と呼びます。 詳細については、「[仮想ネットワークからアクセスの許可](../common/storage-network-security.md#grant-access-from-a-virtual-network)」を参照してください。

- プライマリ VNet と同じリージョンにあるピアリングされた VNet。

  このピアリングされた VNet へのアクセスを許可するようにストレージ アカウントを構成する必要があります。 詳細については、「[仮想ネットワークからアクセスの許可](../common/storage-network-security.md#grant-access-from-a-virtual-network)」を参照してください。

- [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)または [ExpressRoute ゲートウェイ](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)を使用して、プライマリ VNet に接続されているオンプレミス ネットワーク。

  詳細については、「[オンプレミスのネットワークからのアクセスの構成](../common/storage-network-security.md#configuring-access-from-on-premises-networks)」を参照してください。

- ピアリングされたネットワークに接続されているオンプレミス ネットワーク。

  これを行うには、[VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)または [ExpressRoute ゲートウェイ](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)を[ゲートウェイ転送](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit)と共に使用します。

> [!IMPORTANT]
> オンプレミスのネットワークから接続している場合は、クライアントがポート 111 および 2048 を介した発信を許可していることを確認します。 NFS 3.0 プロトコルでは、これらのポートが使用されます。

<a id="azure-storage-features-not-yet-supported"></a>

## <a name="known-issues-and-limitations"></a>既知の問題と制限事項

NFS 3.0 サポートの現在のリリースにおける問題と制限事項の一覧については、[既知の問題](network-file-system-protocol-known-issues.md)に関する記事をご覧ください。

## <a name="pricing"></a>価格

データ ストレージとトランザクションのコストについては、「[Azure Blob Storage の価格](https://azure.microsoft.com/pricing/details/storage/blobs/)」のページを参照してください。

## <a name="see-also"></a>関連項目

- [ネットワーク ファイル システム (NFS) 3.0 プロトコルを使用して Blob Storage をマウントする](network-file-system-protocol-support-how-to.md)
- [Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 のパフォーマンスに関する考慮事項](network-file-system-protocol-support-performance.md)
- [NFS を使用した Azure Files、Blob Storage、および Azure NetApp Files へのアクセスを比較する](../common/nfs-comparison.md)
