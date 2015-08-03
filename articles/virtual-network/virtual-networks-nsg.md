<properties 
   pageTitle="ネットワーク セキュリティ グループ (NSG)"
   description="ネットワーク セキュリティ グループ (NSG) の詳細"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/08/2015"
   ms.author="telmos" />

# ネットワーク セキュリティ グループ (NSG) について

NSG を使用して、仮想ネットワーク内で 1 つまたは複数の仮想マシン (VM) のインスタンスに対するトラフィックを制御できます。ネットワーク セキュリティ グループは、サブスクリプションに関連付けられている最上位オブジェクトです。NSG には、VM インスタンスへのトラフィックを許可または拒否するアクセス制御ルールが含まれています。NSG のルールは、いつでも変更でき、変更は関連付けられているすべてのインスタンスに適用されます。NSG を使用するには、地域 (場所) に関連付けられている VNet が必要です。

>[AZURE.WARNING]NSG は、アフィニティ グループに関連付けられている Vnet に対応していません。地域 VNet がない場合にエンドポイントへのトラフィックを制御するには、[ネットワーク アクセス制御リスト (ACL)](../virtual-networks-acl)を参照してください。

VM または VNet 内のサブネットに NSG を関連付けることができます。VM に関連付けた場合、NSG は、VM インスタンスで送受信されるすべてのトラフィックに適用されます。VNet 内のサブネットに適用した場合は、サブネット内のすべての VM インスタンスで送受信されるすべてのトラフィックに適用されます。VM またはサブネットは、 1 つの NSG のみに関連付けることができます。各 NSG には、最大 200 のルールを含めることができます。サブスクリプションあたり 100 の NSG を割り当てることができます。

