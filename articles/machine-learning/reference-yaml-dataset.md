---
title: CLI (v2) データセット YAML スキーマ
titleSuffix: Azure Machine Learning
description: CLI (v2) データセット YAML スキーマのリファレンス ドキュメント。
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 4a22fee30a28008fa440d606360623e156c332a0
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132133667"
---
# <a name="cli-v2-dataset-yaml-schema"></a>CLI (v2) データセット YAML スキーマ

ソース JSON スキーマは https://azuremlschemas.azureedge.net/latest/dataset.schema.json にあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 構文

| キー | Type | 説明 | 使用できる値 |
| --- | ---- | ----------- | -------------- |
| `$schema` | string | YAML スキーマ。 Azure Machine Learning 用 VS Code 拡張機能を使って YAML ファイルを作成する場合は、ファイルの先頭に `$schema` を含めることで、スキーマとリソースの入力候補を呼び出すことができます。 | |
| `name` | string | **必須。** データセットの名前。 | |
| `version` | string | データセットのバージョン。 省略した場合、Azure ML によってバージョンが自動生成されます。 | |
| `description` | string | データセットの説明。 | |
| `tags` | object | データセットのタグの辞書。 | |
| `local_path` | string | データセットの作成元となる単一のローカル ファイルまたはフォルダーの絶対パスまたは相対パス。 **`local_path`または `paths` のいずれかが必須です。** | |
| `paths` | array | データセットの作成元となる URI ソースのリスト。 リスト内の各エントリは、「[データセットのソース パス](#dataset-source-path)」で定義されているスキーマに従っている必要があります。 現時点では、1 つのソースのみがサポートされています。  **`local_path`または `paths` のいずれかが必須です。** | |

### <a name="dataset-source-path"></a>データセットのソース パス

| キー | Type | 説明 |
| --- | ---- | ----------- |
| `file` | string | データセットのソースとして使用される単一のファイルへの URI。 サポートされる URI の種類は、`azureml`、`https`、`wasbs`、`abfss` および `adl` です。 `azureml://` URI 形式の使用方法の詳細については、[コア yaml 構文](reference-yaml-core-syntax.md)に関するページを参照してください。 **`file` または `folder` のいずれかが必須です。** |
| `folder` | string | データセットのソースとして使用されるフォルダーへの URI。 サポートされる URI の種類は、`azureml`、`https`、`wasbs`、`abfss` および `adl` です。 `azureml://` URI 形式の使用方法の詳細については、[コア yaml 構文](reference-yaml-core-syntax.md)に関するページを参照してください。 **`file` または `folder` のいずれかが必須です。** |

## <a name="remarks"></a>注釈

`az ml dataset` コマンドは、Azure Machine Learning データセットを管理するために使用できます。

## <a name="examples"></a>例

例は、[例の GitHub リポジトリ](https://github.com/Azure/azureml-examples/tree/main/cli/assets/dataset)内にあります。 以下にいくつか示します。

## <a name="yaml-datastore-file"></a>YAML: データストア ファイル

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-file.yml":::

## <a name="yaml-datastore-folder"></a>YAML: データストア フォルダー

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-folder.yml":::

## <a name="yaml-https-file"></a>YAML: https ファイル

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-file-https.yml":::

## <a name="yaml-https-folder"></a>YAML: https フォルダー

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-folder-https.yml":::

## <a name="yaml-wasbs-file"></a>YAML: wasbs ファイル

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-file-wasbs.yml":::

## <a name="yaml-wasbs-folder"></a>YAML: wasbs フォルダー

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-folder-wasbs.yml":::

## <a name="yaml-local-file"></a>YAML: ローカル ファイル

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/local-file.yml":::

## <a name="yaml-local-folder"></a>YAML: ローカル フォルダー

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/local-folder.yml":::

## <a name="next-steps"></a>次のステップ

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)
