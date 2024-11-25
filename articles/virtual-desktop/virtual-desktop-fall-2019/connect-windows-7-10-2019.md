---
title: Windows 10 または 7 で Azure Virtual Desktop (クラシック) に接続する - Azure
description: Windows デスクトップ クライアントを使用して Azure Virtual Desktop (クラシック) に接続する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 07/16/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 004fff41e30ca9c60d51847035e30262e359270e
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2021
ms.locfileid: "113031123"
---
# <a name="connect-with-the-windows-desktop-classic-client"></a>Windows デスクトップ (クラシック) クライアントを使用して接続する

> 適用対象:Windows 7、Windows 10、および Windows 10 IoT Enterprise

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Azure Virtual Desktop オブジェクトをサポートしていない Azure Virtual Desktop (クラシック) に適用されます。 Azure Resource Manager Azure Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../user-documentation/connect-windows-7-10.md)を参照してください。

Windows デスクトップ クライアントを使用して、Windows 7、Windows 10、Windows 10 IoT Enterprise を使用しているデバイス上の Azure Virtual Desktop リソースにアクセスできます。 このクライアントでは、Windows 8 または Windows 8.1 がサポートされていません。

>[!NOTE]
>Windows クライアントは自動的に既定の Azure Virtual Desktop (クラシック) になります。 ただしクライアントは、ユーザーが Azure Resource Manager リソースも所有していることを検出した場合、そのリソースを自動的に追加するか、リソースが利用可能であることをユーザーに通知します。

> [!IMPORTANT]
> Azure Virtual Desktop では、RemoteApp とデスクトップ接続 (RADC) クライアントおよびリモート デスクトップ接続 (MSTSC) クライアントはサポートされていません。

> [!IMPORTANT]
> Azure Virtual Desktop では、現在、Windows Store のリモート デスクトップ クライアントはサポートされていません。

## <a name="install-the-windows-desktop-client"></a>Windows デスクトップ クライアントをインストールする

ご自身の Windows バージョンに合ったクライアントを選択してください。

- [Windows (64 ビット)](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 ビット](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

現在のユーザー用にのクライアントをインストールできます。この場合、管理者権限は必要ありません。または、管理者がクライアントをインストールして構成し、デバイス上のすべてのユーザーがアクセスできるようにすることができます。

インストールが完了すると、クライアントはスタート メニューから **リモート デスクトップ** を検索することにより起動できます。

## <a name="subscribe-to-a-workspace"></a>ワークスペースのサブスクライブ

ワークスペースをサブスクライブするには、2 つの方法があります。 クライアントは、職場または学校のアカウントから利用可能なリソースを探索したり、クライアントがリソースを見つけられない場合にリソースの場所の URL を直接指定したりすることができます。 ワークスペースをサブスクライブした後は、次のいずれかの方法でリソースを起動できます。

- 接続センターにアクセスし、リソースをダブルクリックして起動します。
- また、スタート メニューにアクセスし、ワークスペース名を含むフォルダーを探すか、検索バーにリソース名を入力することもできます。

### <a name="subscribe-with-a-user-account"></a>ユーザー アカウントを使用してサブスクライブする

1. クライアントのメイン ページから、 **[サブスクライブする]** を選択します。
2. メッセージが表示されたら、自分のユーザー アカウントでサインインします。
3. リソースが接続センターに表示され、ワークスペース別にグループ化されます。

### <a name="subscribe-with-a-url"></a>URL を使用してサブスクライブする

1. クライアントのメイン ページから、 **[Subscribe with URL]** \(URL を使用してサブスクライブする\) を選択します。
2. ワークスペース URL または電子メール アドレスを入力します。
   - **ワークスペースの URL** を使用する場合は、管理者から提供されたものを使用します。 Azure Virtual Desktop からリソースにアクセスする場合、次のいずれかの URL を使用できます。
     - Azure Virtual Desktop (クラシック): `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`
     - Azure Virtual Desktop: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`
   - 代わりに **[メール アドレス]** フィールドを使用する場合、メール アドレスを入力します。 これにより、メール アドレスに関連付けられている URL を検索するようにクライアントに指示されます (管理者が[メール検出](/windows-server/remote/remote-desktop-services/rds-email-discovery)を設定している場合)。
3. **[次へ]** を選択します。
4. メッセージが表示されたら、自分のユーザー アカウントでサインインします。
5. リソースがワークスペース別にグループ化された状態で接続センターに表示されるはずです。

## <a name="next-steps"></a>次のステップ

Windows デスクトップ クライアントの使用方法の詳細については、「[Windows デスクトップ クライアントの概要](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/)」を参照してください。
