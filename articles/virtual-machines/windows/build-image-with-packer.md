---
title: Packer を使用して Windows Azure VM のイメージを作成する方法 | Microsoft Docs
description: Packer を使用して Azure に Windows 仮想マシンのイメージを作成する方法について説明します。
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/29/2018
ms.author: cynthn
ms.openlocfilehash: 5f19a6cb356332e95f96484953f1be3df006dd09
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931924"
---
# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Packer を使用して Azure に Windows 仮想マシンのイメージを作成する方法
Azure の各仮想マシン (VM) は、Windows ディストリビューションと OS のバージョンを定義するイメージから作成されます。 イメージには、プリインストールされているアプリケーションと構成を含めることができます。 Azure Marketplace には、ほとんどの OS およびアプリケーション環境用の自社製およびサード パーティ製のイメージが数多く用意されています。また、ニーズに合わせて独自のイメージを作成することもできます。 この記事では、オープン ソース ツール [Packer](https://www.packer.io/) を使用して Azure に独自のイメージを定義およびビルドする方法について、詳しく説明します。


## <a name="create-azure-resource-group"></a>Azure リソース グループを作成する
ビルド プロセス中、Packer はソース VM をビルドする際に一時的な Azure リソースを作成します。 イメージとして使用するためにそのソース VM をキャプチャするには、リソース グループを定義する必要があります。 Packer のビルド プロセスからの出力は、このリソース グループに格納されます。

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用してリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```powershell
$rgName = "myResourceGroup"
$location = "East US"
New-AzureRmResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Azure 資格情報の作成
Packer はサービス プリンシパルを使用して Azure で認証されます。 Azure のサービス プリンシパルは、アプリケーション、サービス、および Packer などのオートメーション ツールで使用できるセキュリティ ID です。 Azure でサービス プリンシパルが実行できる操作を設定するアクセス許可の制御と定義を行います。

[New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) を使用してサービス プリンシパルを作成し、そのサービス プリンシパルにアクセス許可を割り当て、[New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) を使用してリソースを作成および管理します。

```powershell
$sp = New-AzureRmADServicePrincipal -DisplayName "AzurePacker" `
    -Password (ConvertTo-SecureString "P@ssw0rd!" -AsPlainText -Force)
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Azure に対して認証するには、[Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) を使用して Azure のテナントとサブスクリプション ID も取得する必要があります。

```powershell
$sub = Get-AzureRmSubscription
$sub.TenantId[0]
$sub.SubscriptionId[0]
```

次の手順でこれら 2 つの ID を使用します。


## <a name="define-packer-template"></a>Packer テンプレートを定義する
イメージをビルドするには、テンプレートを JSON ファイルとして作成します。 テンプレートでは、実際のビルド プロセスを実行するビルダーとプロビジョナーを定義します。 Packer には [Azure 用のビルダー](https://www.packer.io/docs/builders/azure.html)が用意されており、前の手順で作成したサービス プリンシパルの資格情報などの Azure リソースを定義できます。

*windows.json* というファイルを作成し、次のコンテンツを貼り付けます。 次に対応する実際の値を入力します。

| パラメーター                           | 入手場所 |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | `$sp.applicationId` でサービス プリンシパル ID を表示します |
| *client_secret*                     | `$securePassword` で指定したパスワード |
| *tenant_id*                         | `$sub.TenantId` コマンドからの出力 |
| *subscription_id*                   | `$sub.SubscriptionId` コマンドからの出力 |
| *object_id*                         | `$sp.Id` でサービス プリンシパル オブジェクト ID を表示します |
| *managed_image_resource_group_name* | 最初の手順で作成したリソース グループの名前 |
| *managed_image_name*                | 作成される管理ディスク イメージの名前 |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "0831b578-8ab6-40b9-a581-9a880a94aab1",
    "client_secret": "P@ssw0rd!",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "object_id": "a7dfb070-0d5b-47ac-b9a5-cf214fff0ae2",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "3m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

このテンプレートは、Windows Server 2016 VM を作成し、IIS をインストールした後、Sysprep で VM を一般化します。 IIS のインストールでは、PowerShell プロビジョナーを使用して追加のコマンドを実行する方法が示されます。 最終的な Packer イメージには、必要なソフトウェアのインストールと構成が含まれます。


## <a name="build-packer-image"></a>Packer イメージをビルドする
まだローカル コンピューターに Packer がインストールされていない場合は、[手順に従って Packer をインストールしてください](https://www.packer.io/docs/install/index.html)。

次のように Packer テンプレート ファイルを指定してイメージをビルドします。

```bash
./packer build windows.json
```

上記のコマンドの出力例は、次のとおりです。

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Packer が VM をビルド、プロビジョナーを実行、およびデプロイメントをクリーンアップするのに数分かかります。


## <a name="create-a-vm-from-the-packer-image"></a>Packer イメージから VM を作成する
[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使用して、イメージから VM を作成できるようになりました。 これらが存在しない場合は、サポート ネットワーク リソースが作成されます。 入力を求められたら、VM で作成された管理用のユーザー名とパスワードを入力します。 次の例では、*myPackerImage* から *myVM* という名前の VM を作成します。

```powershell
New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

自分の Packer イメージとは異なるリソース グループまたはリージョンで VM を作成する場合は、イメージ名ではなく、イメージ ID を指定します。 [Get-AzureRmImage](/powershell/module/AzureRM.Compute/Get-AzureRmImage) を使用してイメージ ID を取得できます。

Packer イメージから VM を作成するには数分かかります。


## <a name="test-vm-and-webserver"></a>VM と Web サーバーのテスト
VM のパブリック IP アドレスを取得するには、[Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使用します。 次の例では、先ほど作成した *myPublicIP* の IP アドレスを取得しています。

```powershell
Get-AzureRmPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Packer プロビジョナーからの IIS インストールを含む VM が動いていることを確認するには、Web ブラウザーにでパブリック IP アドレスを入力します。

![IIS の既定のサイト](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>次の手順
この例では、Packer を使用して IIS が既にインストールされた VM イメージを作成しました。 この VM イメージは、アプリを、Team Services、Ansible、Chef、Puppet でイメージから作成した VM にデプロイするなど、既存のデプロイ ワークフローとともに使用できます。

他の Windows ディストリビューション用の追加の Packer テンプレートの例については、[この GitHub リポジトリ](https://github.com/hashicorp/packer/tree/master/examples/azure)をご覧ください。
