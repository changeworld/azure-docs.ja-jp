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
ms.date: 12/07/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 4bf470737f961219250c37d890ed81eb62409626
ms.openlocfilehash: f03026212dfcbe2faa22188ebfb2e953114a87fd


---
# <a name="set-up-gpu-drivers-for-n-series-vms"></a>N シリーズ VM の GPU ドライバーの設定
対応する Linux ディストリビューションを実行する Azure N シリーズ VM の GPU 機能を利用するには、デプロイ後に各 VM に NVIDIA グラフィック ドライバーをインストールする必要があります。 [Windows VM](virtual-machines-windows-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) にも適用されます。

N シリーズ VM の仕様、ストレージの容量、ディスクの詳細については、[仮想マシンのサイズ](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。



## <a name="supported-gpu-drivers"></a>サポートされる GPU ドライバー


> [!NOTE]
> 現時点では、Linux GPU のサポートは、Ubuntu Server 16.04 LTS を実行する Azure NC VM でのみご利用いただけます。

### <a name="nvidia-tesla-drivers-for-nc-vms"></a>NC VM 用の NVIDIA Tesla ドライバー

* [Ubuntu 16.04 LTS](https://go.microsoft.com/fwlink/?linkid=836899) (.run。自己解凍形式のインストーラー)

## <a name="tesla-driver-installation-on-ubuntu-1604-lts"></a>Ubuntu 16.04 LTS での Tesla ドライバーのインストール

1. Azure N シリーズの VM に SSH 接続を行います。

2. システムに CUDA 対応の GPU が備わっているかどうかを確認するには、次のコマンドを実行します。

    ```bash
    lspci | grep -i NVIDIA
    ```
    次の例のような出力が表示されます (NVIDIA Tesla K80 カードが表示されています)。

    ![lspci コマンドの出力](./media/virtual-machines-linux-n-series-driver-setup/lspci.png)

3. お使いのディストリビューションのドライバーの .run ファイルをダウンロードします。 次のコマンドの例では、Ubuntu 16.04 LTS Tesla ドライバーを/tmp ディレクトリにダウンロードします。

    ```bash
    wget -O /tmp/NVIDIA-Linux-x86_64-367.48.run https://go.microsoft.com/fwlink/?linkid=836899
    ```

4. `gcc` と `make` (Tesla ドライバーに必要) をシステムにインストールする必要がある場合は、次のように入力します。

    ```bash
    sudo apt-get update
    
    sudo apt install gcc

    sudo apt install make
    ```

4. ドライバーのインストーラーを含むディレクトリに移動し、次のようなコマンドを実行します。

    ```bash
    chmod +x NVIDIA-Linux-x86_64-367.48.run
    
    sudo sh ./NVIDIA-Linux-x86_64-367.48.run
    ```

## <a name="verify-driver-installation"></a>ドライバーのインストールの確認


GPU デバイスの状態を照会するには、ドライバーとともにインストールされる [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface)コマンド ライン ユーティリティを実行します。 

![NVIDIA デバイスの状態](./media/virtual-machines-linux-n-series-driver-setup/smi.png)

## <a name="optional-installation-of-nvidia-cuda-toolkit-on-ubuntu-1604-lts"></a>Ubuntu 16.04 LTS の NVIDIA CUDA Toolkit のオプション インストール

必要に応じて、Ubuntu 16.04 LTS を実行する NC VM に NVIDIA CUDA Toolkit 8.0 をインストールできます。 GPU ドライバーだけでなく、Toolkit は、GPU アクセラレータを使用したアプリケーションを構築する C と C++ の開発者に包括的な開発環境を提供します。

CUDA Toolkit をインストールするには、次のようなコマンドを実行します。

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.44-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers
```

インストールには数分かかる場合があります。

## <a name="next-steps"></a>次のステップ

* N シリーズ VM の NVIDIA GPU の詳細については、次のサイトをご覧ください。
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (Azure NC VM 用)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (Azure NV VM 用)




<!--HONumber=Dec16_HO2-->


