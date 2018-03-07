---
title: "高速ネットワークを使った Azure 仮想マシンの作成 | Microsoft Docs"
description: "高速ネットワークを使った Linux 仮想マシンの作成方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/02/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 82a0a336e8315d7fe9862e22e47a6847f94bfcb1
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2018
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>高速ネットワークを使った Linux 仮想マシンの作成

> [!IMPORTANT] 
> 仮想マシンは、高速ネットワークを有効にして作成する必要があります。 この機能は、既存の仮想マシンでは有効にできません。 高速ネットワークを有効にするには、以下の手順を実行します。
>   1. 仮想マシンを削除します。
>   2. 高速ネットワークを有効にして仮想マシンを再作成します。
>

このチュートリアルでは、高速ネットワークを有効にして Linux 仮想マシン (VM) を作成する方法について説明します。 高速ネットワークによって、VM との間でシングル ルート I/O 仮想化 (SR-IOV) が可能になり、ネットワークのパフォーマンスが大幅に向上します。 この高パフォーマンスのパスによってデータパスからホストがバイパスされ、サポートされる VM タイプの最も要件の厳しいネットワーク ワークロードで使用した場合でも、待ち時間、ジッター、CPU 使用率が軽減されます。 次の図は、2 台の VM 間の通信を、高速ネットワークを使う場合と使わない場合とで比較したものです。

![比較](./media/create-vm-accelerated-networking/accelerated-networking.png)

