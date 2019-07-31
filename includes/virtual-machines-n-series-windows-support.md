---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 508d13356a7dd221b0767a0cedd60b4f1402baf4
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286168"
---
## <a name="supported-operating-systems-and-drivers"></a>サポートされているオペレーティング システムとドライバー

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla (CUDA) ドライバー

NC、NCv2、NCv3、ND、および NDv2 シリーズ VM (NV シリーズではオプション) の NVIDIA Tesla (CUDA) ドライバーは、次の表に示されているオペレーティング システムでのみサポートされます。 ドライバーのダウンロード リンクは、公開時現在のものです。 最新のドライバーについては、[NVIDIA](https://www.nvidia.com/) Web サイトを参照してください。

> [!TIP]
> CUDA ドライバーを手動で Windows Server VM にインストールする代わりに、Azure [データ サイエンス仮想マシン](../articles/machine-learning/data-science-virtual-machine/overview.md) イメージをデプロイすることもできます。 Windows Server 2016 用の DSVM エディションでは、NVIDIA CUDA ドライバーや CUDA Deep Neural Network ライブラリなどのツールが事前にインストールされています。


| OS | ドライバー |
| -------- |------------- |
| Windows Server 2016 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID ドライバー

Microsoft では、仮想ワークステーションまたは仮想アプリケーションとして使用される NV および NVv3 シリーズ VM 用の NVIDIA GRID ドライバーのインストーラーを再分配します。 次の表に記載されているオペレーティング システム上でのみ、これらの GRID ドライバーを Azure NV シリーズ VM にインストールします。 これらのドライバーには、Azure での GRID 仮想 GPU ソフトウェアのライセンスが含まれています。 NVIDIA vGPU ソフトウェア ライセンス サーバーを設定する必要はありません。

| OS | ドライバー |
| -------- |------------- |
| Windows Server 2019<br/><br/>Windows Server 2016<br/><br/>Windows 10 | [GRID 9.0 (431.02)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe) |
| Windows Server 2012 R2<br/><br/>Windows Server 2008 R2<br/><br/>Windows 8<br/><br/>Windows 7 | [GRID 9.0 (431.02)](https://go.microsoft.com/fwlink/?linkid=874184) (.exe)  |
