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
	ms.date="09/25/2016"
	ms.author="davidmu"/>

# Azure PowerShell を使用した Windows 仮想マシン スケール セットの作成

以下の手順では、空白に記入する方式に従って Azure 仮想マシン スケール セットを作成します。スケール セットについて詳しくは、「[仮想マシン スケール セットの概要](virtual-machine-scale-sets-overview.md)」をご覧ください。

この記事の手順を実行するには約 30 分かかります。

## 手順 1: Azure PowerShell をインストールする

最新バージョンの Azure PowerShell をインストールし、使用するサブスクリプションを選択し、Azure アカウントにサインインする方法については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」を参照してください。

## 手順 2: リソースを作成する

新しい仮想マシン スケール セットに必要なリソースを作成します。

### リソース グループ

仮想マシン スケール セットは、リソース グループに含める必要があります。

1.  利用可能な場所とサポートされるサービスの一覧を取得します。

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

2. 最適な場所を選択し、**$locName** の値を場所の名前に置き換えた後、変数を作成します。

        $locName = "location name from the list, such as Central US"

3. **$rgName** の値を新しいリソース グループに使用する名前に置き換えた後、変数を作成します。

        $rgName = "resource group name"
        
4. リソース グループを作成します。
    
        New-AzureRmResourceGroup -Name $rgName -Location $locName

    次のような結果が表示されます。

        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### ストレージ アカウント

ストレージ アカウントは、オペレーティング システム ディスクとスケーリングに使用する診断データを格納する目的で、仮想マシンによって使用されます。スケール セットに作成した 20 個の仮想マシンにそれぞれ 1 つのストレージ アカウントを所有することをお勧めします。スケール セットは、スケールアウトを簡単に行うことを目的として設計されているため、計画したスケール セット内の仮想マシンの最大数に対して必要なだけのストレージ アカウントを作成します。この記事では、スケール セット内の仮想マシンを無理なく 60 個まで増やせるように、3 つのストレージ アカウントを作成する例を説明します。

1. **$rgName** の値をストレージ アカウントに使用する名前に置き換えた後、変数を作成します。

        $saName = "storage account name"
        
2. 選択した名前が一意であるかどうかをテストします。
    
        Test-AzureName -Storage $saName

    **False** が返された場合、提案した名前は一意です。

3. **$saType** の値をストレージ アカウントの種類に置き換えた後、変数を作成します。

        $saType = "storage account type"
        
    指定できる値は、Standard\_LRS、Standard\_GRS、Standard\_RAGRS、Premium\_LRS です。
        
4. アカウントを作成します。
    
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

5. 手順 1. から 4. を繰り返して、たとえば myst1、myst2、myst3 の 3 つのストレージ アカウントを作成します。

### Virtual Network

仮想ネットワークは、スケール セット内の仮想マシンのために必要です。

1. **$subName** の値を、仮想ネットワーク内のサブネットに使用する名前に置き換えた後、変数を作成します。

        $subName = "subnet name"
        
2. サブネット構成を作成します。
    
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subName -AddressPrefix 10.0.0.0/24
        
    お使いの仮想ネットワークではアドレス プレフィックスが異なる場合があります。

3. **$netName** の値を、仮想ネットワークに使用する名前に置き換えた後、変数を作成します。

        $netName = "virtual network name"
        
4. 仮想ネットワークを作成します。
    
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### パブリック IP アドレス

ネットワーク インターフェイスを作成する前に、パブリック IP アドレスを作成しておく必要があります。

1. **$domName** の値を、パブリック IP アドレスで使用するドメイン名ラベルに置き換えた後、変数を作成します。

        $domName = "domain name label"
        
    ラベルに使用できるのはアルファベット、数字、ハイフンのみであり、最後の文字はアルファベットまたは数字にする必要があります。
    
2. 名前が一意であるかどうかをテストします。
    
        Test-AzureRmDnsAvailability -DomainQualifiedName $domName -Location $locName

    **True** が返された場合、提案した名前は一意です。

3. **$pipName** の値を、パブリック IP アドレスに使用する名前に置き換えた後、変数を作成します。

        $pipName = "public ip address name"
        
4. パブリック IP アドレスを作成します。
    
        $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic -DomainNameLabel $domName

### ネットワーク インターフェイス

パブリック IP アドレスを作成したので、ネットワーク インターフェイスを作成できます。

