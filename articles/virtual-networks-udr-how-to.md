<properties 
   pageTitle="Azure でルートを作成して IP 転送を有効にする方法"
   description="Azure でルートを作成して IP 転送を有効にする方法"
   services="virtual-networks"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="virtual-networks"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# Azure でルートを作成して IP 転送を有効にする方法
Azure では、仮想アプライアンスを使用して、Azure Virtual Network のトラフィックを処理することができます。ただし、仮想ネットワーク内の VM とクラウド サービスから \(目的とする宛先ではなく\) 仮想アプライアンスにパケットを送信するためのルートを作成する必要があります。また、仮想アプライアンスとして機能する VM は、実際には自分に宛てられていないパケットを受信して転送する必要があるので、IP 転送を有効にする必要があります。

## ルートを管理する方法
Azure におけるルートの追加、削除、変更は、PowerShell で行うことができます。ルートを作成するには、そのルートをホストするルート テーブルを事前に作成しておく必要があります。

### テーブルの作成方法
*FrontEndSubnetRouteTable* という名前のルート テーブルを作成するには、次の PowerShell コマンドを実行します。

	New-AzureRouteTable -Name FrontEndSubnetRouteTable `
	-Location usnorth `
	-Label "Route table for frontend subnet"

### ルート テーブルにルートを追加する方法
*10.2.0.0/16* サブネットの次ホップとして *10.1.1.10* が設定されたルートを先ほど作成したルート テーブルに追加するには、次の PowerShell コマンドを実行します。

	Set-AzureRoute -RouteTableName FrontEndSubnetRouteTable `
	-RouteName FirewallRoute -AddressPrefix 10.2.0.0/16 `
	-NextHopType VirtualAppliance `
	-NextHopIpAddress 10.1.1.10

### サブネットにルートを関連付ける方法
ルート テーブルを使用するためには、そのルート テーブルを少なくとも 1 つのサブネットに関連付ける必要があります。仮想ネットワーク *ProductionVnet* の *FrontEndSubnet* というサブネットに *FrontEndSubnetRouteTable* ルート テーブルを関連付けるには、次のPowerShell コマンドを実行します。

	Set-AzureSubnetRouteTable -VNetName ProductionVnet `
	-SubnetName FrontEndSubnet `
	-RouteTableName FrontEndSubnetRouteTable

### VM 内の適用されたルートを確認する方法
特定の VM またはロール インスタンスで実際に適用されているルートは、Azure に照会して確認できます。表示されるルートには、Azure によって提供された既定のルートに加え、VPN ゲートウェイによってアドバタイズされたルートが含まれます。表示されるルートの上限は 800 個です。

*FirewallAppliance1* という VM 上のプライマリ NIC に関連付けられているルートを確認するには、次の PowerShell コマンドを実行します。

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureEffectiveRouteTable

*FirewallAppliance1* という VM 上の *backendnic* という名前のセカンダリ NIC に関連付けられているルートを確認するには、次の PowerShell コマンドを実行します。

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureEffectiveRouteTable -NetworkInterfaceName backendnic

クラウド サービス *myservice* に属している *myRole* というロール インスタンス上のプライマリ NIC に関連付けられているルートを確認するには、次の PowerShell コマンドを実行します。

	Get-AzureEffectiveRouteTable -ServiceName myservice `
	-RoleInstanceName myRole

## IP 転送を管理する方法
既に述べたように、仮想アプライアンスとして機能する VM またはロール インスタンスに対して IP 転送を有効にする必要があります。

### IP 転送を有効にする方法
*FirewallAppliance1* という VM の IP 転送を有効にするには、次の PowerShell コマンドを実行します。

	Get-AzureVM -Name FirewallAppliance1 `
	| Set-AzureIPForwarding -Enable

クラウド サービス *myService* の *FirewallAppliance1* というロール インスタンスの IP 転送を有効にするには、次の PowerShell コマンドを実行します。

	Set-AzureIPForwarding -ServiceName myService `
	-RoleName FirewallAppliance1 -Enable

### IP 転送を無効にする方法
*FirewallAppliance1* という VM の IP 転送を無効にするには、次の PowerShell コマンドを実行します。

	Get-AzureVM -Name FirewallAppliance1 `
	| Set-AzureIPForwarding -Disable

クラウド サービス *myService* の *FirewallAppliance1* というロール インスタンスの IP 転送を無効にするには、次の PowerShell コマンドを実行します。

	Set-AzureIPForwarding -ServiceName myService `
	-RoleName FirewallAppliance1 -Disable

### IP 転送の状態を確認する方法
*FirewallAppliance1* という VM の IP 転送の状態を確認にするには、次の PowerShell コマンドを実行します。

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureIPForwarding

## 関連項目

[ユーザー定義のルートと IP 転送の概要](../virtual-networks-udr-overview)

[インスタンスレベル パブリック IP \(ILIP\)](../virtual-networks-instance-level-public-ip)

[仮想ネットワークの概要](https://msdn.microsoft.com/library/azure/jj156007.aspx)
<!--HONumber=52-->
