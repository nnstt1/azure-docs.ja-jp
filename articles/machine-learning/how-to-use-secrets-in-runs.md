---
title: トレーニングの認証シークレット
titleSuffix: Azure Machine Learning
description: ワークスペース用に Azure Key Vault を使用して、セキュリティで保護された方法でトレーニングの実行にシークレットを渡す方法について説明します。
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.date: 10/21/2021
ms.topic: how-to
ms.openlocfilehash: af61ce1fbea14b6c872916ecf9b501ad074bda9b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556998"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>Azure Machine Learning トレーニングの実行で認証資格情報シークレットを使用する

この記事では、トレーニングの実行でシークレットを安全に使用する方法について説明します。 ユーザー名やパスワードなどの認証情報はシークレットです。 たとえば、トレーニング データを問い合わせる目的で外部データベースに接続する場合、リモート実行コンテキストにユーザー名とパスワードを渡す必要があります。 このような値をクリアテキストでトレーニング スクリプトにコーディングすると、シークレットが公開されるので安全ではありません。 

代わりに、Azure Machine Learning ワークスペースには、[Azure Key Vault](../key-vault/general/overview.md) という名前のリソースが関連付けられています。 この Key Vault を使用して、Azure Machine Learning Python SDK の一連の API を介してシークレットをリモート実行に安全に渡します。

シークレットを使用するための標準的なフローは次のとおりです。
 1. ローカル コンピューターで Azure にログインし、お使いのワークスペースに接続します。
 2. ローカル コンピューターで、ワークスペース キー コンテナーにシークレットを設定します。
 3. リモート実行を送信します。
 4. リモート実行の中で、Key Vault からシークレットを取得して使用します。

## <a name="set-secrets"></a>シークレットを設定する

Azure Machine Learning では、[Keyvault](/python/api/azureml-core/azureml.core.keyvault.keyvault) クラスにシークレットを設定するためのメソッドが含まれています。 ローカル Python セッションで、最初にワークスペース Key Vault への参照を取得します。その後、[`set_secret()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#set-secret-name--value-) メソッドを使用して、名前と値でシークレットを設定します。 __set_secret__ メソッドでは、名前が既に存在する場合は、シークレット値が更新されます。

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

シークレット値をクリアテキストとしてファイルに格納するのは安全ではないため、その値を Python コードには含めないでください。 代わりに、Azure DevOps のビルド シークレットなどの環境変数から、または対話型ユーザー入力からシークレット値を取得してください。

[`list_secrets()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#list-secrets--) メソッドを利用してシークレット名を一覧表示できます。バッチ バージョンの [set_secrets()](/python/api/azureml-core/azureml.core.keyvault.keyvault#set-secrets-secrets-batch-) もありますが、これを利用すると、複数シークレットを一度に設定できます。

> [!IMPORTANT]
> `list_secrets()` を使用すると、Azure ML SDK を使用して `set_secret()` または `set_secrets()` を介して作成されたシークレットのみが表示されます。 SDK 以外によって作成されたシークレットは一覧に表示されません。 たとえば、Azure portal または Azure PowerShell を使用して作成されたシークレットは一覧に表示されません。
> 
> [`get_secret()`](#get-secrets) を使用すると、作成方法に関係なく、キー コンテナーからシークレット値を取得できます。 そのため、`list_secrets()` によって一覧表示されないシークレットを取得できます。

## <a name="get-secrets"></a>シークレットの取得

ローカル コードでは、[`get_secret()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#get-secret-name-) メソッドを使用して、名前でシークレット値を取得できます。

[`Experiment.submit`](/python/api/azureml-core/azureml.core.experiment.experiment#submit-config--tags-none----kwargs-) が送信される実行の場合、[`Run`](/python/api/azureml-core/azureml.core.run%28class%29) クラスを使用して [`get_secret()`](/python/api/azureml-core/azureml.core.run.run#get-secret-name-) メソッドを使用します。 送信される実行ではワークスペースが認識されるため、このメソッドでは、ワークスペースのインスタンス化がショートカットされ、シークレット値が直接返されます。

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

書き込みや出力によってシークレット値が公開されることがないように注意してください。

複数のシークレットに一度にアクセスするための [get_secrets()](/python/api/azureml-core/azureml.core.run.run#get-secrets-secrets-) というバッチ バージョンもあります。

## <a name="next-steps"></a>次のステップ

 * [ノートブックの例を表示する](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Azure Machine Learning のエンタープライズ セキュリティを確認する](concept-enterprise-security.md)