---
title: CLI (v2) コンポーネント ジョブ YAML スキーマ
titleSuffix: Azure Machine Learning
description: CLI (v2) コンポーネント ジョブ YAML スキーマのリファレンス ドキュメント。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: fc1f9580b44132b172c7f58a93d75943dea63efd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565678"
---
# <a name="cli-v2-component-job-yaml-schema"></a>CLI (v2) コンポーネント ジョブ YAML スキーマ

ソース JSON スキーマは https://azuremlschemas.azureedge.net/latest/commandJob.schema.json にあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 構文

| キー | 種類 | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | YAML スキーマ。 Azure Machine Learning 用 VS Code 拡張機能を使用して YAML ファイルを作成する場合は、ファイルの先頭に `$schema` を含めることで、スキーマとリソースの入力候補を呼び出すことができます。 | | |
| `type` | const | ジョブの種類。 | `component` | |
| `component` | object | **必須。** ジョブで呼び出して実行するコンポーネント。 この値には、ワークスペース内の既存のバージョン管理されたコンポーネントへの参照、インライン コンポーネント仕様、または別のコンポーネント YAML 仕様ファイルへのローカル パスを指定できます。 <br><br> 既存のコンポーネントを参照するには、`azureml:<component-name>:<component-version>` 構文を使用します。 <br><br> コンポーネントをインラインまたは別の YAML ファイルで定義するには、[Command コンポーネント スキーマ](reference-yaml-component-command.md#yaml-syntax)に従います。 `name` プロパティと `version` プロパティはインライン コンポーネント使用には適用されないため、除外します。 | | |
| `compute` | string | ジョブを実行するコンピューティング先の名前。 この値は、`azureml:<compute-name>` 構文を使用する、ワークスペース内の既存のコンピューティングへの参照である必要があります。 省略した場合、Azure MLは、パイプライン ジョブの `compute` プロパティで定義されているコンピューティングを使用します。 | | |
| `inputs` | object | ジョブへの入力の辞書。 キーはコンポーネント入力の 1 つの名前に対応し、値はランタイム入力値です。 <br><br> 入力は、`${{ inputs.<input_name> }}` 式を使用して `command` で参照できます。 | | |
| `inputs.<input_name>` | number、integer、boolean、string、または object | リテラル値 (number、integer、boolean、または string 型)、[JobInputUri](#jobinputuri)、または [JobInputDataset](#jobinputdataset) のいずれか。 `jobs.<COMPONENT_NAME>.outputs.<OUTPUT_NAME>` を使用して、同じパイプライン内の別のジョブからの出力を参照できます。 | | |
| `outputs` | object | ジョブの出力構成の辞書。 キーは、コンポーネント出力の 1 つの名前に対応する名前に対応し、値はランタイム出力構成です。 <br><br> 出力は、`${{ outputs.<output_name> }}` 式を使用して `command` で参照できます。 | |
| `outputs.<output_name>` | object | 省略可能な `mode` を指定するか、オブジェクトを空のままにします。 `outputs` 辞書に指定された名前付き出力ごとに、Azure ML はテンプレート化されたパス (`{default-datastore}/azureml/{job-name}/{output-name}/`) に基づいて出力の場所を自動生成します。 ユーザーは、後のリリースでカスタムの場所を指定できます。 | |
| `outputs.<output_name>.mode` | string | 出力ファイルを宛先ストレージに配信する方法のモード。 読み取り/書き込みマウント モードでは、出力ディレクトリはマウントされたディレクトリになります。 アップロード モードの場合、出力ディレクトリに書き込まれたファイルがジョブの最後にアップロードされます。 | `rw_mount`, `upload` | `rw_mount` |
| `overrides` | object | コンポーネントの特定の設定は、ジョブでコンポーネントが実行されるときに、異なるランタイム設定でオーバーライドすることができます。 コマンド コンポーネントの場合、`resources` プロパティと `distribution` プロパティは `overrides.resources` および `overrides.distribution` を使用してオーバーライドできます。 | | |

### <a name="job-inputs"></a>ジョブの入力

#### <a name="jobinputuri"></a>JobInputUri

| キー | 種類 | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `file` | string | 入力として使用する 1 つのファイルへの URI。 サポートされる URI の種類は `azureml`、`https`、`wasbs`、`abfss`、`adl` です。 `azureml` URI 形式の使用方法の詳細については、[コア yaml 構文](reference-yaml-core-syntax.md)に関する記事を参照してください。 **`file` または `folder` のいずれかが必須です。**  | | |
| `folder` | string | 入力として使用するフォルダーの URI。 サポートされる URI の種類は `azureml`、`wasbs`、`abfss`、`adl` です。 `azureml` URI 形式の使用方法の詳細については、[コア yaml 構文](reference-yaml-core-syntax.md)に関する記事を参照してください。 **`file` または `folder` のいずれかが必須です。**   | | |
| `mode` | string | コンピューティング先にデータを配信する方法のモード。 読み取り専用マウントと読み取り/書き込みマウントの場合、データはマウント パスとして使用されます。 フォルダーはフォルダーとしてマウントされ、ファイルはファイルとしてマウントされます。 ダウンロード モードの場合、データはダウンロードされたパスとして使用されます。 | `ro_mount`, `rw_mount`, `download` | `ro_mount` |

#### <a name="jobinputdataset"></a>JobInputDataset

| キー | 種類 | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `dataset` | 文字列またはオブジェクト | **必須。** 入力として使用するデータセット。 この値は、ワークスペース内の既存のバージョン管理されたデータセットへの参照またはインライン データセットの仕様のいずれかです。 <br><br> 既存のデータセットを参照するには、`azureml:<dataset-name>:<dataset-version>` 構文を使用します。 <br><br> データセットをインラインで定義するには、[データセット スキーマ](reference-yaml-dataset.md#yaml-syntax)に従います。 `name` および `version` のプロパティはインライン データセットではサポートされていないので、除外します。 | | |
| `mode` | string | データセットをコンピューティング先に配信する方法のモード。 読み取り専用マウントの場合、データセットはマウント パスとして使用されます。 フォルダーはフォルダーとしてマウントされ、ファイルは親フォルダーとしてマウントされます。 ダウンロード モードの場合、データセットはダウンロードされたパスとして使用されます。 | `ro_mount`, `download` | `ro_mount` |

## <a name="remarks"></a>Remarks

コンポーネント ジョブは、パイプライン ジョブ内で実行できます。 `az ml job` コマンドは、Azure Machine Learning パイプライン ジョブを管理するために使用できます。

コンポーネント ジョブは現在、スタンドアロン ジョブとして実行することはできません。また、パイプライン内でのみ実行できます。

## <a name="next-steps"></a>次の手順

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)
