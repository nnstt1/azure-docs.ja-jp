---
title: Microsoft Store クライアントに接続する - Azure
description: Microsoft Store クライアントを使用して Azure Virtual Desktop に接続する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 10/05/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: d48ce1f2d885783901f4b5d74dfaebf1854943ab
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2021
ms.locfileid: "113034326"
---
# <a name="connect-with-the-microsoft-store-client"></a>Microsoft Store クライアントを使用した接続

>適用対象:Windows 10。

Windows 10 を使用しているデバイス上の Azure Virtual Desktop リソースにアクセスできます。

## <a name="install-the-microsoft-store-client"></a>Microsoft Store クライアントをインストールする

現在のユーザーのためにクライアントをインストールできます。これに管理者権限は必要ありません。 または、デバイス上のすべてのユーザーがクライアントにアクセスできるように、管理者がクライアントのインストールと構成を行えます。

インストールが完了すると、クライアントはスタート メニューからリモート デスクトップを検索することにより起動できます。

作業を開始するには、[Microsoft Store からクライアントをダウンロードしてインストールします](https://www.microsoft.com/store/productId/9WZDNCRFJ3PS)。

## <a name="subscribe-to-a-workspace"></a>ワークスペースにサブスクライブする

管理者から提供されたワークスペースにサブスクライブして、自分の PC でアクセスできる管理対象リソースの一覧を取得します。

ワークスペースにサブスクライブするには:

1. 接続センターの画面で、 **[+追加]** をタップし、次に **[ワークスペース]** をタップします。
2. [ワークスペース URL] フィールドに、管理者から提供されたワークスペース URL を入力します。ワークスペース URL には、URL またはメール アドレスのいずれかを指定できます。
   
   - ワークスペース URL を使用する場合は、管理者から付与された URL を使用します。
   - Azure Virtual Desktop から接続しようとしている場合は、使用しているサービスのバージョンに応じて、次のいずれかの URL を使用します。
       - Azure Virtual Desktop (クラシック): `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`。
       - Azure Virtual Desktop: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`。
  
3. **[サブスクライブ]** をタップします。
4. メッセージが表示されたら、資格情報を入力します。
5. サブスクライブ後、接続センターにワークスペースが表示されるはずです。

ワークスペースは、管理者によって行われる変更に基づいて、追加、変更、または削除されることがあります。

## <a name="next-steps"></a>次のステップ

Microsoft Store クライアントの使用方法の詳細については、「[Microsoft Store クライアントの使用を開始する](/windows-server/remote/remote-desktop-services/clients/windows/)」を参照してください。