>[AZURE.NOTE]エンドポイント ベースの ACL とネットワーク セキュリティ グループは、同じ VM インスタンスではサポートされません。エンドポイントの ACL が既に導入されている場合に NSG を使用するには、初めにエンドポイントの ACL を削除します。これを行う方法については、[PowerShell を使用したエンドポイントのアクセス制御リスト (ACL) の管理](https://msdn.microsoft.com/library/azure/dn376543.aspx)を参照してください。

## ネットワーク セキュリティ グループのしくみ

ネットワーク セキュリティ グループは、エンドポイント ベースの ACL とは異なります。エンドポイントの ACL は、入力エンドポイントによって公開されるパブリック ポートでのみ動作します。NSG は 1 つまたは複数の VM インスタンスで動作し、VM 上の受信と送信のすべてのトラフィックを制御します。

ネットワーク セキュリティ グループには*名前*が割り当てられ、*領域*に関連付けられ、わかりやすいラベルが付きます。これには、**受信**と**送信**の 2 種類のルールが含まれています。受信のルールは VM への受信パケットに適用され、送信のルールは、VM からの送信パケットに適用されます。ルールは、VM が配置されているホストで適用されます。受信パケットまたは送信パケットは、許可されるために**許可**ルールと一致する必要があります。一致しない場合は、削除されます。

ルールは、優先順位に従って処理されます。たとえば、優先順位の値が小さい (100 など) ルールは、優先順位の値が大きい (200 など) ルールより前に処理されます。一致が見つかると、それ以上のルールは処理されません。

ルールでは、次のことを指定します。

- **名前:** ルールの一意の識別子

- **種類:** 受信/送信

- **優先順位:** <You can specify an integer between 100 and 4096>

- **発信元 IP アドレス:** 発信元 IP 範囲の CIDR

- **発信元ポート範囲:** <integer or range between 0 and 65536>

- **宛先 IP の範囲:** 宛先 IP 範囲の CIDR

- **宛先ポート範囲:** <integer or range between 0 and 65536>

- **プロトコル:** < TCP、UDP または ' *' が許可されます>

- **アクセス:** 許可または拒否

### 既定のルール

NSG には、既定のルールが含まれています。既定のルールは削除できませんが、これには最も低い優先順位が割り当てられているため、ルールを作成することで上書きできます。既定のルールでは、プラットフォームによって推奨される既定の設定が指定されています。次の既定のルールが示すように、受信方向と送信方向の両方につき、 VNet 内で発信および着信するトラフィックは許可されます。

インターネットへの接続は送信方向で許可されていますが、既定で、受信方向はブロックされています。既定のルールでは、Azure Load Balancer (LB) による VM の正常性プローブが許可されます。NSG の下の VM または一連の VM が負荷分散セットに参加しない場合は、このルールを上書きできます。

既定のルールは、次のとおりです。

**受信**

| 名前 | 優先順位 | 発信元 IP | 発信元ポート | 宛先 IP | 宛先ポート | プロトコル | アクセス |
|-----------------------------------|----------|--------------------|-------------|-----------------|------------------|----------|--------|
| ALLOW VNET INBOUND | 65000 | VIRTUAL_NETWORK | * | VIRTUAL_NETWORK | * | * | ALLOW |
| ALLOW AZURE LOAD BALANCER INBOUND | 65001 | AZURE_LOADBALANCER | * | * | * | * | ALLOW |
| DENY ALL INBOUND | 65500 | * | * | * | * | * | DENY |

**送信**

| 名前 | 優先順位 | 発信元 IP | 発信元ポート | 宛先 IP | 宛先ポート | プロトコル | アクセス |
|-------------------------|----------|-----------------|-------------|-----------------|------------------|----------|--------|
| ALLOW VNET OUTBOUND | 65000 | VIRTUAL_NETWORK | * | VIRTUAL_NETWORK | * | * | ALLOW |
| ALLOW INTERNET OUTBOUND | 65001 | * | * | INTERNET | * | * | ALLOW |
| DENY ALL OUTBOUND | 65500 | * | * | * | * | * | DENY |

### インフラストラクチャの特別なルール

NSG ルールは、明示的です。NSG ルールで指定されていない限り、いかなるトラフィックも許可または拒否されません。ただし、ネットワーク セキュリティ グループの指定に関わらず、常に許可される 2 種類のトラフィックがあります。インフラストラクチャをサポートするために、このように事前設定されています。

- **ホスト ノードの仮想 IP:** DHCP、DNS、および正常性の監視などの基本的なインフラストラクチャ サービスは、仮想化されたホストの IP アドレス 168.63.129.16 を通じて提供されます。このパブリック IP アドレスは Microsoft に属し、この目的のためにすべての地域で使用される唯一の仮想化 IP アドレスです。この IP アドレスは、仮想マシンをホストしているサーバー マシン (ホスト ノード) の物理 IP アドレスにマッピングされます。ホスト ノードは、DHCP リレー、DNS の再帰的リゾルバー、および Load Balancer の正常性プローブとマシンの正常性プローブのプローブ元として機能します。この IP アドレスへの通信を攻撃と見なさないでください。

- **ライセンス (キー管理サービス):** 仮想マシンで実行されている Windows イメージのライセンスを取得する必要があります。このためには、要求を処理するキー管理サービスのホスト サーバーにライセンス要求を送信します。これは、常に送信ポート 1688 上にあります。

### 既定のタグ

既定のタグは、IP アドレスのカテゴリに対応するシステム指定の識別子です。ユーザー定義のルールで、既定のタグを指定できます。既定のタグは、次のとおりです。

- **VIRTUAL_NETWORK -** この既定のタグは、ネットワーク アドレス空間のすべてを表します。これには、仮想ネットワーク アドレス空間 (Azure での IP CIDR) だけでなく、すべての接続されているオンプレミス アドレス空間 (ローカル ネットワーク) が含まれます。また、VNet 間のアドレス空間も含まれます。

- **AZURE_LOADBALANCER -** この既定のタグは、Azure の インフラストラクチャの Load Balancer を表します。これは、Azure の正常性プローブが開始される Azure データ センター IP に変換されます。これは、NSG に関連付けられている VM または一連の VM が、負荷分散セットに参加している場合にのみ必要です。

- **INTERNET -** この既定のタグは、パブリック インターネットによってアクセスできる仮想ネットワークの外部の IP アドレス空間を表します。この範囲には、Azure に所有されているパブリック IP 領域も含まれます。

### ポートおよびポート範囲

ネットワーク セキュリティ グループのルールは、1 つの発信元/宛先ポートまたはポート範囲で指定できます。これは、FTP などのアプリケーション用にさまざまなポートを開く必要がある場合に特に便利です。範囲は連続的にのみ指定することができ、個々のポートの指定と混在させることはできません。

ポートの範囲を指定するには、 *DestinationPortRange* パラメーターで以下に示すように ‘-’ 記号を使用します。

	Get-AzureNetworkSecurityGroup -Name ApptierSG `
	| Set-AzureNetworkSecurityRule -Name FTP -Type Inbound -Priority 600 -Action Allow `
		-SourceAddressPrefix INTERNET -SourcePortRange * `
		-DestinationAddressPrefix * -DestinationPortRange 100-500 -Protocol *

### ICMP トラフィック

現在の NSG のルールは、プロトコル 'TCP' または 'UDP' のみを許可します。'ICMP' 専用のタグはありません。ただし、ICMP トラフィックは、VNet 内で任意のポートおよびプロトコル間のトラフィックを許可する受信 VNet ルールを通じて、既定で仮想ネットワーク内で許可されます。

## NSG の関連付け

VM に対する NSG の関連付け - VM に対して NSG を直接関連付けた場合、 NSG のネットワーク アクセス ルールは、その VM を宛先とするすべてのトラフィックに直接適用されます。ルールの変更のために NSG が更新されるたびに、変更は、数分でトラフィックの処理に反映されます。NSG と VM の関連付けを解除した場合、 NSG 前の状態 (NSG が導入される前の、システムの既定の状態) に戻ります。

サブネットに対する NSG の関連付け - NSG をサブネットに関連付けた場合、NSG のネットワーク アクセス ルールは、サブネット内のすべての VM に適用されます。NSG のアクセス ルールが更新されるたびに、変更は、数分でサブネット内のすべての仮想マシンに適用されます。

サブネットと VM に対する NSG の関連付け - ある NSG を VM に関連付け、その VM が存在するサブネットに異なる NSG を関連付けることができます。これはサポートされており、この場合、VM は 2 つの保護レイヤーを取得します。次の図に示すように、受信トラフィックの場合、パケットには、VM のルールの次に、サブネットで指定されたアクセスルールが適用され、送信の場合は、サブネットで指定したルールが適用される前に、まず VM で指定したルールが適用されます。

![NSG ACL](./media/virtual-networks-nsg/figure1.png)

VM またはサブネットに NSG を関連付ける場合、ネットワーク アクセス制御のルールは、非常に明示的になります。プラットフォームによって、特定のポートへのトラフィックを許可する暗黙のルールが挿入されることはありません。この場合、VM 内にエンドポイントを作成するときには、インターネットからのトラフィックを許可するルールも作成する必要があります。これを行わない場合、VIP:<Port> には、外部からアクセスできなくなります。

例: 新しい VM を作成し、新しい NSG も作成します。NSG を VM に関連付けます。VM は、ALLOW VNET INBOUND ルールを通じて、仮想ネットワーク内の他の VM と通信できます。VM は、ALLOW INTERNET OUTBOUND ルールを使用して、インターネットへの送信接続を確立することもできます。後で、VM で動作している Web サイトへのトラフィックを受信するために、ポート 80 でエンドポイントを作成します。VIP (パブリック仮想 IP アドレス) のポート 80 を宛先とするインターネットからのパケットは、次のようなルールを NSG に追加するまで、VM にアクセスできません。

| 名前 | 優先順位 | 発信元 IP | 発信元ポート | 宛先 IP | 宛先ポート | プロトコル | アクセス |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
| WEB | 100 | INTERNET | * | * | 80 | TCP | ALLOW |

## 設計上の考慮事項

NSG を設計するときは、VM がインフラストラクチャ サービスや、Azure によってホストされる PaaS サービスと通信する方法を理解しておく必要があります。SQL Database や Storage などのほとんどの Azure PaaS サービスは、パブリックに公開されたインターネット アドレスからのみアクセスできます。負荷分散プローブについても同様です。

VM や PaaS ロールなどのオブジェクトを、インターネット アクセスの必要性の有無に基づいてサブネットに分離するのは、Azure においてよくあるシナリオです。このようなシナリオでは、SQL Database や Storage などの Azure PaaS サービスへのアクセスは必要であり、パブリック インターネットとの間での送受信通信は必要ではない、VM またはロール インスタンスのサブネットを使用することがあります。

このようなシナリオに対する次のような NSG ルールを考えます。

| 名前 | 優先順位 | 発信元 IP | 発信元ポート | 宛先 IP | 宛先ポート | プロトコル | アクセス |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
|NO INTERNET|100| VIRTUAL_NETWORK|&#42;|INTERNET|&#42;|TCP|DENY| 

このルールは仮想ネットワークからインターネットへのすべてのアクセスを拒否するので、VM は、SQL Database などのパブリック インターネット エンドポイントを必要とするすべての Azure PaaS サービスにアクセスできません。

拒否ルールを使用する代わりに、次に示すような、仮想ネットワークからインターネットへのアクセスは許可し、インターネットから仮想ネットワークへのアクセスは拒否するルールの使用を考えます。

| 名前 | 優先順位 | 発信元 IP | 発信元ポート | 宛先 IP | 宛先ポート | プロトコル | アクセス |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
|TO INTERNET|100| VIRTUAL_NETWORK|&#42;|INTERNET|&#42;|TCP|ALLOW|
|FROM INTERNET|110| INTERNET|&#42;|VIRTUAL_NETWORK|&#42;|TCP|DENY| 


## 計画 - ネットワーク セキュリティ グループのワークフロー

ネットワーク セキュリティ グループを使用する場合の基本的なワークフローの手順を次に示します。

### ワークフロー - 作成および NSG との関連付け、

1. ネットワーク セキュリティ グループ (NSG) の作成

1. 既定のルールが十分でない場合は、ネットワーク セキュリティのルールを追加します。

1. VM に NSG を関連付けます。

1. VM を更新します。

1. 更新後、NSG ルールは直ちに有効になります。

### ワークフロー – 既存の NSG の更新

1. 既存の NSG のルールを追加、削除、または更新します。

1. NSG に関連付けられているすべての VM は、数分で更新されます。NSG ルールが既に VM に関連付けられている場合、VM の更新は必要ありません。

### ワークフロー – NSG との関連付けの変更

1. 既に別の NSG に関連付けられている VM に、新しい NSG を関連付けます。

1. VM を更新します。

1. 新しい NSG のルールは、数分で有効になります。

## ネットワーク セキュリティ グループを作成、構成、および管理する方法

この時点で、 PowerShell コマンドレットおよび REST API のみを使用して、NSG を構成および変更できます。管理ポータルを使用して、NSG を構成することはできません。次の PowerShell コマンドレットは、NSG を作成、構成、および管理するために役立ちます。

**ネットワーク セキュリティ グループの作成**

	New-AzureNetworkSecurityGroup -Name "MyVNetSG" -Location uswest `
		-Label "Security group for my Vnet in West US"

**ルールの追加または更新**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Set-AzureNetworkSecurityRule -Name WEB -Type Inbound -Priority 100 `
		-Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' `
		-DestinationAddressPrefix '*' -DestinationPortRange '*' -Protocol TCP


**NSG からのルールの削除**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Remove-AzureNetworkSecurityRule -Name WEB

**VM への NSG の関連付け**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Set-AzureNetworkSecurityGroupConfig -NetworkSecurityGroupName "MyVNetSG" `
	| Update-AzureVM

**Vm に関連付けられている NSG の表示**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Get-AzureNetworkSecurityGroupAssociation

**VM からの NSG の削除**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Remove-AzureNetworkSecurityGroupConfig -NetworkSecurityGroupName "MyVNetSG" `
	| Update-AzureVM

**NSG サブネットへの関連付け**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'VNetUSWest' `
		-SubnetName 'FrontEndSubnet'

**サブネットに関連付けられている NSG の表示**

	Get-AzureNetworkSecurityGroupForSubnet -SubnetName 'FrontEndSubnet' `
		-VirtualNetworkName 'VNetUSWest' 

**サブネットからの NSG の削除**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'VNetUSWest' `
		-SubnetName 'FrontEndSubnet'

**NSG の削除**

	Remove-AzureNetworkSecurityGroup -Name "MyVNetSG"

**ルールと NSG の詳細の取得**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" -Detailed
 
**NSG に関連するすべての Azure PowerShell コマンドレットの表示**

	Get-Command *azurenetworksecuritygroup*

<!---HONumber=July15_HO4-->