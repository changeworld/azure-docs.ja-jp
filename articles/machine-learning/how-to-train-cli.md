---
title: CLI (v2) を使用してモデルをトレーニングする (ジョブを作成する)
titleSuffix: Azure Machine Learning
description: Machine Learning 用の Azure CLI 拡張機能を使用してモデルをトレーニングする (ジョブを作成する) 方法について学習します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: lostmygithubaccount
ms.author: copeters
ms.date: 06/18/2021
ms.reviewer: laobri
ms.custom: devx-track-azurecli, devplatv2
ms.openlocfilehash: 9f3a91f9abc472f285139bfac04af7dff5c63e9f
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444557"
---
# <a name="train-models-create-jobs-with-the-cli-v2"></a>CLI (v2) を使用してモデルをトレーニングする (ジョブを作成する)

Azure Machine Learning CLI (v2) は、モデルのライフサイクルを追跡および監査可能な状態で Azure コンピューティングでスケールアップとスケールアウトをしながら、モデルのトレーニング プロセスを加速させることができる Azure CLI の拡張機能です。

通常、機械学習モデルのトレーニングは反復的プロセスです。 最新のツールを使用すると、より多くのデータでより大きなモデルをより速くトレーニングすることがこれまでになく簡単になります。 多くの場合、ハイパーパラメーター調整などの以前は面倒だった手動プロセスやアルゴリズム選択でさえも自動化されます。 Azure Machine Learning CLI を使用すると、ハイパーパラメーター スイープ、高パフォーマンス Azure コンピューティングでのスケールアップ、分散トレーニングを利用したスケールアウトを使用して、[ワークスペース](concept-workspace.md)でジョブ (およびモデル) を追跡できます。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>前提条件

- CLI を使用するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://azure.microsoft.com/free/) を今すぐお試しください。
- [Machine Learning 用の Azure CLI 拡張機能をインストールして設定する](how-to-configure-cli.md)

> [!TIP]
> 完全な機能を備えた開発環境の場合は、Visual Studio Code と [Azure Machine Learning 拡張機能](how-to-setup-vs-code.md)を使用して、[Azure Machine Learning リソースを管理](how-to-manage-resources-vscode.md)し、[機械学習モデルをトレーニング](tutorial-train-deploy-image-classification-model-vscode.md)します。

### <a name="clone-examples-repository"></a>examples リポジトリを複製する

トレーニング例を実行するには、最初に examples リポジトリを複製し、`cli` ディレクトリに変更します。

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="git_clone":::

なお、`--depth 1` では、リポジトリに対する最新のコミットだけが複製されるので、操作の完了にかかる時間を短縮できます。

### <a name="create-compute"></a>コンピューティングを作成する

コマンド ラインから Azure Machine Learning コンピューティング クラスターを作成できます。 たとえば、次のコマンドでは、`cpu-cluster` という名前のクラスターと `gpu-cluster` という名前のクラスターを作成します。

:::code language="azurecli" source="~/azureml-examples-main/cli/create-compute.sh" id="create_computes":::

