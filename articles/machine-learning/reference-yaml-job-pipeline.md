---
title: CLI (v2) パイプライン ジョブの YAML スキーマ
titleSuffix: Azure Machine Learning
description: CLI (v2) パイプライン ジョブの YAML スキーマのリファレンス ドキュメント。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 93c8f11fbfab8386e1083985399d8138e2b2b7d6
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057083"
---
# <a name="cli-v2-pipeline-job-yaml-schema"></a>CLI (v2) パイプライン ジョブの YAML スキーマ

ソース JSON スキーマは https://azuremlschemas.azureedge.net/latest/pipelineJob.schema.json にあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 構文

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | YAML スキーマ。 Azure Machine Learning 用 VS Code 拡張機能を使用して YAML ファイルを作成する場合は、ファイルの先頭に `$schema` を含めることで、スキーマとリソースの入力候補を呼び出すことができます。 | | |
| `type` | const | **必須。** ジョブの種類。 | `pipeline` | |
| `name` | string | ジョブの名前。 ワークスペース内のすべてのジョブで一意である必要があります。 省略した場合、Azure ML によって名前の GUID が自動生成されます。 | | |
| `display_name` | string | スタジオ UI でのジョブの表示名。 ワークスペース内では一意ではない場合があります。 省略した場合、Azure ML では、表示名の人間が判読できる形容詞と名詞の識別子が自動生成されます。 | | |
| `experiment_name` | string | ジョブを整理するための実験名。 各ジョブの実行レコードは、スタジオの [実験] タブの対応する実験の下に整理されます。省略した場合、Azure ML は既定で、ジョブが作成された作業ディレクトリの名前を設定します。 | | |
| `description` | string | ジョブの説明。 | | |
| `tags` | object | ジョブのタグの辞書。 | | |
| `settings` | object | パイプライン ジョブの既定の設定。 構成可能なプロパティのセットについては、[`settings` キーの属性](#attributes-of-the-settings-key)に関する記事を参照してください。 | | |
| `jobs` | object | **必須。** パイプライン内のステップとして実行する個々のジョブのセットの辞書。 これらのジョブは、親パイプライン ジョブの子ジョブと見なされます。 <br><br> キーは、パイプライン ジョブのコンテキスト内のステップの名前です。 この名前は、子ジョブの一意のジョブ名とは異なります。 値は、[コマンド ジョブ スキーマ](reference-yaml-job-command.md#yaml-syntax)または[コンポーネント ジョブ スキーマ](reference-yaml-job-component.md#yaml-syntax)に従うジョブ仕様です。 現在、パイプラインで実行できるのは、コマンド コンポーネントを実行しているコマンド ジョブとコンポーネント ジョブのみです。 今後のリリースでは、他の種類のジョブがサポートされます。 | | |
| `inputs` | object | パイプライン ジョブへの入力の辞書。 キーは、ジョブのコンテキスト内の入力の名前であり、値は入力値です。 <br><br> これらのパイプライン入力は、`${{ inputs.<input_name> }}` 式を使用して、パイプライン内の個々のステップ ジョブの入力によって参照できます。 | | |
| `inputs.<input_name>` | number、integer、boolean、string、または object | リテラル値 (number、integer、boolean、または string 型)、[JobInputUri](#jobinputuri)、または [JobInputDataset](#jobinputdataset) のいずれか。 | | |
| `outputs` | object | パイプライン ジョブの出力構成の辞書。 キーはジョブのコンテキスト内の出力の名前であり、値は出力構成です。 <br><br> これらのパイプライン出力は、`${{ outputs.<output_name> }}` 式を使用して、パイプライン内の個々のステップ ジョブの出力によって参照できます。 | |
| `outputs.<output_name>` | object | 省略可能な `mode` を指定するか、オブジェクトを空のままにします。 `outputs` 辞書に指定された名前付き出力ごとに、Azure ML はテンプレート化されたパス (`{default-datastore}/azureml/{job-name}/{output-name}/`) に基づいて出力の場所を自動生成します。 ユーザーは、後のリリースでカスタムの場所を指定できます。 | |
| `outputs.<output_name>.mode` | string | 出力ファイルを宛先ストレージに配信する方法のモード。 読み取り/書き込みマウント モードでは、出力ディレクトリはマウントされたディレクトリになります。 アップロード モードの場合、出力ディレクトリに書き込まれたファイルがジョブの最後にアップロードされます。 | `rw_mount`, `upload` | `rw_mount` |
| `compute` | string | 個々のステップにコンピューティングが指定されていない場合にステップ ジョブを実行する既定のコンピューティング先の名前。 この値は、`azureml:<compute-name>` 構文を使用してワークスペース内の既存のコンピューティングへの参照である必要があります。 |

### <a name="attributes-of-the-settings-key"></a>`settings` キーの属性

| キー | Type | 説明 |
| --- | ---- | ----------- |
| `datastore` | string | パイプライン ジョブの既定のデータストアとして使用するデータストアの名前。 この値は、`azureml:<datastore-name>` 構文を使用したワークスペース内の既存のコンピューティングへの参照である必要があります。 親パイプライン ジョブまたは子ステップ ジョブの `outputs` プロパティで定義されている出力は、このデータストアに格納されます。 省略した場合、出力はワークスペース BLOB データストアに格納されます。 |

### <a name="job-inputs"></a>ジョブの入力

#### <a name="jobinputuri"></a>JobInputUri

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `file` | string | 入力として使用する 1 つのファイルへの URI。 サポートされる URI の種類は `azureml`、`https`、`wasbs`、`abfss`、`adl` です。 `azureml` URI 形式の使用方法の詳細については、[コア yaml 構文](reference-yaml-core-syntax.md)に関する記事を参照してください。 **`file` または `folder` のいずれかが必須です。**  | | |
| `folder` | string | 入力として使用するフォルダーの URI。 サポートされる URI の種類は `azureml`、`wasbs`、`abfss`、`adl` です。 `azureml` URI 形式の使用方法の詳細については、[コア yaml 構文](reference-yaml-core-syntax.md)に関する記事を参照してください。 **`file` または `folder` のいずれかが必須です。**   | | |
| `mode` | string | コンピューティング先にデータを配信する方法のモード。 読み取り専用マウントと読み取り/書き込みマウントの場合、データはマウント パスとして使用されます。 フォルダーはフォルダーとしてマウントされ、ファイルはファイルとしてマウントされます。 ダウンロード モードの場合、データはダウンロードされたパスとして使用されます。 | `ro_mount`, `rw_mount`, `download` | `ro_mount` |

#### <a name="jobinputdataset"></a>JobInputDataset

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `dataset` | 文字列またはオブジェクト | **必須。** 入力として使用するデータセット。 この値は、ワークスペース内の既存のバージョン管理されたデータセットへの参照またはインライン データセットの仕様のいずれかです。 <br><br> 既存のデータセットを参照するには、`azureml:<dataset-name>:<dataset-version>` 構文を使用します。 <br><br> データセットをインラインで定義するには、[データセット スキーマ](reference-yaml-dataset.md#yaml-syntax)に従います。 `name` および `version` のプロパティはインライン データセットではサポートされていないので、除外します。 | | |
| `mode` | string | データセットをコンピューティング先に配信する方法のモード。 読み取り専用マウントの場合、データセットはマウント パスとして使用されます。 フォルダーはフォルダーとしてマウントされ、ファイルは親フォルダーとしてマウントされます。 ダウンロード モードの場合、データセットはダウンロードされたパスとして使用されます。 | `ro_mount`, `download` | `ro_mount` |

## <a name="remarks"></a>Remarks

`az ml job` コマンドは、Azure Machine Learning パイプライン ジョブを管理するために使用できます。

## <a name="examples"></a>例

例は、[GitHub リポジトリの例](https://github.com/Azure/azureml-examples/tree/main/cli/jobs)にあります。 以下にいくつか示します。

## <a name="yaml-hello-pipeline"></a>YAML: hello パイプライン

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline.yml":::

## <a name="yaml-inputoutput-dependency"></a>YAML: 入力/出力の依存関係

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-io.yml":::

## <a name="yaml-common-pipeline-job-settings"></a>YAML: 一般的なパイプライン ジョブ設定

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-settings.yml":::

## <a name="yaml-top-level-input-and-overriding-common-pipeline-job-settings"></a>YAML: 上位の入力と一般的なパイプライン ジョブ設定のオーバーライド

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-abc.yml":::

## <a name="yaml-model-training-pipeline"></a>YAML: モデル トレーニング パイプライン

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines/cifar-10/job.yml":::

## <a name="next-steps"></a>次の手順

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)
