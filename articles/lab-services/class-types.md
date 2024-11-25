---
title: Azure Lab Services における各種クラスの例 | Microsoft Docs
description: Azure Lab Services を使用してラボを設定できる、いくつかの種類のクラスを提供します。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: d2adfd22649bb65bb7ae0d360a5b2a33944abd8d
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181238"
---
# <a name="class-types-overview---azure-lab-services"></a>クラスの種類の概要 - Azure Lab Services

Azure Lab Services を使用すると、クラウド内にクラスルーム ラボ環境をすばやく設定することができます。 このセクションの記事では、Azure Lab Services を使用していくつかの種類のラボを設定する方法に関するガイダンスを提供します。

## <a name="adobe-creative-cloud"></a>Adobe Creative Cloud

アプリケーションの [Adobe Creative Cloud](https://www.adobe.com/creativecloud.html) コレクションは、デジタル アートおよびメディア クラスでよく使用されています。  

この種類のラボを設定する方法の詳細については、[Adobe Creative Cloud のラボを設定する](class-type-adobe-creative-cloud.md)ことに関する記事を参照してください。

## <a name="arcgis"></a>「ArcGIS」

[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) は、地理情報システム (GIS) の一種です。  ArcGIS Desktop の [ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) などのさまざまなアプリケーションを使用して、2D マップを作成、編集、分析するラボを設定できます。

この種類のラボを設定する方法の詳細については、「[ArcMap\ArcGIS Desktop のラボを設定する](class-type-arcgis.md)」を参照してください。

## <a name="autodesk"></a>Autodesk

[Autodesk](https://www.autodesk.com/) は、アーキテクチャ、エンジニアリング、建設、設計、製造などのソフトウェア ソリューションを提供しています。  これらのソリューションはエンジニアリング クラスおよび [Project Lead the Way](class-type-pltw.md) カリキュラムでよく使用されます。

この種類のラボを設定する方法の詳細については、「[Autodesk](class-type-autodesk.md)」を参照してください。

## <a name="big-data-analytics"></a>ビッグ データ分析

ビッグ データ分析クラスを教えるための GPU ラボを設定できます。 この種類のクラスでは、学生は大量のデータを処理し、機械と統計の学習のアルゴリズムを適用してデータの分析情報を導き出す方法について学習します。 学生の主な目的は、ビッグ データを格納、管理、および処理するためのツールを提供する Apache Hadoop のオープンソース ソフトウェア パッケージなどのデータ分析ツールを使用する方法について学習することです。 

この種類のラボを設定する方法の詳細については、「[Hortonworks Data Platform の Docker デプロイを使用してビッグ データ分析のためのラボを設定する](class-type-big-data-analytics.md)」を参照してください。

## <a name="database-management"></a>データベースの管理

データベースの概念は、大学のほとんどのコンピューター サイエンス部門で教えられる、入門コースの 1 つです。 基本的なデータベース管理のクラスのためのラボを Azure Lab Services で設定することができます。 たとえば、[MySQL](https://www.mysql.com/) Database Server または [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) サーバーを使用した仮想マシン テンプレートをラボで設定することができます。

この種類のラボを設定する方法の詳細については、「[リレーショナル データベースのデータベース管理を教えるためのラボを設定する](class-type-database-management.md)」を参照してください。

## <a name="deep-learning-in-natural-language-processing"></a>自然言語処理でのディープ ラーニング

Azure Lab Services を使用した自然言語処理 (NLP) でのディープ ラーニングに重点を置いたラボを設定できます。 自然言語処理 (NLP) は、コンピューターで翻訳、音声認識、その他の言語認識機能を実行できるようにする、人工知能 (AI) の一種です。 NLP の授業を受けている学生は、Linux 仮想マシン (VM) を使用し、ニューラル ネットワーク アルゴリズムを適用して、書かれた人間の言語を分析するために使用されるディープ ラーニング モデルを開発する方法を学習します。

この種類のラボの設定方法の詳細については、「[Azure Lab Services を使用した自然言語処理でのディープ ラーニングに重点を置いたラボを設定する](class-type-deep-learning-natural-language-processing.md)」を参照してください。

## <a name="ethical-hacking-with-hyper-v"></a>Hyper-V を使用した倫理的ハッキング

エシカル ハッキングのフォレンジクス面に焦点を当てたクラスのためのラボを設定できます。 侵入テストは、悪意のある攻撃者が利用する可能性のある脆弱性を実証するためにシステムまたはネットワークへの侵入を試みる訓練で、エシカル ハッキング コミュニティで使用します。

エシカル ハッキング クラスの学生は、脆弱性に対する防御のための最新の手法を学習できます。 各学生は、2 つの入れ子になった仮想マシンを持つ Windows Server ホスト仮想マシンを用意し、一方の仮想マシンは [Metasploitable3](https://github.com/rapid7/metasploitable3) イメージ、もう一方のマシンは [Kali Linux](https://www.kali.org/) イメージを使用します。 Metasploitable 仮想マシンは悪用目的に使用します。  Kali Linux 仮想マシンでは、フォレンジック タスクの実行に必要なツールを利用できます。

この種類のラボを設定する方法の詳細については、「[エシカル ハッキング クラスを教えるためのラボを設定する](class-type-ethical-hacking.md)」を参照してください。

## <a name="ethical-hacking-with-virtualbox"></a>VirtualBox を使用した倫理的ハッキング

エシカル ハッキングのフォレンジクス面に焦点を当てたクラスのためのラボを設定できます。 侵入テストは、悪意のある攻撃者が利用する可能性のある脆弱性を実証するためにシステムまたはネットワークへの侵入を試みる訓練で、エシカル ハッキング コミュニティで使用します。

エシカル ハッキング クラスの学生は、脆弱性に対する防御のための最新の手法を学習できます。 各学生は、2 つの入れ子になった仮想マシンを含む Windows Server ホスト仮想マシンを用意し、一方の仮想マシンは [SEED Labs](https://seedsecuritylabs.org/) イメージ、もう一方のマシンは [Kali Linux](https://www.kali.org/) イメージを使用します。 SEED 仮想マシンは悪用目的に使用します。  Kali Linux 仮想マシンでは、フォレンジック タスクの実行に必要なツールを利用できます。

この種類のラボを設定する方法の詳細については、「[エシカル ハッキング クラスを教えるためのラボを設定する](class-type-ethical-hacking-virtualbox.md)」を参照してください。

## <a name="matlab"></a>MATLAB

[MATLAB](https://www.mathworks.com/) は、Matrix laboratory の略で、[MathWorks](https://www.mathworks.com/products/matlab.html) のプログラミング プラットフォームです。  計算能力と視覚化を組み合わせたツールで、数学、エンジニアリング、物理、および化学の分野でよく利用されています。

この種類のラボを設定する方法の詳細については、「[MATLAB を教えるためのラボを設定する](class-type-matlab.md)」を参照してください。

## <a name="networking-with-gns3"></a>GNS3 を使用したネットワーク

学生が [GNS3](https://www.gns3.com/) ソフトウェアを使用して仮想および実際のネットワークをエミュレート、構成、テスト、トラブルシューティングできるようにすることに焦点を合わせたクラス用のラボを設定できます。 

この種類のラボを設定する方法の詳細については、「[ネットワーク クラスの授業を行うためのラボを設定する](class-type-networking-gns3.md)」を参照してください。

## <a name="project-lead-the-way-pltw"></a>Project Lead the Way (PLTW)

[Project Lead the Way (PLTW)](https://www.pltw.org/) は、コンピューター サイエンス、エンジニアリング、バイオメディカル サイエンスの PreK-12 カリキュラムを米国全域に提供する非営利団体です。  各 PLTW クラスでは、学生は実践的な学習体験の一環としてさまざまなソフトウェア アプリケーションを使用します。

これらの種類のラボを設定する方法の詳細については、「[Project Lead the Way クラスでラボを設定する](class-type-pltw.md)」を参照してください。

## <a name="python-and-jupyter-notebooks"></a>Python と Jupyter Notebook

[Jupyter Notebooks](http://jupyter-notebook.readthedocs.io) を使用する方法を学生に教えるために必要なツールを使用して、Azure Lab Services でテンプレート マシンを設定することができます。 Jupyter Notebooks は、ノートブックと呼ばれる単一のキャンバス上でリッチ テキストと実行可能な [Python](https://www.python.org/) ソース コードを簡単に組み合わせることができるオープンソース プロジェクトです。 ノートブックを実行すると、入力と出力の線形レコードが生成されます。  これらの出力には、テキスト、情報テーブル、散布図などを含めることができます。

この種類のラボを設定する方法の詳細については、「[Python と Jupyter Notebook を使用してデータ サイエンスを教えるためのラボを設定する](class-type-jupyter-notebook.md)」を参照してください。

## <a name="react"></a>React

[React](https://reactjs.org/) は、ユーザー インターフェイス (UI) を構築するための一般的な JavaScript ライブラリです。 React は、Web サイト用に再利用可能なコンポーネントを作成するための宣言型の方法です。  JavaScript ベースのフロントエンド開発には、多くの一般的なライブラリがあります。  ラボを作成する際に、これらのライブラリのいくつかを使用します。  [Redux](https://redux.js.org/) は、JavaScript アプリ用の予測可能な状態コンテナーを提供するライブラリであり、多くの場合、React を補完するために使用されます。 [JSX](https://reactjs.org/docs/introducing-jsx.html) は、UI の外観を記述するために React でよく使用される、JavaScript のライブラリ構文拡張機能です。  [NodeJS](https://nodejs.org/) は、React アプリケーション用の Web サーバーを実行する便利な方法です。

[Visual Studio Code](https://code.visualstudio.com/) を使用して、お使いの開発環境に合わせて Linux 上のこの種類のラボを設定する方法の詳細については、「[Windows 上の React のラボを設定する](class-type-react-linux.md)」を参照してください。  [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) を使用して、お使いの開発環境に合わせて Windows 上のこの種類のラボを設定する方法の詳細については、「[Windows 上の React のラボを設定する](class-type-react-windows.md)」を参照してください。

## <a name="rstudio"></a>RStudio

[R](https://www.r-project.org/about.html) は、統計に関するコンピューティングとグラフィックスで使用されるオープン ソースの言語です。  これは、遺伝学の統計分析、自然言語処理、財務データの分析などで使用されます。  R には、[対話型のコマンドライン](https://cran.r-project.org/doc/manuals/r-release/R-intro.html#Invoking-R-from-the-command-line) エクスペリエンスが用意されています。  [RStudio](https://www.rstudio.com/products/rstudio/) は、R 言語で使用できる対話型開発環境 (IDE) です。  無料版には、コード編集ツール、統合されたデバッグ エクスペリエンス、パッケージ開発ツールが用意されています。  このクラスの種類では、RStudio と R の、統計的コンピューティングを使用する必要があるクラスの構成要素としての側面のみを取り上げます。

この種類のラボを設定する方法の詳細については、「[Linux で R を教えるためのラボを設定](class-type-rstudio-linux.md)」または「[Windows で R を教えるためにラボを設定する](class-type-rstudio-windows.md)」を参照してください。

## <a name="shell-scripting-on-linux"></a>Linux でのシェル スクリプト

Linux でのシェル スクリプトの作成を教えるためのラボを設定できます。 スクリプトの作成は、管理者がタスクを繰り返さなくて済むようにできる、システム管理の役に立つ部分です。 このサンプル シナリオのクラスでは、従来の bash スクリプトと拡張スクリプトについて説明します。 拡張スクリプトは、bash コマンドと Ruby を組み合わせたスクリプトです。 このアプローチにより、Ruby でデータを渡すことができ、bash コマンドでシェルと対話することができます。

これらのスクリプト作成のクラスを受講する学生は、Linux 仮想マシンを使用して、Linux の基本を学習し、bash シェル スクリプトの作成にも慣れることができます。 Linux 仮想マシンには、有効になったリモート デスクトップ アクセスが付属しており、[Gedit](https://help.gnome.org/users/gedit/stable/) と [Visual Studio Code](https://code.visualstudio.com/) テキスト エディターがインストールされています。

この種類のラボを設定する方法の詳細については、「[Linux でのシェル スクリプト](class-type-shell-scripting-linux.md)」を参照してください。

## <a name="solidworks-computer-aided-design-cad"></a>SolidWorks のコンピューター支援設計 (CAD)

エンジニアリング系の学生が [SolidWorks](https://www.solidworks.com/) にアクセスできる GPU ラボを設定できます。  SolidWorks には、ソリッド オブジェクトをモデル化するための 3D CAD 環境が用意されています。  SolidWorks を使用すると、エンジニアは設計の作成、視覚化、シミュレーション、ドキュメント化を簡単に行うことができます。

この種類のラボを設定する方法の詳細については、「[SolidWorks を使用するエンジニアリング クラス用にラボを設定する](class-type-solidworks.md)」を参照してください。

## <a name="sql-database-and-management"></a>SQL データベースと管理

構造化照会言語 (SQL) は、データベース内のコンテンツへの追加、アクセス、管理などの、リレーショナル データベース管理のための標準言語です。  [MySQL](https://www.mysql.com/) データベース サーバーと [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) サーバーの両方を使用して、データベースの概念を学習するラボを設定できます。

この種類のラボを設定する方法の詳細については、「[リレーショナル データベースのデータベース管理を教えるためのラボを設定する](class-type-database-management.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

次の記事をご覧ください。

- [Azure Lab Services を使用した自然言語処理でのディープ ラーニングに重点を置いたラボを設定する](class-type-deep-learning-natural-language-processing.md)
- [ネットワーク クラスの授業を行うためのラボを設定する](class-type-networking-gns3.md)
- [Hyper-V でエシカル ハッキング クラスを教えるためのラボを設定する](class-type-ethical-hacking.md)
- [VirtualBox でエシカル ハッキング クラスを教えるためのラボを設定する](class-type-ethical-hacking-virtualbox.md)
