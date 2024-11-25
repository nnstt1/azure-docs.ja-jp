---
title: Azure portal の設定を管理する (旧バージョン)
description: 独自の基本設定を実現するために Azure portal の既定の設定を変更できます。 このドキュメントでは、旧バージョンの設定エクスペリエンスについて説明します。
ms.date: 06/17/2021
ms.topic: how-to
ms.openlocfilehash: 979009954fb7ee60ff631b6f98c895203533b289
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2021
ms.locfileid: "112372227"
---
# <a name="manage-azure-portal-settings-and-preferences-older-version"></a>Azure portal の設定を管理する (旧バージョン)

独自の基本設定を実現するために Azure portal の既定の設定を変更できます。 

> [!IMPORTANT]
> 現在、すべての Azure ユーザーを新しいエクスペリエンスに移行中です。 このトピックでは、以前のエクスペリエンスについて説明します。 最新情報については、「[Azure portal の設定を管理する](set-preferences.md)」を参照してください。

ほとんどの設定は、グローバル ページ ヘッダーの **[設定]** メニューから使用できます。

![設定が強調表示されたグローバル ページ ヘッダー アイコンを示すスクリーンショット](./media/original-preferences/header-settings.png)


## <a name="choose-your-default-subscription"></a>既定のサブスクリプションを選択する

Azure portal にサインインするときに、既定で開かれるサブスクリプションを変更できます。 これは、使用するプライマリ サブスクリプションがありますが、時々他のものを使用する場合に便利です。 

:::image type="content" source="media/original-preferences/filter-subscription-default-view.png" alt-text="サブスクリプションによってリソース リストをフィルター処理します。":::

1. グローバル ページ ヘッダーのディレクトリおよびサブスクリプションのフィルター アイコンを選択します。

1. ポータルを起動するときに、既定のサブスクリプションとして使用するサブスクリプションを選択します。 

    :::image type="content" source="media/original-preferences/default-directory-subscription-filter.png" alt-text="ポータルを起動するときに、既定のサブスクリプションとして使用するサブスクリプションを選択します。"::: 


## <a name="choose-your-default-view"></a>既定のビューを選択する 

Azure portal にサインインするときに、既定で開かれるページを変更できます。

![既定のビューが強調表示された Azure portal 設定を示すスクリーンショット](./media/original-preferences/default-view.png)

- **ホーム** はカスタマイズできません。  よく使用される Azure サービスへのショートカットが表示され、最近使用したリソースが一覧表示されます。 Microsoft Learn や Azure ロードマップなどのリソースへの便利なリンクも表示されます。

- ダッシュボードをカスタマイズし、自分専用に設計されたワークスペースを作成することができます。 たとえば、プロジェクト、タスク、またはロールに焦点を合わせたダッシュボードを作成できます。 **[ダッシュボード]** を選択した場合、既定のビューは最近使用したダッシュボードに移動します。 詳細については、「[Azure Portal でのダッシュボードの作成と共有](azure-portal-dashboards.md)」を参照してください。

## <a name="choose-a-portal-menu-mode"></a>ポータルのメニュー モードを選択する

ポータル メニューの既定のモードでは、ページ上でポータル メニューがどれだけの領域を占めるかを制御します。

![ポータル メニューの既定のモードを設定する方法を示すスクリーンショット。](./media/original-preferences/menu-mode.png)

- ポータル メニューは **ポップアップ** モードの場合、必要になるまで表示されません。 メニュー アイコンを選択して、メニューを開いたり閉じたりします。

- ポータル メニューに対して **ドッキング モード** を選択した場合は、常に表示されます。 メニューを折りたためば、より広い作業領域を確保できます。

## <a name="choose-a-theme-or-enable-high-contrast"></a>テーマを選択するか、ハイ コントラストを有効にする

選択するテーマは、Azure portal に表示される背景とフォント色に影響を及ぼします。 4 つのプリセット配色テーマから選択できます。 各サムネイルを選択し、最適なテーマを見つけます。

または、ハイコントラスト テーマのいずれかを選択できます。 ハイ コントラスト テーマにすると、視覚障碍をお持ちの方が Azure portal を読みやすくなり、その他のテーマの選択はすべてオーバーライドされます。

![テーマが強調表示された Azure portal 設定を示すスクリーンショット](./media/original-preferences/theme.png)

