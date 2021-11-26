---
title: コンポーネントベースの ML パイプラインを作成して実行する (CLI)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning CLI を使用して機械学習パイプラインを作成して実行します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devplatv2
ms.openlocfilehash: 7dafb069419c1ff42d2ec5358fbd8eb37465c88f
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552343"
---
# <a name="create-and-run-machine-learning-pipelines-using-components-with-the-azure-machine-learning-cli-preview"></a>Azure Machine Learning CLI でコンポーネントを使用して機械学習パイプラインを作成して実行する (プレビュー)

この記事では、Azure CLI とコンポーネント (詳細については、[Azure Machine Learning コンポーネントの概要](concept-component.md)に関する記事を参照) を使用して[機械学習パイプライン](concept-ml-pipelines.md)を作成して実行する方法について説明します。 [コンポーネントを使用せずにパイプラインを作成](how-to-train-cli.md#build-a-training-pipeline)することはできますが、コンポーネントによって最大限の柔軟性と再利用が提供されます。 AzureML パイプラインは、YAML に定義して CLI から実行したり、Python で作成したり、ドラッグアンドドロップ UI を使って AzureML Studio Designer で作成したりできます。 このドキュメントでは、CLI に焦点を当てています。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://azure.microsoft.com/free/) をお試しください。

* パイプラインと関連リソース用の [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)が必要です

* [Machine Learning 用の Azure CLI 拡張機能をインストールして設定する](how-to-configure-cli.md)

* examples リポジトリをクローンします。

    ```azurecli-interactive
    git clone https://github.com/Azure/azureml-examples --depth 1
    cd azureml-examples/cli/jobs/pipelines-with-components/
    ```

## <a name="introducing-machine-learning-pipelines"></a>機械学習パイプラインの導入

AzureML のパイプラインを使用すると、機械学習タスクのコレクションをシーケンス処理してワークフローを作成できます。 データ科学者は通常、データの準備、トレーニング、スコアリングなど、個々のタスクに焦点を合わせたスクリプトで反復処理を行います。 これらのすべてのスクリプトの準備が整ったら、パイプラインを使用して、そのようなスクリプトのコレクションを次のような運用品質のプロセスに接続できます。

| 特長 | 説明 |
| --- | --- |
| 自己完結型 | パイプラインは、手動介入なしに、アップストリーム データを取得し、それを処理して、後続のスクリプトに渡すことにより、自己完結型の方法で数時間、または数日間にわたって実行できます。 |
| 強力 | パイプラインは、大規模なデータセットを高速処理したり、何千ものスイープを実行して最適なモデルを見つけたりする処理能力を備えた、クラウドでホストされた大規模なコンピューティング クラスターで実行できます。 | 
| 反復可能で自動化可能 | 新しいデータの実行と処理を行ったり、ML モデルを更新したりするようにパイプラインをスケジュールし、ML ワークフローを反復可能にできます。 | 
| 再現可能 | パイプラインは、すべてのアクティビティをログに記録し、中間データを含むすべての出力をクラウドに保持することで、再現可能な結果を生成でき、コンプライアンスと監査の要件を満たすのに役立ちます。 |

Azure には、他の種類のパイプラインもあります。Azure Data Factory パイプラインでは、データ間パイプラインが強力にサポートされますが、Azure Pipelines は CI/CD オートメーションに最良の選択です。 [Machine Learning パイプラインをこれらの別のパイプラインと比較します](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use)。

## <a name="create-your-first-pipeline"></a>最初のパイプラインの作成

[`azureml-examples` リポジトリ](https://github.com/Azure/azureml-examples)の `cli/jobs/pipelines-with-components/basics` ディレクトリから、`3a_basic_pipeline` サブディレクトリに移動します (そのディレクトリ内の前の例では、非コンポーネント パイプラインが示されています)。 次のコマンドを使用して、使用可能なコンピューティング リソースを一覧表示します。 

```azurecli
az ml compute list
```

ない場合は、次を実行して `cpu-cluster` という名前のクラスターを作成します。

```azurecli
az ml compute create -n cpu-cluster --type amlcompute --min-instances 0 --max-instances 10
```

次に、次のコマンドを使用してパイプライン ジョブを作成します。

```azurecli
az ml job create --file pipeline.yml
```

次のようなパイプライン ジョブに関する情報を含む JSON ディクショナリを受け取ります。

| Key | 説明 | 
| --- | --- | 
| `name` | ジョブの GUID ベースの名前。 | 
| `experiment_name` | Studio でジョブが整理されるときに使用される名前。 | 
| `services.Studio.endpoint` | パイプライン ジョブを監視および確認するための URL。 | 
| `status` | ジョブの状態。 この時点では、これは `Preparing` になる可能性が高くなります。 |

### <a name="review-a-component"></a>コンポーネントを確認する 

 `componentA_src`、`componentB_src`、および `componentC_src` の Python ソース コードを簡単に見てみましょう。 ご覧のように、これらの各ディレクトリには、若干異なる Python の "Hello World" プログラムが含まれています。 

`ComponentA.yaml` を開き、最初のコンポーネントがどのように定義されているのかを確認します。 

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/basics/3a_basic_pipeline/componentA.yml":::

現在のプレビューでは、`command` の種類のコンポーネントだけがサポートされています。 `name` は一意の識別子で、Studio でコンポーネントを記述するために使用され、`display_name` はフレンドリ名を指定するために使用されます。 `version` キーと値のペアを使用すると、以前のバージョンでの再現性を維持しながら、パイプライン コンポーネントを進化させることができます。 

`code.local_path` 値内のすべてのファイルは、処理するために Azure にアップロードされます。 

`environment` セクションを使用すると、コンポーネントが実行されるソフトウェア環境を指定できます。 この場合、コンポーネントでは、`environment.image` に指定されている基本の Docker イメージが使用されます。 詳細については、「[Azure Machine Learning でソフトウェア環境を作成して使用する](how-to-use-environments.md)」を参照してください。 

最後に、`command` キーを使用して、実行するコマンドを指定します。

> [!NOTE]
> `command` の値は、`>-` で始まります。これは、YAML の "ブロック チョンピング付きの折り畳みスタイル" です。 これを使用すると、複数のテキスト行にわたるコマンドを明確に記述できます。

コンポーネントとその仕様の詳細については、[Azure Machine Learning コンポーネントの概要](concept-component.md)に関するページを参照してください。

### <a name="review-the-pipeline-specification"></a>パイプラインの仕様を確認する

サンプル ディレクトリで、`pipeline.yaml` ファイルは次のコードのようになります。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/basics/3a_basic_pipeline/pipeline.yml":::

Studio でジョブの URL (ジョブ作成時の `job create` コマンド、またはジョブ作成後の `job show` からの `services.Studio.endpoint` の値) を開くと、パイプラインのグラフ表現が表示されます。

:::image type="content" source="media/how-to-create-component-pipelines-cli/pipeline-graph.png" lightbox="media/how-to-create-component-pipelines-cli/pipeline-graph.png" alt-text="Studio でのパイプラインのグラフ表現":::

このパイプライン内のコンポーネント間に依存関係はありません。 一般に、パイプラインには依存関係があり、このページにそれらが視覚的に表示されます。 これらのコンポーネントは互いに依存しておらず、また `cpu-cluster` には十分なノードがあったため、それらは同時に実行されました。 

パイプライン グラフ内のコンポーネントをダブルクリックすると、コンポーネントの子の実行に関する詳細を確認できます。 

:::image type="content" source="media/how-to-create-component-pipelines-cli/component-details.png" alt-text="コンポーネントの子の実行に関する詳細と出力を示すスクリーンショット" lightbox="media/how-to-create-component-pipelines-cli/component-details.png"::: 

## <a name="upload-and-use-data"></a>データをアップロードして使用する

この例 `3b_pipeline_with_data` では、パイプラインで入力と出力データフローとストレージを定義する方法を示します。 

パイプラインの入力データ ディレクトリは、`inputs` パスを使用してパイプラインの YAML ファイルに定義します。 出力と中間データ ディレクトリは、`outputs` パスを使用して定義します。 次の図に示すように、これらの定義は `jobs.<JOB_NAME>.inputs` と `jobs.<JOB_NAME>.outputs` パスで使用します。

:::image type="content" source="media/how-to-create-component-pipelines-cli/inputs-and-outputs.png" alt-text="入力と出力パスがどのようにジョブの入力と出力パスにマップされるかを示す画像" lightbox="media/how-to-create-component-pipelines-cli/inputs-and-outputs.png":::

1. `inputs.pipeline_sample_input_data` パスによってキー識別子が作成され、`local_path` ディレクトリから入力データがアップロードされます。 そして、この識別子 `${{inputs.pipeline_sample_input_data}}` が `jobs.componentA_job.inputs.componentA_input` キーの値として使用されます。 
1. `jobs.componentA_job.outputs.componentA_output` パスによって識別子 (`${{jobs.componentA_job.outputs.componentA_output`}}) が作成され、これが次のステップの `jobs.componentB_job.inputs.componentB_input` キーの値として使用されます。 
1. コンポーネント A と同様に、コンポーネント B の出力がコンポーネント C への入力として使用されます。
1. パイプラインの `outputs.final_pipeline_output` キーは、`jobs.componentC_job.outputs.componentC_output` キーの値として使用される識別子のソースです。 つまり、コンポーネント C の出力がパイプラインの最終的な出力です。

