---
title: TensorFlow を使用したモデルのトレーニング
titleSuffix: Azure Machine Learning service
description: TensorFlow エスティ メータを利用して TensorFlow モデルの単一ノードや分散トレーニングを実行する方法を説明します
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: d15d3ed115009ad1395a85d36e833d85197d4d19
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094111"
---
# <a name="train-tensorflow-models-with-azure-machine-learning-service"></a>Azure Machine Learning サービスによる TensorFlow モデルのトレーニング

TensorFlow を利用したディープ ニューラル ネットワーク (DNN) トレーニングの場合、Azure Machine Learning には `Estimator` のカスタム `TensorFlow` クラスが用意されています。 Azure SDK の `TensorFlow` エスティメータ ([`tf.estimator.Estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator/Estimator) クラスと融合されない) を利用することで､Azure コンピューティングでの単一ノード実行と分散実行の両方にで TensorFlow トレーニング ジョブを簡単に送信することができます。

## <a name="single-node-training"></a>単一ノードのトレーニング
`TensorFlow` エスティメータによるトレーニングは、[基本 `Estimator`](how-to-train-ml-models.md) を使用する場合と似ているので、最初にハウツー記事を読み、そこで紹介されている概念を理解しておいてください。
  
TensorFlow ジョブを実行するには､`TensorFlow` オブジェクトのインスタンスを作成します。 [コンピューティング ターゲット](how-to-set-up-training-targets.md#amlcompute)オブジェクト`compute_target`は作成済みである必要があります｡

```Python
from azureml.train.dnn import TensorFlow

script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py',
                    conda_packages=['scikit-learn'],
                    use_gpu=True)
```

ここでは、TensorFlow コンストラクターに次のパラメーターを指定します。

パラメーター | 説明
--|--
`source_directory` | トレーニング ジョブに必要なコードのすべてが含まれているローカル ディレクトリ。 このフォルダーは、ローカル コンピューターからリモート コンピューティングにコピーされています
`script_params` | <コマンドライン引数, 値> ペアの形式で、トレーニング スクリプト `entry_script` にコマンドライン引数を指定するディクショナリ
`compute_target` | トレーニング スクリプトの実行に使用するリモートのコンピューティング先 (この例では Azure Machine Learning コンピューティング ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) クラスター)
`entry_script` | リモート コンピューティングで実行するトレーニング スクリプトのファイルパス (`source_directory` を基準にした相対パス)。 このファイル、およびこのファイルと依存関係があるその他のファイルはすべて、このフォルダーに置かれている必要があります
`conda_packages` | トレーニング スクリプトで必要な、conda を使用してインストールする Python パッケージのリスト。 この場合､トレーニング スクリプトはデータの読み込みに `sklearn` を使用しますから、インストールするこのパッケージを指定します｡  コンストラクターには `pip_packages` という名前のパラメーターもあり、必要な pip パッケージに対して使用できます
`use_gpu` | トレーニングに GPU を使用するには、このフラグを `True` に設定します。 既定値は `False` です。

TensorFlow エスティメータを使用するため､トレーニングに使用されるコンテナーには､CPU と GPU でのトレーニングに必要な TensorFlow パッケージと､依存関係にある関連するファイルが既定で含まれます。

次に TensorFlow ジョブを送信します。
```Python
run = exp.submit(tf_est)
```

## <a name="distributed-training"></a>分散トレーニング
TensorFlow エスティメータではまた､Azure VM からなる CPU および GPU クラスターにまたがった大規模にモデルをトレーニングすることもできます。 分散 TensorFlow トレーニングはいくつかの API 呼び出しを使って簡単に行うことができます｡その間､それらワークロードの実行に必要なインフラストラクチャとオーケストレーションはすべて Azure Machine Learning によってバックグランドで管理されます｡

Azure Machine Learning では、TensorFlow における分散トレーニングについて 2 つの方式をサポートしています。
* [Horovod](https://github.com/uber/horovod)フレームワークを利用した MPI ベースの分散トレーニング
* パラメーター サーバー メソッドを利用したネイティブの[分散 TensorFlow](https://www.tensorflow.org/deploy/distributed)

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) は、Uber によって開発された分散トレーニングのためのオープン ソースの ring-allreduce フレームワークです。

Horovod フレームワークを使用して分散 TensorFlow を実行するには、次のように TensorFlow オブジェクトを作成します。

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi',
                    use_gpu=True)
```

上記のコードには、TensorFlow コンストラクターに対する次の新しいパラメーターがあります｡

パラメーター | 説明 | 既定値
--|--|--
`node_count` | トレーニング ジョブに使用するノードの数。 | `1`
`process_count_per_node` | 各ノードで実行するプロセス (つまり "worker") の数。|`1`
`distributed_backend` | 分散トレーニングを起動するためのバックエンド。Estimator によって MPI 経由で提供されます。 MPI (および Horovod) を使用して並列または分散トレーニングを実行したい場合 (たとえば、`node_count` > 1 か `process_count_per_node` > 1、またはその両方)、`distributed_backend='mpi'` を設定します。 Azure Machine Learning で使用されている MPI 実装は、[Open MPI](https://www.open-mpi.org/) です。 | `None`

上記の例では､2 つのワーカー (ノード 1 つにワーカー 1 つ) を使って分散トレーニングが実行されます。

Horovod と依存関係にあるファイルは自動的にインストールされますから､単に次のようにしてトレーニングスクリプト `train.py` にインポートすればよいだけです｡

```Python
import tensorflow as tf
import horovod
```

最後に、TensorFlow ジョブを送信します。
```Python
run = exp.submit(tf_est)
```

### <a name="parameter-server"></a>パラメーター サーバー
[ネイティブの分散 TensorFlow](https://www.tensorflow.org/deploy/distributed) を実行することもできます｡その場合は､パラメーター サーバー モデルを使用します。 この方法では、パラメーター サーバーとワーカーからなるクラスター全体でトレーニングが行われます。 トレーニング中､ワーカーがグラディエントの計算する一方､パラメーター サーバーはグラディエントを集計します｡

TensorFlow オブジェクトを作成します。

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps',
                    use_gpu=True)
```

上記コードでの TensorFlow コンストラクターに対する次のパラメーターに注意してください。

パラメーター | 説明 | 既定値
--|--|--
`worker_count` | worker 数。 | `1`
`parameter_server_count` | パラメーター サーバー数。 | `1`
`distributed_backend` | 分散トレーニングに使用するバックエンド。 パラメーター サーバー経由で分散トレーニングを実行するには、`distributed_backend='ps'` を設定します | `None`

#### <a name="note-on-tfconfig"></a>`TF_CONFIG` に関する注
[ `tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec) には､クラスタのネットワーク アドレスとポートが必要になるため、Azure Machine Learning によって自動的に `TF_CONFIG` 環境変数が設定されます。

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

TensorFlow の上位 [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API の場合、TensorFlow はこの `TF_CONFIG` 変数を自動的に解析して、自動的にクラスターの仕様をビルドします。 

これに対し TensorFlow の下位のコア API の場合は、ユーザーは自分で `TF_CONFIG` 変数を解析して､自分でトレーニング コード用の `tf.train.ClusterSpec` をビルドする必要があります｡ このためには､[この例](https://aka.ms/aml-notebook-tf-ps)の**トレーニング スクリプト**で次のようにします。

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

トレーニングのスクリプトの作成と TensorFlow オブジェクトの作成を終えると、トレーニング ジョブを送信できます｡
```Python
run = exp.submit(tf_est)
```

## <a name="examples"></a>例

分散型深層学習のノートブックについては、次のページを参照してください。
* [how-to-use-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次の手順
* [トレーニング中に実行メトリクスを追跡する](how-to-track-experiments.md)
* [ハイパーパラメーターを調整する](how-to-tune-hyperparameters.md)
* [トレーニング済みモデルをデプロイする](how-to-deploy-and-where.md)
