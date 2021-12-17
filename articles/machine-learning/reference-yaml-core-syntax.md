---
title: CLI (v2) コア YAML 構文
titleSuffix: Azure Machine Learning
description: CLI (v2) コア YAML 構文の概要。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 943de3f7327c02e4bdb20bb364f530685bce2965
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565672"
---
# <a name="cli-v2-core-yaml-syntax"></a>CLI (v2) コア YAML 構文

すべての Azure Machine Learning エンティティには、スキーマ化された YAML 表現があります。 `.yml` 拡張子または `.yaml` 拡張子を持つ YAML 構成ファイルから新しいエンティティを作成できます。

この記事では、これらの YAML ファイルを構成するときに生じるコア構文の概念の概要について説明します。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="referencing-an-azure-ml-entity"></a>Azure ML エンティティの参照

Azure MLでは、YAML ファイルを構成するときに既存の Azure ML エンティティを参照するための参照構文 (短い形式と長い形式で構成) が提供されます。 たとえば、ワークスペース内の既存の登録済み環境を参照して、ジョブの環境で使用できます。

### <a name="shorthand"></a>短縮形

短い構文は、次で構成されます。

* アセットの場合: `azureml:<asset-name>:<asset-version>`
* リソースの場合: `azureml:<resource-name>`

Azure ML は、ワークスペース内の指定されたアセットまたはリソースにこの参照を解決します。

### <a name="longhand"></a>Longhand

longhand 構文は、`azureml:` プレフィックスにエンティティの ARM リソース ID を加えたもので構成されます。

```
azureml:/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/environments/<environment-name>/versions/<environment-version>
```

## <a name="azure-ml-data-reference-uri"></a>Azure ML データ参照 URI

Azure ML では、Azure ストレージ サービス内のデータを指す便利なデータ参照 URI 形式を使用できます。 これは、YAML ファイルでクラウド ストレージの場所を指定する必要があるシナリオで使用できます。たとえば、ストレージ内のファイルから Azure ML モデルを作成したり、ジョブに入力として渡すデータを指したりする場合に使用できます。

このデータ URI 形式を使用するには、参照するストレージ サービスを最初にワークスペースにデータストアとして登録する必要があります。 Azure ML は、データストアの作成時に指定した資格情報を使用してデータ アクセスを処理します。

形式は、現在のワークスペースのデータストアと、指すファイルまたはフォルダーへのデータストアのパスで構成されます。

```
azureml://datastores/<datastore-name>/paths/<path-on-datastore>/
```

次に例を示します。

* `azureml://datastores/workspaceblobstore/paths/example-data/`
* `azureml://datastores/workspaceblobstore/paths/example-data/iris.csv`

Azure ML データ参照 URI に加えて、Azure ML では、パブリック `http` や `https` URI だけではなく、`https`、`wasbs`、`abfss`、および `adl` などのダイレクト ストレージ URI プロトコルもサポートされています。

## <a name="expression-syntax-for-configuring-azure-ml-jobs-and-components"></a>Azure ML ジョブとコンポーネントを構成するための式の構文

v2 ジョブとコンポーネントの YAML ファイルを使用すると、式を使用してさまざまなシナリオのコンテキストにバインドできます。 重要な使用例として、構成の作成時には認識されていない可能性があるが、実行時に解決する必要が生じる値に式を使用する場合を挙げられます。

次の構文を使用して、Azure ML に、式を文字列として扱うのではなく、式を評価するように指示します。

`${{ <expression> }}`

サポートされているシナリオについて、以下で説明します。

### <a name="parameterizing-the-command-with-the-inputs-and-outputs-contexts-of-a-job"></a>ジョブの `inputs` コンテキストと `outputs` コンテキストで `command` をパラメーター化する

ジョブへの入力として、リテラル値、URI パス、および Azure ML データセットを指定できます。 その後、`${{inputs.<input-name>}}` 構文を使用して、それらの入力への参照を使用し、`command` をパラメーター化できます。 リテラル入力への参照は実行時にリテラル値に解決されます。一方、データ URI または Azure ML データセットの入力への参照は、(指定された `mode` に応じて) ダウンロード パスまたはマウント パスに解決されます。