このパイプラインを Studio で視覚化すると次のようになります。 

:::image type="content" source="media/how-to-create-component-pipelines-cli/pipeline-graph-dependencies.png" alt-text="データの依存関係があるパイプラインの Studio のグラフ ビューを示すスクリーンショット" lightbox="media/how-to-create-component-pipelines-cli/pipeline-graph-dependencies.png":::

`inputs.pipeline_sample_input_data` が `Dataset` として表されているのを確認できます。 `jobs.<COMPONENT_NAME>.inputs` と `outputs` パスのキーは、パイプライン コンポーネント間のデータ フローとして表示されます。

この例を実行するには、サンプル リポジトリの `3b_pipeline_with_data` サブディレクトリに切り替えて次を実行します。

`az ml job create --file pipeline.yaml`

### <a name="access-data-in-your-script"></a>スクリプトのデータにアクセスする

コンポーネントの入力および出力ディレクトリ パスは、引数としてスクリプトに渡されます。 引数の名前は、YAML ファイルで `inputs` または `outputs` パスに指定したキーになります。 次に例を示します。

```python
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--componentA_input", type=str)
parser.add_argument("--componentA_output", type=str)

args = parser.parse_args()

print("componentA_input path: %s" % args.componentA_input)
print("componentA_output path: %s" % args.componentA_output)

```

