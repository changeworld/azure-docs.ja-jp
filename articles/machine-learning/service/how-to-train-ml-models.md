---
title: Azure Machine Learning で機械学習モデルをトレーニングする
description: Azure Machine Learning サービスを使用して従来の機械学習モデルとディープ ラーニング モデルを単一ノードおよび分散トレーニングする方法を説明します
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 50b808b5769f2160d765ecdb431d71856e651b33
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983584"
---
# <a name="how-to-train-models-with-azure-machine-learning"></a>Azure Machine Learning でモデルをトレーニングする方法

機械学習モデル、特にディープ ニューラル ネットワークをトレーニングすることは、しばしは、時間集約型のタスクであり､またコンピューティング集約型のタスクです。 ローカル コンピューターでトレーニング スクリプトを作成し、少量のデータ セットに対する実行を終えると､ワークロードをスケール アップしたいと思うでしょう｡

トレーニングを容易にするため、Azure Machine Learning の Python SDK は高水準の抽象であるエスティメータ クラスを提供し､このクラスによってユーザーは Azure エコシステムで簡単にモデルをトレーニングできます｡ エスティメータ オブジェクトを作成し､使用することで､単一ノード実行でも､GPU クラスタ全体での分散トレーニングのどちらであれ､リモート コンピューティングで実行するトレーニングを更新できます｡ PyTorch や TensorFlow のジョブの場合､ Azure Machine Learning は、それぞれ専用の PyTorch あらいは TensorFlow エスティメータも提供し､これによりフレームワークを簡単に利用できるようにします｡

## <a name="train-with-an-estimator"></a>エスティ メーターでトレーニングする

[ワークスペース](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspace) を作成し､[開発環境](how-to-configure-environment.md)を準備した後の Azure Machine Learning でのモデルのトレーニングは次の手順で構成されます｡  
1. [リモート コンピューティング ターゲットを作成する](how-to-set-up-training-targets.md)
2. [トレーニング データをアップロードする](how-to-access-data.md)(省略可能)
3. トレーニング スクリプトを作成する
4. エスティメータオブジェクトを作成する
5. トレーニング ジョブを送信する

この記事では、手順 4 と 5 を説明します。 手順 1~3 については、この[チュートリアル](tutorial-train-models-with-aml.md)の例を参照してください｡

### <a name="single-node-training"></a>単一ノードのトレーニング

