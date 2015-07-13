<properties 
   pageTitle="Azure でルートを作成して IP 転送を有効にする方法"
   description="UDR と IP 転送の管理方法を説明します。"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# Azure でルートを作成して IP 転送を有効にする方法
Azure では、仮想アプライアンスを使用して、Azure Virtual Network のトラフィックを処理することができます。ただし、仮想ネットワーク内の VM とクラウド サービスから (目的とする宛先ではなく) 仮想アプライアンスにパケットを送信するためのルートを作成する必要があります。また、仮想アプライアンスとして機能する VM は、実際には自分に宛てられていないパケットを受信して転送する必要があるので、IP 転送を有効にする必要があります。

##ルートを管理する方法
Azure におけるルートの追加、削除、変更は、PowerShell で行うことができます。ルートを作成するには、そのルートをホストするルート テーブルを事前に作成しておく必要があります。

### テーブルの作成方法
*FrontEndSubnetRouteTable* という名前のルート テーブルを作成するには、次の PowerShell コマンドを実行します。

```powershell
New-AzureRouteTable -Name FrontEndSubnetRouteTable `
	-Location uscentral `
	-Label "Route table for frontend subnet"
```

上記のコマンドの出力は次のようになります。

	Error          :
	HttpStatusCode : OK
	Id             : 085ac8bf-26c3-9c4c-b3ae-ebe880108c70
	Status         : Succeeded
	StatusCode     : OK
	RequestId      : a8cc03ca42d39f27adeaa9c1986c14f7

### ルート テーブルにルートを追加する方法
*10.2.0.0/16* サブネットの次ホップとして *10.1.1.10* が設定されたルートを先ほど作成したルート テーブルに追加するには、次の PowerShell コマンドを実行します。

```powershell
Get-AzureRouteTable FrontEndSubnetRouteTable `
	|Set-AzureRoute -RouteName FirewallRoute -AddressPrefix 10.2.0.0/16 `
	-NextHopType VirtualAppliance `
	-NextHopIpAddress 10.1.1.10
```

上記のコマンドの出力は次のようになります。

	Name     : FrontEndSubnetRouteTable
	Location : Central US
	Label    : Route table for frontend subnet
	Routes   : 
	           Name                 Address Prefix    Next hop type        Next hop IP address
	           ----                 --------------    -------------        -------------------
	           firewallroute        10.2.0.0/16       VirtualAppliance     10.1.1.10    

### サブネットにルートを関連付ける方法
ルート テーブルを使用するためには、そのルート テーブルを少なくとも 1 つのサブネットに関連付ける必要があります。仮想ネットワーク *ProductionVnet* の *FrontEndSubnet* というサブネットに *FrontEndSubnetRouteTable* ルート テーブルを関連付けるには、次のPowerShell コマンドを実行します。

```powershell
Set-AzureSubnetRouteTable -VirtualNetworkName ProductionVnet `
	-SubnetName FrontEndSubnet `
	-RouteTableName FrontEndSubnetRouteTable
```

### VM 内の適用されたルートを確認する方法
特定の VM またはロール インスタンスで実際に適用されているルートは、Azure に照会して確認できます。表示されるルートには、Azure によって提供された既定のルートに加え、VPN ゲートウェイによってアドバタイズされたルートが含まれます。表示されるルートの上限は 800 個です。

*FWAppliance1* という VM 上のプライマリ NIC に関連付けられているルートを確認するには、次の PowerShell コマンドを実行します。

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Get-AzureEffectiveRouteTable
```

上記のコマンドの出力は次のようになります。

	Effective routes : 
	                   Name            Address Prefix    Next hop type    Next hop IP address Status   Source     
	                   ----            --------------    -------------    ------------------- ------   ------     
	                                   {10.0.0.0/8}      VNETLocal                            Active   Default    
	                                   {0.0.0.0/0}       Internet                             Active   Default    
	                                   {25.0.0.0/8}      Null                                 Active   Default    
	                                   {100.64.0.0/10}   Null                                 Active   Default    
	                                   {172.16.0.0/12}   Null                                 Active   Default    
	                                   {192.168.0.0/16}  Null                                 Active   Default    
	                   firewallroute   {10.2.0.0/16}     Null             10.1.1.10           Active   User      

*FWAppliance1* という VM 上の *backendnic* という名前のセカンダリ NIC に関連付けられているルートを確認するには、次の PowerShell コマンドを実行します。

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Get-AzureEffectiveRouteTable -NetworkInterfaceName backendnic
```

クラウド サービス *ProductionVMs* に属している *myRole* というロール インスタンス上のプライマリ NIC に関連付けられているルートを確認するには、次の PowerShell コマンドを実行します。

```powershell
Get-AzureEffectiveRouteTable -ServiceName ProductionVMs `
	-RoleInstanceName myRole
```

## IP 転送を管理する方法
既に述べたように、仮想アプライアンスとして機能する VM またはロール インスタンスに対して IP 転送を有効にする必要があります。

### IP 転送を有効にする方法
*FWAppliance1* という VM の IP 転送を有効にするには、次の PowerShell コマンドを実行します。

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Set-AzureIPForwarding -Enable
```

クラウド サービス *DMZService* の *FWAppliance* というロール インスタンスの IP 転送を有効にするには、次の PowerShell コマンドを実行します。

```powershell
Set-AzureIPForwarding -ServiceName DMZService `
	-RoleName FWAppliance -Enable
```

### IP 転送を無効にする方法
*FWAppliance1* という VM の IP 転送を無効にするには、次の PowerShell コマンドを実行します。

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Set-AzureIPForwarding -Disable
```

クラウド サービス *DMZService* の *FWAppliance* というロール インスタンスの IP 転送を無効にするには、次の PowerShell コマンドを実行します。

```powershell
Set-AzureIPForwarding -ServiceName DMZService `
	-RoleName FWAppliance -Disable
```

### IP 転送の状態を確認する方法
*FWAppliance1* という VM の IP 転送の状態を確認にするには、次の PowerShell コマンドを実行します。

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Get-AzureIPForwarding
```

## 関連項目

[ユーザー定義のルートと IP 転送の概要](../virtual-networks-udr-overview)

<!---HONumber=62-->