入力の場合、パイプライン オーケストレーターは、クラウド ストアからデータをダウンロード (またはマウント) し、各ジョブで実行されるスクリプトの読み取り元ローカル フォルダーとして利用できるようにします。 この動作は、ローカルで実行する場合とクラウド コンピューティングで実行する場合で、スクリプトでは何の変更も必要ないことを意味します。 同様に、出力の場合、スクリプトの書き込みは、マウントされてクラウド ストアと同期されるか、スクリプトの完了後にアップロードされるローカル フォルダーに対して行われます。 `mode` キーワードを使用して、入力に対してダウンロードとマウント、出力に対してアップロードとマウントを指定できます。 

## <a name="create-a-preparation-train-evaluate-pipeline"></a>準備 - トレーニング - 評価のパイプラインを作成する

機械学習パイプラインの一般的なシナリオの 1 つには、次の 3 つの主要なフェーズがあります。

1. データの準備
1. トレーニング
1. モデルの評価

これらの各フェーズには、複数のコンポーネントがある場合があります。 たとえば、データ準備ステップには、トレーニング データの読み込みと変換を行う別個のステップがある場合があります。 サンプル リポジトリには、`cli/jobs/pipelines-with-components/nyc_taxi_data_regression` ディレクトリにエンドツーエンドのサンプル パイプラインが含まれています。 

`job.yml` は、必須である `type: pipeline` のキーと値のペアで始まります。 次に、次のように入力と出力が定義されます。

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/nyc_taxi_data_regression/job.yml" range="5-22":::

前に説明したように、これらのエントリでは、パイプラインへの入力データ (この場合、`./data` 内のデータセット) と、別のパスに保存される、パイプラインの中間および最終出力が指定されます。 これらの入力と出力エントリ内の名前は、個々のジョブの `inputs` と `outputs` エントリ内の値になります。 

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/nyc_taxi_data_regression/job.yml" range="26-72":::

次の図に示すように、`jobs.train_job.outputs.model_output` がどのように、予測ジョブとスコアリング ジョブの両方に対する入力として使用されるのかに注意してください。 

:::image type="content" source="media/how-to-create-component-pipelines-cli/regression-graph.png" alt-text="NYC タクシー料金予測タスクのパイプライン グラフ" lightbox="media/how-to-create-component-pipelines-cli/regression-graph.png":::

## <a name="caching--reuse"></a>キャッシュと再利用  

既定では、入力が変更されたコンポーネントだけが再実行されます。 この動作を変更するには、コンポーネント仕様 YAML の `is_deterministic` キーを `False` に設定します。 一般的にこれが必要なのは、固定の場所または URL から更新されている可能性があるデータを読み込むコンポーネントです。 

## <a name="faq"></a>よく寄せられる質問

