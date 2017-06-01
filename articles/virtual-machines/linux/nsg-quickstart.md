---
title: "Azure CLI 2.0 を使用した Linux VM へのポートの開放 | Microsoft Docs"
description: "Azure Resource Manager デプロイメント モデルと Azure CLI 2.0 を使用して、Linux VM へのポートを開き、エンドポイントを作成する方法について説明する"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: f1b4910ed0f63fd4914113e83e9fe4b483147c3a
ms.contentlocale: ja-jp
ms.lasthandoff: 05/11/2017


---
# <a name="open-ports-and-endpoints-to-a-linux-vm-with-the-azure-cli"></a>Azure CLI 使用した Linux VM へのポートとエンドポイントの開放
サブネットまたは仮想マシン (VM) ネットワーク インターフェイスでネットワーク フィルターを作成して、Azure で VM へのポートを開くか、エンドポイントを作成します。 着信および発信の両方のトラフィックを制御するこれらのフィルターを、トラフィックを受信するリソースに接続されているネットワーク セキュリティ グループに配置します。 ポート 80 での Web トラフィックの一般的な例を使用して説明します。 この記事では、Azure CLI 2.0 を使用して VM へのポートを開く方法を説明します。 これらの手順は、[Azure CLI 1.0](nsg-quickstart-nodejs.md) を使用して実行することもできます。


## <a name="quick-commands"></a>クイック コマンド
ネットワーク セキュリティ グループとルールを作成するには、最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) がインストールされ、[az login](/cli/azure/#login) を使用して Azure アカウントにログインしている必要があります。

次の例では、パラメーター名を独自の値を置き換えます。 たとえば、*myResourceGroup*、*myNetworkSecurityGroup*、*myVnet* といったパラメーター名にします。

[az network nsg create](/cli/azure/network/nsg#create) で、ネットワーク セキュリティ グループを作成します。 次の例では、*myNetworkSecurityGroup* という名前のネットワーク セキュリティ グループを *eastus* に作成します。

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

[az network nsg rule create](/cli/azure/network/nsg/rule#create) を使用して、Web サーバーへの HTTP トラフィックを許可する規則を追加します (または SSH アクセス、データベース接続など、独自のシナリオに合わせて調整します)。 次の例では、*myNetworkSecurityGroupRule* という名前の規則を作成して、ポート 80 での TCP トラフィックを許可します。

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```

[az network nic update](/cli/azure/network/nic#update)を使用して、ネットワーク セキュリティ グループと仮想マシンのネットワーク インターフェイス (NIC) を関連付けます。 次の例では、*myNic* という名前の既存の NIC を *myNetworkSecurityGroup* という名前のネットワーク セキュリティ グループに関連付けます。

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

また、ネットワーク セキュリティ グループは、単一の VM のネットワーク インターフェイスに関連付けるのではなく、[az network vnet subnet update](/cli/azure/network/vnet/subnet#update) を使用して、仮想ネットワークのサブネットに関連付けることもできます。 次の例では、*myVnet* 仮想ネットワーク内の *mySubnet* という名前の既存のサブネットを *myNetworkSecurityGroup* という名前のネットワーク セキュリティ グループに関連付けます。

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>ネットワーク セキュリティ グループの詳細
このページのクイック コマンドでは、VM にフローするトラフィックの開始と実行を行うことができます。 ネットワーク セキュリティ グループには優れた機能が多数用意されており、リソースへのアクセスをきめ細かく制御できます。 詳細については、 [ネットワーク セキュリティ グループと ACL 規則の作成](../../virtual-network/virtual-networks-create-nsg-arm-cli.md)に関するページをご覧ください。

ネットワーク セキュリティ グループと ACL 規則は、Azure Resource Manager のテンプレートの一部として定義できます。 詳細については、 [テンプレートを使用したネットワーク セキュリティ グループの作成](../../virtual-network/virtual-networks-create-nsg-arm-template.md)に関するページをご覧ください。

ポート フォワーディングを使用して、一意の外部ポートを VM 上の内部ポートにマップする必要がある場合は、ロード バランサーとネットワーク アドレス変換 (NAT) 規則を使用します。 たとえば、TCP ポート 8080 を外部に公開し、VM 上の TCP ポート 80 にトラフィックを送ることができます。 詳細については、 [インターネットに接続するロード バランサーの作成](../../load-balancer/load-balancer-get-started-internet-arm-cli.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
この例では、HTTP トラフィックを許可する単純な規則を作成します。 より精密な環境の作成については、次の記事で確認できます。

* [Azure リソース マネージャーの概要](../../azure-resource-manager/resource-group-overview.md)
* [ネットワーク セキュリティ グループ (NSG) について](../../virtual-network/virtual-networks-nsg.md)
* [ロード バランサー用の Azure Resource Manager の概要](../../load-balancer/load-balancer-arm.md)


