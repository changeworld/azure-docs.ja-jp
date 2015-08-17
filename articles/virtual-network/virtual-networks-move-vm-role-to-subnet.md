<properties 
   pageTitle="VM またはロール インスタンスを別のサブネットに移動する方法"
   description="別のサブネットに VM とロール インスタンスを移動する方法を学習します。"
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
   ms.date="05/29/2015"
   ms.author="telmos" />

# VM またはロール インスタンスを別のサブネットに移動する方法

PowerShell を使用すると、同じ Virtual Network (VNet) 内のサブネット間で VM を移動することができます。ロール インスタンスを移動する場合は、PowerShell を使用せずに、CSCFG を編集します。

サブネット間で VM を移動する理由は何でしょうか。 たとえば、古いサブネットが小さいにもかかわらず、その中で既存の VM が動作しているために拡張できないときは、サブネットを移行することで問題を解決できます。サブネットを移行する場合は、まず新しい、より規模の大きいサブネットを作成し、そこに VM を移行します。そして移行が完了したら、空になった古いサブネットを削除します。

## 別のサブネットに VM を移動する方法

VM を移動するには、次の例をテンプレートとして使用して、Set-AzureSubnet という PowerShell コマンドレットを実行します。以下の例では、TestVM を現在のサブネットから Subnet-2 に移動しています。必ず、環境に合わせて例を編集してください。移動の手順として Update-AzureVM コマンドレットを実行すると、更新プロセスの一環として VM が常に再起動されます。

	Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
	| Set-AzureSubnet –SubnetNames Subnet-2 `
	| Update-AzureVM

VM に対して静的 DIP が指定されている場合は、VM を新しいサブネットに移動する前に、その設定を解除する必要があります。この場合、以下のように指定します。

	Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
	| Remove-AzureStaticVNetIP `
	| Update-AzureVM
	Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
	| Set-AzureSubnet -SubnetNames Subnet-2 `
	| Update-AzureVM

## 別のサブネットにロール インスタンスを移動する方法

ロール インスタンスを移動するには、CSCFG ファイルを編集します。以下の例では、Virtual Network *VNETName* 内の「Role0」 を、現在のサブネットから *Subnet-2* に移動します。ロール インスタンスは既にデプロイされているので、単に Subnet name = Subnet-2 の部分を変更します。必ず、環境に合わせて例を編集してください。

	<NetworkConfiguration>
	    <VirtualNetworkSite name="VNETName" />
	    <AddressAssignments>
	       <InstanceAddress roleName="Role0">
	            <Subnets><Subnet name="Subnet-2" /></Subnets>
	       </InstanceAddress>
	    </AddressAssignments>
	</NetworkConfiguration> 

<!---HONumber=August15_HO6-->