`cpu-cluster` と `gpu-cluster` はジョブが送信されるまで 0 ノードのままであるため、この時点ではコンピューティングの料金は請求されないことに注意してください。 [AmlCompute のコストを管理および最適化する](how-to-manage-optimize-cost.md#use-azure-machine-learning-compute-cluster-amlcompute)方法を確認してください。

この記事の次のジョブ例では、`cpu-cluster` または `gpu-cluster` のいずれかを使用します。 これらを必要に応じて、お使いのクラスター名に調整します。

コンピューティング作成オプションの詳細については、`az ml compute create -h` に関するページを参照してください。

[!INCLUDE [arc-enabled-kubernetes](../../includes/machine-learning-create-arc-enabled-training-computer-target.md)]

## <a name="introducing-jobs"></a>ジョブの概要

Azure Machine Learning CLI (v2) では、ジョブは YAML 形式で作成されます。 ジョブに集約されるもの:

- 実行対象
- 実行方法
- 実行する場所

"hello world" ジョブには、この 3 つのすべてがあります。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/misc/hello-world.yml":::

これは次のように実行します。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world":::

ただし、これはログ ファイルに 1 行のみを出力する単なるサンプルのジョブです。 通常は、システムによって生成されたログに加えて、モデル バイナリや付随するメタデータなどの追加の成果物を生成します。

Azure Machine Learning では、次の成果物を自動的にキャプチャします。

- `./outputs` と `./logs` のディレクトリは、Azure Machine Learning によって特別に扱われます。 ジョブ中にこれらのディレクトリにファイルを書き込むと、これらのファイルがジョブの実行履歴にアップロードされるため、ジョブが完了した後も引き続きこれらにアクセスできるようになります。 `./outputs` フォルダーは、ジョブの最後にアップロードされます。`./logs` に書き込まれたファイルは、リアルタイムでアップロードされます。 TensorBoard ログなど、ジョブ中にログをストリーム配信する場合は、後者を使用します。
- Azure Machine Learning は、MLflow の追跡機能と統合されます。 いくつかの一般的な ML フレームワークに `mlflow.autolog()` を使用して、モデル パラメーター、パフォーマンス メトリック、モデル成果物、さらには特徴の重要度のグラフをログに記録できます。 `mlflow.log_*()` メソッドを使用して、パラメーター、メトリック、および成果物を明示的にログに記録することもできます。 MLflow によってログに記録されたすべてのメトリックと成果物は、ジョブの実行履歴に保存されます。

多くの場合、ジョブには、ローカルで編集および制御されるソース コードの実行が含まれます。 コマンドが実行されるジョブに含めるソース コード ディレクトリを指定できます。

たとえば、examples リポジトリの `jobs/train/lightgbm/iris` プロジェクト ディレクトリを見てみましょう。

```tree
.
├── job-sweep.yml
├── job.yml
└── src
    └── main.py
```

このディレクトリには、2 つのジョブ ファイルとソース コード サブディレクトリ `src` が含まれています。 この例では `src` の下にファイルが 1 つしかありませんが、サブディレクトリ全体が再帰的にアップロードされ、ジョブで使用できるようになります。

コマンド ジョブは、`job.yml` で構成します。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/lightgbm/iris/job.yml":::

これは次のように実行します。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="lightgbm_iris":::

## <a name="basic-python-training-job"></a>基本的な Python トレーニング ジョブ

ジョブ YAML ファイルの詳細を確認してみましょう。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/lightgbm/iris/job.yml":::

| Key | 説明 |
| --- | ----------- |
| `$schema` | [省略可能] YAML スキーマ。 上の例の[スキーマ](https://azuremlschemas.azureedge.net/latest/commandJob.schema.json)をブラウザーで表示すると、コマンド ジョブの YAML ファイルで使用可能なすべてのオプションを確認できます。 Azure Machine Learning 用 VS Code 拡張機能を使用して YAML ファイルを作成する場合は、ファイルの先頭にこの `$schema` プロパティを含めることで、スキーマとリソースの入力候補を呼び出すことができます。 |
| `code.local_path` | [省略可能] アップロードしてジョブで使用する、YAML ファイルを基準としたソース ディレクトリへのローカル パス。 整合性を確保するために、ジョブ ファイルと同じディレクトリ内の `src` を使用することを検討してください。 |
| `command` | 実行するコマンドです。 `>` 規則では、改行をスペースに折りたたむことにより、読み取り可能な複数行コマンドを作成できます。 コマンドライン引数は、コマンドに明示的に書き込むことも、中かっこ表記を使用して他のセクション (具体的には `inputs` または `search_space`) から推論することもできます。 |
| `inputs` | [省略可能] 入力データ バインディングのディクショナリ。キーは、入力バインディングに指定する名前です。 各要素の値は、`data` および `mode` フィールドで構成される入力バインディングです。 `data` には、1) `azureml:` プレフィックスを使用した既存のバージョン管理された Azure Machine Learning データ資産への参照 (たとえば、"iris-url" という名前のデータ資産のバージョン 1 を指す `azureml:iris-url:1`)、または 2) データのインライン定義を指定できます。 クラウドの場所を指定するには、`data.path` を使用します。 既定のデータストアにアップロードされるローカル ファイルシステムのデータを指定するには、`data.local_path` を使用します。 `mode` は、ジョブのコンピューティングでデータを使用できるようにする方法を示します。 "mount" と "download" の 2 つのオプションがサポートされています。 <br><br> 入力は、コマンド内で名前で参照できます (たとえば、`{inputs.my_input_name}`)。 Azure Machine Learning により、コマンド内のパラメーター化された表記が、実行時にコンピューティング先のそのデータの場所に解決されます。 たとえば、マウントされるようにデータが構成されている場合、`{inputs.my_input_name}` はマウント ポイントに解決されます。 |
| `environment` | コンピューティング先でコマンドを実行する環境。 使用する Docker イメージを指定するか、イメージをビルドするための Dockerfile を指定することで、環境をインラインで定義できます。 `azureml:` プレフィックスを使用して、ワークスペース内の既存のバージョン管理された環境、または Azure ML のキュレーションされた環境の 1 つを参照することもできます。 たとえば、`azureml:AzureML-TensorFlow2.4-Cuda11-OpenMpi4.1.0-py36:1` は、GPU をサポートする TensorFlow のキュレーションされた環境のバージョン 1 を参照します。 <br><br> Python は、トレーニングに使用される環境にインストールする必要があります。 必要に応じて、Dockerfile で `apt-get update -y && apt-get install python3 -y` を実行してインストールします。 |
| `compute.target` | コンピューティング ターゲット。 ローカル実行用に `local` を指定するか、`azureml:` プレフィックスを使用してワークスペース内の既存のコンピューティング リソースを参照します。 たとえば、`azureml:cpu-cluster` は、"cpu-cluster" という名前のコンピューティング先を指します。 |
| `experiment_name` | [省略可能] Azure Machine Learning スタジオでより適切に整理できるようにジョブにタグを付けます。 各ジョブの実行レコードは、スタジオの [実験] タブの対応する実験の下に整理されます。省略した場合、これは既定で、ジョブが作成された作業ディレクトリの名前に設定されます。 |
| `name` | [省略可能] ジョブの名前。これは、ワークスペース内のすべてのジョブで一意である必要があります。 YAML ファイル (`name` フィールド) またはコマンド ライン (`--name/-n`) に名前を指定しない限り、GUID または UUID が自動的に生成され、名前に使用されます。 ジョブ名は、ジョブの実行レコードのスタジオ UI の実行 ID に対応します。 |

