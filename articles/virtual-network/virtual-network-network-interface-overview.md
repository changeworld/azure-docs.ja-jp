<properties 
   pageTitle="ネットワーク インターフェイス | Microsoft Azure"
   description="Azure リソース マネージャーでの Azure ネットワーク インターフェイスについて説明します。"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="jdial" />

# ネットワーク インターフェイス

ネットワーク インターフェイス (NIC) とは、仮想マシン (VM) と基礎となるソフトウェア ネットワーク間の相互接続です。この記事では、ネットワーク インターフェイスとはなにか、Azure Resource Manager デプロイメント モデルでどのように使用されているかについて説明します。

Microsoft では、新しいリソースのデプロイには Resource Manager デプロイメント モデルを使用することをお勧めしますが、[クラシック](virtual-network-ip-addresses-overview-classic.md)のデプロイメント モデルでネットワーク接続を使用して VM をデプロイすることもできます。クラシック モデルに使い慣れている場合は、Resource Manager デプロイメント モデルの VM ネットワークには大きな違いがあることにご注意ください。相違点について詳しくは、「[仮想マシンのネットワーク- クラシック](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments)」の記事をご覧ください。

Azure のネットワーク インターフェイスとは:

1. 作成、削除できるリソースであり、独自の構成可能な設定があります。
2. 作成後、Azure 仮想ネットワーク (VNet) の 1 つのサブネットに接続する必要があります。VNet の詳細については、「[仮想ネットワークの概要](virtual-networks-overview.md)」の記事をご覧ください。NIC と同じ Azure の[場所](https://azure.microsoft.com/regions)と[サブスクリプション](../azure-glossary-cloud-terminology.md#subscription)に存在する VNet に接続する必要があります。NIC の作成後、接続先のサブネットは変更できますが、接続先の VNet は変更できません。
3. NIC の名前は、作成後に変更することはできません。名前は Azure [リソース グループ](../resource-group-overview.md#resource-groups)内で一意である必要がありますが、サブスクリプション、作成した Azure の場所、接続先のVNet 内で一意である必要はありません。通常、複数の NIC は Azure サブスクリプション内で作成されます。既定の名前を使用するのではなく、複数の NIC を管理しやすい名前付け規則を独自に設定することをお勧めします。詳細については、「[Recommended Naming Conventions for Azure Resources (Azure リソースの推奨される名前付け規則)](../guidance/guidance-naming-conventions.md)」をご覧ください。
4. VM にアタッチできますが、NIC と同じ場所にある 1 台の VM にしかアタッチできません。
5. MAC アドレスを持ちます。これは、VM にアタッチされている限り、NIC の MAC アドレスとして保持されます。MAC アドレスは、VM が再起動 (オペレーティング システム内で) された、停止 (割り当て解除) された、Azureポータル、Azure PowerShell、Azureコマンドライン インターフェイスを使って開始された場合でも保持されます。1 つのVM からデタッチされて、別の VM にアタッチされると、NIC には別の MAC アドレスが付与されます。NIC が削除されると、MAC アドレスは他の NIC に割り当てられます。
6. 1 つのプライマリ **プライベート** *IPv4* 静的 IP アドレスまたは動的 IP アドレスが割り当てられている必要があります。
8. 1 つのパブリック IP アドレス リソースを関連付けられます。
9. Microsoft Windows Server オペレーティング システムの特定のバージョンを実行している特定のサイズの VM で、シングル ルート I/O 仮想化 (SR-IOV) を使った Accelerated Networking をサポートします。このプレビュー機能の詳細については、「[ Accelerated Networking for a virtual machine (仮想マシンの Accelerated Networking)](virtual-network-accelerated-networking-powershell.md)」の記事をご覧ください。
10. NIC で IP 転送が有効になっている場合は、割り当てられたプライベート IPアドレス宛てではないトラフィックを受信できます。VM でファイアウォール ソフトウェアなどを実行している場合は、独自の IP アドレス宛てではないパケットをルーティングします。VM ではトラフィックをルーティングまたは転送できるソフトウェアを実行する必要がありますが、これを行うには NIC で IP 転送を有効にする必要があります。
11. ほとんどの場合、アタッチ先の VM と同じリソース グループか、接続先と同じ VNet に作成されますが、別の場所に作成することもできます。

VM のサイズでサポートされている場合は、複数の NIC を同じ VM にアタッチできます。複数の NIC をサポートしている VM のサイズの詳細については、「[Windows Server VM のサイズ](../virtual-machines/virtual-machines-windows-sizes.md)」または「[Linux VM のサイズ](../virtual-machines/virtual-machines-linux-sizes.md)」の記事をご覧ください。

## 次のステップ

- 1 つの NIC を持つ VM の作成方法については、「[VM を作成する](../virtual-machines/virtual-machines-windows-hero-tutorial.md)」の記事をご覧ください。
- 複数の NIC を持つ VM の作成方法については、「[複数の NIC VM のデプロイ](virtual-network-deploy-multinic-arm-ps.md)」の記事をご覧ください。
- 複数の IP 構成を持つ NIC の作成方法については、「[Azure 仮想マシンの複数の IP アドレス](virtual-network-multiple-ip-addresses-powershell.md)」の記事をご覧ください。

<!---HONumber=AcomDC_0928_2016-->