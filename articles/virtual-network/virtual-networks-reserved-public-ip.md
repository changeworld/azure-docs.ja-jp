<properties
   pageTitle="予約済み IP | Microsoft Azure"
   description="予約済み IP およびそれらを管理する方法について説明します。"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/10/2016"
   ms.author="telmos" />

# 予約済み IP の概要
Azure での IP アドレスは、動的と予約済みという 2 つのカテゴリに分類されます。 Azure で管理されるパブリック IP アドレスは、既定では動的です。これは、特定のクラウド サービス (VIP) に使用される IP アドレス、または VM やロール インスタンスへの直接アクセスに使用される IP アドレスが、リソースがシャット ダウンまたは割り当て解除された場合に変更される場合があるということです。

IP アドレスが変更されないようにするには、IP アドレスを予約します。予約済み IP は VIP としてのみ使用できるため、リソースがシャット ダウンしたり割り当てが解除されたりした場合でも、クラウド サービスの IP アドレスは変わりません。さらに、VIP として使用されている既存の動的 IP を、予約済み IP アドレスに変換できます。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager モデルを使用してこれらの手順を実行する](virtual-network-ip-addresses-overview-arm.md)方法について説明します。

Azure における [IP アドレス](virtual-network-ip-addresses-overview-classic.md)の動作を理解しておく必要があります。

## 予約済み IP が必要になる場合
- **IP アドレスをサブスクリプションで確実に予約したい場合**。どのような状況でもサブスクリプションから解放されない IP アドレスを予約する必要がある場合は、予約済みパブリック IP を使用します。
- **クラウド サービスまたは VM が停止したり割り当て解除された場合でも、IP アドレスを保持したい場合**。クラウド サービスの VM が停止したり割り当て解除された場合でも、変更されない IP アドレスによるサービスへのアクセスを可能にしたい場合です。
- **Azure の出力トラフィックに予測可能な IP アドレスを使用したい場合**。オンプレミスのファイアウォールが、特定の IP アドレスからのトラフィックのみを許可するよう構成することができます。IP を予約すると、発信元 IP アドレスがわかるため、IP の変更に合わせてファイアウォール ルールを更新する必要がなくなります。

## FAQ
1. 予約済み IP はすべての Azure サービスに使用できますか。
  - 予約済み IP は、VIP を使用して公開される VM およびクラウド サービスのインスタンス ロールに対してのみ使用できます。
1. 予約済み IP は、いくつ使用できますか。
  - 現時点では、すべての Azure サブスクリプションで 20 個の予約済み IP を使用できるようになっています。しかし、予約済み IP の追加を要求することができます。詳細については、[サブスクリプションとサービスの制限](../azure-subscription-service-limits.md)のページを参照してください。
