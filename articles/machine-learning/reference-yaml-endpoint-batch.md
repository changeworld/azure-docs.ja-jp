---
title: CLI (v2) バッチ エンドポイント YAML スキーマ
titleSuffix: Azure Machine Learning
description: CLI (v2) バッチ エンドポイント YAML スキーマのリファレンス ドキュメント。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: tracychms
ms.author: tracych
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: c91de82d4b0eec1256d34a28132ae3161c62690a
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057767"
---
# <a name="cli-v2-batch-endpoint-yaml-schema"></a>CLI (v2) バッチ エンドポイント YAML スキーマ

ソース JSON スキーマは https://azuremlschemas.azureedge.net/latest/batchEndpoint.schema.json にあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 構文

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | YAML スキーマ。 Azure Machine Learning 用 VS Code 拡張機能を使用して YAML ファイルを作成する場合は、ファイルの先頭に `$schema` を含めることで、スキーマとリソースの入力候補を呼び出すことができます。 | | |
| `name` | string | **必須。** エンドポイントの名前。 Azure リージョン レベルで一意である必要があります。 | | |
| `description` | string | エンドポイントの説明。 | | |
| `tags` | object | エンドポイントのタグのディクショナリ。 | | |
| `auth_mode` | string | エンドポイントの認証方法。 現在、Azure Active Directory (Azure AD) トークンベースの認証のみがサポートされています。 | `aad_token` | `aad_token` |
| `defaults` | object | エンドポイントの既定の設定。 | | |
| `defaults.deployment_name` | string | エンドポイントの既定のデプロイとして使用されるデプロイの名前。 | | |

## <a name="remarks"></a>Remarks

`az ml batch-endpoint` コマンドは、Azure Machine Learning エンドポイントを管理するために使用できます。

## <a name="examples"></a>例

例は、[GitHub リポジトリの例](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/batch)にあります。 以下にいくつか示します。

## <a name="yaml-basic"></a>YAML: 基本

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/batch-endpoint.yml":::

## <a name="next-steps"></a>次の手順

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)
