---
title: 機械学習パイプラインの構築 - Azure Machine Learning サービス
description: この記事では、Azure Machine Learning SDK for Python を使用して構築できる機械学習パイプラインと、パイプラインを使用する利点について説明します。 機械学習 (ML) パイプラインは、データ サイエンティストが機械学習ワークフローを構築、最適化、管理するために使用します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 09/24/2018
ms.openlocfilehash: 45aa954d2f85267b2c7c9aa2a7ba04e436765433
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50023929"
---
# <a name="pipelines-and-azure-machine-learning"></a>パイプラインと Azure Machine Learning

この記事では、Azure Machine Learning SDK for Python を使用して構築できる機械学習パイプラインと、パイプラインを使用する利点について説明します。

## <a name="what-are-machine-learning-pipelines"></a>機械学習パイプラインとは

機械学習 (ML) パイプラインは、データ サイエンティストが機械学習ワークフローを構築、最適化、管理するために使用します。 一般的なパイプラインには、次の領域に対応する一連のステップが含まれます。

+ データ準備 (正規化や変換など)
+ モデルのトレーニング (ハイパー パラメーターの調整や検証など)
+ モデルのデプロイと評価  

次の図はパイプラインの例を示しています。

[ ![Azure Machine Learning サービスの機械学習パイプライン](./media/concept-ml-pipelines/pipelines.png) ] (./media/concept-ml-pipelines/machine-learning-pipelines-big.png#lightbox)

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Azure Machine Learning を使用してパイプラインを構築する理由

[Azure Machine Learning SDK for Python](#the-python-sdk-for-pipelines) を使用して、ML パイプラインを作成し、個々のパイプライン実行を送信して追跡できます。

パイプラインを使用すると、シンプルさ、速度、移植性、再利用によってワークフローを最適化できます。 Azure Machine Learning を使用してパイプラインを構築すると、インフラストラクチャではなく、熟知している機械学習に集中できます。

複数の異なるステップを使用すると、ワークフローを微調整してテストする際に、必要なステップだけを再実行することができます。 ステップとは、パイプラインにおけるコンピューティング単位です。 上記の図に示すように、データを準備するタスクには、正規化、変換、検証、特徴抽出など、多くのステップが含まれる可能性があります。

パイプラインの設計後、多くの場合、パイプラインのトレーニング ループを中心にさらに微調整が行われます。 パイプラインを再実行すると、再実行する必要があるステップ (更新されたトレーニング スクリプトなど) にジャンプし、変更されていないものはスキップされます。 ステップの実行に使用される変更されていないスクリプトにも同じパラダイムが適用されます。 

Azure Machine Learning では、パイプラインのステップごとに、Microsoft Cognitive Toolkit や TensorFlow などの各種ツールキットやフレームワークを使用できます。 Azure によって、使用するさまざまな[コンピューティング ターゲット](concept-azure-machine-learning-architecture.md)間の調整が行われるため、中間データをダウンストリームのコンピューティング ターゲットと簡単に共有できます。 

## <a name="key-advantages"></a>主な利点

機械学習ワークフローのパイプラインを構築する主な利点を次に示します。

|主な利点|説明|
|:-------:|-----------|
|**無人実行**|複数のステップを無人で確実に並列実行または順次実行するようにスケジュールします。 データの準備とモデリングには数日または数週間かかる可能性があるため、パイプラインの実行中に他のタスクに集中できるようになります。 |
|**混在した多様なコンピューティング**|スケーラブルな異種コンピューティングおよびストレージ間で確実に調整された複数のパイプラインを使用します。 パイプラインの個々のステップをさまざまなコンピューティング ターゲット上 (HDInsight、GPU Data Science VM、Databricks など) で実行することで、利用可能なコンピューティング オプションを効率的に使用できます。|
|**再利用**|パイプラインは、再トレーニングやバッチ スコアリングなどの特定のシナリオ向けにテンプレート化できます。  単純な REST 呼び出しを使用して、外部システムからパイプラインをトリガーできます。|
|**追跡とバージョン管理**|反復処理するデータや結果のパスを手動で追跡するのではなく、パイプライン SDK を使用して、データ ソース、入力、出力に明示的に名前を付けてバージョン管理し、スクリプトとデータを個別に管理することで、生産性を向上させることができます。|

## <a name="the-python-sdk-for-pipelines"></a>パイプライン用 Python SDK

ML パイプラインを作成するには Python を使用します。 Azure Machine Learning SDK では、データ依存関係が存在しない場合に、パイプラインのステップを順次実行および並列実行するための命令型のコンストラクトを提供します。 Jupyter Notebook や別の任意の IDE でそれを操作できます。 

宣言型のデータ依存関係を使用して、タスクを最適化できます。 SDK には、データ転送、コンピューティング ターゲットの作成、モデルの公開などの一般的なタスク用に事前構築されたモジュールのフレームワークが含まれています。 パイプライン間で再利用可能なカスタム ステップを実装することで、このフレームワークを拡張して独自の規則をモデル化できます。

パイプラインはテンプレートとして保存することができ、REST エンドポイントにデプロイできるので、バッチ スコアリング ジョブや再トレーニング ジョブをスケジュールできます。

独自のパイプラインを構築する方法については、[パイプラインに関する Python SDK リファレンス ドキュメント](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)と次のセクションの Notebook を参照してください。

## <a name="example-notebooks"></a>サンプルの Notebook
 
ノートブック [pipeline/pipeline-batch-scoring.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/pipeline/pipeline-batch-scoring.ipynb) は、Azure Machine Learning を使用したパイプラインを示しています。
 
このノートブックの入手:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
