---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/03/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 689e45f96ed5e7aaea3aecd1193e9cd58f10e80d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/24/2018
---
## <a name="supported-operating-systems-and-drivers"></a>サポートされているオペレーティング システムとドライバー

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-tesla-cuda-drivers"></a>NC、NCv2、NCv3、および ND シリーズ - NVIDIA Tesla (CUDA) ドライバー

次の表にあるドライバーのダウンロード リンクは、公開された時点のものです。 最新のドライバーについては、[NVIDIA](http://www.nvidia.com/) Web サイトを参照してください。

> [!TIP]
> CUDA ドライバーを手動で Windows Server VM にインストールする代わりに、Azure [データ サイエンス仮想マシン](../articles/machine-learning/data-science-virtual-machine/overview.md) イメージをデプロイすることもできます。 Windows Server 2016 用の DSVM エディションでは、NVIDIA CUDA ドライバーや CUDA Deep Neural Network ライブラリなどのツールが事前にインストールされています。


| OS | ドライバー |
| -------- |------------- |
| Windows Server 2016 | [390.85](http://us.download.nvidia.com/Windows/Quadro_Certified/390.85/390.85-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [390.85](http://us.download.nvidia.com/Windows/Quadro_Certified/390.85/390.85-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nv-series---nvidia-grid-drivers"></a>NV シリーズ - NVIDIA GRID ドライバー

Microsoft では、NV VM 用の NVIDIA GRID ドライバー インストーラーを再配布しています。 Azure NV VM にはこれらの GRID ドライバーのみをインストールしてください。 これらのドライバーには、Azure での GRID 仮想 GPU ソフトウェアのライセンスが含まれています。

| OS | ドライバー |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [GRID 6.0 (391.03)](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [GRID 6.0 (391.03)](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |