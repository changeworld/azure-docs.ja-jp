---
title: Azure CLI スクリプト サンプル - 多層アプリケーション用のネットワークの作成 | Microsoft Docs
description: Azure CLI スクリプト サンプル - 多層アプリケーション用の仮想ネットワークの作成
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: e84c9555204a88622282c790d352ff7b052f560c
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2018
ms.locfileid: "30840230"
---
# <a name="create-a-network-for-multi-tier-applications-script-sample"></a>多層アプリケーション用のネットワークの作成のスクリプト サンプル

このスクリプト サンプルは、フロントエンド サブネットとバックエンド サブネットを含む仮想ネットワークを作成します。 フロントエンド サブネットへのトラフィックは HTTP と SSH に制限され、バックエンド サブネットへのトラフィックは MySQL、ポート 3306 に制限されます。 このスクリプトを実行すると、2 つの仮想マシンが、Web サーバーと MySQL ソフトウェアをデプロイできる各サブネットに 1 つずつ作成されます。

Azure [Cloud Shell](https://shell.azure.com/bash) から、またはローカルの Azure CLI インストールからスクリプトを実行することができます。 ローカルで CLI を使用する場合、このスクリプトでは、バージョン 2.0.28 以降を実行する必要があります。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 CLI をローカルで実行している場合、`az login` を実行して Azure との接続を作成する必要もあります。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>サンプル スクリプト


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.sh  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、およびすべての関連リソースを削除します。

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは次のコマンドを使用して、リソース グループ、仮想ネットワーク、およびネットワーク セキュリティ グループを作成します。 以下の表の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Azure 仮想ネットワークとフロントエンド サブネットを作成します。 |
| [az network subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | バックエンド サブネットを作成します。 |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | インターネットから VM にアクセスするためのパブリック IP アドレスを作成します。 |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | 仮想ネットワーク インターフェイスを作成し、それらを仮想ネットワークのフロントエンドおよびバックエンド サブネットに接続します。 |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | フロントエンドおよびバックエンド サブネットに関連付けられているネットワーク セキュリティ グループ (NSG) を作成します。 |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) |特定のサブネットに対して特定のポートを許可またはブロックする NSG ルールを作成します。 |
| [az vm create](/cli/azure/vm#az_vm_create) | 仮想マシンを作成し、NIC を各 VM にアタッチします。 このコマンドでは、使用する仮想マシン イメージと管理者の資格情報も指定します。 |
| [az group delete](/cli/azure/group#az_group_delete) | リソース グループとそれに含まれるすべてのリソースを削除します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

他の仮想ネットワーク CLI スクリプトのサンプルについては、[仮想ネットワーク CLI のサンプル](../cli-samples.md)に関するページを参照してください。
