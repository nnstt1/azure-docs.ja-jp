---
title: Azure Virtual Desktop (クラシック) の環境 - Azure
description: Azure Virtual Desktop (クラシック) 環境の基本的な要素。
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9062565f5c13d7b278b57068df79c1bb4de66424
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111745063"
---
# <a name="azure-virtual-desktop-classic-environment"></a>Azure Virtual Desktop (クラシック) の環境

>[!IMPORTANT]
>この内容は、Azure Resource Manager Azure Virtual Desktop オブジェクトをサポートしていない Azure Virtual Desktop (クラシック) に適用されます。 Azure Resource Manager Azure Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../environment-setup.md)を参照してください。

Azure Virtual Desktop は、仮想デスクトップと RemoteApp への簡単で安全なアクセスをユーザーに提供するサービスです。 このトピックでは、Azure Virtual Desktop 環境の一般構造について少し詳しく説明します。

## <a name="tenants"></a>テナント

Azure Virtual Desktop テナントは、Azure Virtual Desktop 環境を管理するためのプライマリ インターフェイスです。 各 Azure Virtual Desktop テナントは、環境にサインインするユーザーを含む Azure Active Directory に関連付ける必要があります。 Azure Virtual Desktop テナントから、ユーザーのワークロードを実行するホスト プールの作成を開始できます。

## <a name="host-pools"></a>ホスト プール

ホスト プールとは、Azure Virtual Desktop エージェントを実行するときに Azure Virtual Desktop にセッション ホストとして登録する Azure 仮想マシンのコレクションです。 ホスト プール内のすべてのセッション ホスト仮想マシンは、一貫したユーザー エクスペリエンスを実現するために同じイメージから供給される必要があります。

ホスト プールには以下の 2 種類があります。

- 個人用。各セッション ホストが個々のユーザーに割り当てられています。
- プール。セッション ホストは、ホスト プール内のアプリ グループに承認されたユーザーからの接続を受け入れることができます。

ホスト プールに追加のプロパティを設定して、負荷分散の動作、各セッション ホストが取得できるセッション数、およびユーザーが Azure Virtual Desktop セッションへのサインイン時にホスト プール内のセッション ホストに対して実行できる操作を変更できます。 ユーザーに公開されるリソースは、アプリ グループを通じて制御します。

## <a name="app-groups"></a>アプリ グループ

アプリ グループは、ホスト プール内のセッション ホストにインストールされているアプリケーションの論理グループです。 アプリ グループには以下の 2 種類があります。

- RemoteApp。ユーザーは、個別に選択されてアプリ グループに公開された RemoteApp にアクセスします
- デスクトップ。ユーザーは完全なデスクトップにアクセスします

既定では、ホスト プールを作成するたびに、デスクトップ アプリ グループ ("デスクトップ アプリケーション グループ" という名前) が自動的に作成されます。 このアプリ グループはいつでも削除できます。 ただし、デスクトップ アプリ グループが存在する間は、ホスト プールに別のデスクトップ アプリ グループを作成できません。 RemoteApp を公開するには、RemoteApp アプリ グループを作成する必要があります。 複数の RemoteApp アプリ グループを作成してさまざまな worker シナリオに対応することができます。 別の RemoteApp アプリ グループに重複する RemoteApp を含めることもできます。

ユーザーにリソースを公開するには、それらをアプリ グループに割り当てる必要があります。 ユーザーをアプリ グループに割り当てるときは、以下の事項を検討してください。

- 同じホスト プール内のデスクトップ アプリ グループと RemoteApp アプリ グループの両方にユーザーを割り当てることはできません。
- ユーザーは、同じホスト プール内の複数のアプリ グループに割り当てることができ、そのフィードは両方のアプリ グループの累積になります。

## <a name="tenant-groups"></a>テナント グループ

Azure Virtual Desktop では、Azure Virtual Desktop テナントはほとんどのセットアップと構成が行われる場所です。 Azure Virtual Desktop テナントには、ホスト プール、アプリ グループ、およびアプリ グループのユーザー割り当てが含まれています。 ただし、クラウド サービス プロバイダー (CSP) またはホスティング パートナーである場合には特に、特定の状況で一度に複数の Azure Virtual Desktop テナントを管理する必要があります。 このような場合は、カスタム Azure Virtual Desktop テナント グループを使用して、各顧客の Azure Virtual Desktop テナントを配置し、アクセスを一元的に管理できます。 ただし、1 つの Azure Virtual Desktop テナントのみを管理している場合、テナント グループの概念は適用されず、既定のテナント グループに存在するテナントの操作と管理を続行できます。

## <a name="end-users"></a>エンド ユーザー

アプリ グループにユーザーを割り当てた後、いずれかの Azure Virtual Desktop クライアントを使用して Azure Virtual Desktop のデプロイに接続できます。

## <a name="next-steps"></a>次のステップ

委任されたアクセスとユーザーにロールを割り当てる方法について詳しくは、「[Azure Virtual Desktop での委任されたアクセス](delegated-access-virtual-desktop-2019.md)」を参照してください。

Azure Virtual Desktop テナントを設定する方法については、「[Azure Virtual Desktop にテナントを作成する](tenant-setup-azure-active-directory.md)」を参照してください。

Azure Virtual Desktop に接続する方法については、次のいずれかの記事を参照してください。

- [Windows 10 または Windows 7 から接続する](connect-windows-7-10-2019.md)
- [Web ブラウザーから接続する](connect-web-2019.md)
