---
title: "VM 用の一般的なネットワークの Azure PowerShell コマンド | Microsoft Docs"
description: "仮想ネットワークと VM に関連するリソースの作成を開始する際に使用される一般的な PowerShell コマンド"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: ce08e9dcd0585e00bdd42b8aa34ac2c597a53a6e


---
# <a name="common-network-azure-powershell-commands-for-vms"></a>VM 用の一般的なネットワークの Azure PowerShell コマンド
仮想マシンを作成するには、 [仮想ネットワーク](../virtual-network/virtual-networks-overview.md) を作成するか、VM を追加できる既存の仮想ネットワークについて知る必要があります。 通常、VM を作成するときに、この記事で説明されているリソースの作成を検討する必要があります。

最新バージョンの Azure PowerShell をインストールし、サブスクリプションを選択して、ご利用のアカウントにサインインする方法については、「[Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」を参照してください。

## <a name="create-network-resources"></a>ネットワーク リソースを作成する
| タスク | コマンド |
| --- | --- |
| サブネットの構成の作成 |$subnet1 = [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -Name "サブネット名" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name "サブネット名" -AddressPrefix XX.X.X.X/XX<BR><BR>標準的なネットワークには、[インターネットに接続するロード バランサー](../load-balancer/load-balancer-internet-overview.md)のサブネットと[内部ロード バランサー](../load-balancer/load-balancer-internal-overview.md)の別のサブネットがあります。 |
| 仮想ネットワークの作成 |$vnet = [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -Name "仮想ネットワーク名" -ResourceGroupName "リソース グループ名" -Location "場所の名前" -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| 一意のドメイン名のテスト |[Test-AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) -DomainQualifiedName "ドメイン名" -Location "場所の名前"<BR><BR>パブリック IP リソースに DNS ドメイン名を指定して、Azure で管理される DNS サーバーの[パブリック IP アドレス](../virtual-network/virtual-network-ip-addresses-overview-arm.md)に対する domainname.location.cloudapp.azure.com のマッピングを作成できます。 この名前には、文字、数字、ハイフンのみを含めることができます。 最初と最後の文字が英字または数字であり、ドメイン名が Azure の場所内で一意である必要があります。 **True** が返された場合、提案した名前はグローバルに一意です。 |
| パブリック IP アドレスの作成 |$pip = [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -Name "IP アドレス名" -ResourceGroupName "リソース グループ名" -DomainNameLabel "ドメイン名" -Location "場所の名前" -AllocationMethod Dynamic<BR><BR>パブリック IP アドレスは、事前にテストしたドメイン名を使用し、ロード バランサーのフロントエンド構成で使用されます。 |
| フロントエンド IP 構成の作成 |$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -Name "フロントエンド IP 名" -PublicIpAddress $pip<BR><BR>フロントエンド構成には、受信ネットワーク トラフィック用にあらかじめ作成したパブリック IP アドレスが含まれます。 |
| バックエンド アドレス プールの作成 |$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -Name "バックエンド プール名"<BR><BR>ネットワーク インターフェイス経由でアクセスされるロード バランサーのバックエンドの内部アドレスを指定します。 |
| プローブを作成する |$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -Name "プローブ名" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>バックエンド アドレス プール内の仮想マシン インスタンスの可用性の確認に使用される正常性プローブが含まれます。 |
| 負荷分散規則の作成 |$lbRule = [New-AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>ロード バランサーのパブリック ポートをバックエンド アドレス プール内のポートに割り当てる規則が含まれます。 |
| 受信 NAT 規則の作成 |$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -Name "規則の名前" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>ロード バランサーのパブリック ポートをバックエンド アドレス プール内の特定の仮想マシンのポートにマッピングする規則が含まれます。 |
| ロード バランサーの作成 |$loadBalancer = [New-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) -ResourceGroupName "リソース グループ名" -Name "ロード バランサー名" -Location "場所の名前" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| ネットワーク インターフェイスの作成 |$nic1= [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) -ResourceGroupName "リソース グループ名" -Name "ネットワーク インターフェイス名" -Location "場所の名前" -PrivateIpAddress XX.X.X.X -Subnet subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>パブリック IP アドレスと事前に作成した仮想ネットワークのサブネットを使用して、ネットワーク インターフェイスを作成します。 |

## <a name="get-information-about-network-resources"></a>ネットワーク リソースに関する情報の取得
| タスク | コマンド |
| --- | --- |
| 仮想ネットワークの一覧表示 |[Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -ResourceGroupName "リソース グループ名"<BR><BR>リソース グループ内のすべての仮想ネットワークが一覧表示されます。 |
| 仮想ネットワークに関する情報の取得 |Get-AzureRmVirtualNetwork -Name "仮想ネットワーク名" -ResourceGroupName "リソース グループ名" |
| 仮想ネットワーク内のサブネットの一覧表示 |Get-AzureRmVirtualNetwork -Name "仮想ネットワーク名" -ResourceGroupName "リソース グループ名" &#124; Select Subnets |
| サブセットに関する情報の取得 |[Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -Name "サブセット名" -VirtualNetwork $vnet<BR><BR>指定された仮想ネットワーク内のサブネットに関する情報を取得します。 $Vnet 値は、Get AzureRmVirtualNetwork によって返されるオブジェクトを表します。 |
| IP アドレスの一覧表示 |[Get-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619342.aspx) -ResourceGroupName "リソース グループ名"<BR><BR>リソース グループ内の Public IP Addresses を一覧表示します。 |
| ロード バランサーの一覧表示 |[Get-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603668.aspx) -ResourceGroupName "リソース グループ名"<BR><BR>リソース グループ内のすべてのロード バランサーが一覧表示されます。 |
| ネットワーク インターフェイスの一覧表示 |[Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) -ResourceGroupName "リソース グループ名"<BR><BR>リソース グループ内のすべてのネットワーク インターフェイスが一覧表示されます。 |
| ネットワーク インターフェイスに関する情報の取得 |Get-AzureRmNetworkInterface -Name "ネットワーク インターフェイス名" -ResourceGroupName "リソース グループ名"<BR><BR>特定のネットワーク インターフェイスに関する情報を取得します。 |
| ネットワーク インターフェイスの IP 構成の取得 |[Get-AzureRmNetworkInterfaceIPConfig](https://msdn.microsoft.com/library/mt732618.aspx) -Name "IP 構成の名前" -NetworkInterface $nic<BR><BR>指定したネットワーク インターフェイスの IP 構成に関する情報を取得します。 $nic 値は、Get-AzureRmNetworkInterface によって返されるオブジェクトを表します。 |

## <a name="manage-network-resources"></a>ネットワーク リソースの管理
| タスク | コマンド |
| --- | --- |
| 仮想ネットワークへのサブネットの追加 |[Add-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603722.aspx) -AddressPrefix XX.X.X.X/XX -Name "サブネット名" -VirtualNetwork $vnet<BR><BR>既存の仮想ネットワークにサブネットを追加します。 $Vnet 値は、Get AzureRmVirtualNetwork によって返されるオブジェクトを表します。 |
| 仮想ネットワークの削除 |[Remove-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt619338.aspx) -Name "仮想ネットワーク名" -ResourceGroupName "リソース グループ名"<BR><BR>リソース グループから、指定された仮想ネットワークを削除します。 |
| ネットワーク インターフェイスの削除 |[Remove-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt603836.aspx) -Name "ネットワーク インターフェイス名" -ResourceGroupName "リソース グループ名"<BR><BR>リソース グループから、指定されたネットワーク インターフェイスを削除します。 |
| ロード バランサーの削除 |[Remove-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) -Name "ロード バランサー名" -ResourceGroupName "リソース グループ名"<BR><BR>リソース グループから、指定されたロード バランサーを削除します。 |
| パブリック IP アドレスの削除 |[Remove-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619352.aspx)-Name "IP アドレス名" -ResourceGroupName "リソース グループ名"<BR><BR>リソース グループから、指定されたパブリック IP アドレスを削除します。 |

## <a name="next-steps"></a>次のステップ
* [VM を作成する](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ときに、作成したネットワーク インターフェイスを使用します。
* [複数のネットワーク インターフェイスを持つ VM を作成する](../virtual-network/virtual-networks-multiple-nics.md)方法について学習します。




<!--HONumber=Dec16_HO2-->


