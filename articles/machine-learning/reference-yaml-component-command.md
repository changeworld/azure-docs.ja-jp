---
title: CLI (v2) コマンド コンポーネント YAML スキーマ
titleSuffix: Azure Machine Learning
description: CLI (v2) コマンド コンポーネント YAML スキーマのリファレンス ドキュメント。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 2a512226a507d890963ee9e407c7eb8330e33fc5
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552320"
---
# <a name="cli-v2-command-component-yaml-schema"></a>CLI (v2) コマンド コンポーネント YAML スキーマ

ソース JSON スキーマは https://azuremlschemas.azureedge.net/latest/commandComponent.schema.json にあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 構文

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | YAML スキーマ。 Azure Machine Learning 用 VS Code 拡張機能を使用して YAML ファイルを作成する場合は、ファイルの先頭に `$schema` を含めることで、スキーマとリソースの入力候補を呼び出すことができます。 | | |
| `type` | const | コンポーネントの種類。 | `command` | `command` |
| `name` | string | **必須。** コンポーネント名。 | | |
| `version` | string | コンポーネントのバージョン。 省略した場合、Azure ML によってバージョンが自動生成されます。 | | |
| `display_name` | string | Studio UI でのコンポーネントの表示名。 ワークスペース内で一意ではない場合があります。 | | |
| `description` | string | コンポーネントの説明。 | | |
| `tags` | object | コンポーネントのタグの辞書。 | | |
| `command` | string | **必須。** 実行するコマンドです。 | | |
| `code.local_path` | string | アップロードしてコンポーネントに使用するソース コード ディレクトリへのローカル パス。 | | |
| `environment` | 文字列またはオブジェクト | **必須。** コンポーネントに使用する環境。 この値は、ワークスペース内の既存のバージョン管理された環境への参照、またはインライン環境仕様のいずれかになります。 <br><br> 既存の環境を参照するには、`azureml:<environment-name>:<environment-version>` 構文を使用します。 <br><br> 環境をインラインで定義するには、[環境スキーマ](reference-yaml-environment.md#yaml-syntax)に従います。 `name` プロパティと `version` プロパティは、インライン環境ではサポートされていないため、除外します。 | | |
| `distribution` | object | 分散トレーニング シナリオの配布構成。 [MpiConfiguration](#mpiconfiguration)、[PyTorchConfiguration](#pytorchconfiguration)、[TensorFlowConfiguration](#tensorflowconfiguration) のいずれかになります。 | | |
| `resources.instance_count` | 整数 (integer) | ジョブに使用するノードの数。 | | `1` |
| `inputs` | object | コンポーネント入力の辞書。 キーはコンポーネントのコンテキスト内の入力の名前であり、値はコンポーネントの入力定義です。 <br><br> 入力は、`${{ inputs.<input_name> }}` 式を使用して `command` で参照できます。 | | |
| `inputs.<input_name>` | object | コンポーネントの入力定義。 構成可能なプロパティのセットについては、[コンポーネントの入力](#component-input)を参照してください。 | | |
| `outputs` | object | コンポーネント出力の辞書。 キーはコンポーネントのコンテキスト内の出力の名前であり、値はコンポーネントの出力定義です。 <br><br> 出力は、`${{ outputs.<output_name> }}` 式を使用して `command` で参照できます。 | |
| `outputs.<output_name>` | object | コンポーネントの出力定義。 構成可能なプロパティのセットについては、[コンポーネントの出力](#component-output)を参照してください。 | |

### <a name="distribution-configurations"></a>配布構成

#### <a name="mpiconfiguration"></a>MpiConfiguration

| キー | Type | 説明 | 使用できる値 |
| --- | ---- | ----------- | -------------- |
| `type` | const | **必須。** 配布の種類。  | `mpi` |
| `process_count_per_instance` | 整数 (integer) | **必須。** ジョブに対して起動するノードあたりのプロセスの数。  | |

#### <a name="pytorchconfiguration"></a>PyTorchConfiguration

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **必須。** 配布の種類。  | `pytorch` | |
| `process_count_per_instance` | 整数 (integer) | ジョブに対して起動するノードあたりのプロセスの数。 | |  `1` |

#### <a name="tensorflowconfiguration"></a>TensorFlowConfiguration

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **必須。** 配布の種類。  | `tensorflow` |
| `worker_count` | 整数 (integer) | ジョブに対して起動するワーカーの数。 | | 既定値は `resources.instance_count` です。 |
| `parameter_server_count` | 整数 (integer) | ジョブに対して起動するパラメーター サーバーの数。 | | `0` |

### <a name="component-input"></a>コンポーネント入力

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | string | **必須。** コンポーネント入力の種類。 <br><br> コンポーネントの実行時に、ランタイム ジョブ入力の値をデータの URI または Azure ML データセットにする場合は、`type: path` を使用します。 | `number`, `integer`, `boolean`, `string`, `path` | |
| `description` | string | 入力の説明。 | | |
| `default` | number、integer、boolean、または string | 入力の既定値。 | | |
| `optional` | boolean | 入力が必須かどうか。 | | `false` |
| `min` | integer または number | 入力に許容される最小値。 このフィールドは、`type` フィールドが `number` または `integer` の場合にのみ指定できます。 | |
| `max` | integer または number | 入力に許容される最大値。 このフィールドは、`type` フィールドが `number` または `integer` の場合にのみ指定できます。 | |
| `enum` | array | 入力に使用できる値のリスト。 `type` フィールドが `boolean` の場合は適用されません。  | |

### <a name="component-output"></a>コンポーネント出力

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | string | **必須。** コンポーネント出力の種類。 | `path` | |
| `description` | string | 出力の説明。 | | |

## <a name="remarks"></a>Remarks

`az ml component` コマンドを使用して、Azure Machine Learning コンポーネントを管理できます。

## <a name="examples"></a>例

コマンド コンポーネントの例は、GitHub リポジトリの例にあります。 以下に選択可能な例を示します。

例は、[GitHub リポジトリの例](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/pipelines-with-components)にあります。 以下にいくつか示します。

## <a name="hello-world-command-component"></a>Hello world コマンド コンポーネント

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/basics/2a_basic_component/component.yml":::

## <a name="next-steps"></a>次の手順

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)
