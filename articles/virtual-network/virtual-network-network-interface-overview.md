---
title: "Azure のネットワーク インターフェイス | Microsoft Docs"
description: "Azure Resource Manager デプロイメント モデルでの Azure ネットワーク インターフェイスについて説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f58b503f-18bf-4377-aa63-22fc8a96e4be
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 3244d5b52785d820698bf26f9bf189de93ef64e4
ms.openlocfilehash: 691b79d7739246dad7191195fa049fd58340c8ff


---
# <a name="network-interfaces-in-azure"></a>Azure のネットワーク インターフェイス
ネットワーク インターフェイス (NIC) とは、仮想マシン (VM) と基礎となるソフトウェア ネットワーク間の相互接続です。 この記事では、ネットワーク インターフェイスとはなにか、Azure Resource Manager デプロイメント モデルでどのように使用されているかについて説明します。

Microsoft では、新しいリソースのデプロイには Resource Manager デプロイメント モデルを使用することをお勧めしますが、 [クラシック](virtual-network-ip-addresses-overview-classic.md) のデプロイメント モデルでネットワーク接続を使用して VM をデプロイすることもできます。 クラシック モデルに使い慣れている場合は、Resource Manager デプロイメント モデルの VM ネットワークには大きな違いがあることにご注意ください。 相違点について詳しくは、「 [仮想マシンのネットワーク- クラシック](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments) 」の記事をご覧ください。

Azure のネットワーク インターフェイスとは:

1. 作成、削除できるリソースであり、独自の構成可能な設定があります。
2. 作成後、Azure 仮想ネットワーク (VNet) の&1; つのサブネットに接続する必要があります。 VNet の詳細については、「 [仮想ネットワークの概要](virtual-networks-overview.md) 」の記事をご覧ください。 NIC と同じ Azure の[場所](https://azure.microsoft.com/regions)と[サブスクリプション](../azure-glossary-cloud-terminology.md#subscription)に存在する VNet に NIC を接続する必要があります。 NIC の作成後、接続先のサブネットは変更できますが、接続先の VNet は変更できません。
3. NIC の名前は、作成後に変更することはできません。 名前は Azure [リソース グループ](../azure-resource-manager/resource-group-overview.md#resource-groups)内で一意である必要がありますが、サブスクリプション、作成した Azure の場所、接続先のVNet 内で一意である必要はありません。 通常、複数の NIC は Azure サブスクリプション内で作成されます。 既定の名前を使用するのではなく、複数の NIC を管理しやすい名前付け規則を独自に設定することをお勧めします。 詳細については、「 [Recommended Naming Conventions for Azure Resources (Azure リソースの推奨される名前付け規則)](../guidance/guidance-naming-conventions.md) 」をご覧ください。
4. VM にアタッチできますが、NIC と同じ場所にある&1; 台の VM にしかアタッチできません。
5. MAC アドレスを持ちます。これは、VM にアタッチされている限り、NIC の MAC アドレスとして保持されます。 MAC アドレスは、VM が再起動 (オペレーティング システム内で) された、停止 (割り当て解除) された、Azureポータル、Azure PowerShell、Azureコマンドライン インターフェイスを使って開始された場合でも保持されます。 1 つのVM からデタッチされて、別の VM にアタッチされると、NIC には別の MAC アドレスが付与されます。 NIC が削除されると、MAC アドレスは他の NIC に割り当てられます。
6. 1 つのプライマリ **プライベート** *IPv4* 静的 IP アドレスまたは動的 IP アドレスが割り当てられている必要があります。
7. 1 つのパブリック IP アドレス リソースを関連付けられます。
8. Microsoft Windows Server オペレーティング システムの特定のバージョンを実行している特定のサイズの VM で、シングル ルート I/O 仮想化 (SR-IOV) を使った Accelerated Networking をサポートします。 このプレビュー機能の詳細については、「 [Accelerated Networking for a virtual machine (仮想マシンの Accelerated Networking)](virtual-network-accelerated-networking-powershell.md) 」の記事をご覧ください。
9. NIC で IP 転送が有効になっている場合は、割り当てられたプライベート IPアドレス宛てではないトラフィックを受信できます。 VM でファイアウォール ソフトウェアなどを実行している場合は、独自の IP アドレス宛てではないパケットをルーティングします。 VM ではトラフィックをルーティングまたは転送できるソフトウェアを実行する必要がありますが、これを行うには NIC で IP 転送を有効にする必要があります。
10. ほとんどの場合、アタッチ先の VM と同じリソース グループか、接続先と同じ VNet に作成されますが、別の場所に作成することもできます。

## <a name="vms-with-multiple-network-interfaces"></a>複数のネットワーク インターフェイスを持つ VM
VM に複数の NIC をアタッチできますが、これを行う場合、次の点に注意してください。  

* VM サイズで複数の NIC がサポートされている必要があります。 複数の NIC をサポートしている VM のサイズの詳細については、[Windows Server VM のサイズ](../virtual-machines/virtual-machines-windows-sizes.md)に関する記事または [Linux VM のサイズ](../virtual-machines/virtual-machines-linux-sizes.md)に関する記事を参照してください。
* 少なくとも&2; つの NIC を持つ VM を作成する必要があります。 VM を&1; つの NIC のみで作成した場合、VM サイズで複数の NIC をサポートしていても、VM の作成後に VM に追加の NIC をアタッチすることはできません。 VM を&2; つ以上の NIC で作成していれば、作成後に、VM に追加の NIC をアタッチできます。ただし、VM サイズで&2; つ以上の NIC をサポートしている必要があります。  
* VM に&3; つ以上の NIC がアタッチされている場合、セカンダリ NIC を VM からデタッチできます (プライマリ NIC はデタッチできません)。 VM にアタッチされている NIC が&2; つ以下の場合は、NIC をデタッチすることはできません。  
* VM 内の NIC の順序はランダムになり、Azure インフラストラクチャの更新によって変更される場合もあります。 ただし、IP アドレスと、対応するイーサネットの MAC アドレスは変更されません。 たとえば、オペレーティング システムが Azure NIC1 を Eth1 と認識したとします。 Eth1 の IP アドレスが 10.1.0.100 で、MAC アドレスが 00-0D-3A-B0-39-0D だとします。 Azure インフラストラクチャが更新されて再起動された後、オペレーティング システムは Azure NIC1 を Eth2 と識別するようになりますが、オペレーティング システムが Azure NIC1 を Eth1 と識別した場合、IP アドレスと MAC アドレスは同じままになります。 お客様が再起動を開始した場合、NIC の順序はオペレーティング システム内で変更されません。  
* VM が[可用性セット](../azure-glossary-cloud-terminology.md#availability-set)のメンバーである場合、可用性セットのすべての VM は、単一 NIC または複数 NIC のいずれかを持つ必要があります。 VM に複数の NIC がある場合、それぞれの VM に少なくとも&2; つの NIC があれば、各 VM の NIC の数を同じにする必要はありません。

## <a name="next-steps"></a>次のステップ
* 1 つの NIC を持つ VM の作成方法については、「 [VM を作成する](../virtual-machines/virtual-machines-windows-hero-tutorial.md) 」の記事をご覧ください。
* 複数の NIC を持つ VM の作成方法については、「 [複数の NIC VM のデプロイ](virtual-network-deploy-multinic-arm-ps.md) 」の記事をご覧ください。
* 複数の IP 構成を持つ NIC の作成方法については、「 [Azure 仮想マシンの複数の IP アドレス](virtual-network-multiple-ip-addresses-powershell.md) 」の記事をご覧ください。




<!--HONumber=Jan17_HO5-->


