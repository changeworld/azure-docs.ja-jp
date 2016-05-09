<properties
	pageTitle="仮想マシン スケール セットを作成する | Microsoft Azure"
	description="PowerShell を使用した仮想マシン スケール セットの作成"
	services="virtual-machine-scale-sets"
    documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="davidmu"/>

# Azure PowerShell を使用した Windows 仮想マシン スケール セットの作成

以下の手順では、空白に記入する方式に従って Azure 仮想マシン スケール セットを作成します。この記事の可変要素は、実際の値に置き換える必要があります。引用符内のテキストを、実際のサブスクリプションとアプリケーションに応じた値に置き換えてください。

## 手順 1. Azure PowerShell をインストールする

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## 手順2. サブスクリプションを設定する

1. PowerShell プロンプトを起動します。

2. ご使用のアカウントにログインします。

        Login-AzureRmAccount

3. サブスクリプションを取得します。

        Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

4. 使用するサブスクリプションを最新として設定します。

        $subscr = "subscription name"
        Select-AzureSubscription -SubscriptionName $subscr –Current


## 手順 2: リソースを作成する

新しい仮想マシン スケール セットに必要なリソースを作成します。

### リソース グループ

仮想マシン スケール セットは、リソース グループに含める必要があります。

1.  このコマンドを実行して、利用可能な場所とサポートされるサービスの一覧を取得します。

        Get-AzureLocation | Sort Name | Select Name, AvailableServices

    次のような結果が表示されます。

        Name                AvailableServices
        ----                -----------------
        Australia East      {Compute, Storage, PersistentVMRole, HighMemory}
        Australia Southeast {Compute, Storage, PersistentVMRole, HighMemory}
        Brazil South        {Compute, Storage, PersistentVMRole, HighMemory}
        Central India       {Compute, Storage, PersistentVMRole, HighMemory}
        Central US          {Compute, Storage, PersistentVMRole, HighMemory}
        East Asia           {Compute, Storage, PersistentVMRole, HighMemory}
        East US             {Compute, Storage, PersistentVMRole, HighMemory}
        East US 2           {Compute, Storage, PersistentVMRole, HighMemory}
        Japan East          {Compute, Storage, PersistentVMRole, HighMemory}
        Japan West          {Compute, Storage, PersistentVMRole, HighMemory}
        North Central US    {Compute, Storage, PersistentVMRole, HighMemory}
        North Europe        {Compute, Storage, PersistentVMRole, HighMemory}
        South Central US    {Compute, Storage, PersistentVMRole, HighMemory}
        South India         {Compute, Storage, PersistentVMRole, HighMemory}
        Southeast Asia      {Compute, Storage, PersistentVMRole, HighMemory}
        West Europe         {Compute, Storage, PersistentVMRole, HighMemory}
        West India          {Compute, Storage, PersistentVMRole, HighMemory}
        West US             {Compute, Storage, PersistentVMRole, HighMemory}

    最適な場所を選択し、引用符で囲まれたテキストをその場所の名前に置き換えます。

        $locName = "location name from the list, such as Central US"

4. 引用符で囲まれたテキストを、新しいリソース グループに使用する名前に置き換えた後、前に設定した場所にそれを作成します。

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName

    次のような結果が表示されます。

        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### ストレージ アカウント

スケール セットに作成した仮想マシンには、関連付けられているディスクを保存するストレージ アカウントが必要です。

1. 引用符で囲まれたテキストを、ストレージ アカウントに使用する名前に置き換えた後、その名前が一意であるかどうかをテストします。

        $saName = "storage account name"
        Test-AzureName -Storage $saName

    **False** が返された場合、提案した名前は一意です。

2. 引用符で囲まれたテキストをストレージ アカウントの種類に置き換えた後、前に設定した名前と場所を使ってアカウントを作成します。指定できる値は、Standard\_LRS、Standard\_GRS、Standard\_RAGRS、Premium\_LRS です。

        $saType = "storage account type"
        New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

    次のような結果が表示されます。

        ResourceGroupName   : myrg1
        StorageAccountName  : myst1
        Id                  : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft
	                    	.Storage/storageAccounts/myst1
        Location            : centralus
        AccountType         : StandardLRS
        CreationTime        : 3/15/2016 4:51:52 PM
        CustomDomain        :
        LastGeoFailoverTime :
        PrimaryEndpoints    : Microsoft.Azure.Management.Storage.Models.Endpoints
        PrimaryLocation     : centralus
        ProvisioningState   : Succeeded
        SecondaryEndpoints  :
        SecondaryLocation   :
        StatusOfPrimary     : Available
        StatusOfSecondary   :
        Tags                : {}
        Context             : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext

### Virtual Network

仮想ネットワークは、スケール セット内の仮想マシンのために必要です。

1. 引用符で囲まれたテキストを、仮想ネットワーク内のサブネットに使用する名前に置き換えた後、構成を作成します。

        $subName = "subnet name"
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subName -AddressPrefix 10.0.0.0/24

2. 引用符で囲まれたテキストを、仮想ネットワークに使用する名前に置き換えた後、前に定義した情報とリソースを使ってそれを作成します。

        $netName="virtual network name"
        $vnet=New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### パブリック IP アドレス

ネットワーク インターフェイスを作成する前に、パブリック IP アドレスを作成しておく必要があります。

1. 引用符で囲まれたテキストを、パブリック IP アドレスで使用するドメイン名ラベルに置き換えた後、その名前が一意であるかどうかをテストします。ラベルに使用できるのはアルファベット、数字、ハイフンのみであり、最後の文字はアルファベットまたは数字にする必要があります。

        $domName = "domain name label"
        Test-AzureRmDnsAvailability -DomainQualifiedName $domName -Location $locName

    **False** が返された場合、提案した名前は一意です。

