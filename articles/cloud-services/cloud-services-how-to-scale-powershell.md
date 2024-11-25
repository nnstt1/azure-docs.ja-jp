---
title: Windows PowerShell で Azure クラウド サービス (クラシック) をスケールリングする | Microsoft Docs
description: (クラシック) PowerShell を使用して、Azure で Web ロールまたは worker ロールをスケールインまたはスケールアウトする方法について説明します。
ms.topic: article
ms.service: cloud-services
ms.subservice: autoscale
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: de198a3a2449d43461547e216eb0acfc6b02d527
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822276"
---
# <a name="how-to-scale-an-azure-cloud-service-classic-in-powershell"></a>PowerShell で Azure Cloud Services (クラシック) をスケーリングする方法

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

Windows PowerShell を使用して、Web ロールまたは worker ロールのスケールインやスケールアウトを行うには、インスタンスを追加または削除します。  

## <a name="log-in-to-azure"></a>Azure にログインする

PowerShell を使ってサブスクリプションで操作を行う前に、ログインする必要があります。

```powershell
Add-AzureAccount
```

アカウントに関連付けられているサブスクリプションが複数ある場合は、クラウド サービスの場所に応じて、現在のサブスクリプションの変更が必要になる可能性があります。 現在のサブスクリプションを確認するには、次のコマンドを実行します。

```powershell
Get-AzureSubscription -Current
```

現在のサブスクリプションを変更する必要がある場合は、次のコマンドを実行します。

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>ロールの現在のインスタンス数の確認

ロールの現在の状態を確認するには、次のコマンドを実行します。

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

現在の OS バージョン、インスタンス数など、ロールに関する情報が表示されます。 この場合、ロールは 1 つのインスタンスです。

![ロールの情報](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>インスタンスを追加してロールをスケールアウト

ロールをスケールアウトするには、目的のインスタンス数を **Count** パラメーターとして **Set-AzureRole** コマンドレットに渡します。

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

新しいインスタンスがプロビジョニングおよび開始されている間、コマンドレットは一時的にブロックされています。 この間に、新しい PowerShell ウィンドウを開いて、前に示した **Get-AzureRole** を呼び出すと、新しいターゲット インスタンス数が表示されます。 ポータルでロールの状態を調べると、新しいインスタンスが開始中であることが表示されます。

![ポータルで開始されている VM インスタンス](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

新しいインスタンスが完全に開始されると、コマンドレットは適切に結果を返します。

![ロール インスタンス増加の成功](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>インスタンスを削除してロールをスケールイン

ロールをスケールインするには、同じ方法でインスタンスを削除します。 **Set-AzureRole** で **Count** パラメーターを、スケール イン操作が完了した後の目的のインスタンス数に設定します。

## <a name="next-steps"></a>次のステップ

PowerShell からクラウド サービスの自動スケールを構成することはできません。 自動スケールの構成については、「[クラウド サービスの自動スケールの方法](cloud-services-how-to-scale-portal.md)」を参照してください。
