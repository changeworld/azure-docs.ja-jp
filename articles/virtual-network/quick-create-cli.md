---
title: 仮想ネットワークの作成 - クイック スタート - Azure CLI | Microsoft Docs
description: このクイック スタートでは、Azure Portal を使用した仮想ネットワークの作成について説明します。 仮想ネットワークによって、仮想マシンなどの Azure リソースが互いにプライベートな通信を行ったりインターネットと通信したりできるようになります。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: bb45b2b4ecd89187e94066bc81782174738fe3a9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38232703"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>クイック スタート: Azure CLI を使用した仮想ネットワークの作成

仮想ネットワークによって、仮想マシン (VM) などの Azure リソースが互いにプライベートな通信を行ったりインターネットと通信したりできるようになります。 このクイック スタートでは、仮想ネットワークの作成方法について説明します。 仮想ネットワークを作成したら、2 つの VM を仮想ネットワークにデプロイします。 その後、インターネットから 1 つの VM に接続し、もう 1 つの VM とプライベートに通信します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用することを選択する場合、このクイック スタートでは、Azure CLI バージョン 2.0.28 以降を実行している必要があります。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 


## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

仮想ネットワークを作成するには、その前に、仮想ネットワークを含めるリソース グループを作成する必要があります。 [az group create](/cli/azure/group#az_group_create) を使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) を使用して仮想ネットワークを作成します。 次の例では、*default* という名前のサブネットを使って、*myVirtualNetwork* という名前の既定の仮想ネットワークを作成します。

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>仮想マシンを作成する

仮想ネットワークに 2 つの VM を作成します。

### <a name="create-the-first-vm"></a>最初の VM を作成する

[az vm create](/cli/azure/vm#az_vm_create) を使用して VM を作成します。 既定のキーの場所にまだ SSH キーが存在しない場合は、コマンドを使って SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。 `--no-wait` オプションを使用すると、VM はバックグラウンドで作成されるため、次の手順に進むことができます。 次の例では、*myVm1* という名前の VM を作成します。

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>2 つ目の VM を作成する

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

VM の作成には数分かかります。 VM が作成された後、Azure CLI は次の例のような出力を返します。 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm1",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

**publicIpAddress** を書き留めておきます。 このアドレスは、次の手順でインターネットから VM に接続するために使用します。

## <a name="connect-to-a-vm-from-the-internet"></a>インターネットから VM に接続する

`<publicIpAddress>` を *myVm2* VM のパブリック IP アドレスで置換して、次のコマンドを入力します。

```bash 
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>VM 間の通信

*myVm2* VM と *myVm1* VM の間のプライベートな通信を確認するには、次のコマンドを入力します。

```bash
ping myVm1 -c 4
```

*10.0.0.4* から 4 つの応答を受信します。

*myVm2* VM との SSH セッションを終了します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、[az group delete](/cli/azure/group#az_group_delete) を使用して、リソース グループとそのグループに含まれているすべてのリソースを削除できます。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、既定の仮想ネットワークと 2 つの VM を作成しました。 インターネットから 1 つの VM に接続し、その VM ともう 1 つの VM のプライベート通信を行いました。 仮想ネットワーク設定について詳しくは、[仮想ネットワークの管理](manage-virtual-network.md)に関する記事をご覧ください。 

既定で、Azure では仮想マシン間の無制限のプライベート通信が許可されますが、インターネットから Windows VM へは受信リモート デスクトップ接続のみが許可されます。 VM に対するさまざまな種類のネットワーク通信を許可または制限する方法については、[ネットワーク トラフィックのフィルター処理](tutorial-filter-network-traffic.md)に関するページを参照してください。
