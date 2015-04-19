<properties
   pageTitle="manage-vms-azure-powershell"
   description="Azure PowerShell を使用した仮想マシンの管理"
   services="virtual-machines"
   documentationCenter="windows"
   authors="singhkay"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="02/20/2015"
   ms.author="kasing"/>

# Azure PowerShell を使用した仮想マシンの管理

開始する前に、Azure PowerShell がインストールされていることをご確認ください。インストールするには、[「Azure PowerShell のインストールと構成方法」をご覧ください。](install-configure-powershell.md)

## イメージの取得

仮想マシンを作成する前に、**どのイメージを使用するか**決める必要があります。次のコマンドレットを使ってイメージの一覧を取得できます。

      Get-AzureVMImage

このコマンドレットは、Azure で使用できるすべてのイメージの一覧を返します。とても多くのイメージが一覧化されていますので、使用するイメージを見つけるのが大変かもしれません。下記の例では、別の PowerShell コマンドレットを使って、返されるイメージの一覧を **Windows Server 2012 R2 Datacenter** に基づいたものが含まれるイメージのみに絞り込んでいます。さらに、返されるイメージの配列に [-1] を指定することで最新のイメージのみを選択しています。

    $img = (Get-AzureVMImage | Select -Property ImageName, Label | where {$_.Label -like '*Windows Server 2012 R2 Datacenter*'})[-1]

## 仮想マシンの作成

**New-AzureVMConfig** コマンドレットで始まる仮想マシンの作成ここで仮想マシンの **名前**、**サイズ**、その仮想マシンで使用される **イメージ**を指定します。このコマンドレットではローカル仮想マシンオブジェクトである **$myVM** が作成されます。これはこのガイドの他の Azure PowerShell コマンドレットを使って後で変更されます。

      $myVM = New-AzureVMConfig -Name "testvm" -InstanceSize "Small" -ImageName $img.ImageName

次に仮想マシンの**ユーザー名**と**パスワード**を選択する必要があります。**Add-AzureProvisioningConfig** コマンドレットを使って選択できます。こちらで仮想マシンの OS を指定できます。ここではまだローカル **$myVM** オブジェクトに対して変更を加えているということにご注意ください。

    $user = "azureuser"
    $pass = "&Azure1^Pass@"
    $myVM = Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass

これで Azure で仮想マシンを使用する準備ができました。**New-AzureVM** コマンドレットを使用して使用を開始します。

> [AZURE.NOTE] 仮想マシンを作成する前に、クラウド サービスを構成する必要があります。2 つの方法があります。
* New-AzureService コマンドレットを使ってクラウド サービスを作成します。この方法を選択した場合、下記 New-AzureVM コマンドレットで指定した場所がクラウド サービスの場所と合致する必要があります。合致しない場合、New-AzureVM コマンドレットの実行は失敗します。
* New-AzureVM コマンドレットを使用して行います。サービスの名前は固有である必要があります。そうでない場合、New-AzureVM コマンドレットの実行は失敗します。

    New-AzureVM -ServiceName "mytestserv" -VMs $myVM -Location "West US"

**オプション**

**Add-AzureDataDisk** や **Add-AzureEndpoint** などの他のコマンドレットを使用して仮想マシンのオプションを追加構成できます。

## 仮想マシンの取得
Azure で仮想マシンを作成した後は、その動作を確認します。下記のとおり、**Get-AzureVM** コマンドレットを使って確認できます。

    Get-AzureVM -ServiceName "mytestserv" -Name "testvm"


## 次のステップ
[RDP または SSH を使用した Azure 仮想マシンへの接続](https://msdn.microsoft.com/library/azure/dn535788.aspx)<br>
[Azure Virtual Machines FAQ (Azure 仮想マシンの FAQ)](https://msdn.microsoft.com/library/azure/dn683781.aspx)

<!--HONumber=47-->
