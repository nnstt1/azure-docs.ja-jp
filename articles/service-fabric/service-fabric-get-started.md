---
title: Windows 開発環境を設定します
description: ランタイム、SDK、およびツールをインストールし、ローカル開発クラスターを作成します。 このセットアップを完了すると、Windows でアプリケーションを構築する準備が整います。
author: peterpogorski
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: sfrev, devx-track-azurepowershell
ms.openlocfilehash: 8f520d74f8b3ccac8c31fce6fbf4af2016cb51ac
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130217717"
---
# <a name="prepare-your-development-environment-on-windows"></a>Windows で開発環境を準備する

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)
>
>

Windows 開発コンピューターで [Azure Service Fabric アプリケーション][1]をビルドして実行するには、Service Fabric ランタイム、SDK、およびツールをインストールしてください。 また、SDK に含まれる [Windows PowerShell スクリプトの実行を有効にする](#enable-powershell-script-execution)必要があります。

## <a name="prerequisites"></a>前提条件

サポートされている [Windows バージョン](service-fabric-versions.md#supported-windows-versions-and-support-end-date)を使用していることを確認します。

## <a name="install-the-sdk-and-tools"></a>SDK とツールのインストール

SDK とツールをインストールするには、Web Platform Installer (Web PI) を使用することをお勧めします。 Web PI を使用してランタイム エラーが発生した場合は、特定の Service Fabric リリースのリリース ノートで、インストーラーへの直接リンクを検索することもできます。 リリース ノートは、[Service Fabric チーム ブログ](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)の各種リリースの発表で見つけることができます。

> [!NOTE]
> ローカルな Service Fabric 開発クラスターのアップグレードはサポートされていません。

### <a name="to-use-visual-studio-2017-or-2019"></a>Visual Studio 2017 または 2019 を使用するには

Service Fabric ツールは、Visual Studio 2019 および 2017 の Azure 開発ワークロードに含まれています。 このワークロードを Visual Studio のインストールの一環として有効にします。
さらに、Web Platform Installer を使用して Microsoft Azure Service Fabric SDK とランタイムをインストールする必要があります。

* [Microsoft Azure Service Fabric SDK をインストールする][core-sdk]

### <a name="sdk-installation-only"></a>SDK のみのインストール

SDK のみが必要な場合は、次のパッケージをインストールすることができます。

* [Microsoft Azure Service Fabric SDK をインストールする][core-sdk]

現在のバージョンは次のとおりです。

* Service Fabric SDK および Tools 5.2.1235
* Service Fabric ランタイム 8.2.1235

サポートされているバージョンの一覧については、[Service Fabric のバージョン](service-fabric-versions.md)に関するページを参照してください

> [!NOTE]
> アプリケーションまたはクラスターのアップグレードには、1 台のマシンのクラスター (OneBox) はサポートされていません。クラスターのアップグレードを実行する必要がある場合や、アプリケーションのアップグレードの実行で問題がある場合は、OneBox クラスターを削除し再作成します。 

## <a name="enable-powershell-script-execution"></a>PowerShell スクリプトの実行の有効化

Service Fabric は、ローカル開発クラスターの作成、および Visual Studio からのアプリケーションのデプロイに、Windows PowerShell スクリプトを使用します。 既定では、Windows はこれらのスクリプトの実行をブロックします。 これらを有効にするには、PowerShell 実行ポリシーを変更する必要があります。 管理者として PowerShell を開き、次のコマンドを入力します。

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="install-docker-optional"></a>Docker のインストール (省略可能)

Service Fabric は、マシン クラスター全体にマイクロサービスをデプロイする[コンテナー オーケストレーター](service-fabric-containers-overview.md)です。 ローカル開発クラスターで Windows コンテナー アプリケーションを実行するには、まず Docker for Windows をインストールする必要があります。 [Docker CE for Windows (安定版)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) を入手します。 Docker をインストールして起動したら、トレイ アイコンを右クリックし、 **[Switch to Windows containers]\(Windows コンテナーに切り替える\)** を選択します。 この手順は、Windows に基づいて Docker イメージを実行するために必要です。

## <a name="next-steps"></a>次のステップ

開発環境のセットアップが完了したので、アプリのビルドと実行を開始してください。

* [アプリケーションを作成、デプロイ、および管理する方法](service-fabric-tutorial-create-dotnet-app.md)
* [プログラミング モデル Reliable Services と Reliable Actors について学びます](service-fabric-choose-framework.md)
* [GitHub での Service Fabric コード サンプルの確認](/samples/browse/?products=azure)
* [Service Fabric エクスプローラーを使用したクラスターの視覚化](service-fabric-visualizing-your-cluster.md)
* [Windows で Linux 開発環境を準備する](service-fabric-local-linux-cluster-windows.md)
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Service Fabric キャンペーン ページ"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI のリンク"
[full-bundle-dev15]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI のリンク"
[core-sdk]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI のリンク"
[powershell5-download]:https://www.microsoft.com/download/details.aspx?id=54616
