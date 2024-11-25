---
title: Azure NetApp Files にアクセスするための NetApp アカウントを作成する | Microsoft Docs
description: 容量プールを設定してボリュームを作成できるよう Azure NetApp Files にアクセスして NetApp アカウントを作成する方法について説明します。
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
ms.date: 10/04/2021
ms.author: b-juche
ms.openlocfilehash: 994af6291f55e9f1dd44f2f522f9273639531257
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407059"
---
# <a name="create-a-netapp-account"></a>NetApp アカウントを作成する
NetApp アカウントを作成することによって、容量プールを設定し、その後、ボリュームを作成することができます。 新しい NetApp アカウントの作成は、[Azure NetApp Files] ブレードを使用して行います。

## <a name="before-you-begin"></a>開始する前に

NetApp Resource Provider を使用する場合、サブスクリプションが登録済みになっている必要があります。 「[NetApp リソース プロバイダーの登録](azure-netapp-files-register.md)」をご覧ください。

## <a name="steps"></a>手順 

1. Azure portal にサインインします。 
2. 次のいずれかの方法で [Azure NetApp Files] ブレードにアクセスします。  
   * Azure portal の検索ボックスで「**Azure NetApp Files**」を検索します。  
   * ナビゲーションの **[すべてのサービス]** をクリックし、フィルターで Azure NetApp Files を特定します。  

   [Azure NetApp Files] ブレードは、その横に表示される星のアイコンをクリックすることで、"お気に入り" に登録することができます。 

3. **[+ 追加]** をクリックして新しい NetApp アカウントを作成します。  
   [New NetApp account]\(新しい NetApp アカウント\) ウィンドウが表示されます。  

4. NetApp アカウントについて次の情報を入力します。 
   * **アカウント名**  
     サブスクリプションの一意の名前を指定します。
   * **[サブスクリプション]**  
     既存のサブスクリプションからいずれかのサブスクリプションを選択します。
   * **リソース グループ**   
     既存のリソース グループを使用するか、新しいリソース グループを作成します。
   * **場所**  
     アカウントとその子リソースを置くリージョンを選択します。  

     ![新しい NetApp アカウント](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. **Create** をクリックしてください。     
   作成した NetApp アカウントが [Azure NetApp Files] ブレードに表示されます。 

> [!NOTE] 
> Azure NetApp Files サービスへのアクセスが付与されていない場合、最初の NetApp アカウントの作成を試行したときに次のエラーを受信します。  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`

## <a name="next-steps"></a>次のステップ  

[容量プールの作成](azure-netapp-files-set-up-capacity-pool.md)