同様に、ジョブへの出力は、`command` でも参照できます。 `outputs` 辞書に指定された名前付き出力ごとに、Azure ML は、ファイルを書き込むことができる既定のデータストア上に出力の場所を自動生成します。 名前付き出力の出力場所は、テンプレート化されたパス `<default-datastore>/azureml/<job-name>/<output-name>/` に基づいて決定されます。 `${{outputs.<output-name>}}` 構文を使用して `command` をパラメーター化すると、自動生成されたパスにその参照が解決されます。これにより、スクリプトでジョブからその場所にファイルを書き込めるようになります。

コマンド ジョブ YAML ファイルの次の例では、`command` は、リテラル入力と URI 入力という 2 つの入力と、1 つの出力でパラメーター化されます。 実行時に、`${{inputs.learning_rate}}` 式は `0.01` に解決され、`${{inputs.iris}}` 式は `iris.csv` ファイルのダウンロード パスに解決されます。 `${{outputs.model_dir}}` は、自動生成された出力場所のマウント パスに解決されます。

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/commandJob.schema.json
code:
  local_path: ./src
command: python train.py --lr ${{inputs.learning_rate}} --training-data ${{inputs.iris}} --model-dir ${{outputs.model_dir}}
environment: azureml:AzureML-Minimal:1
compute: azureml:cpu-cluster
inputs:
  learning_rate: 0.01
  iris:
    file: https://azuremlexamples.blob.core.windows.net/datasets/iris.csv
    mode: download
outputs:
  model_dir:
```

### <a name="parameterizing-the-command-with-the-search_space-context-of-a-sweep-job"></a>スイープ ジョブの `search_space` コンテキストで `command` をパラメーター化する

また、スイープ ジョブでハイパーパラメーターのチューニングを実行する場合も、ジョブの作成中にハイパーパラメーターの実際の値が認識されないため、この式構文を使用します。 スイープ ジョブを実行すると、Azure ML では、`search_space` に基づいて各試用版のハイパーパラメーター値が選択されます。 トレーニング スクリプト内のこれらの値にアクセスするには、スクリプトのコマンド ライン引数を使用してそれらを渡す必要があります。 これを行うには、`trial.command` で `${{search_space.<hyperparameter>}}` 構文を使用します。

スイープ ジョブ YAML ファイルの次の例では、`trial.command` の `${{search_space.learning_rate}}` および `${{search_space.boosting}}` 参照は、試用ジョブが実行のために送信される際に、各試用版で選択された実際のハイパーパラメーター値に解決されます。

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json
type: sweep
sampling_algorithm: random
search_space:
  learning_rate:
    type: uniform
    min_value: 0.01
    max_value: 0.9
  boosting:
    type: choice
    values: ["gbdt", "dart"]
objective:
  goal: minimize
  primary_metric: test-multi_logloss
trial:
  code: 
    local_path: src 
  command: >-
    python train.py 
    --training-data ${{inputs.iris}}
    --lr ${{search_space.learning_rate}}
    --boosting ${{search_space.boosting}}
  environment: azureml:AzureML-Minimal:1
inputs:
  iris:
    file: https://azuremlexamples.blob.core.windows.net/datasets/iris.csv
    mode: download
compute: azureml:cpu-cluster
```

### <a name="binding-inputs-and-outputs-between-steps-in-a-pipeline-job"></a>パイプライン ジョブのステップ間で入力と出力をバインドする

式は、パイプライン ジョブのステップ間で入力と出力をバインドするためにも使用されます。 たとえば、パイプライン内の 1 つのジョブ (job #2) の入力を別のジョブ (job #1) の出力にバインドできます。 このように使用すると、パイプライン グラフ内の依存関係フローが Azure ML にシグナル送信され、job #2 は job #1 の後に実行されるようになります。これは、job #1 の出力が job #2 の入力として必要であるからです。

パイプライン ジョブ YAML ファイルの場合、それぞれの子ジョブの `inputs` セクションと `outputs` セクションは、親コンテキスト (トップ レベルのパイプライン ジョブ) 内で評価されます。 一方、`command` は現在のコンテキスト (子ジョブ) に解決されます。

パイプライン ジョブで入力と出力をバインドする方法は 2 つあります。

**1) パイプライン ジョブの上位の入力と出力にバインドする**

