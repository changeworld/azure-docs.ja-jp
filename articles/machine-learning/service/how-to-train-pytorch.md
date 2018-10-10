---
title: Azure Machine Learning で PyTorch モデルをトレーニングする
description: PyTorch Estimator を利用して PyTorch モデルの単一ノード トレーニングや分散トレーニングを実行する方法を説明します
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: e569b63f676fb750bcbab88dda6cda39156d41f5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977034"
---
# <a name="how-to-train-pytorch-models"></a>PyTorch モデルをトレーニングする方法

PyTorch を利用したディープ ニューラル ネットワーク (DNN) トレーニングの場合、Azure Machine Learning では Estimator のカスタム PyTorch クラスが提供されます。 Azure SDK の PyTorch Estimator を使用すると、Azure コンピューティングでの単一ノード実行と分散実行の両方に対して、PyTorch トレーニング ジョブを簡単に送信することができます。

## <a name="single-node-training"></a>単一ノードのトレーニング
PyTorch Estimator によるトレーニングは、[ベース Estimator](how-to-train-ml-models.md) の使用と似ているので、最初にハウツー記事を読み、そこで導入されている概念を理解しておいてください。
  
PyTorch ジョブを実行するには、`PyTorch` オブジェクトのインスタンスを作成します。 [コンピューティング ターゲット](how-to-set-up-training-targets.md#batch) オブジェクト `compute_target` および[データストア](how-to-access-data.md) オブジェクト `ds` は、既に作成されている必要があります。

```Python
from azureml.train.dnn import PyTorch

script_params = {
    '--data_dir': ds
}

pt_est = PyTorch(source_directory='./my-pytorch-proj',
                 script_params=script_params,
                 compute_target=compute_target,
                 entry_script='train.py',
                 use_gpu=True)
```

ここでは、PyTorch コンストラクターに次のパラメーターを指定します。
* `source_directory`: トレーニング ジョブに必要なコードのすべてが含まれているローカル ディレクトリ。 このフォルダーは、ローカル コンピューターからリモート コンピューティングにコピーされています
* `script_params`: <コマンドライン引数, 値> の形式で、トレーニング スクリプト `entry_script` にコマンド ライン引数を指定するディクショナリ
* `compute_target`: トレーニング スクリプトの実行に使用するリモート コンピューティング (この例では [Batch AI](how-to-set-up-training-targets.md#batch) クラスター)
* `entry_script`: リモート コンピューティングで実行するトレーニング スクリプトのファイルパス (`source_directory` を基準にした相対パス)。 このファイル、およびこのファイルと依存関係があるその他のファイルはすべて、このフォルダーに置かれている必要があります
* `conda_packages`: トレーニング スクリプトで必要な、conda を使用してインストールする Python パッケージのリスト。
コンストラクターには `pip_packages` という名前のパラメーターもあり、必要な pip パッケージに対して使用できます
* `use_gpu`: トレーニングに GPU を使用するには、このフラグを `True` に設定します。 既定値は `False` です

PyTorch Estimator を使用するため、トレーニングに使用されるコンテナーには、CPU と GPU でのトレーニングに必要な PyTorch パッケージと、依存関係にある関連するファイルが既定で含まれます。

次に、PyTorch ジョブを送信します。
```Python
run = exp.submit(pt_est)
```

## <a name="distributed-training"></a>分散トレーニング
PyTorch Estimator では、Azure VM の CPU および GPU クラスターを使用して大規模にモデルをトレーニングすることもできます。 分散 PyTorch トレーニングはいくつかの API 呼び出しを使って簡単に行うことができます。その間、それらワークロードの実行に必要なインフラストラクチャとオーケストレーションはすべて Azure Machine Learning によってバックグランドで管理されます。

現在、Azure Machine Learning では、Horovod フレームワークを使用する PyTorch の MPI ベースの分散トレーニングがサポートされています。

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) は、Uber によって開発された分散トレーニングのためのオープン ソースの ring-allreduce フレームワークです。

Horovod フレームワークを使用して分散 PyTorch を実行するには、次のように PyTorch オブジェクトを作成します。

```Python
from azureml.train.dnn import PyTorch

pt_est = PyTorch(source_directory='./my-pytorch-project',
                 script_params={},
                 compute_target=compute_target,
                 entry_script='train.py',
                 node_count=2,
                 process_count_per_node=1,
                 distributed_backend='mpi',
                 use_gpu=True)
```

上のコードでは、PyTorch コンストラクターに対して次の新しいパラメーターを公開しています。
* `node_count`: トレーニング ジョブに使用するノードの数。 この引数の既定値は `1` です
* `process_count_per_node`: 各ノードで実行するプロセス (または "ワーカー") の数。 この引数の既定値は `1` です
* `distributed_backend`: 分散トレーニングを起動するためのバックエンド。Estimator によって MPI 経由で提供されます。 この引数の既定値は `None` です。 MPI (および Horovod) を使用して並列または分散トレーニングを実行したい場合 (たとえば、`node_count` > 1 か `process_count_per_node` > 1、またはその両方)、`distributed_backend='mpi'` を設定します。 Azure Machine Learning で使用されている MPI 実装は、[Open MPI](https://www.open-mpi.org/) です。

上記の例では､2 つのワーカー (ノード 1 つにワーカー 1 つ) を使って分散トレーニングが実行されます。

Horovod と依存関係にあるファイルは自動的にインストールされるので、次のようにトレーニング スクリプト `train.py` にインポートするだけで済みます。
```Python
import torch
import horovod
```

最後に、分散 PyTorch ジョブを送信します。
```Python
run = exp.submit(pt_est)
```

## <a name="examples"></a>例
単一ノードの PyTorch トレーニングについては、次のチュートリアルを参照してください。
* `training/01.train-tune-deploy-pytorch/01.train-tune-deploy-pytorch.ipynb`

Horovod を使用した分散 PyTorch については、次のチュートリアルを参照してください。
* `training/02.distributed-pytorch-with-horovod/02.distributed-pytorch-with-horovod.ipynb`

これらの notebook を入手してください。

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次の手順
* [トレーニング中に実行メトリクスを追跡する](how-to-track-experiments.md)
* [ハイパーパラメーターを調整する](how-to-tune-hyperparameters.md)
* [トレーニング済みモデルをデプロイする](how-to-deploy-and-where.md)
