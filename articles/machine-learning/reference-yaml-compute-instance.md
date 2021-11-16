---
title: CLI (v2) コンピューティング インスタンス YAML スキーマ
titleSuffix: Azure Machine Learning
description: CLI (v2) コンピューティング インスタンス YAML スキーマのリファレンス ドキュメント。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 2b4f8ddb82e758899c0de70bf722f1184f2f7aff
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056532"
---
# <a name="cli-v2-compute-instance-yaml-schema"></a>CLI (v2) コンピューティング インスタンス YAML スキーマ

ソース JSON スキーマは https://azuremlschemas.azureedge.net/latest/computeInstance.schema.json にあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 構文

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | YAML スキーマ。 Azure Machine Learning 用 VS Code 拡張機能を使用して YAML ファイルを作成する場合は、ファイルの先頭に `$schema` を含めることで、スキーマとリソースの入力候補を呼び出すことができます。 | | |
| `type` | string | **必須。** コンピューティングの種類。 | `computeinstance` | |
| `name` | string | **必須。** コンピューティングの名前。 | | |
| `description` | string | コンピューティングの説明。 | | |
| `size` | string | コンピューティング インスタンスに使用する VM サイズ。 詳細については、「[サポートされている VM シリーズおよびサイズ](concept-compute-target.md#supported-vm-series-and-sizes)」をご覧ください。 すべてのリージョンですべてのサイズを使用できるわけではありません。 | 特定のリージョンでサポートされているサイズの一覧については、`az ml compute list-sizes` コマンドを使用してください。  | `Standard_DS3_v2` |
| `create_on_behalf_of` | object | 別のユーザーの代理でコンピューティング インスタンスを作成するための設定。 割り当てられたユーザーに正しい RBAC アクセス許可があることを確認してください。 |  |  |
| `create_on_behalf_of.user_tenant_id` | string | ロール割り当て済みユーザーの AAD テナント ID。 |  |  |
| `create_on_behalf_of.user_object_id` | string | ロール割り当て済みユーザーの AAD オブジェクト ID。 |  |  |
| `ssh_public_access_enabled` | boolean | コンピューティング インスタンスでパブリック SSH アクセスを有効にするかどうかを指定します。 | | `false` |
| `ssh_settings` | object | コンピューティング インスタンスに接続するための SSH 設定。 | | |
| `ssh_settings.ssh_key_value` | string | 管理者ユーザー アカウントの SSH 公開キー。 | | |
| `network_settings` | object | ネットワークのセキュリティ設定。 | | |
| `network_settings.vnet_name` | string | 新しい仮想ネットワーク (VNet) を作成するとき、または既存の仮想ネットワークを参照するときの、仮想ネットワークの名前。 | | |
| `network_settings.subnet` | string | 新しい VNet を作成するとき、既存の VNet を参照するとき、または既存の VNet のサブネットの完全修飾リソース ID を参照するときの、サブネットの名前。 サブネット ID が指定されている場合は `network_settings.vnet_name` を指定しないでください。 サブネット ID は、別のリソース グループ内の VNet またはサブネットを参照できます。 | | |

## <a name="remarks"></a>Remarks

`az ml compute` コマンドは、Azure Machine Learning コンピューティング インスタンスを管理するために使用できます。

## <a name="yaml-minimal"></a>YAML: 最小

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/instance-minimal.yml":::

## <a name="yaml-basic"></a>YAML: 基本

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/instance-basic.yml":::

## <a name="next-steps"></a>次の手順

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)
