---
title: CLI (v2) Azure Data Lake Gen1 データストア YAML スキーマ
titleSuffix: Azure Machine Learning
description: CLI (v2) Azure Data Lake Gen1 データストア YAML スキーマのリファレンス ドキュメント。
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 847a296fa567d0365508161eafdacc525efd9191
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132133240"
---
# <a name="cli-v2-azure-data-lake-gen1-yaml-schema"></a>CLI (v2) Azure Data Lake Gen1 YAML スキーマ

ソース JSON スキーマは https://azuremlschemas.azureedge.net/latest/azureDataLakeGen1.schema.json にあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 構文

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | YAML スキーマ。 Azure Machine Learning 用 VS Code 拡張機能を使用して YAML ファイルを作成する場合は、ファイルの先頭に `$schema` を含めることで、スキーマとリソースの入力候補を呼び出すことができます。 | | |
| `type` | string | **必須。** データストアの種類。 | `azure_data_lake_gen1` | |
| `name` | string | **必須。** データストアの名前。 | | |
| `description` | string | データストアの説明。 | | |
| `tags` | object | データストアのタグの辞書。 | | |
| `store_name` | string | **必須。** Azure Data Lake Storage Gen1 アカウントの名前。 | | |
| `credentials` | object | Azure ストレージ アカウントに接続するためのサービス プリンシパルの資格情報。 資格情報のシークレットは、ワークスペースのキー コンテナーに格納されます。 | | |
| `credentials.tenant_id` | string | サービス プリンシパルのテナント ID。 **`credentials` を指定した場合は必須。** | | |
| `credentials.client_id` | string | サービス プリンシパルのクライアント ID。 **`credentials` を指定した場合は必須。** | | |
| `credentials.client_secret` | string | サービス プリンシパルのクライアント シークレット。 **`credentials` を指定した場合は必須。** | | |
| `credentials.resource_url` | string | Azure Data Lake Storage Gen1 アカウントで実行される操作を決定するリソース URL。 | | `https://datalake.azure.net/` |
| `credentials.authority_url` | string | ユーザーの認証に使用される機関 URL。 | | `https://login.microsoftonline.com` |

## <a name="remarks"></a>解説

`az ml datastore` コマンドは、Azure Machine Learning データストアを管理するために使用できます。

## <a name="examples"></a>例

例は、[GitHub リポジトリの例](https://github.com/Azure/azureml-examples/tree/main/cli/resources/datastore)にあります。 以下にいくつか示します。

## <a name="yaml-identity-based-access"></a>YAML: ID ベースのアクセス

:::code language="yaml" source="~/azureml-examples-main/cli/resources/datastore/adls-gen1-credless.yml":::

## <a name="yaml-tenant-id-client-id-client-secret"></a>YAML: テナント ID、クライアント ID、クライアント シークレット

:::code language="yaml" source="~/azureml-examples-main/cli/resources/datastore/adls-gen1.yml":::

## <a name="next-steps"></a>次のステップ

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)
