---
title: "cloud-init で使用するための Azure VM イメージの準備 | Microsoft Docs"
description: "cloud-init でデプロイするために既存の Azure VM イメージを準備する方法"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 2eb7510d4e76e4996e83f351a62c0b025b487df2
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2017
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>cloud-init で使用するための既存の Linux Azure VM イメージの準備
この記事では、cloud-init を使用するように既存の Azure 仮想マシンを再デプロイおよび準備する方法を示します。 生成されるイメージを使用して、新しい仮想マシンまたは仮想マシン スケール セットをデプロイできます。そのどちらも、デプロイ時に cloud-init によってさらにカスタマイズできます。  これらの cloud-init スクリプトは、リソースが Azure によってプロビジョニングされた後の最初の起動時に実行されます。 cloud-init が Azure およびサポートされている Linux ディストリビューションでネイティブに動作する方法について詳しくは、「[cloud-init の概要](using-cloud-init.md)」をご覧ください

## <a name="prerequisites"></a>前提条件
このドキュメントでは、サポートされているバージョンの Linux オペレーティング システムを実行している Azure 仮想マシンが既に実行中であることを前提としています。 ニーズに合わせたマシンの構成、すべての必要なモジュールのインストール、すべての必要な更新プログラムの処理、要件を満たしていることを確認するテストは既に行いました。 

## <a name="preparing-rhel-74--centos-74"></a>RHEL 7.4/CentOS 7.4 の準備
cloud-init をインストールするには、Linux VM に SSH 接続を行い、次のコマンドを実行する必要があります。

```bash
sudo yum install -y cloud-init gdisk
sudo yum check-update cloud-init -y
sudo yum install cloud-init -y
```

`/etc/cloud/cloud.cfg` の `cloud_init_modules` のセクションを更新して次のモジュールを含めます。
```bash
- disk_setup
- mounts
```

汎用的な `cloud_init_modules` セクションのサンプルを次に示します。
```bash
 cloud_config_modules:
 - mounts
 - locale
 - set-passwords
 - rh_subscription
 - yum-add-repo
 - package-update-upgrade-install
 - timezone
 - puppet
 - chef
 - salt-minion
 - mcollective
 - disable-ec2-metadata
 - runcmd
 - disk_setup
 - mounts
```
一時ディスクのプロビジョニングと処理に関連するいくつかのタスクを `/etc/waagent.conf` で更新する必要があります。 次のコマンドを実行して適切な設定を更新します。 
```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
```
任意のエディターを使用して次の行を含む新しいファイル `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` を作成することで、Azure Linux エージェントのデータソースとして Azure のみを許可します。

```bash
# This configuration file is provided by the WALinuxAgent package.
datasource_list: [ Azure ]
```

既存の Azure イメージにスワップ ファイルが構成されており、cloud-init を使用して新しいイメージのスワップ ファイル構成を変更する場合は、既存のスワップ ファイルを削除する必要があります。

RedHat ベースのイメージの場合は、[スワップ ファイルを削除する](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/5/html/Deployment_Guide/s2-swap-removing-file.html)方法を説明している RedHat ドキュメントの指示に従います。

スワップファイルが有効になっている CentOS イメージでは、次のコマンドを実行してスワップファイルをオフにできます。
```bash
sudo swapoff /mnt/resource/swapfile
```

スワップファイル参照が `/etc/fstab` から削除されたことを確認します。出力は次のようになります。
```text
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

領域を節約し、スワップ ファイルを削除するには、次のコマンドを実行します。
```bash
rm /mnt/resource/swapfile
```
## <a name="extra-step-for-cloud-init-prepared-image"></a>cloud-init で準備されたイメージに対する追加の手順
> [!NOTE]
> イメージが **cloud-init** で準備されており、イメージを構成した場合は、次の手順を実行する必要があります。

次の 3 つのコマンドは、新しい特殊なソース イメージとしてカスタマイズする VM が cloud-init によって以前にプロビジョニングされていた場合にのみ使用します。  イメージが Azure Linux エージェントを使用して構成された場合は、これらを実行する必要はありません。

```bash
sudo rm -rf /var/lib/cloud/instances/* 
sudo rm -rf /var/log/cloud-init*
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Linux エージェント設定の最終処理 
すべての Azure プラットフォーム イメージには、cloud-init で構成されたかどうかに関係なく Azure Linux エージェントがインストールされています。  次のコマンドを実行して、Linux マシンからのユーザーのプロビジョニング解除を完了します。 

```bash
sudo waagent -deprovision+user -force
```

Azure Linux エージェントのプロビジョニング解除コマンドについて詳しくは、[Azure Linux エージェント](agent-user-guide.md)に関する記事をご覧ください。

SSH セッションを終了し、bash シェルから次の AzureCLI コマンドを実行して、Azure VM イメージの割り当て解除、汎用化、新規作成を行います。  `myResourceGroup` と `sourceVmName` は、sourceVM を反映した適切な情報で置換します。

```bash
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>次のステップ
構成変更の cloud-init の他の例については、以下をご覧ください。
 
- [VM に他の Linux ユーザーを追加する](cloudinit-add-user.md)
- [初回起動時にパッケージ マネージャーを実行して既存のパッケージを更新する](cloudinit-update-vm.md)
- [VM のローカル ホスト名を変更する](cloudinit-update-vm-hostname.md) 
- [アプリケーション パッケージのインストール、構成ファイルの更新、キーの挿入](tutorial-automate-vm-deployment.md)