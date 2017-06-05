---
title: "複数の NIC を持つ Linux VM を Azure に作成する | Microsoft Docs"
description: "Azure CLI 2.0 または Resource Manager テンプレートを使って、複数の NIC が接続された Linux VM を作成する方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 8a2931e462079c101c91497d459d7d3126234244
ms.contentlocale: ja-jp
ms.lasthandoff: 05/11/2017


---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>複数のネットワーク インターフェイス カードを使用して Linux 仮想マシンを Azure に作成する方法
Azure では、複数の仮想ネットワーク インターフェイス (NIC) を持つ仮想マシン (VM) を作成できます。 一般的なシナリオは、フロント エンドおよびバック エンド接続用に別々のサブネットを使用するか、監視またはバックアップ ソリューション専用のネットワークを用意することです。 この記事では、接続された複数の NIC を使用して VM を作成する方法、および既存の VM の NIC を追加または削除する方法について詳しく説明します。 独自の Bash スクリプト内に複数の NIC を作成する方法など、詳しくは、「[Azure CLI を使用した複数の NIC VM のデプロイ](../../virtual-network/virtual-network-deploy-multinic-arm-cli.md)」をご覧ください。 [VM のサイズ](sizes.md)によってサポートされる NIC の数が異なります。VM のサイズを決める際はご注意ください。

この記事では、Azure CLI 2.0 を使用して複数の NIC を持つ VM を作成する方法について説明します。 これらの手順は、[Azure CLI 1.0](multiple-nics-nodejs.md) を使用して実行することもできます。


## <a name="create-supporting-resources"></a>関連リソースを作成する
最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールし、[az login](/cli/azure/#login) を使用して Azure アカウントにログインします。

次の例では、パラメーター名を独自の値を置き換えます。 たとえば、*myResourceGroup*、*mystorageaccount*、*myVM* といったパラメーター名にします。

最初に、[az group create](/cli/azure/group#create) を使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli
az group create --name myResourceGroup --location eastus
```

[az network vnet create](/cli/azure/network/vnet#create) で仮想ネットワークを作成します。 次の例では、*myVnet* という名前の仮想ネットワークと *mySubnetFrontEnd* という名前のサブネットを作成します。

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 192.168.1.0/24
```

[az network vnet subnet create](/cli/azure/network/vnet/subnet#create) を使用してバックエンド トラフィックのサブネットを作成します。 次の例では、*mySubnetBackEnd* という名前のサブネットを作成します。

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

[az network nsg create](/cli/azure/network/nsg#create) で、ネットワーク セキュリティ グループを作成します。 次の例では、*myNetworkSecurityGroup* という名前のネットワーク セキュリティ グループを作成します。

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>複数の NIC を作成して構成する
[az network nic create](/cli/azure/network/nic#create) を使用して、2 つの NIC を作成します。 次の例では、ネットワーク セキュリティ グループに接続された、*myNic1* と *myNic2* という名前の 2 つの NIC を作成します (1 つの NIC が各サブネットに接続します)。

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>VM を作成して NIC を接続する
VM を作成するとき、`--nics` を使用して、作成した NIC を指定します。 VM のサイズを選択する際には注意が必要です。 1 つの VM に追加できる NIC の合計数には制限があります。 詳しくは、 [Linux VM のサイズ](sizes.md)に関する記事をご覧ください。 

[az vm create](/cli/azure/vm#create) を使用して VM を作成します。 次の例では、*myVM* という名前の VM を作成します。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

## <a name="add-a-nic-to-a-vm"></a>VM に NIC を追加する
前の手順では、複数の NIC を含む VM を作成しました。 Azure CLI 2.0 を使用して NIC を既存の VM に追加することもできます。 

[az network nic create](/cli/azure/network/nic#create) を使用して別の仮想 NIC を作成します。 次の例では、バックエンドのサブネットおよび前の手順で作成されたネットワーク セキュリティ グループに接続された *myNic3* という名前の NIC を作成します。

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

NIC を既存の VM に追加するには、最初に [az vm deallocate](/cli/azure/vm#deallocate) を使用して VM の割り当てを解除します。 次の例では、*myVM* という名前の VM の割り当てを解除します。

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

[az vm nic add](/cli/azure/vm/nic#add) を使用して NIC を追加します。 次の例では、*myNic3* を *myVM* に追加します。

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

[az vm start](/cli/azure/vm#start) を使用して VM を起動します。

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

## <a name="remove-a-nic-from-a-vm"></a>NIC を VM から削除する
NIC を既存の VM から削除するには、最初に [az vm deallocate](/cli/azure/vm#deallocate) を使用して VM の割り当てを解除します。 次の例では、*myVM* という名前の VM の割り当てを解除します。

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

[az vm nic remove](/cli/azure/vm/nic#remove) を使用して NIC を削除します。 次の例では、*myNic3* を *myVM* から削除します。

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM 
    --nics myNic3
```

[az vm start](/cli/azure/vm#start) を使用して VM を起動します。

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Resource Manager テンプレートを使用して複数の NIC を作成する
Azure Resource Manager テンプレートで宣言型の JSON ファイルを使用して環境を定義します。 詳しくは、「[Azure Resource Manager の概要](../../azure-resource-manager/resource-group-overview.md)」をご覧ください。 Resource Manager テンプレートでは、複数の NIC の作成など、デプロイ時にリソースの複数のインスタンスを作成することができます。 *copy* を使用して、作成するインスタンスの数を指定します。

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

詳細については、[*copy* を使用した複数のインスタンスの作成](../../resource-group-create-multiple.md)に関する記事を参照してください。 

`copyIndex()` を使用してリソース名に数値を追加することもできます。これにより、`myNic1`、`myNic2` などを作成することができます。インデックス値を追加する例を次に示します。

```json
"name": "[concat('myNic', copyIndex())]", 
```

完全な例については、「 [Resource Manager テンプレートを使用して複数の NIC を作成する](../../virtual-network/virtual-network-deploy-multinic-arm-template.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
複数の NIC を持つ VM を作成する際は、 [Linux VM のサイズ](sizes.md) を確認してください。 VM の各サイズでサポートされている NIC の最大数に注意してください。 