次のコードは scikit 学習モデルの手順例であり､Azure のリモート コンピューティングで単一ノード トレーニングを実行します｡ [コンピューティング ターゲット](how-to-set-up-training-targets.md#batch)オブジェクト`compute_target`と[データストア](how-to-access-data.md)オブジェクト`ds`は作成済みである必要があります｡

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

上記のコード スニペットは、エスティメータ コンストラクターに次のパラメーターを指定します。
* `source_directory`: トレーニング ジョブに必要なコードのすべてが含まれているローカル ディレクトリ。 このフォルダーは、ローカル コンピューターからリモート コンピューティングにコピーされています｡ 
* `script_params`: <コマンドライン引数,値> の形式でトレーニング スクリプト`entry_script`にコマンドライン引数を指定するディクショナリ｡
* `compute_target`: トレーニング スクリプトの実行に使用するリモート コンピューティング (この例では [Batch AI](how-to-set-up-training-targets.md#batch)クラスター)｡
* `entry_script`: リモート コンピューティングで実行するトレーニングスクリプトのファイルパス (`source_directory` を基準にした相対パス)｡ このファイル、およびこのファイルと依存関係があるその他のファイルはすべて、このフォルダーに置かれている必要があります。
* `conda_packages`: トレーニング スクリプトが必要とする conda を使用してインストールする Python パッケージの一覧。  
コンストラクターには `pip_packages` という名前のパラメーターもあり､ pip パッケージが必要な場合は､このパラメーターを利用できます。

エスティメータオブジェクトを作成すると、[実験](concept-azure-machine-learning-architecture.md#experiment)オブジェクトで `submit` 関数を呼び出すことでリモート コンピューティングで実行するトレーニング ジョブを送信することができます`experiment`。 

```Python
run = experiment.submit(sk_est)
print(run.get_details().status)
```

> [!IMPORTANT]
> **特殊フォルダー** *outputs* および *logs* の 2 つのフォルダーは、Azure Machine Learning によって特別に扱われます｡ トレーニング中に､ルート ディレクトリを基準にした *outputs* と *logs* というディレクトリ (それぞれ `./outputs`と`./logs`) にファイルを書き込んだ場合、それらのファイルは自動的に実行履歴にアップロードされて､実行が完了すると、それらファイルにアクセスできるようになります｡ 
>
> トレーニング中に作成される成果物 (モデル ファイル、チェックポイント、データ ファイル、プロット画像など)をアクセスする場合は､ `./outputs` フォルダーにそれら成果物を書き込みます｡
>
> 同様に、トレーニング実行のログを `./logs` フォルダーに書き込むこともできます。 Azure Machine Learning の [ TensorBoard 統合](https://aka.ms/aml-notebook-tb)を利用するには､このフォルダーに TensorBoard のログを書き込むようにします｡ 実行中に、TensorBoard を起動して、それらログをストリーミングできます｡  過去のどの実行についても､後でログを復元することができます。
>
> たとえば､リモート トレーニングの実行後に *outputs*フォルダーに書き込まれたファイルをローカル コンピューターには､次のコードを使用します。 `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>分散トレーニングとカスタム Docker イメージ

エスティメータを使用して実行できるトレーニング シナリオは他にも 2 つあります｡
1. カスタム Docker イメージを使用する
2. マルチノード クラスターで分散トレーニングする

次のコードでは、CNTK モデルに対して分散トレーニングを実行する方法を示しています。 また､既定の Azure Machine Learning イメージを使わずに、専用のカスタム Docker イメージを利用することもできます｡

[コンピューティング ターゲット](how-to-set-up-training-targets.md#batch)オブジェクト`compute_target`は作成済みである必要があります｡ エスティメータは次のようにして作成できます。

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

上記のコードでは、エスティメータ コンストラクターに対する次の新しいパラメーターを示しています｡
* `custom_docker_base_image`: 使用するイメージの名前｡ パブリックな Docker リポジトリ (このケースは Docker Hub) にあるイメージのみ指定できます｡ プライベートな docker リポジトリにあるイメージを使用するには、コンストラクターの `environment_definition` パラメーターを利用します｡
* `node_count`: トレーニング ジョブに使用するノード数。 この引数の既定値は `1` です｡
* `process_count_per_node`: 各ノードで実行するプロセス (または「ワーカー」) 数。 この場合は、ノード 1 つあたり `2` 個の GPU を使用します｡ この引数の既定値は `1` です｡
* `distributed_backend`: 分散トレーニングを起動するためのバックエンド｡このバックエンドは､エスティメータによって MPI 経由で提供されます｡ この引数の既定値は `None` です｡ 並列または分散トレーニングを実行したい場合 (例えば `node_count` > 1 か`process_count_per_node` > 1､またはその両方) ､`distributed_backend='mpi'` を設定します。 AML が使用する MPI 実装は [Open MPI](https://www.open-mpi.org/) です｡

最後に、トレーニング ジョブを送信します。
```Python
run = experiment.submit(cntk_est)
```

## <a name="examples"></a>例
Sklearn のモデルのトレーニングについては､このチュートリアルを参照してください。
* `tutorials/01.train-models.ipynb`

カスタム docker を使用した分散 CNTK については､このチュートリアルを参照してください。
* `training/06.distributed-cntk-with-custom-docker/06.distributed-cntk-with-custom-docker.ipynb`

これらの notebook を入手してください｡

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次の手順

* [トレーニング中に実行メトリクスを追跡する](how-to-track-experiments.md)
* [PyTorch モデルをトレーニングする](how-to-train-pytorch.md)
* [TensorFlow モデルをトレーニングする](how-to-train-tensorflow.md)
* [ハイパーパラメーターを調整する](how-to-tune-hyperparameters.md)
* [トレーニング済みモデルをデプロイする](how-to-deploy-and-where.md)
