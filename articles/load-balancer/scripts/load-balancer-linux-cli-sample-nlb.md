---
title: HA のために VM へのトラフィックを負荷分散する - Azure CLI - Azure Load Balancer
description: この Azure CLI スクリプトの例では、高可用性のために VM へのトラフィックを負荷分散する方法を示します
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3aaa7246806a1a7f1352481d26df00091e52a440
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790007"
---
# <a name="azure-cli-script-example-load-balance-traffic-to-vms-for-high-availability"></a>Azure CLI スクリプトの例: 高可用性のために VM へのトラフィックを負荷分散する

この Azure CLI スクリプトの例では、高可用性と負荷分散が設定された構成で、構成済みの複数の Ubuntu 仮想マシンを実行するために必要なものをすべて作成します。 このスクリプトを実行すると、Azure 可用性セットに接続され、Azure Load Balancer 経由でアクセス可能な仮想マシンが 3 つ作成されます。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想マシン、可用性セット、ロード バランサー、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Azure 仮想ネットワークとサブネットを作成します。 |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | 静的 IP アドレスおよび関連付けられる DNS 名を指定してパブリック IP アドレスを作成します。 |
| [az network lb create](/cli/azure/network/lb#az_network_lb_create) | Azure ロード バランサーを作成します。 |
| [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create) | ロード バランサー プローブを作成します。 ロード バランサー プローブは、ロード バランサー セット内の各 VM を監視するために使用されます。 アクセス不能になった VM には、トラフィックがルーティングされなくなります。 |
| [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | ロード バランサー規則を作成します。 このサンプルでは、ポート 80 に対する規則を作成します。 HTTP トラフィックは、ロード バランサーに到着すると、LB セット内のいずれかの VM のポート 80 にルーティングされます。 |
| [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#az_network_lb_inbound_nat_rule_create) | ロード バランサーのネットワーク アドレス変換 (NAT) 規則を作成します。  NAT 規則により、ロード バランサーのポートが VM のポートにマッピングされます。 このサンプルでは、ロード バランサー セット内の各 VM への SSH トラフィックに対する NAT 規則を作成します。  |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | インターネットと仮想マシン間のセキュリティ境界となるネットワーク セキュリティ グループ (NSG) を作成します。 |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | 受信トラフィックを許可する NSG 規則を作成します。 このサンプルでは、SSH トラフィック用にポート 22 を開きます。 |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | 仮想ネットワーク カードを作成し、作成したカードを仮想ネットワーク、サブネット、NSG に接続します。 |
| [az vm availability-set create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | 可用性セットを作成します。 可用性セットでは、障害が発生した場合でもセット全体に影響が及ばないように物理リソース全体に仮想マシンを分散させることで、アプリケーションの稼働時間を確保します。 |
| [az vm create](/cli/azure/vm#az_vm_create) | 仮想マシンを作成し、作成したマシンをネットワーク カード、仮想ネットワーク、サブネット、およびNSG に接続します。 このコマンドでは、使用する仮想マシン イメージと管理者の資格情報も指定します。  |
| [az group delete](/cli/azure/vm/extension#az_vm_extension_set) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の Azure Networking CLI のサンプル スクリプトは、[Azure のネットワーク のドキュメント](../cli-samples.md)に関するページで確認できます。