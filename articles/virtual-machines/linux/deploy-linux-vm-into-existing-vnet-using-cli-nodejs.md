---
title: "Azure CLI 1.0 による既存のネットワークへの Linux VM のデプロイ | Microsoft Docs"
description: "Azure CLI 1.0 を使用して既存の仮想ネットワークに Linux VM をデプロイする方法"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 767a3f7cadba6b1e71e5a8f5995a9db090e419dd
ms.contentlocale: ja-jp
ms.lasthandoff: 05/11/2017


---

# <a name="how-to-deploy-a-linux-virtual-machine-into-an-existing-azure-virtual-network-with-the-azure-cli-10"></a>Azure CLI 1.0 を使用して Linux 仮想マシンを既存の Azure 仮想ネットワークにデプロイする方法

この記事では、Azure CLI 1.0 を使用して仮想マシン (VM) を既存の仮想ネットワーク (VNet) にデプロイする方法を説明します。 要件は次のとおりです。

- [Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)
- [SSH パブリック キー ファイルおよびプライベート キー ファイル](mac-create-ssh-keys.md)


## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン
次のいずれかの CLI バージョンを使用してタスクを完了できます。

- [Azure CLI 1.0](#quick-commands) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI (本記事)
- [Azure CLI 2.0](deploy-linux-vm-into-existing-vnet-using-cli.md) - Resource Manager デプロイ モデル用の次世代 CLI


## <a name="quick-commands"></a>クイック コマンド

タスクをすばやく実行する必要がある場合のために、次のセクションでは、必要なコマンドの詳細について説明します。 詳細な情報と各手順のコンテキストが、ドキュメントの残りの部分に記載されています。[ここからお読みください](deploy-linux-vm-into-existing-vnet-using-cli.md#detailed-walkthrough)。

前提条件: リソース グループ、VNet、SSH 受信を使用した NSG、サブネット。 各例を独自の設定に置き換えてください。

### <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>仮想ネットワーク インフラストラクチャに VM をデプロイする

```azurecli
azure vm create myVM \
    -g myResourceGroup \
    -l eastus \
    -y linux \
    -Q Debian \
    -o mystorageaccount \
    -u myAdminUser \
    -M ~/.ssh/id_rsa.pub \
    -n myVM \
    -F myVNet \
    -j mySubnet \
    -N myVNic
```

## <a name="detailed-walkthrough"></a>詳細なチュートリアル

VNet やネットワーク セキュリティ グループなどの Azure 資産は、ほとんどデプロイされることのない有効期間の長い静的リソースにすることをお勧めします。 VNet をデプロイしたら、インフラストラクチャに悪影響を及ぼすことなく、新しいデプロイで再利用できます。 VNet は従来のハードウェア ネットワーク スイッチと考えてください。 デプロイごとにまったく新しいハードウェア スイッチを構成する必要はありません。 適切に構成された VNet があれば、新しいサーバーをその VNet に何度もデプロイできます。VNet の有効期間中に必要な変更は、あるとしてもごくわずかです。

## <a name="create-the-resource-group"></a>リソース グループの作成

まず、このチュートリアルで作成するすべてのものを整理するリソース グループを作成します。 リソース グループの詳細については、「[Azure Resource Manager の概要](../../azure-resource-manager/resource-group-overview.md)」を参照してください

```azurecli
azure group create myResourceGroup --location eastus
```

## <a name="create-the-vnet"></a>VNet の作成

まず、VM を起動する VNet を作成します。 このチュートリアルでは、VNet にサブネットが 1 つ含まれます。 Azure VNet の詳細については、「[Azure CLI を使用した仮想ネットワークの作成](../../virtual-network/virtual-networks-create-vnet-arm-cli.md)」をご覧ください。

```azurecli
azure network vnet create myVNet \
    --resource-group myResourceGroup \
    --address-prefixes 10.10.0.0/24 \
    --location eastus
```

## <a name="create-the-network-security-group"></a>ネットワーク セキュリティ グループの作成

サブネットは既存のネットワーク セキュリティ グループの背後に作成されるので、サブネットの前にネットワーク セキュリティ グループを作成します。 Azure ネットワーク セキュリティ グループはネットワーク層のファイアウォールに相当します。 Azure ネットワーク セキュリティ グループの詳細については、[Azure CLI でネットワーク セキュリティ グループを作成する方法](../../virtual-network/virtual-networks-create-nsg-arm-cli.md)に関するページを参照してください

```azurecli
azure network nsg create myNetworkSecurityGroup \
    --resource-group myResourceGroup \
    --location eastus
```

## <a name="add-an-inbound-ssh-allow-rule"></a>受信 SSH 許可ルールの追加

VM はインターネットからのアクセスを必要とするため、受信ポート 22 のトラフィックを、VM のポート 22 にネットワーク経由で渡すことを許可するルールが必要です。

```azurecli
azure network nsg rule create inboundSSH \
    --resource-group myResourceGroup \
    --nsg-name myNSG \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 100 \
    --source-address-prefix Internet \
    --source-port-range 22 \
    --destination-address-prefix 10.10.0.0/24 \
    --destination-port-range 22
```

## <a name="add-a-subnet-to-the-vnet"></a>VNet へのサブネットの追加

VNet 内の VM はサブネットに配置する必要があります。 各 VNet には複数のサブネットを含めることができます。 サブネットを作成して、ネットワーク セキュリティ グループに関連付けます。

```azurecli
azure network vnet subnet create mySubNet \
    --resource-group myResourceGroup \
    --vnet-name myVNet \
    --address-prefix 10.10.0.0/26 \
    --network-security-group-name myNetworkSecurityGroup
```

これで、サブネットが VNet 内に追加され、ネットワーク セキュリティ グループとルールに関連付けられました。


## <a name="add-a-vnic-to-the-subnet"></a>サブネットへの VNic の追加

仮想ネットワーク カード (Vnic) は重要であり、異なる VM に接続して再利用できます。 このアプローチにより、VM を一時的なものにしたまま VNic を静的リソースとして保持できます。 VNic を作成し、前の手順で作成したサブネットに関連付けます。

```azurecli
azure network nic create myVNic \
    --resource-group myResourceGroup \
    --location eastus \
    ---subnet-vnet-name myVNet \
    --subnet-name mySubNet
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>VNet と NSG への VM のデプロイ

VNet、その VNet 内のサブネット、および SSH 用のポート 22 を除くすべての受信トラフィックをブロックしてサブネットを保護するネットワーク セキュリティ グループの用意ができました。 これで、この既存のネットワーク インフラストラクチャ内に VM をデプロイできます。

Azure CLI と `azure vm create` コマンドを使用して、既存の Azure リソース グループ、VNet、サブネット、VNic に Linux VM をデプロイします。 CLI を使用して完全な VM をデプロイする方法の詳細については、「[Azure CLI を使用して、完全な Linux 環境を作成する](create-cli-complete.md)」をご覧ください。

```azurecli
azure vm create myVM \
    --resource-group myResourceGroup \
    --location eastus \
    --os-type linux \
    --image-urn Debian \
    --storage-account-name mystorageaccount \
    --admin-username myAdminUser \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --vnet-name myVNet \
    --vnet-subnet-name mySubnet \
    --nic-name myVNic
```

CLI フラグを使用して既存のリソースを呼び出すことで、既存のネットワーク内に VM をデプロイするよう Azure に指示します。 VNet とサブネットをデプロイしたら、Azure リージョン内でこれらを静的または永続的なリソースにしておくことができます。  

## <a name="next-steps"></a>次のステップ

* [Azure Resource Manager テンプレートを使用して特定のデプロイを作成する](../windows/cli-deploy-templates.md)
* [Azure CLI コマンドを直接使用して Linux VM 用の独自のカスタム環境を作成する](create-cli-complete.md)
* [テンプレートを使用して Azure に Linux VM を作成する](create-ssh-secured-vm-from-template.md)

