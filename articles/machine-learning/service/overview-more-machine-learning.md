---
title: Microsoft が提供するその他の機械学習製品 - Azure Machine Learning | Microsoft Docs
description: Microsoft では、Azure Machine Learning に加えて、機械学習モデルをビルド、デプロイ、管理するためのさまざまな選択肢を用意しています。
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: garyericson, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: overview
ms.date: 04/11/2018
ms.openlocfilehash: 3e36d9202c578294609b01eaf2731b1551ae67af
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282332"
---
# <a name="other-machine-learning-products-and-services-from-microsoft"></a>Microsoft が提供するその他の機械学習製品とサービス

Microsoft では、[Azure Machine Learning](overview-what-is-azure-ml.md) に加えて、機械学習モデルを構築、デプロイ、管理するためのさまざまな選択肢を用意しています。 
* SQL Server Machine Learning サービス
* Microsoft Machine Learning Server
* Azure Data Science Virtual Machine
* HDInsight の Spark MLLib
* Batch AI トレーニング サービス
* Microsoft Cognitive Toolkit (CNTK)
* Azure Cognitive Services


## <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning サービス
[SQL Server の Microsoft Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) を使用すると、R または Python を使用して機械学習モデルを実行、トレーニング、デプロイできます。 オンプレミスおよび SQL Server データベースにあるデータを使用できます。 

Microsoft Machine Learning サービスは、モデルをオンプレミスまたは Microsoft SQL Server 内でトレーニングまたはデプロイする必要がある場合に使用します。 Machine Learning サービスで構築されたモデルは、Azure Machine Learning モデル管理を使用してデプロイできます。 

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server 
[Microsoft Machine Learning Server](https://docs.microsoft.com/en-us/machine-learning-server/what-is-machine-learning-server) は、R プロセスと Python プロセスの並列および分散ワークロードをホストして管理するためのエンタープライズ サーバーです。 Microsoft Machine Learning Server は、Linux、Windows、Hadoop、Apache Spark で動作します。 さらに、[HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/) でも利用できます。 Microsoft Machine Learning Server は、[Microsoft Machine Learning パッケージ](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)を使用してビルドされたソリューションの実行エンジンを提供し、次のシナリオをサポートしてオープン ソースの R と Python を拡張します。

- 高パフォーマンスの分析
- 統計分析
- 機械学習
- 非常に大規模なデータセット

サーバーと共にインストールされる独自のパッケージによって追加機能が提供されます。 開発用には、[R Tools for Visual Studio](https://www.visualstudio.com/vs/rtvs/) や [Python Tools for Visual Studio](https://www.visualstudio.com/vs/python/) などの IDE を使用することができます。

Microsoft Machine Learning Server は、次の操作を行う必要がある場合に使用します。

- R と Python で構築されたモデルを作成してサーバーにデプロイする
- R および Python トレーニングを Hadoop または Spark クラスターに大規模に配布する

## <a name="azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine
[Data Science Virtual Machine](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) は、特にデータ サイエンス用に Microsoft Azure クラウド上に構築された、カスタマイズされた仮想マシン環境です。 多くのよく使われるデータ サイエンス ツールや他のツールが事前にインストールおよび構成されており、高度な分析のためのインテリジェントなアプリケーションの構築をすぐに始めることができます。 Data Science VM は、Windows Server バージョン 2016 と 2012、 Ubuntu 16.04 LTS と OpenLogic CentOS 7.4 ベースのディストリビューション上の Linux VM で利用できます。 

Data Science VM は、単一のノードでジョブを実行またはホストする必要がある場合や、 1 台のマシンで処理をリモートでスケールアップする必要がある場合に使用します。 データ サイエンス仮想マシンは、Azure Machine Learning 実験と Azure Machine Learning モデル管理の両方のターゲットとしてサポートされています。 

## <a name="spark-mllib-in-hdinsight"></a>HDInsight の Spark MLLib
[HDInsight の Spark MLLib](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-ipython-notebook-machine-learning) を使用すると、ビッグ データに対して実行する Spark ジョブの一部としてモデルを作成できます。 Spark を使用すると、データを簡単に変換して準備し、単一のジョブでモデル作成をスケールアウトできます。 Spark MLLib で作成されたモデルは、Azure Machine Learning モデル管理を使用してデプロイ、管理、監視できます。 トレーニングの実行は、Azure Machine Learning 実験を使用してディスパッチおよび管理できます。 Spark は、Machine Learning Workbench で作成したデータ準備ジョブをスケールアウトするためにも使用できます。 

Spark は、データ処理をスケールアウトし、データ パイプラインの一部としてモデルを作成する必要がある場合に使用します。 Spark ジョブは、Scala、Java、Python、または R で作成できます。 

## <a name="batch-ai-training"></a>Batch AI トレーニング 
[Azure Batch AI トレーニング](https://aka.ms/batchaitraining)は、任意のフレームワークを使用して AI モデルと並行して実験を行い、クラスター化された GPU でそれらを大規模にトレーニングするのに役立ちます。 ジョブの要件と実行する構成を記述すると、残りの処理が自動的に実行されます。 

Batch AI トレーニングを使用すると、次のようなフレームワークを使用して、クラスター化された GPU にディープ ラーニング ジョブをスケールアウトできます。

- Cognitive Toolkit
- Caffe
- Chainer
- TensorFlow

Azure Machine Learning モデル管理を使用すると、Batch AI トレーニングのモデルをデプロイ、管理、監視できます。  Batch AI トレーニングは、将来 Azure Machine Learning 実験と統合される予定です。 

## <a name="microsoft-cognitive-toolkit-cntk"></a>Microsoft Cognitive Toolkit (CNTK)
[Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) は、有向グラフの計算ステップとしてニューラル ネットワークを記述する、統合されたディープ ラーニング ツールキットです。 この有向グラフでは、リーフ ノードは入力値またはネットワーク パラメーターを表し、他のノードはその入力に対するマトリックス演算を表します。 Cognitive Toolkit を使用すると、フィード転送 DNN、たたみ込みネット (CNN)、再帰型ネットワーク (RNN/LSTM) などの一般的なモデル タイプを簡単に実現し、組み合わせることができます。 また、複数の GPU とサーバーの間での自動的な差別化および並列化を使用して、確率的勾配降下法 (SGD、誤差逆伝播) 学習を実装できます。

Cognitive Toolkit は、ディープ ラーニングを使用してモデルを構築する場合に使用します。  Cognitive Toolkit は、上記のいずれのサービスでも使用できます。

## <a name="azure-cognitive-services"></a>Azure Cognitive Services
[Azure Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) は、自然なコミュニケーション方法を使用するアプリのビルドを可能にする約 30 個の API のセットです。 これらの API を使用すると、数行のコードを書くだけで、見る、聞く、話す、理解する、ユーザーのニーズを解釈することができるアプリを作成できます。 次のようなインテリジェントな機能をアプリに簡単に追加できます。 

- 感情とセンチメントの検出
- 視覚と音声認識
- 言語の理解 (LUIS)
- 知識と検索

Cognitive Services を使用して、デバイスとプラットフォームを越えてアプリを開発できます。 API は、継続的に品質改善され、設定も簡単です。 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>次の手順

[Azure Machine Learning](overview-what-is-azure-ml.md) の概要をご覧ください。