高速ネットワークを使用しない場合は、VM に出入りするすべてのネットワーク トラフィックがホストと仮想スイッチをスキャンする必要があります。 仮想スイッチでは、ネットワーク セキュリティ グループ、アクセス制御リスト、分離、その他のネットワーク仮想化サービスなど、すべてのポリシーが適用されます。 仮想スイッチの詳細については、[Hyper-V ネットワーク仮想化と仮想スイッチ](https://technet.microsoft.com/library/jj945275.aspx)に関する記事を参照してください。

高速ネットワークを使用した場合、ネットワーク トラフィックは、VM のネットワーク インターフェイス (NIC) に到達した後、VM に転送されます。 仮想スイッチによって適用されるすべてのネットワーク ポリシーはオフロードされ、ハードウェアで適用されるようになりました。 ハードウェアにポリシーを適用することによって、ホストに適用されるポリシーをすべて維持したまま、ホストや仮想スイッチをバイパスして、NIC からネットワーク トラフィックを直接 VM に転送できます。

高速ネットワークのメリットが得られるのは、高速ネットワークが有効になっている VM だけです。 最適な結果を得るには、同じ Azure 仮想ネットワーク (VNet) に接続された 2 台以上の VM でこの機能を有効にしておくことをお勧めします。 VNet の境界を越えて通信するときや、オンプレミスで接続する場合、全体的な待ち時間に対してこの機能がもたらす効果は限定的です。

## <a name="benefits"></a>メリット
* **待機時間の短縮 / 1 秒あたりのパケット数 (pps)の向上:** データパスから仮想スイッチを削除することで、ホストにおけるパケットのポリシー処理に必要な時間がなくなるため、VM 内で処理できるパケット数が増加します。
* **ジッターの削減:** 仮想スイッチの処理は、適用するポリシーの量と、処理を行う CPU のワークロードによって異なります。 ハードウェアへのポリシーの適用をオフロードすると、パケットが直接 VM に配信され、ホストと VM 間の通信とソフトウェアによる干渉やコンテキスト スイッチがなくなるため、そのばらつきはなくなります。
* **CPU 使用率の削減:** ホストの仮想スイッチをバイパスすることによって、ネットワーク トラフィックを処理するための CPU の使用率を軽減できます。

## <a name="supported-operating-systems"></a>サポートされているオペレーティング システム
* **Ubuntu 16.04**: 4.11.0-1013 以上のカーネル バージョン
* **SLES 12 SP3**: 4.4.92-6.18 以上のカーネル バージョン
* **RHEL 7.4**: 7.4.2017120423 以上のカーネル バージョン
* **CentOS 7.4**: 7.4.20171206 以上のカーネル バージョン

## <a name="supported-vm-instances"></a>サポートされている VM インスタンス
高速ネットワークは、4 つ以上の vCPU を持つ、コンピューティングに最適化された多くの汎用のインスタンス サイズでサポートされています。 ハイパースレッディングをサポートする D/DSv3 や E/ESv3 などのインスタンスでは、8 以上の vCPU を持つ VM インスタンスで高速ネットワークがサポートされています。  D/DSv2、D/DSv3、E/ESv3、F/Fs/Fsv2 および Ms/Mms のシリーズがサポートされています。 

VM インスタンスの詳細については、「[Linux 仮想マシンのサイズ](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。

## <a name="regions"></a>リージョン
東アジアを除くすべてのパブリック Azure リージョンで使用できます。   Azure Government クラウドはまだサポートされていません。

## <a name="limitations"></a>制限事項
この機能を使用する場合は、次の制限事項があります。

* **ネットワーク インターフェイスの作成:** 高速ネットワークは、新しい NIC でのみ有効にできます。 既存の NIC に対して有効にすることはできません。
* **VM の作成:** 高速ネットワークを有効にした NIC は、VM の作成時にのみ VM に接続できます。 既存の VM に NIC を接続することはできません。 VM を既存の可用性セットに追加する場合は、可用性セット内のすべての VM でも高速ネットワークが有効になっている必要があります。
* **Azure Resource Manager でのデプロイのみ:** 仮想マシン (クラシック) は高速ネットワークを使用してデプロイできません。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールし、[az login](/cli/azure/#az_login) を使用して Azure アカウントにログインします。 次の例では、パラメーター名を独自の値を置き換えます。 たとえば、*myResourceGroup*、*myNic*、*myVM* といったパラメーター名にします。

[az group create](/cli/azure/group#az_group_create) を使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを場所 *centralus* に作成します。

```azurecli
az group create --name myResourceGroup --location centralus
```

「[Linux accelerated networking](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview)」 (Linux 高速ネットワーク) に記載されているサポート対象の Linux リージョンを選択する必要があります。

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) を使用して仮想ネットワークを作成します。 次の例では、1 つのサブネットで *myVnet* という名前の仮想ネットワークを作成します。

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成
[az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) で、ネットワーク セキュリティ グループを作成します。 次の例では、*myNetworkSecurityGroup* という名前のネットワーク セキュリティ グループを作成します。

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

ネットワーク セキュリティ グループには、既定の規則がいくつか含まれています。そのうちの 1 つは、インターネットからのすべての着信アクセスを無効にします。 ポートを開き、[az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) を使用して、仮想マシンに SSH アクセスできるようにします。

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>高速ネットワークを使ったネットワーク インターフェイスの作成

パブリック IP アドレスは、[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) で作成します。 インターネットから仮想マシンにアクセスする計画がない場合、パブリック IP アドレスは不要ですが、この記事の手順を完了するには必要です。

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

高速ネットワークを有効にし、[az network nic create](/cli/azure/network/nic#az_network_nic_create) を使用してネットワーク インターフェイスを作成します。 次の例では、*myVnet* 仮想ネットワークの *mySubnet* サブネットの *myNic* というネットワーク インターフェイスを作成し、*myNetworkSecurityGroup* ネットワーク セキュリティ グループをネットワーク インターフェイスに関連付けます。

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nic"></a>VM を作成して NIC を接続する
VM を作成するとき、`--nics` を使用して作成した NIC を指定します。 「[Linux accelerated networking](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview)」 (Linux 高速ネットワーク) に記載されているサイズとディストリビューションを選択する必要があります。 

[az vm create](/cli/azure/vm#az_vm_create) を使用して VM を作成します。 次の例では、UbuntuLTS イメージと高速ネットワークをサポートするサイズ (*Standard_DS4_v2*) を使用して、*myVM* という VM を作成します。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

すべての VM のサイズと特性の一覧は、[Linux VM のサイズ](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページを参照してください。

VM が作成されると、次のサンプル出力のような出力が返されます。 **publicIpAddress** を書き留めておきます。 このアドレスは、後の手順で VM にアクセスするために使われます。

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="confirm-that-accelerated-networking-is-enabled"></a>高速ネットワークが有効になっていることを確認します。

次のコマンドを使用して、VM との SSH セッションを作成します。 作成した仮想マシンに割り当てたパブリック IP アドレスに `<your-public-ip-address>` を置き換え、VM の作成時に `--admin-username` に異なる値を使用していた場合は、*azureuser* を置き換えます。

```bash
ssh azureuser@<your-public-ip-address>
```

Bash シェルから `uname -r` を入力し、カーネル バージョンが次のいずれかのバージョン以降であることを確認します。

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


`lspci` コマンドを使用して、Mellanox VF デバイスが VM に公開されていることを確認します。 次の出力のような出力が返されます。

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

`ethtool -S eth0 | grep vf_` コマンドを使用して、VF (仮想関数) のアクティビティを確認します。 次のサンプル出力と似た出力を受け取った場合、高速ネットワークは有効で、動作しています。

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
VM の高速ネットワークが有効になりました。
