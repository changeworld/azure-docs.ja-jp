---
title: CLI (v2) スイープ ジョブ YAML スキーマ
titleSuffix: Azure Machine Learning
description: CLI (v2) スイープ ジョブ YAML スキーマのリファレンス ドキュメント。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: reference
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 90a69fceb96067ad92cc4d68f8b3e0929d2622a6
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135102"
---
# <a name="cli-v2-sweep-job-yaml-schema"></a>CLI (v2) スイープ ジョブ YAML スキーマ

ソース JSON スキーマは https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json にあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 構文

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | YAML スキーマ。 Azure Machine Learning 用 VS Code 拡張機能を使用して YAML ファイルを作成する場合は、ファイルの先頭に `$schema` を含めることで、スキーマとリソースの入力候補を呼び出すことができます。 | | |
| `type` | const | **必須。** ジョブの種類。 | `sweep` | `sweep` |
| `name` | string | ジョブの名前。 ワークスペース内のすべてのジョブで一意である必要があります。 省略した場合、Azure ML によって名前の GUID が自動生成されます。 | | |
| `display_name` | string | スタジオ UI でのジョブの表示名。 ワークスペース内で一意ではない場合があります。 省略した場合、Azure ML により、表示名として、人間が判読できる形容詞と名詞の識別子が自動生成されます。 | | |
| `experiment_name` | string | ジョブを整理するための実験名。 各ジョブの実行レコードは、スタジオの [実験] タブの対応する実験の下に整理されます。省略した場合、Azure ML により、既定で、ジョブが作成された作業ディレクトリの名前が設定されます。 | | |
| `description` | string | ジョブの説明。 | | |
| `tags` | object | ジョブのタグの辞書。 | | |
| `sampling_algorithm` | string | **必須。** `search_space` で使用するハイパーパラメーター サンプリング アルゴリズム。 | `random`, `grid`, `bayesian` | |
| `search_space` | object | **必須。** ハイパーパラメーター検索空間の辞書。 キーはハイパーパラメーターの名前で、値はパラメーター式です。 <br><br> ハイパーパラメーターは、`${{ search_space.<hyperparameter> }}` 式を使用して `trial.command` で参照できます。 | | |
| `search_space.<hyperparameter>` | object | 使用できる式のセットについては、「[パラメーター式](#parameter-expressions)」を参照してください。 | | |
| `objective.primary_metric` | string | **必須。** 各トライアル ジョブによって報告されるプライマリ メトリックの名前。 メトリックは、同じ対応するメトリック名を持つ `mlflow.log_metric()` を使用して、ユーザーのトレーニング スクリプトに記録する必要があります。 | | |
| `objective.goal` | string | **必須。** `objective.primary_metric` の最適化の目標。 | `maximize`, `minimize` | |
| `early_termination` | object | 使用する早期終了ポリシー。 指定したポリシーの条件が満たされると、トライアル ジョブはキャンセルされます。 省略した場合、早期終了ポリシーは適用されません。 [BanditPolicy](#banditpolicy)、[MedianStoppingPolicy](#medianstoppingpolicy)、または [TruncationSelectionPolicy](#truncationselectionpolicy) のいずれか。 | | |
| `limits` | object | スイープ ジョブの制限。 「[`limits` キーの属性](#attributes-of-the-limits-key)」を参照してください。 | | |
| `compute` | string | **必須。** `azureml:<compute_name>` 構文を使用してジョブを実行するコンピューティング先の名前。 | | |
| `trial` | object | **必須。** 各トライアルのジョブ テンプレート。 各トライアル ジョブには、システムが `search_space` からサンプリングするハイパーパラメーター値のさまざまな組み合わせが提供されます。 「[`trial` キーの属性](#attributes-of-the-trial-key)」を参照してください。 | | |
| `inputs` | object | ジョブへの入力の辞書。 キーは、ジョブのコンテキスト内の入力の名前であり、値は入力値です。 <br><br> 入力は、`${{ inputs.<input_name> }}` 式を使用して `trial.command` で参照できます。 | | |
| `inputs.<input_name>` | 数値、整数、ブール値、文字列、またはオブジェクト | リテラル値 (number、integer、boolean、または string 型)、[JobInputUri](#jobinputuri)、または [JobInputDataset](#jobinputdataset) のいずれか。 | | |
| `outputs` | object | ジョブの出力構成の辞書。 キーはジョブのコンテキスト内の出力の名前であり、値は出力構成です。 <br><br> 出力は、`${{ outputs.<output_name> }}` 式を使用して `trial.command` で参照できます。 | |
| `outputs.<output_name>` | object | 省略可能な `mode` を指定するか、オブジェクトを空のままにしておくことができます。 `outputs` 辞書に指定された名前付き出力ごとに、Azure ML により、出力の場所が自動生成されます。 | |
| `outputs.<output_name>.mode` | string | 出力ファイルを宛先ストレージに配信する方法のモード。 読み取り/書き込みマウント モードの場合、出力ディレクトリはマウントされたディレクトリになります。 アップロード モードの場合、出力ディレクトリに書き込まれたファイルがジョブの最後にアップロードされます。 | `rw_mount`, `upload` | `rw_mount` |

### <a name="early-termination-policies"></a>早期終了ポリシー

#### <a name="banditpolicy"></a>BanditPolicy

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **必須。** ポリシーの種類。 | `bandit` | |
| `slack_factor` | number | 最高パフォーマンスのトライアルからの許容される距離を計算するために使用される比率。 **`slack_factor` または `slack_amount` のいずれかが必須です。** | | |
| `slack_amount` | number | 最高パフォーマンスのトライアルからの許容される絶対距離。 **`slack_factor` または `slack_amount` のいずれかが必須です。** | | |
| `evaluation_interval` | 整数 (integer) | ポリシーを適用する頻度。 | | `1` |
| `delay_evaluation` | 整数 (integer) | 最初のポリシー評価を遅延する間隔の数。 指定した場合、`delay_evaluation` 以上の `evaluation_interval` の倍数ごとにポリシーが適用されます。 | | `0` |

#### <a name="medianstoppingpolicy"></a>MedianStoppingPolicy

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **必須。** ポリシーの種類。 | `median_stopping` | |
| `evaluation_interval` | 整数 (integer) | ポリシーを適用する頻度。 | | `1` |
| `delay_evaluation` | 整数 (integer) | 最初のポリシー評価を遅延する間隔の数。 指定した場合、`delay_evaluation` 以上の `evaluation_interval` の倍数ごとにポリシーが適用されます。 | | `0` |

#### <a name="truncationselectionpolicy"></a>TruncationSelectionPolicy

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **必須。** ポリシーの種類。 | `truncation_selection` | |
| `truncation_percentage` | 整数 (integer) | **必須。** 各評価期間にキャンセルされるトライアル ジョブの割合。 | | |
| `evaluation_interval` | 整数 (integer) | ポリシーを適用する頻度。 | | `1` |
| `delay_evaluation` | 整数 (integer) | 最初のポリシー評価を遅延する間隔の数。 指定した場合、`delay_evaluation` 以上の `evaluation_interval` の倍数ごとにポリシーが適用されます。 | | `0` |

### <a name="parameter-expressions"></a>パラメーター式

#### <a name="choice"></a>choice

| キー | Type | 説明 | 使用できる値 |
| --- | ---- | ----------- | -------------- |
| `type` | const | **必須。** 式の型。 | `choice` |
| `values` | array | **必須。** 選択する不連続値の一覧。 | |

#### <a name="randint"></a>randint

| キー | Type | 説明 | 使用できる値 |
| --- | ---- | ----------- | -------------- |
| `type` | const | **必須。** 式の型。 | `randint` |
| `upper` | 整数 (integer) | **必須。** 整数の範囲の排他的な上限。 | |

#### <a name="qlognormal-qnormal"></a>qlognormal、qnormal

| キー | Type | 説明 | 使用できる値 |
| --- | ---- | ----------- | -------------- |
| `type` | const | **必須。** 式の型。 | `qlognormal`, `qnormal` |
| `mu` | number | **必須。** 正規分布の平均。 | |
| `sigma` | number | **必須。** 正規分布の標準偏差。 | |
| `q` | 整数 (integer) | **必須。** スムージング係数。 | |

#### <a name="qloguniform-quniform"></a>qloguniform、quniform

| キー | Type | 説明 | 使用できる値 |
| --- | ---- | ----------- | -------------- |
| `type` | const | **必須。** 式の型。 | `qloguniform`, `quniform` |
| `min_value` | number | **必須。** 範囲内の最小値 (包含的)。 | |
| `max_value` | number | **必須。** 範囲内の最大値 (包含的)。 | |
| `q` | 整数 (integer) | **必須。** スムージング係数。 | |

#### <a name="lognormal-normal"></a>lognormal、normal

| キー | Type | 説明 | 使用できる値 |
| --- | ---- | ----------- | -------------- |
| `type` | const | **必須。** 式の型。 | `lognormal`, `normal` |
| `mu` | number | **必須。** 正規分布の平均。 | |
| `sigma` | number | **必須。** 正規分布の標準偏差。 | |

#### <a name="loguniform"></a>loguniform

| キー | Type | 説明 | 使用できる値 |
| --- | ---- | ----------- | -------------- |
| `type` | const | **必須。** 式の型。 | `loguniform` |
| `min_value` | number | **必須。** 範囲内の最小値は `exp(min_value)` (包含的) になります。 | |
| `max_value` | number | **必須。** 範囲内の最大値は `exp(max_value)` (包含的) になります。 | |

#### <a name="uniform"></a>uniform

| キー | Type | 説明 | 使用できる値 |
| --- | ---- | ----------- | -------------- |
| `type` | const | **必須。** 式の型。 | `uniform` |
| `min_value` | number | **必須。** 範囲内の最小値 (包含的)。 | |
| `max_value` | number | **必須。** 範囲内の最大値 (包含的)。 | |

### <a name="attributes-of-the-limits-key"></a>`limits` キーの属性

| キー | Type | 説明 | 既定値 |
| --- | ---- | ----------- | ------------- |
| `max_total_trials` | 整数 (integer) | ジョブを実行できる最大時間 (秒単位)。 この制限に達すると、システムによってジョブがキャンセルされます。 | `1000` |
| `max_concurrent_trials` | 整数 (integer) | | 既定値は `max_total_trials` です。 |
| `timeout` | 整数 (integer) | スイープ ジョブ全体を実行できる最大時間 (分単位)。 この制限に達すると、システムにより、すべてのトライアルを含めてスイープ ジョブがキャンセルされます。 | `10080` |
| `trial_timeout` | 整数 (integer) | 各トライアル ジョブを実行できる最大時間 (秒単位)。 この制限に達すると、システムによってトライアルがキャンセルされます。 | |

### <a name="attributes-of-the-trial-key"></a>`trial` キーの属性

| キー | Type | 説明 | 既定値 |
| --- | ---- | ----------- | ------------- |
| `command` | string | **必須。** 実行するコマンドです。 | |
| `code.local_path` | string | アップロードしてジョブに使用するソース コード ディレクトリへのローカル パス。 | |
| `environment` | 文字列またはオブジェクト | **必須。** ジョブに使用する環境。 これは、ワークスペース内のバージョン管理される既存の環境への参照、またはインライン環境仕様のいずれかになります。 <br> <br> 既存の環境を参照するには、`azureml:<environment-name>:<environment-version>` 構文を使用します。 <br><br> 環境のインラインを定義するには、[環境スキーマ](reference-yaml-environment.md#yaml-syntax)に従います。 `name` プロパティと `version` プロパティは、インライン環境ではサポートされていないため、除外します。 | |
| `environment_variables` | object | コマンドが実行されるプロセスに設定する環境変数の名前と値のペアの辞書。 | |
| `distribution` | object | 分散トレーニング シナリオの配布構成。 [MpiConfiguration](#mpiconfiguration)、[PyTorchConfiguration](#pytorchconfiguration)、[TensorFlowConfiguration](#tensorflowconfiguration) のいずれか。 | |
| `resources.instance_count` | 整数 (integer) | ジョブに使用するノードの数。 | `1` |

#### <a name="distribution-configurations"></a>配布構成

##### <a name="mpiconfiguration"></a>MpiConfiguration

| キー | Type | 説明 | 使用できる値 |
| --- | ---- | ----------- | -------------- |
| `type` | const | **必須。** 配布の種類。  | `mpi` |
| `process_count_per_instance` | 整数 (integer) | **必須。** ジョブに対して起動するノードあたりのプロセスの数。  | |

##### <a name="pytorchconfiguration"></a>PyTorchConfiguration

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **必須。** 配布の種類。  | `pytorch` | |
| `process_count_per_instance` | 整数 (integer) | ジョブに対して起動するノードあたりのプロセスの数。 | |  `1` |

##### <a name="tensorflowconfiguration"></a>TensorFlowConfiguration

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **必須。** 配布の種類。  | `tensorflow` |
| `worker_count` | 整数 (integer) | ジョブに対して起動するワーカーの数。 | | 既定値は `resources.instance_count` です。 |
| `parameter_server_count` | 整数 (integer) | ジョブに対して起動するパラメーター サーバーの数。 | | `0` |

### <a name="job-inputs"></a>ジョブの入力

#### <a name="jobinputuri"></a>JobInputUri

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `file` | string | 入力として使用する 1 つのファイルへの URI。 サポートされる URI の種類は `azureml`、`https`、`wasbs`、`abfss`、`adl` です。 `azureml://` URI 形式の使用方法の詳細については、[コア yaml 構文](reference-yaml-core-syntax.md)に関するページを参照してください。 **`file` または `folder` のいずれかが必須です。**  | | |
| `folder` | string | 入力として使用するフォルダーの URI。 サポートされる URI の種類は `azureml`、`wasbs`、`abfss`、`adl` です。 `azureml://` URI 形式の使用方法の詳細については、[コア yaml 構文](reference-yaml-core-syntax.md)に関するページを参照してください。 **`file` または `folder` のいずれかが必須です。**   | | |
| `mode` | string | コンピューティング先にデータを配信する方法のモード。 読み取り専用マウントと読み取り/書き込みマウントの場合、データはマウント パスとして使用されます。 フォルダーはフォルダーとしてマウントされ、ファイルはファイルとしてマウントされます。 ダウンロード モードの場合、データはダウンロードされたパスとして使用されます。 | `ro_mount`, `rw_mount`, `download` | `ro_mount` |

#### <a name="jobinputdataset"></a>JobInputDataset

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `dataset` | 文字列またはオブジェクト | **必須。** 入力として使用するデータセット。 これは、ワークスペース内のバージョン管理される既存のデータセットへの参照、またはインライン データセット仕様のいずれかです。 <br><br> 既存のデータセットを参照するには、`azureml:<dataset_name>:<dataset_version>` 構文を使用します。 <br><br> データセットをインラインで定義するには、[データセット スキーマ](reference-yaml-dataset.md#yaml-syntax)に従います。 `name` プロパティおよび `version` プロパティはインライン データセットではサポートされていないので、除外します。 | | |
| `mode` | string | データセットをコンピューティング先に配信する方法のモード。 読み取り専用マウントの場合、データセットはマウント パスとして使用されます。 フォルダーはフォルダーとしてマウントされ、ファイルは親フォルダーとしてマウントされます。 ダウンロード モードの場合、データセットはダウンロードされたパスとして使用されます。 | `ro_mount`, `download` | `ro_mount` |

## <a name="remarks"></a>解説

`az ml job` コマンドは、Azure Machine Learning ジョブを管理するために使用できます。

## <a name="examples"></a>例

例は、[例の GitHub リポジトリ](https://github.com/Azure/azureml-examples/tree/main/cli/jobs)内にあります。 以下にいくつか示します。

## <a name="yaml-hello-sweep"></a>YAML: hello sweep

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-sweep.yml":::

## <a name="yaml-basic-python-model-hyperparameter-tuning"></a>YAML: 基本的な Python モデルのハイパーパラメーター調整

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/job-sweep.yml":::

## <a name="next-steps"></a>次のステップ

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)
