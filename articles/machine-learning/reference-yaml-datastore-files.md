---
title: CLI (v2) Azure Files データストア YAML スキーマ
titleSuffix: Azure Machine Learning
description: CLI (v2) Azure Files データストア YAML スキーマのリファレンス ドキュメント。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 1f06483d917631be8e1e4d97d8a37f922a6d11dc
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555884"
---
# <a name="cli-v2-azure-files-datastore-yaml-schema"></a>CLI (v2) Azure Files データストア YAML スキーマ

ソース JSON スキーマは https://azuremlschemas.azureedge.net/latest/azureFile.schema.json にあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 構文

| キー | 種類 | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | YAML スキーマ。 Azure Machine Learning 用 VS Code 拡張機能を使って YAML ファイルを作成する場合は、ファイルの先頭に `$schema` を含めることで、スキーマとリソースの入力候補を呼び出すことができます。 | | |
| `type` | string | **必須。** データストアの種類。 | `azure_file` | |
| `name` | string | **必須。** データストアの名前。 | | |
| `description` | string | データストアの説明。 | | |
| `tags` | object | データストアのタグの辞書。 | | |
| `account_name` | string | **必須。** Azure Storage アカウントの名前。 | | |
| `file_share_name` | string | **必須。** ファイル共有の名前。 | | |
| `endpoint` | string | ストレージ アカウント名と `endpoint` を組み合わせてストレージ アカウント エンドポイント URL を作成するために使用される、ストレージ サービスのエンドポイント サフィックス。 ストレージ アカウント URL の例: `https://<storage-account-name>.file.core.windows.net`。 | | `core.windows.net` |
| `protocol` | string | ファイル共有への接続に使用するプロトコル。 | `https` | `https` |
| `credentials` | object | Azure ストレージ アカウントに接続するための資格情報ベースの認証資格情報。 アカウント キーまたは Shared Access Signature (SAS) トークンのいずれかを指定できます。 資格情報のシークレットは、ワークスペースのキー コンテナーに格納されます。 | | |
| `credentials.account_key` | string | ストレージ アカウントにアクセスするアカウント キー。 **`credentials.account_key` または `credentials.sas_token` のいずれかは、`credentials` を指定した場合は必須です。** | | |
| `credentials.sas_token` | string | ストレージ アカウントにアクセスする SAS トークン。 **`credentials.account_key` または `credentials.sas_token` のいずれかは、`credentials` を指定した場合は必須です。** | | |

## <a name="remarks"></a>解説

`az ml datastore` コマンドは、Azure Machine Learning データストアを管理するために使用できます。

## <a name="examples"></a>例

例は、[例の GitHub リポジトリ](https://github.com/Azure/azureml-examples/tree/main/cli/resources/datastore)内にあります。 以下にいくつか示します。

## <a name="yaml-account-key"></a>YAML: アカウント キー

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/datastore/file.yml":::

## <a name="yaml-sas-token"></a>YAML: sas トークン

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/datastore/file-sas.yml":::

## <a name="next-steps"></a>次の手順

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)
