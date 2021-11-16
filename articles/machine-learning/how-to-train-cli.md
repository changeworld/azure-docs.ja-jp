---
title: CLI (v2) を使用してモデルをトレーニングする
titleSuffix: Azure Machine Learning
description: Machine Learning 用の Azure CLI 拡張機能を使用してモデルをトレーニングする (ジョブを作成する) 方法について学習します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: amibp
ms.author: amipatel
ms.date: 10/21/2021
ms.reviewer: laobri
ms.custom: devx-track-azurecli, devplatv2
ms.openlocfilehash: b849293d94b435fa74378d80d8989e6075f78d86
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493668"
---
# <a name="train-models-with-the-cli-v2-preview"></a>CLI (v2) を使用してモデルをトレーニングする (プレビュー)

Azure Machine Learning CLI (v2) は、モデルのライフサイクルを追跡および監査可能な状態で Azure コンピューティングでスケールアップとスケールアウトをしながら、モデルのトレーニング プロセスを加速させることができる Azure CLI の拡張機能です。

通常、機械学習モデルのトレーニングは反復的プロセスです。 最新のツールを使用すると、より多くのデータでより大きなモデルをより速くトレーニングすることがこれまでになく簡単になります。 多くの場合、ハイパーパラメーター調整などの以前は面倒だった手動プロセスやアルゴリズム選択でさえも自動化されます。 Azure Machine Learning CLI (v2) を使用すると、ハイパーパラメーター スイープ、高パフォーマンス Azure コンピューティングでのスケールアップ、分散トレーニングを利用したスケールアウトを使用して、[ワークスペース](concept-workspace.md)でジョブ (およびモデル) を追跡できます。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>必須コンポーネント

- CLI (v2) を使用するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://azure.microsoft.com/free/) を今すぐお試しください。
- [CLI (v2) をインストールして設定](how-to-configure-cli.md)します。

> [!TIP]
> ジョブ YAMLs のスキーマ検証とオートコンプリートを含む完全な機能を備えた開発環境では、Visual Studio Code と [Azure Machine Learning](how-to-setup-vs-code.md) 拡張機能を使用します。

### <a name="clone-examples-repository"></a>examples リポジトリを複製する

トレーニング例を実行するには、最初に examples リポジトリを複製し、`cli` ディレクトリに変更します。

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="git_clone":::

`--depth 1` を使用すると、リポジトリに対する最新のコミットだけが複製されるので、操作の完了にかかる時間を短縮できます。

### <a name="create-compute"></a>コンピューティングを作成する

コマンド ラインから Azure Machine Learning コンピューティング クラスターを作成できます。 たとえば、次のコマンドでは、`cpu-cluster` という名前のクラスターと `gpu-cluster` という名前のクラスターを作成します。

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/create-compute.sh" id="create_computes":::