このジョブを作成すると、指定されたローカル資産 (ソース コード ディレクトリなど) がアップロードされ、YAML ファイルが検証され、実行が送信されます。 必要に応じて、環境が構築され、ジョブを実行するためにコンピューティングがスケールアップおよび構成されます。

lightgbm または iris トレーニング ジョブを実行するには:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="lightgbm_iris":::

ジョブが完了すると、出力をダウンロードできます。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="download_outputs":::

> [!IMPORTANT]
> `$run_id` を実際の実行 ID に置き換えます。これは、コンソール出力またはスタジオの [実行の詳細] ページで見つけることができます。

これにより、ログとキャプチャされた成果物が `$run_id` という名前のディレクトリにローカルにダウンロードされます。 この例では、MLflow によってログに記録されたモデルのサブディレクトリがダウンロードされます。

## <a name="sweep-hyperparameters"></a>ハイパーパラメーターをスイープする

Azure Machine Learning では、機械学習モデルのハイパーパラメーターをより効率的に調整することもできます。 スイープ ジョブと呼ばれるハイパーパラメーター調整ジョブを構成し、CLI を介して送信できます。

`job.yml` を `job-sweep.yml` に変更して、ハイパーパラメーターをスイープすることができます。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/lightgbm/iris/job-sweep.yml":::

