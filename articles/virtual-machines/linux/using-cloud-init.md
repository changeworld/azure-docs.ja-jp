---
title: "cloud-init を使用して Linux VM をカスタマイズする | Microsoft Docs"
description: "Azure CLI 2.0 による作成時に cloud-init を使用して Linux VM をカスタマイズする方法"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/03/2017
ms.author: iainfou
ms.openlocfilehash: 5559f258f5c29b07edb5e61be4755d67173019e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-cloud-init-to-customize-a-linux-vm-in-azure"></a>Azure で cloud-init を使用して Linux VM をカスタマイズする
この記事では、Azure の仮想マシン (VM) で [cloud-init](https://cloudinit.readthedocs.io) スクリプトを使用して、ホスト名の設定、パッケージの更新、ユーザー アカウントの管理を行う方法について説明します。 これらの cloud-init スクリプトは、Azure CLI 2.0 で VM を作成するときに、起動時に実行されます。 アプリケーションのインストール、構成ファイルの記述、Key Vault からのキーの挿入方法の詳細な概要については、[このチュートリアル](tutorial-automate-vm-deployment.md)をご覧ください。 これらの手順は、[Azure CLI 1.0](using-cloud-init-nodejs.md) を使用して実行することもできます。


## <a name="cloud-init-overview"></a>cloud-init の概要
[cloud-Init](https://cloudinit.readthedocs.io) は、Linux VM を初回起動時にカスタマイズするために広く使用されているアプローチです。 cloud-init を使って、パッケージをインストールしてファイルを書き込んだり、ユーザーとセキュリティを構成したりすることができます。 初回起動処理中に cloud-init が実行されるので、構成を適用するために追加の手順や必要なエージェントはありません。

cloud-init はディストリビューション全体でも有効です。 たとえば、パッケージをインストールするときに **apt-get install** や **yum install** は使用しません。 代わりに、cloud-init ではインストールするパッケージの一覧をユーザーが定義できます。 cloud-init によって、選択したディストリビューションに対してネイティブのパッケージ管理ツールが自動的に使用されます。

Microsoft ではパートナーと協力して、パートナーから Azure に提供されたイメージに cloud-init を含めて、使用できるようにしています。 次の表は、Azure プラットフォーム イメージでの最新の cloud-init の可用性の概要を示しています。

| エイリアス | 発行元 | プラン | SKU | バージョン |
|:--- |:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canonical |UbuntuServer |16.04 LTS |最新 |
| UbuntuLTS |Canonical |UbuntuServer |14.04.5-LTS |最新 |
| CoreOS |CoreOS |CoreOS |安定版 |最新 |


## <a name="set-the-hostname-with-cloud-init"></a>cloud-init でホスト名を設定する
cloud-init ファイルは [YAML](http://www.yaml.org) で記述されています。 Azure で [az vm create](/cli/azure/vm#create) を使用して VM を作成するときに、cloud-init スクリプトを実行するには、`--custom-data` スイッチで cloud-init ファイルを指定します。 cloud-init ファイルで構成できるものの例をいくつか見てみましょう。 一般的なシナリオでは、VM のホスト名を設定します。 既定では、ホスト名は VM 名と同じです。 

最初に、[az group create](/cli/azure/group#create) を使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli
az group create --name myResourceGroup --location eastus
```

現在のシェルで、*cloud_init_hostname.txt* というファイルを作成し、次の構成を貼り付けます。 たとえば、ローカル コンピューター上にない Cloud Shell でファイルを作成します。 任意のエディターを使用することができます。 Cloud Shell で `sensible-editor cloud_init_hostname.txt` を入力し、ファイルを作成して使用可能なエディターの一覧を確認します。 cloud-init ファイル全体 (特に最初の行) が正しくコピーされたことを確認してください。

```yaml
#cloud-config
hostname: myhostname
```

ここで、[az vm create](/cli/azure/vm#create) で VM を作成し、次のように `--custom-data cloud_init_hostname.txt` で cloud-init ファイルを指定します。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMHostname \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_hostname.txt
```

作成されると、Azure CLI は VM に関する情報を表示します。 `publicIpAddress` を使用して VM に SSH 接続します。 実際のアドレスを次のように入力します。

```bash
ssh azureuser@publicIpAddress
```

VM 名を表示するには、`hostname` コマンドを次のように使用します。

```bash
hostname
```

VM は、次の出力の例で示すように、cloud-init ファイルで設定された値としてホスト名をレポートします。

```bash
myhostname
```

## <a name="update-a-vm-with-cloud-init"></a>cloud-init で VM を更新する
セキュリティのために、初回起動時に最新の更新プログラムを適用するように VM を構成することが必要な場合があります。 cloud-init はさまざまな Linux ディストリビューションで動作するため、パッケージ マネージャーに `apt` または `yum` を指定する必要はありません。 代わりに、`package_upgrade` を定義し、cloud-init プロセスで、使用中のディストリビューションに適したメカニズムを判断します。 このワークフローでは、異なるディストリビューションで同じ cloud-init スクリプトを使用することができます。

実行中のアップグレード プロセスを確認するには、*cloud_init_upgrade.txt* という名前の cloud-init ファイルを作成し、次の構成を貼り付けます。

```yaml
#cloud-config
package_upgrade: true
```

ここで、[az vm create](/cli/azure/vm#create) で VM を作成し、次のように `--custom-data cloud_init_upgrade.txt` で cloud-init ファイルを指定します。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUpgrade \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_upgrade.txt
```

前述のコマンドからの出力に示すように、VM のパブリック IP アドレスに SSH 接続します。 実際のパブリック IP アドレスを次のように入力します。

```bash
ssh azureuser@publicIpAddress
```

パッケージ管理ツールを実行し、更新プログラムを確認します。 次の例では、Ubuntu VM で `apt-get` を使用します。

```bash
sudo apt-get upgrade
```

cloud-init によって起動時に更新プログラムが確認され、インストールされるため、次の出力例に示すように、適用する更新プログラムはありません。

```bash
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>cloud-init で VM にユーザーを追加する
新しい Linux VM での最初のタスクの 1 つとして、*root* の使用を避けるために自分用のユーザーを追加することがあります。 SSH キーは、セキュリティと使いやすさのためのベスト プラクティスです。 キーは、この cloud-init スクリプトで *~/.ssh/authorized_keys* ファイルに追加されます。

Linux VM にユーザーを追加するには、*cloud_init_upgrade.txt* という名前の cloud-init ファイルを作成し、次の構成を貼り付けます。 *ssh-authorized-keys* に自分の公開キー (*~/.ssh/id_rsa.pub* の内容など) を指定します。

```yaml
#cloud-config
users:
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```

ここで、[az vm create](/cli/azure/vm#create) で VM を作成し、次のように `--custom-data cloud_init_add_user.txt` で cloud-init ファイルを指定します。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUser \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_add_user.txt
```

前述のコマンドからの出力に示すように、VM のパブリック IP アドレスに SSH 接続します。 実際のパブリック IP アドレスを次のように入力します。

```bash
ssh myadminuser@publicIpAddress
```

ユーザーが VM および指定されたグループに追加されたことを確認するには、*/etc/group* ファイルの内容を次のように表示します。

```bash
cat /etc/group
```

次の出力例は、*cloud_init_add_user.txt* ファイルからのユーザーが VM と適切なグループに追加されたことを示しています。

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>次のステップ
cloud-init は、起動時に Linux VM を変更する標準的な方法の 1 つです。 Azure では、VM 拡張機能を使用して、起動時または実行中に Linux VM を変更することもできます。 たとえば、Azure VM 拡張機能を使用して、初回起動時だけでなく実行中の VM でスクリプトを実行できます。 VM 拡張機能について詳しくは、[VM 拡張機能と機能](extensions-features.md)に関する記事をご覧ください。また、拡張機能を使用する方法の例については、[VMAccess 拡張機能を使用した、Azure Linux VM 上のユーザーの管理、SSH、ディスクのチェックまたは修復](using-vmaccess-extension.md)に関する記事をご覧ください。