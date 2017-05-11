---
title: "Linux 用 Azure N シリーズ ドライバー セットアップ | Microsoft Docs"
description: "Azure で Linux を実行する N シリーズ VM 用の NVIDIA GPU ドライバーの設定方法を説明します。"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: de674af369080ad7eb608608685e293f2326c8e6
ms.openlocfilehash: 69f1363c26d8b5a18ffd5629c6a49c34306dd7c0
ms.contentlocale: ja-jp
ms.lasthandoff: 05/04/2017


---

# <a name="set-up-gpu-drivers-for-n-series-vms-running-linux"></a>Linux を実行している N シリーズ VM の GPU ドライバーをセットアップする

Linux を実行する Azure N シリーズ VM の GPU 機能を利用するには、各 VM に NVIDIA グラフィック ドライバーをインストールする必要があります。 この記事では、N シリーズ VM をデプロイした後のドライバーのセットアップ手順について説明します。 ドライバーのセットアップ情報は、[Windows VM](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) でも利用可能です。


N シリーズ VM の仕様、ストレージの容量、およびディスクの詳細については、「[GPU Linux VM のサイズ](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。 



## <a name="supported-distributions-and-drivers"></a>サポートされているディストリビューションとドライバー

> [!IMPORTANT]
> 現時点では、Linux GPU ドライバーのサポートは、Azure NC VM でのみご利用いただけます。 

N シリーズ Linux VM の NVIDIA グラフィック ドライバーを実行するために、Azure Marketplace の次のディストリビューションがサポートされています。

### <a name="nc-vms-tesla-k80-card"></a>NC VM (Tesla K80 カード)
* Ubuntu 16.04 LTS 
* Red Hat Enterprise Linux 7.3 
* CentOS-based 7.3 

**サポートされるドライバー**: NVIDIA CUDA 8.0、ドライバー ブランチ R375。 [インストール手順](#install-cuda-drivers-for-nc-vms)




> [!WARNING] 
> サード パーティ製ソフトウェアを Red Hat 製品にインストールすると、Red Hat サポート条件に影響を与えることがあります。 [Red Hat のサポート技術情報記事](https://access.redhat.com/articles/1067)を参照してください。
>




## <a name="install-cuda-drivers-for-nc-vms"></a>NC VM 用の CUDA ドライバーのインストール

NVIDIA CUDA Toolkit 8.0 から NVIDIA ドライバーを Linux NC VM にインストールする手順は次のとおりです。 

C および C++ の開発者は、GPU アクセラレータを使用したアプリケーションを構築するために、必要に応じて Toolkit 全体をインストールできます。 詳細については、[CUDA インストール ガイド](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)を参照してください。


> [!NOTE]
> ここで示されている CUDA ドライバーのダウンロード リンクは、公開された時点のものです。 最新のドライバーについては、[NVIDIA](http://www.nvidia.com/) Web サイトを参照してください。
>

CUDA Toolkit をインストールするには、各 VM への SSH 接続を作成します。 システムに CUDA 対応の GPU が備わっているかどうかを確認するには、次のコマンドを実行します。

```bash
lspci | grep -i NVIDIA
```
次の例のような出力が表示されます (NVIDIA Tesla K80 カードが表示されています)。

![lspci コマンドの出力](./media/n-series-driver-setup/lspci.png)

次に、ディストリビューションに固有のコマンドを実行します。

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.61-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers

```
インストールには数分かかる場合があります。

必要に応じて完全な CUDA ツールキットをインストールするには、次のように入力します。

```bash
sudo apt-get install cuda
```

VM を再起動して、インストールの確認に進みます。

### <a name="centos-73-or-red-hat-enterprise-linux-73"></a>CentOS 7.3 または Red Hat Enterprise Linux 7.3

> [!IMPORTANT] 
> 既知の問題により、CentOS 7.3 または Red Hat Enterprise Linux 7.3 を実行する NC24r VM への NVIDIA CUDA ドライバーのインストールは失敗します。
>

最初に、更新プログラムを取得します。 

```bash
sudo yum update

sudo reboot
```

VM に再接続し、次のコマンドを使用してインストールを続行します。

```bash
sudo yum install kernel-devel

sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

sudo yum install dkms

CUDA_REPO_PKG=cuda-repo-rhel7-8.0.61-1.x86_64.rpm

wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo yum install cuda-drivers
```

インストールには数分かかる場合があります。 必要に応じて完全な CUDA ツールキットをインストールするには、次のように入力します。

```bash
sudo yum install cuda
```

VM を再起動して、インストールの確認に進みます。


### <a name="verify-driver-installation"></a>ドライバーのインストールの確認


GPU デバイスの状態を照会するには、VM に SSH 接続し、ドライバーと共にインストールされる [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) コマンド ライン ユーティリティを実行します。 

次のような出力が表示されます。

![NVIDIA デバイスの状態](./media/n-series-driver-setup/smi.png)


### <a name="cuda-driver-updates"></a>CUDA ドライバーの更新

デプロイ後は、定期的に CUDA ドライバーを更新することをお勧めします。

#### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers
```

更新の完了後、VM を再起動します。

#### <a name="centos-73-or-red-hat-enterprise-linux-73"></a>CentOS 7.3 または Red Hat Enterprise Linux 7.3

```bash
sudo yum update
```

更新の完了後、VM を再起動します。



## <a name="troubleshooting"></a>トラブルシューティング

* Ubuntu 16.04 LTS で 4.4.0-75 Linux カーネルを実行している、Azure N シリーズ VM の CUDA ドライバーには既知の問題があります。 カーネルのアップグレード時にドライバーの機能を保持するには、カーネル バージョン 4.4.0-77 以上にアップグレードします。 



## <a name="next-steps"></a>次のステップ

* N シリーズ VM の NVIDIA GPU の詳細については、次のサイトをご覧ください。
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (Azure NC VM 用)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (Azure NV VM 用)

* NVIDIA ドライバーがインストールされている Linux VM のイメージをキャプチャするには、「[Linux 仮想マシンを一般化してキャプチャする方法](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。
