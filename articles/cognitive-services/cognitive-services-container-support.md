---
title: Azure Cognitive Services コンテナーをオンプレミスで使用する
titleSuffix: Azure Cognitive Services
description: Docker コンテナーを使用して Cognitive Services をオンプレミスで使用する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: cog-serv-seo-aug-2020, ignite-fall-2021
ms.service: cognitive-services
ms.topic: article
ms.date: 10/28/2021
ms.author: aahi
keywords: オンプレミス, Docker, コンテナー, Kubernetes
ms.openlocfilehash: 8758a4688e66a1000c142ab775e563ff829bc79b
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158282"
---
# <a name="azure-cognitive-services-containers"></a>Azure Cognitive Services コンテナー

Azure Cognitive Services には、[Docker](https://www.docker.com/what-container) コンテナーがいくつか用意されており、これにより、Azure で使用できるものと同じ API をオンプレミスで使用できます。 これらのコンテナーを使用すると、コンプライアンス、セキュリティ、またはその他の運用上の理由でデータに対して Cognitive Services を使用することが柔軟に可能になります。 現在、コンテナー サポートは Azure Cognitive Services のサブセットで利用できます。

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

コンテナー化とは、アプリケーションまたはサービスとその依存関係や構成をコンテナー イメージとしてパッケージ化するソフトウェア配布のアプローチです。 コンテナー イメージは、ほとんどまたはまったく変更せずに、コンテナー ホストにデプロイできます。 コンテナーは、相互および基になるオペレーティング システムから分離され、仮想マシンよりもフット プリントが小さくなります。 短期間のタスクに対してコンテナーをコンテナー イメージからインスタンス化し、不要になったら削除できます。

## <a name="features-and-benefits"></a>機能とメリット

- **イミュータブル インフラストラクチャ**:DevOps チームを有効にすると、一貫性と信頼性のある既知のシステム パラメーターのセットを活用しながら、変更に適応できます。 コンテナーには、予測可能なエコシステム内でピボットして構成の誤差を回避できる柔軟性があります。
- **データの制御**:Cognitive Services によってデータが処理される場所を選択します。 これは、クラウドにはデータを送信できないものの、Cognitive Services API にアクセスする必要がある場合に不可欠です。 ハイブリッド環境でデータ、管理、ID、セキュリティの整合性をサポートします。
- **モデルの更新の制御**:ソリューションにデプロイされているモデルのバージョン管理と更新を柔軟に行うことができます。
- **移植可能なアーキテクチャ**:Azure、オンプレミス、エッジにデプロイできる移植可能なアプリケーション アーキテクチャを作成できます。 コンテナーは、[Azure Kubernetes Service](../aks/index.yml)、[Azure Container Instances](../container-instances/index.yml)、または [Azure Stack](/azure-stack/operator) にデプロイされた [Kubernetes](https://kubernetes.io/) クラスターに直接デプロイできます。 詳しくは、「[Kubernetes を Azure Stack にデプロイする](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)」をご覧ください。
- **高スループット/低待ち時間**:お客様は、アプリケーションのロジックとデータに物理的に近い場所で Cognitive Services を実行できるようにすることで、高いスループットと低待ち時間の要件に合わせてスケーリングできます。 コンテナーでは、1 秒あたりのトランザクション数 (TPS) は制限されません。必要なハードウェア リソースを提供した場合は、コンテナーのスケールアップとスケールアウトの両方を行って需要を処理することができます。
- **スケーラビリティ**:コンテナー化とコンテナー オーケストレーション ソフトウェア (Kubernetes など) がますます普及し、スケーラビリティは、技術進歩の最前線にあります。 スケーラブルなクラスター基盤の上に構築されたアプリケーション開発は、高可用性に対応します。

## <a name="containers-in-azure-cognitive-services"></a>Azure Cognitive Services でのコンテナー

Azure Cognitive Services のコンテナーでは、次の Docker コンテナー セットが提供されます。各コンテナーには、Azure Cognitive Services のサービスの機能のサブセットが含まれます。 以下の表で説明とイメージの場所を確認できます。 [コンテナー イメージ](containers/container-image-tags.md)の一覧も提供されています。

### <a name="decision-containers"></a>デシジョン コンテナー

| サービス |  コンテナー | 説明 | 可用性 |
|--|--|--|--|
| [Anomaly Detector][ad-containers] | **Anomaly Detector** ([イメージ](https://hub.docker.com/_/microsoft-azure-cognitive-services-decision-anomaly-detector))  | Anomaly Detector API では、機械学習を利用することで、時系列データを監視し、その中の異常を検出できます。 | 一般公開 |

### <a name="language-containers"></a>言語コンテナー

| サービス |  コンテナー | 説明 | 可用性 |
|--|--|--|--|
| [LUIS][lu-containers] |  **LUIS** ([イメージ](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | トレーニング済みまたは発行済みの Language Understanding モデル ("LUIS アプリ" と呼ばれます) を Docker コンテナーに読み込みます。ユーザーは、そのコンテナーの API エンドポイントからクエリ予測を利用することができます。 コンテナーからクエリのログを収集し、それらを [LUIS ポータル](https://www.luis.ai)に再度アップロードすることで、アプリの予測精度を高めることができます。 | 一般公開 |
| [言語サービス][ta-containers-keyphrase] | **キー フレーズ抽出** ([イメージ](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | 主なポイントを識別するキー フレーズを抽出します。 たとえば、「食べ物はおいしくて、すばらしいスタッフがいた」というテキストを入力すると、この API は話題の中心として "食べ物" と "すばらしいスタッフ" を返します。 | プレビュー |
| [言語サービス][ta-containers-language] |  **テキスト言語検出** ([イメージ](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | 最大 120 の言語に対して、入力テキストが書かれている言語を検出し、要求で送信されたドキュメントごとに 1 つの言語コードを報告します。 言語コードは、評価値の強度を示すスコアと組みになります。 | 一般公開 |
| [言語サービス][ta-containers-sentiment] | **感情分析** ([イメージ](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) | 肯定的または否定的な感情の手がかりを探して未加工のテキストを分析します。 このバージョンの感情分析からは、各ドキュメントとその中の文のセンチメント ラベル (たとえば、"*肯定的*"、"*否定的*") が返されます。 |  一般公開 |
| [言語サービス][ta-containers-health] |  **Text Analytics for Health** | 構造化されていない臨床テキストから医療情報を抽出してラベル付けします。 | プレビュー |
| [Translator][tr-containers] | **Translator** | 複数の言語と方言にテキストを翻訳できます。 | 限定的なプレビュー。 [アクセスを要求](https://aka.ms/csgate-translator)してください。 | 

### <a name="speech-containers"></a>音声コンテナー

> [!NOTE]
> Speech コンテナーを使用するには、[オンライン要求フォーム](https://aka.ms/csgate)に入力する必要があります。

| サービス |  コンテナー | 説明 | 可用性 |
|--|--|--|
| [Speech Service API][sp-containers-stt] |  **音声テキスト変換** ([イメージ](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | 連続するリアルタイムの音声をテキストに書き起こします。 | 一般公開 |
| [Speech Service API][sp-containers-cstt] | **カスタム音声テキスト変換** ([イメージ](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | カスタム モデルを使用して、連続するリアルタイムの音声をテキストに書き起こします。 | 一般公開 |
| [Speech Service API][sp-containers-tts] | **テキスト読み上げ** ([イメージ](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-text-to-speech)) | テキストを自然な音声に変換します。 | 一般公開 |
| [Speech Service API][sp-containers-ctts] | **カスタム テキスト読み上げ** ([イメージ](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-text-to-speech)) | カスタム モデルを使用してテキストを自然な音声に変換します。 | 限定的なプレビュー |
| [Speech Service API][sp-containers-ntts] | **ニューラル テキスト読み上げ** ([イメージ](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-neural-text-to-speech)) | ディープ ニューラル ネットワーク テクノロジを使用してテキストを自然な響きの音声に変換することで、合成音声がより自然なものになります。 | 一般公開 |
| [Speech Service API][sp-containers-lid] | **音声言語検出** ([イメージ](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-language-detection)) | 音声の言語を特定します。 | 限定的なプレビュー |

### <a name="vision-containers"></a>ビジョン コンテナー


| サービス |  コンテナー | 説明 | 可用性 |
|--|--|--|--|
| [Computer Vision][cv-containers] | **Read OCR** ([イメージ](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read)) | Read OCR コンテナーを使用すると、JPEG、PNG、BMP、PDF、TIFF の各ファイル形式をサポートするイメージとドキュメントから、印刷されたテキストおよび手書きのテキストを抽出できます。 詳細については、[Read API のドキュメント](./computer-vision/overview-ocr.md)に関する記事を参照してください。 | 限定的なプレビュー。 [アクセスを要求][request-access]してください。 |
| [空間分析][spa-containers] | **空間分析** ([イメージ](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-spatial-analysis)) | リアルタイム ストリーミング ビデオを分析して、ユーザーとその動きの間の空間関係、および物理的な環境内のオブジェクトの相互作用を把握します。 | プレビュー |

<!--
|[Personalizer](./personalizer/what-is-personalizer.md) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

さらに、一部のコンテナーは、Cognitive Services [マルチサービス リソース](cognitive-services-apis-create-account.md) オファリング内でサポートされています。 単一の Cognitive Services オールインワン リソースを作成し、次のサービスに対してサポートされているサービス全体で同じ課金キーを使用できます。

* Computer Vision
* LUIS
* 言語サービス

## <a name="prerequisites"></a>前提条件

Azure Cognitive Services コンテナーを使用するには、次の前提条件を満たしている必要があります。

**Docker エンジン**: Docker エンジンをローカルにインストールしている必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms)、[Windows](https://docs.docker.com/docker-for-windows/) 上で Docker 環境を構成するパッケージが用意されています。 Windows では、Linux コンテナーをサポートするように Docker を構成する必要があります。 Docker コンテナーは、[Azure Kubernetes Service](../aks/index.yml) または [Azure Container Instances](../container-instances/index.yml) に直接デプロイできます。

コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。

**Microsoft Container Registry と Docker に関する知識**: レジストリ、リポジトリ、コンテナー、コンテナー イメージなどの Microsoft Container Registry と Docker の両方の概念の基本的な理解に加えて、基本的な `docker` コマンドの知識が必要です。

Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。

個々のコンテナーには、サーバーやメモリ割り当ての要件など、独自の要件もある場合があります。

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

## <a name="developer-samples"></a>開発者向けサンプル

開発者向けサンプルは、[GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-containers-samples)から入手できます。

## <a name="next-steps"></a>次のステップ

Cognitive Services で使用できる[コンテナー レシピ](containers/container-reuse-recipe.md)について学習します。

Azure Cognitive Services のコンテナーによって提供される機能をインストールして試してください。

* [Anomaly Detector コンテナー][ad-containers]
* [Computer Vision コンテナー][cv-containers]
* [Language Understanding (LUIS) コンテナー][lu-containers]
* [Speech Service API コンテナー][sp-containers]
* [言語サービス コンテナー][ta-containers]
* [Translator コンテナー][tr-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[spa-containers]: ./computer-vision/spatial-analysis-container.md
[sp-containers-lid]: speech-service/speech-container-howto.md?tabs=lid
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[sp-containers-ntts]: speech-service/speech-container-howto.md?tabs=ntts
[ta-containers]: language-service/overview.md#deploy-on-premises-using-docker-containers
[ta-containers-keyphrase]: language-service/key-phrase-extraction/how-to/use-containers.md
[ta-containers-language]: language-service/language-detection/how-to/use-containers.md
[ta-containers-sentiment]: language-service/sentiment-opinion-mining/how-to/use-containers.md
[ta-containers-health]: language-service/text-analytics-for-health/how-to/use-containers.md
[tr-containers]: translator/containers/translator-how-to-install-container.md
[request-access]: https://aka.ms/csgate
