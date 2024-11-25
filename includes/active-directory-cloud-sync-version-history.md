---
ms.openlocfilehash: 9dabeaa9dd8e22e63b356768e34ebf828ac43632
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861554"
---
この記事では、これまでにリリースされた Azure Active Directory Connect プロビジョニング エージェントのバージョンと機能を一覧表示します。 Azure AD チームは、プロビジョニング エージェントを新機能で定期的に更新しています。 

Microsoft では、エージェントの最新バージョンと 1 つ前のバージョンを直接サポートしています。

## <a name="download-link"></a>ダウンロード リンク
最新バージョンのエージェントは、[このリンク](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/provisioningAgentInstaller)からダウンロードできます。

## <a name="115870"></a>1.1.587.0

2021 年 11 月 2 日 - ダウンロード用にリリース

新機能と機能強化

- パスワード ライトバックを構成するコマンドレット


## <a name="115840"></a>1.1.584.0 

2021 年 8 月 20 日 - ダウンロード開始

### <a name="fixed-issues"></a>修正された問題

- ドメインの名前が変更された場合、イベント ログに「指定されたキャストが無効です」というエラーが表示され、パスワード ハッシュ同期が失敗するバグを修正しました。 これは、以前のビルドからの回帰です。

## <a name="115820"></a>1.1.582.0

2021 年 8 月 8 日 - ダウンロード開始

>[!NOTE] 
>これは Azure AD Connect のセキュリティ更新プログラム リリースです。 このリリースで[こちらの CVE](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-36949) に記載されている脆弱性に対処します。 この脆弱性に関する詳細については、CVE を参照してください。

## <a name="113590"></a>1.1.359.0

### <a name="new-features-and-improvements"></a>新機能と機能強化
- アクセス許可を設定/リセットするための GMSA コマンドレット

### <a name="fixed-issues"></a>修正された問題
- GMSA フォルダーのアクセス許可のバグ修正 (元来、ブートストラップの問題によって発生した問題)
- 1 つの値参照属性に対する複数の変更を処理するためのバグ修正 (マネージャーなど)
- 初期列挙の失敗に対するバグ修正と、失敗に対する強化されたトレース
- スコープ グループへのグループ メンバーシップの更新を最適化します。 これにより、お客様はグループ スコープ フィルターを使用して、最大 50,000 人のメンバーのグループを同期できるようになりました。 
- スコープ設定ロジックに従うために、オンデマンド プロビジョニングで使用されるスコープ設定と DN による単一オブジェクトの取得をサポートします





## <a name="113540"></a>1.1.354.0

2021 年 1 月 20 日: ダウンロード対象としてリリース済み

### <a name="new-features-and-improvements"></a>新機能と機能強化
- 事前にカスタムで作成された GMSA アカウントに対するサポートなど、GMSA エクスペリエンスの向上
- GMSA の設定に対する [PowerShell コマンドレット](../articles/active-directory/cloud-sync/how-to-gmsa-cmdlets.md)のサポート
- エージェント インストール (サイレント インストール) に対する [CLI のサポート](../articles/active-directory/cloud-sync/how-to-install-pshell.md)
- エージェント ソースの検疫に関する問題の追加診断
- OU スコープ フィルターのメモリ使用量の削減、スコープ内のユーザーのみを対象とした PHS の実行、OU スコープ使用時における OU 内の入れ子になったオブジェクトの処理を含むバグの修正。 


### <a name="fixed-issues"></a>修正された問題
-    スコープ グループがスコープ外になったときに検疫を防止
-   スコープ フィルターが構成されている場合 - PHS ジョブはスコープ内のユーザーに対してのみ動作するようになりました
-   アップグレード中にエージェントが時々応答しない
-   OU スコープ使用時における入れ子になった OU 内のオブジェクトの初期同期
-   Repair-AADCloudSyncToolsAccount の堅牢性を向上
-   OU スコープ フィルターでの大量のメモリ使用量を削減
-   ロール メンバーにセキュリティ グループが含まれていると、管理者ロールの検査が失敗する
-   エージェント証明書の更新を妨げる GMSA フォルダーのアクセス許可に関する問題を修正







## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>リリースの状態

2020 年 11 月 23 日:ダウンロード対象としてリリース済み

### <a name="new-features-and-improvements"></a>新機能と機能強化

* [gMSA](../articles/active-directory/cloud-sync/how-to-prerequisites.md#group-managed-service-accounts) のサポート
* 増分または差分の同期サイクル中の、1500 未満までのメンバー サイズのグループのサポート。 これは、グループ スコープ フィルターの使用時に適用されます。
* メンバー サイズ 15,000 までの大規模なグループのサポート
* 初期同期の機能強化
* 高度な詳細ログ
* [AADCloudSyncTools PowerShell モジュール](../articles/active-directory/cloud-sync/reference-powershell.md)の紹介
* 英語以外のサーバーにエージェントをインストールできるようにするために修正された制限
* スコープ内のオブジェクトのみに対する PHS フィルター処理のサポート (最初は、すべてのオブジェクトのパスワード ハッシュを同期していました)
* エージェントのメモリ リークの問題を修正
* プロビジョニング ログの強化
* [LDAP 接続タイムアウト](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-ldap-connection-timeout)を構成するためのサポート 
* [参照の追跡](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing)を構成するためのサポート 


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>リリースの状態

2019 年 12 月 4 日:ダウンロード対象としてリリース済み

### <a name="new-features-and-improvements"></a>新機能と機能強化

* オンプレミスの Active Directory から Azure AD にユーザー、連絡先、グループのデータを同期するための [Azure AD Connect クラウド同期](../articles/active-directory/cloud-sync/what-is-cloud-sync.md)に対するサポートが組み込まれています


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>リリースの状態

2019 年 9 月 9 日: 自動更新向けにリリース済み

### <a name="new-features-and-improvements"></a>新機能と機能強化

* プロビジョニング エージェントに関する問題をデバッグするために追加のトレースとログ記録を構成する機能
* 同期のパフォーマンスを向上させるために、マッピングで構成されている Azure AD 属性のみを取り込む機能

### <a name="fixed-issues"></a>修正された問題

* Azure AD 接続エラーに関する問題が発生した場合に、エージェントを応答しない状態にしていたバグを修正しました
* バイナリ データを Azure Active Directory から読み取るときに問題を引き起こしていたバグを修正しました
* エージェントがクラウド ハイブリッド ID サービスとの信頼を更新するのを失敗させていたバグを修正しました

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>リリースの状態

2019 年 1 月 23 日:ダウンロード対象としてリリース済み

### <a name="new-features-and-improvements"></a>新機能と機能強化

* パフォーマンス、安定性、信頼性を向上させるためにプロビジョニング エージェントおよびコネクタのアーキテクチャが刷新されました 
* UI 駆動型インストール ウィザードを使用してプロビジョニング エージェントの構成が簡略化されました
