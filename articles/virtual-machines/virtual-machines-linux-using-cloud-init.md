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
    ms.date="04/29/2016"
    ms.author="v-livech"
/>

# cloud-init を利用し、作成時に Linux VM をカスタマイズする

この記事では、ホスト名の設定、インストールされているパッケージの更新、およびユーザー アカウントの管理を行う cloud-init スクリプトを作成する方法について説明します。これらの cloud-init スクリプトは、[Azure CLI](../xplat-cli-install.md) から VM を作成するときに使用されます。

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

ホスト名を設定する cloud-init スクリプトを作成します。

```bash
#cloud-config
hostname: exampleServerName
```

最初の起動時に Linux を更新する、Debian 製品用の cloud-init スクリプトを作成します。

```bash
#cloud-config
apt_upgrade: true
```

ユーザーを追加する cloud-init スクリプトを作成します。

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

## 詳細なチュートリアル

### Azure CLI を使用し、VM の作成に cloud-init スクリプトを追加する

Azure で VM を作成するときに、cloud-init スクリプトを起動するには、Azure CLI の `--custom-data` スイッチを使用して cloud-init ファイルを指定します。

注: この記事では、`--custom-data` スイッチを cloud-init ファイルに使用する方法を説明していますが、このスイッチを使用して任意のコードやファイルを渡すこともできます。そのようなファイルの処理方法が Linux VM で既に理解されている場合、ファイルは自動的に実行されます。

```bash
azure vm create \
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

#### `cloud_config_hostname.txt` という名前の cloud-init スクリプトの例

``` bash
#cloud-config
hostname: exampleServerName
```

VM の初回起動時に、この cloud-init スクリプトによってホスト名が `exampleServerName` に設定されます。

```bash
azure vm create \
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
ssh exampleVM
hostname
exampleServerName
```

### Linux を更新する cloud-init スクリプトを作成する

セキュリティ上の理由から、最初の起動で Ubuntu VM を更新することがあります。Linux ディストリビューションによっては、cloud-init を利用し、次のスクリプトで更新できます。

#### Debian 製品用の cloud-init スクリプト `cloud_config_apt_upgrade.txt` の例

```bash
#cloud-config
apt_upgrade: true
```

新しい Linux VM が起動すると、`apt-get` によって、インストールされているすべてのパッケージが即座に更新されます。

```bash
azure vm create \
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

新しい Linux VM での最初のタスクの 1 つとして、ユーザーを手動で追加すること、または `root` を使用しないようにすることがあります。これはセキュリティ上の理由から重要です。また、使いやすさを考慮し、追加したユーザーの `~/.ssh/authorized_keys` ファイルに SSH 公開キーを追加して、パスワード不要の安全な SSH ログインを可能にします。

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

新しい Linux VM を起動すると、新しいユーザーが作成され、sudo グループに追加されます。

```bash
azure vm create \
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

<!---HONumber=AcomDC_0504_2016-->