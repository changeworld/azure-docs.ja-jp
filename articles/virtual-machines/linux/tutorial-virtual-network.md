---
title: "Azure 仮想ネットワークと Linux 仮想マシン | Microsoft Docs"
description: "チュートリアル - Azure CLI を使用した Azure 仮想ネットワークと Linux 仮想マシンの管理"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: c1494009c126c4715caf7972a0f59cffdaba3a7f
ms.lasthandoff: 04/22/2017

---

# <a name="manage-azure-virtual-networks-and-linux-virtual-machines-with-the-azure-cli"></a>Azure CLI を使用した Azure 仮想ネットワークと Linux 仮想マシンの管理

このチュートリアルでは、仮想ネットワーク内に複数の仮想マシン (VM) を作成し、これらの仮想マシン間のネットワーク接続を構成する方法について説明します。 このチュートリアルを完了すると、SSH 接続用のポート 22 と HTTP 接続用のポート 80 で、インターネットから "フロントエンド" の VM にアクセスできるようになります。 MySQL データベース搭載の "バックエンド" VM は分離され、ポート 3306 でフロントエンド VM からのみアクセスできるようになります。

このチュートリアルの手順は、最新バージョンの [Azure CLI 2.0](/cli/azure/install-azure-cli) を使用して行うことができます。

## <a name="create-vm-and-vnet"></a>VM と VNet の作成

Azure 仮想ネットワーク (VNet) は、クラウド内のユーザー独自のネットワークを表すものです。 サブスクリプション専用に Azure クラウドが論理的に分離されています。 VNet 内には、サブネット、これらのサブネットへの接続規則、VM からサブネットへの接続があります。 Azure CLI を使用すると、VM へのアクセスをサポートするために必要なすべてのネットワーク関連リソースを簡単に作成できます。 

他の Azure リソースを作成する前に、az group create を使用してリソース グループを作成する必要があります。 次の例では、`myRGNetwork` という名前のリソース グループを `westus` の場所に作成します。

```azurecli
az group create --name myRGNetwork --location westus
```

