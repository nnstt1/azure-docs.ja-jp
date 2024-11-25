---
title: Azure Active Directory Identity Protection 用語集
description: Azure Active Directory Identity Protection 用語集
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9adb0f453aebb47c939b1ab0ead601b05145f217
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727631"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory Identity Protection 用語集

### <a name="at-risk-user"></a>危険 (ユーザー)
1 つ以上のアクティブなリスク検出があるユーザーです。 

### <a name="atypical-sign-in-location"></a>特殊なサインインの場所
特定のユーザー、類似ユーザーまたはテナントに対して一般的ではない地域からのサインインです。

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
組織の ID に影響するリスク検出や潜在的な脆弱性に関する統合ビューを提供する Azure Active Directory のセキュリティ モジュールです。

### <a name="conditional-access"></a>条件付きアクセス
リソースへのアクセスを保護するためのポリシー。 条件付きアクセス規則は、Azure Active Directory に格納され、リソースへのアクセス権を付与する前に Azure AD によって評価されます。  規則の例には、ユーザーの場所、デバイスの正常性またはユーザーの認証方法に基づいたアクセス権の制限が含まれます。

### <a name="credentials"></a>資格情報
ID およびローカル リソースとネットワーク リソースへのアクセス権を取得するために使用する ID の証明を含む情報です。 資格情報には、ユーザー名とパスワード、スマート カードおよび証明書などがあります。

### <a name="event"></a>イベント
Azure Active Directory 内のアクティビティのレコードです。

### <a name="false-positive-risk-detection"></a>誤検知 (リスク検出)
Identity Protection ユーザーが手動で設定するリスク検出の状態です。リスク検出が調査され、リスク検出として誤ってフラグが付けられたことを示します。

### <a name="identity"></a>ID
パスワードまたは証明書などの条件に基づいて、認証を使用して検証する必要のある個人またはエンティティです。

### <a name="identity-risk-detection"></a>ID リスク検出
Identity Protection が異常としてフラグを付けた Azure AD イベントです。ID が侵害されていることを示す可能性があります。

### <a name="ignored-risk-detection"></a>無視 (リスク検出)
Identity Protection ユーザーが手動で設定するリスク検出の状態です。修復アクションを実行することなく、リスク検出がクローズされたことを示します。

### <a name="impossible-travel-from-atypical-locations"></a>特殊な場所からのあり得ない移動
同じユーザーに 2 回のサインインが検出される場合、少なくともその 1 回が特殊なサインインの場所からの場合、およびサインインの間隔がこれらの場所を物理的に移動する最短時間よりも短い場合にトリガーされるリスク検出です。  

### <a name="investigation"></a>調査
修復または対応策の手順が必要であるかどうかを決定し、ID が侵害を受けているかどうか、およびどのように侵害されたかを理解し、侵害された ID がどのように使用されたかを理解するために、アクティビティ、ログ、およびリスク検出に関するその他の関連情報を確認するプロセスです。

### <a name="leaked-credentials"></a>漏洩した資格情報
現在のユーザー資格情報 (ユーザー名とパスワード) が、悪質な Web に公開投稿されたことを調査員が発見した場合にトリガーされるリスク検出です。

### <a name="mitigation"></a>対応策
ID またはデバイスを安全な状態に復元せずに、侵害された ID またはデバイスを悪用されないように、攻撃者の能力を制限または排除するアクションです。 対応策では、ID またはデバイスに関連する以前のリスク検出は解決しません。

### <a name="multi-factor-authentication"></a>多要素認証
2 つ以上の認証方法を必要とする認証方法です。これには、ユーザーの所有物 (証明書など)、ユーザーが知っている内容 (ユーザー名、パスワードまたはパス フレーズなど)、物理属性 (拇印など)、個人属性 (署名など) が含まれる場合があります。

### <a name="offline-detection"></a>オフラインの検出
既に発生したイベントの事後のサインイン試行など、イベントのリスクの評価と異常の検出です。

### <a name="policy-condition"></a>ポリシーの条件
ポリシーに含まれる、またはポリシーから除外されるエンティティ (グループ、ユーザー、アプリケーション、デバイスのプラットフォーム、デバイスの状態、IP 範囲、クライアントの種類) を定義するセキュリティ ポリシーの一部です。

### <a name="policy-rule"></a>ポリシー規則
ポリシーをトリガーする状況と、ポリシーがトリガーされたときに実行されるアクションを説明するセキュリティ ポリシーの一部です。

### <a name="prevention"></a>防止
侵害の疑いがある、または侵害が検知された ID またはデバイスの不正使用によって、組織の被害を防ぐためのアクション。 防止アクションでは、デバイスまたは ID は保護されず、以前のリスク検出は解決しません。

### <a name="privileged-user"></a>特権 (ユーザー)
Azure Active Directory に 1 つ以上のリソースへの永続的または一時的な管理者アクセス許可を所有するリスク検出時のユーザーです。全体管理者、課金管理者、サービス管理者、ユーザー管理者、パスワード管理者など。 

### <a name="real-time"></a>リアルタイム
リアルタイムの検出を参照してください。

### <a name="real-time-detection"></a>リアルタイムの検出
イベントの続行が許可される前にサインインを試行するなど、イベントのリスクの評価および異常の検出です。

