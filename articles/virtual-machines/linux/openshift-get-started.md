---
title: Azure の OpenShift の概要
description: Azure の OpenShift の概要について説明します。
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: 940973aa0465c94e8df4882af9b2a1a89c65ff87
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687864"
---
# <a name="openshift-in-azure"></a>Azure の OpenShift

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: フレキシブルなスケール セット 

OpenShift は、エンタープライズに Docker と Kubernetes を導入する、オープンで拡張可能なコンテナー アプリケーション プラットフォームです。  

OpenShift には、コンテナーのオーケストレーションと管理のための Kubernetes が含まれています。 これにより、開発者と操作を中心に据えたツールが追加され、次のことが可能になります。

- 迅速なアプリケーション開発。
- 容易なデプロイとスケール変更。
- チームおよびアプリケーション向けの長期的なライフサイクル メンテナンス。

複数のバージョンの OpenShift を使用できます。  これらのバージョンのうち、現時点でお客様が Azure にデプロイできるのは、OpenShift Container Platform と OKD (旧 OpenShift Origin) の 2 つのみです。

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift は Azure で実行される OpenShift のフル マネージド オファリングです。 このサービスは、Microsoft と Red Hat によって共同で管理され、サポートされています。 詳細については、[Azure Red Hat OpenShift Service](../../openshift/index.yml) ドキュメントをご覧ください。

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform は Red Hat 製のエンタープライズ対応[市販バージョン](https://www.openshift.com)であり、Red Hat でサポートされています。 このバージョンでは、OpenShift Container Platform の必要な権利を購入します。インフラストラクチャ全体のインストールと管理は顧客が行います。

プラットフォーム全体を顧客が "所有" しているため、プラットフォームを自社のオンプレミスのデータセンターや、パブリック クラウド (Azure など) にインストールすることができます。

## <a name="okd"></a>OKD

OKD は OpenShift の[オープン ソース](https://www.okd.io/)上流プロジェクトであり、コミュニティによりサポートされています。 OKD は CentOS または Red Hat Enterprise Linux (RHEL) にインストールできます。

## <a name="next-steps"></a>次のステップ

- [Azure で OpenShift の共通の前提条件を構成する](./openshift-container-platform-3x-prerequisites.md)
- [OpenShift Container Platform の Azure へのデプロイ](./openshift-container-platform-3x.md)
- [OpenShift Container Platform 自己管理型マーケットプレース プランをデプロイする](./openshift-container-platform-3x-marketplace-self-managed.md)
- [OpenShift を Azure Stack にデプロイする](./openshift-azure-stack.md)
- [デプロイ後タスク](./openshift-container-platform-3x-post-deployment.md)
- [OpenShift デプロイのトラブルシューティング](./openshift-container-platform-3x-troubleshooting.md)
