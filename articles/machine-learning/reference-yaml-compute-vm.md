---
title: CLI (v2) 接続済み仮想マシン YAML スキーマ
titleSuffix: Azure Machine Learning
description: CLI (v2) 接続済み仮想マシン スキーマのリファレンス ドキュメント。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: b639c4193a246ccf7f57520089132f0f49d16ebb
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062436"
---
# <a name="cli-v2-attached-virtual-machine-yaml-schema"></a>CLI (v2) 接続済み仮想マシン YAML スキーマ

ソース JSON スキーマは https://azuremlschemas.azureedge.net/latest/vmCompute.schema.json にあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 構文

| キー | Type | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | YAML スキーマ。 Azure Machine Learning 用 VS Code 拡張機能を使って YAML ファイルを作成する場合は、ファイルの先頭に `$schema` を含めることで、スキーマとリソースの入力候補を呼び出すことができます。 | | |
| `type` | string | **必須。** コンピューティングの種類。 | `virtualmachine` | |
| `name` | string | **必須。** コンピューティングの名前。 | | |
| `description` | string | コンピューティングの説明。 | | |
| `resource_id` | string | **必須。** コンピューティング ターゲットとしてワークスペースにアタッチする Azure Virtual Machine の完全修飾リソース ID。 | | |
| `ssh_settings` | object | 仮想マシンに接続するための SSH 設定。 | | |
| `ssh_settings.admin_username` | string | 仮想マシンへの SSH 接続に使用できる管理者のユーザー アカウントの名前。 | | |
| `ssh_settings.admin_password` | string | 管理者のユーザー アカウントのパスワード。 **`admin_password` または `ssh_private_key_file` のいずれかが必須です。** | | |
| `ssh_settings.ssh_private_key_file` | string | 管理者のユーザー アカウントの SSH 秘密キー ファイルへのローカル パス。 **`admin_password` または `ssh_private_key_file` のいずれかが必須です。** | | |
| `ssh_settings.ssh_port` | 整数 (integer) | 仮想マシンの SSH ポート。 | | `22` |

## <a name="remarks"></a>Remarks

`az ml compute` コマンドは、Azure Machine Learning ワークスペースに接続されている Virtual Machines (VM) を管理するために使用できます。

## <a name="examples"></a>例

例は、[例の GitHub リポジトリ](https://github.com/Azure/azureml-examples/tree/main/cli/resources/compute)内にあります。 以下にいくつか示します。

## <a name="yaml-basic"></a>YAML: 基本

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/vm-attach.yml":::

## <a name="next-steps"></a>次の手順

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)
