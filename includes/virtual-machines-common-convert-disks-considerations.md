---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 4ce308871a14ee972290fca47bda8f2f677ebf0c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130217614"
---
* 変換作業によって VM が再起動するので、既に設定されているメンテナンス期間中に VM の移行をスケジュールしてください。 

* 一度行った変換を元に戻すことはできません。 

* [仮想マシン共同作成者](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールを持つユーザーは、VM のサイズを変更できないことに注意してください (事前の変換は可能)。 マネージド ディスクを持つ VM が OS ディスク上での Microsoft.Compute/disks/write 権限をユーザーに要求するのは、これが理由です。

* 変換のテストは必ず行ってください。 まずテスト用の仮想マシンで試してから運用環境で移行を実行します。

* 変換中に、VM の割り当てを解除します。 VM は、変換後に起動されたときに、新しい IP アドレスを受け取ります。 VM には必要に応じて[静的 IP アドレスを割り当て](../articles/virtual-network/ip-services/public-ip-addresses.md)ることができます。

* 変換プロセスをサポートするために必要な Azure VM エージェントの最小バージョンを確認します。 エージェントのバージョンを確認して更新する方法については、[Azure VM エージェントの最小バージョンのサポート](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)に関するページを参照してください。