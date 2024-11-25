---
title: Azure Spring Cloud のアプリケーションを開始、停止、削除する | Microsoft Docs
description: Azure Spring Cloud のアプリケーションを開始、停止、削除する方法
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 0e27f7ac841635f33bc40db739df4cf2240e2149
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484761"
---
# <a name="start-stop-and-delete-an-application-in-azure-spring-cloud"></a>Azure Spring Cloud のアプリケーションを開始、停止、削除する

**この記事の適用対象:** ✔️ Java ✔️ C#

このガイドでは、Azure portal または Azure CLI を使用して Azure Spring Cloud のアプリケーションの状態を変更する方法について説明します。

## <a name="using-the-azure-portal"></a>Azure ポータルの使用

アプリケーションをデプロイしたら、Azure portal を使用してそのアプリケーションを開始、停止、削除できます。

1. Azure portal で、Azure Spring Cloud サービス インスタンスに移動します。
1. **[アプリケーション ダッシュボード]** タブを選択します。
1. 状態を変更するアプリケーションを選択します。
1. そのアプリケーションの **[概要]** ページで **[開始/停止]**、**[再起動]**、または **[削除]** を選択します。

## <a name="using-the-azure-cli"></a>Azure CLI の使用

> [!NOTE]
> オプションのパラメーターを使用して、Azure CLI で既定値を構成できます。 Azure CLI の詳細については、[リファレンス ドキュメント](/cli/azure/spring-cloud)を参照してください。

まず、次のように Azure CLI 用の Azure Spring Cloud 拡張機能をインストールします。

```azurecli
az extension add --name spring-cloud
```

次に、次のいずれかの Azure CLI 操作を選択します。

* アプリケーションを開始するには:

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* アプリケーションを停止するには:

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* アプリケーションを再開するには:

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* アプリケーションを削除するには:

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
