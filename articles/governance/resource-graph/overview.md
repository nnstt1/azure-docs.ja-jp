---
title: Azure Resource Graph の概要
description: Azure Resource Graph サービスによってサブスクリプションとテナントにまたがるリソースの複雑なクエリの大規模な実行がどのように実現されるかについて理解します。
ms.date: 08/17/2021
ms.topic: overview
ms.openlocfilehash: 740a629bd37309d71e153b38c13d8fe91b4e08d3
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324416"
---
# <a name="what-is-azure-resource-graph"></a>Azure Resource Graph とは

Azure Resource Graph は Azure 内のサービスであり、Azure Resource Management を拡張するよう設計されています。環境を効果的に管理できるように、特定のセットのサブスクリプションにわたって大規模にクエリを実行する機能を備えた、高効率および高性能のリソース探索を提供します。 これらのクエリでは、次の機能を提供します。

- 複雑なフィルター処理、グループ化、およびリソースのプロパティでの並べ替えによりリソースのクエリを行う機能。
- ガバナンスの要件に基づいてリソースを繰り返し探索する機能。
- 広大なクラウド環境にポリシーを適用することの影響を評価する機能。
- [リソースのプロパティに加えられた変更について詳しく説明する](./how-to/get-resource-changes.md)機能 (プレビュー)。

このドキュメントでは、それぞれの機能について詳しく見ていきます。