2. 引用符で囲まれたテキストを、パブリック IP アドレスに使用する名前に置き換えた後、パブリック IP アドレスを作成します。

        $pipName = "public ip address name"
        $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic -DomainNameLabel $domName

### ネットワーク インターフェイス

パブリック IP アドレスを作成したので、ネットワーク インターフェイスを作成できます。

1. 引用符で囲まれたテキストを、ネットワーク インターフェイスに使用する名前に置き換えた後、前に作成したリソースを使ってそれを作成します。

        $nicName = "network interface name"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


### 仮想マシン スケール セットの作成

これで必要なリソースがすべてそろいましたので、スケール セットを作成します。

1. 引用符で囲まれたテキストを、IP 構成に使用する名前に置き換えた後、それを作成します。

        $ipName = "IP configuration name"
        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

2. 引用符で囲まれたテキストを、スケール セット構成に使用する名前に置き換えた後、それを作成します。この手順では、セット内の仮想マシンのサイズ (SkuName と呼ばれます) を設定します。[仮想マシンのサイズ](..\virtual-machines\virtual-machines-windows-sizes.md)を確認し、自分のニーズに応じたサイズを調べます。この例の場合、 Standard\_A0 の使用をお勧めします。

        $vmssName = "Scale set configuration name"
        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0"
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssName -Primary $true -IPConfiguration $ipConfig

    次のような結果が表示されます。

        Sku                   : {
                                  "name": "Standard_A0",
                                  "tier": null,
                                  "capacity": 3
        						}
        UpgradePolicy         : {
                                  "mode": "automatic"
                                }
        VirtualMachineProfile : {
                                  "osProfile": null,
                                  "storageProfile": null,
                                  "networkProfile": {
                                    "networkInterfaceConfigurations": [
                                      {
                                        "name": "myniccfg1",
                                        "properties.primary": true,
                                        "properties.ipConfigurations": [
                                          {
                                            "name": "myipconfig1",
                                            "properties.subnet": {
                                              "id": "/subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1"
                                            },
                                            "properties.loadBalancerBackendAddressPools": [],
                                            "properties.loadBalancerInboundNatPools": [],
                                            "id": null
                                          }
                                        ],
                                        "id": null
                                      }
                                    ]
                                  },
                                  "extensionProfile": {
                                    "extensions": null
                                  }
                                }
        ProvisioningState     :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags.Count            : 0
        Tags                  :

3. 引用符で囲まれたテキストを、使用するコンピューター名プレフィックス、仮想マシンの管理者アカウントの名前、アカウントのパスワードに置き換えた後、オペレーティング システム プロファイルを作成します。

        $computerName = "computer name prefix"
        $adminName = "administrator account name"
        $adminPassword = "password for administrator accounts"
        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

    osProfile セクションは、次のようになります。

        "osProfile": {
          "computerNamePrefix": "myvmss1",
          "adminUsername": "########",
          "adminPassword": "########",
          "customData": null,
          "windowsConfiguration": null,
          "linuxConfiguration": null,
          "secrets": null
        },

4. 引用符で囲まれたテキストを、ストレージ プロファイルに使用する名前、イメージ情報、仮想マシンが格納されるディスクのストレージ パスに置き換えた後、プロファイルを作成します。必要な情報を調べる方法については、「[Windows PowerShell と Azure CLI による Azure 仮想マシン イメージのナビゲーションと選択](..\virtual-machines\virtual-machines-windows-cli-ps-findimage.md)」をご覧ください。

        $storeProfile = "storage profile name"
        $imagePublisher = "image publisher name, such as MicrosoftWindowsServer"
        $imageOffer = "offer from publisher, such as WindowsServer"
        $imageSku = "sku of image, such as 2012-R2-Datacenter"
        $vhdContainer = "URI of storage container"
        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storeProfile -VhdContainer $vhdContainer -OsDiskCreateOption "FromImage" -OsDiskCaching "None"

    storageProfile セクションは、次のようになります。

        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2012-R2-Datacenter",
            "version": "latest"
          },
          "osDisk": {
            "name": "mystore1",
            "caching": "None",
            "createOption": "FromImage",
            "osType": null,
            "image": null,
            "vhdContainers": {
              "http://myst1.blob.core.windows.net/vhds"
            }
          }
        },

5. 引用符で囲まれたテキストを、仮想マシン スケール セットの名前に置き換えた後、それを作成します。

        $vmssName = "scale set name"
        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss

    次のように表示され、正常にデプロイされたことがわかります。

        ProvisioningState     : Succeeded
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags.Count            : 0
        Tags                  :

## 手順 3: リソースを調査する

次のリソースを使って、作成した仮想マシン スケール セットを調査します。

- Azure ポータル - ポータルを使用して入手できる情報の量は限られています。
- [Azure リソース エクスプローラー](https://resources.azure.com/) - スケール セットの現在の状態を調査するうえで最適なツールです。
- Azure PowerShell - 次のコマンドを使用して情報を得ることができます。

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

## 次のステップ

1. 詳細については、「[仮想マシン スケール セットの概要](virtual-machine-scale-sets-overview.md)」をご覧ください。

2. 「[自動スケールと仮想マシン スケール セット](virtual-machine-scale-sets-autoscale-overview.md)」の情報を使って、スケール セットの自動スケールを設定することを検討してください。

<!---HONumber=AcomDC_0427_2016-->