| Key | 説明 |
| --- | ----------- |
| `$schema` | [省略可能] YAML スキーマが変更され、スイープ ジョブの[スキーマ](https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json)を指すようになりました。 |
| `type` | ジョブの種類。 |
| `algorithm` | サンプリング アルゴリズムの "random" は、多くの場合に適しています。 オプションの列挙については、スキーマを参照してください。 |
| `trial` | 実行する各トライアルのコマンド ジョブ構成。 コマンド (`trial.command`) が前の例から変更され、`{search_space.<hyperparameter_name>}` 表記を使用して、`search_space` で定義されたハイパーパラメーターを参照するようになりました。 Azure Machine Learning により、パラメーター化された各表記が、各トライアルに対して生成される対応するハイパーパラメーターの値に解決されます。 |
| `search_space` | スイープするハイパーパラメーターの辞書。 キーは、ハイパーパラメーターの名前です (たとえば、`search_space.learning_rate`)。 名前は、トレーニング スクリプトの引数自体と一致する必要はありませんが、コマンドの中かっこ表記の検索空間参照と一致する必要があることに注意してください (例: `{search_space.learning_rate}` )。 値は、ハイパーパラメーター分布です。 オプションの列挙については、スキーマを参照してください。 |
| `objective.primary_metric` | 最適化メトリック。これは、トレーニング コードからログに記録されたメトリックの名前と一致する必要があります。 `objective.goal` は、方向 ("最小化" または "最大化") を指定します。 オプションの完全な列挙については、スキーマを参照してください。 |
| `max_total_trials` | 実行する個々のトライアルの最大数。 |
| `max_concurrent_trials` | [省略可能] 自分のコンピューティング クラスターで同時に実行するトライアルの最大数。 |
| `timeout_minutes` | [省略可能] スイープ ジョブを実行する最大時間 (分)。 |
| `experiment_name` | [省略可能] スイープ ジョブを追跡する実験。 省略した場合、これは既定でジョブの作成時の作業ディレクトリの名前に設定されます。 |

ジョブを作成し、スタジオで開きます。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="lightgbm_iris_sweep":::

> [!TIP]
> ハイパーパラメーター スイープは、分散コマンド ジョブで使用できます。

## <a name="distributed-training"></a>分散トレーニング

コマンド ジョブで `distribution` セクションを指定できます。 Azure ML では、PyTorch、Tensorflow、および MPI 互換フレームワークの分散トレーニングをサポートしています。 PyTorch と TensorFlow を使用すると、TensorFlow の `tf.distributed.Strategy` API など、それぞれのフレームワークに対するネイティブ分散トレーニングが可能になります。

`compute.instance_count` を、ジョブに必要なノード数に必ず設定してください (既定値は 1)。

### <a name="pytorch"></a>PyTorch

CIFAR-10 データセットでの分散 PyTorch トレーニング用の YAML ファイルの例:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/pytorch/cifar-distributed/job.yml":::

これはクローンされた examples リポジトリに存在しないローカル データを参照していることに注意してください。 最初に、CIFAR-10 データセットをローカルでダウンロード、抽出、および再配置して、プロジェクト ディレクトリ内の適切な場所に配置する必要があります。

:::code language="bash" source="~/azureml-examples-main/cli/train.sh" id="download_cifar":::

ジョブを作成し、スタジオで開きます。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="pytorch_cifar":::

### <a name="tensorflow"></a>TensorFlow

MNIST データセットでの分散 TensorFlow トレーニング用の YAML ファイルの例:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/tensorflow/mnist-distributed/job.yml":::

ジョブを作成し、スタジオで開きます。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="tensorflow_mnist":::

### <a name="mpi"></a>MPI

Azure ML では、複数のノード間およびノードごとの複数のプロセス間での MPI ジョブの起動をサポートしています。 ジョブは、`mpirun` を介して起動されます。 たとえば、トレーニング コードで分散トレーニングに Horovod フレームワークを使用している場合は、このジョブの種類を利用して Azure ML でトレーニングすることができます。

MPI ジョブを起動するには、`distribution` セクションで、種類として `mpi` を指定し、起動するノードごとのプロセスの数 (`process_count_per_instance`) を指定します。 このフィールドを指定しないと、Azure ML では、既定でノードごとに 1 つのプロセスが起動されます。

Horovod を使用して MNIST で TensorFlow ジョブを実行する YAML 指定の例:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/tensorflow/mnist-distributed-horovod/job.yml":::

ジョブを作成し、スタジオで開きます。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="tensorflow_mnist_horovod":::

## <a name="next-steps"></a>次の手順

- [マネージド オンライン エンドポイントを使用して機械学習モデルをデプロイおよびスコアリングする (プレビュー)](how-to-deploy-managed-online-endpoints.md)
- [REST を使用してモデルをトレーニングする (プレビュー)](how-to-train-with-rest.md)
