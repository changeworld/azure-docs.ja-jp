<properties 
   pageTitle="Azure ネットワーク リソース プロバイダーのパブリック IP アドレスとプライベート IP アドレス | Microsoft Azure"
   description="Azure リソース マネージャーのネットワーク リソース プロバイダーのパブリック IP アドレスとプライベート IP アドレスについて説明します。"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="joaoma" />

# Azure Virtual Network の IP アドレス
この記事では、Virtual Machines、Load Balancer、VPN Gateway、Application Gateway の IP アドレスの指定について説明します。

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)].

## パブリック IP アドレス
Azure リソースは、パブリック IP アドレスを使用して、インターネットや [Azure Redis Cache](https://azure.microsoft.com/services/cache)、[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs) など、Azure の公開されている他のサービスと通信できます。パブリック IP アドレスは独立したリソースとして、[Virtual Machines](virtual-machines-about.md) (VM)、[Load Balancer](load-balancer-overview.md) (LB) などのさまざまな種類の Azure リソースに関連付けることができます。

### パブリック IP アドレスの割り当て方法
パブリック IP アドレスは、特定の場所で提供される IP アドレスのプールから割り当てられます。Microsoft Azure データセンターで使用される IP アドレス範囲の詳細なリストが、[Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)に関するページで公開されています。

IP アドレスをパブリック IP リソースに割り当てる方法には、*動的*と*静的*の 2 種類があります。既定の割り当て方法は*動的*です。*動的*な割り当て方法では、IP アドレスは*パブリック IP アドレス リソース*の作成時には**割り当てられません**。*パブリック IP アドレス リソース*に関連付けられたリソース (VM やロード バランサーなど) を作成または開始すると、IP アドレスが IP アドレスのプールから割り当てられます。この IP アドレスは、関連付けられたリソースを削除または停止すると、割り当てが解除され、使用可能なプールに解放されます。

*静的*な割り当て方法を使用する場合、IP アドレスは、*パブリック IP アドレス リソース*の作成時に割り当てられます。この場合、関連付けられたリソースの状態に関係なく、同じ IP アドレスが割り当てられたままになります。*パブリック IP アドレス リソース*を削除したときと割り当て方法を*動的*に変更したときのみ、IP アドレスは使用可能なプールに開放されます。

### DNS の解決
パブリック IP リソースに対して DNS ドメイン名のラベルを指定できます。これによって、対応する DNS エントリが Azure DNS サーバーに作成されます。対応する FQDN *ドメイン名ラベル*.*場所*.cloudapp.azure.com は、パブリック IP リソースに割り当てられた IP アドレスにグローバルに解決されます。

以下で、パブリック IP アドレスに関連付けることができるさまざまな種類のリソースについて説明します。

### 仮想マシン
パブリック IP アドレスは、ネットワーク インターフェイス カード (NIC) を介して[仮想マシン](virtual-machines-about.md) (VM) に関連付けられます。関連付けられている NIC の数に関係なく、各 VM に割り当てることができるパブリック IP アドレスは 1 つのみです。VM の NIC には、*動的に*割り当てられる*パブリック IP アドレス リソース*のみを関連付けることができます。複数の NIC を備える VM の場合、*パブリック IP アドレス リソース*は、プライマリ NIC のみに関連付けることができます。

### Azure Load Balancer
パブリック IP アドレスは、[Azure Load Balancer](load-balancer-overview.md) (LB) のフロント エンド構成に関連付けることができます。この場合、パブリック IP アドレスはインターネットから到達できる負荷分散された仮想 IP アドレス (VIP) として機能します。LB には、*動的*および*静的*の両方のパブリック IP リソースを関連付けることができます。複数の*パブリック IP アドレス リソース*を 1 つの LB フロント エンド構成に関連付けることができます。これによって、複数の SSL ベース Web サイトを使用したマルチテナント環境などのシナリオを実現できます。

### Application Gateway
パブリック IP アドレスは、[Azure Application Gateway](application-gateway-introduction.md) のフロント エンド構成に関連付けることができ、インターネットから到達可能な負荷分散された仮想 IP (VIP) としてフロント エンドを構成します。現在のところ、Application Gateway には、*動的*に割り当てられるパブリック IP リソースのみを関連付けることができます。ただし、複数のパブリック IP アドレスに関連付けることができます。

### VPN Gateway
パブリック IP アドレスは、Azure の仮想ネットワークとオンプレミスのネットワークまたは別の Azure の仮想ネットワークの間で VPN 接続を作成する過程で、[Azure VPN Gateway](vpn-gateway-about-vpngateways.md) IP 構成に関連付ける必要があります。現在のところ、VPN Gateway には、*動的*に割り当てられるパブリック IP リソースのみを関連付けることができます。

### 早見表

|リソース|静的割り当て|動的割り当て|複数の IP アドレス|
|---|---|---|---|
|仮想マシン (VM)/ネットワーク インターフェイス カード (NIC)|あり|なし|なし|
|Load Balancer のフロント エンド|あり|あり|あり|
|Application Gateway のフロント エンド|あり|なし|あり|
|VPN Gateway|あり|なし|なし|

## プライベート IP アドレス
プライベート IP アドレスを使用すると、インターネットから到達可能な IP アドレスを使用せずに、仮想ネットワーク内のリソース間で通信できるようになります。この IP アドレスは、仮想ネットワーク内のサブネットのアドレス範囲から割り当てます。

### プライベート IP アドレスの割り当て方法
プライベート IP アドレスを割り当てる方法には、*動的*と*静的*の 2 種類があります。既定の割り当て方法は*動的*です。IP アドレスは DHCP を使用して割り当てられます。また、割り当て方法を*静的*と明示的に指定して、IP アドレスを指定することもできます。この場合、IP アドレスがサブネットのアドレスの範囲内にあり、自由である (他のリソースに割り当てられていない) 限り、リソースには指定した IP アドレスが割り当てられます。

さまざまな種類のリソースにプライベート IP アドレスを割り当てることができます。両方の割り当て方法 (*静的*と*動的*) が、以下のすべての種類のリソースに対して機能します。

### 仮想マシン
プライベート IP アドレスは、[仮想マシン](virtual-machines-about.md) (VM) のネットワーク インターフェイス カード (NIC) に割り当てます。各 VM は、VM に関連付けられた NIC と同数のプライベート IP アドレスを持つことができます。

#### 内部 DNS ホスト名の解決
作成時に明示的に指定された場合を除き、すべての Azure VM が、[Azure で管理される DNS サーバー](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution)を使用して構成されます。Azure で管理される DNS サーバーを使用する場合は、DNS レコードが自動的に作成されます。このレコードを使用して、VM のホスト名が VM のプライベート IP アドレスに解決されます。複数の NIC を備える VM の場合、ホスト名はプライマリ NIC のプライベート IP アドレスに解決されます。

### 内部ロード バランサー
プライベート IP アドレスは、[Azure 内部ロード バランサー](load-balancer-internal-overview.md) (ILB) フロント エンドに割り当てることができます。これは、仮想ネットワーク内のリソースの負荷分散された仮想 IP アドレス (VIP) として機能します。これにより、インターネットに IP アドレスを公開することなく負荷分散できます。

### Application Gateway
プライベート IP アドレスは、[Azure Application Gateway](application-gateway-introduction.md) フロント エンドに割り当てることができます。内部ロード バランサー (ILB) エンドポイントとも呼ばれる、インターネットに公開されない内部エンドポイントとしてフロント エンドを構成します。動作および割り当て方法は、上で説明した ILB と同じです。

### 早見表
|リソース|静的割り当て|動的割り当て|複数の IP アドレス|
|---|---|---|---|
|仮想マシン (VM)/ネットワーク インターフェイス カード (NIC)|あり|あり|あり|
|Load Balancer のフロント エンド|あり|あり|あり|
|Application Gateway のフロント エンド|あり|あり|あり|

## 次のステップ


[ネットワーク用 Azure PowerShell のリファレンス](https://msdn.microsoft.com/library/azure/mt163510.aspx)

[Azure リソース マネージャー テンプレートの言語](../resource-group-authoring-templates.md)

[Azure のネットワーク – 一般的に使用されているテンプレート](https://github.com/Azure/azure-quickstart-templates)

[コンピューティング リソース プロバイダー](../virtual-machines-azurerm-versus-azuresm)

[Azure リソース マネージャーの概要](../resource-group-overview)

[Azure リソース マネージャーでのロール ベースのアクセス制御](https://msdn.microsoft.com/library/azure/dn906885.aspx)

[Azure リソース マネージャーでのタグの使用](https://msdn.microsoft.com/library/azure/dn848368.aspx)

[テンプレートのデプロイ](https://msdn.microsoft.com/library/azure/dn790549.aspx)

<!---HONumber=AcomDC_1210_2015-->