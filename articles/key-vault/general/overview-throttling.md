---
title: Azure Key Vault のスロットル ガイダンス
description: Key Vault の調整では同時呼び出しの数を制限して、リソースの乱用を防ぎます。
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: f9e3f2095e6fd7a744769c11209ed115767c3aed
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2021
ms.locfileid: "113492597"
---
# <a name="azure-key-vault-throttling-guidance"></a>Azure Key Vault のスロットル ガイダンス

スロットルは、Azure サービスに対する同時呼び出し数を制限し、リソースの過剰使用を防止することを目的に開始されるプロセスです。 Azure Key Vault (AKV) は、大量の要求を処理できるように設計されていますが、 処理しきれない数の要求が発生した場合、クライアントの要求をスロットルすることで、AKV サービスのパフォーマンスと信頼性を最適な状態に保つことができます。

スロットルの制限は、シナリオによって異なります。 たとえば大量の書き込みを実行している場合、読み取りだけを実行している場合と比べて、スロットルの余地は大きくなります。

## <a name="how-does-key-vault-handle-its-limits"></a>Key Vault における制限の扱い

Key Vault のサービス制限は、リソースの乱用を防ぎ、Key Vault の全クライアントのサービス品質を確保します。 サービスのしきい値を超えた場合、Key Vault によってそのクライアントからの要求が一定期間制限され、HTTP 状態コード 429 (要求が多すぎます) が返され、要求は失敗します。 429 が返されて失敗した要求は、Key Vault によって追跡されているスロットル制限に加算されません。 

Key Vault は本来、デプロイ時にシークレットを格納および取得するために使用するように設計されました。  世界が進化し、実行時にシークレットを格納および取得するために Key Vault が使用されています。そして多くの場合、アプリやサービスは Key Vault をデータベースのように使用することを希望しています。  現在の制限は、高いスループット率をサポートしていません。

Key Vault は、最初は [Azure Key Vault サービスの制限](service-limits.md)で指定された制限を使用して作成されました。  Key Vault のスループット率を最大化するための、いくつかの推奨ガイドラインおよびベスト プラクティスを次に示します。
1. スロットルが機能していることを確認します。  クライアントは、429 に対してのエクスポネンシャル バックオフ ポリシーを遵守し、以下のガイダンスに従って再試行を行う必要があります。
1. Key Vault トラフィックを複数のコンテナーと異なるリージョンに分割します。   セキュリティ/可用性ドメインごとに個別のコンテナーを使用します。   5 つのアプリが、2 つのリージョンのそれぞれにある場合は、アプリおよびリージョンに固有のシークレットを格納する 10 個のコンテナーを使用することをお勧めします。  すべてのトランザクションの種類に適用されるサブスクリプション全体の制限は、個々のキー コンテナーの制限の 5 倍です。 たとえば、1 つのサブスクリプションで許可される HSM - その他のトランザクションの最大数は、10 秒間に 5,000 トランザクションです。 サービスまたはアプリ内にシークレットをキャッシュして、キー コンテナーへの直接の RPS を減らしたり、バースト ベースのトラフィックに対処したりすることを検討してください。  また、トラフィックを異なるリージョンに分割して待機時間を最小限にしたり、別のサブスクリプション/コンテナーを使用したりすることもできます。  単一の Azure リージョンの Key Vault サービスには、サブスクリプションの制限を超えて送信しないでください。
1. Azure Key Vault から取得したシークレットをメモリにキャッシュし、可能な限りメモリから再利用してください。  キャッシュされたコピーが動作しなくなった場合 (たとえば、ソースでローテーションされたため) にだけ、Azure Key Vault から再度読み取りしてください。 
1. Key Vault は、自身のサービス シークレット向けに設計されています。   顧客のシークレットを格納する場合 (特に、高スループットの主要なストレージ シナリオの場合) は、データベースまたはストレージ アカウントに暗号化した状態でキーを配置し、マスター キーだけを Azure Key Vault に格納することを検討してください。
1. 公開キーの操作の暗号化、ラップおよび確認は、Key Vault にアクセスせずに実行できます。これにより、スロットルのリスクが軽減されるだけでなく、信頼性も向上します (公開キー マテリアルを適切にキャッシュしている場合)。
1. サービスの資格情報を格納するために Key Vault を使用する場合は、直接認証するために、そのサービスが Azure AD Authentication をサポートしているかどうかを確認します。 これにより、Key Vault は Azure AD トークンを使用できるようになるため、Key Vault の負荷が軽減され、信頼性が向上してコードが簡素化されます。  多くのサービスは、Azure AD 認証の使用に移行しました。[「Azure リソースのマネージド ID をサポートするサービス」](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)の現在の一覧を参照してください。
1. 現在の RPS 制限を維持するために、より長い期間にわたって負荷/デプロイを分散させることを検討してください。
1. アプリが同じシークレットを読み取る必要がある複数のノードで構成されている場合は、1 つのエンティティが Key Vault からシークレットを読み取ってすべてのノードに展開する、ファンアウト パターンを使用することを検討してください。   取得したシークレットをメモリだけにキャッシュします。


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>サービス制限に対応してアプリをスロットルする方法

サービスがスロットルされているときに実践すべき **ベスト プラクティス** を次に示します。
- 要求あたりの操作の数を減らす。
- 要求の頻度を減らす。
- すぐに再試行しない。 
    - すべての要求が使用制限のカウント対象になります。

アプリのエラー処理を実装するときに、HTTP エラー コード 429 を使って、クライアント側でのスロットルの必要性を検出してください。 HTTP 429 エラー コードで再び要求が失敗すれば、Azure のサービス制限が引き続き適用されます。 それ以降は、推奨されているクライアント側のスロットル手法を使用して、成功するまで要求を再試行してください。

エクスポネンシャル バックオフを実装するコードを次に示します。 
```
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
    var client = new SecretClient(new Uri("https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


このコードは、クライアントの C# アプリケーションで使用するのが簡単です。 

### <a name="recommended-client-side-throttling-method"></a>推奨されるクライアント側のスロットル手法

HTTP エラー コード 429 が発生したら、次のように指数関数的バックオフ手法を使ってクライアントのスロットルを開始します。

1. 1 秒待って要求を再試行
2. 引き続きスロットルされる場合は 2 秒待って要求を再試行
3. 引き続きスロットルされる場合は 4 秒待って要求を再試行
4. 引き続きスロットルされる場合は 8 秒待って要求を再試行
5. 引き続きスロットルされる場合は 16 秒待って要求を再試行

通常は、この時点で HTTP 429 応答コードは発生しなくなります。

## <a name="see-also"></a>関連項目

Microsoft Cloud のスロットルに関するさらに詳しい情報については、「[Throttling Pattern (スロットル パターン)](/azure/architecture/patterns/throttling)」を参照してください。
