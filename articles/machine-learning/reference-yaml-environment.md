---
title: CLI (v2) 環境 YAML スキーマ
titleSuffix: Azure Machine Learning
description: CLI (v2) 環境 YAML スキーマのリファレンス ドキュメント。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: d26f25f2a007d0488b515485be5cc3bf0c9d735d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557948"
---
# <a name="cli-v2-environment-yaml-schema"></a>CLI (v2) 環境 YAML スキーマ

ソース JSON スキーマは https://azuremlschemas.azureedge.net/latest/environment.schema.json にあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 構文

| キー | 種類 | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | YAML スキーマ。 Azure Machine Learning 用 VS Code 拡張機能を使用して YAML ファイルを作成する場合は、ファイルの先頭に `$schema` を含めることで、スキーマとリソースの入力候補を呼び出すことができます。 | | |
| `name` | string | **必須。** 環境の名前。 | | |
| `version` | string | 環境のバージョン。 省略した場合、Azure ML によってバージョンが自動生成されます。 | | |
| `description` | string | 環境の説明。 | | |
| `tags` | object | 環境のタグのディクショナリ。 | | |
| `image` | string | 環境に使用する Docker イメージ。 **`image` または `build` のいずれかが必須です。** | | |
| `conda_file` | 文字列またはオブジェクト | conda 環境の依存関係の標準 conda YAML 構成ファイル。 「https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually」を参照してください。 <br> <br> 指定されている場合は、`image` を指定する必要があります。 Azure ML は、提供されている Docker イメージの上に conda 環境を構築します。 | | |
| `build` | object | 環境に使用する Docker ビルド コンテキスト構成。 **`image` または `build` のいずれかが必須です。** | | |
| `build.local_path` | string | ビルド コンテキストとして使用するディレクトリへのローカル パス。 | | |
| `build.dockerfile_path` | string | ビルド コンテキスト内の Dockerfile への相対パス。 | | `Dockerfile` |
| `os_type` | string | オペレーティング システムの種類。 | `linux`, `windows` | `linux` |  
| `inference_config` | object | 推論コンテナーの構成。 環境を使用してオンライン デプロイ用のサービス コンテナーを構築する場合にのみ適用されます。 詳しくは、[`inference_config`キー](#attributes-of-the-inference_config-key)の属性について参照してください。 | | |

### <a name="attributes-of-the-inference_config-key"></a>`inference_config` キーの属性

| キー | 種類 | 説明 |
| --- | ---- | ----------- |
| `liveness_route` | object | サービス コンテナーの liveness ルート。 |
| `liveness_route.path` | string | liveness 要求をルーティングするパス。 |
| `liveness_route.port` | 整数 | liveness 要求をルーティングするポート。 |
| `readiness_route` | object | サービス コンテナーの準備ルート。 |
| `readiness_route.path` | string | 準備要求をルーティングするパス。 |
| `readiness_route.port` | 整数 | 準備要求をルーティングするポート。 |
| `scoring_route` | object | サービス コンテナーのスコアリング ルート。 |
| `scoring_route.path` | string | スコアリング要求をルーティングするパス。 |
| `scoring_route.port` | 整数 | スコアリング要求をルーティングするポート。 |

## <a name="remarks"></a>解説

`az ml environment` コマンドは、Azure Machine Learning 環境を管理するために使用できます。

## <a name="examples"></a>例

例は、[GitHub リポジトリの例](https://github.com/Azure/azureml-examples/tree/main/cli/assets/environment)にあります。 以下にいくつか示します。

## <a name="yaml-local-docker-build-context"></a>YAML: ローカル Docker ビルド コンテキスト

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/environment/docker-context.yml":::

## <a name="yaml-docker-image"></a>YAML: Docker イメージ

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/environment/docker-image.yml":::

## <a name="yaml-docker-image-plus-conda-file"></a>YAML: Docker イメージと conda ファイル

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/environment/docker-image-plus-conda.yml":::

## <a name="next-steps"></a>次の手順

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)
