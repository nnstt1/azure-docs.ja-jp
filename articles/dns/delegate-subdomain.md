---
title: サブドメインを委任する - Azure DNS
description: このラーニング パスでは、Azure DNS サブドメインの委任について説明します。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 05/03/2021
ms.author: rohink
ms.openlocfilehash: 11c9fd2e453db37a5aa985bcc53acdabf4a42aaf
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108763309"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Azure DNS サブドメインを委任する

Azure portal を使用して DNS サブドメインを委任することができます。 たとえば、contoso.com ドメインを所有している場合は、*engineering* というサブドメインを contoso.com ゾーンとは別に管理できる別のゾーンに委任できます。

必要に応じて、[Azure PowerShell](delegate-subdomain-ps.md) を使用して、サブドメインを委任することもできます。

## <a name="prerequisites"></a>前提条件

Azure DNS サブドメインを委任するには、まずパブリック ドメインを Azure DNS に委任する必要があります。 ネーム サーバーを委任のために構成する方法については、[Azure DNS へのドメインの委任](./dns-delegate-domain-azure-dns.md)に関するページを参照してください。 ドメインが Azure DNS ゾーンに委任された後は、サブドメインを委任できるようになります。

> [!NOTE]
> この記事では、contoso.com を例として使用します。 contoso.com を独自のドメイン名に置き換えてください。

## <a name="create-a-zone-for-your-subdomain"></a>サブドメインのゾーンを作成する

まず **engineering** サブドメインのゾーンを作成します。

1. Azure portal から **[リソースの作成]** を選択します。

1. **[DNS ゾーン]** を検索し、 **[作成]** を選択します。

1. **[DNS ゾーンの作成]** ページで、ゾーンのリソース グループを選択します。 似たリソースをまとめておけるよう、親ゾーンと同じリソース グループを使用することをお勧めします。

1.  **[名前]** に「`engineering.contoso.com`」と入力し、 **[作成]** を選択します。

1. デプロイが成功したら、新しいゾーンに移動します。

## <a name="note-the-name-servers"></a>ネーム サーバーをメモする

次に、engineering サブドメインの 4 つのネーム サーバーをメモします。

**engineering** ゾーンの概要ページで、ゾーンの 4 つのネーム サーバーをメモします。 これらのネーム サーバーは、後で必要になります。

## <a name="create-a-test-record"></a>テスト レコードを作成する

テストに使用する **A** レコードを作成します。 たとえば、**www** A レコードを作成し、**10.10.10.10** IP アドレスを使用してそれを構成します。

## <a name="create-an-ns-record"></a>NS レコードの作成

次に、**engineering** ゾーンのネーム サーバー (NS) レコードを作成します。

1. 親ドメインのゾーンに移動します。

1. 概要ページの上部にある **[レコード セット]** を選択します。

1. **[レコード セットの追加]** ページで、 **[名前]** ボックスに「**engineering**」と入力します。

1. **[種類]** には **[NS]** を選択します。

1. **[ネーム サーバー]** に、以前にメモした **engineering** ゾーンの 4 つのネーム サーバーを入力します。

1. **[OK]** を選択してレコードを保存します。

## <a name="test-the-delegation"></a>委任をテストする

nslookup を使用して委任をテストします。

1. PowerShell ウィンドウを開きます。

1. コマンド プロンプトに「`nslookup www.engineering.contoso.com.`」と入力します。

1. アドレス **10.10.10.10** を示す権限のない回答を受け取ります。

## <a name="next-steps"></a>次のステップ

[Azure でホストされているサービスの逆引き DNS を構成する](dns-reverse-dns-for-azure-services.md)方法について説明します。
