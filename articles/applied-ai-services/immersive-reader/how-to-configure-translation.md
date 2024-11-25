---
title: 翻訳を構成する
titleSuffix: Azure Applied AI Services
description: この記事では、翻訳のさまざまなオプションを構成する方法について説明します。
author: metanMSFT
manager: guillasi
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: e8fb21ac286997601ae8896616826651bad8b1f7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779571"
---
# <a name="how-to-configure-translation"></a>翻訳を構成する方法

この記事では、イマーシブ リーダーの翻訳のさまざまなオプションを構成する方法について説明します。

## <a name="configure-translation-language"></a>翻訳言語の構成

`options` パラメーターには、翻訳を構成するために使用できるすべてのフラグが含まれています。 `language` パラメーターを翻訳先の言語に設定します。 サポートされている言語の完全な一覧については、[言語サポート](./language-support.md)を参照してください。

```typescript
const options = {
    translationOptions: {
        language: 'fr-FR'
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="automatically-translate-the-document-on-load"></a>読み込み時にドキュメントを自動的に翻訳する

`autoEnableDocumentTranslation` を `true` に設定すると、イマーシブ リーダーの読み込み時にドキュメント全体が自動的に翻訳されます。

```typescript
const options = {
    translationOptions: {
        autoEnableDocumentTranslation: true
    }
};
```

## <a name="automatically-enable-word-translation"></a>単語の翻訳を自動的に有効にする

`autoEnableWordTranslation` を `true` に設定して、単一の単語の翻訳を有効にします。

```typescript
const options = {
    translationOptions: {
        autoEnableWordTranslation: true
    }
};
```

## <a name="next-steps"></a>次のステップ

* [イマーシブ リーダー SDK リファレンス](./reference.md)を参照する