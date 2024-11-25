---
title: トレーニングでの Azure DevTest Labs の使用
description: この記事では、Azure DevTest Labs でトレーニング用のラボを設定するために実行する詳細な手順について説明します。
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 6809ee2a9d08e059184ccbcb98c20ac5ea2160fa
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398119"
---
# <a name="use-azure-devtest-labs-for-training"></a>トレーニングでの Azure DevTest Labs の使用
Azure DevTest Labs を使用すると、開発とテストのみならず、さまざまな主要シナリオを実現できます。 たとえば、トレーニング用のラボを設定するシナリオが考えられます。 Azure DevTest Labs を使用してラボを作成し、個々の受講者が、まったく同じでかつ分離されたトレーニング環境を作成するためのカスタム テンプレートを提供できます。 各受講者が必要としている場合にのみトレーニング環境を利用できるようにすると共に、トレーニングに必要なリソース、たとえば仮想マシンが十分にトレーニング環境に含まれるようにするポリシーを適用できます。 最後に、受講者がワンクリックでアクセスできるよう、ラボを簡単に共有できます。

![Use DevTest Labs for training](./media/devtest-lab-training-lab/devtest-lab-training.png)

Azure DevTest Labs は、任意の仮想環境でトレーニングを実施するための次の要件を満たしています。 

* 受講者は、他の受講者によって作成された VM を見ることができない。
* すべてのトレーニング マシンが同じである。
* 受講者が各自のトレーニング環境を迅速にプロビジョニングできる。
* 受講者が必要以上の VM を使用できないようにし、コストを管理できる。また、受講者が使用していない VM をシャットダウンできる。
* トレーニング ラボを各受講者と簡単に共有できる。
* トレーニング ラボを繰り返し再利用できる。

この記事では、トレーニングの要件を満たすために使用できる Azure DevTest Labs の各種機能について説明します。 詳しい手順に従ってトレーニング用のラボをセットアップできます。  

## <a name="implementing-training-with-azure-devtest-labs"></a>Azure DevTest Labs でのトレーニングの実現
1. **ラボを作成する** 
   
    ラボは Azure DevTest Labs における開始点です。 ラボを作成したら、ラボへのユーザー (受講者) の追加、コストを管理するためのポリシーの設定、迅速に作成可能な VM イメージの定義など、各種タスクを実行できます。   
   
    次の表のリンクをクリックして詳細を確認してください。
   
   | タスク | 学習内容 |
   | --- | --- |
   | [Azure DevTest Labs でのラボの作成](devtest-lab-create-lab.md) |Azure Portal で Azure DevTest Labs のラボを作成する方法について学びます。 |
2. **既製の Marketplace イメージとカスタム イメージを使用してトレーニング用の VM を数分で作成する** 
   
    Azure Marketplace にあるさまざまなイメージから既製のイメージを選択し、ラボの受講者が利用できるようにすることが可能です。 既製のイメージが要件を満たしていない場合は、カスタム イメージを作成できます。 Azure Marketplace から入手した既製のイメージを使用してラボの VM を作成し、トレーニングに必要なソフトウェアをインストールしたうえで、その VM をカスタム イメージとしてラボに保存します。 
   
    次の表のリンクをクリックして詳細を確認してください。
   
   | タスク | 学習内容 |
   | --- | --- |
   | [Azure Marketplace イメージの構成](devtest-lab-configure-marketplace-images.md) |Azure Marketplace イメージで、トレーニング担当者に必要なイメージのみを選択できるようにする方法について説明します。 |
   | [カスタム イメージの作成](devtest-lab-create-template.md) |トレーニングに必要なソフトウェアを事前にインストールしてカスタム イメージを作成し、受講者がそのカスタム イメージを使用して VM を迅速に作成できるようにします。 |
3. **トレーニング マシン用に、再利用可能なテンプレートを作成する** 
   
    Azure DevTest Labs における数式とは、VM の作成に使用できる既定のプロパティ値の一覧です。 ラボで数式を作成するには、イメージ、VM サイズ (CPU と RAM の組み合わせ)、仮想ネットワークを選択します。 各受講者は、ラボの数式を確認して、VM の作成に使用することができます。 
   
    次の表のリンクをクリックして詳細を確認してください。
   
   | タスク | 学習内容 |
   | --- | --- |
   | [VM を作成するための DevTest ラボの数式の管理](devtest-lab-manage-formulas.md) |イメージ、VM サイズ (CPU と RAM の組み合わせ)、仮想ネットワークを選択して数式を作成する方法について学びます。 |
4. **コストを管理する**
   
    Azure DevTest Labs では、ラボでポリシーを設定して、ラボで受講者が作成できる VM の最大数を指定できます。 
   
    複数の日にまたがるトレーニングを実施する場合は、特定の時刻にすべての VM を停止し、翌日に自動的に再起動することができます。 そのためには、自動シャットダウンと自動起動のポリシーをラボで設定します。 
   
    さらに、トレーニングが終了したときに、単一の PowerShell スクリプトを実行して、すべての VM を一度に削除することもできます。 
   
    次の表のリンクをクリックして詳細を確認してください。
   
   | タスク | 学習内容 |
   | --- | --- |
   | [ラボのポリシーの定義](devtest-lab-set-lab-policy.md) |ラボでポリシーを設定してコストを管理します。 |
   | [PowerShell スクリプトを使用したすべてのラボ VM の削除](./devtest-lab-faq.yml) |トレーニングが完了したときに、1 回の操作ですべてのラボを削除します。 |
5. **各受講者とラボを共有する**
   
    受講者と共有したリンクを使用すれば、ラボに直接アクセスできます。 受講者は [Microsoft アカウント](./devtest-lab-faq.yml)さえ持っていれば、Azure アカウントすら必要ありません。 受講者は、他の受講者によって作成された VM を見ることができない。  
   
    次の表のリンクをクリックして詳細を確認してください。
   
   | タスク | 学習内容 |
   | --- | --- |
   | [Azure DevTest Labs でのラボへの受講者の追加](devtest-lab-add-devtest-user.md) |Azure Portal を使用してトレーニング ラボに受講者を追加します。 |
   | [PowerShell スクリプトを使用した、ラボへの受講者の追加](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |PowerShell を使用してトレーニング ラボへの受講者の追加を自動化します。 |
   | [ラボへのリンクの取得](./devtest-lab-faq.yml) |ハイパーリンクを通じてラボに直接アクセスする方法について学びます。 |
6. **ラボを繰り返し再利用する** 
   
    カスタム設定も含め、ラボの作成は自動化できます。それには、Resource Manager テンプレートを作成し、それを使って同一のラボを繰り返し作成します。 
   
    次の表のリンクをクリックして詳細を確認してください。
   
   | タスク | 学習内容 |
   | --- | --- |
   | [Resource Manager テンプレートを使用したラボの作成](./devtest-lab-faq.yml) |Resource Manager テンプレートを使用して Azure DevTest Labs でラボを作成します。 |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
