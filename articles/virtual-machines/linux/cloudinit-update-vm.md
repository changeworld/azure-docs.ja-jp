---
title: Azure の Linux VM で cloud-init を使用する
description: Azure CLI による作成時に Linux VM で cloud-init を使用してパッケージを更新およびインストールする方法
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 06/01/2021
ms.author: cynthn
ms.subservice: cloud-init
ms.openlocfilehash: a5bd15f19cd641ce7c10ea932084d36cca0d7901
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687902"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Azure の Linux VM で cloud-init を使用してパッケージを更新およびインストールする

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: フレキシブルなスケール セット 

この記事では、Azure でのプロビジョニング時に、[cloud-init](https://cloudinit.readthedocs.io) を使用して、Linux 仮想マシン (VM) または仮想マシン スケール セットでパッケージを更新する方法を示します。 これらの cloud-init スクリプトは、Azure によってリソースがプロビジョニングされた後の最初の起動時に実行されます。 cloud-init が Azure およびサポートされている Linux ディストリビューションでネイティブに動作する方法の詳細については、[cloud-init の概要](using-cloud-init.md)に関するページをご覧ください

## <a name="update-a-vm-with-cloud-init"></a>cloud-init で VM を更新する
セキュリティのために、初回起動時に最新の更新プログラムを適用するように VM を構成することが必要な場合があります。 cloud-init はさまざまな Linux ディストリビューションで動作するため、パッケージ マネージャーに `apt` または `yum` を指定する必要はありません。 代わりに、`package_upgrade` を定義し、cloud-init プロセスで、使用中のディストリビューションに適したメカニズムを判断します。 

この例では、Azure Cloud Shell を使用します。 実行中のアップグレード プロセスを確認するには、*cloud_init_upgrade.txt* という名前のファイルを作成し、次の構成を貼り付けます。 

下のコード ブロックの **[試してみる]** ボタンを選択して、Cloud Shell を開きます。 Cloud Shell でファイルを作成して使用可能なエディターの一覧を確認するには、以下を入力します。

```azurecli-interactive
sensible-editor cloud_init_upgrade.txt 
```

下のテキストをコピーし、`cloud_init_upgrade.txt` ファイルに貼り付けます。 cloud-init ファイル全体 (特に最初の行) が正しくコピーされたことを確認してください。  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

デプロイする前に、[az group create](/cli/azure/group) コマンドを使用してリソース グループを作成する必要があります。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

ここで、[az vm create](/cli/azure/vm) で VM を作成し、次のように `--custom-data` パラメーターで cloud-init ファイルを指定します。

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

前述のコマンドからの出力に示すように、VM のパブリック IP アドレスに SSH 接続します。 実際の **publicIpAddress** を次のように入力します。

```bash
ssh <publicIpAddress>
```

パッケージ管理ツールを実行し、更新プログラムを確認します。

```bash
sudo yum update
```

cloud-init によって起動時に更新プログラムが確認され、インストールされるため、追加で適用する更新プログラムはありません。  更新プロセス、変更されたパッケージの数、`httpd` のインストールを確認するには、`yum history` を実行します。以下のような出力が表示されます。

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>次のステップ
構成変更の cloud-init の他の例については、以下をご覧ください。
 
- [VM に他の Linux ユーザーを追加する](cloudinit-add-user.md)
- [初回起動時にパッケージ マネージャーを実行して既存のパッケージを更新する](cloudinit-update-vm.md)
- [VM のローカル ホスト名を変更する](cloudinit-update-vm-hostname.md) 
- [アプリケーション パッケージのインストール、構成ファイルの更新、キーの挿入](tutorial-automate-vm-deployment.md)
