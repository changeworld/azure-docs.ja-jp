---
title: オンライン エンドポイント (プレビュー) YAML リファレンス
titleSuffix: Azure Machine Learning
description: オンライン エンドポイントとしてモデルをデプロイするために使用される YAML ファイルについての説明
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: how-to
author: rsethur
ms.author: seramasu
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 462c781ae4f304d2b9cb46b9b89d2ff7fbf7eb22
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137831"
---
# <a name="cli-v2-online-endpoint-yaml-schema"></a>CLI (v2) オンライン エンドポイント YAML スキーマ

ソース JSON スキーマは https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.schema.json にあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!NOTE]
> マネージド オンライン エンドポイント用に詳細に指定されたサンプル YAML があるので、[ご参考](https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.template.yaml)ください

## <a name="yaml-syntax"></a>YAML 構文

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | YAML スキーマ。 Azure Machine Learning 用 VS Code 拡張機能を使用して YAML ファイルを作成する場合は、ファイルの先頭に `$schema` を含めることで、スキーマとリソースの入力候補を呼び出すことができます。 | | |
| `name` | string | **必須。** エンドポイントの名前。 Azure リージョン レベルで一意である必要があります。 <br><br> 名前付け規則は[ここ](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)で定義されています。| | |
| `description` | string | エンドポイントの説明。 | | |
| `tags` | object | エンドポイントのタグのディクショナリ。 | | |
| `auth_mode` | string | エンドポイントの認証方法。 キーベースの認証と Azure ML トークンベースの認証がサポートされています。 キーベースの認証の有効期限が切れることはありませんが、Azure ML トークンベースの認証は期限切れになります。 | `key`, `aml_token` | `key` |
| `allow_public_access` | boolean | Private Link が有効になっているときにパブリック アクセスするかどうか。 | | `true` |
| `identity` | object | エンドポイントのプロビジョニングと推論のために Azure リソースにアクセスするマネージド ID 構成。 | | |
| `identity.type` | string | マネージド ID の種類。 型が `user_assigned` の場合は、`identity.user_assigned_identities` プロパティも指定する必要があります。 | `system_assigned`, `user_assigned` | |
| `identity.user_assigned_identities` | array | ユーザー割り当て ID の完全修飾リソース ID の一覧。 | | |

## <a name="remarks"></a>注釈

`az ml online-endpoint` コマンドは、Azure Machine Learning オンライン エンドポイントを管理するために使用できます。

## <a name="examples"></a>例

例は、[GitHub リポジトリの例](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/batch)にあります。 以下にいくつか示します。

## <a name="yaml-basic"></a>YAML: basic

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/sample/endpoint.yml":::

## <a name="yaml-system-assigned-identity"></a>YAML: システム割り当て ID

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/1-sai-create-endpoint.yml":::

## <a name="yaml-user-assigned-identity"></a>YAML: ユーザー割り当て ID

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/1-uai-create-endpoint.yml":::

## <a name="next-steps"></a>次のステップ

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)
- [マネージド オンライン エンドポイントを使用してモデルをデプロイする](how-to-deploy-managed-online-endpoints.md)方法について学習する
- [マネージド オンライン エンドポイントのデプロイとスコアリングのトラブルシューティング (プレビュー)](./how-to-troubleshoot-online-endpoints.md)