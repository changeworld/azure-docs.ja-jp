---
title: Estimator を使用して ML モデルをトレーニングする
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service の Estimator クラスを使用して従来の機械学習モデルとディープ ラーニング モデルを単一ノードおよび分散トレーニングする方法を説明します
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 0ebb12df835cf1c32e02419989b21684e9884c18
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184357"
---
# <a name="train-models-with-azure-machine-learning"></a>Azure Machine Learning を使用してモデルをトレーニングする

機械学習モデル、特にディープ ニューラル ネットワークをトレーニングすることは、しばしは、時間集約型のタスクであり､またコンピューティング集約型のタスクです。 ローカル コンピューターでトレーニング スクリプトを作成し、少量のデータ セットに対する実行を終えると､ワークロードをスケール アップしたいと思うでしょう｡

トレーニングを支援するため、Azure Machine Learning の Python SDK は高水準の抽象である Estimator クラスを提供し、このクラスによってユーザーは Azure エコシステムで簡単にモデルをトレーニングできます。 [`Estimator` オブジェクト](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)を作成し、使用することで、単一ノード実行でも、GPU クラスター全体での分散トレーニングでも、リモート コンピューティングで実行するトレーニング コードを送信できます。 PyTorch や TensorFlow のジョブの場合、Azure Machine Learning では、それぞれカスタムの `PyTorch` Estimator および `TensorFlow` Estimator も提供され、これらのフレームワークを簡単に使用できるようにします。

## <a name="train-with-an-estimator"></a>Estimator でトレーニングする

