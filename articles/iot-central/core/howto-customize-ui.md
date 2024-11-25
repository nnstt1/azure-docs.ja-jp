---
title: Azure IoT Central の UI をカスタマイズする | Microsoft Docs
description: Azure IoT Central アプリケーションのテーマとヘルプのリンクをカスタマイズする方法
author: dominicbetts
ms.author: dobett
ms.date: 08/18/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 87ff1d68076f9e4ce45a6b0de6a567b2ce90da16
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431002"
---
# <a name="customize-the-azure-iot-central-ui"></a>Azure IoT Central の UI をカスタマイズする

この記事では、カスタム テーマを適用したり、独自のカスタム ヘルプ リソースを指すようにヘルプのリンクを変更したりして、アプリケーションの UI をカスタマイズする方法について説明します。 

次のスクリーンショットは、標準テーマを使用したページを示しています。

![標準 IoT Central テーマ](./media/howto-customize-ui/standard-ui.png)

次のスクリーンショットは、カスタマイズされた UI 要素が強調表示されたカスタム テーマを使用したページを示しています。

![カスタム IoT Central テーマ](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>テーマの作成

カスタム テーマを作成するには、**[管理]** セクションの **[アプリケーションのカスタマイズ]** ページに移動します。

![IoT Central のテーマ](./media/howto-customize-ui/themes.png)

このページで、アプリケーションの以下の要素をカスタマイズできます。

### <a name="application-logo"></a>アプリケーション ロゴ

1 MB 以下で、透明な背景の PNG イメージ。 このロゴは、IoT Central アプリケーションのタイトル バーの左側に表示されます。

ロゴのイメージにアプリケーションの名前が含まれている場合は、アプリケーション名のテキストを非表示にすることができます。 詳細については、「[アプリケーションの管理](howto-administer.md#change-application-name-and-url)」を参照してください。

### <a name="browser-icon-favicon"></a>ブラウザー アイコン (favicon)

32 x 32 ピクセル以下で、透明な背景の PNG イメージ。 Web ブラウザーでは、このイメージをアドレス バー、履歴、ブックマーク、およびブラウザー タブで使用できます。

### <a name="browser-colors"></a>ブラウザーの色

ページ ヘッダーの色と、ボタンやその他の強調対象を目立たせるために使用される色を変更できます。 6 文字の 16 進カラー値を `##ff6347` の形式で使用します。 **16 進値** の色表記の詳細については、「[HTML Colors (HTML の色)](https://www.w3schools.com/html/html_colors.asp)」を参照してください。

> [!NOTE]
> **[アプリケーションのカスタマイズ]** ページで、いつでも既定のオプションに戻すことができます。

### <a name="changes-for-operators"></a>オペレーターのための変更

管理者がカスタム テーマを作成すると、アプリケーションのオペレーターと他のユーザーは、**[設定]** でテーマを選択できなくなります。

## <a name="replace-help-links"></a>ヘルプ リンクの置き換え

オペレーターや他のユーザーにカスタム ヘルプ情報を提供するには、アプリケーションの **[ヘルプ]** メニューのリンクを変更します。

ヘルプのリンクを変更するには、**[管理]** セクションの **[ヘルプのカスタマイズ]** ページに移動します。

![IoT Central のヘルプのリンクをカスタマイズする](./media/howto-customize-ui/help-links.png)

ヘルプ メニューに新しいエントリを追加したり、既定のエントリを削除したりすることもできます。

![カスタマイズされた IoT Central のヘルプ](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> **[ヘルプのカスタマイズ]** ページで、いつでも既定のヘルプのリンクに戻すことができます。

## <a name="next-steps"></a>次のステップ

ここでは、IoT Central アプリケーションの UI をカスタマイズする方法について説明しました。推奨される次の手順は以下のとおりです。

- [アプリケーションを管理する](./howto-administer.md)
- [ダッシュボードへのタイルの追加](howto-manage-dashboards.md)