<properties
   pageTitle="Windows VM での複数 NIC の構成 | Microsoft Azure"
   description="Azure PowerShell または Resource Manager テンプレートを使用して、複数の NIC を持つ VM を作成する方法について説明します。"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="08/04/2016"
   ms.author="iainfou"/>

# 複数 NIC を持つ VM の作成
Azure では、複数の仮想ネットワーク インターフェイス (NIC) を持つ仮想マシン (VM) を作成できます。一般的なシナリオは、フロント エンドおよびバック エンド接続用に別々のサブネットを使用するか、監視またはバックアップ ソリューション専用のネットワークを用意することです。この記事では、複数の NIC を持つ VM を作成するためのクイック コマンドを紹介します。独自の PowerShell スクリプト内に複数の NIC を作成する方法など、詳しくは、[複数 NIC の VM のデプロイ](../virtual-network/virtual-network-deploy-multinic-arm-ps.md)に関する記事を参照してください。[VM のサイズ](virtual-machines-windows-sizes.md)によってサポートされる NIC の数が異なります。VM のサイズを決める際はご注意ください。

>[AZURE.WARNING] VM の作成時に複数の NIC をアタッチする必要があります。既存の VM に NIC を追加することはできません。[元の仮想ディスクに基づいて新しい VM を作成](virtual-machines-windows-specialized-image.md)し、VM をデプロイするときに複数の NIC を作成できます。

## コア リソースの作成
[最新の Azure PowerShell がインストールおよび構成](../powershell-install-configure.md)されていることを確認します。

まず、リソース グループを作成します。

```powershell
New-AzureRmResourceGroup -Name TestRG -Location WestUS
```

VM を保持するストレージ アカウントを作成します。

```powershell
$storageAcc = New-AzureRmStorageAccount -Name teststorage `
    -ResourceGroupName TestRG -Kind Storage -SkuName Premium_LRS -Location WestUS
```

## 仮想ネットワークとサブネットの作成
2 つの仮想ネットワーク サブネットを定義します。1 つはフロント エンド トラフィック用、もう 1 つはバック エンド トラフィック用です。これらのサブネットを持つ仮想ネットワークを作成します。

```powershell
$frontEndSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "FrontEnd" `
    -AddressPrefix 192.168.1.0/24
$backEndSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "BackEnd" `
    -AddressPrefix 192.168.2.0/24


$vnet = New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
    -AddressPrefix 192.168.0.0/16 -Location WestUS `
    -Subnet $frontEndSubnet,$backEndSubnet
```


## 複数の NIC の作成
2 つの NIC を作成します。1 つをフロント エンド サブネットに、もう 1 つをバック エンド サブネットにアタッチします。

```powershell
$frontEnd = $vnet.Subnets|?{$_.Name -eq 'FrontEnd'}
$NIC1 = New-AzureRmNetworkInterface -Name NIC1 -ResourceGroupName TestRG `
        -Location WestUS -SubnetId $FrontEnd.Id

$backEnd = $vnet.Subnets|?{$_.Name -eq 'BackEnd'}
$NIC2 = New-AzureRmNetworkInterface -Name NIC2 -ResourceGroupName TestRG `
        -Location WestUS -SubnetId $BackEnd.Id
```

通常、複数の VM 間にトラフィックを分散および管理するための[ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)または[ロード バランサー](../load-balancer/load-balancer-overview.md)も作成します。ネットワーク セキュリティ グループの作成方法や NIC の割り当て方法については、[より詳しい複数 NIC の VM](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) に関する記事を参照してください。


## 仮想マシンの作成
では、VM 構成を構築してみましょう。各 VM サイズについて、1 つの VM に追加できる NIC の合計数には制限があります。詳しくは、「[Windows VM のサイズ](virtual-machines-windows-sizes.md)」を参照してください。次の例では、最大 2 つの NIC をサポートする VM サイズを使用します (`Standard_DS2_v2`)。

```powershell
$cred = Get-Credential

$vmConfig = New-AzureRmVMConfig -VMName TestVM -VMSize "Standard_DS2_v2"

$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName TestVM `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
```

以前に作成した 2 つの NIC をアタッチします。

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $NIC1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $NIC2.Id
```

新しい VM 用のストレージと仮想ディスクを構成します。

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption fromImage
```

最後に、VM を作成します。

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName TestRG -Location WestUS
```

## Resource Manager テンプレートを使用して複数の NIC を作成する
Azure Resource Manager テンプレートで宣言型の JSON ファイルを使用して環境を定義します。詳しくは、「[Azure Resource Manager の概要](../resource-group-overview.md)」を参照してください。Resource Manager テンプレートでは、複数の NIC の作成など、デプロイ時にリソースの複数のインスタンスを作成することができます。*copy* を使用して、作成するインスタンスの数を指定します。

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

詳しくは、[*copy* を使用した複数のインスタンスの作成](../resource-group-create-multiple.md)に関する記事を参照してください。

`copyIndex()` を使用してリソース名に数値を追加することもできます。これにより、`NIC1`、`NIC2` などを作成することができます。インデックス値を追加する例を次に示します。

```bash
"name": "[concat('NIC-', copyIndex())]", 
```

完全な例については、「[Resource Manager テンプレートを使用して複数の NIC を作成する](../virtual-network/virtual-network-deploy-multinic-arm-template.md)」を参照してください。

## 次のステップ
複数 NIC を持つ VM を作成するときは必ず「[Windows VM のサイズ](virtual-machines-windows-sizes.md)」をご覧ください。VM の各サイズでサポートされている NIC の最大数に注意してください。

既存の VM に NIC を追加することはできません。VM をデプロイするときにすべての NIC を作成する必要があります。デプロイメントの計画時に、初めから必要なすべてのネットワーク接続があることを確認してください。

<!---HONumber=AcomDC_0817_2016-->