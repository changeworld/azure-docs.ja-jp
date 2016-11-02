<properties
    pageTitle="cloud-init を利用し、作成時に Linux VM をカスタマイズする | Microsoft Azure"
    description="cloud-init を利用し、作成時に Linux VM をカスタマイズする"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="v-livech"
/>

# cloud-init を利用し、作成時に Linux VM をカスタマイズする

この記事では、ホスト名の設定、インストールされているパッケージの更新、およびユーザー アカウントの管理を行う cloud-init スクリプトを作成する方法について説明します。cloud-init スクリプトは、VM の作成時に Azure CLI から呼び出します。

## 前提条件

前提条件としては、[Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)および [SSH の公開キーと秘密キー](virtual-machines-linux-mac-create-ssh-keys.md)があり、`azure config mode arm`を使用して [Azure CLI](../xplat-cli-install.md) を Azure Resource Manager モードに切り替えてあることです。

## クイック コマンド

ホスト名の設定、すべてのパッケージの更新、および Linux への sudo ユーザーの追加を実行する cloud-init.txt スクリプトを作成します。

```bash
#cloud-config
hostname: exampleServerName
apt_upgrade: true
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==exampleuser@slackwarelaptop
```

cloud-init を使用して Linux VM を作成し、起動時にこの VM を構成します。

```bash
azure group create cloudinitexample westus
```

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud-init.txt

```

## はじめに

新しい Linux VM の起動時には、何もカスタマイズされていない (ニーズに合わせてカスタマイズ可能な) 標準 Linux VM が起動されます。[cloud-init](https://cloudinit.readthedocs.org) は、初回起動時にこの Linux VM にスクリプトまたは構成設定を挿入する一般的な方法です。

Azure では、3 つの方法でデプロイ時または起動時に Linux VM を変更できます。

- cloud-init を使用してスクリプトを挿入する。
- Azure [VMAccess 拡張機能](virtual-machines-linux-using-vmaccess-extension.md)を使用してスクリプトを挿入する。
- cloud-init を使用した Azure テンプレート。
- [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md) を使用した Azure テンプレート。

次の方法で起動後いつでもスクリプトを挿入できます。

- SSH でコマンドを直接実行する
- Azure [VMAccess 拡張機能](virtual-machines-linux-using-vmaccess-extension.md)を使用して、強制的に、または Azure テンプレートにスクリプトを挿入する
- Ansible、Salt、Chef、Puppet などの構成管理ツール

>[AZURE.NOTE]VMAccess 拡張機能は、SSH と同じ方法でスクリプトをルートとして実行します。ただし、VM 拡張機能を使用すると、シナリオに応じて使用可能な Azure 提供の各種機能を利用できます。

### Azure VM の各簡易作成イメージ エイリアスで cloud-init を使用できるかどうかを次に示します。

| エイリアス | 発行元 | プラン | SKU | バージョン | cloud-init |
|:----------|:----------|:-------------|:------------|:--------|:-----------|
| CentOS | OpenLogic | Centos | 7\.2 | 最新 | × |
| CoreOS | CoreOS | CoreOS | 安定版 | 最新 | ○ |
| Debian | credativ | Debian | 8 | 最新 | × |
| openSUSE | SUSE | openSUSE | 13\.2 | 最新 | × |
| RHEL | Redhat | RHEL | 7\.2 | 最新 | × |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | 最新 | ○ |

Microsoft ではパートナーと協力して、パートナーから Azure に提供されたイメージに cloud-init を含めて、使用できるようにしています。


## 詳細なチュートリアル

### Azure CLI を使用し、VM の作成に cloud-init スクリプトを追加する

Azure で VM を作成するときに cloud-init スクリプトを起動するには、Azure CLI の `--custom-data` スイッチを使用して cloud-init ファイルを指定します。

```bash
azure group create cloudinitexample westus
```

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud-init.txt

```

### Linux VM のホスト名を設定する cloud-init スクリプトを作成する

Linux VM の最も単純で最も重要な設定にホスト名があります。このスクリプトで cloud-init を使用し、ホスト名を簡単に設定できます。

#### `cloud_config_hostname.txt` という名前の cloud-init スクリプトの例

``` bash
#cloud-config
hostname: exampleServerName
```

VM の初回起動時に、この cloud-init スクリプトによってホスト名が `exampleServerName` に設定されます。

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud_config_hostname.txt

```

ログインし、新しい VM のホスト名を確認します。

```bash
ssh exampleVM
hostname
exampleServerName
```

### Linux を更新する cloud-init スクリプトを作成する

セキュリティ上の理由から、最初の起動時に Ubuntu VM を更新する必要があるとします。Linux ディストリビューションによっては、cloud-init を利用し、次のスクリプトで更新できます。

#### Debian 製品用の cloud-init スクリプト `cloud_config_apt_upgrade.txt` の例

```bash
#cloud-config
apt_upgrade: true
```

Linux の起動後、`apt-get` によりすべてのインストール済みパッケージが更新されます。

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud_config_apt_upgrade.txt
```

ログインし、すべてのパッケージが更新されていることを確認します。

```bash
ssh exampleVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

### ユーザーを Linux に追加する cloud-init スクリプトを作成する

新しい Linux VM での最初のタスクの 1 つとして、自分用に、または `root` の使用を避けるためにユーザーを追加することがあります。セキュリティおよび使いやすさの面では SSH キーを使用するのが最も一般的であるため、次の cloud-init スクリプトを使用してこのキーを `~/.ssh/authorized_keys`

#### Debian 製品用の cloud-init スクリプト `cloud_config_add_users.txt` の例

```bash
#cloud-config
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==exampleuser@slackwarelaptop
```

Linux の起動後、リストに含まれるすべてのユーザーが作成され、sudo グループに追加されます。

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud_config_add_users.txt
```

ログインし、新しく作成したユーザーを確認します。

```bash
cat /etc/group
```

出力

```bash
root:x:0:
<snip />
sudo:x:27:exampleUser
<snip />
exampleUser:x:1000:
```

## 次のステップ

cloud-init は、起動時に Linux VM を変更する標準的な方法の 1 つになっています。Azure では、Linux VM を起動時または実行中に変更できる VM 拡張機能も使用可能です。たとえば、Azure VMAccess 拡張機能を使用すると、VM の実行中に SSH またはユーザー情報をリセットすることができます。cloud-init を使用する場合、パスワードをリセットするには再起動の必要があります。

[仮想マシンの拡張機能とその機能について](virtual-machines-linux-extensions-features.md)

[VMAccess 拡張機能を使用して、Azure Linux VM 上のユーザー、SSH を管理し、ディスクをチェックまたは修復する](virtual-machines-linux-using-vmaccess-extension.md)

<!---HONumber=AcomDC_0831_2016-->