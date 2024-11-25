---
title: 'Azure Front Door: キャッシュ'
description: この記事は、キャッシュを有効にしたルーティング規則を使用した Azure Front Door Standard/Premium の動作の理解に役立ちます。
services: front-door
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 687a18ff1f66f5bd14a76b620c4fda04e6b7a6f0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234979"
---
# <a name="caching-with-azure-front-door-standardpremium-preview"></a>Azure Front Door Standard/Premium (プレビュー) でのキャッシュ

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

この記事では、キャッシュを有効にした場合に Front Door Standard/Premium (プレビュー) のルートとルール セットがどのように動作するかについて説明します。 Azure Front Door は最新の Content Delivery Network (CDN) であり、動的サイト アクセラレーションと負荷分散に対応しています。

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="request-methods"></a>要求メソッド

GET 要求メソッドのみが、Azure Front Door 上にキャッシュされたコンテンツを生成できます。 その他のすべての要求メソッドは、常にネットワーク経由でプロキシ化されます。

## <a name="delivery-of-large-files"></a>大きなファイルの配信

Front Door Standard/Premium (プレビュー) では、ファイル サイズの制限なしで、大きなファイルを配信します。 Front Door では、オブジェクト チャンクと呼ばれる方法を使用します。 大きなファイルが要求されると、Front Door は配信元からファイルを分割して取得します。 完全なファイル要求またはバイト範囲を指定したファイル要求を受け取った後、Front Door 環境は、8 MB のチャンク単位で配信元にファイルを要求します。

Front Door 環境に届いたチャンクはキャッシュされ、即座にユーザーに提供されます。 その後、Front Door は並列処理で次のチャンクをプリフェッチします。 このプリフェッチにより、コンテンツはチャンク 1 つ分だけ常にユーザーより先行することになるため、待ち時間が短縮されます。 このプロセスは、ファイル全体がダウンロードされるか (要求された場合)、クライアントが接続を閉じるまで続行されます。