構文 `${{inputs.<input-name>}}` または `${{outputs.<output-name>}}` を使用して、子ジョブの入力または出力を、上位の親パイプライン ジョブの入力/出力にバインドできます。 この参照は親コンテキスト、つまり、上位の入力/出力に解決されます。 

次の例では、最後の `train` ステップの出力 (`model_dir`) が、`${{outputs.trained_model}}` を介して上位パイプライン ジョブの出力にバインドされています。

**2) 別の子ジョブ (ステップ) の入力と出力にバインドする**

あるステップの入力/出力を別のステップの入力/出力にバインドするには、`${{jobs.<step-name>.inputs.<input-name>}}` または `${{jobs.<step-name>.outputs.<outputs-name>}}` の構文を使用します。 ここでも、この参照は親コンテキストに解決されるため、コンテキストは `jobs.<step-name>` で始まります。

次の例では、`train` ステップの入力 (`clean_data`) が、`${{jobs.prep.outputs.prep_data}}` を介して `prep` ステップの出力 (`prep_data`) にバインドされています。 `prep` ステップから用意されたデータは、`train` ステップのトレーニング データとして使用されます。

一方、`command` プロパティ内のコンテキスト参照は現在のコンテキストに解決されます。 たとえば、`prep` ステップの `command` の `${{inputs.raw_data}}` 参照は、`prep` 子ジョブである現在のコンテキストの入力に解決されます。 `prep.inputs` に対して検索が行われるため、`raw_data` という名前の入力をここで定義する必要があります。

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/pipelineJob.schema.json
type: pipeline
inputs:
outputs:
  trained_model:
jobs:
  prep:
    type: command
    inputs:
      raw_data:
        folder:
        mode: rw_mount
    outputs:
      prep_data: 
        mode: upload
    code:
      local_path: src/prep
    environment: azureml:AzureML-Minimal:1
    command: >-
      python prep.py 
      --raw-data ${{inputs.raw_data}} 
      --prep-data ${{outputs.prep_data}}
    compute: azureml:cpu-cluster
  train:
    type: command
    inputs: 
      clean_data: ${{jobs.prep.outputs.prep_data}}
    outputs:
      model_dir: $${{outputs.trained_model}}
    code: 
      local_path: src/train
    environment: azureml:AzureML-Minimal:1
    compute: azureml:gpu-cluster
    command: >-
      python train.py 
      --training-data ${{inputs.clean_data}} 
      --model-output ${{outputs.model_dir}}
```

### <a name="parameterizing-the-command-with-the-inputs-and-outputs-contexts-of-a-component"></a>コンポーネントの `inputs` コンテキストと `outputs` コンテキストで `command` をパラメーター化する

ジョブの `command` と同様に、コンポーネントの `command` も `inputs` コンテキストと `outputs` コンテキストへの参照を使用してパラメーター化することができます。 この場合、参照はコンポーネントの入力と出力に対する参照です。 コンポーネントがジョブで実行されている場合、Azure ML は、それぞれのコンポーネントの入力と出力に指定されたジョブ ランタイムの入力値と出力値に参照を解決します。 コマンド コンポーネント YAML 仕様のコンテキスト構文の使用例を次に示します。

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/commandComponent.schema.json
type: command
code:
  local_path: ./src
command: python train.py --lr ${{inputs.learning_rate}} --training-data ${{inputs.iris}} --model-dir ${{outputs.model_dir}}
environment: azureml:AzureML-Minimal:1
inputs:
  learning_rate:
    type: number
    default: 0.01
    optional: true
  iris:
    type: path
outputs:
  model_dir:
    type: path
```

## <a name="next-steps"></a>次の手順

* [CLI (v2) をインストールして使用する](how-to-configure-cli.md)
* [CLI (v2) を使用してモデルをトレーニングする](how-to-train-cli.md)
* [CLI (v2) YAML スキーマ](reference-yaml-overview.md)
