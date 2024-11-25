---
title: Azure Automation を使用した Azure Cloud Services (クラシック) の管理 | Microsoft Docs
description: Azure Automation サービスを使用して大規模に Azure クラウド サービスを管理する方法について説明します。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 6dd814f2c834428d321747a989af8beb76890bd0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124799170"
---
# <a name="managing-azure-cloud-services-classic-using-azure-automation"></a>Azure Automation を使用した Azure Cloud Services (クラシック) の管理

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

このガイドでは、Azure Automation サービスと、このサービスを使用して Azure クラウド サービスの管理を簡略化する方法について紹介します。

## <a name="what-is-azure-automation"></a>Azure Automation とは
[Azure Automation](https://azure.microsoft.com/services/automation/) は、プロセスの自動化によってクラウド管理を簡略化するための Azure サービスです。 Azure Automation を使用して実行時間の長いタスク、手動タスク、エラーが発生しやすいタスク、頻繁に繰り返されるタスクを自動化し、信頼性と効率性を向上して組織のゴール達成までの時間を短縮することができます。

Azure Automation は、組織の拡大に伴って変化するニーズに対応可能な、信頼性と可用性の高いワークフロー実行エンジンを提供します。 Azure Automation では、サード パーティ製のシステムによって手動でプロセスを開始したり、必要なときに正確にタスクが起動されるようにスケジュールされた間隔でプロセスを開始できます。

Azure Automation でクラウド管理タスクを自動実行すれば、運用上のオーバーヘッドが削減され、IT/DevOps スタッフの負担が軽減されるため、ビジネス価値の向上にフォーカスすることができます。

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Azure Automation を Azure クラウド サービスの管理に役立てる方法
[Azure PowerShell ツール](/powershell/)で利用可能な PowerShell コマンドレットを使用することにより、Azure Automation で Azure クラウド サービスを管理できます。 Azure Automation には、このようなクラウド サービス PowerShell コマンドレットがあらかじめ用意されており、サービス内ですべてのクラウド サービス管理タスクを実行することができます。 Azure Automation 内のこれらのコマンドレットと別の Azure サービスのコマンドレットを組み合わせて、Azure サービスおよびサード パーティ システム全体の複雑なタスクを自動化することもできます。

Azure Automation を使用して、次のような Azure Cloud Services を管理します。

* [cscfg または cspkg が Azure BLOB ストレージで更新されるたびのクラウド サービスの継続的なデプロイ](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [クラウド サービスの並列インスタンスの再起動 (一度に 1 つのアップグレード ドメイン)](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>次のステップ
ここまで、Azure Automation の基本と Azure Automation を使用して Azure クラウド サービスを管理する方法について説明しました。Azure Automation の詳細については、これらのリンクを参照してください。

* [Azure Automation Overview (Azure Automation の概要)](../automation/automation-intro.md)
* [初めての Runbook](../automation/learn/powershell-runbook-managed-identity.md)