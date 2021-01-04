---
title: Azure Notebooks プレビューの概要
description: 無料の Azure Notebooks プレビューサービスを使用して、クラウド上で Jupyter Notebook を実行します。セットアップも構成も不要です。
ms.topic: overview
ms.date: 04/05/2019
ms.openlocfilehash: 7e622bdef785085384395bb0c8ac3efba2b2053a
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400271"
---
# <a name="overview-of-azure-notebooks-preview"></a>Azure Notebooks プレビューの概要

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Azure Notebooks は、インストールせずにクラウド上で Jupyter Notebook を作成、実行するための無料でホストされているサービスです。 [Jupyter](https://jupyter.org/) (旧称 IPython) は、マークダウン テキスト、実行可能コード、永続データ、グラフィックス、およびグラフを組み合わせて "*ノートブック*" (画像提供: jupyter.org) と呼ばれる 1 つの共有可能なキャンバスを簡単に作成できるオープン ソース プロジェクトです。

[![Jupyter Notebook の例](https://jupyter.org/assets/jupyterpreview.png)](https://jupyter.org/assets/jupyterpreview.png#lightbox)

コード、グラフィックス、および説明テキストのこの強力な組み合わせにより、Jupyter は、データ サイエンス教育、データのクリーニングと変換、数値シミュレーション、統計モデリング、機械学習モデルの開発などを行う多数のユーザーの間で一般に使用されるようになっています。

## <a name="hassle-free-experience"></a>手間のかからないエクスペリエンス

Azure Notebooks の使用は、プロトタイプ作成、データ サイエンス、学術研究、または Python のプログラミング学習をすばやく開始するのに役立ちます。

- データ サイエンティストは、インストールなしで Anaconda 環境全体にすぐにアクセスできます。
- 教師は、手間のかからない Python 環境を学生に提供できます。
- プレゼンターは、出席者に 45 分かけてソフトウェアをインストールするように依頼することなく、ウェビナーなどを実施できます。
- 開発者や愛好家は、コードをすぐに記述するためのスクラッチパッドとして Notebooks を使用できます。

ユーザーが Azure Notebooks (プレビュー段階) などのブラウザーでアクセスできるクラウド サービスを通して共同作業を行うと、ノートブックの力がさらに発揮されます。 クラウドでは、ユーザーは、Jupyter をローカルにインストールする必要も、環境の管理を意識する必要もありません。 さらに、クラウドでは、他の承認されたユーザーとノートブック (および関連付けられたデータ ファイル) を簡単に共有でき、ソース管理リポジトリなどの外部手段を使用したノートブックの共有の複雑さを回避できます。 ユーザーは、Azure Notebooks を使用して、改変や実験のためにノートブックを自分のアカウントにコピー (複製) することもできます。これは教育が目的の場合に特に有用です。

Azure Notebooks はコードを作成、実行、および共有するための汎用プラットフォームであるため、多くのさまざまなシナリオで使用できます。

- 新しいプログラミング言語を学習する – [入門チュートリアル](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb)のいずれかをお試しください。
- データ サイエンスを学習する – [Jake VanderPlas の書籍](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook)をご覧ください。
- 何百人もの受講者に[コースを提供する](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas)
- インストール時間を費やすことなく、オンラインまたは会議でウェビナーを実施する 
- [GitHub 起動バッジを作成](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge)することで、GitHub ユーザーがノートブックを直接読み込んで実行できるようにする
- スライド内のコードが実行可能であるときに [PowerPoint のようなスライドショー](https://notebooks.azure.com/help/jupyter-notebooks/slides)を表示する

つまり、Azure Notebooks を使用すれば、作業をさらに効率的に進めることができ、その結果、さらに多くの成果を上げるのに役立ちます。

> [!Note]
> Jupyter 自体の詳細については、[jupyter.org](https://jupyter.org/)、および [Jupyter のドキュメント](https://jupyter-notebook.readthedocs.io/en/latest/)で確認できます。

## <a name="pricing-and-quotas"></a>価格とクォータ

Azure Notebooks は無料のサービスですが、乱用を防ぐために各プロジェクトのメモリは 4 GB、データは 1 GB に制限されています。 これらの制限を超える正当なユーザーは、CAPTCHA チャレンジをクリアしてノートブックの実行を継続してください。

すべての制限を解除するには、Azure Active Directory を使用したアカウント (会社のアカウントなど) で Azure Notebooks にサインインします。 そのアカウントが Azure サブスクリプションに関連付けられている場合は、そのサブスクリプション内の任意の Azure Data Science Virtual Machine インスタンスに接続できます。 

Notebook サーバーは、最大で 8 時間存在することが保証されます。 ほとんどの場合、コンテナーはこの制限を受けず、この期間を超えて実行を続けます。ただし、システムの安定性のために、有効期間の長いセッションがシャットダウンされることがあります。

## <a name="available-kernels-and-environments"></a>利用可能なカーネルと環境

各ノートブックでは、コード セルを実行するために使用されるカーネル (つまり、ランタイム環境) を選択します。 Azure Notebooks では、次のカーネルをサポートしています。

- Python 2.7 + Anaconda2-5.3.0
- Python 3.6 + Anaconda3-5.3.0
- Python 3.5 + Anaconda3-4.2.0 (非推奨になる予定)
- R 3.4.1 + Microsoft R Open 3.4.1
- F# 4.1.9

Azure Notebooks には、基本ディストリビューションの他に、追加のパッケージも含まれています。 たとえば、Python カーネルには、numpy、pandas、scikit-learn、matplotlib、および bokeh ライブラリが含まれています。

プロジェクトをカスタマイズして、そのプロジェクトに含まれるすべてのノートブック用の環境を作成することもできます。

基本ディストリビューションに加え、Azure Notebooks には、データ サイエンティストにとって有用な多くのパッケージが事前インストールされています。 各言語の一般的なプロセスを使用して、ご自分のパッケージをインストールすることもできます。

## <a name="pre-configured-jupyter-extensions"></a>事前構成済みの Jupyter の拡張機能

Azure Notebooks では、次の Jupyter の拡張機能が事前に構成されています。

- [RISE](https://github.com/damianavila/RISE): Jupyter のスライド ショー拡張機能 (live_reveal とも呼ばれます)。
- [JupyterLab](https://github.com/jupyterlab/jupyterlab): Jupyter Notebook を操作するための完全なコンピューティング環境。
- [Altair](https://github.com/ellisonbg/altair): Python 用の宣言型統計可視化ライブラリです。
- [BQPlot](https://github.com/bloomberg/bqplot): Jupyter Notebook 用の対話型プロット フレームワーク。
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets): Jupyter Notebook 用の対話型 HTML ウィジェット。

## <a name="issues-and-getting-help"></a>問題とヘルプの取得

Azure Notebooks はまだプレビュー段階であるため、一時的な障害が他の Azure サービスよりも頻繁に発生したり、長時間続いたりする可能性があります。 不完全な機能やバグが含まれている機能もあります。

現時点では、ビジネスに不可欠なアプリケーションで使用したり、重要性の高いノートブックや機微なデータを処理したりしないことをお勧めします。

Azure Notebooks に関する質疑応答を行うには、[GitHub リポジトリ](https://github.com/Microsoft/AzureNotebooks/issues)に問題を送信してください。

## <a name="next-steps"></a>次のステップ  

- クイック スタート:

  - [サインインとユーザー ID の設定](quickstart-sign-in-azure-notebooks.md)
  - [Jupyter Notebook をエクスポートする](quickstart-export-jupyter-notebook-project.md)
