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
   ms.date="05/03/2016"
   ms.author="v-livech"/>


# CLI を使用した Azure での Linux VM の作成

この記事では、Azure CLI の `azure vm quick-create` コマンドを使用して、Azure に Linux 仮想マシンをすばやくデプロイする方法を説明します。`quick-create` コマンドを使用すると、VM が基本的なインフラストラクチャと共にデプロイされます。この VM を使用して、概念のプロトタイピングやテストを短時間で行うことができます (これは、Linux bash シェルを使う最も簡単な方法です)。この記事では、Azure アカウント ([無料試用版の入手](https://azure.microsoft.com/pricing/free-trial/)) と、[Azure CLI](../xplat-cli-install.md) へのログイン (`azure login`) および Resource Manager モードの有効化(`azure config mode arm`) が必要です。[Azure ポータル](virtual-machines-linux-quick-create-portal.md)を使用して Linux VM を短時間でデプロイすることもできます。

## クイック コマンドの概要

CoreOS VM をデプロイして SSH キーを関連付けるコマンドの例です。

```bash
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
```

## Linux VM のデプロイ

以下では、上記のコマンドを使用して、各プロンプトと予想される出力を示します。ただし、RedHat Enteprise Linux 7.2 VM の作成には RHEL のエイリアスを使用しています。

## ImageURN エイリアスの使用

Azure CLI `quick-create` コマンドには、最も一般的な OS ディストリビューションにマップされたエイリアスがあります。次の表に、そのディストリビューション エイリアスを示します (Azure CLI バージョン 0.10 の時点)。`quick-create` を使用するデプロイは、いずれも SSD ストレージを利用する VM となり、高いパフォーマンスを実現します。

| エイリアス | 発行元 | プラン | SKU | バージョン |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS | OpenLogic | Centos | 7\.2 | 最新 |
| CoreOS | CoreOS | CoreOS | 安定版 | 最新 |
| Debian | credativ | Debian | 8 | 最新 |
| openSUSE | SUSE | openSUSE | 13\.2 | 最新 |
| RHEL | Redhat | RHEL | 7\.2 | 最新 |
| SLES | SLES | SLES | 12-SP1 | 最新 |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | 最新 |



**ImageURN** オプション (`-Q`) には `RHEL` を使用して RedHat Enterprise Linux 7.2 VM をデプロイします(これら 7 つのオプションは、Azure で利用できる OS のごく一部です。マーケットプレースで他の[イメージを探す](virtual-machines-linux-cli-ps-findimage.md)ことも、[独自のカスタム イメージをアップロード](virtual-machines-linux-create-upload-generic.md)することもできます)。

以下のコマンド チュートリアルでは、プロンプトを実際の環境の値に置き換えてください。ここでは、"サンプル" の値を使用します。

画面の指示に従って、独自の名前を入力してください。

```bash
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q RHEL
```

出力は次のような出力ブロックになります。

```bash
info:    Executing command vm quick-create
Resource group name: rhel-quick
Virtual machine name: rhel
Location name: westus
Operating system Type [Windows, Linux]: linux
User name: ops
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "rhel"
info:    Verifying the public key SSH file: /Users/ops/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli1630678171193501687
info:    Could not find the storage account "cli1630678171193501687", trying to create new one
+ Creating storage account "cli1630678171193501687" in "westus"
+ Looking up the storage account cli1630678171193501687
+ Looking up the NIC "rhel-westu-1630678171-nic"
info:    An nic with given name "rhel-westu-1630678171-nic" not found, creating a new one
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
info:    Preparing to create new virtual network and subnet
+ Creating a new virtual network "rhel-westu-1630678171-vnet" [address prefix: "10.0.0.0/16"] with subnet "rhel-westu-1630678171-snet" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
+ Looking up the subnet "rhel-westu-1630678171-snet" under the virtual network "rhel-westu-1630678171-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "rhel-westu-1630678171-pip"
info:    PublicIP with given name "rhel-westu-1630678171-pip" not found, creating a new one
+ Creating public ip "rhel-westu-1630678171-pip"
+ Looking up the public ip "rhel-westu-1630678171-pip"
+ Creating NIC "rhel-westu-1630678171-nic"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the storage account clisto909893658rhel
+ Creating VM "rhel"
+ Looking up the VM "rhel"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the public ip "rhel-westu-1630678171-pip"
data:    Id                              :/subscriptions/<guid>/resourceGroups/rhel-quick/providers/Microsoft.Compute/virtualMachines/rhel
data:    ProvisioningState               :Succeeded
data:    Name                            :rhel
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :RedHat
data:        Offer                       :RHEL
data:        Sku                         :7.2
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic5abbc145c0242c1-os-1462425492101
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli1630678171193501687.blob.core.windows.net/vhds/clic5abbc145c0242c1-os-1462425492101.vhd
data:
data:    OS Profile:
data:      Computer Name                 :rhel
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-32-0F-DD
data:          Provisioning State        :Succeeded
data:          Name                      :rhel-westu-1630678171-nic
data:          Location                  :westus
data:            Public IP address       :104.42.236.196
data:            FQDN                    :rhel-westu-1630678171-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto909893658rhel.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

既定の SSH ポート 22 と、上記の出力に示されている完全修飾ドメイン名 (FQDN) を使用して、VM に SSH 接続できます(列挙されている IP アドレスを使用することもできます)。

```bash
ssh ops@rhel-westu-1630678171-pip.westus.cloudapp.azure.com
```
ログイン プロセスは次のようになります。

```bash
The authenticity of host 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com (104.42.236.196)' can't be established.
RSA key fingerprint is 0e:81:c4:36:2d:eb:3c:5a:dc:7e:65:8a:3f:3e:b0:cb.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com,104.42.236.196' (RSA) to the list of known hosts.
[ops@rhel ~]$ ls -a
.  ..  .bash_logout  .bash_profile  .bashrc  .cache  .config  .ssh
```

## 次のステップ

`azure vm quick-create` を使用すると、VM を短時間でデプロイして、bash シェルにログインして作業できます。`vm quick-create` を使用しても、複雑な環境が備えているようなメリットは得られません。インフラストラクチャに合わせてカスタマイズした Linux VM をデプロイする方法については、次の記事を参照してください。

- [Azure Resource Manager テンプレートを使用して特定のデプロイを作成する](virtual-machines-linux-cli-deploy-templates.md)
- [Azure CLI コマンドを直接使用して Linux VM 用の独自のカスタム環境を作成する](virtual-machines-linux-cli-deploy-templates.md)
- [Azure テンプレートを使用して安全な Linux VM を作成する](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Azure CLI を使用して新しく Linux VM を作成する](virtual-machines-linux-create-cli-complete.md)

これらの記事を読むと、Azure インフラストラクチャの構築を始めることができ、独自またはオープン ソースのインフラストラクチャ デプロイ、構成、オーケストレーション ツールを使用できます。

<!---HONumber=AcomDC_0511_2016-->