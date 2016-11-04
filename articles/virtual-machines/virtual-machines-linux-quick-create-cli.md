---
title: CLI を使用した Azure での Linux VM の作成 | Microsoft Docs
description: CLI を使用して Azure で Linux VM を作成します。
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: timlt
editor: ''

ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/08/2016
ms.author: v-livech

---
# CLI を使用した Azure での Linux VM の作成
この記事では、Azure コマンド ライン インターフェイス (CLI) で `azure vm quick-create` コマンドを使用して、Azure に Linux 仮想マシン (VM) をすばやくデプロイする方法を説明します。`quick-create` コマンドは、プロトタイプの作成や概念のテストを迅速に行うことができる、セキュリティで保護された基本的なインフラストラクチャ内に VM をデプロイします。この記事では以下が必要です。

* Azure アカウント ([無料試用版を入手](https://azure.microsoft.com/pricing/free-trial/))。
* `azure login` を使用した [Azure CLI](../xplat-cli-install.md) へのログイン。
* Azure CLI は、Azure Resource Manager モード (`azure config mode arm`) である_必要があります_。

[Azure ポータル](virtual-machines-linux-quick-create-portal.md)を使用して、Linux VM をすばやくデプロイすることもできます。

## クイック コマンド
次の例は、CoreOS VM をデプロイし、Secure Shell (SSH) キーを添付する方法を示しています (引数は異なる場合があります)。

```bash
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
```

以下のセクションでは、このコマンドと、Linux ディストリビューションとして Ubuntu Server 14.04 LTS を使用する際の要件について説明します。

## VM の quick-create エイリアス
ディストリビューションを簡単に選択するには、最も一般的な OS ディストリビューションにマップされた Azure CLI のエイリアスを使用します。次の表にエイリアスを示します (Azure CLI バージョン 0.10 の時点)。`quick-create` を使用したすべてのデプロイの既定の VM では、プロビジョニングの高速化と高パフォーマンスのディスク アクセスを実現するソリッドステート ドライブ (SSD) ストレージを利用します (これらのエイリアスは、Azure で利用可能なディストリビューションのごく一部を表しています。Azure Marketplace で[イメージを検索](virtual-machines-linux-cli-ps-findimage.md)してさらに多くのイメージを見つけることも、[独自のカスタム イメージをアップロード](virtual-machines-linux-create-upload-generic.md)することもできます)。

| エイリアス | 発行元 | プラン | SKU | バージョン |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |CentOS |7\.2 |最新 |
| CoreOS |CoreOS |CoreOS |安定版 |最新 |
| Debian |credativ |Debian |8 |最新 |
| openSUSE |SUSE |openSUSE |13\.2 |最新 |
| RHEL |Red Hat |RHEL |7\.2 |最新 |
| UbuntuLTS |Canonical |Ubuntu Server |14\.04.4-LTS |最新 |

以下のセクションでは、**ImageURN** オプション (`-Q`) に `UbuntuLTS` エイリアスを使用して、Ubuntu 14.04.4 LTS Server をデプロイします。

## 詳細なチュートリアル
前の `quick-create` の例では、アップロードする SSH 公開キーを識別する `-M` フラグだけを指定し、SSH パスワードを無効にしているので、以下の引数を入力するよう求められます。

* リソース グループ名 (最初の Azure リソース グループでは、任意の文字列で通常は問題ありません)。
* VM 名
* 場所 (`westus`、`westeurope` など)
* linux (必要な OS を Azure に通知します)。
* username

次の例では、さらに入力を要求しなくて済むように、すべての値を指定しています。`~/.ssh/id_rsa.pub` が ssh-rsa 形式の公開キー ファイルであれば、これは現状のまま機能します。

```bash
azure vm quick-create \
-g exampleResourceGroup \
-n exampleVMName \
-l westus \
-y Linux \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub \
-Q UbuntuLTS
```

出力は次のような出力ブロックになります。

```bash
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "exampleVMName"
info:    Verifying the public key SSH file: /Users/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli16330708391032639673
+ Looking up the NIC "examp-westu-1633070839-nic"
info:    An nic with given name "examp-westu-1633070839-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-1633070839-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "examp-westu-1633070839-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-1633070839-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-1633070839-vnet"
+ Looking up the subnet "examp-westu-1633070839-snet" under the virtual network "examp-westu-1633070839-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-1633070839-pip"
info:    PublicIP with given name "examp-westu-1633070839-pip" not found, creating a new one
+ Creating public ip "examp-westu-1633070839-pip"
+ Looking up the public ip "examp-westu-1633070839-pip"
+ Creating NIC "examp-westu-1633070839-nic"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the storage account clisto1710997031examplev
+ Creating VM "exampleVMName"
+ Looking up the VM "exampleVMName"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the public ip "examp-westu-1633070839-pip"
data:    Id                              :/subscriptions/2<--snip-->d/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMName
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMName
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
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
data:        Name                        :clic7fadb847357e9cf-os-1473374894359
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli16330708391032639673.blob.core.windows.net/vhds/clic7fadb847357e9cf-os-1473374894359.vhd
data:
data:    OS Profile:
data:      Computer Name                 :exampleVMName
data:      User Name                     :exampleAdminUser
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-42-FB
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-1633070839-nic
data:          Location                  :westus
data:            Public IP address       :138.91.247.29
data:            FQDN                    :examp-westu-1633070839-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto1710997031examplev.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

この出力に示されているパブリック IP アドレスを使用して、VM にログインします。ここで示されている完全修飾ドメイン名 (FQDN) を使用することもできます。

```bash
ssh -i ~/.ssh/id_rsa.pub exampleAdminUser@138.91.247.29
```

ログイン プロセスは次の出力ブロックのようになります。

```bash
Warning: Permanently added '138.91.247.29' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-65-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Sep  8 22:50:57 UTC 2016

  System load: 0.63              Memory usage: 2%   Processes:       81
  Usage of /:  39.6% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

exampleAdminUser@exampleVMName:~$
```

## 次のステップ
`azure vm quick-create` を使用すると、bash シェルにログインして作業できるように、VM をすばやくデプロイできます。ただし、`vm quick-create` を使用した場合、広範な制御を行ったり、より複雑な環境を作成したりすることはできません。インフラストラクチャに合わせてカスタマイズした Linux VM をデプロイする方法については、次の記事を参照してください。

* [Azure Resource Manager テンプレートを使用して特定のデプロイを作成する](virtual-machines-linux-cli-deploy-templates.md)
* [Azure CLI コマンドを直接使用して Linux VM 用の独自のカスタム環境を作成する](virtual-machines-linux-create-cli-complete.md)
* [テンプレートを使用して、SSH で保護された Linux VM を Azure で作成する](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

[さまざまなコマンドで `docker-machine` Azure ドライバーを使用すると、Linux VM を Docker ホストとしてもすばやく作成](virtual-machines-linux-docker-machine.md)できます。

<!---HONumber=AcomDC_1005_2016-->