Azure CLI を使用して仮想マシンを作成すると、同時にこの仮想マシンに必要なネットワーク リソースも自動で作成されます。 [az vm create](https://docs.microsoft.com/cli/azure/vm#create) を使用して、`myFrontendVM` とその関連ネットワーク リソースを作成します。

```azurecli
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --image UbuntuLTS \
  --generate-ssh-keys
```

VM が作成されたら、パブリック IP アドレスを記録します。 このアドレスは、本チュートリアルの後半で使用します。

```bash
{
  "fqdns": "",
  "id": "/subscriptions/{id}/resourceGroups/myRGNetwork/providers/Microsoft.Compute/virtualMachines/myFrontendVM",
  "location": "westus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myRGNetwork"
}
```

次のネットワーク リソースが作成されています。

- **myFrontendVMNSG** – `myFrontendVM` への着信トラフィックを保護するネットワーク セキュリティ グループです。
- **myVMPublicIP** – インターネットで `myFrontendVM` にアクセスできるようにするパブリック IP アドレスです。
- **myVMVMNic** – `myFrontendVM` がネットワークに接続できるようにする仮想ネットワーク インターフェイスです。
- **myVMVNET** – `myFrontendVM` が接続される仮想ネットワークです。

## <a name="install-web-server"></a>Web サーバーのインストール

`myFrontendVM` に SSH 接続を作成します。 サンプルの IP アドレスは、VM のパブリック IP アドレスに置き換えてください。

```bash
ssh 40.68.254.142
```

次のコマンドを実行して NGINX をインストールします。

```bash
sudo apt-get -y update && sudo apt-get -y install nginx
```

SSH セッションを終了します。

```bash
exit
```

## <a name="manage-internet-traffic"></a>インターネット トラフィックの管理

ネットワーク セキュリティ グループ (NSG) には、VNet に接続されたリソースへのネットワーク トラフィックを許可または拒否する一連のセキュリティ規則が含まれています。 NSG は、サブネット、または VM にアタッチされている個々の NIC に関連付けることができます。 ポートを介した VM へのアクセスの開始と終了は、NSG 規則を使用して行われます。 `myFrontendVM` を作成したときに、SSH 接続用の受信ポート 22 が自動的に開かれています。

[az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port) を使用して、`myFrontendVM` のポート 80 を開きます。

```azurecli
az vm open-port --port 80 --resource-group myRGNetwork --name myFrontendVM
```

これで、VM の パブリック IP アドレスを参照して NGINX サイトへアクセスできるようになりました。

![NGINX の既定のサイト](./media/quick-create-cli/nginx.png)

## <a name="manage-internal-traffic"></a>内部トラフィックの管理

VM のインターネット通信は、NSG を使用して構成することもできます。 このセクションでは、ネットワークに追加のサブネットを作成し、このサブネットに ポート 3306 での `myFrontendVM` から `myBackendVM` への接続を許可する NSG を割り当てる方法について説明します。 サブネットは、作成時に VM に割り当てられます。

[az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#create) を使用して、`myBackendNSG` という名前の新しいネットワーク セキュリティ グループを追加します。 

```azurecli
az network nsg create \
 --resource-group myRGNetwork \
 --name myBackendNSG
```

`myFrontendVM` と `myBackendVM` が VNet 内で相互に通信できるようにポートを設定します。 `az network rule create` を使用して、`myVMSubnet` のみからの `myBackendSubnet` へのトラフィックを許可する NSG ルールを追加します。

```azurecli
az network nsg rule create \
 --resource-group myRGNetwork \
 --nsg-name myBackendNSG \
 --name com-rule \
 --access Allow \
 --protocol Tcp \
 --direction Inbound \
 --priority 100 \
 --source-address-prefix 10.0.0.0/24 \
 --source-port-range "*" \
 --destination-address-prefix "*" \
 --destination-port-range 3306
```

## <a name="add-back-end-subnet"></a>バックエンド サブネットの追加

サブネットは、VNet の子リソースで、IP アドレスのプレフィックスを使用して、CIDR ブロック内のアドレス空間のセグメントの定義に役立ちます。 NIC をサブネットに追加し、VM に接続して、さまざまなワークロードへの接続を提供できます。

[az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#create) を使用して、`myFrontendVMVNet` に `myBackEndSubnet` を追加します。

```azurecli
az network vnet subnet create \
 --address-prefix 10.0.1.0/24 \
 --name myBackendSubnet \
 --resource-group myRGNetwork \
 --vnet-name myFrontendVMVNET \
 --network-security-group myBackendNSG
```

## <a name="create-back-end-vm"></a>バックエンド VM の作成

`az vm create` により、`myBackendSubnet` を使用する `myBackendVM` を作成します。

```azurecli
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --subnet myBackendSubnet \
  --vnet-name myFrontendVMVNET \
  --public-ip-address ""

```

## <a name="install-database"></a>データベースのインストール

このチュートリアルでは、開発環境の VM の秘密キーを `myFrontendVM` にコピーします。 運用環境では、VM の作成時に --generate-ssh-keys を使用するのではなく、この VM 専用のキーを作成することをお勧めします。 

バックエンド VM は、パブリックにアクセスするためのものではありません。 このセクションでは、SSH を使用して `myFrontendVM` にログインし、次に SSH を使用して `myFrontendVM` から `myBackendVM` へログインする方法について説明します。

サンプルの IP アドレスは、`myFrontendVM` のパブリック IP アドレスに置き換えてください。

```bash
scp ~/.ssh/id_rsa 40.68.254.142:~/.ssh/id_rsa
```

`myFrontendVM` に SSH 接続を作成します。 サンプルの IP アドレスは、`myFrontendVM` のパブリック IP アドレスに置き換えてください。

```bash
ssh 40.68.254.142
```

`myFrontendVM` からへ `myBackendVM` 接続します。

```bash
ssh myBackendVM
```

次のコマンドを実行して MySQL をインストールします。

```bash
sudo apt-get -y install mysql-server
```

MySQL の設定手順を実行します。

SSH セッションを終了します。

```bash
exit
```

MySQL は `myBackendVM` へのアプリケーションのインストール方法を示すためにインストールしており、このチュートリアルで実際に使用することはありません。
