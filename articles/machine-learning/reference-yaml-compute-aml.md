---
title: CLI (v2) コンピューティング クラスター (AmlCompute) YAML スキーマ
titleSuffix: Azure Machine Learning
description: CLI (v2) コンピューティング クラスター (AmlCompute) YAML スキーマのリファレンス ドキュメント。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: eac97f87de7edafc45f0e434188579df19d99c36
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057613"
---
# <a name="cli-v2-compute-cluster-amlcompute-yaml-schema"></a>CLI (v2) コンピューティング クラスター (AmlCompute) YAML スキーマ

ソース JSON スキーマは https://azuremlschemas.azureedge.net/latest/amlCompute.schema.json にあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 構文

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | YAML スキーマ。 Azure Machine Learning 用 VS Code 拡張機能を使用して YAML ファイルを作成する場合は、ファイルの先頭に `$schema` を含めることで、スキーマとリソースの入力候補を呼び出すことができます。 | | |
| `type` | string | **必須。** コンピューティングの種類。 | `amlcompute` | |
| `name` | string | **必須。** コンピューティングの名前。 | | |
| `description` | string | コンピューティングの説明。 | | |
| `location` | string | コンピューティングの場所。 省略した場合、既定ではワークスペースの場所に設定されます。 | | |
| `size` | string | クラスターに使用する VM サイズ。 詳細については、「[サポートされている VM シリーズおよびサイズ](concept-compute-target.md#supported-vm-series-and-sizes)」をご覧ください。 すべてのリージョンですべてのサイズを使用できるわけではありません。 | 特定のリージョンでサポートされているサイズの一覧については、`az ml compute list-sizes` を使用してください。  | `Standard_DS3_v2` |
| `tier` | string | クラスターに使用する VM 優先度レベル。 優先順位の低い VM はプリエンプティブルですが、専用 VM と比較してコストが削減されます。 | `dedicated`, `low_priority` | `dedicated` |
| `min_instances` | 整数 (integer) | クラスターで使用するノードの最小数。 ノードの最小数を `0` に設定すると、Azure ML はクラスターが使用されていないときに、クラスターをスケールダウンしてノードをゼロにすることができます。 `0` よりも大きい値を指定すると、クラスターが使用されていない場合でも、指定した数のノードが実行され続けます。 | | `0` |
| `max_instances` | 整数 (integer) | クラスターで使用するノードの最大数。 | | `1` |
| `idle_time_before_scale_down` | 整数 (integer) | クラスターをスケールダウンするまでのノードのアイドル時間 (秒)。 | | `120` |
| `ssh_public_access_enabled` | boolean | クラスターのノードでパブリック SSH アクセスを有効にするかどうか。 | | `false` |
| `ssh_settings` | object | クラスターに接続するための SSH 設定。 | | |
| `ssh_settings.admin_username` | string | ノードへの SSH 接続に使用できる管理者のユーザー アカウントの名前。 | | |
| `ssh_settings.admin_password` | string | 管理者のユーザー アカウントのパスワード。 **`admin_password` または `ssh_key_value` のいずれかが必須です。** | | |
| `ssh_settings.ssh_key_value` | string | 管理者ユーザー アカウントの SSH 公開キー。 **`admin_password` または `ssh_key_value` のいずれかが必須です。** | | |
| `network_settings` | object | ネットワークのセキュリティ設定。 | | |
| `network_settings.vnet_name` | string | 新しい仮想ネットワーク (VNet) を作成するとき、または既存の仮想ネットワークを参照するときの、仮想ネットワークの名前。 | | |
| `network_settings.subnet` | string | 新しい VNet を作成するとき、既存の VNet を参照するとき、または既存の VNet のサブネットの完全修飾リソース ID を参照するときの、サブネットの名前。 サブネット ID が指定されている場合は `network_settings.vnet_name` を指定しないでください。 サブネット ID は、別のリソース グループ内の VNet またはサブネットを参照できます。 | | |
| `identity` | object | コンピューティングに割り当てるマネージド ID 構成。 AmlCompute クラスターは、システムによって割り当てられた 1 つの ID またはユーザーによって割り当てられた複数の ID のみをサポートします。両方同時にはサポートされません。 | | |
| `identity.type` | string | コンピューティングに割り当てるマネージド ID の種類。 型が `user_assigned` の場合は、`identity.user_assigned_identities` プロパティも指定する必要があります。 | `system_assigned`, `user_assigned` | |
| `identity.user_assigned_identities` | array | ユーザー割り当て ID の完全修飾リソース ID の一覧。 | | |

## <a name="remarks"></a>Remarks

`az ml compute` コマンドを使用して、Azure Machine Learning コンピューティング クラスター (AmlCompute) を管理できます。

## <a name="examples"></a>例

例は、[GitHub リポジトリの例](https://github.com/Azure/azureml-examples/tree/main/cli/resources/compute)にあります。 以下にいくつか示します。

## <a name="yaml-minimal"></a>YAML: 最小

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/cluster-minimal.yml":::

## <a name="yaml-basic"></a>YAML: 基本

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/cluster-basic.yml":::

## <a name="yaml-custom-location"></a>YAML: カスタムの場所

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/cluster-location.yml":::

## <a name="yaml-low-priority"></a>YAML: 低優先度

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/cluster-low-priority.yml":::

## <a name="yaml-ssh-username-and-password"></a>YAML: SSH ユーザー名およびパスワード

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/cluster-ssh-password.yml":::

## <a name="next-steps"></a>次の手順

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)
