---
title: 分散 GPU トレーニング ガイド
titleSuffix: Azure Machine Learning
description: MPI、Horovod、DeepSpeed、PyTorch、PyTorch Lightning、Hugging Face Transformers、TensorFlow、InfiniBand など、Azure Machine Learning でサポートされているフレームワークで分散トレーニングを実行するためのベスト プラクティスについて説明します。
author: fuhuifang
ms.author: fufang
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 10/21/2021
ms.openlocfilehash: 7c4d85f1f1c2d0c628b3e724f1af0297888504d5
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557283"
---
# <a name="distributed-gpu-training-guide"></a>分散 GPU トレーニング ガイド

Azure Machine Learning (ML) で分散 GPU トレーニング コードを使用する方法の詳細について説明します。 この記事では、分散トレーニングについては説明しません。  これは、既存の分散トレーニング コードを Azure Machine Learning で実行するのに役立ちます。 各フレームワークを使用するためのヒントと例が提供されています。

* メッセージ パッシング インターフェイス (MPI)
    * Horovod
    * DeepSpeed
    * Open MPI からの環境変数
* PyTorch
    * プロセス グループの初期化
    * 起動のオプション
    * DistributedDataParallel (プロセス単位の起動)
    * `torch.distributed.launch` の使用 (ノード単位の起動)
    * PyTorch Lightning
    * Hugging Face Transformers
* TensorFlow
    * TensorFlow の環境変数 (TF_CONFIG)
* InfiniBand による GPU トレーニングの高速化

## <a name="prerequisites"></a>前提条件

"_データ並列処理_"、"_分散データ並列処理_"、"_モデル並列処理_" など、[分散 GPU トレーニングの基本的な概念](concept-distributed-training.md)を確認します。

> [!TIP]
> 使用する並列処理の種類がわからない場合は、90% 超の時間は __分散データ並列処理__ を使用するはずです。

## <a name="mpi"></a>MPI

