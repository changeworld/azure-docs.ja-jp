---
title: "インクルード ファイル"
description: "インクルード ファイル"
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 506c2a4cf675a347dc4c45c9ccf8bce95de2f6fc
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2018
---
## <a name="supported-operating-systems-and-drivers"></a>サポートされているオペレーティング システムとドライバー

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-tesla-drivers"></a>NC、NCv2、NCv3、および ND シリーズ - NVIDIA Tesla ドライバー

| OS | ドライバー |
| -------- |------------- |
| Windows Server 2016 | [385.54](http://us.download.nvidia.com/Windows/Quadro_Certified/385.54/385.54-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [385.54](http://us.download.nvidia.com/Windows/Quadro_Certified/385.54/385.54-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

> [!NOTE]
> Tesla ドライバーのダウンロード リンクは、公開時現在のものです。 最新のドライバーについては、[NVIDIA](http://www.nvidia.com/) Web サイトを参照してください。
>

### <a name="nv-series---nvidia-grid-drivers"></a>NV シリーズ - NVIDIA GRID ドライバー

| OS | ドライバー |
| -------- |------------- |
| Windows Server 2016 | [GRID 5.2 (386.09)](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [GRID 5.2 (386.09)](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |

> [!NOTE]
> Microsoft では、NV VM 用の NVIDIA GRID ドライバー インストーラーを再配布しています。 Azure NV VM にはこれらの GRID ドライバーのみをインストールしてください。 これらのドライバーには、Azure での GRID 仮想 GPU ソフトウェアのライセンスが含まれています。
>