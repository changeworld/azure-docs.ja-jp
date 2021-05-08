---
title: PowerShell を使用した Azure スポット仮想マシンのデプロイ
description: Azure PowerShell を使用して Azure スポット仮想マシンをデプロイし、コストを節約する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 9a2ad2eb197af613919efa4414da1759cd47e2e7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802745"
---
# <a name="deploy-azure-spot-virtual-machines-using-azure-powershell"></a>Azure PowerShell を使用して Azure スポット仮想マシンをデプロイする


[Azure スポット仮想マシン](../spot-vms.md)を使用すると、非常に低コストで未使用の容量を利用できます。 Azure で容量の回復が必要になると常に、Azure インフラストラクチャによって Azure スポット仮想マシンが削除されます。 したがって、Azure スポット仮想マシンは、バッチ処理ジョブ、開発/テスト環境、大規模なコンピューティング ワークロードなど、中断してもかまわないワークロードに最適です。

Azure スポット仮想マシンの価格は、リージョンと SKU に基づいて変動します。 詳細については、[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) と [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) の VM 価格を参照してください。 最大価格の設定の詳細については、[Azure スポット仮想マシンの価格](../spot-vms.md#pricing)に関するページを参照してください。

VM に対して、1 時間あたりに支払うことができる最大価格を設定するオプションがあります。 Azure スポット仮想マシンの最大価格は、小数点以下最大 5 桁を使用して米ドル (USD) で設定できます。 たとえば、`0.98765` の値は、1 時間あたり $0.98765 米ドルの最大価格になります。 最大価格を `-1` に設定した場合、VM は価格に基づいて削除されません。 VM の価格は、使用可能な容量とクォータがある限り、現在のスポットの価格または標準 VM の価格のいずれか低い方になります。


## <a name="create-the-vm"></a>VM の作成

構成を作成するための [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) を使用してスポット VM を作成します。 `-Priority Spot` を追加し、`-MaxPrice` を次のいずれかに設定します。
- `-1`。この場合、VM は価格に基づいて削除されません。
- 最大 5 桁の金額 (ドル)。 たとえば、`-MaxPrice .98765` は、スポット VM の価格が 1 時間あたり $0.98765 になると VM の割り当てが解除されることを意味します。


この例では、価格に基づいて割り当てを解除されないスポット VM を作成します (Azure で容量の回復が必要な場合のみ)。 この VM の割り当てを解除するように削除ポリシーが設定されているため、後で再起動することができます。 VM が削除されるときに VM と基になるディスクを削除する場合は、`New-AzVMConfig` で `-EvictionPolicy` を `Delete` に設定します。


```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be an Azure Spot Virtual Machine

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1 -EvictionPolicy Deallocate | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

VM が作成されたら、クエリを実行して、リソース グループ内のすべての VM の最大価格を確認できます。

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="simulate-an-eviction"></a>削除をシミュレートする

REST、PowerShell、または CLI を使用して、Azure スポット仮想マシンの削除をシミュレートし、突然の削除に対してアプリケーションがどの程度適切に応答するかをテストすることができます。

ほとんどの場合、アプリケーションの自動テストに役立てることができるように、REST API [仮想マシン - 削除のシミュレーション](/rest/api/compute/virtualmachines/simulateeviction)を使用します。 REST では、`Response Code: 204` はシミュレートされた削除が成功したことを意味します。 シミュレートされた削除と[スケジュールされたイベント サービス](scheduled-events.md)を組み合わせることで、VM が削除されたときにアプリケーションがどのように応答するかを自動化できます。

スケジュールされた実行中のイベントを確認するには、[Azure Friday - Azure Scheduled Events を使用した VM のメンテナンスの準備](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance)に関するページをご覧ください。


### <a name="quick-test"></a>クイック テスト

シミュレートされた削除がどのように動作するかを簡単に確認するには、スケジュールされたイベント サービスに対してクエリを実行し、PowerShell を使用して、削除をシミュレートするときに、どのように表示されるかを確認してみましょう。

スケジュールされたイベント サービスは、初めてイベントを要求したときに、サービスに対して有効になります。 

VM にリモートでログインし、コマンド プロンプトを開きます。 

VM のコマンド プロンプトで、次のように入力します。

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

この最初の応答には最大 2 分かかることがあります。 これ以降は、ほぼ瞬時に出力が表示されます。

Az PowerShell モジュールがインストールされているコンピューター (ローカル コンピューターなど) から、[Set-AzVM](https://docs.microsoft.com/powershell/module/az.compute/set-azvm) を使用して削除をシミュレートします。 リソース グループ名と VM 名を実際の名前に置き換えます。 

```azurepowershell-interactive
Set-AzVM -ResourceGroupName "mySpotRG" -Name "mySpotVM" -SimulateEviction
```

要求が正常に行われた場合、応答の出力は `Status: Succeeded` になります。

スポット仮想マシンへのリモート接続にすぐに戻り、スケジュールされたイベントのエンドポイントに対して、もう一度クエリを実行します。 詳細情報が含まれる出力が得られるまで、次のコマンドを繰り返します。

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

スケジュールされたイベント サービスが削除通知を受け取ると、次のような応答が返されます。

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

`"EventType":"Preempt"`、およびリソースが VM リソース `"Resources":["myspotvm"]` であることを確認できます。 

`"NotBefore"` 値を確認することで、VM がいつ削除されるかを確認することもできます。 VM は `NotBefore` で指定した時刻より前に削除されないため、これはアプリケーションで正常に終了するための時間になります。


## <a name="next-steps"></a>次のステップ

[Azure CLI](../linux/spot-cli.md)、[ポータル](../spot-portal.md)、または [テンプレート](../linux/spot-template.md)を使用して Azure スポット仮想マシンを作成することもできます。

Azure スポット仮想マシンの価格については、[Azure 小売価格 API](/rest/api/cost-management/retail-prices/azure-retail-prices) を使用して現在の価格情報を照会してください。 `meterName` と `skuName` の両方に `Spot` が含まれています。

エラーが発生した場合は、[エラー コード](../error-codes-spot.md)を参照してください。
