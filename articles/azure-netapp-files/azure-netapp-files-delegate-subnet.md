---
title: サブネットを Azure NetApp Files に委任する | Microsoft Docs
description: サブネットを Azure NetApp Files に委任する方法について説明します。 ボリュームを作成する際は、委任されたサブネットを指定します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/25/2021
ms.author: b-juche
ms.openlocfilehash: cdb184d4b96e4cfee2b5450f35c947efb768da9b
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866978"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>サブネットを Azure NetApp Files に委任する 

サブネットを Azure NetApp Files に委任する必要があります。   ボリュームを作成する際は、委任されたサブネットを指定する必要があります。

## <a name="considerations"></a>考慮事項

* 新しいサブネットを作成するウィザードでは、既定で /24 ネットワーク マスクになっており、251 個の使用可能な IP アドレスが提供されます。 /28 ネットワーク マスクを使用すると、11 個の使用可能な IP アドレスが提供され、ほとんどのユース ケースにはそれで十分です。 多くのボリュームとストレージ エンドポイントが予想される SAP HANA などのシナリオでは、より大きなサブネット (/26 ネットワーク マスクなど) を検討する必要があります。 Azure Virtual Network (VNet) で多数のクライアントまたは VM IP アドレスを予約する必要がなき場合は、ウィザードで提案されている既定のネットワーク マスク /24 のままにすることもできます。 委任されたネットワークのネットワーク マスクは、最初の作成後に変更できないことに注意してください。 
* 各 VNet で、1 つのサブネットだけを Azure NetApp Files に委任できます。   
   Azure では、VNet 内に複数の委任されたサブネットを作成できます。  ただし、委任されたサブネットを複数使用している場合、新しいボリュームを作成しようとすると失敗します。  
   1 つの VNet には、委任されたサブネットを 1 つだけ含めることができます。 NetApp アカウントでは、複数の Vnet にボリュームを配置でき、それぞれに独自の委任されたサブネットがあります。  
* 委任されたサブネット内のネットワーク セキュリティ グループまたはサービス エンドポイントを指定することはできません。 そうした場合、サブネットの委任が失敗します。
* グローバルにピアリングされた仮想ネットワークからボリュームへのアクセスは、現在はサポートされていません。
* Azure NetApp Files 用の委任サブネットの場合は、[ユーザー定義ルート](../virtual-network/virtual-networks-udr-overview.md#custom-routes) (UDR) とネットワーク セキュリティ グループ (NSG) はサポートされていません。 しかし、UDR と NSG を他のサブネットに適用することはできます。これは、Azure NetApp Files に委任されたサブネットと同じ VNet 内であっても同様です。  
   Azure NetApp Files によって、委任されたサブネットへのシステム ルートが作成されます。 トラブルシューティングのために必要な場合、ルートはルート テーブルの **[有効なルート]** に表示されています。

## <a name="steps"></a>手順

1.  Azure portal で **[仮想ネットワーク]** ブレードに移動し、Azure NetApp Files のために使用する仮想ネットワークを選択します。    

1. 仮想ネットワーク ブレードで **[サブネット]** を選択し、 **[+ サブネット]** ボタンをクリックします。 

1. [サブネットの追加] ページで以下の必須フィールドを指定して、Azure NetApp Files のために使用する新しいサブネットを作成します。
    * **Name**:サブネット名を指定します。
    * **アドレス範囲**:IP アドレス範囲を指定します。
    * **サブネットの委任**: **[Microsoft.NetApp/volumes]** を選択します。 

      ![サブネットの委任](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
[Azure NetApp Files のためのボリュームを作成する](azure-netapp-files-create-volumes.md)ときに、サブネットを作成して委任することもできます。 

## <a name="next-steps"></a>次のステップ

* [Azure NetApp Files のボリュームを作成する](azure-netapp-files-create-volumes.md)
* [Azure サービスの仮想ネットワーク統合について理解する](../virtual-network/virtual-network-for-azure-services.md)
