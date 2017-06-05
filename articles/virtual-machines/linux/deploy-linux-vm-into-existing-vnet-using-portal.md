---
title: "Azure ポータルで既存のネットワークに Linux VM をデプロイする | Microsoft Docs"
description: "ポータルを使用して、既存の Azure Virtual Network に Linux VM をデプロイします。"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 964c0fc41773b50a9fbe476df47460484c2ada66
ms.contentlocale: ja-jp
ms.lasthandoff: 05/11/2017


---

# <a name="how-to-deploy-a-linux-virtual-machine-into-an-existing-azure-virtual-network-with-the-azure-portal"></a>Azure ポータルを使用して Linux 仮想マシンを既存の Azure 仮想ネットワークにデプロイする方法

この記事では、仮想マシン (VM) を既存の仮想ネットワーク (VNet) にデプロイする方法を説明します。 VNet やネットワーク セキュリティ グループなどの Azure 資産は、ほとんどデプロイされることのない有効期間の長い静的リソースにすることをお勧めします。 VNet をデプロイしたら、インフラストラクチャに悪影響を及ぼすことなく、継続的なデプロイで再利用できます。 VNet を従来のハードウェア ネットワーク スイッチと考えると、デプロイごとにまったく新しいハードウェア スイッチを構成する必要はありません。  

適切に構成された VNet があれば、新しいサーバーをその VNet に何度もデプロイできます。VNet の有効期間中に必要な変更は、あるとしてもごくわずかです。

## <a name="create-the-resource-group"></a>リソース グループの作成

まず、このチュートリアルで作成するすべてのものを整理するリソース グループを作成します。 Azure リソース グループの詳細については、「[Azure Resource Manager の概要](../../azure-resource-manager/resource-group-overview.md)」を参照してください

![createResourceGroup](./media/deploy-linux-vm-into-existing-vnet-using-portal/createResourceGroup.png)


## <a name="create-the-vnet"></a>VNet の作成

次に、VM を起動する VNet を作成します。 VNet には 1 つのサブネットが含まれていて、そのサブネットが含まれるネットワーク セキュリティ グループに関連付けられています。これについては後の手順で説明します。

![createVNet](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNet.png)

## <a name="add-a-vnic-to-the-subnet"></a>サブネットへの VNic の追加

仮想ネットワーク カード (Vnic) は重要であり、異なる VM に接続できます。 このアプローチにより、VM を一時的なものにしたまま VNic を静的リソースとして保持できます。 VNic を作成し、前の手順で作成したサブネットに関連付けます。

![createVNic](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNic.png)

## <a name="create-the-network-security-group"></a>ネットワーク セキュリティ グループの作成

Azure ネットワーク セキュリティ グループはネットワーク層のファイアウォールに相当します。 Azure ネットワーク セキュリティ グループの詳細については、「[What is a Network Security Group](../../virtual-network/virtual-networks-nsg.md)」 (ネットワーク セキュリティ グループ (NSG) について) を参照してください。

![createNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/createNSG.png)

## <a name="add-an-inbound-ssh-allow-rule"></a>受信 SSH 許可ルールの追加

VM はインターネットからのアクセスを必要とするため、受信ポート 22 のトラフィックを、VM のポート 22 にネットワーク経由で渡すことを許可するルールを作成します。

![createInboundSSH](./media/deploy-linux-vm-into-existing-vnet-using-portal/createInboundSSH.png)

## <a name="associate-the-nsg-with-the-subnet"></a>サブネットへの NSG の関連付け

VNet とサブネットが作成されたので、サブネットにネットワーク セキュリティ グループを関連付けます。 ネットワーク セキュリティ グループはサブネット全体に関連付けることも、個々の VNic に関連付けることもできます。 サブネット レベルでのファイアウォール フィルタリング トラフィックにより、サブネット内のすべての VNic と VM がネットワーク セキュリティ グループで保護されます。 ネットワーク セキュリティ グループを単一の VNic と関連付けて、VM を 1 つだけ保護するという方法もあります。

![associateNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/associateNSG.png)


## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>VNet と NSG への VM のデプロイ

Azure Portal を使用して、既存の Azure リソース グループ、VNet、サブネット、VNic に Linux VM をデプロイします。

![createVM](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVM.png)

ポータルを使用して既存のリソースを選択することで、既存のネットワーク内に VM をデプロイするよう Azure に指示します。 VNet とサブネットをデプロイしたら、Azure リージョン内でこれらを静的または永続的なリソースにしておくことができます。  

## <a name="next-steps"></a>次のステップ

* [Azure Resource Manager テンプレートを使用して特定のデプロイを作成する](../windows/cli-deploy-templates.md)
* [Azure CLI コマンドを直接使用して Linux VM 用の独自のカスタム環境を作成する](create-cli-complete.md)
* [テンプレートを使用して Azure に Linux VM を作成する](create-ssh-secured-vm-from-template.md)