Azure ML には、各ノードで特定の数のプロセスを起動する [MPI ジョブ](https://www.mcs.anl.gov/research/projects/mpi/)が用意されています。 このアプローチを採用すると、`process_count_per_node` の設定 (ノード単位ランチャーの場合は 1 (既定値)、プロセス単位ランチャーの場合はデバイスまたは GPU の数と等しい値) に応じて、プロセス単位ランチャーまたはノード単位ランチャーを使用して分散トレーニングを実行できます。 Azure ML により、バックグラウンドで完全な MPI 起動コマンド (`mpirun`) が作成されます。  `mpirun` や `DeepSpeed launcher` のように、独自の完全なヘッド ノード ランチャー コマンドを提供することはできません。

> [!TIP]
> Azure Machine Learning MPI ジョブによって使用される基本 Docker イメージに、MPI ライブラリがインストールされている必要があります。 [Open MPI](https://www.open-mpi.org/) は、すべての [AzureML GPU 基本イメージ](https://github.com/Azure/AzureML-Containers)に含まれています。 カスタム Docker イメージを使用する場合は、イメージに MPI ライブラリが含まれていることを自分で確認する必要があります。 Open MPI を使用することをお勧めしますが、Intel MPI などの別の MPI 実装を使用することもできます。 Azure ML では、人気のあるフレームワーク用の[キュレーションされた環境](resource-curated-environments.md)も提供されています。 

MPI を使用して分散トレーニングを実行するには、次の手順のようにします。

1. 好みのディープ ラーニング フレームワークと MPI で Azure ML 環境を使用します。 AzureML から、人気のあるフレームワーク用の[キュレーションされた環境](resource-curated-environments.md)が提供されます。
1. `process_count_per_node` と `node_count` を使用して `MpiConfiguration` を定義します。 `process_count_per_node` は、プロセス単位の起動の場合はノードあたりの GPU の数と等しい値に設定し、ユーザー スクリプトでノードごとにプロセスを起動するノード単位の起動の場合は 1 (既定値) に設定する必要があります。
1. `MpiConfiguration` オブジェクトを `ScriptRunConfig` の `distributed_job_config` パラメーターに渡します。

```python
from azureml.core import Workspace, ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import MpiConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = MpiConfiguration(process_count_per_node=4, node_count=2)

run_config = ScriptRunConfig(
  source_directory= './src',
  script='train.py',
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

# submit the run configuration to start the job
run = Experiment(ws, "experiment_name").submit(run_config)
```

### <a name="horovod"></a>Horovod

ディープ ラーニング フレームワークによる分散トレーニングに [Horovod](https://horovod.readthedocs.io/en/stable/index.html) を使用する場合は、MPI ジョブ構成を使用します。

コードが次のヒントに従っていることを確認します。

* Azure ML のパーツを追加する前に、トレーニング コードを Horovod で正しくインストルメント化します
* Azure ML 環境に Horovod と MPI が含まれています。 PyTorch および TensorFlow のキュレーションされた GPU 環境に、Horovod とその依存関係が事前に構成されています。
* 目的の分散で `MpiConfiguration` を作成します。

### <a name="horovod-example"></a>Horovod の例

* [azureml-examples: Horovod を使用する TensorFlow 分散トレーニング](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/tensorflow/mnist-distributed-horovod)

### <a name="deepspeed"></a>DeepSpeed

Azure ML 上の [DeepSpeed](https://www.deepspeed.ai/) ライブラリを使用して分散トレーニングを実行するには、DeepSpeed のカスタム ランチャーを使用しないでください。 代わりに、[MPI を使用して](https://www.deepspeed.ai/getting-started/#mpi-and-azureml-compatibility)トレーニング ジョブを起動するように MPI ジョブを構成します。

コードが次のヒントに従っていることを確認します。

* Azure ML 環境に、DeepSpeed とその依存関係、Open MPI、mpi4py が含まれています。
* 目的の分散で `MpiConfiguration` を作成します。

### <a name="deepseed-example"></a>DeepSeed の例

* [azureml-examples: CIFAR-10 で DeepSpeed を使用する分散トレーニング](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/deepspeed/cifar)

### <a name="environment-variables-from-open-mpi"></a>Open MPI からの環境変数

Open MPI イメージで MPI ジョブを実行するときは、起動されるプロセスごとに次の環境変数:

1. `OMPI_COMM_WORLD_RANK` - プロセスのランク
2. `OMPI_COMM_WORLD_SIZE` - ワールド サイズ
3. `AZ_BATCH_MASTER_NODE` - プライマリ アドレスとポート: `MASTER_ADDR:MASTER_PORT`
4. `OMPI_COMM_WORLD_LOCAL_RANK` - ノード上のプロセスのローカル ランク
5. `OMPI_COMM_WORLD_LOCAL_SIZE` - ノード上のプロセスの数

> [!TIP]
> その名前にもかかわらず、環境変数 `OMPI_COMM_WORLD_NODE_RANK` は `NODE_RANK` に対応していません。 ノード単位ランチャーを使用するには、`process_count_per_node=1` を設定し、`NODE_RANK` として `OMPI_COMM_WORLD_RANK` を使用します。

## <a name="pytorch"></a>PyTorch

Azure ML では、PyTorch のネイティブ分散トレーニング機能 (`torch.distributed`) を使用した分散ジョブの実行がサポートされています。

> [!TIP]
> データ並列処理の場合、[PyTorch の公式ガイダンス](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html#comparison-between-dataparallel-and-distributeddataparallel)では、シングルノードとマルチノードのどちらの分散トレーニングにも、DataParallel ではなく DistributedDataParallel (DDP) の使用が推奨されています。 また、PyTorch では、[マルチプロセッシング パッケージより DistributedDataParallel を使用することも推奨されています](https://pytorch.org/docs/stable/notes/cuda.html#use-nn-parallel-distributeddataparallel-instead-of-multiprocessing-or-nn-dataparallel)。 したがって、Azure Machine Learning のドキュメントと例では、DistributedDataParallel のトレーニングに焦点が当てられています。

### <a name="process-group-initialization"></a>プロセス グループの初期化

分散トレーニングのバックボーンは、相互を認識し、バックエンドを使用して相互に通信できる、プロセスのグループに基づいています。 PyTorch の場合、プロセス グループは、__すべての分散プロセス__ で [torch.distributed.init_process_group](https://pytorch.org/docs/stable/distributed.html#torch.distributed.init_process_group) を呼び出して集合的にプロセス グループを形成することによって作成されます。

```
torch.distributed.init_process_group(backend='nccl', init_method='env://', ...)
```

使用される最も一般的な通信バックエンドは、`mpi`、`nccl`、`gloo` です。 GPU ベースのトレーニングの場合は、最適なパフォーマンスのために `nccl` が推奨され、可能な限りそれを使用する必要があります。 

`init_method` により、各プロセスが相互を検出する方法と、通信バックエンドを使用してプロセス グループを初期化および検証する方法が指示されます。 `init_method` を指定しないと、PyTorch により既定で、環境変数の初期化方法 (`env://`) が使用されます。 `init_method` は、Azure ML で分散 PyTorch を実行するためにトレーニング コードで使用することが推奨される初期化方法です。  PyTorch によって、初期化のために次の環境変数が検索されます。

- **`MASTER_ADDR`** - ランクが 0 のプロセスをホストするマシンの IP アドレス。
- **`MASTER_PORT`** - ランクが 0 のプロセスをホストするマシンの空きポート。
- **`WORLD_SIZE`** - プロセスの合計数。 分散トレーニングに使用されるデバイスの合計数 (GPU) と同じである必要があります。
- **`RANK`** - 現在のプロセスの (グローバル) ランク。 指定できる値は 0 から (ワールド サイズ - 1) です。

プロセス グループの初期化の詳細については、[PyTorch のドキュメント](https://pytorch.org/docs/stable/distributed.html#torch.distributed.init_process_group)を参照してください。

これら以外に、多くのアプリケーションでは次の環境変数も必要です。
- **`LOCAL_RANK`** - ノード内のプロセスのローカル (相対) ランク。 指定できる値は 0 から (ノードのプロセス数 - 1) です。 この情報は、データの準備などの多くの操作はノードごとに 1 回だけ実行する必要があるため便利です (通常は、local_rank = 0 で)。
- **`NODE_RANK`** - マルチノード トレーニングのノードのランク。 指定できる値は 0 から (ノードの合計数 - 1) です。

### <a name="pytorch-launch-options"></a>PyTorch の起動オプション

Azure ML の PyTorch ジョブでは、分散トレーニングを開始するために 2 種類のオプションがサポートされています。

- __プロセス単位ランチャー__: プロセス グループを設定するためのすべての関連情報 (環境変数など) を使用して、システムによりすべての分散プロセスが自動的に起動されます。
- __ノード単位ランチャー__: 各ノードで実行されるユーティリティ ランチャーを Azure ML に提供します。 ユーティリティ ランチャーによって、特定のノード上での各プロセスの起動を処理します。 各ノード内でローカルに、`RANK` と `LOCAL_RANK` がランチャーによって設定されます。 **torch.distributed.launch** ユーティリティと PyTorch Lightning はどちらも、このカテゴリに属します。

これらの起動オプションの間に基本的な違いはありません。 ほとんどの場合、選択は、ユーザーの好みや、vanilla PyTorch (Lightning や Hugging Face など) 上に構築されたフレームワークまたはライブラリの規則に基づいています。

以下のセクションでは、各起動オプションに対する Azure ML PyTorch ジョブの詳細な構成方法について説明します。

### <a name="distributeddataparallel-per-process-launch"></a><a name="per-process-launch"></a> DistributedDataParallel (プロセス単位の起動)

`torch.distributed.launch` のようなランチャー ユーティリティを使用する必要はありません。 分散 PyTorch ジョブを実行するには:

1. トレーニング スクリプトと引数を指定します。
1. `PyTorchConfiguration` を作成し、`process_count` と `node_count` を指定します。 `process_count` は、ジョブに対して実行するプロセスの合計数に対応しています。 通常、`process_count` は `# GPUs per node x # nodes` と等しくする必要があります。 `process_count` を指定しないと、Azure ML によって既定でノードごとに 1 つのプロセスが起動されます。

Azure ML によって、各ノードで環境変数 `MASTER_ADDR`、`MASTER_PORT`、`WORLD_SIZE`、`NODE_RANK` が設定され、プロセス レベルで `RANK` および `LOCAL_RANK` 環境変数が設定されます。

ノードごとにマルチプロセスのトレーニングでこのオプションを使用するには、Azure ML Python SDK `>= 1.22.0` を使用します。 process_count は 1.22.0 で導入されました。

```python
from azureml.core import ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(process_count=8, node_count=2)

run_config = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  arguments=['--epochs', 50],
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(run_config)
```

> [!TIP]
> トレーニング スクリプトで、スクリプト引数としてローカル ランクやランクなどの情報を渡す場合は、引数で環境変数を参照できます。
>
> ```python
> arguments=['--epochs', 50, '--local_rank', $LOCAL_RANK]
> ```

### <a name="pytorch-per-process-launch-example"></a>Pytorch のプロセス単位の起動の例

- [azureml-examples: CIFAR-10 で PyTorch を使用する分散トレーニング](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/pytorch/cifar-distributed)

### <a name="using-torchdistributedlaunch-per-node-launch"></a><a name="per-node-launch"></a> `torch.distributed.launch` の使用 (ノード単位の起動)

PyTorch には、ノードごとに複数のプロセスを起動するために使用できる起動ユーティリティが [torch.distributed.launch](https://pytorch.org/docs/stable/distributed.html#launch-utility) に用意されています。 `torch.distributed.launch` モジュールによって、各ノードで複数のトレーニング プロセスが生成されます。

次の手順では、Azure ML でノード単位ランチャーを使用して PyTorch ジョブを構成する方法を示します。  このジョブにより、次のコマンドを実行するのと同等の機能が実現されます。

```shell
python -m torch.distributed.launch --nproc_per_node <num processes per node> \
  --nnodes <num nodes> --node_rank $NODE_RANK --master_addr $MASTER_ADDR \
  --master_port $MASTER_PORT --use_env \
  <your training script> <your script arguments>
```

1. `ScriptRunConfig` コンストラクターの `command` パラメーターに `torch.distributed.launch` コマンドを指定します。 Azure ML によって、トレーニング クラスターの各ノードでこのコマンドが実行されます。 `--nproc_per_node` は、各ノードで使用できる GPU の数以下である必要があります。 MASTER_ADDR、MASTER_PORT、NODE_RANK はすべて Azure ML によって設定されるので、コマンドで環境変数を参照するだけで済みます。 Azure ML によって MASTER_PORT は `6105` に設定されますが、必要に応じて、torch.distributed.launch コマンドの `--master_port` 引数に別の値を渡すことができます。 (起動ユーティリティによって環境変数がリセットされます。)
2. `PyTorchConfiguration` を作成し、`node_count` を指定します。

```python
from azureml.core import ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(node_count=2)
launch_cmd = "python -m torch.distributed.launch --nproc_per_node 4 --nnodes 2 --node_rank $NODE_RANK --master_addr $MASTER_ADDR --master_port $MASTER_PORT --use_env train.py --epochs 50".split()

run_config = ScriptRunConfig(
  source_directory='./src',
  command=launch_cmd,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(run_config)
```

> [!TIP]
> **単一ノード マルチ GPU のトレーニング:** 起動ユーティリティを使用して、単一ノード マルチ GPU の PyTorch トレーニングを実行する場合は、ScriptRunConfig の `distributed_job_config` パラメーターを指定する必要はありません。
>
>```python
> launch_cmd = "python -m torch.distributed.launch --nproc_per_node 4 --use_env train.py --epochs 50".split()
>
> run_config = ScriptRunConfig(
>  source_directory='./src',
>  command=launch_cmd,
>  compute_target=compute_target,
>  environment=pytorch_env,
> )
> ```

### <a name="pytorch-per-node-launch-example"></a>PyTorch のノード単位の起動の例

- [azureml-examples: CIFAR-10 で PyTorch を使用する分散トレーニング](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/pytorch/cifar-distributed)

### <a name="pytorch-lightning"></a>PyTorch Lightning

[PyTorch Lightning](https://pytorch-lightning.readthedocs.io/en/stable/) は、PyTorch 用の高レベル インターフェイスを提供する軽量のオープンソース ライブラリです。 Lightning により、vanilla PyTorch に必要な低レベルの分散トレーニング構成の多くが抽象化されます。 Lightning を使用すると、単一 GPU、単一ノードのマルチ GPU、マルチノードのマルチ GPU の設定で、トレーニング スクリプトを実行できます。 バックグラウンドでは、`torch.distributed.launch` と同様に複数のプロセスが自動的に起動されます。

単一ノード トレーニング (単一ノードのマルチ GPU を含む) の場合は、`distributed_job_config` を指定することなく、Azure ML でコードを実行できます。 マルチノード トレーニングの場合の Lightning には、トレーニング クラスターの各ノードで次の環境変数を設定する必要があります。

- MASTER_ADDR
- MASTER_PORT
- NODE_RANK

Azure ML でマルチノードの Lightning トレーニングを実行するには、ほぼ[ノード単位の起動のガイド](#per-node-launch)に従うことができます。

- `PyTorchConfiguration` を定義し、`node_count` を指定します。 ノードごとのワーカー プロセス起動は Lightning によって内部的に処理されるので、`process_count` は指定しないでください。
- PyTorch ジョブの場合、Lightning に必要な MASTER_ADDR、MASTER_PORT、NODE_RANK の各環境変数の設定は、Azure ML によって処理されます。
- Lightning によって、Trainer フラグ `--gpus` と `--num_nodes` からのワールド サイズの計算が処理され、ランクとローカル ランクが内部的に管理されます。

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.runconfig import PyTorchConfiguration

nnodes = 2
args = ['--max_epochs', 50, '--gpus', 2, '--accelerator', 'ddp', '--num_nodes', nnodes]
distr_config = PyTorchConfiguration(node_count=nnodes)

run_config = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  arguments=args,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(run_config)
```

### <a name="hugging-face-transformers"></a>Hugging Face Transformers

Hugging Face には、その Transformers ライブラリ `torch.distributed.launch` を使用して分散トレーニングを実行するための、多くの[例](https://github.com/huggingface/transformers/tree/master/examples)が用意されています。 Transformers Trainer API を使用してこれらの例と独自のカスタム トレーニング スクリプトを実行するには、「[`torch.distributed.launch` の使用](#per-node-launch)」セクションに従います。

8 GPU の 1 ノードで `run_glue.py` スクリプトを使用して、テキスト分類 MNLI タスクで BERT ラージ モデルを微調整するジョブ構成コードの例:

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

distr_config = PyTorchConfiguration() # node_count defaults to 1
launch_cmd = "python -m torch.distributed.launch --nproc_per_node 8 text-classification/run_glue.py --model_name_or_path bert-large-uncased-whole-word-masking --task_name mnli --do_train --do_eval --max_seq_length 128 --per_device_train_batch_size 8 --learning_rate 2e-5 --num_train_epochs 3.0 --output_dir /tmp/mnli_output".split()

run_config = ScriptRunConfig(
  source_directory='./src',
  command=launch_cmd,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)
```

[プロセス単位の起動](#per-process-launch)オプションを使用して、`torch.distributed.launch` を使用せずに分散トレーニングを実行することもできます。 この方法を使用する場合に注意することの 1 つは、Transformers の [TrainingArguments](https://huggingface.co/transformers/main_classes/trainer.html?highlight=launch#trainingarguments) には引数としてローカル ランクを渡す必要があるということです (`--local_rank`)。 `--use_env=False` のときは、これは `torch.distributed.launch` によって処理されますが、プロセス単位の起動を使用している場合は、Azure ML によって設定されるのは `LOCAL_RANK` 環境変数だけなので、ユーザーはトレーニング スクリプト `--local_rank=$LOCAL_RANK` に引数としてローカル ランクを明示的に渡す必要があります。

## <a name="tensorflow"></a>TensorFlow

TensorFlow 2.x の `tf.distribute.Strategy` API など、トレーニング コードで[ネイティブの TensorFlow](https://www.tensorflow.org/guide/distributed_training) を使用している場合は、`TensorflowConfiguration` を使用して Azure ML から分散ジョブを起動することができます。

そのためには、`ScriptRunConfig` コンストラクターの `distributed_job_config` パラメーターに `TensorflowConfiguration` オブジェクトを指定します。 `tf.distribute.experimental.MultiWorkerMirroredStrategy` を使用している場合は、`TensorflowConfiguration` で、トレーニング ジョブのノード数に対応する `worker_count` を指定します。

```python
from azureml.core import ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import TensorflowConfiguration

curated_env_name = 'AzureML-TensorFlow-2.3-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=0)

run_config = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  compute_target=compute_target,
  environment=tf_env,
  distributed_job_config=distr_config,
)

# submit the run configuration to start the job
run = Experiment(ws, "experiment_name").submit(run_config)
```

トレーニング スクリプトで、レガシの TensorFlow 1.x など、分散トレーニングのためにパラメーター サーバー戦略を使用している場合は、ジョブで使用するパラメーター サーバーの数も指定する必要があります (例: `tf_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)`)。

### <a name="tf_config"></a>TF_CONFIG

TensorFlow では、複数マシンでのトレーニングには、**TF_CONFIG** 環境変数が必要です。 TensorFlow ジョブの場合は、トレーニング スクリプトを実行する前に、Azure ML によって TF_CONFIG 変数が構成され、各ワーカーに対して適切に設定されます。

必要な場合は、トレーニング スクリプトから TF_CONFIG にアクセスできます: `os.environ['TF_CONFIG']`。

最高ワーカー ノードに設定される TF_CONFIG の例:
```json
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

### <a name="tensorflow-example"></a>TensorFlow の例

- [azureml-examples: MultiWorkerMirroredStrategy を使用する分散 TensorFlow トレーニング](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/tensorflow/mnist-distributed)

## <a name="accelerating-gpu-training-with-infiniband"></a><a name="infiniband"></a> InfiniBand による GPU トレーニングの高速化

特定の Azure VM シリーズ (具体的には、NC、ND、H シリーズ) には、SR-IOV と Infiniband をサポートする RDMA 対応 VM が用意されています。 これらの VM は、低遅延で高帯域幅の InfiniBand ネットワークを介して通信し、イーサネットベースの接続よりパフォーマンスがはるかに高くなります。 InfiniBand 用 SR-IOV を使用すると、任意の MPI ライブラリでベアメタルに近いパフォーマンスが可能になります (MPI は、NVIDIA の NCCL ソフトウェアなど、多くの分散トレーニング フレームワークとツールで使用されています)。これらの SKU の目的は、コンピューティング集中型で GPU により高速化される機械学習ワークロードのニーズを満たすことです。 詳細については、[ による Azure Machine Learning での分散トレーニングの高速化](https://techcommunity.microsoft.com/t5/azure-ai/accelerating-distributed-training-in-azure-machine-learning/ba-p/1059050)に関する記事を参照してください。

このような RDMA 対応で InfiniBand が有効なサイズのいずれか (`Standard_ND40rs_v2` など) の `AmlCompute` クラスターを作成する場合、OS イメージには、InfiniBand を有効にするために必要な Mellanox OFED ドライバーがプレインストールされ、事前構成されています。

## <a name="next-steps"></a>次の手順

* [機械学習モデルを Azure にデプロイする](how-to-deploy-and-where.md)
* [マネージド オンライン エンドポイントを使用して機械学習モデルをデプロイおよびスコアリングする (プレビュー)](how-to-deploy-managed-online-endpoints.md)
* [Azure での分散型ディープ ラーニング トレーニングの参照アーキテクチャ](/azure/architecture/reference-architectures/ai/training-deep-learning)
