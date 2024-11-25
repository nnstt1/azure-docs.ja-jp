---
title: Azure Data Studio でデータ コントローラーを作成する
description: Azure Data Studio でデータ コントローラーを作成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: a42b0ff8a23f3504b642ab79a1b85718aa0c62e1
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557758"
---
# <a name="create-data-controller-in-azure-data-studio"></a>Azure Data Studio でデータ コントローラーを作成する

展開ウィザードとノートブックから Azure Data Studio を使用して、データ コントローラーを作成できます。


## <a name="prerequisites"></a>前提条件

- Kubernetes クラスターにアクセスし、デプロイ先の Kubernetes クラスターを示すように kubeconfig ファイルを構成しておく必要があります。
- `arcdata` 拡張機能を使用して、**Azure Data Studio**、**Azure Arc** という Azure Data Studio の拡張機能、および Azure CLI などの[クライアント ツールをインストール](install-client-tools.md)する必要があります。
- Azure Data Studio で Azure にログインする必要があります。  これを行うには、「CTRL/Command + SHIFT + P」と入力してコマンド テキスト ウィンドウを開き、「**Azure**」と入力します。  **Azure:サインイン**」を検索して選びます。   パネルの右上にある [+] アイコンをクリックして、Azure アカウントを追加します。

## <a name="use-the-deployment-wizard-to-create-azure-arc-data-controller"></a>展開ウィザードを使用して Azure Arc データ コントローラーをデプロイする

展開ウィザードを使用して Azure Arc データ コントローラーを作成するには、次の手順に従います。

1. Azure Data Studio の左側にあるナビゲーションで、[接続] タブをクリックします。
1. [接続] パネルの上部にある **[...]** ボタンをクリックし、 **[新しい展開...]** を選択します。
1. 新しい展開ウィザードで、 **[Azure Arc データ コントローラー]** を選択し、下部にある **[選択]** ボタンをクリックします。
1. 前提条件のツールが使用可能であり、必要なバージョンを満たしていることを確認します。 **[次へ]** をクリックします。
1. 既定の kubeconfig ファイルを使用するか、別のファイルを選択します。  **[次へ]** をクリックします。
1. Kubernetes クラスター コンテキストを選択します。 **[次へ]** をクリックします。
1. ターゲットの Kubernetes クラスターに応じて、展開構成プロファイルを選択します。 **[次へ]** をクリックします。
1. 目的のサブスクリプションとリソース グループを選択します。
1. Azure の場所を選択します。
   
   ここで選択した Azure の場所は、データ コントローラーと管理されるデータベース インスタンスに関する *メタデータ* が格納される Azure 内の場所です。 実際には、データ コントローラーとデータベース インスタンスは、その場所がどこにあるのかに関係なく、Kubernetes クラスターに作成されます。
   
   完了したら、 **[次へ]** をクリックします。

1. データ コントローラーの名前と、データ コントローラーが作成される名前空間の名前を入力します。

    データ コントローラーと名前空間の名前は、Kubernetes クラスターでカスタム リソースを作成するために使用されるため、[Kubernetes の名前付け規則](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names)に従っている必要があります。
    
    既に名前空間が存在する場合は、名前空間に他の Kubernetes オブジェクト (ポッドなど) がまだ含まれていなければ使用されます。名前空間が存在しない場合は、名前空間の作成が試みられます。  Kubernetes クラスターで名前空間を作成するには、Kubernetes クラスター管理者特権が必要です。  Kubernetes クラスター管理者特権を持っていない場合は、[Kubernetes ネイティブのツールを使用したデータ コントローラーの作成](./create-data-controller-using-kubernetes-native-tools.md)に関する記事の最初のいくつかの手順を実行するように、Kubernetes クラスター管理者に依頼してください。これらの手順は、このウィザードを完了する前に、Kubernetes 管理者が実行する必要があります。


1. データ コントローラーが展開されるストレージ クラスを選択します。 
1.  ユーザー名とパスワードを入力し、データ コントローラー管理者ユーザー アカウントのパスワードを確認します。 **[次へ]** をクリックします。

1. 展開構成を確認します。
1. **[展開]** をクリックして目的の構成を展開するか、または **[ノートブックへのスクリプト]** をクリックして展開手順を確認したり、ストレージ クラス名やサービスの種類などの必要な変更を行ったりします。 ノートブックの上部にある **[すべて実行]** をクリックします。

## <a name="monitoring-the-creation-status"></a>作成状態の監視

コントローラーの作成が完了するまでに数分かかります。 次のコマンドを使用して、別のターミナル ウィンドウで進行状況を監視できます。

> [!NOTE]
>  次のコマンド例では、"arc" という名前のデータ コントローラーと Kubernetes 名前空間が作成されていることを前提としています。  異なる名前の名前空間/データ コントローラーを使用している場合は、"arc" をその名前で置き換えることができます。

```console
kubectl get datacontroller --namespace arc
```

```console
kubectl get pods --namespace arc
```

次のようなコマンドを実行して、特定のポッドの作成状態を確認することもできます。  これは特に、何らの問題をトラブルシューティングするのに役立ちます。

```console
kubectl describe pod/<pod name> --namespace arc

#Example:
#kubectl describe pod/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>作成の問題のトラブルシューティング

作成で問題が発生した場合は、「[トラブルシューティング ガイド](troubleshoot-guide.md)」を参照してください。