1. **$nicName** の値を、ネットワーク インターフェイスに使用する名前に置き換えた後、変数を作成します。

        $nicName = "network interface name"
        
2. ネットワーク インターフェイスを作成します。
    
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### スケール セットの構成

スケール セットの構成に必要なリソースはすべて揃っているため、作成を開始しましょう。

1. **$ipName** の値を、IP 構成に使用する名前に置き換えた後、変数を作成します。

        $ipName = "IP configuration name"
        
2. IP 構成を作成します。

        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

2. **$vmssConfig** の値を、スケール セットの構成に使用する名前に置き換えた後、変数を作成します。

        $vmssConfig = "Scale set configuration name"
        
3. スケール セットの構成を作成します。

        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A1" -UpgradePolicyMode "manual"
        
    この例では、3 つの仮想マシンを使用して作成されるスケール セットを示しています。スケール セットの容量について詳しくは、「[仮想マシン スケール セットの概要](virtual-machine-scale-sets-overview.md)」をご覧ください。この手順では、セット内の仮想マシンのサイズ (SkuName と呼ばれます) も設定します。[仮想マシンのサイズ](../virtual-machines/virtual-machines-windows-sizes.md)に関するページを確認し、自分のニーズに応じたサイズを調べます。
    
4. ネットワーク インターフェイス構成をスケール セット構成に追加します。
        
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
        
    次のような結果が表示されます。

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     :
        OverProvision         :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags                  :

#### オペレーティング システム プロファイル

1. **$computerName** の値を、使用するコンピューター名プレフィックスに置き換えた後、変数を作成します。

        $computerName = "computer name prefix"
        
2. **$adminName** の値を、仮想マシンの管理者アカウント名に置き換えた後、変数を作成します。

        $adminName = "administrator account name"
        
3. **$adminPassword** の値を、アカウント パスワードに置き換えた後、変数を作成します。

        $adminPassword = "password for administrator accounts"
        
4. オペレーティング システム プロファイルを作成します。

        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### ストレージ プロファイル

1. **$storageProfile** の値を、ストレージ プロファイルに使用する名前に置き換えた後、変数を作成します。

        $storageProfile = "storage profile name"
        
2. 使用するイメージを定義する変数を作成します。
      
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
        
    使用する他のイメージについて詳しくは、[Windows PowerShell と Azure CLI による Azure 仮想マシン イメージのナビゲーションと選択](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md)に関するページをご覧ください。
        
3. **$vhdContainers** の値を、仮想ハード ディスクが格納されているパス (https://mystorage.blob.core.windows.net/vhds など) を含むリストに置き換えた後、変数を作成します。
       
        $vhdContainers = @("https://myst1.blob.core.windows.net/vhds","https://myst2.blob.core.windows.net/vhds","https://myst3.blob.core.windows.net/vhds")
        
4. ストレージ プロファイルを作成します。

        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storageProfile -VhdContainer $vhdContainers -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### 仮想マシン スケール セット

ここでようやく、スケール セットを作成できます。

1. **$vmssName** の値を、仮想マシン スケール セットの名前に置き換えた後、変数を作成します。

        $vmssName = "scale set name"
        
2. スケール セットを作成します。

        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss

    次のように表示され、正常にデプロイされたことがわかります。

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     : Updating
        OverProvision         :
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microso
                               ft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags                  :

## 手順 3: リソースを調査する

次のリソースを使って、作成した仮想マシン スケール セットを調査します。

- Azure ポータル - ポータルを使用して入手できる情報の量は限られています。
- [Azure リソース エクスプローラー](https://resources.azure.com/) - スケール セットの現在の状態を調査するうえで最適なツールです。
- Azure PowerShell - 次のコマンドを使用して情報を得ることができます。

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        
        Or 
        
        Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        

## 次のステップ

- 「[仮想マシン スケール セットで仮想マシンを管理する](virtual-machine-scale-sets-windows-manage.md)」の情報を参照して、作成したスケール セットを管理します。
- 「[自動スケールと仮想マシン スケール セット](virtual-machine-scale-sets-autoscale-overview.md)」の情報を使用して、スケール セットの自動スケールを設定することを検討します。
- 「[仮想マシン スケール セットを使用した垂直方向の自動スケール](virtual-machine-scale-sets-vertical-scale-reprovision.md)」で垂直スケーリングの詳細を確認します。

<!---HONumber=AcomDC_0928_2016-->