> [!NOTE]
> Azure Resource Graph は、Azure portal の検索バー、新しい "すべてのリソース" 参照エクスペリエンス、Azure Policy の[変更履歴](../policy/how-to/determine-non-compliance.md#change-history)
> の "_差分表示_" を強化します。 大規模な環境を管理するお客様をサポートするために設計されています。

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Resource Graph が Azure Resource Manager をどのように補完するか

Resource Manager では現在、基本的なリソース フィールドに対するクエリがサポートされています。具体的には、リソース名、ID、種類、リソース グループ、サブスクリプション、および場所です。 Resource Manager には、一度に 1 つのリソースについて詳細なプロパティを得るために個別のリソース プロバイダーを呼び出す機能も用意されています。

Azure Resource Graph を使用することにより、各リソースプロバイダーへの個別の呼び出しを行う必要なく、リソースプロバイダーが返すこれらのプロパティにアクセスすることができます。 サポートされるリソースの種類の一覧については、[テーブルとリソースの種類のリファレンス](./reference/supported-tables-resources.md)に関するページを確認してください。 サポートされるリソースの種類は、[Azure Resource Graph エクスプローラーのスキーマ ブラウザー](./first-query-portal.md#schema-browser)を使用して確認することもできます。

Azure Resource Graph では、次のことができます。

- 各リソース プロバイダーへの個別の呼び出しを行う必要なく、リソース プロバイダーから返されるプロパティにアクセスする。
- リソースに加えられた変更の過去 14 日間分の履歴を表示して、どのプロパティがいつ変更されたかを確認する。 (プレビュー)

> [!NOTE]
> "_プレビュー_" 機能として、一部の `type` オブジェクトは、Resource Manager 以外の追加のプロパティを備えています。 詳細については、「[拡張プロパティ (プレビュー)](./concepts/query-language.md#extended-properties)」を参照してください。

## <a name="how-resource-graph-is-kept-current"></a>Resource Graph が最新の状態に保たれるしくみ

Azure リソースが更新されると、Resource Manager から Resource Graph に変更の通知が届きます。
その後、Resource Graph によってそのデータベースが更新されます。 Resource Graph では、定期的な "_フル スキャン_" も行われます。 このスキャンにより、通知が届かなかった場合、またはリソースが Resource Manager の外部で更新されたときにも、Resource Graph が最新の状態に維持されます。

> [!NOTE]
> Resource Graph では、各リソースプロバイダーの最新の非プレビュー API に対して `GET` を使用して、プロパティと値を収集します。 その結果、想定されるプロパティを取得できない場合があります。 場合によっては、使用される API バージョンがオーバーライドされ、最新のプロパティまたは広く使用されているプロパティが結果に提供されます。 ご使用の環境での完全な一覧については、[各リソースの種類の API バージョンを表示する](./samples/advanced.md#apiversion)方法に関するサンプルを参照してください。

## <a name="the-query-language"></a>クエリ言語

これで Azure Resource Graph についてはよく理解したので、クエリを作成する方法に進みましょう。

Azure Resource Graph のクエリ言語が Azure Data Explorer で使用される [Kusto クエリ言語](/azure/data-explorer/data-explorer-overview)に基づくことを理解することが重要です。

最初に、Azure Resource Graph と共に使用することができる操作および機能の詳細については、[Resource Graph のクエリ言語](./concepts/query-language.md)を参照してください。 リソースをブラウズするためには、[リソースを探索する](./concepts/explore-resources.md)を参照してください。

## <a name="permissions-in-azure-resource-graph"></a>Azure Resource Graph でのアクセス許可

Resource Graph を使用するためには、最低限、クエリを実行するリソースに対する読み取りアクセスを可能にする適切な権限が、[Azure ロール ベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) を通じて付与されている必要があります。 Azure のオブジェクトまたはオブジェクト グループに対する `read` 以上のアクセス許可がないと、結果は返されません。

> [!NOTE]
> Resource Graph では、プリンシパルがログイン中に利用できるサブスクリプションが使用されます。 アクティブなセッション中に追加された新しいサブスクリプションのリソースを表示するには、プリンシパルがコンテキストを更新する必要があります。 ログアウトしてから再度ログインすると、このアクションが自動的に実行されます。

Azure CLI と Azure PowerShell はユーザーがアクセスできるサブスクリプションを使用します。 REST API を直接使用すると、サブスクリプションの一覧がユーザーごとに提供されます。 一覧内のいずれかのサブスクリプションにユーザーがアクセスできる場合は、ユーザーがアクセス権を持っているサブスクリプションに対するクエリ結果が返されます。 この動作は、[Resource Groups - 一覧](/rest/api/resources/resourcegroups/list)を呼び出す場合と同じです \- アクセス権を持っているリソース グループが取得されますが、この結果が部分的である可能性は示されません。 ユーザーに適切な権限があるサブスクリプションがサブスクリプション一覧にない場合の応答は、_403_ (禁止) です。

> [!NOTE]
> **プレビュー** REST API バージョン `2020-04-01-preview` では、サブスクリプションの一覧が省略されている場合があります。
> `subscriptions` と `managementGroupId` の両方のプロパティが要求で定義されていない場合、"_スコープ_" はテナントに設定されます。 詳しくは、[クエリのスコープ](./concepts/query-language.md#query-scope)に関するページを参照してください。

## <a name="throttling"></a>Throttling

無料サービスとして、すべてのお客様に最適なエクスペリエンスと応答時間が提供されるよう、Resource Graph へのクエリはスロットルされます。 お客様の組織が大規模かつ頻繁なクエリに Resource Graph API を使用したい場合、[Resource Graph ポータル ページ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph)からポータルの "フィードバック" を使用してください。
ビジネス ケースを明記し、チームが連絡できるように [Microsoft からフィードバックについてメールをお送りする場合があります] チェック ボックスをオンにしてください。

Resource Graph では、ユーザー レベルでクエリのスロットルが行われます。 サービスの応答には、次の HTTP ヘッダーが含まれています。

- `x-ms-user-quota-remaining` (int):ユーザーの残りリソース クォータ。 この値はクエリ カウントにマップされます。
- `x-ms-user-quota-resets-after` (hh:mm:ss):ユーザーのクォータ消費量がリセットされるまでの期間

詳細については、[調整された要求に関するガイダンス](./concepts/guidance-for-throttled-requests.md)のページを参照してください。

## <a name="running-your-first-query"></a>最初のクエリを送信する

Azure Resource Graph エクスプローラーは Azure portal に組み込まれており、Resource Graph クエリを Azure portal 内から直接実行できます。 その結果を動的なグラフとしてピン留めすれば、ポータルのワークフローから動的な情報をリアルタイムで得ることができます。 詳細については、[Azure Resource Graph エクスプローラーを使った初めてのクエリ](./first-query-portal.md)に関するページを参照してください。

Resource Graph は、Azure CLI、Azure PowerShell、Azure SDK for Python などをサポートします。 どの言語も、クエリの構造は同じです。 以下、Resource Graph を有効にする方法を手段ごとに示します。

- [Azure portal および Resource Graph エクスプローラー](./first-query-portal.md)
- [Azure CLI](./first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](./first-query-powershell.md#add-the-resource-graph-module)
- [Python](./first-query-python.md#add-the-resource-graph-library)

## <a name="next-steps"></a>次のステップ

- [クエリ言語](./concepts/query-language.md)の詳細について学習します。
- [初歩的なクエリ](./samples/starter.md)で使用されている言語を確認します。
- [高度なクエリ](./samples/advanced.md)で高度な使用方法を確認します。
