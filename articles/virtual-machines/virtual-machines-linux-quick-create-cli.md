<properties
   pageTitle="CLI を使用した Azure での Linux VM の作成 | Microsoft Azure"
   description="CLI を使用して Azure で Linux VM を作成します。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="04/04/2016"
   ms.author="v-livech"/>


# CLI を使用した Azure での Linux VM の作成

この記事では、Azure CLI の `azure vm quick-create` コマンドを使って Azure で Linux VM を簡単に作成する方法について説明します。このコマンドにより、概念のプロトタイプ作成またはテストを非常に短時間で実行するための基本的なインフラストラクチャを使用した VM が作成されます。この記事では、Azure アカウント ([無料試用版の入手](https://azure.microsoft.com/pricing/free-trial/)) と、Resource Manager モードの [Azure CLI](../xplat-cli-install.md) (`azure config mode arm`) が必要です。

## クイック コマンドの概要

```
# One command to quickly the VM that prompts for arguments
chrisL@fedora$ azure vm quick-create
```

## 詳しいチュートリアル

### Linux VM の作成

次のコマンドでは任意のイメージを使用できますが、この例では、`canonical:ubuntuserver:14.04.2-LTS:latest` を使用して VM をすばやく作成します (Marketplace でイメージを探すには、[イメージを検索](virtual-machines-linux-cli-ps-findimage.md)するか、[独自のカスタム イメージをアップロード](virtual-machines-linux-create-upload-generic.md)することもできます)。 次のようになります。

以下のコマンド例の &lt; と &gt; で囲まれた値は、実際の環境の値に置き換えてください。

```bash
# Create the Linux VM using prompts
username@macbook$ azure vm quick-create
info:    Executing command vm quick-create
Resource group name: exampleResourceGroup
Virtual machine name: exampleVMname
Location name: westus
Operating system Type [Windows, Linux]: linux
ImageURN (in the format of "publisherName:offer:skus:version") or a VHD link to the user image: Canonical:UbuntuServer:14.04.4-LTS:latest
User name: ops
Password: *********
Confirm password: *********
+ Looking up the VM "exampleVMname"
info:    Using the VM Size "Standard_D1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli133501687
info:    Could not find the storage account "cli1301687", trying to create new one
+ Creating storage account "cli133501687" in "westus"
+ Looking up the storage account cli133501687
+ Looking up the NIC "quick-westu-1363648838-nic"
info:    An nic with given name "quick-westu-1363648838-nic" not found, creating a new one
+ Looking up the virtual network "quick-westu-1363648838-vnet"
info:    Preparing to create new virtual network and subnet
\ Creating a new virtual network "quick-westu-1363648838-vnet" [address prefix: "10.0.0.0/16"] with subnet "quick-westu-13636488+8-snet" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "quick-westu-1363648838-vnet"
+ Looking up the subnet "quick-westu-1363648838-snet" under the virtual network "quick-westu-1363648838-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "quick-westu-1363648838-pip"
info:    PublicIP with given name "quick-westu-1363648838-pip" not found, creating a new one
+ Creating public ip "quick-westu-1363648838-pip"
+ Looking up the public ip "quick-westu-1363648838-pip"
+ Creating NIC "quick-westu-1363648838-nic"
+ Looking up the NIC "quick-westu-1363648838-nic"
+ Creating VM "quickcreate"
+ Looking up the VM "exampleVMname"
+ Looking up the NIC "quick-westu-1363648838-nic"
+ Looking up the public ip "quick-westu-1363648838-pip"
data:    Id                              :/subscriptions/<guid>/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMname
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMname
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_D1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :ubuntuserver
data:        Sku                         :14.04.2-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli350d386daac1f01c-os-1457063387485
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli1361687.blob.core.windows.net/vhds/cli350d386daac1f01c-os-1457063387485.vhd
data:
data:    OS Profile:
data:      Computer Name                 :exampleVMname
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-32-E9-66
data:          Provisioning State        :Succeeded
data:          Name                      :quick-westu-1363648838-nic
data:          Location                  :westus
data:            Public IP address       :137.135.33.58
data:            FQDN                    :quick-westu-1363648838-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://cli13601687.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

既定の SSH ポート 22 で VM に SSH 接続できます。

`azure vm quick-create` を使用すると、VM がすぐに作成されるため、ログインして作業できます。ただし、複雑な環境はないので、環境をカスタマイズする必要がある場合は、[Azure Resource Manager テンプレートを使用して特定のデプロイをすばやく作成する](virtual-machines-linux-cli-deploy-templates.md)か、[Azure CLI コマンドを直接使用して Linux VM 用の独自のカスタム環境を作成する](virtual-machines-linux-cli-deploy-templates.md)ことができます。

上の例では次のものが作成されます。

- Azure リソース グループにより VM が以下にデプロイされます。
- VM イメージの .vhd ファイルを保持するための Azure ストレージ アカウント
- VM に接続するための Azure Virtual Network とサブネット
- VM とネットワークを関連付けるための仮想ネットワーク インターフェイス カード (NIC)
- 外部使用のためのインターネット アドレスを提供するパブリック IP アドレスとサブドメイン プレフィックス。その後、その環境内に Linux VM が作成されます。

この VM はインターネットに直接公開され、ユーザー名とパスワードによってのみ保護されます。

## 次のステップ

テストまたはデモンストレーション用の Linux VM を迅速に作成しました。Azure の Linux VM でさらに安全な実行環境を作成するには、次の方法を使用できます。

- [Azure テンプレートを使用して Azure に Linux VM を作成します](virtual-machines-linux-cli-deploy-templates.md)
- [Azure テンプレートを使用して Azure に SSH で保護された Linux VM を作成します](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Azure CLI を使用し、インフラストラクチャをカスタマイズして、Azure に Linux VM を作成します](virtual-machines-linux-create-cli-complete.md)

また、独自またはオープン ソースのインフラストラクチャ デプロイ、構成、オーケストレーション ツールを使用できます。

<!---HONumber=AcomDC_0406_2016-->