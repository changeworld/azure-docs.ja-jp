---
title: CLI (v2) ワークスペース YAML スキーマ
titleSuffix: Azure Machine Learning
description: CLI (v2) ワークスペース YAML スキーマのリファレンス ドキュメント。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: ea641b24b43c7ad1d22e497f9d2558ccb163e81b
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060020"
---
# <a name="cli-v2-workspace-yaml-schema"></a>CLI (v2) ワークスペース YAML スキーマ

ソース JSON スキーマは https://azuremlschemas.azureedge.net/latest/workspace.schema.json にあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 構文

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | YAML スキーマ。 Azure Machine Learning 用 VS Code 拡張機能を使って YAML ファイルを作成する場合は、ファイルの先頭に `$schema` を含めることで、スキーマとリソースの入力候補を呼び出すことができます。 | | |
| `name` | string | **必須。** ワークスペースの名前。 | | |
| `display_name` | string | Studio UI のワークスペースの表示名。 リソース グループ内で一意でないことがあります。 | | |
| `description` | string | ワークスペースの説明。 | | |
| `tags` | object | ワークスペースのタグの辞書。 | | |
| `location` | string | ワークスペースの場所。 省略すると、既定でリソース グループの場所が設定されます。 | | |
| `resource_group` | string | **必須。** ワークスペースを含むリソース グループ。 リソース グループが存在しない場合は、新規に作成されます。 | | |
| `hbi_workspace` | boolean | お客様のデータが、機密性の高いビジネス情報を含む High Business Impact (HBI) であるかどうか。 詳細については、[保存時のデータの暗号化](concept-data-encryption.md#encryption-at-rest)に関するページを参照してください。 | | `false` |
| `storage_account` | string | ワークスペースの既定のストレージ アカウントとして使う、既存の Azure Storage アカウントの完全修飾リソース ID。 Premium ストレージまたは階層型名前空間を持つストレージ アカウントを既定のストレージ アカウントとして使うことはできません。 省略すると、新しいストレージ アカウントが作成されます。 | | |
| `container_registry` | string | ワークスペースの既定のコンテナー レジストリとして使う、既存の Azure コンテナー レジストリの完全修飾リソース ID。 Azure ML には、トレーニングとデプロイに使われるコンテナー イメージの管理に、Azure Container Registry (ACR) が使われます。 省略すると、新しいコンテナー レジストリが作成されます。 作成は遅延読み込みなので、トレーニングまたはデプロイのいずれかの操作でコンテナー レジストリが初めて必要になったときに作成されます。 | | |
| `key_vault` | string | ワークスペースの既定のキー コンテナーとして使う、既存の Azure キー コンテナーの完全修飾リソース ID。 省略すると、新しいキー コンテナーが作成されます。 | | |
| `application_insights` | string | ワークスペースの既定のアプリケーション分析情報として使う、既存の Azure アプリケーション分析情報の完全修飾リソース ID。 省略すると、新しいアプリケーション分析情報が作成されます。 | | |
| `customer_managed_key` | object | Azure Machine Learning では、メトリックとメタデータは Azure Cosmos DB インスタンスに格納されます。 既定では、データは Microsoft のマネージド キーを使って保存時に暗号化されます。 独自のカスタマー マネージド キーを暗号化に使うには、このセクションでカスタマー マネージド キーの情報を指定します。 詳細については、[Azure Cosmos DB のデータ暗号化](concept-data-encryption.md#azure-cosmos-db)に関するページを参照してください。 | | |
| `customer_managed_key.key_vault` | string | カスタマー マネージド キーを含むキー コンテナーの完全修飾リソース ID。 このキー コンテナーは、`key_vault` で指定した既定のワークスペースのキー コンテナーとは異なる場合があります。| | |
| `customer_managed_key.key_uri` | string | 保存データを暗号化するためのカスタマーマネージド キーのキー URI。 URI の形式は `https://<keyvault-dns-name>/keys/<key-name>/<key-version>` です。 | | |
| `image_build_compute` | string | コンテナー レジストリが VNet の背後にある場合、環境 Docker イメージの構築に使うコンピューティング先の名前。 詳細については、[VNet の背後にあるワークスペース リソースのセキュリティ保護](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr)に関するページを参照してください。 | | |
| `public_network_access` | string | ワークスペースに Private Link を使う場合、パブリック エンドポイントへのアクセスを許可するかどうかを指定します。 詳細については、[VNet の背後でパブリック アクセスを有効にする](how-to-configure-private-link.md#enable-public-access)方法に関するページを参照してください。 | `enabled`, `disabled` | `disabled` |

## <a name="remarks"></a>解説

`az ml workspace` コマンドは、Azure Machine Learning ワークスペースの管理に使用できます。

## <a name="examples"></a>例

例は、[GitHub リポジトリの例](https://github.com/Azure/azureml-examples/tree/main/cli/resources/workspace)にあります。 以下にいくつか示します。

## <a name="yaml-basic"></a>YAML: 基本

:::code language="yaml" source="~/azureml-examples-main/cli/resources/workspace/basic.yml":::

## <a name="yaml-with-existing-resources"></a>YAML: 既存のリソースで動作します。

:::code language="yaml" source="~/azureml-examples-main/cli/resources/workspace/with-existing-resources.yml":::

## <a name="yaml-customer-managed-key"></a>YAML: カスタマー マネージド キー

:::code language="yaml" source="~/azureml-examples-main/cli/resources/workspace/cmk.yml":::

## <a name="yaml-private-link"></a>Private Link: プライベート リンク

:::code language="yaml" source="~/azureml-examples-main/cli/resources/workspace/privatelink.yml":::

## <a name="yaml-high-business-impact"></a>YAML: High Business Impact

:::code language="yaml" source="~/azureml-examples-main/cli/resources/workspace/hbi.yml":::

## <a name="next-steps"></a>次の手順

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)
