---
title: 機械学習製品オプションの比較
titleSuffix: Microsoft
description: 機械学習モデルを構築、デプロイ、および管理するための Microsoft のさまざまな製品を比較します。 ソリューションとしてどの製品を選択するかを決定します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 12/04/2018
ms.openlocfilehash: b2d7888234a05c82f1245bd58b98f505989d7f2d
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557888"
---
# <a name="what-are-the-machine-learning-products-at-microsoft"></a>Microsoft の機械学習製品とは

Microsoft は、機械学習モデルを構築、デプロイ、および管理するためのさまざまな製品オプションを提供しています。 これらの製品を比較し、機械学習ソリューションを最も効率的に開発するために必要なものを選択します。

| 機械学習製品 | 説明 | この製品でできること |
|-|-|-|
| "***クラウド内***" | | |
| [Azure Machine Learning サービス](#azure-machine-learning-services) | ML 用のマネージド クラウド サービス  | Python と CLI を使用して Azure でモデルをトレーニング、デプロイ、および管理する |
| [Azure Machine Learning Studio](#azure-machine-learning-studio) | &ndash;ML 用のドラッグ アンド ドロップ式のビジュアル インターフェイス&ndash; | 事前に構成されたアルゴリズムを使用してモデルを構築、実験、およびデプロイする |
| [Azure Databricks](#azure-databricks) | Spark ベースの分析プラットフォーム | モデルとデータ ワークフローを構築およびデプロイする |
| [Azure Cognitive Services](#azure-cognitive-services) | AI および ML モデルが事前に構築された Azure サービス | インテリジェントな機能をアプリに簡単に追加する |
| [Azure Data Science Virtual Machine](#azure-data-science-virtual-machine) | データ サイエンス ツールがプレインストールされた仮想マシン | 事前に構成された環境で ML ソリューションを開発する |
| ***オンプレミスの***<br>"*(クラウド上の仮想マシンでオンプレミスのサーバーを実行することも可能)*" | | |
| [SQL Server Machine Learning サービス](#sql-server-machine-learning-services) | SQL に組み込まれた分析エンジン | SQL Server の内部にモデルを構築およびデプロイする |
| [Microsoft Machine Learning Server](#microsoft-machine-learning-server) | 予測分析のためのスタンドアロンのエンタープライズ サーバー | R と Python でモデルを構築およびデプロイする |
| ***開発者ツール*** | | |
| [ML.NET](#mlnet) | オープンソースのクロスプラットフォーム ML SDK | .NET アプリケーション用の ML ソリューションを開発する |
| [Windows ML](#windows-ml) | Windows 10 ML プラットフォーム | Windows 10 デバイス上のトレーニング済みのモデルを評価する |

## <a name="azure-machine-learning-service"></a>Azure Machine Learning サービス

[Azure Machine Learning サービス](overview-what-is-azure-ml.md) は、ML モデルを大規模にトレーニング、デプロイ、および管理するために使用されるフル マネージド クラウド サービスです。 オープンソース テクノロジを完全にサポートしているため、TensorFlow、PyTorch、scikit-learn などの数万のオープンソース Python パッケージを使用できます。 データの探索と変換、モデルのトレーニングとデプロイを容易にするために、[Azure Notebooks](https://notebooks.azure.com/)、[Jupyter Notebook](http://jupyter.org)、[Azure Machine Learning for Visual Studio Code](https://aka.ms/vscodetoolsforai) などの豊富なツールも利用できます。 Azure Machine Learning サービスには、モデルの生成とチューニングを簡単に、効率よく、かつ正確に自動化する機能が含まれています。

Azure Machine Learning サービスは、Python と CLI を使用して ML モデルをクラウド規模でトレーニング、デプロイ、および管理する場合に使用します。

[無料版または有料版の Azure Machine Learning service](http://aka.ms/AMLFree) を今日からお試しいただけます。

## <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio

[Azure Machine Learning Studio](../studio/what-is-ml-studio.md) は、事前に構築された機械学習アルゴリズムを使用して、モデルを簡単かつ迅速に構築、テスト、およびデプロイするために使用できる対話型のビジュアル ワークスペースを提供します。 Machine Learning Studio でモデルを Web サービスとして公開すれば、カスタム アプリや BI ツール (Excel など) からそのモデルを簡単に利用することができます。
プログラミングは必要ありません。対話型のキャンバス上のデータセットと分析モジュールを接続することによって機械学習モデルを構成した後、それを数回のクリックでデプロイします。

Machine Learning Studio は、コードを必要とすることなくモデルを開発およびデプロイする場合に使用します。

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/azure-databricks/what-is-azure-databricks) は、Microsoft Azure クラウド サービス プラットフォーム用に最適化された Apache Spark ベースの分析プラットフォームです。 Databricks は、ワンクリックでのセットアップ、効率化されたワークフロー、およびデータ サイエンティスト、データ エンジニア、ビジネス アナリストの間のコラボレーションを可能にする対話型ワークスペースを実現するために、Azure に統合されています。
データのクエリ実行、視覚化、およびモデル化を行うには、Web ベースのノートブックで Python、R、Scala、および SQL コードを使用します。

Databricks は、Apache Spark 上で機械学習ソリューションの構築に関して共同作業する場合に使用します。

## <a name="azure-cognitive-services"></a>Azure Cognitive Services

[Azure Cognitive Services](/azure/cognitive-services/welcome) は、自然なコミュニケーション方法を使用するアプリを構築できるようにする一連の API です。 これらの API を使用すると、数行のコードを書くだけで、見る、聞く、話す、理解する、ユーザーのニーズを解釈することができるアプリを作成できます。 次のようなインテリジェントな機能をアプリに簡単に追加できます。 

- 感情とセンチメントの検出
- 視覚と音声認識
- 言語の理解 (LUIS)
- 知識と検索

Cognitive Services は、デバイスやプラットフォームにまたがるアプリを開発する場合に使用します。 API は、継続的に品質改善され、設定も簡単です。

## <a name="azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine

[Azure Data Science Virtual Machine](../data-science-virtual-machine/overview.md) は、特にデータ サイエンスを実行するために構築された Microsoft Azure クラウド上のカスタマイズされた仮想マシン環境です。 多くのよく使われるデータ サイエンス ツールや他のツールが事前にインストールおよび構成されており、高度な分析のためのインテリジェントなアプリケーションの構築をすぐに始めることができます。

Data Science Virtual Machine は、Azure Machine Learning サービスのターゲットとしてサポートされています。
Windows と Linux Ubuntu の両方のバージョンで使用できます (Linux CentOS では Azure Machine Learning サービスはサポートされていません)。
特定のバージョン情報および含まれている内容の一覧については、[Azure Data Science Virtual Machine の概要](../data-science-virtual-machine/overview.md)に関するページを参照してください。

Data Science VM は、単一のノードでジョブを実行またはホストする必要がある場合や、 1 台のマシンで処理をリモートでスケールアップする必要がある場合に使用します。

## <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning サービス

[SQL Server Microsoft Machine Learning サービス](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)は、SQL Server データベース内のリレーショナル データに対する R と Python での統計分析、データ視覚化、および予測分析を追加します。 Microsoft の R および Python ライブラリには、SQL Server で大規模に、並行して実行できる高度なモデリングおよび ML アルゴリズムが含まれています。

SQL Server Machine Learning サービスは、SQL Server 内のリレーショナル データに対する組み込みの AI および予測分析が必要な場合に使用します。

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

[Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server) は、R プロセスと Python プロセスの並列および分散ワークロードをホストして管理するためのエンタープライズ サーバーです。 Microsoft Machine Learning Server は Linux、Windows、Hadoop、および Apache Spark 上で動作するほか、[HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/) でも使用できます。 これにより、[RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)、[revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)、および [MicrosoftML パッケージ](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)を使用して構築されたソリューション用の実行エンジンが提供されると共に、オープンソースの R と Python が高パフォーマンスの分析、統計分析、機械学習、および非常に大規模なデータセットのサポートで拡張されます。 この機能は、サーバーと共にインストールされる独自のパッケージによって提供されます。 開発用には、[R Tools for Visual Studio](https://www.visualstudio.com/vs/rtvs/) や [Python Tools for Visual Studio](https://www.visualstudio.com/vs/python/) などの IDE を使用することができます。

Microsoft Machine Learning Server は、R と Python で構築されたモデルをサーバー上で構築して運用化するか、あるいは R と Python のトレーニングを Hadoop または Spark クラスター上で大規模に配布する必要がある場合に使用します。

## <a name="mlnet"></a>ML.NET

[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/) は、カスタムの機械学習ソリューションを構築し、それを .NET アプリケーションに統合できるようにする、無料のオープンソースおよびクロスプラットフォームの機械学習フレームワークです。

ML.NET は、機械学習ソリューションを .NET アプリケーションに統合する場合に使用します。

## <a name="windows-ml"></a>Windows ML

[Windows ML](https://docs.microsoft.com/windows/uwp/machine-learning/) を使用すると、アプリケーションでトレーニング済みの機械学習モデルを使用して、トレーニング済みのモデルを Windows 10 デバイス上でローカルで評価できます。

Windows ML は、Windows アプリケーション内でトレーニング済みの機械学習モデルを使用する場合に使用します。

## <a name="next-steps"></a>次の手順

- Microsoft から入手できるすべての人工知能 (AI) 開発製品の詳細については、「[Microsoft AI プラットフォーム](https://www.microsoft.com/ai)」を参照してください。
- AI ソリューションを開発する方法のトレーニングについては、「[Microsoft AI スクール](https://aischool.microsoft.com/learning-paths)」を参照してください。