バイト範囲の要求の詳細については、[RFC 7233](https://www.rfc-editor.org/info/rfc7233) を参照してください。
Front Door は受け取ったチャンクをそのままキャッシュするため、ファイル全体を Front Door キャッシュにキャッシュする必要はありません。 ファイルまたはバイト範囲に対する後続の要求に対しては、キャッシュの内容が提供されます。 チャンクがすべてキャッシュされていない場合は、プリフェッチを使用してバックエンドにチャンクが要求されます。 この最適化は、配信元サーバーのバイト範囲要求をサポートする配信元の能力に依存します。 配信元がバイト範囲要求をサポートしていない場合、この最適化の効果はありません。

## <a name="file-compression"></a>ファイル圧縮

Azure Front Door での[ファイル圧縮によるパフォーマンスの向上](how-to-compression.md)に関するページを参照してください。

## <a name="query-string-behavior"></a>クエリ文字列の動作

Front Door を使用すると、クエリ文字列を含む Web 要求のためにファイルをキャッシュする方法を制御することができます。 クエリ文字列を含む Web 要求で、クエリ文字列は要求の疑問符 (?) の後に指定されます。 クエリ文字列には、フィールド名とその値を等号 (=) で区切って指定される、キーと値のペア (複数可) を含めることができます。 キーと値のペアはそれぞれ、アンパサンド (&) で区切られます。 たとえば、「 `http://www.contoso.com/content.mov?field1=value1&field2=value2` 」のように入力します。 要求のクエリ文字列にキーと値のペアを複数指定する場合、どのような順序で指定してもかまいません。

* **クエリ文字列を無視**: このモードでは、Front Door は要求元からのクエリ文字列を最初の要求時に配信元に渡して、アセットをキャッシュします。 Front Door 環境から提供されるアセットに対する後続のすべての要求で、キャッシュされたアセットの有効期限が切れるまで、クエリ文字列が無視されます。

* **一意の URL をすべてキャッシュ**: このモードでは、クエリ文字列を含む一意の URL が指定された各要求は、独自のキャッシュがある一意の資産として扱われます。 たとえば、`www.example.ashx?q=test1` の要求に対する配信元からの応答は Front Door 環境にキャッシュされ、同じクエリ文字列を持つ後続のキャッシュに対して返されます。 `www.example.ashx?q=test2` の要求は、独自の有効期限設定を持つ個別の資産としてキャッシュされます。
* ルール セットを使用して **キャッシュ キー クエリ文字列** の動作を指定して、キャッシュ キーの生成時に指定されたパラメーターを追加したり除外したりすることもできます。 たとえば、既定のキャッシュ キーが /foo/image/asset.html で、要求の例が `https://contoso.com//foo/image/asset.html?language=EN&userid=100&sessionid=200` であるとします。 クエリ文字列 'userid' を除外するルール セット ルールがあります。 この場合、クエリ文字列のキャッシュ キーは `/foo/image/asset.html?language=EN&sessionid=200` になります。

## <a name="cache-purge"></a>キャッシュの消去

キャッシュの消去に関するページを参照してください。

## <a name="cache-expiration"></a>キャッシュの有効期限
キャッシュに項目が格納される期間を判断するために、次のヘッダーの順序が使用されます。</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-Control: max-age=\<seconds>
3. 有効期限: \<http-date>

応答がキャッシュされないことを示す Cache-Control 応答ヘッダー (Cache-Control: private、Cache-Control: no-cache、Cache-Control: no-store など) は受け入れられます。  キャッシュ制御が存在しない場合、既定の動作として、Front Door によってリソースが X 時間の間、キャッシュに入れられます。 ここで、X は 1 日から 3 日までの範囲からランダムに選択されます。

## <a name="request-headers"></a>要求ヘッダー

キャッシュを使用している場合、次の要求ヘッダーは配信元に転送されません。
* Content-Length
* Transfer-Encoding

## <a name="cache-behavior-and-duration"></a>キャッシュの動作と期間

キャッシュの動作と期間は、Front Door デザイナー ルーティング規則とルール エンジンの両方で構成できます。 ルール エンジンのキャッシュ構成は、常に Front Door デザイナー ルーティング規則の構成をオーバーライドします。

* "*キャッシュ*" が **無効** になっている場合は、配信元の応答ディレクティブには関係なく、Front Door では応答のコンテンツをキャッシュしません。

* "*キャッシュ*" が **有効** になっている場合、キャッシュの動作は、 *[キャッシュの既定の期間を使用する]* の値によって異なります。
    * *[キャッシュの既定の期間を使用する]* が **[はい]** に設定されている場合、Front Door では常に、配信元の応答ヘッダー ディレクティブに従います。 配信元のディレクティブがない場合、Front Door では、1 日から 3 日までの任意の期間コンテンツをキャッシュします。
    * *[キャッシュの既定の期間を使用する]* が **[いいえ]** に設定されている場合、Front Door では常に、"*キャッシュ期間*" (必須フィールド) でオーバーライドします。つまり、配信元の応答ディレクティブの値を無視して、そのキャッシュ期間コンテンツをキャッシュします。 

> [!NOTE]
> * Front Door デザイナー ルーティング規則で設定される "*キャッシュ期間*" は **最小キャッシュ期間** です。 配信元のキャッシュ制御ヘッダーの TTL がオーバーライド値より大きい場合、このオーバーライドは機能しません。
> * Azure Front Door では、オブジェクトをキャッシュに保存する最低期間を保証していません。 キャッシュされたコンテンツは、要求を受ける頻度が他のコンテンツほど高くない場合、頻繁に要求されるコンテンツを受け入れる空きを作るため、有効期間が過ぎる前に、エッジのキャッシュから削除される場合があります。
>

## <a name="next-steps"></a>次のステップ

* [ルート セットの一致条件](concept-rule-set-match-conditions.md)の詳細を確認します。
* [ルート セットのアクション](concept-rule-set-actions.md)の詳細を確認します。
