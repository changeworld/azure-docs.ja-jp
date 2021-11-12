---
title: CLI (v2) モデル YAML スキーマ
titleSuffix: Azure Machine Learning
description: CLI (v2) モデル YAML スキーマのリファレンス ドキュメント。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 51db45ecaac8be0679832dde29685a6ba2909f75
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055759"
---
# <a name="cli-v2-model-yaml-schema"></a>CLI (v2) モデル YAML スキーマ

ソース JSON スキーマは https://azuremlschemas.azureedge.net/latest/model.schema.json にあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 構文

| キー | Type | 説明 | 使用できる値 |
| --- | ---- | ----------- | -------------- |
| `$schema` | string | YAML スキーマ。 | |
| `name` | string | **必須。** モデルの名前。 | |
| `version` | string | モデルのバージョン。 省略した場合、Azure ML によってバージョンが自動生成されます。 | |
| `description` | string | モデルの説明。 | |
| `tags` | object | モデルのタグの辞書。 | |
| `local_path` | string | モデル ファイルへのローカル パス。 これは、ファイルまたはディレクトリのいずれかを指します。 **`local_path` または `model_uri` のいずれかが必須です。** | |
| `model_uri` | string | モデル ファイルの URI。 これは、ファイルまたはディレクトリのいずれかを指します。 **`local_path` または `model_uri` のいずれかが必須です。** | |
| `model_format` | string | モデルの Storage 形式。 コードをデプロイしない場合に適用されます。 | `custom`, `mlflow`, `triton`, `openai` |
| `flavors` | object | モデルのフレーバー。 各モデル ストレージ形式の種類には、1 つまたは複数のサポートされているフレーバーを含めることができます。 コードをデプロイしない場合に適用されます。 | |

## <a name="remarks"></a>解説

`az ml model` コマンドは、Azure Machine Learning モデルを管理するために使用できます。

## <a name="examples"></a>例

例は、[GitHub リポジトリの例](https://github.com/Azure/azureml-examples/tree/main/cli/assets/model)にあります。 以下にいくつか示します。

## <a name="yaml-local-file"></a>YAML: ローカル ファイル

:::code language="yaml" source="~/azureml-examples-main/cli/assets/model/local-file.yml":::

## <a name="yaml-local-folder-in-mlflow-format"></a>YAML: MLflow 形式のローカル フォルダー

:::code language="yaml" source="~/azureml-examples-main/cli/assets/model/local-mlflow.yml":::

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)
