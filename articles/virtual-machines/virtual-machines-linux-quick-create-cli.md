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
   ms.date="04/08/2016"
   ms.author="v-livech"/>


# CLI を使用した Azure での Linux VM の作成

この記事では、Azure CLI の `azure vm quick-create` コマンドを使用して、Azure に Linux 仮想マシンをすばやく作成する方法を説明します。`quick-create` コマンドは、短い時間で概念のプロトタイピングやテストを行うために使用できる基本的なインフラストラクチャで VM を作成します。Linux bash シェルの最も簡単な方法です。この記事では、Azure アカウント ([無料試用版の入手](https://azure.microsoft.com/pricing/free-trial/)) と、Resource Manager モードの [Azure CLI](../xplat-cli-install.md) (`azure config mode arm`) が必要です。

## クイック コマンドの概要

```
# One command to quickly the VM that prompts for arguments
ahmet@fedora$ azure vm quick-create -M ~/.ssh/azure_id_rsa.pub
```

## 詳しいチュートリアル

## Linux VM の作成

次のコマンドでは任意のイメージを使用できますが、この例では、`canonical:ubuntuserver:14.04.2-LTS:latest` を使用して VM をすばやく作成します (Marketplace でイメージを探すには、[イメージを検索](virtual-machines-linux-cli-ps-findimage.md)するか、[独自のカスタム イメージをアップロード](virtual-machines-linux-create-upload-generic.md)することもできます)。 次のようになります。

以下のコマンド チュートリアルでは、プロンプトを実際の環境の値に置き換えてください。この記事で使用する値は "例" です。

```bash
# Create the Linux VM using prompts
ahmet@fedora$ azure vm quick-create -M ~/.ssh/azure_id_rsa.pub
info:    Executing command vm quick-create
Resource group name: exampleRGname
Virtual machine name: exampleVMname
Location name: westus
Operating system Type [Windows, Linux]: Linux
ImageURN (in the format of "publisherName:offer:skus:version") or a VHD link to the user image: Canonical:UbuntuServer:14.04.4-LTS:latest
User name: ahmet
Password: ************************************************
Confirm password: ************************************************
+ Looking up the VM "exampleVMname"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/azure_id_rsa.pub
info:    Using the VM Size "Standard_D1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli38948918364134011018
info:    Could not find the storage account "cli38948918364134011018", trying to create new one
+ Creating storage account "cli38948918364134011018" in "westus"
+ Looking up the storage account cli38948918364134011018
+ Looking up the NIC "examp-westu-3894891836-nic"
info:    An nic with given name "examp-westu-3894891836-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-3894891836-vnet"
info:    Preparing to create new virtual network and subnet
| Creating a new virtual network "examp-westu-3894891836-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-3894891836-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-3894891836-vnet"
+ Looking up the subnet "examp-westu-3894891836-snet" under the virtual network "examp-westu-3894891836-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-3894891836-pip"
info:    PublicIP with given name "examp-westu-3894891836-pip" not found, creating a new one
+ Creating public ip "examp-westu-3894891836-pip"
+ Looking up the public ip "examp-westu-3894891836-pip"
+ Creating NIC "examp-westu-3894891836-nic"
+ Looking up the NIC "examp-westu-3894891836-nic"
+ Creating VM "exampleVMname"
+ Looking up the VM "exampleVMname"
+ Looking up the NIC "examp-westu-3894891836-nic"
+ Looking up the public ip "examp-westu-3894891836-pip"
data:    Id                              :/subscriptions/<**subscriptionsID**>/resourceGroups/exampleRGname/providers/Microsoft.Compute/virtualMachines/exampleVMname
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
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clife36db80ae0539d2-os-1460152163612
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://<**subscriptionsID**>.blob.core.windows.net/vhds/clife36db80ae0539d2-os-1460152163612.vhd
data:
data:    OS Profile:
data:      Computer Name                 :exampleVMname
data:      User Name                     :ahmet
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-4C-B2
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-3894891836-nic
data:          Location                  :westus
data:            Public IP address       :13.88.22.244
data:            FQDN                    :examp-westu-3894891836-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://<**subscriptionsID**>.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

既定の SSH ポート 22 と、上記の出力に示されているパブリック IP アドレスを使用して、VM に SSH 接続できます。

```
ahmet@fedora$ ssh -i ~/.ssh/azure_id_rsa ubuntu@13.88.22.244
```

`azure vm quick-create` は、bash シェルにログインして作業できるように VM をすばやく作成する方法です。ただし、`vm quick-create` を使用すると複雑な環境のメリットはないので、環境をカスタマイズする必要がある場合は、[Azure Resource Manager テンプレートを使用して特定のデプロイをすばやく作成する](virtual-machines-linux-cli-deploy-templates.md)か、[Azure CLI コマンドを直接使用して Linux VM 用の独自のカスタム環境を作成する](virtual-machines-linux-cli-deploy-templates.md)ことができます。

上の例では次のものが作成されます。

- Azure リソース グループにより VM が以下にデプロイされます。
- VM イメージの .vhd ファイルを保持するための Azure ストレージ アカウント
- VM に接続するための Azure Virtual Network とサブネット
- VM とネットワークを関連付けるための仮想ネットワーク インターフェイス カード (NIC)
- 外部使用のためのインターネット アドレスを提供するパブリック IP アドレスとサブドメイン プレフィックス。その後、その環境内に Linux VM が作成されます。

## 次のステップ

テストまたはデモンストレーション用の Linux VM を迅速に作成しました。インフラストラクチャに合わせてカスタマイズした Linux VM を作成する方法については、次の記事を参照してください。

- [テンプレートを使用して Azure に Linux VM を作成する](virtual-machines-linux-cli-deploy-templates.md)
- [Azure テンプレートを使用して安全な Linux VM を作成する](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Azure CLI を使用して新しく Linux VM を作成する](virtual-machines-linux-create-cli-complete.md)

これらの記事を読むと、Azure インフラストラクチャの構築を始めることができ、独自またはオープン ソースのインフラストラクチャ デプロイ、構成、オーケストレーション ツールを使用できます。

<!---HONumber=AcomDC_0420_2016-->