---
title: Internet Explorer (MSAL.js) の問題 | Azure
titleSuffix: Microsoft identity platform
description: JavaScript 用 Microsoft Authentication Library (MSAL.js) を Internet Explorer ブラウザーで使用します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 9df985c3ae6f9852357c0bfb49802f94b32617da
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2021
ms.locfileid: "113354477"
---
# <a name="known-issues-on-internet-explorer-browsers-msaljs"></a>Internet Explorer ブラウザーに関する既知の問題 (MSAL.js)

JavaScript 用 Microsoft Authentication Library (MSAL.js) は、Internet Explorer で実行できるように、[JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) 向けに生成されています。 ただし、いくつかの点について知っておく必要があります。

## <a name="run-an-app-in-internet-explorer"></a>Internet Explorer でアプリを実行する
Internet Explorer で実行できるアプリケーションで MSAL.js を使用する場合は、MSAL.js スクリプトを参照する前に、Promise ポリフィルへの参照を追加する必要があります。

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

これは、Internet Explorer が JavaScript Promise をネイティブでサポートしていないためです。

## <a name="debugging-an-application-running-in-internet-explorer"></a>Internet Explorer で実行されるアプリケーションのデバッグ

### <a name="running-in-production"></a>運用環境での実行
エンド ユーザーがポップアップを受け入れた場合、運用環境 (たとえば、Azure Web Apps) へのアプリケーションのデプロイは、通常、問題なく機能します。 Microsoft では、Internet Explorer 11 でこのテストを行いました。

### <a name="running-locally"></a>ローカルでの実行
Internet Explorer で動作するアプリケーションをローカルで実行およびデバッグする場合は、次の点に注意してください (アプリケーションを *http://localhost:1234* として実行すると想定します)。

- Internet Explorer には、"保護モード" というセキュリティ メカニズムが備わっています。このため、MSAL.js が正常に動作しません。 また、サインイン後にページが http://localhost:1234/null にリダイレクトされるという現象が発生する可能性もあります。

- アプリケーションをローカルで実行およびデバッグするには、この "保護モード" を無効にする必要があります。 この場合、

    1. Internet Explorer の **[ツール]** (歯車アイコン) をクリックします。
    1. **[インターネット オプション]** を選択し、次に **[セキュリティ]** タブを選択します。
    1. **[インターネット]** ゾーンをクリックし、 **[保護モードを有効にする (Internet Explorer の再起動が必要)]** をオフにします。 Internet Explorer に、コンピューターが保護されていないことを示す警告が表示されます。 **[OK]** をクリックします。
    1. Internet Explorer を再起動します。
    1. アプリケーションを実行してデバッグします。

完了したら、Internet Explorer のセキュリティ設定を元に戻します。  **[設定]**  ->  **[インターネット オプション]**  ->  **[セキュリティ]**  ->  **[すべてのゾーンを既定のレベルにリセットする]** の順に選択します。

## <a name="next-steps"></a>次のステップ
[Internet explorer で MSAL.js を使用する場合の既知の問題](msal-js-use-ie-browser.md)を確認します。