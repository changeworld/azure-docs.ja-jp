<properties
   pageTitle="Azure リソース マネージャーでパブリックおよびプライベートの IP アドレスを指定する | Microsoft Azure"
   description="Azure リソース マネージャーでパブリックおよびプライベートの IP アドレスを指定する方法について説明します"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/14/2015"
   ms.author="telmos" />

# Azure リソース マネージャーの IP アドレス
Azure リソースには、他の Azure リソース、オンプレミス ネットワーク、およびインターネットと通信するために IP アドレスを割り当てることができます。Azure で使用できる IP アドレスには、パブリックとプライベートの 2 種類があります。

パブリック IP アドレスは、Azure の公開されたサービスを含め、インターネットとの通信に使用します。

プライベート IP アドレスは、VPN ゲートウェイまたは ExpressRoute 回線を使用してネットワークを Azure に拡張するときに、Azure 仮想ネットワーク (VNet)、およびオンプレミス ネットワーク内での通信に使用します。

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-ip-addresses-overview-classic.md)。

## パブリック IP アドレス
パブリック IP アドレスを使用すると、Azure リソースはインターネットのほか、[Azure Redis Cache](https://azure.microsoft.com/services/cache)、[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs)、[SQL データベース](sql-database-technical-overview.md)、[Azure ストレージ](storage-introduction.md)など、Azure の公開されたサービスと通信できます。

Azure リソース マネージャーで、[パブリック IP](resource-groups-networking.md#public-ip-address) アドレスは、独自のプロパティを持つリソースです。パブリック IP アドレスのリソースは、次のリソースのいずれかと関連付けることができます。

- VM
- インターネットに接続するロード バランサー
- VPN ゲートウェイ
- アプリケーション ゲートウェイ

### 割り当て方法
IP アドレスを*パブリック IP リソース*に割り当てる方法には、*動的*と*静的*の 2 種類があります。既定の割り当て方法は*動的*で、IP アドレスの作成時に割り当ては**行われません**。代わりに、関連付けられたリソース (VM やロード バランサーなど) を開始 (または作成) するときに、パブリック IP アドレスが割り当てられます。IP アドレスは、リソースを停止 (または削除) すると解放されます。これにより、リソースの停止および開始の際に IP アドレスが変更されます。

関連付けられたリソースの IP アドレスが変わらないようにするため、割り当て方法を明示的に*静的*に設定できます。この場合、IP アドレスが即座に割り当てられます。IP アドレスはリソースを削除するか、その割り当て方法を*動的*に変更した場合にのみ解放されます。

>[AZURE.NOTE]割り当て方法を*静的*に設定しても、*パブリック IP リソース*に割り当てられた実際の IP アドレスは指定できません。代わりに、リソースが作成される Azure の location に使用可能な IP アドレスのプールから割り当てられます。

次のようなシナリオでは、静的なパブリック IP アドレスが一般的に使用されます。

- エンドユーザーが Azure リソースと通信するためのファイアウォール規則を更新する必要がある。
- DNS 名の解決で、IP アドレスの変更によりレコードを更新する必要がある。
- Azure のリソースが、IP ベースのセキュリティ モデルを使用する他の Web サービスと通信する。
- IP アドレスにリンクされている SSL 証明書を使用する。

>[AZURE.NOTE]Azure リソースへのパブリック IP アドレス (動的/静的) の割り当てに使用する IP アドレス範囲のリストは、「[Azure データセンターの IP アドレス範囲](https://www.microsoft.com/download/details.aspx?id=41653)」で公開されています。

### DNS ホスト名の解決
パブリック IP リソースに DNS ドメイン名ラベルを指定して、Azure で管理される DNS サーバーのパブリック IP アドレスに対する *domainnamelabel*.*location*.cloudapp.azure.com のマッピングを作成できます。たとえば、Azure の *location* が **West US** で、*domainnamelabel* が **contoso** のパブリック IP リソースを作成した場合、 完全修飾ドメイン名 (FQDN) **contoso.westus.cloudapp.azure.com** がリソースのパブリック IP アドレスに解決されます。この FQDN を使用して、Azure 内のパブリック IP アドレスをポイントするカスタム ドメイン CNAME レコードを作成できます。

>[AZURE.IMPORTANT]作成された各ドメイン名ラベルは、Azure の location 内で一意である必要があります。

### VM
パブリック IP アドレスは、その**ネットワーク インターフェイス カード** (NIC) に割り当てることで、[仮想マシン](virtual-machines-about.md) (VM) に関連付けることができます。複数の NIC を備える VM の場合、*プライマリ* NIC にのみ割り当てることができます。VM には、動的または静的のどちらかのパブリック IP アドレスを割り当てることができます。

### インターネットに接続するロード バランサー
パブリック IP アドレスをロード バランサーの**フロント エンド**構成に割り当てることで、[Azure Load Balancer](load-balancer-overview.md) に関連付けることができます。このパブリック IP アドレスは、負荷分散された仮想 IP アドレス (VIP) として機能します。ロード バランサーのフロント エンドには、動的または静的のどちらかのパブリック IP アドレスを割り当てることができます。複数のパブリック IP アドレスをロード バランサーのフロント エンドに割り当てて、SSL ベースの Web サイトを含むマルチテナント環境のような[マルチ VIP](load-balancer-multivip.md) シナリオを有効にすることもできます。

### VPN ゲートウェイ
[Azure VPN Gateway](vpn-gateway-about-vpngateways.md) は、Azure 仮想ネットワーク (VNet) を他の Azure VNet またはオンプレミス ネットワークに接続するために使用されます。リモート ネットワークとの通信を有効にするには、パブリック IP アドレスをその **IP 構成**に割り当てる必要があります。現時点で VPN ゲートウェイに割り当てることができるのは、動的パブリック IP アドレスのみです。

### アプリケーション ゲートウェイ
パブリック IP アドレスをゲートウェイの**フロント エンド**構成に割り当てることで、Azure [Application Gateway](application-gateway-introduction.md) に関連付けることができます。このパブリック IP アドレスは、負荷分散された VIP として機能します。現時点で Application Gateway のフロント エンド構成に割り当てることができるのは、*動的*パブリック IP アドレスのみです。複数のパブリック IP アドレスを割り当てて、マルチ VIP シナリオを有効にすることもできます。

### 早見表
次の表は、各リソースの種類と、可能な割り当て方法 (動的または静的) および複数のパブリック IP アドレスの割り当てが可能かどうかを示しています。

|リソース|動的|静的|複数の IP アドレス|
|---|---|---|---|
|(VM の) ネットワーク インターフェイス カード (NIC)|あり|はい|いいえ|
|Load Balancer のフロント エンド|あり|あり|あり|
|VPN Gateway|あり|なし|いいえ|
|Application Gateway のフロント エンド|あり|なし|いいえ|

## プライベート IP アドレス
プライベート IP アドレスを使用すると、Azure リソースは、インターネットが到達可能な IP アドレスを使用せずに、[仮想ネットワーク](virtual-networks-overview.md) (VNet) の他のリソース、あるいはオンプレミス ネットワーク (VPN ゲートウェイまたは ExpressRoute 回線経由) と通信することができます。

Azure リソース マネージャーのデプロイメント モデルでは、プライベート IP アドレスは Azure のさまざまなリソースに関連付けられます。

- VM
- 内部ロード バランサー (ILB)
- アプリケーション ゲートウェイ

### 割り当て方法
プライベート IP アドレスは、リソースが関連付けられているサブネットのアドレス範囲から割り当てられます。サブネット自体のアドレス範囲は、VNet のアドレス範囲に含まれます。

プライベート IP アドレスを割り当てる方法には、*動的*と*静的*の 2 種類があります。既定の割り当て方法は*動的*です。IP アドレスは (DHCP を使用して) リソースのサブネットから自動的に割り当てられます。リソースを停止して起動すると、この IP アドレスが変更される場合があります。

IP アドレスが変わらないようにするため、割り当て方法を*静的*に設定することができます。この場合、リソースのサブネットの一部として有効な IP アドレスも指定する必要があります。

静的プライベート IP アドレスは、通常は次のものに使用されます。

- ドメイン コントローラーまたは DNS サーバーとして機能する VM。
- IP アドレスを使用するファイアウォール規則を必要とするリソース。
- IP アドレスを使用して他のアプリ/リソースからアクセスされるリソース。

### VM
プライベート IP アドレスは、[仮想マシン](virtual-machines-about.md)の**ネットワーク インターフェイス カード** (NIC) に割り当てます。複数の NIC を備える VM の場合、各 NIC にプライベート IP アドレスが割り当てられます。NIC に対して動的または静的のどちらかの割り当て方法を指定することができます。

#### 内部 DNS ホスト名の解決 (VM の場合)
カスタムの DNS サーバーを明示的に構成しない限り、既定ではすべての Azure VM が [Azure で管理される DNS サーバー](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution)で構成されます。これらの DNS サーバーは、同じ VNet 内に存在する VM の内部名前解決を可能にします。

VM を作成すると、そのプライベート IP アドレスへのホスト名のマッピングが、Azure で管理される DNS サーバーに追加されます。複数の NIC を備える VM の場合、ホスト名はプライマリ NIC のプライベート IP アドレスにマップされます。

Azure で管理される DNS サーバーで構成されている VM は、その VNet 内のすべての VM のホスト名をプライベート IP アドレスに解決することができます。

### 内部ロード バランサー (ILB) と Application Gateway
[Azure 内部ロード バランサー](load-balancer-internal-overview.md) (ILB) または [Azure Application Gateway](application-gateway-introduction.md) の**フロント エンド**構成にプライベート IP アドレスを割り当てることができます。このプライベート IP アドレスは内部エンドポイントとして機能し、その仮想ネットワーク (VNet) 内のリソースおよび VNet に接続されたリモート ネットワークからのみアクセスできます。フロント エンド構成には、動的または静的のどちらかのプライベート IP アドレスを割り当てることができます。複数のプライベート IP アドレスを割り当てて、マルチ VIP シナリオを有効にすることもできます。

### 早見表
次の表は、各リソースの種類と、可能な割り当て方法 (動的または静的) および複数のプライベート IP アドレスの割り当てが可能かどうかを示しています。

|リソース|静的|動的|複数の IP アドレス|
|---|---|---|---|
|仮想マシン (VM)/ネットワーク インターフェイス カード (NIC)|あり|あり|あり|
|内部ロード バランサーのフロント エンド|あり|あり|あり|
|Application Gateway のフロント エンド|あり|あり|あり|

## リソース マネージャーとクラシック デプロイメントの比較
リソース マネージャーの IP アドレスとクラシック デプロイメント モデルとの比較を次に示します。

||リソース|クラシック|リソース マネージャー| |---|---|---|---| |**パブリック IP アドレス**|VM|ILPIP と呼ばれる (動的のみ)|パブリック IP と呼ばれる (動的または静的)| |||IaaS VM または PaaS ロール インスタンスに割り当てられる|VM の NIC に関連付けられる| ||インターネットに接続するロード バランサー|VIP (動的) または予約済み IP (静的) と呼ばれる|パブリック IP (動的または静的) と呼ばれる| |||クラウド サービスに割り当てられる|ロード バランサーのフロント エンド構成に関連付けられる| |||| |**プライベート IP アドレス**|VM|DIP と呼ばれる|プライベート IP アドレスと呼ばれる| |||IaaS VM または PaaS ロール インスタンスに割り当てられる|VM の NIC に割り当てられる| ||内部ロード バランサー (ILB)|ILB に割り当てられる (動的または静的)|ILB のフロント エンド構成に割り当てられる (動的または静的)|

## 次のステップ
- [Deploy a VM with a static public IP (静的パブリック IP で VM をデプロイする)](virtual-network-deploy-static-pip-arm-template.md)
- [Create a public IP address for an Internet facing load balancer by using the Azure CLI (Azure CLI を使用してインターネットに接続するロード バランサーのパブリック IP アドレスを作成する)](load-balancer-get-started-internet-arm-cli.md#create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool)
- [Create a public IP address for an application gateway by using PowerShell (PowerShell を使用して Application Gateway のパブリック IP アドレスを作成する)](application-gateway-create-gateway-arm.md#create-public-ip-address-for-front-end-configuration)
- [Create a public IP address for a VPN gateway by using PowerShell (PowerShell を使用して VPN ゲートウェイのパブリック IP アドレスを作成する)](vpn-gateway-create-site-to-site-rm-powershell.md#4-request-a-public-ip-address-for-the-gateway)
- [Deploy a VM with a static private IP address (静的プライベート IP アドレスで VM をデプロイする)](virtual-networks-static-private-ip-arm-pportal.md)
- [Create a front end static private IP address for an internal load balancer by using PowerShell (PowerShell を使用して内部ロード バランサーのフロント エンドの静的プライベート IP アドレスを作成する)](load-balancer-get-started-ilb-arm-ps.md#create-front-end-ip-pool-and-backend-address-pool)
- [Create a backend pool with static private IP addresses for an application gateway by using PowerShell (PowerShell を使用して Application Gateway の静的プライベート IP アドレスを含むバックエンド プールを作成する)](application-gateway-create-gateway-arm.md#create-an-application-gateway-configuration-object)

<!---HONumber=AcomDC_1223_2015-->