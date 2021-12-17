---
title: CLI (v2) コマンド ジョブ YAML スキーマ
titleSuffix: Azure Machine Learning
description: CLI (v2) コマンド ジョブ YAML スキーマのリファレンス ドキュメント。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: ecfd6d57d7f978e64bed0003253a05941effa910
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060966"
---
# <a name="cli-v2-command-job-yaml-schema"></a>CLI (v2) コマンド ジョブ YAML スキーマ

ソース JSON スキーマは https://azuremlschemas.azureedge.net/latest/commandJob.schema.json にあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 構文

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | YAML スキーマ。 Azure Machine Learning 用 VS Code 拡張機能を使用して YAML ファイルを作成する場合は、ファイルの先頭に `$schema` を含めることで、スキーマとリソースの入力候補を呼び出すことができます。 | | |
| `type` | const | ジョブの種類。 | `command` | `command` |
| `name` | string | ジョブの名前。 ワークスペース内のすべてのジョブで一意である必要があります。 省略した場合、Azure ML によって名前の GUID が自動生成されます。 | | |
| `display_name` | string | スタジオ UI でのジョブの表示名。 ワークスペース内では一意ではない場合があります。 省略した場合、Azure ML では、表示名の人間が判読できる形容詞と名詞の識別子が自動生成されます。 | | |
| `experiment_name` | string | ジョブを整理するための実験名。 各ジョブの実行レコードは、スタジオの [実験] タブの対応する実験の下に整理されます。省略した場合、Azure ML は既定で、ジョブが作成された作業ディレクトリの名前を設定します。 | | |
| `description` | string | ジョブの説明。 | | |
| `tags` | object | ジョブのタグの辞書。 | | |
| `command` | string | **必須。** 実行するコマンドです。 | | |
| `code.local_path` | string | アップロードしてジョブに使用するソース コード ディレクトリへのローカル パス。 | | |
| `environment` | 文字列またはオブジェクト | **必須。** ジョブに使用する環境です。 これは、ワークスペース内の既存のバージョン管理された環境への参照、またはインライン環境仕様のいずれかになります。 <br><br> 既存の環境を参照するには、`azureml:<environment_name>:<environment_version>` 構文を使用します。 <br><br> 環境のインラインを定義するには、[環境スキーマ](reference-yaml-environment.md#yaml-syntax)に従ってください。 `name` プロパティと `version` プロパティは、インライン環境ではサポートされていないため、除外します。 | | |
| `environment_variables` | object | コマンドが実行されるプロセスに設定する環境変数の名前と値のペアのディクショナリ。 | | |
| `distribution` | object | 分散トレーニング シナリオの配布構成。 [MpiConfiguration](#mpiconfiguration)、[PyTorchConfiguration](#pytorchconfiguration)、[TensorFlowConfiguration](#tensorflowconfiguration) のいずれか。 | | |
| `compute` | string | ジョブを実行するコンピューティング先の名前。 これは、ワークスペース内の既存のコンピューティングへの参照 (`azureml:<compute_name>` 構文を使用) または `local` でローカル実行を指定する場合があります。 | | `local` |
| `resources.instance_count` | 整数 (integer) | ジョブに使用するノードの数。 | | `1` |
| `limits.timeout` | 整数 (integer) | ジョブの実行が許可される最大時間 (秒単位)。 この制限に達すると、システムによってジョブが取り消されます。 | | |
| `inputs` | object | ジョブへの入力のディクショナリ。 キーは、ジョブのコンテキスト内の入力の名前であり、値は入力値です。 <br><br> 入力は、`command` 式を使用して `${{ inputs.<input_name> }}` で参照できます。 | | |
| `inputs.<input_name>` | 数値、整数、ブール値、文字列、またはオブジェクト | リテラル値 (number、integer、boolean、または string 型)、[JobInputUri](#jobinputuri)、または [JobInputDataset](#jobinputdataset) のいずれか。 | | |
| `outputs` | object | ジョブの出力構成のディクショナリ。 キーはジョブのコンテキスト内の出力の名前であり、値は出力構成です。 <br><br> 出力は、`command` 式を使用して `${{ outputs.<output_name> }}` で参照できます。 | |
| `outputs.<output_name>` | object | 省略可能な `mode` を指定するか、オブジェクトを空のままにします。 `outputs` 辞書に指定された名前付き出力ごとに、Azure ML は出力の場所を自動生成します。 | |
| `outputs.<output_name>.mode` | string | 出力ファイルを宛先ストレージに配信する方法のモード。 読み取り/書き込みマウント モードでは、出力ディレクトリはマウントされたディレクトリになります。 アップロード モードの場合、出力ディレクトリに書き込まれたファイルがジョブの最後にアップロードされます。 | `rw_mount`, `upload` | `rw_mount` |

### <a name="distribution-configurations"></a>配布構成

#### <a name="mpiconfiguration"></a>MpiConfiguration

| キー | Type | 説明 | 使用できる値 |
| --- | ---- | ----------- | -------------- |
| `type` | const | **必須。** ディストリビューションの種類。  | `mpi` |
| `process_count_per_instance` | 整数 (integer) | **必須。** ジョブに対して起動するノードあたりのプロセスの数。  | |

#### <a name="pytorchconfiguration"></a>PyTorchConfiguration

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **必須。** ディストリビューションの種類。  | `pytorch` | |
| `process_count_per_instance` | 整数 (integer) | ジョブに対して起動するノードあたりのプロセスの数。 | |  `1` |

#### <a name="tensorflowconfiguration"></a>TensorFlowConfiguration

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **必須。** ディストリビューションの種類。  | `tensorflow` |
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
| `dataset` | 文字列またはオブジェクト | **必須。** 入力として使用するデータセット。 これは、ワークスペース内の既存のバージョン管理されたデータセットへの参照またはインライン データセットの仕様のいずれかです。 <br><br> 既存のデータセットを参照するには、`azureml:<dataset_name>:<dataset_version>` 構文を使用します。 <br><br> データセットをインラインで定義するには、[データセット スキーマ](reference-yaml-dataset.md#yaml-syntax)に従います。 `name` プロパティおよび `version` プロパティはインライン データセットではサポートされていないので、除外します。 | | |
| `mode` | string | データセットをコンピューティング先に配信する方法のモード。 読み取り専用マウントの場合、データセットはマウント パスとして使用されます。 フォルダーはフォルダーとしてマウントされ、ファイルは親フォルダーとしてマウントされます。 ダウンロード モードの場合、データセットはダウンロードされたパスとして使用されます。 | `ro_mount`, `download` | `ro_mount` |

## <a name="remarks"></a>解説

`az ml job` コマンドは、Azure Machine Learning ジョブを管理するために使用できます。

## <a name="examples"></a>例

例は、[GitHub リポジトリの例](https://github.com/Azure/azureml-examples/tree/main/cli/jobs)にあります。 以下にいくつか示します。

## <a name="yaml-hello-world"></a>YAML: hello world

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world.yml":::

## <a name="yaml-display-name-experiment-name-description-and-tags"></a>YAML: 表示名、実験名、説明、タグ

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-org.yml":::

## <a name="yaml-environment-variables"></a>YAML: 環境変数

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-env-var.yml":::

## <a name="yaml-source-code"></a>YAML: ソース コード

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-code.yml":::

## <a name="yaml-literal-inputs"></a>YAML: リテラル入力

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-input.yml":::

## <a name="yaml-write-to-default-outputs"></a>YAML: 既定の出力への書き込み

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-output.yml":::

## <a name="yaml-write-to-named-data-output"></a>YAML: 名前付きデータ出力に書き込む

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-output-data.yml":::

## <a name="yaml-datastore-uri-file-input"></a>YAML: データストア URI ファイルの入力

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-datastore-file.yml":::

## <a name="yaml-datastore-uri-folder-input"></a>YAML: データストア URI フォルダーの入力

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-datastore-folder.yml":::

## <a name="yaml-uri-file-input"></a>YAML: URI ファイルの入力

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-file.yml":::

## <a name="yaml-uri-folder-input"></a>YAML: URI フォルダーの入力

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-folder.yml":::

## <a name="yaml-notebook-via-papermill"></a>YAML: 紙を使用したノートブック

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-notebook.yml":::

## <a name="yaml-basic-python-model-training"></a>YAML: 基本的な Python モデル トレーニング

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/job.yml":::

## <a name="yaml-basic-r-model-training-with-local-docker-build-context"></a>YAML: ローカル Docker ビルド コンテキストを使用した基本的な R モデル トレーニング

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/r/iris/job.yml":::

## <a name="yaml-distributed-pytorch"></a>YAML: 分散 PyTorch

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/pytorch/cifar-distributed/job.yml":::

## <a name="yaml-distributed-tensorflow"></a>YAML: 分散 TensorFlow

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/tensorflow/mnist-distributed/job.yml":::

## <a name="yaml-distributed-mpi"></a>YAML: 分散 MPI

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/tensorflow/mnist-distributed-horovod/job.yml":::

## <a name="next-steps"></a>次の手順

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)