1. 予約済み IP に料金はかかりますか。
  - 料金については、[予約済み IP アドレスの価格詳細](http://go.microsoft.com/fwlink/?LinkID=398482)に関するページを参照してください。
1. どうやって IP アドレスを予約するのですか。
  - PowerShell または [Azure Management REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx) を使用して、特定のリージョンの IP アドレスを予約します。この予約済み IP アドレスは、サブスクリプションに関連付けられます。管理ポータルを使用して IP アドレスを予約することはできません。
1. 予約済み IP を VNet ベースのアフィニティ グループで使用できますか。
  - 予約済み IP はリージョン VNet に対してのみサポートされます。アフィニティ グループに関連付けられている VNet に対してはサポートされません。リージョンまたはアフィニティ グループとの VNet の関連付けの詳細については、[リージョン VNet およびアフィニティ グループについて](virtual-networks-migrate-to-regional-vnet.md)を参照してください。

## 予約済み VIP の管理方法

予約済み IP アドレスは、使用する前にサブスクリプションに追加する必要があります。*Central US* 地域で使用できるパブリック IP アドレスのプールから予約済み IP を作成するには、次の PowerShell コマンドを実行します。

	New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

ただし、予約する IP を指定できないのでご注意ください。サブスクリプションで予約された IP アドレスを表示するには、次の PowerShell コマンドを実行し、*ReservedIPName* および *Address* の値を参照してください 。

	Get-AzureReservedIP

	ReservedIPName       : MyReservedIP
	Address              : 23.101.114.211
	Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
	Label                :
	Location             : Central US
	State                : Created
	InUse                : False
	ServiceName          :
	DeploymentName       :
	OperationDescription : Get-AzureReservedIP
	OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
	OperationStatus      : Succeeded

IP アドレスが予約されると、サブスクリプションとの関連付けは、サブスクリプションが削除されるまで維持されます。上記の予約済み IP を削除するには、次の PowerShell コマンドを実行します。

	Remove-AzureReservedIP -ReservedIPName "MyReservedIP"

## 既存のクラウド サービスの IP アドレスを予約する方法

*- ServiceName* パラメーターを追加することで、既存のクラウド サービスの IP アドレスを予約できます。*米国中部*の *TestService* クラウド サービスの IP アドレスを予約するには、次の PowerShell コマンドを実行します。

	New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService


## 予約済み IP を新しいクラウド サービスに関連付ける方法
次のスクリプトによって新しい予約済み IP が作成され、*TestService* という名前の新しいクラウド サービスに関連付けられます。

	New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"

>[AZURE.NOTE] クラウド サービスに使用する予約済み IP を作成する場合、着信通信用に *VIP:&lt;port number>* を使用して VM を参照する必要があります。IP を予約しても、VM に直接接続できないためです。予約済み IP は、VM がデプロイされたクラウド サービスに割り当てられます。IP を使用して VM に直接接続するには、インスタンスレベル パブリック IP を構成する必要があります。インスタンスレベル パブリック IP とは、VM に直接割り当てられているパブリック IP (ILPIP と呼ばれる) の一種です。この IP は予約できません。詳細については、[インスタンスレベル パブリック IP (ILPIP)](virtual-networks-instance-level-public-ip.md) を参照してください。

## 予約済み IP を実行中のデプロイから削除する方法
上記のスクリプトで作成した新しいサービスに追加された予約済み IP を削除するには、次の PowerShell コマンドを実行します。

	Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService

>[AZURE.NOTE] 実行中のデプロイから予約済み IP を削除しても、サブスクリプションから予約は削除されません。単に IP が、サブスクリプション内の他のリソースで使用できるように解放されるだけだからです。

## 予約済み IP を実行中のデプロイに関連付ける方法
次のスクリプトによって、*TestVM2*という名前の新しい VM を含む *TestService2* という新しいクラウド サービスが作成され、*MyReservedIP* という予約済み IP がクラウド サービスに関連付けられます。

	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| New-AzureVM -ServiceName TestService2 -Location "Central US"
	Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2

## サービス構成ファイルを使用して、クラウド サービスに予約済み IP を関連付ける方法
サービスの構成 (CSCFG) ファイルを使用すると、クラウド サービスに予約済み IP を関連付けることもできます。次の xml サンプルでは、*MyReservedIP* という予約済み VIP が使用されるようにクラウド サービスを構成する方法を示しています。

	<?xml version="1.0" encoding="utf-8"?>
	<ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
	  <Role name="WebRole1">
	    <Instances count="1" />
	    <ConfigurationSettings>
	      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
	    </ConfigurationSettings>
	  </Role>
	  <NetworkConfiguration>
	    <AddressAssignments>
	      <ReservedIPs>
	       <ReservedIP name="MyReservedIP"/>
	      </ReservedIPs>
	    </AddressAssignments>
	  </NetworkConfiguration>
	</ServiceConfiguration>

## 次のステップ

- クラシック デプロイ モデルの [IP アドレス指定](virtual-network-ip-addresses-overview-classic.md)の仕組みを理解します。

- [予約済みプライベート IP アドレス](virtual-networks-reserved-private-ip.md)について理解する。

- [インスタンス レベル パブリック IP (ILPIP) アドレス](virtual-networks-instance-level-public-ip.md)について理解する。

<!---HONumber=AcomDC_0629_2016-->