## <a name="enable-or-disable-pop-up-notifications"></a>ポップアップ通知を有効または無効にする

通知は、現在のセッションに関連するシステム メッセージです。 これらは、たとえば、作成したばかりのリソースが利用可能になったときや、最後のアクションを確認するときに、現在のクレジット残高のような情報を提供します。 ポップアップ通知がオンになっているときには、画面の上隅にメッセージが短時間表示されます。 

ポップアップ通知を有効または無効にするには、 **[ポップアップ通知を有効にする]** をオンまたはオフにします。

![ポップアップ通知が強調表示された Azure portal の設定を示すスクリーンショット](./media/original-preferences/pop-up-notifications.png)

現在のセッション中に受信したすべての通知を読むには、グローバル ヘッダーから **[通知]** を選択します。

![通知が強調表示された Azure portal のグローバル ヘッダーを示すスクリーンショット](./media/original-preferences/read-notifications.png)

以前のセッションからの通知を読む場合は、アクティビティ ログでイベントを検索します。 詳細については、「[アクティビティ ログを表示する](../azure-monitor/essentials/activity-log.md#view-the-activity-log)」を参照してください。 

## <a name="change-the-inactivity-timeout-setting"></a>非アクティブ タイムアウト設定を変更する

非アクティブ タイムアウト設定は、ワークステーションのセキュリティ保護を忘れた場合にリソースを未承認のアクセスから保護するのに役立ちます。 ユーザーはしばらくの間アイドル状態であった後、Azure portal セッションから自動的にサインアウトされます。 個人として、自分でタイムアウト設定を変更することができます。 管理者の場合は、ディレクトリ内のすべてのユーザーに対してディレクトリ レベルで設定できます。

### <a name="change-your-individual-timeout-setting-user"></a>個々のタイムアウト設定を変更する (ユーザー)

**[非アクティブのときにサインアウトする]** の下のドロップダウンを選択します。 アイドル状態になっている場合に、Azure portal セッションがサインアウトするまでの時間を選択します。

![非アクティブ タイムアウト設定が強調表示されたポータル設定を示すスクリーンショット](./media/original-preferences/inactive-sign-out-user.png)

変更は自動的に保存されます。 アイドル状態になっている場合は、設定した時間が経過すると、Azure portal セッションがサインアウトします。

管理者が非アクティブ タイムアウト ポリシーを有効にしている場合でも、ディレクトリ レベルの設定よりも小さい限り、独自の設定を行うことができます。 **[ディレクトリの非アクティブ タイムアウト ポリシーをオーバーライドする]** を選択してから、時間間隔を設定します。

![ディレクトリの非アクティブ タイムアウトを上書きするポリシー設定が強調表示されたポータル設定を示すスクリーンショット](./media/original-preferences/inactive-sign-out-override.png)

### <a name="change-the-directory-timeout-setting-admin"></a>ディレクトリ タイムアウト設定を変更する (管理者)

[グローバル管理者ロール](../active-directory/roles/permissions-reference.md#global-administrator)の管理者は、セッションがサインアウトするまでの最大アイドル時間を適用できます。非アクティブ タイムアウト ポリシーは、ディレクトリ レベルで適用されます。 この設定は、新しいセッションに対して有効になります。 既にサインインしているユーザーには、すぐには適用されません。 ディレクトリの詳細については、「[Active Directory Domain Services の概要](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)」を参照してください。

グローバル管理者として、Azure portal のすべてのユーザーに対してアイドル タイムアウト設定を適用する場合は、これらの手順に従います。

1. リンク テキスト「**ディレクトリ レベルのタイムアウトの構成**」を選択します。

    ![リンク テキストが強調表示されたポータル設定を示すスクリーンショット](./media/original-preferences/settings-admin.png)

1. **[ディレクトリ レベルの非アクティブ タイムアウトの構成]** ページで、**[Azure portal のディレクトリ レベルのアイドル タイムアウトを有効にする]** を選択して設定をオンにします。

1. 次に、セッションが自動的にサインアウトになる前までのユーザーの最大アイドル時間について **[時間]** と **[分]** を入力します。

1. **[適用]** を選択します。

    ![ディレクトリ レベルの非アクティブ タイムアウトの設定を示すページのスクリーンショット](./media/original-preferences/configure.png)

非アクティブ タイムアウト ポリシーが設定されていることを確認するには、グローバル ページ ヘッダーから **[通知]** を選択します。 成功通知が一覧表示されていることを確認します。

![ディレクトリ レベルの非アクティブ タイムアウトの成功通知を示すスクリーンショット](./media/original-preferences/confirmation.png)

## <a name="restore-default-settings"></a>既定の設定に戻す

Azure portal の設定に変更を加えて、それらを破棄する場合は、 **[既定の設定に戻す]** を選択します。 ポータルの設定に加えた変更はすべて失われます。 このオプションは、ダッシュボードのカスタマイズには影響しません。

![既定の設定の復元を示すスクリーンショット](./media/original-preferences/useful-links-restore-defaults.png)

## <a name="export-user-settings"></a>ユーザー設定をエクスポートする

カスタム設定に関する情報は、Azure に格納されます。 次のユーザー データをエクスポートできます。

* Azure portal のプライベート ダッシュボード
* お気に入りのサブスクリプションまたはディレクトリなどのユーザーの設定、およびディレクトリの前回のログイン
* テーマとその他のカスタム ポータル設定

設定を削除する予定の場合は、それをエクスポートして確認することをお勧めします。 ダッシュボードの再構築や設定のやり直しには、時間がかかる場合があります。

ポータルの設定をエクスポートするには、 **[すべての設定をエクスポートする]** を選択します。

![設定のエクスポートを示すスクリーンショット](./media/original-preferences/useful-links-export-settings.png)

設定をエクスポートすると、配色テーマ、お気に入り、プライベート ダッシュボードなどのユーザー設定が含まれる *.json* ファイルが作成されます。 ユーザー設定の動的な性質とデータが破損するリスクのため、*.json* ファイルから設定をインポートすることはできません。

## <a name="delete-user-settings-and-dashboards"></a>ユーザー設定とダッシュボードを削除する

カスタム設定に関する情報は、Azure に格納されます。 次のユーザー データを削除できます。

* Azure portal のプライベート ダッシュボード
* お気に入りのサブスクリプションまたはディレクトリなどのユーザーの設定、およびディレクトリの前回のログイン
* テーマとその他のカスタム ポータル設定

設定を削除する前に、エクスポートして確認するのはよいことです。 ダッシュボードの再構築やカスタム設定のやり直しには、時間がかかる場合があります。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

ポータルの設定を削除するには、 **[すべての設定とプライベート ダッシュボードを削除]** を選択します。

![設定の削除を示すスクリーンショット](./media/original-preferences/useful-links-delete-settings.png)

## <a name="change-language-and-regional-settings"></a>地域と言語の設定を選択する

Azure portal でテキストをどのように表示するかを制御する設定は 2 つあります。 
- **[言語]** 設定では、Azure portal 内のテキストが表示される言語を制御します。 

- **[地域設定]** では、日付、時刻、数値、通貨の表示方法を制御します。

Azure portal で使用される言語を変更するには、ドロップダウンを使用して、使用可能な言語の一覧から選択します。

地域設定を選択すると、選択した言語についてのみ、地域オプションを表示するように変更されます。 自動選択を変更するには、ドロップダウンを使用して、必要な地域設定を選択します。

たとえば、使用する言語として [英語] を選択してから地域設定として [米国] を選択すると、通貨は米ドルで表示されます。 言語として [英語] を選択してから地域設定として [ヨーロッパ] を選択すると、通貨はユーロで表示されます。

**[適用]** を選択し、言語と地域の形式設定を更新します。

   ![言語と地域の形式設定が表示されたスクリーンショット](./media/original-preferences/language.png)

>[!NOTE]
>これらの言語と地域の設定は、Azure portal にのみ影響を与えます。 新しいタブまたはウィンドウで開かれるドキュメント リンクで、ブラウザーの言語設定が使用しされ、表示する言語が決定されます。
>

## <a name="next-steps"></a>次のステップ

- [Azure portal のキーボード ショートカット](azure-portal-keyboard-shortcuts.md)
- [サポートされているブラウザーとデバイス](azure-portal-supported-browsers-devices.md)
- [お気に入りの追加、削除、並べ替え](azure-portal-add-remove-sort-favorites.md)
- [カスタム ダッシュボードの作成と共有](azure-portal-dashboards.md)
- [Azure portal の使い方に関するビデオ シリーズ](azure-portal-video-series.md)
