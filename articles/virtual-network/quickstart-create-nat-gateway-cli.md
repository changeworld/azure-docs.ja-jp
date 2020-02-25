---
title: クイック スタート:NAT ゲートウェイの作成 - Azure CLI
titlesuffix: Azure Virtual Network NAT
description: このクイックスタートでは、Azure CLI を使用して NAT ゲートウェイを作成する方法について説明します。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 816dc9c4460792b56a7dbf0c5d77f92afd257e73
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429153"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-cli"></a>クイック スタート:Azure CLI を使用した NAT ゲートウェイの作成

このクイックスタートでは、Azure Virtual Network NAT サービスを使用する方法について説明します。 Azure 内の仮想マシンにアウトバウンド接続を提供する NAT ゲートウェイを作成しましょう。 

>[!NOTE] 
>Azure Virtual Network NAT は、現時点ではパブリック プレビューとして提供され、ご利用いただける[リージョン](https://azure.microsoft.com/global-infrastructure/regions/)が限られています。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms)」をご覧ください。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

このチュートリアルは、Azure Cloud Shell を使用して行うことができます。また、それぞれのコマンドをローカルで実行してもかまいません。  Azure Cloud Shell を使用したことがない場合は、[今すぐサインイン](https://shell.azure.com)して初期設定を行ってください。
これらのコマンドをローカルで実行する場合は、CLI をインストールする必要があります。  このチュートリアルでは、Azure CLI バージョン 2.0.71 以降のバージョンが実行されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

> [!IMPORTANT]
> ご利用のサブスクリプションで Virtual Network NAT [プレビューを有効](./nat-overview.md#enable-preview)にした後は、 https://aka.ms/natportal を使用してポータルにアクセスしてください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create](https://docs.microsoft.com/cli/azure/group) を使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、**myResourceGroupNAT** という名前のリソース グループを **eastus2** という場所に作成します。

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>NAT ゲートウェイの作成

### <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

パブリック インターネットにアクセスするには、NAT ゲートウェイに少なくとも 1 つのパブリック IP アドレスが必要です。 [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) を使用して、**myResourceGroupNAT** に **myPublicIP** というパブリック IP アドレス リソースを作成します。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>パブリック IP プレフィックスの作成

NAT ゲートウェイでは、1 つまたは複数のパブリック IP アドレス リソース、パブリック IP プレフィックス、またはその両方を使用できます。 このシナリオでは、デモンストレーションとしてパブリック IP プレフィックス リソースを追加することにします。   [az network public-ip prefix create](https://docs.microsoft.com/cli/azure/network/public-ip-prefix) を使用して、**myResourceGroupNAT** に **myPublicIPprefix** というパブリック IP プレフィックス リソースを作成します。

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>NAT ゲートウェイ リソースの作成

このセクションでは、NAT ゲートウェイ リソースを使用して、次の NAT サービス コンポーネントを作成、構成する方法について詳しく説明します。
  - NAT ゲートウェイ リソースによって変換されるアウトバウンド フローに使用される、パブリック IP プールとパブリック IP プレフィックス。
  - アイドル タイムアウトを既定値の 4 分から 10 分に変更します。

[az network nat gateway create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) を使用して、**myNATgateway** というグローバル Azure NAT ゲートウェイを作成します。 このコマンドでは、パブリック IP アドレス **myPublicIP** とパブリック IP プレフィックス **myPublicIPprefix** の両方を使用します。 アイドル タイムアウトは **10** 分に変更します。

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

この時点で、NAT ゲートウェイは機能する状態となっていますが、仮想ネットワークのどのサブネットでそれを使用するかを構成する作業だけ残っています。

## <a name="configure-virtual-network"></a>仮想ネットワークを構成する

VM をデプロイして NAT ゲートウェイを使用する前に、仮想ネットワークを作成しておく必要があります。

[az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) を使用して、**myResourceGroupNAT** に **mySubnet** という名前のサブネットがある **myVnet** という名前の仮想ネットワークを作成します。  この仮想ネットワークの IP アドレス空間は **192.168.0.0/16** です。 この仮想ネットワーク内のサブネットは **192.168.0.0/24** です。

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>送信元サブネット用の NAT サービスの構成

[az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet) を使用して、仮想ネットワーク **myVnet** 内の送信元サブネット **mySubnet** を、特定の NAT ゲートウェイ リソース **myNATgateway** を使用するように構成しましょう。  このコマンドにより、指定したサブネット上で NAT サービスがアクティブになります。

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

以降、インターネットを宛先とするすべてのアウトバウンド トラフィックがこの NAT ゲートウェイを使用するようになります。  UDR を構成する必要はありません。

## <a name="create-a-vm-to-use-the-nat-service"></a>NAT サービスを使用する VM の作成

今度は、NAT サービスを使用する VM を作成します。  この VM には、インスタンスレベルのパブリック IP として使用するパブリック IP が割り当てられます。そのパブリック IP を使用して、VM にアクセスすることができます。  NAT サービスでフロー方向が認識され、この NAT サービスが、サブネットの既定のインターネット宛先に取って代わることになります。 VM のパブリック IP アドレスは、アウトバウンド接続には使用されません。

### <a name="create-public-ip-for-source-vm"></a>送信元 VM 用のパブリック IP の作成

VM へのアクセスに使用するパブリック IP を作成します。  [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) を使用して、**myResourceGroupNAT** に **myPublicIPVM** というパブリック IP アドレス リソースを作成します。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>VM に使用する NSG の作成

Standard パブリック IP アドレスは "既定でセキュリティ保護" されているため、SSH のインバウンド アクセスを許可する NSG を作成する必要があります。 [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) を使用して、**myNSG** という NSG リソースを **myResourceGroupNAT** に作成します。

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>送信元 VM 上の SSH エンドポイントの公開

送信元 VM に SSH でアクセスするためのルールを NSG に作成します。 [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) を使用して、**myResourceGroupNAT** 内の **myNSG** という NSG に **ssh** という NSG ルールを作成します。

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSG \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-vm"></a>VM に使用する NIC の作成

[az network nic create](/cli/azure/network/nic#az-network-nic-create) を使用してネットワーク インターフェイスを作成し、パブリック IP アドレスとネットワーク セキュリティ グループに関連付けます。 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIPVM \
    --network-security-group myNSG
```

### <a name="create-vm"></a>VM を作成する

[az vm create](/cli/azure/vm#az-vm-create) を使用して、仮想マシンを作成します。  この VM の SSH キーを生成し、後で使用するために秘密キーを格納します。

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys
```

VM がデプロイされるまで待ってから、残りの手順に進んでください。

## <a name="discover-the-ip-address-of-the-vm"></a>VM の IP アドレスの検出

まず、作成した VM の IP アドレスを検出する必要があります。 VM のパブリック IP アドレスを取得するには、[az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) を使用します。 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>パブリック IP アドレスをコピーしたら、VM へのアクセスに使用できるようメモ帳に貼り付けます。

### <a name="sign-in-to-vm"></a>VM へのサインイン

Cloud Shell には、前の操作で生成した SSH の資格情報が格納されているはずです。  ご使用のブラウザーで [Azure Cloud Shell](https://shell.azure.com) を開きます。 前の手順で取得した IP アドレスを使用して、仮想マシンに SSH 接続します。

```bash
ssh <ip-address-destination>
```

これで、NAT サービスを使用する準備が整いました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループとそこに含まれているすべてのリソースは、不要になったら、[az group delete](/cli/azure/group#az-group-delete) コマンドを使用して削除できます。

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、NAT ゲートウェイとそれを使用する VM を作成しました。 

Azure Monitor でメトリックを見て、NAT サービスの稼動状態を確認しましょう。 利用可能な SNAT ポートのリソース枯渇などの問題を診断します。  SNAT ポートのリソース枯渇は、新しいパブリック IP アドレス リソースかパブリック IP プレフィックス リソース、またはその両方を追加することで解決できます。


- [Azure Virtual Network NAT](./nat-overview.md) について学習する。
- [NAT ゲートウェイ リソース](./nat-gateway-resource.md)について学習する。
- [Azure CLI を使用して NAT ゲートウェイ リソース](./quickstart-create-nat-gateway-cli.md)をデプロイするためのクイックスタート。
- [Azure PowerShell を使用して NAT ゲートウェイ リソース](./quickstart-create-nat-gateway-powershell.md)をデプロイするためのクイックスタート。
- [Azure portal を使用して NAT ゲートウェイ リソース](./quickstart-create-nat-gateway-portal.md)をデプロイするためのクイックスタート。
- [パブリック プレビューに関するフィードバックを送る](https://aka.ms/natfeedback)。
> [!div class="nextstepaction"]

