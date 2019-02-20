---
title: Azure Stack のマネージド ディスクに関する相違点と考慮事項 | Microsoft Docs
description: Azure Stack でマネージド ディスクを操作する際の相違点と考慮事項について説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 60f633049d6fdbb59744c8003e742ff649d48ea7
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243388"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack Managed Disks: 相違点と考慮事項

この記事は、[Azure Stack の Managed Disks](azure-stack-manage-vm-disks.md) と [Azure の Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) の既知の相違点をまとめたものです。 Azure Stack と Azure の違いの概要については、「[主な考慮事項](azure-stack-considerations.md)」をご覧ください。

マネージド ディスクを使用すると、VM ディスクに関連付けられている[ストレージ アカウント](../azure-stack-manage-storage-accounts.md)を管理できるため、IaaS VM のディスク管理が簡素化されます。

> [!Note]  
> Azure Stack の Managed Disks は 1808 更新プログラムから使用できます。 1811 更新プログラムから Azure Stack ポータルを使用して仮想マシンを作成すると、既定で有効になります。
  
## <a name="cheat-sheet-managed-disk-differences"></a>チート シート:マネージド ディスクの相違点

| 機能 | Azure (グローバル) | Azure Stack |
| --- | --- | --- |
|保存データの暗号化 |Azure Storage Service Encryption (SSE)、Azure Disk Encryption (ADE)     |BitLocker 128 ビット AES 暗号化      |
|Image          | マネージド カスタム イメージのサポート |サポートされています|
|バックアップ オプション |Azure Backup サービスのサポート |まだサポートされていません |
|ディザスター リカバリーのオプション |Azure Site Recovery のサポート |まだサポートされていません|
|ディスクの種類     |Premium SSD、Standard SSD (プレビュー)、および Standard HDD |Premium SSD、Standard HDD |
|Premium Disk  |完全にサポートされます |プロビジョニング可能ですがパフォーマンス制限や保証がありません  |
|Premium Disk の IOP  |ディスク サイズによって異なります  |ディスクあたりの 2300 IOP |
|Premium ディスクのスループット |ディスク サイズによって異なります |ディスクあたり 145 MB/秒 |
|ディスク サイズ  |Azure Premium ディスク:P4 (32 GiB) ～ P80 (32 TiB)<br>Azure Standard SSD ディスク:E10 (128 GiB) ～ E80 (32 TiB)<br>Azure Standard HDD ディスク:S4 (32 GiB) ～ S80 (32 TiB) |M4:32 GiB<br>M6:64 GiB<br>M10:128 GiB<br>M15:256 GiB<br>M20:512 GiB<br>M30:1,024 GiB |
|ディスクのスナップショット コピー|実行中の VM にアタッチされている Azure のマネージド ディスクのスナップショットはサポートされています|まだサポートされていません |
|ディスクのパフォーマンス分析 |サポートされる合計メトリックおよびディスクあたりのメトリック |まだサポートされていません |
|移行      |VM を再作成する必要なしに既存のアンマネージド Azure Resource Manager VM から移行するためのツールが提供されます  |まだサポートされていません |

> [!NOTE]  
> Azure Stack のマネージド ディスクの IOP とスループットは、プロビジョニングされた数値に代わりの上限数です。これは Azure Stack で実行されているハードウェアとワークロードによって影響を受ける可能性があります。

## <a name="metrics"></a>メトリック

ストレージ メトリックにも相違点があります。

- Azure Stack では、ストレージ メトリックのトランザクション データで、内部と外部のネットワーク帯域幅は区別されません。
- ストレージ メトリックの Azure Stack トランザクション データには、マウントされたディスクへの仮想マシンのアクセスは含まれません。

## <a name="api-versions"></a>API のバージョン

Azure Stack のマネージド ディスクは次の API バージョンをサポートしています。

- 2017-03-30
- 2017-12-01

## <a name="managed-images"></a>マネージド イメージ

Azure Stack では*マネージド イメージ*がサポートされています。これにより、汎用化された VM (アンマネージドとマネージドの両方) でマネージド イメージ オブジェクトを作成できます。以降、このオブジェクトではマネージド ディスク VM のみを作成できます。 マネージド イメージにより、次の 2 つのシナリオが可能になります。

- 汎用化されたアンマネージド VM があり、今後はマネージド ディスクを使用する。
- 汎用化されたマネージド VM があり、同様のマネージド VM を複数作成する。

### <a name="migrate-unmanaged-vms-to-managed-disks"></a>アンマネージド VM をマネージド ディスクに移行する

[こちら](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-vhd-in-a-storage-account)の指示に従い、ストレージ アカウントで、汎用化された VHD からマネージド イメージを作成します。 以降、このイメージはマネージド VM の作成に使用できます。

### <a name="create-managed-image-from-vm"></a>VM からマネージド イメージを作成する

[こちら](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-managed-disk-using-powershell)のスクリプトを使って既存のマネージド ディスク VM からイメージを作成した後、次のサンプル スクリプトで、既存のイメージ オブジェクトから類似の Linux VM を作成します。

```powershell
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "redmond"
$vmName = "myVM"
$imagerg = "managedlinuxrg"
$imagename = "simplelinuxvmm-image-2019122"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

$image = get-azurermimage -ResourceGroupName $imagerg -ImageName $imagename
# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred | `
Set-AzureRmVMSourceImage -Id $image.Id | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

詳細については、Azure マネージド イメージに関する記事「[Azure で汎用化された VM のマネージド イメージを作成する](../../virtual-machines/windows/capture-image-resource.md)」および「[マネージド イメージから VM を作成する](../../virtual-machines/windows/create-vm-generalized-managed.md)」を参照してください。

## <a name="configuration"></a>構成

1808 以降の更新プログラムを適用した後、Managed Disks を使用する前に次の構成を実行する必要があります。

- 1808 更新プログラムの前にサブスクリプションが作成された場合は、次の手順に従ってサブスクリプションを更新します。 そうでないと、このサブスクリプションに VM をデプロイする操作がエラー メッセージ "ディスク マネージャーでの内部エラーです" で失敗することがあります。
   1. テナント ポータルで、**[サブスクリプション]** に移動して、サブスクリプションを検索します。 **[リソース プロバイダー]** をクリックし、**[Microsoft.Compute]** をクリックした後、**[再登録]** をクリックします。
   2. 同じサブスクリプションで、**[アクセス制御 (IAM)]** に移動し、**[Azure Stack – マネージド ディスク]** がリストに含まれていることを確認します。
- マルチテナント環境を使用している場合は、[この記事](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)の次の手順に従って、各ゲスト ディレクトリを再構成するように (お客様の組織内またはサービス プロバイダーからの) クラウド オペレーターに依頼してください。 そうでないと、そのゲスト ディレクトリに関連付けられているサブスクリプションに VM をデプロイする操作がエラー メッセージ "ディスク マネージャーでの内部エラーです" で失敗することがあります。

## <a name="next-steps"></a>次の手順

- [Azure Stack 仮想マシンについての詳細](azure-stack-compute-overview.md)
