---
title: ゲスト OS ファミリ 1 の提供終了に関する通知 | Microsoft Docs
description: Azure ゲスト OS ファミリ 1 の提供が終了した時期と、利用中のサービスがその影響を受けるかどうかを判断する方法について説明します
services: cloud-services
ms.subservice: auto-os-updates
documentationcenter: na
author: raiye
manager: timlt
ms.service: cloud-services
ms.topic: article
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: b4ba01ddeb0f0fe7392abc0eec2d947ec387a99e
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108736159"
---
# <a name="guest-os-family-1-retirement-notice"></a>ゲスト OS ファミリ 1 の提供終了に関する通知

OS ファミリ 1 の提供終了は、2013 年 6 月 1 日に最初に発表されました。

**2014 年 9 月 2 日** : Windows Server 2008 オペレーティング システムに基づく Azure ゲスト オペレーティング システム (ゲスト OS) ファミリ 1.x が正式に提供終了となりました。 ファミリ 1 を使用して新しいサービスをデプロイしようとする試みまたは既存のサービスをアップグレードしようとする試みはすべて失敗し、ゲスト OS ファミリ 1 の提供が終了したことを示すエラー メッセージが表示されます。

**2014 年 11 月 3 日** : ゲスト OS ファミリ 1 の延長サポートが終了し、完全に提供終了となりました。 ファミリ 1 を利用しているすべてのサービスは、この影響を受けます。 これらのサービスは、突然停止される可能性があります。 自分で手動でこれらのサービスをアップグレードしない限り、サービスが継続される保証はありません。

質問がある場合は、[Cloud Services の Microsoft Q&A 質問ページ](/answers/topics/azure-cloud-services.html)にアクセスするか、[Azure サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。

## <a name="are-you-affected"></a>利用中のサービスが影響を受けるか

次のいずれかの条件に該当する場合、利用中の Cloud Services は影響を受けます。

1. クラウド サービスの ServiceConfiguration.cscfg ファイルに "osFamily = 1" という値が明示的に指定されている。
2. クラウド サービスの ServiceConfiguration.cscfg ファイル内で osFamily の値が明示的に指定されていない。 現在、この場合、既定値として "1" が使用されます。
3. Azure Portal で、ゲスト オペレーティング システム ファミリの値として "Windows Server 2008" が表示されている。

クラウド サービスで実行されている OS ファミリを調べるには、Azure PowerShell を使用して次のスクリプトを実行します。最初に、[Azure PowerShell](/powershell/azure/) を設定する必要があります。 スクリプトの詳細については、「[Azure ゲスト OS ファミリ 1 の提供中止: 2014 年 6 月](/archive/blogs/ryberry/azure-guest-os-family-1-end-of-life-june-2014)」を参照してください。

```powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

スクリプトの出力の osFamily 列が空であるか、"1" が含まれている場合、クラウド サービスは、OS ファミリ 1 の提供終了の影響を受けます。

## <a name="recommendations-if-you-are-affected"></a>利用中のサービスが影響を受ける場合の推奨事項

クラウド サービスのロールを、サポートされているゲスト OS ファミリのいずれかに移行することをお勧めします。

**ゲスト OS ファミリ 4.x** - Windows Server 2012 R2 *(推奨)*

1. アプリケーションで SDK 2.1 以降と .NET Framework 4.0、4.5、または 4.5.1 を使用していることを確認します。
2. ServiceConfiguration.cscfg ファイルで osFamily 属性を "4" に設定し、クラウド サービスを再デプロイします。

**ゲスト OS ファミリ 3.x** - Windows Server 2012

1. アプリケーションで SDK 1.8 以降と .NET Framework 4.0 または 4.5 を使用していることを確認します。
2. ServiceConfiguration.cscfg ファイルで osFamily 属性を "3" に設定し、クラウド サービスを再デプロイします。

**ゲスト OS ファミリ 2.x** - Windows Server 2008 R2

1. アプリケーションで SDK 1.3 以降と .NET Framework 3.5 または 4.0 を使用していることを確認します。
2. ServiceConfiguration.cscfg ファイルで osFamily 属性を "2" に設定し、クラウド サービスを再デプロイします。

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>2014 年 11 月 3 日付けでゲスト OS ファミリ 1 の延長サポートが終了

ゲスト OS ファミリ 1 のクラウド サービスはサポートされていません。 サービスの中断を回避するには、できるだけ早くファミリ 1 から移行してください。

## <a name="next-steps"></a>次のステップ

最新の [ゲスト OS リリース](cloud-services-guestos-update-matrix.md)を確認します。