### <a name="remediated-risk-detection"></a>修復済み (リスク検出)
Identity Protection から自動的に設定されるリスク検出の状態です。この種類のリスク検出に対する標準的な修復アクションを使用して、リスク検出が修復されたことを示します。 たとえば、ユーザーのパスワードがリセットされると、以前のパスワードが侵害されたことを示す多くのリスク検出は自動的に修復されます。

### <a name="remediation"></a>修復
以前に侵害の疑いがある、または侵害を検知した ID またはデバイスをセキュリティで保護するアクションです。 修復アクションで、ID やデバイスが安全な状態に復元され、ID またはデバイスに関連する以前のリスク検出が解決します。

### <a name="resolved-risk-detection"></a>解決済み (リスク検出)
Identity Protection ユーザーが手動で設定するリスク検出の状態です。ユーザーが Identity Protection 以外で適切な修復アクションを実行し、リスク検出がクローズされたと見なされることを示します。

### <a name="risk-detection-status"></a>リスク検出の状態
リスク検出のプロパティです。イベントがアクティブかどうか、およびイベントがクローズされている場合はクローズの理由を示します。

### <a name="risk-detection-type"></a>リスク検出の種類
リスク検出のカテゴリです。危険と見なされるイベントの原因となった異常の種類を示します。

### <a name="risk-level-risk-detection"></a>リスク レベル (リスク検出)
Identity Protection ユーザーが、組織のリスクを低減するアクションに優先順位を付けるために役立つリスク検出の重大度 (高、中、低) を示します。 

### <a name="risk-level-sign-in"></a>リスク レベル (サインイン)
他のユーザーがユーザーの ID を使用しようとする、特定のサインインの可能性 (高、中、低) を示します。

### <a name="risk-level-user-compromise"></a>リスク レベル (ユーザーの侵害)
ID が侵害されている可能性 (高、中、低) を示します。

### <a name="risk-level-vulnerability"></a>リスク レベル (脆弱性)
Identity Protection ユーザーが、組織のリスクを低減するアクションに優先順位を付けるために役立つ脆弱性の重大度 (高、中、低) を示します。

### <a name="secure-identity"></a>セキュア (ID)
パスワードの変更やマシンの再イメージ化などの修復アクションを実行し、侵害された可能性のある ID を侵害されていない状態に復元します。

### <a name="security-policy"></a>セキュリティ ポリシー
ポリシー規則と条件のコレクションです。 ポリシーは、ユーザー、グループ、アプリ、デバイス、デバイスのプラットフォーム、デバイスの状態、IP 範囲、および Auth2.0 クライアントの種類などのエンティティに適用できます。 ポリシーが有効化されると、ポリシーに含まれるエンティティがリソースのトークンを発行するたびに評価されます。

### <a name="sign-in-v"></a>サインイン (動詞)
Azure Active Directory 内で ID を認証することです。

### <a name="sign-in-n"></a>サインイン (名詞)
Azure Active Directory で ID を認証するプロセスとアクション、およびこの操作をキャプチャするイベントです。

### <a name="sign-in-from-anonymous-ip-address"></a>匿名の IP アドレスからのサインイン
匿名のプロキシ IP アドレスとして識別されている IP アドレスからのサインインに成功した後にトリガーされるリスク検出です。

### <a name="sign-in-from-infected-device"></a>感染したデバイスからのサインイン
ボット サーバーとの通信をアクティブに試行していて、1 つ以上の侵害されたデバイスで使用されていることが判明している IP アドレスからサインインが開始された場合に、トリガーされるリスク検出です。

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>不審なアクティビティのある IP アドレスからのサインイン
短期間に複数のユーザー アカウントでログイン試行に失敗した回数が多い IP アドレスから正常にサインインされた後にトリガーされるリスク検出です。

### <a name="sign-in-from-unfamiliar-location"></a>不明な場所からのサインイン
ユーザーが新しい場所 (IP、緯度/経度、ASN) から正常にサインインしたときにトリガーされるリスク検出です。

### <a name="sign-in-risk"></a>サインイン リスク
リスク レベル (サインイン) を参照してください

### <a name="sign-in-risk-policy"></a>サインイン リスク ポリシー
事前定義された条件と規則に基づいて、特定のサインインのリスクを評価し、対応策に適用する条件付きアクセス ポリシー。

### <a name="user-compromise-risk"></a>ユーザーの侵害リスク
リスク レベル (ユーザーの侵害) を参照してください。

### <a name="user-risk"></a>ユーザー リスク
リスク レベル (ユーザーの侵害) を参照してください。

### <a name="user-risk-policy"></a>ユーザー リスク ポリシー
事前定義された条件と規則に基づいて、サインインを考慮し、対応策に適用する条件付きアクセス ポリシー。

### <a name="users-flagged-for-risk"></a>リスクのフラグ付きユーザー
アクティブまたは修復済みのいずれかのリスク検出があるユーザーです。

### <a name="vulnerability"></a>脆弱性
ディレクトリが悪用または脅威の影響を受けやすくする Azure Active Directory の構成または条件です。

## <a name="see-also"></a>関連項目

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)