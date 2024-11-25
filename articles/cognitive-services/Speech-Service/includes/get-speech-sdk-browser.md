---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: eur
ms.custom: devx-track-js
ms.openlocfilehash: 017fb0117f26000d5cc35f4967bf56d8c714de6f
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131506500"
---
:::row:::
    :::column span="3":::
        JavaScript 用 Speech SDK は npm パッケージとして提供されています。<a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk </a> とそれに付随する GitHub リポジトリ <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-services-speech-sdk-js </a> を参照してください。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> JavaScript 用 Speech SDK は npm パッケージとして提供されており、クライアントの Web ブラウザーと Node.js の両方で使用できますが、各環境のさまざまなアーキテクチャの影響を考慮してください。 例えば、<a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">ドキュメントオブジェクトモデル (DOM)</a>は、<a href="https://nodejs.org/api/fs.html" target="_blank">ファイルシステム</a>がクライアント側アプリケーションで使用できない場合と同様に、サーバー側アプリケーションでは使用できません。

### <a name="nodejs-package-manager-npm"></a>Node.js パッケージマネージャー (NPM)

JavaScript 用 Speech SDK をインストールするには、次の `npm install` コマンドを実行します。

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>HTML スクリプトタグ

または、HTMLs`<head>`要素に`<script>`タグを直接含めることもできます。この場合,<a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank">**JSDelivr** NPM シンジケート に依存します</a>。

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@latest/distrib/browser/microsoft.cognitiveservices.speech.sdk.bundle-min.js">
</script>
```

詳細については、<a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">Web ブラウザー音声SDK のクイックスタート を参照してください </a>。
