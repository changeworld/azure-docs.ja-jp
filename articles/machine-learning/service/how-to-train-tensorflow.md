---
title: TensorFlow と Keras を使用してモデルをトレーニングする
titleSuffix: Azure Machine Learning service
description: TensorFlow 推定器と Keras 推定器を利用して TensorFlow モデルと Keras モデルの単一ノードトレーニングと分散トレーニングを実行する方法について説明します
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: 82c9aa961221b582bb16438f30a0584232164393
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2019
ms.locfileid: "65915099"
---
# <a name="train-tensorflow-and-keras-models-with-azure-machine-learning-service"></a>Azure Machine Learning サービスによる TensorFlow モデルと Keras モデルのトレーニング

TensorFlow トレーニング ジョブを Azure コンピューティング上で実行するには、Azure Machine Learning SDK の [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 推定器クラスを使用すると簡単です。 `TensorFlow` 推定器は Azure Machine Learning service に対して、ジョブを TensorFlow 対応のコンテナー上で実行してディープ ニューラル ネットワーク (DNN) トレーニングを行うよう指示します。

`TensorFlow` 推定器には実行を抽象化するレイヤーとしての役割もあります。つまり、さまざまなコンピューティング先で実行する場合にパラメーター化実行を簡単に構成できるので、トレーニング スクリプトに変更を加える必要がありません。

## <a name="get-started"></a>作業開始

`TensorFlow` 推定器クラスはベースの [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) と似ているため、まず[ベース推定器の使用方法に関する記事](how-to-train-ml-models.md)を読んで包括的な概念を理解することをお勧めします。

初めて Azure Machine Learning service を使用する場合は、[クイックスタートを完了します](quickstart-run-cloud-notebook.md)。 完了すると、[Azure Machine Learning ワークスペース](concept-workspace.md)と、TensorFlow と Keras を使用した DNN のトレーニング用を含むすべての[サンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml)が作成されます。

## <a name="single-node-training"></a>単一ノードのトレーニング

TensorFlow ジョブを実行するには、[`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) オブジェクトのインスタンスを作成して実験として送信します。

次のコードは、TensorFlow 推定器のインスタンスを作成して実験として送信します。 トレーニング スクリプト `train.py` が、指定のスクリプト パラメーターを使用して実行されます。 このジョブは GPU を有効化した[コンピューティング先](how-to-set-up-training-targets.md)で実行され、scikit-learn が `train.py` の依存関係としてインストールされます。

```Python
from azureml.train.dnn import TensorFlow

# training script parameters passed as command-line arguments
script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

# TensorFlow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    conda_packages=['scikit-learn'],
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

## <a name="distributed-training"></a>分散トレーニング

[`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 推定器は、CPU と GPU のクラスターでの分散トレーニングもサポートしています。 分散 TensorFlow ジョブは簡単に実行でき、Azure Machine Learning service がインフラストラクチャの管理とオーケストレーションを自動的に行います。

Azure Machine Learning service では、TensorFlow での分散トレーニング方法として次の 2 つがサポートされます。

* [MPI ベースの](https://www.open-mpi.org/)分散トレーニング ([Horovod](https://github.com/uber/horovod) フレームワークを使用)
* ネイティブの[分散 TensorFlow](https://www.tensorflow.org/deploy/distributed) (パラメーター サーバー方式を使用)

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) は、分散トレーニング用のオープンソース フレームワークとして Uber が開発したものです。 これを利用すると、分散 GPU TensorFlow ジョブを簡単に実行できます。

次の例は、Horovod を使用して分散トレーニング ジョブを実行するものであり、2 つのワーカーを 2 つのノードに分散しています。

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi', # specifies Horovod backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

Horovod とその依存関係にあるファイルが自動的にインストールされるので、トレーニング スクリプトの中でインポートできます。

```Python
import tensorflow as tf
import horovod
```

### <a name="parameter-server"></a>パラメーター サーバー

[ネイティブの分散 TensorFlow](https://www.tensorflow.org/deploy/distributed) を実行することもできます｡その場合は､パラメーター サーバー モデルを使用します。 この方法では、パラメーター サーバーとワーカーからなるクラスター全体でトレーニングが行われます。 トレーニング中､ワーカーがグラディエントの計算する一方､パラメーター サーバーはグラディエントを集計します｡

次の例は、パラメーター サーバー方式を使用して分散トレーニング ジョブを実行するものであり、4 つのワーカーを 2 つのノードに分散しています。

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps', # specifies parameter server backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="note-on-tfconfig"></a>`TF_CONFIG` に関する注

[`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec) のためのクラスターのネットワーク アドレスとポートも必要になるため、Azure Machine Learning によって `TF_CONFIG` 環境変数が自動的に設定されます。

`TF_CONFIG` 環境変数は JSON 文字列です｡ 以下に､パラメーター サーバー対する変数例を次に示します。

```
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

TensorFlow の高レベル [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) API の場合は、TensorFlow がこの `TF_CONFIG` 変数を解析してクラスターの仕様を自動的にビルドします。

TensorFlow の低レベル コア API をトレーニングに使用する場合は、トレーニング コードの中で自分で `TF_CONFIG` 変数を解析して `tf.train.ClusterSpec` をビルドします。 このためには､[この例](https://aka.ms/aml-notebook-tf-ps)の**トレーニング スクリプト**で次のようにします。

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="keras-support"></a>Keras のサポート

[Keras](https://keras.io/) は、よく利用されている高レベルの DNN Python API であり、TensorFlow、CNTK、Theano をバックエンドとしてサポートします。 TensorFlow をバックエンドとして使用する場合は、`pip_package` コンストラクター パラメーターを組み込むだけで Keras を追加できます。

次の例では、[`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 推定器のインスタンスを作成して実験として送信します。 この推定器は Keras のトレーニング スクリプト `keras_train.py` を実行します。 このジョブは GPU を有効化した[コンピューティング先](how-to-set-up-training-targets.md)で実行され、Keras が依存関係として pip を介してインストールされます。

```Python
from azureml.train.dnn import TensorFlow

keras_est = TensorFlow(source_directory='./my-keras-proj',
                       script_params=script_params,
                       compute_target=compute_target,
                       entry_script='keras_train.py', # relative path to your TensorFlow job
                       pip_packages=['keras'], # add keras through pip
                       use_gpu=True)
```

## <a name="export-to-onnx"></a>ONNX にエクスポートする

[ONNX Runtime](concept-onnx.md) での推論を最適化するために、トレーニングされた TensorFlow モデルを ONNX 形式に変換することができます。 [例](https://github.com/onnx/tensorflow-onnx/blob/master/examples/call_coverter_via_python.py)を参照してください。

## <a name="examples"></a>例

単一ノードと分散型の両方の TensorFlow 実行について、さまざまなフレームワークを使用する実際のコード サンプルが [GitHub ページ](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)にあります。

## <a name="next-steps"></a>次の手順

* [トレーニング中に実行メトリクスを追跡する](how-to-track-experiments.md)
* [ハイパーパラメーターを調整する](how-to-tune-hyperparameters.md)
* [トレーニング済みモデルをデプロイする](how-to-deploy-and-where.md)
