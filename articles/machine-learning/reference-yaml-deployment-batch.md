---
title: CLI (v2) バッチ デプロイ YAML スキーマ
titleSuffix: Azure Machine Learning
description: CLI (v2) バッチ デプロイ YAML スキーマのリファレンス ドキュメント。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: tracychms
ms.author: tracych
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 53e83f76e61038bc1fb95bfba653b18f63febede
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054507"
---
# <a name="cli-v2-batch-deployment-yaml-schema"></a>CLI (v2) バッチ デプロイ YAML スキーマ

ソース JSON スキーマは https://azuremlschemas.azureedge.net/latest/batchDeployment.schema.json にあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 構文

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | YAML スキーマ。 Azure Machine Learning 用 VS Code 拡張機能を使用して YAML ファイルを作成する場合は、ファイルの先頭に `$schema` を含めることで、スキーマとリソースの入力候補を呼び出すことができます。 | | |
| `name` | string | **必須。** デプロイの名前。 | | |
| `description` | string | デプロイの説明。 | | |
| `tags` | object | デプロイのタグの辞書。 | | |
| `endpoint_name` | string | **必須。** デプロイの作成先となるエンドポイントの名前。 | | |
| `model` | 文字列またはオブジェクト | **必須。** デプロイに使用するモデル。 この値は、ワークスペース内の既存のバージョン管理されたモデルへの参照またはインライン モデルの仕様のいずれかです。 <br><br> 既存のモデルを参照するには、`azureml:<model-name>:<model-version>` 構文を使用します。 <br><br> モデルをインラインで定義するには、[モデル スキーマ](reference-yaml-model.md#yaml-syntax)に従います。 <br><br> 運用シナリオのベスト プラクティスとして、モデルを個別に作成し、ここで参照するようにしてください。 | | |
| `code_configuration` | object | スコアリング コード ロジックの構成。 <br><br> モデルが MLflow 形式の場合、このプロパティは必要ありません。 | | |
| `code_configuration.code.local_path` | string | モデルのスコアリングに使用するソース コード ディレクトリへのローカル パス。 | | |
| `code_configuration.scoring_script` | string | ソース コード ディレクトリ内のスコアリング ファイルへの相対パス。 | | |
| `environment` | 文字列またはオブジェクト | デプロイに使用する環境。 この値は、ワークスペース内の既存のバージョン管理された環境への参照、またはインライン環境仕様のいずれかになります。 <br><br> モデルが MLflow 形式の場合、このプロパティは必要ありません。 <br><br> 既存の環境を参照するには、`azureml:<environment-name>:<environment-version>` 構文を使用します。 <br><br> 環境をインラインで定義するには、[環境スキーマ](reference-yaml-environment.md#yaml-syntax)に従います。 <br><br> 運用シナリオのベスト プラクティスとして、環境を別途作成し、ここで参照することをお勧めします。 | | |
| `compute` | string | **必須。** バッチ スコアリング ジョブを実行するコンピューティング ターゲットの名前。 この値は、`azureml:<compute-name>` 構文を使用する、ワークスペース内の既存のコンピューティングへの参照である必要があります。 | | |
| `resources.instance_count` | 整数 (integer) | 各バッチ スコアリング ジョブに使用するノードの数。 | | `1` |
| `max_concurrency_per_instance` | 整数 (integer) | インスタンスあたりの `scoring_script` の並列実行の最大数。 | | `1` |
| `error_threshold` | 整数 (integer) | 無視するファイル エラーの数。 入力全体でのエラーの数がこの値を超えると、ジョブは終了します。 `error_threshold` は、個々のミニバッチではなく、入力全体を対象としています。 省略した場合、ジョブを終了せずに、任意の数のファイル エラーが許可されます。  | | `-1` |
| `logging_level` | string | ログの詳細レベル。 | `warning`, `info`, `debug` | `info` |
| `mini_batch_size` | 整数 (integer) | `code_configuration.scoring_script` が 1 回の `run()` 呼び出しで処理できるファイルの数。 | | `10` |
| `retry_settings` | object | 各ミニバッチのスコアリングの設定を再試行します。 | | |
| `retry_settings.max_retries` | 整数 (integer) | 失敗した、またはタイムアウトになったミニバッチの最大再試行回数。 | | `3` |
| `retry_settings.timeout` | 整数 (integer) | ミニ バッチのスコアリングに対するタイムアウト (秒単位)。 | | `30` |
| `output_action` | string | 出力ファイルで出力がどのように整理されるかを示します。 | `append_row`, `summary_only` | `append_row` |
| `output_file_name` | string | バッチ スコアリングの出力ファイルの名前。 | | `predictions.csv` |
| `environment_variables` | object | バッチ スコアリング ジョブごとに設定される環境変数の名前と値のペアの辞書。 | | |

## <a name="remarks"></a>Remarks

`az ml batch-deployment` コマンドを使用して、Azure Machine Learning バッチ デプロイを管理できます。

## <a name="examples"></a>例

例は、[GitHub リポジトリの例](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/batch)にあります。 以下にいくつか示します。

## <a name="yaml-basic-mlflow"></a>YAML: 基本 (MLflow)

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/mlflow-deployment.yml":::

## <a name="yaml-custom-model-and-scoring-code"></a>YAML: カスタム モデルおよびスコアリング コード

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/nonmlflow-deployment.yml":::

## <a name="next-steps"></a>次の手順

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)
