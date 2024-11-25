---
title: 元のリリースとの違い
titleSuffix: Azure Digital Twins
description: Azure Digital Twins の新しいバージョンでの変更点について理解する
author: baanders
ms.author: baanders
ms.date: 8/24/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 551eae5e1d6ed5ee57e9753cbd487e9b7bd12277
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225739"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-original-version-2018"></a>新しい Azure Digital Twins と以前のバージョン (2018) との違い 元のバージョン (2018) とどのように違うか

Azure Digital Twins の最初のパブリック プレビューは、2018 年 10 月にリリースされました。 元のバージョンの主要な概念は、現在のサービスに引き継がれていますが、インターフェイスと実装の詳細の多くは、サービスの柔軟性を高め、サービスにアクセスしやすくするために変更されています。 これらの変更は、お客様からのフィードバックによって行われました。

> [!IMPORTANT]
> 新しいサービスの拡張された機能を受けて、元の Azure Digital Twins サービスは廃止されました。 2021 年 1 月の時点で、その API および関連付けられたデータは使用できなくなりました。

最初のパブリック プレビューで Azure Digital Twins の最初のバージョンを使用した場合は、この記事の情報とベスト プラクティスを利用して、現在のサービスの使用方法を学習し、その機能を活用してください。

## <a name="differences-by-topic"></a>トピック別の相違点

次の表では、元のバージョンのサービスと現在のサービスの間で変更された概念を並べて示します。

| トピック | 元のバージョン | 現在のバージョン |
| --- | --- | --- | --- |
| **モデリング**<br>*より柔軟* | 元のリリースは、スマート空間を中心に設計されていました。そのため、構築のために組み込みのボキャブラリが付属していました。 | 現在の Azure Digital Twins は、ドメインに依存しません。 ソリューションに独自のカスタム ボキャブラリとカスタム モデルを定義して、より柔軟な方法でより多くの種類の環境を表すことができます。<br><br>詳細については、[カスタム モデル](concepts-models.md)に関するページを参照してください。 |
| **トポロジ**<br>*より柔軟*| 元のリリースでは、スマート空間に合わせたツリー データ構造がサポートされていました。 デジタル ツインは、階層リレーションシップを使用して接続されていました。 | 現在のリリースでは、デジタル ツインは任意のグラフ トポロジに接続でき、必要に応じて整理することができます。 このように自由であるために、実際の複雑なリレーションシップをより柔軟に表現できるようになります。<br><br>詳細については、[デジタル ツインとツイン グラフ](concepts-twins-graph.md)に関するページを参照してください。 |
| **Compute**<br>*より豊富でより柔軟* | 元のリリースでは、イベントとテレメトリを処理するロジックは、JavaScript ユーザー定義関数 (UDF) で定義されていました。 UDF を使用したデバッグは制限されていました。 | 現在のリリースには、オープン コンピューティング モデルがあります。[Azure Functions](../azure-functions/functions-overview.md) などの外部のコンピューティング リソースを接続することによって、カスタム ロジックを提供します。 この機能により、ご自分の好きなプログラミング言語を使用して、制限なくカスタム コード ライブラリにアクセスし、外部サービスが持っている場合のある開発リソースやデバッグ リソースを活用することができます。<br><br>Azure Functions を通じたデータ フローによるエンドツーエンドのシナリオについては、[エンドツーエンド ソリューションの接続](tutorial-end-to-end.md)に関するページを参照してください。 |
| **IoT Hub を使用したデバイス管理**<br>*よりアクセスしやすい* | 元のリリースでは、Azure Digital Twins サービスの内部にある [IoT Hub](../iot-hub/about-iot-hub.md) のインスタンスを使用してデバイスを管理していました。 この統合されたハブには、開発者は完全にはアクセスできませんでした。 | 現在のリリースでは、個別に作成された IoT Hub インスタンスを (既に管理しているすべてのデバイスと共に) 接続して、自分の所有する IoT ハブを使用します。 このアーキテクチャにより、IoT Hub のすべての機能にアクセスできるようになり、デバイス管理を制御できるようになります。<br><br>詳細については、[IoT Hub からのテレメトリの取り込み](how-to-ingest-iot-hub-data.md)に関するページを参照してください。 |
| **Security**<br>*より標準* | 元のリリースには、インスタンスへのアクセスを管理するために使用できる定義済みのロールがありました。 | 現在のリリースは、他の Azure サービスで使用されているのと同じ [Azure のロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) バックエンド サービスと統合されています。 この種類の統合により、ご自分のソリューション内の他の Azure サービス (IoT Hub、Azure Functions、Event Grid など) 間での認証が簡単になります。<br>RBAC を使用すると、事前定義されたロールを引き続き使用することも、カスタム ロールを作成して構成することもできます。<br><br>詳細については、[Azure Digital Twins ソリューションのセキュリティ](concepts-security.md)に関するページを参照してください。 |
| **スケーラビリティ**<br>*より大きい* | 元のリリースでは、デバイス、メッセージ、グラフ、およびスケール ユニットに対するスケール制限がありました。 Azure Digital Twins のインスタンスは、サブスクリプションごとに 1 つしかサポートされていませんでした。  | 現在のリリースでは、スケーラビリティが向上した新しいアーキテクチャに依存しており、コンピューティング能力が向上しています。 また、サブスクリプションごと、リージョンごとに 10 個のインスタンスがサポートされます。<br><br>現在のリリースでの制限の詳細については、「[Azure Digital Twins サービスの制限](reference-service-limits.md)」を参照してください。 |

## <a name="service-limits"></a>サービスの制限

Azure Digital Twins の制限の一覧については、「[Aure Digital Twins サービスの制限](reference-service-limits.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* 「[Azure Digital Twins Explorer を開始する](quickstart-azure-digital-twins-explorer.md)」で、現在のリリースの使用方法について学習します。

* または、最初に主要な概念について[カスタム モデル](concepts-models.md)に関するページを参照してください。