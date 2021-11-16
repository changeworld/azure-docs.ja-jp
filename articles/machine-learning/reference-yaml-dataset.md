---
title: CLI (v2) データセット YAML スキーマ
titleSuffix: Azure Machine Learning
description: CLI (v2) データセット YAML スキーマのリファレンス ドキュメント。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: b92324d61316ba59f964cff97ebbb9869d01dafb
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557986"
---
# <a name="cli-v2-dataset-yaml-schema"></a>CLI (v2) データセット YAML スキーマ

ソース JSON スキーマは https://azuremlschemas.azureedge.net/latest/dataset.schema.json にあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 構文

| キー | 種類 | 説明 | 使用できる値 |
| --- | ---- | ----------- | -------------- |
| `$schema` | string | YAML スキーマ。 Azure Machine Learning 用 VS Code 拡張機能を使用して YAML ファイルを作成する場合は、ファイルの先頭に `$schema` を含めることで、スキーマとリソースの入力候補を呼び出すことができます。 | |
| `name` | string | **必須。** データセットの名前。 | |
| `version` | string | データセットのバージョン。 省略した場合、Azure ML によってバージョンが自動生成されます。 | |
| `description` | string | データセットの説明。 | |
| `tags` | object | データセットのタグの辞書。 | |
| `local_path` | string | データセットの作成元となる単一のローカル ファイルまたはフォルダーの絶対パスまたは相対パス。 **`local_path`または `paths` のいずれかが必須です。** | |
| `paths` | array | データセットの作成元となる URI ソースのリスト。 リスト内の各エントリは、「[データセットのソース パス](#dataset-source-path)」で定義されているスキーマに従っている必要があります。 現時点では、1 つのソースのみがサポートされています。  **`local_path`または `paths` のいずれかが必須です。** | |

### <a name="dataset-source-path"></a>データセットのソース パス

| キー | 種類 | 説明 |
| --- | ---- | ----------- |
| `file` | string | データセットのソースとして使用される単一のファイルへの URI。 サポートされる URI の種類は、`azureml`、`https`、`wasbs`、`abfss` および `adl` です。 `azureml://` URI 形式の使用方法の詳細については、[コア yaml 構文](reference-yaml-core-syntax.md)に関するページを参照してください。 **`file` または `folder` のいずれかが必須です。** |
| `folder` | string | データセットのソースとして使用されるフォルダーへの URI。 サポートされる URI の種類は、`azureml`、`https`、`wasbs`、`abfss` および `adl` です。 `azureml://` URI 形式の使用方法の詳細については、[コア yaml 構文](reference-yaml-core-syntax.md)に関するページを参照してください。 **`file` または `folder` のいずれかが必須です。** |

## <a name="remarks"></a>Remarks

`az ml dataset` コマンドは、Azure Machine Learning データセットを管理するために使用できます。

## <a name="examples"></a>例

例は、[GitHub リポジトリの例](https://github.com/Azure/azureml-examples/tree/main/cli/assets/dataset)にあります。 以下にいくつか示します。

## <a name="yaml-datastore-file"></a>YAML: データストア ファイル

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/dataset/cloud-file.yml":::

## <a name="yaml-datastore-folder"></a>YAML: データストア フォルダー

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/dataset/cloud-folder.yml":::

## <a name="yaml-https-file"></a>YAML: https ファイル

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/dataset/cloud-file-https.yml":::

## <a name="yaml-https-folder"></a>YAML: https フォルダー

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/dataset/cloud-folder-https.yml":::

## <a name="yaml-wasbs-file"></a>YAML: wasbs ファイル

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/dataset/cloud-file-wasbs.yml":::

## <a name="yaml-wasbs-folder"></a>YAML: wasbs フォルダー

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/dataset/cloud-folder-wasbs.yml":::

## <a name="yaml-local-file"></a>YAML: ローカル ファイル

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/dataset/local-file.yml":::

## <a name="yaml-local-folder"></a>YAML: ローカル フォルダー

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/dataset/local-folder.yml":::

## <a name="next-steps"></a>次の手順

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)
