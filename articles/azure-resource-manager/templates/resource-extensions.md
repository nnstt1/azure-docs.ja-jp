---
title: 拡張機能を使用したデプロイ後の構成
description: Azure Resource Manager テンプレート (ARM テンプレート) 拡張機能を使用してデプロイ後に構成する方法について説明します。
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: 3d458673163454a6d7914de958cb1d338ed12687
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026672"
---
# <a name="post-deployment-configurations-by-using-extensions"></a>拡張機能を使用したデプロイ後の構成

Azure Resource Manager テンプレート (ARM テンプレート) 拡張機能は、Azure リソースでのデプロイ後の構成と自動タスクを提供する小さなアプリケーションです。 最も人気のあるテンプレートは、仮想マシンの拡張機能に関するものです。 「[仮想マシンの拡張機能とその機能について (Windows)](../../virtual-machines/extensions/features-windows.md)」と「[仮想マシンの拡張機能とその機能について (Linux)](../../virtual-machines/extensions/features-linux.md)」をご覧ください。

## <a name="extensions"></a>拡張機能

既存の拡張機能:

- [Microsoft.Compute/virtualMachines/extensions](/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/extensions](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft.HDInsight clusters/extensions](/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Microsoft.Sql servers/databases/extensions](/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions)
- [Microsoft.Web/sites/siteextensions](/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

使用可能な拡張機能を確認するには、[テンプレート リファレンス](/azure/templates/)を参照します。 **[タイトルでフィルター処理します]** で、**拡張機能** を入力します。

これらの拡張機能の使用方法については、次をご覧ください。

- [チュートリアル: ARM テンプレートを使用して仮想マシン拡張機能をデプロイする](template-tutorial-deploy-vm-extensions.md)」を参照してください。
- [チュートリアル:ARM テンプレートを使用して SQL BACPAC ファイルをインポートする](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:ARM テンプレートを使用して仮想マシン拡張機能をデプロイする](template-tutorial-deploy-vm-extensions.md)
