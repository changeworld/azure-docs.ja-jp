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
    ms.date="04/22/2016"
    ms.author="v-livech"
/>

# cloud-init を利用し、作成時に Linux VM をカスタマイズする

この記事では、ホスト名を設定し、インストールされているパッケージを更新し、ユーザー アカウントを管理する cloud-init スクリプトを作成します。それから、[Azure CLI](../xplat-cli-install.md) を利用し、Linux VM の作成時に cloud-init スクリプトを起動します。

## 前提条件

前提条件としては、[Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)、[SSH の公開キーと秘密キー](virtual-machines-linux-mac-create-ssh-keys.md)、(Linux VM を起動する) Azure リソース グループがあり、Azure CLI がインストールされていて、`azure config mode arm` を使用して Azure Resource Manager モードに切り替えてあることです。

## はじめに

新しい Linux VM を起動すると、何もカスタマイズされていない (ニーズに合わせてカスタマイズ可能な) 標準 Linux VM が与えられます。[cloud-init](https://cloudinit.readthedocs.org) は、初めて起動するとき、その (標準) Linux VM にスクリプトまたは構成設定を挿入する一般的な方法です。

Azure では、3 つの方法で起動時の Linux VM を変更できます。

- cloud-init でスクリプトを挿入できます。
- Azure [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md) でスクリプトを挿入できます。
- Azure テンプレートにカスタム設定を指定し、それを利用して Linux VM を起動し、カスタマイズできます。その際、cloud-init や CustomScript VM など、さまざまな方法が利用できます。

次の方法でいつでもスクリプトを挿入できます。

- SSH を利用し、コマンドを直接実行できます。Azure [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md) を強制的に、または Azure テンプレートで実行できます。あるいは、Ansible、Salt、Chef、Puppet などの一般的構成管理ツールを利用できます。VM の起動が完了すると、これらの管理ツールは SSH で動作します。

注: [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md) の場合、SSH と同様に、スクリプトはルートとしてのみ実行されますが、VM 拡張を利用すると、Azure のさまざまな機能が有効になり、シナリオによっては便利です。

## クイック コマンド

```bash
# Create a hostname cloud-init script
#cloud-config
hostname: exampleServerName

# Create an update Linux on first boot cloud-init script for Debian Family
#cloud-config
apt_upgrade: true

# Create an add a user cloud-init script
#cloud-config
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa
AAAAB3NzaC1yc2EAAAADAQABAAABAQDf0q4PyG0doiBQYV7OlOxbRjle<snip />== exampleuser@slackwarelaptop

```

## 詳細なチュートリアル

### Azure CLI を使用し、VM の作成に cloud-init スクリプトを追加する

Azure で VM を作成するとき、cloud-init スクリプトを起動するには、Azure CLI `--custom-data` スイッチを利用し、cloud-init ファイルを指定します。

注: この記事では、cloud-init ファイルに `--custom-data` スイッチを利用する方法について説明しますが、このスイッチを利用し、任意のコードやファイルを渡すこともできます。そのようなファイルの処理方法が Linux VM で既に理解されている場合、ファイルは自動的に実行されます。

```bash
bill@slackware$ azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_init_script.txt
```

### Linux VM のホスト名を設定する cloud-init スクリプトを作成する

Linux VM の最も単純で最も重要な設定にホスト名があります。このスクリプトで cloud-init を使用し、ホスト名を簡単に設定できます。

#### `cloud_config_hostname.txt` という名前の cloud-init スクリプトの例。

``` bash
#cloud-config
hostname: exampleServerName
```

VM の初回起動時、この cloud-init スクリプトが `exampleServerName` にホスト名を設定します。

```bash
bill@slackware$ azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_config_hostname.txt
```

ログインし、新しい VM のホスト名を確認します。

```bash
bill@slackware$ ssh exampleVM
bill@ubuntu$ hostname
bill@ubuntu$ exampleServerName
```

### Linux を更新する cloud-init スクリプトを作成する

セキュリティ上の理由から、最初の起動で Ubuntu VM を更新することがあります。Linux ディストリビューションによっては、cloud-init を利用し、次のスクリプトで更新できます。

#### Debian 製品に cloud-init スクリプト `cloud_config_apt_upgrade.txt` を使用する例

```bash
#cloud-config
apt_upgrade: true
```

新しい Linux VM の起動後に、`apt-get` を介して、インストールされているすべてのパッケージが更新されます。

```bash
bill@slackware$ azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_config_apt_upgrade.txt
```

ログインし、すべてのパッケージが更新されていることを確認します。

```bash
bill@slackware$ ssh exampleVM
bill@ubuntu$ sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

### ユーザーを Linux に追加する cloud-init スクリプトを作成する

新しい Linux VM で行う最初のタスクに、手動でユーザーを追加すること、または `root` の利用を回避することがあります。追加したユーザーの `~/.ssh/authorized_keys` ファイルに SSH 公開キーを追加し、パスワードの要らない安全な SSH ログインを可能にすることは、セキュリティや操作性を大幅に改善します。

#### Debian 製品に cloud-init スクリプト `cloud_config_add_users.txt` を使用する例

```bash
#cloud-config
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa
AAAAB3NzaC1yc2EAAAADAQABAAABAQDf0q4PyG0doiBQYV7OlOxbRjle<snip />== exampleuser@slackwarelaptop
```

新しい Linux VM を起動すると、新しいユーザーが作成され、sudo グループに追加されます。

```bash
bill@slackware$ azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_config_add_users.txt
```

ログインし、新しく作成したユーザーを確認します。

```bash
bill@slackware$ cat /etc/group
root:x:0:
<snip />
sudo:x:27:exampleUser
<snip />
exampleUser:x:1000:
```

<!---HONumber=AcomDC_0427_2016-->