---
title: イマーシブ リーダー用の HTML コンテンツを準備する方法
titleSuffix: Azure Applied AI Services
description: HTML、JavaScript、Python、Android、または iOS を使用してイマーシブ リーダーを起動する方法について説明します。 イマーシブ リーダーは、実績のある技術を使用して、言語学習者、新しい読者、および学習方法の異なる学生の読解力を向上させます。
author: nitinme
manager: nitinme
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: nitinme
ms.openlocfilehash: 505151c22c095f1168ccad4a84870c430b3dd586
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026526"
---
# <a name="how-to-prepare-html-content-for-immersive-reader"></a>イマーシブ リーダー用の HTML コンテンツを準備する方法

この記事では、HTML を構築し、コンテンツを取得して、それをイマーシブ リーダーで使用できるようにする方法について説明します。

## <a name="prepare-the-html-content"></a>HTML コンテンツを準備する

イマーシブ リーダーでレンダリングしたいコンテンツをコンテナー要素内に配置します。 コンテナー要素に一意の `id` があることを確認してください。 イマーシブ リーダーは、基本的な HTML 要素をサポートします。詳細については、[リファレンス](reference.md#html-support)を参照してください。

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>JavaScript で HTML コンテンツを取得する

JavaScript コードで HTML コンテンツを取得するには、コンテナー要素の `id` を使用します。

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>イマーシブ リーダーを起動して HTML コンテンツを表示する

`ImmersiveReader.launchAsync` を呼び出すときに、チャンクの `mimeType` プロパティを `text/html` に設定すると、HTML のレンダリングが有効になります。

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>次のステップ

* [イマーシブ リーダー SDK リファレンス](reference.md)を参照する