`cpu-cluster` と `gpu-cluster` はジョブが送信されるまでゼロ ノードのままであるため、この時点ではコンピューティングの料金は請求されません。 [AmlCompute のコストを管理および最適化する](how-to-manage-optimize-cost.md#use-azure-machine-learning-compute-cluster-amlcompute)方法を確認してください。

この記事の次のジョブ例では、`cpu-cluster` または `gpu-cluster` のいずれかを使用します。 必要に応じて、この記事全体のサンプル ジョブでこれらの名前をクラスターの名前に調整します。 コンピューティング作成オプションの詳細については、`az ml compute create -h` に関するページを参照してください。

## <a name="hello-world"></a>Hello world

Azure Machine Learning CLI (v2) では、ジョブは YAML 形式で作成されます。 ジョブに集約されるもの:

- 実行対象
- 実行方法
- 実行する場所

"hello world" ジョブには、この 3 つのすべてがあります。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world.yml":::

> [!WARNING]
> Python は、ジョブに使用される環境にインストールする必要があります。 必要に応じてをインストールするには、Dockerfile で `apt-get update -y && apt-get install python3 -y` を実行するか、既に Python がインストールされている基本イメージから派生させます。

> [!TIP]
> `$schema:` 全体の例では、[Azure Machine Learning 拡張機能を使用して VSCode](how-to-setup-vs-code.md) で YAML ファイルを作成する場合、スキーマの検証とオートコンプリートが可能です。

これは次のように実行します。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world":::

> [!TIP]
> `--web` パラメーターは、既定の Web ブラウザーを使用して、Azure Machine Learning スタジオでジョブを開こうとします。 `--stream` パラメーターを使用すると、コンソールにログをストリーミングし、さらにコマンドをブロックできます。

## <a name="overriding-values-on-create-or-update"></a>作成または更新時の値のオーバーライド

YAML ジョブの仕様値は、ジョブの作成時または更新時に `--set` を使用して上書きできます。 次に例を示します。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_set":::

## <a name="job-names"></a>ジョブ名

`create` と `list` 以外のほとんどの `az ml job` コマンドでは、`--name/-n` が必要です。これは、スタジオのジョブ名または "実行 ID" です。 ジョブの `name` プロパティは、ワークスペースごとに一意である必要があるため、作成時に直接設定しないでください。 Azure Machine Learning は、ジョブ名が設定されていない場合、そのジョブ名に対してランダムな GUID を生成します。これは、CLI でのジョブ作成の出力から取得することも、スタジオおよび MLflow API で "実行 ID" プロパティをコピーすることによって取得することもできます。

スクリプトと CI/CD フローのジョブを自動化するためにジョブ名の作成時に `--query name -o tsv` を追加してジョブ名を問い合わせて出力を部分的に取り除くことで、ジョブの名前を取得できます。 詳細はシェルによって異なりますが、Bash の場合は次のようになります。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_name":::

次に、後続の `update`、`show`、`stream` などのコマンドで、`$run_id` を使用します。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_show":::

## <a name="organize-jobs"></a>ジョブを整理する

ジョブを整理するには、表示名、実験名、説明、タグを設定します。 説明は、スタジオでのマークダウン構文をサポートしています。 これらのプロパティは、ジョブの作成後に変更できます。 完全な例:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-org.yml":::

このジョブを実行できます。これらのプロパティは、スタジオですぐに表示されます。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_org":::

`--set` を使用すると、ジョブの作成後に変更可能な値を更新できます。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_org_set":::

## <a name="environment-variables"></a>環境変数

ジョブで使用する環境変数を設定できます。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-env-var.yml":::

このジョブは、次のように実行できます。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_env_var":::

> [!WARNING]
> `command` で引数をパラメーター化するには、`inputs` を使用する必要があります。 「[入力と出力を使用する](#inputs-and-outputs)」を参照してください。

## <a name="track-models-and-source-code"></a>モデルとソース コードの追跡

実稼働機械学習モデルは、(再現できない場合は) 監査可能である必要があります。 特定のモデルのソース コードを追跡することが重要です。 Azure Machine Learning は、ソース コードのスナップショットを取得し、ジョブと共に保持します。 また、Git リポジトリからジョブを実行している場合は、ソース リポジトリとコミットも保持されます。

> [!TIP]
> 例のリポジトリに従って実行している場合は、これまでに実行されているジョブのいずれかで、ソース リポジトリを確認し、スタジオでコミットすることができます。

ソース コード ディレクトリへのパスとして値を指定して、ジョブ内の `code.local_path` キーを指定できます。 ディレクトリのスナップショットが取得され、ジョブと共にアップロードされます。 ディレクトリの内容は、ジョブの作業ディレクトリから直接使用できます。

> [!WARNING]
> ソース コードには、モデル トレーニングのための大きなデータ入力を含めないでください。 代わりに、[データ入力を使用](#data-inputs)します。 ソース コード ディレクトリ内のファイルを使用して、スナップショットから `.gitignore` ファイルを除外することができます。 スナップショット サイズの制限は、300 MB または 2000 個のファイルです。

コードを指定するジョブを見てみましょう。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-mlflow.yml":::

Python スクリプトは、ローカルのソース コード ディレクトリにあります。 次に、コマンドは、`python` を呼び出してスクリプトを実行します。 同じパターンを他のプログラミング言語にも適用できます。

> [!WARNING]
> この記事に示されているジョブの "hello" ファミリは、デモンストレーションを目的としており、必ずしも推奨されるベスト プラクティスに従うわけではありません。 `&&` または類似の方法を使用して、1 つのシーケンス内で多くのコマンドを実行することは推奨されません。代わりに、コマンドをソース コード ディレクトリのスクリプト ファイルに書き込み、`command` でスクリプトを呼び出すことを検討してください。 上に示したように、`pip install` を介して `command` に依存関係をインストールすることは推奨されません。代わりに、すべてのジョブの依存関係を環境の一部として指定する必要があります。 詳しくは、「[CLI (v2) を使用して環境を作成および管理する](how-to-manage-environments-v2.md)」を参照してください。

### <a name="model-tracking-with-mlflow"></a>MLflow を使用したモデル追跡

モデルの反復処理中に、データ科学者はモデル パラメーターとトレーニング メトリックを追跡できる必要があります。 Azure Machine Learning は、MLflow 追跡と統合して、ジョブに対するモデル、成果物、メトリック、およびパラメーターのログ記録を有効にします。 Python スクリプトで MLflow を使用するには、トレーニング コードに `import mlflow` を追加し、`mlflow.log_*` API または `mlflow.autolog()` API を呼び出します。

> [!WARNING]
> MLflow 追跡機能を使用するには、`mlflow` パッケージおよび `azureml-mlflow` パッケージを Python 環境にインストールする必要があります。

> [!TIP]
> この `mlflow.autolog()` 呼び出しは多くの一般的なフレームワークでサポートされており、ほとんどのログ記録を行います。

`mlflow` を使用してパラメーター、メトリック、成果物をログに記録する上記のジョブで呼び出された Python スクリプトを見てみましょう。

:::code language="python" source="~/azureml-examples-main/cli/jobs/basics/src/hello-mlflow.py":::

このジョブは、Azure Machine Learning を使用してクラウドで実行できます。このジョブは追跡され、監査可能です。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_mlflow":::

### <a name="query-metrics-with-mlflow"></a>MLflow によるメトリックのクエリ

ジョブを実行した後、ジョブの実行結果とログに記録されたメトリックに対してクエリを実行することができます。 Python は CLI よりも、このタスクに適しています。 `mlflow` を使用して実行とそのメトリックを照会し、Pandas のデータフレーム分析などの使い慣れたオブジェクトに読み込むことができます。

まず、Azure Machine Learning ワークスペースの MLflow 追跡 URI を取得します。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="mlflow_uri":::

MLflow がインポートされた Python 環境から、`mlflow.set_tracking_uri(<YOUR_TRACKING_URI>)` でこのコマンドの出力を使用します。 MLflow の呼び出しが、Azure Machine Learning ワークスペースのジョブに対応するようになりました。

## <a name="inputs-and-outputs"></a>入力と出力

ジョブには通常、入力と出力があります。 入力には、ハイパーパラメーターの最適化のためにスイープされるモデル パラメーターや、コンピューティング ターゲットにマウントまたはダウンロードされるクラウド データ入力を使用できます。 出力 (メトリックを無視する) は、既定の出力または名前付きのデータ出力に書き込むことができる成果物です。

### <a name="literal-inputs"></a>リテラル入力

リテラル入力は、コマンドで直接解決されます。 "hello world" ジョブを変更して、リテラル入力を使用できます。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-input.yml":::

このジョブは、次のように実行できます。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_input":::

`--set` を使用して、入力をオーバーライドできます。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_input_set":::

ジョブへのリテラル入力は、モデル トレーニングでのハイパーパラメーター スイープの[検索空間入力に変換](#search-space-inputs)できます。

### <a name="search-space-inputs"></a>検索スペースの入力

スイープ ジョブの場合は、選択するリテラル入力の検索空間を指定できます。 検索空間の入力に関するオプションの全範囲については、[スイープ ジョブの YAML 構文リファレンス](reference-yaml-job-sweep.md)を参照してください。

> [!WARNING]
> 現在、スイープ ジョブはパイプライン ジョブではサポートされていません。

引数を取り込み、ランダムなメトリックをログに記録する単純な Python スクリプトを使用して、概念を示します。

:::code language="python" source="~/azureml-examples-main/cli/jobs/basics/src/hello-sweep.py":::

さらに対応するスイープ ジョブを作成します。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-sweep.yml":::

さらに次を実行します。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_sweep":::

### <a name="data-inputs"></a>データ入力

データ入力は、ジョブ コンピューティングのローカル ファイル システム上のパスに解決されます。 `https://azuremlexamples.blob.core.windows.net/datasets/iris.csv` で BLOB コンテナーでパブリックにホストされている従来の Iris データセットを使用してデモを行います。

Iris CSV ファイルへのパスを引数として受け取り、データフレームに読み込み、最初の 5 行を出力して `outputs` ディレクトリに保存する Python スクリプトを作成できます。

:::code language="python" source="~/azureml-examples-main/cli/jobs/basics/src/hello-iris.py":::

Azure Storage URI の入力を指定できます。この入力は、ローカル ファイル システムにデータをマウントまたはダウンロードします。 1 つのファイルを指定できます。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-file.yml":::

次に、以下を実行します。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="iris_file":::

または、フォルダー全体を指定します。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-folder.yml":::

次に、以下を実行します。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="iris_folder":::

#### <a name="private-data"></a>プライベート データ

データストアを介して Azure Machine Learning に接続されている Azure Blob Storage または Azure Data Lake Storage のプライベート データの場合は、入力データに対して `azureml://datastores/<DATASTORE_NAME>/paths/<PATH_TO_DATA>` 形式の Azure Machine Learning URI を使用できます。 たとえば、Iris CSV を、`workspaceblobstore` という名前のデータストアに対応する BLOB コンテナー内の `/example-data/` という名前のディレクトリにアップロードする場合は、データストア内のこのファイルを使用する前のジョブを変更できます。

> [!WARNING]
> Iris CSV を `workspaceblobstore` 内の同じ場所にコピーしていない場合、これらのジョブの実行は失敗します。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-datastore-file.yml":::

または、ディレクトリ全体を次に示します。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-datastore-folder.yml":::

### <a name="default-outputs"></a>既定の出力

`./outputs` と `./logs` のディレクトリは、Azure Machine Learning によって特別に扱われます。 ジョブ中にこれらのディレクトリにファイルを書き込むと、これらのファイルがジョブにアップロードされるため、完了した後も引き続きこれらにアクセスできるようになります。 `./outputs` フォルダーは、ジョブの最後にアップロードされます。`./logs` に書き込まれたファイルは、リアルタイムでアップロードされます。 TensorBoard ログなど、ジョブ中にログをストリーム配信する場合は、後者を使用します。

`stdout` に出力するのではなく、既定の出力ディレクトリ内のファイルに出力するように、"hello world" ジョブを変更できます。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-output.yml":::

このジョブは、次のように実行できます。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_output":::

ログをダウンロードします。`helloworld.txt` は、`<RUN_ID>/outputs/` ディレクトリ内に配置されます。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_output_download":::

### <a name="data-outputs"></a>データ出力

名前付きデータ出力を指定できます。 これにより、既定で読み取り/書き込みマウントされる既定のデータストアにディレクトリが作成されます。

以前の "hello world" ジョブを変更して、名前付きデータ出力に書き込む場合があります。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-output-data.yml":::

## <a name="hello-pipelines"></a>Hello パイプライン

パイプライン ジョブでは、複数のジョブを並列または順に実行できます。 パイプライン内のステップ間に入出力の依存関係がある場合は、他のステップが完了した後に依存ステップが実行されます。

"hello world" ジョブは、次の 2 つのジョブに分割できます。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline.yml":::

さらに次を実行します。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_pipeline":::

コンピューティング先に使用可能なリソースがある場合、"hello" ジョブと "world" ジョブはそれぞれ並列で実行されます。

パイプライン内のステップ間でデータを渡す場合は、"hello" ジョブのデータ出力と、前の出力を参照する "world" ジョブでの対応する入力を定義します。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-io.yml":::

さらに次を実行します。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_pipeline_io":::

今回は、"hello" ジョブの完了後に "world" ジョブが実行されます。

パイプライン内のジョブ間で共通設定が重複しないようにするには、ジョブの外部で設定できます。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-settings.yml":::

次を実行できます。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_pipeline_settings":::

個々のジョブに対応する設定によって、パイプライン ジョブの共通設定がオーバーライドされます。 ここまでの概念は、ジョブ "A"、"B"、および "C" を含む 3 段階のパイプライン ジョブに組み合わせることができます。 "C" ジョブは "B" ジョブにデータ依存関係を持ち、"A" ジョブは個別に実行できます。 "A" ジョブでは、個別に設定された環境を使用し、その入力の 1 つをトップ レベルのパイプライン ジョブ入力にバインドします。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-abc.yml":::

次を実行できます。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_pipeline_abc":::

## <a name="train-a-model"></a>モデルをトレーニングする

この時点で、モデルはまだトレーニングされていません。 MLflow 追跡を使用して Python スクリプトにいくつかの `sklearn` コードを追加し、Iris CSV でモデルをトレーニングします。

:::code language="python" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/src/main.py":::

scikit-learn フレームワークは、自動ログ記録のために MLflow でサポートされています。そのため、スクリプト内の 1 回の `mlflow.autolog()` 呼び出しで、すべてのモデル パラメーター、トレーニング メトリック、モデル成果物、およびいくつかの追加成果物 (この場合は混同行列の画像) がログに記録されます。

これをクラウドで実行するには、次をジョブとして指定します。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/job.yml":::

さらに次を実行します。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="sklearn_iris":::

モデルを登録するには、出力をダウンロードし、ローカル ディレクトリからモデルを作成します。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="sklearn_download_register_model":::

## <a name="sweep-hyperparameters"></a>ハイパーパラメーターをスイープする

前のジョブをハイパーパラメーターをスイープするように変更できます。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/job-sweep.yml":::

さらに次を実行します。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="sklearn_sweep":::

> [!TIP]
> 進行状況を監視し、パラメーター グラフを表示するには、スタジオの [子実行] タブを確認します。

その他のスイープ オプションについては、[スイープ ジョブの YAML 構文リファレンス](reference-yaml-job-sweep.md)を参照してください。

## <a name="distributed-training"></a>分散トレーニング

Azure Machine Learning では、PyTorch、TensorFlow、MPI ベースの分散トレーニングがサポートされています。 詳細については、[コマンド ジョブ YAML 構文リファレンスの分散セクション](reference-yaml-job-command.md#distribution-configurations)を参照してください。

たとえば、分散 PyTorch を使用して、CIFAR-10 データセットで自力ニューラル ネットワーク (CNN) をトレーニングできます。 完全なスクリプトは、[サンプル リポジトリ](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/single-step/pytorch/cifar-distributed)で入手できます。

`torchvision` の CIFAR-10 データセットは、`cifar-10-batches-py` ディレクトリを含むディレクトリを入力として受け取る必要があります。 zip 形式のソースをダウンロードし、ローカル ディレクトリに抽出できます。

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/create-datasets.sh" id="download_untar_cifar":::

次に、ローカル ディレクトリから Azure Machine Learning データセットを作成します。このデータセットは、既定のデータストアにアップロードされます。

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/create-datasets.sh" id="create_cifar":::

必要に応じて、そのローカル ファイルとディレクトリを削除します。

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/create-datasets.sh" id="cleanup_cifar":::

データセット (ファイルのみ) は、データ入力の `dataset` キーを使用してジョブで参照できます。 形式は `azureml:<DATASET_NAME>:<DATASET_VERSION>` なので、作成されたばかりの CIFAR-10 データセットの場合は、`azureml:cifar-10-example:1` です。

データセットを配置すると、分散 PyTorch ジョブを作成してモデルをトレーニングできます。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/pytorch/cifar-distributed/job.yml":::

さらに次を実行します。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="pytorch_cifar":::

## <a name="build-a-training-pipeline"></a>トレーニング パイプラインを構築する

上記の CIFAR-10 の例は、パイプライン ジョブにうまく変換されます。 前のジョブは、パイプラインでオーケストレーションを行う 3 つのジョブに分割できます。

- "get-data" によって、`cifar-10-batches-py` をダウンロードして抽出するための Bash スクリプトを実行する
- 分散 PyTorch を使用してデータを取得し、モデルをトレーニングする "train-model"
- データとトレーニング済みのモデルを取得し、精度を評価する "eval-model"

"train-model" と "eval-model" はどちらも、"get-data" ジョブの出力に依存します。 さらに、"eval-model" は "train-model" ジョブの出力に依存します。 したがって、3 つのジョブは順番に実行されます。

パイプライン ジョブ内で、次の 3 つのジョブを調整できます。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines/cifar-10/job.yml":::

次に、以下を実行します。

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="pipeline_cifar":::

Pipelines は、再利用可能なコンポーネントを使用して記述することもできます。 詳細については、[Azure Machine Learning CLI (プレビュー) を使用してコンポーネントベースの機械学習パイプラインを作成して実行する](how-to-create-component-pipelines-cli.md)方法に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

- [マネージド オンライン エンドポイントを使用して機械学習モデルをデプロイおよびスコアリングする (プレビュー)](how-to-deploy-managed-online-endpoints.md)
