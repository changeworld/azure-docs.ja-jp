---
title: "Windows 用 Azure N シリーズ ドライバー セットアップ | Microsoft Docs"
description: "Azure で Windows を実行する N シリーズ VM 用の NVIDIA GPU ドライバーの設定方法"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 4bf470737f961219250c37d890ed81eb62409626
ms.openlocfilehash: feb9370f0241fd860749d32f5db5842cb18463f1


---
# <a name="set-up-gpu-drivers-for-n-series-vms"></a>N シリーズ VM の GPU ドライバーの設定
Windows Server を実行する Azure N シリーズ VM の GPU 機能を利用するには、デプロイ後に各 VM に NVIDIA グラフィック ドライバーをインストールする必要があります。 [Linux VM](virtual-machines-linux-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) にも適用されます。

基本的な仕様、ストレージの容量、ディスクの詳細については、[仮想マシンのサイズ](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事をご覧ください。




## <a name="supported-gpu-drivers"></a>サポートされる GPU ドライバー

リモート デスクトップで N シリーズの各 VM に接続します。 ご使用の Windows オペレーティング システムに、サポートされるドライバーをダウンロード、抽出、インストールします。 

### <a name="nvidia-grid-drivers-for-nv-vms"></a>NV VM 用の NVIDIA GRID ドライバー

* [Windows Server 2016](https://go.microsoft.com/fwlink/?linkid=836843) (.zip)

* [Windows Server 2012 R2](https://go.microsoft.com/fwlink/?linkid=836844) (.zip)

### <a name="nvidia-tesla-drivers-for-nc-vms"></a>NC VM 用の NVIDIA Tesla ドライバー

* [Windows Server 2016](https://go.microsoft.com/fwlink/?linkid=836841) (.zip)

* [Windows Server 2012 R2](https://go.microsoft.com/fwlink/?linkid=836842) (.zip)



## <a name="verify-driver-installation"></a>ドライバーのインストールの確認

Azure NV VM では、ドライバーのインストール後に再起動が必要です。 NC VM では、再起動は必要ありません。

ドライバーのインストールはデバイス マネージャーで確認できます。 次は、Azure NC VM で正しく構成された K80 カードの例です。

![GPU ドライバーのプロパティ](./media/virtual-machines-windows-n-series-driver-setup/GPU_driver_properties.png)

GPU デバイスの状態を照会するには、ドライバーとともにインストールされる [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface)コマンド ライン ユーティリティを実行します。 

![NVIDIA デバイスの状態](./media/virtual-machines-windows-n-series-driver-setup/smi.png)  

## <a name="next-steps"></a>次のステップ

* N シリーズ VM の NVIDIA GPU の詳細については、次のサイトをご覧ください。
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (Azure NC VM 用)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (Azure NV VM 用)

* NVIDIA Tesla GPU 向けに GPU アクセラレータを使用したアプリケーションを構築する開発者は、[CUDA Toolkit 8](https://developer.nvidia.com/cuda-downloads) をダウンロードしてインストールできます。





<!--HONumber=Dec16_HO2-->