### <a name="how-do-i-change-the-location-of-the-outputs-generated-by-the-pipeline"></a>パイプラインによって生成された出力の場所を変更するにはどうすればいいですか?
パイプライン ジョブの `settings` セクションを使用すると、パイプライン内のすべてのジョブに対して別のデータストアを指定できます ([こちらの例](https://github.com/Azure/azureml-examples/blob/main/cli/jobs/pipelines-with-components/basics/1a_e2e_local_components/pipeline.yml)の 25 - 26 行目を参照)。 特定のジョブまたは特定の出力に対して別のデータストアを指定することは、現在サポートされていません。 データストア上で出力が保存されるパスを指定することも、現在サポートされていません。

### <a name="how-do-i-specify-a-compute-that-can-be-used-by-all-jobs"></a>すべてのジョブで使用できるコンピューティングを指定するにはどうすればいいですか?
パイプライン ジョブ レベルでコンピューティングを指定できます。これかが、明示的にコンピューティングを指定していないジョブで使用されます。 ([こちらの例](https://github.com/Azure/azureml-examples/blob/main/cli/jobs/pipelines-with-components/basics/1a_e2e_local_components/pipeline.yml)の 28 行目を参照)。

### <a name="what-job-types-are-supported-in-the-pipeline-job"></a>パイプライン ジョブではどのようなジョブの種類がサポートされていますか?
現在のリリースでは、コマンド、コンポーネント、スイープ ジョブの種類がサポートされています。

### <a name="what-are-the-different-modes-that-i-use-with-inputs-or-outputs"></a>入力または出力で使用するモードにはどのようなものがありますか?
| カテゴリ | 許可されるモード | Default |
| --- | --- | --- |
| データセットの入力 | `ro_mount` および `download` | `ro_mount` |
| URI の入力 | `ro_mount`、`rw_mount`、`download` | `ro_mount` | 
| 出力 | `rw_mount`, `upload` | `rw_mount` | 

### <a name="when-do-i-use-command-jobs-vs-component-jobs"></a>コマンド ジョブとコンポーネント ジョブはどのようなときに使用しますか?
コマンド ジョブでは素早く反復処理を行い、それらを一緒にパイプラインに接続できます。 ただし、これにより、パイプラインはモノリシックになります。 パイプラインのステップの 1 つを別のパイプラインで使用する必要がある場合は、ジョブ定義、スクリプト、環境などをコピーする必要があります。 個々のステップをパイプライン間で再利用したり、チーム内の他のユーザーが簡単に理解して使用できるようにしたりしたい場合は、作成と登録を行うための追加ステップが必要になります。 コンポーネントの使用を検討するもう 1 つの理由は、ドラッグ アンド ドロップ デザイナー UI を使用してパイプラインを作成できることです。 ジョブはワークスペースに登録されていないため、デザイナー キャンバスにドラッグ アンド ドロップすることはできません。

### <a name="im-doing-distributed-training-in-my-component-the-component-which-is-registered-specifies-distributed-training-settings-including-node-count-how-can-i-change-the-number-of-nodes-used-during-runtime-the-optimal-number-of-nodes-is-best-determined-at-runtime-so-i-dont-want-to-update-the-component-and-register-a-new-version"></a>コンポーネントで分散トレーニングを行っています。 登録されているコンポーネントでは、ノード数を含む分散トレーニング設定が指定されています。 実行時に使用されるノード数を変更するにはどうすればいいですか? 最適なノード数の決定は実行時に行うのが最良なので、コンポーネントを更新して新しいバージョンを登録したくありません。

コンポーネント ジョブの [上書き] セクションを使用すると、リソースと分散の設定を変更できます。 [TensorFlow を使用したこちらの例](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/pipelines-with-components/basics/6a_tf_hello_world)または [PyTorch を使用したこちらの例](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/pipelines-with-components/basics/6c_pytorch_hello_world)を参照してください。  

### <a name="how-can-i-define-an-environment-with-conda-dependencies-inside-a-component"></a>コンポーネント内に Conda 依存関係を含む環境を定義するにはどうすればいいですか?
[こちらの例](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/pipelines-with-components/basics/5c_env_conda_file)を参照してください。
 

## <a name="next-steps"></a>次のステップ

- パイプラインを仕事仲間や顧客と共有するには、[機械学習パイプラインの発行](how-to-deploy-pipelines.md)に関する記事をご覧ください
- [GitHub 上のこれらの Jupyter notebook](https://aka.ms/aml-pipeline-readme) を使用して、機械学習パイプラインをさらに調べます
- [azureml-pipelines-core](/python/api/azureml-pipeline-core/) パッケージおよび [azureml-pipelines-steps](/python/api/azureml-pipeline-steps/) パッケージの SDK リファレンス ヘルプを参照します
- パイプラインのデバッグとトラブルシューティングのヒントについては、「[機械学習パイプラインのトラブルシューティング](how-to-debug-pipelines.md)」を参照してください。
- ノートブックの実行方法については、[Jupyter Notebook を使用してこのサービスを探索する方法](samples-notebooks.md)に関するページを参照してください。
