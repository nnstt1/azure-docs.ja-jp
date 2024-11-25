---
title: 管理エンドポイント IP アドレスの検出
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance 管理エンドポイントのパブリック IP アドレスを取得し、組み込みファイアウォールの保護機能を検証する方法について説明します
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: mathoma
ms.date: 12/04/2018
ms.openlocfilehash: 8603f026d396316edf9d83e52ef33973ddc8edd7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751226"
---
# <a name="determine-the-management-endpoint-ip-address---azure-sql-managed-instance"></a>管理エンドポイント IP アドレスを確認する - Azure SQL Managed Instance 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance 仮想クラスターには、Azure で管理操作のために使用される管理エンドポイントが含まれています。 管理エンドポイントは、ネットワーク レベルでは組み込みのファイアウォールにより保護され、アプリケーション レベルでは相互の証明書の検証により保護されます。 管理エンドポイントの IP アドレスを確認することはできますが、このエンドポイントにアクセスすることはできません。

管理 IP アドレスを確認するには、ご利用の SQL Managed Instance FQDN 上で [DNS 参照](/windows-server/administration/windows-commands/nslookup)を実行します: `mi-name.zone_id.database.windows.net`。 これにより、`trx.region-a.worker.vnet.database.windows.net` のような DNS エントリが返されます。 その後、".vnet" を削除した状態で、この FQDN で DNS 参照を実行できます。 これにより、管理 IP アドレスが返されます。 

\<MI FQDN\> を SQL Managed Instance の DNS エントリに置き換えると、この PowerShell コードによって以上の作業がすべて自動的に行われます: `mi-name.zone_id.database.windows.net`。
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

SQL Managed Instances と接続の詳細については、「[Azure SQL Managed Instance の接続アーキテクチャ](connectivity-architecture-overview.md)」を参照してください。
