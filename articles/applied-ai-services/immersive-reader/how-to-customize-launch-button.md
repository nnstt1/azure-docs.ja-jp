---
title: イマーシブ リーダーの起動ボタンを編集する
titleSuffix: Azure Applied AI Services
description: この記事では、イマーシブ リーダーの起動ボタンをカスタマイズする方法について説明します。
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 03/08/2021
ms.author: metang
ms.openlocfilehash: 1f5feff0857b98b077ed1e19dbb7aaa82004df69
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779465"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>イマーシブ リーダーのボタンをカスタマイズする方法

この記事では、イマーシブ リーダーの起動ボタンをアプリケーションのニーズに合わせてカスタマイズする方法について説明します。

## <a name="add-the-immersive-reader-button"></a>イマーシブ リーダーのボタンを追加する

イマーシブ リーダー SDK には、イマーシブ リーダーの起動ボタンに使用される既定のスタイルが用意されています。 このスタイルを有効にするには、`immersive-reader-button` クラス属性を使用します。

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>ボタンのスタイルをカスタマイズする

ボタンのスタイルを設定するには、`data-button-style` 属性を使用します。 使用できる値は、`icon`、`text`、`iconAndText` です。 既定値は `icon` です。

### <a name="icon-button"></a>アイコン ボタン

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

これは次のようにレンダリングされます。

![これはレンダリングされた [テキスト] ボタンです](./media/button-icon.png)

### <a name="text-button"></a>テキスト ボタン

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

これは次のようにレンダリングされます。

![これはレンダリングされた [イマーシブ リーダー] ボタンです。](./media/button-text.png)

### <a name="icon-and-text-button"></a>アイコンとテキスト ボタン

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

これは次のようにレンダリングされます。

![アイコン ボタン](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>ボタンのテキストをカスタマイズする

ボタンの言語と代替テキストは、`data-locale` 属性を使用して構成します。 既定の言語は English (英語) です。

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>アイコンのサイズをカスタマイズする

イマーシブ リーダー アイコンのサイズは、`data-icon-px-size` 属性を使用して構成できます。 アイコンのサイズがピクセル単位で設定されます。 既定のサイズは 20px です。

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>次のステップ

* [イマーシブ リーダー SDK リファレンス](./reference.md)を参照する