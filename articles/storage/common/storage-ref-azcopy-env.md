---
title: azcopy env | Microsoft Docs
description: この記事では、azcopy env コマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 71a4c27b84a16a4acb37c196ccd8ee571c2b2468
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503492"
---
# <a name="azcopy-env"></a>azcopy env

AzCopy の動作を構成できる環境変数を示します。 環境変数の完全な一覧については、[AzCopy v10 の構成設定 (Azure Storage)](storage-ref-azcopy-configuration-settings.md) に関する記事を参照してください。

## <a name="synopsis"></a>概要

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> コマンド ラインを使用して環境変数を設定した場合、その変数はコマンド ラインの履歴で読み取ることができます。 資格情報を含む変数をコマンド ラインの履歴から消去することを検討してください。 変数が履歴に表示されないようにするために、ユーザーに資格情報の入力を要求し、環境変数を設定するためのスクリプトを使用できます。

## <a name="related-conceptual-articles"></a>関連する概念に関する記事

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)
- [AzCopy と Blob Storage でデータを転送する](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)

## <a name="options"></a>オプション

|オプション|説明|
|--|--|
|-h, --help|env コマンドのヘルプ コンテンツを表示します。 |
|--show-sensitive|機密/シークレット環境変数を表示します。|

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

|オプション|説明|
|---|---|
|--cap-mbps float|転送速度の上限を設定します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。|
|--output-type string|コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。|
|--trusted-microsoft-suffixes string  | Azure Active Directory ログイン トークンを送信できる追加のドメイン サフィックスを指定します。  既定値は " *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net" です。 ここに記載されているすべてが既定値に追加されます。 セキュリティのために、Microsoft Azure のドメインのみをここに入力してください。 複数のエンティティは、セミコロンで区切ります。|

## <a name="see-also"></a>関連項目

- [azcopy](storage-ref-azcopy.md)
