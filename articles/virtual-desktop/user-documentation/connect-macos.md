---
title: macOS から Azure Virtual Desktop に接続する - Azure
description: macOS クライアントを使用して Azure Virtual Desktop に接続する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 04/08/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 3626bd66f02b747f079bfd6777c86a7f7bfaba92
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2021
ms.locfileid: "113034318"
---
# <a name="connect-to-azure-virtual-desktop-with-the-macos-client"></a>macOS クライアントを使用して Azure Virtual Desktop に接続する

> 適用対象: macOS 10.12 以降

>[!IMPORTANT]
>この内容は、Azure Resource Manager Azure Virtual Desktop オブジェクトを含む Azure Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトを含まない Azure Virtual Desktop (クラシック) を使用している場合は、[こちらの記事](../virtual-desktop-fall-2019/connect-macos-2019.md)を参照してください。

ダウンロード可能なクライアントを使用して、ご使用の macOS デバイスから Azure Virtual Desktop リソースにアクセスできます。 このガイドでは、クライアントを設定する方法について説明します。

## <a name="install-the-client"></a>クライアントをインストールします。

開始するには、クライアントを[ダウンロード](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12)してご使用の macOS デバイスにインストールします。

## <a name="subscribe-to-a-feed"></a>フィードのサブスクライブ

管理者から提供されたフィードにサブスクライブすると、ご使用の macOS デバイスで使用可能な管理対象リソースの一覧が取得されます。

フィードをサブスクライブするには:

1. サービスに接続して自分のリソースを取得するには、メイン ページで **[ワークスペースの追加]** を選択します。
2. フィード URL を入力します。 URL またはメール アドレスを指定できます。
   - URL を使用する場合は、管理者から提供されたものを使用します。 通常、URL は <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery> です。
   - 電子メールを使用するには、電子メール アドレスを入力します。 これにより、メール アドレスに関連付けられている URL を検索するようにクライアントに指示されます (管理者がそのようにサーバーを構成した場合)。
   - US Gov ポータルから接続するには、<https://rdweb.wvd.azure.us/api/arm/feeddiscovery> を使用します。
3. **[追加]** を選択します。
4. メッセージが表示されたら、自分のユーザー アカウントでサインインします。

サインインすると、使用可能なリソースの一覧が表示されるはずです。

フィードをサブスクライブすると、フィードのコンテンツは定期的に自動的に更新されます。 管理者によって行われる変更に基づいて、リソースが追加、変更、または削除されることがあります。

## <a name="next-steps"></a>次のステップ

macOS クライアントの詳細については、「[macOS クライアントの概要](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/)」のドキュメントで確認してください。
