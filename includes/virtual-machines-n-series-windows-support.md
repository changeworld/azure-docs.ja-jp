---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09/24/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 2fefe23a57668ce20ed1a6afaf3514750e3c72da
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47043882"
---
## <a name="supported-operating-systems-and-drivers"></a>サポートされているオペレーティング システムとドライバー

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla (CUDA) ドライバー

NC、NCv2、NCv3、および ND シリーズ VM (NV シリーズではオプション) の NVIDIA Tesla (CUDA) ドライバーは、次の表に表示されているオペレーティング システム上でのみサポートされます。 ドライバーのダウンロード リンクは、公開時現在のものです。 最新のドライバーについては、[NVIDIA](http://www.nvidia.com/) Web サイトを参照してください。

> [!TIP]
> CUDA ドライバーを手動で Windows Server VM にインストールする代わりに、Azure [データ サイエンス仮想マシン](../articles/machine-learning/data-science-virtual-machine/overview.md) イメージをデプロイすることもできます。 Windows Server 2016 用の DSVM エディションでは、NVIDIA CUDA ドライバーや CUDA Deep Neural Network ライブラリなどのツールが事前にインストールされています。


| OS | ドライバー |
| -------- |------------- |
| Windows Server 2016 | [398.75](http://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398.75](http://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |


### <a name="nvidia-grid-drivers"></a>NVIDIA GRID ドライバー

Microsoft では、仮想ワークステーションまたは仮想アプリケーションとして使用される NV および NVv2 シリーズ VM 用の NVIDIA GRID ドライバーのインストーラーを再分配します。 次の表に記載されているオペレーティング システム上でのみ、これらの GRID ドライバーを Azure NV VM にインストールします。 これらのドライバーには、Azure での GRID 仮想 GPU ソフトウェアのライセンスが含まれています。

| OS | ドライバー |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [GRID 6.2 (391.81)](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [GRID 6.2 (391.81)](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |