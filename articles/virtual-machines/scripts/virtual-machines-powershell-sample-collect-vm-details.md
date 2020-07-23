---
title: PowerShell を使用してサブスクリプション内のすべての VM に関する詳細情報を収集する
description: PowerShell を使用してサブスクリプション内のすべての VM に関する詳細情報を収集する
services: virtual-machines-windows
documentationcenter: virtual-machines
author: v-miegge
manager: dcscontentpm
editor: v-miegge
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/01/2019
ms.author: v-miegge
ms.custom: mvc
ms.openlocfilehash: a45f0a882c58c7035badcc1270c66bd9c6fb252a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86526913"
---
# <a name="collect-details-about-all-vms-in-a-subscription-with-powershell"></a>PowerShell を使用してサブスクリプション内のすべての VM に関する詳細情報を収集する

このスクリプトでは、指定されたサブスクリプション内の VM の VM 名、リソース グループ名、リージョン、VM サイズ、仮想ネットワーク、サブネット、プライベート IP アドレス、OS タイプ、およびパブリック IP アドレスを含む csv が作成されます。

[Azure サブスクリプション](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)をお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free)を作成してください。

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

## <a name="sample-script"></a>サンプル スクリプト

```azurepowershell-interactive
#Provide the subscription Id where the VMs reside
$subscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

#Provide the name of the csv file to be exported
$reportName = "myReport.csv"

Select-AzSubscription $subscriptionId
$report = @()
$vms = Get-AzVM
$publicIps = Get-AzPublicIpAddress 
$nics = Get-AzNetworkInterface | ?{ $_.VirtualMachine -NE $null} 
foreach ($nic in $nics) { 
    $info = "" | Select VmName, ResourceGroupName, Region, VmSize, VirturalNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress 
    $vm = $vms | ? -Property Id -eq $nic.VirtualMachine.id 
    foreach($publicIp in $publicIps) { 
        if($nic.IpConfigurations.id -eq $publicIp.ipconfiguration.Id) {
            $info.PublicIPAddress = $publicIp.ipaddress
            } 
        } 
        $info.OsType = $vm.StorageProfile.OsDisk.OsType 
        $info.VMName = $vm.Name 
        $info.ResourceGroupName = $vm.ResourceGroupName 
        $info.Region = $vm.Location 
        $info.VmSize = $vm.HardwareProfile.VmSize
        $info.VirturalNetwork = $nic.IpConfigurations.subnet.Id.Split("/")[-3] 
        $info.Subnet = $nic.IpConfigurations.subnet.Id.Split("/")[-1] 
        $info.PrivateIpAddress = $nic.IpConfigurations.PrivateIpAddress 
        $report+=$info 
    } 
$report | ft VmName, ResourceGroupName, Region, VmSize, VirturalNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress 
$report | Export-CSV "$home/$reportName"
```

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトは、次のコマンドを使用して、サブスクリプション内の VM の詳細の csv エクスポートを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

|コマンド|Notes|
|-|-|
|[Select-AzSubscription](/powershell/module/az.accounts/set-azcontext)|現在のセッションで使用するコマンドレットのテナント、サブスクリプション、および環境を設定します。|
|[Get-AzVM](/powershell/module/az.compute/get-azvm)|仮想マシンのプロパティを取得します。|
|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)|パブリック IP アドレスを取得します。|
|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface)|ネットワーク インターフェイスを取得します。|

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/powershell-samples.md?toc=/azure/virtual-machines/windows/toc.json)にあります。