[ワークスペース](concept-azure-machine-learning-architecture.md#workspace)を作成し、[開発環境](how-to-configure-environment.md)を設定したら、Azure Machine Learning でのモデルのトレーニングは次の手順で構成されます。  
1. [リモート コンピューティング ターゲット](how-to-set-up-training-targets.md)を作成する
2. [トレーニング データ](how-to-access-data.md)をアップロードする (省略可能)
3. [トレーニング スクリプト](tutorial-train-models-with-aml.md#create-a-training-script)を作成する
4. `Estimator` オブジェクトを作成する
5. トレーニング ジョブを送信する

この記事では、手順 4 と 5 を説明します。 手順 1 から 3 については、[モデルのトレーニングに関するチュートリアル](tutorial-train-models-with-aml.md)の例を参照してください。

### <a name="single-node-training"></a>単一ノードのトレーニング

scikit 学習モデルについては、Azure のリモート コンピューティングで実行される単一ノード トレーニングに `Estimator` を使用します。 [コンピューティング ターゲット](how-to-set-up-training-targets.md#amlcompute) オブジェクト `compute_target` および[データストア](how-to-access-data.md) オブジェクト `ds` は、既に作成されている必要があります。

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

このコード スニペットは、`Estimator` コンストラクターに次のパラメーターを指定します。

パラメーター | 説明
--|--
`source_directory`| トレーニング ジョブに必要なコードのすべてが含まれているローカル ディレクトリ。 このフォルダーは、ローカル コンピューターからリモート コンピューティングにコピーされています 
`script_params`| <コマンドライン引数, 値> ペアの形式で、トレーニング スクリプト `entry_script` にコマンドライン引数を指定するディクショナリ
`compute_target`| トレーニング スクリプトの実行に使用するリモートのコンピューティング先 (この例では Azure Machine Learning コンピューティング ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) クラスター)
`entry_script`| リモート コンピューティングで実行するトレーニング スクリプトのファイルパス (`source_directory` を基準にした相対パス)。 このファイル、およびこのファイルと依存関係があるその他のファイルはすべて、このフォルダーに置かれている必要があります
`conda_packages`| トレーニング スクリプトで必要な、conda を使用してインストールする Python パッケージのリスト。  
コンストラクターには `pip_packages` という名前の別のパラメーターもあり、必要な pip パッケージに対して使用します

ご自分の `Estimator` オブジェクトを作成すると、[実験](concept-azure-machine-learning-architecture.md#experiment)オブジェクト `experiment` で `submit` 関数を呼び出すことで、リモート コンピューティングで実行するトレーニング ジョブを送信します。 

```Python
run = experiment.submit(sk_est)
print(run.get_details().status)
```

> [!IMPORTANT]
> **特殊フォルダー** *outputs* および *logs* の 2 つのフォルダーは、Azure Machine Learning によって特別に扱われます｡ トレーニング中に、ルート ディレクトリを基準にした *outputs* と *logs* というフォルダー (それぞれ `./outputs` と `./logs`) にファイルを書き込んだ場合、それらのファイルは自動的に実行履歴にアップロードされて、実行が完了すると、それらのファイルにアクセスできるようになります。
>
> トレーニング中に成果物 (モデル ファイル、チェックポイント、データ ファイル、プロット画像など) を作成するには、`./outputs` フォルダーにそれら成果物を書き込みます。
>
> 同様に、トレーニング実行のログを `./logs` フォルダーに書き込みます。 Azure Machine Learning の [ TensorBoard 統合](https://aka.ms/aml-notebook-tb)を利用するには､このフォルダーに TensorBoard のログを書き込むようにします｡ 実行中に、TensorBoard を起動して、それらログをストリーミングできます｡  過去のどの実行についても､後でログを復元することができます。
>
> たとえば､リモート トレーニングの実行後に *outputs*フォルダーに書き込まれたファイルをローカル コンピューターには､次のコードを使用します。 `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>分散トレーニングとカスタム Docker イメージ

`Estimator` を使用して実行できるトレーニング シナリオは他にも 2 つあります。
* カスタム Docker イメージを使用する
* マルチノード クラスターで分散トレーニングする

次のコードでは、CNTK モデルに対して分散トレーニングを実行する方法を示しています。 また、既定の Azure Machine Learning イメージを使わずに、独自のカスタム Docker イメージを使用すると想定します。

[コンピューティング ターゲット](how-to-set-up-training-targets.md#amlcompute)オブジェクト`compute_target`は作成済みである必要があります｡ Estimator は次のようにして作成できます。

```Python
from azureml.train.estimator import Estimator

estimator = Estimator(source_directory='./my-cntk-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',     
                      pip_packages=['cntk==2.5.1'],
                      custom_docker_base_image='microsoft/mmlspark:0.12')
```

上記のコードでは、`Estimator` コンストラクターに対して次の新しいパラメーターを公開しています。

パラメーター | 説明 | 既定値
--|--|--
`custom_docker_base_image`| 使用するイメージの名前。 パブリックな Docker リポジトリ (この場合は Docker Hub) にあるイメージのみを指定します。 プライベートな docker リポジトリにあるイメージを使用するには、コンストラクターの `environment_definition` パラメーターを利用します｡ | `None`
`node_count`| トレーニング ジョブに使用するノードの数。 | `1`
`process_count_per_node`| 各ノードで実行するプロセス (つまり "worker") の数。 この場合は、ノード 1 つあたり `2` 個の GPU を使用します｡| `1`
`distributed_backend`| 分散トレーニングを起動するためのバックエンド。Estimator によって MPI 経由で提供されます。  並列または分散トレーニングを実行するには (例えば `node_count`>1 か `process_count_per_node`>1、またはその両方)､`distributed_backend='mpi'` を設定します。 AML が使用する MPI 実装は [Open MPI](https://www.open-mpi.org/) です｡| `None`

最後に、トレーニング ジョブを送信します。
```Python
run = experiment.submit(cntk_est)
```

## <a name="examples"></a>例
sklearn モデルをトレーニングするノートブックについては、次のページを参照してください。
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

分散型深層学習のノートブックについては、次のページを参照してください。
* [how-to-use-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次の手順

* [トレーニング中に実行メトリクスを追跡する](how-to-track-experiments.md)
* [PyTorch モデルをトレーニングする](how-to-train-pytorch.md)
* [TensorFlow モデルをトレーニングする](how-to-train-tensorflow.md)
* [ハイパーパラメーターを調整する](how-to-tune-hyperparameters.md)
* [トレーニング済みモデルをデプロイする](how-to-deploy-and-where.md)
