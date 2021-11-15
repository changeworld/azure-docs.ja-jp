---
title: CLI (v2) Azure Data Lake Gen2 データストア YAML スキーマ
titleSuffix: Azure Machine Learning
description: CLI (v2) Azure Data Lake Gen2 データストア YAML スキーマのリファレンス ドキュメント。
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 156b2de4794a40df8ddbe3aaa9ff143756d9dd03
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135093"
---
# <a name="cli-v2-azure-data-lake-gen2-yaml-schema"></a>CLI (v2) Azure Data Lake Gen2 YAML スキーマ

ソース JSON スキーマは https://azuremlschemas.azureedge.net/latest/azureDataLakeGen2.schema.json にあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 構文

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | YAML スキーマ。 Azure Machine Learning 用 VS Code 拡張機能を使用して YAML ファイルを作成する場合は、ファイルの先頭に `$schema` を含めることで、スキーマとリソースの入力候補を呼び出すことができます。 | | |
| `type` | string | **必須。** データストアの種類。 | `azure_data_lake_gen2` | |
| `name` | string | **必須。** データストアの名前。 | | |
| `description` | string | データストアの説明。 | | |
| `tags` | object | データストアのタグの辞書。 | | |
| `account_name` | string | **必須。** Azure Storage アカウントの名前。 | | |
| `filesystem` | string | **必須。** ファイル システムの名前。 ファイルとフォルダーを含む親ディレクトリ。 これは、Azure Blob Storage 内のコンテナーと同じです。 | | |
| `endpoint` | string | ストレージ アカウント名と `endpoint` を組み合わせてストレージ アカウント エンドポイント URL を作成するために使用される、ストレージ サービスのエンドポイント サフィックス。 ストレージ アカウント URL の例: `https://<storage-account-name>.dfs.core.windows.net`。 | | `core.windows.net` |
| `protocol` | string | ファイル システムへの接続に使用するプロトコル。 | `https`, `abfss` | `https` |
| `credentials` | object | Azure ストレージ アカウントに接続するためのサービス プリンシパルの資格情報。 資格情報のシークレットは、ワークスペースのキー コンテナーに格納されます。 | | |
| `credentials.tenant_id` | string | サービス プリンシパルのテナント ID。 **`credentials` を指定した場合は必須。** | | |
| `credentials.client_id` | string | サービス プリンシパルのクライアント ID。 **`credentials` を指定した場合は必須。** | | |
| `credentials.client_secret` | string | サービス プリンシパルのクライアント シークレット。 **`credentials` を指定した場合は必須。** | | |
| `credentials.resource_url` | string | Azure Data Lake Storage Gen2 アカウントで実行される操作を決定するリソース URL。 | | `https://storage.azure.com/` |
| `credentials.authority_url` | string | ユーザーの認証に使用される機関 URL。 | | `https://login.microsoftonline.com` |

## <a name="remarks"></a>解説

`az ml datastore` コマンドは、Azure Machine Learning データストアを管理するために使用できます。

## <a name="examples"></a>例

例は、[GitHub リポジトリの例](https://github.com/Azure/azureml-examples/tree/main/cli/resources/datastore)にあります。 以下にいくつか示します。

## <a name="yaml-identity-based-access"></a>YAML: ID ベースのアクセス

:::code language="yaml" source="~/azureml-examples-main/cli/resources/datastore/adls-gen2-credless.yml":::

## <a name="yaml-tenant-id-client-id-client-secret"></a>YAML: テナント ID、クライアント ID、クライアント シークレット

:::code language="yaml" source="~/azureml-examples-main/cli/resources/datastore/adls-gen2.yml":::

## <a name="next-steps"></a>次のステップ

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)
